# Synchronization
- To prevent **race condition**(when 2 or more thread trying to read/write same object/variable) while dealing with multiple threads.
**Monitor:** Key which is used to synchronized the method.

## Synchronized Type
**a. Instance Block:** sysnchronized block is used to define some peace of code. it will block threads to execute specific code of method rather then locking the entire method.
```java 
    public void doSomething(){..
	 synchronized(this){
	 
	 }..
	}
```
**b. Instance Method Level:** instance/object will be used to lock the synchronized methods. only one thread can access all synchronized methods at a time.
**Ex:** if a class holding 4 instnace methods and all are synchronized then only current thread which is holding lock can access anyof this methods.
```java
	public synchronized void doSomething(){
	 ..
	}
```	
**c. Static Method Block:** it is at class level. so in such case thread will use class as a monitor to prevent other threads to enter into critical area.
```java
	public static void doSomething(){
	 ..
	 synchronized(ABC.class){
	 
	 }
	 ..
	}
```
**d. Static Method:** it is at class level. so in such case thread will use class as a monitor to prevent other threads to enter into critical area.
```java
	public static synchronized void doSomething(){
	 ..
	}
```	
### Reentrant Synchronization
Recall that a thread cannot acquire a lock owned by another thread. But a thread can acquire a lock that it already owns. Allowing a thread to acquire the same lock more than once enables reentrant synchronization. This describes a situation where synchronized code, directly or indirectly, invokes a method that also contains synchronized code, and both sets of code use the same lock. Without reentrant synchronization, synchronized code would have to take many additional precautions to avoid having a thread cause itself to block.
## Highlights:
1. Class level lock is using Class object of that class. so anything bound to the class wll be locked if any thread is accessing any Static methods of that class. even though there are 100 objects availale, but only one thread can access synchronized static method. 
2. it is always recomanded to use indipendent object to lock synchronized methods, It's because you will have better control on code when there are multiple sync methods in same class and they are not depend on each other. in such case other threads will be indipendent to access other sync methods even one is locked by one thread.
**Ex:**
```java
public class Test {
 private final Object lock1 = new Object();
 private final Object lock2 = new Object();
	void test1(){
		synchronized(lock1){
			i+=1;
		}
	}
	
	void test2(){
		synchronized(lock2){
			i+=1;
		}
	}
}
```
#### In below example there are 2 instance and 2 static methods. so at a time class level between thread 1 or 2 and instnace level between 3 or 4 can be executed.
**Note:** There will not be any lock on static data members. it will be shared in between static and instance methods.
```java
public class SynchronizedExam {
	static int j = 10;

	final static Object lock = new Object();
	final Object lock1 = new Object();

	void thirdThread() {
		System.out.println(Thread.currentThread().getName());
		synchronized (lock1) {
			while (j > 0)
				System.out.println(Thread.currentThread().getName() + ":j:" + j--);
		}
	}

	void fourthThread() {
		System.out.println(Thread.currentThread().getName());
		synchronized (lock1) {
			while (j > 0)
				System.out.println(Thread.currentThread().getName() + ":j:" + j--);
		}
	}

	static void secondThread() {
		synchronized (lock) {
			while (j > 0)
				System.out.println(Thread.currentThread().getName() + ":j:" + j--);
		}
	}

	static void mainThread() {
		System.out.println(Thread.currentThread().getName());
		synchronized (lock) {
			while (j > 0)
				System.out.println(Thread.currentThread().getName() + ":j:" + j--);
		}
	}

	public static void main(String[] args) {
		Thread t1 = new Thread(() -> {
			mainThread();
		}, "1");

		Thread t2 = new Thread(() -> {
			secondThread();
		}, "2");
		SynchronizedExam tExam = new SynchronizedExam();

		Thread t3 = new Thread(() -> {
			tExam.thirdThread();
		}, "3");

		Thread t4 = new Thread(() -> {
			tExam.fourthThread();
		}, "4");

		t1.start();
		t2.start();
		t3.start();
		t4.start();
	}
}

```
### Deadlock
> In below example there are 2 thread waiting for each other in to
> finish the task. while there are 2 keys are in deadlock condition.
```java
public class DeadlockTest {
	static int i = 5;
	static Object k1 = new Object();
	static Object k2 = new Object();
	static void m1() {
		synchronized (k1) {
			while (i > 0)
				System.out.println(Thread.currentThread().getName() + ":" + i--);
			m2();
		}
	}
	static void m2() {
		synchronized (k2) {
			while (i > 0)
				System.out.println(Thread.currentThread().getName() + ":" + i--);
			m1();
		}
	}
	public static void main(String[] args) {
		Thread t1 = new Thread(() -> {
			m1();
		}, "t1");
		Thread t2 = new Thread(() -> {
			m2();
		}, "t2");
		t1.start();
		t2.start();
	}
}
```
### Object Class Methods
----
Below methods are part of Object class as they are directly linked to object and locking. When a thread is taking lock over object, in such case Object can push thread back to in waiting pool and get the lock Key back. Also once Object will have key in hand can notify to share the key with other waiting threads. 

