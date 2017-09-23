##Concurrency
###Basic threading
---
A thread **drives a task**. To define a task, simply **implement Runnable** interface and **write a run() method** to make the task do your bidding.

A task’s run() method usually has **some kind of loop** that continues until the task is no longer necessary.
> You can also **simply return** from run().

You could call the **static method Thread.yield()** inside run(), It's a suggestion to the **thread scheduler** (the part of the Java threading mechanism that moves the CPU from one thread to the next) that says:
> I’ve done the important parts of my cycle and this would be a good time to **switch to another task** for a while.

When a class is derived from Runnable, it **must have a run() method**. To achieve threading behavior, you must **explicitly attach a task to a thread**. The traditional way to turn a Runnable object into a working task is to **hand it to a Thread constructor**.

**Executors** provide a layer of indirection **between a client and the execution of a task**; instead of a client executing a task directly, **an intermediate object executes the task**. Executors allow you to **manage the execution of asynchronous tasks** without having to explicitly manage the lifecycle of threads.

The **CachedThreadPool** creates **one thread per task**. With the **FixedThreadPool**, you do expensive thread **allocation once**, and you thus **limit** the number of threads.

A **Executors.newCachedThreadPool()** will generally **create as many threads as it needs** during the execution of a program and then will stop creating new threads **as it recycles the old ones**, so it’s a reasonable first choice as an Executor in production code.

A **SingleThreadExecutor** is like a FixedThreadPool with **a size of one thread**. It also offers an important **concurrency guarantee** that the others do not — **no two tasks will be called concurrently**. This changes the locking requirements for the tasks.

If more than one task is submitted to a SingleThreadExecutor, the tasks will be **queued and each task will run to completion** before the next task is begun, **all using the same thread**.
> Thus, a SingleThreadExecutor **serializes the tasks** that are submitted to it, and maintains its own (hidden) **queue of pending tasks**.
> 
> By **serializing tasks**, you can eliminate the need to serialize the objects.

A **Runnable** is a separate task that performs work, but it **doesn’t return a value**. If you want the task to **produce a value** when it’s done, you can implement the **Callable interface** rather than the Runnable interface.

**Callable** is a generic with a type parameter **representing the return value** from the method **call()** (instead of run()), and **must** be invoked using an ExecutorService **submit() method**.

The overloaded Executors.callable() method **takes a Runnable and produces a Callable**. ExecutorService has some "invoke" methods that run collections of Callable objects.

A simple way to **affect the behavior of your tasks** is by calling **sleep()** to **cease (block) the execution of that task for a given time**. When the task goes to sleep (it blocks), which allows the **thread scheduler** to **switch to another thread**, driving another task.
> If you must **control the order of execution** of tasks, your best bet is to use synchronization controls or, in some cases, not to use threads at all, but instead to write your own cooperative routines that hand control to each other in a specified order.

The **priority of a thread** conveys the importance of a thread to the scheduler. Although the order in which the CPU runs a set of threads is indeterminate, the scheduler will **lean toward running the waiting thread with** the highest priority first, and the lower-priority threads tend to run less often.

If you know that you’ve accomplished what you need to during one pass through a loop in your run() method, you can **give a hint to the threadscheduling mechanism** that you’ve done enough and that some other task might as well have the CPU.
> When you call **yield() method**, you are suggesting that other threads of the same priority might be run.
> 
> However, you can’t rely on yield() for any serious control or tuning of your application. 

A **daemon thread** is intended to provide a general service **in the background** as long as the program is running, but is not part of the essence of the program. Thus, **when all of the non-daemon threads complete**, the program is terminated, killing all daemon threads in the process.

If a thread is a daemon, then any threads it creates will **automatically be daemons**.

You should be aware that daemon threads will terminate their run() methods **without executing finally clauses**.

Daemons are **terminated "abruptly"** when the last of the non-daemons terminates. So as soon as main() exits, the JVM **shuts down all the daemons immediately**, without any of the formalities you might have come to expect.

In very simple cases, you may want to **inheriting directly from Thread**. You can also **use self-managed Runnable**. Implementing an interface does allow you to **inherit from a different class**, whereas inheriting from Thread does not.

You should be aware that **starting threads inside a constructor** can be quite problematic, because another task might start executing before the constructor has completed, which means the task may be able to **access the object in an unstable state**.

