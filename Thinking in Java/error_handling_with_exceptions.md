##Error Handling with Exceptions
By providing a consistent **error-reporting model** using exceptions, Java allows components to reliably communicate problems to client code.

###Basic exceptions
---
An **exceptional condition** is a problem that prevents the continuation of the current method or scope.
> With an exceptional condition, you **cannot continue processing** because you don’t have the information necessary to deal with the problem in the current context.
> 
> All you can do is **jump out** of the **current context** and relegate that problem to a **higher context**. This is what happens when you throw an exception.

When you **throw an exception**, several things happen.
> First, the **exception object** is **created** in the same way that any Java object is created: on the **heap**, with **new**.
> 
> Then the current path of execution (the one you couldn’t continue) is **stopped** and the **reference** for the exception object is **ejected** from the current context.
> > At this point the **exception-handling mechanism** takes over and begins to **look for an appropriate place** to continue executing the program.
> 
> > This appropriate place is the **exception handler**, whose job is to **recover from the problem** so the program can **either try another tack or just continue**.

You can also think about **exceptions** as a **built-in undo system**, because (with some care) you can have **various recovery points** in your program.
> If a part of the program fails, the exception will **"undo" back to** a known **stable point** in the program.

One of the most important aspects of exceptions is that if something bad happens, they **don’t allow** a program to continue along its ordinary path. 
> Exceptions allow you to (if nothing else) **force the program to stop** and tell you **what went wrong**,
> 
> or (ideally) force the program to **deal with the problem** and **return to a stable state**.

There are **two constructors** in all standard exceptions:
> The first is the default constructor,
> 
> and the second takes a string argument so that you can place pertinent information in the exception.

After creating an exception object with new, you **give** the resulting **reference to throw**. You can **throw any type of Throwable**, which is the exception **root class**.

The **information about the error** is represented both **inside the exception object** and implicitly **in the name of the exception class** so someone in the bigger context can figure out what to do with your exception.

###Catching an exception
---
**Guarded region** is a section of code that **might produce exceptions** and is **followed by the code** to handle those exceptions.

With exception handling, you put everything in a **try block** and **capture** all the exceptions **in one place**.
> The thrown exception must **end up with exception handler**. Exception handlers immediately **follow the try block** and are denoted by the **keyword catch**.

Each catch clause (exception handler) takes **one and only one argument** of a particular type. If an **exception is thrown**,
> the exception-handling mechanism goes **hunting for the first handler** with an argument that **matches the type** of the exception.
> 
> Then it **enters** that catch clause, and the exception is **considered handled**.
> > The search for handlers **stops** once the catch clause is finished. **Only** the **matching** catch clause **executes**.

There are two basic models in **exception-handling theory**.
- **termination** (Java supportst)
> You assume that the error is so critical that there’s **no way to get back** to where the exception occurred.
- **resumption**
> The exception handler is expected to do something to rectify the situation, and then the faulting method is retried, presuming success the second time. 

If you want **resumption-like behavior** in Java, **don’t throw** an exception when you encounter an error. Instead, **call a method** that fixes the problem.
> Placing your try block **inside a while loop** that keeps reentering the try block until the result is satisfactory.

A **resumptive handler** would need to be **aware of where the exception** is thrown, and **contain non-generic code** specific to the throwing location.
> This makes the code **difficult to write and maintain**, especially for large systems where the exception can be generated from many points.

###Creating your own exceptions
---
You can create your own exceptions to **denote a special problem** that your library might encounter.
> To create your own exception class, you must **inherit from an existing exception class**.

You may also want to **log the output** using the **java.util.logging** facility. However, that all this **dressing-up** might be **lost on the client programmers** using your packages, since they might simply look for the exception to be thrown and nothing more.

###The exception specification
---
You’re encouraged to **inform** the client programmer, who calls your method, of the exceptions that **might be thrown** from your method.

Java provides syntax (and forces you to use that syntax) to allow you to politely tell the client programmer **what exceptions this method throws**, so the client programmer can handle them.
> This is the **exception specification** and it’s part of the **method declaration**, appearing after the argument list.

The exception specification **uses an additional keyword**, **throws**, followed by a list of all the potential exception types.
```
void f() throws TooBig, TooSmall, DivZero {}
```
> By enforcing exception specifications **from top to bottom**, Java guarantees that a certain level of exception correctness can be **ensured at compile time**.

Exceptions that are **checked and enforced at compile time** are called **checked exceptions**.

It is possible to create a handler that **catches any type of exception**. You do this by catching the base-class exception type **Exception**.
```
catch(Exception e) {
	System.out.println("Caught an exception");
}
```
> If you use it you’ll want to put it **at the end of your list** of handlers to avoid preempting any exception handlers that might otherwise follow it.

