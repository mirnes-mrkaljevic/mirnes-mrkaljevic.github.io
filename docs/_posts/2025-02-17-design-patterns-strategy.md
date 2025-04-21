---
layout: post
title:  "Design Patterns: Strategy"
date:   2025-02-17
tags: [cleancode,csharp,designpatterns,strategy]
series: Design Patterns

---
The Strategy Pattern is a behavioral design pattern that defines a family of algorithms, encapsulates each one, and makes them interchangeable. This pattern allows the algorithm to be selected at runtime, providing flexibility in designing software. It’s particularly useful when you have multiple ways of performing a task, and you want to choose the implementation dynamically without altering the client code.

## When To Use It?

- **You Have Multiple Algorithms or Behaviors.** Use it when you have a need for muplitple ways of performing a task, and you want to make these implementations interchangeable. Examples: Different sorting algorithms, payment methods, discount calculations...
- **You Want to Eliminate Conditional Logic.** If you find yourself writing large if-else or switch statements to decide which algorithm to use, this pattern can simplify and clean up your code. Examples: A game character with different attack styles 
- **You Need Runtime Flexibility.** Use this pattern if the choice of algorithm or behavior needs to be decided dynamically at runtime rather than compile-time. Example: A game character that changes its movement strategy based on terrain type.
  

## When Not To Use It?

- **The Behavior is Unlikely to Change.** If the behavior or algorithm is stable and unlikely to require changes, introducing the this pattern may add unnecessary complexity. Example: A basic calculator where operations (add, subtract) are fixed.
- **You Don't Need Runtime Flexibility.** If the algorithm is chosen at compile-time or hardcoded, the strategy pattern is overkill.
- **Strategies Require Too Much Shared State.** If the strategies rely heavily on the internal state of the context class, they can become tightly coupled, defeating the purpose of the pattern.

  

## Key Components

- **Strategy Interface:** Defines a common interface for all the algorithms (strategies).
- **Concrete Strategies:** Implement different versions of the algorithm defined in the strategy interface.
- **Context:** Maintains a reference to a Strategy object and delegates the work to the strategy.

  

## Example in C&#35;

**Interface**

```csharp
public interface IMoveStrategy
{
    void Move();
}

```

**Concrete Strategies**

```csharp
public class Crouch : IMoveStrategy
{
    public void Move()
    {
        Console.WriteLine("Character is crouching slowly...");
    }
}

public class Walk : IMoveStrategy
{
    public void Move()
    {
        Console.WriteLine("Character is walking at a normal pace...");
    }
}

public class Run : IMoveStrategy
{
    public void Move()
    {
        Console.WriteLine("Character is running fast...");
    }
}

```

**Context**

```csharp
public class CharacterContext
{
    private IMoveStrategy _moveStrategy;

    public CharacterContext(IMoveStrategy moveStrategy)
    {
        _moveStrategy = moveStrategy;
    }

    public void SetMovementStrategy(IMoveStrategy moveStrategy)
    {
        _moveStrategy = moveStrategy;
    }

    public void ExecuteMove()
    {
        _moveStrategy.Move();
    }
}

```

**Client**

```csharp
using System;

class Program
{
    static void Main(string[] args)
    {
        // Create a character with an initial movement strategy (Walk)
        CharacterContext character = new CharacterContext(new Walk());

        while (true)
        {
            Console.WriteLine("\nChoose movement: crouch, walk, run, or exit.");
            string input = Console.ReadLine()?.ToLower();

            if (input == "exit")
                break;

            // Manually select the strategy based on user input
            IMoveStrategy selectedStrategy = input switch
            {
                "crouch" => new Crouch(),
                "walk" => new Walk(),
                "run" => new Run(),
                _ => null
            };

            if (selectedStrategy != null)
            {
                character.SetMovementStrategy(selectedStrategy);
                character.ExecuteMove();
            }
            else
            {
                Console.WriteLine("Invalid input! Please enter crouch, walk, or run.");
            }
        }
    }
}

```

  

## Conclusion

The Strategy Pattern provides flexibility, scalability, and cleaner code, making it a great choice for scenarios like game character movement, AI behavior, and dynamic algorithm selection.