Sometimes it makes sense to **hide your threading code** inside your class by **using an inner class**. You call the method when you’re ready to run the thread, and the method returns after the thread begins.
> If the thread is **only performing an auxiliary operation** rather than being fundamental to the class, this is probably a more useful and appropriate approach than starting a thread inside the constructor of the class.

If a thread **calls t.join()** on another thread t, then the calling thread is **suspended until the target thread t finishes** (when **t.isAlive()** is false).

You may also call join() **with a timeout** argument so that if the target thread doesn’t finish in that period of time, the call to join() **returns anyway**.

The call to join() may be **aborted by calling interrupt()** on the calling thread, so a **try-catch** clause is required.

A **thread group** holds a collection of threads. The value of thread groups can be summed up by a quote:
> Thread groups are best viewed as an **unsuccessful experiment**, and you may simply **ignore their existence**.

Because of the nature of threads, you **can’t catch an exception that has escaped from a thread**. Once an exception gets outside of a task’s run() method, it will **propagate out to the console** unless you take special steps to capture such errant exceptions.

**Thread.UncaughtExceptionHandler** allows you to **attach an exception handler to each Thread object**. Thread.UncaughtExceptionHandler.uncaughtException() is **automatically called** when that thread is about to die from an uncaught exception.

You can set the **default uncaught exception handler** by using **Thread.setDefaultUncaughtExceptionHandler()**, this handler is only called if **there is no per-thread uncaught exception handler**.

###Sharing resources
---
**Increment** is **not an atomic operation** in Java, so even a single increment isn’t safe to do without protecting the task. Preventing this kind of collision is simply a matter of **putting a lock on a resource** when one task is using it.

To solve the problem of **thread collision**, virtually all concurrency schemes **serialize access to shared resources**. This means that only one task at a time is allowed to access the shared resource.
> This is ordinarily accomplished by **putting a clause around a piece of code that only allows one task at a time** to pass through that piece of code. Because this clause **produces mutual exclusion**, a common name for such a mechanism is mutex.

The **shared resource** is typically just **a piece of memory in the form of an object**, but may also be **a file**, **an I/O port**, or **something like a printer**.
> To **control access** to a shared resource, you first **put it inside an object**.
> 
> Then **any method** that uses the resource can be **made synchronized**.
> 
> If a task is in a call to one of the synchronized methods, all other tasks are **blocked from entering any of the synchronized methods of that object** until the first task returns from its call.

To prevent collisions over resources, Java has built-in support in the form of the **synchronized keyword**. When a task wishes to execute a piece of code guarded by the synchronized keyword, it **checks** to see if the lock is available, then **acquires** it, **executes** the code, and **releases** it.
```
synchronized void f() { /* ... */ }
synchronized void g() { /* ... */ }
```
> if f() is called for an object by one task, a different task **cannot call f() or g() for the same object** until f() is completed and releases the lock.
> 
> Thus, there is a **single lock** that is **shared by all the synchronized methods of a particular object**, and this lock can be used to prevent object memory from being written by more than one task at a time.

The synchronized keyword is **not part of the method signature** and thus may be added during overriding.

All objects **automatically contain a single lock** (also referred to as a monitor). When you **call any synchronized method**, that object is **locked and no other synchronized method of that object can be called** until the first one finishes and releases the lock.

One task may **acquire an object’s lock multiple times**. This happens **if one method calls a second method on the same object**, which in turn calls another method on the same object, etc. The JVM **keeps track of the number of times** the object has been locked. If the object is **unlocked**, it has a **count of zero**.
> As a task acquires the lock for the first time, the count goes to one. Each time **the same task acquires another lock on the same object**, the count is incremented.

If you are writing a variable that might next be read by another thread, or reading a variable that might have last been written by another thread, you **must use synchronization**, and further, both the reader and the writer **must synchronize using the same monitor lock**.

The **Lock object** must be **explicitly** created, locked and unlocked. Right after the call to lock(), you must **place a try-finally statement with unlock() in the finally clause** — this is **the only way to guarantee that the lock is always released**. With explicit Lock objects, you can **maintain proper state** in your system **using the finally clause**.

When you are **using synchronized**, there is less code to write, and the opportunity for user error is greatly reduced. 
> With the synchronized keyword, you **can’t try and fail to acquire a lock**, or **try to acquire a lock for a certain amount of time and then give up**.

A **ReentrantLock** allows you to **try and fail to acquire the lock**, so that if someone else already has the lock, you can decide to **go off and do something else** rather than waiting until it is free.

