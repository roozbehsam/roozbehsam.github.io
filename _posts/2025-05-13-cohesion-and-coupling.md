---
title: "Cohesion and Coupling"
date: 2025-05-13 00:00 +0330
description: "Cohesion and Coupling"
#image:
#  path: /assets/img/posts/img.png
category: [Architecture and Design Patterns]
tags: [architecture_patterns, design_patterns]
published: true
sitemap: true
---

**Cohesion** and **coupling** are two complementary quality attributes used to judge how “good” the internal structure of a software system is.

---

### Cohesion – How **Focused** a Module Is

- **Definition**: The degree to which the elements **inside** a single module/class/function belong together and work toward one, well-defined purpose.
    
- **Goal**: **High cohesion**—each unit does one thing and does it well.
    
- **Why it matters**
    
    - Easier to **understand**: the name and code tell the same story.
        
    - Easier to **reuse**: you can drop the unit into another context without dragging in unrelated logic.
        
    - Easier to **maintain & test**: changes stay local; tests can be narrowly scoped.
        
- **Classic cohesion scale** (from worst to best, per the original structured-design literature):
    
    1. **Coincidental** – random utilities jammed together.
        
    2. **Logical** – grouped only because they are similar types of operations (e.g., “all I/O”).
        
    3. **Temporal** – executed at the same time (e.g., program start-up).
        
    4. **Procedural** – follow a sequence but still different purposes.
        
    5. **Communicational** – operate on the same data set.
        
    6. **Sequential** – output of one is input to the next.
        
    7. **Functional** – everything collaborates to accomplish one, and only one, well-defined function.
        

---

### Coupling – How **Entangled** Different Modules Are

- **Definition**: The degree of **inter-dependence** between two separate modules/classes/functions.
    
- **Goal**: **Low (loose) coupling**—modules know as little as possible about each other.
    
- **Why it matters**
    
    - Facilitates **parallel development**: teams can work on separate modules safely.
        
    - Improves **flexibility**: you can replace or refactor one module without a ripple of changes.
        
    - Simplifies **testing**: you can isolate a component with minimal stubs/mocks.
        
- **Common forms of coupling** (from tightest/worst to loosest/best):
    
    1. **Content coupling** – one module reaches into another’s internals (e.g., `other.privateField`).
        
    2. **Common coupling** – share global variables.
        
    3. **External coupling** – rely on external data formats or device interfaces.
        
    4. **Control coupling** – pass flags that dictate another module’s flow.
        
    5. **Stamp (data-structured) coupling** – share a whole record/struct when only part is needed.
        
    6. **Data coupling** – share only the data actually required (clean parameter lists).
        
    7. **Message/event coupling** – communicate indirectly via events, message queues, or interfaces (loosest).
        

---

### Putting Them Together

- **Ideal target**: **High cohesion + low coupling**.
    
- **Trade-off**: Trying to raise cohesion can **reduce** coupling (good) because well-focused modules expose smaller, clearer interfaces. Sometimes, however, you may split a large low-coupled module into finer ones—increasing cohesion but also creating extra connections. Good design seeks the sweet spot.
    

---

### Quick Example (OO)

|Design|Cohesion|Coupling|
|---|---|---|
|`UserService` that **creates, authenticates, deletes, and emails** users|Low (does many unrelated things)|Low (maybe talks only to DB)|
|Split into `UserRepository`, `AuthService`, `UserNotifier`|Each class does one job → High cohesion|If they share clear interfaces (e.g., pass only user ID) → Low coupling|
|If `AuthService` directly manipulates `UserRepository`’s SQL strings|–|Raises coupling (content/common)|

---

### Tiny Mental Checklist

- When you open a file/class, can you summarize its purpose in one short phrase?
    
    - **Yes ➜ good cohesion.**
        
- If you tweak this piece, how many other files will you probably touch?
    
    - **Few ➜ loose coupling.**

### How to Improve

1. **Refactor** large classes into smaller ones around single responsibilities (SRP).
    
2. **Hide internals** with clear interfaces or patterns (e.g., Facade, Adapter).
    
3. **Pass only what’s needed** (data coupling) rather than whole objects or flags.
    
4. Favor **events/callbacks** over direct calls when modules need to talk but shouldn’t depend heavily on each other.
    

Keeping cohesion and coupling in mind during design and code reviews pays off with systems that are easier to evolve and safer to change.

