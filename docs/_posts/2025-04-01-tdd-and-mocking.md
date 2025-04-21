---
layout: post
title:  "TDD and Mocking"
date:   2025-04-01
tags: [cleancode,csharp,tdd,unittesting,dependencyinjection,moq]
series: "Unit testing and TDD"

---
In the [last post](https://mirnes-mrkaljevic.github.io/2025/03/24/unit-testing-and-test-driven-development-tdd-in-csharp.html) we have discussed core principles of unit testing and TDD. In this post, we’ll walk through a simple example of how to use **Moq**  as mocking framework and **Dependency Injection** with **NUnit** in the context of **Test-Driven Development (TDD)** in C#. This combination allows for clean, maintainable tests by mocking dependencies and focusing on the behavior of the system under test.

Imagine we have an application where we process payments through a `PaymentService`. This service depends on an external `IPaymentGateway` interface to make the actual payment. 

Let’s break this down step by step.

### Step 1: Write the Test First (Before Code)

We want to test the `PaymentService`, which depends on the `IPaymentGateway`. Our first test will focus on making sure the `PaymentService` behaves correctly when making a valid payment.

Here's the test code:

```csharp
using Moq;
using NUnit.Framework;

[TestFixture]
public class PaymentServiceTests
{
    private Mock<IPaymentGateway> _mockPaymentGateway;
    private PaymentService _paymentService;

    [SetUp]
    public void Setup()
    {
        // Create the mock object
        _mockPaymentGateway = new Mock<IPaymentGateway>();

        // Inject the mock into the PaymentService
        _paymentService = new PaymentService(_mockPaymentGateway.Object);
    }

    [Test]
    public void MakePayment_WhenAmountIsValid_ShouldReturnTrue()
    {
        // Arrange
        var amount = 100m; // Valid amount
        _mockPaymentGateway.Setup(gateway => gateway.ProcessPayment(amount)).Returns(true);

        // Act
        var result = _paymentService.MakePayment(amount);

        // Assert
        Assert.IsTrue(result);
    }
}

```


### Step 2: Run the Test (It Will Fail)

Since we haven’t implemented the `PaymentService` or the `IPaymentGateway` yet, running the test will result in a failure. The error message would indicate that the `PaymentService` and `IPaymentGateway` don’t exist yet.

Now we need to implement the minimal code to make this test pass.

### Step 3: Write the Code to Pass the Test

We need to create the `PaymentService` and `IPaymentGateway` interface so that the test can run successfully.

Here’s the implementation of the `IPaymentGateway` interface and the `PaymentService` class:

```csharp
public interface IPaymentGateway
{
    bool ProcessPayment(decimal amount);
}public class PaymentService
{
    private readonly IPaymentGateway _paymentGateway;

    public PaymentService(IPaymentGateway paymentGateway)
    {
        _paymentGateway = paymentGateway;
    }

    public bool MakePayment(decimal amount)
    {
        // Business logic for validating amount (e.g., check if it's positive)
        if (amount <= 0)
        {
            return false;
        }

        // Process the payment through the gateway
        return _paymentGateway.ProcessPayment(amount);
    }
}

```


### Step 4: Run the Test Again (It Should Pass)

Now that we've implemented the `PaymentService` and `IPaymentGateway`, we can run the test again.

If everything is set up correctly, this time the test should pass. The output should indicate that the `MakePayment` method of the `PaymentService` is functioning as expected, returning `true` when a valid amount is passed and the gateway returns `true`.

### Step 5: Write Another Test (The Test-Grow Approach)

The next step in TDD is to write another test for a different scenario. In this case, we will test when an invalid payment amount is passed (e.g., a negative or zero amount). Let's write this test.

Here’s the next test:

```csharp
[Test]
public void MakePayment_WhenAmountIsInvalid_ShouldReturnFalse()
{
    // Arrange
    var amount = -50m; // Invalid amount
    _mockPaymentGateway.Setup(gateway => gateway.ProcessPayment(It.IsAny<decimal>())).Returns(false);

    // Act
    var result = _paymentService.MakePayment(amount);

    // Assert
    Assert.IsFalse(result);
}

```

### Step 6: Refactor the Code if Needed

Now, we have a test for the invalid case, and we should implement any additional logic if necessary to make this test pass. In our current code, the logic already returns `false` if the amount is less than or equal to `0`. This means the code is already correct, and we don't need to change anything.


## Conclusion

This example demonstrates how to apply Test-Driven Development in C# with NUnit, Moq, and Dependency Injection. By mocking dependencies, you can easily test the behavior of your services without relying on the real implementations, making your tests faster and more reliable. Dependency injection ensures that dependencies are easily replaceable, promoting clean, maintainable code.