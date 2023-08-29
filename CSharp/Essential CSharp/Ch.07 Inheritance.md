# Derivation 
When declaring a derived class, follow the class identifier with a colon and then the base class.
The inheritance chain has no practical limit, and each derived class will have all the members of its base class inheritance chain combined.
All classes that don’t have some other derivation will derive from `object`, regardless of whether it is specified.

## Casting between Base and Derived Types 
The derived type, can be assigned directly to a variable of its superType. This is known as an implicit conversion because no cast operator is required and the conversion will, in principle, always succeed; that is, it will not throw an exception.

When casting a derived type to a base type in C#, the specific fields and methods that are unique to the derived type become inaccessible through the reference of the base type. This is known as "upcasting" because you are moving up the inheritance hierarchy, from the derived type to its base type.

Here's what happens when you upcast a derived type to a base type:

1. **Access to Base Type Members**: After the upcast, you can only access the members (fields, properties, and methods) that are defined in the base type. Any additional members that were introduced in the derived type are no longer directly accessible through the base type reference.

2. **Runtime Behavior Remains Intact**: It's important to note that the actual object (instance) being referred to remains of the derived type even after upcasting. The upcast merely restricts the view of the object to its base type interface. At runtime, the behavior of the object remains based on its actual derived type, even if accessed through the base type reference.

3. **Explicit Downcasting Needed for Derived Type Members**: If you need to access the derived type-specific members that are not present in the base type, you'll have to perform a downcast. This downcasting explicitly converts the base type reference back to the derived type, making the specific members available for use. However, downcasting should be done with caution and preceded by type checking (using `as` or `is` operators) to avoid runtime exceptions if the object is not of the expected derived type.

Here's an example to illustrate the concept:

```csharp
class Shape { }
class Circle : Shape { public void DrawCircle() { } }
class Square : Shape { public void DrawSquare() { } }

static void Main()
{
    Circle circle = new Circle();
    Shape shape = circle; // Upcasting: Circle is treated as a Shape

    // Now, shape can only access Shape members
    // shape.DrawCircle(); // Error: DrawCircle is not accessible through shape

    // To access the derived type-specific members, downcasting is needed
    Circle circleAgain = shape as Circle;
    if (circleAgain != null)
    {
        circleAgain.DrawCircle(); // Valid, accessing Circle-specific method
    }
}
```

Casting from the base type to the derived type requires an explicit cast, which could fail at runtime. To perform an explicit cast, you identify the target type within parentheses prior to the original reference.

Although the C# compiler allows an explicit conversion at compile time between potentially compatible types, the Common Language Runtime (CLR) will still verify the explicit cast at execution time, throwing an exception if the object instance is not actually of the targeted type.

When you downcast an object to a derived class, you are telling the compiler to treat the object as if it were of the derived class type, allowing you to access the specific members of the derived class. However, the object itself remains unchanged, and the downcast doesn't create a new instance of the derived class.

The C# compiler allows use of the cast operator even when the type hierarchy allows an implicit conversion.

***We can define custom conversions between unrelated types.***

## `private` Access modifier
All members of a base class, except for constructors and destructors, are inherited by the derived class.

the derived class can access the public properties of the base class, which internally accesses the private backing field in the base class. The public property serves as an interface through which the derived class can interact with the private data.

## `protected` Access Modifier 
accessing a protected member from a derived class requires a compile-time determination that the protected member is an instance of the derived class.

## Extension Methods 
Extension methods are technically not members of the type they extend and, therefore, are not inherited. Nevertheless, because every derived class may be used as an instance of any of its base classes, an extension method for one type also extends every derived type. 
However, as with all extension methods, priority is given to instance methods. If a compatible signature appears anywhere within the inheritance chain, it will take precedence over an extension method.

## Single Inheritance 
C# is a single-inheritance programming language (as is the Common Intermediate Language [CIL] to which C# compiles). Consequently, a class cannot derive from two classes directly.
For the rare cases that require a multiple-inheritance class structure, one solution is to use ***aggregation***; instead of one class inheriting from another, one class contains an instance of the other. 

## Sealed Classes 
Sealed classes include the sealed modifier, so they cannot be derived from. The string type is an example of a type that uses the sealed modifier to prevent derivation.

