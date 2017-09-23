##Strings
###Immutable Strings
---
Objects of the **String** class are **immutable**.
> Every method in the class that appears to modify a String actually **creates and returns a brand new String object** containing the modification. 

###Overloading '+' vs. StringBuilder
---
Immutability can have **efficiency issues**. A case in point is the **operator +** that has been **overloaded** for String objects.
> The '+' operator allows you to **concatenate Strings**.
> > The **compiler** creates a **StringBuilder object** and **calls append()**, finally it **calls toString()** to produce the result.

Creating an **explicit StringBuilder** allows you to **preallocate** its size if you have extra information about how big it might need to be, so that it doesn’t need to constantly reallocate the buffer.
> the mehtods that usually use are **append()** and **toString()**.

###Unintended recursion
---
Because the Java standard containers are **ultimately inherited from Object**, they contain a **toString()** method.
> toString has been **overridden** so that containers can **produce a String representation of themselves**, including the objects they hold.

If you want to **print the address** of your class by **using toStirng()**, it **seems to make sense** to simply refer to this:
```
public String toString() { return "address: " + this + "\n"; }
```
> What’s happening is **automatic type conversion** for Strings.
> > The **compiler** sees a String followed by a '+' and something that’s not a String, so it tries to **convert this to a String**.
> 
> > It does this **conversion** by **calling toString()**, which produces a **recursive call**.
> 
> And you’ll get a very long sequence of **exceptions**. 

If you really do want to **print the address of the object**, the solution is to call the **Object toString() method**, which does just that.
> So instead of saying this, you’d say **super.toString()**.

###Operations on Strings
---
| Method | Use |
| --- | --- |
| Constructor | Creating String objects. |
| length() | Number of characters in the String. |
| charAt() | The char at a location in the String. |
| getChars(), getBytes() | Copy chars or bytes into an external array. |
| toCharArray() | Produces a char[] containing the characters in the String. |
| equals(), equalsIgnoreCase() | An equality check on the contents of the two Strings. |
| compareTo() | Result is negative, zero, or positive depending on the lexicographical ordering of the String and the argument. |
| contains() | Result is true if the argument is contained in the String. |
| indexOf(), lastIndexOf() | Returns -1 if the argument is not found within this String. |
| concat() | Returns a new String object containing the original String’s characters followed by the characters in the argument. |
| trim() | Returns a new String object with the whitespace removed from each end. |
| valueOf() | Returns a String containing a character representation of the argument. |
| intern() | Produces one and only one String reference per unique character sequence. |
> Every String method carefully **returns a new String object** when it’s necessary to change the contents.
> 
> If the contents don’t need changing, the method will just **return a reference to** the original String.
> > This saves storage and overhead.

###Formatting output
---
The **format() method** is available to PrintStream or PrintWriter objects, which includes System.out.
```
System.out.format("Row 1: [%d %f]\n", x, y);
System.out.printf("Row 1: [%d %f]\n", x, y);
```
> The printf() just calls format().

All of Java’s new **formatting functionality** is handled by the **java.util.Formatter** class.
> You can think of Formatter **as a translator** that **converts** your format string and data into the desired result.
> 
> When you create a Formatter object, you tell it where you want this **result to go** by passing that information to the **constructor**.

To control **spacing and alignment** when data is inserted, you need more elaborate format specifiers.
```
%[argument_index$][flags][width][.precision]conversion

f.format("%-15.15s %5d %10.2f\n", name, qty, price);
```
> You control the **minimum size** of a field by specifying a **width**.
> > The Formatter guarantees that a field is at least a certain number of characters wide by padding it with spaces if necessary.
> 
> > By default, the data is **right justified**, but this can be overridden by including a '-' in the flags section.
> 
> The opposite of width is **precision**, which is used to **specify a maximum**.
> > Precision has a different meaning for different types.
> 
> > For Strings, the precision specifies the **maximum number of characters** from the String to print.
> 
> > For floating point numbers, precision specifies the **number of decimal places to display** (the default is 6), rounding if there are too many or adding trailing zeroes if there are too few.

