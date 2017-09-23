##新的日期和时间 API
###LocalDate、LocalTime、Instant、Duration 以及 Period
**LocalDate 类**的实例是一个**不可变对象**，表示**简单的日期**。
> 它也不附带任何与时区相关的信息。
> 
> 通过**静态工厂方法 of** 可以创建一个 LocalDate 实例。

使用**静态工厂方法 now** 可以从系统时钟中获取**当前的日期**：
```
LocalDate today = LocalDate.now();
```

还可以通过**传递一个 TemporalField 参数**给 **get 方法**获取到同样的信息。
```
LocalDate date = LocalDate.of(2017, 9, 21);

int year = date.get(ChronoField.YEAR);
int month = date.get(ChronoField.MONTH_OF_YEAR);
int day = date.get(ChronoField.DAY_OF_MONTH);
```
> 创建一个日期，并分别访问它的年、月、日。
> 
> **TemporalField 接口**定义了如何访问 temporal 对象某个字段的值。
> > **ChronoField 枚举**实现了该接口。

**LocalTime 类**可以表示一天中的**时间**。可以使用 **of** 重载的两个工厂方法创建 LocalTime 的实例。
- 第一个重载函数接收**小时和分钟**
- 第二个重载函数同时**还接收秒**

LocalDate 和 LocalTime 都可以通过**解析代表它们的字符串创建**。使用**静态方法 parse** 可以实现这一目的：
```
LocalDate date = LocalDate.parse("2014-03-18");
LocalTime time = LocalTime.parse("13:45:20");
```
> 创建一个日期对象和一个时间对象。
> 
> 可以向 parse 方法**传递一个 DateTimeFormatter**。
> > 该类的实例定义了**如何格式化一个日期或者时间对象**。

**LocalDateTime** 是 LocalDate 和 LocalTime 的合体，**表示日期和时间**，但不带有时区信息。
```
LocalDateTime dt1 = LocalDateTime.of(2014, Month.MARCH, 18, 13, 45, 20);
LocalDateTime dt2 = LocalDateTime.of(date, time);

LocalDateTime dt3 = date.atTime(13, 45, 20);
LocalDateTime dt4 = date.atTime(time);

LocalDateTime dt5 = time.atDate(date);
```
> 向 LocalDate 的 **atTime 方法**传递一个时间对象，可以创建一个 LocalDateTime 对象。
> 
> 向 LocalTime 的 **atDate 方法**传递一个日期对象，可以创建一个 LocalDateTime 对象。

可以使用 **toLocalDate** 或者 **toLocalTime 方法**，从 LocalDateTime 中**提取 LocalDate 或者 LocalTime 
组件**：
```
LocalDate date1 = dt1.toLocalDate();
LocalTime time1 = dt1.toLocalTime();
```

从计算机的角度来看，建模时间最自然的格式是表示**一个持续时间段上某个点的单一大整型数**。
> 这也是 **java.time.Instant** 类对时间建模的方式。
> > 它以 **Unix 元年**时间开始所**经历的秒数**进行计算。

可以通过向**静态工厂方法 ofEpochSecond** 传递一个代表秒数的值创建一个该类的实例。它还有个重载版本，接收**第二个以纳秒为单位的参数值**。
```
Instant.ofEpochSecond(3);

Instant.ofEpochSecond(4, -1_000_000_000);
```
> 3 秒。
> 
> 4 秒之前的 100 万纳秒（1 秒）。

Instant 类的**静态工厂方法 now** 可以获取**当前时刻的时间戳**。

LocalDate、LocalTime、LocalDateTime 以及 Instant 等类**均实现了 Temporal 接口**，这个接口定义了**如何读取和操纵为时间建模的对象的值**。

**Duration 类**的**静态工厂方法 between** 可以创建两个 Temporal 对象**之间的 duration**。
```
Duration d1 = Duration.between(time1, time2);
Duration d1 = Duration.between(dateTime1, dateTime2);
Duration d2 = Duration.between(instant1, instant2);
```
> 两个 LocalTimes 对象、两个 LocalDateTimes 对象，或两个 Instant 对象之间的 duration。
> 
> Duration 类主要用于**以秒和纳秒衡量时间的长短**，所以不能仅向 between 方法传递一个 LocalDate 对象做参数。

**Period 类**可用年、月或日的方式**对多个时间单位建模**，使用该类的**静态工厂方法 between** 可以得到两个 **LocalDate 之间的时长**。
```
Period tenDays = Period.between(LocalDate.of(2014, 3, 8), LocalDate.of(2014, 3, 18));
```

Duration 类和 Period 类**共享的方法**如下表所示：

