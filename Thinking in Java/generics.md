##Generics
Ordinary classes and methods work **with specific types**:
> either primitives or class types.
> 
> If you are writing code that might be used **across more types**, this rigidity can be overconstraining.

One way that object-oriented languages **allow generalization** is through **polymorphism**. 
> You can write a method that **takes a base class object** as an argument, and then use that method with **any class derived from** that base class. 

If a method **argument is an interface** instead of a class, the limitations are **loosened** to include **anything that implements the interface** — including classes that haven’t been created yet.

Generics implement the concept of **parameterized types**, which allow **multiple types**.
> When you create an instance of a parameterized type, **casts** will be taken care of for you and the **type correctness** will be **ensured at compile time**.

###Simple generics
---
One of the most compelling initial motivations for generics is to **create container classes**.
> A container is a place to hold objects while you’re working with them.

There are some cases where you want a container to hold multiple types of objects, but typically you only **put one type** of object into a **container**. 
> One of the primary motivations for generics is to **specify what type of object** a container holds, and to have that specification backed up **by the compiler**.

You can put a **type parameter** inside angle brackets after the class name, and then substitute an actual type when you use the class. 
> That’s the core idea of Java generics: You tell it **what type you want to use**, and it takes care of the details.

One of the things you often want to do is **return multiple objects** from a method call.
> The return statement only allows you to **specify a single object**, so the answer is to create an object that **holds the multiple objects** that you want to return.

This concept is called a **tuple**, and it is simply **a group of objects** wrapped together into a **single object**.
> The recipient of the object is allowed **to read** the elements but **not put** new ones in.
> 
> This concept is also called a **Data Transfer Object** (or Messenger.)

Tuples can typically be **any length**, but each object in the tuple can be of a **different type**.
> However, we want to specify the type of each object and ensure that when the recipient reads the value, they get the right type.
> 
> A tuple **implicitly** keeps its elements **in order**.

###Generic interfaces & methods
---
**Generics** work with **interfaces**. You can also **parameterize methods** within a class.

A **generic method** allows the method to **vary independently of the class**.
> If a method is **static**, it has **no access to** the generic type parameters of the class.
> 
> To define a generic method, you simply **place a generic parameter list** before the return value.

With a **generic class**, you must **specify the type parameters** when you instantiate the class.
> But with a **generic method**, you **don’t** usually have to **specify the parameter types**, because the **compiler can figure** that out for you.
> > This is called **type argument inference**.

**Java 8 has no issue**:
> **Type inference** doesn’t work for anything **other than assignment**.
> > If you pass the result of a method call such as New.map() as an argument to another method, the compiler will **not** try to **perform type inference**.
> 
> > Instead it will **treat the method call as though the return value** is assigned to a variable of type Object.
> 
> It is possible to **explicitly specify the type** in a generic method.
> > You place the type **in angle brackets after the dot** and immediately preceding the method name.
> 
> > When calling a method from **within the same class**, you must **use this** before the dot, and when working with **static methods**, you must **use the class name** before the dot.

**Generic methods** and **variable argument lists** coexist nicely.

**Generics** can also be used with **inner classes** and **anonymous inner classes**. 

An important **benefit of generics** is the ability to simply and safely **create complex models**.

###The mystery of erasure
---
**Class.getTypeParameters()** returns an array of **TypeVariable objects** that represent the **type variables** declared by the **generic declaration**.
> You might be able to find out **what the parameter types** are, but all you find out is the **identifiers** that are used as the **parameter placeholders**, which is **not** such an interesting piece of **information**.

There’s **no information** about **generic parameter types** available inside generic code.
> You **can know** things like the **identifier** of the **type parameter** and the **bounds** of the **generic type**.
> 
> You **can’t know** the **actual type parameter(s)** used to create a particular instance.
> 
> This fact is the **most fundamental issue** that you must deal with when working with **Java generics**.

