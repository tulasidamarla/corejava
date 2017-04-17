		Item 1: Consider static factory methods instead of constructors
		---------------------------------------------------------------------------

Advantages:
---------------
1)One advantage of static factory methods is that, unlike constructors, they have names. If the parameters to a constructor do not, in and of themselves, describe the object being returned, a static factory with a well-chosen name is easier to use and the resulting client code easier to read. For example, the constructor BigInteger(int, int, Random), which returns a BigInteger that is probably prime, would have been better expressed as a static factory method named BigInteger. probablePrime. (This method was eventually added in the 1.4 release.)

2)A second advantage of static factory methods is that, unlike constructors, they are not required to create a new object each time they’re invoked. This allows immutable classes (Item 15) to use preconstructed instances, or to cache instances as they’re constructed, and dispense them repeatedly to avoid creating unnecessary duplicate objects. The Boolean.valueOf(boolean) method illustrates this technique: it never creates an object.

The ability of static factory methods to return the same object from repeated invocations allows classes to maintain strict control over what instances exist at any time. Classes that do this are said to be instance-controlled. There are several reasons to write instance-controlled classes. Instance control allows a class to guarantee that it is a singleton (Item 3) or noninstantiable (Item 4). Also, it allows an immutable class (Item 15) to make the guarantee that no two equal instances exist: a.equals(b) if and only if a==b. If a class makes this guarantee, then its clients can use the == operator instead of the equals(Object) method, which may result in improved performance. Enum types (Item 30) provide this guarantee.

3)A third advantage of static factory methods is that, unlike constructors, they can return an object of any subtype of their return type.This gives you great flexibility in choosing the class of the returned object.

4)A fourth advantage of static factory methods is that they reduce the verbosity of creating parameterized type instances.Unfortunately, you must specify the type parameters when you invoke the constructor of a parameterized class even if they’re obvious from context. This typically requires you to provide the
type parameters twice in quick succession:

	Map<String, List<String>> m =new HashMap<String, List<String>>();

This redundant specification quickly becomes painful as the length and complexity of the type parameters increase. With static factories, however, the compiler can figure out the type parameters for you. This is known as type inference. For example, suppose that HashMap provided this static factory:
	public static <K, V> HashMap<K, V> newInstance() {
		return new HashMap<K, V>();
	}

Then you could replace the wordy declaration above with this succinct alternative:
	Map<String, List<String>> m = HashMap.newInstance();

Disadvantages:
------------------
1)The main disadvantage of providing only static factory methods is that classes without public or protected constructors cannot be subclassed.The same is true for non public classes returned by public static factories. For example, it is impossible to subclass any of the convenience implementation classes in the Collections Framework. Arguably this can be a blessing in disguise, as it encourages programmers to use composition instead of inheritance.

2)A second disadvantage of static factory methods is that they are not readily distinguishable from other static methods.

	Item 4: Enforce noninstantiability with a private constructor
	-----------------------------------------------------------------------
Utility classes are not designed to be instantiated, an instance would be non sensical. JVM will put a default public constructor, so to avoid instantiating use private constructor. Also, throw an assertion error if it is accidentally invoked from with in the method.

	Item 5: Avoid creating unnecessary objects
	---------------------------------------------------
It is often appropriate to reuse a single object instead of creating a new functionally equivalent object each time it is needed. Reuse can be both faster and more stylish. An object can always be reused if it is immutable. 
		String s = new String("stringette"); // don't do this. this will create new instance each time it is invoked. Generally private constructor and public static method will help in reusing the objects, but String provides an easier approach.
		String s = "stringette";
So, Use Static factory methods to constructors if a class provides both. For ex, Boolean.valueOf(String) is preferred to new Boolean(String), because the later creates a new object for each invocation. We can also reuse mutable objects if know they won’t be modified. The following class models a person and has an isBabyBoomer method that tells whether the person was born between 1946 and 1964:

	public class Person {
		private final Date birthDate;
		// Other fields, methods, and constructor omitted
		
		// DON'T DO THIS!
		public boolean isBabyBoomer() {
			// Unnecessary allocation of expensive object
			Calendar gmtCal =
			Calendar.getInstance(TimeZone.getTimeZone("GMT"));
			gmtCal.set(1946, Calendar.JANUARY, 1, 0, 0, 0);
			Date boomStart = gmtCal.getTime();
			gmtCal.set(1965, Calendar.JANUARY, 1, 0, 0, 0);
			Date boomEnd = gmtCal.getTime();
			return birthDate.compareTo(boomStart) >= 0 && birthDate.compareTo(boomEnd) < 0;
		}
	}

The isBabyBoomer method unnecessarily creates a new Calendar, TimeZone, and two Date instances each time it is invoked. The version that follows avoids this inefficiency with a static initializer:

	class Person {
		private final Date birthDate;
		// Other fields, methods, and constructor omitted
		
		/**
		* The starting and ending dates of the baby boom.
		*/
		private static final Date BOOM_START;
		private static final Date BOOM_END;
		static {
			Calendar gmtCal = Calendar.getInstance(TimeZone.getTimeZone("GMT"));
			gmtCal.set(1946, Calendar.JANUARY, 1, 0, 0, 0);
			BOOM_START = gmtCal.getTime();
			gmtCal.set(1965, Calendar.JANUARY, 1, 0, 0, 0);
			BOOM_END = gmtCal.getTime();
		}
		public boolean isBabyBoomer() {
			return birthDate.compareTo(BOOM_START) >= 0 &&birthDate.compareTo(BOOM_END) < 0;
		}
	}	

In the previous examples in this item, it was obvious that the objects in question could be reused because they were not modified after initialization. There are other situations where it is less obvious. Consider the case of adapters [Gamma95, p. 139], also known as views. An adapter is an object that delegates to a backing object, providing an alternative interface to the backing object. Because an adapter has no state beyond that of its backing object, there’s no need to create more than one instance of a given adapter to a given object.

For example, the keySet method of the Map interface returns a Set view of the Map object, consisting of all the keys in the map. Every call to keySet on a given Map object may return the same Set instance. Although the returned Set instance is typically mutable, all of the returned objects are functionally identical: when one of the returned objects changes, so do all the others because they’re all backed by the same Map instance.

Autoboxing creates unnecessary objects. See the below example 
	public static void main(String[] args) {
		Long sum = 0L;
		for (long i = 0; i < Integer.MAX_VALUE; i++) {
		sum += i;
		}
		System.out.println(sum);
	}
In the above example sum could have been long instead of Long. This program would be much slower than it should be.
		
				Item 6: Eliminate obsolete object references
				-----------------------------------------------------
Consider the following simple stack implementation.
 
	public class Stack {
		private Object[] elements;
		private int size = 0;
		private static final int DEFAULT_INITIAL_CAPACITY = 16;
		public Stack() {
			elements = new Object[DEFAULT_INITIAL_CAPACITY];
		}
		public void push(Object e) {
			ensureCapacity();
			elements[size++] = e;
		}
		public Object pop() {
			if (size == 0)
			throw new EmptyStackException();
			return elements[--size];
		}
		/**
		* Ensure space for at least one more element, roughly
		* doubling the capacity each time the array needs to grow.
		*/
		private void ensureCapacity() {
			if (elements.length == size)
			elements = Arrays.copyOf(elements, 2 * size + 1);
		}
	}

The program has a “memory leak,” which can silently manifest itself as reduced performance due to increased garbage collector activity or increased memory footprint. In extreme cases, such memory leaks can cause disk paging and even program failure with an OutOfMemoryError, but such failures are relatively rare.

So where is the memory leak? If a stack grows and then shrinks, the objects that were popped off the stack will not be garbage collected, even if the program using the stack has no more references to them. This is because the stack maintains obsolete references to these objects. An obsolete reference is simply a reference that will never be dereferenced again. 

In the case of our Stack class, the reference to an item becomes obsolete as soon as it’s popped off the stack. The correct version of pop method would look like this.

	public Object pop() {
		if (size == 0)
		throw new EmptyStackException();
		Object result = elements[--size];
		elements[size] = null; // Eliminate obsolete reference
		return result;
	}
	
Note:
-----	
An added benefit of nulling out obsolete references is that, if they are subsequently dereferenced by mistake, the program will immediately fail with a NullPointerException, rather than quietly doing the wrong thing. It is always beneficial to detect programming errors as quickly as possible.

The best way to eliminate an obsolete reference is to let the variable that contained the reference fall out of scope. This occurs naturally if you define each variable in the narrowest possible scope (Item 45).

Common sources for Memory leaks:
------------------------------------------
1)whenever a class manages its own memory, the programmer should be alert for memory leaks. Whenever an element is freed, any object references contained in the element should be nulled out.
2)Another common source of memory leaks is caches.Once you put an object reference into a cache, it’s easy to forget that it’s there and leave it in the cache long after it becomes irrelevant.

Solution:Implement a cache for which an entry is relevant exactly so long as there are references to its key outside of the cache, represent the cache as a WeakHashMap;entries will be removed automatically after they become obsolete .

3)A third common source of memory leaks is listeners and other callbacks .If you implement an API where clients register callbacks but don’t deregister them explicitly, they will accumulate unless you take some action.

Solution:The best way to ensure that callbacks are garbage collected promptly is to store only weak
         references to them, for instance, by storing them only as keys in a WeakHashMap.


								Item -7: Avoid finalizers.
								-----------------------------

Problems of using a finalizer:
----------------------------------
1)If an uncaught exception is thrown during finalization, the exception is ignored, and finalization of that object terminates.Uncaught exceptions can leave objects in a corrupt state. If another thread attempts to use such a corrupted object, arbitrary nondeterministic behavior may result. Normally, an uncaught exception will terminate the thread and print a stack trace, but not if it occurs in a finalizer—it won’t even print a warning.
2)There is a severe performance penalty for using finalizers : The time to create and destroy a simple object is less compared to adding a finalizer.
3)It can take arbitrarily long between the time that an object becomes unreachable and the time that its finalizer is executed. This means that you should never do anything time-critical in a finalizer.

Note:
------
1)For example, it is a grave error to depend on a finalizer to close files, because open file descriptors are a limited resource. If many files are left open because the JVM is tardy in executing finalizers, a program may fail because it can no longer open files.
2)you should never depend on a finalizer to update critical persistent state. For example, depending on a finalizer to release a persistent lock on a shared resource such as a database is a good way to bring your entire distributed system to a grinding halt.

Solution:
----------
Just provide an explicit termination method, and require clients of the class to invoke this method on each instance when it is no longer needed.One detail worth mentioning is that the instance must keep track of whether it has been terminated: the explicit termination method must record in a private field that the object is no longer valid, and other methods must check this field and throw an Illegal- StateException if they are called after the object has been terminated.

Typical examples of explicit termination methods are the close methods on InputStream, OutputStream, and java.sql.Connection. Another example is the cancel method on java.util.Timer, which performs the necessary state change to cause the thread associated with a Timer instance to terminate itself gently. Examples from java.awt include Graphics.dispose and Window.dispose.

So what, if anything, are finalizers good for?
To act as a “safety net” in case the owner of an object forgets to call its explicit termination method. While there’s no guarantee that the finalizer will be invoked promptly, it may be better to free the resource late than never, in those (hopefully rare) cases when the client fails to call the explicit termination method. But the finalizer should log a warning if it finds that the resource has not been terminated, as this indicates a bug in the client code, which should be fixed.


					Methods common to all objects
					-------------------------------------
				Item 8: Obey the general contract when overriding equals
				---------------------------------------------------------------------

Overriding the equals method seems simple, but there are many ways to get it wrong, and consequences can be dire.The easiest way to avoid problems is not to override the equals method, in which case each instance of the class is equal only to itself. This is the right thing to do if any of the following conditions apply:

Each instance of the class is inherently unique:
--------------------------------------------------------
 This is true for classes such as Thread that represent active entities rather than values. The equals implementation provided by Object has exactly the right behavior for these classes.

You don’t care whether the class provides a “logical equality” test :
-------------------------------------------------------------------------------- 
For example, java.util.Random could have overridden equals to check whether two Random instances would produce the same sequence of random numbers going forward, but the designers didn’t think that clients would need or want this functionality.

A superclass has already overridden equals, and the superclass behavior is appropriate for this class:
-------------------------------------------------------------------------------------------------------------------------
 For example, most Set implementations inherit their equals implementation from AbstractSet, List implementations from AbstractList, and Map implementations from AbstractMap.
 
The class is private or package-private, and you are certain that its equals method will never be invoked:
----------------------------------------------------------------------------------------------------------------------------- 
Arguably, the equals method should be overridden under these circumstances, in case it is accidentally invoked:

	@Override public boolean equals(Object o) {
		throw new AssertionError(); // Method is never called
	}

Overriding equals is necessary in the following conditions:
---------------------------------------------------------------------
1)When a class has a notion of logical equality that differs from mere object identity, and a superclass has not already overridden equals to implement the desired behavior. This is generally the case for value classes. A value class is simply a class that represents a value, such as Integer or Date.
2) To enable instances to serve as map keys or set elements with predictable, desirable behavior.

Note:One kind of value class that does not require the equals method to be overridden is a class that uses instance control (Item 1) to ensure that at most one object exists with each value. Enum types (Item 30) fall into this category. For these classes, logical equality is the same as object identity, so Object’s equals method functions as a logical equals method.

Here is the contract, copied from the specification for Object [JavaSE6]:
• Reflexive: For any non-null reference value x, x.equals(x) must return true.

• Symmetric: For any non-null reference values x and y, x.equals(y) must return true if and only if y.equals(x) returns true.

• Transitive: For any non-null reference values x, y, z, if x.equals(y) returns true and y.equals(z) returns true, then x.equals(z) must return true.

• Consistent: For any non-null reference values x and y, multiple invocations of x.equals(y) consistently return true or consistently return false, provided no information used in equals comparisons on the objects is modified.

• For any non-null reference value x, x.equals(null) must return false.


Reflexivity : 
--------------
The first requirement says merely that an object must be equal to itself. It is hard to imagine violating this requirement unintentionally

Symmetry:
-------------
The second requirement says that any two objects must agree on whether they are equal.

consider the following class, which implements a case-insensitive string.

	// Broken - violates symmetry!
	public final class CaseInsensitiveString {
		private final String s;
		public CaseInsensitiveString(String s) {
			if (s == null)
			throw new NullPointerException();
			this.s = s;
		}
		
		// Broken - violates symmetry!
		@Override public boolean equals(Object o) {
			if (o instanceof CaseInsensitiveString)
				return s.equalsIgnoreCase(((CaseInsensitiveString) o).s);
			if (o instanceof String) // One-way interoperability!
				return s.equalsIgnoreCase((String) o);
			return false;
		}
		... // Remainder omitted
	}
	
see the client code:
-----------------------
	CaseInsensitiveString cis = new CaseInsensitiveString("Polish");
	String s = "polish";
	cis.equals(s); //---> returns true where as s.equals(cis) returns false;
	
Suppose you put a case-insensitive string into a collection:
	List<CaseInsensitiveString> list = new ArrayList<CaseInsensitiveString>();
	list.add(cis);
	
What does list.contains(s) return at this point? Who knows? Once you’ve violated the equals contract, you simply don’t know how other objects will behave when confronted with your object.	

To eliminate the problem, merely remove the ill-conceived attempt to interoperate with String from the equals method.i.e. remove the second if block from the equals method.

Transitivity:
-------------
The third requirement of the equals contract says that if one object is equal to a second and the second object is equal to a third, then the first object must be equal to the third.

Let’s start with a simple immutable twodimensional integer point class:
	public class Point {
		private final int x;
		private final int y;
		public Point(int x, int y) {
			this.x = x;
			this.y = y;
		}
		@Override public boolean equals(Object o) {
			if (!(o instanceof Point))
				return false;
			Point p = (Point)o;
			return p.x == x && p.y == y;
			}
		... // Remainder omitted
	}

Suppose you want to extend this class, adding the notion of color to a point:
	public class ColorPoint extends Point {
		private final Color color;
		public ColorPoint(int x, int y, Color color) {
			super(x, y);
			this.color = color;
		}
		@Override public boolean equals(Object o) {
		if (!(o instanceof ColorPoint))
			return false;
			return super.equals(o) && ((ColorPoint) o).color == color;
		}
	}	

The problem with this method is that you might get different results when comparing a point to a color point and vice versa.

To make this concrete, let’s create one point and one color point:
		Point p = new Point(1, 2);
		ColorPoint cp = new ColorPoint(1, 2, Color.RED);

Then p.equals(cp) returns true, while cp.equals(p) returns false.You might try to fix the problem by having ColorPoint.equals ignore color when doing “mixed comparisons”:
		// Broken - violates transitivity!
		@Override public boolean equals(Object o) {
			if (!(o instanceof Point))
				return false;
			// If o is a normal Point, do a color-blind comparison
			if (!(o instanceof ColorPoint))
				return o.equals(this);
			// o is a ColorPoint; do a full comparison
				return super.equals(o) && ((ColorPoint)o).color == color;
		}
This approach does provide symmetry, but at the expense of transitivity:
			ColorPoint p1 = new ColorPoint(1, 2, Color.RED);
			Point p2 = new Point(1, 2);
			ColorPoint p3 = new ColorPoint(1, 2, Color.BLUE);

Now p1.equals(p2) and p2.equals(p3) return true, while p1.equals(p3) returns false, a clear violation of transitivity.

