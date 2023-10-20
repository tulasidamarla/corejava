Serialization 
-------------
- Serialization is a mechanism using which an object can be written to an output stream.
- Reading serialized data back to java object is called Deserialization.
- A class must implement java.io.Serializable to do this.
- Serializable is a marker interface.
- Implementing the interface gives default serialization, which may not sufficient in all scenarios.
- To serialize any object obj, use ObjectOutputStream.writeObject() method.
- To deserialize any object, use ObjectInputStream.readObject() method.
```java
Cat c = new Cat();
try {
	FileOutputStream fs = new FileOutputStream("testSer.ser");
	ObjectOutputStream os = new ObjectOutputStream(fs);
	os.writeObject(c); // 3
	os.close();
} catch (Exception e) { 
	e.printStackTrace(); 
}

try {
	FileInputStream fis = new FileInputStream("testSer.ser");
	ObjectInputStream ois = new ObjectInputStream(fis);
	c = (Cat) ois.readObject(); // 4
	ois.close();
} catch (Exception e) { 
	e.printStackTrace(); 
}
```
- If an object contains primitives then it is straight forward.
- If an object contains reference to other objects, then saving object reference has no meaning.
- If an object contains reference to other objects, the classes of those objects also must implement Serializable,
  otherwise NotSerializableException is thrown as below.

```java  	
class Dog implements Serializable{
	private Collar collar;
	private int dogSize;
	public Dog(Collar collar, int size) {
		this.collar = collar;
		dogSize = size;
	}
	
	public Collar getCollar() { 
		return this.collar; 
	}
}

class Collar {
	private int collarSize;
	public Collar(int size) { 
		collarSize = size; 
	}
	public int getCollarSize() { 
		return collarSize; 
	}
}
```

- If Collar class also implements serializable then Dog object will be serialized.

### _What if we don't have access to Collar class source code?_

- Subclass Collar and can serialize subclass instead.
- If subclassing is not an option because Collar class may be final, or it may be referencing other objects, then use
  `transient` modifier with field to skip serializing the fields.
```
	private transient Collar collar;
```	

- Serializing the dog object saves dog object successfully without collar.
- If dog object is deserialized later it will have null value for collar.
- To assign custom values while serializing and deserializing, there are two private call back methods are provided as
  shown below.
  
 ```java 
	private void writeObject(ObjectOutputStream os) {
		try {
			os.defaultWriteObject();
			os.writeInt(this.collar.getCollarSize());
		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}
	private void readObject(ObjectInputStream is) {
		try {
			is.defaultReadObject();
			this.collar = new Collar(is.readInt());
		} catch (ClassNotFoundException e) {
			e.printStackTrace();
		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}	
```

- If part of an object state need to be handled manually while serializing and dersializing use the private callback
  methods as shown above.
- If the whole object state need to be managed manually use Externalizable.
- If a super class implements Serializable all subclasses will be serialized automatically.

### _What if a super class is not serializable but subclass is serializable?_

- During deserialization any instance variables inherited from that superclass(es) will be reset to the values they were given during the original construction of the object.
- This is because the nonserializable class constructor will run. For ex,

```java	
 	class Super {
		int x=42;
	}

	class Sub extends Super implements Serializable {
		int y;
		public Sub(int x,int y) {
			this.x = x;
			this.y = y;
		}
	}
```
- If Sub object is created with new Sub(3,30) and serialized, Later when object is deserialized x and y values are
  set to 42 and 30 respectively.
- Static variables are NEVER saved as part of the object's state.
- `The collection interfaces are not serializable, but the concrete collection classes in the Java API are serializable.`

### _Why is the name Serialization?_
- Each object is saved with the serial number, hence the name object serialization.
  

## Serialization Format

- If an object is saved first time, it is saved to the output stream.
- If an object has been previously saved, then write "same as previously saved object with serial number x".

A class object is saved in the following format.<br>

	• 72
	• 2-byte length of class name
	• Class name
	• 8-byte finger print
	• 1-byte flag
	• 2-byte count of data field descriptors
	• Data field descriptors
	• 78 (end marker)
	• Superclass type (70 if none)
 
