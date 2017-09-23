##I/O
###The File class
---
The **File class** can represent either **the name of a particular file** or **the names of a set of files in a directory**.
> It's **list() method** returns **an array of String**.
> 
> You can use a **File object** to create a **new directory** or an **entire directory path** if it doesn't exist.
> 
> You can also look at the characteristics of files, see whether a File object **represents a file or a directory**, and **delete a file**.

###Input and output
---
Through inheritance, everything derived from the **InputStream or Reader classes** has basic methods called **read()** for reading a single byte or an array of bytes; everything derived from the **OutputStream or Writer classes** has basic methods called **write()** for writing a single byte or an array of bytes.

InpuStream's job is to represent classes that **produce input from different source**, these source can be:
- **An array of bytes**
- **A String object**
- **A file**
- **A "pipe"**
- **A sequence of other streams**, so you can collect them together into a single stream
- Other source, such as an **Internet connection**

###Adding attributes and useful interfaces
---
The **FileInputStream** provide a base class for **"decorator" classes** that **attach attributes or useful interfaces** to input streams, the FileOutputStream likes FileInputStream.

The reason for the existence of the **"filter" classes** in the Java I/O library is that the abstract "filter" class is the **base class for all the decorators**. The classes that **provide the decorator interface** to control a particular InputStream or OutputStream are the **FilterInputStream and FilterOutputStream**, they are derived from InputStream and OutputStream.

**DataInputStream** allows you to **read different types of primitive data** as well as **String objects**; **DataOutputStream** formats each of the **primitive types and String objects** onto a stream in such a way that any DataInputStream can read them on any machine.

You'll need to **buffer your input** almost every time, regardless of the I/O device you're connecting to, so it would made more sense for the I/O library to have a special case for unbuffered input rather than buffered input.

The original intent of **PrintStream** was to print all of the primitive data types and String objects **in a viewable format**, it's **print() and println()** methods are **overload to print all the various types**.
> PrintStream can be problematic because it **traps all IOExceptions**.
> 
> Also, PrintStream **doesn't internationalize properly** and **doesn't handle line breaks** in a platform-independent way. These problems are **solved with PrintWriter**.

**BufferedOutputStream** is a **modifier** and tells the stream to use buffering so you **don't get a physical write every time** you write write to the stream.

###Readers & Writers
---
The **InputStream and OutputStream** classes provide a valuable functionality in the form of **byte-oriented I/O**, whereas the **Reader and Writer** classes provide **Unicode-compliant, character-based I/O**.
> The most important reason for the Reader and Writer hierarchies is **for internationlizaton**. 
> 
> **InputStreamReader** converts an **InputStream to a Reader** and OutputStreamWriter converts an OutputStream to a Writer.

###Off by itself: RandomAccessFile
---
**RandomAccessFile** is used for files containing records of known size so that you can **move from one record to another using seek()**, then read for change the records.
> The records don't have to be the same size; you just have to **determine how big they are** and **where they are placed in the file**.

Using RandomAccessFile is like to using a **combined DataInputStream and DataOutputStream** (it implement the DataInput and DataOutput interfaces). When using RandomAccessFile, you must know **the layout of the file** so that you can manipulate it properly.
> RandomAccessFile **implements DataInput and DataOutput interfaces** (which are also implemented by the DataInputStream and DataOutputStream), it **stands alone**, as a **direct descendant of Object**.

Some **useful methods** of RandomAccessFile: 
- **getFilePointer()**
> find out where you are in the file.
- **seek()**
> move to a new point in the file.
- **length()**
> determine the maximum size of the file.

A PrintWriter **formats data** so that it's readable by a human. However, to **output data for recovery by another stream**, you **use a DataOuptStream to write the data and a DataInputStream to recover the data**.
> The Java guarantees that you can **accurately recover the data** using a DataInputStream if you use a DataOutputStream to write the data.

When you're using a DataOutputStream, the **only reliable way** to write a String so that it **can be recovered** by a DataInputStream is to **use UTF-8 encoding**.
> UTF-8 is a **multi-byte format**, and the **length of encoding varies** according to the actual character set in use.
> 
> UTF-8 **encodes ASCII characters** in a **single byte**, and **non-ASCII characters** in **two or three bytes**. The length of string is stored in the first two bytes of the UTF-8 string.