Conclusion:There is no way to extend an instantiable class and add a value component while preserving the equals contract, unless you are willing to forgo the benefits of object-oriented abstraction. You can use composition instead of Inheritance to work with this. Here is the sample code.

	// Adds a value component without violating the equals contract
	public class ColorPoint {
		private final Point point;
		private final Color color;
		public ColorPoint(int x, int y, Color color) {
			if (color == null)
			throw new NullPointerException();
			point = new Point(x, y);
			this.color = color;
		}
		/**
		* Returns the point-view of this color point.
		*/
		public Point asPoint() {
			return point;
		}
		@Override public boolean equals(Object o) {
			if (!(o instanceof ColorPoint))
			return false;
			ColorPoint cp = (ColorPoint) o;
			return cp.point.equals(point) && cp.color.equals(color);
		}
		... // Remainder omitted
	}

Note: you can add a value component to a subclass of an abstract class without violating the equals contract.Problems of the sort shown above won’t occur so long as it is impossible to create a superclass instance directly.

Consistency:
------------
The fourth requirement of the equals contract says that if two objects are equal, they must remain equal for all time unless one (or both) of them is modified.In other words, mutable objects can be equal to different objects at different times while immutable objects can’t

Whether or not a class is immutable, do not write an equals method that depends on unreliable resources.For example, java.net.URL’s equals method relies on comparison of the IP addresses of the hosts associated with the URLs. Translating a host name to an IP address can require network access, and it isn’t guaranteed to yield the same results over time. This can cause the URL equals method to violate the equals contract and has caused problems in practice.

“Non-nullity”:
----------------
The final requirement, which in the absence of a name I have taken the liberty of calling “non-nullity,” says that all objects must be unequal to null.

Many classes have equals methods that guard against this with an explicit test for null:
	@Override public boolean equals(Object o) {
		if (o == null)
			return false;
	}
	
This test is unnecessary. To test its argument for equality, the equals method must first cast its argument to an appropriate type. For example,
		if (!(o instanceof MyType))
			return false;
		
The instanceof operator is specified to return false if its first operand is null, regardless of what type appears in the second operand.Therefore the type check will return false if null is passed in, so you don’t need a separate null check.

Summary to write a high quality equals method:
---------------------------------------------------------
I)Use the == operator to check if the argument is a reference to this object, if the comparision is expensive.

II)Use the instanceof operator to check if the argument has the correct type.If not, return false
Note: if the class implements an interface that refines the equals contract to permit comparisons across classes that implement the interface. Collection interfaces such as Set, List, Map, and Map.Entry have this property.

III)Cast the argument to the correct type. Because this cast was preceded by an instanceof test, it is guaranteed to succeed.

IV)For each “significant” field in the class, check if that field of the argument matches the corresponding field of this object.

For primitive fields whose type is not float or double, use the == operator for comparisons; for object reference fields, invoke the equals method recursively; 

For float fields, use the Float.compare method; and for double fields, use Double.compare.The special treatment of float and double fields is made necessary by the existence of Float.NaN, -0.0f .

For array fields, apply these guidelines to each element. If every element in an array field is significant, you can use one of the Arrays.equals methods added in release 1.5.

Some object reference fields may legitimately contain null. To avoid the possibility of a NullPointerException, use this idiom to compare such fields:
(field == null ? o.field == null : field.equals(o.field))

This alternative may be faster if field and o.field are often identical:
(field == o.field || (field != null && field.equals(o.field)))			

V)When you are finished writing your equals method, ask yourself three questions: Is it symmetric? Is it transitive? Is it consistent.

Note:Always override hashCode when you override equals.

		Item 9: Always override hashCode when you override equals
		-----------------------------------------------------------------------

Here is the contract, copied from the Object specification [JavaSE6]:

• Whenever it is invoked on the same object more than once during the execution of an application, the hashCode method must consistently return the same integer, provided no information used in equals comparisons on the object is modified. This integer need not remain consistent from one execution
of an application to another execution of the same application.

• If two objects are equal according to the equals(Object) method, then calling the hashCode method on each of the two objects must produce the same integer result.

• It is not required that if two objects are unequal according to the equals(Object) method, then calling the hashCode method on each of the two objects must produce distinct integer results. However, the programmer should be aware that producing distinct integer results for unequal objects may improve the performance of hash tables.

For example, consider the following simplistic PhoneNumber class:

	public final class PhoneNumber {
		private final short areaCode;
		private final short prefix;
		private final short lineNumber;
		public PhoneNumber(int areaCode, int prefix,int lineNumber) {
			rangeCheck(areaCode, 999, "area code");
			rangeCheck(prefix, 999, "prefix");
			rangeCheck(lineNumber, 9999, "line number");
			this.areaCode = (short) areaCode;
			this.prefix = (short) prefix;
			this.lineNumber = (short) lineNumber;
		}
		private static void rangeCheck(int arg, int max,String name) {
			if (arg < 0 || arg > max)
			throw new IllegalArgumentException(name +": " + arg);
		}
		@Override public boolean equals(Object o) {
			if (o == this)
				return true;
			if (!(o instanceof PhoneNumber))
				return false;
				
				PhoneNumber pn = (PhoneNumber)o;
				return pn.lineNumber == lineNumber&& pn.prefix == prefix&& pn.areaCode == areaCode;
		}
		// Broken - no hashCode method!
		... // Remainder omitted
	}

Suppose you attempt to use this class with a HashMap:
	Map<PhoneNumber, String> m = new HashMap<PhoneNumber, String>();
	m.put(new PhoneNumber(707, 867, 5309), "Jenny");
	
Now m.get(new PhoneNumber(707, 867, 5309)) returns null.The PhoneNumber class’s failure to override hashCode causes the two equal instances to have unequal hash codes, in violation of the hashCode contract.

Fixing this problem is as simple as providing a proper hashCode method for the PhoneNumber class.A good hash function tends to produce unequal hash codes for unequal objects.

Luckily it’s not too difficult to achieve a fair approximation. Here is a simple recipe:	

1. Store some constant nonzero value, say, 17, in an int variable called result.
2. For each significant field f in your object (each field taken into account by the equals method), do the following:
	a. Compute an int hash code c for the field:
		i. If the field is a boolean, compute (f ? 1 : 0).
		
		ii. If the field is a byte, char, short, or int, compute (int) f.
		
		iii. If the field is a long, compute (int) (f ^ (f >>> 32)).
		
		iv. If the field is a float, compute Float.floatToIntBits(f).
		
		v. If the field is a double, compute Double.doubleToLongBits(f), and then hash the resulting long as in step 2.a.iii.
		
		vi. If the field is an object reference and this class’s equals method compares the field by recursively invoking equals, recursively invoke hashCode on the field. If a more complex comparison is required, compute a “canonical representation” for this field and invoke hashCode on the canonical representation. If the value of the field is null, return 0 (or some other constant, but 0 is traditional).
		
		vii. If the field is an array, treat it as if each element were a separate field. That is, compute a hash code for each significant element by applying these rules recursively, and combine these values per step 2.b. If every element in an array field is significant, you can use one of the
		Arrays.hashCode methods added in release 1.5.
		
		b. Combine the hash code c computed in step 2.a into result as follows:
			result = 31 * result + c;
			
Note:
-----
You must exclude any fields that are not used in equals comparisons. A nonzero initial value is used in step 1 so the hash value 31* result will give some integer even if c becomes 0. The value 17 is arbitrary. The multiplication in step 2.b makes the result depend on the order of the fields, yielding a much better hash function if the class has multiple similar fields. The value 31 was chosen because it is an odd prime. If it were even and the multiplication overflowed, information would be lost, as multiplication by 2 is equivalent to shifting. The advantage of using a prime is less clear, but it is traditional.

Let's apply it for our phone number class.

	@Override public int hashCode() {
		int result = 17;
		result = 31 * result + areaCode;
		result = 31 * result + prefix;
		result = 31 * result + lineNumber;
		return result;
	}
				
				Item 10: Always override toString
				----------------------------------------

The toString contract goes on to say, “It is recommended that all subclasses override this method.” Good advice, indeed!

providing a good toString implementation makes your class much more pleasant to use. The toString method is automatically invoked when an object is passed to println, printf, the string concatenation operator, or assert, or printed by a debugger.

If you’ve provided a good toString method for PhoneNumber, generating a useful diagnostic message is as easy as this:
		System.out.println("Failed to connect: " + phoneNumber);
		
The disadvantage of specifying the format of the toString return value is that once you’ve specified it, you’re stuck with it for life, assuming your class is widely used. Programmers will write code to parse the representation, to generate it, and to embed it into persistent data. If you change the representation in a future release, you’ll break their code and data, and they will yowl. By failing to specify a format, you preserve the flexibility to add information or improve the format in a subsequent release.
	
Whether or not you decide to specify the format, you should clearly document your intentions.Also, provide programmatic access to all of the information contained in the value returned by toString, otherwise you force programmers who need this information to parse the string.
				
				Item 11: Override clone judiciously
				-----------------------------------------
The Cloneable interface was intended as a mixin interface (Item 18) for objects to advertise that they permit cloning. Unfortunately, it fails to serve this purpose. Its primary flaw is that it lacks a clone method, and Object’s clone method is protected.	you cannot invoke the clone method just because it implements cloneable.

Cloneable determines the behavior of Object’s protected clone implementation. If a class implements Cloneable, Object’s clone method returns a field-by-field copy of the object; otherwise it throws CloneNotSupportedException.

Note:Normally, implementing an interface says something about what a class can do for its clients. In the case of Cloneable, it modifies the behavior of a protected method on a superclass.

Here it is, copied from the specification for java.lang.Object [JavaSE6]:
For any object x,the expression
	x.clone() != x
will be true, and the expression
	x.clone().getClass() == x.getClass()
will be true, but these are not absolute requirements. While it is typically the case that
	x.clone().equals(x)
will be true, this is not an absolute requirement.

There are a number of problems with this contract. The provision that “no constructors are called” is too strong. A well-behaved clone method can call constructors to create objects internal to the clone under construction. If the class is final, clone can even return an object created by a constructor.

The provision that x.clone().getClass() should generally be identical to x.getClass(), however, is too weak. In practice, programmers assume that if they extend a class and invoke super.clone from the subclass, the returned object will be an instance of the subclass. The only way a superclass can provide this functionality is to return an object obtained by calling super.clone. If a clone method of any super class returns an object created by a constructor(instead of calling super.clone()), it will have the wrong class. Therefore, if you override the clone method in a nonfinal class, you should return an object obtained by invoking super.clone. If all of a class’s superclasses obey this rule, then invoking super.clone will eventually invoke Object’s clone method, creating an instance of the right class. This mechanism is vaguely similar to automatic constructor chaining, except that it isn’t enforced.

In practice, a class that implements Cloneable is expected to provide a properly functioning public clone method. It is not, in general, possible to do so unless all of the class’s superclasses provide a well-behaved clone implementation, whether public or protected.

If every field contains a primitive value or a reference to an immutable object, the returned object may be exactly what you need, in which case no further processing is necessary. In this case, all you need do in addition to declaring that you implement Cloneable is to provide public access to Object’s protected clone method: For ex:

	@Override public PhoneNumber clone() {
		try {
				return (PhoneNumber) super.clone();
			}
		catch(CloneNotSupportedException e) {
				throw new AssertionError(); // Can't happen
			}
		}

Note that the above clone method returns PhoneNumber, not Object. As of release 1.5, it is legal. This is far preferable to requiring every caller of PhoneNumber.clone to cast the result.The general principle at play here is never make the client do anything the library can do for the client.

If an object contains fields that refer to mutable objects, using the simple clone implementation shown above can be disastrous.

If an object contains fields that refer to mutable objects, using the simple clone implementation shown above can be disastrous.For example, consider the Stack class

	public class Stack {
		private Object[] elements;
		private int size = 0;
		private static final int DEFAULT_INITIAL_CAPACITY = 16;
		}
Suppose you want to make this class cloneable. If its clone method merely returns super.clone(), the resulting Stack instance will have the correct value in its size field, but its elements field will refer to the same array as the original Stack instance.You will quickly find that your program produces nonsensical results or throws a NullPointerException.

The easiest way to solve this is to call clone recursively on the elements array:

	@Override public Stack clone() {
		try {
			Stack result = (Stack) super.clone();
			result.elements = elements.clone();
			return result;
		} catch (CloneNotSupportedException e) {
			throw new AssertionError();
		}
	}

***
Note also that the above solution would not work if the elements field were final, because clone would be prohibited from assigning a new value to the field.This is a fundamental problem:the clone architecture is incompatible with normal use of final fields referring to mutable objects, except in cases where the mutable objects may be safely shared between an object and its clone.In order to make a class cloneable, it may be necessary to remove final modifiers from some fields.

***
Like a constructor, a clone method should not invoke any nonfinal methods on the clone under construction (Item 17). If clone invokes an overridden method, this method will execute before the subclass in which it is defined has had a chance to fix its state in the clone, quite possibly leading to corruption in the clone and the original.
***
Object’s clone method is declared to throw CloneNotSupportedException,but overriding clone method can omit this declaration. Public clone methods should omit it because methods that don’t throw checked exceptions are easier to use.
***
If a class that is designed for inheritance (Item 17) overrides clone,the overriding method should mimic the behavior of Object.clone: it should be declared protected, it should be declared to throw CloneNotSupportedException, super.clone() should be the first stament in the method and the class should not implement Cloneable. This gives subclasses the freedom to implement Cloneable or not, just as if they extended Object directly.

If you decide to make a thread-safe class implement Cloneable, remember that its clone method must be properly synchronized just like any other method.
**
It doesn’t make sense for immutable classes to support object copying, because copies would be virtually indistinguishable from the original.

Copy Constructors:
----------------------
A fine approach to object copying is to provide a copy constructor or copy factory. A copy constructor is simply a constructor that takes a single argument whose type is the class containing the constructor, for example,     
			 public Yum(Yum yum);
A copy factory is the static factory analog of a copy constructor: 
             public static Yum newInstance(Yum yum);
**
The copy constructor approach and its static factory variant have many advantages over Cloneable/clone: they don’t rely on a risk-prone extralinguistic object creation mechanism; they don’t demand unenforceable adherence to thinly documented conventions; they don’t conflict with the proper use of final fields;they don’t throw unnecessary checked exceptions; and they don’t require casts.

Furthermore, a copy constructor or factory can take an argument whose type is an interface implemented by the class.Interface-based copy constructors and factories, more properly known as conversion constructors and conversion factories.

Suppose you have a HashSet s, and you want to copy it as a TreeSet. The clone method can’t offer this functionality, but it’s easy with a conversion constructor: new TreeSet(s).

				Item 12: Consider implementing Comparable
				-----------------------------------------------------
Comparable is similar in character to Object’s equals method, except that it permits order comparisons in addition to simple equality comparisons, also it is generic .By implementing Comparable, a class indicates that its instances have a natural ordering.Sorting an array of objects that implement Comparable is as simple as this:
		Arrays.sort(a);
The other advantages include easy search, compute extreme values ( min and max) and maintain automatically sorted collections (Ex:TreeSet).

If you are writing a value class with an obvious natural ordering, such as alphabetical order, numerical order, or chronological order, you should strongly consider implementing the interface:
	public interface Comparable<T> {
		int compareTo(T t);
	}
The general contract of the compareTo method is similar to that of equals: Compares this object with the specified object for order. Returns a negative integer,zero, or a positive integer as this object is less than, equal to, or greater than the specified object. Throws ClassCastException if the specified object’s type prevents it from being compared to this object.

Here is the contract:
------------------------
i)sgn(x.compareTo(y)) == -sgn(y.compareTo(x)) for all x and y.
ii)Transitive: (x.compareTo(y) > 0 && y.compareTo(z) > 0) implies x.compareTo(z) > 0.
iii)x.compareTo(y) == 0 implies that sgn(x.compareTo(z)) == sgn(y.compareTo(z)), for all z.
iv)It is strongly recommended, but not strictly required, that (x.compareTo(y) == 0) == (x.equals(y)).

Just as a class that violates the hashCode contract can break other classes that depend on hashing, a class that violates the compareTo contract can break other classes that depend on comparison. Classes that depend on comparison include the sorted collections TreeSet and TreeMap, and the utility classes Collections and Arrays, which contain searching and sorting algorithms.

The equality test imposed by a compareTo method must obey the same restrictions imposed by the equals contract: reflexivity, symmetry, and transitivity.Therefore the same caveat applies: there is no way to extend an instantiable class with a new value component while preserving the compareTo contract, unless you are willing to forgo the benefits of object-oriented abstraction.

If the last provision(4th one) is obeyed, the ordering imposed by the compareTo method is said to be consistent with equals. If it’s violated, the ordering is said to be inconsistent with equals. Because the general contracts for the interfaces like Collection, Set and Map are defined in terms of the equals method, but sorted collections use the equality test imposed by compareTo in place of equals.
***
For example, consider the BigDecimal class, whose compareTo method is inconsistent with equals.If you create a HashSet instance and add new BigDecimal("1.0") and new BigDecimal("1.00"), the set will contain two elements because they are unequal when compared using equals method, the same with TreeSet contains one instance, because they are equal when compared using compareTo() method.
***
Instructions for writing compareTo() method:
-----------------------------------------------------
The field comparisons in a compareTo method are order comparisons rather than equality comparisons
i)Compare object reference fields by invoking the compareTo method recursively.
ii)For integral primitives use the relational operators < and >.
iii)For floating-point fields, use Double.compare or Float.compare.
iv) For array fields, apply the above guidelines to each element.

