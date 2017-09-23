##Type Information
###The need for RTTI
---
**Runtime type information** (RTTI) allows you to discover and use type information while a program is running.
> At run time, the type of an object is **identified**.

Java has two ways to discover information about objects and classes **at run time**.
- **tranditional RTTI**
> which assumes that you have all the types available at **compile time**.
- **reflection mechanism**
> which allows you to discover and use class information solely at **run time**.

You want the bulk of your code to **know as little as possible** about **specific types** of objects, and to just deal with the **general representation** of a family of objects.
> As a result, your **code** will be **easier to write, read, and maintain**, and your **designs** will be **easier to implement, understand, and change**.

If you have a special programming problem that’s **easiest to solve if you know the exact type** of a generic reference,  with RTTI, you can ask a **generic reference** the exact type that it’s referring to, and thus **select and isolate special cases**.

###The Class object
---
The **type information** is represented at run time is accomplished through the **Class object**, which contains **information about the class**.
> The Class object is used to create all of the "regular" objects of your class. Java **performs its RTTI using the Class object**, even if you’re doing something like a cast.

Each time you write and compile a **new class**, a single **Class object** is also created (and stored in an identically named .class file). To **make an object** of that class, the JVM that’s **executing your program** uses a subsystem called a **class loader**.

The **class loader subsystem** can actually comprise **a chain of class loaders**, but there’s only one **primordial class loader**, which is part of the JVM implementation. The primordial class loader loads so-called **trusted classes**, including Java API classes, typically from the **local disk**.
> If you have **special needs** (such as loading classes in a special way to support Web server applications, or downloading classes across a network), you have a way to **hook in additional class loaders**.

All classes are **loaded** into the JVM **dynamically**, upon the **first use** of a class. This happens when the program makes the **first reference to a static member** of that class.
> It turns out that the **constructor** is also a **static method** of a class, so creating a new object of that class using the new operator also counts as a reference to a static member of the class.

Thus, a Java program **isn’t completely loaded** before it begins, but instead pieces of it are loaded when necessary.
> The class loader first checks to see if the **Class object** for that type is **loaded**.
> 
> If not, the default class loader **finds the .class file** with that name.
> > An add-on class loader might look for the bytecodes **in a database** instead.
> 
> > As the bytes for the class are loaded, they are **verified** to ensure that they have not been corrupted and that they do not comprise bad Java code.
> 
> Once the Class object for that type is **in memory**, it is used to **create all objects** of that type.

Class object's **static forName() method** takes a String of the particular class you want a reference for and **returns a Class reference**.
> This method has a **side effect**, which is to **load the specified class** if it isn’t already loaded. In the process of loading, the class’s **static clause is executed**.

If you already **have an object** of the type you’re interested in, you can **fetch the Class reference** by calling a method that’s part of the Object root class: **getClass()**.
> This **returns the Class reference** representing the actual type of the object. 

Some **methods of Class** class:
- **String getName()**
> Returns the name of the entity (class, interface, array class, primitive type, or void) represented by this Class object.
- **boolean isInterface()**
> Determines if the specified Class object represents an interface type.
- **String getSimpleName()**
> Returns the simple name of the underlying class as given in the source code.
- **String getCanonicalName()**
> Returns the canonical name of the underlying class as defined by the Java Language Specification.
- **Class<?>[] getInterfaces()**
> Determines the interfaces implemented by the class or interface represented by this object.
- **Class<? super T> getSuperclass()**
> Returns the Class representing the superclass of the entity (class, interface, primitive type or void) represented by this Class.
- **T newInstance()**
> Creates a new instance of the class represented by this Class object.
- **T cast(Object obj)**
> Casts an object to the class or interface represented by this Class object.
- **boolean isInstance(Object obj)**
> Determines if the specified Object is assignment-compatible with the object represented by this Class.
> 
> Provides a way to dynamically test the type of an object.
- **boolean isAssignableFrom(Class<?> cls)**
> Determines if the class or interface represented by this Class object is either the same as, or is a superclass or superinterface of, the class or interface represented by the specified Class parameter.