The **explicit Lock object** also gives you **finer-grained control** over locking and unlocking than does the built-in synchronized lock. This is useful for implementing specialized synchronization structures.

An **atomic operation** is one that **cannot be interrupted by the thread scheduler**; if the operation begins, then it will **run to completion before the possibility of a context switch**.
> Atomicity applies to "simple operations" on **primitive types** except for longs and doubles.
> 
> **Reading and writing** primitive variables other than long and double is **guaranteed to go to and from memory as indivisible (atomic) operations**.

On multiprocessor systems **visibility rather than atomicity** is much more of an issue than on single-processor systems. Changes made by one task, even if they’re atomic in the sense of not being interruptible, **might not be visible to other tasks** (the changes might be temporarily stored in a local processor cache).
> The **synchronization** mechanism **forces changes** by one task on a multiprocessor system to be **visible** across the application.

The **volatile keyword** ensures **visibility** across the application. If you declare a field to be volatile, this means that **as soon as a write occurs** for that field, **all reads will see the change**.
> the compiler **not** to do any **optimizations**, reads and writes go **directly to memory**, and are **not cached**.
> 
> it also **restricts** compiler **reordering of accesses** during optimization.

You should **make a field volatile** if that field could be **simultaneously accessed by multiple tasks**, and at least one of those accesses is a **write**.

**Atomicity** and **volatility** are distinct concepts.
> An **atomic operation** on a non-volatile field will **not necessarily be flushed to main memory**, and so another task that reads that field will not necessarily see the new value.
>
> If **multiple tasks** are accessing a field, that **field should be volatile**; otherwise, the field should **only be accessed via synchronization**.
> > **Synchronization** also causes **flushing to main memory**, so if a field is completely guarded by synchronized methods or blocks, it is not necessary to make it volatile.

In Java, the following operations are **definitely not atomic**:
```
i++;
i += 2;
```

As you can see from the **JVM instructions** produced by the following methods:
```
public class Atomicity {
  int i;
  
  void f1() { i++; }
  void f2() { i += 3; }
}

void f1();
Code:
0: aload_0
1: dup
2: getfield #2; //Field i:I
5: iconst_1
6: iadd
7: putfield #2; //Field i:I
10: return

void f2();
Code:
0: aload_0
1: dup
2: getfield #2; //Field i:I
5: iconst_3
6: iadd
7: putfield #2; //Field i:I
10: return
```

A **Java increment** is **not atomic** and involves **both a read and a write**. 

You may only want to **prevent multiple thread** access to **part of the code** inside a method, this part of code called a **critical section** and is created using the **synchronized keyword**. You can also use **explicit Lock objects** to create critical sections.

synchronized is used to **specify the object** whose lock is being **used to synchronize the enclosed code**:
```
synchronized(syncObject) {
	// This code can be accessed by only one task at a time
}
```
> Before it can be entered, the lock **must be acquired on syncObject**.
> 
> If some other task already has this lock, then the critical section **cannot be entered until the lock is released**.

When the **lock is acquired for the synchronized block**, other synchronized methods and critical sections in the object **cannot be called**.

Sometimes you must **synchronize on another object**, but if you do this you must **ensure that all relevant tasks are synchronizing on the same object**.

**Thread local storage** is a mechanism that automatically **creates different storage for the same variable**, for each different thread that uses an object.

**ThreadLocal objects** are usually stored as **static fields**. When you create a ThreadLocal object, you are only able to access the contents of the object **using the get() and set() methods**.
> the get() method **returns a copy of the object** that is associated with that thread.
> 
> the set() inserts its argument into the object stored for that thread, **returning the old object** that was in storage.

ThreadLocal **guarantees** that **no race condition** can occur.

###Terminating tasks
---
A thread can be in any one of **four states**:
- **New**
> A thread remains in this state only momentarily, as it is **being created**. It **allocates any necessary system resources and performs initialization**.
> 
> At this point it **becomes eligible to receive CPU time**. The scheduler will then **transition this thread to the runnable or blocked state**.
- **Runnable**
> A thread can be run **when the time-slicing mechanism has CPU cycles available** for the thread. 
> 
> There’s **nothing to prevent it from being run** if the scheduler can arrange it. 
- **Blocked**
> The thread can be run, but **something prevents it**. The scheduler will simply **skip it and not give it any CPU time**.
- **Dead**
> **No longer schedulable** and will **not receive any CPU time**, its task is completed.
> 
> A task is die when **return from its run() method or be interrupted**.

