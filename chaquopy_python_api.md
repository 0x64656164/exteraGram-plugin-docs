# Python API - Chaquopy 16.1
The `java` module provides facilities to use Java classes and objects from Python code.

Data types#
-------------------------------------------------------

### Overview#

Data types are converted between Python and Java as follows:

*   Java `null` corresponds to Python `None`.
    
*   The Java boolean, integer and floating point types correspond to Python `bool`, `int` and `float` respectively. When Java code uses a “boxed” type, auto-boxing is done when converting from Python to Java, and auto-unboxing when converting from Java to Python.
    
*   Java `String` and `char` both correspond to Python `str`.
    
*   A Java array is represented by a `jarray` object. Java arrays can also be implicitly created from any Python sequence, except a string.
    
*   All other Java objects are represented by a `jclass` object.
    

A `jclass` or `jarray` object received from Java will be represented in Python as its actual run-time type, which is not necessarily the declared type of the method, field or parameter the object was obtained from. It can be viewed as another compatible type using the `cast` function.

### Primitives#

A Python `bool`, `int`, `float` or `str` object can be passed directly to any compatible Java method parameter or field.

However, Java has more primitive types than Python, so when more than one compatible integer or floating-point overload is applicable for a method call, the longest one will be used. Similarly, when a 1-character string is passed to a method which has overloads for both `String` and `char`, the `String` overload will be used.

If these rules do not give the desired result, the following wrapper classes can be used to select which Java primitive type you want to use:

_class_ java.jboolean(_value_)
#

_class_ java.jbyte(_value_, _truncate\=False_)
#

_class_ java.jshort(_value_, _truncate\=False_)
#

_class_ java.jint(_value_, _truncate\=False_)
#

_class_ java.jlong(_value_, _truncate\=False_)
#

_class_ java.jfloat(_value_, _truncate\=False_)
#

_class_ java.jdouble(_value_, _truncate\=False_)
#

_class_ java.jchar(_value_)
#

_class_ java.jvoid#

`jvoid` cannot be instantiated, but may be used as a return type when defining a static proxy.

For example, if `p` is a PrintStream:

```
p.print(42)              # will call print(long)
p.print(jint(42))        # will call print(int)
p.print(42.0)            # will call print(double)
p.print(jfloat(42.0))    # will call print(float)
p.print("x")             # will call print(String)
p.print(jchar("x"))      # will call print(char)

```


The numeric type wrappers take an optional `truncate` parameter. If this is set, any excess high-order bits of the given value will be discarded, as with a cast in Java. Otherwise, passing an out-of-range value to a wrapper class will result in an `OverflowError`.

When these wrappers are used, Java overload resolution rules will be in effect for the wrapped parameter. For example, a `jint` will only be applicable to a Java `int` or larger, and the _shortest_ applicable overload will be used.

### Classes#

The most convenient way to access Java classes is to use the import hook. However, you may also use the following function:

java.jclass(_cls\_name_)
#

Returns a Python class for a Java class or interface type. The name must be fully-qualified, using either Java notation (e.g. `java.lang.Object`) or JNI notation (e.g. `Ljava/lang/Object;`). To refer to a nested or inner class, separate it from the containing class with `$`, e.g. `java.lang.Map$Entry`.

If the class cannot be found, a `NoClassDefFoundError` is raised.

Java classes and objects can be used with normal Python syntax:

```
>>> Calendar = jclass("java.util.Calendar")
>>> c = Calendar.getInstance()
>>> c.getTimeInMillis()
1588934677166
>>> c.getTime()
<java.util.Date 'Fri May 08 11:44:37 GMT+01:00 2020'>
>>> c.get(Calendar.YEAR)
2020
>>> c.before(Calendar.getInstance())
True

```


Overloaded methods are resolved according to Java rules:

```
>>> from java.lang import String, StringBuffer
>>> sb = StringBuffer(1024)
>>> sb.append(True)
>>> sb.append(123)
>>> sb.append(cast(String, None))
>>> sb.append(3.142)
>>> sb.toString()
'true123null3.142'

```


