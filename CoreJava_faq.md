what is encapsulation,inheritance and polymorphism?
Ans:
Encapsulation
-------------
The ability to make changes in your implementation code without breaking the code of others who use your code is a key benefit of encapsulation.
By hiding implementation details, you can rework your method code (perhaps also altering the way variables are used by your class) without forcing a change in the code that calls your changed method.If you want maintainability, flexibility, and extensibility ,your design must include encapsulation.
1)Keep instance variables protected (with an access modifier, often private).
2)Make public accessor methods, and force calling code to use those methods
rather than directly accessing the instance variable.
public class Box {
	// protect the instance variable; only an instance of Box can access it
	private int size;
	// Provide public getters and setters
	public int getSize() {
		return size;
	}
	public void setSize(int newSize) {
		size = newSize;
	}
}

how useful is the previous code? It doesn't even do any validation or processing. What benefit can there be from having getters and setters that add no additional functionality? The point is, you can change your mind later, and add more code to your methods without breaking your API. Even if today you don't think you really need validation or processing of the data, good OO design dictates that you plan for the future. To be safe, force calling code to go through your
methods rather than going directly to instance variables. 

Inheritance:
------------
you can create inheritance relationships in Java by extending a class. It's also important to understand that the two most common reasons to use inheritance are
1)To promote code reuse
2)To use polymorphism

Polymorphism
------------
Any Java object that can pass more than one IS-A test can be considered polymorphic. Other than objects of type Object, all Java objects are polymorphic in that they pass the IS-A test for their own type and for class Object.

Abstraction
------------
Abstraction in Java is achieved by using interface and abstract class in Java. An interface or abstract class is something which is not concrete , something which is incomplete.

e.g. You create an interface called Server which has start() and stop() method. This is called abstraction of Server because every server should have way to start and stop and details may differ.


1)What are the disadvatnages of method overloading?
Ans:see the exampl below.

public class MethodOVerloadingTest {

	/**
	 * @param args
	 */
	public static void main(String[] args) {
		// TODO Auto-generated method stub
		MethodOVerloadingTest mot=new MethodOVerloadingTest();
		mot.method1(5,5);
	}
	public void method1(int x,float y)
	{
		System.out.println("inside method1");
	}
	public void method1(float x,int y)
	{
		System.out.println("inside method1");
	}

}

The above code gives java.lang.Error with description which contains ambiguity message.
*******************************************************************************************
2)what is the output of the following code?

class Super
{
	public static void method1()
	{
		System.out.println("inside super class method");
	}
	
}
class Sub extends Super
{
	public void method1()
	{
		System.out.println("inside sub class method");
	}
}


public class StaticMethodTest {

	/**
	 * @param args
	 */
	public static void main(String[] args) {
		// TODO Auto-generated method stub
		Super s1=new Sub();
		s1.method1();
	}

}

Ans: The above code gives compilation error, stating static method cannot be overridden.
(same is the case with private method in the super class and public method in the subclass).

If, you change the subclass method also to static, the output will come from super class method. i.e.  inside super class method.
-->for private method in the super class and public method in the subclass, the scenario is entirely different. you cannot call using super class reference. 
*******************************************************************************************
3)Difference between Late Binding and Dynamic polymorphism

In Dynamic polymorphism the function to be executed is decided at runtime but the function has a body at the time of compilation. Where as in Late binding the body is not associated at the compilation time.. Late binding can be coded using interface.

Note:Late binding is highly dynamic than Dynamci polymorphism.	 

Example:

Class ABC
{
public void fun1(XYZ x1)
{
x1.fun2() ;
}
}

interface XYZ
{
void fun2();
}

Class A implements XYZ
{
	public void fun2()
	{
	----
	----
	}
}

class Test
{
public static void main()
{
A a1=new A();
ABC abc=new ABC();
abc.fun1(a1);
}
}




Abstract class
------------------

An abstract class can have main method. 

abstract class Abs{
	int x,y;
	public abstract void func1();
	public static void main(String args[]){
		Abs a1=new A2();
		a1.func1();
	}
}

class A2 extends Abs{
	public void func1()
	{
		System.out.println(“in func1”);
	}
}

Note: we cannot access private members of super class with subclass object.
Note: Access specifiers are not applicable to local variables.
*******************************************************************************************
Inner classes:
--------------
A class defined inside another class is called Inner class.There are 4 types of inner classes
1) Member inner classes: if we define a class inside another class just as a member of that class (like variables and methods), we call it as Member inner class.
2) Static inner classes: if we define any inner class as static then it is called Static inner class.
3) Local inner classes:
4) Anonymous inner classes

Ex1: Member inner class.

		class Outer1{
			int x;
			class Inner1{
				int i;
				public void funIn()
				{
					System.out.println(“inside funIn of Inner1”);
					x=25;
					funOu();
				}
			}
			public void funOu(){
				x=x+1;
				Inner1 in1=new Inner1();
				in1.i = 20;
				System.out.println(in1.i);
			}
			
			public static void main(String args[]){
				Outer1.Inner1 ou1=new Outer1().new Inner1();
			}
		}

Notes:
------
1) Outer1.Inner1 ou1=new Outer1().new Inner1(); is the syntax to create an object for inner class from a static function from outer class or from outside the class.
2) For an inner class any member of outer class is visible (either static or non-static).
3) Member inner classes should not have any static members. i.e. you cannot define a static member inside an inner class. It will show compiler error.
4) Inner class can be declared as private. Then outer class can only access it.
5) Inner class can extend any class that is available for outer class.

Ex2: Static inner class:

		class Outer2{
			int x;
			static int y;
			static class Sinner1{
				public static void funs1(){
					System.out.println(“inside funs1 of Sinner1”);
					y=y+1;
				}
				public void fun2(){
					System.out.println(“inside fun2 of Sinner1”);
					x=x+1; //compilation error.
				}
			}
			
			public void funOu1(){
				System.out.println(“inside funOu1 of Outer2”);
				Sinner1 s1=new Sinner1();
				s1.fun2();
				Sinner1.funs1();
			}
		
			public static void main(String args[]){
				Sinner1 s2=new Sinner1();
				s2.fun2();
				Sinner1.funs1();
			}
	}

Notes:
------
1)	Static inner class can have any access specifier and it can have both static and non-static members in it.
2)	Static members are directly available to outer class, but for non-static members object creation is compulsory.
3)	From s class, non-static members of outer class are not available. We need to create object for that.
4)	If the main method is in a different class, we need to create the object using the following syntax.
Outer2.Sinner1 os=new Outer2.Sinner1 ();

Question:
What is the use of inner classes?
Ans:We can achieve modularity using inner classes.

