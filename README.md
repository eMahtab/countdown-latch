# Countdown Latch
A synchronization aid that allows one or more threads to wait until a set of operations being performed in other threads completes.

A CountDownLatch is initialized with a given count. The await methods block until the current count reaches zero due to invocations of the countDown() method, after which all waiting threads are released and any subsequent invocations of await return immediately. **This is a one-shot phenomenon -- the count cannot be reset. If you need a version that resets the count, consider using a CyclicBarrier.**

```java
import java.util.concurrent.CountDownLatch;

class Service implements Runnable {
    private String name;
    private CountDownLatch latch;
    public Service(String name, CountDownLatch latch) {
        this.name = name;
        this.latch = latch;
    }

    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName() + " " + name + " is starting...");
        try {
            Thread.sleep((long) (Math.random() * 10000)); // Simulate startup time
            System.out.println(Thread.currentThread().getName() + " " + name + " is up and running.");
            latch.countDown(); // Signal that this service is ready
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}

public class Main {
    public static void main(String[] args) {
        CountDownLatch latch = new CountDownLatch(3);
        new Thread(new Service("Database Service", latch)).start();
        new Thread(new Service("Cache Service", latch)).start();
        new Thread(new Service("Message Queue Service", latch)).start();
        try {
            System.out.println("Main Application: Waiting for services to start...");
            latch.await();
            System.out.println("Main Application: All services are up. Starting the application...");
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

### Execution Output :
```
Main Application: Waiting for services to start...
Thread-0 Database Service is starting...
Thread-2 Message Queue Service is starting...
Thread-1 Cache Service is starting...
Thread-1 Cache Service is up and running.
Thread-0 Database Service is up and running.
Thread-2 Message Queue Service is up and running.
Main Application: All services are up. Starting the application...
```
# References :
https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CountDownLatch.html
