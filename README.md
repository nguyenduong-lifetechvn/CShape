# CShape

## Coding Conventions

#### 1. Naming Conventions
 ```Any of the guidance pertaining to elements marked public is also applicable when working with protected and protected internal elements. Use pascal casing ("PascalCasing") when naming a class, record, or struct ```
 
##### Use pascal casing ("PascalCasing") when naming a class, record, or struct.
```
public class DataService
{
}
```
```
public record PhysicalAddress(
    string Street,
    string City,
    string StateOrProvince,
    string ZipCode);
```
```
public struct ValueCoordinate
{
}
```
##### When naming an interface, use pascal casing in addition to prefixing the name with an I. This clearly indicates to consumers that it's an interface.
```
public interface IWorkerQueue
{
}
```
###### When naming public members of types, such as fields, properties, events, methods, and local functions, use pascal casing.
```
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
```
public class DataService
{
    private IWorkerQueue _workerQueue;
}
```
##### Using naming when this is ``static`` of ``private`` or ``internal``, When working with static fields that are private or internal, use the ``s_`` prefix and for thread static use ```t_```.
```
public class DataService
{
    private static IWorkerQueue s_workerQueue;

    [ThreadStatic]
    private static TimeSpan t_timeSpan;
}
```
##### When writing method parameters, use camel casing.
```
public T SomeMethod<T>(int someNumber, bool isValid)
{
}
```
