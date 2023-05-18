# CShape

## Coding Conventions

#### 1. Naming Conventions
 ```Any of the guidance pertaining to elements marked public is also applicable when working with protected and protected internal elements. Use pascal casing ("PascalCasing") when naming a class, record, or struct ```
 
##### Use pascal casing ("PascalCasing") when naming a class, record, or struct.
```cs
public class DataService
{
}
```
```cs
public record PhysicalAddress(
    string Street,
    string City,
    string StateOrProvince,
    string ZipCode);
```
```cs
public struct ValueCoordinate
{
}
```
##### When naming an interface, use pascal casing in addition to prefixing the name with an I. This clearly indicates to consumers that it's an interface.
```cs
public interface IWorkerQueue
{
}
```
###### When naming public members of types, such as fields, properties, events, methods, and local functions, use pascal casing.
```cs
public class ExampleEvents
{
    // A public field, these should be used sparingly
    public bool IsValid;

    // An init-only property
    public IWorkerQueue WorkerQueue { get; init; }

    // An event
    public event Action EventProcessing;

    // Method
    public void StartEventProcessing()
    {
        // Local function
        static int CountQueueItems() => WorkerQueue.Count;
        // ...
    }
}
```

##### Camel case Use camel casing ("camelCasing") when naming private or internal fields, and prefix them with _.
```cs
public class DataService
{
    private IWorkerQueue _workerQueue;
}
```
##### Using naming when this is ``static`` of ``private`` or ``internal``, When working with static fields that are private or internal, use the ``s_`` prefix and for thread static use ```t_```.
```cs
public class DataService
{
    private static IWorkerQueue s_workerQueue;

    [ThreadStatic]
    private static TimeSpan t_timeSpan;
}
```
##### When writing method parameters, use camel casing.
```cs
public T SomeMethod<T>(int someNumber, bool isValid)
{
}
```
# SOLID
## 1.Single Reponsibility Principle
##### As the name suggests, this principle states that each class should have One Reponsibility Principle, One Single Purpose. This means that a class will do only one Job, which leads us to conclude(kết luận) it should have Only One Reason To Change

##### We don't want objects that know too much and have unrelated behavior. These classes are harder to maintain. For example, if we have a class that we change a lot, and for differentreasons, then this class should be broken down into more classes, each handling a signle concern, Surely, If an error occurs, it will be easier to find.



----------------------------- COURSE ------------------------------
Set up Web API

> dotnet new webapi
> 
> dotnet watch run
> 
> dotnet new gitignore
> 
> Coming up:
> 
>     New controller & Models
> 
>     Asynchronous implementations with async/await
> 
>     Data Transfer Onjects DTOs
> 
>     Best practices
> 
>     MVC pattern
> 
> AddScoped SingleTon Transient

global using DotNet.Models; trong controller (note first line) or put it in first line in program.cs

RESTFul web service calls

`dotnet add package AutoMapper.Extensions.Microsoft.DependencyInjection`

program.cs

            builder.Services.AddAutoMapper(typeof(Program).Assembly);

> trong CharacterService
> 
>   private readonly IMapper _mapper;
> 
>         public CharacterService(IMapper mapper)
> 
>         {
> 
>             _mapper = mapper;
> 
>         }

Persistence with Entity Framework Core

Code First Migration

SQL Server

Relationships 1 - 1, 1 - n, n - n


```cs
dotnet add package Microsoft.EntityFrameworkCore
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Design

dotnet tool install --global dotnet-ef
dotnet-ef
```
##### Implement Data Context
*** new Folder Data --> New class DataContext
```cs
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;

namespace DotNet.Data
{
    public class DataContext : DbContext
    {
        public DataContext(DbContextOptions options) : base(options){ }
        public DbSet<Character> Characters => Set<Character>();
    }
}
```
*** New Connection String in appsetting.json

```cs
"ConnectionStrings": {
    "DefaultConnetionString": "Server=localhost;Database=CharacterDb;integrated security=True;MultipleActiveResultSets=True;TrustServerCertificate=True"
  }
  ```
*** Add DbContext in Program.cs file (note in once line)
```
builder.Services.AddDbContext<ContactsAPIDbContext>(options => options.UseSqlServer(builder.Configuration.GetConnectionString("ContactAPIConnetionString")));

```

*** Add Migrations
```cs
 dotnet ef migrations add InitTableCharacter
```

*** Update Database
```
dotnet ef database update
```

*** In CharacterService add 
```cs
public CharacterService(IMapper mapper, DataContext context)
        {
            _mapper = mapper;
            _context = context;
        }
```















































