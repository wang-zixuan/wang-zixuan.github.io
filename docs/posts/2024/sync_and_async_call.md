---
layout: default
title: Synchronous and Asynchronous Call [Java]
parent: 2024
grand_parent: Posts
permalink: /posts/2024/sync_and_async_call
nav_order: 0.0
---

Synchronous and asynchronous call are core concepts in programming, especially when dealing with operations that may have significant differences in execution time, such as I/O operations and network requests. 

### Synchronous calls
In synchronous call, a caller waits for the called function to complete before preceeding to the next line of code. 

Synchronous calls are suitable for scenarios where subsequent operations depend on the result of the previous operation. Below is a simple example of reading a file:

```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

public class SyncFileRead {
    public static void main(String[] args) {
        BufferedReader reader = null;
        try {
            reader = new BufferedReader(new FileReader("example.txt"));
            String line = null;
            while ((line = reader.readLine()) != null) {
                System.out.println(line);
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (reader != null) {
                try {
                    reader.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```

### Asynchronous calls
Asynchronous calls allows the program to continue to execute without waiting for the called function to finish. The completion of the operation is typically handled by callbacks, futures, or promises. There are several approaches to implement asynchronous calls in Java:

#### Threads
We can directly use `Thread` or `Runnable` to create asynchronous calls without blocking the main thread:

```java
public class ThreadExample {
    public static void main(String[] args) {
        Runnable task = () -> {
            System.out.println("Executing task...");
            try {
                Thread.sleep(5000); // simulate operations
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("The task is completed.");
        };

        Thread thread = new Thread(task);
        thread.start();

        System.out.println("Main thread keeps running...");
    }
}
```

Java's executor framework abstracts the manual handling of threads and provides a higher-level interface for executing asynchronous calls. It allows us to manage a pool of threads and execute Runnable or Callable tasks.
```java
import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.*;

public class ExecutorServiceExample {

    public static void main(String[] args) {
        // create ExecutorService using fixed thread pool
        ExecutorService executorService = Executors.newFixedThreadPool(3);

        // create a list of tasks
        List<Callable<Integer>> tasks = new ArrayList<>();
        tasks.add(new Task("Task 1"));
        tasks.add(new Task("Task 2"));
        tasks.add(new Task("Task 3"));

        try {
            // submit tasks and get future lists
            List<Future<Integer>> futures = executorService.invokeAll(tasks);

            // process the results of tasks
            for (Future<Integer> future : futures) {
                try {
                    int result = future.get();
                    System.out.println("Task completed: " + result);
                } catch (InterruptedException | ExecutionException e) {
                    e.printStackTrace();
                }
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        // shut down ExecutorService
        executorService.shutdown();
    }
}

// Callable task with return value
class Task implements Callable<Integer> {
    private final String taskName;

    public Task(String taskName) {
        this.taskName = taskName;
    }

    @Override
    public Integer call() throws Exception {
        for (int i = 1; i <= 5; i++) {
            System.out.println(taskName + " - Step " + i);

            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }

        return 0;
    }
}
```

#### CompletableFuture
`CompletableFuture` is introduced in Java 8, which allows us to write non-blocking asynchronous code by attaching callbacks. Say we now have to get data from API A and API B,

```java
import java.util.concurrent.CompletableFuture;

public class CompletableFutureExample {
    public static void main(String[] args) {
        // asynchronously call API A
        CompletableFuture<String> futureDataA = getDataFromAPIA();

        // asynchronously call API B
        CompletableFuture<String> futureDataB = getDataFromAPIB();

        // .thenRun() will not block the thread
        // .join() is blocking, it will wait until all CompletableFuture objects are completed
        CompletableFuture.allOf(futureDataA, futureDataB).thenRun(() -> {
            String dataA = futureDataA.join();
            String dataB = futureDataB.join();
            processData(dataA, dataB);
        }).join();
    }
}
```