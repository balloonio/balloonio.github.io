---
title: Object-Oriented Design Principles
date: 2018-11-12 23:43:43
tags:
- OOD
- design
categories:
  - Design
---

# Why does it matter?

Why does it matter? Why do we need OO design principles? The short answer is - bad design leads to bad code. Principles are defined so that people can avoid bad design, and thus avoid bad code.  

It sounds pretty reasonable, right? But wait, what do you mean when you say **bad code**? What is fundamentally so bad about it?

{% blockquote Robert C. Martin (aka Uncle Bob) %}

**Rigidity**
Every change affects many other parts
**Fragility**
Things break in unrelated places
**Immobility**
Cannot reuse code outside of its original context

{% endblockquote %}

## Rigidity

When we are adding a new feature, we have to add code to multiple places instead of only few classes. Here is an example of rigidity:


# S.O.L.I.D. Principles

## **S**ingle-responsibility principle

a class should have only a single responsibility (i.e. changes to only one part of the software's specification should be able to affect the specification of the class)

## **O**pen-closed principle

"software entities … should be open for extension, but closed for modification."

## **L**iskov substitution principle

"objects in a program should be replaceable with instances of their subtypes without altering the correctness of that program."

## **I**nterface segregation principle

"many client-specific interfaces are better than one general-purpose interface.

## **D**ependency inversion principle

one should "depend upon abstractions, not concretions."

# Reference

{% youtube GtZtQ2VFweA %}

{% youtube TMuno5RZNeE %}