If a method or field name clashes with a Python reserved word, it can be accessed by appending an underscore, e.g. `from` becomes `from_`. The original name is still accessible via `getattr`").

Aside from attribute access, Java objects also support the following operations:

*   `str`") calls toString).
    
*   `==` and `!=` call equals).
    
*   `is` is equivalent to Java `==` (i.e. it tests object identity).
    
*   `hash`") calls hashCode).
    
*   The equivalent of the Java syntax `ClassName.class` is `ClassName.getClass()`; i.e. the `getClass()` method can be called on a class as well as an instance.
    
*   If a Java object implements a functional interface, then it can be called like a function using `()` syntax. This includes lambdas, method references, and any interface with a single abstract method, such as `java.lang.Runnable`. If an object implements more than one functional interface, you must use `cast` to select the one you want, or simply call the method by name.
    

The Java class hierarchy is reflected in Python, e.g. if `s` is a Java `String` object, then `isinstance(s, Object)` and `isinstance(s, CharSequence)` will both return `True`. All array and interface types are also considered subclasses of `java.lang.Object`.

### Arrays#

java.jarray(_element\_type_)
#

Returns a Python class for a Java array type. The element type may be specified as any of:

*   The primitive types `jboolean`, `jbyte`, etc.
    
*   A Java class returned by `jclass`, or by `jarray` itself.
    
*   A `java.lang.Class` instance
    
*   A JNI type signature
    

`jarray` objects represent Java arrays. They support the standard Python sequence protocol, including:

*   Getting and setting elements using `[]` syntax. Negative indices are supported.
    
*   Getting and setting slices using `[:]` syntax. When getting a slice, a new array of the same type will be returned.
    
*   Copying using the `copy` method or the `copy.copy`") function. A new array of the same type will be returned. `copy.deepcopy`") is not currently supported.
    
*   Getting length using `len`"), and testing for emptiness using `bool`").
    
*   Iteration using `for`, and searching using `in`.
    
*   Since Java arrays are fixed-length, they do not support `append`, `del`, or any other way of adding or removing elements.
    

All arrays are instances of `java.lang.Object`, so they inherit the `Object` implementations of `toString`, `equals` and `hashCode`. However, these default implementations are not very useful, so the equivalent Python operations are defined as follows:

*   `str` returns a representation of the array contents.
    
*   `==` and `!=` can compare the contents of the array with any sequence.
    
*   Like Python lists, Java array objects are not hashable in Python because they’re mutable.
    

#### Creating#

There’s usually no need to create `jarray` objects directly, because any Python sequence (except a string) can be passed directly to a Java method or field which takes an array type:

*   When passing a sequence to a Java method, Chaquopy will create a Java array and copy the sequence into it. If the sequence is writable, it will also copy the Java array back into the sequence after the method returns.
    
*   Assigning a sequence to a Java field is the same, except modifications will not be copied back.
    

When a Java method has multiple array-type overloads, you may need to disambiguate the call. For example, if a class defines both `f(long[] x)` and `f(int[] x)`, then calling `f([1,2,3])` will fail with an ambiguous overload error. To call the `int[]` overload, use `f(jarray(jint)([1,2,3]))`.

More examples:

```
# Python code                                 // Java equivalent
jarray(jint)([1, 2, 3])                       new int[] {1, 2, 3}
jarray(jarray(jint))([[1, 2], [3, 4]])        new int[][] {{1, 2}, {3, 4}}
jarray(String)(["Hello", "world"])            new String[] {"Hello", "world"}
jarray(jchar)("hello")                        new char[] {'h', 'e', 'l', 'l', 'o'}

```


You can also pass an integer to create an array filled with zero, `false` or `null`:

```
# Python code                                 // Java equivalent
jarray(jint)(5)                               new int[5]

```


#### Converting#