**Methods** of Exception's base type **Throwable**:
- **String getMessage()**
- **String getLocalizedMessage()**
> Gets the detail message, or a message adjusted for this particular locale.
- **String toString()**
> Returns a short description of the Throwable, including the detail message if there is one.
- **void printStackTrace()**
- **void printStackTrace(PrintStream)**
- **void printStackTrace(java.io.PrintWriter)**
> Prints the Throwable and the Throwable’s call stack trace.
> > The call stack shows the sequence of method calls that brought you to the point at which the exception was thrown.
> 
> The first version prints to standard error, the second and third print to a stream of your choice.
- **Throwable fillInStackTrace()**
> Records information within this Throwable object about the current state of the stack frames.

You also get some other **methods** from Throwable’s base type **Object**:
- **getClass()**
> returns an object representing the class of this object.
- **getName()**
> query this Class object for its name, which includes package information.
- **getSimpleName()** 
> produces the class name alone.

The information provided by printStackTrace() can also be accessed directly using **getStackTrace()**.
> This method returns **an array of stack trace elements**, each representing one stack frame. 
> 
> **Element zero** is the top of the stack, and is the **last method invocation** in the sequence (the point this Throwable was created and thrown).
> 
> The **last element** of the array and the bottom of the stack is the **first method invocation** in the sequence. 

Sometimes you’ll want to **rethrow the exception** that you just caught, you can simply rethrow that reference that reference to the current exception.
```
catch(Exception e) {
	System.out.println("An exception was thrown");
	throw e;
}
```
> Rethrowing an exception causes it to go to the exception handlers in the **nexthigher context**.
> 
> **Everything** about the exception object is **preserved**, so the handler at the higher context that catches the specific exception type can extract all the information from that object.

If you **simply rethrow** the current exception, the information that you print about that exception in printStackTrace() will **pertain to the exception’s origin**, not the place where you rethrow it.
> If you want to **install new stack trace information**, you can do so by calling **fillInStackTrace()**, which returns a Throwable object that it creates by **stuffing the current stack information** into the old exception object.
> > The information about the **original site of the exception is lost**, and what you’re left with is the information pertaining to the **new throw**.
> 
> It’s also possible to **rethrow a different exception** from the one you caught. If you do this, you get a **similar effect** as when you use fillInStackTrace().

Often you want to **catch one** exception and **throw another**, but still keep the information about the originating exception — this is called **exception chaining**.

All **Throwable subclasses** have the option to take a **cause object** in their constructor.
> The cause is **intended to be the originating exception**, and by passing it in you  **maintain** the stack trace back **to its origin**, even though you’re creating and throwing a new exception.

The only Throwable subclasses that **provide the cause argument** in the **constructor** are the three fundamental exception classes:
- **Error**
> used by the JVM to report system errors.
- **Exception**
- **RuntimeException**

If you want to **chain any other exception types**, you do it through the **initCause()** method rather than the constructor.

###Standard Java exceptions
---
The Java class **Throwable** describes anything that can be thrown as an exception. There are **two general types** of Throwable objects.
> **Error** represents compile-time and system errors that you don’t worry about catching
> 
> **Exception** is the basic type that can be thrown from any of the standard Java **library class methods** and from **your methods** and **runtime accidents**. 

You **never need** to write an **exception specification** saying that a method might throw a **RuntimeException** (or any type inherited from RuntimeException), because they are **unchecked exceptions**.
> They’re always **thrown automatically** by Java and you don’t need to include them in your exception specifications.
> 
> If a RuntimeException gets all the way out to main() without being caught, **printStackTrace() is called** for that exception **as the program exits**.

Only exceptions of type **RuntimeException** (and subclasses) **can be ignored** in your coding, since the compiler carefully **enforces** the handling of **all checked exceptions**.

A RuntimeException **represents a programming error**, which is:
> An **error** you **cannot anticipate**.
> > For example, a null reference that is outside of your control.
> 
> An **error** that you should have **checked for** in your code.
> An exception that happens from point #1 often becomes an issue for point #2.

###Performing cleanup with finally
---
There’s often some piece of code that you want to **execute whether or not an exception is thrown** within a try block. You use a **finally clause** at the end of all the exception handlers to achieve this effect.

In a language **without garbage collection** and **without automatic destructor calls**, finally is important because it allows the programmer to **guarantee the release of memory** regardless of what happens in the try block. 
> The finally clause is necessary in Java when you need to **set something** other than memory back to its **original state**.

Because a finally clause is **always executed**, it’s possible to **return from multiple points** within a method and still **guarantee** that important **cleanup** will be **performed**.

Although exceptions are an indication of a crisis in your program and should never be ignored, it’s **possible for an exception to simply be lost**. This happens with a **particular configuration** using a **finally clause**.

