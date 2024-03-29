---
title: Understanding the "const" Keyword in JavaScript. Constant References, Not Values
date: 2024-03-25 00:00 +0330
description: My first post.
category: [Notes]
tags: [javascript, reactjs]
published: true
sitemap: true
---

In JavaScript, the `const` keyword is indeed a bit misleading if interpreted by its name alone. While many might think `const` implies the value it holds is immutable (cannot be changed), what it actually means is that the *reference* to the value cannot be changed. This distinction is crucial, especially when dealing with objects and arrays.

### Example 1: Primitive Values

For primitive values (like numbers, strings, booleans, null, undefined), using `const` works as expected in the sense that the value cannot be changed because these values don't have properties or methods. In this case, the value itself is the reference.

```javascript
const number = 42;
// number = 50; // This will throw an error: TypeError: Assignment to constant variable.

const text = "Hello, world!";
// text = "Goodbye, world!"; // This will throw an error: TypeError: Assignment to constant variable.
```

In the above examples, attempting to reassign `number` or `text` will result in a runtime error because you're trying to change the reference of a `const` variable.

### Example 2: Objects

With objects, the `const` keyword ensures the reference to the object cannot be changed, but the contents of the object can be modified.

```javascript
const person = {
    name: "Alice",
    age: 30
};

// This is allowed:
person.age = 31;
person.city = "New York";

// But this is not allowed and will throw an error:
// person = { name: "Bob" }; // TypeError: Assignment to constant variable.
```

In this example, we can change the properties of the `person` object or even add new properties. However, attempting to assign a new object to `person` will result in a runtime error.

### Example 3: Arrays

Similarly, with arrays, the `const` keyword allows you to modify the contents of the array, but you cannot reassign the array itself.

```javascript
const numbers = [1, 2, 3, 4, 5];

// Modifying the contents of the array is allowed:
numbers.push(6);
numbers[0] = 10;

// But reassigning the array is not allowed and will throw an error:
// numbers = [7, 8, 9]; // TypeError: Assignment to constant variable.
```

In this case, you can add, remove, or change elements within the `numbers` array, but you cannot make `numbers` refer to a different array.

### Conclusion

The `const` keyword in JavaScript guarantees that the variable's reference to a value (the memory address where the value is stored) cannot be changed. This means you cannot reassign a `const` variable. However, if the value is an object or an array, you can still modify the contents of that object or array. This is an important distinction to understand when working with `const` in JavaScript.
