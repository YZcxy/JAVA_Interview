# Jave Util Concurrent

Q：什么是Java AQS？  
A：AQS (AbstractQueuedSynchronizer ) 使用一个 FIFO 的队列表示排队等待锁的线程，队列头节点称作“哨兵节点”或者“哑节点”，它不与任何线程关联。其他的节点与等待线程关联，每个节点维护一个等待状态 waitStatus 。它是构建锁或者其他同步组件的基础框架（如 ReentrantLock、ReentrantReadWriteLock、Semaphore 等）  
***
Q：AQS 如何表示同步状态？  
A：AQS 的主要使用方式是**继承**（其他工具类一般都拥有一个子类（Sync）继承着AbstractQueuedSynchronizer），子类通过继承同步器，并实现它的抽象方法来管理同步状态。  
AQS 使用一个 int 类型的成员变量 state 来表示同步状态：  
- 当 state > 0 时，表示已经获取了锁。
- 当 state = 0 时，表示释放了锁。
它提供了三个方法，来对同步状态 state 进行操作，并且 AQS 可以确保对 state 的操作是安全的：  
- `getState()` 返回同步状态的当前值。
- `setState(int newState)` 设置当前同步状态。
- `compareAndSetState(int expect, int update)` 使用 CAS 设置当前状态，该方法能够保证状态设置的原子性。
***
Q：AQS 的同步队列是怎样的？  
A：AQS 通过内置的 FIFO 同步队列（CLH）来完成资源获取线程的排队工作：  
- 如果当前线程获取同步状态失败（锁）时，AQS 则会将当前线程以及等待状态等信息构造成一个节点（Node）并将其加入 CLH 同步队列，同时会阻塞当前线程
- 当同步状态释放时，则会把首节点中的线程唤醒（公平锁），使其再次尝试获取同步状态。
***
Q：CLH 队列中保存着什么？  
A：CLH 同步队列中存储着首节点和尾节点，每一个节点（Node）就代表着一个线程，它保存着线程的引用（thread）、状态（waitStatus）、前驱节点（prev）、后继节点（next）。  
![](https://gitee.com/chenssy/blog-home/raw/master/image/sijava/2018120810001.png)
***
Q：AQS 中已经实现了哪些功能？  
A：对于子类而言，它并没有太多的活要做，AQS 已经提供了大量的模板方法来实现同步，主要是分为三类：  
- 独占式获取和释放同步状态。
- 共享式获取和释放同步状态。
- 查询同步队列中的等待线程情况。
***
Q：什么是 Java Lock 接口？  
A：`java.util.concurrent.locks.Lock` 接口，比 `synchronized` 提供更具拓展行的锁操作。它允许更灵活的结构，可以具有完全不同的性质，并且可以支持多个相关类的条件对象。它的优势有：  
- 可以使锁更公平。
- 可以使线程在等待锁的时候响应中断。
- 可以让线程尝试获取锁，并在无法获取锁的时候立即返回或者等待一段时间。
- 可以在不同的范围，以不同的顺序获取和释放锁。
***
Q：什么是可重入锁（ReentrantLock）？  
A：可重入就意味着：线程可以进入任何一个它已经拥有的锁所同步着的代码块。`synchronized、ReentrantLock` 都是可重入的锁，可重入锁相对来说简化了并发编程的开发。
***
Q：synchronized 和 ReentrantLock 异同？  
A：如下：  
- 相同点
  - 都实现了多线程同步和内存可见性。
  - 都是可重入锁。
- 不同点
  - 同步实现机制不同：`synchronized` 通过 Java 对象头锁标记和 Monitor 对象实现同步。`ReentrantLock` 通过CAS、AQS（AbstractQueuedSynchronizer）和 LockSupport（用于阻塞和解除阻塞）实现同步。
  - 可见性实现机制不同：`synchronized` 依赖 JVM 内存模型保证包含共享变量的多线程内存可见性。`ReentrantLock` 通过 ASQ 的 volatile state 保证包含共享变量的多线程内存可见性。
  - 使用方式不同：`synchronized` 可以修饰实例方法（锁住实例对象）、静态方法（锁住类对象）、代码块（显示指定锁对象）。`ReentrantLock` 显示调用 tryLock 和 lock 方法，需要在 finally 块中释放锁。
  - 功能丰富程度不同：`synchronized` 不可设置等待时间、不可被中断（interrupted）。`ReentrantLock` 提供有限时间等候锁（设置过期时间）、可中断锁（lockInterruptibly）、condition（提供 await、condition（提供 await、signal 等方法）等丰富功能。
  - 锁类型不同：`synchronized` 只支持非公平锁。`ReentrantLock` 提供公平锁和非公平锁实现。当然，在大部分情况下，非公平锁是高效的选择。
***
Q：ReadWriteLock 是什么？  
A：ReadWriteLock ，读写锁是，用来提升并发程序性能的锁分离技术的 Lock 实现类。可以用于 “多读少写” 的场景，读写锁支持多个读操作并发执行，写操作只能由一个线程来操作。
***
Q：Condition 是什么？  
A：在没有 Lock 之前，我们使用 `synchronized` 来控制同步，配合 Object 的 `wait()、notify()` 等一系列方法可以实现等待 / 通知模式。Lock 提供了条件 `Condition` ，对线程的等待、唤醒操作更加详细和灵活。  
***
Q：Condition 和 Object 监视器的区别？  
A：区别如下：  
![qb](http://static.iocoder.cn/e7e7bb0837bbe68a4364366d4ec9c5db)
***
Q：同步容器和并发容器的区别？  
A：通过 `synchronized` 来实现同步的叫做 同步容器，如 `Vector、Hashtable、synchronizedList` 等等。并发容器使用了与之完全不同的加锁策略，提供了更高的并发性和伸缩性，如 `ConcurrentHashMap、CopyOnWriteArrayList`。  
***
Q：ConcurrentHashMap 的并发度是什么？  
A：JDK8之前，采用分段锁(Segment)的概念，将 map 分为一个一个的桶来实现加锁。JDK8之后采用 CAS 算法，同时加入了更多的辅助变量来提高并发度。  
***
Q：CopyOnWriteArrayList 可以用于什么应用场景？  
A：CopyOnWriteArrayList(免锁容器)的好处之一是当多个迭代器同时遍历和修改这个列表时，不会抛出 `ConcurrentModificationException` 异常。在 CopyOnWriteArrayList 中，写入将导致创建整个底层数组的副本，而源数组将保留在原地，使得复制的数组在被修改时，读取操作可以安全地执行。  
***
Q：什么是阻塞队列？  
A：BlockingQueue 接口，是 Queue 的子接口，它的主要用途并不是作为容器，而是作为线程同步的的工具，因此他具有一个很明显的特性：  
- 当生产者线程试图向 BlockingQueue 放入元素时，如果队列已满，则线程被阻塞。  
- 当消费者线程试图从中取出一个元素时，如果队列为空，则该线程会被阻塞。  
- 正是因为它所具有这个特性，所以在程序中多个线程交替向BlockingQueue中 放入元素，取出元素，它可以很好的控制线程之间的通信。  
阻塞队列使用最经典的场景，就是 Socket 客户端数据的读取和解析。  
***
Q：Java 提供了哪些阻塞队列？  
A：一共有下面七种实现：  
- 【最常用】ArrayBlockingQueue ：一个由数组结构组成的有界阻塞队列。
- LinkedBlockingQueue ：一个由链表结构组成的有界阻塞队列。
- PriorityBlockingQueue ：一个支持优先级排序的无界阻塞队列。
- DelayQueue：支持延时获取元素的无界阻塞队列，即可以指定多久才能从队列中获取当前元素。
- SynchronousQueue：一个不存储元素的阻塞队列。
- LinkedTransferQueue：一个由链表结构组成的无界阻塞队列。
- LinkedBlockingDeque：一个由链表结构组成的双向阻塞队列。
***
Q：什么是双端队列？  
A：ArrayDeque、LinkedBlockingDeque 就是双端队列，类名以 Deque 结尾。其特点如下：  
- 正如阻塞队列适用于生产者消费者模式，双端队列同样适用与另一种模式，即工作密取。在生产者-消费者设计中，所有消费者共享一个工作队列，而在工作密取中，每个消费者都有各自的双端队列。
- 如果一个消费者完成了自己双端队列中的全部工作，那么他就可以从其他消费者的双端队列末尾秘密的获取工作。具有更好的可伸缩性，这是因为工作者线程不会在单个共享的任务队列上发生竞争。
- 在大多数时候，他们都只是访问自己的双端队列，从而极大的减少了竞争。当工作者线程需要访问另一个队列时，它会从队列的尾部而不是头部获取工作，因此进一步降低了队列上的竞争。
适用于网页爬虫任务。
***
Q：什么是原子操作？  
A：原子操作（Atomic Operation），意为”不可被中断的一个或一系列操作”。   
- 处理器使用基于对缓存加锁或总线加锁的方式，来实现多处理器之间的原子操作。
- 在 Java 中，可以通过锁和循环 CAS 的方式来实现原子操作。
***
Q：Java 提供了哪些原子类？  
A：如下：  
- 原子类：AtomicBoolean，AtomicInteger，AtomicLong，AtomicReference 。
- 原子数组：AtomicIntegerArray，AtomicLongArray，AtomicReferenceArray 。
- 原子属性更新器：AtomicLongFieldUpdater，AtomicIntegerFieldUpdater，AtomicReferenceFieldUpdater 。
- 解决 ABA 问题的原子类：AtomicMarkableReference，AtomicStampedReference。
***
Q：CAS 操作有什么缺点？
A：有以下三个缺点：  
1. ABA 问题：第一个线程读取了数据 A，第二个线程读取了数据 A，然后修改成 B 之后再修改回了 A，这时候第一个线程发现数据还是 A，觉得没问题，操作成功。  
2. 循环时间长：对于资源竞争严重的情况，CAS 自旋的概率比较大，浪费了很多 CPU 资源。效率低于 `synchronized`。
3. 只能保证一个共享变量的原子操作：CAS 只能保证一个共享变量的原子性，如果有有个共享变量，就只能用锁了。  
***
Q：Semaphore 是什么？  
A：Semaphore ，是一种新的同步类，它是一个计数信号。从概念上讲，信号量维护了一个许可集合。  
- 如有必要，在许可可用前会阻塞每一个 #acquire() 方法，然后再获取该许可。
- 每个 #release() 方法，添加一个许可，从而可能释放一个正在阻塞的获取者。
- 但是，不使用实际的许可对象，Semaphore 只对可用许可的数量进行计数，并采取相应的行动。
***
Q：CountDownLatch 原理是什么？  
A：CountDownLatch ，字面意思是减小计数（CountDown）的门闩（Latch）。它要做的事情是，等待指定数量的计数被减少，意味着门闩被打开，然后进行执行。
***
Q：CyclicBarrier 原理是什么？  
A：CyclicBarrier ，字面意思是可循环使用（Cyclic）的屏障（Barrier）。它要做的事情是，让一组线程到达一个屏障（也可以叫同步点）时被阻塞，直到最后一个线程到达屏障时，屏障才会开门，所有被屏障拦截的线程才会继续干活。
***
Q：CyclicBarrier 和 CountdownLatch 有什么区别？  
A：CountDownLatch 其实可以把它看作一个计数器，只不过这个计数器的操作是原子操作。CyclicBarrier 一个同步辅助类，它允许一组线程互相等待，直到到达某个公共屏障点 (common barrier point)。在涉及一组固定大小的线程的程序中，这些线程必须不时地互相等待，此时 CyclicBarrier 很有用。  

| CountDownLatch                                               | CyclicBarrier                                                |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 减计数方式                                                   | 加计数方式                                                   |
| 计算为 0 时释放所有等待的线程                                | 计数达到指定值时释放所有等待线程                             |
| 计数为 0 时，无法重置                                        | 计数达到指定值时，计数置为 0 重新开始                        |
| 调用 `#countDown()` 方法计数减一，调用 `#await()` 方法只进行阻塞，对计数没任何影响 | 调用 `#await()` 方法计数加 1 ，若加 1 后的值不等于构造方法的值，则线程阻塞 |
| 不可重复利用                                                 | 可重复利用                                                   |

***
Q：什么是 Executor 框架？  
A：Executor 框架，是一个根据一组执行策略调用，调度，执行和控制的异步任务的框架。  
***
Q：为什么使用 Executor 框架？  
A：有以下原因：  
1. 每次执行任务创建线程 new Thread() 比较消耗性能，创建一个线程是比较耗时、耗资源的。
2. 调用 new Thread() 创建的线程缺乏管理，被称为野线程，而且可以无限制的创建，线程之间的相互竞争会导致过多占用系统资源而导致系统瘫痪，还有线程之间的频繁交替也会消耗很多系统资源。
3. 接使用 new Thread() 启动的线程不利于扩展，比如定时执行、定期执行、定时定期执行、线程中断等都不便实现。
***
Q：创建线程池的几种方式？  
A：Executors 创建的线程池，分成普通任务线程池，和定时任务线程池。  
- 普通任务线程池
  1. `newFixedThreadPool(int nThreads)` 方法，创建一个固定长度的线程池。
每当提交一个任务就创建一个线程，直到达到线程池的最大数量，这时线程规模将不再变化。
  2. `newCachedThreadPool()` 方法，创建一个可缓存的线程池。
  3. `newSingleThreadExecutor()` 方法，创建一个单线程的线程池。
- 定时任务线程池
  1. `newScheduledThreadPool(int corePoolSize)` 方法，创建了一个固定长度的线程池，而且以延迟或定时的方式来执行任务，类似 Timer 。
  2. `newSingleThreadExecutor()` 方法，创建了一个固定长度为 1 的线程池，而且以延迟或定时的方式来执行任务，类似 Timer 。
***
Q：ThreadPoolExecutor 如何使用？  
A：Executors 提供了创建线程池的常用模板，实际场景下，我们可能需要自动以更灵活的线程池，此时就需要使用 ThreadPoolExecutor 类。  
- corePoolSize 参数，核心线程数大小，当线程数 < corePoolSize ，会创建线程执行任务。
- maximumPoolSize 参数，最大线程数， 当线程数 >= corePoolSize 的时候，会把任务放入 workQueue 队列中。
- keepAliveTime 参数，保持存活时间，当线程数大于 corePoolSize 的空闲线程能保持的最大时间。
- unit 参数，时间单位。
- workQueue 参数，保存任务的阻塞队列。
- handler 参数，超过阻塞队列的大小时，使用的拒绝策略。
- threadFactory 参数，创建线程的工厂。
***
Q：ThreadPoolExecutor 有哪些拒绝策略？  
A：默认有以下四个拒绝策略：  
- ThreadPoolExecutor.AbortPolicy() ，直接抛出异常 RejectedExecutionException 。
- ThreadPoolExecutor.CallerRunsPolicy() ，直接调用 run 方法并且阻塞执行。
- ThreadPoolExecutor.DiscardPolicy() ，直接丢弃后来的任务。
- ThreadPoolExecutor.DiscardOldestPolicy() ，丢弃在队列中队首的任务。
如果我们有需要，可以自己实现 `RejectedExecutionHandler` 接口，实现自定义的拒绝逻辑。
***
Q：线程池的关闭方式有几种？  
A：ThreadPoolExecutor 提供了两个方法，用于线程池的关闭，分别是：  
- shutdown() 方法，不会立即终止线程池，而是要等所有任务缓存队列中的任务都执行完后才终止，但再也不会接受新的任务。
- shutdownNow() 方法，立即终止线程池，并尝试打断正在执行的任务，并且清空任务缓存队列，返回尚未执行的任务。
***
Q：Java 线程池大小为何会大多被设置成 CPU 核心数 +1 ？  
A：一般说来，大家认为线程池的大小经验值应该这样设置：  
- 如果是 CPU 密集型应用，则线程池大小设置为 N+1
- 如果是 IO 密集型应用，则线程池大小设置为 2N+1
- 如果是混合型应用，那么分别创建线程池
***
Q：什么是 Callable、Future、FutureTask ？  
A：如下：  
1. Callable 接口，类似于 Runnable ，从名字就可以看出来了，但是Runnable 不会返回结果，并且无法抛出返回结果的异常，而 Callable 功能更强大一些，被线程执行后，可以返回值，这个返回值可以被 Future 拿到，也就是说，Future 可以拿到异步执行任务的返回值。
2. Future 接口，表示异步任务，是还没有完成的任务给出的未来结果。所以说 Callable 用于产生结果，Future 用于获取结果。
3. 在 Java 并发程序中，FutureTask 表示一个可以取消的异步运算。
***
Q：线程池执行任务的过程？  
A：过程如下：  
1. 如果正在运行的线程数量小于核心参数 corePoolSize ，继续创建线程运行这个任务
  - 否则，如果正在运行的线程数量大于或等于 corePoolSize ，将任务加入到阻塞队列中。
  - 否则，如果队列已满，同时正在运行的线程数量小于核心参数 maximumPoolSize ，继续创建线程运行这个任务。
  - 否则，如果队列已满，同时正在运行的线程数量大于或等于 maximumPoolSize ，根据设置的拒绝策略处理。
2. 完成一个任务，继续取下一个任务处理。
  - 没有任务继续处理，线程被中断或者线程池被关闭时，线程退出执行，如果线程池被关闭，线程结束。
  - 否则，判断线程池正在运行的线程数量是否大于核心线程数，如果是，线程结束，否则线程阻塞。因此线程池任务全部执行完成后，继续留存的线程池大小为 corePoolSize 。
***
Q：线程池中 submit 和 execute 方法有什么区别？  
A：两个方法都可以向线程池提交任务。execute 方法，返回类型是 void，submit 方法，可以返回持有计算结果的 Future 对象。
***
