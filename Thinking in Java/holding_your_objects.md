##Holding Your Objects
###Basic concepts
---
One of the problems of using pre-Java SE5 containers was that the compiler allowed you to **insert an incorrect type** into a container.

The Java container library takes the idea of "holding your objects" and divides it into **two distinct concepts**:
- **Collection**
> a **sequence of individual elements** with one or more rules applied to them.
> > A **List** must hold the elements in the way that they were inserted.
> 
> > A **Set** cannot have duplicate elements.
> 
> > A **Queue** produces the elements in the order determined by a queuing discipline. 
- **Map**
> A **group of key-value object pairs**, allowing you to look up a value using a key.

###Adding groups of elements
---
There are utility methods in both the Arrays and Collections classes that **add groups of elements** to a Collection.
- **Arrays.asList()**
> takes either an **array** or a **comma separated list of elements** (using varargs) and turns it into a **List object**.
- **Collections.addAll()**
> takes a **Collection object** and either an array or a comma-separated list and adds the elements to the Collection.

You must use **Arrays.toString()** to produce a **printable representation of an array**, but the containers print nicely without any help.

###List
---
Lists promise to maintain elements in a particular sequence. There are two types of List:
- **ArrayList**
> randomly accessing elements, but is **slower** when **inserting and removing elements** in the **middle of a List**.
- **LinkedList**
> provides optimal sequential access, with **inexpensive insertions and deletions** from the middle of the List.
> 
> relatively slow for random access.

Unlike an array, a List allows you to add elements after it has been created, or remove elements, and it **resizes itself**.

Some **useful methods** of the List:
- **contains()**
> find out whether an object is in the list.
- **remove()**
> remove an object.
- **indexOf()**
> discover the index number where that object is located in.
- **equals()**
- **subList()**
> create a slice out of a larger list.
- **containsAll()**
> whether the list contains all of the elements of the specified collection.
- **retainAll()**
> retains only the elements in the list that are contained in the specified collection.
- **removeAll()**
> removes all the objects from the List.
- **set()**
> replaces the element at the index (the first argument) with the second argument.
- **toArray()**
> create a new array of the appropriate size.

###Iterator
---
An **iterator** is an object whose job is to **move through a sequence** and select each object in that sequence without the client programmer knowing or caring about the underlying structure of that sequence.
> An iterator is usually what’s called a **lightweight object**: one that’s cheap to create. 

With an Iterator you can:
- Ask a Collection to hand you an **Iterator** using a method called **iterator()**.
> That Iterator will be ready to return the first element in the sequence.
- Get the **next object** in the sequence with **next()**.
- See if there are **any more objects** in the sequence with **hasNext()**.
- **Remove the last element** returned by the iterator with **remove()**.

The true power of the Iterator is the ability to **separate the operation of traversing a sequence** from the underlying structure of that sequence. 

The **ListIterator** is a more powerful subtype of Iterator that is produced **only by List** classes.
> While Iterator can only move forward, ListIterator is **bidirectional**.
> 
> It can **produce the indexes** of the **next and previous elements** relative to where the iterator is pointing in the list.
> > It can **replace the last element** that it visited using the **set()** method.
> 
> You can produce a **ListIterator** that points to the beginning of the List by calling **listIterator()**.
> > You can also create a ListIterator that starts out **pointing to an index** n in the list by calling **listIterator(n)**.

###LinkedList & Stack
---
LinkedList adds methods that allow it to be **used as** a **stack**, a **Queue** or a **deque**.
> It is less efficient for random-access operations.

Some **useful methods** of the LinkedList:
- **getFirst()**
- element()
> identical with getFirst(), return the head (first element) of the list without removing it.
- **removeFirst()**
- **remove()**
> identical with removeFirst(), remove and return the head of the list.
- **poll()**
> retrieves and removes the head (first element) of this list.
- **addFirst()**
> inserts an element at the beginning of the list.
- **add()**
- **addLast()**
- **offer()**
> same as add() and addLast(), add an element to the tail (end) of a List.
- **removeLast()**
> removes and returns the last element of the list.

A **stack** is sometimes referred to as a **LIFO container**. 

###Set & Map
---
A Set **refuses to hold more than one instance** of each object value.
> The Set is **exactly a Collection**, a Set **determines membership** based on **the "value" of an object**.

You can test a Map to see if it **contains a key or a value** with **containsKey() and containsValue()**.

A Map can return a **Set of its keys**, a **Collection of its values**, or a **Set of its pairs**.

###Queue & PriorityQueue
---
A **queue** is typically a **FIFO container**. Queues are commonly used as a way to **reliably transfer objects** from one area of a program to another.

LinkedList has methods to support queue behavior and it **implements the Queue interface**, so a LinkedList can be **used as a Queue implementation** by **upcasting a LinkedList to a Queue**.

Some **useful methods** of Queue:
- **offer()**
> Queue-specific methods. inserts an element at the tail of the queue if it can, or returns false.
- **peek() & element()**
> both return the head of the queue without removing it.
> > peek() returns null if the queue is empty.
> 
> > element() throws NoSuchElementException. 
- **poll() & remove()**
> both remove and return the head of the queue.
> > poll() returns null if the queue is empty.
> 
> > remove() throws NoSuchElementException.
- **nextInt()**
> autoboxing automatically converts the int result of nextInt() into the Integer object.

A **PriorityQueue** says that the element that **goes next** is the one with the **greatest need**.

When you offer() an object onto a PriorityQueue, that object is sorted into the queue.
> The **default sorting** uses the **natural order** of the objects in the queue, but you can **modify the order** by providing your own **Comparator**.

The PriorityQueue **ensures** that when you call peek(), poll() or remove(), the element you get will be **the one with the highest priority**.

###Collection vs. Iterator
---
**Collection** is the **root interface** that describes what is common for all sequence containers.
> One argument for having an interface is that it allows you to **create more generic code**.
> 
> By writing to an interface rather than an implementation, your code can be **applied to more types of objects**.

The Standard **C++ Library** has no common base class for its containers — all commonality between containers is **achieved through iterators**.

In Java, the Collection and iterator are **bound together**, since implementing Collection also means providing an **iterator() method**.

The use of Iterator becomes compelling when you **implement a foreign class**, one that is not a Collection, in which it would be difficult or annoying to make it implement the Collection interface.

Producing an Iterator is the **least-coupled way** of **connecting a sequence to a method** that consumes that sequence, and puts **far fewer constraints** on the sequence class than does implementing Collection.

###Foreach and iterators
---
The **foreach syntax** works with any Collection object.
> The reason that this works is that Java SE5 introduced a new interface called Iterable which contains an iterator() method to produce an Iterator, and the **Iterable interface** is **what foreach uses** to move through a sequence.
>  
> If you create any class that implements Iterable, you can use it in a foreach statement.

A **foreach statement** works with an **array or anything Iterable**, but that doesn’t mean that an array is automatically an Iterable, nor is there any autoboxing that takes place.

If you have an existing class that is Iterable, and you’d like to **add one or more new ways** to use this class **in a foreach statement**, you can use **Adapter Method**.
> When you have one interface and you need another one, writing an adapter solves the problem.
