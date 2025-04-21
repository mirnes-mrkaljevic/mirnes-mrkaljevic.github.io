---
layout: post
title:  "Design Patterns: Decorator"
date:   2025-03-10
tags: [csharp,decorator,designpatterns,softwarearhitecture]
series: Design Patterns

---

The Decorator Pattern is a structural design pattern that allows behavior to be dynamically added to individual objects, without modifying their code. It is often used to extend the functionalities of classes in a flexible and reusable way.

## When To Use It?

- **You Need to Add Behavior Dynamically at Runtime.** Example: A coffee shop app where users can customize their drinks with add-ons like Milk, Sugar, Whipped Cream, etc.
- **You Want to Avoid a Large Inheritance Tree.**  If you use inheritance, each combination of behaviors would require a new subclass (e.g., `CoffeeWithMilk`, `CoffeeWithSugar`, `CoffeeWithMilkAndSugar`, etc.).
- **You Want More Flexible and Reusable Code.** Different decorators can be reused independently. Example: A LoggingDecorator, CompressionDecorator, and EncryptionDecorator can be used separately or in different orders.
- **You Follow the Open-Closed Principle.** Instead of modifying an existing class, you can extend behavior using decorators. This makes your code more maintainable.
- **You Need to Combine Multiple Independent Behaviors.** Example: A text editor where you can apply bold, italics, and underline without modifying the base class.

  

## When Not To Use It?

- **The Object’s Behavior Doesn’t Change at Runtime.** If an object's behavior is fixed and won’t need modifications dynamically.
- **The Number of Possible Combinations is Small**
- **Alternative Patterns Are Simpler.** If you need different behaviors but don’t need stacking, the [strategy](https://mirnes-mrkaljevic.github.io/2025/02/17/design-patterns-strategy.html) pattern may be a better choice. If you have a few known configurations, a [factory](https://mirnes-mrkaljevic.github.io/2025/02/03/design-patterns-factory-method.html) might be more maintainable.
  

## **Example in C#**

**Interface**

```csharp
public interface ICoffee
{
    string GetDescription();
    double GetCost();
}
  
```

**Concrete Component Implementation**

```csharp
public class SimpleCoffee : ICoffee
{
    public string GetDescription()
    {
        return "Simple Coffee";
    }

    public double GetCost()
    {
        return 5.0; // Base price of coffee
    }
}

```

  

**Abstract Decorator**
  

```csharp
public abstract class CoffeeDecorator : ICoffee
{
    protected ICoffee _coffee;

    public CoffeeDecorator(ICoffee coffee)
    {
        _coffee = coffee;
    }

    public virtual string GetDescription()
    {
        return _coffee.GetDescription();
    }

    public virtual double GetCost()
    {
        return _coffee.GetCost();
    }
}

```

  

**Concrete Decorators**
  

```csharp
public class MilkDecorator : CoffeeDecorator
{
    public MilkDecorator(ICoffee coffee) : base(coffee) { }

    public override string GetDescription()
    {
        return _coffee.GetDescription() + ", Milk";
    }

    public override double GetCost()
    {
        return _coffee.GetCost() + 1.5; // Extra cost for milk
    }
}

public class SugarDecorator : CoffeeDecorator
{
    public SugarDecorator(ICoffee coffee) : base(coffee) { }

    public override string GetDescription()
    {
        return _coffee.GetDescription() + ", Sugar";
    }

    public override double GetCost()
    {
        return _coffee.GetCost() + 0.5; // Extra cost for sugar
    }
}

```

  

**Client**
  

```csharp
class Program
{
    static void Main()
    {
        // Create a simple coffee
        ICoffee coffee = new SimpleCoffee();
        Console.WriteLine($"{coffee.GetDescription()} - ${coffee.GetCost()}");

        // Add milk to the coffee
        coffee = new MilkDecorator(coffee);
        Console.WriteLine($"{coffee.GetDescription()} - ${coffee.GetCost()}");

        // Add sugar to the coffee
        coffee = new SugarDecorator(coffee);
        Console.WriteLine($"{coffee.GetDescription()} - ${coffee.GetCost()}");
    }
}

```


## Conclusion 

The Decorator Pattern is a great tool for extending functionality dynamically while keeping the base code clean and modular. It is widely used in the .NET Core framework and its libraries to provide flexible and extendable functionality. Some of the usages are:

- **Middleware Pipeline** ,  app.UseMiddleware()
- **Logging**, `ILogger` with multiple providers (`Console`, `Debug`, etc.)
- **Caching**, `IMemoryCache`, `IDistributedCache`