Java arrays of `boolean`, `byte`, `short`, `int`, `long`, `float` and `double` all implement the Python buffer protocol. So if you convert a Java array to a NumPy array, it will automatically use the correct data type:

```
>>> a = jarray(jshort)([0, 32767])
>>> numpy.array(a)
array([    0, 32767], dtype=int16)

```


Calling `bytes`") on an array will return its in-memory representation:

```
>>> a = jarray(jshort)([0, 32767])
>>> bytes(a)
b'\x00\x00\xff\x7f'

```


When converting a Python `bytes`") or `bytearray`") object to a Java `byte[]` array, there is an unsigned-to-signed conversion: Python values 128 to 255 will be mapped to Java values -128 to -1:

```
>>> b = bytes([0, 127, 128, 255])
>>> jarray(jbyte)(b)
jarray('B')([0, 127, -128, -1])

```


Similarly, when converting a `byte[]` array to a `bytes`") or `bytearray`") object, there is a signed-to-unsigned conversion:

```
>>> a = jarray(jbyte)([0, 127, -128, -1])
>>> [int(x) for x in bytes(a)]
[0, 127, 128, 255]

```


### Casting#

java.cast(_cls_, _obj_)
#

Returns a view of the given object as the given class. The class must be one created by `jclass` or `jarray`, or a JNI type signature for a class or array. The object must either be assignable to the given class, or `None` (representing Java `null`), otherwise `TypeError` will be raised.

Situations where this could be useful are the same as those where you might use the Java cast syntax `(ClassName)obj`. By changing the apparent type of an object:

*   Different members may be visible on the object.
    
*   A different overload may be chosen when passing the object to a method.
    

Import hook#
---------------------------------------------------------

The import hook allows you to import Java classes using normal Python syntax. For example:

```
from java.lang import System, Thread

```


Be aware of the following limitations:

*   Only the `from ... import` form is supported, e.g. `import java.lang.String` will not work.
    
*   Wildcard import is not supported, e.g. `from java.lang import *` will not work.
    
*   Importing entire packages is not supported, e.g. `import java.lang` and `from java import lang` will not work.
    
*   Nested and inner classes cannot be imported directly. Instead, import the outer class (e.g. `from java.util import Map`), then access the nested class as an attribute (e.g. `Map.Entry`).
    
*   The Java package will not be added to `sys.modules`").
    

To avoid confusion, it’s recommended to avoid having a Java package and a Python module with the same name. However, this is still possible, subject to the following points:

*   Names imported from the Java package will not automatically be added as attributes of the Python module.
    
*   Imports from both languages may be intermixed, even within a single `from ... import` statement. If you attempt to import a name which exists in both languages, the value from Python will be returned. This can be worked around by accessing the Java name via `jclass`. For example, if both Java and Python have a class named `com.example.Class`, then you can access them like this:
    
    ```
from com.example import Class as PythonClass
JavaClass = jclass("com.example.Class")

```

    
*   Relative package syntax is supported. For example, within a Python module called `com.example.module`:
    
    ```
from . import Class                # Same as "from com.example import Class"
from ..other.package import Class  # Same as "from com.other.package import Class"

```

    

java.set\_import\_enabled(_enable_)
#

Sets whether the import hook is enabled. The import hook is enabled automatically when the `java` module is first loaded, so you only need to call this function if you want to disable it.

Inheriting Java classes#
---------------------------------------------------------------------------

To allow Python code to be called from Java without using the Chaquopy Java API, you can inherit from Java classes in Python. To make your inherited class visible to Java code, a corresponding Java proxy class must be generated, and there are two ways of doing this:

*   A dynamic proxy uses the java.lang.reflect.Proxy mechanism to generate a Java class at runtime.
    
*   A static proxy uses a build-time tool to generate Java source code.
    

### Dynamic proxy#

java.dynamic\_proxy(_\*implements_)
#

If you use the return value of this function as the first base of a class declaration, then the java.lang.reflect.Proxy mechanism will be used to generate a Java class for it at runtime. All arguments must be Java interface types.

