##Lambda 表达式
###Lambda 及其使用
**Lambda 表达式**是一种表示**可传递的匿名函数**的方式：它**没有名称**，但它有参数列表、函数主体、返回类型，可能还有一个可以抛出的异常列表。
- **匿名**
> 没有有一个明确的名称。
- **函数**
> Lambda 函数**不属于某个特定的类**，但和方法一样， Lambda 有参数列表、函数主体、返回类型，还可能有可以抛出的异常列表。
- **传递**
> Lambda 表达式可以作为参数**传递给方法**或**存储在变量**中。
- **简洁**
> 无需像匿名类那样写很多模板代码。

Lambda 的**基本语法**如下：
```
(parameters) -> expression

(parameters) -> { statements; }
```
> 可以只为一个**表达式**，或是**包含多条语句**。

Lambda 表达式的**定义**如下：
```
(Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight());
```
- **参数列表**
> 采用了 Comparator 中 compare 方法的参数，两个 Apple。
- **箭头**
> -> 把参数列表与 Lambda 主体分隔开。
- **Lambda 主体**
> 比较两个 Apple 的重量。表达式就是 Lambda 的返回值。

Lambda 表达式仅可用于**上下文是函数式接口**的情况。 函数式接口就是**只定义一个抽象方法**的接口。
> 接口可以拥有默认方法，但只要接口只定义了一个抽象方法，它就仍然是一个函数式接口。

Lambda 表达式允许你直接**以内联的形式**为函数式接口的抽象方法**提供实现**，并把整个表达式作为函数式接口的实例。
> 你用匿名内部类也可以完成同样的事情，只不过比较笨拙：需要**提供一个实现**，然后**再直接内联**将它实例化。

```
Runnable r1 = () -> System.out.println("Hello World 1");

Runnable r2 = new Runnable(){
	public void run(){
		System.out.println("Hello World 2");
	}
};

public static void process(Runnable r){
	r.run();
}

process(r1);
process(r2);

process(() -> System.out.println("Hello World 3"));
```
> r1 使用 Lambda 实现，r2 使用匿名类实现。
> 
> 也可在 process() 中直接传递 Lambda 表达式。

函数式接口的**抽象方法的签名**基本上就是 **Lambda 表达式的签名**，这种抽象方法叫作**函数描述符**。
> Runnable 接口可以看作一个什么也不接受什么也不返回（void）的函数的签名，因为它唯一的 run 抽象方法什么也不接受，什么也不返回。
> 
> () -> void 代表了参数列表为空，且返回 void 的函数。

Lambda 表达式可以被赋给一个变量，或传递给一个接受函数式接口作为参数的方法。
> Lambda 表达式的签名要和函数式接口的抽象方法一样。

在 Java 8 中，函数式接口会带有 **@FunctionalInterface 注解**，这个注解用于表示该接口会**设计成一个函数式接口**。
> 如果你用 @FunctionalInterface 定义了一个接口，而它却不是函数式接口，编译器将返回一个提示原因的错误。

###环绕执行模式
**资源处理**（例如处理文件或数据库）时一个常见的模式就是**打开**一个资源，做一些**处理**，然后**关闭**资源。
> 这个设置和清理阶段总是很类似，并且会围绕着执行处理的那些重要代码。这就是所谓的**环绕执行**（execute around）**模式**。