| 方法名 | 是否静态方法 | 方法描述 |
| --- | --- | --- |
| between | 是 | 创建两个时间点之间的 interval |
| from | 是 | 由一个临时时间点创建 interval |
| of | 是 | 由它的组成部分创建 interval 的实例 |
| parse | 是 | 由字符串创建 interval 的实例 |
| addTo | 否 | 创建该 interval 的副本，并将其叠加到某个指定的 temporal 对象 |
| get | 否 | 读取该 interval 的状态 |
| isNegative | 否 | 检查该 interval 是否为负值，不包含零 |
| isZero | 否 | 检查该 interval 的时长是否为零 |
| minus | 否 | 通过减去一定的时间创建该 interval 的副本 |
| multipliedBy | 否 | 将 interval 的值乘以某个标量创建该 interval 的副本 |
| negated | 否 | 以忽略某个时长的方式创建该 interval 的副本 |
| plus | 否 | 以增加某个指定的时长的方式创建该 interval 的副本 |
| subtractFrom | 否 | 从指定的 temporal 对象中减去该 interval |

前述的这些日期-时间对象都是**不可修改的**，这是为了更好地**支持函数式编程**，确保**线程安全**，保持**领域模式一致性**而做出的重大设计决定。

###操纵、解析和格式化日期
若已有一个 **LocalDate 对象**，想要创建它的一个**修改版**，可使用 **withAttribute 方法**。
> withAttribute 方法会创建对象的一个副本，并按照需要修改它的属性。

所有的日期和时间 API 类**都实现了** Temporal 接口的 **with 和 get 方法**。
> 通过使用 get 和 with 方法，可以将 Temporal 对象值的**读取和修改区分开**。

LocalDate、LocalTime、LocalDateTime 以及 Instant 等表示时间点的日期-时间类提供了**大量通用的方法**，如下表所示：

| 方法名 | 是否静态方法 | 方法描述 |
| --- | --- | --- |
| from | 是 | 依据传入的 Temporal 对象创建对象实例 |
| now | 是 | 依据系统时钟创建 Temporal 对象 |
| of | 是 | 由 Temporal 对象的某个部分创建该对象的实例 |
| parse | 是 | 由字符串创建 Temporal 对象的实例 |
| atOffset | 否 | 将 Temporal 对象和某个时区偏移相结合 |
| atZone | 否 | 将 Temporal 对象和某个时区相结合 |
| format | 否 | 使用某个指定的格式器将Temporal 对象转换为字符串（Instant 类不提供该方法） |
| get | 否 | 读取 Temporal 对象的某一部分的值 |
| minus | 否 | 创建 Temporal 对象的一个副本，通过将当前 Temporal 对象的值减去一定的时长创建该副本 |
| plus | 否 | 创建 Temporal 对象的一个副本，通过将当前 Temporal 对象的值加上一定的时长创建该副本 |
| with | 否 | 以该 Temporal 对象为模板，对某些状态进行修改创建该对象的副本 |

有时可能需要进行一些**更加复杂的日期操作**，比如，将日期调整到下个周日、下个工作日，或者是本月的最后一天。
> 此时可以使用 LocalDate 类**重载版本的 with 方法**，向其传递一个提供了**更多定制化选择的 TemporalAdjuster 对象**，更加灵活地处理日期。

```
import static java.time.temporal.TemporalAdjusters.*;

LocalDate date1 = LocalDate.of(2014, 3, 18);
LocalDate date2 = date1.with(nextOrSame(DayOfWeek.SUNDAY));
LocalDate date3 = date2.with(lastDayOfMonth());
```
> date1 表示 2014-3-18。
> 
> date2 表示 2014-3-23。
> 
> date3 表示 2014-3-31。

**TemporalAdjuster 类**中的工厂方法如下：
- **dayOfWeekInMonth**
> 创建一个新的日期，它的值为同一个月中每一周的第几天
- **firstDayOfMonth**
> 创建一个新的日期，它的值为当月的第一天
- **firstDayOfNextMonth**
> 创建一个新的日期，它的值为下月的第一天
- **firstDayOfNextYear**
> 创建一个新的日期，它的值为明年的第一天
- **firstDayOfYear**
> 创建一个新的日期，它的值为当年的第一天
- **firstInMonth**
> 创建一个新的日期，它的值为同一个月中，第一个符合星期几要求的值
- **lastDayOfMonth**
> 创建一个新的日期，它的值为当月的最后一天
- **lastDayOfNextMonth**
> 创建一个新的日期，它的值为下月的最后一天
- **lastDayOfNextYear**
> 创建一个新的日期，它的值为明年的最后一天
- **lastDayOfYear**
> 创建一个新的日期，它的值为今年的最后一天
- **lastInMonth**
> 创建一个新的日期，它的值为同一个月中，最后一个符合星期几要求的值
- **next/previous**
> 创建一个新的日期，并将其值设定为日期调整后或者调整前，第一个符合指定星期几要求的日期
- **nextOrSame/previousOrSame**
> 创建一个新的日期，并将其值设定为日期调整后或者调整前，第一个符合指定星期几要求的日期，如果该日期已经符合要求，直接返回该对象