3) Local Inner classes:
--------------------
If we define a class inside a method, then it is called Local inner class.

		Ex:
		--
		class outer3{
			int x;
			public void funOu3(){
				int i;
				final int j=30;
				class Linner1{
					public void funL(){
						System.out.println("inside funL() of Linner1");
						System.out.println(x);
						System.out.println(i);//compilation error--no access;
						System.out.println(j);//only constants accepted.
					}
				}
				Linner1 l1=new Linner1();
				l1.funL();
			}
			public static void main(String args[]){
				Outer3 o3=new Outer3();
				o3.funOu3();
			}
		}
Note:
------
1) Local variables of a function are not accessible from local inner class.
2) Local constants are accessible from Local inner class.
3) Local inner class should not have any access specifier.
4) It is not possible to create object of Local inner class outside the method in which it was defined.

4)Anonymous Inner classes:
--------------------------
These are inner classes without a name. These are two types.
1)Local Anonymous Inner classes
2)Member Anonymous Inner classes
Ex for Local Anonymous Inner class:
-----------------------------------

		interface XYZ{
			void funX();
			void funY();
		}

		class Anony1{
			static XYZ x1=new XYZ(){
				public void funX(){
					System.out.println("inside funX()");
				}
				public void funY(){
					System.out.println("inside funY()");
				}
			};
		}
		class Test{
			public static void main(String args[]){
				Anony1.x1.funX();
				Anony1.x1.funY();
			}
		}


Ex for Member Anonymous Inner class:
------------------------------------

		interface XYZ2{
			void funX();
			void funY();
		}

		class Anony2{
			public static XYZ2 getXYZ2(){
				XYZ2 x1=new XYZ2(){
					public void funX(){
						System.out.println("inside funX()");
					}
					public void funY(){
						System.out.println("inside funY()");
					}
				};
				return x1;
			}
		}

		class Test2{
			public static void main(String args[]){
				XYZ2 x1=Anony2.getXYZ2();
				x1.funX();
				x1.funY();
			}
		}
*******************************************************************************************
Order of Execution:
------------------
static blocks---->main method---->non-static blocks or Init blocks---->constructor loading or object creation

note:
-----
1) Init block will be executed, if the once the constructor's call to super() method call returned.
2) Init blocks are used to provide some information which is common for all constructors.becuase Instance init blocks run every time a class instance is created.
3) Init blocks execute in the order they appear.
4) Static blocks run once, when the class is first loaded.Static blocks are used to provide information regarding project, copy rights,name of the company etc. before the project is executed.
*******************************************************************************************
Does java uses pass by value or pass by reference?
Ans:Java always uses pass by value for both primitives or object references. If the called method can re-assign the caller with a new object , then it is called pass by reference. In java called method cannot re-assign the caller reference with a new object.
*******************************************************************************************
Q: Wrapper conversions in java?
Ans: 
primitives to wrappers
----------------------
new Integer(5) 

wrappers to primitives
----------------------
xxxValue() methods. 
Ex: Integer i1= new Intger(10); ---> for converting back int i=i1.intValue(); 
you can convert this integer wrapper to any primitive. for ex: byte b=i1.byteValue();

primitives to String
--------------------
using static toString methods present in each wrapper class.
Ex: String s=Integer.toString(5);

String to primitive
--------------------
using parseXXX() methods
int i=Integer.parseInt("5"); throws number format exception if the string does not represents an int.

String to wrapper
-----------------
new Integer("5") or Integer.valueOf("5");  throws number format exception

wrapper to String
-----------------
using instance toString() method present in each wrapper class.
For ex: Integer i1=new Integer(10); for converting this wrapper to string call toString() on i1. i.e. i1.toString();
*******************************************************************************************
Q: How does method invocation works when combining widening and boxing?
Ans:Widening and then boxing does not work where as boxing and widening will work.
class WidenAndBox {
  static void go(Long x) { System.out.println("Long"); }

  public static void main(String [] args) {
    byte b = 5;
    go(b);           // must widen then box - illegal
  }
}

This is just too much for the compiler:

WidenAndBox.java:6: go(java.lang.Long) in WidenAndBox cannot be
applied to (byte)

	class BoxAndWiden {
	  static void go(Object o) {
		Byte b2 = (Byte) o;        // ok - it's a Byte object
		System.out.println(b2);
	  }

	  public static void main(String [] args) {
		byte b = 5;
		go(b);       // can this byte turn into an Object ?
	  }
	}

This compiles (!), and produces the output:

5
*******************************************************************************************
Q:How does var args work with widening and boxing?
Ans:
class Vararg {
  static void wide_vararg(long... x){ 
	System.out.println("long...");
  }
  static void box_vararg(Integer ... x){ 
	System.out.println("Integer..."); 
  }
  public static void main(String [] args) {
    wide_vararg(5,5);    // needs to widen and use var-args
    box_vararg(5,5);     // needs to box and use var-args
  }
}

This compiles and produces:

long...
Integer...
*******************************************************************************************
What is the difference between association, aggregation and composition?
Ans:
Association is a relationship where all objects have their own lifecycle and there is no owner.
Ex:
Teacher and Student. Multiple students can associate with single teacher and single student can associate with multiple teachers, but there is no ownership between the objects and both have their own lifecycle. Both can be created and deleted independently.

Aggregation is a specialised form of Association where all objects have their own lifecycle, but there is ownership and child objects can not belong to another parent object.
Ex:
Department and teacher. A single teacher can not belong to multiple departments, but if we delete the department, the teacher object will not be destroyed. We can think about it as a “has-a” relationship.

Composition is again specialised form of Aggregation and we can call this as a “death” relationship. It is a strong type of Aggregation. Child object does not have its lifecycle and if parent object is deleted, all child objects will also be deleted.
Ex:
House can contain multiple rooms - there is no independent life of room and any room can not belong to two different houses. If we delete the house - room will automatically be deleted.

*******************************************************************************************		

MultiThreading
--------------
1) When do you get InterruptedException?
Ans: Every thread have an interrupted status flag and an interrupt() method. If interrupt() method is called by another thread (which is waiting for the object), then interrupted status flag is set to true. so every 	thread 	needs to check its interrupted status flag once in a while whether anybody requested for interruption. If a thread is blocked(due to sleep or wait) it cannot check the flag status. This is where 	InterruptedException comes in. The thread is terminated with an InterruptedException. 
	
Note: When a thread is waiting for I/O no InterruptedException will come.
***********************************************************************************************
2) Is it possible to check whether any thread requested for interruption?
Ans: There are two methods which serve this purpose. 
	1)static boolean interrupted()
		tests whether the current thread (that is, the thread that is executing this instruction) has been interrupted. Note that this is a static method. The call has a side effect.it resets the interrupted status of the current thread to false.
	2)boolean isInterrupted()
		tests whether a thread has been interrupted. Unlike the static interrupted method, this call does not change the interrupted status of the thread.
