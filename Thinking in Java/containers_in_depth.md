##Containers in Depth
###Filling containers
---
Filling containers **suffers** from the same **deficiency** as java.util.Arrays. Just as with Arrays, there is a companion class called **Collections** containing **static utility methods**.
- **Collections.fill()**
> just duplicates a single object reference throughout the container.
> 
> only works for **List objects**, but the resulting list can be passed to a constructor or to an addAll() method.
- **Collections.nCopies()**
> creates a List which is passed to the constructor.

Virtually all Collection subtypes have a constructor that **takes another Collection object**, from which it can fill the new container.
> In order to easily **create test data**, then, all we need to do is build a class that takes constructor arguments of **a Generator** and **a quantity value**.
> 
> We can take the same approach for a Map, but that **requires a Pair class** since a pair of objects must be produced by each call to a Generator’s next() in order to populate a Map.

An alternative approach to the problem of **producing test data** for containers is to **create custom Collection and Map implementations**.
> Each java.util container has its own **Abstract class** that provides a partial implementation of that container, so all you must do is implement the necessary methods in order to produce the desired container.
> 
> In order to create a **read-only Map**, you inherit from **AbstractMap** and implement **entrySet()**.
> 
> In order to create a **read-only Set**, you inherit from **AbstractSet** and implement **iterator() and size()**.
> 
> In order to create a **read-only List**, you inherit from **AbstractList** and implement **get() and size()**.

###Collection functionality
---
The following table shows everything **you can do with a Collection**, and thus, everything you can do with **a Set or a List**. (List also has additional functionality.) Maps are not inherited from Collection and will be treated separately.

| method | description |
| --- | --- |
| boolean contains(T) | true if the container holds the argument which is of generic type T. |
| Boolean containsAll(Collection<?>) | true if the container holds all the elements in the argument. |
| boolean isEmpty() | true if the container has no elements. |
| Iterator<T> iterator() | Returns an Iterator<T> that you can use to move through the elements in the container. |
| int size() | Returns the number of elements in the container. |
| Object[] toArray() | Returns an array containing all the elements in the container. |
| <T>T[] toArray(T[] a) | Returns an array containing all the elements in the container. The runtime type of the result is that of the argument array a rather than plain Object. |
| boolean add(T) | Ensures that the container holds the argument which is of generic type T. Returns false if it doesn’t add the argument. ("Optional.") |
| boolean addAll(Collection<? extends T>) | Adds all the elements in the argument. Returns true if any elements were added. ("Optional.") |
| void clear() | Removes all the elements in the container. ("Optional.") |
| Boolean remove(Object) | If the argument is in the container, one instance of that element is removed. Returns true if a removal occurred. ("Optional") |
| boolean removeAll(Collection<?>) | Removes all the elements that are contained in the argument. Returns true if any removals occurred. ("Optional.") |
| Boolean retainAll( Collection<?>) | Retains only elements that are contained in the argument (an "intersection," from set theory). Returns true if any changes occurred. ("Optional.") |
> There’s **no get() method** for random-access element selection.
> > That’s because Collection also includes **Set**, which maintains its **own internal ordering**.
> 
> > If you want to examine the elements of a Collection, you must use an **iterator**.

###Optional operations
---
The methods that **perform various kinds of addition and removal** are **optional operations** in the Collection interface.
> This means that the implementing class is **not required** to provide **functioning definitions** for these methods.

An **"optional" operation** means that calling some methods will **nor perform** meaningful behavior. Instead, they will **throw exceptions**. It appears that compile-time type safety is **discarded**.
> If an operation is optional, the compiler still restricts you to calling only the methods in that interface.

In addition, most methods that take a Collection as an argument only read from that Collection, and all the **"read" methods** of Collection are **not optional**.

Define methods as "optional" prevents an **explosion of interfaces** in the design. The "unsupported operation" approach achieves an important goal of the Java containers library:
> The containers are simple to learn and use.
> 
> Unsupported operations are a special case that can be **delayed until necessary**.

For this approach to work:
> The UnsupportedOperationException must be a rare event.
> > That is, for most classes, all operations should work, and only in special cases should an operation be unsupported.
> 
> When an operation is unsupported, there should be reasonable likelihood that an UnsupportedOperationException will **appear at implementation time**, rather than after you’ve shipped the product to the customer.

Unsupported operations are **only detectable at run time**, and therefore represent dynamic type checking.

