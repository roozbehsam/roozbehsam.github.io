---
title: "Value Objects vs Entities in DDD"
date: 2025-04-16 00:00 +0330
description: "Value Objects vs Entities in DDD"
image:
  path: /assets/img/posts/valueobject-vs-entity-ddd.png
category: [System Design & Architecture Patterns]
tags: [architecture_patterns, system_design, DDD]
published: true
sitemap: true
---

In Domain-Driven Design (DDD), **Value Objects** and **Entities** are two fundamental building blocks used to model the domain.

---

### **Value Object**

A **Value Object** represents a descriptive aspect of the domain that **does not have a unique identity**. Its **meaning comes from its attributes**, not from any kind of identifier. Think of it as a *value that is defined by its content*.

- **Immutable**: Once created, you don’t change a Value Object. Instead, you replace it.
- **No identity**: Two Value Objects with the same data are considered the same.
- **Examples**: 
  - A money amount (like $20.00)
  - A date range
  - An address (e.g., "123 Main St, New York")

They are useful when you want to describe something and focus on *what it is*, not *who it is*.

---

### **Entity**

An **Entity** is an object that **has a distinct identity that runs through time and different states**. Even if its data changes, the identity remains the same.

- **Has an identity**: Usually represented by a unique ID (like a user ID or order number).
- **Mutable**: Its properties can change, but its identity stays constant.
- **Examples**:
  - A person
  - An order
  - A product in inventory

Entities are used when you care about tracking *who or what something is*, rather than just the values it holds.

---

### Quick Comparison:

| Feature             | Value Object                  | Entity                        |
|---------------------|-------------------------------|-------------------------------|
| Identity            | No                            | Yes                           |
| Equality            | Based on values               | Based on identity             |
| Mutability          | Usually immutable             | Usually mutable               |
| Purpose             | Describes things              | Tracks and distinguishes things |