###### wait();
- wait will release the lock(object) key from current thread and move the thread into wait pool.
- object only can invoke the wait method to release the lock from current thread.
- wait can be used only in synchronized block or method. It is because practically it will make scence to move any thread in wait condition if other thread waiting to accure the current lock.

###### notify();
- As wait method, lock object can give signal to jvm to notify the waiting threads.
- in case of notify only one thread from waitpool(waiting for the same lock/mutex) will be notified based on algorithm.

###### notifyAll();
- notifyAll will invoke all the waiting threads from wait pool and only one thread will get opportunity to accure the lock on object.

### Thread Class Methods
----
###### sleep();
- sleep is part of thread class and it will make sure tomake current thread sleep for period of time.
- while thread is in sleep condition, no lock will be released and post sleep duration same thread will continue with execution.
```java
	TimeUnit.Seconds.sleep(2);
```
###### join();
- part of Thread class. and will makesure to wait other threads to complete the current thread task.
-Ex: if t1 thread part of main thread and we are using t1.join(). main thread has to wait for its exection untill t1 is not completing its task.

```
in below example, t1 and main thread will untill t2 is not completing its exection.
```

```java
package prac.thread.concurrency;

import java.util.concurrent.TimeUnit;

public class JoinExam {
	final static Object obj = new Object();
	static int count = 0;

	public static void main(String[] args) throws InterruptedException {

		Thread t = new Thread(() -> {
			synchronized (obj) {
				for (int i = 0; i < 15; i++) {
					count++;
					System.out.println(Thread.currentThread().getName() + ":count:" + count);

				}
			}
		}, "1");

		Thread t2 = new Thread(() -> {
			synchronized (obj) {
				for (int i = 0; i < 5; i++) {
					count++;
					try {
						TimeUnit.SECONDS.sleep(1);
					} catch (InterruptedException e) {
						e.printStackTrace();
					}
					System.out.println(Thread.currentThread().getName() + ":count:" + count);
				}
			}

		}, "2");
		t2.start();
		t.start();

		// t.join();
		t2.join();

		System.out.println(Thread.currentThread().getName() + ":count:" + count);
	}
}
```

###### yeild();
- yeild is part of thread class. and indicating jvm to push current running thread into waiting condition.
- in case there are any high priority threads available in runnable pool, jvm will give cpu to task exection. or else jvm will give chance to same or other same priority thread.


#### volatile keyword
- Volatile keyword is used to modify the value of a variable by different threads.
- The volatile keyword can be used either with primitive type or objects. The volatile keyword cannot be used with classes or methods.
- In case of multicore cpu, The volatile keyword does not cache the value of the variable and always read the variable from the main memory.

#### TimeUnit enum:
- Mainly used to convert or specify time in specific units like seconds, nano, mili or so on. 
- In context of Threads it can be used to give instruction in different time units to sleep, or wait the thread.

```java
try {
	TimeUnit.SECONDS.sleep(1);
} catch (InterruptedException e) {
	e.printStackTrace();
}
```

# Different types of Lock in Java
1. ReentrantLock
2. ReadWriteLock
3. StampedLock
4. optimistic Locking
 
https://winterbe.com/posts/2015/04/30/java8-concurrency-tutorial-synchronized-locks-examples/