If a class has multiple significant fields, the order in which you compare them is critical. You must start with the most significant field and work your way down.If the most significant fields are equal, go on to compare the next-most-significant fields, and so on. If all fields are equal, the objects are equal; return zero.

							Item 13: Minimize the accessibility of classes and members
							----------------------------------------------------------------------
****
A well-designed module hides all of its implementation details, cleanly separating its API from its implementation. Modules then communicate only through their APIs and are oblivious to each others inner workings. This concept, known as information hiding or encapsulation.

Information hiding decouples the modules that comprise a system, allowing them to be developed, tested, optimized, used, understood, and modified in isolation.	This speeds up system development because modules can be developed in parallel. It eases the burden of maintenance because modules can be understood more quickly and debugged with little fear of harming other modules. a system is complete and profiling has determined which modules are causing performance problems , those modules can be optimized without affecting the correctness of other modules.Information hiding increases software reuse because modules that aren’t tightly coupled often prove useful in other contexts besides the ones for which they were developed. Finally, information hiding decreases the risk in building large systems, because individual modules may prove successful even if the system does not.
***
The rule of thumb is simple: make each class or member as inaccessible as possible.

By making it package-private, you make it part of the implementation rather than the exported API, and you can modify it, replace it, or eliminate it in a subsequent release without fear of harming existing clients. If you make it public, you are obligated to support it forever to maintain compatibility.

If a package-private top-level class (or interface) is used by only one class, consider making the top-level class a private nested class of the sole class that uses it (Item 22). This reduces its accessibility from all the classes in its package to the one class that uses it.

Both private and package-private members are part of a class’s implementation and do not normally impact its exported API. These fields can, however, “leak” into the exported API if the class implements Serializable.

For members of public classes, a huge increase in accessibility occurs when the access level goes from package-private to protected. A protected member is part of the class’s exported API and must be supported forever.

To facilitate testing, you may be tempted to make a class, interface, or member more accessible. This is fine up to a point. It is acceptable to make a private member of a public class package-private in order to test it, but it is not acceptable to raise the accessibility any higher than that.

Instance fields should never be public.Even if a field is final and refers to an immutable object, by making the field public you give up the flexibility to switch to a new internal data representation(in next release) in which the field does not exist.

The same advice applies to static fields, with the one exception.You can expose constants via public static final fields(see the example in the next Item).A static final field containing a reference to a mutable object has all the disadvantages of a nonfinal field.Note that a nonzero-length array is always mutable, so it is wrong for a class to have a public static final array field, or an accessor that returns such a field.

						Item 14: In public classes, use accessor methods, not public fields
						-------------------------------------------------------------------------------
Ocassionally, we write degenerate(independent) classes to group instance fields. For ex,
		class Point {
			public double x;
			public double y;
		}
This class do not offer the benefits of encapsulation. i.e. you cannot change the representation(change the fields) without changing API, you can't enforce invariants, and you can’t take auxiliary action when a field is accessed. This class should be replaced with private fields and public accessor methods.

If a public class exposes its data fields, all hope of changing its representation is lost, as client code can be distributed far and wide.However, if a class is package-private or is a private nested class, there is nothing inherently wrong with exposing its data fields—assuming they do an adequate job of describing the abstraction provided by the class.If a change in representation becomes desirable, you can make the change without touching any code outside the package. In the case of a private nested class, the scope of the change is further restricted to the enclosing class.

While it’s never a good idea for a public class to expose fields directly, it is less harmful if the fields are immutable. i.e. you cannot change the internal representation, you can't take auxially action when field is read but you can force invariants.

	public final class Time {
		private static final int HOURS_PER_DAY = 24;
		private static final int MINUTES_PER_HOUR = 60;
		public final int hour;
		public final int minute;
		public Time(int hour, int minute) {
			if (hour < 0 || hour >= HOURS_PER_DAY)
				throw new IllegalArgumentException("Hour: " + hour);
			if (minute < 0 || minute >= MINUTES_PER_HOUR)
				throw new IllegalArgumentException("Min: " + minute);
			this.hour = hour;
			this.minute = minute;
		}
		... // Remainder omitted
	}
	
							Item 15: Minimize mutability
							----------------------------------
							
An immutable class is simply a class whose instances cannot be modified. All of the information contained in each instance is provided when it is created and is fixed for the lifetime of the object . There are many good reasons for this: Immutable classes are easier to design, implement, and use than mutable classes. They are less prone to error and are more secure.

To make a class immutable follow these five rules.

1)Don’t provide any methods that modify the object’s state i.e. setter methods.
2)Ensure that the class can’t be extended . This prevents careless subclasses from compromising the immutable behaviour of the super class by behaving as if the object's state has changed. 
Example: It was not widely understood that immutable classes had to be effectively final when BigInteger and BigDecimal were written, so all of their methods may be overridden. Unfortunately, this could not be corrected after the fact while preserving backward compatibility. If you write a class whose security depends on the immutability of a BigInteger or BigDecimal argument from an untrusted client, you must check to see that the argument is a “real” BigInteger or BigDecimal, rather than an instance of an untrusted subclass. If it is the latter, you must defensively copy it under the assumption that it might be mutable :
	public static BigInteger safeInstance(BigInteger val) {
		if (val.getClass() != BigInteger.class)
			return new BigInteger(val.toByteArray());
		return val;
	}
3)Make all fields final.
4)Make all fields private. 
***
Note: While it is technically permissible for immutable classes to have public final fields containing primitive values or references to immutable objects, it is not recommended because it precludes changing the internal representation in a later release.
5)Ensure exclusive access to any mutable components. If your class has any fields that refer to mutable objects, ensure that clients of the class cannot obtain references to these objects. Never initialize such a field to a client-provided object reference or return the object reference from an accessor. Make defensive copies (Item 39) in constructors, accessors, and readObject methods.

Advantages of Immutable Objects:
-----------------------------------------
Immutable objects are simple.
------------------------------------
An immutable object can be in exactly one state, the state in which it was created. If you make sure that all constructors establish class invariants, then it is guaranteed that these invariants will remain true for all time, with no further effort on your part or on the part of the programmer who uses the class. Mutable objects, on the other hand, can have arbitrarily complex state spaces. If the documentation does not provide a precise description of the state transitions performed by mutator methods, it can be difficult or impossible to use a mutable class reliably.

Immutable objects are inherently thread-safe; they require no synchronization.
---------------------------------------------------------------------------------------------
They cannot be corrupted by multiple threads accessing them concurrently. This is far and away the easiest approach to achieving thread safety. In fact, no thread can ever observe any effect of another thread on an immutable object.
***
Immutable objects can be reused, which will reduce memory footprint and garbage collection costs
---------------------------------------------------------------------------------------------------------------------
An immutable class can provide static factories (Item 1) that cache frequently requested instances to avoid creating
new instances when existing ones would do. We have seen this in Item 1 for Boolean.valueOf(String s). This method returns a boolean value that was created before if one instance already exists.
***
Note: A consequence of reuse is that you never have to make defensive copies. In fact, you never have to make any copies at all because the copied would be forever equal to the original ones. Therefore Immutable classes does not have to provide clone() or copy constructors.

Not only can you share immutable objects, but you can share their internals. For example, 
		BigInteger i1 = new BigInteger("10000001010");
		BigInteger i2 = i1.negate();
negate method will simply change sign of the object value on which it is invoked, but the magnitude is same. so, i1 and i2 can share the same value. In fact, this is what happens inside.

Immutable objects make great building blocks for other objects:
----------------------------------------------------------------------------
A special case of this principle is that immutable objects make great map keys and set elements: you don’t have to worry about their values changing once they’re in the map or set, which would destroy the map or set’s invariants.

Disadvantages:
------------------
The only real disadvantage of immutable classes is that they require a separate object for each distinct value. The performance problem is magnified if you perform a multistep operation that generates a new object at every step, eventually discarding all objects except the final result.

The solution to this problem is to provide a mutable companion class approach. i.e. String class has a mutable companion StringBuilder which solves this problem.

					Item 16: Favor composition over inheritance
					----------------------------------------------------
					
Inheritance is a powerful way to achieve code reuse, but it is not always the best tool for the job. Inheriting from ordinary concrete classes across package boundaries, however, is dangerous.

Unlike method invocation, inheritance violates encapsulation:
-------------------------------------------------------------------------
In other words, a subclass depends on the implementation details of its superclass for its proper function. The superclass’s implementation may change from release to release, and if it does, the subclass may break, even though its code has not been touched. As a consequence, a subclass must evolve in tandem with its superclass, unless the superclass’s authors have designed and documented it specifically for the purpose of being extended.

let’s suppose we have a program that uses a HashSet. To tune the performance of our program, we need to query the HashSet as to how many elements have been added since it was created. So, we need to override add and addAll() methods for this.

	// Broken - Inappropriate use of inheritance!
	public class InstrumentedHashSet<E> extends HashSet<E> {
		// The number of attempted element insertions
		private int addCount = 0;
		public InstrumentedHashSet() {
		}
		public InstrumentedHashSet(int initCap, float loadFactor) {
			super(initCap, loadFactor);
		}
		@Override public boolean add(E e) {
			addCount++;
			return super.add(e);
		}
		@Override public boolean addAll(Collection<? extends E> c) {
			addCount += c.size();
			return super.addAll(c);
		}
		public int getAddCount() {
			return addCount;
		}
	}

This class looks reasonable, but it doesn’t work. Suppose we create an instance and add three elements using the addAll method:
	InstrumentedHashSet<String> s = new InstrumentedHashSet<String>();
	s.addAll(Arrays.asList("Snap", "Crackle", "Pop"));	
We would expect the getAddCount method to return three at this point, but it returns six. Because, Internally addAll() method of calls super class's (HashSet) addAll() method, which inturn calls add() method of HashSet, but due to overriding, add() method of InstrumentedHashSet is called, which will add another 3 to the count.

We could fix this issue simply by not overriding addAll() method, but we should remember that the addAll() method of HashSet for its proper functioning depends on add() method.May be in a future release this may vary.

It would be slightly better to override the addAll method to iterate over the specified collection, calling the add method once for each element. This technique, however, does not solve all our problems. It amounts to reimplementing superclass methods that may or may not self-use, which is difficult, time-consuming, and error-prone.Additionally, it isn’t always possible, as some methods cannot be implemented without access to private fields inaccessible to the subclass.

Suppose a program depends for its security on the fact that all elements inserted into some collection satisfy some predicate. This can be guaranteed by subclassing the collection and overriding each method capable of adding an element to ensure that the predicate is satisfied before adding the element.This works fine until a new method capable of inserting an element is added to the superclass in a subsequent release. Once this happens, it becomes possible to add an “illegal” element merely by invoking the new method, which is not overridden in the subclass.

It seems like , it is safe to extend a class and add new methods without overriding existing ones. This approach has its own problems. For ex, if a new method is added to the super class with same signature but different return type, your class will not compile. If the new method of super class has same signature as that of your subclass now it becomes overriding , so the above two problems may occur. 
***
The solution to the above problems is Composition.Instead of extending an existing class, give your new class a private field that references an instance of the existing class.This design is called composition because the existing class becomes a component of the new one.Each instance method in the new class invokes the corresponding method on the contained instance of the existing class and returns the results. This is known as forwarding, and the methods in the new class are known as forwarding methods.

To make this concrete, here’s a replacement for InstrumentedHashSet that uses the composition-and-forwarding approach. Note that the implementation is broken into two pieces, the class itself and a reusable forwarding class, which contains all of the forwarding methods and nothing else:

	// Reusable forwarding class
	public class ForwardingSet<E> implements Set<E> {
		private final Set<E> s;
		public ForwardingSet(Set<E> s) { this.s = s; }
		public void clear() { s.clear(); }
		public boolean contains(Object o) { return s.contains(o); }
		public boolean isEmpty() { return s.isEmpty(); }
		public int size() { return s.size(); }
		public Iterator<E> iterator() { return s.iterator(); }
		public boolean add(E e) { return s.add(e); }
		public boolean remove(Object o) { return s.remove(o); }
		public boolean containsAll(Collection<?> c)
		{ return s.containsAll(c); }
		public boolean addAll(Collection<? extends E> c)
		{ return s.addAll(c); }
		public boolean removeAll(Collection<?> c)
		{ return s.removeAll(c); }
		public boolean retainAll(Collection<?> c)
		{ return s.retainAll(c); }
		public Object[] toArray() { return s.toArray(); }
		public <T> T[] toArray(T[] a) { return s.toArray(a); }
		@Override public boolean equals(Object o)
		{ return s.equals(o); }
		@Override public int hashCode() { return s.hashCode(); }
		@Override public String toString() { return s.toString(); }
	}

Instead of extending a concrete implementation , the above forwarding class used composition . i.e. It has Set<E> reference defined.

	// Wrapper class - uses composition in place of inheritance
	public class InstrumentedSet<E> extends ForwardingSet<E> {
		private int addCount = 0;
		public InstrumentedSet(Set<E> s) {
			super(s);
		}
		@Override public boolean add(E e) {
			addCount++;
			return super.add(e);
		}
		@Override public boolean addAll(Collection<? extends E> c) {
			addCount += c.size();
			return super.addAll(c);
		}
		public int getAddCount() {
			return addCount;
		}
	}

The design of the InstrumentedSet class is enabled by the existence of the Set interface.Besides being robust, this design is extremely flexible.The InstrumentedSet class implements the Set interface and has a single constructor whose argument is also of type Set.Unlike the inheritance-based approach, which works only for a single concrete class and requires a separate constructor for each supported constructor in the superclass, the wrapper class can be used to instrument any Set implementation and will work in conjunction with any preexisting constructor.
		Set<Date> s = new InstrumentedSet<Date>(new TreeSet<Date>(cmp));
		Set<E> s2 = new InstrumentedSet<E>(new HashSet<E>(capacity));
		
The InstrumentedSet class is known as a wrapper class because each InstrumentedSet instance contains (“wraps”) another Set instance.

Disadvantages:
------------------
wrapper classes are not suited for use in callback frameworks, wherein objects pass selfreferences to other objects for subsequent invocations (“callbacks”). Because a wrapped object doesn’t know of its wrapper, it passes a reference to itself (this) and callbacks elude the wrapper. This is known as the SELF problem.

Some people worry about the performance impact of forwarding method invocations or the memory footprint impact of wrapper objects.

Inheritance is appropriate only in circumstances where the subclass really is a subtype of the superclass.In other words, a class B should extend a class A only if an “is-a” relationship exists between the two classes.There are a number of obvious violations of this principle in the Java platform libraries. For example, a stack is not a vector, so Stack should not extend Vector.

If you use inheritance where composition is appropriate, you needlessly expose implementation details. The resulting API ties you to the original implementation, forever limiting the performance of your class.More seriously, by exposing the internals you let the client access them directly. At the very least, this can lead to confusing semantics.  For ex, Properties class extends Hashtable. If p refers to a Properties instance, then p.getProperty(key) may return different results from p.get(key). The former takes defaults into account. Most, seriously the client can be able to corrupt invariants of the subclass by modifying the superclass directly. In the case of Properties, the designers intended that only strings be
allowed as keys and values. But direct access to the underlying Hashtable allows this invariant to be violated. Once this invariant is violated, it is no longer possible to use other parts of the Properties API (load and store).

			Item 17: Design and document for inheritance or else prohibit it
			----------------------------------------------------------------------------

A class must document its self-use of overridable methods.For each public or protected method or constructor, the documentation must indicate which overridable methods are invoked by other methods or constructor.Also, it must document under what circumstances these methods are invoked(sometimes invocation may come from background threads and staic initializers). Here’s an example, copied from the specification for java.util.AbstractCollection:
	public boolean remove(Object o)
Removes a single instance of the specified element from this collection, if it is present (optional operation). More formally, removes an element e such that (o==null ? e==null : o.equals(e)), if the collection contains one or more such elements. Returns true if the collection contained the specified element (or equivalently, if the collection changed as a result of the call).	
This implementation iterates over the collection looking for the specified element. If it finds the element, it removes the element from the collection using the iterator’s remove method. Note that this implementation throws an UnsupportedOperationException if the iterator returned by this collection’s iterator method does not implement the remove method.
***
This documentation leaves no doubt that overriding the iterator method will affect the behavior of the remove method. Furthermore, it describes exactly how the behavior of the Iterator returned by the iterator method will affect the behavior of the remove method. Contrast to this, in the previous item, overriding add method affect the behaviour of super class addAll() method.

Design for inheritance involves more than just documenting patterns of selfuse. To allow programmers to write efficient subclasses without undue pain a class may have to provide hooks into its internal workings in the form of judiciously chosen protected methods or, in rare instances, protected fields. You should expose as few protected members as possible, because each one represents a commitment to an implementation detail.

The only way to test a class designed for inheritance is to write subclasses.When you design for inheritance a class that is likely to achieve wide use, realize that you are committing forever to the self-use patterns that you document and to the implementation decisions implicit in its protected methods and fields.These commitments can make it difficult or impossible to improve the performance or functionality of the class in a subsequent release. Therefore, you must test your class by writing subclasses before you release it.
***
Constructors must not invoke overridable methods, directly or indirectly.The superclass constructor runs before the subclass constructor, so the overriding method in the subclass will get invoked before the subclass constructor has run.If the overriding method depends on any initialization performed by the subclass constructor, the method will not behave as expected. To make this concrete, here’s a class that violates this rule:
	public class Super {
		// Broken - constructor invokes an overridable method
		public Super() {
			overrideMe();
		}
		public void overrideMe() {
		}
	}

	public final class Sub extends Super {
		private final Date date; // Blank final, set by constructor
		Sub() {
			date = new Date();
		}
		// Overriding method invoked by superclass constructor
		@Override public void overrideMe() {
			System.out.println(date);
		}
		public static void main(String[] args) {
			Sub sub = new Sub();
			sub.overrideMe();
		}
	}
	
