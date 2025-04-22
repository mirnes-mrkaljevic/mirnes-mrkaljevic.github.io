---
layout: post
title: "Web API Project Setup and Migrations"
date: 2025-04-28
tags: [dotnet9,cleancode,csharp,designpatterns,sofwaredevelopment,softwaredesign,fluentmigrator,migrations]
series: "Web API – Step-by-Step Best Practices: Part 1"
---

Welcome to the first post in the **“Web API – Step-by-Step Best Practices”** series. In this series, we’ll walk through building a modern, clean, and maintainable Web API using .NET 9. Each post will focus on a specific aspect—from project setup and architecture to testing, migrations, and deployment.


![Software Design](/assets/images/flowchart-design.jpg)

{: .space }
## In This Post

We'll cover:

- Creating a new .NET 9 Web API project  
- Understanding the default project structure  
- Setting up **FluentMigrator** using a **separate migrations library**  
- Writing and running your first database migration  



{: .space }
## Step 1: Create the API Project

Start by creating a new Web API project using the .NET CLI:

```bash
dotnet new webapi -n Sample.Api
cd Sample.Api
```

{: .space }
## Understanding the Project Structure

After creating the project, you’ll see something like this:

```
Sample.Api/
├── Controllers/
│   └── WeatherForecastController.cs
├── Program.cs
├── appsettings.json
└── Sample.Api.csproj
```
{: .space }
### `Program.cs`

This is the entry point of the app. It sets up services and configures the request pipeline. Here's the full code of `Program.cs`:

```csharp
var builder = WebApplication.CreateBuilder(args);

// Add services to the container.
builder.Services.AddControllers();
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

var app = builder.Build();

// Configure the HTTP request pipeline.
if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}

app.UseAuthorization();

app.MapControllers();

app.Run();
```

Let’s break it down:

- `var builder = WebApplication.CreateBuilder(args);` initializes the app's builder, where you configure services and settings. The `args` parameter allows command-line arguments to be passed in.
  
- `builder.Services.AddControllers();` adds support for controllers, enabling your Web API to handle incoming HTTP requests.

- `builder.Services.AddEndpointsApiExplorer();` and `builder.Services.AddSwaggerGen();` add Swagger support for generating API documentation. This is useful for testing and exploring your API.

- `var app = builder.Build();` builds the `WebApplication` object, finalizing the app configuration and preparing it for running.

- `if (app.Environment.IsDevelopment()) { app.UseSwagger(); app.UseSwaggerUI(); }` enables Swagger UI for development environments, making it easy to explore and test the API.

- `app.UseAuthorization();` adds middleware for handling authorization, which is necessary if your API needs to control access to certain routes.

- `app.MapControllers();` maps the controller routes to the HTTP request pipeline, allowing the API to handle requests.

- `app.Run();` starts the app and begins listening for incoming requests.


### `appsettings.json`

This file is used for configuration—stuff like connection strings, logging, and environment-specific settings:

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=localhost;Database=SampleDb;Trusted_Connection=True;TrustServerCertificate=True"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```


### `Controllers/`

This folder holds your API endpoints. It’s where we’ll define controllers, but we’ll keep them thin by offloading logic to other layers.


{: .space }
## Step 2: Create a Separate Migrations Project

We want to keep our code **modular and focused**, so we’ll separate database migration logic from our main Web API project. That means creating a **dedicated class library** just for migrations.

{: .space }
###  Why put migrations in a separate class library?

Here are a couple of solid reasons:

- **Separation of concerns**: Your Web API project should focus on handling HTTP requests and responses—not managing database schema. Keeping migrations in their own project ensures the API stays lean and focused.
- **Cleaner dependencies**: The migration project will only reference what's needed for database changes.
- **Better organization**: As your project grows, having migrations tucked away in a dedicated folder or project keeps things easier to manage and navigate.


{: .space }
### Create the migration library

Let’s create the class library:

```bash
dotnet new classlib -n Sample.Migrations
```

Then install the FluentMigrator packages:

```bash
cd Sample.Migrations
dotnet add package FluentMigrator.Runner
dotnet add package FluentMigrator.Extensions.SqlServer
```

Now go back to your Web API project and link it to the migration library:

```bash
cd ../Sample.Api
dotnet add reference ../Sample.Migrations/Sample.Migrations.csproj
```

At this point, your solution structure looks like this:

```
Sample.Api/
├── Program.cs
├── appsettings.json
└── ...

Sample.Migrations/
└── Sample.Migrations.csproj
```

Your API handles HTTP traffic. Your migration project takes care of database schema changes. Nice and clean!


{: .space }
## Step 3: Create Your First Migration

Inside the `Sample.Migrations` project, create folder `Migrations/`, and add this migration class:

```csharp
using FluentMigrator;

namespace Sample.Migrations.Migrations;

[Migration(20250421001)]
public class InitialMigration : Migration
{
    public override void Up()
    {
        Create.Table("Users")
            .WithColumn("Id").AsInt32().PrimaryKey().Identity()
            .WithColumn("Username").AsString(100).NotNullable()
            .WithColumn("Email").AsString(200).NotNullable();
    }

    public override void Down()
    {
        Delete.Table("Users");
    }
}
```

{: .space }
### About the `[Migration]` attribute

The number you pass into the `[Migration(...)]` attribute is a unique ID for the migration. A common and helpful naming convention is to use a **timestamp** format, followed by a sequence number. Example:

```
YYYYMMDDHHmm + sequence
```

So in our case:

```
20250421001
```

That means this is the first migration created on April 21, 2025. This format is nice because:

- Migrations are naturally sorted in order
- You can tell when each migration was created at a glance
- It avoids name conflicts even if multiple migrations are added in the same day


{: .space }
## Step 4: Configure FluentMigrator in the Web API

Back in `Program.cs` of `Sample.Api`, register FluentMigrator and point it to the `Sample.Migrations` assembly:

```csharp
using FluentMigrator.Runner;
using Sample.Migrations.Migrations;

var builder = WebApplication.CreateBuilder(args);

// Add services
builder.Services.AddControllers();
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

// Configure FluentMigrator using the separate migrations project
builder.Services.AddFluentMigratorCore()
    .ConfigureRunner(rb => rb
        .AddSqlServer()
        .WithGlobalConnectionString(
            builder.Configuration.GetConnectionString("DefaultConnection")!)
        .ScanIn(typeof(InitialMigration).Assembly).For.Migrations())
    .AddLogging(lb => lb.AddFluentMigratorConsole());

var app = builder.Build();

// Apply pending migrations on startup
using (var scope = app.Services.CreateScope())
{
    var runner = scope.ServiceProvider.GetRequiredService<IMigrationRunner>();
    runner.MigrateUp();
}

if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}

app.UseAuthorization();
app.MapControllers();
app.Run();
```

{: .space }
## Coming Up Next…

In Part 2, we’ll dive into **global error handling and logging**. We'll cover:

- Setting up custom error handling middleware
- Implementing centralized logging for your API using **NLog**
- How to effectively capture and log error details



Thanks for reading! See you in Part 2! 