A common source of unsupported operations involves a container backed by a **fixed-sized data structure**.
> You get such a container when you turn an array into a List with the Arrays.asList() method.
> 
> You can also choose to make any container (including a Map) throw UnsupportedOperationExceptions by using the "unmodifiable" methods in the Collections class.

The **unmodifiable methods** in the Collections class **wrap the container in a proxy** that produces an UnsupportedOperationException if you perform any operation that **modifies the container** in any way.
> The goal of using these methods is to **produce a constant container object**.

###List & Queues
---
The basic List is quite simple to use:
- call **add()** to insert objects.
- use **get()** to get them out one at a time.
- call **iterator()** to get an Iterator for the sequence. 

The only two Java SE5 **implementations of Queue** are **LinkedList and PriorityQueue**.
> They are **differentiated by ordering behavior** rather than performance.

A deque (double-ended queue) is like a queue, but you can **add and remove** elements from **either end**.
> There are methods in LinkedList that support deque operations, but there is **no explicit interface for a deque** in the Java standard libraries.
> 
> You can **create a Deque** class **using composition**, and simply expose the relevant methods from **LinkedList**.

###Sets and storage order
---
When creating your own types, be aware that a Set needs a way to maintain **storage order**. 
> How the storage order is maintained varies from one implementation of Set to another. 
> 
> Different Set implementations not only have **different behaviors**, they have different requirements for the type of object that you can put into a particular Set.

The basic Set implementations:
- **Set** (interface)
> Each element that you add to the Set must be **unique**.
> > Elements added to a Set must at least define **equals()** to establish object **uniqueness**.
> 
> The Set interface does **not guarantee** that it will maintain its elements in **any particular order**.
- **HashSet**
> For Sets where **fast lookup time** is important.
> 
> Elements must also **define hashCode()**.
> 
> In the absence of other constraints, this should be your **default choice** because it is optimized for speed.
- **TreeSet**
> An **ordered Set** backed by a tree. You can extract an **ordered sequence** from a Set.
> 
> Elements must also implement the **Comparable interface**.
- **LinkedHashSet**
> Has the **lookup speed** of a HashSet, but internally **maintains the order** in which you add the elements (the insertion order) using a linked list.
> 
> Elements must also **define hashCode()**.

You must create an **equals()** for both **hashed and tree storage**, but the **hashCode()** is necessary only if the class will be placed in a **HashSet or LinkedHashSet**.
> For good programming style, you should **always override hashCode() when you override equals()**.

The elements in a **SortedSet** are **guaranteed** to be in **sorted order** (sorted according to the comparison function of the object), which allows additional functionality to be provided with the following methods that are in the SortedSet interface:
- **Comparator comparator()**
> Produces the Comparator used for this Set, or null for natural ordering.
- **Object first()**
> Produces the lowest element.
- **Object last()**
> Produces the highest element.
- **SortedSet subSet(fromElement, toElement)**
> Produces a view of this Set with elements from fromElement, inclusive, to toElement, exclusive.
- **SortedSet headSet(toElement)**
> Produces a view of this Set with elements less than toElement.
- **SortedSet tailSet(fromElement)**
> Produces a view of this Set with elements greater than or equal to fromElement.

###Understanding Maps
---
The basic idea of a map (**associative array**) is that it maintains **key-value associations** (pairs) so you can look up a value using a key.
> The standard Java library contains different basic implementations of Maps:
> > HashMap, TreeMap, LinkedHashMap, WeakHashMap, ConcurrentHashMap, and IdentityHashMap.
> 
> They differ in behaviors including **efficiency**, the **order** in which the pairs are held and presented, how long the objects are **held** by the map, how the map works in **multithreaded** programs, and how key **equality** is determined.

The basic Map implementations:
- **HashMap**
> Implementation based on a **hash table**.
> 
> Provides **constant-time performance** for inserting and locating pairs.
> 
> Performance can be **adjusted via constructors** that allow you to set the capacity and load factor of the hash table.
- **LinkedHashMap**
> When you **iterate** through it, you get the pairs in **insertion order**, or in **least-recently-used** (LRU) **order**.
> 
> Only slightly slower than a HashMap, except when iterating.
- **TreeMap**
> Implementation based on a **red-black tree**.
> 
> When you view the keys or the pairs, they will be in **sorted order** (determined by Comparable or Comparator).
> 
> TreeMap is the **only Map** with the **subMap() method**, which allows you to **return a portion of the tree**.
- **WeakHashMap**
> A map of weak keys that allow objects referred to by the map to be released.
> 
> If no references to a particular key are held outside the map, that key may be garbage collected.
- **ConcurrentHashMap**
> A **thread-safe Map** which does not involve synchronization locking.
- **IdentityHashMap**
> A hash map that **uses ==** instead of equals() to **compare keys**. 

