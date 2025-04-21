---
layout: post
title:  "Book Reviews: Clean Code"
date:   2025-01-08
tags: [books,cleancode,softwaredevelopment,designprinciples]
series: "Book reviews"
---
Undoubtedly, one of the most popular books in software engineering, it teaches the fundamentals of software architecture, methods for organizing code, and is widely regarded as a must-read for any software developer. Written by Robert C. Martin, also known as "Uncle Bob," the full title of the book is "_[Clean Code: A Handbook of Agile Software Craftsmanship](https://amzn.eu/d/eoB3OjW)_".

## Key Takeaways

The book emphasizes a few important topics to pay attention to:

- **Readability:** The code should be written in a meaningful manner that is easy to read and understand. It emphasizes the importance of simple, short, clear naming for variables, functions/methods, classes, and other structures for code organization (folders, namespaces, packages), as well as maintaining consistency in naming throughout the application. This also reduces the need to write comments in the code, as the code itself should be self-explanatory. Additionally, it's important to avoid using global variables, as they often introduce unseen or difficult-to-track dependencies, making the code harder to maintain and debug.

- **Simplicity:** Keep it simple and avoid the trap of overengineering to show how smart you are ☺Good code is the code that can be easily maintained.

- **Modularity:** Code should be organized in smaller units easy to change and refactor. Also here important to mention is to make modules based on single responsibility priciple, meaning that one module/unit should not be resposible for more than one task. This principle should be upheld at all architectural levels, considering the appropriate granularity of each module. Granularity, in other words, refers to the abstraction level of a module/unit (such as a function, class, package/namespace, library, etc.).

- **Testability and Refactoring:** These two topics are somehow related. In the process of writing the code that is easy to test, we are constantly refactoring it. Once when all tests are passed, we ensure that we can go with further refactoring if needed, because we have something that backs up the functionality and ensures it won't be broken.

  

## Conclusion

Although there is much more to discuss regarding this book, it wouldn’t be suitable for a single blog post. That’s why we’ll keep it short and simple. One important thing to remember is that the book is not a set of rigid rules, but rather a collection of best practices for writing efficient and maintainable software. The main focus, after efficiency, is maintainability, since we, as developers (except for the lucky ones ☺), spend much more time reading old code than writing new one.
