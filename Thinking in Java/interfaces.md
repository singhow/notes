##Interfaces
###Abstract classes and methods
---
Interfaces and abstract classes provide **more structured way** to separate interface from implementation.

Java provides a mechanism called the **abstract method**. This is a **incomplete method**; it has only a **declaration** and no method body.
> It’s possible to make a class abstract **without including** any abstract methods. It means that you want to **prevent** any instances of that class.
> 
> If you inherit from an abstract class and you want to make objects of the new type, you must **provide method definitions** for all the abstract methods in the base class.
> > If you don’t, then the derived class is also abstract
> 
> It **cannot** safely **create an object** of an abstract class, so you get an error message from the compiler.

###Interfaces
---
The **interface keyword** produces a **completely abstract class**, one that provides no implementation at all.

Any code that uses a particular interface knows what methods might be called for that interface. So the interface is used to **establish a "protocol"** between classes.

If you **leave off the public keyword** when creating an interface, you get **package access**.
> An interface can also contain **fields**, but these are **implicitly static and final**.
> 
> To make a class that conforms to a particular interface (or group of interfaces), use the **implements keyword**.

###Complete decoupling
---
Creating a method that **behaves differently depending on the argument object** that you pass it is called the **Strategy design pattern**.
> The method contains the **fixed part** of the algorithm to be performed, and the Strategy contains **the part that varies**.
> 
> The Strategy is **the object** that you pass in, and it contains code to be executed.

The first way you can **reuse code** is if client programmers can write their classes to **conform to the interface**. However, you are often in the situation of **not** being able to **modify the classes** that you want to use. In these cases, you can use the **Adapter design pattern**.
> In Adapter, you write code to take the interface that **you have** and produce the interface that **you need**.

**Decoupling interface** from implementation allows an interface to be **applied to multiple different implementations**, and thus your code is more reusable.

###"Multiple inheritance" in Java
---
Because an interface has **no implementation** at all — that is, there is **no storage associated** with an interface — there’s nothing to prevent many interfaces from being combined.

In a **derived class**, you aren’t forced to have a base class that is either abstract or "concrete" (one with no abstract methods).
> If you do inherit from a non-interface, you can **inherit from only one**. All the rest of the base elements **must be interfaces**.
> 
> You can **upcast to each interface**, because each interface is an **independent type**.

If you know something is going to be a **base class**, you can consider **making it an interface**.

You can easily **add new method declarations** to an interface by using inheritance, and you can also **combine several interfaces** into a new interface with inheritance.
> In both cases you get a **new interface**.

Using the **same method names** in different interfaces that are intended to be combined generally **causes confusion** in the readability of the code.
> Overloaded methods **cannot differ** only by **return type**.

One of the most compelling reasons for interfaces is to **allow multiple implementations** for the same interface.
> In simple cases this is in the form of a method that **accepts an interface**, leaving it up to you to implement that interface and pass your object to the method.

A **common use for interfaces** is the aforementioned **Strategy design pattern**. 
> You **write a method** that performs certain operations, and that method **takes an interface** that you also specify.
> 
> You’re basically saying, "You can use my method with any object you like, as long as your object **conforms to my interface**."

Because you can **add an interface** onto **any existing class** in this way, it means that a method that **takes an interface** provides a way for any class to be **adapted to work with** that method. This is the **power of using interfaces** instead of classes.

###Fields in interfaces
---
Because **any fields** you put into an interface are **automatically static and final**, the interface is a convenient tool for **creating groups of constant values**.
> The **fields** in an **interface** are **automatically public**, so that is not explicitly specified.
> 
> Since the fields are **static**, they are **initialized** when the class is **first loaded**, which happens when any of the fields are **accessed** for the first time.
> 
> The fields are **not part of the interface**. The values are **stored** in the **static storage area** for that interface.

Fields defined in interfaces **cannot be "blank finals,"** but they can be initialized with **nonconstant expressions**.

###Nesting interfaces
---
**Interfaces** may be **nested** within classes and within other interfaces.

Interfaces can also be **private**. Implementing a **private interface** is a way to **force the definition** of the methods in that interface **without adding any type information** (that is, **without** allowing any **upcasting**).

When you implement an interface, you are **not required** to implement **any interfaces nested** within.
> Also, **private** interfaces **cannot** be **implemented outside** of their defining classes.

###Interfaces and factories
---
An **interface** is intended to be **a gateway to multiple implementations**, and a typical way to **produce objects** that **fit the interface** is the **Factory Method** design pattern.
> Instead of calling a constructor directly, you **call a creation method** on a factory object which **produces an implementation of the interface**.
> > Your code is completely **isolated from the implementation of the interface**, thus making it possible to **transparently swap one implementation for another**.