Dynamic proxy classes are implemented just like regular Python classes, but any method names defined by the given interfaces will be visible to Java. If Java code calls an interface method which is not implemented by the Python class, a PyException will be thrown.

Simple example:

```
>>> from java.lang import Runnable, Thread
>>> class R(dynamic_proxy(Runnable)):
...     def __init__(self, name):
            super().__init__()
            self.name = name
...     def run(self):
...         print("Running " + self.name)
...
>>> r = R("hello")
>>> t = Thread(r)
>>> t.getState()
<java.lang.Thread$State 'NEW'>
>>> t.start()
Running hello
>>> t.getState()
<java.lang.Thread$State 'TERMINATED'>

```


For more examples, see the unit tests.

Dynamic proxy classes have the following limitations:

*   They cannot extend Java classes, only implement Java interfaces.
    
*   They cannot be referenced by name in Java source code.
    
*   They cannot be instantiated in Java, only in Python.
    

If you need to do any of these things, you’ll need to use a static proxy instead.

### Static proxy#

java.static\_proxy(_extends\=None_, _\*implements_, _package\=None_, _modifiers\='public'_)
#

If you use the return value of this function as the first base of a class declaration, and pass the containing module to the static proxy generator tool, then it will create a Java source file allowing the class to be instantiated and accessed by Java code.

*   `extends` must be a Java class type. Pass `None` if you only want to implement interfaces.
    
*   All other positional arguments must be Java interface types to implement.
    
*   `package` is the name of the Java package in which the Java class will be generated, defaulting to the same as the Python module name.
    
*   `modifiers` is described below.
    

To generate Java methods for the class, use the following decorators:

java.method(_return\_type_, _arg\_types_, _\*_, _modifiers\='public'_, _throws\=None_)
#

Generates a Java method.

*   `return_type` must be a single Java type, or `jvoid`.
    
*   `arg_types` must be a (possibly empty) list or tuple of Java types.
    
*   `modifiers` is a string which is copied to the generated Java declaration. For example, to make a method synchronized, you can pass `modifiers="public synchronized"`.
    
*   `throws` is a list or tuple of `java.lang.Throwable` subclasses.
    

All Java types must be specified as one of the following:

*   A Java class imported using the import hook.
    
*   One of the primitive types.
    
*   A `jarray` type.
    

Multiple decorators may be used on the same method, in which case multiple Java signatures will be generated for it (see notes below on overloading).

java.Override(_return\_type_, _arg\_types_, _\*_, _modifiers\='public'_, _throws\=None_)
#

Same as `method`, but adds the @Override annotation.

java.constructor(_arg\_types_, _\*_, _modifiers\='public'_, _throws\=None_)
#

Same as `method`, except it generates a Java constructor. This decorator can only be used on the `__init__` method. Note there is no return type.

Simple example:

```
# Python code                                     // Java equivalent
from com.example import Base, Iface1, Iface1      import com.example.*;
from java.lang import String, Exception

class C(static_proxy(Base, Iface1, Iface2)):      class C extends Base implements Iface1, Iface2 {

    @constructor([jint, String])                      public C(int i, String s) {
    def __init__(self, i, s):                             ...
        ...                                           }

    @method(jvoid, [jint], throws=[Exception])        public void f(int i) throws Exception {
                                                          ...
                                                      }

    @Override(String, [jint, String],                 @Override
              modifiers="protected")                  protected String f(int i, String s) {
    def f(self, i, s=None):                               ...
        ...                                           }
                                                  }

```


For more examples, see the demo app and unit tests.

Because the static proxy generator works by static analysis of the Python source code, there are some restrictions on the code’s structure:

*   Only classses which appear unconditionally at the module top-level will be processed.
    