A task can **become blocked** for the following reasons:
- **put the task to sleep** by calling sleep(milliseconds)
- **suspended the execution of the thread** with wait(), it will not become runnable again until the thread gets the notify() or notifyAll() message
- **waiting for some I/O** to complete
- call a synchronized method on another object and that **object’s lock is not available**

When you break out of a blocked task, you might **need to clean up resources**. So breaking out of the middle of a task’s run() is **more like throwing an exception** than anything else.

The **interrupt() method** sets the **interrupted status** for a thread. A thread with its interrupted status set **will throw an InterruptedException** if it is already blocked or if it attempts a blocking operation. The interrupted status **will be reset** when the exception is thrown or if the task calls Thread.interrupted().

To call interrupt(), you **must hold a Thread object**. You can also **call shutdownNow()** on an Executor, it will **send an interrupt() call to each of the threads** it has started.

If you’re using Executors, you can **hold on to the context of a task** when you start it **by calling submit()** instead of execute().

submit() **returns a generic Future<?>**, with an unspecified parameter because you won’t ever call get() on it.
> the point of holding this kind of Future is that you can **call cancel() on it** and thus **use it to interrupt a particular task**.

If you **call cancel(true)**, it **has permission to call interrupt() on that thread** in order to stop it; thus cancel() is a way **to interrupt individual threads** started with an Executor.

SleepBlock is **interruptible blocking**, whereas IOBlocked and SynchronizedBlocked are **uninterruptible blocking**.
> You **can interrupt a call** to sleep() (or any call that requires you to catch InterruptedException).
> 
> However, you **cannot interrupt a task** that is trying to acquire a synchronized lock or one that is trying to perform I/O.

NIO provide for more civilized interruption of I/O. **Blocked nio channels automatically respond to interrupts**. You can also **close the underlying channel to release the block**.

Unlike an I/O call, interrupt() can **break out of a call** that’s **blocked by a mutex**.

When you **call interrupt() on a thread**, the only time that **the interrupt occurs** is when the task **enters**, or is already inside, **a blocking operation**.
> Except in the case of uninterruptible I/O or blocked synchronized methods, in which case **there’s nothing you can do**.

If you can **only exit by throwing an exception on a blocking call**, you won’t always be able to leave the run() loop. Thus, if you call interrupt() to stop a task, your task needs **a second way to exit** in the event that your run() loop doesn’t happen to be making any blocking calls.

**Interrupted status** is set by the call to interrupt(). You **check for the interrupted status** by calling **interrupted()**. This not only tells you **whether interrupt() has been called**, it also **clears the interrupted status**. 

A class **designed to respond to an interrupt()** must establish a policy **to ensure that it will remain in a consistent state**. This generally means that **the creation of all objects that require cleanup must be followed by try-finally clauses** so that **cleanup will occur regardless of how the run() loop exits**.

**wait()** provides a way to **synchronize activities between tasks**. It **suspends the task** while waiting for the world to change, and **only when a notify() or notifyAll() occurs** does the task wake up and check for changes.
> You don’t want to idly loop while testing the condition inside your task; this is called **busy waiting**, and it’s usually a **bad use of CPU cycles**.

**sleep()** does **not release the object lock** when it is called, and **neither does yield()**. On the other hand, when a task enters **a call to wait()** inside a method, that thread’s execution is suspended, and **the lock on that object is released**.
> It means that the lock **can be acquired by another task**, so other synchronized methods in the (now unlocked) object can be called during a wait(). Those other methods are **typically what cause the change** that makes it interesting for the suspended task **to reawaken**.

###Cooperation between tasks
---
There are two forms of wait(). One version **takes an argument** in milliseconds that has **the same meaning as in sleep()**. But unlike with sleep(), with wait(pause):
- The **object lock is released** during the wait().
- You can also come out of the wait() due to a notify() or notifyAll(), in addition to letting the clock run out.

The second version **takes no arguments**. This wait() **continues indefinitely until** the thread receives a notify() or notifyAll().

wait(), notify(), and notifyAll() are **part of the base class Object** and not part of Thread. It’s essential because these methods **manipulate the lock that’s also part of every object**.

The **only place** you can call wait(), notify(), or notifyAll() is **within a synchronized method or block**.
> sleep() can be called within non-synchronized methods since **it doesn’t manipulate the lock**.