Java provides a second way to **produce the reference** to the Class object: the **class literal**. 
> It’s checked at **compile time** and thus does **not need** to be placed in a **try block**.
> 
> It **eliminates the forName()** method call, so it’s more efficient.

Class literals **work with** regular classes as well as **interfaces**, **arrays**, and **primitive types**. 

There’s a **TYPE field** that exists for each of the **primitive wrapper classes**. This field **produces a reference** to the Class object for the associated primitive type.

Creating a reference to a Class object using ".class" **doesn’t** automatically **initialize** the Class object. There are actually three steps in **preparing a class for use**:
- **Loading**
> which is performed by the **class loader**. This **finds the bytecodes** (usually, but not necessarily, on your disk in your classpath) and creates a Class object from those bytecodes.
- **Linking**
> the link phase **verifies the bytecodes** in the class, **allocates storage** for static
fields, and if necessary, **resolves all references** to other classes made by this class.
- **Initialization**
> if there’s a superclass, initialize that. Execute **static initializers** and **static initialization blocks**.

The **generic class reference** can only be assigned to its **declared type**. By **using the generic syntax**, you allow the compiler to enforce **extra compile-time type checking**.
> To **loosen the constraints** when using generic Class references, you can use the **wildcard** (?).

The **benefit of Class<?>** is that it indicates that you **aren’t **just **using a non-specific class reference** by accident, or out of ignorance.
> In order to create a Class reference that is **constrained to a type** or any subtype, you combine the wildcard with the **extends keyword** to create a bound.

Java SE5 adds a **casting syntax** for use with Class references, which is the **cast() method**. It takes the argument object and casts it to the type of the Class reference.
> The new casting syntax is **useful for situations** where you **can’t just use an ordinary cast**.
> > This usually happens when you’re **writing generic code**, and you’ve **stored a Class reference** that you want to use to cast with at a later time.

###Checking before a cast
---
The forms of RTTI including:
- The **classic cast**.
> which **uses RTTI** to make sure the cast is correct.
- The **Class object** representing the **type of your object**.
> the Class object can be **queried** for useful **runtime information**.
- **instanceof keyword**  
> which tells you if an object is **an instance of a particular type**.
> 
> it’s important to **use instanceof before a downcast** when you don’t have other information that tells you the type of the object.

The classic cast perform the **type check**, this cast is often called a **type-safe downcast**. If casting a derived-class to a super-class is an **upcast**, then casting a super-class to a derived-class is a **downcast**.
> The compiler knows that a derived-class is **also a super-class**, so it **freely allows** an upcast assignment, without requiring any explicit cast syntax.
> 
> The compiler **cannot know**, given a super-class, **what that derived-class actually is** — it could be exactly a super-class, or it could be a subtype of super-class.
> > At **compile time**, the compiler **only sees a super-class**.

When you are **querying for type information**, there’s an **important difference between**
> either form of **instanceof**.
> > instanceof or islnstance().
> 
> > instanceof and islnstance() **produce exactly the same results**, as do **equals() and ==**. instanceof says, "Are you this class, or a class derived from this class?"
> 
> and the direct comparison of the **Class objects**.
> > if you compare the actual Class objects **using ==**, there is **no concern with inheritance** — it’s either the exact type or it isn’t.

###Reflection: runtime class information
---
By using RTTI, the **compiler must know** about **all the classes** you’re working with. If you’re given a reference to an object that’s **not in your program space**, or the class of the object isn’t even available to your program at compile time, the use of **RTTI is limited**.

The **class Class** supports the concept of **reflection**, along with the **java.lang.reflect** library which contains the classes **Field**, **Method**, and **Constructor**. Objects of these types are **created by the JVM at run time** to represent the **corresponding member in the unknown class**.
> You can use the Constructors to **create new objects**.
> 
> the get() and set() methods to **read and modify the fields** associated with Field objects.
> 
> and the invoke() method to **call a method** associated with a Method object.
> 
> You can call the Class's convenience methods getFields(), getMethods(), getConstructors() to return **arrays of the objects representing the fields, methods, and constructors**.

