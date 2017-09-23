##Initialization & Cleanup
###Guaranteed initialization with the constructor
---
Java guarantee **initialization of every object** by providing a constructor. If you create a class that has no constructors, the compiler will **automatically create a default constructor** for you.
> The constructor is an unusual type because it has **no return value**. This is distinctly different from a void return value.
> 
> In Java, creation and initialization are **unified concepts**. You can't have one without the other.

###Method overloading
---
**Method overloading** is essential to allow the same method name to be used **with different argument types**. Each overload method must take a unique list of argument types. Even **differences in the ordering of arguments** are sufficient to distinguish two methods, but better not do that way.

A primitive can be **automatically promoted** from a smaller type to a larger one, and this can be slightly confusing in combination with overloadings.
> if you have a data type that is **smaller** than the argument in the method, that data type is **promoted**.
> 
> if your argument is **wider**, then you must perform a **narrowing conversion** with a cast.

The **return value can't** be used to **distinguish overloaded methods**, because you can call a method and ignore the return value (call a method for its side effect), and Java can't determine which method should be called.

###The this keyword
---
The **this keyword** (only inside a **non-static method**) produces the **reference to the object** that the method has been called for. You can treat the reference to the object like any other object reference.
> In a constructor, the this keyword takes on a different meaning when you **give it an argument list**. It makes an **explicit call to the constructor** that matches that argument list.

Making a **method static** means that there is **no this** for that particular method.
> you **can not call non-static methods** from **inside static methods** (the reverse is possible).
> 
> you can call a static method for the class itself (without any objects).
> 
> Putting the static method inside a class **allows it access to** other static methods and to static fields.

###Cleanup: finalization and garbage collection
---
The garbage collector only knows how to **release memory** allocated with **new**, if your object **allocates “special” memory** without using new, it won’t know how to release the object’s “special” memory.
> To handle this case, Java provides a **finalize() method** that you can define for your class.
> 
> When the garbage collector is ready to release the storage used for your object, it will **first call finalize()**, and only on the next garbage-collection pass will it reclaim the object’s memory.

In C++, objects always get destroyed, whereas in Java,
- Your objects might **not get garbage collected**.
- Garbage collection is **not destruction**.
> If there is some activity that must be performed before you no longer need an object, you must perform that activity yourself.
>  
> Java has **no destructor** or similar concept, so you must create an ordinary method to perform this cleanup.
>
> If your program completes and the garbage collector never gets around to releasing the storage for any of your objects, that storage will be **returned to the operating system** en masse as the program exits.
- Garbage collection is **only about memory**.
> The **sole reason** for the existence of the garbage collector is to **recover memory** that your program is no longer using.
> 
> So any activity that is associated with garbage collection, most notably your finalize() method, must also be **only about memory and its deallocation**.

The garbage collector takes care of the **release of all object memory** regardless of how the object is created. It turns out that the need for finalize() is limited to special cases in which your object can **allocate storage in some way** other than creating an object.
> **Native methods**, which are a way to **call non-Java code** from Java, may allocating memory using a mechanism (C’s malloc() family of functions) other than the normal one in Java.
> 
> You’d need to **call free()** in a native method **inside your finalize()** to release the storage.

In C++, all objects are destroyed.
> If the C++ object is created as a **local** (on the stack), then the **destruction** happens at the closing curly brace of the scope in which the object was created.
> 
> If the object was created using **new**, the **destructor** is called when the programmer calls the C++ operator **delete**.
> > If not call delete, the destructor is never called, and you have a **memory leak**.

Java **doesn’t** allow you to create **local objects** — you must **always use new**. There’s no “delete” for releasing the object, the garbage collector will release the storage for you.
> However, if you want **some kind of cleanup** performed other than storage release, you must still **explicitly call an appropriate method** in Java, which is the equivalent of a C++ destructor without the convenience.

**Neither** garbage collection **nor** finalization is **guaranteed**. If the JVM **isn’t** close to **running out of memory**, then it might **not waste time** recovering memory through garbage collection.

It appears that finalize() is only useful for **obscure memory cleanup** that most programmers will never use. However, there is an interesting use of finalize() that **does not rely on it being called every time**. This is the verification of the **termination condition** of an object.
> At the point that you’re **no longer interested in an object** — when it’s ready to be cleaned up — that object should be **in a state** whereby its memory can be **safely released**.
> > If the object represents an **open file**, that file should be closed by the programmer before the object is garbage collected.

The **garbage collector** can have a significant impact on **increasing the speed of object creation**. It means that allocating storage for heap objects in Java can be nearly **as fast as creating storage on the stack** in other languages.

