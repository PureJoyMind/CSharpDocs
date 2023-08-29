```C#
public class Shape
    {
        public virtual void Draw()
        {
            Console.WriteLine("drawing a shape.");
        }
    }

public class Circle : Shape
    {
        public override void Draw()
        {
            Console.WriteLine("drawing a circle.");
        }
    }

public class Rect : Shape
    {
        public override void Draw()
        {
            Console.WriteLine("drawing a rect.");
        }
    }

internal class Program
    {
        static void Main(string[] args)
        {
            var shapes = new List<Shape>();
            shapes.Add(new Circle());
            shapes.Add(new Rect());

            foreach (Shape shape in shapes)
            {
                shape.Draw();
            }
        }
    }
```

Upcasting an object from a derived class to a base class does restrict access to the specific members of the derived class that are not present in the base class. However, when a virtual method is called on an object that is upcast to its base class, the runtime determines which implementation of the method to call based on the actual type of the object.

In the code you provided, the `Draw` method is marked as `virtual` in the base `Shape` class and overridden in the derived `Circle` and `Rect` classes. When you add instances of `Circle` and `Rect` to a list of `Shape` objects, you are upcasting those objects to the base class. However, when you call the `Draw` method on each object in the list, the runtime determines which implementation of the method to call based on the actual type of the object. This means that the overridden `Draw` method in each derived class is called when you iterate through the list and call the `Draw` method on each `Shape` object.

In summary, upcasting an object to a base class does not prevent the overridden method in the derived class from being called if it is marked as `virtual` and overridden in the derived class. The runtime will determine which implementation of the method to call based on the actual type of the object.