It prints out null the first time, because the overrideMe method is invoked by the Super constructor before the Sub constructor has a chance to initialize the date field.
***
If you do decide to implement Cloneable or Serializable in a class designed for inheritance, you should be aware that because the clone and readObject methods behave a lot like constructors, a similar restriction applies: neither clone nor readObject may invoke an overridable method, directly or indirectly.In the case of the readObject method, the overriding method will run before the subclass’s state has been deserialized. In the case of the clone method, the overriding method will run before the subclass’s clone method has a chance to fix the clone’s state. In either case, a program failure is likely to follow. In the case of clone, the failure can damage the original object as well as the clone.

Finally, if you decide to implement Serializable in a class designed for inheritance and the class has a readResolve or writeReplace method, you must make the readResolve or writeReplace method protected rather than private. If these methods are private, they will be silently ignored by subclasses.This is one more case where an implementation detail becomes part of a class’s API to permit inheritance.
***
Ordinary classes neither final nor designed and documented for inheritance. Each time a change is made in such a class the client classes that extend the class will break. So, better prohibit inheritance by making a class final or using a private constructor. 

This advice may be somewhat controversial, as many programmers have grown accustomed to subclassing ordinary concrete classes to add facilities such as instrumentation, notification, and synchronization or to limit functionality. If a class implements some interface that captures its essence, such as Set, List, or Map, then we can prohibit subclassing. Becuase, if you want to add a new functionality, use wrapper design pattern used in the previous item.

If a concrete class does not implement a standard interface, then you may inconvenience some programmers by prohibiting inheritance. If you feel that you must allow inheritance from such a class, one reasonable approach is to ensure that the class never invokes any of its overridable methods and to document this fact.

					Item 18: Prefer interfaces to abstract classes
					------------------------------------------------------
Advantages of Interface over abstract class:
----------------------------------------------------
Existing classes can be easily retrofitted to implement a new interface.
------------------------------------------------------------------------------------
All you have to do is add the required methods if they don’t yet exist and add an implements clause to the class declaration. For example, many existing classes were retrofitted to implement the Comparable interface when it was introduced into the platform. Existing classes cannot, in general be retrofitted to extend a new abstract class because they might have already extended a class.
Interfaces are ideal for defining mixins.
----------------------------------------------
Loosely speaking, a mixin is a type that a class can implement in addition to its “primary type” to declare that it provides some optional behavior. For example, Comparable is a mixin interface that allows a class to declare that its instances are ordered with respect to other mutually comparable objects.
Interfaces allow the construction of nonhierarchical type frameworks:
----------------------------------------------------------------------------------
For example, suppose we have an interface representing a singer and another representing a songwriter:
	public interface Singer {
		AudioClip sing(Song s);
	}
	public interface Songwriter {
		Song compose(boolean hit);
	}
In real life, some singers are also songwriters. Because we used interfaces rather than abstract classes to define these types, it is perfectly permissible for a single class to implement both Singer and Songwriter. In fact, we can define a third interface that extends both Singer and Songwriter and adds new methods that are appropriate to the combination:	
	public interface SingerSongwriter extends Singer, Songwriter {
		AudioClip strum();
		void actSensitive();
	}
The alternative is a bloated class hierarchy containing a separate class for every supported combination of attributes. Bloated class hierarchies can lead to bloated classes containing many methods that differ only in the type of their arguments, as there are no types in the class hierarchy to capture common behaviors.

Interfaces enable safe, powerful functionality enhancements via the wrapper class idiom:
----------------------------------------------------------------------------------------------------------
If you use abstract classes to define types, you leave the programmer who wants to add functionality with no alternative but to use inheritance.

Note:
------
You can combine the virtues of interfaces and abstract classes by providing an abstract skeletal implementation class to go with each nontrivial interface that you export.The interface still defines the type, but the skeletal implementation takes all of the work out of implementing it. For example, the Collections Framework provides a skeletal implementation to go along with each main collection interface: AbstractCollection, AbstractSet, AbstractList, and AbstractMap.

When properly designed, skeletal implementations can make it very easy for programmers to provide their own implementations of your interfaces. For example, here’s a static factory method containing a complete, fully functional List implementation:

	// Concrete implementation built atop skeletal implementation
	static List<Integer> intArrayAsList(final int[] a) {
		if (a == null)
			throw new NullPointerException();
		return new AbstractList<Integer>() {
			public Integer get(int i) {
				return a[i]; // Autoboxing (Item 5)
			}
			@Override public Integer set(int i, Integer val) {
				int oldVal = a[i];
				a[i] = val; // Auto-unboxing
				return oldVal; // Autoboxing
			}
			public int size() {
				return a.length;
			}
		};
	}

*****
The beauty of skeletal implementations is that they provide the implementation assistance of abstract classes without imposing the severe constraints that abstract classes impose when they serve as type definitions.	For most implementors of an interface, extending skeletal implementation is an obvious choice but it is optional. If a preexisting class cannot be made to extend the skeletal implementation, the class can always implement the interface manually. Furthermore, the skeletal implementation can still help the implementor’s task.The class implementing the interface can forward invocations of interface methods to a contained instance of a private inner class that extends the skeletal implementation. This technique, known as simulated multiple inheritance, is closely related to the wrapper class idiom.

Advantage of abstract class:
----------------------------------
It is far easier to evolve an abstract class than an interface. i.e. If, in a subsequent release, you want to add a new method to an abstract class, you can always add a concrete method containing a reasonable default implementation.All existing implementations of the abstract class will then provide the new method. This does not work for interfaces.It is, generally speaking, impossible to add a method to a public interface without breaking all existing classes that implement the interface.
***
Public interfaces, therefore, must be designed carefully. Once an interface is released and widely implemented, it is almost impossible to change.

					Item 19: Use interfaces only to define types
					-----------------------------------------------------
When a class implements an interface, the interface serves as a type that can be used to refer to instances of the class. A class implements an interface should therefore say something about what a client can do with instances of the class. It is inappropriate to define an interface for any other purpose.

One kind of interface that fails this test is the so-called constant interface. Such an interface contains no methods; it consists solely of static final fields, each exporting a constant.

The constant interface pattern is a poor use of interfaces i.e. If a class implements such an interface is nothing but exposing implementaion details. Implementing a constant interface causes this implementation detail to leak into the class’s exported API.If in a future release the class is modified so that it no longer needs to use the constants, it still must implement the interface to ensure binary compatibility. If a nonfinal class implements a constant interface, all of its subclasses will have their namespaces polluted by the constants in the interface. Java API has java.io.ObjectStreamConstants.

Note:If you want to export constants use utility class or Enums.

						Item 21: Use function objects to represent strategies
						---------------------------------------------------------------

Invoking a method on an object typically performs some operation on that object. However, it is possible to define an object whose methods perform operations on other objects, passed explicitly to the methods. For ex, Collections.sort() method takes a collection and comparator . Based on comparator you pass in, different sort orders will result. This is a typical strategy design pattern. See the example

	class StringLengthComparator {
		private StringLengthComparator() { }
		public static final StringLengthComparator INSTANCE = new StringLengthComparator();
		public int compare(String s1, String s2) {
			return s1.length() - s2.length();
		}
	}

As this class has no instance fields , hence all instances of this class are functionally equivalent. Thus it should be a singleton to save on unnecessary object creation costs.

Therefore a primary use of function pointers is to implement the Strategy pattern. To implement this pattern in Java, declare an interface to represent the strategy, and a class that implements this interface for each concrete strategy.

Concrete strategy classes are often declared using anonymous classes.
Arrays.sort(stringArray, new Comparator<String>() {
			public int compare(String s1, String s2) {
				return s1.length() - s2.length();
			}});
But note that using an anonymous class in this way will create a new instance each time the call is executed. If it is to be executed repeatedly, consider storing the function object in a private static final field and reusing it. Another advantage of doing this is that you can give the field a descriptive name for the function object.			

Because the strategy interface serves as a type for all of its concrete strategy instances, a concrete strategy class needn’t be made public to export a concrete strategy. Instead, a “host class” can export a public static field (or static factory
method) whose type is the strategy interface, and the concrete strategy class can be a private nested class of the host.

	// Exporting a concrete strategy
	class Host {
		private static class StrLenCmp implements Comparator<String>, Serializable {
			public int compare(String s1, String s2) {
				return s1.length() - s2.length();
			}
		}
		// Returned comparator is serializable
		public static final Comparator<String> STRING_LENGTH_COMPARATOR = new StrLenCmp();
		... // Bulk of class omitted
	}
						Item 22: Favor static member classes over nonstatic
						--------------------------------------------------------------
A nested class should exist only to serve its enclosing class. If a nested class would be useful in some other context, then it should be a top-level class.

A static member class is the simplest kind of nested class.

One common use of a static member class is as a public helper class, useful only in conjunction with its outer class. For example, consider an enum describing the operations supported by a calculator (Item 30). The Operation enum should be a public static member class of the Calculator class. Clients of Calculator could then refer to operations using names like Calculator.Operation.PLUS and Calculator.Operation.MINUS.

Note:
Each instance of a nonstatic inner class is implicitly associated with an enclosing instance of its containing class.with in instance methods of a nonstatic inner class, you can invoke the enclosing(outer) class methods by using 'this' keyword. It is impossible to create an instance of a nonstatic inner class without the enclosing instance.

If an instance of an inner class can exist without an instance of outer class then it should be static inner class.

One common use of a nonstatic member class is to define an Adapter that allows an instance of the outer class to be viewed as an instance of some unrelated class. For example, implementations of the Map interface typically use nonstatic member classes to implement their collection views,which are returned by Map’s keySet, entrySet, and values methods. for ex,

		// Typical use of a nonstatic member class
		public class MySet<E> extends AbstractSet<E> {
			... // Bulk of the class omitted
			public Iterator<E> iterator() {
				return new MyIterator();
			}
			private class MyIterator implements Iterator<E> {
			...
			}
		}

Note:
If you declare a member class that does not require access to an enclosing instance, always put the static modifier in its declaration, making it static rather than a nonstatic member class. If you omit this modifier, each instance will have an extraneous reference to its enclosing instance.Storing this reference costs time and space, and can result in the enclosing instance being retained when it would otherwise be eligible for garbage collection.
****
A common use of private static member classes is to represent components of the object represented by their enclosing class. For example, consider a Map instance, which associates keys with values. Many Map implementations have an internal Entry object(entrySet() method) for each key-value pair in the map. While each entry is associated with a map, the methods on an entry (getKey, getValue, and setValue) do not need access to the map because once you got reference to the Entry you call methods on entry but not on Map.
***
It is doubly important to choose correctly between a static and a nonstatic member class if the class in question is a public or protected member of an exported class. In this case, the member class is an exported API element and cannot be changed from a nonstatic to a static member class in a subsequent release without violating binary compatibility.
***
Rather than being declared along with other members, it is simultaneously declared and instantiated at the point of use. Anonymous classes are permitted at any point in the code where an expression is legal. Anonymous classes have enclosing instances if and only if they occur in a nonstatic context. But even if they occur in a static context, they cannot have any static members.

There are many limitations on the applicability of anonymous classes.
1)You can’t instantiate them except at the point they’re declared.
2)You can’t perform instanceof tests or do anything else that requires you to name the class.
3)You can’t declare an anonymous class to implement multiple interfaces, or to extend a class and implement an interface at the same time.
4)Clients of an anonymous class can’t invoke any members except those it inherits from its supertype.
5)they must be kept short about ten lines or fewer—or readability will suffer.

Uses of Anonymous inner classes
---------------------------------------
1)One common use of anonymous classes is to create function objects (Item 21) on the fly.
2)Another common use of anonymous classes is to create process objects, such as Runnable, Thread, or TimerTask instances.
3)A third common use is within static factory methods.

Local classes are the least frequently used of the four kinds of nested classes.Like member classes, they have names and can be used repeatedly. Like anonymous classes, they have enclosing instances only if they are defined in a nonstatic context, and they cannot contain static members.

								Item 23: Don’t use raw types in new code
								-------------------------------------------------
If you use raw types, you lose all the safety and expressiveness benefits of generics. Given that you shouldn’t use raw types, why did the language designers allow them? To provide compatibility.

While you shouldn’t use raw types such as List in new code, it is fine to use types that are parameterized to allow insertion of arbitrary objects, such as List<Object>. The difference between the two is you can pass a List<String> to a method whose parameter is of type List, but you can’t pass it to a parameter of type List<Object>. There are subtyping rules for generics. i.e. List<String> is a subtype of List, but not List<Object>. As a consequence you loose all the type safety with raw List. For ex,
		// Uses raw type (List) - fails at runtime!
		public static void main(String[] args) {
			List<String> strings = new ArrayList<String>();
			unsafeAdd(strings, new Integer(42));
			String s = strings.get(0); // Compiler-generated cast
		}
		private static void unsafeAdd(List list, Object o) {
			list.add(o);
		}
This program compiles, but because it uses the raw type List, you get a warning:
		Test.java:10: warning: unchecked call to add(E) in raw type List
		list.add(o);
				^		
If you run the program, you get a ClassCastException when the program tries to cast the result of the invocation strings.get(0) to a String. If you replace parameter's of unsafeAdd to List<string> and String, the above program won't compile.

For example, suppose you want to write a method that takes two sets and returns the number of elements they have in common.
	// Use of raw type for unknown element type - don't do this!
	static int numElementsInCommon(Set s1, Set s2) {
		int result = 0;
		for (Object o1 : s1)
			if (s2.contains(o1))
				result++;
		return result;
	}
***
If you want to use a generic type but you don’t know or care what the actual type parameter is, you can use a question mark instead. These are called unbounded wild card types. The difference is You can put any element into a collection with a raw type, easily corrupting the collection’s type invariant; you can’t put any element  (other than null) into a Collection<?>. Attempting to do so will generate a compile-time error message like this:
	WildCard.java:13: cannot find symbol
	symbol : method add(String)
	location: interface Collection<capture#825 of ?>
	c.add("verboten");
			^
You can’t assume anything about the type of the objects that you get out with unbounded wildcard types. If this restriction is unacceptable then go for bounded wild card types. There are two minor exceptions to the rule that you should not use raw types in new code.
1)You must use raw types in class literals. In other words, List.class, String[].class, and int.class are all legal, but List<String>.class and List<?>.class are not.
2)The second exception to the rule concerns the instanceof operator. Because generic type information is erased at runtime, it is illegal to use the instanceof operator on parameterized types other than unbounded wildcard types. For ex,
		// Legitimate use of raw type - instanceof operator
		if (o instanceof Set) { // Raw type
			Set<?> m = (Set<?>) o; // Wildcard type
		...
		}
Note that once you’ve determined that o is a Set, you must cast it to the wildcard type Set<?>, not the raw type Set. This is a checked cast, so it will not cause a compiler warning.	

								Item 24: Eliminate unchecked warnings
								-----------------------------------------------

	Set<Lark> exaltation = new HashSet();
The above line of code generates a warning by compiler like this. 
			Venery.java:4: warning: [unchecked] unchecked conversion
			found : HashSet, required: Set<Lark>
			Set<Lark> exaltation = new HashSet();
								 ^
You can then make the indicated correction, causing the warning to disappear:
			Set<Lark> exaltation = new HashSet<Lark>();
****			
Some warnings will be much more difficult to eliminate.Eliminate every unchecked warning that you can.If you can’t eliminate a warning, and you can prove that the code that provoked the warning is typesafe, then (and only then) suppress the warning with an @SuppressWarnings("unchecked") annotation. If you suppress warnings without first proving that the code is typesafe, you are only giving yourself a false sense of security. The code may compile without emitting any warnings, but it can still throw a ClassCastException at runtime.

Note:If you ignore unchecked warnings that you know to be safe , you won’t notice when a new warning that represents a real problem.
***
The SuppressWarnings annotation can be used at any granularity, from an individual local variable declaration to an entire class. Always use the Suppress Warnings annotation on the smallest scope possible. Typically this will be a variable declaration or a very short method or constructor. Never use Suppress Warnings on an entire class. Doing so could mask critical warnings.

If you find yourself using the SuppressWarnings annotation on a method or constructor that’s more than one line long, you may be able to move it onto a local variable declaration. For ex, consider this toArray method, which comes from ArrayList:

	public <T> T[] toArray(T[] a) {
		if (a.length < size)
			return (T[]) Arrays.copyOf(elements, size, a.getClass());
		System.arraycopy(elements, 0, a, 0, size);
		if (a.length > size)
			a[size] = null;
		return a;
	}
If you compile ArrayList, the method generates this warning:
	ArrayList.java:305: warning: [unchecked] unchecked cast found : Object[], required: T[]
		return (T[]) Arrays.copyOf(elements, size, a.getClass());
							^
It is illegal to put a SuppressWarnings annotation on the return statement, because it isn’t a declaration.You might be tempted to put the annotation on the entire method, but don’t. Instead, declare a local variable to hold the return value and annotate its declaration, like so:

// This cast is correct because the array we're creating is of the same type as the one passed in, which is T[].
	@SuppressWarnings("unchecked") T[] result = (T[]) Arrays.copyOf(elements, size, a.getClass());
Note:	
Every time you use an @SuppressWarnings("unchecked") annotation, add a comment saying why it’s safe to do so. This will help others understand the code, and more importantly, it will decrease the odds that someone will modify the code so as to make the computation unsafe.
						
						Item 25: Prefer lists to arrays
						-----------------------------------
