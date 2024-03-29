---
title: Why Use Django's "AbstractBaseUser" and Not Use the "User" Model Directly?
date: 2024-03-29 00:00 +0330
description: My first post.
category: [Notes]
tags: [python, django, backend]
published: true
sitemap: true
---

In Django, an `AbstractBaseUser` is a base class provided by Django's authentication framework that allows you to create a custom User model by extending it. This is especially useful when you need a user model that goes beyond what is provided by Django's default `User` model.

### Why Use `AbstractBaseUser`?

Django's built-in `User` model is designed to handle common use cases, but there might be situations where its fields and methods do not align with the needs of your application. For example, you might want to use an email address as the primary user identifier instead of a username. In such cases, extending `AbstractBaseUser` allows you to define your own User model with the specific fields and behaviors your application requires.

### Why Not Use the Django `User` Model Directly?

There are several reasons why you might not want to use the Django `User` model directly:

1. **Custom User Requirements:** If your application needs user fields that aren't provided by the default `User` model, such as a birthdate, you'd need to extend the User model.
2. **Custom Authentication:** You may want to authenticate users based on email addresses or some other system instead of the traditional username and password combination.
3. **Flexibility:** By defining your own user model, you have full control over the user attributes and methods, making your application more flexible.

### How to Use `AbstractBaseUser`

To create a custom User model using `AbstractBaseUser`, you would typically follow these steps:

1. **Define Your Custom User Model:** Subclass `AbstractBaseUser` and add any additional fields you require.
2. **Specify the `USERNAME_FIELD`:** This is required by Django's authentication framework to denote the field used as the unique identifier for authentication.
3. **Optionally, Specify `REQUIRED_FIELDS`:** These are the fields required when creating a user via the `createsuperuser` command, aside from the password and the field specified in `USERNAME_FIELD`.
4. **Implement `get_full_name()` and `get_short_name()`:** These methods are required to be compatible with Django's user model.
5. **Manage Users and Permissions:** Optionally, you can also extend `PermissionsMixin` to manage user permissions.

### Example

Here's a basic example of how you might define a custom user model that uses an email address as the primary identifier:

```python
from django.contrib.auth.models import AbstractBaseUser, BaseUserManager, PermissionsMixin
from django.db import models

class MyUserManager(BaseUserManager):
    def create_user(self, email, password=None, **extra_fields):
        if not email:
            raise ValueError('The Email field must be set')
        email = self.normalize_email(email)
        user = self.model(email=email, **extra_fields)
        user.set_password(password)
        user.save(using=self._db)
        return user

    def create_superuser(self, email, password=None, **extra_fields):
        extra_fields.setdefault('is_staff', True)
        extra_fields.setdefault('is_superuser', True)

        return self.create_user(email, password, **extra_fields)

class MyUser(AbstractBaseUser, PermissionsMixin):
    email = models.EmailField(unique=True)
    is_active = models.BooleanField(default=True)
    is_staff = models.BooleanField(default=False)

    objects = MyUserManager()

    USERNAME_FIELD = 'email'
    REQUIRED_FIELDS = []

    def get_full_name(self):
        return self.email

    def get_short_name(self):
        return self.email

# Don't forget to set AUTH_USER_MODEL in your settings.py to point to your new model
# AUTH_USER_MODEL = 'myapp.MyUser'
```

In this example, `MyUser` is the custom user model with `email` as the unique identifier. `MyUserManager` is a custom manager that provides helper methods for creating users or superusers.

Remember, after defining a custom user model, it's crucial to set the `AUTH_USER_MODEL` setting in your `settings.py` to point to your new model. This tells Django to use your custom model instead of the built-in `User` model for authentication and throughout your Django project.
