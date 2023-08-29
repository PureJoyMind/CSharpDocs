# Multiple Exception Types
C# allows code to throw any type that derives (perhaps indirectly) from `System.Exception`. To throw an exception, you simply prefix the exception instance with the keyword `throw`.

`ArgumentNullException` should be thrown for the inappropriate passing of null arguments. This is a special case of an invalid argument exception that would more generally (when it isn’t null) be thrown as an `ArgumentException` or an `ArgumentOutOfRangeException`.

Several other exceptions are intended only for the runtime and derive (sometimes indirectly) from `System.SystemException`. They include `System.StackOverflowException`, `System.OutOfMemoryException`, `System.Runtime.InteropServices.COMException`, `System.ExecutionEngineException`, and `System.Runtime.InteropServices.SEHException`. Do not throw exceptions of these types. Similarly, you should avoid throwing a `System.Exception` or `System.ApplicationException`, as these exceptions are so general that they provide little indication of the cause of or resolution to the problem. Instead, throw the most derived exception that fits the scenario.

if the executing code reaches a certain state such that continuing to execute is unsafe or unrecoverable, it should call `System.Environment.FailFast()`. This will immediately terminate the process after potentially writing a message to standard error and, on Microsoft Windows, the Windows Application event log.

# Catching Exceptions
Throwing a particular exception type enables the catcher to use the exception’s type itself to identify the problem. It is not necessary, in other words, to catch the exception and use a switch statement on the exception message to determine which action to take in light of the exception. Instead, C# allows for multiple catch blocks, each targeting a specific exception type.

When an exception occurs, the execution will jump to the catch block with the exception type that most closely matches the exception.

Instead of limiting whether a catch block matches based only on an exception type match, there is also a conditional clause. This `when` clause allows you to supply a Boolean expression; the catch block handles the exception only if the condition is true.
Of course, you could also simply place the conditional check as an if block within the catch body. However, doing so causes the catch block to become the handler for the exception before the condition is checked. It is difficult to write code that allows a different catch block to handle the exception in the scenario where the condition is not met

Catch blocks must appear in order, from most specific to most general, to avoid a compile-time error. For example, moving the `catch(Exception...)` block before any of the other exceptions will result in a compile error, since all prior exceptions derive from `System.Exception` at some point in their inheritance chain.

# Rethrowing an Existing Exception
Throwing a specific exception would update all the stack information to match the new throw location. As a result, all the stack information indicating the call site where the exception originally occurred would be lost, making it significantly more difficult to diagnose the problem. For this reason, C# supports a throw statement or expression without the explicit exception reference as long as it occurs within a catch block.
This way, code can examine the exception to determine if it is possible to fully handle it and, if not, rethrow the exception (even though not specified explicitly) as though it was never caught and without replacing any stack information.

There is a mechanism that enables the throwing of a previously thrown exception without losing the stack trace information in the original exception. This allows you to rethrow exceptions, for example, even from outside a catch block and, therefore, without using `throw;`. 
`System.Runtime.ExceptionServices.ExceptionDispatchInfo` class is specifically designed to handle this scenario through the use of its static `Capture()` and instance `Throw()` methods. 

```C#
 catch (AggregateException exception)
        {
            exception = exception.Flatten();
            ExceptionDispatchInfo.Capture(
                exception.InnerException??exception).Throw();
        }
```

With the `ExceptionDispatchInfo.Throw()` method, the compiler doesn’t treat the code as a return statement in the same way that it might a normal throw statement. For example, if the method signature returned a value but no value was returned from the code path with `ExceptionDispatchInfo.Throw()`, the compiler would issue an error indicating no value was returned. On occasion, therefore, developers may be forced to follow `ExceptionDispatchInfo.Throw()` with a return statement even though such a statement would never execute at runtime—the exception would be thrown instead.


# General Catch Block
All exceptions, whether derived from `System.Exception` or not, will propagate into C# assemblies as derived from `System.Exception`. The result is that `System.Exception` catch blocks will catch all exceptions not caught by earlier blocks. 

C# also supports a general catch block (`catch{ }`) that behaves identically to the `catch(System.Exception exception)` block, except that there is no type or variable name. Also, the general catch block must appear last within the list of catch blocks. Since the general catch block is identical to the `catch(System.Exception exception)`block and the general catch block must appear last, the compiler issues a warning if both exist within the same try/catch statement because the general catch block will never be invoked.

# [Guidelines](https://essentialcsharp.com/guidelines-for-exception-handling#guidelines-for-exception-handling)
* Catch only the exceptions that you can handle.
* Don’t hide (bury) exceptions you don’t fully handle.
* Use `System.Exception` and general catch blocks rarely.
* Avoid exception reporting or logging lower in the call stack.
* Use `throw;` rather than `throw <exception object>` inside a catch block.
	* It is possible to rethrow an exception inside a catch block. For example, the implementation of `catch(ArgumentNullException exception)` could include a call to `throw exception`. However, rethrowing the exception like this will reset the stack trace to the location of the rethrown call instead of reusing the original throw point location. Therefore, unless you are rethrowing with a different exception type or intentionally hiding the original call stack, use `throw;` to allow the same exception to propagate up the call stack.
* Favor exception conditions to avoid rethrowing an exception inside a catch block.
* Use caution when rethrowing different exceptions.
	* From inside a catch block, rethrowing a different exception will not only reset the call stack but also hide the original exception. To preserve the original exception, set the new exception’s InnerException property, generally assignable via the constructor. 

# Defining Custom Exceptions
The only requirement for a custom exception is that it derives from `System.Exception` or one of its descendants. Other good practices for custom exceptions are as follows:

* Generally, all exceptions should include constructors that take no parameters, a string parameter, and a parameter set consisting of a string and an inner exception. Furthermore, since exceptions are usually constructed within the same statement in which they are thrown, any additional exception data should be allowed as part of the constructor. (The obvious exception to creating all these constructors is if certain data is required and a constructor would circumvent the requirements.)
* The inheritance chain should be kept relatively shallow (with fewer than approximately five levels).

# Rethrowing a Wrapped Exception
On occasion, an exception thrown at a lower level in the stack will no longer make sense when caught at a higher level.


# #Serializable Objects

Serializable objects are objects that the runtime can persist into a stream—a file stream, for example—and that can then be reinstantiated out of the stream.

