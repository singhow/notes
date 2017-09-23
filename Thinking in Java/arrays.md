##Arrays
###Why arrays are special
---
There are three issues that **distinguish arrays from** other types of containers:
- efficiency
- type
- the ability to hold primitives

The array is Java’s most efficient way to store and **randomly access** a sequence of object references.
> The array is a simple **linear sequence**, which makes element access fast.
> 
> The cost of this speed is that the **size** of an array object is **fixed** and cannot be changed for the lifetime of that array.

Arrays are superior to pre-generic containers because you create an array to **hold a specific type**.
> This means that you **get compile-time type checking** to prevent you from inserting the wrong type or mistaking the type that you’re extracting.

An array can **hold primitives**, whereas a pre-generic container could not. However, with the advent of **autoboxing**, containers are nearly as easy to use for primitives as arrays.
> The only remaining advantage to arrays is **efficiency**.

###Arrays are first-class objects
---
The **array identifier** is actually a **reference to a true object** that’s created on the **heap**.
> This is the object that holds the references to the other objects, and it can be created
> > either implicitly, as part of the **array initialization syntax**;
> 
> > or explicitly with a **new expression**.

Arrays of objects and arrays of primitives are almost identical in their use. The only difference is that arrays of objects **hold references**, but arrays of primitives **hold the primitive values directly**.

###Returning an array
---
You can only **return a pointer** to an array in C/C++. This introduces problems because it becomes messy to control the lifetime of the array, which leads to **memory leaks**.

In Java, you just **return the array**. You never worry about responsibility for that array.
> It will be around as long as you need it, and the garbage collector will clean it up when you’re done.

###Multidimensional arrays
---
You can easily create **multidimensional arrays** as following:
```
int[][] a = {
	{ 1, 2, 3, },
	{ 4, 5, 6, },
};
```

You can also allocate an array **using new**.
```
int[][][] a = new int[2][2][4];
```
> **Primitive** array values are automatically initialized to **0**.
> 
> Arrays of **objects** are initialized to **null**.

###Arrays and generics
---
Arrays and generics do not mix well. You **cannot** instantiate arrays of **parameterized types**:
```
Peel<Banana>[] peels = new Peel<Banana> [10]; // Illegal
```
> **Erasure** removes the parameter type information, and arrays must **know the exact type** that they hold, in order to enforce type safety.

However, you can **parameterize the type of the array** itself.
```
class ClassParameter<T> {
	public T[] f(T[] arg) { return arg; }
}

class MethodParameter {
	public static <T> T[] f(T[] arg) { return arg; }
}
```
> The convenience of using a parameterized method instead of a parameterized class is:
> > You don’t have to instantiate a class with a parameter for each different type you need to apply it to, and you can make it static.

The compiler **won’t** let you **instantiate an array of a generic type**. However, it will let you **create** a **reference to** such an array.
```
List<String>[] ls;
```
> Although you cannot create an actual array object that holds generics, you can create an **array of the non-generified** type and **cast it**.

###Creating test data
---
Arrays class has a **fill() method**:
> It only **duplicates a single value** into each location, or in the case of objects, **copies the same reference** into each location.

You can also **use the Generator concept** to create more interesting arrays of data in a flexible fashion.

In order to **take a Generator** and **produce an array**, we need two conversion tools.
> The first one uses any Generator to **produce an array of Object subtypes**.
> 
> To cope with the problem of primitives, the second tool takes **any array of primitive wrapper types** and produces the **associated array of primitives**.

###Arrays utilities
---
Arrays class holds a set of **static utility methods** for arrays. There are six basic methods:
- **equals()**
> compare two arrays for equality.
> 
> **deepEquals()** is for multidimensional arrays.
- **fill()**
> duplicates a single value into each location.
- **sort()**
> sort an array.
- **binarySearch()**
> find an element in a sorted array.
- **toString()**
> produce a String representation for an array.
- **hashCode()**
> produce the hash value of an array.

The Java standard library provides a **static System.arraycopy() method**, which can **copy arrays** far more quickly than if you use a for loop to perform the copy by hand.
> System.arraycopy() is **overloaded** to **handle all types**.

**equals() method** is **overloaded** for all the primitives and for Object.
> To be equal, the arrays must have the **same number of elements**, and **each element** must be equivalent to each corresponding element in the other array.

**Sorting** must perform comparisons based on the actual type of the object. A primary goal of programming design is to "separate things that change from things that stay the same."
> The code that **stays the same** is the **general sort algorithm**.
> 
> The thing that **changes** from one use to the next is **the way objects are compared**.

Instead of placing the comparison code into many different sort routines, the **Strategy design pattern** is used.
> With a Strategy, the part of the **code that varies** is encapsulated inside a separate class (the Strategy object).
> 
> You **hand a Strategy object** to the code that’s always the same, which uses the Strategy **to fulfill its algorithm**.

Java has two ways to provide **comparison functionality**.
> The first is with the **"natural" comparison** method that is imparted to a class by implementing the **java.lang.Comparable interface**.
> > This is a very simple interface with a single method, **compareTo()**.
> 
> > This method **takes another object of the same type** as an argument and produces a **negative value** if the current object is less than the argument, **zero** if the argument is equal, and a **positive value** if the current object is greater than the argument.
> 
> You can also **create a separate class** that **implements Comparator interface**.
> > It has two methods, **compare()** and **equals()**.
> 
> > This is an example of the **Strategy design pattern**.
> 
> > The Collections class contains a method **reverseOrder()** that **produces a Comparator** to **reverse the natural sorting order**.

With the built-in sorting methods, you can sort any array of primitives, or any array of objects that either implements Comparable or has an associated Comparator.
> The **sorting algorithm** that’s used in the Java standard library:
> > A **quicksort for primitives**.
> 
> > A **stable merge sort for objects**.

Once an array is sorted, you can perform a **fast search** for a particular item by **using Arrays.binarySearch()**.
> However, if you try to use binarySearch() on an **unsorted array** the results will be **unpredictable**.
> 
> If an array contains **duplicate elements**, there is **no guarantee** which of those duplicates will be found.
> > The search algorithm is not designed to support duplicate elements, but rather to tolerate them.
> 
> > If you need a **sorted list of non-duplicated elements**, use a **TreeSet** (to maintain sorted order) or **LinkedHashSet** (to maintain insertion order).
