##Reusing Classes
There are two ways to **reuse classes**:
- The first is **composition**.
> You simply create objects of your existing class inside the new class.
> 
> The new class is composed of objects of existing classes. You’re simply **reusing the functionality** of the code, not its form.
- The second approach is **inheritance**.
> It creates a new class **as a type of** an existing class.

###Composition syntax
---
Every **non-primitive** object has a **toString() method**, and it’s called in special situations when the **compiler wants a String** but it has an object.

**Primitives** that are fields in a class are automatically initialized to **zero**. The **object references** are initialized to **null**.
> If you try to **call methods** for any of them, you’ll **get an exception** - a runtime error.
> 
> Conveniently, you can still **print a null reference** without throwing an exception.

If you want the **references initialized**, you can do it:
> **Initializing at point of definition**.
> > They’ll always be **initialized before the constructor** is called.
> 
> **Initializing in the constructor**.
>
> **Lazy initialization**.
> > Right before you **actually need to use** the object.
> 
> > It can **reduce overhead** in situations where object creation is expensive.
> 
> **Instance initialization**.

###Inheritance syntax
---
You’re **always doing inheritance** when you create a class:
> You **explicitly inherit** from some other class,
> 
> Or you **implicitly inherit** from Java’s standard root class Object.

When inheriting you’re **not restricted** to using the methods of the base class. You can also **add new methods to the derived class** exactly the way you put any method in a class.

When you create an **object of the derived class**, it contains within it a **subobject of the base class**.

It’s essential that the **base-class subobject** be **initialized correctly**, and there’s only one way to guarantee this:
> Perform the initialization in the constructor by **calling the baseclass constructor**, which has all the appropriate knowledge and privileges to perform the base-class initialization.
> 
> Java **automatically inserts calls** to the base-class constructor in the derived-class constructor.

The **base class** is **initialized before** the derived-class constructors can access it.
> Even if you don’t create a constructor for derived-class, the compiler will **synthesize a default constructor** for you that calls the base class constructor.

If you want to call a base-class constructor that **has an argument**, you must explicitly write the calls to the base-class constructor using the **super keyword** and the appropriate argument list.
> The **call to the base-class constructor** must be the **first thing** you do in the derived-class constructor.

###Delegation
---
**Delegation** is midway between inheritance and composition.
> Like composition, you **place a member object** in the class you’re building.
> 
> At the same time, like inheritance, you **expose all the methods** from the member object in your new class. 

The Java language doesn’t support delegation, but **development tools support**. 

###Combining composition and inheritance
---
There are times when your class might **perform some activities** during its lifetime that **require cleanup**.
> You can’t know when the garbage collector will be called, or if it will be called.
> > So if you want something **cleaned up for a class**, you must **explicitly write a special method** to do it, and make sure that the client programmer knows that they must call this method.
> 
> On top of this you must guard against an exception by **putting such cleanup in a finally clause**.

In your **cleanup method**, you must also **pay attention to the calling order** for the base-class and member-object cleanup methods in case one subobject depends on another.
> **First** perform all of the cleanup work **specific to your class**, in the **reverse order** of creation.
> 
> **Then** call the **base-class cleanup method**.

Overloading a base-class method name in a derived class does **not hide** the base-class versions.
> When you mean to **override a method**, you can choose to add **@Override annotation** and the compiler will **produce an error message** if you **accidentally overload** instead of overriding.

###Choosing composition vs. inheritance
---
Both **composition (explicitly)** and **inheritance (implicit)** allow you to **place subobjects** inside your new class.
> **Composition** is generally used when you want **the features of an existing class** inside your new class, but **not its interface**.
> > You embed an object so that you can **use it to implement features** in your new class, but the user of your new class **sees the interface** you’ve defined for the **new class** rather than the interface from the embedded object.
> 
> > For this effect, you embed **private objects of existing classes** inside your new class.
> 
> When you **inherit**, you take an existing class and **make a special version** of it. In general, this means that you’re taking a general-purpose class and specializing it **for a particular need**.

The **is-a** relationship is expressed with **inheritance**, and the **has-a** relationship is expressed with **composition**.

###Upcasting
---
The most important aspect of inheritance is the **relationship** expressed **between the new class and the base class**.
> The new class is **a type of** the existing class.

Casting **from a derived type to** a base type **moves up** on the inheritance diagram, so it’s commonly referred to as **upcasting**. 
> Upcasting is **always safe** because you’re going from a **more specific type** to a **more general type**.
> > The **derived class** is **a superset** of the base class.
> 
> You can also perform the **reverse of upcasting**, called **downcasting**.

One of the clearest ways to **determine** whether you should use **composition or inheritance** is to ask whether you’ll **ever need to upcast** from your new class to the base class.
 
###protected & final keyword
---
The **protected keyword** says:
> This is private as far as the class user is concerned, but **available** to anyone who **inherits from** this class or anyone else **in the same package**.

Java’s **final keyword** means this **cannot be changed**. You might want to **prevent changes** for two reasons:
> **design or efficiency**.
> > The final can be used for **data**, **methods**, and **classes**.

A **constant** is useful for two reasons:
> It can be a **compile-time constant** that won’t ever change.
> > The **calculation** can be performed at **compile time**, eliminating some run-time overhead.
> 
> It can be a value **initialized at run time** that you don’t want changed.

A **field** that is both **static and final** has **only one** piece of **storage** that **cannot be changed**.

With a **primitive**, final makes the **value a constant**, but with an **object reference**, final makes the **reference a constant**.
> It can **never be changed** to point **to another object**.
>  
> However, the **object itself** can be **modified**.
> > object's value can be changed.

Java **does not** provide a way to make any **arbitrary object** a **constant**, includes arrays.

Java allows the creation of **blank finals**, which are **fields** that are declared as final but are **not** given an **initialization value**.
> The blank final must be **initialized before** it is **used**, and the compiler ensures this.

Java allows you to **make arguments final**. This means that **inside the method** you **cannot change** what the argument reference **points to**.

There are two reasons for **final methods**.
> The first is to **put a “lock”** on the method to **prevent any inheriting class** from changing its meaning.
> > When you want to make sure that a **method’s behavior is retained** during inheritance and cannot be overridden.
> 
> The second reason for final methods is **efficiency**.
> > Made a method final allowed the compiler to **turn any calls** to that method **into inline calls**.
> 
> > With Java SE5/6, its **no longer necessary** to use final to try to help the optimizer.

Any **private methods** in a class are **implicitly final**.

A **final class** means that you **don’t** want to **inherit from** this class or allow anyone else to do so.
> The **fields** of a final class can be **final or not**.
> 
> However, **all methods** in a final class are **implicitly final**, since there’s no way to override them. 

###Initialization and class loading
---
The **compiled code** for each class exists in its own **separate file**. That file **isn’t loaded** until the code is **needed**.
> A class is **first loaded** when any one of its **static members** is **accessed**.
> 
> All the static objects and the static code block will be initialized **in textual order** at the point of loading.
