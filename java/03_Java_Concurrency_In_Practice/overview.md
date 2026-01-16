# Java Concurrency in Practice - Overview

## 📖 Thông tin sách

| Thông tin        | Chi tiết                                                                       |
| ---------------- | ------------------------------------------------------------------------------ |
| **Tác giả**      | Brian Goetz, Tim Peierls, Joshua Bloch, Joseph Bowbeer, David Holmes, Doug Lea |
| **Xuất bản**     | 2006                                                                           |
| **Nhà xuất bản** | Addison-Wesley                                                                 |
| **ISBN**         | 978-0-321-34960-6                                                              |
| **Số trang**     | ~400 trang                                                                     |
| **Java version** | Java 5+ (java.util.concurrent)                                                 |

## 🎯 Mục đích

Đây là **sách kinh điển bậc nhất** về lập trình concurrent/multi-threading trong Java. Được viết bởi những người thiết kế chính của `java.util.concurrent` package.

> **Quan trọng**: Dù xuất bản năm 2006, các khái niệm trong sách vẫn **hoàn toàn đúng** và là nền tảng cho mọi ứng dụng concurrent hiện đại.

---

## 👥 Về tác giả (All-Star Team)

| Tác giả            | Vai trò                                          |
| ------------------ | ------------------------------------------------ |
| **Brian Goetz**    | Java Language Architect tại Oracle               |
| **Doug Lea**       | Giáo sư SUNY - Thiết kế java.util.concurrent     |
| **Joshua Bloch**   | Tác giả Effective Java, thiết kế Collections API |
| **Tim Peierls**    | Thành viên JSR-166 Expert Group                  |
| **David Holmes**   | JVM specialist tại Oracle                        |
| **Joseph Bowbeer** | Java concurrency expert                          |

---

## 📚 Cấu trúc sách - 4 Parts, 16 Chapters

---

## PART I: FUNDAMENTALS (Chapters 1-5)
*Nền tảng về Thread Safety*

### Chapter 1: Introduction (Trang 1-10)

| Section | Nội dung                                                    |
| ------- | ----------------------------------------------------------- |
| 1.1     | A (Very) Brief History of Concurrency                       |
| 1.2     | Benefits of Threads (Multi-processors, Modeling, Async, UI) |
| 1.3     | Risks of Threads (Safety, Liveness, Performance)            |
| 1.4     | Threads are Everywhere                                      |

**Key concepts**: Tại sao cần threads, các nguy cơ khi dùng threads

---

### Chapter 2: Thread Safety (Trang 11-22)

| Section | Nội dung                                     |
| ------- | -------------------------------------------- |
| 2.1     | What is Thread Safety?                       |
| 2.2     | Atomicity (Race conditions, Check-then-act)  |
| 2.3     | Locking (Intrinsic locks, Reentrant locking) |
| 2.4     | Guarding State with Locks                    |
| 2.5     | Liveness and Performance                     |

**Key concepts**: 
- **Thread-safe**: Đúng khi được truy cập bởi nhiều threads
- **Atomicity**: Các operation không thể bị interrupted
- **synchronized**: Intrinsic lock mechanism
- **Race condition**: Kết quả phụ thuộc vào timing

---

### Chapter 3: Sharing Objects (Trang 23-36)

| Section | Nội dung                                       |
| ------- | ---------------------------------------------- |
| 3.1     | Visibility (volatile, Memory visibility)       |
| 3.2     | Publication and Escape                         |
| 3.3     | Thread Confinement (Stack, ThreadLocal)        |
| 3.4     | Immutability (Final fields, Immutable objects) |
| 3.5     | Safe Publication                               |

**Key concepts**:
- **Visibility**: Thread A thay đổi, Thread B có thấy không?
- **volatile**: Đảm bảo visibility nhưng không atomicity
- **Thread confinement**: Chỉ 1 thread access object
- **Immutability**: Object không thể thay đổi = thread-safe
- **Safe publication**: Cách publish object an toàn

---

### Chapter 4: Composing Objects (Trang 37-50)

