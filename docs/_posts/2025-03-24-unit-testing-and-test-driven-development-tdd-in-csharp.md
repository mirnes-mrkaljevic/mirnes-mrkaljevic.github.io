---
layout: post
title:  "Unit Testing and Test-Driven Development (TDD) in C#"
date:   2025-03-24
tags: [cleancode,csharp,tdd,unittesting]
---
Writing code without unit tests can be risky—you might not realize issues until it’s too late. Unit testing helps make sure your code actually does what it's supposed to. In this post, we’ll break down unit testing in C# using NUnit and explore how Test-Driven Development (TDD) can make your life easier.

## What’s Unit Testing Anyway?

Unit testing is all about testing small, isolated chunks of your code to make sure they work correctly. Instead of waiting until the whole application is built and then scrambling to fix bugs, unit tests let you catch issues early. In C#, we typically use frameworks like **NUnit** , **xUnit** , or **MSTest** to write and run these tests.

### Why Should You Care About Unit Testing?

- **Find Bugs Early:** Fixing issues sooner rather than later saves time (and headaches).

- **Make Your Code More Maintainable:** Well-tested code is easier to update and improve.

- **Write Better Code:** Writing testable code forces you to structure it well.

- **Acts as Documentation:** Your tests describe what your code is supposed to do.

  

## What is Test-Driven Development (TDD)?

TDD is a mindset shift where you write your tests **before** you write the actual implementation. It follows a simple cycle:

- Write a test that fails (because the feature doesn’t exist yet).
- Write just enough code to make the test pass.
- Refactor and clean up the code while keeping the test green.
  

### Why Bother with TDD?

- Guarantees better test coverage.

- Helps you write only the code you actually need.

- Saves you time debugging later.

- Makes your code modular and easy to change.

- Every test case serves as a description of a use case, making tests a form of living documentation for your code.

- Encourages better code structure by promoting loose coupling, dependency injection, and composition-based design patterns.

  

## Step-by-Step Guide: TDD with NUnit in C&#35;

### Step 1: Setting Up NUnit in Your Project

Getting started is easy:

- Create a new NUnit Test Project in Visual Studio.
- Install NUnit and NUnit3TestAdapter via NuGet.
- Add a reference to the project containing the code you want to test.
  

### Step 2: Write a Failing Test

Let’s use TDD to build a **Calculator** class with an `Add` method. First, create a test class `CalculatorTests.cs` and add this test:

```csharp
using NUnit.Framework;

[TestFixture]
public class CalculatorTests
{
    [Test]
    public void Add_TwoNumbers_ReturnsSum()
    {
        var calculator = new Calculator();
        int result = calculator.Add(2, 3);
        Assert.AreEqual(5, result); // This test will fail since the method doesn’t exist yet
    }
}

```  

### Step 3: Write Just Enough Code to Pass the Test

Since our `Calculator` class and `Add` method don’t exist yet, let’s create them:

```csharp
public class Calculator
{
    public int Add(int a, int b) => a + b;
}

```

Run the test—it should now pass!

### Step 4: Add More Tests and Implement Subtraction

Next, let’s implement a `Subtract` method using the same TDD approach.

- **Write a test first:**

```csharp
[Test]
public void Subtract_TwoNumbers_ReturnsDifference()
{
    var calculator = new Calculator();
    int result = calculator.Subtract(5, 3);
    Assert.AreEqual(2, result); // This test will fail initially
}

```

- **Write just enough code to pass the test:**

```csharp
public int Subtract(int a, int b) => a - b;

```

- **Refactor if needed to keep your code clean and efficient.**
  

### Step 5: Run and Verify Your Tests

Use Visual Studio’s **Test Explorer** to run all your tests. If they pass, congrats! You’ve successfully followed the TDD cycle.

## How TDD Improves Code Structure

If TDD is applied properly, the following structural benefits emerge:

- **Loose Coupling:** Since tests require isolating components, TDD encourages designing classes that don’t depend too much on each other.

- **Dependency Injection:** Writing testable code often means injecting dependencies instead of hardcoding them, leading to better modularity.

- **Composition Over Inheritance:** Rather than relying too much on inheritance, TDD pushes towards a **composition-based** approach, making code more flexible.

- **Separation of Concerns:** Following the TDD cycle helps ensure each class has a single responsibility, making it easier to maintain and extend.

- **Improved Testability:** The necessity to test first forces developers to create well-structured, reusable components that are easier to test in isolation.

  

### Pro Tips for Writing Good Unit Tests

- Stick to the AAA (Arrange-Act-Assert) pattern to keep tests clean and readable.

- Make tests independent—don’t let them rely on external data or states.

- Use mocking frameworks like Moq to isolate dependencies.

- Keep tests fast and focused—each test should check just one thing.

- Avoid over-testing trivial methods like simple property getters.

  

## Wrapping Up

Unit testing (especially with TDD) makes your code more reliable, easier to maintain, and less prone to bugs. Since each test case describes a specific use case, they double as a form of documentation for how your code is expected to behave. Additionally, applying TDD properly leads to better code structure through **dependency injection, loose coupling, and composition-based patterns**. 

In the next post, we'll dive deeper into **mocking** —an essential technique for isolating dependencies and making unit tests more effective. Stay tuned!