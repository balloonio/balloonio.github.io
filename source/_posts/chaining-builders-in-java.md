---
title: Chaining Builders in Java
date: 2019-04-10 14:14:59
tags:
  - Java
categories:
  - fundamentals
---

This blog is a borrowed content from Adam's blog [here](http://adamish.com/blog/archives/405). All copyright are due to the original blogger.

<!-- more -->

JavaFX uses chaining builders as a kind of syntactic sugar. Here’s an example. The first `create()` creates the builder (and inner text object), the subsequent calls set attributes on the the object, and finally it is returned via `.build()`.

```java
final Text text = TextBuilder.create()
    .text("hello")
    .x(50).y(100)
    .fill(Color.RED)
.build();
```

Advantages:

- Slightly less code.
- Safer (as self-documenting) than complicated constructors with same type e.g. (String, int, int, int, int)
- No need for intermediate object if we were just passing it to another method.
- I think they’re can be useful than that… What if the builder was the only way to create a new instance of your class… What if you were creating an immutable type….

Here’s a standard immutable type.

```java
public class Horse {
    private String name;
    private int height;
    public Horse(String name, int height) {
      this.name = name;
      this.height = height;
    }
    public String getName() {
        return name;
    }
    public int getHeight() {
        return height;
    }
}
```

It looks harmless enough, but what happens when someone adds a new field, say age, weight? We have to extend the constructor, and because we already have code using the old constructor we end up with many different constructors leading to confusion.

```java
public Horse(String name, int height) {
    this.name = name;
    this.height = height;
}
public Horse(String name, int height, int age) {
    this(name, height);
    this.age = age;
}
public Horse(String name, int height, int age, int weight) {
    this(name, height, age);
    this.weight = weight;
}
```

So how can we make this more future-proof using chaining builders. Well here’s the same class with a built in builder. Using the builder is the only way to instiate a new horse.

```java
public class Horse {
    private String name;
    private int height;
    private Horse() {
    }
    public static class Builder {
        private Horse horse = new Horse();
        public Builder name(String name) {
            horse.name = name;
            return this;
        }
        public Builder height(int height) {
            horse.height = height;
            return this;
        }
        public Horse build() {
            return horse;
        }
    }
    public static Builder create() {
        return new Builder();
    }
    public String getName() {
        return name;
    }
    public int getHeight() {
        return height;
    }
}
```

So we create a horse as follows.

```java
Horse h = Horse.create()
        .name("ed")
        .height(123)
        .build();
```

So how do we support future attributes? We just add new sections to the builder… We also have a clear single point of construction, the build() method. This could contain additional logic to finish the job.

```java
public Builder weight(int weight) {
    horse.weight = weight;
    return this;
}
public Builder age(int age) {
    horse.age = age;
    return this;
}
```