**TemporalAdjuster 接口**只声明了一个方法（即是一个**函数式接口**），定义如下：
```
@FunctionalInterface
public interface TemporalAdjuster {
	Temporal adjustInto(Temporal temporal);
}
```
> TemporalAdjuster 接口的实现需要定义**如何将一个 Temporal 对象转换为另一个 Temporal 对象**。
> > 可以把它**看成一个 UnaryOperator&lt;Temporal>**。

java.time.format 包专门用于**格式化以及解析日期-时间对象**，最重要的类是 **DateTimeFormatter**。
> **创建格式器**最简单的方法是通过它的**静态工厂方法以及常量**。
> 
> 所有的 DateTimeFormatter 实例都是**线程安全的**。因此可用单例模式创建格式器实例，并能在多个线程间共享这些实例。

```
LocalDate date = LocalDate.of(2014, 3, 18);

String s1 = date.format(DateTimeFormatter.BASIC_ISO_DATE);
String s2 = date.format(DateTimeFormatter.ISO_LOCAL_DATE);
```
> 使用两个不同的格式器生成字符串。

```
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("dd/MM/yyyy");

LocalDate date1 = LocalDate.of(2014, 3, 18);
String formattedDate = date1.format(formatter);
LocalDate date2 = LocalDate.parse(formattedDate, formatter);
```
> **ofPattern** 静态工厂方法可以**按照某个特定的模式创建格式器**。
> 
> **formate** 方法可以**使用指定的模式生成了一个代表该日期的字符串**。
> 
> **parse** 静态工厂方法可以**使用同样的格式器解析生成的字符串，并重建日期对象**。

**DateTimeFormatterBuilder 类**提供了更复杂的格式器，你可以选择恰当的方法，一步一步地构造自己的格式器。
> 它提供了非常强大的解析功能，比如**区分大小写的解析**、**柔性解析**（允许解析器使用启发式的机制去解析输入，不精确地匹配指定的模式）、**填充**，以及**在格式器中指定可选节**。 

```
DateTimeFormatter italianFormatter = new DateTimeFormatterBuilder()
	.appendText(ChronoField.DAY_OF_MONTH)
	.appendLiteral(". ")
	.appendText(ChronoField.MONTH_OF_YEAR)
	.appendLiteral(" ")
	.appendText(ChronoField.YEAR)
	.parseCaseInsensitive()
	.toFormatter(Locale.ITALIAN);
```

###处理不同的时区和历法
java.time.ZoneId 类用于处理时区，在 **ZoneRules 类**包含了 40 个**时区实例**，可通过调用 ZoneId 的 **getRules() 方法**得到指定时区的规则。

每个特定的 ZoneId 对象都**由一个地区 ID 标识**：
```
ZoneId romeZone = ZoneId.of("Europe/Rome");
```
> 地区ID都为“{区域}/{城市}”的格式。

**toZoneId 方法**可将一个老的时区对象（java.util.TimeZone 对象）**转换为 ZoneId**：
```
ZoneId zoneId = TimeZone.getDefault().toZoneId();
```

ZoneId 对象可与 LocalDate、LocalDateTime 或是 Instant 对象整合起来，**构造为一个 ZonedDateTime 实例**，它代表了**相对于指定时区的时间点**。
```
LocalDate date = LocalDate.of(2014, Month.MARCH, 18);
ZonedDateTime zdt1 = date.atStartOfDay(romeZone);

LocalDateTime dateTime = LocalDateTime.of(2014, Month.MARCH, 18, 13, 45);
ZonedDateTime zdt2 = dateTime.atZone(romeZone);

Instant instant = Instant.now();
ZonedDateTime zdt3 = instant.atZone(romeZone);
```

ZonedDateTime 的**组成部分**如下图所示：
![](https://i.imgur.com/gVx3LI1.png)

通过 ZoneId，可**将 LocalDateTime 转换为 Instant**：
```
LocalDateTime dateTime = LocalDateTime.of(2014, Month.MARCH, 18, 13, 45);
Instant instantFromDateTime = dateTime.toInstant(romeZone);
```

也可以通过反向的方式**将 Instant 转换为 LocalDateTime**：
```
Instant instant = Instant.now();
LocalDateTime timeFromInstant = LocalDateTime.ofInstant(instant, romeZone);
```

另一种比较通用的表达时区的方式是**利用当前时区和 UTC/GMT 的固定偏差**，此时可使用 **ZoneOffset 类**，它是 **ZoneId 的一个子类**，表示的是当前时间和伦敦格林尼治子午线时间的差异：
```
ZoneOffset beiJingOffset = ZoneOffset.of("+08:00");
```
> “+08:00” 的偏差实际上对应的是北京时间。