# Overriding the base class
All members of a base class are inherited in the derived class, except for constructors and destructors. However, sometimes the base class does not have the optimal implementation of a particular member.
the base class property declaration is appropriate for the derived class, but the implementation is not always valid. A mechanism is needed for overriding the base class implementation with a custom implementation in the derived class.

## `virtual` Modifier 
C# supports overriding on instance methods and properties but not on fields or on any static members. It requires an explicit action within both the base class and the derived class. The base class must mark each member for which it allows overriding as `virtual`. If `public` or `protected` members do not include the virtual modifier, subclasses will not be able to override those members.

To override a method or property, use the `virtual` keyword on the method and whenever you want to override it in a derived type, use the `override` keyword. 

Eliminating `virtual` would result in an error and omitting `override` would cause a warning to be generated, as you will see shortly. C# requires the overriding methods to use the `override` keyword explicitly. In other words, virtual identifies a method or property as available for replacement (overriding) in the derived type.

Overriding a member causes the runtime to call the most derived implementation. whenever the runtime encounters a virtual method, it calls the most derived and overriding implementation of the virtual member. This means that when we upcast to a base type when we call an overriden method, still the overriden method from the derived type is called.

virtual methods should be used judiciously and not declared as virtual by default. This caution is because using virtual methods can introduce complexities in the design of inheritance hierarchies. It's essential to carefully consider the scenarios where virtual methods are necessary and provide added value to the design of the classes.

only instance members can be virtual. The CLR uses the concrete type, specified at instantiation time, to determine where to dispatch a virtual method call; thus `static virtual` methods are meaningless and the compiler prohibits them.

***Any members that are decorated with override are automatically designated as virtual, so additional child classes may further specialize the implementation.***

## [`new` Modifier](https://essentialcsharp.com/new-modifier#new-modifier)
In C#, the `new` modifier is used in the context of method declaration and inheritance to hide a member of the base class (or a member inherited from an interface) with a member of the same name in the derived class. It allows you to provide a new implementation for the member in the derived class, effectively overriding the member from the base class.

The `new` modifier is different from method overriding with the `override` keyword. When a method is marked as `new`, it means you are creating a new method in the derived class that has no relation to the method in the base class. It's essentially a way to have two separate methods with the same name, one in the base class and one in the derived class.

Here's the basic syntax for using the `new` modifier:

```csharp
class BaseClass
{
    public void SomeMethod()
    {
        // Base class implementation
    }
}

class DerivedClass : BaseClass
{
    public new void SomeMethod()
    {
        // Derived class implementation
    }
}
```

It's important to note that using the `new` modifier does not affect the base class method; it simply hides it in the context of the derived class. If you call `SomeMethod` on an instance of `DerivedClass`, it will execute the method defined in `DerivedClass`. However, if you call `SomeMethod` on an instance of `BaseClass` that is actually referring to an object of `DerivedClass`, it will execute the method defined in `BaseClass`.

```csharp
BaseClass baseObj = new DerivedClass();
baseObj.SomeMethod(); // Calls the method in BaseClass, not the one in DerivedClass
```

If you call the new method on an instance of the derived type, it will execute the method defined in the derived class. However, if you upcast the derived instance to the base class type and then call the method, it will execute the original inherited method from the base class.

In general, using the `new` modifier should be done with caution, as it can lead to confusion and unexpected behavior when working with polymorphism and inheritance. It's often preferable to use method overriding with the `override` keyword instead, which ensures that the correct method is called based on the actual type of the object.

## `sealed` Modifier 
Just as you can prevent inheritance using the sealed modifier on a class, so virtual members may be sealed as well.
This approach prevents a subclass from overriding a base class member that was originally declared as virtual higher in the inheritance chain. Such a situation arises when a subclass B overrides a base class A’s member and then needs to prevent any further overriding below subclass B.

leaving types unsealed has become increasingly desirable as unit testing has assumed greater prominence, because of the need to support mock (test double) object creation in place of real implementations.

a more targeted sealing of individual members—perhaps because of dependencies in the base implementation that are necessary for correct behavior—is likely to be preferable.

## `base` Member 
In choosing to override a member, developers often want to invoke the member on the base class.

```C#
public class Address
{
    public string StreetAddress, City, State, Zip;
 
    public override string ToString()
    {
        return $"{ StreetAddress + NewLine }"
            + $"{ City }, { State }  { Zip }";
    }
}
 
public class InternationalAddress : Address
{
    public string Country;
 
    public override string ToString()
    {
        return base.ToString() +
            NewLine + Country;
    }
} 
```