***********************************************************************************************
3) what are the thread states?
Ans: Threads can be in one of four states.
	1)New -- when creating thread with new operator(), thread comes to new state. 
	2)Runnable -- calling start() will put into Runnable.
	3)Blocked -- can be blocked by 1) calling sleep() 2)I/O  3) calling wait() 4) waiting for a lock
	4)Dead -- can be dead when 1) run method exits 2)throws an uncaught exception.
***********************************************************************************************
4) How do we know whether current thread is alive?
Ans: calling isAlive() returns true if thread is in Runnable or blocked state. If thread is in new or dead it will return false. But we cannot distinguish between Runnable and Blocked states.
***********************************************************************************************
5) what are thread groups?
Ans: If a program contains quite a few threads, then it becomes useful to categorize them by functionality. 
	Ex: If many threads are trying to acquire images from a server and the user clicks on a Stop button to interrupt the loading of the current page, then it is handy to have a way of interrupting all these threads simultaneously.

	String groupName = . . .;
	ThreadGroup g = new ThreadGroup(groupName);
	Thread t = new Thread(g, threadName);
	if (g.activeCount() == 0)
	{
	   // all threads in the group g have stopped
	}
	
	To interrupt all threads in a thread group, simply call interrupt on the group object.
	g.interrupt(); 
	Note: executors let you achieve the same task without requiring the use of thread group. It is not recommended to use thread groups.
***********************************************************************************************
6)How to Handle uncaught exceptions in Threads because run() method can't throw any checked exceptions?
Ans: Prior to JDK 5.0, you needed to subclass the ThreadGroup class and override the uncaughtException method.
	
	From JDK 5.0, we can install a handler for uncaught exceptions. 
	Thread has a nested interface called Thread.UncaughtExceptionHandler . It has a method with name uncaughtException(Thread t, Throwable e) . We need to write a class implementing this Interface. 
	Then we need to configure this in the Thread class using setUncaughtExceptionHandler(). 
	
	Ex:

	class SimpleThreadExceptionHandler implements Thread.UncaughtExceptionHandler{
		public void uncaughtException(Thread t, Throwable e) {
			System.err.println("inside exception handler");
		}
	}

	class ABC extends Thread{
		ABC(){
			setUncaughtExceptionHandler(new SimpleThreadExceptionHandler());
		}
		public void run( ) {
			//inside run method
		}
	}

Note:we can log the error messages.
***********************************************************************************************
7)How to Lock objects ?
Ans: Java used the synchronized keyword for this purpose, and JDK 5.0 introduces the ReentrantLock class.The synchronized keyword automatically provides a lock as well as an associated "condition" .
***********************************************************************************************
8)What is Reentrant lock?
Ans:
	private Lock myLock = new ReentrantLock(); // ReentrantLock implements the Lock interface
	myLock.lock(); // a ReentrantLock object
	try
	{
	   critical section
	}
	finally
	{
	   myLock.unlock(); // make sure the lock is unlocked even if an exception is thrown
	}

Note:This construct guarantees that only one thread at a time can enter the critical section. As soon as one thread locks the lock object, no other thread can get past the lock statement. When other threads call lock, they are deactivated until the first thread unlocks the lock object.	

Note:The lock is called reentrant because a thread can repeatedly acquire a lock that it already owns. 
***********************************************************************************************
9)What are condition Objects?
Ans: what do we do when there is not enough money in the account? We wait until some other thread has added funds. But this thread has just gained exclusive access to the bankLock, so no other thread has a chance to make a deposit. This is where condition objects come in.

	A lock object can have one or more associated condition objects.You obtain a condition object with the newCondition method. It is customary to give each condition object a name that evokes the condition that it represents.
	Ex:
	class Bank{
		private Lock myLock = new ReentrantLock();
		private Condition sufficientFunds=null;
		private Condition otherCondition=null;
	    public Bank(){
	      . . .
	      sufficientFunds = bankLock.newCondition();
	    }
	    public void transfer(int from, int to, int amount){
		   bankLock.lock();
		   try{
		      while (accounts[from] < amount)
				sufficientFunds.await();
		      // transfer funds
		      . . .
		      sufficientFunds.signalAll();
		   }
		   finally
		   {
		      bankLock.unlock();
		   }
		}
	   . . .
	   private Condition sufficientFunds;
	}
	
	public void otherMethod(){
		bankLock.lock();
		try{
			while(//some othe condition){
				otherCondition.await();
			}
			otherCondition.signalAll();
		}finally{
			bankLock.unlock();
		}
	
	}

Note:The statement sufficientFunds.await(); makes the current thread deactive and gives up the lock.
***********************************************************************************************
10)Explain Synchronization?
Ans: If a method is declared with the synchronized keyword, then the object's lock protects the entire method. That is, to call the method, a thread must acquire the object lock.
	
	Ex:
	class Bank
	{
	   public synchronized void transfer(int from, int to, int amount) throws InterruptedException
	   {
	      while (accounts[from] < amount)
			wait(); // wait on object lock's single condition
	      accounts[from] -= amount;
	      accounts[to] += amount;
	      notifyAll(); // notify all threads waiting on the condition
	   }
	   public synchronized double getTotalBalance() { . . . }
	   private double accounts[];
	}

	using the synchronized keyword yields code that is much more concise. Here each object has an implicit lock, and that the lock has an implicit condition.

	implicit locks and conditions have some limitations

	1)You cannot interrupt a thread that is trying to acquire a lock.
	2)You cannot specify a timeout when trying to acquire a lock.
	3)Having a single condition per lock can be inefficient.
	4)The virtual machine locking primitives do not map well to the most efficient locking mechanisms available in hardware.
***********************************************************************************************
11)what is Lock Testing?
Ans: A thread blocks indefinitely when it calls the lock method to acquire a lock that is owned by another thread. The tryLock method tries to acquire a lock and returns true if it was successful. Otherwise, it immediately returns false, and the thread can go off and do something else.
	Ex: if (myLock.tryLock())
	   // now the thread owns the lock
	   try  { . . .  }
	   finally  { myLock.unlock(); }
	else
	  // do something else
	  
