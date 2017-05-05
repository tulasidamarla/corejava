Serialization 
-------------
Serialization is a mechanism using which an object can be written to an output stream and read again later. Later is called <br> Deserialization.

To Serialize an object, class must implement java.io.Serializable. Serializable is a marker interface. It means no need to alter <br> class in any way. Simply implementing Serializable interface we get default serialization. But, this is not advisable.

To serialize any object, we need to use ObjectOutputStream.writeObject() method.<br>
To deserialize any object, we need to use ObjectInputStream.readObject() method.

Ex:

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

Note: If an object contains primitives then it is straight forward. If an object contains reference to other objects, <br> 
then saving object reference has no meaning. For ex, If you serialize below Dog object you will get java.io.NotSerializableException.
	
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

Note: If Collar class also implements serializable then Dog object will be serialized.

What if we don't have access to Collar class source code?<br>
We can subclass Collar and can serialize subclass instead. 

what if subclassing is not an option because Collar class may be final, or it may be referencing other objects?<br>
This is where transient modifier comes in. transient modifier allows to serialize an object fields except those which have <br>
transient modifier. In the above example modify Dog class instance variable Collar like this.
	private transient Collar collar;
	
Note: Now serializing the dog object saves dog object successfully without collar. If we try to deserialize dog object now we will <br> get a Collar object with null value. To avoid this situation we have set of private methods you can implement in your class that, <br> if present, will be invoked automatically during serialization and deserialization.

	private void writeObject(ObjectOutputStream os) {
		// your code for saving the Collar variables
	}
	private void readObject(ObjectInputStream is) {
		// your code to read the Collar state, create a new Collar,
		// and assign it to the Dog
	}

Ex:
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

Note:The most common reason to implement writeObject() and readObject() is when you have to save some part of an object's state <br> manually. If you choose, you can write and read ALL of the state yourself, but that's very rare. For this scenario, we use <br> Externalizable.

Note: By default, If a super class/interface implements Serializable all subclasses or implementing classes will be serialized <br> automatically.

What if a super class is not serializable but subclass is serializable?<br>
Ans:During deserialization any instance variables inherited from that superclass will be reset to the values they were given during <br> the original construction of the object. This is because the nonserializable class constructor will run. For ex,
	class Super{
		int x=42;
	}

	class Sub extends Super implements Serializable{
		int y;
		public Sub(int x,int y) {
			this.x = x;
			this.y = y;
		}
	}

For ex, If Sub object is created with new Sub(3,30), Later when object is deserialized we get values of 42,30.

Note: Static variables are NEVER saved as part of the object's state.

Note:while the collection interfaces are not serializable, the concrete collection classes in the Java API are serializable.

Why is the name Serialization?
Ans: Each object is saved with the serial number, hence the name object serialization. 

Note: If an object is saved first time, it is saved to the output stream. If an object has been previously saved, then write <br> 
"same as previously saved object with serial number x".

Serialization Format<br>
--------------------<br>
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
 
Ex: If the class name is test, then it is represented as "72 00 04 Test"

8-byte finger print --> This is nothing but the serial version id. The fingerprint is obtained by ordering the descriptions <br> 
of the class, superclass, interfaces, field types, and method signatures in a canonical way, and then applying the so-called <br>
Secure Hash Algorithm (SHA) to that data. 

Note: SHA finger print is always 20 byte data packet. Java serialization uses first 8 bytes of SHA code as finger print.

	1 byte flag -->
	static final byte SC_WRITE_METHOD = 1;
	// class has a writeObject method that writes additional data
	static final byte SC_SERIALIZABLE = 2;
	// class implements the Serializable interface
	static final byte SC_EXTERNALIZABLE = 4;
	// class implements the Externalizable interface

2-byte count of data field descriptors --> 
	00 02 // There are two data fields 
	
	Data field descriptor format -->
	• 1-byte type code
	• 2-byte length of field name
	• Field name
	• Class name (if the field is an object)

1-byte type code<br>
----------------<br>
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

From the above description we understand that <br>
• The serialized format contains the types and data fields of all objects.<br>
• Each object is assigned a serial number.<br>
• Repeated occurrences of the same object are stored as references to that serial number.

Externalizable<br>
--------------<br>
The readObject and writeObject methods only need to save and load their data fields. They should not concern themselves <br> 
with superclass data or any other class information.

Instead of letting the serialization mechanism save and restore object data, a class can define its own mechanism. To do this, <br> 
a class must implement the Externalizable interface. This, in turn, requires it to define two methods:

public void readExternal(ObjectInputStream in) throws IOException, ClassNotFoundException;<br>
public void writeExternal(ObjectOutputStream out) throws IOException;

Unlike the readObject and writeObject methods these methods are fully responsible for saving and restoring the entire object, <br> including the superclass data. When writing an object, the serialization mechanism merely records the class of the object in the <br> output stream. When reading an externalizable object, the object input stream creates an object with the no-argument constructor <br> and then calls the readExternal method.

Ex:

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

Note:Unlike the readObject and writeObject methods, which are private and can only be called by the serialization mechanism, <br> 
the readExternal and writeExternal methods are public. In particular, readExternal potentially permits modification of the state of <br> an existing object.

Serialization for Singleton<br>
---------------------------<br>
	public class Elvis {
		public static final Elvis INSTANCE = new Elvis();
		private Elvis() { ... }
		public void leaveTheBuilding() { ... }
	}