| Section | Nội dung                                             |
| ------- | ---------------------------------------------------- |
| 4.1     | Designing a Thread-safe Class                        |
| 4.2     | Instance Confinement                                 |
| 4.3     | Delegating Thread Safety                             |
| 4.4     | Adding Functionality to Existing Thread-safe Classes |
| 4.5     | Documenting Synchronization Policies                 |

**Key concepts**:
- **Java Monitor Pattern**: Encapsulate mutable state
- **Confinement**: Giới hạn access đến object
- **Delegation**: Delegate thread safety cho internal components
- **@ThreadSafe, @NotThreadSafe, @GuardedBy**: Annotations

---

### Chapter 5: Building Blocks (Trang 51-70)

| Section | Nội dung                                                         |
| ------- | ---------------------------------------------------------------- |
| 5.1     | Synchronized Collections (Vector, Hashtable)                     |
| 5.2     | Concurrent Collections (ConcurrentHashMap, CopyOnWriteArrayList) |
| 5.3     | Blocking Queues and Producer-Consumer Pattern                    |
| 5.4     | Blocking and Interruptible Methods                               |
| 5.5     | Synchronizers (CountDownLatch, Semaphore, Barrier, FutureTask)   |
| 5.6     | Building an Efficient, Scalable Result Cache                     |

**Key concepts**:
- **ConcurrentHashMap**: Thread-safe Map với high concurrency
- **BlockingQueue**: Queue cho producer-consumer
- **CountDownLatch**: Đợi N events xảy ra
- **Semaphore**: Limit concurrent access
- **FutureTask**: Async computation với result

---

## PART II: STRUCTURING CONCURRENT APPLICATIONS (Chapters 6-9)
*Cách cấu trúc ứng dụng concurrent*

### Chapter 6: Task Execution (Trang 72-84)

| Section | Nội dung                        |
| ------- | ------------------------------- |
| 6.1     | Executing Tasks in Threads      |
| 6.2     | The Executor Framework          |
| 6.3     | Finding Exploitable Parallelism |

**Key concepts**:
- **Executor**: Interface để tách task submission khỏi execution
- **ExecutorService**: Lifecycle management cho Executor
- **ThreadPoolExecutor**: Customizable thread pool
- **Callable & Future**: Task với return value
- **CompletionService**: Xử lý results khi chúng complete

---

### Chapter 7: Cancellation and Shutdown (Trang 85-103)

| Section | Nội dung                             |
| ------- | ------------------------------------ |
| 7.1     | Task Cancellation (Interruption)     |
| 7.2     | Stopping a Thread-based Service      |
| 7.3     | Handling Abnormal Thread Termination |
| 7.4     | JVM Shutdown                         |

**Key concepts**:
- **Interruption**: Cooperative cancellation mechanism
- **InterruptedException**: Cách handle đúng
- **shutdown() vs shutdownNow()**: Graceful vs immediate
- **Shutdown hooks**: Clean up khi JVM shutdown

---

### Chapter 8: Applying Thread Pools (Trang 104-116)

| Section | Nội dung                                                |
| ------- | ------------------------------------------------------- |
| 8.1     | Implicit Couplings Between Tasks and Execution Policies |
| 8.2     | Sizing Thread Pools                                     |
| 8.3     | Configuring ThreadPoolExecutor                          |
| 8.4     | Extending ThreadPoolExecutor                            |
| 8.5     | Parallelizing Recursive Algorithms                      |

**Key concepts**:
- **Thread pool sizing**: `N_cpu * U_cpu * (1 + W/C)`
- **Saturation policies**: AbortPolicy, CallerRunsPolicy, DiscardPolicy
- **Thread factory**: Custom thread creation
- **Fork/Join**: Divide and conquer

---

### Chapter 9: GUI Applications (Trang 117-126)

| Section | Nội dung                                  |
| ------- | ----------------------------------------- |
| 9.1     | Why are GUIs Single-threaded?             |
| 9.2     | Short-running GUI Tasks                   |
| 9.3     | Long-running GUI Tasks                    |
| 9.4     | Shared Data Models                        |
| 9.5     | Other Forms of Single-threaded Subsystems |

**Key concepts**: Swing/JavaFX và single-threaded event dispatch

---

