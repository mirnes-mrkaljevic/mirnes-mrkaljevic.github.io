---
layout: post
title:  "Dapper On Steroids: SqlKata"
date:   2025-01-29
tags: [csharp,sqlkata,dapper,entityframework]
---
Dapper is a lightweight, high-performance ORM for .NET. It is widely used because of its simplicity and speed. Dapper in its essence provides simple mapping to POCO and works with raw queries minimizing the overhead. On the other hand it lacks of flexibility in terms of query writing, because we need to write queries like static strings. One simple example is shown in the code snippet below.

```csharp
using System;
using System.Data.SqlClient;
using Dapper;

class Program
{
    static void Main(string[] args)
    {
        using (var connection = new SqlConnection("connection_string"))
        {
            connection.Open();

            string sql = "SELECT * FROM Companies";
            var companies = connection.Query<Company>(sql);
        }
    }
}
```

With the lack of flexibility in query construction, we would be in temptation to go for Entity Framework or some other heavy weight ORM, but there is also a simple library ([SqlKata](https://sqlkata.com/)) which provides a decent flexibility in query construction and also keeps a full control over the generated query and we don't need to trade of the execution speed. 

The library itself is mostly based on [Builder Design Pattern](https://mirnes-mrkaljevic.github.io/2025/01/22/design-patterns-builder.html) that is used for constructing queries. In the code snipet below is a simple example how to use the library.

```csharp
var connection = new SqlConnection("connection string example");
var compiler = new SqlServerCompiler();
var db = new QueryFactory(connection, compiler);
var query = db.Query("Companies")
    .Select("Id", "Name")
    .Where("IsActive", true)
    .OrderBy("Name")
    .Limit(10);
```

  

  

The library also supports all other SQL comamnds, so it is worth to check it if you want to keep your query generation lighweight, simple and in your full control, what we cannot say for Entity Framework. Of course, this cannot be a full alternative to Entity Framework, but it can replace it in most cases.