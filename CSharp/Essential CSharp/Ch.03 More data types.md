# Categories of Types

Value type data is always copied by value, whereas reference type data is always copied by reference.

## Reference Types

Reference types store the reference where the data is located instead of storing the data directly, as value types do. Therefore, to access the data, the runtime reads the memory location out of the variable and then “jumps” to the location in memory that contains the data, an operation known as ***dereferencing***. The memory area of the data a reference type points to is called the ***heap***.

In practice, a reference is always the same size as the “native size” of the processor—a 32-bit processor will copy a 32-bit reference, a 64-bit processor will copy a 64-bit reference, and so on. Obviously, copying the small reference to a large block of data is faster than copying the entire block, as a value type would.

## Declaring Types That Allow `null`

Assigning null is especially useful in database programming. Frequently, columns in database tables allow null values. Retrieving such columns and assigning them to corresponding variables within C# code is problematic, unless the data type in C# can contain null as well.

The clearest way to check for null is with an if statement and the is operator.

```C#
public static void Main(string[] args)
{
   int? number = null;
   // ...
   if (number is null)
   {
      ...
   }
   else
   {
     	...
   }
} 
```

Another useful operator when working with `null` is the `null`-conditional operator. It first checks for `null` before dereferencing a nullable value. For example, `int? length = text?.Length;` will automatically return `null` if text has the value `null` and the length of the `string` stored in text otherwise. Notice that because the value returned from `text?.Length` could be `null` (if text is null), the variable length must be declared as nullable.

Nullable reference types bring reference types on par with value types, in that reference type declarations can occur with or without a nullable modifier. ***In C# 8.0, declaring a variable without the nullable modifier implies it is not nullable.*** ^c14efc

There are several mechanisms to configure nullability: the `#nullable` directive, project properties, and even the command line. First, the null reference type feature is activated in this example with the `#nullable` directive:

`#nullable enable`

The directive supports values of enable, disable, and restore—the last of which restores the nullable context to the project-wide setting.

# [Tuples](https://essentialcsharp.com/tuples#tuples)

# Arrays

## Declaring an Array

`string[] languages;`

The square brackets that are part of the declaration identify the rank, or the number of dimensions, for the array.

Commas within the square brackets define additional dimensions.

`int[,] cells;`

***Note***: the number of items that occur for a particular dimension is not part of the variable declaration. This is specified when creating (instantiating) the array and allocating space for each element.

It is not possible to specify the array size as part of the variable declaration.

Once an array is declared, you can immediately fill its values using a comma-delimited list of items enclosed within a pair of curly braces.

```c#
string[] languages = { "C#", "COBOL", "Java",
    "C++", "TypeScript", "Visual Basic",
    "Python", "Lisp", "JavaScript" }; 
```

Here we initialized the array.
The array size is required at initialization time unless an array literal is provided.

To assign the value after declaration requires the use of the keyword `new`.

```c#
string[] languages;

languages = new string[]{ "C#", "COBOL", "Java",
    "C++", "TypeScript", "Visual Basic",
    "Python", "Lisp", "JavaScript" }; 
```

Specifying the data type of the array (string) following new is optional as long as the compiler is able to deduce the element type of the array from the types of the elements in the array initializer.

C# also allows use of the new keyword as part of the declaration statement.

The use of the new keyword tells the runtime to allocate memory for the data type. It instructs the runtime to instantiate the data type—in this case, an array.

```c#
string[] languages = new string[]{
    "C#", "COBOL", "Java",
    "C++", "TypeScript", "Visual Basic",
    "Python", "Lisp", "JavaScript" }; 
```

Here the code explicitly creates a new array of strings with a length of 9 using the new operator and initializes its elements with the provided values in the curly braces. This is equivalent to creating an empty array and then using a loop or array indexing to set each element to the desired value.

Whenever you use the new keyword as part of an array assignment, you ***may*** also specify the size of the array within the square brackets. it is possible to assign an array but not specify the initial values of the array.

Assigning an array but not initializing the initial values will still initialize each element. The runtime initializes array elements to their default values, as follows:
• Reference types—whether nullable or not (such as `string` and `string?`)—are initialized to `null`.
• Nullable value types are all initialized to `null`.
• Non-nullable numeric types are initialized to 0.
• bool is initialized to `false`.
• char is initialized to `\0`.

Nonprimitive value types (like `struct`) are recursively initialized by initializing each of their fields to their default values. As a result, it is not necessary to individually assign each element of an array before using it.

it is possible to specify the size at runtime.

`int[,] cells = new int[3,3];`

```c#
int[,] cells = {
    {1, 0, 2},
    {1, 2, 0},
    {1, 2, 1}
}; 
```

The initialization follows the pattern in which there is an array of three elements of type `int[]`, and each element has the same size; in this example, the size is 3. Note that the sizes of each `int[]` element must all be identical.