The DataOutputStream's **writeDouble() method** stores the double number to the stream and the complementary **readDouble() meothd** recovers it.
> But for any of the reading methods to work correctly, you must know **the exact placement of the data item** in the stream. 
> 
> You must either have a **fixed format** for the data in the file, or **extra information** must be stored in the file that you **parse to determine where the data** is located.

###Standard I/O
---
Following the standard I/O model, Java has **System.in**, **System.out**, and **System.err**.
> System.out is already pre-wrapped as a PrintStream object; System.err is likewise a PrintStream.
> 
> However, System.in is a raw InputStream with no wrapping.
> > You must be **wrapped System.in** before you can read from it.
> 
> > You typically read input a line at a time using readLine(), so you can **wrap System.in in a BufferedReader**, which requires you to convert System.in to a Reader **using InputStreamReader**.

The Java System class allows you to **redirect** the standard input, output, and error **I/O streams** using simple static method calls:
- **setIn(InputStream)**
- **setOut(PrintStream)**
- **setErr(PrintStream)**

I/O **redirection** manipulates **streams of bytes**, not streams of characters.

###New I/O
---
The Java "new" I/O library has one goal: speed. The speed increase occurs both in **file I/O** and in **network I/O**. The speed comes from using structures that are **closer to the operating system's way** of performing I/O: **channels and buffers**.
> You don't interact directly with the channel, you **interact with the buffer** and send the buffer into the channel.
> 
> The **channel** either **pulls data from buffer**, or **puts data into the buffer**.

The only kind of buffer that **communicates directly with a channel** is **ByteBuffer** (a buffer holds raw bytes).
> You create one by telling it how much storage to allocate, and there are methods to put and get data, in either **raw bytes form** or as **primitive data types**.

Three of the "old" I/O have been modified so that they **produce a FileChannel** by **getChannel() method**:
- **FileInputStream**
- **FileOutputStream**
- **RandomAccessFile**
> for both reading and writing

These are the **byte manipulation streams**, in keeping with the **low-level nature** of nio. The Reader and Writer character-mode classes do not produce channels, but the **java.nio.channels.Channels** class has utility methods to **produce Readers and Writers from channels**.

A **channel** is fairly basic:
> You can **hand it a ByteBuffer** for reading or writing, and you can **lock regions of the file** for exclusive access.

One way to **put bytes into a ByteBuffer** is to stuff them in **directly using one of the "put" methods**, to put one or more bytes, or values of primitive types. You can also **"wrap" an existing byte array** in a ByteBuffer using the **wrap() method**. When you do this, the underlying array is **not copied**, but instead is **used as the storage** for the generated ByteBuffer.

For **read-only access**, you must explicitly allocate a ByteBuffer using the **static allocate() method**.
> The goal of nio is to rapidly move large amounts of data, so the size of the ByteBuffer should be significant.
> 
> It also possible to go for **even more speed** by using **allocateDirect()** instead of allocate() to **produce a "direct" buffer** that may have an even higher coupling with the operating system.

The **ByteBuffer object** contains **plain bytes**, and to turn these into characters, we must either encode them as we put them in or decode them as they out of the buffer.

A "**view buffer**" allows you to look at an underlying ByteBuffer **through the window of a particular primitive type**, and any changes you make to the view are reflect in modifications to the data in the ByteBuffer.
> Once the underlying ByteBuffer is **filled** with ints or some other primitive type **via a view buffer**, then that ByteBuffer can be **written directly to a channel**.
> 
> You can just as easily read from a channel and use a view buffer to **convert everything to** a particular type of primitive.

A **ByteBuffer** stores data in **big endian form**, and data sent over a network always uses big endian order. You can change the endian-ness of a ByteBuffer using **order()** with an argument of **ByteOrder.BIG_ENDIAN or ByteOrder.LITTLE_ENDIAN**.

**ByteBuffer** is the **only way** to move data **into and out of channels**, and you can only create a **standalone primitive-type buffer**, or get one from a ByteBuffer using an "as" method.
> You cannot convert a primitive-typed buffer to a ByteBuffer. However, since you are able to move primitive data into and out of a ByteBuffer via a view buffer, this is not really a restriction.