***********************************************************************************************
12)What are timeouts?
Ans:
	WAITING ON LOCKS
	-----------------------------
	You can call tryLock with a timeout parameter, like this:
	if (myLock.tryLock(100, TimeUnit.MILLISECONDS))  //TimeUnit is an enumeration with values SECONDS, MILLISECONDS, MICROSECONDS, and NANOSECONDS.
	
	Note:the tryLock method without timeout can barge in. if the lock is available when the call is made, the current thread gets it, even if another thread has been waiting to lock it.
	
	The lock method cannot be interrupted. If a thread is interrupted while it is waiting to acquire a lock, the interrupted thread continues to be blocked until the lock is available. If a deadlock occurs, then the lock method can never terminate.
	
	However, if you call tryLock with a timeout, then an InterruptedException is thrown if the thread is interrupted while it is waiting. This is clearly a useful feature because it allows a program to break up deadlocks.
	
	You can also call the lockInterruptibly method. It has the same meaning as tryLock with an infinite timeout.

	WAITING ON CONDITIONS
	-------------------------------------
	myCondition.await(100, TimeUnit.MILLISECONDS))
	The await method returns if another thread has activated this thread by calling signalAll or signal, or if the timeout has elapsed, or if the thread was interrupted.
	The await method throw an InterruptedException if the waiting thread is interrupted. In the (perhaps unlikely) case that you'd rather continue waiting, use the awaitUninterruptibly method instead.
***********************************************************************************************
13)what are read/write locks?
Ans: java.util.concurrent.locks  has two lock classes 1)ReentrantLock  2)ReentrantReadWriteLock .
	when there are many threads that read from a data structure and fewer threads that modify it we use ReentrantReadWriteLock.It will allow shared access for readers and exclusive access for writers.

	Ex:

	1)Construct a ReentrantReadWriteLock object:
		private ReentrantReadWriteLock rwl = new ReentrantReadWriteLock();
	
	2) Extract read and write locks:
		private Lock readLock = rwl.readLock();
		private Lock writeLock = rwl.writeLock();
	
	3) Use the read lock in all accessors:
		public double getTotalBalance()
		{
		   readLock.lock();
		   try { . . . }
		   finally { readLock.unlock(); }
		}
	4) Use the write lock in all mutators:
		public void transfer(. . .)
		{
		   writeLock.lock();
		   try { . . . }
		   finally { writeLock.unlock(); }
		}
***********************************************************************************************
14)Why the stop and suspend Methods were Deprecated?
Ans:Stop method terminates all pending methods, including the run method. When a thread is stopped, it immediately gives up the locks on all objects that it has locked. This can leave objects in an inconsistent state.
	
Note:When a thread wants to stop another thread, it has no way of knowing when the stop method is safe and it can lead to damaged objects. Therefore, the method has been deprecated

Unlike stop, suspend won't damage objects. However, if you suspend a thread that owns a lock, then the lock is unavailable until the thread is resumed. If the thread that calls the suspend method tries to acquire the same lock, then the program deadlocks.
***********************************************************************************************
15)What are blocking queues?
Ans:A blocking queue causes a thread to block when you try to add an element when the queue is currently full or to remove an element when the queue is empty
	java.util.concurrent.BlockingQueue is an interface that extends Queue, and adds two more methods: put( ) and take( ). 

	There are five out-of-the-box implementations of BlockingQueue; all are in the java.util.concurrent package

	ArrayBlockingQueue 
	----------------------------
	You have to specify the initial capacity when you create this queue, and like any other array, this capacity is the fixed limit. This queue has a somewhat reduced throughput as compared to other implementations, but threads are served in the order that they arrive.
	
	LinkedBlockingQueue 
	-------------------------------
	This queue is based on a linked list (duh!). While you can specify a maximum size, it is by default unbounded.

	PriorityBlockingQueue 
	-------------------------------
	This queue bases ordering on a specified Comparator, and the element returned by any take( ) call is the smallest element based on this ordering. If you don't specify a Comparator, the natural ordering is used (assuming the objects supplied to it implement Comparable). If your objects don't implement Comparable, and you don't have a Comparator to supply, there's really no reason to use PriorityBlockingQueue.
	
	DelayQueue 
	-----------
	DelayQueue is essentially a version of PriorityBlockingQueue that uses elements that implement the new java.util.concurrent.Delayed interface. Since this interface extends Comparable, it fits right into a PriorityBlockingQueue structure. Additionally, it won't allow an element to be grabbed with take( ) until that element's delay has elapsed.

	SynchronousQueue 
	-----------------
	This queue has a size of zero (yes, you read that correctly). It blocks put( ) calls until another thread calls take( ), and blocks take( ) calls until another thread calls put( ). Essentially, elements can only go directly from a producer to a consumer, and nothing is stored in the queue itself (other than for transition purposes).
***********************************************************************************************
16)What are Thread-safe collections?
Ans: From jdk 5.0 there are two Thread-safe collections defined in java.util.concurrent package.
	They are 1)ConcurrentLinkedQueue --constructs an unbounded, nonblocking queue that can	be safely accessed by multiple threads.
		 2)ConcurrentHashMap. --construct a hash map that can be safely accessed by multiple threads
		
	Also Two Thread-aware collections.
		1)CopyOnWriteArrayList 
		2)CopyOnWriteArraySet -- These two are useful if the number of threads that iterate over the collection greatly outnumbers the threads that mutate it. When you construct an iterator, it contains a reference to the current array. If the array is later mutated, the iterator still has the old array, but the collection's array is replaced.
***********************************************************************************************
17)How to work with ConcurrentHashMap?
Ans: The ConcurrentHashMap can efficiently support a large number of readers and a fixed number of writers. By 	default, it is assumed that there are up to 16 simultaneous writer threads. There can be many more writer threads, but if more than 16 write at the same time, the others are temporarily blocked.

	These collections use sophisticated algorithms that never lock the entire table and that minimize contention by allowing simultaneous access to different parts of the data structure.

	Map map = new ConcurrentHashMap(2000, 25, 25);

	The first parameter is the initial size, common to normal HashMap implementations. Next comes the load factor, and then the concurrency level. This isn't specifically named as the number of segments, but instead as the number of threads you expect to be performing concurrent updates
	
	This collection return weakly consistent iterators. They will not return a value twice and they will not throw any exceptions.

	The ConcurrentHashMap has useful methods for atomic insertion and removal of associations.

	V putIfAbsent(K key, V value)

	if the key is not yet present in the map, associates the given value with the given key and returns null. Otherwise returns the existing value associated with the key but don't put the new value.

	boolean remove(K key, V value)

	if the given key is currently associated with this value, removes the given key and value and returns true. Otherwise returns false.

	boolean replace(K key, V oldValue, V newValue)

	if the given key is currently associated with oldValue, associates it with newValue. Otherwise, returns false.
***********************************************************************************************
18)What is a Callable?
Ans:A Callable is similar to a Runnable, but it returns a value. The Callable interface is a parameterized type, with a single method call.

	public interface Callable<V>
	{
	   V call() throws Exception;
	}
	
	The type parameter is the type of the returned value. For example, a Callable<Integer> represents an asynchronous computation that eventually returns an Integer object.