- If the class name is test, then it is represented as `72 00 04 Test`
- `8-byte finger print:` This is nothing but the serial version id.
  - The fingerprint is obtained by ordering the descriptions of the class, superclass, interfaces, field types, and
     method signatures in a canonical way, and then applying the so-called Secure Hash Algorithm (SHA) to that data. 
  - SHA finger print is always 20 byte data packet. Java serialization uses first 8 bytes of SHA code as finger print.
- `1 byte flag:` Represents the serialization method.
```
// 1 byte flag
static final byte SC_WRITE_METHOD = 1;
// class has a writeObject method that writes additional data
static final byte SC_SERIALIZABLE = 2;
// class implements the Serializable interface
static final byte SC_EXTERNALIZABLE = 4;
// class implements the Externalizable interface
```

- `2-byte count of data field descriptors`
	00 02 // There are two data fields
- `Data field descriptor`   	
   - Data field descriptor format
     - 1-byte type code
     - 2-byte length of field name
     - Field name
     - Class name (if the field is an object)
```
1-byte type code
	B byte
	C char
	D double
	F float
	I int
	J long
	L object
	S short
	Z boolean
	[ array
```

### Conclusion

- The serialized format contains the types and data fields of all objects.
- Each object is assigned a serial number.
- Repeated occurrences of the same object are stored as references to that serial number.

## Externalizable
- The readObject and writeObject methods only need to save and load their data fields.
- They are not concerned with superclass data or any other class information.
- Instead of letting the serialization mechanism save and restore object data, a class can define its own mechanism by
  implementing the Externalizable interface.
- This, requires a class to define two methods:
  
```java
public void readExternal(ObjectInputStream in) throws IOException, ClassNotFoundException;
public void writeExternal(ObjectOutputStream out) throws IOException;
```

- The readObject and writeObject methods are fully responsible for saving and restoring the entire object, including the superclass data.
- When serializing object, it records the class of the object in the output stream.
- while deserialization, it creates an object with the no-argument constructor and then calls the readExternal method.

```java
public void readExternal(ObjectInput s) throws IOException {
	name = s.readUTF();
	salary = s.readDouble();
	hireDay = LocalDate.ofEpochDay(s.readLong());
}

public void writeExternal(ObjectOutput s)throws IOException{
	s.writeUTF(name);
	s.writeDouble(salary);
	s.writeLong(hireDay.toEpochDay());
}
```

- The readExternal and writeExternal methods are public.
- The readExternal permits modification of the state of an existing object.

## Serialization for Singleton

```java
public class Elvis {
	public static final Elvis INSTANCE = new Elvis();
	private Elvis() { ... }
	public void leaveTheBuilding() { ... }
}
```

- If the above class implements either default or custom serialization, then it is no longer Singleton.
- Any readObject method, whether explicit or default, returns a newly created instance, which will not be the same instance that was created at class initialization time.
- The readResolve feature allows to substitute another instance for the one created by readObject.
- If the class of an object being deserialized defines a readResolve method with the proper declaration, this method is invoked on the newly created object after it is deserialized.

```java
// readResolve for instance control - you can do better!
private Object readResolve() {
	// Return the one true Elvis and let the garbage collector
	// take care of the Elvis impersonator.
	return INSTANCE;
}
```
- This method ignores the deserialized object, returning the Elvis instance that was created during class initialization.
- The serialized form of an Elvis instance need not contain any real data; all instance fields should be declared
  transient.
- It is possible for a determined attacker to secure a reference to the deserialized object before its readResolve
  method is run, so better make all instance fields transient.

### _Cost of implementing Serialization_

#### _Fleixbility_
- A major cost of implementing Serializable is that it decreases the flexibility to change a class’s implementation once
  it has been released.
- When a class implements Serializable, its byte-stream encoding (or serialized form) becomes part of its exported API.
- Once you distribute a class widely, you are generally required to support the serialized form forever, just as you are
  required to support all other parts of the exported API.
