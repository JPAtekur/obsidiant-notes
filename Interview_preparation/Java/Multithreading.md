
## ✅ 1. What is Multithreading in Java?

Multithreading allows **concurrent execution** of two or more threads (lightweight sub-processes), enabling better CPU utilization and performance, especially in I/O or CPU-bound operations.

## ✅ 2. Creating Threads in Java

### 🔹 Method 1: Extend `Thread` class
```
class MyThread extends Thread {
    public void run() {
        System.out.println("Thread is running...");
    }
}
MyThread t = new MyThread();
t.start();
```

🔹 Method 2: Implement `Runnable` interface
```
class MyRunnable implements Runnable {
    public void run() {
        System.out.println("Thread is running...");
    }
}
Thread t = new Thread(new MyRunnable());
t.start();
```
✅ Prefer `Runnable` over `Thread` for flexibility (supports multiple inheritance).

✅ 3. Thread Lifecycle

|State|Description|
|---|---|
|**New**|Thread created but not started|
|**Runnable**|After calling `start()`, ready to run|
|**Running**|Executing its `run()` method|
|**Blocked/Waiting**|Waiting for resources or signal|
|**Terminated**|Thread completes or is interrupted|

✅ 4. Thread Methods

|Method|Description|
|---|---|
|`start()`|Begins execution|
|`run()`|Contains the code executed by the thread|
|`sleep(ms)`|Pauses the thread|
|`join()`|Waits for a thread to die|
|`interrupt()`|Interrupts a thread|
|`isAlive()`|Checks if thread is alive|
## ✅ 5. Thread Synchronization

Used to avoid **race conditions** in multithreaded environments.

### 🔹 `synchronized` block
```
synchronized(obj) {
    // only one thread at a time
}
```
🔹 `synchronized` method
```
public synchronized void increment() {
    count++;
}
```

## ✅ 6. Inter-thread Communication

Use `wait()`, `notify()`, and `notifyAll()` to coordinate between threads.
```
synchronized(obj) {
    obj.wait();     // waits and releases the lock
    obj.notify();   // notifies one waiting thread
}
```

✅ 7. Thread Priorities
```
thread.setPriority(Thread.MAX_PRIORITY); // 10
```
But priority behavior is **platform dependent** and not always reliable.

## ✅ 8. Concurrency Utilities (`java.util.concurrent`)

| Class/Interface       | Use Case                       |
| --------------------- | ------------------------------ |
| `ExecutorService`     | Thread pool management         |
| `Callable` + `Future` | Return result from thread      |
| `CountDownLatch`      | Wait for threads to finish     |
| `Semaphore`           | Limit access to resources      |
| `ReentrantLock`       | Advanced locking mechanism     |
| `ConcurrentHashMap`   | Thread-safe map                |
| `BlockingQueue`       | Thread-safe queue with waiting |
## ✅ 9. Daemon Threads

Runs in background (e.g., GC thread). Dies when main thread finishes.
```
Thread t = new Thread(...);
t.setDaemon(true);
```

## ✅ 10. Volatile Keyword

Ensures visibility of changes to variables across threads.

## ✅ 10. Volatile Keyword

Ensures visibility of changes to variables across threads.
```
volatile boolean flag = true;
```
✅ Doesn’t guarantee atomicity.


## ForkJoinPool

✅ What is `ForkJoinPool`?
`ForkJoinPool` is a **specialized thread pool** introduced in Java 7 to support **parallelism** using the **fork/join** framework.

It is optimized for **divide-and-conquer** algorithms and uses a **work-stealing** technique to efficiently manage threads.

## ✅ Key Concepts

| Concept         | Description                                 |
| --------------- | ------------------------------------------- |
| Fork            | Split a task into subtasks                  |
| Join            | Combine results from subtasks               |
| Work-stealing   | Idle threads "steal" work from busy threads |
| RecursiveTask   | A task that returns a result                |
| RecursiveAction | A task that does not return a result        |
## ✅ Use Case

- Large computations (e.g., array summation, sorting)
    
- Divide-and-conquer problems (e.g., parallel mergesort)
    
- Tasks that can be broken down into smaller independent subtasks

✅ Example: Recursive Sum using `RecursiveTask`

``` java
import java.util.concurrent.*;

class SumTask extends RecursiveTask<Integer> {
    int[] arr;
    int start, end;

    public SumTask(int[] arr, int start, int end) {
        this.arr = arr;
        this.start = start;
        this.end = end;
    }

    @Override
    protected Integer compute() {
        if (end - start <= 5) {
            int sum = 0;
            for (int i = start; i < end; i++) sum += arr[i];
            return sum;
        } else {
            int mid = (start + end) / 2;
            SumTask left = new SumTask(arr, start, mid);
            SumTask right = new SumTask(arr, mid, end);
            left.fork(); // asynchronously execute
            int rightResult = right.compute(); // compute right task
            int leftResult = left.join(); // wait for left result
            return leftResult + rightResult;
        }
    }
}
```

💡 Execution:

```java
public class ForkJoinExample {
    public static void main(String[] args) {
        int[] numbers = new int[20];
        for (int i = 0; i < numbers.length; i++) numbers[i] = i + 1;

        ForkJoinPool pool = new ForkJoinPool();
        int result = pool.invoke(new SumTask(numbers, 0, numbers.length));
        System.out.println("Sum: " + result);
    }
}
```

## ✅ Advantages

- Automatically manages a pool of worker threads
    
- Optimized for recursive, short-running tasks
    
- Reduces thread overhead compared to manual thread creation
    
- Work-stealing improves load balancing