A **Buffer** consists of data and **four indexes** to access and manipulate this data efficiently:
- **mark**
- **position**
- **limit**
- **capacity**

There are methods to **set and reset** these indexes and to **query** their value:
- **capacity()**
> Returns the buffer’s capacity.
- **clear()**
> Clears the buffer, sets the position to zero, and limit to capacity. You call this method to overwrite an existing buffer.
- **flip()**
> Sets limit to position and position to zero. This method is used to prepare the buffer for a read after data has been written into it.
- **limit()**
> Returns the value of limit.
- **limit(int lim)**
> Sets the value of limit.
- **mark()**
> Sets mark at position.
- **position()**
> Returns the value of position.
- **position(int pos)**
> Sets the value of position.
- **remaining()**
> Returns (limit - position).
- **hasRemaining()**
> Returns true if there are any elements between position and limit.

Methods that **insert and extract data** from the buffer **update these indexes** to reflect the changes.

**Memory-mapped files** allow you to create and modify files that are too big to bring into memory. You can **pretend that the entire file is in memory** and that you can access it by simply treating it **as a very large array**.
> The file appears to be **accessible all at once** because **only portions** of it are **brought into memory**, and other parts are swapped out.
> 
> The file-mapping facilities of the underlying operating system are used to **maximize performance**.

**File locking** allows you to **synchronize access** to a file as a shared resource. 
> However, two threads that contend for the same file may be **in different JVMs**, or one may be **a Java thread** and the other **some native thread** in the operating system.
> 
> The file locks are **visible to other operating system processes** because Java file locking **maps directly to the native operating system** locking facility.

You get a FileLock on the **entire file** by calling either **tryLock() or lock() on a FileChannel**.
> tryLock() is **non-blocking**, it tries to grab the lock, but it simple returns from the method call if it cannot.
> 
> lock() **blocks until** the lock is **acquired**, or the thread that invoked lock() is **interrrupted**, or the channel on which the lock() method is called is **closed**.
> > A lock is **released** using **FileLock.release()**.

Although the zero-argument locking methods adapt to changes in the size of a file, **locks with fixed size** do **not change** if the **file size changes**.
> If a lock is acquired for a region from position to position+size and the file increases beyond position+size, then the **seciton beyond position+szie is not locked**.

Support for exlusive or shared locks must be provided by the underlying operating system. If the operating system dose not support shared locks and a request is made for one, an exclusive lock is used instead. The **type of lock** can be queried using **FileLock.isShared()**.

The lock are **automatically released** when the JVM exits, or the channel on which it was acquired is closed, but you can also explicitly call release() on the FileLock object.

###Compression
---
The Java I/O library contains classes to support reading and writing streams **in a compression format**.
> These classes are part of the InputStream and OutputStream hierachies, because the compression library **works with bytes**.

With **Zip format** you can easily store multiple files, and there's even a seperate class to make the process of reading a Zip file easy.
> You can use the **Checksum classes** to calculate and verify the checksum for the file.
> > There are two Checksum types: **Adler32** and **CRC32**.

The Zip format is used in the **JAR file format**, which is a way to **collect a group of files into a single compressed file**.
> A JAR file consists of a single file containing a collection of zipped files along with a "**mainifest**" that describes them.

###Object serialization
---
Java's **object serialization** allows you to take any object that implements the **Serializable interface** and **turn it into a sequance of bytes** that can later be **fully restored to regenerate** the original object.

Object serialization allows you to implement **lightweight persistence**.
> **Persistence** means that an **object's lifetime** is not determined by whether a program is executing; the object **lives in between invocations of the program**.

Object serilization was added to the language to **support two major features**:
- **Java RMI** allows objects that live on other machines to **behave as if they live on your machine**.
- When a **JavaBean** is used, its **state information** is generally configured at design time.
> This state information must be **stored and later recovered** when the program is started.

To **serialize an object**, you create **some sort of OutputStream object** and then **wrap it inside an ObjectOutputStream object**.
> At this point you need only **call writeObject()**, and you object is **serialized and sent to the OutputStream**.
> 
> To **reverse the process**, you **wrap an InputStream** inside an **ObjectInputStream** and **call readObject()**.

