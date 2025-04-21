---
layout: post
title:  "Keep Your Database In Sync With Application: Fluent Migrator"
date:   2025-01-26
tags: [csharp,database,migrations,entityframework]
---
In this post, we continue to explore ways to free your application from being tightly bound to the Entity Framework environment. The primary goal is to offer an alternative solution that is not only more flexible and robust but also gives you, as a developer, greater control over database manipulation with less abstraction and complexity. Here, we introduce a library for database migrations, which serves as a natural extension to tools like [Dapper and SqlKata](https://mirnes-mrkaljevic.github.io/2025/01/25/dapper-on-steroids-sqlkata.html).

## Fluent Migrator

[Fluent Migrator](https://fluentmigrator.github.io/) is a library designed for managing database migrations, giving you complete control over the execution and how migrations are written. It offers greater flexibility compared to standard EF migrations, which are closely tied to EF entities and generated automatically, often accompanied by additional overhead, including extra snapshot files.

In the code snippet below, you’ll find a simple migration class. This class includes two methods that override the base `Migration` class, allowing you to define your migration. This is all you need to create a migration.

  

```csharp
using FluentMigrator;

[Migration(2025011801)]
public class Mig2025011801Init : Migration
{
    public override void Up()
    {
        Create.Table("Company")
            .WithColumn("Id").AsInt32().PrimaryKey().Identity()
            .WithColumn("Name").AsString(255).NotNullable()
    }

    public override void Down()
    {
        Delete.Table("Company");
    }
}

  

```

Additionally, when starting the project, we need to configure FluentMigrator to locate the migration classes and define how they should be executed. This is demonstrated in the code snippet below.

```csharp
using FluentMigrator.Runner;
using Microsoft.Extensions.DependencyInjection;

class Program
{
    static void Main(string[] args)
    {
        var serviceProvider = CreateServices();

        using (var scope = serviceProvider.CreateScope())
        {
            UpdateDatabase(scope.ServiceProvider);
        }
    }

    private static IServiceProvider CreateServices()
    {
        return new ServiceCollection()
            .AddFluentMigratorCore()
            .ConfigureRunner(rb => rb
                .AddSqlServer()
                .WithGlobalConnectionString("conn string")
                .ScanIn(typeof(Mig2025011801Init).Assembly).For.Migrations())
            .AddLogging(lb => lb.AddFluentMigratorConsole())
            .BuildServiceProvider(false);
    }

    private static void UpdateDatabase(IServiceProvider serviceProvider)
    {
        var runner = serviceProvider.GetRequiredService<IMigrationRunner>();
        runner.MigrateUp();
    }
}

  

```

## Conclusion

In conclusion, let us show one table where we see simple comparison between FluentMigrator and EF migrations. This can help you to decide which migration library to use for your next or existing project.

| Fluent Migrator | EF Migrations |
| --- | --- |
| Schema migrations for databases with full control over structure | Built-in to EF for schema migrations tied to EF models |
| Independent of ORM (works with raw ADO.NET, Dapper, or others) | Closely integrated with Entity Framework |
| Requires writing migrations manually, which can be verbose but provides full control. | Migrations are auto-generated and customizable in C#. |
| Allows full control, suitable for existing schemas | Focused on Code First or Model First approaches |
| Explicitly define Down() methods for each migration. Provides precise control over rollback logic | Rollbacks involve reverting migrations using commands like Update-Database -TargetMigration |
| Integrates well into CI/CD pipelines as a standalone migration tool. Can be used independently of application deployment | Integrated into EF workflows, making deployment tied to EF-related commands. Often relies on the application's context to execute migrations |