```C#
// Define an interface with a method signature
public interface IAnimal
{
    void Speak();
}

// Implement the interface in two classes
public class Dog : IAnimal
{
    public void Speak()
    {
        Console.WriteLine("Woof!");
    }
}

public class Cat : IAnimal
{
    public void Speak()
    {
        Console.WriteLine("Meow!");
    }
}

// Use the interface to create a list of animals and call their Speak method
public class Program
{
    static void Main()
    {
        List<IAnimal> animals = new List<IAnimal>();
        animals.Add(new Dog());
        animals.Add(new Cat());

        foreach (IAnimal animal in animals)
        {
            animal.Speak();
        }
    }
}

```

The `Dog` class is a subclass of the `Object` class, and it also implements the `IAnimal` interface. This means that an instance of the `Dog` class can be treated as an instance of the `Object` class or the `IAnimal` interface, since it has all of the properties and methods defined by those types. 

When we add `new Dog()` to the `animals` list, it is being implicitly upcasted to the `IAnimal` type, which is the type of the list. This means that we are treating the `Dog` instance as an `IAnimal` instance, and only the methods and properties defined by the `IAnimal` interface are available to us.
This is different from upcasting to a superclass type, which is a common way to achieve polymorphism in object-oriented programming, but still similar in concept.