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
**c. Static Method:** it is at class level. so in such case thread will use class as a monitor to prevent other threads to enter into critical area.
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