Java generics are **implemented using erasure**.
> This means that any **specific type information** is **erased** when you use a generic. 
> 
> Inside the generic, the **only thing that you know** is that you’re **using an object**.
> > So List<String> and List< Integer> are the **same type at run time**.
> 
> > Both forms are **"erased" to their raw type**, List.

**Erasure** is not a language feature. It is a **compromise** in the implementation of Java generics, necessary because generics were not made part of the language from the beginning. 

If generics had been part of Java 1.0, the feature would **not** have been implemented **using erasure**.
> It would have **used reification** to retain the type parameters **as first-class entities**, so you would have been able to perform type-based language and reflective operations on type parameters. 

In an **erasure-based implementation**, generic types are treated as **second-class types** that cannot be used in some important contexts.
> The generic types are **present only during static type checking**, after which every generic type in the program is **erased** by replacing it with a **non-generic upper bound**.
> > For example, type annotations such as List&lt;T> are **erased to List**, and ordinary type variables are **erased to Object** unless a bound is specified.

The **core motivation** for erasure is that it **allows generified clients** to be used with **non-generified libraries**, and vice versa.
> This is often called **migration compatibility**.

So Java generics not only must support **backwards compatibility**, but also must support **migration compatibility**.
> Erasure enables this migration towards generics by allowing non-generic code to **coexist with generic code**.

So the **primary justification** for erasure is the **transition process from nongenerified code to generified code**, and to incorporate generics into the language without breaking existing libraries.
> Erasure allows existing nongeneric client code to continue to be used without change, until clients are ready to rewrite code for generics. 

The **cost of erasure** is significant. Generic types **cannot be used** in operations that **explicitly refer to runtime types**, such as **casts**, **instanceof operations**, and **new expressions**.
> Because **all the type information** about the parameters is **lost**, whenever you’re writing generic code you must constantly be reminding yourself that it only appears that you have **type information about a parameter**.

Using **Array.newInstance()** is the **recommended** approach for **creating arrays in generics**.

Even though erasure **removes the information** about the actual type inside a method or class, the **compiler** can still **ensure internal consistency** in the way that **the type is used** within the method or class.

Because erasure **removes type information** in the body of a method, what matters at run time is the **boundaries**: the **points** where objects **enter and leave a method**.
> These are the points at which the **compiler performs type checks** at compile time, and **inserts casting code**.

All the action in generics **happens at the boundaries** — the **extra compile-time check** for incoming values, and the **inserted cast** for outgoing values.
> It helps to counter the confusion of erasure to remember that "**the boundaries are where the action takes place**."

###Compensating for erasure
---
Erasure **loses the ability** to **perform certain operations** in generic code. Anything that requires the **knowledge** of the **exact type** at **run time** won’t work.
> Occasionally you can program around these issues, but sometimes you must **compensate** for erasure by introducing a **type tag**.
> > This means you **explicitly pass** in the Class object **for your type** so that you can **use it in type expressions**.

The attempt to **use instanceof fails** because the **type information** has been **erased**.
> If you introduce a **type tag**, a **dynamic islnstance()** can be used instead.
> > The compiler ensures that the type tag **matches** the **generic argument**.

The attempt to **create a new T()** won’t work, partly because of **erasure**, and partly because the compiler **cannot verify** that **T has a default constructor**.
> The solution in Java is to **pass in a factory object**, and use that to **make the new instance**.
> > A convenient factory object is just the **Class object**, so if you use a **type tag**, you can use **newlnstance()** to **create a new object** of that type.
> 
> You also use an **explicit factory** and **constrain the type** so that it **only takes a class** that **implements this factory**.

You **can’t** create **arrays of generics**. The general solution is to **use an ArrayList** everywhere that you are tempted to create an array of generics.
> You get the **behavior of an array** but the **compile-time type safety** afforded by **generics**.

At times, you will still want to **create an array of generic types**. You can **define a reference** in a way that makes the compiler happy.