You can think of the **C++ heap** as a yard where each object stakes out its own piece of turf. This real estate can become abandoned sometime later and must be reused.
> In some JVMs, the **Java heap** is more like a conveyor belt that moves forward every time you allocate a new object.
> > This means that object storage allocation is **remarkably rapid**. The “heap pointer” is **simply moved forward** into virgin territory, so it’s effectively the same as C++’s stack allocation.

A simple but slow garbage-collection technique is called **reference counting**.
> This means that each object **contains a reference counter**, and every time a reference is attached to that object, the reference count is **increased**. Every time a reference goes out of scope or is set to null, the reference count is **decreased**.
> 
> The garbage collector **moves through** the entire list of objects, and when it finds one with a reference count of zero it releases that storage.
> 
> The one **drawback** is that if objects **circularly refer** to each other they can have nonzero reference counts while still being garbage.
> > Locating such **self-referential groups** requires significant extra work for the garbage collector.

In **faster schemes**, garbage collection is based on the idea that any **non-dead object** must ultimately be **traceable back to a reference** that lives either on the **stack** or in **static storage**.
> The chain might go through **several layers of objects**. Thus, if you start in the stack and in the static storage area and **walk through all the references**, you’ll find all the live objects.
> 
> For **each reference** that you find, you must **trace into the object** that it points to and then **follow all the references** in that object, tracing into the objects they point to, etc., until you’ve **moved through the entire Web** that originated with the reference on the stack or in static storage.
> 
> Each object that you move through **must still be alive**. The detached self-referential groups are simply **not found**, and are therefore **automatically garbage**.

In the approach described here, the JVM uses an **adaptive garbage-collection scheme**, and what it does with the live objects that it locates **depends on the variant** currently being used.
- **stop-and-copy**
> This means that the program is first **stopped**. Then, each live object is **copied from one heap to another**, leaving behind all the garbage.
> 
> In addition, as the objects are copied into the **new heap**, they are **packed end-to-end**, thus compacting the new heap.

When an object is **moved** from one place to another, all **references** that point at the object must be **changed**. The reference that goes from the heap or the static storage area to the object can be changed **right away**, but there can be other references pointing to this object that will be **encountered later** during the “walk.” These are fixed up as they are found.

There are two issues that make these so-called “copy collectors” **inefficient**. 
> The first is the idea that you have **two heaps** and you slosh all the memory back and forth between these two separate heaps, **maintaining twice** as much memory as you actually need.
> > Some JVMs deal with this by **allocating the heap in chunks** as needed and simply **copying from one chunk to another**.
> 
> The second issue is the **copying process** itself. Once your program becomes **stable**, it might be generating little or **no garbage**. Despite that, a copy collector will **still copy all the memory** from one place to another, which is **wasteful**.
> > Some JVMs detect that **no new garbage** is being generated and **switch to a different scheme** (this is the “adaptive” part).
> 
> > This other scheme is called **mark-and-sweep**. For general use, mark-and-sweep is fairly slow, but when you know you’re generating **little or no garbage**, it’s **fast**.

**Mark-and-sweep** follows the same logic of starting from the stack and static storage, and **tracing** through all the references to **find live objects**.
> Each time it finds a live object, that object is **marked by setting a flag** in it, but the object isn’t collected yet. Only when the marking process is finished **does the sweep occur**.
> 
> During the sweep, the **dead objects are released**. However, no copying happens, so if the collector chooses to **compact a fragmented heap**, it does so by shuffling objects around. 

**Stop-and-copy** refers to the idea that this type of garbage collection is **not done in the background**; instead, the **program is stopped** while the garbage collection occurs.
> The Sun garbage collector stopped the program when memory got low. Mark-and-sweep also requires that the program be stopped.

In the JVM described here memory is **allocated in big blocks**. If you allocate a large object, it gets its own block.
> Strict **stop-and-copy** requires **copying every live object** from the source heap to a new heap before you can free the old one, which translates to lots of memory.
> 
> With blocks, the garbage collection can typically **copy objects to dead blocks** as it collects.

Each block has a **generation count** to keep track of whether it’s alive. In the normal case, only the blocks created since the last garbage collection are **compacted**; all other blocks **get their generation count bumped** if they have been referenced from somewhere.
> This handles the normal case of lots of **short-lived temporary objects**. Periodically, a **full sweep** is made — large objects are still not copied (they just get their generation count bumped), and blocks containing **small objects** are **copied and compacted**.
> 
> The JVM **monitors the efficiency** of garbage collection and if it becomes a **waste of time** because all objects are **long-lived**, then it **switches to mark-and-sweep**. Similarly, the JVM **keeps track of** how successful mark-and-sweep is, and if the heap starts to become **fragmented**, it switches **back to stop-and-copy**.

There are a number of additional speedups possible in a JVM. An especially important one involves the **operation of the loader** and what is called a **JIT** (just-in-time) **compiler**.
> A JIT compiler partially or fully **converts** a program into **native machine code** so that it **doesn’t** need to be **interpreted by the JVM** and thus runs much faster.