The **essential methods** in an associative array are **put() and get()**.
> **toString()** has been **overridden** to print the key-value pairs. 

Instead of a slow liner search for the key, HashMap uses a **special value** called a **hash code**.
> The hash code is a way to **take some information in the object** in question and turn it into a "relatively unique" int for that object.
> 
> A HashMap takes the hashCode() of the object and uses it to **quickly hunt for the key**.

Any key must have an **equals() method**.
> If the key is used in a **hashed Map**, it must also have a proper **hashCode()**.
> 
> If the key is used in a **TreeMap**, it must implement **Comparable**.

If you have a **SortedMap** (**TreeMap**), the **keys** are guaranteed to be in **sorted order**, which allows additional functionality to be provided with these methods in the SortedMap interface:
- **Comparator comparator()**
> Produces the comparator used for this Map, or null for natural ordering.
- **T firstKey()**
> Produces the lowest key.
- **T lastKey()**
> Produces the highest key.
- **SortedMap subMap(fromKey, toKey)**
> Produces a view of this Map with keys from fromKey, inclusive, to toKey, exclusive.
- **SortedMap headMap(toKey)**
> Produces a view of this Map with keys less than toKey.
- **SortedMap tailMap(fromKey)**
> Produces a view of this Map with keys greater than or equal to fromKey.

The LinkedHashMap **hashes everything for speed**, but also produces the pairs in **insertion order** during a traversal.
> A LinkedHashMap can be configured in the constructor to **use a least-recently-used (LRU) algorithm** based on accesses, so elements that haven’t been accessed (and thus are candidates for removal) appear at the front of the list.
> 
> This allows easy creation of programs that do **periodic cleanup** in order to save space. 

###Hashing and hash codes
---
A **proper equals()** must satisfy the following five conditions:
- **Reflexive**
> For any x, x.equals(x) should return true.
- **Symmetric**
> For any x and y, x.equals(y) should return true if and only if y.equals(x) returns true.
- **Transitive**
> For any x, y, and z, if x.equals(y) returns true and y.equals(z) returns true, then x.equals(z) should return true.
- **Consistent**
> For any x and y, multiple invocations of x.equals(y) consistently return true or consistently return false, provided no information used in equals comparisons on the object is modified.
- For any non-null x, x.equals(null) should return false.

The whole point of hashing is speed: Hashing **allows the lookup** to **happen quickly**.
> Since the bottleneck is in the speed of the key lookup, one of the solutions to the problem is to **keep the keys sorted** and then use **Collections.binarySearch()** to perform the lookup.

Hashing goes further by saying that all you want to do is to **store the key somewhere** in a way that it can be **found quickly**.
> The **fastest structure** in which to store a group of elements is an **array**, so that will be used for representing the **key information**.
> > Because an array **cannot be resized**, and we want to store an indeterminate number of values in the Map, so the array will not hold the keys.
> 
> From the **key object**, a number will be derived that will **index into the array**. This number is the **hash code**, produced by the **hashCode() method** (hash function) defined in Object and presumably overridden by your class.
> > To solve the problem of the fixed-size array, **more than one key** may produce the **same index**. So any key object’s hash code will **land somewhere** in that array.

The process of **looking up a value** starts by **computing the hash code** and using it to **index into the array**. 
> If you could guarantee that there were **no collisions**, then you’d have a **perfect hashing junction**.
> 
> In all other cases, collisions are **handled by external chaining**: The array doesn’t point directly to a value, but instead **to a list of values**.
> > These values are **searched in a linear** fashion using the **equals()** method.
>  
> > The linear search is much slower, but if the hash function is good, there will only be **a few values in each slot**.
> 
> > So instead of searching through the entire list, you quickly **jump to a slot** where you only have to **compare a few entries** to find the value.

The creation of the actual value that’s used to index into the array of buckets **dependent on the capacity** of the particular HashMap object.
> That **capacity changes** depending on **how full the container is**, and **what the load factor is**.

Thus, the value produced by your hashCode() will be **further processed** in order to **create the bucket index**.

The **most important factor** in creating a hashCode() is that it **produces the same value for a particular object** every time it is called.

