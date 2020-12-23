Synchronization:
To prevent race(when 2 or more thread trying to read/write same object/variable) condition while dealing with multiple threads.

Monitor: Key which is used to synchronized the critical section.

1. Synchronized:
a. Instance Block: sysnchronized block is used to define some peace of code. it will block threads to execute specific code of method rather then locking the entire method.
	public void doSomething(){
	 ---
	 --
	 synchronized(this){
	 
	 }
	 --
	 --
	}

b. Instance Method Level: instance/object will be used to lock the synchronized methods. only one thread can access all synchronized methods at a time.
	Ex: if a class holding 4 instnace methods and all are synchronized then only current thread which is holding lock can access anyof this methods.
	public synchronized void doSomething(){
	 ---
	 --
	}
	
c. Static Method Block: it is at class level. so in such case thread will use class as a monitor to prevent other threads to enter into critical area.
	public static void doSomething(){
	 ---
	 --
	 synchronized(ABC.class){
	 
	 }
	 --
	 --
	}

c. Static Method: it is at class level. so in such case thread will use class as a monitor to prevent other threads to enter into critical area.
	public static synchronized void doSomething(){
	 ---
	 --
	}
