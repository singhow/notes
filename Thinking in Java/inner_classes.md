##Inner Classes
It’s possible to place a class definition within another class definition. This is called an **inner class**.
> The inner class is a valuable feature because it allows you to **group classes** that logically belong together and to control the visibility of one within the other.

###The link to the outer class
---
When you create an inner class, an **object** of that inner class has **a link to the enclosing object** that made it, and so it can **access the members** of that enclosing object — without any special qualifications.
> Inner classes have access rights **to all the elements** in the enclosing class.
> > The inner class secretly **captures a reference** to the particular object of the enclosing class that was responsible for creating it.
> 
> > Then, when you **refer to a member** of the enclosing class, that **reference** is used to **select that member**.

An **object of an inner class** can be created **only in association** with an object of the **enclosing class**.
> **Construction** of the inner-class object **requires the reference** to the object of the **enclosing class**, and the compiler will complain if it cannot access that reference.

###Using .this and .new
---
If you need to produce the **reference to the outer-class object**, you **name the outer class** followed **by a dot and this**.
> The **resulting reference** is automatically the correct type, which is known and **checked at compile time**, so there is no runtime overhead.

Sometimes you want to tell some other object to **create an object** of one of its **inner classes**. To do this you must **provide a reference** to the other **outer-class** object in the **new expression**.
```
Outer outer = new Outer();
Outer.Inner inner = Outer.new Inner();
```
> Use an object of the **outer class** to make an object of the **inner class**.
> 
> This also **resolves the name scoping issues** for the inner class.

It’s not possible to create an object of the inner class **unless** you already **have an object of the outer class**.
> Because the object of the inner class is **quietly connected** to the object of the **outer class** that it was made from.

###Inner classes and upcasting
---
Inner classes really come into their own when you start **upcasting** to a **base class**, and in particular to **an interface**.
> The effect of **producing an interface reference** from an object that implements it is essentially the **same as upcasting to a base class**. 

Because the inner class — the implementation of the interface — can be unseen and unavailable, which is convenient for **hiding the implementation**.
> **All you get** back is **a reference to** the base class or the interface.

The **private inner class** provides a way for the class designer to completely **prevent any type-coding dependencies** and to **completely hide details** about implementation.
> This means that the **client programmer** has **restricted knowledge and access** to these members.
> 
> In fact, you **can’t even downcast** to a **private inner class** (or a protected inner class unless you’re an inheritor), because you **can’t access the name**.
> 
> In addition, extension of an interface is useless from the client programmer’s perspective since the client programmer **cannot access any additional methods** that aren’t part of the public interface.

###Inner classes in methods and scopes
---
Inner classes can be **created within a method** or even **an arbitrary scope**. There are two reasons for doing this:
> You’re **implementing an interface** of some kind so that you can **create and return a reference**.
> 
> You’re solving a complicated problem and you want to create a class to aid in your
solution, but you **don’t want it publicly available**.

An inner classes can be created:
> within a method.
> 
> within a scope inside a method.
> 
> an anonymous class implementing an interface.
> 
> an anonymous class extending a class that has a **non-default constructor**.
> 
> an anonymous class that **performs field initialization**.
> 
> an anonymous class that **performs construction** using instance initialization

The **semicolon** at the end of the **anonymous inner class** doesn’t mark the end of the class body.
> Instead, it **marks the end of the expression** that happens to contain the anonymous class.

If you’re defining an anonymous inner class and want to **use an object that’s defined outside** the anonymous inner class, the compiler requires that the **argument reference** be **final**.

You can’t have a named constructor in an anonymous class, but with **instance initialization**, you can **create a constructor** for an anonymous inner class.
> An **instance initializer** is the constructor for an anonymous inner class. You **can’t overload** instance initializers, so you can have **only one** of these constructors.

**Anonymous inner classes** are somewhat **limited** compared to regular inheritance, because they can **either extend** a class **or implement** an interface, but not both.
> If you do implement an interface, you can **only implement one**.

###Nested classes
---
If you **don’t need** a **connection** between the **inner-class object** and the **outerclass object**, then you can **make the inner class static**. This is commonly called a **nested class**.
> The object of an ordinary inner class **implicitly keeps a reference** to the object of the **enclosing class** that created it.
> 
> However, this is not true when an inner class is static.

A nested class means:
> You **don’t need an outer-class object** in order to **create** an object of a **nested class**.
> 
> You **can’t access** a **non-static outer-class** object from an object of a **nested class**.

Fields and methods in **ordinary inner classes** can only be at the **outer level of a class**, so ordinary inner classes **cannot have** static data, static fields, or nested classes.
> A nested class does **not have a special this reference**, which makes it analogous to a **static method**.

