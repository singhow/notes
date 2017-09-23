##Access Control
Java provides **access specifiers** to allow the library creator to say what is **available to the client programmer** and what is not. The levels of **access control** from “most access” to “least access” are:
- public
- protected
- package access
> which has no keyword.
- private

###Package
---
A **package** contains a group of classes, organized together **under a single namespace**. The **import keyword** provide a mechanism to **manage namespaces**.
> A method f() inside a class A will **not clash** with an f() that has the same signature in class B.

When you create a source-code file for Java, it’s commonly called a **compilation unit**.
> Each compilation unit must have a name ending in .java, and inside the compilation unit there can be a public class that must have the **same name as the file**.
> 
> There can be **only one public class** in each compilation unit.
> 
> If there are **additional classes** in that compilation unit, they are **hidden** from the world outside that package because they’re not public, and they comprise “support” classes for the main public class.
> 
> When you compile a .java file, you get an **output file for each class** in the .java file.

A working program is **a bunch of .class files**, which can be packaged and compressed into a JAR file. The Java interpreter is responsible for **finding**, **loading**, and **interpreting** these files.

A **library** is a group of these class files. Each source file usually has a public class and any number of non-public classes, so there’s **one public component** for each source file. If you want to say that all these components **belong together**, that’s where the **package keyword** comes in.
```
package access;
```
> You’re stating that this compilation unit is **part of** a library named access.
> 
> Anyone who wants to use the public class name must either **fully specify** the name or use the **import keyword** in combination with access.

Collecting the package files into a **single subdirectory** solves two other problems: 
> creating **unique package names**.
> 
> **finding those classes** that might be buried in a directory structure someplace. 

This is accomplished by **encoding the path **of the location of the .class file **into the name** of the package.

To **resolve** the package name into a directory, the **Java interpreter** proceeds as follows:
> First, it **finds** the environment variable **CLASSPATH**.
> > CLASSPATH contains one or more directories that are **used as roots** in a **search for .class files**.
> 
> > Starting at that root, the interpreter will take the package name and **replace each dot with a slash** to generate a path name off of the CLASSPATH root. 
> 
> This is then **concatenated to the various entries** in the CLASSPATH.
> > That’s where it looks for the .class file with the name corresponding to the class you’re trying to create.

When the compiler **encounters the import** statement, it begins **searching at the directories** specified by CLASSPATH, looking for the specified subdirectory, then seeking the compiled files of the appropriate names.

If **two libraries** are imported via \* and they **include the same names**, this will cause a potential **collision**.
```
import net.mindview.simple.*;
import java.util.*;

// error
Vector v = new Vector();

net.mindview.simple.Vector v = new net.mindview.simple.Vector();
```
> The compiler can’t know which Vector class does this refer to, so the compiler complains and forces you to be explicit.

A very common use of **conditional compilation** is for debugging code. The **debugging features** are enabled during development and disabled in the shipping product.
> You can accomplish this by **changing the package** that’s imported in order to change the code used in your program from the debug version to the production version.

The package must live in the directory indicated　by its name, which must be a directory that is **searchable** starting from the CLASSPATH.
> The compiled code is often placed in a **different directory** than source code, but the path to the compiled code must still be **found by the JVM using the CLASSPATH**.

###Java access specifiers
---
The Java **access specifiers** public, protected, and private are placed in front of each definition for **each member** in your class, whether it’s **a field or a method**. 
> If you don’t provide an access specifier, it means “**package access**.” 

**Package access** (and sometimes “friendly”) means that all the other classes **in the current package** have access to that member, but to all the classes **outside of this package**, the member **appears to be private**. 
> Since a compilation unit — a file — can belong only to a single package, all the classes within a single compilation unit are **automatically available** to each other via package access.

Package access allows you to **group related classes** together in a package so that they can easily interact with each other.
> When you put classes together in a package, thus granting **mutual access** to their package-access members, you “own” the code in that package.

The class **controls the code** that has access to its members. The only way to **grant access to a member** is to:
> Make the member **public**. Then everybody, everywhere, can access it.
> 
> Give the **member package access** by leaving off any access specifier, and put the other classes in the **same package**. Then the other classes in that package can access the member.
> 
> An inherited class can access a **protected member** as well as a public member.
> 
> Provide **“accessor/mutator” methods** (also known as “get/set” methods) that read and change the value. 

The **default package access** often provides an adequate amount of hiding; remember, a packageaccess member is **inaccessible** to the **client programmer** using the class. 

###Interface and implementation
---
Wrapping data and methods within classes **in combination with** implementation hiding is often called **encapsulation**.

Access control **puts boundaries within a data type** for two important reasons.
> establish what the client programmers **can and can’t use**.
> 
> which is to **separate the interface** from the implementation.  

###Class access
---
If you want a class to be **available to a client programmer**, you use the **public keyword** on the entire class definition.
> There can be **only one public class** per compilation unit (file). The idea is that each compilation unit has a **single public interface** represented by that public class.
> > It can have as many supporting package-access classes as you want.
> 
> The name of the public class must **exactly match the name** of the file containing the compilation unit, including capitalization.
> 
> It is possible, though not typical, to have a compilation unit **with no public class** at all.
> > In this case, you can name the file whatever you like.

You have only two choices for **class access**:
- **package access**
- **public**

If you don’t want anyone else to have access to that class, you can **make all the constructors private**.