***********************************************************************************************
19)What is a Future?
Ans: Future holds the result of an asynchronous computation.Use a Future object so that you can start a computation, give the result to someone, and forget about it. The owner of the Future object can obtain the result when it  is ready.

	The Future interface has the following methods:

	public interface Future<V>
	{
	   V get() throws . . .; //blocks until the computation is finished
	   
	   V get(long timeout, TimeUnit unit) throws . . .;//throws a TimeoutException if the call timed out before the computation finished,If the thread running the computation is interrupted, both methods throw an InterruptedException 
	   
	   void cancel(boolean mayInterrupt);//If the computation has not yet started, it is canceled and will never start. If the computation is currently in progress, then it is interrupted if the mayInterrupt parameter is true.

	   boolean isCancelled();

	   boolean isDone();//The isDone method returns false if the computation is still in progress
	}
***********************************************************************************************
20)How to work with a Callable?
Ans:   First you need to write a class that implements Callable.
	Ex:	class ABC implements Callable<Integer>
		{
		   public ABC() { . . . }
		   public Integer call() { . . . }  
		}

		Then we construct a FutureTask. FutureTask is a convenient class, which can turn a callable into a Future or it can convert into a simple Runnable because it implements both Runnable and Future interfaces.
		
		1)converting into Future and to a Runnable to run the thread, and give the result to Future
		Callbale<Integer> abc=new ABC(); 
		FutureTask<Integer> task = new FutureTask<Integer>(abc);
		//Now construct a thread out of Future(or FutureTask) and start the thread.
		Thread t=new Thread(task);
		t.start();
		//Now get the result using the Future defined above.
		Integer result = task.get();
***********************************************************************************************
21)What are executors?
Ans: Executor is an interface from java.util.concurrent package. It has a single method named 
	execute(Runnable obj), executes the given Runnable object immedaitely or some time in the future.
	The main use of executor is , we create threads(or Runnables) and pass to the execute method, and execute method will run thread. So you can write your own logic in execute method and get the control on execution.

	How to do that?

	Create a class that implements Executor.

	Class MyExecutor implements Executor
	{
	public void execute(Runnable r)
	{
		//logic to run thread. It can be your own logic
		Thread t=new Thread(r);
		t.start();
	}
	}
***********************************************************************************************
22)what is ExecutorService?
Ans: There is a sub interface of Executor called ExecutorService. execute method won't return anything, but ExecutorService has some useful methods to manage termination and methods that can produce a Future for tracking progress of one or more asynchronous tasks.
	
	You can submit a Runnable or Callable to an ExecutorService with one of the following methods:

	Future<?> submit(Runnable task)
	Future<T> submit(Runnable task, T result)
	Future<T> submit(Callable<T> task)
	
	The pool will run the submitted task at its earliest convenience. When you call submit, you get back a Future object that you can use to query the state of the task.

	The first submit method returns an odd-looking Future<?>. You can use such an object to call isDone, cancel, or isCancelled. But the get method simply returns null upon completion.

	The second version of submit also submits a Runnable, and the get method of the Future returns the given result object upon completion.

	The third version submits a Callable, and the returned Future gets the result of the computation when it is ready.

	There are other convenient methods like shutdown(), shutdownNow().
	
	1)void shutdown(). //This method initiates the shutdown sequence for the pool. An executor that is shut down accepts no new tasks. When all tasks are finished, the threads in the pool die
	
	2)List<Runnable>  shutdownNow() //Attempts to stop all actively executing tasks, halts the processing of waiting tasks, and returns a list of the tasks that were awaiting execution. 

	Note:if any tasks mask or fail to respond to interrupts, they may never terminate.

	There are other sub interfaces to ExecutorService like ThreadPoolExecutor,ScheduledThreadPoolExecutor which have other convenient methods to work with thread pools and for scheduled threads.

***********************************************************************************************
23)How to create thread pools?
Ans: There is a class called Executors in java.util.concurrent package, which has a number of static factory methods 	for constructing thread pools. 
	They are 

	1)newCachedThreadPool//	 New threads are created as needed; idle threads are kept for 60 seconds.
	 
	2)newFixedThreadPool// The pool contains a fixed set of threads; idle threads are kept indefinitely.
	 
	3)newSingleThreadExecutor// A "pool" with a single thread that executes the submitted tasks sequentially.
	 
	4)newScheduledThreadPool// A fixed-thread pool for scheduled execution.
	 
	5)newSingleThreadScheduledExecutor// A single-thread "pool" for scheduled execution.
	 
	First three methods return objects of ThreadPoolExecutor which implements ExecutorService interface.

	Last two methods return objects of ScheduledThreadPoolExecutor which implements ScheduledExecutorService.

	Ex:
	class MyCallable implements Callable<Integer>
	{
	public Integer call()
	{
	//logic
	}
	}

	class Main{
		private ExecutorService pool;
		public static void main(String[] args){
			pool=Executors.newCachedThreadPool();
			//For scheduling threads
			//ScheduledExecutorService scheduler=Executors.newSingleThreadScheduledExecutor();
			//scheduler.schedule(Callable<V> callable,long delay,TimeUnit unit); //return type is ScheduledFuture.
			MyCallable m1=new MyCallable();
			MyCallable m2=new MyCallable();
			pool.submit(m1);
			pool.submit(m2);
			//your other work
			pool.shutdown();
		}
	}
******************************************************************************************
Questions from Java concurrency Book

What is a Thread?
Ans:A thread is a single sequential flow of control within a program. 

A thread is considered lightweight because it runs within the context of a full-blown program and takes advantage of the resources allocated for that program and the program's environment, but it must have its own execution stack and program counter.

what is race condition?
Ans:A race condition is a situation in which two or more threads are reading or writing some shared data, and the final result depends on the timing of how the threads are scheduled.Here is an example,
@NotThreadSafe
public class UnsafeSequence {
	private int value;
	/** Returns a unique value. */ 
	public int getNext() {
		return value++;
	}
}

The problem with UnsafeSequence is that with some unlucky timing, two threads could call getNext and receive the same value.The increment notation, value++, may appear to be a single  operation,but is in fact three separate operations: read the value, add one  to  it, and write out the new value. Since operations  in  multiple  threads  may  be  arbitrarily interleaved by the runtime, it is possible for two threads to read the value at the same time, both see the same value, and then both add one to it. The result is that  the same sequence number is returned from multiple calls in different threads.

Note:Problems due to Race conditions can be avoided using proper synchronization.

what is thread safety?

Ans:A class is thread safe if it behaves correctly when accessed from multiple threads, and with no additional synchronization or other coordination on the part of the callers code.

Note:Stateless objects are always thread safe.