If the above class implements Serializable, then it is no longer Singleton.	It doesn’t matter whether the class uses the <br> default serialized form or a custom serialized form, nor does it matter whether the class provides an explicit readObject method. <br> Any readObject method, whether explicit or default, returns a newly created instance, which will not be the same instance that was <br> created at class initialization time.

The readResolve feature allows you to substitute another instance for the one created by readObject.If the class of an object <br> 
being deserialized defines a readResolve method with the proper declaration, this method is invoked on the newly created object <br> after it is deserialized.

	// readResolve for instance control - you can do better!
	private Object readResolve() {
		// Return the one true Elvis and let the garbage collector
		// take care of the Elvis impersonator.
		return INSTANCE;
	}

Note:This method ignores the deserialized object, returning the distinguished Elvis instance that was created when the class was <br> initialized. Therefore, the serialized form of an Elvis instance need not contain any real data; all instance fields should be <br> declared transient.	
Otherwise, it is possible for a determined attacker to secure a reference to the deserialized object before its readResolve method <br> is run.

Serialization When to Use?<br>
--------------------------

1)A major cost of implementing Serializable is that it decreases the flexibility to change a class’s implementation once it has <br> been released. When a class implements Serializable, its byte-stream encoding (or serialized form) becomes part of its exported API.<br> Once you distribute a class widely, you are generally required to support the serialized form forever, just as you are required to <br> support all other parts of the exported API. If you accept the default serialized form, the class’s private and package-private <br> instance fields become part of its exported API, and the practice of minimizing access to fields loses its effectiveness as a tool <br> for information hiding.

Clients attempting to serialize an instance using an old version of the class and deserialize it using the new version will <br> experience program failures. Therefore, you should carefully design a high-quality serialized form that you are willing to live <br> with for the long haul. Doing so will add to the initial cost of development, but it is worth the effort.

Every serializable class has a unique identification number associated with it. If you do not specify this number explicitly by <br> declaring a static final long field named serialVersionUID, the system automatically generates it at runtime by applying a complex <br> procedure to the class. The automatically generated value is affected by the class’s name, the names of the interfaces it <br> implements, and all of its public and protected members. If you change any of these things in any way, for example, by adding a <br> trivial convenience method, the automatically generated serial version UID changes. If you fail to declare an explicit serial <br> version UID, compatibility will be broken, resulting in an InvalidClassException at runtime. 

2)A second cost of implementing Serializable is that it increases the likelihood of bugs and security holes. serialization is an <br> extralinguistic mechanism for creating objects. Whether you accept the default behavior or override it, deserialization is a <br> “hidden constructor” with all of the same issues as other constructors.Because there is no explicit constructor associated with <br> deserialization, it is easy to forget that you must ensure that it guarantees all of the invariants established by the constructors <br> and that it does not allow an attacker to gain access to the internals of the object under construction. Relying on the default <br> deserialization mechanism can easily leave objects open to invariant corruption and illegal access .

3)A third cost of implementing Serializable is that it increases the testing burden associated with releasing a new version of a class.

When a serializable class is revised, it is important to check that it is possible to serialize an instance in the new release and <br> deserialize it in old releases, and vice versa. The amount of testing required is thus proportional to the product of the number of <br> serializable classes and the number of releases, which can be large. you must ensure both that the  serialization, deserialization<br> process succeeds and that it results in a faithful replica of the original object. The greater the change to a serializable class, <br> the greater the need for testing. The need is reduced if a custom serialized form is carefully designed when the class is first <br> written , but it does not vanish entirely.

Benefits:<br>
---------<br>
Implementing the Serializable interface is not a decision to be undertaken lightly. It offers real benefits. It is essential if a<br> class is to participate in a framework that relies on serialization for object transmission or persistence. Also, it greatly eases<br> the use of a class as a component in another class that must implement Serializable. There are, however, many real costs associated<br> with implementing Serializable. Each time you design a class, weigh the costs against the benefits. As a rule of thumb, value<br> classes such as Date and BigInteger should implement Serializable, as should most collection classes. Classes representing active<br> entities, such as thread pools, should rarely implement Serializable.

Classes designed for inheritance should rarely implement Serializable, and interfaces should rarely extend it. For example, if a<br> class or interface exists primarily to participate in a framework that requires all participants to implement Serializable, then it<br> makes perfect sense for the class or interface to implement or extend Serializable. 

Classes designed for inheritance that do implement Serializable include Throwable, Component, and HttpServlet. Throwable implements<br> Serializable so exceptions from remote method invocation (RMI) can be passed from server to client. Component implements Serializable <br> so GUIs can be sent, saved, and restored. HttpServlet implements Serializable so session state can be cached.

If a class that is designed for inheritance is not serializable, it may be impossible to write a serializable subclass. Specifically,<br> it will be impossible if the superclass does not provide an accessible parameterless constructor. Therefore, you should<br> consider providing a parameterless constructor on nonserializable classes designed for inheritance. Often this requires no effort<br> because many classes designed for inheritance have no state, but this is not always the case.

Inner classes should not implement Serializable. They use compiler-generated synthetic fields to store references to enclosing<br> instances and to store values of local variables from enclosing scopes. How these fields correspond to the class definition is<br> unspecified, as are the names of anonymous and local classes. Therefore, the default serialized form of an inner class is illdefined.<br> A static member class can, however, implement Serializable.