In addition, you will probably **nor want to** generate a hashCode() that is **based on unique object information**.
> In particular, **the value of this** makes a **bad hashCode()** because then you can’t generate a new key identical to the one used to put() the original key-value pair.
> 
> Because the **default implementation of hashCode()** does use the **object address**. 

For a hashCode() to be **effective**, it must generate a value **based on the contents of the object**.
> This value doesn’t have to be unique — you should lean **toward speed rather than uniqueness**.
> 
> But between **hashCode() and equals()**, the **identity of the object** must be **completely resolved**.

A **good hashCode()** should result in an **even distribution of values**.
> If the values **tend to cluster**, then the HashMap or HashSet will be **more heavily loaded** in some areas and will **not be as fast as** it can be with an evenly distributed hashing function.

A basic recipe for generating a **decent hashCode()** as following:
- Store some **constant nonzero value**, say 17, in an int variable called **result**.
- For each **significant field fin** your object (that is, each field taken into account by the equals() method), **calculate an int hash code c** for the field:

| Field type | Calculation |
| --- | --- |
| boolean | c = ( f ? 0 : 1) |
| byte, char, short, or int | c = (int)f |
| long | c = (int)(f ^ (f>>>32)) |
| float | c = Float.floatToIntBits(f); |
| double | long l = Double.doubleToLongBits(f); c = (int)(1 ^ (l>>>32)) |
| Object, where equals() calls equals() for this field | c = f.hashCode() |
| Array | Apply above rules to each element |

- **Combine the hash code(s) computed above**: result = 37 * result + c;
- Return result.
- Look at the resulting hashCode() and make sure that **equal instances have equal hash codes**.

###Holding references
---
The **java.lang.ref** library contains a set of classes that allow **greater flexibility in garbage collection**.
> These classes are especially useful when you have **large objects** that may cause **memory exhaustion**.

If an object is **reachable**, it means that you have an **ordinary reference on the stack** that goes right to the object, but you might also have a reference to an object that has a reference to the object in question; there can be many intermediate links.
> If an object is reachable, the garbage collector **cannot release it** because it’s still in use by your program.
>  
> If an object **isn’t reachable**, there’s no way for your program to use it, so it’s safe to **garbage collect that object**.

You use **Reference objects** when you want to continue to hold on to a reference to that object, but you also want to **allow the garbage collector to release** that object.
> Thus, you have a way to use the object, but **if memory exhaustion is imminent**, you allow that object to be released.

There are **three classes** inherited from the abstract class Reference, in the order of **SoftReference**, **WeakReference**, and **PhantomReference**, each one is "weaker" than the last and corresponds to a different level of reachability.
- **SoftReference**
> for implementing **memory-sensitive caches**.
- **WeakReference**
> for implementing "**canonicalizing mappings**" that do not prevent their keys (or values) from being reclaimed.
> 
> To save storage, instances of objects can be **simultaneously used in multiple places** in a program.
- **PhantomReference**
> for scheduling **pre-mortem cleanup actions** in a more flexible way than is possible with the Java finalization mechanism.

Each of these provides a **different level of indirection** for the garbage collector if the object in question is only reachable through one of these Reference objects.

With **SoftReferences and WeakReferences**, you have a choice about **whether to place them on a ReferenceQueue** (the device used for premortem cleanup actions).
> However, a **PhantomReference** can only be **built on a ReferenceQueue**.

The **WeakHashMap** is designed to make the creation of **canonicalized mappings** easier.
> In such a mapping, you are **saving storage by creating only one instance** of a particular value.
> 
> When the program needs that value, it **looks up the existing object** in the mapping and uses that (rather than creating one from scratch).
> 
> The keys and values that you place in the WeakHashMap are **automatically wrapped in WeakReferences** by the map.

Since this is a **storage-saving technique**, it’s very convenient that the WeakHashMap allows the **garbage collector** to **automatically clean up** the keys and values.
> The **trigger** to allow cleanup is that the **key is no longer in use**.

###Choosing an implementation
---
Although there are only **four fundamental container types** — **Map**, **List**, **Set**, and **Queue** — there is more than one implementation of each interface.

Each different implementation **has its own features, strengths, and weaknesses**. Sometimes different implementations of a particular container will have operations in common, but the **performance** of those operations will be **different**.

The **different types of Queues** in the Java library are differentiated **only by the way they accept and produce values**.

