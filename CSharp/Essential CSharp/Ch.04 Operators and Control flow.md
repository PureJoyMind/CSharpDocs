# Operators
There are three operators categories—unary, binary, and ternary—, corresponding to the number of operands.
some operators are represented with symbols like `+`, `-`, `?.`, and `??`, other operators take the form of keywords, like `default` and `is`. 

The precedence for the operators used so far is as follows:

1. `*`, `/`, and `%` have the highest precedence.
2. `+` and `-` have lower precedence.
3. `=` has the lowest precedence of these six operators. 

the `char` type is an integral type (integral means it is based on an integer). It can participate in arithmetic operations with other integer types. However, interpretation of the value of the `char` type is not based on the character stored within it but rather on its underlying value.

```C#
int n = '3' + '4';
char c = (char)n;
Console.WriteLine(c);  // Writes out g
```

***AVOID*** using equality conditionals with binary floating-point types. Either subtract the two values and see if their difference is less than a tolerance, or use the decimal type. 

Dividing by 0 results in an error with data types such as `int` and `decimal`. The `float` and `double` types aloow for certain special values.
Dividing `float` numbers by 0 results in `NaN` (Not a Number). also `System.Math.Sqrt(-1)` will result in `NaN` as well. 
A floating-point number could overflow its bounds as well. For example, the upper bound of the `float` type is approximately 3.4 × 1038. Should the number overflow that bound, the result would be stored as positive infinity, and the output of printing the number would be `Infinity`. Similarly, the lower bound of a float type is −3.4 × 1038, and computing a value below that bound would result in negative infinity, which would be represented by the string `-Infinity`.
Further examination of the floating-point number reveals that it can contain a value very close to zero without actually containing zero. If the value exceeds the lower threshold for the float or double type, the value of the number can be represented as negative zero or positive zero, depending on whether the number is negative or positive, and is represented in output as -0 or 0.

## Increment and Decrement operators
C# makes two guarantees: 
1. ***The arguments to a call are always computed from left to right***
2. the assignment of the incremented value to the variable always 

```C#
public class IncrementExample
{
    public static void Main()
    {
        int x = 123;
        // Displays 123, 124, 125
        Console.WriteLine($"{x++}, {x++}, {x}");
        // x now contains the value 125
        // Displays 126, 127, 127
        Console.WriteLine($"{++x}, {++x}, {x}");
        // x now contains the value 127
    }
} 
```

Despite the brevity of the increment and decrement operators, these operators are not atomic. A thread context switch can occur during the execution of the operator and can cause a race condition. You could use a `lock` statement to prevent the race condition. However, for simple increments and decrements, a less expensive alternative is to use the thread-safe `Increment()` and `Decrement()` methods from the `System.Threading.Interlocked` class. These methods rely on processor functions for performing fast, thread-safe increments and decrements. 

Atomic operations are ones that cannot be interrupted partway through, such as by threading.

Race conditions occur in C# when we have a variable shared by several threads and these threads want to modify the variables simultaneously. The problem with this is that depending on the order of the sequence of operations done on a variable by different threads, the value of the variable will be different.

## Constant Expressions and Constant Locals
By definition, a constant expression is one that the C# compiler can evaluate at compile time (instead of evaluating it when the program runs) because it is composed entirely of constant operands. 

C# 10 added support for constant interpolated strings whereby which you can define a constant string with interpolation if it is comprised solely of other constant strings and, as such, it can be evaluated at compile time.
Constant string interpolation works when formed solely of other constant strings, not of other data types even if those types are constant. The restriction is to allow conversion to a string to account for culture variation at execution time. For example, converting the golden ratio to a string could be 1.6180339887 or 1,6180339887 depending on the culture.

# Null
Although you can check for `null` using the equality operators and even the relational equality operators, there are several other ways to do so, including the C# 7.0’s support for `is null` and C# 10.0’s support for `is not null` operator support.

