##用 Optional 取代 null
###如何为缺失的值建模
为**避免 NullPointerException 异常**，可以在需要的地方**添加 null 的检查**。
> 用 if 语句对 null 的检查会**增加代码缩进的层数**，这种方式**不具备扩展性**，同时还**牺牲了代码的可读性**。
> 
> 若为了避免深层递归的 if 语句块，而在每次遇到 null 变量时都返回一个值，这会使得代码中**有多个退出点**，使得代码的**维护异常艰难**。

在 Java 程序开发中使用 null 会带来理论和实际操作上的**种种问题**：
- null 是**错误之源**
> NullPointerException 是 Java 程序开发中最典型的异常。
- null 会**使代码膨胀**
> null 让代码充斥着深度嵌套的 null 检查，使得可读性很糟。
- null **自身毫无意义**
> null 自身没有任何的语义，它代表的是在静态类型语言中以一种错误的方式对缺失变量值的建模。
- null **破坏了 Java 的哲学**
> Java 一直试图避免让开发者意识到指针的存在，唯一的例外是： **null 指针**。
- null 在 Java 的**类型系统上开了一个口子**
> null **不属于任何类型**，这意味着它**可以被赋值给任意引用类型的变量**。
> > 这会导致问题，原因是当这个变量被传递到系统中的另一个部分后，用户将**无法获知这个 null 变量最初的赋值的类型**。

###Optional 类入门
Java 8 中引入了一个新的类 **java.util.Optional&lt;T>**，它只是**对类简单封装**。
> 变量不存在时，缺失的值会被**建模成一个“空”的 Optional 对象**，由方法 **Optional.empty() 返回**。
> > Optional.empty() 方法是一个**静态工厂方法**，它返回 Optional 类的**特定单一实例**。

使用 Optional 而不是 null 的一个**非常重要而又实际的语义区别**是：
> 声明变量时**使用的是 Optional&lt;Car> 类型**，而非 Car 类型，这表明了此处**发生变量缺失是允许的**。

在代码中始终如一地使用 Optional，能非常**清晰地界定出**变量值的缺失是**结构上的问题**，还是**算法上的缺陷**，抑或是**数据中的问题**。

###应用 Optional 的几种模式
Optional 对象有**多种创建方式**：
- **声明一个空的 Optional**
> 可以通过静态工厂方法 **Optional.empty**，创建一个空的 Optional 对象：
> > Optional&lt;Car> optCar = Optional.empty();
- **依据一个非空值创建 Optional**
> 可以使用静态工厂方法 **Optional.of**，依据一个非空值创建一个 Optional 对象：
> > Optional&lt;Car> optCar = Optional.of(car);
> 
> > 若 car 是一个 null，则会立即抛出一个 NullPointerException。
- **可接受 null 的 Optional**
> 使用静态工厂方法 **Optional.ofNullable** 创建一个允许 null 值的 Optional 对象：
> > Optional&lt;Car> optCar = Optional.ofNullable(car);
> 
> > 若 car 是 null，那么得到的 Optional 对象就是个空对象。

Optional 提供了一个 **map 方法**，用于**从对象中提取信息**。
```
Optional<Insurance> optInsurance = Optional.ofNullable(insurance);
Optional<String> name = optInsurance.map(Insurance::getName);
```
> map 操作会**将提供的函数应用于流的每个元素**，可以把 Optional 对象看成一种**特殊的集合数据**，它**至多包含一个元素**。
> > 若 Optional 包含一个值，那函数就将该值作为参数传递给 map，对该值进行转换。
> 
> > 若 Optional 为空，就什么也不做。

