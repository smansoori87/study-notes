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

###### yeild();
- yeild is part of thread class. and indicating jvm to push current running thread into waiting condition.
- in case there are any high priority threads available in runnable pool, jvm will give cpu to task exection. or else jvm will give chance to same or other same priority thread.


#### volatile keyword
- Volatile keyword is used to modify the value of a variable by different threads.
- The volatile keyword can be used either with primitive type or objects. The volatile keyword cannot be used with classes or methods.
- In case of multicore cpu, The volatile keyword does not cache the value of the variable and always read the variable from the main memory.

