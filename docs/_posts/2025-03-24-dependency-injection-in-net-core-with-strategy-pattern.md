---
layout: post
title:  "Dependency Injection In .NET Core With Strategy Pattern"
date:   2025-03-24
tags: [csharp,dependencyinjection,netcore,strategy]
---
In the previous post, we gave an introduction and explained the basic concept of the [Strategy Pattern](https://optimalcoder.net/design-patterns-strategy). Now, we want to go a bit further and demonstrate how it works in practice alongside a dependency injection in .NET Core. Instead of manually instantiating strategies, we let the ASP.NET Core DI container inject the correct implementation at runtime.

## Example

For the showing purposes, let us asume that we are implementing some payment service, and we want to use one service at the time, depending on user input.

  

For our strategy pattern, we would need following components:

  

**Interface**

```csharp
public interface IPaymentStrategy
{
    void Pay(decimal amount);
}
```

**Concrete Strategies**

```csharp
public class CreditCardPayment : IPaymentStrategy
{
    public void Pay(decimal amount)
    {
        Console.WriteLine($"Paid ${amount} using Credit Card.");
    }
}

public class PayPalPayment : IPaymentStrategy
{
    public void Pay(decimal amount)
    {
        Console.WriteLine($"Paid ${amount} using PayPal.");
    }
}

public class BitcoinPayment : IPaymentStrategy
{
    public void Pay(decimal amount)
    {
        Console.WriteLine($"Paid ${amount} using Bitcoin.");
    }
}

```

**Context**

```csharp
public interface IPaymentContext
{
    void ExecutePayment(string paymentMethod, decimal amount);
}
public class PaymentContext : IPaymentContext
{
    private readonly IServiceProvider _serviceProvider;

    public PaymentContext(IServiceProvider serviceProvider)
    {
        _serviceProvider = serviceProvider;
    }

    public void ExecutePayment(string paymentMethod, decimal amount)
    {
        IPaymentStrategy strategy = paymentMethod switch
        {
            "creditcard" => _serviceProvider.GetService<CreditCardPayment>(),
            "paypal" => _serviceProvider.GetService<PayPalPayment>(),
            "bitcoin" => _serviceProvider.GetService<BitcoinPayment>(),
            _ => throw new ArgumentException("Invalid payment method")
        };

        strategy.Pay(amount);
    }
}

```

  

Next, we are implementing our controller:

  

```csharp
using Microsoft.AspNetCore.Mvc;

[Route("api/[controller]")]
[ApiController]
public class PaymentController : ControllerBase
{
    private readonly IPaymentContext _paymentContext;

    public PaymentController(IPaymentContext paymentContext)
    {
        _paymentContext = paymentContext;
    }

    [HttpPost("{paymentMethod}")]
    public IActionResult ProcessPayment(string paymentMethod, [FromBody] decimal amount)
    {
        _paymentContext.ExecutePayment(paymentMethod.ToLower(), amount);

        return Ok($"Payment of ${amount} processed using {paymentMethod}.");
    }
}

```

  

And, we register each payment strategy in Program.cs.

  

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

var builder = WebApplication.CreateBuilder(args);

// Register concrete strategies
builder.Services.AddTransient<CreditCardPayment>();
builder.Services.AddTransient<PayPalPayment>();
builder.Services.AddTransient<BitcoinPayment>();
builder.Services.AddTransient<IPaymentContext, PaymentContext>();


// Register controllers
builder.Services.AddControllers();

var app = builder.Build();

app.UseHttpsRedirection();
app.UseAuthorization();
app.MapControllers();

app.Run();

```

  

## How It Works

- The DI container registers each strategy (CreditCardPayment, PayPalPayment, BitcoinPayment).
- The controller receives an IPaymentContext, which allows it to dynamically resolve the appropriate payment strategy.
- When a user makes a payment request, the context dynamically selects the strategy based on the URL parameter (creditcard, paypal, bitcoin).
- The selected strategy is injected into the PaymentContext and executed.
  

## Conclusion

Finally, we can highlight some key points on why this approach offers significant benefits to the code structure.

- **Loose Coupling** – The controller does not depend on concrete implementations, making the code more flexible.
- **Easy Maintenance** – New payment methods can be added without modifying existing code.
- **Testability** – The strategies can be mocked and tested independently.
- **Runtime Flexibility** – The payment method can be selected dynamically at runtime.