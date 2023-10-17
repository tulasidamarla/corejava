# Equals method

- We can't use objects of a class as a key in a hashtable if equals() method is not overridden and won't get accurate Sets, such that there are no conceptual duplicates.
- The equals() method in class Object uses only the == operator for comparisons, so unless overriding equals(),  two objects are considered equal only if the two references refer to the same object.

## The equals() Contract

- It is reflexive. For any reference value x, x.equals(x) should return true.<br>
- It is symmetric. For any reference values x and y, x.equals(y) should return true if and only if y.equals(x) returns true.<br>
- It is transitive. For any reference values x, y, and z, if x.equals(y) returns true and y.equals(z) returns true, then x.equals(z) must return true.<br>
- It is consistent. For any reference values x and y, multiple invocations of x.equals(y) consistently return true or consistently return false, provided no information used in equals comparisons on the object is modified.<br>
- For any non-null reference value x, x.equals(null) should return false.

## Objects.equals()

- To guard against the possibility of one of the elements could be null, use the Objects.equals method.
- The call Objects.equals(a, b) returns true if both arguments are null, false if only one is null, and calls a.equals(b) otherwise.

## equals() method with inheritance

- If subclasses can have their own notion of equality, then the symmetry requirement forces you to use the getClass test.
  - For ex if e is an Employee object and m is a Manager object, both of which happen to have the same name, salary, and hire date.
  - If e.equals(m) uses an instanceof test, the call returns true because a Manager is also of type Employee.
  - For symmetry property to be true. But, call m.equals(e) returns false because employee is not of type Manager.	

		if (getClass() != obj.getClass())
			return false;

		
- If the notion of equality is fixed in the superclass, then you use the `instanceof` test to allow objects of different subclasses to be equal to one another.
- It means that Manager and Clerk are both Employees because equals() contract is defined in Employee class.
- As mentioned above, symmetry doesn't work for Manager and Employee, so equals() should not defined in subclasses at all.

		if(!(this instanceof other))
			return false;

## equals implementation mismatches in Java library

- The standard Java library contains over 150 implementations of equals methods, with a mismatch of using instanceof, 
calling getClass, catching a ClassCastException, or doing nothing at all.
- For ex, Check out the API documentation of the java.sql.Timestamp class.
- The Timestamp class inherits from java.util.Date, whose equals method uses an instanceof test, and it is impossible to override equals to be both symmetric and accurate.

# Writing the perfect equals method:

- Test whether this happens to be identical to otherObject:
```
	if (this == otherObject) return true;
```
- Test whether otherObject is null and return false if it is. This test is required.
	```if (otherObject == null) return false;```
- Compare the classes of this and otherObject. If the semantics of equals can change in subclasses, use the getClass test:
	```if (getClass() != otherObject.getClass()) return false;```
- If the same semantics holds for all subclasses, you can use an instanceof test:
	```if (!(otherObject instanceof ClassName)) return false;```
- Cast otherObject to a variable of your class type:
	```ClassName other = (ClassName) otherObject```
- Now compare the fields, as required by your notion of equality. Use `==` for primitive type fields, Objects.equals for object fields. 
Return true if all fields match, false otherwise.
	```return field1 == other.field1
	&& Objects.equals(field2, other.field2)
	&& . . .;```
	
- If equals() is redefined in a subclass, include a call to super.equals(other).

## Collections

- Key interfaces in java collection framework are Collection, List, Set, Queue, Map, SortedSet,SortedMap,NavigableSet, NavigableMap.
- Key legacy collections Vector, Hashtable are synchronized.
- Searching a collection using Collections class can be done with binarySearch() method.
  - For searching collection has to be sorted first.
  - If comparator is used for sorting, then same comparator has to be passed for searching also. 

## binarySearch()

- binarySearch() method returns an integer representing the index of the element if found.
- Unsuccesful search returns a negative integer which represents the insertion position.
- If search returns an index -n, then insertion position is (-(n)-1)).
  - For ex, if search returns -3, then insertion position is 3-1.i.e. 2.
- Searching an array that was not sorted will give unpredictable results.
- Similarly, using compartor either with sorting or with searching but not both can also result in unpredictable results.

## NavigableSet and NavigableMap

- Java 6 introduces two interfaces NavigableSet and NavigableMap to collection API. To describe these, here is an example.
```
	TreeSet<Integer> times = new TreeSet<Integer>();
	times.add(1205); 
	times.add(1505);
	times.add(1545);
	times.add(1830);
	times.add(2010);
	times.add(2100);
```
- To find the biggest number before 1600 and smallest number after 2000, in java 5.
```
	TreeSet<Integer> subset = new TreeSet<Integer>();
	subset = (TreeSet)times.headSet(1600);
	System.out.println("J5 - last before 4pm is: " + subset.last());
	
	TreeSet<Integer> sub2 = new TreeSet<Integer>();
	sub2 = (TreeSet)times.tailSet(2000);
	System.out.println("J5 - first after 8pm is: " + sub2.first());
```	
- In java 6, same can be achived using lower() and higher() methods like this.
	```
	System.out.println("J6 - last before 4pm is: " + times.lower(1600));
	System.out.println("J6 - first after 8pm is: " + times.higher(2000));	
	```
- Similarly there are other methods like ceiling(),floor() etc. For maps, there are lowerKey(),floorKey() etc. 
- The lower() returns an element lessthan the given element and floor() returns an element lessthan or equal to the given element.
- Same behavior with celiling() and higher() methods.



