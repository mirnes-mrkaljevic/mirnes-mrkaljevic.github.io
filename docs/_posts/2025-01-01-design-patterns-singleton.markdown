---
layout: post
title:  "Design Patterns: Singleton"
date:   2025-01-01
categories: csharp,softwaredevelopment,designpatterns,singleton
---
Tyipically the first design pattern most people learn, often wrongly ☺ To give an introduction, we can say that singleton is one of the creational design patterns which ensures only one class instance with single point of access thru entire application. 

Because it is relatively simple to implement, the Singleton pattern is sometimes misapplied in situations where it is not the most suitable choice.


## When to use it?

Here are the few examples of corrent usage of singleton:

- **Configuration Management** Centralized configuration settings for consistent use thru entire application

- **Caching Maintaning**  Single istance of cached objects for easy and fast acces

- **Logging** Ensure unified mechanism to avoid duplication of log files, formats, etc

- **Global State Management**  Centralized management of the state which is needed to be shared accross the application

- **Resource sharing**  Thread pools, database connection, I/O operations
  


## When not to use it?

On the other hand, here are few examples on when  NOT to use it:

- **When you have multiple instances of the application by infrastructure design**. Here you need to be careful because in most cases it won't behave as expected, because every process will have its own instance.

- **When you can solve problem easy without overcomplicating.** Strive to keep your design as simple as possible and avoid unnecessary complexity.

- **When by design you need stateless service.**  Here singleton does more harm than help

- **When application needs to overwrite initialization parameters frequently.** This is clear sign to not use it in this place.

- **High cuncerrency systems.** Here, singleton instance can become bottleneck and actually be missused in order to keep it synced all the time.

  

## Code Example

Here, we will give a thread safe C# implementation of the pattern.

```csharp
public sealed class Singleton
{
    private static Singleton _instance;
    private static readonly object _lock = new object();
    private Singleton()
    {
        // initialization code
    }

    public static Singleton Instance
    {
        get
        {
            if (_instance == null)
            {
                lock (_lock)
                {
                    if (_instance == null)
                    {
                        _instance = new Singleton();
                    }
                }
            }
            return _instance;
        }
    }

    public void Hello()
    {
        Console.WriteLine("Hello from singleton.");
    }
}

class Program
{
    static void Main(string[] args)
    {
        Singleton.Instance.Hello();
    }
}
  
		
```