Since **all arrays** have the **same structure** (size of each array slot and array layout) **regardless** of the type they hold, it seems that you should be able to **create an array of Object** and **cast** that to the desired array type. This does in fact compile, but it won’t run; it produces a **ClassCastException**.
> The problem is that arrays **keep track of their actual type**, and that type is established **at the point of** creation of the array.
> 
> In ArrayOfGeneric.java, even though gia has been **cast** from a Generic[] to a Generic &lt;Integer>[], that **information** only **exists at compile time** (and without the @SuppressWarnings annotation, you’d get a warning for that cast).
> > At **run time**, it’s still **an array of Generic**, and that causes problems.

The only way to successfully **create an array of a generic type** is to create **a new array of the erased type**, and **cast that**.
> Because of erasure, the **runtime type** of T[] can only be **Object[]**.
> > If we immediately **cast it** to T[], then at **compile time** the **actual type of the array** is **lost**, and the compiler may **miss out on** some potential error checks.
> 
> > Because of this, it’s better to **use an Object[]** inside the **collection**, and **add a cast to T** when you use an array element.

You can also pass the **type token** Class&lt;T> into the constructor in order to **recover from the erasure**, and then call **Array.newInstance() method** to create an array.
```
(T[])Array.newInstance(componentType, length);
```
> The **runtime type** of the array is the exact **type T[]**.

There’s **no way to subvert** the type of the **underlying array**, which can **only be Object[]**.
> The advantage of **treating array internally as Object[]** instead of T[] is that it’s less likely that you’ll **forget the runtime type of the array** and accidentally introduce a bug.

###Bounds & Wildcards
---
Bounds allow you to **place constraints** on the **parameter types** that can be used with generics.
> Although this allows you to enforce rules about the types that your generics can be applied to, a potentially more important effect is that you can **call methods** that are **in your bound types**.

Because erasure removes type information, the **only methods** you can call for an unbounded generic parameter are those **available for Object**.
> However, if you are able to constrain that parameter to be a subset of types, then you can **call the methods in that subset**.

Arrays is covariance, however, **generics** is **not covariance**.
```
// covariance
Animal[] animal = new Pet[10];

// compile error: non-covariance
List<Animal> list = new ArrayList<Pet>();
```
> **Arrays** are completely defined in the language and can thus have both **compile-time** and **runtime checks** built in. 
> 
> With generics, the compiler and runtime system **cannot know** what you want to do with your types and what the **rules** should be.

Wildcards allow you to establish **some kind of relationship** between the two.
- **Covariance**. **Subtype wildcards List&lt;? extends T>** mean a list of any type that’s **inherited from** T.
> You don’t know what type the List is holding, so you **can't** safely **add** an object.
> > The compiler **cannot know** which **specific subtype of T** is required, so it won’t accept any type of T.
> 
> You can call a method that **returns T**, because anything in the List must **at least be of type T**.
- **Contravariance**. **Supertype wildcards List&lt;? super T>** mean a list that is any **base class of** a particular class T.
> It relaxes the constraints on what you can pass into a method, and you can safely **pass a typed object** into a generic type.

The **unbounded wildcard &lt;?>** means **anything**, but using an unbounded wildcard not equivalent to using a raw type.
> **List&lt;?>** would seem to be equivalent to **List&lt;Object>**, and **List** is effectively **List&lt;Object>** as well.
> 
> **List** actually means a **raw List** that holds **any Object type**, whereas **List&lt;?>** means a **non-raw List** of some **specific type**, but we just don’t know what that type is.

The **benefit** of **using exact types** instead of wildcard types is that you can **do more with the generic parameters**. But using **wildcards** allows you to **accept a broader range of parameterized types** as arguments.

**Capture conversion** means the **unspecified wildcard type** is captured and converted to an **exact type**. 
> If you **pass a raw type** to a method that uses &lt;?>, it’s possible for the compiler to **infer the actual type parameter**, so that the method can turn around and **call another method** that uses the exact type.
> 
> Capture conversion **only works** in situations where, within the method, you need to work with the **exact type**.