###Exception restrictions
---
**Overridden methods** may **throw only the exceptions** specified in their base-class versions, or exceptions **derived from** the base-class exceptions.

A **constructor** can **throw anything it wants**, regardless of what the base-class constructor throws.
> However, since a base-class constructor must always be called one way or another, the derived-class constructor must **declare any base-class constructor exceptions** in its exception specification.
> 
> A derived-class constructor **cannot catch exceptions** thrown by its base-class constructor.

By forcing the derived-class methods to **conform to** the exception specifications of the **base-class methods**, substitutability of objects is maintained.

If you’re dealing with exactly a **derived-class object**, the compiler **forces** you to catch **only the exceptions** that are specific to that class.
> But if you **upcast to the base type**, then the compiler (correctly) **forces** you to catch the exceptions for the **base type**.

Although exception specifications are **enforced** by the compiler **during inheritance**, the exception specifications are **not part of** the type of a method, which comprises **only the method name and argument types**.
> Therefore, you **cannot overload** methods **based on** exception specifications.
> 
> Just because an exception specification exists in a baseclass version of a method **doesn’t mean** that it must **exist** in the derived-class version of the method.
> > The "exception specification interface" for a particular method may **narrow** during **inheritance** and **overriding**, but it may **not widen**.

###Constructors
---
The **constructor** puts the object into a safe starting state, but it might **perform some operation** — such as opening a file - that **doesn’t get cleaned up** until the user is finished with the object and calls a special cleanup method. 
> If you **throw an exception** from **inside** a constructor, these **cleanup** behaviors might **not occur** properly.
> 
> finally **performs** the cleanup code **every time**. If a constructor fails partway through its execution, it might **not** have successfully **created some part** of the object that **will be cleaned up** in the finally clause.

One of the **design issues** with exceptions is **whether to handle** an exception completely **at this level**:
> to handle it **partially** and pass the same exception (or a different one) on,
> 
> or whether to **simply pass it on**.
> > Passing it on, when appropriate, can certainly **simplify coding**.

A general **cleanup idiom** should still be **used** if the constructor **throws no exceptions**. The basic rule is:
> Right after you create an object that requires cleanup, **begin a try finally**.

###Exception matching
---
When an exception is **thrown**, the exception-handling system **looks** through the **"nearest" handlers** in the order they are written.
> Matching an exception **doesn’t require a perfect match** between the exception and its handler.
> > A **derived-class** object will **match** a handler for the **base class**. 

You usually **use exceptions** to:
> **Handle problems** at the appropriate level.
> > Avoid catching exceptions unless you know what to do with them.
> 
> **Fix the problem** and call the method that caused the exception again.
> 
> **Patch** things up and **continue** without retrying the method.
> 
> **Calculate some alternative result** instead of what the method was supposed to produce.
> 
> Do whatever you can in the current context and **rethrow** the same exception to a **higher context**.
> 
> Do whatever you can in the current context and **throw a different exception** to a **higher context**.
> 
> **Terminate** the program.
> 
> Simplify.
> > If your exception scheme makes things more complicated, then it is painful and annoying to use.
> 
> Make your library and program safer.
> > This is a short-term investment for debugging, and a long-term investment for application robustness.

###Alternative approaches
---
One of the important goals of exception handling is to **move the error-handling code away** from the point where the errors occur.
> This allows you to **focus on** what you want to accomplish **in one section** of your code, and how you’re going to deal with problems **in a distinct separate section** of your code.
> 
> As a result, your mainline code is **not cluttered with error-handling logic**, and it’s much easier to understand and maintain.

**Checked exceptions** force you to **add catch clauses** in places where you may not be ready to handle an error.
> This results in the "**harmful if swallowed**" problem, programmers would just do the simplest thing, and "swallow" the exception — often unintentionally.
> 
> Once you do it, the **compiler** has been **satisfied**, so unless you remember to revisit and correct the code, the exception will be lost.

**main() method** is also a method that may have an **exception specification**, and here the type of exception is **Exception**, the root class of all checked exceptions.
> By passing it out to the console, you are **relieved from writing try-catch clauses** within the body of main().

Throwing an exception from main() is convenient, but is not generally useful. The real problem is when you are **writing an ordinary method** body, and you **call another method** and realize:
> "I have no idea what to do with this exception here, but I don’t want to swallow it or print some banal message."
> 
> With **chained exceptions**, a new and simple solution prevents itself.
> > You simply **"wrap" a checked exception** inside a **RuntimeException** by passing it to the RuntimeException constructor.
> 
> This technique provides the option to **ignore the exception** and let it **bubble up the call stack** without being required to write try-catch clauses and/or exception specifications.
> > However, you may still **catch and handle** the specific exception by **using getCause()**.
