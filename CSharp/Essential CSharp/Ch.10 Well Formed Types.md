# Access modifiers

![[Pasted image 20230823100548.png]]

# Namespaces
1. **Namespace Definition**: In the CLR, namespaces are used to organize and categorize data types. A namespace is essentially a way to group related classes together. The combination of a namespace and a class name uniquely identifies a data type.

2. **No Explicit Namespace in CLR**: Unlike in some programming languages, the CLR does not have an explicit concept of a "namespace." Instead, a type's name itself is considered the fully qualified type name, including the namespace.

3. **Default Global Namespace**: Classes defined without an explicit namespace declaration are automatically part of the default global namespace. This can lead to name collisions when defining classes with the same name.

4. **Name Collision and Third-Party Assemblies**: Assemblies from third-party sources increase the likelihood of name collisions. This happens when attempting to define classes with the same name from different sources.

5. **Organizing Types**: Organizing types into namespaces solves name collision issues and aids in finding the right type for a given problem. Namespaces group related classes into logical categories.

6. **Namespace Naming**: Namespaces should be labeled using PascalCase. Stable product names, rather than volatile names, should be used to ensure compatibility.

7. **Nested Namespaces**: Namespaces can be nested, providing a hierarchical organization of classes. This allows for logical grouping of related classes. Nesting can be done within one another or using full namespace identifiers.

8. **Visual Studio Projects and Folders**: It's helpful to organize class files within folders that correspond to the namespaces. For instance, each namespace could have a corresponding folder within the project structure.

9. **Namespace's Role in Organization**: Namespaces are crucial for organizing types, and they are frequently used to structure class files within a project.

# [XML Comment](https://essentialcsharp.com/xml-comments#xml-comments)

# Garbage Collection
Its purpose is to restore memory consumed by objects that are no longer referenced.
the garbage collector determines what to clean up, based on whether any references remain. Implicitly, this means that the garbage collector works with reference objects and restores memory on the heap only. Additionally, it means that maintaining a reference to an object will delay the garbage collector from reusing the memory consumed by the object.

Garbage collection is a key responsibility of the runtime. Nevertheless, it is important to recognize that the garbage collection process centers on the code’s memory utilization. It is not about the cleaning up of file handles, database connection strings, ports, or other limited resources.
## Weak References
All references discussed so far are strong references because they maintain an object’s accessibility and prevent the garbage collector from cleaning up the memory consumed by the object. The framework also supports the concept of weak references. Weak references do not prevent garbage collection on an object, but they do maintain a reference so that if the garbage collector does not clean up the object, it can be reused.

Weak references are designed for reference objects that are expensive to create, yet too expensive to keep around. Consider, for example, a large list of objects loaded from a database and displayed to the user. The loading of this list is potentially expensive, and once the user closes the list, it should be available for garbage collection. However, if the user requests the list multiple times, a second expensive load call will always be required. With weak references, it becomes possible to use code to check whether the list has been cleaned up, and if not, to re-reference the same list. In this way, weak references serve as a memory cache for objects. Objects within the cache are retrieved quickly, but if the garbage collector has recovered the memory of these objects, they will need to be re-created.

# Resource Cleanup
## Finalizers
Finalizers allow developers to write code that will clean up a class’s resources. Unlike constructors that are called explicitly using the new operator, finalizers cannot be called explicitly from within the code. There is no new equivalent such as a delete operator. Rather, the garbage collector is responsible for calling a finalizer on an object instance. Therefore, developers cannot determine at compile time exactly when the finalizer will execute. All they know is that the finalizer will run sometime between when an object was last used and generally when the application shuts down normally. The deliberate injection of incertitude with the word “Generally” highlights the fact that finalizers might not execute. This possibility is obvious when you consider that a process might terminate abnormally. For instance, events such as the computer being turned off or a forced termination of the process, such as when debugging the process, will prevent the finalizer from running. However, with .NET Core, even under normal circumstances, finalizers may not get processed before the application shuts down. As we shall see in the next section, it thus may be necessary to take additional action to register finalization activities with other mechanisms.

The finalizer declaration is identical to the destructor syntax of C#’s predecessor—that is, C++.

```C#
// Finalizer

    ~TemporaryFileStream()
    {
        try
        {
            Close();
        }
        catch (Exception )
        {
            // Write event to logs or UI
        }
    }
    public FileStream? Stream { get; private set; }
    public FileInfo? File { get; private set; }
    public void Close()
    {
        Stream?.Dispose();
        try
        {
            File?.Delete();
        }
        catch(IOException exception)
        {
            Console.WriteLine(exception);
        }
        stream = null;
        File = null;
    }
```

only the garbage collector can invoke a finalizer. Access modifiers on finalizers are therefore meaningless, and as such, they are not supported. Finalizers in base classes will be invoked automatically as part of an object finalization call.

Because the garbage collector handles all memory management, finalizers are not responsible for de-allocating memory. Rather, they are responsible for freeing up resources such as database connections and file handles—resources that require an explicit activity that the garbage collector doesn’t know about.

Finalizers execute on an unspecified thread, making their execution even less deterministic.

Finalizers execute on an unspecified thread, making their execution even less deterministic. This indeterminate nature makes an unhandled exception within a finalizer (outside of the debugger) likely to crash the application—and the source of this problem is difficult to diagnose because the circumstances that led to the exception are not clear. From the user’s perspective, the unhandled exception will be thrown relatively randomly and with little regard for any action the user was performing. For this reason, you should take care to avoid exceptions within finalizers. Instead, you should use defensive programming techniques such as checking for null (refer to the use of the null-conditional operator in Listing 10.15). In fact, it is advisable to catch all exceptions in the finalizer and report them via an alternative means (such as logging or via the user interface) rather than keeping them as unhandled exceptions. This guideline leads to the try/catch block surrounding the Delete() invocation.

Another potential option to force finalizers to execute is to invoke `System.GC.WaitForPendingFinalizers()`. When this method is invoked, the current thread will be suspended until all finalizers for objects that are no longer referenced have executed.

## [Deterministic Finalization with the `using` Statement](https://essentialcsharp.com/deterministic-finalization-with-the-using-statement#deterministic-finalization-with-the-using-statement)
Because of the importance of deterministic finalization, the base class library includes a specific interface for the pattern and C# integrates the pattern into the language. The `IDisposable` interface defines the details of the pattern with a single method called `Dispose()`, which developers call on a resource class to “dispose” of the consumed resources.

## Lazy Initialization
Using lazy initialization, you can create (or obtain) objects when you need them rather than beforehand—the latter can be an especially problematic situation when those objects are never used.

```c#
    public TemporaryFileStream FileStream =>
        InternalFileStream ??= new TemporaryFileStream();
            
    private TemporaryFileStream? InternalFileStream { get; set; } = null;
```

### Lazy Loading with Generics and Lambda Expressions
In the [advanced section](https://essentialcsharp.com/lazy-initialization#lazy-initialization).