###Issues of using generics
---
You **cannot use primitives** as **type parameters** in Java generics, the solution is to **use the primitive wrapper classes** in conjunction with Java SE5 **autoboxing**.
> Autoboxing **does the conversion to and from Integer** automatically, it even allows the **foreach syntax** to produce ints.

A class **cannot implement two variants** of the **same generic interface**. Because of **erasure**, these are both the same interface.
```
interface Payable<T> {}

class Employee implements Payable<Employee> {}
class Hourly extends Employee implements Payable<Hourly> {}
```
> Hourly won’t compile because **erasure reduces** Payable<Employee> and Payable<Hourly> **to the same class**, Payable.
> 
> if you **remove the generic parameters** from both uses of Payable - as the compiler does during erasure - the code compiles.

Using a cast or instanceof with a generic type parameter doesn’t have any effect, however, there are times when **generics do not eliminate the need to cast**, and this generates a warning by the compiler which is inappropriate.

**Overloading the method** produces the **identical type signature** because of erasure. Instead, you must provide **distinct method names** when the erased arguments do not produce a unique argument list.

###Self-bounded types
---
You can’t inherit directly from a generic parameter. However, you can **inherit from a class** that uses that generic parameter in its **own definition**.
```
class GenericType<T> {}

public class CuriouslyRecurringGeneric
extends GenericType<CuriouslyRecurringGeneric> {}
```
> This could be called **curiously recurring generics** (CRG).
> 
> You're creating a new class that inherits from a generic type that **takes your class name as its parameter**.

Generics in Java can produce a base class that **uses the derived type** for its **arguments and return types**. It can also use the derived type for **field types**, even though those will be erased to Object.

The **essence of CRG** is the base class **substitutes the derived class** for its parameters.
> This means that the generic base class becomes **a kind of template for common functionality** for all its derived classes, but this functionality will **use the derived type** for all of its arguments and return values.
> 
> That is, the **exact type** instead of the base type will be used in the resulting class.

**Self-bounding** takes the extra step of **forcing the generic to be used** as its own bound argument. What self-bounding does is require the **use of the class** in an **inheritance relationship** like this:
```
class A extends SelfBounded<A> {}
```
> This forces you to **pass the class that you are defining** as a parameter to the base class.

The **self-bounding constraint** serves only to **force the inheritance relationship**. If you use self-bounding, you know that the **type parameter** used by the class will be the **same basic type** as the class that’s using that parameter.
> It forces anyone using that class to follow that form.

It’s also possible to **use self-bounding for generic methods**, this prevents the method from being applied to anything but a self-bounded argument.

The value of self-bounding types is that they **produce covariant argument types** — method argument types **vary to follow the subclasses**.

A self-bounded generic does in fact produce the **exact derived type** as a return value. In non-generic code, however, the argument types **cannot** be made to **vary with the subtypes**.

With self-bounding types, there is **only one method** in the derived class, and that method **takes the derived type** as its argument, not the base type.
> Without self-bounding, the ordinary inheritance mechanism steps in, and you **get overloading**.

###Dynamic type safety & Exceptions
---
Java SE5 has a set of utilities in java.util.Collections to **solve the type-checking problem** when you pass generic containers to pre-Java SE5 code.
> The static methods checkedCollection(), checkedList(), checkedMap(), checkedSet(), checkedSortedMap() and checkedSortedSet().
> 
> Each of these takes the **container** you want to dynamically check as the **first argument** and the **type** that you want to enforce as the **second argument**.

Because of **erasure**, the use of generics with **exceptions** is extremely limited.
> A catch clause **cannot catch an exception of a generic type**, because the exact type of the exception must be **known** at **both compile time and run time**.
> 
> Also, a generic class **can’t** directly or indirectly **inherit from Throwable**.

However, **type parameters** may be used in the throws clause of a **method declaration**.
> This allows you to write generic code that **varies with the type of a checked exception**.