When you’re using reflection to interact with **an object of an unknown type**, the JVM will simply **look at the object** and see that it **belongs to a particular class** (just like ordinary RTTI).
> Before anything can be done with it, the **Class object must be loaded**. Thus, the **.class file** for that particular type must still be **available to the JVM**, either on the local machine or across the network.

The true **difference between RTTI and reflection** is that:
> with **RTTI**, the compiler opens and examines the .class file **at compile time**. You can call all the methods of an object in the "normal" way.
> 
> With **reflection**, the **.class file** is **unavailable at compile time**; it is opened and **examined by the runtime environment**.

###Dynamic proxies
---
**Proxy** is **an object** that you insert **in place of the "real" object** in order to **provide additional or different operations**.
> These usually **involve communication with a "real" object**.

A proxy can be helpful anytime you’d like to **separate extra operations into a different place** than the "real object," and especially when you want to easily change from not using the extra operations to using them, and vice versa.

Java’s dynamic proxy **creating the proxy object dynamically** and **handling calls to the proxied methods dynamically**.
> All calls made on a dynamic proxy are **redirected to a single invocation handler**, which has the job of **discovering what the call is** and **deciding what to do about it**. 

Proxy's **newProxyInstance(ClassLoader loader, Class<?>[] interfaces, InvocationHandler h) method** is used to **create a dynamic proxy**. It returns an instance of a proxy class for the specified interfaces that **dispatches method invocations** to the specified **invocation handler**.
> The InvocationHandler's **invoke(Object proxy, Method method, Object[] args) method** processes a method invocation on a proxy instance and returns the result as a Object.
> 
> When calling methods on the proxy **inside invoke()**, the calls through the interface are **redirected** through the proxy.

###Null Objects
---
When you use the built-in null to indicate the **absence of an object**, you must **test a reference for null-ness** every time you use it. 
> The problem is that **null has no behavior** of its own except for **producing a NullPointerException** if you try to do anything with it.

Sometimes it is useful to introduce the idea of a **Null Object** that will accept messages for the object that it’s "standing in" for, but will return values indicating that **no "real" object** is actually there.
> In general, the Null Object will be a **Singleton**.
> 
> You still **need a interface** to test for nullness.
> > This interface **allows instanceof to detect the Null Object**, and more importantly, does not require you to add an isNull() method to all your classes.

If you are **working with interfaces** instead of concrete classes, it’s possible to use a **DynamicProxy** to automatically **create the Null Objects**.

**Logical variations** of the Null Object are the **Mock Object** and the **Stub**. Like Null Object, both of these are **stand-ins for the "real" object** that will be used in the finished program.
> However, both Mock Object and Stub **pretend to be live objects** that **deliver real information**, rather than being a more intelligent placeholder for null, as Null Object is.

The **distinction between Mock Object and Stub** is one of degree.
> Mock Objects tend to be **lightweight and self-testing**, and usually many of them are created to handle **various testing situations**.
> 
> Stubs just **return stubbed data**, are typically **heavyweight** and are often **reused** between tests.
> 
> Stubs can be configured to **change depending on** how they are called. So a Stub is a **sophisticated object** that does lots of things.
> > Whereas you usually **create lots of** small, simple Mock Objects if you need to do many things.

###Interfaces and type information
---
An important goal of the **interface keyword** is to allow the programmer to **isolate components**, and thus **reduce coupling**.
> but **with type information** it’s possible to get around that — **interfaces** are **not** airtight **guarantees of decoupling**.

By **using package access** to implement the interface, it’s still possible to **reach in** and **call all of the methods** using reflection, even **private methods**.
> If you know the **name of the method**, you can **call setAccessible(true)** on the Method object to **make it callable**.

All you must do is run **javap** to decompiler C.class:
```
javap -private C
```
> The **-private** flag indicates that **all members should be displayed**, even private ones.
> 
> So anyone can **get the names and signatures** of your most private methods, and call them.

**Implementing the interface** as a **private inner class** or a **anonymous inner classes didn’t hide anything** from reflection either.

There doesn’t seem to be any way to **prevent reflection from reaching in and calling methods** that have **non-public access**. This is also true for fields, even **private fields**.
> However, **final fields** are actually **safe** from change. 
