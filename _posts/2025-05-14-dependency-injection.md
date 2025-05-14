---
title: "Dependency Injection in Plain Language"
date: 2025-05-14 00:00 +0330
description: "Dependency Injection in Plain Language"
#image:
#  path: /assets/img/posts/img.png
category: [Architecture and Design Patterns]
tags: [architecture_patterns, design_patterns]
published: true
sitemap: true
---

Dependency injection (DI) is a design pattern that keeps two ideas in balance:

1. **Separation of concerns** – a class shouldn’t know the concrete details of the things it depends on.
    
2. **Inversion of control** – instead of a class constructing or looking-up its collaborators, someone else hands those collaborators (“dependencies”) to it.
    

The result is code that is easier to test, swap out, and extend, because the pieces are loosely coupled.

---

### The Problem Without DI

```python
class EmailSender:
    def send(self, to, subject, body):
        print(f"→ Email to {to} | {subject}\n{body}")


class OrderService:
    # 🔴 Directly *creates* its own EmailSender
    def __init__(self):
        self.email = EmailSender()

    def place_order(self, user_email, items):
        # ...save order...
        self.email.send(user_email,
                        "Order confirmed",
                        f"You bought {', '.join(items)}")
```

_Why this hurts_:  
`OrderService` is welded to `EmailSender`. If you need an SMS sender, a mock for unit tests, or a fancier email provider, you must edit `OrderService`.

---

### Constructor Injection (Most Common)

```python
class OrderService:
    def __init__(self, notifier):          # dependency comes *from outside*
        self.notifier = notifier

    def place_order(self, user_email, items):
        # ...save order...
        self.notifier.send(user_email,
                           "Order confirmed",
                           f"You bought {', '.join(items)}")


# --- wiring the graph ---
email_sender = EmailSender()
order_service = OrderService(email_sender)   # inject
```

Now `OrderService` doesn’t care _how_ messages are sent, only that the object obeys `.send()`.

---

### Swapping Dependencies

```python
class DummyNotifier:
    def __init__(self):
        self.outbox = []

    def send(self, to, subject, body):
        self.outbox.append((to, subject, body))


def test_place_order_sends_confirmation():
    dummy = DummyNotifier()
    svc = OrderService(dummy)

    svc.place_order("alice@example.com", ["book", "pen"])

    assert dummy.outbox == [
        ("alice@example.com", "Order confirmed", "You bought book, pen")
    ]
```

Because the dependency is injected, unit tests stay in-memory and deterministic.

---

### When Frameworks Help

Many Python frameworks (FastAPI, Django with third-party libraries, or dependency-injector, punq, etc.) include containers that:

- build a graph of objects and their constructor parameters,
    
- resolve and inject them at runtime,
    
- optionally provide scopes (per-request, singleton, …).
    

That removes most of the manual “wiring” code while preserving decoupling.

---

### Key Takeaways

- **DI ≠ a library** – it’s a way of structuring code; containers just automate it.
    
- Inject via the constructor first; fall back to setter or method injection only when necessary.
    
- Favor **interfaces (protocols/ABCs)** instead of concrete classes for dependencies.
    
- Your tests become simpler mocks + assertions rather than heavyweight integration setups.
