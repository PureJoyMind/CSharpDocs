Generics were added to the runtime and C# in version 2.0.
C# is a language that encourages type safety: The language is designed so that many type errors, such as assigning an integer to a variable of type string, can be caught at compile time.

To minimize the learning curve for developers, the C# designers chose syntax that superficially resembles C++ templates. In C#, the syntax for generic classes and structures uses angle brackets to both declare the generic type parameters in the type declaration and specify the generic type arguments when the type is used.

## Benefits of Generics

* Generics facilitate increased type safety, preventing data types other than those explicitly intended by the members within the parameterized class
* Compile-time type checking reduces the likelihood of `InvalidCastException` type errors at runtime.
* Using value types with generic class members no longer causes a boxing conversion to `object`
* Performance improves because casting from an object is no longer required, thereby eliminating a type check operation. Also, performance improves because boxing is no longer necessary for value types.
* Generics reduce memory consumption by avoiding boxing and thus consuming less memory on the heap.

## Type Parameter Naming Guidelines
just as when naming a method’s formal parameter, so you should be as descriptive as possible when naming a type parameter. Furthermore, to distinguish the parameter as being a type parameter, its name should include a T prefix. For example, in defining a class such as `EntityCollection<TEntity>`, you would use the type parameter name `TEntity`.

It is a good practice to use constraint-descriptive type names. For example, if a type parameter must implement `IComponent`, consider using a type name of `TComponent`.

## Generic Interfaces and Structs
C# supports the use of generics throughout the language, including interfaces and structs. The syntax is identical to that used by classes. To declare an interface with a type parameter, place the type parameter in angle brackets immediately after the interface name.

```C#
interface IPair<T>
{
    T First { get; set; }
    T Second { get; set; }
}
```

To implement the interface, you use the same syntax as you would for a nongeneric class. Note that it is legal—indeed, common—for the type argument for one generic type to be a type parameter of another generic type.

```C#
public struct Pair<T> : IPair<T>
{
    public T First { get; set; }
    public T Second { get; set; }
}
```

Support for generic interfaces is especially important for collection classes, where generics are most prevalent. Before generics were available in C#, developers relied on a series of interfaces within the `System.Collections` namespace. Like their implementing classes, these interfaces worked only with type `object`, and as a result, the interface forced all access to and from these collection classes to require a cast. By using type-safe generic interfaces, you can avoid cast operations.

## Implementing the Same Interface Multiple Times on a Single Class
Two different constructions of the same generic interface are considered different types. Consequently, “the same” generic interface can be implemented multiple times by a class or struct.

```C#
public interface IContainer<T>
{
    ICollection<T> Items { get; set; }
}
public class Person : IContainer<Address>,
    IContainer<Phone>, IContainer<Email>
{
    ICollection<Address> IContainer<Address>.Items
    {
        get { ... }
        set { ... }
    }
    ICollection<Phone> IContainer<Phone>.Items
    {
        get { ... }
        set { ... }
    }
    ICollection<Email> IContainer<Email>.Items
    {
        get { ... }
        set { ... }
    }
}
```

However, this technique of implementing multiple versions of the same interface is considered by many developers to be a “bad code smell” because it is potentially confusing (particularly if the interface permits covariant or contravariant conversions).

## Specifying a Default Value with the default operator
Specifying a constructor to initialize a property is a problem if the property is not given in the parameters because we don't know the type of `T`. If it is a nullable type, `null` would work, but this approach would not work if `T` were a non-nullable type.
To deal with this scenario, C# provides the `default` operator.

```C#
public struct Pair<T> : IPair<T>
{
    public Pair(T first)
    {
        First = first;
        Second = default;
    }
}
```

The `default` operator can provide the default value for any type, including type parameters.
It’s possible to use `default` without specifying a parameter if it is possible to infer the data type.
For example, with variable initialization or assignment, you can use `Pair<T> pair = default` in place of `Pair<T> pair = default(Pair<T>)`. Furthermore, if a method returns an `int`, it is possible to simply use `return default` and have the compiler infer a `default(int)` from the return of the method. Other scenarios where such inference is possible are default parameter (optional) values and method call arguments.

Note that all nullable types have `null` as their default value, as do nullable generic types such as `default(T?)`. Furthermore, the default value for all reference types is `null`. As a result, if nullable reference types are enabled with C# 8.0, assigning default to a non-nullable reference type will result in a warning. Prior to C# 8.0 (and obviously after it), you should avoid assigning default or null to reference types unless null is expected to be a valid value. Where possible, favor leaving the variable uninitialized until a valid value is available for assignment.

## Multiple Type Parameters
The number of type parameters—that is, the #arity—uniquely distinguishes the class from others of the same name. Because of this arity variation, it is possible to define both `Pair<T>` and `Pair<TFirst, TSecond>` within the same namespace. Furthermore, because of their close semantic relationship, generics that differ only by arity should be placed into the same C# file.

## Nested Generic Types
Type parameters on a containing generic type will “cascade” down to any nested types automatically. If the containing type declares a type parameter T, for example, all nested types will also be generic, and type parameter T will be available on the nested type as well. If the nested type includes its own type parameter named T, this will hide the type parameter within the containing type, and any reference to T in the nested type will refer to the nested T type parameter. Fortunately, reuse of the same type parameter name within the nested type will cause a compiler warning to prevent accidental overlap.

# Constraints
Generics support the ability to define constraints on type parameters. These constraints ensure that the types provided as type arguments conform to various rules.

## IComparable

The “ #IComparable ” is an interface that defines a single method for comparing objects of the same type and allows us to sort them based on a specific field.
When you implement the `IComparable` interface in a class, you are essentially saying that objects of that class can be compared with each other using a predefined criterion. This criterion is defined by the `CompareTo` method, which must be implemented as part of the interface contract.
We must implement the `CompareTo` method as part of this interfaces' contract:


```csharp
class Student : IComparable<Student>
{
    public int RollNo { get; set; }
    // Constructor
    public Student(int rollNo)
    {
        RollNo = rollNo;
    }
    // Implement the CompareTo method from IComparable<Student>
    public int CompareTo(Student other)
    {
        if (other == null)
            return 1;
        // Compare based on RollNo
        return RollNo.CompareTo(other.RollNo);
    }
}
```

