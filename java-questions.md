## Memory Architecture in Java:
```
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


- Stack: All the local variables or references or method calls.

- GC only runs on Heap memory.
- Minor GC: It runs in young space to collect objects to shift from Eden to S0, S1 and so on.
- Minor GC will increment count of object by one each time its moving from one space to other. Also it will increase till 15 before object moved from Young to Old Gen space.
- Major GC: runs for long time hence not invoked frequently.
- In case of large objects it will be created directly in Heap Old Gen Space.
- 


```