***
Arrays differ from generic types in two important ways.Generics, by contrast, are invariant: for any two distinct types Type1 and Type2, List<Type1> is neither a subtype nor a supertype of List<Type2>.You might think this means that generics are deficient, but arguably it is arrays that are deficient.

This code fragment is legal: // Fails at runtime!
				Object[] objectArray = new Long[1];
				objectArray[0] = "I don't fit in"; // Throws ArrayStoreException
but this one is not: // Won't compile!
				List<Object> ol = new ArrayList<Long>(); // Incompatible types
				ol.add("I don't fit in");
				
Either way you can’t put a String into a Long container, but with an array you find out that you’ve made a mistake at runtime; with a list, you find out at compile time.
****
The second major difference between arrays and generics is that arrays are reified. This means that arrays know and enforce their element types at runtime.Generics, by contrast, are implemented by erasure . This means that they enforce their type constraints only at compile time and discard (or erase) their element type information at runtime. Erasure is what allows generic types to interoperate freely with legacy code that does not use generics.
***
Because of these fundamental differences, arrays and generics do not mix well. For example, it is illegal to create an array of a generic type, a parameterized type, or a type parameter.None of these array creation expressions are legal: 
new List<E>[], new List<String>[], new E[].
All will result in generic array creation errors at compile time.To make this more concrete, consider the following code fragment:
			// Why generic array creation is illegal - won't compile!
			List<String>[] stringLists = new List<String>[1]; // (1)
			List<Integer> intList = Arrays.asList(42); // (2)
			Object[] objects = stringLists; // (3)
			objects[0] = intList; // (4)
			String s = stringLists[0].get(0); // (5)

Let’s pretend that line 1, which creates a generic array, is legal. line 2 creates an List of Integers. line 3 stores List<String> into object array. line 4 replaces the sole element present in the object array. line 5 reads the element , which will throw a ClassCastException at runtime.

Types such as E, List<E>, and List<String> are technically known as nonreifiable types. Intuitively speaking, a non-reifiable type is one whose runtime representation contains less information than its compile-time representation. The only parameterized types that are reifiable are unbounded wildcard types such as List<?> and Map<?,?>.

When you get a generic array creation error, the best solution is often to use the collection type List<E> in preference to the array type E[]. You might sacrifice some performance or conciseness, but in exchange you get better type safety and interoperability.

						Item 26: Favor generic types
						----------------------------------
Consider the simple stack implementation from Item 6:
	// Object-based collection - a prime candidate for generics
	public class Stack {
		private Object[] elements;
		private int size = 0;
		private static final int DEFAULT_INITIAL_CAPACITY = 16;
		public Stack() {
			elements = new Object[DEFAULT_INITIAL_CAPACITY];
		}
		public void push(Object e) {
			ensureCapacity();
			elements[size++] = e;
		}
		public Object pop() {
			if (size == 0)
			throw new EmptyStackException();
			Object result = elements[--size];
			elements[size] = null; // Eliminate obsolete reference
			return result;
		}
		public boolean isEmpty() {
			return size == 0;
		}
		private void ensureCapacity() {
			if (elements.length == size)
			elements = Arrays.copyOf(elements, 2 * size + 1);
		}
	}						

The first step in generifying a class is to add one or more type parameters to its declaration. In this case there is one type parameter, representing the element type of the stack, and the conventional name for this parameter is E. 

If we simply replace every 'Object' with E in the above example, it results in compilation error.
	Stack.java:8: generic array creation elements = new E[DEFAULT_INITIAL_CAPACITY];
														^
i.e. you can’t create an array of a non-reifiable type, such as E. There are two solutions to this. 
1)In the stack constructor create an Object array and cast it to the generic array type. i.e. 
	elements = (E[])new Object[DEFAULT_INITIAL_CAPACITY];

Now in place of an error, the compiler will emit a warning. This usage is legal, but it’s not (in general) typesafe:
		Stack.java:8: warning: [unchecked] unchecked cast found : Object[], required: E[]
				elements = (E[]) new Object[DEFAULT_INITIAL_CAPACITY];
***
You know that the cast is typesafe , so suppress the warning in as narrow a scope as possible.In this case, the constructor contains only the unchecked array creation, so it’s appropriate to suppress the warning in the entire constructor. With the addition of an annotation to do this, Stack compiles cleanly and you can use it without explicit casts or fear of a ClassCastException:

	// The elements array will contain only E instances from push(E). This is sufficient to ensure type safety, but the runtime
	// type of the array won't be E[]; it will always be Object[]!
	@SuppressWarnings("unchecked")
	public Stack() {
		elements = (E[]) new Object[DEFAULT_INITIAL_CAPACITY];
	}

2)The second way to eliminate the generic array creation is declare the array elements as it is . i.e Object[] insteadof E[].
If you do this, you’ll get a different error:
	Stack.java:19: incompatible types found : Object, required: E
		E result = elements[--size];
You can change this error into a warning by casting the element retrieved from the array from Object to E:
	Stack.java:19: warning: [unchecked] unchecked cast found : Object, required: E
		E result = (E) elements[--size];		
***
Again, you can easily prove to yourself that the unchecked cast is safe, so it’s appropriate to suppress the warning.
	// push requires elements to be of type E, so cast is correct
	@SuppressWarnings("unchecked") E result = (E) elements[--size];

Note:
1)It is riskier to suppress an unchecked cast to an array type than to a scalar type, which would suggest the second solution.
2)But in a more realistic generic class than Stack, you would probably be reading from the array at many points in the code, so choosing the second solution would require many casts to E rather than a single cast to E[],which is why the first solution is used more commonly.

				Item 27: Favor generic methods
				--------------------------------------
Static utility methods are particularly good candidates for generification. All of the “algorithm” methods in Collections (such as binarySearch and sort) have been generified.	For Ex,
	// Generic method
	public static <E> Set<E> union(Set<E> s1, Set<E> s2) {
		Set<E> result = new HashSet<E>(s1);
		result.addAll(s2);
		return result;
	}
Note:The type parameter list, which declares the type parameter, goes between the method’s modifiers and its return type. In this example, the type parameter list is <E> and the return type is Set<E>.

A limitation of the union method is that the types of all three sets (both input parameters and the return value) have to be the same. You can make the method more flexible by using bounded wildcard types.

One noteworthy feature of generic methods is that you needn’t specify the value of the type parameter explicitly. i.e. unlike generic constructors. For ex(Item 1) ,
	Map<String, List<String>> anagrams = new HashMap<String, List<String>>(); 
***
To eliminate this redundancy, write a generic static factory method corresponding to each constructor that you want to use.	
For ex, 
	// Generic static factory method
	public static <K,V> HashMap<K,V> newHashMap() {
		return new HashMap<K,V>();
	}
Now, to create a map, Map<String, List<String>> anagrams = newHashMap();
Note: This automatic detection of type is called type inference.

It is permissible, though relatively rare, for a type parameter to be bounded by some expression involving that type parameter itself. This is what’s known as a recursive type bound. The most common use of recursive type bounds is in connection with the Comparable interface, which defines a type’s natural ordering:
	public interface Comparable<T> {
		int compareTo(T o);
	}
Note:
In practice, nearly all types can be compared only to elements of their own type. So, for example, String implements Comparable<String>, Integer implements Comparable<Integer>, and so on.	

There are many methods that take a list of elements that implement Comparable, in order to sort the list, search within it, calculate its minimum or maximum etc. For ex,

	// Returns the maximum value in a list - uses recursive type bound
	public static <T extends Comparable<T>> T max(List<T> list) {
		Iterator<T> i = list.iterator();
		T result = i.next();
		while (i.hasNext()) {
			T t = i.next();
			if (t.compareTo(result) > 0)
				result = t;
		}
		return result;
	}

							Item 28: Use bounded wildcards to increase API flexibility
							---------------------------------------------------------------------
As mentioned in Item 25 parameterized types are invariant.i.e. for any two distinct types Type1, Type2 List<Type1> is neither subtype or supertype of List<Type2>.Sometimes you need more flexibility than invariant typing can provide.For ex, we want to add a method that takes a sequence of elements and pushes them all onto the stack. i.e.
	// pushAll method without wildcard type - deficient!
	public void pushAll(Iterable<E> src) {
		for (E e : src)
		push(e);
	}
This method compiles cleanly, but it is not complete entirely. For ex, we want to push some integers to a stack of numbers, logically it should work because Integer is a subtype of Number
	Stack<Number> numberStack = new Stack<Number>();
	Iterable<Integer> integers = ... ;
	numberStack.pushAll(integers);
	
This will produce a compilation error saying pushAll(Iterable<Number>) cannot be applied to pushAll(Iterable<Integer>). The solution for this is Bounded wild card types. i.e. The type of the input parameter to pushAll should not be “Iterable of E” but “Iterable of some subtype of E”.Now the pushAll() method looks like this.
	// Wildcard type for parameter that serves as an E producer
	public void pushAll(Iterable<? extends E> src) {
		for (E e : src)
		push(e);
	}
Note: The above method produces (or pushes) new elements on to the stack.
Now suppose you want to write a popAll method to go with pushAll. For ex,
	// popAll method without wildcard type - deficient!
	public void popAll(Collection<E> dst) {
		while (!isEmpty())
		dst.add(pop());
	}
This method is also not complete like the first pushAll() method. For ex, you want to pop all the elements from stack and add it to a collection of objects.
	Stack<Number> numberStack = new Stack<Number>();
	Collection<Object> objects = ... ;
	numberStack.popAll(objects);
**
Once again you’ll get an error very similar to the one that we got with our first version of pushAll:Collection<Object> is not a subtype of Collection<Number>. Once again, wildcard types provide a way out. The type of the input parameter to popAll should not be “collection of E” but “collection of some supertype of E”. Let's modify popAll().
	// Wildcard type for parameter that serves as an E consumer
	public void popAll(Collection<? super E> dst) {
		while (!isEmpty())
		dst.add(pop());
	}
Note: The above method consumes from stack and populates into the collection.	
*****
Note:
For maximum flexibility, use wildcard types on input parameters that represent producers or consumers.If an input parameter is both a producer and a consumer, then wildcard types will do you no good:Here is a mnemonic to help you remember which wildcard type to use: "PECS stands for producer-extends, consumer-super".

Let us take the set union example defined before. The new signature of the method is 
public static <E> Set<E> union(Set<? extends E> s1, Set<? extends E> s2). Both s1 and s2 are producers, they won't consume anyting. 

Note:Do not use wildcard types as return types. Rather than providing additional flexibility for your users, it would force them to use wildcard types in client code.If the user of a class has to think about wildcard types, there is probably something wrong with the class’s API.

Unfortunately, the type inference rules are quite complex. Looking at the revised declaration for union, you might think that you could do this:
		Set<Integer> integers = ... ;
		Set<Double> doubles = ... ;
		Set<Number> numbers = union(integers, doubles);
Because both integers and doubles are subtypes of Number the above code should work. But, it will produce a compilation error saying both integers and doubles are not comparable. But, there is a way to explicitly tell the compiler to use Number's comparable. i.e.
	Set<Number> numbers = Union.<Number>union(integers, doubles);
*****
Next let’s turn our attention to the max method from Item 27. Here is the original declaration:
	public static <T extends Comparable<T>> T max(List<T> list)
Here is a revised declaration that uses wildcard types:
	public static <T extends Comparable<? super T>> T max(List<? extends T> list)	

Note:Comparables are always consumers(they consume T instances and return an integer), so you should always use Comparable<? super T> in preference to Comparable<T>. The same is true of comparators, so you should always use Comparator<? super T> in preference to Comparator<T>.
****
There is a duality between type parameters and wildcards, and many methods can be declared using one way or the other.For ex, here are two possible declarations for a static method to swap two indexed items in a list.
	public static <E> void swap(List<E> list, int i, int j);
	public static void swap(List<?> list, int i, int j);
***
In a public API, the second is better because it’s simpler.There is no type parameter to worry about. 
***
Note:As a rule, if a type parameter appears only once in a method declaration, replace it with a wildcard.

There’s one problem with the second declaration for swap, which uses a wildcard in preference to a type parameter: the straightforward implementation won’t compile:
	public static void swap(List<?> list, int i, int j) {
		list.set(i, list.set(j, list.get(i)));
	}
****	
The above code won't compile because with List<?> you can’t put any value except null into a List<?>.Fortunately, there is a way to implement this method without resorting to an unsafe cast or a raw type. The idea is to write a private helper method to capture the wildcard type.
	public static void swap(List<?> list, int i, int j) {
		swapHelper(list, i, j);
	}
	// Private helper method for wildcard capture
	private static <E> void swapHelper(List<E> list, int i, int j) {
		list.set(i, list.set(j, list.get(i)));
	}

								Item 29: Consider typesafe heterogeneous containers
								----------------------------------------------------------------
The most common use of generics is for collections, such as Set and Map, and single-element containers, such as ThreadLocal and AtomicReference.In all of these uses, it is the container that is parameterized. This limits you to a fixed number of type parameters per container.Sometimes, however, you need more flexibility.For example, a database row can have arbitrarily many columns, and it would be nice to be able to access all of them in a typesafe manner.

For example, a database row can have arbitrarily many columns, and it would be nice to be able to access all of them in a typesafe manner.The idea is to parameterize the key instead of the container. Here is an example:

		// Typesafe heterogeneous container pattern - API
		public class Favorites {
		public <T> void putFavorite(Class<T> type, T instance);
		public <T> T getFavorite(Class<T> type);
		}

Here is the sample code that uses Favorites:

	// Typesafe heterogeneous container pattern - client
	public static void main(String[] args) {
		Favorites f = new Favorites();
		f.putFavorite(String.class, "Java");
		f.putFavorite(Integer.class, 0xcafebabe);
		f.putFavorite(Class.class, Favorites.class);
		String favoriteString = f.getFavorite(String.class);
		int favoriteInteger = f.getFavorite(Integer.class);
		Class<?> favoriteClass = f.getFavorite(Class.class);
		System.out.printf("%s %x %s%n", favoriteString,favoriteInteger, favoriteClass.getName());
	}		
*****
A Favorites instance is typesafe: it will never return an Integer when you ask it for a String. It is also heterogeneous: unlike an ordinary map, all the keys are of different types. Therefore, we call Favorites a typesafe heterogeneous container.
The implementation of favorites is:
		// Typesafe heterogeneous container pattern - implementation
		public class Favorites {
			private Map<Class<?>, Object> favorites = new HashMap<Class<?>, Object>();
			public <T> void putFavorite(Class<T> type, T instance) {
				if (type == null)
					throw new NullPointerException("Type is null");
				favorites.put(type, instance);
			}
			public <T> T getFavorite(Class<T> type) {
				return type.cast(favorites.get(type));
			}
		}

Note:
Each Favorites instance is backed by a private Map<Class<?>, Object> called favorites. You might think that you couldn’t put anything into this Map because of the unbounded wildcard type, but the truth is quite the opposite. Because, Map key is not unbounded, the key i.e. class type is unbounded. This means that every key is different parameterized type. i.e. Class<String>, Class<Integer> etc. "That’s where the heterogeneity comes from".

There are two limitations to the Favorites class that are worth noting. 
1)First, a malicious client could easily corrupt the type safety of a Favorites instance, simply by using a Class object in its raw form. For ex, sometimes we use List instead of List<String> or List<Integer> etc.The way to ensure that Favorites never violates its type invariant is to have the putFavorite method check that instance is indeed an instance of the type represented by type. 
	// Achieving runtime type safety with a dynamic cast
	public <T> void putFavorite(Class<T> type, T instance) {
		favorites.put(type, type.cast(instance));
	}
***	
There are collection wrappers in java.util.Collections that play the same trick. They have methods named checkedSet, checkedList, checkedMap, and so forth. They check the type parameter before insertion , and if it fails throws a classcast exception.
2)The second limitation of the Favorites class is that it cannot be used on a non-reifiable type. In other words, you can store your favorite String or String[], but not your favorite List<String> because List<String>.class is a syntax error. Also, it's a good thing because List<Integer> and List<String> both share the same class List.class.

							Item 30: Use enums instead of int constants
							------------------------------------------------------
An enumerated type is a type whose legal values consist of a fixed set of constants, such as the seasons of the year, the planets in the solar system, or the suits in a deck of playing cards. Before enums int constants(or Strings etc..) were used, one for each memeber type. This int enum pattern has lot of short comings. They are
1)The compiler won’t complain if you pass a wrong type. For example, your method is expecting an int value which is of one type. If you pass another type (which is also int), compiler cannot catch the problem. This is because java does not provide namespaces.
2)Because int enums are compile-time constants, they are compiled into the clients that use them. If the int associated with an enum constant is changed, its clients must be recompiled. If they aren’t, they will still run, but their behavior will be undefined.
3)There is no easy way to translate int enum constants into printable strings. If you print such a constant or display it from a debugger, all you see is a number, which isn’t very helpful.
4)There is no reliable way to iterate over all the int enum constants in a group, or even to obtain the size of an int enum group.

Enums avoid all the short comings of enum pattern(either int or String).

Note:
Enums are classes that export one instance for each enumeration constant via a public static final field. Enum types are effectively final, by virtue of having no accessible constructors. Because clients can neither create instances of an enum type nor extend it, there can be no instances but the declared enum constants. In other words, enum types are instance-controlled. They are a generalization of singletons, which are essentially single-element enums.

Ex:
public enum Apple { FUJI, PIPPIN, GRANNY_SMITH }
public enum Orange { NAVEL, TEMPLE, BLOOD }