*   Java classes passed to `static_proxy` or its method decorators must have been imported using the import hook, not dynamically with `jclass`.
    
    *   Nested classes can be referenced by importing the top-level class and then using attribute notation, e.g. `Outer.Nested`.
        
    *   The names bound by the `import` statement must be used directly. For example, after `from java.lang import IllegalArgumentException as IAE`, you may use `IAE` in a `throws` declaration. However, if you assign `IAE` to another variable, you cannot use that variable in a `throws` declaration, as the static proxy generator will be unable to resolve it.
        
    *   The static proxy generator does not currently support relative package syntax.
        
*   String parameters must be given as literals.
    
*   Extending `static_proxy` classes with other `static_proxy` classes is not currently supported. However, behaviour can still be shared between `static_proxy` classes by making them inherit from a common Python base class.
    

### Notes#

The following notes apply to both types of proxy:

*   Proxy classes may have additional Python base classes, as long as the `dynamic_proxy` or `static_proxy` expression comes first.
    
*   When a Python method is called from Java, the parameters and return value are converted as described in data types above. In particular, if the method is passed an array, including via varargs syntax (”…”), it will be received as a `jarray` object.
    
*   If a method is overloaded (i.e. it has multiple Java signatures), the Python signature should be able to accept them all. This can usually be achieved by some combination of duck typing, default arguments, and `*args` syntax. For example, see the method `f` in the static proxy example above.
    
*   To call through to the base class implementation of a method, use the syntax `SuperClass.method(self, args)`. Using `super`") is not currently supported for Java methods, though it may be used in `__init__`.
    
*   Special methods:
    
    *   If you override `__init__`, you _must_ call through to the superclass `__init__` with zero arguments. Until this is done, the object cannot be used as a Java object. Supplying arguments to the superclass constructor is not currently possible.
        
    *   If you override `__str__`"), `__eq__`") or `__hash__`"), they will only be called when an object is accessed from Python. It’s usually better to override the equivalent Java methods `toString`, `equals` and `hashCode`, which will take effect in both Python and Java.
        
    *   If you override any other special method, make sure to call through to the superclass implementation for any cases you don’t handle.
        

Exceptions#
-------------------------------------------------------

### Throwing#

`java.lang.Throwable` is represented as inheriting from the standard Python `Exception`") class, so all Java exceptions can be thrown in Python.

When inheriting Java classes, exceptions may propagate from Python to Java code:

*   The exception will have a combined Python and Java stack trace, with Python frames indicated by a package name starting “<python>”.
    
*   If the exception is of a Java type, the original exception object will be used. Otherwise, it will be represented as a PyException.
    

### Catching#

Exceptions thrown by Java methods will propagate into the calling Python code. The Java stack trace is added to the exception message:

```
>>> from java.lang import Integer
>>> Integer.parseInt("abc")
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  ...
java.lang.NumberFormatException: For input string: "abc"
        at java.lang.NumberFormatException.forInputString(NumberFormatException.java:65)
        at java.lang.Integer.parseInt(Integer.java:580)
        at java.lang.Integer.parseInt(Integer.java:615)

```


Java exceptions can be caught with standard Python syntax, including catching a subclass exception via the base class:

```
>>> from java.lang import IllegalArgumentException
>>> try:
...     Integer.parseInt("abc")
... except IllegalArgumentException as e:
...     print type(e)
...     print e
...
<class 'java.lang.NumberFormatException'>
For input string: "abc"
        at java.lang.NumberFormatException.forInputString(NumberFormatException.java:65)
        at java.lang.Integer.parseInt(Integer.java:580)
        at java.lang.Integer.parseInt(Integer.java:615)

```


Multi-threading#
------------------------------------------------------------------------

The global interpreter lock (GIL) is automatically released whenever Python code calls a Java method or constructor, allowing Python code to run on other threads while the Java code executes.

If your program contains threads created by any means _other_ than the Java Thread API or the Python `threading`") module, you should be aware of the following function:

java.detach()
#

Detaches the current thread from the Java VM. This is done automatically on exit for threads created via the `threading`") module. Any other non-Java-created thread which uses the `java` module must call `detach` before the thread exits, or the process may crash.