If you call any of these within a method that’s **not synchronized**, the program will compile, but when you run it, you’ll **get an IllegalMonitorStateException** with the somewhat nonintuitive message "current thread not owner."
> This message means that the task calling wait(), notify(), or notifyAll() **must "own" (acquire) the lock for the object** before it can call any of those methods.

You can **ask another object to perform an operation** that manipulates its own lock, but you must **first capture that object’s lock**.
```
synchronized(x) {
	x.notifyAll();
}
```
> if you want to send notifyAll() to an object x, you must do so inside a synchronized block that **acquires the lock for x**.

When you **call wait()**, the thread is **suspended** and the lock is **released**. It is essential that the lock be released because, to safely change the state of the object, that lock must be **available to be acquired by some other task**.
> In order for the task to wake up from a wait(), it must **first reacquire the lock** that it released when it entered the wait().
> 
> The task will not wake up **until that lock becomes available**.

On some platforms there’s **a third way to come out of a wait()**: the so-called **spurious wake-up**.
> It means a thread **may prematurely stop blocking** (while waiting on a condition variable or semaphore) **without** being prompted by a notify() or notifyAll() (or their equivalents for the new Condition objects).
> 
> Spurious wake-ups **exist because implementing POSIX threads**, or the equivalent, isn’t always as straightforward as it should be on some platforms.
> 
> Allowing spurious wake-ups makes the job of building a library like pthreads easier for those platforms.

You must **surround a wait() with a while loop** that checks the condition(s) of interest.
> You may **have multiple tasks waiting on the same lock for the same reason**, and the first task that wakes up **might change the situation** (even if you don’t do this someone might inherit from your class and do it). If that is the case, this task should be suspended again until its condition of interest changes.
> 
> By the time this task awakens from its wait(), it’s possible that **some other task will have changed things** such that this task is **unable to perform or is uninterested in performing its operation** at this time. Again, it should be resuspended by calling wait() again.
> 
> It’s also possible that **tasks could be waiting on your object’s lock for different reasons** (in which case you must use notifyAll()). In this case, you **need to check whether you’ve been woken up for the right reason**, and if not, call wait() again.

When two threads are coordinated using notify()/wait(), it’s possible to **miss a signal**. 
```
T1:
synchronized(sharedMonitor) {
	<setup condition for T2>
sharedMonitor.notify();
}

T2:
while(someCondition) {
	// Point 1
	synchronized(sharedMonitor) {
		sharedMonitor.wait();
	}
}
```
> The <setup condition for T2> is an action **to prevent T2 from calling wait()**.
> 
> Assume that T2 evaluates someCondition and finds it true. **At Point 1**, the thread scheduler **might switch to T1**. T1 executes its setup, and then calls notify().
> 
> When T2 continues executing, it is **too late for T2 to realize that the condition has been changed** in the meantime, and it will blindly enter wait(). The **notify() will be missed** and T2 will wait indefinitely for the signal that was already sent, **producing deadlock**.

The solution is to **prevent the race condition over the someCondition variable**.
```
synchronized(sharedMonitor) {
	while(someCondition)
		sharedMonitor.wait();
}
```
> if **T1 executes first**, when control returns back to T2 it will figure out that the condition has changed, and will not enter wait().
> 
> if **T2 executes first**, it will enter wait() and **later be awakened by T1**. Thus, the signal cannot be missed.

Using notify() instead of notifyAll() is an **optimization**. **Only one task** of the possible many that are waiting on a lock **will be awoken** with notify(), so you must be certain that the right task will wake up.
> All tasks must be **waiting on the same condition** in order for you to use notify(), because if you have tasks that are waiting on different conditions, you don’t know if the right one will wake up.
> 
> If you use notify(), **only one task must benefit** when the condition changes.
> 
> Finally, these constraints **must always be true** for all possible subclasses.  

In fact, only the tasks that are **waiting on a particular lock are awoken** when **notifyAll()** is called.

There are two different ways of **leaving the loop**:
- leaving the loop **with an exception**
- leaving it by **checking the interrupted() flag**

**Condition class** that **uses a mutex** and **allows task suspension**. You can **suspend a task** by calling **await()** on a Condition. When external state changes take place that might mean that **a task should continue processing**, you notify the task by calling:
- **signal()**, to wake up one task,
- or **signalAll()**, to wake up all tasks that have suspended themselves on that Condition object.