To call the parent class’s implementation, you use the base keyword. The syntax is virtually identical to the use of the this keyword, including support for using base as part of the constructor.

## Invoking Base Class Constructors 
When instantiating a derived class, the runtime first invokes the base class’s constructor so that the base class initialization is not circumvented. However, if there is no accessible (non-private) default constructor on the base class, it is not clear how to construct the base class; in turn, the C# compiler reports an error.
To avoid the error caused by the lack of an accessible default constructor, programmers need to designate explicitly, in the derived class constructor header, which base constructor to run 

```C#
public class B : A 
{
	public B(int a)
		: base(a)
	{}
}
```

# Abstract Classes
Abstract classes are not intended to be instantiated. They have no meaning by themselves. it has meaning only when it is used as a base class—to share default method implementations across the set of data types that derive from it. These characteristics are indicative of the need for the base class to be an ***abstract class*** rather than a ***concrete class***. Abstract classes are designed for derivation only. It is not possible to instantiate an abstract class, except in the context of instantiating a class that derives from it. Classes that are not abstract and can instead be instantiated directly are concrete classes.

Abstract classes represent abstract entities. Their abstract members define what an object derived from an abstract entity should contain, but they don’t include the implementation. Often, much of the functionality within an abstract class is unimplemented. Before a class can successfully derive from an abstract class, however, it needs to provide the implementation for the abstract methods in its abstract base class.

starting with C# 8.0, interfaces almost (specifically, no instance fields can be declared) support a superset of the functionality previously limited to abstract classes. While the details of the new interface capabilities are available in [[Ch.08 Interfaces]], understanding the concepts regarding abstract members is a prerequisite, so we will provide the details of abstract classes here.

To #define an abstract class, C# requires the abstract modifier to the class definition.

An ***[[abstract member]]*** is a method or property that has no implementation. Its purpose is to force all derived classes to provide the implementation.

Because abstract members are supposed to be overridden, such members are automatically virtual and cannot be declared so explicitly. In addition, abstract members cannot be private because derived classes would not be able to see them.

It is surprisingly difficult to develop a well-designed object hierarchy. For this reason, when programming abstract types, you should be sure to implement at least one (and preferably more) concrete type that derives from the abstract type to validate the design.

Abstract members are intended to be a way to enable [[Polymorphism]] . The base class specifies the signature of the method, and the derived class provides the implementation.

This way we can have a collection of type base class, consisting of different derived types. Then we can iterate through them all and call the same method, and each type performs its own implementation.
## [[Polymorphism]]
the implementation for the same member signature varies between.
polymorphism refers to the existence of multiple implementations of the same signature. Also, because the same signature cannot be used multiple times within a single class, each implementation of the member signature occurs on a different class.
The idea behind polymorphism is that the object itself knows best how to perform a particular operation.
So each derivation of a type knows how to perform a task for its own needs.

# All Classes Derive from `System.Object`
Given any class, whether a custom class or one built into the system, the methods shown in Table 7.2 will be defined.

![[Pasted image 20230804200132.png]]

all classes derive (either directly or via an inheritance chain) from `object`. Even literals include these methods, enabling somewhat peculiar-looking code such as this:

`Console.WriteLine( 42.ToString() );`

Even class definitions that don’t have any explicit derivation from object derive from object anyway.
When the object’s default implementation isn’t sufficient, programmers can override one or more of the three virtual methods.

# Type Checking
Since C# 1.0, there have been multiple ways to check an object’s type including the `is` operator and the switch statement. In C# 8.0, the switch expression was introduced to enable a value return from the expression.

## Type Checking with the `Is` Operator
Since C# allows casting down the inheritance chain, it is sometimes desirable to determine what the underlying type is before attempting a conversion. Also, checking the type may be necessary for type-specific actions where polymorphism was not implemented. To determine the type, C# has included an is operator since C# 1.0.

The advantage is that the `is` operator enables a code path for when the explicit cast might fail without the expense of exception handling. With the addition of declaration in C# 7.0 and later, the is operator (and switch syntax shown next) will make the type check and the assignment in the same step.

`if (entity is Employee employee)`