what are intrinsic locks?
Ans:Java provides a built-in mechanism for enforcing atomicity:the synchronized block.
Every java object can implicitly act as a lock for purposes of synchronization;these built-in locks are called implicit locks or monitor locks.

Implicit locks in java act as mutexes(or mutual exclusion locks), which means that at most one thread may own the lock.

what is reentrant lock?
Ans:When a thread requests a lock that is already held by another thread, the requesting thread blocks. But because intrinsic locks are reentrant, if a thread tries to acquire a lock that it already holds, the request succeeds. Reentrancy means that locks are acquired on a perthread rather than perinvocation basis. [7] Reentrancy is implemented by associating with each lock an acquisition count and an owning thread.

How to Guard state of an object?
Ans:
Every shared, mutable variable should be guarded by exactly one lock. Make it clear to maintainers which lock that is.

For every invariant that involves more than one variable, all the variables involved in that invariant must be guarded by  the same lock.

Note:
-----
Merely  synchronizing every method, as Vector does, is not enough to render compound actions on a Vector atomic: 

	if (!vector.contains(element)) 
		vector.add(element);

This attempt at a put if absent operation has a race condition, even though both contains and add are atomic. While  synchronized methods can make individual operations atomic, additional locking is required   when multiple operations  are  combined  into  a  compound  action.

What are volatile Variables?
Ans:The Java language also provides an alternative, weaker form of synchronization, volatile variables, to ensure that updates to a variable are propagated predictably to other threads.When a field is declared volatile,the compiler and runtime are put on notice that this variable is shared and that operations on it should not be reordered with other memory operations. Volatile variables are not cached in registers or in caches where they are hidden from other processors, so a read of a volatile variable always returns the most recent write by any thread.

Volatile variables are convenient, but they have limitations. Locking can guarantee visibility and atomicity;volatile variables can only guarantee visibility.
	
You can use volatile variables only when all the following criteria are met: 
1)Writes to the variable do not depend  on  its  current  value,  or  you  can  ensure  that  only  a  single  thread  ever  updates the value; 
2)The variable does not participate in invariants with other state variables; and    
3)Locking is not required for any other reason while the variable is being accessed.	

what is Thread Local?
Ans:Core concept of ThreadLocal is, every thread that accesses a ThreadLocal variable via its get or set method has its own, independently initialized copy of the variable.

Example:
--------
import java.text.SimpleDateFormat;
import java.util.Date;
 
public class ThreadLocalExample {
  private static final ThreadLocal formatter = new ThreadLocal() {
 
    protected SimpleDateFormat initialValue() {
      return new SimpleDateFormat("yyyyMMdd HHmm");
    }
  };
 
  public String formatIt(Date date) {
    return formatter.get().format(date);
  }
}

In the above sample code, get() method is key to understanding. It returns the value in the current thread's copy of this thread-local variable. If the variable has no value for the current thread, it is first initialized to the value returned by an invocation of the initialValue method.

why concurrent collections when synchronized collections were available?
Ans:There are problems with synchronized collections.
The synchronized collections are thread safe, but you may sometimes need to use additional client side locking to guard  compound  actions.  Common  compound  actions  on  collections  include  iteration  (repeatedly  fetch  elements  until  the  collection  is  exhausted),  navigation  (find  the  next  element  after  this  one  according  to  some  order),  and  conditional  operations  such  as  put if absent  (check  if  a  Map  has  a  mapping  for  key  K,  and  if  not,  add  the  mapping  (K,V)).  With  a  synchronized  collection,  these  compound  actions  are  still  technically  thread safe  even  without  client side  locking,  but  they may not behave as you might expect when other threads can concurrently modify the collection. 
 
Ex:Compound Actions on a Vector that may Produce Confusing Results.

public static Object getLast(Vector list) { 
	int lastIndex = list.size() - 1; 
	return list.get(lastIndex);
}
public static void deleteLast(Vector list) { 
	int lastIndex = list.size() - 1; 
	list.remove(lastIndex);
}

If thread A calls getLast on a  Vector with ten elements, thread B calls deleteLast on the same Vector, and the operations are interleaved,getLast throws ArrayIndexOutOfBoundsException. Between the call to size and the subsequent call to  get in getLast, the Vector shrank and the index computed in the first step is no longer valid. This is perfectly consistent  with the specification of Vector. It throws an exception if asked for a non existent element. But this is not what a caller  expects  getLast  to  do,  even  in  the  face  of  concurrent  modification.

Note:
-----
The risk that the size of the list might change between a call to size and the corresponding call to get is also present when we iterate through the elements of a Vector. In this case it throws ConcurrentModificationException.

Even though the iteration can throw an exception, this doesn't mean Vector isn't thread safe. The state of  the Vector is still valid and the exception is in fact in conformance with its specification. However, that something as  mundane as fetching the last element or iteration throw an exception is clearly undesirable.

Note:
-----
The problem of unreliable iteration again can be addressed by client side locking.

when does ConcurrentModificationException occurs?
Ans:The iterators returned by the synchronized/non synchronized collections are not designed to deal with concurrent modification,  and they are fail fast meaning that if they detect that the collection has changed since iteration began, they throw the  unchecked ConcurrentModificationException.

These  fail fast  iterators  are  not  designed  to  be  foolproof they  are  designed  to  catch  concurrency  errors  on  a  "good  faith effort"  basis  and  thus  act  only  as  early warning  indicators  for  concurrency  problems.  They  are  implemented  by  associating a modification count with the collection: if the modification count changes during iteration, hasNext or next  throws ConcurrentModificationException. 

Solution: 
1)Needs to synchronized access to collection. It is not advisable because holding lock on collection for the whole duration of iteration can cause lot of problems including dead locks.

2)Cloning the collection. During the cloning operation again collection needs to be synchronized. Again cloning is an expensive task. Needs to decide between synchronization and cloning.

what is thread dump?
Ans:A thread dump is a list of all the Java threads that are currently active in a Java Virtual Machine (JVM).

The java JDK ships with the jps command which lists all java process ids. You can run this command like this: jps -l

70660 sun.tools.jps.Jps
70305 

To obtain a thread dump using jstack, run the following command:
jstack <pid>

what are hidden iterators?
Ans:There  is  no  explicit  iteration  in  HiddenIterator.  The  string  concatenation  gets  turned  by  the  compiler  into  a  call  to  StringBuilder.append(Object), which in turn invokes the collection's toString method   and the implementation of  toString  in  the  standard  collections  iterates  the  collection  and  calls  toString  on  each  element  to  produce  a  nicely  formatted representation of the collection's contents.

