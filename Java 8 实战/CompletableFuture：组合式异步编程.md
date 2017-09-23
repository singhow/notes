##CompletableFuture：组合式异步编程
随着多核处理器的出现，提升应用程序处理速度最有效的方式是编写能充分发挥多核能力的软件。
> 可以通过**切分大型的任务**，让每个**子任务并行运行**来实现这一目标。
> 
> 相对直接使用线程的方式，还可以**使用分支/合并框架和并行流**能以更简单、更有效的方式实现这一目标。

第二种趋势反映在公共 API 日益增长的互联网服务应用，现在的网络应用都**采用“混聚”（mash-up）的方式**：它会使用来自多个来源的内容，将这些内容聚合在一起，方便用户的生活。
![](https://i.imgur.com/UATnodS.png)
> 要实现类似的服务，你需要与互联网上的**多个 Web 服务通信**。可是，你并不希望因为**等待某些服务的响应**，**阻塞应用程序的运行**，浪费数十亿宝贵的 CPU 时钟周期。
> 
> 这些场景体现了多任务程序设计的另一面。

若开发者的意图是**实现并发**，而非并行，或者主要目标是**在同一个 CPU 上执行几个松耦合的任务**，充分利用 CPU 的核，让其足够忙碌，从而**最大化程序的吞吐量**，那么真正想做的是**避免**因为等待远程服务的返回，或者对数据库的查询，而**阻塞线程的执行**。
![](https://i.imgur.com/UlEiVrc.png)

###Future 接口
Future 接口的设计初衷是**对将来某个时刻会发生的结果**进行建模。
> 它建模了一种**异步计算**，返回一个**执行运算结果的引用**，当运算结束后，这个引用**被返回给调用方**。
> > 在 Future 中**触发那些潜在耗时的操作**把调用线程解放出来，让它能继续执行其他有价值的工作，而不是呆呆等待耗时的操作完成。
> 
> 要使用 Future，通常只需**将耗时的操作封装在一个 Callable 对象**中，再将它**提交给 ExecutorService** 即可。
> > 这种编程方式使得线程可以在 ExecutorService **以并发方式调用另一个线程执行耗时操作**的同时，去执行一些其他的任务。
> 
> > 若已经运行到没有异步操作的结果就无法继续任何有意义的工作时，可以调用 Future 的 **get 方法去获取操作的结果**。

![](https://i.imgur.com/2fwNoFh.png)
> Future 提供了一个**重载版本的 get** 方法，它接受一个**超时的参数**。
> > 你可以定义你的线程等待 Future 结果的最长时间。

Future 接口提供了方法来**检测异步计算是否已经结束**（使用 isDone 方法），**等待异步操作结束**，以及**获取计算的结果**。但是这些特性还不足以让你编写简洁的并发代码。比如：
- **将两个异步计算合并为一个**。
> 这两个异步计算之间相互独立，同时第二个又依赖于第一个的结果。
- **等待 Future 集合中的所有任务都完成**。
- 仅等待 Future 集合中最快结束的任务完成（有可能因为它们试图通过不同的方式计算同一个值），并返回它的结果。
- **通过编程方式完成一个 Future 任务的执行**。
> 即以手工设定异步操作结果的方式。
- **应对 Future 的完成事件**。
> 即当 Future 的完成事件发生时会收到通知，并能使用 Future 计算的结果进行下一步的操作，不只是简单地阻塞等待操作的结果。

**CompletableFuture 类**（它实现了 Future 接口）利用 Java 8 的新特性以更直观的方式**将上述需求都变为可能**。
> Stream 和 CompletableFuture 的设计都遵循了类似的模式：它们都**使用了 Lambda 表达式以及流水线的思想**。
> 
> 从这个角度可以说 **CompletableFuture 和 Future** 的关系就跟 **Stream 和 Collection** 的关系一样。

CompletableFuture 的强大特性能使你学到几个重要的技能：
- 首先，你会学到**如何为你的客户提供异步 API**。
- 其次，你会掌握如何**让使用了同步 API 的代码变为非阻塞代码**。
> 你会了解如何使用流水线**将两个接续的异步操作合并为一个异步计算操作**。
- 你还会学到如何**以响应式的方式处理异步操作的完成事件**。

**同步 API** 只是对传统方法调用的另一种称呼：你调用了某个方法，调用方**在被调用方运行的过程中会等待**，被调用方运行结束返回，调用方取得被调用方的返回值并继续运行。
> 即使调用方和被调用方**在不同的线程中运行**，调用方**还是需要等待**被调用方结束运行，这即是**阻塞式调用**。

**异步 API** 会**直接返回**，或者至少在被调用方计算完成之前，将它**剩余的计算任务交给另一个线程**去做，该线程和调用方是异步的，这即是**非阻塞式调用**。
> 执行剩余计算任务的线程会**将它的计算结果返回给调用方**。
> > 返回的方式**要么是通过回调函数**，要么是**由调用方再次执行一个“等待，直到计算完成”的方法调用**。
> 
> 这种方式的计算在 **I/O 系统程序设计**中非常常见：
> > **发起一次磁盘访问**，这次访问和你的其他计算操作是**异步的**，你完成其他的任务时，磁盘块的数据可能还没载入到内存，你只需要**等待数据载入完成**。

###实现异步 API
Future 是一个**暂时还不可知值**的处理器，这个值在**计算完成后**，可以通过**调用它的 get 方法取得**。

若线程池中**线程的数量过多**，最终它们会**竞争稀缺的处理器和内存资源**，导致大量时间**浪费在上下文切换上**。
> 反之，若线程的数目过少，处理器的一些核可能就无法充分利用。
> 
> **线程池大小与处理器的利用率之比**可以使用下面的公式进行估算：
> > Nthreads = Ncpu * Ucpu * (1 + W/C)
> 
> > Ncpu 是**处理器的核的数目**，可通过 Runtime.getRuntime().availableProcessors() 得到。
> 
> > Ucpu 是**期望的 CPU 利用率**（介于 0 和 1）。
> 
> > W/C 是**等待时间与计算时间的比率**。

到目前为止，**对集合进行并行计算**有两种方式：
- 要么将其**转化为并行流**，利用 map 这样的操作开展工作。
> 若进行的是**计算密集型的操作**，并且没有 I/O，那么推荐**使用 Stream 接口**。
- 要么**枚举出集合中的每一个元素，创建新的线程**，在 CompletableFuture 内对其进行操作。
> 这种方式提供了更多的灵活性，你可以**调整线程池的大小**，而这能帮助你确保整体的计算**不会因为线程都在等待 I/O 而发生阻塞**。
> 
> 若并行的工作单元**涉及等待 I/O 的操作**（包括网络连接等待），那么**使用 CompletableFuture** 灵活性更好。

通常而言，名称中**不带 Async 的方法**和它的前一个任务一样，在**同一个线程**中运行；
> 而名称**以 Async 结尾的方法**会将后续的任务提交到一个线程池，所以每个任务是由**不同的线程**处理的。

###实践
使用**顺序流**查询价格：
```
public List<String> findPricesSequential(String product) {
	return shops.stream()
			.map(shop -> shop.getPrice(product))
			.map(Quote::parse)
			.map(Discount::applyDiscount)
			.collect(Collectors.toList());
}
```

使用**并行流**查询价格：
```
public List<String> findPricesParallel(String product) {
	return shops.parallelStream()
			.map(shop -> shop.getPrice(product))
			.map(Quote::parse)
			.map(Discount::applyDiscount)
			.collect(Collectors.toList());
}
```

使用**异步请求**查询价格：
```
public List<String> findPricesAsync(String product) {
	List<CompletableFuture<String>> priceFutures =
		shops.stream()
		.map(shop -> CompletableFuture.supplyAsync(
			() -> shop.getName() + " price is " + shop.getPrice(product)))
		.collect(Collectors.toList());
		
		return priceFutures.stream()
				.map(CompletableFuture::join)
				.collect(toList());
}
```
> 第一个流将 CompletableFutures 对象**聚集到一个列表**中，使得对象可以在其他对象完成操作之前就能启动。
> 
> 第二个流中对 CompletableFutures 对象调用 join 以**等待其他对象完成**，当所有对象都完成后，将结果**归约到一个列表**，然后返回。

异步请求使用**两个不同的 Stream 流水线**来进行处理，其过程如下图所示：
![](https://i.imgur.com/k4F9jFM.png)
> 考虑流操作之间的延迟特性，若在**单一流水线**中处理流，发向不同商家的请求只有**以同步、顺序执行的方式**才会成功。
> > 因此如图中上半部分所示，每个创建 CompletableFuture 对象**只能在前一个操作结束之后**执行查询指定商家的动作、通知 join 方法返回计算结果。
> 
> 图中下半部分则表示先将 CompletableFutures 对象**聚集到一个列表**中，让对象们可以在**等待其他对象完成操作之前就能启动**。
> > 此处即使用到了**两个不同的 Stream 流水线**。

**异步版本**只比并行版本**快一点**，其原因如下：
> 它们内部采用的是同样的通用线程池，**默认都使用固定数目的线程**，具体线程数取决于 Runtime.getRuntime().availableProcessors() 的返回值。
> 
> 然而 CompletableFuture 具有一定的优势，因为它允许你**对执行器（Executor）进行配置**，尤其是线程池的大小。

使用**带 executor 参数的异步请求**查询价格：
```
public List<String> findPricesAsync(String product) {
	List<CompletableFuture<String>> priceFutures =
		shops.stream()
		.map(shop -> CompletableFuture.supplyAsync(
			() -> shop.getName() + " price is " + shop.getPrice(product),
			executor))
		.collect(Collectors.toList());
		
		return priceFutures.stream()
				.map(CompletableFuture::join)
				.collect(toList());
}
```
> supplyAsync 方法接受了第二个 executor 参数。 

**使用 Discount 服务**的查找价格方法：
```
public List<String> findPricesSequential(String product) {
	return shops.stream()
		.map(shop -> shop.getPrice(product))
		.map(Quote::parse)
		.map(Discount::applyDiscount)
		.collect(Collectors.toList()); // 将结果归约到一个列表
}
```
> 第一个 map 将每个 shop 对象转换成包含指定商品价格和折扣代码的字符串。
> 
> 第二个 map 对这些字符串进行了解析，在 Quote 对象中对它们进行转换。
> 
> 第三个 map 联系远程的 Discount 服务，计算出最终的折扣价格，然后返回该价格及提供该价格商品的 shop。

使用带有**同步操作和异步请求**的方式查找价格：
```
public List<String> findPricesSyncAsync(String product) {
	List<CompletableFuture<String>> priceFutures = 
		shops.stream()
		.map(shop -> CompletableFuture.supplyAsync(
			// 以异步方式取得每个 shop 中产品的原始价格
			() -> shop.getPrice(product), executor))
		.map(future -> future.thenApply(Quote::parse))
		.map(future -> future.thenCompose(quote -> CompletableFuture
			.supplyAsync(
				// 使用了一个异步任务构造期望的 Future，申请折扣
				() -> Discount.applyDiscount(quote), executor)))
		.collect(Collectors.toList());
		
	return priceFutures.stream()
			// 等待流中的所有 Future 执行完毕，并提取各自的返回值
			.map(CompletableFuture::join)
			.collect(Collectors.toList());
}
```
> 首先以异步方式取得每个 shop 中指定产品的原始价格。
> 
> 然后使用另一个异步任务构造期望的 Future，申请折扣。
> 
> 最后调用 join 等待流中的所有 Future 执行完毕，并提取各自的返回值。

**构造同步和异步请求**的三次转换的流程如下图所示：
![](https://i.imgur.com/TgtpqdV.png)
- **异步获取价格**
> 以**异步方式**对 shop 进行查询，转换的结果是一个 Stream&lt;CompletableFuture&lt;String>>。
> 
> 运行结束后每个 CompletableFuture 对象中都会包含对应 shop 返回的字符串。
- **同步解析报价**
> 调用 **thenApply 方法**，将一个由字符串转换为 Quote 的方法作为参数传递给它。
> 
> 由于解析操作不涉及任何远程服务，也不会进行任何 I/O 操作，它几乎可以在第一时间进行，所以能够**采用同步操作**，不会带来太多的延迟。
- **异步为计算折扣价格构造 Future**
> 该操作涉及联系远程的 Discount 服务，因此希望它能够**异步执行**。
> 
> **thenCompose 方法**允许你**对两个异步操作进行流水线**，第一个操作完成时，将其**结果作为参数传递给第二个操作**。
> > 可以创建两个 CompletableFutures 对象，对第一个 CompletableFuture 对象调用 thenCompose，并向其传递一个函数。
> 
> > 当第一个 CompletableFuture 执行完毕后，它的结果将作为该函数的参数，这个函数的返回值是以第一个 CompletableFuture 的返回做输入计算出的第二个 CompletableFuture 对象。
> 
> > 使用这种方式，即使 Future 在向不同的商店收集报价，**主线程还是能继续执行其他重要的操作**，比如响应 UI 事件。
- 通过使用 CompletableFuture 类提供的特性，在需要的地方把它们变成了异步操作。

**合并两个独立 CompletableFuture 对象**的代码如下：
```
public List<String> findPricesInUSD(String product) {
	List<CompletableFuture<Double>> priceFutures = new ArrayList<>();
	
	for (Shop shop : shops) {
		// Only the type of futurePriceInUSD has been changed to
		// CompletableFuture so that it is compatible with the
		// CompletableFuture::join operation below.
		CompletableFuture<Double> futurePriceInUSD = 
			CompletableFuture.supplyAsync(() -> shop.getPrice(product))
				.thenCombine(
					CompletableFuture.supplyAsync(
						() ->  ExchangeService.getRate(Money.EUR, Money.USD)),
						(price, rate) -> price * rate);
            
		priceFutures.add(futurePriceInUSD);
	}
		
	// Drawback: The shop is not accessible anymore outside the loop,
	// so the getName() call below has been commented out.
	List<String> prices = priceFutures
		.stream()
		.map(CompletableFuture::join)
		.map(price -> /*shop.getName() +*/ " price is " + price)
		.collect(Collectors.toList());
	
	return prices;
}
```

**合并两个独立异步任务**的流程如下图所示：
![](https://i.imgur.com/SolvjMQ.png)
> 多个任务在线程池中选择不同的线程执行，然后整合最终的运行结果。

我们最终想要达到的效果是**只要有商店返回商品价格就在第一时间显示返回值**，不再等待那些还未返回的商店（有些甚至会发生超时）。
> 首先要**避免等待创建**一个包含了所有价格的List创建完成。
> > 应该**直接处理 CompletableFuture 流**，这样每个 CompletableFuture 都在为某个商店执行必要的操作。

返回一个 **CompletableFuture 流**：
```
public Stream<CompletableFuture<String>> findPricesStream(String product) {
	return shops.stream()
		.map(shop -> CompletableFuture.supplyAsync(
			() -> shop.getPrice(product), executor))
		.map(future -> future.thenApply(Quote::parse))
		.map(future -> future.thenCompose(
			quote -> CompletableFuture.supplyAsync(
				() -> Discount.applyDiscount(quote), executor)));
}
```

现在可以为 findPricesStream 返回的 CompletableFuture **流添加第四个 map 方法**，它在每个 CompletableFuture 上**注册一个操作**，该操作会在 CompletableFuture **完成执行后使用它的返回值 **。
> CompletableFuture 的 **thenAccept 方法**提供了这一功能，它**接收** CompletableFuture 执行完毕后的**返回值做参数**。
> 
> thenAccept 方法也提供了一个名为 **thenAcceptAsync 的异步版本**。
> > 异步版本的方法会对处理结果的消费者进行调度，从线程池中选择一个新的线程继续执行，不再由同一个线程完成 CompletableFuture 的所有任务。

**响应 CompletableFuture 的 completion 事件**：
```
CompletableFuture[] futures = findPricesStream("myPhone")
	.map(f -> f.thenAccept(System.out::println))
	.toArray(size -> new CompletableFuture[size]);

CompletableFuture.allOf(futures).join();
```
> 由于要**避免不必要的上下文切换**，更重要的是**避免在等待线程上浪费时间**，尽快响应 CompletableFuture 的 completion 事件，所以这里没有采用异步版本。
> 
> thenAccept 方法中定义了**如何处理 CompletableFuture 返回的结果**，一旦 CompletableFuture 计算得到结果，它就返回一个 CompletableFuture&lt;Void>，所以，map 操作**返回**的是一个 **Stream&lt;CompletableFuture&lt;Void>>**。
> > 此处想要最慢的商店也有机会打印输出返回的结果，因此把构成 Stream 的所有 CompletableFuture&lt;Void> 对象放到了一个数组中，等待所有的任务执行完成。
> 
> **allOf 工厂方法**接收一个**由 CompletableFuture 构成的数组**，数组中的**所有 CompletableFuture 对象执行完成之后**，它**返回一个 CompletableFuture&lt;Void> 对象**。
> 
> 若希望只要 CompletableFuture 对象数组中有**任何一个执行完毕就不再等待**，则可以使用一个 **anyOf 工厂方法**。
> > 该方法接收一个 CompletableFuture 对象构成的数组，返回由第一个执行完毕的 CompletableFuture 对象的返回值构成的 CompletableFuture&lt;Object>。