Here, in checking whether the input operand is an `employee`, it also declares a new variable of type `Employee`. The result from `GetObjectById(id)`, therefore, is checked against the `Employee` type— – type pattern matching— – and assigned to the variable `employee` in the same expression. If the result of `GetObjectById(id)` is null or not an `Employee`, then `false` is produced, and the else clause executes.

Note that the `employee` variable is available within and after the `if` statement; however, it would need to be assigned a value before accessing it inside or after the `else` clause. Also, you can implicitly specify the data type with `var`, as expected, but as with local variable declaration in general, use caution when the data type isn’t obvious.

## Type Checking with `switch` Statements
The switch statement also allows for type checking, but it can check for multiple types in addition to null checking.
we demonstrate this with a method that returns the time (using .NET 6.0’s `TimeOnly`), from a variety of types.

```C#
public static TimeOnly GetTime(object input)

{

    switch (input)

    {

        case DateTime datetime:

            return TimeOnly.FromDateTime(datetime);

        case DateTimeOffset datetimeOffset:

            return TimeOnly.FromDateTime(datetimeOffset.DateTime);

        case string dateText:

            return TimeOnly.Parse(dateText);

        case null:

           throw new ArgumentNullException(nameof(input));

        default:

            throw new ArgumentException(

                $"Invalid type - {input.GetType().FullName}");

    };

}
```

like with the `is` operator, you can declare a variable simultaneously, providing access to an instance of the object that matches the case.

## Type Checking with `switch` Expressions
Starting in C# 8.0, the switch statement includes a more succinct expression form that returns a value.