When a class must be **loaded** (typically, the first time you want to create an object of that class), the .class file is **located**, and the **bytecodes** for that class are **brought into memory**.
> At this point, one approach is to simply **JIT compile all the code**, but this has two **drawbacks**:
> > It takes a little **more time**, which, compounded throughout the life of the program, can add up.
> 
> > It **increases the size** of the executable (bytecodes are significantly more compact than expanded JIT code), and this might **cause paging**, which definitely slows down a program.
> 
> An alternative approach is **lazy evaluation**, which means that the code is **not JIT compiled until necessary**.
> Code that never gets executed might never be JIT compiled.
> 
> The Java **HotSpot technologies** in recent JDKs take a similar approach by **increasingly optimizing a piece of code** each time it is executed, so the more the code is executed, the faster it gets.

###Member initialization
---
Java goes out of its way to guarantee that variables are **properly initialized** before they are used.
> In the case of a method’s **local variables**, if the local variables isn't initialized, you'll get a **compile-time error**.
> 
> If a primitive is a **field in a class**, each primitive field of a class is guaranteed to get an **initial value**.
> 
> When you define an **object reference** inside a class without initializing, it is given a special value of **null**.

Within class, the **order of initialization** is determined by the order that the variable are defined with the class, the variable are initialized **before any methods can be called** (even the constructor).

There's only a single piece of storage for a **static**, you **can't** apply the static keyword to **local variables**, so it **only applies to fields**.
> If a field is a static primitive and you don’t initialize it, it gets the standard initial value for its type.
> 
> If it’s a reference to an object, the default initialization value is null.

**static initializations** is executed **only once**:
- the first time you **make an object** of that class,
- or the first time you **access a static member** of that class.

The order of initialization is **statics first**, and **then the non-static objects**.

To summarize the **process of creating an object**, consider a class called Dog:
> The **constructor** is actually a **static method**.
> > The first time an object of type Dog is created, or the first time a static method or static field of class Dog is accessed, the Java interpreter must **locate Dog.class**, which it does by **searching through the classpath**.
> 
> As Dog.class is loaded (creating a Class object), all of its static initializers are run. thus, static initialization takes place only once, as the **Class object** is loaded for the first time.
> 
> When you create a **new** Dog(), the construction process for a Dog object first allocates enough storage for a Dog object **on the heap**.
> > This storage is **wiped to zero**, automatically setting all the primitives in that Dog object to their default value and the reference to null.
> 
> > Any **initializations** that occur at the point of **field definition** are executed.
> 
> > Constructors are executed. this might actually **involve a fair amount of activity**, especially when inheritance is involved.

Java allows you to group other static initializations inside a special "**static clause**" in a class. It also provides a similar syntax, called **instance initialization**, for **initializing non-static variables** for each object.
> The instance initialization clause is executed **before** either one of the constructors.
> 
> This syntax is necessary to support the **initialization of anonymous inner classes**, but it also allows you to **guarantee** that **certain operations occur** regardless of which explicit constructor is called.

###Array initialization
---
To define an array reference, you simple follow your type name with empty square brackets. The compiler **dosen't** allow you to tell it **how big the array** is, all you have at this point is a reference to an array, and there's no space allocated for the array object itself. Array creation is actually **happening at run time**.

All arrays have an **intrinsic member** (**length() method**) that you can query to tell you how many elements there are in the array.

There are **three forms** to initialize arrays of objects:
```
Integer[] a = new Integer[rand.nextInt(20)];
Integer[] a = { new Integer(1), new Integer(2), 3, };
Integer[] a = new Integer[] { new Integer(1), new Integer(2), 3, };
```
> Although the first form is useful, it's more limited because it only be used at the point **where array is defined**.
> 
> You can use the second and third forms anywhere, even **inside a method call**.
> 
> The third form provides a convenient syntax to create and call methods that can produces an effect similar to C's variable arguments (varargs).
> > These can include **unknown quantities** of arguments as well as **unknown types**.
> 
> > Varargs do work in harmony with **autoboxing**, it can promotes some arguments.
> 
> >The complier is **using autoboxing** to match the overloaded method.

Since all classes are ultimately inherited from the common root class Object, you can create a method that **takes an array of Object** and call it.

###Enumerated types
---
To use an enum, you create a reference of that type and assign it to an instance. The complier **automatically adds useful features** when you create an enum.
- **toString()**
> display the **name** of an enum instance.
- **ordinal()**
> indicate the **declaration order** of a particular enum constant
- **values()**
> produces **an array of values** of the enum constants in the order that they were declared.

Although enums appear to be a new data type, the keyword only produces some complier behavior while generating a class for the enum, so in many ways you can **treat an enum as if it were any other class**. In fact, enums are classes and have their own methods.