The distinction between containers often comes down to **what they are "backed by"** — that is, the **data structures** that physically implement the desired interface.
> **ArrayList** is backed by an **array**.
> 
> **LinkedList** is implemented in the usual way for a **doubly linked list**.
> 
> If you want to do many **insertions and removals** in the **middle of a list**, a **LinkedList** is the appropriate choice.
> > If not, an **ArrayList** is typically faster.

A Set can be implemented as either a **TreeSet**, a **HashSet**, or a **LinkedHashSet**.
> **HashSet** is for typical use and provides **raw speed on lookup**.
> 
> **LinkedHashSet** keeps pairs in **insertion order**.
> 
> **TreeSet** is backed by TreeMap and is designed to produce a **constantly sorted set**.

**Insertions** for all the **Map** implementations except for IdentityHashMap **get significantly slower** as the size of the Map gets large.
> However, **lookup is much cheaper** than insertion, which is good because you’ll typically be **looking items up much more often** than you insert them.

A **TreeMap** is generally **slower than a HashMap**. A TreeMap is a way to **create an ordered list**. The behavior of a tree is such that it’s **always in order** and doesn’t have to be specially sorted.
> You can **call keySet()** to **get a Set view of the keys**, then **toArray()** to **produce an array of those keys**.
> 
> You can then use the static method **Arrays.binarySearch()** to **rapidly find objects** in your sorted array.
> 
> You can easily **create a HashMap from a TreeMap** with a single object creation or call to **putAll()**.

When you’re **using a Map**, your **first choice** should be **HashMap**, and only if you need a constantly sorted Map will you need TreeMap.
> **LinkedHashMap** tends to be **slower than HashMap for insertions** because it maintains the linked list (to preserve insertion order) in addition to the hashed data structure. Because of this list, **iteration is faster**.
> 
> **IdentityHashMap** has different performance because it **uses == rather than equals() for comparisons**.

It’s possible to **hand-tune a HashMap** to increase its performance for your particular application.
- **Capacity**
> The number of buckets in the table.
- **Initial capacity**
> The number of buckets when the table is created. HashMap and HashSet have constructors that allow you to specify the initial capacity.
- **Size**
> The number of entries currently in the table.
- **Load factor**
> Size/capacity. A load factor of 0 is an **empty table**, 0.5 is a **half-full table**.
> 
> A **lightly loaded table** will have few collisions and so is **optimal for insertions and lookups** (but will slow down the process of traversing with an iterator).

HashMap and HashSet have constructors that allow you to **specify the load factor**, which means that when this **load factor is reached**, the container will **automatically increase the capacity** (the number of buckets) by **roughly doubling** it and will **redistribute the existing objects** into the new set of buckets (this is called **rehashing**).
> The **default load factor** used by HashMap is **0.75**.
> > It **doesn’t rehash until** the table is **three-fourths full**.
> 
> This seems to be **a good trade-off** between time and space costs.
> >  A higher load factor **decreases the space** required by the table **but increases the lookup** cost, which is important because lookup is what you do most of the time (including both get() and put()). 

###Utilities
---
There are a number of **standalone utilities** for containers, expressed as **static methods** inside the **java.util.Collections** class.

Utilities to **perform sorting and searching for Lists** have the same names and signatures as those for sorting arrays of objects, but are **static methods of Collections** instead of Arrays.

The Java containers library uses a **fail-fast mechanism** that **looks for any changes to the container** other than the ones your process is personally responsible for.
> If it detects that **someone else is modifying the container**, it immediately produces a **ConcurrentModification- Exception**.

###Java 1.0/1.1 containers
---
The only **self-expanding sequence** in Java 1.0/1.1 was the **Vector**.

The **Enumeration interface** is smaller than Iterator, with **only two methods**:
- **boolean hasMoreElements()**
> produces true if this enumeration contains more elements.
- **Object nextElement()**
> returns the next element of this enumeration if there are any more.

You can **produce an Enumeration** for any Collection by using the **Collections.enumeration() method**.

A **BitSet** is used if you want to **efficiently store** a lot of **on-off information**. The **minimum size** of the BitSet is that of a long: **64 bits**.

An **EnumSet** is usually a **better choice** than a BitSet if you have a **fixed set of flags** that you can name, because the EnumSet allows you to **manipulate the names rather than numerical bit locations**, and thus reduces errors.
> EnumSet also **prevents** you from **accidentally adding new flag locations**, which could cause some serious, difficult-to-find bugs.

The **only reasons** you should **use BitSet** instead of EnumSet is if you **don’t know how many flags** you will need **until run time**, or if it is unreasonable to assign names to the flags, or you need one of the special operations in BitSet. 