[Checking for null and Not null](https://essentialcsharp.com/checking-for-null-and-not-null#checking-for-null-and-not-null)

## Null-Coalescing and Null-Coalescing Assignment Operators
The null-coalescing operator is a concise way to express “If this value is null, then use this other value.” It has the following form:

`expression1 ?? expression2`

The null-coalescing operator “chains” nicely. For example, an expression of the form `x ?? y ?? z` results in x if x is not null; otherwise, it results in y if y is not null; otherwise, it results in z. 

Null-coalescing assignment operator:
`fullName ??= $"{ directory }/{ fileName }";`

With this operator, you can evaluate if the left-hand side is null and assign the value on the righthand side if it is.

## Null-Conditional Operator
The null-conditional operator (`int? length = segments?.Length`) checks whether the operand is null prior to invoking the method or property.

The logically equivalent explicit code would be the following (although in the original syntax, the value of segments is evaluated only once):

`int? length = (segments != null) ? (int?)segments.Length : null`

 What makes the null-conditional operator especially convenient is that it can be chained (with and without more null-coalescing operators). For example, in the following code, both `ToLower()` and `StartWith()` will be invoked only if both `segments` and `segments[0]` are not null:

`segments?[0]?.ToLower().StartsWith("file:");`

When null-conditional expressions are chained, if the first operand is null, the expression evaluation is short-circuited, and no further invocation within the expression call chain will occur. You can also chain a null-coalescing operator at the end of the expression so that if the operand is null, you can specify which default value to use:
```c#
string uriString = segments?[0]?.ToLower().StartsWith(

     "file:")??"intellitect.com";
```

Notice that the data type resulting from the null-coalescing operator is not nullable (assuming the right-hand side of the operator \["intellitect.com" in this example\] is not null—which would make little sense).

Although perhaps a little peculiar (in comparison to other operator behavior), the return of a nullable value type is produced only at the end of the call chain. Consequently, calling the dot (.) operator on Length allows invocation of only `int` (not `int?`) members. However, encapsulating `segments?.Length` in parentheses—thereby forcing the `int?` result via parentheses operator precedence—will invoke the `int?` return and make the `Nullable<T>` specific members (`HasValue` and `Value`) available. In other words, `segments?.Length`.Value won’t compile because `int` (the data type returned by `Length`) doesn’t have a member called `Value`. However, changing the order of precedence using `(segments?.Length).Value` will resolve the compiler error because the return of `(segments?.Length)` is `int?` , so the `Value` property is available.

# Control Flow
## For loop
```
 for (initial ; condition ; loop)

   statement
```

The `initial` expression does not have to be a declaration of a new variable (though it frequently is). It is possible, for example, to declare the variable beforehand and simply initialize it in the for loop, or to skip the initialization section entirely by leaving it blank.

The for statement doesn’t require any of the elements in its header. The expression `for(;;){ ... }` is perfectly valid, although there still needs to be a means to escape from the loop so that it will not continue to execute indefinitely. (If the condition is missing, it is assumed to be the constant `true`.)

```C#
for (int x = 0, y = 5; ((x <= 5) && (y >= 0)); y--, x++)
{
    Console.Write(
        $"{ x }{ ((x > y)  ? '>' : '<')}{ y }\t");
} 
```

## Foreach Loop
```
 foreach(type variable in collection)

   statement
```

`variable` is a read-only variable into which the foreach loop will automatically assign the next item within the collection. The scope of the `variable` is limited to the body of the loop. 
***Note*** that the compiler prevents modification of the variable during the execution of a foreach loop.

## Switch
```
 switch (expression)

{

  case constant:

       statements

   default:

       statements

}
```

***DO NOT*** use `continue` as the jump statement that exits a switch section. This is legal when the switch is inside a loop, but it is easy to become confused about the meaning of `break` in a later switch section. 

`expression` is the value that is being compared against the different constants. The type of this expression determines the “governing type” of the switch. Allowable governing data types are `bool, sbyte, byte, short, ushort, int, uint, long, ulong, char`, any `enum` type, the corresponding nullable types of each of those value types, and `string`. 

In C++, if a switch section does not end with a jump statement, control “falls through” to the next switch section, executing its code. Because unintended fall-through is a common error in C++, C# does not allow control to accidentally fall through from one switch section to the next. The C# designers believed it was better to prevent this common source of bugs and encourage better code readability than to match the potentially confusing C++ behavior. If you do want one switch section to execute the statements of another switch section, you may do so explicitly with a goto statement, as demonstrated later in this chapter.

C# 7.0 introduced an improvement to the switch statement that enables pattern matching so that any data type—not just the limited few identified earlier—can be used for the switch expression. Pattern matching enables the use of switch statements based on the type of the switch expression and the use of case labels that also declare variables. Lastly, pattern matching switch statements support conditional expressions so that not only the type but also a Boolean expression at the end of the case label can identify which case label should execute. ^d4268c

# C# Preprocessor Directives 
Control flow statements evaluate expressions at execution time. In contrast, the C# preprocessor is invoked during compilation.
The preprocessor commands are directives to the C# compiler, specifying the sections of code to compile or identifying how to handle specific errors and warnings within the code. C# preprocessor commands can also provide directives to C# editors regarding the organization of code.

