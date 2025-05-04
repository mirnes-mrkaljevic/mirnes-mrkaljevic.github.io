---
layout: post
title: "Design Patterns: Adapter"
date: 2025-05-26
tags: [cleancode,csharp,designpatterns,adapter]
series: Design Patterns
---

The Adapter Pattern is a structural design pattern that allows objects with incompatible interfaces to work together. Rather than modifying existing code to fit a new system, the Adapter Pattern lets us wrap it with a new interface—acting as a bridge between mismatched parts of your application.

This approach is especially valuable when integrating legacy systems or third-party APIs that don’t follow your architecture or naming conventions.

## When to Use the Adapter Pattern

You should consider using the Adapter Pattern when:

- You need to integrate an existing class, but its interface doesn't match what your code expects.

- You're modernizing or refactoring a system and want to avoid breaking changes.

- You're working with third-party libraries that can’t be modified.


## Implementing the Prototype Pattern in C&#35;

Lets imagine we are building an application that relies on a clean, simple interface for logging:

```csharp
public interface ILogger
{
    void LogInfo(string message);
}

```

Now imagine you have a legacy logging class written years ago:

```csharp
public class LegacyLogger
{
    public void WriteLogToFile(string severity, string message)
    {
        Console.WriteLine($"[{severity.ToUpper()}] {message} (written to file)");
    }
}
```
These two interfaces are incompatible. The client expects `LogInfo(string)`, but the legacy class only supports `WriteLogToFile(string, string)`.

Instead of modifying either interface, you can use the Adapter Pattern to resolve this mismatch.

### Create the Adapter

```csharp
public class LegacyLoggerAdapter : ILogger
{
    private readonly LegacyLogger _legacyLogger;

    public LegacyLoggerAdapter(LegacyLogger legacyLogger)
    {
        _legacyLogger = legacyLogger;
    }

    public void LogInfo(string message)
    {
        // Adapt method name and parameter structure
        _legacyLogger.WriteLogToFile("info", message);
    }
}
```
Here, `LegacyLoggerAdapter` translates the expected `LogInfo` method into the legacy class's `WriteLogToFile` method.


### Client

Now, our client code can use the `ILogger` interface without knowing anything about the legacy system:


```csharp
class Program
{
    static void Main()
    {
        ILogger logger = new LegacyLoggerAdapter(new LegacyLogger());
        logger.LogInfo("Adapter pattern works!");
    }
}
```

The client gets the expected behavior, and the legacy system remains untouched.


## Benefits

- Decouples client code from implementation details

- Avoids rewriting or breaking legacy systems

- Supports Open/Closed Principle – open to extension, closed to modification

- Makes third-party or legacy classes reusable in new contexts

## When Not to Use It

- When you can directly modify the original interface or class

- When adding an adapter just introduces unnecessary complexity

- If it’s used to hide bad design permanently instead of improving it over time

## Conclusion

The Adapter Pattern is really useful when we need to make incompatible interfaces work together without modifying existing code. This pattern provides clean and maintanable solution in the cases when we need to deal with legacy systems, some external libraries or when we just trying to reuse old components in new ways.