**Stream 和 Optional 的 map 方法**对比如下图所示：
![](https://i.imgur.com/OL25djw.png)

```
Optional<Person> optPerson = Optional.of(person);
Optional<String> name = optPerson.map(Person::getCar)
								 .map(Car::getInsurance)
								 .map(Insurance::getName);
```
> optPerson 是 Optional<Person> 类型的变量，getCar 返回的是一个 Optional&lt;Car> 类型的对象。
> > 即 map 操作返回的是一个 **Optional&lt;Optional&lt;Car>> 类型的对象**，这是一个嵌套式 optional 结构。
> 
> Optional&lt;Optional&lt;Car>> 对 getInsurance 的调用非法，因此无法通过编译。

**嵌套式 optional 结构**如下图所示：
![](https://i.imgur.com/PMaWDnk.png)
> 此时可使用 flatMap 方法**将流合并或者扁平化为一个单一的流**。

**Stream 和 Optional 的 flagMap 方法**对比如下图所示：
![](https://i.imgur.com/2X0B6P5.png)

对于 **Optional&lt;Person> 对象**，可以结合使用 map 和 flatMap 方法：
- 从 Person 中**解引用出 Car**
- 然后从 Car 中**解引用出 Insurance**
- 最后从 Insurance 对象中**解引用出包含 insurance 公司名称的字符串**

使用 Optional **解引用串接的 Person/Car/Insurance 的过程**如下图所示：
![](https://i.imgur.com/lZyonXb.png)
- 首先从以 Optional 封装的 Person 入手，对其**调用 flatMap(Person::getCar)**
> 第一步，某个 **Function** 作为**参数**，被传递给由 Optional 封装的 Person 对象，**对其进行转换**。
> > Function 的具体表现是**一个方法引用**，即**对** Person 对象的 **getCar 方法进行调用**。
> 
> > 由于该方法**返回一个 Optional&lt;Car> 类型**的对象， Optional 内的 Person 也被转换成了这种对象的实例，结果就是一个**两层的 Optional 对象**，最终它们会**被 flagMap 操作合并**。
> 
> 从纯理论的角度而言，可将这种合并操作简单地看成**把两个 Optional 对象结合在一起**，若其中有一个对象为空，就构成一个空的 Optional 对象。
- 第二步**将 Optional&lt;Car> 转换为 Optional&lt;Insurance>**。
- 第三步则会**将 Optional&lt;Insurance> 转化为 Optional&lt;String> 对象**。
> 由于 Insurance.getName() 方法的返回 String，此处**不再需要进行 flapMap 操作**。
- 截至目前为止，**返回的 Optional 可能是两种情况**：
> 如果调用链上的任何一个方法**返回一个空的 Optional**，那么**结果就为空**。
> 
> 否则**返回的值即期望的保险公司的名称**。
> > 可以使用 **orElse 方法**，当 Optional 的**值为空时**，它会为其**设定一个默认值**。

由于 Optional 类设计时并未特别考虑将其作为类的字段使用，因此它也**并未实现 Serializable 接口**。

Optional 类提供了多种方法**读取 Optional 实例中的变量值**：
- **get()**
> 若变量存在，它直接返回封装的变量值，否则就抛出一个 NoSuchElementException 异常。
> 
> 这种方式即便相对于嵌套式的 null 检查，也并未体现出多大的改进。
- **orElse(T other)**
> 允许用户在 Optional 对象不包含值时提供一个默认值。
- **orElseGet(Supplier&lt;? extends T> other)**
> orElse 方法的延迟调用版，Supplier 方法只有在 Optional 对象不含值时才执行调用。
- **orElseThrow(Supplier&lt;?? extends X> exceptionSupplier)**
> 与 get 类似，但使用 orElseThrow 可以定制希望抛出的异常类型。
- **ifPresent(Consumer<? super T>)**
> 能在变量值存在时执行一个作为参数传入的方法，否则就不进行任何操作。

假设要一个方法接受一个 Person 和一个 Car 对象，并以此为条件对外部提供的服务进行查询，通过一些复杂的业务逻辑，试图找到满足该组合的最便宜的保险公司。
> 此外还想要该方法的一个 null-安全的版本，它接受两个 Optional 对象作为参数，返回值是一个 Optional&lt;Insurance> 对象。
> > 若传入的任何一个参数值为空，它的返回值亦为空。

```
public Insurance findCheapestInsurance(Person person, Car car) {
	// 不同的保险公司提供的查询服务
	// 对比所有数据
	return cheapestCompany;
}

public Optional<Insurance> 
nullSafeFindCheapestInsurance(Optional<Person> person, Optional<Car> car) {
	if (person.isPresent() && car.isPresent()) {
		return Optional.of(findCheapestInsurance(person.get(), car.get()));
	} else {
		return Optional.empty();
	}
}
```
> Optional 类提供了一个 **isPresent 方法**：
> > 若 Optional 对象**包含值**，该方法就**返回 true**。
> 
> 这个方法具有明显的优势，从它的签名可以知道**无论是 person 还是 car**，它的值**都有可能为空**。
> > 出现这种情况时，方法的返回值不会包含任何值。

上述代码的优化版本如下，可以像**使用三元操作符**那样，无需任何条件判断的结构，以一行语句实现该方法。
```
public Optional<Insurance>
nullSafeFindCheapestInsurance(Optional<Person> person, Optional<Car> car) {
	return person.flatMap(p -> car.map(c -> findCheapestInsurance(p, c)));
}
```
> 首先对第一个 Optional 对象 person **调用 flatMap 方法**。
> > 若它是个**空值**，传递给它的 Lambda 表达式不会执行，这次调用会**直接返回一个空的 Optional 对象**。
> 
> > 反之，这次调用就会**将 person 对象作为函数 Function 的输入**，并按照与 flatMap 方法的约定**返回一个 Optional&lt;Insurance> 对象**。
> 
> 这个函数的函数体会**对第二个 Optional 对象 car 执行 map 操作**，若 car 为空，函数 Function 就返回一个空的 Optional 对象，整个 nullSafeFindCheapestInsuranc 方法的返回值也是一个空的 Optional 对象。
> 
> 最后，若 **person 和 car 对象都非空**，作为参数传递给 map 方法的 Lambda 表达式能够使用这两个值**安全地调用原始的 findCheapestInsurance 方法**，完成期望的操作。

若需要**调用某个对象的方法**，**查看**它的某些**属性**，可使用 Optional 对象的 **filter 方法**：
```
Optional<Insurance> optInsurance = ...;

optInsurance
.filter(insurance -> "CambridgeInsurance".equals(insurance.getName()))
.ifPresent(x -> System.out.println("ok"));
```
> filter 方法**接受一个谓词作为参数**，
> > 若 Optional 对象的**值存在**，且它**符合谓词的条件**，filter 方法就**返回其值**。
> 
> > 否则它**返回一个空的 Optional 对象**。

可以将 Optional 看成**最多包含一个元素的 Stream 对象**：
> 若 Optional 对象**为空**，则它**不做任何操作**。
> 
> 反之，它就对 Optional 对象中包含的值**施加谓词操作**。
> > 若该操作的**结果为 true**，它不做任何改变，**直接返回该 Optional 对象**。
> 
> > 否则就**将该值过滤掉**，将 Optional 的值置空。

**Optional 类的方法**：
- **empty**
> 返回一个**空的 Optional 实例**。
- **filter**
> 如果值存在并且满足提供的谓词，就返回包含该值的 Optional 对象；否则返回一个空的 Optional 对象
- **flatMap**
> 如果值存在，就对该值执行提供的 mapping 函数调用，返回一个 Optional 类型的值，否则就返回一个空的 Optional 对象
- **get**
> 如果该值存在，将该值用 Optional 封装返回，否则抛出一个 NoSuchElementException 异常
- **ifPresent**
> 如果值存在，就执行使用该值的方法调用，否则什么也不做
- **isPresent**
> 如果值存在就返回 true，否则返回 false
- **map**
> 如果值存在，就对该值执行提供的 mapping 函数调用
- **of**
> 将指定值用 Optional 封装之后返回，如果该值为 null，则抛出一个 NullPointerException 异常
- **ofNullable**
> 将指定值用 Optional 封装之后返回，如果该值为 null，则返回一个空的 Optional 对象
- **orElse**
> 如果有值则将其返回，否则返回一个默认值
- **orElseGet**
> 如果有值则将其返回，否则返回一个由指定的 Supplier 接口生成的值
- **orElseThrow**
> 如果有值则将其返回，否则抛出一个由指定的 Supplier 接口生成的异常

###使用 Optional 的实战示例
假设有一个 Map&lt;String, Object> 方法，**访问由 key 索引的值**时，若 map 中没有与 key 关联的值，该次调用就会返回一个 null。
```
Object value = map.get("key");
```

使用 Optional **封装 map 的返回值**，可以对这段代码进行优化。
```
Optional<Object> value = Optional.ofNullable(map.get("key"));
```
> 每当希望**安全地对潜在为 null 的对象进行转换**，将其**替换为 Optional 对象**时，都可以考虑使用这种方法。

由于某种原因，**函数无法返回某个值**，这时除了返回 null，Java API 比较常见的替代做法是抛出一个异常。
> 例如使用**静态方法 Integer.parseInt(String)**，将 String 转换为 int。
> > 若 String **无法解析到对应的整型**，该方法就**抛出一个 NumberFormatException**。
> 
> > 最后的效果是，发生 String **无法转换为 int 时**，代码**发出一个遭遇非法参数的信号**。
> 
> > 唯一的不同是，此时**需使用 try/catch 语句**，而不是使用 if 条件判断来控制一个变量的值是否非空。

可使用空的 Optional 对象，**对遭遇无法转换的 String 时返回的非法值进行建模**，这时**期望 parseInt 的返回值是一个 optional**。
```
public static Optional<Integer> stringToInt(String s) {
	try {
		return Optional.of(Integer.parseInt(s));
	} catch (NumberFormatException e) {
		return Optional.empty();
	}
}
```
> 若 String **能转换为对应的 Integer**，则将其**封装在 Optioal 对象中返回**。
> 
> 否则**返回一个空的 Optional 对象**。

与 Stream 对象一样，Optional 也提供了类似的基础类型——OptionalInt、 OptionalLong 以及 OptionalDouble。
> 若 **Stream 对象包含了大量元素**，**出于性能的考量**，**使用基础类型是不错的选择**。
> 
> 但对 Optional 对象而言，这个理由并不成立，因为 Optional 对象最多只包含一个值。
> > 因此并**不推荐使用基础类型的 Optional**，因为它们**不支持 map、flatMap 以及 filter 方法**。