Normally, you **can’t put any code** inside an interface, but a **nested class** can **be part of an interface**.
> Any class you put inside an interface is **automatically public and static**.
> 
> Since the class is static, it doesn’t violate the rules for interfaces.
> > the nested class is only **placed inside the namespace of the interface**.
> 
> You can even **implement the surrounding interface** in the inner class

It **doesn’t matter how deeply** an inner class may be nested.
> it can **transparently access** all of the members of all the classes it is nested within.

###Why inner classes
---
The **inner class** inherits from a class or implements an interface, and the code in the inner class **manipulates the outer-class object** that it was created within.
> You could say that an inner class **provides a kind of window** into the outer class.

The **most compelling reason** for inner classes is:
> Each inner class can **independently inherit** from an implementation.
> 
> Thus, the inner class is **not limited** by whether the outer class is **already inheriting** from an implementation.
> 
> So one way to look at the inner class is as **the rest of the solution of the multiple inheritance problem**.
> > Interfaces solve part of the problem, but inner classes **effectively allow "multiple implementation inheritance."**
> 
> > That is, inner classes effectively **allow you to inherit from more than one non-interface**.

If you have **abstract or concrete classes** instead of interfaces, you are suddenly **limited to using inner classes** if your class must somehow **implement both of the others**.

With inner classes you have these additional features:
> The inner class can have **multiple instances**, each with its **own state information** that is **independent** of the information in the outer-class object.
> 
> In a single outer class you can have **several inner classes**, each of which implements the same interface or inherits from the same class **in a different way**.
> 
> The point of **creation of the inner-class** object is **not tied** to the **creation of the outerclass** object.
> 
> There is **no** potentially confusing **"is-a" relationship** with the inner class; it’s a **separate entity**.

###Closures & callbacks
---
A **closure** is a **callable object** that **retains information** from the scope in which it was created.
> An inner class is an **object-oriented closure**, because it **contains each piece of information** from the outer-class object ("the scope in which it was created").
> 
> It also automatically **holds a reference** back to the whole outer-class object, where it **has permission to manipulate all** the members, even private ones.

One of the most compelling arguments made to include some kind of pointer mechanism in Java was to **allow callbacks**.
> With a callback, some other object is **given a piece of information** that allows it to **call back into the originating object** at some later point. 

The value of the callback is in its **flexibility**; you can **dynamically decide** what methods will be called at **run time**.

An **application framework** is a class or a set of classes that’s designed to **solve a particular type** of problem.
> To apply an application framework, you typically **inherit** from one or more classes and **override** some of the methods.
> 
> The code that you write in the overridden methods **customizes the general solution** provided by that application framework in order to **solve your specific problem**.
> > This is an example of the **Template Method** design pattern.

The **Template Method** contains the basic structure of the algorithm, and it **calls one or more overrideable methods** to complete the action of that algorithm.
> A design pattern **separates things** that change from things that stay the same, and in this case the **Template Method** is the part that **stays the same**, and the **overrideable methods** are the things that **change**.

###Inheriting from inner classes
---
Because the inner-class constructor **must attach to a reference** of the enclosing class object, things are slightly complicated when you inherit from an inner class. 
> The problem is that the **"secret" reference** to the enclosing class object **must be initialized**, and yet in the derived class there’s **no longer a default object** to attach to.
> 
> You must use a special syntax to **make the association explicit**.

You must use the following syntax **inside the derived-class's constructor**.
```
enclosingClassReference.super();
```
> This provides the necessary reference, and the program will then compile.

An inner class **cannot be overriden** like a method.

Inner classes can also be created **inside code blocks**, typically inside the body of a method.
> A **local inner class** can **not have an access specifier** because it **isn’t part of the outer class**, but it does have **access to the final variables** in the current code block and **all the members** of the enclosing class.
> 
> **Local inner class** can have a constructor, however, **anonymous inner class** cannot have a named constructor, only an **instance initializer**.

Since every class **produces a .class file** that holds **all the information** about how to create objects of this type.
> This information produces a "meta-class" called the **Class object**.

**Inner classes** also produce .class files to **contain the information** for their Class objects.
> The names of these files/classes have a **strict formula**:
> > the name of the enclosing class, **followed by a ‘$’**, followed by the name of the inner class.
> 
> If inner classes are **anonymous**, the compiler simply starts **generating numbers** as inner-class identifiers.
> 
> If inner classes are **nested within inner classes**, their names are simply appended after a ‘$’ and the outer-class identifier(s).