Iteration  is  also  indirectly  invoked  by  the  collection's  hashCode  and  equals  methods,  which  may  be  called  if  the  collection is used as an element or key of another collection. Similarly, the containsAll, removeAll, and retainAll  methods, as well as the constructors that take collections are arguments, also iterate the collection. All of these indirect  uses of iteration can cause ConcurrentModificationException.
***
what are concurrent collections?
Ans:Java  5.0  improves  on  the  synchronized  collections  by  providing  several  concurrent  collection  classes.  Synchronized  collections achieve their thread safety by serializing all access to the collection's state. The cost of this approach is poor  concurrency; when multiple threads contend for the collection wide lock, throughput suffers. 
	
The concurrent collections, on the other hand, are designed for concurrent access from multiple threads. Java 5.0 adds  ConcurrentHashMap, a replacement for synchronized hash based Map implementations, and CopyOnWriteArrayList, a  replacement  for  synchronized  List  implementations  for  cases  where  traversal  is  the  dominant  operation.  The  new  ConcurrentMap  interface  adds  support  for  common  compound  actions  such  as  put if absent,  replace,  and  conditional  remove. 

Replacing  synchronized  collections  with  concurrent  collections  can  offer  dramatic  scalability  improvements  with  little  risk.

Java 5.0 also adds two new collection types, Queue and BlockingQueue. A Queue is intended to hold a set of elements  temporarily while they await processing. Several implementations are provided, including ConcurrentLinkedQueue, a  traditional FIFO queue, and PriorityQueue, a (non concurrent) priority ordered queue. Queue operations do not block;  if  the  queue  is  empty,  the  retrieval  operation  returns null. While  you  can  simulate  the  behavior  of  a  Queue  with  a  List infact, LinkedList also implements Queue the Queue classes were added because eliminating the random access  requirements of List admits more efficient concurrent implementations. 

BlockingQueue  extends  Queue  to  add  blocking  insertion  and  retrieval  operations.  If  the  queue  is  empty,  a  retrieval  blocks until an element is available, and if the queue is full (for bounded queues) an insertion blocks until there is space  available.  Blocking  queues  are  extremely  useful  in  producer consumer  designs.

Just  as  ConcurrentHashMap  is  a  concurrent  replacement  for  a  synchronized  hash based  Map,  Java  6  adds  ConcurrentSkipListMap  and  ConcurrentSkipListSet,  which  are  concurrent  replacements  for  a  synchronized  SortedMap or SortedSet (such as TreeMap or TreeSet wrapped with synchronizedMap).
	
what is ConcurrentHashMap?
Ans:ConcurrentHashMap is a hash based Map like HashMap, but it uses an entirely different locking strategy that offers better  concurrency  and  scalability.  Instead  of  synchronizing  every  method  on  a  common  lock,  restricting  access  to  a  single  thread  at  a  time,  it  uses  a  finer grained  locking  mechanism  called  lock  striping to  allow  a  greater  degree of shared access. Arbitrarily many reading threads can access the map concurrently, readers can access the map  concurrently  with  writers,  and  a  limited  number  of  writers  can  modify  the  map  concurrently.  The  result  is  far  higher  throughput under concurrent access, with little performance penalty for single threaded access.

ConcurrentHashMap,  along  with  the  other  concurrent  collections,  further  improve  on  the  synchronized  collection  classes by providing iterators that do not throw ConcurrentModificationException, thus eliminating the need to lock  the collection during iteration. The iterators returned by ConcurrentHashMap are weakly consistent instead of fail fast.  A weakly consistent iterator can tolerate concurrent modification, traverses elements as they existed when the iterator  was constructed, and may (but is not guaranteed to) reflect modifications to the collection after the construction of the  iterator. 

TradeOffs: 
1)The semantics of methods that operate on the entire Map, such  as size and isEmpty, have been slightly weakened to reflect the concurrent nature of the collection. Since the result of  size  could  be  out  of  date  by  the  time  it  is  computed,  it  is  really  only  an  estimate,  so  size  is  allowed  to  return  an  approximation  instead  of  an  exact  count. So  the  requirements  for  these  operations  were  weakened  to  enable  performance  optimizations  for  the  most  important  operations, primarily get, put, containsKey, and remove.
2)It does not have the ability to lock the map for exclusive access like synchronized collections.

Usage:
Because it has so many advantages and so few disadvantages compared to Hashtable or synchronizedMap, replacing  synchronized Map implementations with ConcurrentHashMap in most cases results only in better scalability. Only if your  application  needs  to  lock  the  map  for  exclusive  access is  ConcurrentHashMap  not  an  appropriate  drop in  replacement. 
	
Note:
-----
Since  a  ConcurrentHashMap  cannot  be  locked  for  exclusive  access,  we  cannot  use  client side  locking  to  create  new  atomic operations such as put if absent, as we can do for Vector.Instead, a number of common compound  operations  such  as  put if absent,  remove if equal, and  replace if equal  are  implemented  as  atomic  operations  and  specified  by  the  ConcurrentMap  interface.
	
what is CopyOnWriteArrayList?
Ans:CopyOnWriteArrayList  is  a  concurrent  replacement  for  a  synchronized  List  that  offers  better  concurrency  in  some  common  situations  and  eliminates  the  need  to  lock  or  copy  the  collection  during  iteration.  (Similarly,  CopyOnWriteArraySet is a concurrent replacement for a synchronized Set.) 

The copy on write collections derive their thread safety from the fact that as long as an effectively immutable object is  properly published, no further synchronization is required when accessing it. They implement mutability by creating and  republishing  a  new  copy  of  the  collection  every  time  it  is  modified.  Iterators  for  the  copy on write  collections  retain  a  reference  to  the  backing  array  that  was  current  at  the  start  of  iteration,  and  since  this  will  never  change,  they need  to  synchronize only briefly to ensure visibility of the array contents. As a result, multiple threads can iterate the collection  without interference from one another or from threads wanting to modify the collection. 

The iterators returned by the  copy on write  collections  do  not  throw  ConcurrentModificationException  and  return  the  elements  exactly  as  they  were at the time the iterator was created, regardless of subsequent modifications. 

Obviously,  there  is  some  cost  to  copying  the  backing  array  every  time  the  collection  is  modified,  especially  if  the  collection  is  large;  the  copy on write  collections  are  reasonable  to  use  only  when  iteration  is  far  more  common  than  modification. This criterion exactly describes many event notification systems: delivering a notification requires iterating  the  list  of  registered  listeners  and  calling  each  one  of  them,  and  in  most  cases  registering  or  unregistering  an  event  listener is far less common than receiving an event notification.
	
what are blocking queues?
Ans:Blocking queues provide blocking put and take methods as well as the timed equivalents offer and poll. If the queue  is full, put blocks until space becomes available; if the queue is empty, take blocks until an element is available. Queues  can be bounded or unbounded; unbounded queues are never full, so a put on an unbounded queue never blocks. 
Note:
-----
Blocking  queues  support  the  producer consumer  design  pattern. 