1)Enums provide type safety.
----------------------------------
If you declare a parameter to be of type Apple, you are guaranteed that any non-null object reference passed to the parameter is one of the three valid Apple values. Attempts to pass values of the wrong type will result in compile-time errors.

2)Enum constants are not compile-time constants:
------------------------------------------------------------
Enum types with identically named constants coexist peacefully because each type has its own namespace. You can add or reorder constants in an enum type without recompiling its clients, the constant values are not compiled into the clients.

3)Enum can be provided with good toString() Implementation:
-------------------------------------------------------------------------
Let us see an example of an Enum called "Operation".
	// Enum type with constant-specific class bodies and data
	public enum Operation {
		PLUS("+") {
			double apply(double x, double y) { return x + y; }
		},
		MINUS("-") {
			double apply(double x, double y) { return x - y; }
		},
		TIMES("*") {
			double apply(double x, double y) { return x * y; }
		},
		DIVIDE("/") {
			double apply(double x, double y) { return x / y; }
		};
		private final String symbol;
		Operation(String symbol) { this.symbol = symbol; }
		@Override public String toString() { return symbol; }
		abstract double apply(double x, double y);
	}
The toString implementation above makes it easy to print arithmetic expressions, as demonstrated by this little program:
	public static void main(String[] args) {
		double x = Double.parseDouble(args[0]);
		double y = Double.parseDouble(args[1]);
		for (Operation op : Operation.values())
			System.out.printf("%f %s %f = %f%n",x, op, y, op.apply(x, y));
		}

Running this program with 2 and 4 as command line arguments produces the following output:
2.000000 + 4.000000 = 6.000000
2.000000 - 4.000000 = -2.000000
2.000000 * 4.000000 = 8.000000
2.000000 / 4.000000 = 0.500000

Note:
Enum types have an automatically generated valueOf(String) method that translates a constant’s name into the constant itself.

4)Enum has values() method, which allows you iterate over the elements . see the above example.

When to Use Enums:
-------------------------
Anytime you need a fixed set of constants. This includes “natural enumerated types,” such as the planets, the days of the week, and the chess pieces. But it also includes other sets for which you know all the possible values at compile time, such as choices on a menu, operation codes, and command line flags. It is not necessary that the set of constants in an enum type stay fixed for all time.

When to use constant specific methods:
-----------------------------------------------
If you want to have multiple behaviours with a single method, prefer constant specific methods to enums that switch behaviours based on their own values.

						Item 35: Prefer annotations to naming patterns
						---------------------------------------------------------
Prior to release 1.5, it was common to use naming patterns to indicate that some program elements demanded special treatment by a tool or framework. For example, the JUnit testing framework originally required its users to designate test methods by beginning their names with the characters test. This technique works, but it has several big disadvantages. They are

1)Typographical errors may result in silent failures. For example, suppose you accidentally name a test method tsetSafetyOverride instead of testSafetyOverride. JUnit will not complain, but it will not execute the test either, leading to a false sense of security.

2)A second disadvantage of naming patterns is that there is no way to ensure that they are used only on appropriate program elements. For example, suppose you call a class TestSafetyMechanisms in hopes that JUnit will automatically test all of its methods, regardless of their names. Again, JUnit won’t complain, but it won’t execute the tests either.

3)A third disadvantage of naming patterns is that they provide no good way to associate parameter values with program elements.For example, suppose you want to support a category of test that succeeds only if it throws a particular exception. The exception type is essentially a parameter of the test. You could encode the exception type as a string. This would be ugly. The compiler would have no way of knowing to check that the string that was supposed to name an exception actually did. 

Annotations solve all the above problems.
**
Suppose you want to define an annotation type to designate simple tests that are run automatically and fail if they throw an exception. Here’s how such an annotation type, named Test, might look:

		// Marker annotation type declaration
		import java.lang.annotation.*;
		/**
		* Indicates that the annotated method is a test method.
		* Use only on parameterless static methods.
		*/
		@Retention(RetentionPolicy.RUNTIME)
		@Target(ElementType.METHOD)
		public @interface Test {
		}

The annotation Test itself is annotated with two other annotations Retention and Target(these are java in built annotations). Such annotations are called meta annotations.

a)The @Retention(RetentionPolicy.RUNTIME) meta-annotation indicates that Test annotations should be retained at runtime. Without it, Test annotations would be invisible to the test tool.

b)The @Target(ElementType.METHOD) meta-annotation indicates that the Test annotation is legal only on method declarations.

Note the comment above the Test annotation declaration that says, “Use only on parameterless static methods.” It would be nice if the compiler could enforce this restriction, but it can’t. There are limits to how much error checking the compiler can do for you even with annotations. If you put a Test annotation on the declaration of an instance method or a method with one or more parameters, the test program will still compile, leaving it to the testing tool to deal with the problem at runtime.

Here is a simple program .

	// Program containing marker annotations
	public class Sample {
		@Test public static void m1() { } // Test should pass
		public static void m2() { }
		@Test public static void m3() { // Test Should fail because of exception.
			throw new RuntimeException("Boom");
		}
		public static void m4() { }
		@Test public void m5() { } // INVALID USE: nonstatic method
		public static void m6() { }
		@Test public static void m7() { // Test should fail
			throw new RuntimeException("Crash");
		}
		public static void m8() { }
	}

More generally, annotations never change the semantics of the annotated code, but enable it for special treatment by tools such as this simple test runner:

		// Program to process marker annotations
		import java.lang.reflect.*;
		public class RunTests {
			public static void main(String[] args) throws Exception {
				int tests = 0;
				int passed = 0;
				Class testClass = Class.forName(args[0]);
				for (Method m : testClass.getDeclaredMethods()) {
					if (m.isAnnotationPresent(Test.class)) {
						tests++;
						try {
							m.invoke(null);
							passed++;
						}
						catch (InvocationTargetException wrappedExc) {
							Throwable exc = wrappedExc.getCause();
							System.out.println(m + " failed: " + exc);
						} 
						catch (Exception exc) {
							System.out.println("INVALID @Test: " + m);
						}
					}
				}
				System.out.printf("Passed: %d, Failed: %d%n",
				passed, tests - passed);
			}
		}
***
The test runner tool takes a fully qualified class name on the command line and runs all of the class’s Test-annotated methods reflectively, by calling Method.invoke. If a test method throws an exception, the  reflection facility wraps it in an InvocationTargetException.
*****
Now let’s add support for tests that succeed only if they throw a particular exception. We’ll need a new annotation type for this:

		// Annotation type with a parameter
		import java.lang.annotation.*;
		/**
		* Indicates that the annotated method is a test method that
		* must throw the designated exception to succeed.
		*/
		@Retention(RetentionPolicy.RUNTIME)
		@Target(ElementType.METHOD)
		public @interface ExceptionTest {
		Class<? extends Exception> value();
		}

The type of the parameter for this annotation is Class<? extends Exception>. It allows the user of the
annotation to specify any exception type. For ex,

	// Program containing annotations with a parameter
	public class Sample2 {
		@ExceptionTest(ArithmeticException.class)
		public static void m1() { // Test should pass
			int i = 0;
			i = i / i;
		}
	}
Now, the test runner tool to process the new annotation. But the code looks like this.
	if (m.isAnnotationPresent(ExceptionTest.class)) {
		tests++;
		try {
			m.invoke(null);
			System.out.printf("Test %s failed: no exception%n", m);
		} catch (InvocationTargetException wrappedEx) {
			Throwable exc = wrappedEx.getCause();
			Class<? extends Exception> excType = m.getAnnotation(ExceptionTest.class).value();
			if (excType.isInstance(exc)) {
				passed++;
			} 
			else {
				System.out.printf("Test %s failed: expected %s, got %s%n",m, excType.getName(), exc);
			}
		} catch (Exception exc) {
			System.out.println("INVALID @Test: " + m);
		}
	}

This code extracts the value of the annotation parameter and uses it to check if the exception thrown by the test is of the right type(using m.getAnnotation(ExceptionTest.class).value()).	

It is possible to envision a test that passes if it throws any one of several specified exceptions. Then ExceptionTest annotation to be an array of Class objects: For ex,
		// Annotation type with an array parameter
		@Retention(RetentionPolicy.RUNTIME)
		@Target(ElementType.METHOD)
		public @interface ExceptionTest {
			Class<? extends Exception>[] value();
		}

						Item 36: Consistently use the Override annotation
						------------------------------------------------------------

The compiler can protect you from a great many errors if you use the Override annotation on every method declaration that you believe to override a supertype declaration, with one exception. In concrete classes, you need not annotate methods that you believe to override abstract method declarations(though it is not harmful to do so).

						Item 37: Use marker interfaces to define types
						--------------------------------------------------------
A marker interface is an interface that contains no method declarations, but merely designates (or “marks”) a class that implements the interface as having some property. For example, consider the Serializable interface (Chapter 11). By implementing this interface, a class indicates that its instances can be written to an ObjectOutputStream (or “serialized”).

Marker interfaces have two advantages over marker annotations.
1)First and foremost, marker interfaces define a type that is implemented by instances of the marked class; marker annotations do not.The existence of this type allows you to catch errors at compile time that you couldn’t catch until runtime if you use a marker annotation.

2)Another advantage of marker interfaces over marker annotations is that they can be targeted more precisely. If an annotation type is declared with target ElementType.TYPE, it can be applied to any class or interface.

Note:
The chief advantage of marker annotations over marker interfaces is that it is possible to add more information to an annotation type after it is already in use, by adding one or more annotation type elements with defaults. What starts life as a mere marker annotation type can evolve into a richer annotation type over time.

Q:So when should you use a marker annotation and when should you use a marker interface?
If the marker applies only to classes and interfaces, ask yourself the question, Might I want to write one or more methods that accept only objects that have this marking. (For ex passing Serializable objects to write()). If so, you should use a marker interface in preference to an annotation. 

Clearly you must use an annotation if the marker applies to any program element other than a class or interface.

						Item 45: Minimize the scope of local variables
						-------------------------------------------------------

The most powerful technique for minimizing the scope of a local variable is to declare it where it is first used.If a variable is declared before it is used,the reader who is trying to figure out what the program does, by the time the variable is used, the reader might not remember the variable’s type or initial value.

If a variable is declared outside of the block in which it is used, it remains visible after the program exits that block. If a variable is used accidentally before or after its region of intended use, the consequences can be disastrous.

Note:
Nearly every local variable declaration should contain an initializer. If you don’t yet have enough information to initialize a variable sensibly, you should postpone the declaration until you do.

If a variable is initialized by a method that throws a checked exception,If the value must be used outside of the try block, then it must be declared before the try block, where it cannot yet be “sensibly initialized.”

Loops present a special opportunity to minimize the scope of variables. The for loop, in both its traditional and for-each forms, allows you to declare loop variables, limiting their scope to the exact region where they’re needed.

Note:
prefer for loops to while loops, assuming the contents of the loop variable aren’t needed after the loop terminates.

			Iterator<Element> i = c.iterator();
			while (i.hasNext()) {
			doSomething(i.next());
			}
			...
			Iterator<Element> i2 = c2.iterator();
			while (i.hasNext()) { // BUG!
			doSomethingElse(i2.next());
			}
			
The second loop contains a cut-and-paste error: it initializes a new loop variable, i2, but uses the old one, i, which is, unfortunately, still in scope. The resulting code compiles without error and runs without throwing an exception, but it does the wrong thing. Instead of iterating over c2, the second loop terminates immediately, giving the false impression that c2 is empty. Because the program errs silently, the error can remain undetected for a long time.

Note:
If a similar cut-and-paste error were made in conjunction with either of the for loops (for-each or traditional), the resulting code wouldn’t even compile.

				Item 46: Prefer for-each loops to traditional for loops
				---------------------------------------------------------------

The for-each loop, introduced in release 1.5, gets rid of the clutter and the opportunity for error by hiding the iterator or index variable completely.

The advantages of the for-each loop over the traditional for loop are even greater when it comes to nested iteration over multiple collections. Here is a common mistake that people make when they try to do nested iteration over two collections:

			enum Suit { CLUB, DIAMOND, HEART, SPADE }
			enum Rank { ACE, DEUCE, THREE, FOUR, FIVE, SIX, SEVEN, EIGHT,
			NINE, TEN, JACK, QUEEN, KING }
			...
			Collection<Suit> suits = Arrays.asList(Suit.values());
			Collection<Rank> ranks = Arrays.asList(Rank.values());
			List<Card> deck = new ArrayList<Card>();
			for (Iterator<Suit> i = suits.iterator(); i.hasNext(); )
			for (Iterator<Rank> j = ranks.iterator(); j.hasNext(); )
			deck.add(new Card(i.next(), j.next()));

The problem is that the next method is called too many times on the iterator for the outer collection (suits). It should be called from the outer loop, so that it is called once per suit, but instead it is called from the inner loop, so it is called once per card. After you run out of suits, the loop throws a NoSuchElementException.

If instead you use a nested for-each loop, the problem simply disappears. The resulting code is as succinct as you could wish for:
		// Preferred idiom for nested iteration on collections and arrays
		for (Suit suit : suits)
			for (Rank rank : ranks)
				deck.add(new Card(suit, rank));

Not only does the for-each loop let you iterate over collections and arrays, it lets you iterate over any object that implements the Iterable interface. This simple interface, which consists of a single method, was added to the platform at the same time as the for-each loop. Here is how it looks:
			public interface Iterable<E> {
			// Returns an iterator over the elements in this iterable
			Iterator<E> iterator();
			}

Unfortunately, there are three common situations where you can’t use a for-each loop:

1. Filtering—If you need to traverse a collection and remove selected elements, then you need to use an explicit iterator so that you can call its remove method.

2. Transforming—If you need to traverse a list or array and replace some or all of the values of its elements, then you need the list iterator or array index in order to set the value of an element.

3. Parallel iteration—If you need to traverse multiple collections in parallel, then you need explicit control over the iterator or index variable, so that all iterators or index variables can be advanced in lockstep (as demonstrated unintentionally in the buggy card and dice examples above).

				Item 49: Prefer primitive types to boxed primitives
				------------------------------------------------------------

There are three major differences between primitives and boxed primitives. First, primitives have only their values, whereas boxed primitives have identities distinct from their values. In other words, two boxed primitive instances can have the same value and different identities. Second, primitive types have only fully functional values, whereas each boxed primitive type has one nonfunctional value, which is "null". Last, primitives are generally more time- and space-efficient than boxed primitives. All three of these differences can get you into real trouble if you aren’t careful.

Consider the following comparator, which is designed to represent ascending numerical order on Integer values.You would not need to write this comparator in practice, as it implements the natural ordering on Integer, which you get without a comparator, but it makes for an interesting example:

			// Broken comparator - can you spot the flaw?
			Comparator<Integer> naturalOrder = new Comparator<Integer>() {
			public int compare(Integer first, Integer second) {
			return first < second ? -1 : (first == second ? 0 : 1);
			}
			};

This comparator is deeply flawed.The value of naturalOrder.compare(new Integer(42), new Integer(42)) should be 0 but it prints 1. Evaluatingthe expression first < second causes first and second to be auto-unboxed. In this case it fails. So, it takes second expression first == second . At this point first and second are boxed int's not primitives. So, it does an identity comparision, which will fail because both first and second are referred to different memory locations. so, the comparator will incorrectly return 1.

Next, consider this little program:
		public class Unbelievable {
			static Integer i;
			public static void main(String[] args) {
			if (i == 42)
			System.out.println("Unbelievable");
			}
		}

It throws a NullPointerException when evaluating the expression (i == 42). The problem is that i is an Integer, not an int, and like all object reference fields, its initial value is null. When the program evaluates the expression (i == 42), it is comparing an Integer to an int. In nearly every case when you mix primitives and boxed primitives in a single operation, the boxed primitive is auto-unboxed, and this case is no exception. If a null object reference is auto-unboxed, you get a NullPointerException.

Q:So when should you use boxed primitives?
Ans:They have several legitimate uses.
1)The first is as elements, keys, and values in collections. You can’t put primitives in collections, so you’re forced to use boxed primitives.

2)You must use boxed primitives as type parameters in parameterized types (Chapter 5), because the language does not permit you to use primitives. For example, you cannot declare a variable to be of type ThreadLocal<int>, so you must use ThreadLocal<Integer> instead.

3)Finally, you must use boxed primitives when making reflective method invocations.

					Item 50: Avoid strings where other types are more appropriate
					--------------------------------------------------------------------------

This item discusses a few things you should not do with strings.

1)Strings are poor substitutes for other value types. When a piece of data comes into a program from a file, from the network, or from keyboard input, it is often in string form. There is a natural tendency to leave it that way, but this tendency is justified only if the data really is textual in nature. If it’s numeric, it should be translated into the appropriate numeric type, such as int, float, or BigInteger.

2)Strings are poor substitutes for enum types. Enums make far better enumerated type constants than strings.

3)Strings are poor substitutes for aggregate types. If an entity has multiple components, it is usually a bad idea to represent it as a single string. For example, here’s a line of code that comes from a real system—identifier names have been changed to protect the guilty:
		String compoundKey = className + "#" + i.next();

Note:
This approach has many disadvantages. If the character used to separate fields occurs in one of the fields, chaos may result. To access individual fields, you have to parse the string, which is slow, tedious, and error-prone. You can’t provide equals, toString, or compareTo methods but are forced to accept the behavior that String provides. A better approach is simply to write a class to represent the aggregate, often a private static member class.

					Item 52: Refer to objects by their interfaces
					-----------------------------------------------------