A particularly clever aspect of object serialization is that it not only **saves an image of your object**, but it also **follows all the references contained in your object and saves those objects**, and follows all the references in each of those objects... 
> This is sometimes referred to as the "**web of objects**" that a single object can be connected to, and it includes arrays of references to objects as well as member objects. 
> 
> If you had to maintain you own object serialization scheme, Java object serialization can pull it off flawlessly, no doubt using an optimized algorithm that traverses the web of objects.

You can **control the process of serialization** by implementing the **Externalizable interface**.
> The Externalizable interface extends the Serializable interface and adds two methods, **writeExternal()** and **readExternal()**.
> 
> That are **automatically called** for your object during serialization and deserialization so that you can perform your **special operations**.

When an object which implemented Externalizable interface is recoverd, the object's **constructor is called**.
> This is **different from recovering** a Serializable object, in which the object is constructed entirely from its stored bits, with **no constructor calls**.
> 
> With an Externalizable object, all the normal default **construction behavior occurs** (including the initializations at the point of field definition), and then **readExternal() is called**.

When you’re controlling serialization, there might be a particular subobject that you **don’t want Java’s serialization** mechanism to automatically save and restore.
> One way to **prevent sensitive parts** of your object from being serialized is to **implement** your class as **Externalizable**, Then **nothing is automatically serialized**, and you can **explicitly serialize** only the necessary parts inside **writeExternal()**.

If you’re working with a **Serializable object**, however, **all serialization happens automatically**. To control this, you can turn off serialization on a **field-by-field** basis using the **transient keyword**.

If you’re not keen on implementing the Externalizable interface, there’s another approach. 
> You can **implement the Serializable interface** and **add methods** called **writeObject() and readObject()** that will automatically be called when the object is serialized and deserialized, respectively.
> 
> That is, if you provide these two methods, they will be **used instead of the default serialization**.

The methods must have these **exact signatures**:
```
private void writeObject(ObjectOutputStream stream)
throws IOException;

private void readObject(ObjectlnputStream stream)
throws IOException, ClassNotFoundException
```
> They are **defined as private**. However, you don’t actually call them from other members of this class, but instead the **writeObject() and readObject()** methods of the **ObjectOutputStream and ObjectInputStream** objects call your **object’s writeObject() and readObject() methods**.
> 
> It would appear that when you **call ObjectOutputStream.writeObject()**, the Serializable object that you pass it to is interrogated (using reflection) to see **if it implements its own writeObject()**.
> > If so, the normal serialization process is **skipped** and the **custom writeObject() is called**.

If you use the **default mechanism to write the non-transient parts** of your object, you must **call defaultWriteObject()** as the first operation in writeObject(), and **defaultReadObject()** as the first operation in readObject().
> It would appear, for example, that you are calling defaultWriteObject() for an ObjectOutputStream and passing it no arguments, and yet it somehow turns around and **knows the reference to your object** and **how to write all the non-transient parts**.

It’s possible that you might want to **change the version of a serializable class**. This is supported, but you’ll probably do it only in special cases, and it requires an extra depth of understanding.
> The **versioning mechanism** is too simple to work reliably in all situations, especially with JavaBeans. They’re working on a correction for the design.

As long as you’re **serializing everything to a single stream**, you’ll recover the same web of objects that you wrote, with no accidental duplication of objects. The objects will be written in whatever state they are in at the time you serialize them.

The safest thing to do if you want to save the state of a system is to **serialize as an "atomic" operation**.
> Putting all the objects that comprise the state of your system **in a single container** and simply write that container out **in one operation**.
> 
> Then you can **restore it with a single method call** as well.

###XML & Preferences
---
An important **limitation of object serializaiton** is that **only Java programs can deserialization such objects**.
> A more interoerable solution is to **convert data to XML format**, which allows it to be **consumed by a large variety of platforms and languages**.

The **Preference API** is much closer to persistence than it is to object serialization, because it **automatically stores and retrieves** your information. However, its use is restricted to small and limited data sets:
> You can **only hold primitives and Strings**, and the **length of** each stored **String** can't be longer than **8K**.

The Preference API is designed to store and retrieve **user preference and program-configuration settings**.
> Preferences are **key-value sets** stored in a hireachy of nodes.
> 
> It's typical to **create a single node** named after your class and **store the information there**.
