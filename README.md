# Countdown Latch


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
