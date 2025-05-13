---
title: "Functional Core - Imperative Shell (FCIS) — When to Use It"
date: 2025-05-07 00:00 +0330
description: "Functional Core - Imperative Shell (FCIS) — When to Use It"
#image:
#  path: /assets/img/posts/img.png
category: [Architecture and Design Patterns]
tags: [architecture_patterns, design_patterns]
published: true
sitemap: true
---

Functional core/imperative shell is a way of structuring code so that the _heart_ of your program is pure, predictable, and easy to test (the **functional core**), while the messy realities of the outside world—databases, networks, user input—are handled around the edges (the **imperative shell**).

---

#### The Basic Idea (in Plain Language)

1. **Functional core**  
    _Write code that looks like math:_
    
    - It always returns the same result for the same input.
        
    - It doesn’t touch the outside world.
        
    - It has no hidden state.
        
2. **Imperative shell**  
    _Write code that talks to the world:_
    
    - Read files, hit APIs, show UIs.
        
    - Gather the raw data your core needs and pass it in.
        
    - Take the core’s results and push them back out.
        

Put differently:

> Pure functions decide _what_ should happen; the shell decides _how_ and _where_ it actually happens.

---

#### When You **Should** Use It	

|Situation|Why it helps|
|---|---|
|**Business rules / calculations**|Bugs hide in logic, not in I/O; a functional core lets you test that logic without spinning up databases or mocking HTTP.|
|**Code that needs unit tests**|Pure functions are trivial to test → fast feedback loops.|
|**Repeated or concurrent workflows**|Deterministic code is safer to call many times or run in parallel.|
|**Long-lived back-end services**|Easy-to-reason-about cores age well and survive refactors.|

---

#### When You **Shouldn’t** Bother	

|Situation|Why not|
|---|---|
|**Tiny scripts or one-offs**|The ceremony outweighs the gain; just write and ship.|
|**Heavily stateful UIs**|Frameworks already manage state; forcing a pure core can fight the grain.|
|**Hard-real-time systems**|Extra indirection may introduce latency you can’t afford.|
|**Teams unfamiliar with functional ideas**|A pattern nobody understands becomes tech debt, not a blessing.|

---

#### How to Start in Three Small Steps

1. **Draw a line**—decide which functions _must_ touch the outside world. Everything else belongs in the core.
    
2. **Return data, not side effects**—have the core _describe_ changes (e.g., “send this email”) instead of sending the email itself.
    
3. **Wrap the core with an adapter**—the shell takes those descriptions and executes them.
    

---
#### Example

```python
# -------- functional core --------
def decide(action, state):
    """
    Pure: given the current state and an intent,
    return a *description* of what should happen next.
    """
    if action == "deposit":
        amount = state["amount"]
        return {"new_balance": state["balance"] + amount}
    if action == "withdraw":
        amount = state["amount"]
        return {"new_balance": state["balance"] - amount}
    raise ValueError("unknown action")

# -------- imperative shell --------
if __name__ == "__main__":
    import json, sys

    # I/O: read a single JSON blob from stdin, e.g.
    # {"action":"deposit","balance":100,"amount":25}
    state = json.loads(sys.stdin.read())

    # pure decision
    result = decide(state["action"], state)

    # I/O: persist and report
    state["balance"] = result["new_balance"]
    print(json.dumps(state))

```

- **Functional core (`decide`)** – deterministic, stateless, no printing or file work.
    
- **Imperative shell (`__main__`)** – handles JSON, stdin/stdout, persistence.
    
Swap the shell (CLI, web route, queue worker) or test against `decide` directly—no other changes required.


#### A Closing Analogy

Think of your functional core as a **chef’s recipe**: precise, repeatable, pure instructions.  
The imperative shell is the **kitchen staff**: they buy ingredients, heat the stove, and serve the dish.  
Keep the recipe immaculate and the cooking chaotic bits on the periphery, and you’ll ship meals—er, software—that are both reliable and easy to improve.
