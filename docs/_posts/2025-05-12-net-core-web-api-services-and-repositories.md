---
layout: post
title: "Web API Architecture: Services and Repositories"
date: 2025-05-12
tags: [dotnet,dotnet9,cleancode,csharp,softwaredesign,services,repository,sqlkata,dapper]
series: ".NET Core Web API – Step-by-Step Best Practices"
---

In the [previous post](https://optimalcoder.net/net-core-web-api-exceptions-and-logger), we added global error handling and logging using NLog. Now that we’ve made our API more resilient, it’s time to go forwards with cleaning up the architecture and separate concerns.

In this post, we’ll cover:
- Introduce a clean project  structure
- Create Service and Repository layers
- Register dependencies with Dependency Injection
- Hook everything up in a simple API endpoint

This sets the foundation for building a maintainable and testable API going forward.


{: .space }
## Why Clean Architecture?

A clean arhitecture helps us keeping business logic separate from infrastructure layer and make our 
project easier to test and extend and, in the end, it improves readability and maintanability over time.

{: .space }
## Proposed Project Structure

We’ll split the solution into multiple class libraries to keep things modular and clean:

- `Sample.Api` contains API-specific setup like controllers and middleware.

- `Sample.Services` includes business logic, independent of controllers or data sources.

- `Sample.Repositories` handles communication with the database.

- `Sample.Entities` contains models that map directly to your database schema.

- `Sample.DTO` defines what’s exposed via API—keeping entities encapsulated.


Each layer references only what it needs. For example:

- `Sample.Services` references `Sample.Repositories`, `Sample.DTO`, `Sample.Entities`

- `Sample.Repositories` references `Sample.Entities`

- `Sample.Api` references `Sample.Services`, `Sample.Repositories` and `Sample.DTO`


### Lets Create Migration With Some Test Data

Update your Migrations with:

```csharp
    [Migration(20250503001)]
    public class Mig20250503001_UsersTestData : Migration
    {
        public override void Down()
        {
            throw new NotImplementedException();
        }

        public override void Up()
        {
            Insert.IntoTable("Users").Row(new
            {
                Username = "john.doe",
                Email = "john.doe@email.com"
            });
            
        }
    }
}
```

### Repository

In the repository project we need to add SqlKata library. More info about SqlKata can be found in one of my [earlier post](https://optimalcoder.net/dapper-on-steroids-sqlkata)

```csharp
using Sample.Entities;
using SqlKata.Execution;

namespace Sample.Repositories
{
    public interface IUserRepository
    {
        int InsertNewUser(User user);
        User GetUser(int userId);
        User GetUser(string username);
    }
    public class UserRepository : IUserRepository
    {
        private readonly QueryFactory _db;
        private const string TableName = "Users";

        public UserRepository(QueryFactory db)
        {
            _db = db;
        }

        public int InsertNewUser(User user)
        {
            return _db.Query(TableName).InsertGetId<int>(user);
        }


        public User GetUser(int userId)
        {
            return _db.Query(TableName).Where(nameof(User.Id), userId).Get<User>().Single();
        }

        public User GetUser(string username)
        {
            return _db.Query(TableName).Where(nameof(User.Username), username).Get<User>().Single();

        }
    }
}

```

### Service

```csharp
namespace Sample.Services
{
    public interface IUserService
    {
        int InsertNewUser(UserDTO user);
        UserDTO GetUser(string username);
    }
    public class UserService : IUserService
    {
        private readonly IUserRepository _repository;
        public UserService(IUserRepository repository)
        {
            _repository = repository;
        }

        public UserDTO GetUser(string username)
        {
            var entity = _repository.GetUser(username);
            return new UserDTO()
            {
                Username = entity.Username,
                Email = entity.Email
            };
        }

        public int InsertNewUser(UserDTO user)
        {
            var entity = new User()
            {
                Username = user.Username,
                Email = user.Email
            };
            return _repository.InsertNewUser(entity);        }
    }
}

```
Here is just a simple implementation given, but the base idea is for service classes to manage interaction between `Repositories` and `Api Controllers` and do all the things in between about mapping data and additional business logic.


### Register all in  `Program.cs`
```csharp

....

builder.Services.AddScoped(provider =>
{
    var connection = new SqlConnection(connectionString);

    var compiler = new SqlServerCompiler();

    return new QueryFactory(connection, compiler);
});

builder.Services.AddScoped<IUserRepository, UserRepository>();

builder.Services.AddScoped<IUserService, UserService>();

....
```


### Controller

```csharp
namespace Sample.Api.Controllers
{
    [Route("[controller]")]
    [ApiController]
    public class UserController : Controller
    {

        private readonly IUserService _service;
        public UserController(IUserService service)
        {
            _service = service;
        }

        public UserDTO GetUserByUsername(string username)
        {
            return _service.GetUser(username);
        }
    }
}
```


{: .space }
## Summary

With the new project structure in place:

- Business logic lives in the service layer

- Data access is cleanly separated in a repository

- Models are split into entities and DTOs

- The API is minimal and focused on routing and HTTP interaction

This is a scalable foundation that makes future growth and testing straightforward.

{: .space }
## Coming Up Next…

In Part 4, we’ll focus more on testing and API versioning.

Stay tuned!