| Conversion Characters | Description |
| --- | --- |
| d | Integral (as decimal) |
| c | Unicode character |
| b | Boolean value |
| s | String |
| f | Floating point (as decimal) |
| e | Floating point (in scientific notation) |
| x | Integral (as hex) |
| h | Hash code (as hex) |
| % | Literal "%" |

**String.format()** is a **static method** which takes all the same arguments as Formatter’s format() but **returns a String**. 
> It likes  C’s sprintf().

###Regular expressions
---
The **regular expression** tools that’s built into **String** as following:
- **matches(String regex)**
> tells whether or not this string matches the given regular expression.
- **split(String regex)**
> splits this string around matches of the given regular expression.
- **split(String regex, int limit)** 
> Splits this string around matches of the given regular expression.
- **replaceAll(String regex, String replacement)**
> replaces each substring of this string that matches the given regular expression with the given replacement.
- **replaceFirst(String regex, String replacement)**
> replaces the first substring of this string that matches the given regular expression with the given replacement.

A complete list of constructs for **building regular expressions** is in **java.util.regex.Pattern**.

| Characters | Description |
| --- | --- |
| B | The specific character B |
| \xhh | Character with hex value oxhh |
| \uhhhh | The Unicode character with hex representation 0xhhhh |
| \t | Tab |
| \n | Newline |
| \r | Carriage return |
| \f | Form feed |
| \e | Escape |

The power of regular expressions begins to appear when you are defining character classes. Here are some typical ways to **create character classes**, and some predefined classes:

| Characters Classes | Description |
| --- | --- |
| . | Any character |
| [abc] | Any of the characters a, b, or c |
| [^abc] | Any character except a, b, and c (negation) |
| [a-zA-Z] | Any character a through z or A through Z (range) |
| [abc[hij]] | Any of a,b,c,h,I,j (union) |
| [a-z&&[hij]] | Either h, i, or j (intersection) |
| \s | A whitespace character (space, tab, newline, form feed, carriage return) |
| \S | A non-whitespace character ([^\s]) |
| \d | A numeric digit [0-9] |
| \D | A non-digit [^o-9] |
| \w | A word character [a-zA-Z_0-9] |
| \W | A non-word character [^\w] |

| Boundary Matchers | Description |
| --- | --- |
| ^ | Beginning of a line |
| $ | End of a line |
| \b | Word boundary |
| \B | Non-word boundary |
| \G | End of the previous match |

A **quantifier** describes the way that a pattern **absorbs input text**:
- **Greedy**
> Finds as many possible matches for the pattern as possible. 
- **Reluctant**
> Matches the minimum number of characters necessary to satisfy the pattern. 
- **Possessive**
> As a regular expression is applied to a string, it generates many states so that it can backtrack if the match fails.
> 
> Possessive quantifiers do not keep those intermediate states, and thus prevent backtracking.
> > They can be used to prevent a regular expression from running away and also to make it execute more efficiently.

| Greedy | Reluctant | Possessive | Matches |
| --- | --- | --- | --- |
| X? | X?? | X?+ | X, one or none |
| X* | X*? | x*+ | X, zero or more |
| X+  |X+?  |X++ | X, one or more |
| X{n}  |X{n}? | X{n}+ | X, exactly n times |
| X{n,} | X{n,}? | X{n,}+ | X, at least n times |
| X{n,m} | X{n,m}? | X{n,m}+ | X, at least n but not more than m times |
> The **expression 'X'** will often need to be **surrounded in parentheses** for it to work the way you desire.

**CharSequence interface** establishes a generalized definition of a character sequence abstracted from the CharBuffer, String, StringBuffer, or StringBuilder classes:
```
interface CharSequence {
	charAt(int i);
	length();
	subSequence(int start,| int end);
	toString();
}
```
> Many regular expression operations **take CharSequence arguments**.