the switch expression (C# 8.0) provides multiple paths for each case— – generically a match expression (for both switch expressions and statements). Unlike the switch statement, there are no labels, no break statements, and, in fact, no enclosed statements at all. Most importantly, the switch expression returns a value (except when it throws an exception).

```C#
public static TimeOnly GetTime(object input) =>

    input switch

    {

        DateTime datetime

            => TimeOnly.FromDateTime(datetime),

        DateTimeOffset datetimeOffset

                => TimeOnly.FromDateTime(datetimeOffset.DateTime),

        string dateText => TimeOnly.Parse(

            dateText),

        null => throw new ArgumentNullException(nameof(input)),

        _ => throw new ArgumentException(

            $"Invalid type - {input.GetType().FullName}"),

    };
```

you will notice that unlike a switch statement, the input operand for the switch statement appears before the switch keyword. Each match expression is simply identified by the type, optionally followed by a variable declaration. Also, instead of the colon designating a label, the switch expression uses a [[lambda operator]] `=>`. Lastly, instead of the `default` keyword, the switch expression uses the discard symbol: `_` .

In the switch expression, the default `_` catch allcatchall, ***Must*** appear at the end or else it will obscure later match expression.
But in a switch statement, `default` provides the same functionality regardless of where it appears.

# Pattern Matching
The `is` operator and `switch` statement remained relatively static until C# 7.0 where declaration and significant pattern matching capabilities were added. 
Pattern matching provides a syntax through which criteria can be evaluated and the code execution branched accordingly. The C# team continued pattern matching enhancements all the way to C# 11.

## Constant Patterns (C# 7.0)
In Chapter 4, we demonstrated constant patterns when using the is operator to check for null (i.e., data is null) and not null and in explaining the basic switch statement:
![[Ch.04 Operators and Control flow#^d4268c]]
The principle is that you can compare the input operand against any constant.

`if (date.Day is 21)`

Constants can be any [[literal]] including strings, defined constants, and, of course, null. Note that the comparison of a string to the literal empty string "" will work, but comparing it to `string.Empty` will fail because `string.Empty` is not declared as a constant.

## Relational Patterns (C# 9.0)
With relational patterns you can compare to a constant relatively using the operators `<, >, <=, ` and` >=`.
Note that == is not one of the operators since this functionality is already available in the constant pattern matching.

```C#
public static bool IsDeveloperWorkHours(

    int hourOfTheDay) =>

        hourOfTheDay is > 10 &&

            hourOfTheDay is < 24;

public static string GetPeriodOfDay(int hourOfTheDay) =>

    hourOfTheDay switch

    {

        < 6 => "Dawn",

        < 12 => "Morning",

        < 18 => "Afternoon",

        < 24 => "Evening",

        int hour => throw new ArgumentOutOfRangeException(

            $"The hour of the day specified is invalid.", nameof(hourOfTheDay))

    };
```

It is important to note that starting in C# 7.0, match expressions are no longer necessarily mutually exclusive. For example, we could have a match expression of > 0 and another with < 24 and both would be true regardless of the hour. Order will make the final determination of which path the code execution follows. For this reason, use caution when ordering the match expressions in a switch statements and expressions (in the same way you would be careful about ordering a series of if statements).

## Logical Patterns (C# 9.0)
```C#
public bool IsStandardWorkHours(

    TimeOnly time) =>

        time.Hour is > 8

            and < 17

            and not 12; // lunch

static bool TryGetPhoneButton(

    char character,

    [NotNullWhen(true)] out char? button)

{

    return (button = char.ToLower(character) switch

    {

        '1' => '1',

        '2' or >= 'a' and <= 'c' => '2',

        // not operator and parethesis example (C# 10)

        '3' or not (< 'd' or > 'f') => '3',

        '4' or >= 'g' and <= 'i' => '4',

        '5' or >= 'j' and <= 'l' => '5',

        '6' or >= 'm' and <= 'o' => '6',

        '7' or >= 'p' and <= 's' => '7',

        '8' or >= 't' and <= 'v' => '8',

        '9' or >= 'w' and <= 'z' => '9',

        '0' or '+' => '0',

        _ => null,// Set the button to indicate an invalid value

    }) is not null;

}
```

The order of precedence is `not`, and, `or`.

## Parenthesized Patterns (C# 9.0)
To override the order of precedence of logical pattern operators you can group them together using parenthesis.

```C#
public bool IsOutsideOfStandardWorkHours(

    TimeOnly time) =>

        time.Hour is not

            (> 8 and < 17 and not 12); // Parentbhesis Pattern - C# 10.
```

Parenthesizing is available with both is operators and switch syntax.

## [Tuple Patterns (C# 8.0)](https://essentialcsharp.com/tuple-patterns-c-8-0#tuple-patterns-c-8-0)
With tuple pattern matching, you can check for constant values within the tuple or assign tuple items to a variable.

```C#
public static void Main(params string[] args)

{

    const int command = 0;

    const int fileName = 1;

    const string dataFile = "data.dat";

    // ...

    if ((args.Length, args[command].ToLower()) is (1, "cat"))

    {

        Console.WriteLine(File.ReadAllText(dataFile));

    }

    else if ((args.Length, args[command].ToLower())

        is (2, "encrypt"))

    {

        string data = File.ReadAllText(dataFile);

        File.WriteAllText(

            args[fileName], Encrypt(data).ToString());

    }

}
```

## [Positional Patterns Matching(C# 8.0)](https://essentialcsharp.com/positional-patterns-matchingc-8-0#positional-patterns-matchingc-8-0)
Building on the deconstructor construct introduced in C# 7.0 ![[Ch.06 Classes#^9d22d1]], C# 8.0 enables positional pattern matching with a syntax that closely matches tuple pattern matching.

# Avoid Pattern Matching When Polymorphism Is Possible
One scenario where polymorphism fails is when no object hierarchy matches your goals—if you are working with classes that are part of unrelated systems.
A second scenario is when functionality you’re adding isn’t part of the core abstraction for these classes
For example, the toll paid by drivers changes for different types of vehicles traveling on a toll road, but the toll isn’t a core function of the vehicle.

## Conversion Using the `as` Operator
In addition to the is operator, C# has an as operator. Originally, the as operator offered an advantage over the is operator: It not only checks whether the operand is of a specific type but also attempts a conversion to the particular data type and assigns `null` if the source type is not inherently (within the inheritance chain) of the target type. Furthermore, it provides an advantage over casting because it won’t throw an exception.

By using the as operator, you can avoid additional try/catch handling code if the conversion is invalid, because the as operator provides a way to attempt a cast without throwing an exception if the cast fails.

One advantage of the is operator over the as operator is that the latter cannot successfully determine the underlying type. The as operator may implicitly cast up or down an inheritance chain as well as to types supporting the cast operator. Unlike the as operator, the is operator can determine the underlying type. Also, the as operator works only with reference types, whereas the is operator works with all types.

More important, the as operator generally requires the additional step of checking the assigned variable for null. Since the pattern matching is operator includes this conditional check automatically, it effectively eliminates the need for the as operator—assuming C# 7.0 or later is available.