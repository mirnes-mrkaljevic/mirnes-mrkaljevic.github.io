---
layout: post
title:  "Design Patterns: Builder"
date:   2025-01-22
tags: [builder,csharp,softwaredevelopment,designpatterns]
series: Design Patterns
---
This is also, like a [Singleton](https://mirnes-mrkaljevic.github.io/2025/01/01/design-patterns-singleton.html), one of the creational design patterns. It provides the way of creating complex objects step by step by simple chaining and every particular step is independent of other steps.

Let us dive into the real example of usage. For showing purpose we have created an example in C# which creates simple SQL queries using described pattern. 

```csharp

using System;
using System.Text;

namespace BuilderPatternExample
{
    public interface ISqlQueryBuilder
    {
        ISqlQueryBuilder Select(string columns);
        ISqlQueryBuilder From(string table);
        ISqlQueryBuilder Where(string condition);
        ISqlQueryBuilder OrderBy(string columns);
        string Build();
    }

    public class SelectQueryBuilder : ISqlQueryBuilder
    {
        private readonly StringBuilder _queryBuilder;

        public SelectQueryBuilder()
        {
            _queryBuilder = new StringBuilder();
        }

        public ISqlQueryBuilder Select(string columns)
        {
            _queryBuilder.Append($"SELECT {columns} ");
            return this;
        }

        public ISqlQueryBuilder From(string table)
        {
            _queryBuilder.Append($"FROM {table} ");
            return this;
        }

        public ISqlQueryBuilder Where(string condition)
        {
            _queryBuilder.Append($"WHERE {condition} ");
            return this;
        }

        public ISqlQueryBuilder OrderBy(string columns)
        {
            _queryBuilder.Append($"ORDER BY {columns} ");
            return this;
        }

        public string Build()
        {
            return _queryBuilder.ToString().Trim();
        }
    }
    

    class Program
    {
        static void Main(string[] args)
        {
            ISqlQueryBuilder queryBuilder = new SelectQueryBuilder();

            string query = queryBuilder
                .Select("id, name, age")
                .From("Users")
                .Where("age > 18")
                .OrderBy("name ASC")
                .Build();

            Console.WriteLine("Generated SQL Query:");
            Console.WriteLine(query);
        }
    }
}
```

In this particular example we can see that we avoid to use hardcoded SQL strings and provide one flexible way of building the queries. Same example can be extended to other SQL commands. In the end, client that uses the builder interface does not need to care about the order of commands, or on spelling of SQL keywords.


## When to use it?

- When constructing a complex object requires many steps.
- To create object with lot of optional parameters.
- When constructing objects with immutable states.
- To improve code readability and reduce the complexity of the object creation logic.

  

## When not to use it?

- When having simple objects with few properties.
- High-performance systems with frequent object creation.
- A singleton or a constant-like object.
- If the codebase is unlikely to grow or change significantly.

  

## Conclusion

The Builder pattern is ideal for creating complex objects step-by-step, especially when they have many configurable parts or require different variations. It improves code readability, ensures consistency, and simplifies object construction by separating the build logic from the object itself. However, it’s best suited for scenarios where complexity justifies its use—avoid it for simple objects or straightforward creation processes.