In many cases, you can move up a level of abstraction and solve task cooperation problems using a **synchronized queue**, which **only allows one task at a time** to insert or remove an element.

java.util.concurrent.**BlockingQueue interface** has a number of standard implementations.
- **LinkedBlockingQueue** is an unbounded queue
- **ArrayBlockingQueue** has a fixed size

These queues also **suspend** a consumer task if that task tries to get an object from the queue and the **queue is empty**, and **resume** when more elements become **available**.

An important difference between a PipedReader and normal I/O is seen when shutdownNow() is called — the **PipedReader is interruptible**.

###Deadlock
---
An object can have synchronized methods or other forms of locking that **prevent tasks from accessing** that object until the mutex is **released**. And the tasks can become **blocked**.
> Thus it’s possible for one task to **get stuck waiting for another task**, which in turn waits for another task, and so on, until the chain **leads back to a task waiting on the first one**.
> 
> You get a **continuous loop of tasks waiting on each other**, and no one can move. This is called **deadlock**.

The **deadlock can occur** if four conditions are **simultaneously met**:
- Mutual exclusion. At least one resource used by the tasks must **not be shareable**.
> a Chopstick can be used by only one Philosopher at a time.
- At least one task must be **holding a resource** and **waiting to acquire a resource** currently held by another task.
> for deadlock to occur, a Philosopher must be holding one Chopstick and waiting for another one.
- A resource **cannot be preemptively taken away** from a task. Tasks only **release** resources **as a normal event**.
> Our Philosophers are polite and they don’t grab Chopsticks from other Philosophers.
- A **circular wait** can happen, whereby a task waits on a resource held by another task, which in turn is waiting on a resource held by another task, and so on, until one of the tasks is waiting on a resource **held by the first task**.
> the circular wait happens because each Philosopher tries to get the right Chopstick first and then the left.

This is used to **synchronize one or more tasks** by forcing them to **wait for the completion of** a set of operations being performed by other tasks.

###New library components
---
A **CountDownLatch** is designed to be used in a one-shot fashion; the **count cannot be reset**.
> You give an **initial count** to a CountDownLatch object, and any task that **calls await()** on that object will **block until the count reaches zero**.
> 
> Other tasks may **call countDown()** on the object to **reduce the count**, presumably when a task finishes its job.
> 
> The tasks that call **countDown() are not blocked** when they make that call. Only the call to **await() is blocked** until the count reaches zero.

A typical use is to **divide a problem into n independently solvable tasks** and create a CountDownLatch **with a value of n**.
> When each task is **finished** it calls **countDown()** on the latch.
> 
> Tasks **waiting for the problem** to be solved call **await()** on the latch to hold themselves back until it is completed.

A **CyclicBarrier** is used in situations where you want to **create a group of tasks to perform work in parallel**, and then **wait until they are all finished** before moving on to the next step.
> It **brings all the parallel tasks into alignment at the barrier** so you can move forward in unison.
> 
> A CountDownLatch is a **one-shot event**, whereas a CyclicBarrier can be **reused over and over**.

**DelayQueue** is an **unbounded BlockingQueue** of objects that implement the Delayed interface. An object can **only be taken** from the queue when **its delay has expired**.
> The queue is **sorted** so that the object **at the head** has a delay that has **expired for the longest time**.
> 
> If **no delay has expired**, then there is **no head element** and poll() will return null.

**PriorityBlockingQueue** is basically a **priority queue** that has blocking retrieval operations. A **PrioritizedTask** is given a **priority number** to provide this order.

You don’t have to think about whether the queue has any elements in it when you’re reading from it, because **the queue will simply block** the reader when it is out of elements.

The **ScheduledThreadPoolExecutor** using either **schedule()** (to run a task once) or **scheduleAtFixedRate()** (to repeat a task at a regular interval), you **set up Runnable objects to be executed** at some time in the future.

A **normal lock** (from concurrent.locks or the built-in synchronized lock) only allows **one task at a time** to access a resource. A **counting semaphore** allows **n tasks** to access the resource at the same time.

An **Exchanger** is a **barrier** that **swaps objects between two tasks**. When the tasks enter the barrier, they have one object, and when they leave, they have the object that was formerly held by the other task.

**Exchangers** are typically used **when one task is creating objects that are expensive to produce** and another task is consuming those objects.

When you call the **Exchanger.exchange() method**, it **blocks until the partner task calls its exchange() method**, and when both exchange() methods have completed, the object has been swapped.

