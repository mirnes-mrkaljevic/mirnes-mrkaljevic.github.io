---
layout: post
title:  "Understanding State Machines with a Practical Example  in C#"
date:   2025-04-21
tags: [cleancode,csharp,designpatterns,statemachine]
---
State machines might sound like a complex topic, but at their core, they’re just a way to manage different states in a system and control how things transition from one state to another. 

In this post, we’ll break down what state machines are, why they’re useful, and how to implement one in C# using a straightforward approach. We’ll also walk through a real-world example to see them in action.

## What is a State Machine?

A state machine is a pattern used to control the flow of a system by defining possible states and the rules that determine how transitions between those states happen. It consists of:

- **States** : Different conditions or modes the system can be in.

- **Transitions** : Rules that define how the system moves from one state to another.

- **Events** : Triggers that cause state transitions.

- **Actions** : Operations performed when entering, exiting, or staying in a state.


## Why Use a State Machine?

State machines provide several benefits when managing workflows:

- **Improved Readability** : Helps organize complex logic in an understandable way.

- **Encapsulation of State Logic** : Keeps state-specific behavior separate from other business logic.

- **Predictability and Maintainability** : Makes it easier to track and debug state changes.

- **Flexibility** : New states and transitions can be added without major changes.
 

## Implementing a State Machine in C&#35;

Let’s implement a simple state machine using a dictionary to manage state transitions.


### Use Case: Order Processing System

Imagine you’re building an e-commerce application that handles order statuses. An order can go through several states before completion.

### States and Transitions

- **States** : `PendingPayment`, `Processing`, `Shipped`, `Delivered`, `Cancelled`

- **Events** : 
   - `Pay` (moves from PendingPayment to Processing)
   - `Ship` (moves from Processing to Shipped)
   - `Deliver` (moves from Shipped to Delivered)
   - `Cancel` (moves from PendingPayment to Cancelled or from Processing to Cancelled)

### C# Implementation

-  **State Machine Logic** :

```csharp
using System;
using System.Collections.Generic;

class OrderStateMachine
{
    public enum OrderState { PendingPayment, Processing, Shipped, Delivered, Cancelled }
    public enum OrderTrigger { Pay, Ship, Deliver, Cancel }

    private Dictionary<(OrderState, OrderTrigger), OrderState> _stateTransitions;
    public OrderState CurrentState { get; private set; }

    public OrderStateMachine()
    {
        CurrentState = OrderState.PendingPayment;
        _stateTransitions = new Dictionary<(OrderState, OrderTrigger), OrderState>
        {
            { (OrderState.PendingPayment, OrderTrigger.Pay), OrderState.Processing },
            { (OrderState.PendingPayment, OrderTrigger.Cancel), OrderState.Cancelled },
            { (OrderState.Processing, OrderTrigger.Ship), OrderState.Shipped },
            { (OrderState.Processing, OrderTrigger.Cancel), OrderState.Cancelled },
            { (OrderState.Shipped, OrderTrigger.Deliver), OrderState.Delivered }
        };
    }

    public void ProcessTransition(OrderTrigger trigger)
    {
        if (_stateTransitions.TryGetValue((CurrentState, trigger), out OrderState newState))
        {
            Console.WriteLine($"Moving from {CurrentState} to {newState} due to {trigger}");
            CurrentState = newState;
        }
        else
        {
            Console.WriteLine($"Invalid transition: {CurrentState} cannot handle {trigger}");
        }
    }
}
```

  

-  **Client** :

```csharp
class Program
{
    static void Main()
    {
        var orderStateMachine = new OrderStateMachine();
        Console.WriteLine($"Initial state: {orderStateMachine.CurrentState}");

        // Simulating order processing
        orderStateMachine.ProcessTransition(OrderStateMachine.OrderTrigger.Pay);
        orderStateMachine.ProcessTransition(OrderStateMachine.OrderTrigger.Ship);
        orderStateMachine.ProcessTransition(OrderStateMachine.OrderTrigger.Deliver);
    }
}
```


### How It Works

1. The `OrderStateMachine` class encapsulates state logic, making the main program cleaner.

2. The `CurrentState` property tracks the current order state.

3. The `_stateTransitions` dictionary holds valid state transitions.

4. The `ProcessTransition()` method checks for valid transitions and updates the state.

5. The `Main` method initializes the state machine and processes transitions.

  

## Conclusion

State machines are an excellent way to manage complex workflows in a clean, predictable manner. Using a simple dictionary in C#, we can build a flexible and maintainable system to handle order states. By encapsulating state logic in a class, we make our code cleaner, more modular, and easier to maintain. Whether you're working on UI workflows, business processes, or game logic, state machines provide a structured approach to managing state transitions effectively.