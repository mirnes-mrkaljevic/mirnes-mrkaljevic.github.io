---
layout: post
title: "Web API Versioning and Testing"
date: 2025-05-19
tags: [dotnet,dotnet9,cleancode,csharp,softwaredesign,versioning,mocking,unittesting]
series: ".NET Core Web API – Step-by-Step Best Practices"
---

In the [previous post](https://optimalcoder.net/net-core-web-api-services-and-repositories), we have paid attention on our project structure and organizing the code in multiple layers to achieve better readability and easier maintanence in the future. Now we can focus on long-term sustainability through versioning and testing.

In this final part of the series, we will:
- Introduce API versioning so your app can evolve without breaking existing clients
- Add unit tests to validate service and controller behavior


{: .space }
## API Versioning?

Versioning helps your API evolve over time without breaking existing clients. Let’s enable it.

{: .space }
### Step 1: Install Required Package

In the `Sample.Api` project, install:
```bash
dotnet add package Microsoft.AspNetCore.Mvc.Versioning
```
{: .space }
### Step 2: Configure Versioning in Program.cs

In `Program.cs`, add the following:

```bash
    builder.Services.AddApiVersioning(options =>
    {
        options.AssumeDefaultVersionWhenUnspecified = true;
        options.DefaultApiVersion = new ApiVersion(1, 0);
        options.ReportApiVersions = true;
    });

```

This:

- Defaults to version 1.0 when none is specified

- Adds version info in response headers

- Prepares your API to support multiple versions side-by-side


You can version your API using:

- URL segments (/v1/controller)

- Query string (?api-version=1.0)

- Headers (e.g., api-version: 1.0)

We’ll use URL-based versioning in this example.

{: .space }
### Step 3: Create a Versioned Controller

In `Sample.Api/Controllers`, decorate your controller like this:


```csharp

[Route("api/v{version:apiVersion}/[controller]")]
[ApiVersion("1.0")]
[ApiController]
public class UserController : ControllerBase
{
....
```

That’s it—you’ve now added support for versioning via the URL.

{: .space }
## Setting Up a Test Project

Now let’s set up unit testing using NUnit.
{: .space }
### Step 1: Create a New Test Project

In the solution folder:

```bash
dotnet new nunit -n Sample.Api.Tests
```
Then add project references:

```bash
dotnet add Sample.Api.Tests reference Sample.Services
dotnet add Sample.Api.Tests reference Sample.Repositories
dotnet add Sample.Api.Tests reference Sample.DTO
dotnet add Sample.Api.Tests reference Sample.Entities

```

And install these packages:
```bash
dotnet add Sample.Api.Tests package Moq
dotnet add Sample.Api.Tests package Microsoft.AspNetCore.Mvc
```
Next, we can organize our tests in different folders, like `/Services`, `/Repositories`, `/Controllers`

{: .space }
### Step 2: Writing Unit Tests for Services

Now, for the showing purpose, we can test UserService by mocking UserRepository:

```csharp
[TestFixture]
public class UserTests
{
    [Test]
    public void GetUserByUsername_ReturnsDataFromRepo()
    {
        var mockRepo = new Mock<IUserRepository>();
        mockRepo.Setup(r => r.GetUser("Test")).Returns(new User()
        {
            Id = 1,
            Email = "test mail",
            Username = "Test",
        });

        var service = new UserService(mockRepo.Object);
        var user = service.GetUser("Test");

        Assert.That(user.Email, Is.EqualTo("test mail"));
    }
}
```

Here, we have shown one mocking example and how to isolate our service testing from depencency on repository. Similar can be done with controllers as well. For more details on testing and mocking, you can take a look at some of the previous posts for [unit testing](https://optimalcoder.net/unit-testing-and-test-driven-development-tdd-in-csharp) and [mocking](https://optimalcoder.net/tdd-and-mocking).


{: .space }
## Summary

With versioning and testing in place, your API is now ready for real-world use:

- Versioning allows the API to grow without breaking existing clients

- Unit tests help you catch regressions early and keep logic robust

This wraps up our step-by-step guide to building a clean, testable, and production-ready .NET 9 Web API using best practices. Entire code can be found at my [github repository](https://github.com/mirnes-mrkaljevic/net-core-web-api).

Thanks for following the series! 

Happy coding!