###Performance tuning
---
We will only see the **true performance difference** if the mutexes are **under contention**, so there must be **multiple tasks trying to access the mutexed code sections**. We also need to **prevent** the possibility that the compiler can **predict** the outcome. 

![](http://i.imgur.com/bY30he7.png)

It is fairly clear that **using Lock** is usually **significantly more efficient** than using synchronized, and it also appears that the overhead of synchronized varies widely, while Locks are relatively consistent.

There are two factors to consider when **using synchronized keyword**:
> First, the bodies of the mutexed methods are extremely small, only mutex the sections that you absolutely must.
> 
> Second, the synchronized keyword produces much **more readable code**.
> 
> It makes sense to **start with the synchronized keyword** and only change to **Lock objects** when you are **tuning for performance**. 

**Atomic objects** are only useful in very simple cases, generally when you **only have one Atomic object** that’s being modified and when that object is **independent from all other objects**.
> It’s safer to **start with more traditional mutexing approaches** and only attempt to change to Atomic later, if performance requirements dictate.

The general strategy behind these **lock-free containers** is this:
> **Modifications** to the containers can happen **at the same time that reads** are occurring, as long as the readers can **only see the results of completed modifications**.

A **modification** is performed on a **separate copy of a portion** of the data structure, and this copy is **invisible during the modification** process.
> Only when the modification is complete is the **modified structure atomically swapped with the "main" data structure**, and after that readers will see the modification.

In **CopyOnWriteArrayList**, a write will cause a **copy of the entire underlying array** to be created. However, In **ConcurrentHashMap and ConcurrentLinkedQueue**, only **portions of the container** are copied and modified. 
> Readers will still **not see any modifications** before they are complete.
> 
> **CopyOnWriteArraySet** uses CopyOnWriteArrayList to achieve its lock-free behavior.
> 
> They all do **not throw ConcurrentModificationException** when multiple iterators are traversing and modifying.

A **synchronized ArrayList** has roughly the **same performance** regardless of the number of readers and writers — readers contend with other readers for locks in the same way that writers do.

The **CopyOnWriteArrayList** is **dramatically faster** when there are no writers, and is still significantly faster when there are five writers. 

Although Atomic objects perform atomic operations, Atomic classes also allow you to perform what is called "**optimistic locking**."
> You do not actually use a mutex when you are performing a calculation, but after the calculation is finished and you’re ready to **update the Atomic object**, you use a method called **compareAndSet()**.
> 
> If compareAndSet() **fails**, you must **decide what to do**.
> Perhaps you can retry the operation and it will be OK if you get it the second time.
> 
> Or perhaps it’s OK just to ignore the failure.

By using an Atomic instead of synchronized or Lock, you might gain **performance benefits**.

**ReadWriteLocks** optimize the situation where you write to a data structure relatively infrequently, but **multiple tasks read** from it often.
> It allows you to have many readers at one time as long as no one is attempting to write.
> 
> If the write lock is held, then no readers are allowed **until the write lock is released**.

###Active objects
---
With **active objects**, we **serialize messages** rather than methods, which means we no longer need to guard against problems that happen when a task is interrupted midway through its loop.
> The reason the objects are called "active" is that each object **maintains its own worker thread and message queue**, and all requests to that object are **enqueued**, to be run one at a time.
> 
> When you send a message to an active object, that message is **transformed into a task** that goes on the object’s queue to be run at some later point.

In order to inadvertently **prevent coupling** between threads, any arguments to pass to an active-object method call must be
- read-only
- or other active objects
- or objects that have no connection to any other task

With active objects:
- Each object has its **own worker thread**.
- Each object **maintains total control** of its own fields.
> which is somewhat more rigorous than normal classes, which only have the option of guarding their fields.
- All communication between active objects happens **in the form of messages** between those objects.
- All messages between active objects are **enqueued**.

Since a message from one active object to another can **only be blocked by the delay** in enqueuing it, and because that delay is always very short and is **not dependent on any other objects**, the sending of a message is effectively **unblockable**. 

Since an active-object system **only communicates via messages**, two objects **cannot be blocked** while contending to call a method on another object, and this means that **deadlock cannot occur**.

Because the worker thread within an active object **only executes one message at a time**, there is **no resource contention** and you don’t have to worry about synchronizing methods.
> Synchronization still happens, but it **happens on the message level**, by enqueuing the method calls so that only one can happen at a time.
