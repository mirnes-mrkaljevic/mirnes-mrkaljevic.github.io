---
layout: post
title:  "Composition Over Inheritence"
date:   2025-02-10
tags: [composition,csharp,designpatterns,inheritence]
---
Inheritence is often misused or overused or even forced for several reasons, and they are mostly due to education, misunderstanding or habit. Many developers who studied OOP are introduced with inheritence in early stage and then it stays with them as a default way how to build relationships between objects. Escaping this matrix can be quite challenging, as lacking alternative perspectives often leads to the trap of believing that inheritance is the solution to everything. Here, we will try to find the reasoning behind this way of thinking.

  

## Historical Influence

Early OOP examples often revolved around "is-a" relationships like `Car -> Vehicle, Dog -> Animal`, reinforcing inheritance as the primary method of code reuse. We can see this in early OOP literature, such as the influential "Gang of Four Design Patterns",  where inheritance is prominently discussed. And then, as a result, developers internalized it as the default design approach.

  

## Inheritance = Code Reuse (Misconception)

Many developers learn that inheritance is the primary way to reuse code. They might think: “If I need to reuse functionality, I must derive from a base class.” This mindset leads to deep hierarchies, tight coupling, even when simpler or more modular solutions (like composition) would work better.

## Simplicity of Implementation

Inheritance is Easy to Implement - defining a base class and extending it is straightforward, both conceptually and in terms of syntax. Composition requires more design work, such as defining interfaces or injecting dependencies. Let's show the difference in one simple example:

**Inheritence**

```csharp
public class Vehicle 
{ 
	public void Move() 
	{ 
		Console.WriteLine("Moving"); 
	} 
}
public class Car : Vehicle { }
```

  

**Composition**

  

```csharp
public interface IMovable 
{ 
	void Move(); 
}

public class Car 
{ 
	private readonly IMovable _movable; 
	public Car(IMovable movable) 
	{ 
		_movable = movable; 
	} 
}
```

## Legacy code

Older systems were built using inheritance-heavy designs, and developers working on these systems are forced to maintain and extend these hierarchies.

  

## Frameworks that Encourage Inheritance

Many frameworks historically relied on inheritance (e.g., GUI frameworks like Swing or .NET's WinForms) where developers extended base classes to customize behavior.  
Developers often mimic these patterns without questioning if they're the best solution.

  

## How to Shift Away from Overusing Inheritance

**Educate About Composition:**

Teach patterns like Strategy, Decorator, and Dependency Injection as alternatives to deep hierarchies. Example: Instead of a Car base class, use a `IMovable, IEngine` interface and inject behavior dynamically.  

**Promote Design Principles:**

Follow SOLID principles, especially: 

- Single Responsibility: Avoid bloated base classes. 
- Dependency Inversion: Prefer abstractions over specific implementations.

**Use Interfaces and Delegation:**

Example: Instead of `class Car : Vehicle`, use `Car` with a composable `IEngine` or `IMovable`.

  

**Emphasize Refactoring:**

  

Refactor overly complex hierarchies into flatter, modular designs using composition.

  

## Conclusion

Inheritance is often "forced" because it's historically emphasized, easy to use, and aligns with developers' early understanding of OOP. However, it has significant limitations that make composition a more flexible, scalable, and maintainable choice in many scenarios. By learning and applying composition patterns, developers can avoid rigid hierarchies and design systems that are more robust and adaptable.