You usually **compile regular expression objects** by using the **static Pattern.compile() method** that from java.util.regex, and **produces a Pattern object** based on its String argument.
> A **Pattern object** represents the **compiled version** of a regular expression.
> 
> You use the Pattern by **calling the matcher() method**, passing the string that you want to search.
> > The matcher() method **produces a Matcher object**, which has a set of operations to choose from.

A **Matcher object** is generated by calling **Pattern.matcher()** with the input string as an argument.
> This object is then used to **access the results**, using methods to evaluate the success or failure of different types of matches.
> > The **matches() method** is successful if the pattern **matches the entire input string**.
> 
> > The **lookingAt()** is successful if the input string, starting at the beginning, is a **match to the pattern**.
> 
> > **Matcher.find()** can be used to **discover multiple pattern matches** in the CharSequence to which it is applied.

**Groups** are regular expressions **set off by parentheses** that can be called up later with their group number.
```
A(B(C))D
```
> Group 0 is ABCD, group 1 is BC, and group 2 is C.

The Matcher object has methods to give you information about groups:
- **public int groupCount()**
> returns the number of groups in this matcher’s pattern. Group 0 is not included in this count.
- **public String group()**
> returns group 0 from the previous match operation.
- **public String group(int i)**
> returns the given group number during the previous match operation. 
- **public int start(int group)**
> returns the start index of the group found in the previous match operation.
> 
> **start()** returns the start index.
- **public int end(int group)**
> returns the index of the last character, plus one, of the group found in the previous match operation.
> 
> **end()** returns the index of the last character matched, plus one. 

An alternative **compile() method** accepts **flags** that affect matching behavior:
```
Pattern Pattern.compile(String regex, int flag)
```
- flag is drawn from among the following Pattern class **constants**:
> **Pattern.CASE_INSENSITIVE**
> > only characters in the ASCII character set are being matched.
> 
> **Pattern.MULTILINE**
> > only match at the beginning and the end of the entire input string.
> 
> **Pattern.COMMENTS**
> > whitespace is ignored, and embedded comments starting with # are ignored until the end of a line.

Matcher's **split() method** divides an input string into **an array of String objects**, delimited by the regular expression.
```
String[] split(CharSequence input)
String[] split(CharSequence input, int limit)
```

Regular expressions are especially useful to **replace text**, here are the available methods from String:
- **replaceFirst(String replacement)**
> replaces the first matching part of the input string with replacement.
- **replaceAll(String replacement)**
> replaces every matching part of the input string with replacement.
- **appendReplacement(StringBuffer sbuf, String replacement)**
> performs step-by-step replacements into sbuf.
> 
> it allows you to call methods and perform other processing in order to produce replacement.
- **appendTail(StringBuffer sbuf, String replacement)**
> invoked after one or more invocations of the appendReplacement() method in order to copy the remainder of the input string.

An existing Matcher object can be **applied to a new character sequence** using the **reset() methods**.
> reset() without any arguments **sets** the Matcher to the **beginning of the current sequence**.

###Scanning input
---
The usual solution to **read data** from a human-readable file or from standard input is as following:
> read in a line of text.
> 
> tokenize it.
> 
> **use the various parse methods** of Integer, Double, etc., to parse the data.

The **Scanner class** relieves much of the burden of scanning input.
> With Scanner, the input, tokenizing, and parsing are all ensconced in various different kinds of **"next" methods**.
> > All of the "next" methods **block**, meaning they will return only after a **complete data token** is **available** for input.
> 
> One of the assumptions made by the Scanner is that an **IOException signals the end of input**, and so these are swallowed by the Scanner.

By default, a Scanner **splits input tokens along whitespace**, but you can also specify your **own delimiter pattern** in the form of a regular expression by using **useDelimiter() method**.
> When scanning with regular expressions, the pattern is **matched against the next input token only**, so if your pattern contains a delimiter it will never be matched.

Before regular expressions or the Scanner class, the way to split a string into parts was to "tokenize" it with **StringTokenizer**.
> With regular expressions or Scanner objects, you can also **split a string into parts** using more complex patterns — something that’s difficult with StringTokenizer.
> 
> It seems safe to say that the StringTokenizer is **obsolete**.
