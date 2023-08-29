Interfaces introduce another category of data types. (They are one of the few categories of types that don’t extend System.Object.) Unlike classes, however, interfaces can never be instantiated

Polymorphism is available in C# not only via inheritance (as discussed in Chapter 7) but also via interfaces. Unlike abstract classes, interfaces could not include any implementation—until C# 8.0. (But even in C# 8.0, it is questionable whether you should use this capability except for “versioning” the interfaces.) Like abstract classes, however, interfaces define a set of members that callers can rely on being implemented.

By implementing an interface, a type defines its capabilities. The interface implementation relationship is a “can do” relationship. The type can do what the interface requires an implementing type to do. The interface defines the contract between the types that implement the interface and the code that uses the interface. Types that implement interfaces must declare methods with the same signatures as the methods declared by the implemented interfaces.

# Introducing Interfaces
Interfaces are useful because—unlike abstract classes—they enable the complete separation of implementation details from services provided.

By convention—a convention so strong it is universal—the interface name is PascalCase with a capital “I” prefix.

The power of interfaces is that they grant the ability to callers to switch among implementations without modifying the calling code.

Prior to C# 8.0, one of the key characteristics of an interface was that it had no implementation and no data (fields). Method declarations in an interface always had a single semicolon in place of curly braces after the declaration. Properties, while looking like automatically implemented properties, had no backing fields. In fact, fields (data) could not appear in an interface declaration either.

Many of these rules were relaxed in C# 8.0 for the purposes of allowing interfaces to have some level of restricted changes after publishing.
The declared members of an interface describe the members that must be accessible on an implementing type. The purpose of non-public members is to make those members inaccessible to other code. Therefore, ***C# does not allow access modifiers on interface members; instead, it automatically defines them as public***

# Interface Implementation
Declaring a class to implement an interface is similar to deriving from a base class: The implemented interfaces appear in a comma-separated list along with the base class. The base class specifier (if there is one) must come first, but otherwise order is not significant.

Once a class declares that it implements an interface, all (abstract2) members of the interface must be implemented. An abstract class is permitted to supply an abstract implementation of an interface member. A non-abstract implementation may throw a `NotImplementedException` type exception in the method body, but an implementation of the member must always be supplied.

One important characteristic of interfaces is that they can never be instantiated; you cannot use new to create an interface, so interfaces do not have instance constructors or finalizers. Interface instances are available only by instantiating a type that implements the interface. Furthermore, interfaces cannot include static members.3 One key interface purpose is polymorphism, and polymorphism without an instance of the implementing type has little value.

Each (non-implemented) interface member is abstract, forcing the derived class to implement it. Therefore, it is not possible to use the abstract modifier on interface members explicitly.

When implementing an interface member in a type, there are two ways to do so: ***explicitly*** or ***implicitly***.

## Explicit Member Implementation
Explicitly implemented methods are available only by calling them through the interface itself; this is typically achieved by casting an object to the interface.

When we have multiple interfaces which have a method with the same name, we have to implement those in the class by calling the method from the interface itself. 
When we want to use the method, since there are multiple implementations of it, we cast the instance of the class which implemented them to the specific interface before calling the method. 
This way we can also discriminate the instances of the class. 

```C#
interface IExample
{
    void SomeMethod();
}

class MyClass : IExample
{
    // Explicit implementation of IExample's SomeMethod
    void IExample.SomeMethod()
    {
        Console.WriteLine("Explicit implementation");
    }
}

class Program
{
    static void Main(string[] args)
    {
        MyClass myObject = new MyClass();

        // You can't call myObject.SomeMethod() directly because it's explicitly implemented.

        // Instead, you can cast myObject to IExample and call the method.
        IExample myInterface = (IExample)myObject;
        myInterface.SomeMethod(); // Output: "Explicit implementation"
    }
}

```

This goes for properties as well, since in a way properties have method like behaviors.

