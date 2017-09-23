##Annotations
###Basic syntax
---
Annotations (also known as metadata) provide a formalized way to **add information to your code** so that you can easily use that data at some later point.
> It allows you **store extra information** about your program in a format that is **tested and verified by the compiler**.
> 
> It can be used to generate **descriptor files** or even **new class definitions** and help ease the burden of writing "boilerplate" code.

The **syntax of annotations** is reasonably simple and consists mainly of the additon of the **@ symbol** to the language. java.lang defined three standard annotations:
- **@Override**
> indicating a method definition that is intend to override a method in the base class.
- **@Deprecated**
> producing a complier warning if this element is used.
- **@SuppressWarnings**
> turning off inappropriate complier warnings

Anytime you create descriptor classes or interfaces that involve repetitive work, you can usually **use annotations** to automate and simplify the process. Annotations are true language constructs and hence are structured, and are **type-checked at compile time**.

**Annotation definitions** look a lot like interface definitions, they **compile to class files** like any other Java interface. An annotation definition requires the **meta-annotations** @Target and @Retension.

There are currently only **three standard annotations** and **four meta-annotations** defined in the Java language. The meta-annotations are for annotating annotations.
- **@Target**
> where this annotation can be applied.
> 
> The possible **ElementType arguments** are:
> - **CONSTRUCTOR**: constructor declaration.
> - **FIELD**: field declaration (includes enum constants).
> - **LOCAL_VARIABLE**: local variable declaration.
> - **METHOD**: method declaration.
> - **PACKAGE**: package declaration.
> - **PARAMETER**: parameter declaration.
> - **TYPE**: class, interface (including annotation type), or enum declaration.
- **@Retension**
> how long the annotation information is kept.
> 
> The possible **RetentionPolicy arguments** are:
> - **SOURCE**: annotations are discarded by the compiler.
> - **CLASS**: annotations are available in the class file by the compiler but can be discarded by the VM.
> - **RUNTIME**: annotations are retained by the VM at run time.
- **@Documented**
> include this annotation in the Javadocs.
- **@Inherited**
> allow subclasses to inherit parent annotations.

###Writing annotation processors
---
Annotations will usually **contain elements to specify values** in your annotations. A program or tool can use these parameters when processing your annotations.
> **Elements** like interface methods, except that you can **declare default values**.
> 
> An annotation without any elements is called a **marker annotation**.
> 
> The values of the annotations are expressed as **name-value pairs**.

Here is a list of the **allowed types** for annotation elements:
- all primitives (int, float, boolean...)
- String
- Class
- enum
- Annotation
- Arrays of any of the above

The compiler is quite picky about default element values, the element must either have **default values** or **values provided** by the class that uses the annotation.
> And none of the non-primitive type elements are allowed to take null as a value, either when declared in the source code or when defined as a default value in the annotation interface.
> 
> For a processor that acts on the presence or absence of an element, it can checking for specific values, like empty strings or negative values.

Annotations are especially when working with frameworks that **require some sort of additional information** to accompany your source code.
> Web services, coustom tag libraries and object/relational mapping tools often require XML descriptors that are **external to the code**.
> 
> Whenever you use an **external descriptor file**, you end up with **two separate source of information about class**, which usually leads to code **synchronization problems**.

Suppose you want to provide basic object/rational mapping funcitonality to **automate the creation of a database mapping**.
> You could **use an XML descriptor file** to specify the name of the class, each member, and information about its database mapping.
> 
> However, using annotations you can **keep all of the information in the JavaBean source file**. You need annotations to define the name of the database table associated with the bean, the columns, and the SQL type to map the bean's properties.

###Using apt to process annotations
---
The **annotation processing tool** apt is designed to be **run on Java source files** rather than compiled classes, it can easily group several annotation processor together, and allowing you to specify multiple classes to be processed.

apt works by using an **AnnotationProcessorFactory** to **create the right kind of annotation processor** for each annotation it finds. When you run apt, you specify either a factory class or a classpath where it can find the factories it needs.

When you create an annotation processor for use with apt, you **can't use the reflection features** in Java because you are working with source code, not compiled class. The **mirror API** solves this problem by allowing you to **view methods, fields, and types in umcompiled source code**.

###Using the Visitor pattern with apt
---
The mirror API provides classes to support the Visitor design pattern.
> A visitor **traverse** a data structures or collection of objects, **performing an operation** on each one.
> 
> The data structure need not be ordered, and the operation that you perform on each object will be **specific to its type**.
> > This decopules the operations from the object themselves, meaning that you can **add new operation without adding methods to the class definations**.

This makes it useful for processing annotations, because a Java class can be thought of **as a collection of objects** such as TypeDeclarations, FieldDeclarations, MethodDeclarations...
> When you use the apt tool with the Visitor pattern, you provide a Visitor class which **has a method of handling each type of declaration** that you visit.
> 
> Thus you can implement appropriate behavior for annotations on methods, class, fields...

###Annotation-based unit testing
---
**Unit testing** is the practice of creating one or more tests for **each method** in a class, in order to regularly test the portions of a class for correct behavior.

Our test framework is called **@Unit**. The most basic form of testing only needs the @Test annotation to indicate which **methods** should be tested.  @Unit test methods can have any access that you’d like, including private.

To use @Unit, all you need to do is mark the appropriate methods and fields with **@Unit test tags** and then have your build system run @Unit on the resulting class.
