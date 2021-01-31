## Memory Architecture in Java:
- Partitions
	- Heap: holding all the objects. All JRE classes and objects will be in Heap
		- Young Gen
			- Eden: Initial object will be created in this space and will shift to S0, S1 if Eden is full.
			- Survivor 0
			- Survivor 1
		- Old Gen
			- Memory Pool: Stores immutable objects like String. 
	- Permanent Generation: Holding meta data.
		- Method Area: holding class or methods strct.	
		- Run Time Const Pool: static vars, const.
	
	- Stack:
		- Per thread there will be one stack.
		- All the local variables or references or method calls will be part of Stack.
		- Method Block: used to allocate for method execution and will be release once method is done and given to other method.
		

- GC only runs on Heap memory.
- Minor GC: It runs in young space to collect objects to shift from Eden to S0, S1 and so on.
- Minor GC will increment count of object by one each time its moving from one space to other. Also it will increase till 15 before object moved from Young to Old Gen space.
- Major GC: runs for long time hence not invoked frequently.
- In case of large objects it will be created directly in Heap Old Gen Space.
- Stack memory is very less compare to Heap Memory.
---

## Why Wait and Notify is part of Object Class?
- wait - wait method tells the current thread to give up monitor(Key) and go to sleep.
- notify - Wakes up a single thread that is waiting on this object's monitor. 
- So you see wait() and notify() methods work at the monitor level, thread which is currently holding the monitor is asked 
to give up that monitor through wait() method and through notify method (or notifyAll) threads which are waiting on the 
object's monitor are notified that threads can wake up.
- Important point to note here is that monitor is assigned to an object not to a particular thread. 
That's one reason why these methods are in Object class. To reiterate threads wait on an Object's monitor (lock) and 
notify() is also called on an object to wake up a thread waiting on the Object's monitor.

---

## 