## Implicit Member Implementation
Interface member implementation does not require use of the override keyword or any indication that this member is tied to the interface. Furthermore, since the member is declared just like any other class member, code that calls implicitly implemented members can do so directly, just as it would any other class member:

`result = contact1.CompareTo(contact2);`

implicit member implementation does not require a cast because the member is not hidden from direct invocation on the implementing class.

Many of the modifiers disallowed on an explicit member implementation are required or are optional on an implicit implementation. For example, implicit member implementations must be public. Furthermore, `virtual` is optional, depending on whether derived classes may override the implementation. Eliminating virtual will cause the member to behave as though it is `sealed`

## Explicit vs Implicit Interface Implementation
The key difference lies in how the methods are accessed through instances of the implementing type and interfaces. Implicit implementation reflects core class functionality, while explicit implementation separates mechanism concerns. Interfaces are used to model mechanism relationships, often independent of semantic models. Guidelines are provided for choosing between implicit and explicit implementations, such as considering the appropriateness of the member name for the class, whether the member already exists with the same signature, and whether the member is a core part of the class's behavior. Intuition plays a role in the decision, but these questions aid in making informed choices. The ability to switch from explicit to implicit implementation suggests favoring explicit interfaces initially. Lastly, the choice between implicit and explicit doesn't have to be consistent across all interface members.

# Converting between the Implementing Class and Its Interfaces
Just as with a derived type and a base class, a conversion from an implementing type to its implemented interface is an implicit conversion. No cast operator is required because an instance of the implementing type will always provide all the members in the interface; therefore, the object can always be converted successfully to the interface type.

Although the conversion will always be successful from the implementing type to the implemented interface, many different types could implement a particular interface. Consequently, you can never be certain that a “downward” cast from an interface to one of its implementing types will be successful. Therefore, converting from an interface to one of its implementing types requires an explicit cast.

# Interface Inheritance
Interfaces can derive from each other, resulting in an interface that inherits all the members in its base interfaces.
When an interface inherits another interface, if a member is implemented explicitly, it must be done using the base interface. 

```C#
interface IBase
{
    void Method();
}

interface IDerived : IBase
{
    void IDerivedMethod();
}

class MyClass : IDerived
{
    // Explicit implementation of Method from the base interface IBase
    void IBase.Method()
    {
        Console.WriteLine("Base Method implementation");
    }

    public void IDerivedMethod()
    {
        Console.WriteLine("Derived Method implementation");
    }
}

class Program
{
    static void Main(string[] args)
    {
        MyClass myObject = new MyClass();

        IBase baseInterface = myObject;
        baseInterface.Method(); // Output: "Base Method implementation"

        IDerived derivedInterface = myObject;
        derivedInterface.IDerivedMethod(); // Output: "Derived Method implementation"
    }
}

```

The fully qualified interface member name used for explicit interface member implementation must reference the interface name in which it was originally declared.

Unlike explicit implementation, implicit implementation doesn't require any special syntax when dealing with derived interfaces. When you implement a derived interface, you can provide implementations for all the methods declared in both the derived interface and its base interfaces using regular method definitions

# Multiple Interface Inheritance
Just as classes can implement multiple interfaces, so interfaces can inherit from multiple interfaces. The syntax used for this purpose is consistent with class derivation and implementation.

It is unusual to have an interface with no members, but it is a reasonable choice when implementing both interfaces together.

# Extension Methods on Interfaces
Perhaps one of the most important features of extension methods is the fact that they work with interfaces in addition to classes. The syntax used is identical to that used for extension methods for classes. The extended type (the first parameter and the parameter prefixed with this) is the interface that we extend.

# [Additional Encapsulation and Polymorphism with Protected Interface Members](Additional Encapsulation and Polymorphism with Protected Interface Members)

# Interfaces Compared with Abstract Classes
An interface instance is accessible only via a reference to an object that implements the interface. It is not possible to use the new operator with an interface; therefore, interfaces cannot contain any instance constructors or finalizers. Prior to C# 8.0, static members are not allowed on interfaces.