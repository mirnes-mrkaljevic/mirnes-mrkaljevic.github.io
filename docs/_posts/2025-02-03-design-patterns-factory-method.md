---
layout: post
title:  "Design Patterns: Factory Method"
date:   2025-02-03
tags: [csharp,cleancode,designpatterns,factorymethod]
series: "Design Patterns"

---

Let’s continue our series on design patterns with the Factory Method. Like the [Builder](https://optimalcoder.net/design-patterns-builder) or [Singleton](https://optimalcoder.net/design-patterns-singleton) patterns we covered in previous posts, the Factory Method is also one of the creational patterns. It introduces a slightly more complex approach, offering a higher level of abstraction for object creation. Additionally, it helps decouple the object creation process from the rest of the application.

It can be represented through the following components:

- **Abstract object** , which represents the base class for the objects that we want to create
- **Concreate objects** , classes that inherit the given base class
- **Creator** , abstraction of the classes resposible for the object creation
- **Concreate creators,** classes that inherit and implement given creator class
- **Client or Consumer side** , parts of the application where we call objects creation
Bellow, we will show all these parts in one simple example.
  

```csharp
using System;

// abstract object
abstract class Engine
{
    public abstract void Start();
}

// Concrete object, concrete Engine classes
class DieselEngine: Engine
{
    public override void Start()
    {
        Console.WriteLine("Starting diesel engine!");
    }
}

class PetrolEngine: Engine
{
    public override void Start()
    {
        Console.WriteLine("Starting petrol engine!");
    }
}

// Creator
abstract class EngineFactory
{
    public abstract Engine CreateEngine();
}

// Concrete Creators
class DieselEngineFactory : EngineFactory
{
    public override Engine CreateEngine()
    {
        return new DieselEngine();
    }
}

class PetrolEngineFactory : EngineFactory
{
    public override Engine CreateEngine()
    {
        return new PetrolEngine();
    }
}

// Client code
class Program
{
    static void Main(string[] args)
    {
        EngineFactory factory;

        // Let's assume user chooses the engine type at runtime.
        Console.WriteLine("Enter the type of engine (diesel/petrol):");
        string choice = Console.ReadLine()?.ToLower();

        if (choice == "diesel")
        {
            factory = new DieselEngineFactory();
        }
        else if (choice == "petrol")
        {
            factory = new PetrolEngineFactory();
        }
        else
        {
            Console.WriteLine("Invalid choice!");
            return;
        }

        Engine engine = factory.CreateEngine();
        engine.Start();
    }
}

```

  

In the example above, we can see the simple case where this pattern could be applied. Now, when we want to add a new engine type, we just need to extend existing logic with new engine type, without change of existing code resposible for Diesel or Petrol engines. Here we can see the flexibility of such approach, but of course, in some cases this can be the overhead. Examples of the overhead could be when we have the cases for simple object creation or when we know that extension of existing objects creation won't happen in the future. In such cases, this pattern would be just an overengineering and not necessary at all.