- If you accept the default serialized form, the class’s private and package-private instance fields become part of its
  exported API, and the practice of minimizing access to fields loses its effectiveness as a tool for information hiding.
- Clients attempting to serialize an instance using an old version of the class and deserialize it using the new version
  will experience program failures.
- Therefore, you should carefully design a high-quality serialized form that you are willing to live with for the long
  haul. Doing so will add to the initial cost of development, but it is worth the effort.

#### _Use Unique Serialization UID_

- Every serializable class has a unique identification number associated with it.
- If you do not specify this number explicitly by declaring a static final long field named serialVersionUID, the system automatically generates it at runtime by applying a complex  procedure to the class.
- The automatically generated value is affected by the class’s name, the names of the interfaces it implements, and all of its public and protected members.
- If you change any of these things in any way, for example, by adding a trivial convenience method, the automatically generated serial version UID changes.
- If you fail to declare an explicit serial version UID, compatibility will be broken, resulting in an InvalidClassException at runtime. 

#### _Bugs_

- A second cost of implementing Serializable is that it increases the likelihood of bugs and security holes. 
- serialization is an
- extralinguistic mechanism for creating objects. Whether you accept the default behavior or override it, deserialization is a “hidden constructor” with all of the same issues as other constructors.
- Because there is no explicit constructor associated with deserialization, it is easy to forget that you must ensure that it guarantees all of the invariants established by the constructors and that it does not allow an attacker to gain access to the internals of the object under construction.
- Relying on the default deserialization mechanism can easily leave objects open to invariant corruption and illegal access .

#### _Testing_

- A third cost of implementing Serializable is that it increases the testing burden associated with releasing a new version of a class.
- When a serializable class is revised, it is important to check that it is possible to serialize an instance in the new release and  deserialize it in old releases, and vice versa.
- The amount of testing required is thus proportional to the product of the number of serializable classes and the number of releases, which can be large.
- you must ensure both that the  serialization, deserialization process succeeds and that it results in a faithful replica of the original object.
- The greater the change to a serializable class, the greater the need for testing.
- The need is reduced if a custom serialized form is carefully designed when the class is first written , but it does not vanish entirely.

### Benefits:

- Implementing the Serializable interface is not a decision to be undertaken lightly. It offers real benefits. It is essential if a class is to participate in a framework that relies on serialization for object transmission or persistence. Also, it greatly eases the use of a class as a component in another class that must implement Serializable. There are, however, many real costs associated with implementing Serializable. Each time you design a class, weigh the costs against the benefits. As a rule of thumb, value classes such as Date and BigInteger should implement Serializable, as should most collection classes. Classes representing active<br> entities, such as thread pools, should rarely implement Serializable.

- Classes designed for inheritance should rarely implement Serializable, and interfaces should rarely extend it. For example, if a class or interface exists primarily to participate in a framework that requires all participants to implement Serializable, then it makes perfect sense for the class or interface to implement or extend Serializable. 

- Classes designed for inheritance that do implement Serializable include Throwable, Component, and HttpServlet. Throwable implements Serializable so exceptions from remote method invocation (RMI) can be passed from server to client. Component implements Serializable so GUIs can be sent, saved, and restored. HttpServlet implements Serializable so session state can be cached.

- If a class that is designed for inheritance is not serializable, it may be impossible to write a serializable subclass. Specifically, it will be impossible if the superclass does not provide an accessible parameterless constructor. Therefore, you should consider providing a parameterless constructor on nonserializable classes designed for inheritance. Often this requires no effort because many classes designed for inheritance have no state, but this is not always the case.

- Inner classes should not implement Serializable. They use compiler-generated synthetic fields to store references to enclosing instances and to store values of local variables from enclosing scopes. How these fields correspond to the class definition is unspecified, as are the names of anonymous and local classes. Therefore, the default serialized form of an inner class is illdefined. A static member class can, however, implement Serializable.