If appropriate interface types exist, then parameters, return values, variables, and fields should all be declared using interface types.

If you get into the habit of using interfaces as types, your program will be much more flexible. If you decide that you want to switch implementations, all you have to do is change the class name in the constructor.
		List<Subscriber> subscribers = new Vector<Subscriber>();
If you want to change the implementation to ArrayList, 
		List<Subscriber> subscribers = new ArrayList<Subscriber>();

Note:
It is entirely appropriate to refer to an object by a class rather than an interface if no appropriate interface exists.For example, consider value classes, such as String and BigInteger. Value classes are rarely written with multiple implementations in mind. They are often final and rarely have corresponding
interfaces.

Classes that implement an interface but provide extra methods not found in the interface for example, LinkedHashMap. Such a class should be used to refer to its instances only if the program relies on the extra methods.

					Item 53: Prefer interfaces to reflection
					----------------------------------------------

The core reflection facility, java.lang.reflect, offers programmatic access to information about loaded classes. Given a Class object, you can obtain Constructor, Method, and Field instances representing the constructors, methods, and fields of the class represented by the Class instance. These objects provide programmatic access to the class’s member names, field types, method signatures, and so on.

Moreover, Constructor, Method, and Field instances let you manipulate their underlying counterparts reflectively: you can construct instances, invoke methods, and access fields of the underlying class by invoking methods on the Constructor, Method, and Field instances.For example, Method.invoke lets you invoke any method on any object of any class (subject to the usual security constraints). Reflection allows one class to use another, even if the latter class did not exist when the former was compiled. This power, however, comes at a price:

• You lose all the benefits of compile-time type checking, including exception checking. If a program attempts to invoke a nonexistent or inaccessible method reflectively, it will fail at runtime unless you’ve taken special precautions.

• The code required to perform reflective access is clumsy and verbose. It is tedious to write and difficult to read.

• Performance suffers. Reflective method invocation is much slower than normal method invocation. Exactly how much slower is hard to say, because there are so many factors at work. On my machine, the speed difference can be as small as a factor of two or as large as a factor of fifty.

As a rule, objects should not be accessed reflectively in normal applications at runtime.

					Item 54: Use native methods judiciously
					------------------------------------------------
The Java Native Interface (JNI) allows Java applications to call native methods, which are special methods written in native programming languages such as C or C++. Native methods can perform arbitrary computation in native languages before returning to the Java programming language.

Historically, native methods have had three main uses.

1)provides access to platform-specific facilities such as registries and file locks.
2)provides access to libraries of legacy code, which could in turn provide access to legacy data.
3)used to write performance-critical parts of applications in native languages for improved performance.

It is legitimate to use native methods to access platform-specific facilities, but as the Java platform matures, it provides more and more features previously found only in host platforms. For example, java.util.prefs, added in release 1.4, offers the functionality of a registry, and java.awt.SystemTray, added in release 1.6, offers access to the desktop system tray area.

Note:
It is rarely advisable to use native methods for improved performance. Because JVM implementaions and other API implementations got much faster now.

The use of native methods has serious disadvantages.
1)Because native languages are not safe (Item 39), applications using native methods are no longer immune to memory corruption errors.
2)Because native languages are platform dependent, applications using native methods are far less portable.
3)Applications using native code are far more difficult to debug.
4)There is a fixed cost associated with going into and out of native code, so native methods can decrease performance if they do only a small amount of work.
5)Finally, native methods require “glue code” that is difficult to read and tedious to write.

						Item 57: Use exceptions only for exceptional conditions
						------------------------------------------------------------------
			// Horrible abuse of exceptions. Don't ever do this!
			try {
				int i = 0;
				while(true)
				range[i++].climb();
			}
			catch(ArrayIndexOutOfBoundsException e) {
			}
It’s a misguided attempt to improve performance based on the faulty reasoning that, since the VM checks the bounds of all array accesses, the normal loop termination test—hidden by the compiler should be avoided. There are three things wrong with this reasoning:

• Because exceptions are designed for exceptional circumstances, there is little incentive for JVM implementors to make them as fast as explicit tests.
• Placing code inside a try-catch block inhibits certain optimizations that modern JVM implementations might otherwise perform.
• The standard idiom for looping through an array doesn’t necessarily result in redundant checks. Modern JVM implementations optimize them away.

Note:
Not only does the exception-based loop obfuscate the purpose of the code and reduce its performance, but it’s not guaranteed to work! In the presence of an unrelated bug, the loop can fail silently and mask the bug, greatly complicating the debugging process.

Exceptions are, as their name implies, to be used only for exceptional conditions; they should never be used for ordinary control flow.Even if the performance advantage is real, it may not remain in the face of steadily improving platform implementations. The subtle bugs and maintenance headaches that come from overly clever techniques, however, are sure to remain.
***
A well-designed API must not force its clients to use exceptions for ordinary control flow. A class with a “state-dependent” method that can be invoked only under certain unpredictable conditions should generally have a separate “state-testing” method indicating whether it is appropriate to invoke the state-dependent method. For example, the Iterator interface has the state-dependent method next and the corresponding state-testing method hasNext.

This enables the standard idiom for iterating over a collection with a traditional for loop 

				for (Iterator<Foo> i = collection.iterator(); i.hasNext(); ) {
				Foo foo = i.next();
				...
				}
If Iterator lacked the hasNext method, clients would be forced to do this instead:
				// Do not use this hideous code for iteration over a collection!
				try {
				Iterator<Foo> i = collection.iterator();
				while(true) {
				Foo foo = i.next();
				...
				}
				} catch (NoSuchElementException e) {
				}

An alternative to providing a separate state-testing method is to have the statedependent method return a distinguished value such as null if it is invoked with the object in an inappropriate state. This technique would not be appropriate for Iterator, as null is a legitimate return value for the next method.

*****
If an object is to be accessed concurrently without external synchronization or is subject to externally induced state transitions, you must use a distinguished return value, as the object’s state could change in the interval between the invocation of a state-testing method and its state-dependent method. Performance concerns may dictate that a distinguished return value be used if a separate state-testing method would duplicate the work of the statedependent method. All other things being equal, a state-testing method is mildly preferable to a distinguished return value. It offers slightly better readability, and incorrect use may be easier to detect: if you forget to call a state-testing method, the state-dependent method will throw an exception, making the bug obvious; if you forget to check for a distinguished return value, the bug may be subtle.

	Item 58: Use checked exceptions for recoverable conditions and runtime exceptions for programming errors.
	-------------------------------------------------------------------------------------------------------------------------------

use checked exceptions for conditions from which the caller can reasonably be expected to recover. By confronting the API user with a checked exception, the API designer presents a mandate to recover from the condition.

There are two kinds of unchecked throwables: runtime exceptions and errors.If a program throws an unchecked exception or an error, it is generally the case that recovery is impossible and continued execution would do more harm than good. If a program does not catch such a throwable, it will cause the current thread to halt with an appropriate error message.
***
Use runtime exceptions to indicate programming errors. The great majority of runtime exceptions indicate precondition violations. A precondition violation is simply a failure by the client of an API to adhere to the contract established by the API specification. For example, the contract for array access specifies that the array index must be between zero and the array length minus one. ArrayIndexOutOfBoundsException indicates that this precondition was violated
***
While the Java Language Specification does not require it, there is a strong convention that errors are reserved for use by the JVM to indicate resource deficiencies, invariant failures, or other conditions that make it impossible to continue execution. Given the almost universal acceptance of this convention, it’s best not to implement any new Error subclasses. Therefore, all of the unchecked throwables you implement should subclass RuntimeException.
*****
To summarize, use checked exceptions for recoverable conditions and runtime exceptions for programming errors. Of course, the situation is not always black and white. For example, consider the case of resource exhaustion, which can be caused by a programming error such as allocating an unreasonably large array or by a genuine shortage of resources. If resource exhaustion is caused by a temporary shortage or by temporarily heightened demand, the condition may well be recoverable. It is a matter of judgment on the part of the API designer whether a given instance of resource exhaustion is likely to allow for recovery. If you believe a condition is likely to allow for recovery, use a checked exception; if not, use a runtime exception.
***
Exceptions are full-fledged objects on which arbitrary methods can be defined. The primary use of such methods is to provide the code that catches the exception with additional information concerning the condition that caused the exception to be thrown. In the absence of such methods, programmers have been known to parse the string representation of an exception to ferret out additional information. This is extremely bad practice. Classes seldom specify the details of their string representations, so string representations can differ from implementation to implementation and release to release. Therefore, code that parses the string representation of an exception is likely to be nonportable and fragile.

Because checked exceptions generally indicate recoverable conditions, it’s especially important for such exceptions to provide methods that furnish information that could help the caller to recover. For example, suppose a checked exception is thrown when an attempt to make a purchase with a gift card fails because the card doesn’t have enough money left on it. The exception should provide an accessor method to query the amount of the shortfall, so the amount can be relayed to the shopper.

					Item 59: Avoid unnecessary use of checked exceptions
					-----------------------------------------------------------------
As a litmus test, ask yourself how the programmer will handle the exception. Is this the best that can be done?	

			} catch(TheCheckedException e) {
			throw new AssertionError(); // Can't happen!
			}
How about this?
			} catch(TheCheckedException e) {
			e.printStackTrace(); // Oh well, we lose.
			System.exit(1);
			}

If the programmer using the API can do no better, an unchecked exception would be more appropriate. One example of an exception that fails this test is CloneNotSupportedException. It is thrown by Object.clone, which should be invoked only on objects that implement Cloneable. In practice, the catch block almost always has the character of an assertion failure. The checked nature of the exception provides no benefit to the programmer, but it requires effort and complicates programs.

One technique for turning a checked exception into an unchecked exception is to break the method that throws the exception into two methods, the first of which returns a boolean that indicates whether the exception would be thrown.

// Invocation with state-testing method and unchecked exception
			if (obj.actionPermitted(args)) {
			obj.action(args);
			} else {
			// Handle exceptional condition
			...
			}

This refactoring is not always appropriate, but where it is appropriate, it can make an API more pleasant to use.

					Item 61: Throw exceptions appropriate to the abstraction
					--------------------------------------------------------------------
Higher layers should catch lower-level exceptions and, in their place, throw exceptions that can be explained in terms of the higher-level abstraction. This idiom is known as exception translation:
			// Exception Translation
			try {
			// Use lower-level abstraction to do our bidding
			...
			} catch(LowerLevelException e) {
			throw new HigherLevelException(...);
			}

A special form of exception translation called exception chaining is appropriate in cases where the lower-level exception might be helpful to someone debugging the problem that caused the higher-level exception.	 The lower-level exception (the cause) is passed to the higher-level exception, which provides an accessor method (Throwable.getCause) to retrieve the lower-level exception:
	// Exception Chaining
	try {
	... // Use lower-level abstraction to do our bidding
	} catch (LowerLevelException cause) {
	throw new HigherLevelException(cause);
	}

Most standard exceptions have chaining-aware constructors. For exceptions that don’t, you can set the cause using Throwable’s initCause method.

				Item 64: Strive for failure atomicity
				------------------------------------------

A failed method invocation should leave the object in the state that it was in prior to the invocation. A method with this property is said to be failure atomic.

There are several ways to achieve this effect.

1)The simplest is to design immutable objects. If an object is immutable, failure atomicity is free. If an operation fails, it may prevent a new object from getting created, but it will never leave an existing object in an inconsistent state, because the state of each object is consistent when it is created and can’t be modified thereafter.

2)For methods that operate on mutable objects, the most common way to achieve failure atomicity is to check parameters for validity before performing the operation. This causes any exception to get thrown before object modification commences. For example,

	public Object pop() {
	if (size == 0)
		throw new EmptyStackException();
		Object result = elements[--size];
		elements[size] = null; // Eliminate obsolete reference
		return result;
	}
If the initial size check were eliminated, the method would still throw an exception when it attempted to pop an element from an empty stack. It would, however, leave the size field in an inconsistent (negative number because of --size) state, causing any future method invocations on the object to fail. Additionally, the exception thrown by the pop method would be inappropriate to the abstraction.

3)perform the operation on a temporary copy of the object and to replace the contents of the object with the temporary copy once the operation is complete. This approach occurs naturally when the computation can be performed more quickly once the data has been stored in a temporary data structure. For example, Collections.sort dumps its input list into an array prior to sorting to reduce the cost of accessing elements in the inner loop of the sort. This is done for performance, but as an added benefit, it ensures that the input list will be untouched if the sort fails.

							Item 65: Don’t ignore exceptions
							---------------------------------------

It is easy to ignore exceptions by surrounding a method invocation with a try statement with an empty catch block:
			// Empty catch block ignores exception - Highly suspect!
			try {
			...
			} catch (SomeException e) {
			}

An empty catch block defeats the purpose of exceptions, which is to force you to handle exceptional conditions. Ignoring an exception is analogous to ignoring a fire alarm—and turning it off so no one else gets a chance to see if there’s a real fire. At the very least, the catch block should contain a comment explaining why it is appropriate to ignore the exception.

An example of the sort of situation where it might be appropriate to ignore an exception is when closing a FileInputStream. You haven’t changed the state of the file, so there’s no need to perform any recovery action, and you’ve already read the information that you need from the file, so there’s no reason to abort the operation in progress. Even in this case, it is wise to log the exception, so that you can investigate the matter if these exceptions happen often.

											Concurrency
											-------------
											
								Item 66: Synchronize access to shared mutable data
								--------------------------------------------------------------

The synchronized keyword ensures that only a single thread can execute a method or block at one time. Synchronization is for mutual exclusion, to prevent an object from being observed in an inconsistent state while it’s being modified by another thread. 

Note: The above view is correct, but it’s only half the story. Without synchronization, one thread’s changes might not be visible to other threads. It ensures that each thread entering a synchronized method or block sees the effects of all previous modifications that were guarded by the same lock.

The language specification guarantees that reading or writing a variable is atomic unless the variable is of type long or double, even if multiple threads modify the variable concurrently and without synchronization. This advice is dangerously wrong. While the language specification guarantees that a thread will not see an arbitrary value when reading a field, it does not guarantee that a value written by one thread will be visible to another. Synchronization is required for reliable communication between threads as well as for mutual exclusion. This is due to a part of the language specification known as the memory model, which specifies when and how changes made by one thread become visible to others.

The consequences of failing to synchronize access to shared mutable data can be dire even if the data is atomically readable and writable. Consider the task of stopping one thread from another. The libraries provide the Thread.stop method, but this method was deprecated long ago because it is inherently unsafe—its use can result in data corruption. Do not use Thread.stop. A recommended way to stop one thread from another is to have the first thread poll a boolean field that is initially false but can be set to true by the second thread to indicate that the first thread is to stop itself. Because reading and writing a boolean field is atomic, some programmers dispense with synchronization when accessing the field:

		public class StopThread {
			private static boolean stopRequested;
			public static void main(String[] args) throws InterruptedException {
				Thread backgroundThread = new Thread(new Runnable() {
				public void run() {
					int i = 0;
					while (!stopRequested)
					i++;
				}
				});
				backgroundThread.start();
				TimeUnit.SECONDS.sleep(1);
				stopRequested = true;
			}
		}
		
You might expect this program to run for about a second, after which the main thread sets stopRequested to true, causing the background thread’s loop to terminate. Infact, the background thread loops forever!

In the absence of synchronization, the background thread will not see the change in the value of stopRequested that was made by the main thread.

Note: In the absence of synchronization, it’s quite acceptable for the virtual machine to transform this code:
	while (!done)
		i++;
	
	into this code:
	
	if (!done)
		while (true)
			i++;

This optimization is known as hoisting, and it is precisely what the HotSpot server VM does. One way to fix the problem is to synchronize access to the stopRequested field.This program terminates in about one second, as expected:

		public class StopThread {
			private static boolean stopRequested;
			private static synchronized void requestStop() {
				stopRequested = true;
			}
			private static synchronized boolean stopRequested() {
				return stopRequested;
			}
			public static void main(String[] args) throws InterruptedException {
				Thread backgroundThread = new Thread(new Runnable() {
					public void run() {
						int i = 0;
						while (!stopRequested())
						i++;
					}
					});
				backgroundThread.start();
				TimeUnit.SECONDS.sleep(1);
				requestStop();
			}
		}

Note: synchronization has no effect unless both read and write operations are synchronized.

The actions of the synchronized methods in StopThread would be atomic even without synchronization. In other words, the synchronization on these methods is used solely for its communication effects, not for mutual exclusion.

There is a correct alternative that is less verbose and whose performance is likely to be better. If stopRequested is declared volatile it would solve the problem. It guarantees that any thread that reads the field will see the most recently written value. You do have to be careful when using volatile. For ex,
	private static volatile int nextSerialNumber = 0;
	public static int generateSerialNumber() {
		return nextSerialNumber++;
	}

The problem is that the increment operator (++) is not atomic. It performs two operations on the nextSerialNumber field: first it reads the value, then it writes back a new value, equal to the old value plus one. If a second thread reads the field between the time a thread reads the old value and writes back a new one, the second thread will see the same value as the first and return the same serial number. This is a safety failure: the program computes the wrong results.

									Item 67: Avoid excessive synchronization
									-------------------------------------------------
Inside a synchronized region, do not invoke a method that is designed to be overridden, or one provided by a client in the form of a function object. The class has no knowledge of what the method does and has no control over it. Depending on what an alien method does, calling it from a synchronized region can cause exceptions, deadlocks, or data corruption.

