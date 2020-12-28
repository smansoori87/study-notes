# Synchronization
To prevent **race condition**(when 2 or more thread trying to read/write same object/variable) while dealing with multiple threads.
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
public class ThreadExam {
	static int j = 10;
	
	final static Object lock = new Object();
	final Object lock1 = new Object();
	
	void thirdThread() {
		System.out.println(Thread.currentThread().getName() );
		synchronized (lock1) {
			while (j > 0)
				System.out.println(Thread.currentThread().getName() + ":j:" + j--);
		}
	}
	
	void fourthThread() {
		System.out.println(Thread.currentThread().getName() );
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
		System.out.println(Thread.currentThread().getName() );
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
		ThreadExam tExam = new ThreadExam();
		
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
### Different Methods
----

###### wait();
- wait is used to release the lock from current thread and move it into wait pool.
- wait is bound to object and object only can invoke the wait method to release the lock from current thread.
- wait can be used only in synchronized block or method. Its because practically it will make scence to move any thread in wait condition if other thread waiting to accure the current lock.
###### notify();
- notify isp art of object class and as wait method, lock object can give signal to jvm to notify the waiting thread.
- in case of notify only one thread from waitpool(waiting for the same lock/mutex) will be notified based on algorithm.
###### notifyAll();
- notifyAll will invoke all the waiting threads from wait pool and only one thread will get opportunity to accure the lock on object.
###### sleep();
- sleep is part of thread class and it will make sure tomake current thread sleep for period of time.
- while thread is in sleep condition, no lock will be released and post sleep duration same thread will continue with execution.

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

# Executor Framework
---
![Screenshot](https://github.com/smansoori87/study-notes/blob/master/images/executor_framework_interface.png)


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