# Executor Framework
---
![Executor_Framework_Interface](https://github.com/smansoori87/study-notes/blob/master/images/concurrency/executor_framework_interface.png)


Executor framework is to use threads as service. There are many different implementation of Executor are available.
- Threads are available in pool as a server.
- once assigned task is complete, Executor will will assign new task to the Thread.
- irrespective of normal Thread, Executor Threads will not stop untill Executor service is running.
- Executor frame work support both Thread Interfaces(Runnable and Callable) 
- ** Executor execute() method is to execute Runnable. **
- ** ExecutorService submit() method is to submit Callable. **

#### a. newSingleThreadExecutor(): 
- Executor will complete all the submit task with single thread.	
- only one thread will be there to serve all the request.

#### b. newFixedThreadPool(int size): 
- can create a pool of thread while specifying the pool size.

#### c. newScheduledThreadPool(int corePoolSize):
- To call run method after every given duraion.

```java
ScheduledExecutorService ex = Executors.newScheduledThreadPool(2);
for (Thread t : threads) {
	ex.scheduleAtFixedRate(t, 1, 1, TimeUnit.SECONDS);
	//scheduleAtFixedRate(Runnable command, long initialDelay, long period, TimeUnit unit)
}
```

#### shutdown()
- executor will stop accepting any new task.
- will shutdown the executor post completing all the task from pool.

#### shutDownNow()
- Attempts to stop all actively executing tasks, halts theprocessing of waiting task.
- Returns a list of the tasks that were awaiting execution.

#### awaitTermination(long timeout, TimeUnit unit)
- Returns:true if this executor terminated and false if the timeout elapsed before termination
- If there are 4 tasks running in Executor, calling the shutDown method will wait all the 4 task to get complete before shutting down the executor.
- If we dont want to wait for ongoing task execution, calling the shutDownNow() will immediatly attempt to stop the taskes.
- Before calling shutDownNow() if want to wait for some period of time, there is awaitTermination usefull.

```java
public class ExecutorExam {

	static void fixSizePool() {
		Thread[] threads = new Thread[10];

		for (int i = 0; i < threads.length; i++) {
			threads[i] = new Thread(() -> {
				try {
					TimeUnit.SECONDS.sleep(1);
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
				System.out.println("Hi I am in fixSizePool..." + Thread.currentThread().getId());
			});
		}

		ExecutorService exec = Executors.newFixedThreadPool(2);
		for (Thread t : threads) {
			exec.execute(t);
		}
		exec.shutdown();
		
	}

	static void scheculedThreadPool() {
		Thread[] threads = new Thread[10];

		for (int i = 0; i < threads.length; i++) {
			threads[i] = new Thread(() -> {
				System.out.println("Hi I am in scheculedThreadPool..." + Thread.currentThread().getId());
			});
		}

		ScheduledExecutorService ex = Executors.newScheduledThreadPool(2);
		for (Thread t : threads) {
			ex.scheduleAtFixedRate(t, 1, 1, TimeUnit.SECONDS);
		}
		
		//**Will stop accepting any new tasks. and shutdown post pool completion.**
		ex.shutdown();
		
		try {
		//**will wait for 1000 seconds to complete the task.
		//return true if this executor terminated and false if the timeout elapsed before termination.**	
			if(!ex.awaitTermination(1000, TimeUnit.SECONDS)) {
				//**Attempts to stop all actively executing tasks, halts theprocessing of waiting tasks, 
				//and returns a list of the tasksthat were awaiting execution.**
				ex.shutdownNow();
			}
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
	}

	public static void main(String[] args) {
		fixSizePool();
		scheculedThreadPool();
	}
}
```

# Callable and Future
---
- While runnable does not have any option to return any response post task execution. Callable uses Future object to response 
the result while invoking 
get() method of Future object.
- Future is wrapper arround Callable and will wait for task to complete before returning the result on call of get() method.
- Internally Future logic is based on flag "done" which is boolean to check if task is complete or not. if Flag is false, Caller 
thread will moved into wait(). and on task completion Executor will notify all waiting thread to enquire on done flag. if done 
is true, Future will return the object.

#### ** Example **
In below example it is clearly visible.
1. Creted a newSingleThreadExecutor();
2. An list of Future object to collect all the Future from Callable.
3. While submiting the Callable to Executor, Each time we are forcing thread to sleep random time between 3 to 10 sec.
4. Post Submit, collecting all the future objects into ArryaList. 
5. While Invoking get(), on each Future object we can check in logs that each thread is waiting for randome seconds
before returning the result. and post timelapse only next line getting executed.

**Note:**
- While using callable it needs to make sure the Executor pool size to be optimal to achive 
the optimal perfmance and parallalism.
- As many As threads available in pool does not mean all can get serveparallaly. as it is 
completely depend on System Hardware and CPU Cores.
- If there is 4 core CPU then only Max 4 threads can execute parallay.
- If All tasks are submited, Future get() method for each Future object Thread 
needs to wait untill task is not completed. in such situation if any one of the future get() method 
is hanged in processing then all the down objects will still be waiting for there turn of collection.

```java
package prac.thread.concurrency;

import java.util.ArrayList;
import java.util.List;
import java.util.Random;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;
import java.util.concurrent.TimeUnit;

class UserData {

	private int id;
	private String name;

	UserData(int id, String name) {
		this.id = id;
		this.name = name;
	}

	public String getName() {
		return name;
	}

	public int getId() {
		return id;
	}
}

public class CallableExam {

	static void fixCallableThreadPool(UserData[] users) throws InterruptedException, ExecutionException {
		ExecutorService ex = Executors.newSingleThreadExecutor();

		List<Future<UserData>> futList = new ArrayList<>();

		for (int i = 0; i < users.length; i++) {
			final UserData ud = users[i];
			Future<UserData> fut = ex.submit(() -> {
				System.out.println("Thread-" + Thread.currentThread().getId() + " : id-" + ud.getId());
				TimeUnit.SECONDS.sleep(new Random().ints(3, 10).findFirst().getAsInt());
				return ud;
			});
			futList.add(fut);
		}

		// When calling get() method of Future Object, it will wait for threads to
		// complete the assigned task. as internally it uses wait() based on "done" flag
		// if thread not completed its task caller thread will go into wait condition.
		// As can see in below example, get is waiting for 5 sec to jump into next line.
		for (Future<UserData> fut : futList) {
			Long startTime = System.currentTimeMillis();
			System.out.println("in future...");
			System.out.println("User Data: " + fut.get().getId());
			System.out.println("out future...Sec:" + (System.currentTimeMillis() - startTime) / 1000F);
		}

		ex.shutdown();
	}

	public static void main(String[] args) throws InterruptedException, ExecutionException {
		UserData[] users = new UserData[3];

		for (int i = 0; i < users.length; i++) {
			users[i] = new UserData(i, "Name-" + i);
		}

		fixCallableThreadPool(users);
	}
}
```
#### O/P:
```console
in future...
Thread-11 : id-0
User Data: 0
Thread-11 : id-1
out future...Sec:4.023
in future...
Thread-11 : id-2
User Data: 1
out future...Sec:9.008
in future...
User Data: 2
out future...Sec:8.005
```

## Completable Future
- So there are certain limitation with callable. 
- while submitting multiple jobs using Executor frameworks, main thread will be in waiting stage if it will call get method on future object.
- As can see in below flow, while invoking get method main thread is in blocking state untill future get will not return the value,

![callable_problem_1](https://github.com/smansoori87/study-notes/blob/master/images/concurrency/callable_problem_1.JPG)

![callable_problem_2](https://github.com/smansoori87/study-notes/blob/master/images/concurrency/callable_problem_2.JPG)

![callable_problem_3](https://github.com/smansoori87/study-notes/blob/master/images/concurrency/callable_problem_3.JPG)

- from the solution side, async thread should not bother main or caller thread for its response.
- same is the solution or implemenation as part of completable future. 
- internally it uses ForkJoinPool and submit the task. 
- while there is scenario where on return value of one thread we need to perform another action, it can be taken care as a
 thenApply(Supplier(T)) and if there is no return we are expecting further we can use thenAccept(Consumer(T)).
 
![callable_solution_1](https://github.com/smansoori87/study-notes/blob/master/images/concurrency/callable_solution_1.JPG)

## CountDownLatch
- A synchronization aid that allows one or more threads to wait until a set of operations being performed in other threads completes. 
- A CountDownLatch is initialized with a given count. The await methods block until the current count reaches zero due to invocations of the countDown method, after which all waiting threads are released and any subsequent invocations of await return immediately. 

**Important Methods:**
- countDown: will decrement the latch value by one.
- await: will make other threads to wait until latch is not going to reach value 0.
- await(time, timeunit): will make sure to break the waiting condition if tasks are not executed in given time or latch reach to 0.

```java
import java.util.concurrent.CountDownLatch;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;

public class CountdownLatchExam {

	private static void countDownLatchWithTimeoutAwait() throws InterruptedException {
		ExecutorService es = Executors.newSingleThreadExecutor();
		final CountDownLatch cdLatch = new CountDownLatch(5);
		for (int i = 0; i < 3; i++) {
			es.execute(() -> {
				System.out.println("Inside Run..." + Thread.currentThread().getId());
				try {
					TimeUnit.SECONDS.sleep(3);
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
				cdLatch.countDown();
			});
		}

		System.out.println("All Task Submited, and latch counter is waitng for 0...");
		cdLatch.await(10, TimeUnit.SECONDS);
		System.out.println("All task finished...");

	}

	static void countDownLatchKeepWaiting() throws InterruptedException {
		ExecutorService es = Executors.newSingleThreadExecutor();
		final CountDownLatch cdLatch = new CountDownLatch(5);
		for (int i = 0; i < 3; i++) {
			es.execute(() -> {
				System.out.println("Inside Run..." + Thread.currentThread().getId());
				try {
					TimeUnit.SECONDS.sleep(3);
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
				cdLatch.countDown();
			});
		}

		System.out.println("All Task Submited, and latch counter is waitng for 0...");
		cdLatch.await();
		System.out.println("All task finished...");
	}

	public static void main(String[] args) throws InterruptedException {

		/*
		 * await(long timeout, TimeUnit unit), is to timeout the latch in case if
		 * Threads execution not finished in given timeunit.
		 */
		countDownLatchWithTimeoutAwait();

		/*
		 * When there is any mismatch in no. of task and threads execution or any of the
		 * thread task failed during execution which miss the cdLatch.countDown()
		 * execution. In such case latch await will keep waiting for Latch to get 0. In
		 * this situation it can wait for infinite time and application will hang in
		 * condition.
		 */
		countDownLatchKeepWaiting();
	}
}
```

## CyclicBarrier
- It is as similar as CountDownLatch. Only the difference it will use **await()** method to manage the count.
- Also CyclicBarrier have an overloaded constructor to define Runnable.
- Once All threads execution complete and barrier reached its count, Runnable will execute.
- Also CyclicBarrier have an option to **rest()** the count to further use it. 

```java
import java.util.concurrent.BrokenBarrierException;
import java.util.concurrent.CyclicBarrier;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;

public class CyclicBarrierExam {

	public static void main(String[] args) {
		final CyclicBarrier cb = new CyclicBarrier(3, () -> {
			System.out.println("CyclicBarrier Runnable...");
		});

		ExecutorService es = Executors.newSingleThreadExecutor();

		for (int i = 0; i < 3; i++) {
			es.execute(() -> {
				System.out.println("Inside Run: " + Thread.currentThread().getId());
				try {
					TimeUnit.SECONDS.sleep(3);
					cb.await();

					/*
					 * Below method is as similar of CountDownLatch, in case of await hanged for
					 * more then given time it will break the await with TimeOutException.
					 */
					// cb.await(5, TimeUnit.SECONDS);
				} catch (InterruptedException | BrokenBarrierException e) {
					e.printStackTrace();
				}
				System.out.println("Outside Run: " + Thread.currentThread().getId());
			});
		}
		System.out.println("All Task Submitted...");
	}
}
```

## BlockingQueue
- It is thread safe queue. where multiple thread can read and write data in queue.
- Queue **put()** and **take()** method internally will take care of the size, wait and notify to manage the queue if it is full or empty.
- classic example of Producer and Consumer problem where it was depend on wait() and notify() method in case if queue is empty or full.
- in case producer is very fast, where consumer is very slow. in such case very frequently queue will be full with data. 
Blocking Q will internally manage such case to control the Q to prevent from overflow.

```java
import java.util.concurrent.ArrayBlockingQueue;
import java.util.concurrent.BlockingQueue;
import java.util.concurrent.TimeUnit;

public class BlockingQueueExam {

	public static void main(String[] args) {
		BlockingQueue<Integer> bq = new ArrayBlockingQueue<>(5);
		
		new Thread(()->{
			System.out.println("inside Producer...");
			int count=0;
			while(true) {
				try {
					bq.put(count);
					TimeUnit.SECONDS.sleep(1);
					System.out.println("data added in q: "+count);
					count++;
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
			}
		}).start();

		new Thread(()->{
			System.out.println("inside Consumer...");
			while(true) {
				try {
					int data = bq.take();
					TimeUnit.SECONDS.sleep(4);
					System.out.println("data taken from q: "+data);
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
			}
		}).start();
		System.out.println("Done!!!");	
	}
}
```
## Delayed Queue
- This queue is making objects available only after objects are expired.
- element can only be taken when its delay has expired.  The <em>head</em> of the queue is that
- If no delay has expired there is no head and {@code poll} will return {@code null}. 
- Even though unexpired elements cannot be removed using {@code take} or {@code poll}.
- size method returns the count of both expired and unexpired elements.
- This queue does not permit null elements.

## PriorityQueue
- Thread safe Queue, store objects or premitive data types in sorted order.
- In case of custom object, there is need to implement the Comparable interface. 
  and the compare to method need to specify with the fields details on which sorting needs to apply.
- This queue does not permit null elements.
- There are method ** put() ** and **take()** to add and retrive the data.

#### Ex: Primitive Data  
```java
import java.util.Random;
import java.util.concurrent.BlockingQueue;
import java.util.concurrent.PriorityBlockingQueue;
import java.util.concurrent.TimeUnit;

public class PriorityQueueExam {

	static void writeData(BlockingQueue<Integer> bq) {
		Random random = new Random();
		while (bq.size() != 5) {
			int num = random.ints(1, 10).findFirst().getAsInt();
			try {
				bq.put(num);
				System.out.println("Thread-" + Thread.currentThread().getName() + " :addNum: " + num);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}
	}

	static void readData(BlockingQueue<Integer> bq) {
		try {
			TimeUnit.SECONDS.sleep(5);
			while (!bq.isEmpty()) {
				System.out.println("Thread-" + Thread.currentThread().getName() + " :takeNum: " + bq.take());
			}
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
	}

	public static void main(String[] args) {
		BlockingQueue<Integer> bq = new PriorityBlockingQueue<>();
		new Thread(() -> {
			writeData(bq);
		}, "1").start();

		new Thread(() -> {
			readData(bq);
		}, "2").start();
	}
}

O/P:
Thread-1 :addNum: 7
Thread-1 :addNum: 2
Thread-1 :addNum: 3
Thread-1 :addNum: 1
Thread-1 :addNum: 5
Thread-2 :takeNum: 1
Thread-2 :takeNum: 2
Thread-2 :takeNum: 3
Thread-2 :takeNum: 5
Thread-2 :takeNum: 7
```

### Ex: Custom Objects, using Comparable

```java
import java.util.concurrent.BlockingQueue;
import java.util.concurrent.PriorityBlockingQueue;
import java.util.concurrent.TimeUnit;

class User implements Comparable<User> {
	int id;
	String name;

	User(int id, String name) {
		this.id = id;
		this.name = name;
	}

	@Override
	public int compareTo(User o) {
		return this.name.compareTo(o.name);
	}

	@Override
	public String toString() {
		return "User [id=" + id + ", name=" + name + "]";
	}
}

public class PriorityQueueExam {

	static void writeThread(BlockingQueue<User> bq) {
		bq.add(new User(1, "Sam"));
		bq.add(new User(200, "Piter"));
		bq.add(new User(10, "Arno"));
		bq.add(new User(5, "Wishly"));
		
	}

	static void readThread(BlockingQueue<User> bq) {
		try {
			TimeUnit.SECONDS.sleep(5);
			while (!bq.isEmpty()) {
				System.out.println("Thread-" + Thread.currentThread().getName() + " :takeNum: " + bq.take().toString());
			}
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
	}

	public static void main(String[] args) {
		BlockingQueue<User> bq = new PriorityBlockingQueue<>();
		new Thread(() -> {
			writeThread(bq);
		}, "1").start();

		new Thread(() -> {
			readThread(bq);
		}, "2").start();
	}
}

O/P:
Thread-2 :takeNum: User [id=10, name=Arno]
Thread-2 :takeNum: User [id=200, name=Piter]
Thread-2 :takeNum: User [id=1, name=Sam]
Thread-2 :takeNum: User [id=5, name=Wishly]
```

## ConcurretMap
![Concurrent_Hashmap](https://github.com/smansoori87/study-notes/blob/master/images/concurrency/concurrent-hashmap.jpg)
- The underlined data structure for ConcurrentHashMap is Hashtable.
- ConcurrentHashMap class is thread-safe i.e. multiple threads can operate on a single object without any complications.
- At a time any number of threads are applicable for a read operation without locking the ConcurrentHashMap object which is not there in HashMap.
- In ConcurrentHashMap, the Object is divided into a number of segments according to the concurrency level.
- The default concurrency-level of ConcurrentHashMap is 16.
- In ConcurrentHashMap, at a time any number of threads can perform retrieval operation but for updation in the object, the thread must lock the particular segment in which the thread wants to operate. This type of locking mechanism is known as Segment locking or bucket locking. Hence at a time, 16 update operations can be performed by threads.
- Inserting null objects is not possible in ConcurrentHashMap as key or value.

**Constructors of ConcurrentHashMap**
- **Concurrency-Level:** It is the number of threads concurrently updating the map. The implementation performs internal sizing to try to accommodate this many threads.
- **Load-Factor:** It is a threshold, used to control resizing.
- **Initial Capacity:** Accommodation of a certain number of elements initially provided by the implementation. if the capacity of this map is 10. It means that it can store 10 entries.

**Constructors**
1. **ConcurrentHashMap()** : Creates a new, empty map with a default initial capacity (16), load factor (0.75) and concurrencyLevel (16).
2. **ConcurrentHashMap(int initialCapacity)** : Creates a new, empty map with the specified initial capacity, and with default load factor (0.75) and concurrencyLevel (16).
3. **ConcurrentHashMap(int initialCapacity, float loadFactor)** : Creates a new, empty map with the specified initial capacity and load factor and with the default concurrencyLevel (16).
4. **ConcurrentHashMap(int initialCapacity, float loadFactor, int concurrencyLevel)**: Creates a new, empty map with the specified initial capacity, load factor, and concurrency level.
5. **ConcurrentHashMap(Map m)**: Creates a new map with the same mappings as the given map.

## Exchanger
- Thread safe class to exchange data between two threads. 
- Its operation is simple: it simply waits until two separate threads call its exchange() method. 
When that occurs, it exchanges the data supplied by the threads. It can also be viewed as a bidirectional SynchronousQueue. 
It is a generic class that is declared as below.

**Methods:**

exchange(V x):
- When invoked this function causes the current thread to suspend its execution and wait for another thread to call its exchange method. When another thread calls its exchange method, the threads exchange their data and the execution resumes.

Syntax:
```java
public V exchange(V x) throws InterruptedException
```

public exchange(V x, long timeout, TimeUnit unit):
- When invoked this function causes the current thread to suspend its execution and wait for another thread to call its exchange method. When another thread calls its exchange method, the threads exchange their data and the execution resumes. The thread waits only for the duration specified by the timeout argument and in case if timeout duration elapses, a TimeoutException is thrown.

Syntax:
```java
public V exchange(V x, long timeout, TimeUnit unit) throws InterruptedException, TimeoutException
```

# Fork-Join Framework
---
It is concrete implementation for parallel execution.

#### Fork-Join Framework
![fork-join-framework_1](https://github.com/smansoori87/study-notes/blob/master/images/concurrency/fork-join-framework_1.JPG)

#### ForkJoinTask extensions
![fork-join-framework_2](https://github.com/smansoori87/study-notes/blob/master/images/concurrency/fork-join-framework_2.JPG)

#### Fork Activity
![fork_1](https://github.com/smansoori87/study-notes/blob/master/images/concurrency/fork_1.JPG)

#### Join Activity
![join_1](https://github.com/smansoori87/study-notes/blob/master/images/concurrency/join_1.JPG)

#### SimpleRecurivAction Example:
```java
Compute Logic:

import java.util.concurrent.RecursiveAction;

public class SimpleRecursiveAction extends RecursiveAction {

	private static final long serialVersionUID = 1L;

	private int input;

	public SimpleRecursiveAction(int input) {
		this.input = input;
	}

	@Override
	protected void compute() {
		if (input > 50) {
			System.out.println("Task is in " + "devide...:" + input);
			SimpleRecursiveAction sra1 = new SimpleRecursiveAction(input / 2);
			SimpleRecursiveAction sra2 = new SimpleRecursiveAction(input / 2);

			sra1.fork();
			sra2.fork();
		} else {
			System.out.println("Task is not big enough to further devide...:" + input);
		}
	}
}

Client:

import java.util.concurrent.ForkJoinPool;

public class SimpleRecursiveActionClient {

	public static void main(String[] args) {
		ForkJoinPool fjp = new ForkJoinPool(Runtime.getRuntime().availableProcessors());
		System.out.println("Processors: "+Runtime.getRuntime().availableProcessors());
		SimpleRecursiveAction sra = new SimpleRecursiveAction(100);
		fjp.invoke(sra);
	}
}
```