## PART III: LIVENESS, PERFORMANCE, AND TESTING (Chapters 10-12)
*Các vấn đề nâng cao*

### Chapter 10: Avoiding Liveness Hazards (Trang 128-136)

| Section | Nội dung                                      |
| ------- | --------------------------------------------- |
| 10.1    | Deadlock (Lock-ordering, Dynamic deadlocks)   |
| 10.2    | Avoiding and Diagnosing Deadlocks             |
| 10.3    | Other Liveness Hazards (Starvation, Livelock) |

**Key concepts**:
- **Deadlock**: 2+ threads chờ nhau forever
- **Lock ordering**: Consistent lock acquisition order
- **Open calls**: Gọi methods không hold lock
- **Starvation**: Thread không bao giờ được resources
- **Livelock**: Threads liên tục retry mà không tiến triển

---

### Chapter 11: Performance and Scalability (Trang 137-152)

| Section | Nội dung                                                     |
| ------- | ------------------------------------------------------------ |
| 11.1    | Thinking about Performance                                   |
| 11.2    | Amdahl's Law                                                 |
| 11.3    | Costs Introduced by Threads (Context switching, Memory sync) |
| 11.4    | Reducing Lock Contention                                     |
| 11.5    | Example: Comparing Map Performance                           |
| 11.6    | Reducing Context Switch Overhead                             |

**Key concepts**:
- **Amdahl's Law**: Speedup giới hạn bởi sequential portion
- **Lock contention**: Nhiều threads chờ 1 lock
- **Lock striping**: Chia 1 lock thành nhiều locks
- **Narrowing lock scope**: Giảm thời gian hold lock

---

### Chapter 12: Testing Concurrent Programs (Trang 153-169)

| Section | Nội dung                              |
| ------- | ------------------------------------- |
| 12.1    | Testing for Correctness               |
| 12.2    | Testing for Performance               |
| 12.3    | Avoiding Performance Testing Pitfalls |
| 12.4    | Complementary Testing Approaches      |

**Key concepts**: Cách test multi-threaded code, timing tests, stress tests

---

## PART IV: ADVANCED TOPICS (Chapters 13-16)
*Chủ đề nâng cao*

### Chapter 13: Explicit Locks (Trang 171-178)

| Section | Nội dung                                        |
| ------- | ----------------------------------------------- |
| 13.1    | Lock and ReentrantLock                          |
| 13.2    | Performance Considerations                      |
| 13.3    | Fairness                                        |
| 13.4    | Choosing Between Synchronized and ReentrantLock |
| 13.5    | Read-write Locks (ReentrantReadWriteLock)       |

**Key concepts**:
- **ReentrantLock**: Explicit lock với more features
- **tryLock()**: Non-blocking lock acquisition
- **ReadWriteLock**: Nhiều readers, 1 writer

---

### Chapter 14: Building Custom Synchronizers (Trang 179-194)

| Section | Nội dung                                         |
| ------- | ------------------------------------------------ |
| 14.1    | Managing State Dependence                        |
| 14.2    | Using Condition Queues (wait/notify)             |
| 14.3    | Explicit Condition Objects                       |
| 14.4    | Anatomy of a Synchronizer                        |
| 14.5    | AbstractQueuedSynchronizer (AQS)                 |
| 14.6    | AQS in java.util.concurrent Synchronizer Classes |

**Key concepts**:
- **Condition**: wait/notify với explicit locks
- **AQS**: Framework để build synchronizers (ReentrantLock, Semaphore dùng AQS)

---

### Chapter 15: Atomic Variables and Non-blocking Synchronization (Trang 195-206)

| Section | Nội dung                                                    |
| ------- | ----------------------------------------------------------- |
| 15.1    | Disadvantages of Locking                                    |
| 15.2    | Hardware Support for Concurrency (CAS - Compare-and-Swap)   |
| 15.3    | Atomic Variable Classes (AtomicInteger, AtomicReference...) |
| 15.4    | Non-blocking Algorithms                                     |

**Key concepts**:
- **CAS (Compare-and-Swap)**: Hardware instruction cho atomic update
- **AtomicInteger, AtomicLong, AtomicReference**: Lock-free atomic variables
- **Non-blocking algorithms**: Algorithms không dùng locks