For ex, consider the following class, which implements an observable set wrapper. It allows clients to subscribe to notifications when elements are added to the set. This is the Observer pattern. 

		public class ObservableSet<E> extends ForwardingSet<E> {
			public ObservableSet(Set<E> set) { 
				super(set);
			}
			private final List<SetObserver<E>> observers = new ArrayList<SetObserver<E>>();
			public void addObserver(SetObserver<E> observer) {
				synchronized(observers) {
					observers.add(observer);
				}
			}
			public boolean removeObserver(SetObserver<E> observer) {
				synchronized(observers) {
					return observers.remove(observer);
				}
			}
			private void notifyElementAdded(E element) {
				synchronized(observers) {
					for (SetObserver<E> observer : observers)
						observer.added(this, element);
				}
			}
			@Override public boolean add(E element) {
				boolean added = super.add(element);
				if (added)
				notifyElementAdded(element);
				return added;
			}
			@Override public boolean addAll(Collection<? extends E> c) {
				boolean result = false;
				for (E element : c)
					result |= add(element); // calls notifyElementAdded
				return result;
			}
		}

Observers subscribe to notifications by invoking the addObserver method and unsubscribe by invoking the removeObserver method. In both cases, an instance of this callback interface is passed to the method:

	public interface SetObserver<E> {
		// Invoked when an element is added to the observable set
		void added(ObservableSet<E> set, E element);
	}

On cursory inspection, ObservableSet appears to work. For example, the following program prints the numbers from 0 through 99:

		public static void main(String[] args) {
			ObservableSet<Integer> set = new ObservableSet<Integer>(new HashSet<Integer>());
			set.addObserver(new SetObserver<Integer>() {
				public void added(ObservableSet<Integer> s, Integer e) {
					System.out.println(e);
				}
			});
			for (int i = 0; i < 100; i++)
			set.add(i);
		}

Suppose we remove the element from observers list if the integer value is 23. i.e.

			set.addObserver(new SetObserver<Integer>() {
				public void added(ObservableSet<Integer> s, Integer e) {
					System.out.println(e);
					if (e == 23) {
						s.removeObserver(this);
					}
				}
			});
	
You might expect the program to print the numbers 0 through 23, after which the observer would unsubscribe and the program complete its work silently.What actually happens is that it prints the numbers 0 through 23, and then throws a  ConcurrentModificationException. The problem is that notifyElementAdded is in the process of iterating over the observers list when it invokes the observer’s added method. The added method calls the observable set’s removeObserver method, which in turn calls observers.remove. i.e. We are trying to remove an element from a list in the midst of iterating over it, which is illegal. The iteration in the notifyElementAdded method is in a synchronized block to prevent concurrent modification, but it doesn’t prevent the iterating thread itself from calling back into the observable set and modifying its observers list.

Let’s write an observer that attempts to unsubscribe, but instead of calling removeObserver directly, it engages the services of
another thread to do the deed. This observer uses an executor service.

		set.addObserver(new SetObserver<Integer>() {
				public void added(final ObservableSet<Integer> s, Integer e) {
					System.out.println(e);
					if (e == 23) {
						ExecutorService executor =
						Executors.newSingleThreadExecutor();
						final SetObserver<Integer> observer = this;
						try {
							executor.submit(new Runnable() {
								public void run() {
									s.removeObserver(observer);
								}
							}).get();
						} catch (ExecutionException ex) {
							throw new AssertionError(ex.getCause());
						} catch (InterruptedException ex) {
							throw new AssertionError(ex.getCause());
						} finally {
							executor.shutdown();
						}
					}
				}
		});

This time we don’t get an exception; we get a deadlock. The background thread calls s.removeObserver, which attempts to lock observers, but it can’t acquire the lock, because the main thread already has the lock. All the while, the main thread is waiting for the background thread to finish removing the observer, which explains the deadlock.

Fix this sort of problem by moving alien method invocations out of synchronized blocks. For the notifyElementAdded method, this involves taking a “snapshot” of the observers list that can then be safely traversed without a lock. With this change, both of the previous examples run without exception or deadlock:

			private void notifyElementAdded(E element) {
				List<SetObserver<E>> snapshot = null;
				synchronized(observers) {
					snapshot = new ArrayList<SetObserver<E>>(observers);
				}
				for (SetObserver<E> observer : snapshot)
					observer.added(this, element);
			}

In fact, there’s a better way to move the alien method invocations out of the synchronized block. Since release 1.5, the Java libraries have provided a concurrent collection (Item 69) known as CopyOnWriteArrayList, which is tailor-made for this purpose. It is a variant of ArrayList in which all write operations are implemented by making a fresh copy of the entire underlying array. Because the internal array is never modified, iteration requires no locking and is very fast. For most uses, the performance of CopyOnWriteArrayList would be atrocious, but it’s perfect for observer lists, which are rarely modified and often traversed.

Here is how the class looks.

	// Thread-safe observable set with CopyOnWriteArrayList
		private final List<SetObserver<E>> observers = 	new CopyOnWriteArrayList<SetObserver<E>>();
		public void addObserver(SetObserver<E> observer) {
			observers.add(observer);
		}
		public boolean removeObserver(SetObserver<E> observer) {
			return observers.remove(observer);
		}
		private void notifyElementAdded(E element) {
			for (SetObserver<E> observer : observers)
				observer.added(this, element);
		}

An alien method invoked outside of a synchronized region is known as an open call. Besides preventing failures, open calls can greatly
increase concurrency. An alien method might run for an arbitrarily long period. If the alien method were invoked from a synchronized region, other threads would be denied access to the protected resource unnecessarily.

As a rule, you should do as little work as possible inside synchronized regions. Obtain the lock, examine the shared data, transform it as necessary, and drop the lock. If you must perform some time-consuming activity, find a way to move the activity out of the synchronized region without violating the guidelines in Item 66.

Now let’s take a brief look at performance.
1)In a multicore world, the real cost of excessive synchronization is not the CPU time spent obtaining locks; it is the lost opportunities for parallelism and the delays imposed by the need to ensure that every core has a consistent view of memory.
2)It can limit the VM’s ability to optimize code execution.

You should make a mutable class thread-safe if it is intended for concurrent use and you can achieve significantly higher concurrency by synchronizing internally than you could by locking the entire object externally. Otherwise, don’t synchronize internally. Let the client synchronize externally where it is appropriate. In the early days of the Java platform, many classes violated these guidelines. For example, StringBuffer instances are almost always used by a single thread, yet they perform internal synchronization. It is for this reason that StringBuffer was essentially replaced by StringBuilder. When in doubt, do not synchronize your class, but document that it is not thread-safe.

If you do synchronize your class internally, you can use various techniques to achieve high concurrency, such as lock splitting, lock striping, and nonblocking concurrency control.

If a method modifies a static field, you must synchronize access to this field, even if the method is typically used only by a single thread. It is not possible for clients to perform external synchronization on such a method because there can be no guarantee that unrelated clients will do likewise. The generateSerialNumber method in Item 66  exemplifies this situation.

							Item 68: Prefer executors and tasks to threads
							-------------------------------------------------------
In release 1.5, java.util.concurrent was added to the Java platform. This package contains an Executor Framework, which is a flexible interface-based task execution facility. Creating a work queue requires a single line of code:
					ExecutorService executor = Executors.newSingleThreadExecutor();
Here is how to submit a runnable for execution:
					executor.execute(runnable);
And here is how to tell the executor to terminate gracefully (if you fail to do this, it is likely that your VM will not exit):
					executor.shutdown();	

You can do many more things with an executor service. For example, you can wait for a particular task to complete (as in the “background thread SetObserver” in Item 67, page 267), you can wait for any or all of a collection of tasks to complete (using the invokeAny or invokeAll methods), you can wait for the executor service’s graceful termination to complete (using the awaitTermination method), you can retrieve the results of tasks one by one as they complete (using an ExecutorCompletionService), and so on.

If you want more than one thread to process requests from the queue, simply call a different static factory that creates a different kind of executor service called a thread pool. You can create a thread pool with a fixed or variable number of threads. The java.util.concurrent.Executors class contains static factories that provide most of the executors you’ll ever need. If, however, you want some thing out of the ordinary, you can use the ThreadPoolExecutor class directly. This class lets you control nearly every aspect of a thread pool’s operation.
					
Choosing the executor service for a particular application can be tricky. If you’re writing a small program, or a lightly loaded server, using Executors.newCachedThreadPool is generally a good choice, as it demands no configuration and generally “does the right thing.” But a cached thread pool is not a good choice for a heavily loaded production server! In a cached thread pool, submitted tasks are not queued but immediately handed off to a thread for execution. If no threads are available, a new one is created. If a server is so heavily loaded that all of its CPUs are fully utilized, and more tasks arrive, more threads will be created, which will only make matters worse. Therefore, in a heavily loaded production server, you are much better off using Executors.newFixedThreadPool, which gives you a pool with a fixed number of threads, or using the ThreadPoolExecutor class directly, for maximum control.

The key abstraction is no longer Thread, which served as both the unit of work and the mechanism for executing it. Now the unit of work and mechanism are separate. The key abstraction is the unit of work, which is called a task. There are two kinds of tasks: Runnable and Callable. The general mechanism for executing tasks is the executor service. If you think in terms of tasks and let an executor service execute them for you, you gain great flexibility in terms of selecting appropriate execution policies. 

Note: In essence, the Executor Framework does for execution what the Collections Framework did for aggregation.

The Executor Framework also has a replacement for java.util.Timer, which is ScheduledThreadPoolExecutor. While it is easier to use a timer, a scheduled thread pool executor is much more flexible. A timer uses only a single thread for task execution, which can hurt timing accuracy in the presence of longrunning tasks. If a timer’s sole thread throws an uncaught exception, the timer ceases to operate. A scheduled thread pool executor supports multiple threads and recovers gracefully from tasks that throw unchecked exceptions.

									Item 69: Prefer concurrency utilities to wait and notify
									----------------------------------------------------------------

As of release 1.5, the Java platform provides higher-level concurrency utilities that do the sorts of things you formerly had to hand-code atop wait and notify. Given the difficulty of using wait and notify correctly, you should use the higher-level concurrency utilities instead.

The higher-level utilities in java.util.concurrent fall into three categories: the Executor Framework, concurrent collections; and synchronizers.

The concurrent collections provide high-performance concurrent implementations of standard collection interfaces such as List, Queue, and Map. To provide high concurrency, these implementations manage their own synchronization internally. Therefore, it is impossible to exclude concurrent activity from a concurrent collection; locking it will have no effect but to slow the program.

This means that clients can’t atomically compose method invocations on concurrent collections. Some of the collection interfaces have therefore been extended with state-dependent modify operations, which combine several primitives into a single atomic operation. For example, ConcurrentMap extends Map and adds several methods, including putIfAbsent(key, value), which inserts a mapping for a key if none was present and returns the previous value associated with the key, or null if there was none.This makes it easy to implement thread-safe canonicalizing maps. For example, this method simulates the behavior of String.intern:

	// Concurrent canonicalizing map atop ConcurrentMap - not optimal
	private static final ConcurrentMap<String, String> map = new ConcurrentHashMap<String, String>();
	public static String intern(String s) {
		String previousValue = map.putIfAbsent(s, s);
		return previousValue == null ? s : previousValue;
	}

In fact, you can do even better. ConcurrentHashMap is optimized for retrieval operations, such as get. Therefore, it is worth invoking get initially and calling putIfAbsent only if get indicates that it is necessary:

	// Concurrent canonicalizing map atop ConcurrentMap - faster!
	public static String intern(String s) {
		String result = map.get(s);
		if (result == null) {
			result = map.putIfAbsent(s, s);
		if (result == null)
			result = s;
		}
		return result;
	}

Besides offering excellent concurrency, ConcurrentHashMap is very fast. On my machine the optimized intern method above is over six times faster than String.intern.

		Item 70: Document thread safety
	---------------------------------------
If you don’t document thread safety of a class’s behavior, programmers who use the class will be forced to make assumptions. You might hear it said that you can tell if a method is thread-safe by looking for the synchronized modifier in its documentation. In normal operation, Javadoc does not include the synchronized modifier in its output, and with good reason. The presence of the synchronized modifier in a method declaration is an implementation detail, not a part of its exported API.

Moreover, the claim that the presence of the synchronized modifier is sufficient to document thread safety embodies the misconception that thread safety is an all-or-nothing property. In fact, there are several levels of thread safety. To enable safe concurrent use, a class must clearly document what level of thread safety it supports. The following list summarizes levels of thread safety. It is not exhaustive but covers the common cases:

• immutable—Instances of this class appear constant. No external synchronization is necessary. Examples include String, Long, and BigInteger (Item 15).

• unconditionally thread-safe—Instances of this class are mutable, but the class has sufficient internal synchronization that its instances can be used concurrently without the need for any external synchronization. Examples include Random and ConcurrentHashMap.

• conditionally thread-safe—Like unconditionally thread-safe, except that some methods require external synchronization for safe concurrent use. Examples include the collections returned by the Collections.synchronized wrappers, whose iterators require external synchronization.

• not thread-safe—Instances of this class are mutable. To use them concurrently, clients must surround each method invocation (or invocation sequence) with external synchronization of the clients’ choosing. Examples include the general-purpose collection implementations, such as ArrayList and HashMap.

• thread-hostile—This class is not safe for concurrent use even if all method invocations are surrounded by external synchronization. Thread hostility usually results from modifying static data without synchronization. No one writes a thread-hostile class on purpose; such classes result from the failure to consider concurrency. Luckily, there are very few thread-hostile classes or methods in the Java libraries. The System.runFinalizersOnExit method is thread-hostile and has been deprecated.

		Item 74: Implement Serializable judiciously
		----------------------------------------------------

1)A major cost of implementing Serializable is that it decreases the flexibility to change a class’s implementation once it has been released. When a class implements Serializable, its byte-stream encoding (or serialized form) becomes part of its exported API. Once you distribute a class widely, you are generally required to support the serialized form forever, just as you are required to support all other parts of the exported API. If you accept the default serialized form, the class’s private and package-private instance fields become part of its exported API, and the practice of minimizing access to fields (Item 13) loses its effectiveness as a tool for information hiding.

Clients attempting to serialize an instance using an old version of the class and deserialize it using the new version will experience program failures. Therefore, you should carefully design a high-quality serialized form that you are willing to live with for the long haul. Doing so will add to the initial cost of development, but it is worth the effort.

Every serializable class has a unique identification number associated with it. If you do not specify this number explicitly by declaring a static final long field named serialVersionUID, the system automatically generates it at runtime by applying a complex procedure to the class. The automatically generated value is affected by the class’s name, the names of the interfaces it implements, and all of its public and protected members. If you change any of these things in any way, for example, by adding a trivial convenience method, the automatically generated serial version UID changes. If you fail to declare an explicit serial version UID, compatibility will be broken, resulting in an InvalidClassException at runtime. 

2)A second cost of implementing Serializable is that it increases the likelihood of bugs and security holes. serialization is an extralinguistic mechanism for creating objects. Whether you accept the default behavior or override it, deserialization is a “hidden constructor” with all of the same issues as other constructors.Because there is no explicit constructor associated with deserialization, it is easy to forget that you must ensure that it guarantees all of the invariants established by the constructors and that it does not allow an attacker to gain access to the internals of the object under construction. Relying on the default deserialization mechanism can easily leave objects open to invariant corruption and illegal access .

3)A third cost of implementing Serializable is that it increases the testing burden associated with releasing a new version of a class.
When a serializable class is revised, it is important to check that it is possible to serialize an instance in the new release and deserialize it in old releases, and vice versa. The amount of testing required is thus proportional to the product of the number of serializable classes and the number of releases, which can be large. you must ensure both that the serialization-deserialization process succeeds and that it results in a faithful replica of the original object. The greater the change to a serializable class, the greater the need for testing. The need is reduced if a custom serialized form is carefully designed when the class is first written (Items 75, 78), but it does not vanish entirely.

Benefits:
---------
Implementing the Serializable interface is not a decision to be undertaken lightly. It offers real benefits. It is essential if a class is to participate in a framework that relies on serialization for object transmission or persistence. Also, it greatly eases the use of a class as a component in another class that must implement Serializable. There are, however, many real costs associated with implementing Serializable. Each time you design a class, weigh the costs against the benefits. As a rule of thumb, value classes such as Date and BigInteger should implement Serializable, as should most collection classes. Classes representing active entities, such as thread pools, should rarely implement Serializable.

Classes designed for inheritance (Item 17) should rarely implement Serializable, and interfaces should rarely extend it. For example, if a class or interface exists primarily to participate in a framework that requires all participants to implement Serializable, then it makes perfect sense for the class or interface to implement or extend Serializable. 

Classes designed for inheritance that do implement Serializable include Throwable, Component, and HttpServlet. Throwable implements Serializable so exceptions from remote method invocation (RMI) can be passed from server to client. Component implements Serializable so GUIs can be sent, saved, and restored. HttpServlet implements Serializable so session state can be cached.

If a class that is designed for inheritance is not serializable, it may be impossible to write a serializable subclass. Specifically, it will be impossible if the superclass does not provide an accessible parameterless constructor. Therefore, you should consider providing a parameterless constructor on nonserializable classes designed for inheritance. Often this requires no effort because many classes designed for inheritance have no state, but this is not always the case.

Inner classes (Item 22) should not implement Serializable. They use compiler-generated synthetic fields to store references to enclosing instances and to store values of local variables from enclosing scopes. How these fields correspond to the class definition is unspecified, as are the names of anonymous and local classes. Therefore, the default serialized form of an inner class is illdefined. A static member class can, however, implement Serializable.