![](http://i.imgur.com/hT2WIbD.png)
> 任务 A 和任务 B 周围都环绕着进行准备/清理的同一段冗余代码。

```
public static String processFile() throws IOException {
	try (BufferedReader br =
		new BufferedReader(new FileReader("data.txt"))) {
			return br.readLine();
	}
}
```
> 只能读取文件的第一行。若想要返回头两行，或是其他操作，则需要一种方法把行为传递给 processFile，以便它可以利用 BufferedReader 执行不同的行为。

**行为参数化**，使用函数式接口来传递行为。
```
@FunctionalInterface
public interface BufferedReaderProcessor {
	String process(BufferedReader b) throws IOException;
}

public static String processFile(BufferedReaderProcessor p)
throws IOException {
	//…
}
```
> 定义一个函数式接口。
> > 此时需要一个接收 BufferedReader 并返回 String 的 Lambda。
> 
> > 即 Lambda 表达式 BufferedReader -> String。 

**执行一个行为**。
```
public static String processFile(BufferedReaderProcessor p)
throws IOException {
	try (BufferedReader br =
		new BufferedReader(new FileReader("data.txt"))) {
			return p.process(br);
	}
}
```

**传递 Lambda**。
```
String oneLine = processFile((BufferedReader br) -> br.readLine());

String twoLines =
	processFile((BufferedReader br) -> br.readLine() + br.readLine());
```
> Lambda 表达式允许你**直接内联**，为函数式接口的抽象方法提供实现，并且将整个表达式作为函数式接口的一个实例。
> 
> 通过传递不同的 Lambda 重用 processFile 方法，即可以不同的方式处理文件。

###使用函数接口
函数式接口定义且**只定义了一个抽象方法**，它的**抽象方法的签名**称为**函数描述符**。
> 抽象方法的签名可以**描述 Lambda 表达式的签名**。

**Predicate&lt;T> 接口**定义了一个 **test 方法**，它**接受泛型 T 对象**，并**返回一个 boolean**。
> 若需要表示一个涉及类型 T 的布尔表达式，可以使用该接口。
> 
> T -> boolean 

**Consumer&lt;T> 接口**定义了一个 **accept 方法**，它**接受泛型 T 的对象**，**没有返回（void）**。
> 若需要访问类型 T 的对象，并对其执行某些操作，可以使用该接口。
> 
> T -> void

**Function&lt;T, R> 接口**定义了一个 **apply 方法**，它**接受一个泛型 T 的对象**，并**返回一个泛型 R 的对象**。
> 若需要定义一个 Lambda，将输入对象的信息映射到输出，就可以使用该接口。
> 
> T -> R

Java 类型**要么是引用类型**（比如 Byte、 Integer），**要么是原始类型**（比如 byte、 int）。但**泛型只能绑定到引用类型**。
> 在 Java 里有一个将原始类型转换为对应的引用类型的**装箱**（boxing）**机制**，以及将引用类型转换为对应的原始类型的**拆箱**（unboxing）**机制**。

Java 中有一个**自动装箱**（autoboxing）**机制**来帮助开发者自动完成装箱和拆箱操作。 
> 装箱后的值本质上就是把原始类型包裹起来，并**保存在堆里**。因此，装箱后的值需要**更多的内存**，并需要**额外的内存搜索**来获取被包裹的原始值。这在性能方面会造成影响。
> 
> Java 8 为函数式接口带来了一个**专门的版本**，以便在输入和输出都是原始类型时**避免自动装箱**的操作。

一般来说，针对**专门的输入参数类型**的函数式接口的名称都要加上**对应的原始类型前缀**。
> 例如 DoublePredicate、 IntConsumer、 LongBinaryOperator、 IntFunction 等。

**Lambdas 及函数式接口**的使用示例

| 使用案例 | Lambda 的例子 | 对应的函数式接口 |
| --- | --- | --- |
| 布尔表达式 | (List&lt;String> list) -> list.isEmpty() | Predicate&lt;List&lt;String>> |
| 创建对象 | () -> new Apple(10) | Supplier&lt;Apple> |
| 消费一个对象 | (Apple a) -> System.out.println(a.getWeight()) | Consumer&lt;Apple> |
| 从一个对象中选择/提取 | (String s) -> s.length() | Function&lt;String, Integer> 或 ToIntFunction&lt;String> |
| 合并两个值 | (int a, int b) -> a * b | IntBinaryOperator |
| 比较两个对象 | (Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight()) | Comparator&lt;Apple> 或 BiFunction&lt;Apple, Apple, Integer> 或 ToIntBiFunction&lt;Apple, Apple> |

任何函数式接口都**不允许抛出受检异常**（checked exception）。若需要 Lambda 表达式抛出异常，有两种办法：
- 定义一个自己的函数式接口，并**声明受检异常**。
- 把 Lambda 包含在一个 **try/catch 块**中。

###类型检查、推断以及限制
Lambda 表达式本身并不包含它在实现哪个函数式接口的信息，它的类型是**从使用 Lambda 的上下文推断出来**的。
> 上下文（接受它传递的方法的参数，或接受它的值的局部变量）中 **Lambda 表达式需要的类型**称为**目标类型**。

```
filter(List<Apple> inventory, Predicate<Apple> p);

List<Apple> heavierThan150g =
	filter(inventory, (Apple a) -> a.getWeight() > 150);
```
> Lambda 的类型检查过程如下所示：
> > 首先，找出 filter 方法的声明。
> 
> > 第二，根据 filter 的声明判断目标类型是 Predicate&lt;Apple>。
> 
> > 第三，Predicate&lt;Apple> 是一个函数式接口，定义了一个 test 抽象方法。
> 
> > 第四，test 方法描述了一个函数描述符，它接受一个 Apple，并返回一个 boolean。
> 
> > 第五，函数描述符 Apple -> boolean 匹配 Lambda 的签名，类型检查正确。
> 
> 若 Lambda 表达式抛出一个异常，那么抽象方法所声明的 throws 语句也必须与之匹配。

通过**目标类型**，同一个 Lambda 表达式可以**与不同的函数式接口联系**起来，只要它们的抽象方法**签名能够兼容**。
> 同一个 Lambda 可用于多个不同的函数式接口。

Java 7 中引入了**菱形运算符**（<>）利用泛型推断**从上下文推断类型**的思想。一个类实例表达式可以出现在**两个或更多不同的上下文**中，并会像下面这样推断出适当的类型参数：
```
List<String> listOfStrings = new ArrayList<>();
List<Integer> listOfIntegers = new ArrayList<>();
```

若一个 **Lambda 的主体**是一个**语句表达式**，那它和一个**返回 void 的函数描述符兼容**（当然需要参数列表也兼容）。
```
// Predicate 返回一个 boolean
Predicate<String> p = s -> list.add(s);

// Consumer 返回一个 void
Consumer<String> b = s -> list.add(s);
```

Lambda 表达式可以从赋值的上下文、方法调用的上下文（参数和返回值），以及类型转换的上下文中获得目标类型。
> 目标类型除了用来检查一个 Lambda 是否可以用于某个特定的上下文，还可用来推断 Lambda 参数的类型。

Java 编译器会**从上下文（目标类型）推断出**用什么函数式接口来配合 Lambda 表达式，这意味着它也可以推断出适合 Lambda 的签名，因为函数描述符可以通过目标类型来得到。
> 编译器可以了解 Lambda 表达式的参数类型，这样就可以在 Lambda 语法中**省去标注参数类型**。

Lambda 表达式除了使用其主体里面的参数，也可像匿名类一样**使用自由变量**。
> 这些自由变量被称作**捕获 Lambda**。 

```
int portNumber = 1337;
Runnable r = () -> System.out.println(portNumber);
```
> Lambda 捕获了 portNumber 变量。

Lambda 可以没有限制地捕获（即在其主体中引用）实例变量和静态变量。但**局部变量**必须**显式声明为 final**，或事实上是final。
> Lambda 表达式只能**捕获**指派给它们的**局部变量一次**。

对局部变量的**限制**基于下述原因：
> 第一，实例变量和局部变量的**实现不同**。实例变量都**存储在堆中**，而局部变量则**保存在栈上**。
> > 若 Lambda 可以直接访问局部变量，且 Lambda 在一个线程中使用，则使用 Lambda 的线程可能会在分配该变量的线程将这个变量收回之后去访问该变量。
> 
> > Java 在访问自由局部变量时，实际上是在**访问它的副本**，而非其原始变量。若局部变量仅赋值一次，那就没有区别了。
> 
> 第二，本限制**不鼓励使用改变外部变量的**典型命令式编程模式。
> > 这种模式会阻碍很容易做到的并行处理。

**闭包**（closure）是一个**函数的实例**，且它可以**无限制地访问**那个函数的**非本地变量**。
> 闭包可以**作为参数传递**给另一个函数，也可以**访问和修改其作用域之外的变量**。

Java 8 的 Lambda 和匿名类可以做类似于闭包的事情：它们可以作为参数传递给方法，并且可以访问其作用域之外的变量。
> 但它们**不能修改定义 Lambda 的方法的局部变量**的内容。这些变量必须是隐式 final 的。
> > 可以认为 Lambda 是**对值封闭**，而**不是对变量封闭**。
> 
> 这种限制存在的原因在于局部变量保存在栈上，并且隐式表示它们**仅限于其所在线程**。
> > 若允许捕获可改变的局部变量，就会引发造成线程不安全的可能性。
> 
> > 实例变量可以，因为它们保存在堆中，而堆是在线程之间共享的。

###方法引用
**方法引用**使你可以**重复使用现有的方法定义**，并像 Lambda 一样传递它们。
```
inventory.sort((Apple a1, Apple a2)-> a1.getWeight().compareTo(a2.getWeight()));

inventory.sort(comparing(Apple::getWeight));
```
> 使用方法引用和 java.util.Comparator.comparing。
> 
> 目标引用放在分隔符 :: 前，方法的名称放在后面。
> > 不需要括号，因为并**没有实际调用**这个方法。 

方法引用可以被看作**仅仅调用特定方法的 Lambda 的**一种快捷写法。
> 它的基本思想是，若一个 Lambda 代表的只是“直接调用这个方法”，那**最好使用名称调用它**，而不是去描述如何调用它。
> 
> 事实上，方法引用就是让你**根据已有的方法实现来创建 Lambda**。
> 
> 方法引用可看作是针对**涉及单一方法**的 Lambda 的**语法糖**。

方法引用主要有三类：
- **指向静态方法的方法引用**
> 例如 Integer 的 parseInt 方法，写作 Integer::parseInt。
- ** 指向任意类型实例方法的方法引用**
> 引用一个对象的方法，这个对象本身是 Lambda 的一个参数。
> 
> 例如 String 的 length 方法，写作 String::length。
- **指向现有对象的实例方法的方法引用**
> 调用一个已经存在的外部对象中的方法。
> 
> 假设一个局部变量 expensiveTransaction 用于存放 Transaction 类型的对象，它支持实例方法 getValue。
> 
> 那么可以写作 expensiveTransaction::getValue。

![](http://i.imgur.com/SxYoQK7.png)
> 编译器会进行一种**与 Lambda 表达式类似的类型检查**过程，来确定对于给定的函数式接口，这个方法引用是否有效：方法引用的签名必须和上下文类型匹配。

对于一个现有构造函数，可以利用它的**名称和关键字 new 来创建**它的一个引用：
```
ClassName::new。
```
> 其功能与指向静态方法的引用类似。

若构造函数**没有参数**，即其形式为 **void -> void**，则它适合 **Supplier 的签名**。
```
Supplier<Apple> c1 = Apple::new;
Apple a1 = c1.get();
```
> 构造函数引用指向默认的 Apple() 构造函数。
> 
> 调用 Supplier 的 get 方法将产生一个新的 Apple。

若构造函数**有一个参数**，即其形式为 **T -> void**，则它适合 **Function 的签名**。
```
Function<Integer, Apple> c2 = Apple::new;
Apple a2 = c2.apply(110);
```
> 指向 Apple(Integer weight) 的构造函数引用。
> 
> 调用该 Function 函数的 apply 方法，根据要求的重量，产生一个 Apple。

若构造函数**有两个参数**，即其形式为 **(T, R) -> void**，则它适合 **BiFunction 的签名**。
```
BiFunction<String, Integer, Apple> c3 = Apple::new;
Apple c3 = c3.apply("green", 110);
```
> 指向 Apple(String color, Integer weight) 的构造函数引用。
> 
> 调用该 BiFunction 函数的 apply 方法，根据要求的颜色和重量，产生一个新的 Apple 对象。

###Lambda 表达式的复合
用于传递 Lambda 表达式的 Comparator、Function 和 Predicate 函数式接口**提供了进行复合的方法**，通过复合可将多个简单的 Lambda 复合成复杂的表达式。
> 可以让两个谓词之间做一个 or 操作，组合成一个更大的谓词。
> 
> 可以让一个函数的结果成为另一个函数的输入。

####比较器复合
Comparator 接口的 **reversed() 默认方法**可以对给定的比较器进行逆序。
```
Comparator<Apple> c = Comparator.comparing(Apple::getWeight);

inventory.sort(comparing(Apple::getWeight).reversed());
```
> comparing() 辅助静态方法接受一个 Function 来提取 Comparable 键值，并生成一个 Comparator 对象。
> 
> reversed() 方法对给定的比较器逆序。
> > 按重量递减排序。

Comparator 接口的 **thenComparing() 默认方法**接收一个函数作为参数，若两个对象用第一个 Comparator 比较之后结果一样的，就提供第二个 Comparator。
```
inventory.sort(comparing(Apple::getWeight)
	.reversed()
	.thenComparing(Apple::getCountry));
```
> 按重量递减排序，两个苹果一样重时，进一步按国家排序。

####谓词复合
Predicate 接口包括 **negate、 and 和 or 三个默认方法**，and 和 or 方法是按照在表达式链中的位置，**从左向右确定优先级**的。
```
Predicate<Apple> notRedApple = redApple.negate();

Predicate<Apple> redAndHeavyApple =
	redApple.and(a -> a.getWeight() > 150);

Predicate<Apple> redAndHeavyAppleOrGreen =
	redApple.and(a -> a.getWeight() > 150)
	.or(a -> "green".equals(a.getColor()));
```
> 返回一个不是红色的苹果。
> 
> 返回一个既是红色又比较重的苹果。
> 
> 返回一个指定重量的红苹果，或是绿苹果。

####函数复合
Function 接口提供了 **andThen 和 compose 两个默认方法**，它们都会**返回 Function 的一个实例**。
![](http://i.imgur.com/Mq6uTjh.png)

**andThen() 方法**会返回一个函数，它先对输入应用一个给定函数，再对输出应用另一个函数。
```
Function<Integer, Integer> f = x -> x + 1;
Function<Integer, Integer> g = x -> x * 2;

Function<Integer, Integer> h = f.andThen(g);
int result = h.apply(1);
```
> 函数 f 给数字加 1，函数 g 给数字乘 2。
> 
> 函数 f 和 g 可组合成函数 h，先给数字加 1，再给结果乘 2。

**compose() 方法**先把给定的函数用作 compose 的参数里面给的那个函数，然后再把函数本身用于结果。
```
Function<Integer, Integer> f = x -> x + 1;
Function<Integer, Integer> g = x -> x * 2;

Function<Integer, Integer> h = f.compose(g);
int result = h.apply(1);
```
> 先对数字乘 2，再给数字加 1。