---

### Chapter 16: The Java Memory Model (Trang 207-215)

| Section | Nội dung                                              |
| ------- | ----------------------------------------------------- |
| 16.1    | What is a Memory Model, and Why would I Want One?     |
| 16.2    | Publication (happens-before, Safe publication idioms) |

**Key concepts**:
- **Java Memory Model (JMM)**: Đảm bảo visibility và ordering
- **happens-before**: Relationship đảm bảo visibility
- **final fields**: Đặc biệt trong safe publication
- **Double-checked locking**: Cách làm đúng với volatile

---

## 🔥 Top 10 Concepts quan trọng nhất

| #   | Concept                   | Chapter | Mô tả                           |
| --- | ------------------------- | ------- | ------------------------------- |
| 1   | **Thread Safety**         | 2       | Hiểu thế nào là thread-safe     |
| 2   | **Visibility (volatile)** | 3       | Memory visibility giữa threads  |
| 3   | **Immutability**          | 3       | Immutable objects = thread-safe |
| 4   | **Executor Framework**    | 6       | Tách task khỏi execution        |
| 5   | **Blocking Queues**       | 5       | Producer-consumer pattern       |
| 6   | **Deadlock**              | 10      | Hiểu và tránh deadlock          |
| 7   | **Lock Contention**       | 11      | Performance với locks           |
| 8   | **ReentrantLock**         | 13      | Explicit locks với more control |
| 9   | **Atomic Variables**      | 15      | Lock-free programming           |
| 10  | **Java Memory Model**     | 16      | happens-before relationship     |

---

## 📋 java.util.concurrent Classes được cover

| Category          | Classes                                                   |
| ----------------- | --------------------------------------------------------- |
| **Collections**   | ConcurrentHashMap, CopyOnWriteArrayList, BlockingQueue    |
| **Executors**     | ThreadPoolExecutor, ScheduledExecutorService, Executors   |
| **Synchronizers** | CountDownLatch, Semaphore, CyclicBarrier, Exchanger       |
| **Locks**         | ReentrantLock, ReentrantReadWriteLock, Condition          |
| **Atomics**       | AtomicInteger, AtomicLong, AtomicReference, AtomicBoolean |
| **Utilities**     | Future, FutureTask, CompletionService                     |

---

## 💡 Ai nên đọc cuốn này?

| Đối tượng             | Lợi ích                        |
| --------------------- | ------------------------------ |
| Java developers       | Hiểu multi-threading đúng cách |
| Backend developers    | Xử lý concurrent requests      |
| Performance engineers | Optimize multi-threaded code   |
| Architects            | Design concurrent systems      |

---

## ⚠️ Lưu ý quan trọng

1. **Sách năm 2006 nhưng vẫn còn giá trị** - Các concepts về concurrency không thay đổi
2. **Java 5+ centric** - Focuses on java.util.concurrent
3. **Không cover Java 8+ features** - CompletableFuture, Streams parallel không có
4. **Nặng lý thuyết** - Cần đọc kỹ và thực hành

---

## 📝 Ghi chú học tập

> Phần này để ghi chú trong quá trình học

### Part I: Fundamentals
- [ ] Chapter 1: Introduction
- [ ] Chapter 2: Thread Safety
- [ ] Chapter 3: Sharing Objects
- [ ] Chapter 4: Composing Objects
- [ ] Chapter 5: Building Blocks

### Part II: Structuring Concurrent Applications
- [ ] Chapter 6: Task Execution
- [ ] Chapter 7: Cancellation and Shutdown
- [ ] Chapter 8: Applying Thread Pools
- [ ] Chapter 9: GUI Applications

### Part III: Liveness, Performance, and Testing
- [ ] Chapter 10: Avoiding Liveness Hazards
- [ ] Chapter 11: Performance and Scalability
- [ ] Chapter 12: Testing Concurrent Programs

### Part IV: Advanced Topics
- [ ] Chapter 13: Explicit Locks
- [ ] Chapter 14: Building Custom Synchronizers
- [ ] Chapter 15: Atomic Variables and Non-blocking Synchronization
- [ ] Chapter 16: The Java Memory Model