If the producers consistently generate work faster than the consumers can process it, eventually the application will run  out of memory because work items will queue up without bound. Again, the blocking nature of put greatly simplifies  coding  of  producers;  if  we  use  a  bounded  queue,  then  when  the  queue  fills  up  the  producers  block,  giving  the  consumers time to catch up because a blocked producer cannot generate more work. 

Blocking  queues  also  provide  an  offer  method,  which  returns  a  failure  status  if  the  item  cannot  be  enqueued.
Note:
-----
Bounded  queues  are  a  powerful  resource  management  tool  for  building  reliable  applications:  they  make  your  program  more robust to overload by throttling activities that threaten to produce more work than can be handled.

The class library contains several implementations of BlockingQueue. LinkedBlockingQueue and ArrayBlockingQueue  are FIFO queues, analogous to LinkedList and ArrayList but with better concurrent performance than a synchronized  List. PriorityBlockingQueue is a priority ordered queue, which is useful when you want to process elements in an  order other than FIFO. Just like other sorted collections, PriorityBlockingQueue can compare elements according to  their natural order (if they implement Comparable) or using a Comparator. 

The last BlockingQueue implementation, SynchronousQueue, is not really a queue at all, in that it maintains no storage  space for queued elements. Instead, it maintains a list of queued threads waiting to enqueue or dequeue an element.

what are deques?
Ans:Java 6 also adds another two collection types, Deque (pronounced "deck") and BlockingDeque, that extend Queue and  BlockingQueue. A Deque is a double ended queue that allows efficient insertion and removal from both the head and  the tail. Implementations include ArrayDeque and LinkedBlockingDeque. 

Just  as  blocking  queues  lend  themselves  to  the  producer consumer  pattern,  deques  lend  themselves  to  a  related  pattern  called  work  stealing.  A  producer consumer  design  has  one  shared  work  queue  for  all  consumers;  in  a  work  stealing design, every consumer has its own deque. If a consumer exhausts the work in its own deque, it can steal work  from the tail of someone else's deque. Work stealing can be more scalable than a traditional producer consumer design  because workers don't contend for a shared work queue; most of the time they access only their own deque, reducing  contention.  When  a  worker  has  to  access  another's  queue,  it  does  so  from  the  tail  rather  than  the  head,  further  reducing contention. 
Note:
-----
Work  stealing  is  well  suited  to  problems  in  which  consumers  are  also  producers     when  performing  a  unit  of  work  is  likely to result in the identification of more work.

what are synchronizers?
Ans:
A synchronizer is any object that coordinates the control flow of threads based on its state. Blocking queues can act as  synchronizers;  other  types  of  synchronizers  include  semaphores,  barriers,  and latches.  There  are  a  number  of  synchronizer  classes  in  the  platform  library;  if  these  do  not  meet  your  needs,  you  can  also  create  your  own.

All synchronizers share certain structural properties: they encapsulate state that determines whether threads arriving at the synchronizer should be allowed to pass or forced to wait, provide methods to manipulate that state, and provide methods to wait efficiently for the synchronizer to enter the desired state.

what are latches?
Ans:A latch is a synchronizer that can delay the progress of the threads until it reaches its terminal state.A latch acts as a gate: until the latch reaches the terminal state the gate is closed and no thread can pass, and in the terminal state the gate opens, allowing all threads to pass. Once the latch reaches the terminal state, it cannot change state again, so it remains open forever.

Note:Barriers are similar to latches but can be reused.

CountDownLatch is a flexible latch implementation. It allows one or more threads to wait for a set of events to occur. In this, latch state consists of a counter initialized to a positive number, representing the number of events to wait for. 

The countDown method decrements the counter, indicating that an event has occurred, and the await methods wait for the counter to reach zero, which happens when all the events have occurred. If the counter is nonzero on entry, await blocks until the counter reaches zero, the waiting thread is interrupted, or the wait times out.

Example:
	public class CountDownLatchDemo {
		public static void main(String args[]) throws InterruptedException {
			CountDownLatch latch = new CountDownLatch(4);
			Worker first = new Worker(1000, latch, "WORKER-1");
			Worker second = new Worker(2000, latch, "WORKER-2");
			Worker third = new Worker(3000, latch, "WORKER-3");
			Worker fourth = new Worker(4000, latch, "WORKER-4");
			first.start();
			second.start();
			third.start();
			fourth.start(); // Main thread will wait until all thread finished
			latch.await();
			System.out.println(Thread.currentThread().getName() + " has finished");
		}
	}

	class Worker extends Thread { 
		private int delay; 
		private CountDownLatch latch; 
		public Worker(int delay, CountDownLatch latch, String name) { 
			super(name); 
			this.delay = delay; 
			this.latch = latch; 
		} 
		@Override 
		public void run() { 
			try { 
				Thread.sleep(delay); 
				latch.countDown(); 
				System.out.println(Thread.currentThread().getName() + " has finished"); 
			} catch (InterruptedException e) { 
				e.printStackTrace(); 
			} 
		} 
	}

Note: Other uses of CountDownLatch include testing the performance of a method with concurrent invocations from a number of threads. It uses two latches, a “starting gate” and an “ending gate”. The starting gate is initialized with a count of one; the ending gate is initialized with a count equal to the number of worker threads. The first thing each worker thread does is wait on the starting gate; this ensures that none of them starts working until they all are ready to start. The last thing each does is count down on the ending gate; this allows the master
thread to wait efficiently until the last of the worker threads has finished, so it can calculate the elapsed time. 

Here is an example:
	class TestHarness {
		public long timeTasks(int nThreads, final Runnable task) throws InterruptedException {
			final CountDownLatch startGate = new CountDownLatch(1);
			final CountDownLatch endGate = new CountDownLatch(nThreads);
			for (int i = 0; i < nThreads; i++) {
				Thread t = new Thread() {
					public void run() {
						try {
							startGate.await();
							try {
								task.run();
							} finally {
								endGate.countDown();
							}
						} catch (InterruptedException ignored) {
						}
					}
				};
				t.start();
			}
			long start = System.nanoTime();
			startGate.countDown();
			endGate.await();
			long end = System.nanoTime();
			return end - start;
		}
	}

why is not advisable to start a thread from constructor or static initializer?
Ans:

Core Java volume 1
------------------
what is the difference between multiple processes and multiple threads?
Ans:
The essential difference is that while each process has a complete set of its own variables,
threads share the same data.However, shared variables make communication between threads more efficient and easier to program than interprocess communication. Threads are more
“lightweight” than processes—it takes less overhead to create and destroy individual threads than it does to launch new processes.