it is also possible to define a ***jagged*** array, which is an array of arrays.

```c#
int[][] cells = {
   new [] {1, 0, 2, 0},
   new [] {1, 2, 0},
   new [] {1, 2},
   new [] {1}
}; 
```

Notice that a jagged array requires an array instance (or null) for each internal array. In the preceding example, you use new to instantiate the internal element of the jagged arrays. Leaving out the instantiation would cause a compile error. (As noted before, specifying the data type is optional when values are provided.)

Starting with C# 8.0, you can also access items relative to the end of the array using the index from end operator, also known as the ^(hat) operator.
item ^0 corresponds to one past the end of the list. Of course, there is no such element, so you can’t successfully index the array using ^0.
using the length of the array, 9 in this case, refers to an element off the end of the array. In addition, you can’t use negative values when indexing into an array.

Note that the index from end operator is not limited to a literal integer value. You can also use expressions such as

`languages[^langauges.Length]`

which will return the first item.

For a jagged array, `Length` returns the number of elements in the first array. Because a jagged array is an array of arrays, `Length` evaluates only the outside containing array and returns its element count, regardless of what is inside the internal arrays.

Another indexing-related feature added to C# 8.0 is support for array slicing—that is, extracting a slice of an array into a new array. The syntax for specifying a range is `..`, the range operator, which may optionally be placed between indices (including the indices from the end).

```C#
string[] languages = new [] {
    "C#", "COBOL", "Java",
    "C++", "TypeScript", "Swift",
    "Python", "Lisp", "JavaScript"}; 

languages[0..3] // C#, COBOL, Java
    
languages[^3..^0] // Python, Lisp, JavaScrip
    
languages[3..^3] // C++, TypeScript, Swift

languages[..^6]  // C#, COBOL, Java
    
languages[6..]  // Python, Lisp, JavaScript
```

The important thing to note about the range operator is that it identifies the items by specifying the first (inclusive) up to the end (exclusive).

*Specifying either the beginning index or “up to” (the end) index is optional*

if indices are missing entirely, it is equivalent to specifying `0..^0`.

Finally, note that indices and ranges are first-class types in .NET/C# (as described in the System.Index and System.Range advanced block). Their behavior is not limited to use in an array accessor.

## System.Index and System.Range

Using the index from end operator is a literal way of specifying a `System.Index` value. Therefore, you can use indexes outside the context of the square brackets. For example, you could declare an index and assign it a literal value: `System.Index index = ^42`. Regular integers can also be assigned to a `System.Index`. The `System.Index` type has two properties, a `Value` of type int, and an `IsFromEnd` property of type bool. The latter is obviously used to indicate whether the index counts from the beginning of the array or the end.

In addition, the data type used to specify a range is a `System.Range`. As such, you can declare and assign a range value: `System.Range range = ..^0` or even `System.Range range = ..`, for example. `System.Range` has two properties, `Start` and `End`.

By making these types available, C# enables you to write custom collections that support ranges and the “from the end” indices.

## Array methods

`Array.Sort(arr)`: sorts the array. It changes the array itself and does not create a new array.

`Array.BinarySearch(arr, searchString)`: Returns the index of the found item. Before using the `BinarySearch()` method, it is important to sort the array. If values are not sorted in increasing order, the incorrect index may be returned. If the search element does not exist, the value returned is negative.

The `Clear()` method does not remove elements of the array and does not set the length to zero. The array size is fixed and cannot be modified. Therefore, the `Clear()` method sets each element in the array to its default value (null, 0, or false).

## Array Instance Members

arrays have instance members that are accessed not from the data type but directly from the variable.
Other significant instance members besides `Length` are `GetLength()`, `Rank`, and `Clone()`.

Retrieving the length of a particular dimension does not utilize the `Length` property. To retrieve the size of a particular rank, an array includes a `GetLength()` instance method. When calling this method, it is necessary to specify the rank whose length will be returned.

```C#
bool[, ,] cells;
cells = new bool[2, 3, 3];
Console.WriteLine(cells.GetLength(0)); // Displays 2
Console.WriteLine(cells.Rank); // Displays 3
```

By default, assigning one array variable to another copies only the array reference, not the individual elements of the array. To make an entirely new copy of the array, use the array’s `Clone()` method. The `Clone()` method will return a copy of the array; changing any of the members of this new array will not affect the members of the original array.

## Strings as Arrays

because strings are immutable, it is not possible to assign particular characters within a string. C#, therefore, would not allow `palindrome[3]='a'`, where `palindrome` is declared as a string.

it is possible to retrieve the entire string as an array of characters using the string’s `ToCharArray()` method. Using this approach, you could reverse the string with the `System.Array.Reverse()` method.

***[Common Array Errors](https://essentialcsharp.com/common-array-errors#common-array-errors)***