##Polymorphism
**Polymorphism** is the third essential feature of an OOP language, after **data abstraction** and **inheritance**.

The **polymorphic method call** allows one type to express its distinction from another as long as they’re both **derived from the same base type**.
> This distinction is expressed through **differences in behavior of the methods** that you can call through the base class.

###The twist
---
**Connecting** a method call to a method body is called **binding**.
> When binding is performed before the program is run (by the **compiler and linker**, if there is one), it’s called **early binding**.
> 
> **Late binding** (dynamic binding or runtime binding) means that the binding **occurs at run time**, based on the **type of object**.

When a language **implements late binding**, there must be some mechanism to **determine the type** of the object **at run time** and to **call the appropriate method**.
> The compiler **still doesn’t know** the object type, but the **method-call mechanism** finds out and calls the correct method body.
> > You can imagine that some sort of **type information** must be **installed in the objects**.

All method binding in Java **uses late binding unless** the method is **static or final**.
> You don’t need to make any decisions about whether late binding will occur — it **happens automatically**.

You can write your code to **talk to the base class** and know that all the **derived-class** cases will **work correctly** using the same code.
> You **send a message** to an object and **let the object figure out** the right thing to do.

In a well-designed OOP program, most or all of your methods will follow the **model of the method** and **communicate only** with the **base-class interface**.
> Such a program is **extensible** because you can **add new functionality** by **inheriting new data types** from the common base class.
> 
> The **methods** that manipulate the base-class interface will **not need to be changed** at all to accommodate the new classes.

Polymorphism is an important technique for the programmer to “**separate the things** that change **from** the things that stay the same.”

Only **ordinary method calls** can be **polymorphic**.
> if you **access a field directly**, that access will be **resolved at compile time**, and thus they are not polymorphic.
> 
> if a **method is static**, it **doesn’t** behave **polymorphically** either.
> > static methods are **associated with the class**, and not the individual objects.

###Constructors and polymorphism
---
A **constructor** for the **base class** is **always called** during the construction process for a derived class.
> This call **automatically move up** the inheritance hierarchy so that a constructor for every base class is called.
> 
> It’s essential that **all constructors get called**; otherwise the entire object wouldn’t be constructed.
> > That’s why the compiler **enforces a constructor call** for every portion of a derived class. 

The **order of constructor calls** for a complex object is as follows:
> The **storage** allocated for the object is **initialized to binary zero** before anything else happens.
> 
> The **base-class constructor** is called. This step is **repeated recursively** until the most-derived class is reached.
> > The **root of the hierarchy** is constructed **first**, followed by the next-derived class, etc.
> 
> **Member initializers** are called in the order of declaration.
> 
> The body of the **derived-class constructor** is called.

When you inherit, you **know all about the base class** and can **access any public and protected members** of the base class.
> This means that you must be able to assume that **all the members of the base class are valid** when you’re in the derived class.

**Inside the constructor**, you must be able to assume that **all members that you use have been built**.
> The only way to guarantee this is for the **base-class constructor** to be **called first**.
> 
> Then when you’re **in the derived-class constructor**, all the members you can **access in the base class** have been initialized. 

The **order of disposal** should be the **reverse of** the order of **initialization**, in case one subobject is dependent on another.
> For **fields**, this means the reverse of the order of declaration.
> 
> For **base classes**, you should **perform the derived-class cleanup first**, then the base-class cleanup.
> > That’s because the derived-class cleanup could **call some methods in the base class** that require the base-class components to be alive, so you must not destroy them prematurely.

A object “owns” its member objects. It creates them, and it **knows how long they should live**, so it knows when to dispose() the member objects.
> However, if one of these member objects is **shared with one or more other objects**, the problem becomes more complex and you cannot simply assume that you can call dispose().
> 
> In this case, **reference counting** may be necessary to keep track of the number of objects that are still accessing a shared object.
> 
> When you **attach a shared object** to your class, you must remember to **call addRef()**, but the **dispose() method** will **keep track of the reference count** and decide when to actually perform the cleanup.

If you **call a dynamically-bound method** inside a **constructor**, the overridden definition for that method is used.
> A dynamically bound method call **reaches “outward”** into the inheritance hierarchy. It calls a method **in a derived class**.
> 
> If you do this inside a constructor, you call a method that might **manipulate members** that **haven’t been initialized** yet.

The **only safe methods** to call **inside a constructor** are those that are **final** in the base class.

**Covariant return types** means that an **overridden method** in a derived class can **return a type derived from** the type returned by the base-class method.
> It allows the **more specific return type**.

###Designing with inheritance
---
**Composition** does not force a design into an inheritance hierarchy. But composition is also more flexible since it’s possible to **dynamically choose a type** (and thus behavior) when using composition, whereas inheritance **requires an exact type to be known** at compile time.

A general guideline is “Use **inheritance** to express **differences in behavior**, and **fields** to express **variations in state**.”

The cleanest way to create an **inheritance hierarchy** is to take the **“pure” approach**. 
> That is, **only methods** that have been established **in the base class** are **overridden** in the derived class.
> 
> This can be called a **pure “is-a” relationship** because the interface of a class establishes what it is.

**Derived classes** will also have **no more than the base-class interface**.
> This can be thought of as **pure substitution**, because derived class objects can be perfectly substituted for the base class.

The **base class** can **receive any message** you can **send to the derived class** because the two have **exactly the same interface**.
> All you need to do is **upcast from the derived class** and never look back to see what exact type of object you’re dealing with.

If you want to **access the extended interface** of a derived-class, you can try to **downcast**.
> If it’s the correct type, it will be successful.
> 
> Otherwise, you’ll get a **ClassCastException**.

With a **downcast**, you don’t really know that **a base-class is actually a specified derived-class**.
> To solve this problem, there must be some way to **guarantee** that a **downcast is correct**.
> 
> **RTTI** solved this problem.

In Java, **every cast is checked**. So even though it looks like you’re just performing an ordinary **parenthesized cast**, at **run time** this cast is **checked to ensure** that it is in fact the type you think it is.
> If it isn’t, you get a **ClassCastException**.
> 
> This act of **checking types** at run time is called **RTTI**. 
