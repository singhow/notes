##Enumerated Types
The **enum keyword** allows you to create a **new type** with a **restricted set of named values**, and to treat those values as regular program components.

###Basic enum features
---
When you create an enum, an associated class that automatically **inherited from java.lang.Enum** is produced for you by the compiler. 
- **values()**
> **static method**, inserted by compiler.
> 
> produces **an array of the enum constants** in the order in which they were declared.
- **ordinal()**
> produces an int **indicating the declaration order** of each enum instance.
- **equals()**
- **compareTo()**
- **hashCode()**
- **getDeclaringClass()**
> find out the enclosing enum class
- **name()**
> produces **the name** exactly as it is **declared**, this is what you get with toString()
- **valueOf()**
> static member of **Enum class**, produces the **enum instance** that corresponds to the **String name** you pass to it.

```
import static enumerated.Spiciness.*;
```
> The **static import** brings all the **enum instance identifiers** into the **local namespace**, so they **don’t** need to be **qualified**.

###Adding methods to an enum
---
Except for the fact that you **can’t inherit** from it, an enum can be treated much like a regular class.
> This means that you can **add methods to an enum**. It’s even possible for an enum to have a main().
>
> If you are going to define methods you must **end** the sequence of enum instances **with a semicolon**.
> > Java forces you to **define the instances** as the **first** thing in the enum.

You may want to **produce different descriptions** for an enumeration than the default toString(), you can **provide a constructor** to **capture extra information**, and additional methods to provide an extended description.
> The **constructor** can only be used to **create the enum instances** that you **declare inside the enum definition**
> > The compiler **won’t** let you use it to create any new instances **once the enum definition is complete**.
> 
> You can also **overriding the toString() method** for an enum to producing different string values for enumerations.

###enums in switch statements
---
One very convenient capability of **enums** is the way that they can be used in **switch statements**.
> A **switch** only works with an **integral value**.
> > enums have an established **integral order** and the **order of an instance** can be produced with the **ordinal() method**.

The **compiler** does **not complain** that there is **no default statement** inside the switch.
> This means you will have to pay attention and ensure that you **cover all the cases** on your own.
> 
> On the other hand, if you are **calling return** from case statements, the **compiler** will **complain** if you don’t have a default.

###The mystery of values()
---
values() is a static method **inserted** into the enum definition **by the compiler**, if you **upcast an enum type** to Enum, the **values() method** will **not be available**.
> However, there is a **getEnumConstants() method** in Class, so even if values() is not part of the interface of Enum, you can still **get the enum instances** via the **Class object**.

We’ve established that all enums **extend java.lang.Enum**. Since Java does **not support multiple inheritance**, this means that you **cannot create an enum via inheritance**:
```
enum NotPossible extends Pet { ... // Won’t work }
```
> However, it is possible to **create an enum** that **implements one or more interfaces**.

###Using interfaces for organization
---
The motivation for **inheriting from an enum** comes partly from wanting to **extend the number of elements** in the original enum, and partly from wanting to **create subcategories by using subtypes**.
> You can **achieve categorization** by **grouping the elements together** inside an interface and **creating an enumeration** based on that interface.

###Using EnumSet & EnumMap
---
An **enum** requires that all its **members be unique**, so it would seem to **have set behavior**, but since you **can’t add or remove elements** it’s not very useful as a set.
> The **EnumSet** works in concert with enums to create a **replacement for** traditional **int-based "bit flags."**
> > Such flags are used to **indicate** some kind of **on-off information**.

The generally used methods of **EnumSet**:
- **EnumSet.of()**
> creates an enum set initially containing the specified elements.
- **EnumSet.allOf()**
> creates an enum set containing all of the elements in the specified element type.
- **EnumSet.range()**
> creates an enum set initially containing all of the elements in the range defined by the two specified endpoints.
- **EnumSet.complementOf()**
> creates an enum set with the same element type as the specified enum set, initially containing all the elements of this type that are not contained in the specified set.

Internally, **EnumSet** is represented by (if possible) **a single long** that is treated as a **bit-vector**, so it’s extremely fast and efficient.
> The benefit is that you now have a **much more expressive way** to **indicate the presence or absence** of a binary feature, without having to worry about performance.
> 
> The elements of an EnumSet must **come from a single enum**.

EnumSets are built on top of **longs**, a long is **64 bits**, and each enum instance **requires one bit** to indicate presence or absence.
> This means you can have an EnumSet for an enum of **up to 64 elements** without going beyond the use of a single long.

An **EnumMap** is a specialized Map that requires that its **keys** be from a **single enum**.
> An EnumMap can be implemented internally **as an array**, thus they are extremely fast.
> 
> There is **always a key entry** for each of the enums, but the **value is null** unless you have called put() for that key.

One advantage of **EnumMap** over constant-specific methods is that an EnumMap **allows you to change the value objects**, whereas the **constant-specific methods** are **fixed at compile time**.

###Constant-specific methods
---
Java enums allows you to **give each enum instance different behavior** by **creating methods for each one**. 
> You define **one or more abstract methods** as part of the enum, then **define the methods for each enum instance**.
> 
> You can look up and call methods **via their associated enum instance**. This is often called **table-driven code**.
> 
> Because each instance of an enum can have its own behavior via constant-specific methods, this suggests that **each instance is a distinct type**.

**enum instances of inner enums** do not behave like ordinary inner classes, you **cannot access non-static fields or methods** in the outer class.

In the **Chain of Responsibility** design pattern, you create **a number of different ways** to solve a problem and chain them together.
> When a request occurs, it is **passed along the chain** until one of the solutions can handle the request.

**Enumerated types** can be ideal for **creating state machines**. A **state machine** can be in a **finite number of specific states**.
> The machine normally **moves from one state to the next** based on an **input**, but there are also **transient states**.
> 
> The machine **moves out of these** as soon as their **task is performed**.

There are certain **allowable inputs for each state**, and **different inputs** change the state of the machine to **different new states**.
> Because enums **restrict the set** of possible cases, they are quite useful for **enumerating the different states and inputs**.
> 
> Each state also typically has some kind of **associated output**.

###Multiple dispatching
---
Java **only** performs **single dispatching**.
> If you are performing an operation on **more than one object** whose **type is unknown**, Java can invoke the **dynamic binding** mechanism **on only one** of those types.

Polymorphism can **only occur via method calls**, so if you want **double dispatching**, there must be **two method calls**:
- the first to determine the first unknown type.
- the second to determine the second unknown type.

With **multiple dispatching**, you must have a **virtual call for each of the types** — if you are working with two different type hierarchies that are interacting, you’ll need a virtual call in each hierarchy.
> You’ll **set up a configuration** such that a single method call **produces more than one virtual method call** and thus **services more than one type** in the process.
> > You need to **work with more than one method**: You’ll need a method call **for each dispatch**.

Because **constant-specific methods** allow you to **provide different method implementations** for each enum instance, they might seem like a perfect solution for **setting up multiple dispatching**.
> Even though they can be given different behavior in this way, enum instances are **not types**, so you **cannot** use them as argument types in **method signatures**.

It’s possible to **perform a "true" double dispatch** using the **EnumMap** class, which is specifically designed to work very efficiently with enums.
> Since the goal is to **switch on two unknown types**, an **EnumMap of EnumMaps** can be used to **produce the double dispatch**.
