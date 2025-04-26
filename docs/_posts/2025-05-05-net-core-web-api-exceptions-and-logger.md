---
layout: post
title: "Web API Exception Handling and Logging"
date: 2025-05-05
tags: [dotnet,dotnet9,cleancode,csharp,softwaredesign,exceptions,nlog]
series: "Web API – Step-by-Step Best Practices"
---

In the previous post, we set up a basic .NET 9 Web API project. Now it’s time to take things further by adding **global error handling** and **logging** to make our API more robust. 

In this post, we’ll cover:
- How to implement **global error handling** with middleware  
- Setting up **NLog** to log errors to both files and a **database table**  
- Creating a **migration** for the log table in the database 


{: .space }
## Why Global Error Handling?

Handling errors in one centralized place makes your API easier to maintain and more consistent. Instead of repeating error-catching logic in every controller, you can handle unexpected issues in a single middleware.  

This approach allows you to return uniform error responses and capture useful logs automatically when something goes wrong.

{: .space }
## Creating Global Error Handling Middleware

Create a `Middleware` folder in your project and add a file called `ExceptionHandlingMiddleware.cs`:

```csharp
using Microsoft.AspNetCore.Http;
using System;
using System.Net;
using System.Threading.Tasks;

public class ExceptionHandlingMiddleware
{
    private readonly RequestDelegate _next;

    public ExceptionHandlingMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    public async Task InvokeAsync(HttpContext httpContext)
    {
        try
        {
            await _next(httpContext);
        }
        catch (Exception ex)
        {
            await HandleExceptionAsync(httpContext, ex);
        }
    }

    private Task HandleExceptionAsync(HttpContext context, Exception exception)
    {
        context.Response.StatusCode = (int)HttpStatusCode.InternalServerError;
        context.Response.ContentType = "application/json";
        var result = new { message = "An unexpected error occurred. Please try again later." };
        return context.Response.WriteAsJsonAsync(result);
    }
}
```

## Registering the Middleware

Update your `Program.cs` to use this middleware:


```csharp
using FluentMigrator.Runner;
using Sample.Api.Middleware;
using Sample.Migrations.Migrations;

var builder = WebApplication.CreateBuilder(args);

// Add services to the container.
builder.Services.AddControllers();

// Learn more about configuring OpenAPI at https://aka.ms/aspnet/openapi
builder.Services.AddOpenApi();

var app = builder.Build();

app.UseMiddleware<ExceptionHandlingMiddleware>();

// Configure the HTTP request pipeline.
if (app.Environment.IsDevelopment())
{
    app.MapOpenApi();
}

app.UseHttpsRedirection();

app.UseAuthorization();

app.MapControllers();

app.Run();

```

## Setting Up NLog in .NET 9


### Install the NLog packages

```bash
dotnet add package NLog.Web.AspNetCore
dotnet add package NLog.Database
```


### Add a `nlog` config to `appsettings.json` file

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=localhost;Database=SampleDb;Trusted_Connection=True;TrustServerCertificate=True"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    },
    "NLog": {
      "IncludeScopes": true,
      "RemoveLoggerFactoryFilter": true
    }
  },
  "AllowedHosts": "*",
  "NLog": {
    "autoReload": true,
    "throwConfigExceptions": true,
    "internalLogLevel": "Info",
    "internalLogFile": "${basedir}/internal-nlog.txt",
    "extensions": [
      { "assembly": "NLog.Extensions.Logging" },
      { "assembly": "NLog.Web.AspNetCore" },
      { "assembly": "NLog.Database" }
    ],
    "variables": {
      "var_logdir": "c:/temp"
    },
    "time": {
      "type": "AccurateUTC"
    },
    "targetDefaultWrapper": {
      "type": "AsyncWrapper",
      "overflowAction": "Block"
    },
    "targets": {
      "all-file": {
        "type": "File",
        "fileName": "${var_logdir}/nlog-all-${shortdate}.log",
        "layout": {
          "type": "JsonLayout",
          "Attributes": [
            {
              "name": "timestamp",
              "layout": "${date:format=o}"
            },
            {
              "name": "level",
              "layout": "${level}"
            },
            {
              "name": "logger",
              "layout": "${logger}"
            },
            {
              "name": "message",
              "layout": "${message:raw=true}"
            },
            {
              "name": "properties",
              "encode": false,
              "layout": {
                "type": "JsonLayout",
                "includeallproperties": "true"
              }
            }
          ]
        }
      },
      "database": {
        "type": "Database",
        "dbProvider": "Microsoft.Data.SqlClient.SqlConnection,Microsoft.Data.SqlClient",
        "connectionString": "Data Source=localhost;Initial Catalog=SampleDb;Trusted_Connection=True;TrustServerCertificate=True",
        "keepConnection": "true",
        "commandText": "insert into dbo.[Logs] (TimeStamp,Level,Message,Logger,Exception) values (@Timestamp, @Level, @Message, @Logger, @Exception);",
        "parameters": [
          {
            "name": "@Timestamp",
            "layout": "${date:format=o}",
            "dbType": "DbType.DateTime"
          },
          {
            "name": "@Level",
            "layout": "${level}"
          },
          {
            "name": "@Message",
            "layout": "${message}"
          },
          {
            "name": "@Logger",
            "layout": "${logger}"
          },
          {
            "name": "@Exception",
            "layout": "${exception:format=toString}"
          }
        ]
      }
    },
    "rules": [
      {
        "logger": "*",
        "minLevel": "Trace",
        "writeTo": "all-file"
      },
      {
        "logger": "*",
        "minLevel": "Error",
        "writeTo": "database"
      }
    ]
  }
}

```

### Add logger to `ExceptionHandlingMiddleware.cs`

```csharp
public class ExceptionHandlingMiddleware
{
    ...

    private readonly Logger _logger = LogManager.GetCurrentClassLogger();

    ...
    public async Task InvokeAsync(HttpContext httpContext)
    {
        try
        {
            await _next(httpContext);
        }
        catch (Exception ex)
        {
            _logger.Error(ex, ex.Message);
            await HandleExceptionAsync(httpContext, ex);
        }
    }
    ...
}
```


{: .space }
## Creating the `Logs` Table with FluentMigrator

Let’s create a migration to add a Logs table to our database where NLog can store log entries.

```csharp
using FluentMigrator;

namespace Sample.Migrations.Migrations
{
    [Migration(20250426001)]
    public class CreateLogsTable: Migration
    {
        public override void Up()
        {
            Create.Table("Logs")
                .WithColumn("Id").AsInt32().PrimaryKey().Identity()
                .WithColumn("TimeStamp").AsDateTime().NotNullable()
                .WithColumn("Level").AsString(50).NotNullable()
                .WithColumn("Logger").AsString(100).NotNullable()
                .WithColumn("Message").AsString(1000).NotNullable()
                .WithColumn("Exception").AsString(100).Nullable();
        }

        public override void Down()
        {
            Delete.Table("Logs");
        }
    }
}
```



{: .space }
## Coming Up Next…

In Part 3, we’ll start working on **authentication** and **authorization**.  
We’ll set up secure login, protect endpoints, and manage user roles and permissions properly. 

Stay tuned!
