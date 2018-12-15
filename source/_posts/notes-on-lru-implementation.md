---
title: Notes on LRU implementation
date: 2018-11-08 00:43:45
tags:
  - algorithm
  - data structure
  - hashmap
  - linked list
categories:
  - online judge
---

This blog covers a detailed implementation on LRU, as well as why such data structure is being used. In the end, it also covers how such data structure can be used to solve another similar problem.

<!-- more -->

# What is LRU

Least recently used (LRU) is a cache replacement policy. Here is a description of this policy, putting into the shortest words:

{% blockquote Wikipedia https://en.wikipedia.org/wiki/Cache_replacement_policies#LRU Least recently used (LRU) %}
Discards the least recently used items first.
{% endblockquote %}

&nbsp;
When people talk about LRU cache, really what they are referring to is any cache that adopts this cache replacement policy. For example, here is the problem requirement for an LRU cache from LeetCode:

{% blockquote LeetCode https://leetcode.com/problems/lru-cache/description/ LRU Cache %}
Design and implement a data structure for Least Recently Used (LRU) cache. It should support the following operations: get and put.

`get(key)` - Get the value (will always be positive) of the key if the key exists in the cache, otherwise return -1.
`put(key, value)` - Set or insert the value if the key is not already present. When the cache reached its capacity, it should invalidate the least recently used item before inserting a new item.
{% endblockquote %}

# Cracking the problem

For any of the requirements, let's see if it is possible to achieve constant O(1) time complexity, which is the optimal time complexity

- Insert, update or remove a key-value pair if needed

The most common data structure that is able to support O(1) insertion and removal is hashmap. Therefore, very intuitively, a **hashmap** can be used here to somehow store the key to the value.  
However, this is not the only requirement here, and let's see how that can be handled.

- When capacity is reached, new insertion will evict the least used item.

In order to remove the least used item when cache is full, apparently the data structure must be able to figure out which one is the most aged item. This requires the data structure to preserve some sort of time sequence. Since hashmap doesn't preserve any order or sequence, something else is needed here. Array, heap, tree set, linked list, and double linked list are all common data structures that support order or sequence; yet only one of them supports O(1) insertion and removal - that is **double linked list**.

Therefore, the final idea is to use a double linked list to store the items in time sequence, while at the same time to use a hashmap to keep track of a key to its item node in the list so that O(1) insertion and removal based on the key is achieved.

# Show me the code

A generic double linked list that supports insertion and removal by node:

```python
class Node:
    def __init__(self, key, value):
        self.key = key
        self.value = value
        self.prev = None
        self.nxt = None

class NodeList:
    def __init__(self):
        self.size = 0
        self.head = None
        self.tail = None
    
    def insert(self, node):
        if node is None:
            return
        self.size += 1
        if self.size == 1:
            self.head = node
            self.tail = node
        else:
            node.nxt = self.head
            self.head.prev = node
            self.head = node
    
    def remove(self, node):
        if node is None:
            return
        self.size -= 1

        if self.head == node:
            self.head = node.nxt
        if self.tail == node:
            self.tail = node.prev

        if node.prev is not None:
            node.prev.nxt = node.nxt
        if node.nxt is not None:
            node.nxt.prev = node.prev

        node.prev = None
        node.nxt = None

```

A LRU implementation using the above double linked list and a hashmap:

```python
class LRUCache:
    def __init__(self, capacity):
        self.capacity = capacity
        self.key2node = {}
        self.nodelist = NodeList()
    
    def get(self, key):
        if key not in self.key2node:
            return -1
        node = self.key2node[key]
        self.nodelist.remove(node)
        self.nodelist.insert(node)
        return node.value
    
    def put(self, key, value):
        if key in self.key2node:
            old_node = self.key2node[key]
            self.nodelist.remove(old_node)
        
        new_node = Node(key, value)
        self.key2node[key] = new_node
        self.nodelist.insert(new_node)

        # evict the least used item if cache is over capacity
        if self.nodelist.size > self.capacity:
            least_used = self.nodelist.tail
            self.key2node.pop(least_used.key)
            self.nodelist.remove(least_used)
        
```

# Testing

Testcases for our LRU implementation using unittest:

```python
class LRUTest(unittest.TestCase):
    def test_empty(self):
        cache = LRUCache(0)
        self.assertIsNone(cache.get("key0"))

    def test_singleitem(self):
        cache = LRUCache(1)
        cache.put("key0", "val0")
        self.assertEqual(cache.get("key0"), "val0")

    def test_evict(self):
        cache = LRUCache(1)
        cache.put("key0", "val0")
        cache.put("key1", "val1")
        self.assertIsNone(cache.get("key0"))
        self.assertEqual(cache.get("key1"), "val1")

    def test_bigput(self):
        size = random.randint(100, 1000)
        gensize = size * 5
        control = [None] * size
        cache = LRUCache(size)
        for i in range(gensize):
            randkey = random.random() * 1000
            randval = random.random() * 100
            cache.put(randkey, randval)
            control[i % size] = (randkey, randval)

        for key, val in control:
            self.assertEqual(cache.get(key), val)


if __name__ == "__main__":
    unittest.main()
```

# Similar problem

Now let's examine this implementation closely - hashmap is used to achieve O(1) insertion and removal by key, while double linked list is used to preserve the sequence. Therefore, it is natural to ask the question - can such data structure be used to solve other problems that also requires fast insertion and removal together with sequencing?

The answer is yes, and here is an example:

## Weighted random cache

Design a data structure to hold objects with a corresponding integer weight. It should support:

- Obtain an object randomly with probability equal to (weight of the element) / (sum of the weights).
- Set an object-weight pair. If the object is already in the structure, its weight will be updated. Otherwise, the object will be inserted and set to its weight. If the weight is zero, the object can be removed.

## Cracking the problem

Similar to LRU cache, this data structure requires fast insertion and removal, which leads to hashmap. In addition, it requires a weighted random functionality. Usually with weigthted random questions, a prefix sum will be used to calculate the cumulative weight so far. However, in order to calculate the cumulative weight, the cache has to be iterated in a certain sequence. Thus, it leads to a linked list.

## Show me the code

A generic double linked list that is similar to the one from LRU cache, except the insertion takes place at the tail instead of at the head:

```python
class Node:
    def __init__(self, key, weight):
        self.key = key  # object key or id
        self.weight = weight
        self.prev = None
        self.nxt = None

class NodeList:
    def __init__(self):
        self.head = None
        self.tail = None
        self.size = 0

    def insert(self, node):
        if node is None:
            return
        self.size += 1

        # blank list
        if self.head is None and self.tail is None:
            self.head = node
            self.tail = node
            return

        # something in there
        node.prev = self.tail
        self.tail.nxt = node
        self.tail = node

    def remove(self, node):
        self.size -= 1

        if self.head == node:
            self.head = node.nxt
        if self.tail == node:
            self.tail = node.prev

        if node.prev is not None:
            node.prev.nxt = node.nxt
        if node.nxt is not None:
            node.nxt.prev = node.prev
        
        node.prev = None
        node.nxt = None
```

A weighted random implementation with O(1) `setObjectWeight(key, weight)` and O(n) `getRandom()`:

```python
class WeightRandom:
    def __init__(self):
        self.key2node = {}  # object key or id : the node contains the object info (weight, id)
        self.nodelist = NodeList()
        self.totalsum = 0

    # O(1)
    def setObjectWeight(self, obj_key, weight):
        # not in map and also try to zero weight, no op
        if weight == 0 and obj_key not in self.key2node:
            return
        # not in map, insert node
        if obj_key not in self.key2node:
            node = Node(obj_key, weight)
            self.key2node[obj_key] = node
            self.nodelist.insert(node)
            self.totalsum += weight
        # in map, update
        else:
            self.totalsum += weight - self.key2node[obj_key].weight
            self.key2node[obj_key].weight = weight

            # if weight is zero, try to remove
            if weight == 0:
                self.nodelist.remove(self.key2node[obj_key])
                self.key2node.pop(obj_key)

    # linear O(n)
    def getRandom(self):
        if self.totalsum == 0:
            return None

        randres = random.randint(1, self.totalsum)
        # loop through list to find the first node that is greater than or equal to randres
        cumsum = 0
        node = self.nodelist.head

        while node is not None:
            cumsum += node.weight
            if cumsum >= randres:
                return node.key  # obj key
            node = node.nxt

        return None
```

## Testing

Testing code:

```python
wr = WeightRandom()
wr.setObjectWeight(1, 1)
wr.setObjectWeight(2, 1)
wr.setObjectWeight(2, 0)
wr.setObjectWeight(3, 2)

for i in range(10):
    print(wr.getRandom())
```