# effective-java-notes
A collection of the major points made in the book Effective Java by Joshua Bloch (Third Edition). 

Disclaimer: All content is taken from the book and should credited to Joshua Bloch and the references listed in the book.

# TODO
- Special formatting for code, method names, etc
- Escape some angled brackets that aren't showing up

# Table of Contents
#### [2. Creating and Destroying Objects](https://github.com/earlejam/effective-java-notes/blob/master/README.md#2-creating-and-destroying-objects-1)
#### [3. Methods Common to All Objects](https://github.com/earlejam/effective-java-notes/blob/master/README.md#3-methods-common-to-all-objects-1)
#### [4. Classes and Interfaces](https://github.com/earlejam/effective-java-notes/blob/master/README.md#4-classes-and-interfaces-1)
#### [5. Generics](https://github.com/earlejam/effective-java-notes/blob/master/README.md#5-generics-1)
#### [6. Enums and Annotations](https://github.com/earlejam/effective-java-notes/blob/master/README.md#6-enums-and-annotations-1)
#### [7. Lambdas and Streams](https://github.com/earlejam/effective-java-notes/blob/master/README.md#7-lambdas-and-streams-1)
#### [8. Methods](https://github.com/earlejam/effective-java-notes/blob/master/README.md#8-methods-1)
#### [9. General Programming](https://github.com/earlejam/effective-java-notes/blob/master/README.md#9-general-programming-1)
#### [10. Exceptions](https://github.com/earlejam/effective-java-notes/blob/master/README.md#10-exceptions-1)
#### [11. Concurrency](https://github.com/earlejam/effective-java-notes/blob/master/README.md#11-concurrency-1)
#### [12. Serialization](https://github.com/earlejam/effective-java-notes/blob/master/README.md#12-serialization-1)

# 2. Creating and Destroying Objects

#### 1. Consider static factory methods

Pros:
- Named, unlike constructors
- Not required to return a _new_ object, which allows for instance controlling
- Can return an object of any subtype of their return type
- Class of returned object can vary from call to call as a function of the input parameters
- Class of returned object need not exist when the class containing the method is written, which is the basis of _Service Provider Frameworks_

Cons:
- No public/protected constructor means no subclassing
- Hard for programmers to find

Common names:
- `from()` - type conversion
- `of()` - aggregation
- `valueOf()` - more verbose
- `instance()` or `getInstance()` - returns instance, but not the same value
- `create()` or `newInstance()` - call returns new instance
- `getType()` or `newType()` - if in different class
- `type()` - more concise

#### 2. Consider a builder when faced with many constructor parameters

- Telescoping constructors pattern hard to read, doesn't scale well
- JavaBeans pattern allows inconsistency, mandates mutability
- Builder simulates named parameters from languages like Python
- Well-suited to class hierarchies
- Downside: have to create `Builder` objects

#### 3. Enforce the singleton property with a private constructor or an enum type

- A singleton is a class that is instantiated exactly once
- Can make it difficult to test its clients
- Best way to implement is with a single element enum

#### 4. Enforce noninstantiability with a private constructor

- E.g. a collection of methods (Math, Arrays)
- Attempting to enforce noninstantiability by making a class abstract does not work
- Solution: private constructor means no public method to create an instance

#### 5. Prefer dependency injection to hardwiring resources

- Static utility classes and singletons are inappropriate for classes whose behavior is parameterized by an underlying resource (e.g. dictionary for spellcheck program)
- Dependency injection provides testability and flexibility

#### 6. Avoid creating unnecessary objects

- Objects can always be reused if they're immutable
- Try static factory methods or static initializers (for mutable objects)
- Prefer primitives to boxed primitives and watch out for unintentional autoboxing

#### 7. Eliminate obsolete object references

- Memory leaks ~ "unintentional object retentions"
- Solution: null out references once they're obsolete
- But: nulling out references should be the exception, not the norm
- So: do this when you're "managing memory manually", e.g. a custom data structure like a queue where old elements may remain but the garbage collector doesn't know what is in use and not
- Another source: caches & listeners and other callbacks

#### 8. Avoid finalizers and cleaners

- Finalizers are unpredictable, often dangerous, and generally unnecessary
- Cleaners are less dangerous than finalizers, but are still unpredictable and generally unnecessary
- Never do anything time-critical in a finalizer or cleaner
- Never depend on a finalizer to update critical persistent state
- There is a _severe_ performance penalty for using finalizers
- Instead, provide an explicit termination method (e.g. `file.close()`)
- Explicit termination methods are typically used in combination with the try-finally construct to ensure termination
- Two valid uses of finalizers and cleaners:
  - Can be used as a fail-safe if explicit termination method is forgotten by programmer
  - Finalizer should log warning if it finds that the resource has not been terminated
  - Objects with native peers
  - Remember `super.finalize()`
  - Finalizer Guardian with public nonfinal class
- In sum, don't use either of these except as a safety net or to terminate noncritical native resources. Even then, beware indeterminacy and performance hits

#### 9. Always use try-with-resources in preference to try-finally when working with resources that must be closed

- Code shorter and cleaner, and better exceptions provided to programmer


# 3. Methods Common to All Objects

#### 10. Obey the general contract when overriding equals

- Don't override if:
  - each instance is inherently unique
  - no need for logical equality test
  - superclass overrode equals and still applies
  - certain that `equals` will never be invoked
- Once you've violated the `equals` contract, you don't how other objects will behave when confronted with yours
- There is no way to extend an instantiable class and add a value component while preserving the `equals` contract
- Workaround: favor composition over inheritance (e.g. give `ColorPoints` a private `Point` field and a public view method: `asPoint()`)
- But: can add a value component to a subclass of an abstract class without violating `equals` contract
- Do NOT write an `equals` method that relies on unreliable resources (e.g. network access)
- Recipe for a high-quality equals method:
  1. Use `==` operator to check if argument is reference to this object
  2. Use `instanceof` to check if argument has correct type
  3. Cast argument to correct type (since it's an `Object` to start)
  4. For each "significant" field in the class, check if the field in the argument matches the corresponding one in the object. Compare fields most likely to differ first, or the ones that are less expensive to compare
  5. When done writing, check 1) symmetry, 2) transitive, 3) consistent
- Always override `hashcode` when you override `equals`. Don't be too clever
- Make sure the parameter is the `Object` type

#### 11. Always override hashcode when you override equals

- Equal objects must have equal hashcodes
- Do not be tempted to exclude significant fields from the `hashcode` computation to improve performance
- Don't provide a detailed specification of the value returned by `hashcode`, so clients can't depend on it and you can change it

#### 12. Always override toString

- Makes your class more pleasant to use and makes systems using the class easier to debug
- When practical, `toString` should return _all_ the interesting info contained in the object
- Whether or not you decide to specify a format (and corresponding static factory for converting back) you should clearly document your intentions
- Provide programmatic access to the info contained in the value returned by `toString` (e.g. accessors)

#### 13. Override clone judiciously

- `Cloneable` interface means protected `clone` method on `Object` returns field-by-field copy of the object
- A class implementing `Cloneable` is expected to provide a properly functioning, public clone method
- By convention, object should be obtained by calling `super.clone()`, not constructor
- Immutable classes should never provide a `clone` method (wasteful copying)
- Must ensure that the new object does no harm to the original object and properly establishes invariants on the clone
- The `Cloneable` architecture is incompatible with normal use of final fields referring to mutable objects
- Try a `deepCopy` method for objects with complex mutable state
- A clone method must never invoke an overridable method on the clone under construction
- Public `clone` methods should omit the `throws` clause
- A better approach to object copying is to provide a "copy constructor" or "copy factory" because 1) they don't conflict with proper use of final fields and 2) don't throw unnecessary checked exceptions, etc
- New interfaces should not extend `Cloneable`
- Arrays are better with `clone`, everything else is better with copy constructors or factories

#### 14. Consider implementing Comparable

- Should generally agree with equals
- Use of `<` and `>` in `compareTo` methods is verbose, error-prone, and not recommended
- Start with the most significant fields
- Do not use difference-based comparators


# 4. Classes and Interfaces

#### 15. Minimize the accessibility of classes and members

- Cleanly separate API from implementation (encapsulation)
- Make each class or member as inaccessible as possible
- Accessibility levels:
  - `private` - accessible only from top-level class where it's declared
  - `package-private`: accessible from any class in the package where it's declared
  - `protected`: accessible from subclasses of the class and from any class in the package where it's declared
  - `public`: accessible from anywhere
- Instance fields of public classes should rarely be public; classes with public mutable fields are not generally thread-safe
- It is _wrong_ for a class to have a `public static final` array field or an accessor that returns such a field

#### 16. In public classes, use accessor methods, not public fields

- If a class is accessible outside its package, provide accessor methods
- If a class is package-private or is a private nested class, there is nothing inherently wrong with exposing its data fields

#### 17. Minimize mutability

- 5 rules to follow:
  1. Don't provide mutators
  2. Ensure the class can't be extended (make the class final)
  3. Make all fields final
  4. Make all fields private
  5. Ensure exclusive access to any mutable components
- Immutable objects are simple
- Immutable objects are inherently thread safe and require no synchronization
- Immutable objects can be shared freely: they never require defensive copies
- You should never provide a `clone` method or a copy constructor for an immutable class
- Not only can you share immutable objects, but they can share their internals
- Immutable objects make great building blocks for other objects (especially good for map keys or sets)
- Immutable objects provide failure atomicity for free
- The major disadvantage of immutable classes is that they require a separate object for each distinct value (which can be costly, especially if objects are large)
- Classes should be immutable unless there's a very good reason to make them mutable
- If a class cannot be made immutable, limit its mutability as much as possible
- Constructors should create fully initialized objects with all invariants established

#### 18. Favor composition over inheritance

- Inheritance is powerful for code reuse, but used inappropriately can lead to fragile code
- Unlike method invocation, inheritance violates encapsulation
- Superclass implementation details can change, breaking subclass
- Superclass can acquire new methods in subsequent releases that subclasses don't know about
- Solution: give your class a private field that references an instance of the existing class instead of extending it (_composition_)
  - The resulting class will be rock-solid, with no dependencies on the implementation details of the existing class
  - 'Forward' results of calling methods on the existing (private member) class
- Composition also known as the _Decorator_ pattern
- Disadvantage of wrapper classes:
  - Not suited for use in callback frameworks; callback elude wrapper (SELF problem)
  - Tedious to write forwarding methods
- If you use inheritance where composition is preferable, you needlessly expose implementation details

#### 19. Design and document for inheritance or else prohibit it

- The class must document its self-use of overridable methods
- A class may have to provide hooks into its internal workings in the form of judiciously chosen protected methods
- The _only_ way to test a class designed for inheritance is to write subclasses (~3 sufficient)
- You must test your class by writing subclasses _before_ you release it
- Constructors must not invoke overridable methods (leads to program failures)
- Neither `clone` nor `readObject` may invoke an overridable method directly or indirectly
- The best solution is to prohibit subclassing in classes that are not designed and documented to be safely subclassed

#### 20. Prefer interfaces to abstract classes

- Existing classes can easily be retrofitted to implement a new interface
- Interfaces are ideal for defining _mixins_
  - mixin: type that a class can implement in addition to its "primary type" to declare that it provides some optional behavior (e.g. `Comparable`)
- Interfaces allow for the construction of nonhierarchical type frameworks
- Interfaces enable safe, powerful functionality enhancements via the wrapper class idiom
- Can provide a "skeletal implementation class" to go with an interface
  - The interface defines the type, and the skeletal implementation class implements the remaining non-private interface methods atop the primitive interface methods (_Template Method_ pattern)
  - Called `AbstractInterfaceName` by convention
- Good documentation is absolutely essential in a skeletal implementation

#### 21. Design interfaces for posterity

- Adding new methods to existing interfaces is fraught with risk
- It is not always possible to write a default method that maintains all invariants of every conceivable implementation (e.g. thread safety)
- In the presence of default methods, existing implementations of an interface may compile without errors or warnings but fail at runtime
- It is still of the utmost importance to design interfaces with great care; don't count on fixing flaws post-release

#### 22. Use interfaces only to define types

- The constant interface (anti)pattern is a poor use of interfaces
- Misc: can use underscore in large numbers to make them more readable

#### 23. Prefer class hierarchies to tagged classes

- Tagged classes are cluttered with boilerplate, readability is harmed
- Tagged classes have multiple implementations jumbled together into a single class
- Tagged classes are verbose, error-prone, and inefficient
- A tagged class is just a pallid imitation of a class hierarchy

#### 24. Favor static member classes over nonstatic

- Static member class: an ordinary class that happens to be declared inside another class
- Common use: public helper class (e.g. `Operation` enum with calculator class)
- Common use of non-static version: define an _Adapter_ that allows an instance of the outer class to be viewed as an instance of some unrelated class
- If you declare a member class that does not require access to an enclosing instance, _always_ make it static
- Anonymous class: has no name, not a member of its enclosing class.
  - They're permitted at any point an expression is legal
  - Can't instantiate them except at point they're declared
  - Can't use `instanceof`
  - Can't implement multiple interfaces
  - Must be kept short

#### 25. Limit source files to a single top-level class

- Multiple top-level classes in same file means it's possible to provide multiple definitions
- Can change behavior based on the order files are passed to the compiler (!)
- Never put multiple top-level classes or interfaces in a single source file


# 5. Generics

#### 26. Don't use raw types

- Each generic type defines a set of parameterized types, which consist of the class or interface name followed by an angle-bracketed list of _actual_ type parameters (List<E> -> List<String>)
- Raw types (List) behave as if all generic type info were erased from the type declaration
- If you use raw types, you lose all the safety and expressiveness benefits of generics
- You lose type safety if you use a raw type like List, but not if you use a parameterized type list List<Object>
- Instead of raw types, use "unbounded wildcard types" if you want to use a generic type but you don't know or care what the actual type parameter is; so for a Set<E>, use Set<?>
- You can't put _any_ element (other than null) into a Collection<?>; you also can't assume anything about the type of objects you get out. If you care about type, try generic methods or _bounded_ wildcard types
- You must use raw types in class literals
- Also illegal to use instanceof operator on parameterized types since generic type info is erased as runtime
- This is the preferred way to use the instanceof operator with generic types:
  -  if (o instanceof Set) {
      Set<?> s = (Set<?> o);
     }
- Generic Terms:
  
  | Term | Example |
  | ------| ------- |
  | Parameterized Type | List\<String> |
  | Actual Type Parameter | String |
  | Generic Type | List<E> |
  | Format Type Parameter | E |
  | Unbounded Wildcard Type | List<?> |
  | Raw Type | List |
  | Bounded Type Parameter | \<E extends Number> |
  | Recursive Type Bound | <T extends Comparable<T>> |
  | Bounded Wildcard Type | List<? extends Number> |
  | Generic Method | static <E> List<E> asList(E[] a) |
  | Type Token | String.class |

#### 27. Eliminate unchecked warnings

- Eliminate every unchecked warning that you can
- If you can't eliminate a warning, but you can prove that the code that provoked the warning is typesafe, then (and only then) suppress the warning with an @SuppressWarnings("Unchecked") annotation
- Always use the @SuppressWarnings annotation on the smallest scope possible
- Every time you use a @SuppressWarnings("Unchecked") annotation, add a comment saying why it is safe to do so

#### 28. Prefer lists to arrays

- Arrays are "deficient" since some type checks will fail at runtime instead of compile time
- Arrays are covariant whereas generics are invariant
- Arrays are reified, meaning they enforce their type at runtime, whereas generics use type erasure
- Generics and arrays don't mix well: new List<E>[], new List<String>[], new E[] are all illegal
- Generic arrays would not be typesafe
- E, List<E>, List<String> are known as non-reifiable types meaning their runtime representations contain less info than their compile-time representations
- Try List<E> instead of E[]

#### 29. Favor generic types

- How to generify a class:
  - Add 1+ type parameters to its declaration (public class Stack<E>)
  - Replace Object with E
  - Fix warnings and errors (e.g. with arrays, but beware of heap pollution)
- Generifying a class does not break it for existing clients

#### 30. Favor generic methods

- Generic methods need a type parameter declared in angle brackets before the return type
- Generic singleton factory: a static factory method that deals out a single immutable object for each requested type parameterization
- Common use of recursive type bounds is in connection with the Comparable interface:
  - public interface Comparable<T> { int compareTo(T o); }
  - Used to ensure "mutual compatibility", i.e. each element in a collection can be compared to every other
  - public static <E extends Comparable<E>> E max(Collection<E> c);
    - Type reads as "any type E that can be compared to itself"
  - Can generify methods without breaking existing clients

#### 31. Use bounded wildcards to increase API flexibility

- <? extends E> for subtypes
- <? super E> for supertypes
- For maximum flexibility, use wildcard types on input parameters that represent producers or consumers
- Rule - PECS: Producer-extends, Consumer-super
- Do not use bounded wildcard types as return types!
- If the user of a class has to think about wildcard types, there is probably something wrong with its API
- Use Comparable<? super T> in preference to Comparable<T>
- Use Comparator<? super T> in preference to Comparator<T>
- If a type parameter appears only once in a _method declaration_, replace it with a wildcard
- Remember that all comparables and comparators are consumers

#### 32. Combine generics and varargs judiciously

- Varargs are a "leaky abstraction"; array is visible
- Heap pollution occurs when a variable of a parameterized type refers to an object that is not of that type
- It is unsafe to store a value in a generic varargs array parameter
- The @SafeVarargs annotation constitutes a promise by the author of a method that it's typesafe
- Safe means the method doesn't store anything into the varargs array (overwrite) and doesn't allow an array reference to escape
- It is unsafe to give another method access to a generic varargs parameter array (with two exceptions)
- Use @SafeVarargs on every method with a varargs parameter of generic or parameterized type
- Could also replace varargs parameter with List parameter

#### 33. Consider typesafe heterogeneous containers

- E.g. database rows have arbitrary number of columns
- Parameterize the _key_ instead of the container, then present key to access container
- When a class literal is passed among methods to communicate both compile-time and runtime type info, it's called a _type token_.
- Recall "Favorites" example with a String, an int, and a class.
- Also possible to use a _bounded type token_ using a bounded type parameter or bounded wildcard


# 6. Enums and Annotations

#### 34. Use enums instead of int constants

- Int constants have no type safety, little expressiveness
- Brittle because if the actual values change, must be re-compiled by clients
- Enums are classes that export one instance for each enum constant via a public final static field
- To associate data with enum constants, declare instance fields and write a constructor that takes the data and stores it in the fields
- To associate different behaviors with each enum constant, declare an abstract method in the enum type, and override it with a concrete method for each constant in a "constant-specific class body".
- Use the "Strategy enum" pattern to force each new enum constant that's added to provide an implementation for the strategy (e.g. Overtime pay)
- Switches on enums are good for augmenting enum types with constant-specific behavior
- Use enums anytime you need a set of constants whose members are known at compile-time
- It is not necessary that the set of all constants in an enum type stay fixed for all time

#### 35. Use instance fields instead of ordinals

- Never derive a value associated with an enum from its ordinal; store it in an instance field instead

#### 36. Use EnumSet instead of bit fields

- Just because an enumerated type will be used in sets, there is no reason to represent it with bit fields

#### 37. Use EnumMap instead of ordinal indexing

- Most serious problem with ordinal indexing: your responsibility to use correct int value; ints do not provide the type safety of enums
- Can use 3-parameter version of Collectors.groupingBy to specify EnumMap implementation if desired
- It is rarely appropriate to use ordinals to index into arrays; use EnumMap instead

#### 38. Emulate extensible enums with interfaces

- Would like to have extensible enums for opcodes
- Emulate this by defining an interface for the opcode type and an enum that is the standard implementation of the interface
- While you cannot write an extensible enum type, you can emulate it by writing an interface to accompany a basic enum type that implements the interface

#### 39. Prefer annotations to naming patterns

- Naming patterns:
  - Typographical errors lead to silent failures
  - No way to ensure they're used on appropriate program elements
  - No good way to associate parameter values with program elements
- Annotations solve these problems (@AnnotationName)
- There is simply no reason to use naming patterns when you can add annotations instead
- All programmers should use the predefined annotation types that Java provides

#### 40. Consistently use the @Override annotation

- Use the @Override annotation on every method declaration that you believe to override a superclass declaration
- Not required to annotate methods that you believe to override abstract method declarations in concrete classes

#### 41. Use marker interfaces to define types

- Marker interface: no method declarations, merely designates or "marks" a class that implements the interface as having some property (e.g. Serializable)
- Marker interfaces _define a type_ that is implemented by instances of the marked class; marker annotations do not
- Marker interfaces can be targeted more precisely
- The Set interface is arguable just a restricted marker interface
- The chief advantage of marker annotations over marker interfaces is that they're part of the larger annotations facility
- How to decide between the two:
  - If it's not a class or interface, choose the annotation
  - If class or interface: "Might I want to write 1+ methods that accept objects only having this marking?"
    - If so, use a marker interface
- If you find yourself writing a marker annotation type whose target is ElementType TYPE, take the time to figure out whether it really should be an annotation type or marker interface


# 7. Lambdas and Streams

#### 42. Prefer lambdas to anonymous classes

- Interfaces with a single abstract method are now known as _functional interfaces_: the language now allows you to create instances using lambda expressions
- The compiler uses type inference to eliminate boilerplate
- Omit the types of all lambda parameters unless their presence makes your program clearer
- Unlike methods and classes, lambdas lack names and documentation; if a computation isn't self-explanatory, or exceeds a few lines, don't put it in a lambda
- For lambdas, one line is ideal, and 3 lines is a reasonable max
- Constant-specific class bodies:
  - Use if enum type has constant-specific behavior that is 1) difficult to understand, 2) can't be implemented in a few lines, or 3) requires access to instance fields or methods
- Uses for anonymous classes:
  - Create instance of abstract class
  - Create instances of interfaces with multiple abstract methods
  - Need access to function object from within its body
- You should rarely, if ever, serialize a lambda. Instead, use instance of private static nested class
- Don't use anonymous classes for function objects unless you have to create instances of types that aren't functional interfaces

#### 43. Prefer method references to lambdas

- Multiset with lambda vs method reference:
  - lambda: map.merge(key, 1, (count, incr) -> count + incr);
  - method reference: map.merge(key, 1, Integer::sum);
- Method reference reduces visual clutter
- Sometimes lambda parameters are useful documentation
  - Consider them especially with large class names or if method lies within same class as lambda
- Method references vs lambdas:

| Method Reference Type | Example | Lambda Equivalent |
| --- | --- | --- |
| Static | Integer::parseInt | str -> Integer.parseInt(str) |
| Bound | Instant.now()::isAfter | Instant then = Instant.now(); t -> then.isAfter(t); |
| Unbound | String::toLowerCase | str -> str.toLowerCase() |
| Class constructor | TreeMap<K,V>::new | () -> new TreeMap<K,V> |
| Array constructor | int[]::new | len -> new int[len] |
- Where method references are shorter and clearer, use them; where they aren't, stick with lambdas

#### 44. Favor the use of standard functional interfaces

- Now that Java has lambdas, you'll be writing more constructors and methods that take function objects as parameters
- If one of the standard functional interfaces does the job, you should generally use it in preference to a purpose-built functional interface
- Basic functional interfaces:
  1. Operator: function whose result and argument types are the same (UnaryOperator and BinaryOperator)
  2. Predicate: function that takes an argument and returns a boolean
  3. Function: argument and return types differ
  4. Supplier: function that takes no arguments and returns (supplies) a value
  5. Consumer: function that takes an argument but returns nothing
- 6 basic functional interfaces:

| Interface | Function Signature | Example |
| --- | --- | --- |
| UnaryOperator<T> | T apply(T t) | String::toLowerCase |
| BinaryOperator<T> | T apply(T t1, T t2) | BigInteger::add |
| Predicate<T> | boolean test(T t) | Collection::isEmpty |
| Function<T, R> | R apply(T t) | Arrays::asList |
| Supplier<T> | T get() | Instant::now |
| Consumer<T> | void accept(T t) | System.out::println |
- Don't be tempted to use basic functional interfaces with boxes primitives instead of primitive functional interfaces
- Seriously consider writing a purpose-built functional interface if you need one that shares one of the following with Comparator:
   - It will be commonly used and could benefit from a descriptive name
   - It has a strong contract associated with it
   - It would benefit from custom default methods
- Always annotate your functional interfaces with the @FunctionalInterface annotation
  - Tells readers that the interface was designed to enable lambdas
  - Keeps you honest because interface won't compile unless it has exactly one abstract method
  - Prevents maintainers from accidentally adding abstract methods

#### 45. Use streams judiciously

- Stream: a finite or infinite sequence of elements
- Stream pipeline: multistage computation on these elements
  - Source stream -> 1+ intermediate operations -> 1 terminal operation
- Stream pipelines are evaluated lazily
- Default is to run sequentially
- Overusing streams makes programs hard to read and maintain
- In the absence of explicit types, careful naming of lambda parameters is essential to the readability of stream pipelines
- Using helper methods is even more important for readability in stream pipelines than in iterative code
- Refrain from using streams to process char values
- Refactor existing code to use streams and use them in new code only where it makes sense to do so
- It's hard to access corresponding elements from multiple stages of a pipeline simultaneously with streams; when applicable, try inverting the mapping when you need access to the earlier-stage value
- Name streams the plural noun describing the elements of the stream
- If you're not sure whether a task is better served by streams or iteration, try both and see which works better

#### 46. Prefer side-effect-free functions in streams

- Streams paradigm: want result of each stage as close as possible to 'pure function' of the result of the previous stage
  - Pure functions' results only depend on input, no other state
- A foreach operation that does anything more than present the result of the computation performed by the stream is a "bad smell"
- The foreach operation should be used only to report the result of a stream computation, not to perform the computation
- _Collector_: an opaque object that encapsulates a reduction strategy (combining elements of the stream into a single object)
- It is customary and wise to statically import all members of Collectors because it makes stream pipelines more readable
- _groupingBy_: returns collectors to produce maps that group elements into categories based on a classifier function
- _downstream collector_: produces a value from a stream containing all the elements in a category
- There is never a reason to say collect(counting())
- _minBy/maxBy_: take a comparator and return the minimum or maximum element in the stream (determined by Comparator)
-_joining_: joins streams of character sequences (e.g. strings)
- most important ones: toList, toSet, toMap, groupingBy, joining

#### 47. Prefer Collection to Stream as a return type

- Programmers cannot use for-each loops with streams because Stream does not extend Iterable
- Can write an adapter to go from stream to iterable, and vice versa
- Collections provide both iteration and stream access. So, Collection or an appropriate subtype is generally the best return type for a public, sequence-returning method
- Do not store a large sequence in memory just to return it as a collection

#### 48. Use caution when making streams parallel

- Parallelizing a pipeline is unlikely to increase its performance if the source is from Stream.iterate or the intermediate operation 'limit' is used
- Do not parallelize stream pipelines indiscriminately
- Performance gains from parallelism are best on streams over ArrayList, HashMap, HashSet, and ConcurrentHashMap instances; arrays; int ranges; and long ranges
  - This is because all of these can be easily split into ranges by a 'spliterator'
  - All provide at least good locality of reference
- Parallelizing a pipeline will do little if a lot of work is done in the terminator and that work is inherently sequential
  - The best candidates for parallelism are reductions and short-circuiting operations
- Not only can parallelizing a stream lead to poor performance, including liveness failures; it can lead to incorrect results and unpredictable behavior (safety failures)
- Under the right circumstances, it _is_ possible to achieve near-linear speedup in the number of processor cores simply by adding a 'parallel' call to a stream pipeline


# 8. Methods

#### 49. Check parameters for validity

- A method can fail quickly and cleanly with an appropriate exception if checked right away
  - Failure to do so can result in a violation of _failure atomicity_
- The Objects.requireNonNull method is flexible and convenient, so there's no reason to perform null checks manually anymore
- It's important to check validity of parameters not used by a method, but stored for later use (including constructors)
- Indiscriminate reliance on implicit validity checks can result in the loss of _failure atomicity_

#### 50. Make defensive copies when needed

- Even in a safe language, you must program defensively, with the assumption that clients of your class will do their best to destroy its invariants
- Date is obsolete and should no longer be used in new code
- It is essential to make defensive copies of the mutable parameters to the constructor
- Defensive copies are made _before_ checking the validity of the parameters, and the validity check is performed on the copies rather than the originals
  - Protects the class against changes to the parameters from another thread, known as TOCTOU attacks
- Do not use the clone method to make a defensive copy of a parameter whose type is subclassable by untrusted parties
- Return defensive copies of mutable internal fields
- Nonzero length arrays are always mutable; thus, make a defensive copy or return an immutable view
- Real lesson: use immutable objects as components when possible to avoid worrying about defensive copying
- If the cost of the copy would be prohibitive _and_ the class trusts its clients not to modify the components inappropriately, then the defensive copy may be replaced by documentation outlining the client's responsibility not to modify the affected components

#### 51. Design method signatures carefully

- Choose method names carefully
- Don't go overboard in providing convenience methods; when in doubt, leave it out
- Avoid long parameter lists: aim for 4 or fewer
- Long sequences of identically typed parameters are especially harmful
  - Will compile and run with swapped parameters
  - How to shorten:
    1) Break method into multiple methods
    2) Create helper classes to hold groups of parameters
    3) Adapt the builder pattern from object construction to method invocation
- For parameter types, favor interfaces over classes
- Prefer two-element enum types to boolean parameters, unless the meaning of the boolean is clear from the _method name_

#### 52. Use overloading judiciously

- Recall that the choice of which overloading (method) to invoke is made at compile time
- Selection among overloaded methods is _static_, while selection among overridden methods is _dynamic_
- Avoid confusing uses of overloading
- A safe, conservative policy is never to export two overloading with the same number of parameters
- You can always give methods different names instead of overloading them
- Do NOT overload methods to take different functional interfaces in the same argument position

#### 53. Use varargs judiciously

- Varargs methods accept 0+ parameters of the same type (int... args)
- To accept 1+, use one guaranteed parameter and one varargs parameter
- Exercise care when using varargs in performance-critical situations

#### 54. Return empty collections or arrays, not nulls

- There is no reason to special-case the situation where nothing is returned
- Returning nulls is error-prone because the programmer writing the client may forget to write the special case code to handle a null return value
- Do not preallocate an empty array in hopes of improving performance
- Never return null in place of an empty array or collection

#### 55. Return optionals judiciously

- The Optional<T> class represents an immutable container that can hold either a single non-null T reference or nothing at all
  - If the Optional<T> contains nothing, we call it _empty_
  - If the Optional<T> contains a value of type T, we call it _present_
- A method that conceptually returns a T but may be unable to do so under certain circumstances can instead be declared to return an Optional<T>
- Never return a null value from an Optional-returning method: it defeats the entire purpose of the facility
- Optionals are similar in spirit to checked exceptions, in that they force the user of an API to confront the fact that there may be no value returned
- Container types, including Collections, Maps, Streams, arrays and Optionals should _not_ be wrapped in Optionals
- You should declare a method to return Optional<T> if it might not be able to return a result _and_ clients will have to perform special processing if no result is returned
- You should never return an Optional of a boxed primitive type (use OptionalInt, etc)
- It is almost never appropriate to use an Optional as a key, value, or element in a collection or an array

#### 56. Write doc comments for all exposed API elements

- To document your API properly, you must precede every exported class, interface, constructor, method, and field declaration with a doc comment
- If a class is serializable, you should also document its serialized form
- The doc comment for a method should describe succinctly the contract between them method and its client
  - What it does, not how
  - Method's preconditions and postconditions
  - Any side effects: observable changes in the state of the system that are not obviously required in order to achieve the postcondition
- Doc comments should be readable both in the source code and in the generated documentation
- No two members or constructors in a class or interface should have the same summary description
- When documenting a generic type or method, be sure to document all type parameters
- When documenting an enum type, be sure to document the constants as well as the type and any public methods
- When documenting an annotation type, be sure to document any members and the type itself
- Whether or not a class or static method is thread-safe, you should document its thread safety
- Read the web pages generated by the Javadoc Utility to ensure they look correct and are readable


# 9. General Programming

#### 57. Minimize the scope of local variables

- Increases the readability and maintainability of the code, reduces likelihood for error
- The most powerful technique for minimizing the scope of a local variable is to declare it where it is first used
- Nearly every local variable declaration should contain an initializer
- Prefer for-loops to while-loops
- Keep methods small and focused

#### 58. Prefer for-each loops to traditional for-loops

- 3 common situations where you can't use for-each:
  1. Destructive filtering: use iterator & its remove method instead
  2. Transforming: use list iterator or array index instead
  3. Parallel iteration: use iterator or array index variable instead
- For-each loop provides compelling advantages over traditional for-loop in clarity, flexibility, and bug prevention, with no performance penalty

#### 59. Know and use the libraries

- By using a standard library, you take advantage of the knowledge of the experts who wrote it and the experience of those who used it before you
- The random number generator ThreadLocalRandom is now the top choice
- Numerous features are added to libraries in every major release, and it pays to keep abreast of these additions
- Every programmer should be familiar with the basics of java.lang, java.util, and java.io, and their subpackages; the others can be learned on a need-to-know basis

#### 60. Avoid float and double if exact answers are required

- The float and double types are particularly ill-suited for monetary calculations
- Instead, use BigDecimal, int, or long for monetary calculations
- BigDecimal less convenient than using primitive, and a lot slower

#### 61. Prefer primitive types to boxed primitives

- Applying the == operator to boxed primitives is almost always wrong
- When you mix primitives and boxed primitives in an operation, the boxed primitive is auto-unboxed
- Use boxed primitives as elements, keys, and values in collections, and for types in parameterized types or methods
- Boxed primitives should be used for reflective method invocations
- Autoboxing reduces verbosity, but not the danger of using boxed primitives; (auto) unboxing can cause a NullPointerException

#### 62. Avoid strings where other types are more appropriate

- Strings are poor substitutes for other value types
- Strings are poor replacements for enum types
- Strings are poor substitutes for aggregate types
- Strings are poor substitutes for capabilities
- Used inappropriately, strings are more cumbersome, less flexible, slower and more error-prone than other types

#### 63. Beware the performance of string concatenation

- Using the string concatenation operator repeatedly to concatenate n strings requires time quadratic in n
- To achieve acceptable performance, use a StringBuilder in place of a String
- Don't use the string concatenation operator to combine more than a few strings

#### 64. Refer to objects by their interfaces

- If appropriate interfaces exist, then parameters, return values, variables, and fields should all be declared using interface types
- If you get in the habit of using interfaces as types, your program will be much more flexible
- It is entirely appropriate to refer to an object by a class rather than an interface if no appropriate interface exists
  - E.g. value classes, objects belonging to a framework, and classes that implement an interface but also provide extra methods not found in the interface
- If there is no appropriate interface, just use the least-specific class in the class hierarchy that provides the required functionality

#### 65. Prefer interfaces to reflection

- The _core reflection facility_ offers programmatic access to arbitrary classes
- Given a Class object, you can obtain Constructor, Method, and Field instances representing the actual entities of the class
- These let you manipulate their underlying counterparts reflectively: you can construct instances, invoke methods, and access fields of the underlying class
- Reflection allows one class to use another, even if the latter class didn't exist when the former was compiled
- Price of reflection:
  1. You lose all the benefits of compile-time type checking, including exception checking
  2. The code required to perform reflective access is clumsy and verbose
  3. Performance suffers
- You can obtain many of the benefits of reflection while incurring few of its costs by using it only in a very limited form
- Create instances reflectively and access them normally via their interface or superclass
- A legitimate, if rare, use of reflection is to manage a class's dependencies on other classes, methods, or fields that may be absent at runtime

#### 66. Use native methods judiciously

- The Java Native Interface (JNI) allows Java programs to call _native_ methods, which are methods written in _native programming languages_ such as C or C++.
- Uses:
  1. Provide access to platform-specific facilities such as registries
  2. Provide access to existing libraries of native code & legacy libraries & legacy data
  3. Write performance-critical parts of applications in native languages for improved performance
- It is rarely advisable to use native methods for improved performance
- Serious disadvantages:
  1. Native languages are not safe
  2. Programs are less portable
  3. Harder to debug
  4. Performance can decrease due to unknowing garbage collector
  5. Cost associated with going into and out of native code

#### 67. Optimize judiciously

- Don't sacrifice sound architectural principles for performance
- Strive to write good programs rather than fast ones
- Good programs embody the principle of information hiding: where possible, they localize design decisions within individual components
- Strive to avoid design decisions that limit performance
- The design components most difficult to change after the fact are those specifying interactions between components and with the outside world: APIs, wire-level protocols, and persistent data formats in particular
- Consider the performance consequences of your API design decisions
- It is a very bad idea to warp an API to achieve good performance
- Measure performance before and after each attempted optimization
- Profiling tools can help you decide where to focus your optimization efforts

#### 68. Adhere to generally accepted naming conventions

- The Java Platform has a well-established set of naming conventions, many of which are contained in the JLS
- Two main categories: typographical & grammatical
- Violations can confuse and irritate other programmers who work with the code

- Naming Conventions:

  | Identifier Type | Examples |
  | --- | --- |
  | Package or module | org.junit.jupiter.api, com.google.common.collect |
  | Class or Interface | Stream, FutureTask, LinkedHashMap, HttpClient |
  | Method or Field | remove, groupingBy, getCrc |
  | Constant Field | MIN_VALUE, NEGATIVE_INFINITY |
  | Local Variable | i, denom, houseNum |
  | Type Parameter | T, E, K, V, X, R, U, V, T1, T2 |
- Grammatical naming conventions are more flexible and more controversial than typographical conventions
- Refer to the JLS for more examples


# 10. Exceptions

#### 69. Use exceptions only for exceptional conditions

- Exceptions are, as their name implies, to be used only for exceptional conditions; they should never be used for ordinary control flow
- A well-designed API must not force its clients to use exceptions for ordinary control flow
- State-depending methods should have a separate state-testing method (e.g. next() and hasNext() for iterators) OR return an empty optional or a distinguished value

#### 70. Use checked exceptions for recoverable conditions and runtime exceptions for programming errors

- Use checked exceptions for conditions from which the caller can reasonably be expected to recover
- By confronting the user with a checked exception, the API designer presents a mandate to recover from the condition
- Use runtime exceptions to indicate programming errors
- The great majority of runtime exceptions indicate precondition violations
- All of the unchecked throwables you implement should subclass RuntimeException
- Parsing the string representation of an exception to ferret out additional information is extremely bad practice; it is nonportable and fragile
- Provide methods on your checked exceptions to aid in recovery

#### 71. Avoid unnecessary use of checked exceptions

- Unlike return codes and unchecked exceptions, these force programmers to deal with problems, enhancing reliability
- The burden is justified if the exceptional condition cannot be prevented by proper use of the API _and_ the programmer using the API can take some useful action once confronted with the exception
- The easiest way to eliminate a checked exception is to return an optional of the desired result type
- When used sparingly, checked exceptions can increase the reliability of programs; when overused, they make APIs painful to use

#### 72. Favor the use of standard exceptions

- The Java libraries provide a set of exceptions that covers most APIs
- Benefits of reusing standard exceptions:
  1) Makes API easier to learn because it matches established conventions
  2) Programs using your API are easier to read because they're not cluttered with unfamiliar exceptions
  3) Smaller memory footprint
- Do _not_ reuse Exception, RuntimeException, Throwable, or Error directly
- Common Exceptions Use Cases:

  | Exception | Occasion for use |
  | --- | --- |
  | IllegalArgumentException | Non-null parameter value is inappropriate |
  | IllegalStateException | Object state is inappropriate for method invocation |
  | NullPointerException | Parameter value is null where prohibited |
  | IndexOutOfBoundsException | Index parameter value is out of range |
  | ConcurrentModificationException | Concurrent modification of an object has been detected where prohibited |
  | UnsupportedOperationException | Object does not support method |
- Throw IllegalStateException if no argument values would have worked, otherwise throw IllegalArgumentException

#### 73. Throw exceptions appropriate to the abstraction

- Higher layers should catch lower-level exceptions and, in their place, throw exceptions that can be explained in terms of the higher-level abstraction; this is known as _Exception translation_
- _Exception chaining_ is called for when the lower-level exception might be helpful to someone debugging the problem that caused the higher-level exception; lower-level exception passed to high-level one as "cause"
- While exception translation is superior to mindless propagation of exceptions from lower layers, it should not be overused

#### 74. Document all exceptions thrown by each method

- Always declare checked exceptions individually, and document precisely the conditions under which each one is thrown
- Familiarizing programmers with all of the errors they can make helps them avoid making these errors
- The unchecked exceptions of an interface form part of the interface's _general contract_ and enables common behavior among multiple implementations
- Use the Javadoc @throws tag to document each exception that a method can throw, but do NOT use the 'throws' keyword on unchecked exceptions; this helps programmers separate the two
- If an exception is thrown by many methods in a class for the same reason, you can document the exception in the class's documentation comment (e.g. NullPointerException)

#### 75. Include failure-capture information in detail messages

- To capture a failure, the detail message of an exception should contain the values of all parameters and fields that contributed to the exception
- Do not include passwords, encryption keys, and the like in detail messages
- Consider using a constructor for the exception which provides or requires the values involved in the failure for automatically generated detail messages

#### 76. Strive for failure atomicity

- Generally speaking, a failed method invocation should leave the object in the state that it was in prior to the invocation
  - This is known as _failure atomicity_
- If an object is immutable, failure atomicity is free
- For methods with mutable objects, check validity of parameters before making any changes
- Another approach: perform the operation on a temporary copy of the object and replace the contents once the operation is complete
- Far less common: write "recovery code" to intercept failures and roll back (mostly for durable data structures)
- If this rule is violated, the API documentation should clearly indicate what state the object will be left in

#### 77. Don't ignore exceptions

- An empty catch block defeats the purpose of exceptions
- If you choose to ignore an exception, the catch block should contain a comment explaining why it is appropriate to do so, and the variable should be named 'ignored'.

# 11. Concurrency

#### 78. Synchronize access to shared mutable data

- The synchronized keyword ensures only a single thread can execute a method or block at a time
- Synchronization is required for reliable communication between threads as well as for mutual exclusion
- Do not use Thread.stop()
- More common pattern: have thread set a boolean field to true, with other (stopper) thread polling the boolean
- Beware of _hoisting_ and the resulting liveness failures (while loop changed to if through compiler optimizations)
- Synchronization is not guaranteed to work unless _both_ read and write operations are synchronized
- Declaring a field as 'volatile' is a strategy that performs no mutual exclusion, but guarantees that any thread that reads the field will see the most recently written value
- Try using data types like AtomicLong from java.util.concurrent.atomic
- Confine mutable data to a single thread
- When multiple threads share mutable data, each thread that reads or writes the data must perform synchronization

#### 79. Avoid excessive synchronization

- Depending on the situation, excessive synchronization can cause reduced performance, deadlock, or even nondeterministic behavior
- To avoid liveness and safety failures, never cede control to the client within a synchronized method or block, including "alien" methods
- Multi-catch clauses for exceptions can greatly increase the clarity and reduce the size of programs that behave identically in response to different exception types
- Java uses _reentrant_ locks; they can simplify the construction of multithreaded object-oriented programs, but they can turn liveness failures into safety failures
- Try concurrent collections like CopyOnWriteArrayList
- An alien method invoked outside of a synchronized region is known as an _open call_; besides preventing failures, open calls can greatly increase concurrency
- As a rule, you should do as little work as possible inside synchronized regions
- In a multicore world, the real cost of excessive synchronization is not the CPU time spent getting locks; it is contention: the lost opportunities for parallelism and the delays imposed by the need to ensure that every core has a consistent view of memory
- If you're writing a mutable class, you have two options: you can omit all synchronization and allow the client to synchronize externally if concurrent use is desired, or you can synchronize internally, making the class _thread safe_
- You should choose #2 only if you can achieve significantly higher concurrency with internal synchronization versus external
- If a method modifies a static field and there is any possibility that the method will be called from multiple threads, you _must_ synchronize access to the field internally because it is not possible for the client to do this correctly externally
- Document your decision to make your class thread-safe or not clearly

#### 80. Prefer executors, tasks, and streams to threads

- An _Executor Framework_ is a flexible interface-based task execution facility
- If you want more than one thread to process requests, simply call a different kind of executor service called a _thread pool_
- For a small program or lightly loaded server, Executors.newCachedThreadPool is generally a good choice because it demands no configuration and generally "does the right thing"
- But, a cached thread pool is not a good choice for a heavily loaded production server! (try using Executors.newFixedThreadPool instead)
- Avoid working directly with Threads because the unit of work and mechanism are the same (Thread); executor framework separates the two allowing for policy changes
- _Task_ is the abstraction for a unit of work: Runnable and Callable versions (Callable is like Runnable but returns a value & can throw exceptions)
- Parallel streams are written atop fork-join pools, allowing for the parallelism for little effort, assuming the task is appropriate

#### 81. Prefer concurrency utilities to wait and notify

- Given the difficulty of using wait and notify correctly, you should use higher-level concurrency utilities instead
- The concurrent collections are high-performance concurrent implementations of standard collection interface (List, Queue, Map, etc.)
- It is impossible to exclude concurrent activity from a concurrent collection; locking it will only slow the program
- _Canonicalization_ - process of converting data that involves more than representation into a standard approved format
- Use ConcurrentHashMap in preference to Collections.SynchronizedMap
- BlockingQueue can be used as a work queue
- Synchronizers are objects that enable threads to wait for one another, allowing them to coordinate their activities
  - E.g. CountDownLatch, Semaphore, Phaser, etc.
- Countdown latches are single-use barriers that allow one or more threads to wait for 1+ threads to do something
- For internal timing, always use System.nanoTime rather than System.currentTimeMillis
- The wait method is used to make a thread wait for some condition, and must be invoked inside a synchronized region that locks the object on which it is invoked
- Always use the wait loop idiom to invoke the wait method; never invoke it outside of a loop
- The advice to use notifyall rather than notify is reasonable and conservative
- There is seldom, if ever, a reason to use wait and notify in new code

#### 82. Document thread safety

- The presence of the synchronized modifier in a method declaration is an implementation detail, not part of its API; it does not reliably indicate if a method is thread-safe
- To enable safe concurrent use, a class must clearly document what level of thread safety it supports:
  - Immutable: No external synchronization necessary (String, Long, BigInteger)
  - Unconditionally thread-safe: No need for any external synchronization even though it's mutable (AtomicLong, ConcurrentHashMap)
  - Conditionally thread-safe: Some methods require external synchronization (Collections.synchronized collections)
  - Not thread-safe: Instances are mutable, clients must surround each method invocation with external synchronization (ArrayList, HashMap)
  - Thread-hostile: Unsafe for concurrent use even if every method invocation is surrounded by external synchronization. Usually from modifying static data without synchronization.
    - Such classes are typically fixed or deprecated
- Publicly accessible locks are vulnerable to Denial of Service (DoS) attacks because a client can intentionally or unintentionally hold the lock for a prolonged period
- To prevent this, use a private lock object instead of using synchronized methods
- Lock fields should _always_ be final

#### 83. Use lazy initialization judiciously

- Under most circumstances, normal initialization is preferable to lazy initialization
- If you use lazy initialization to break an initialization circularity, use a synchronized accessor
- If you need to use lazy initialization for performance on a static field, use the _lazy initialization holder class_ idiom
- If you need to use lazy initialization for performance on an instance field, use the _double-check_ idiom

#### 84. Don't depend on the thread scheduler

- Any program that relies on the thread scheduler for correctness or performance is likely to be nonportable
- The best way to write a robust, responsive, portable program is to ensure the number of runnable threads is not significantly greater than the number of processors
- Threads should not run if they aren't doing useful work
- Size thread pools appropriately and keep tasks short, but not _too_ short
- Resist the temptation to "fix" a program by putting in calls to Thread.yield
- Thread.yield has no testable semantics
- Thread priorities are among the least portable features of Java, and should only be used to tweak performance on a system, not to "fix" a program that has liveness issues


# 12. Serialization

#### 85. Prefer alternatives to Java serialization

- Object Serialization is Java's framework for encoding objects as byte streams (_serializing_) and reconstructing objects from their encodings (_deserializing_)
- A fundamental problem with serialization: its _attack surface_ is too big to protect and is constantly growing
- Deserialization of untrusted streams can result in Remote Code Execution (RCE), Denial of Service (DoS) and a range of other exploits
- Attackers can create gadget chains powerful enough to execute arbitrary code
- Deserialization bombs can cause DoS
- The best way to avoid serialization exploits is never to deserialize anything
- There is no reason to use Java serialization in any new system you write
- The leading cross-platform structured data representations are JSON and Protocol Buffers (a.k.a _protobuf_)
  - JSON is text-based and human readable
  - protobuf is binary and substantially more efficient
- Never deserialize untrusted data
- Prefer whitelisting to blacklisting when deciding what to deserialize, since blacklists only work against known threats

#### 86. Implement Serializable with great caution

- A major cost of implementing Serializable is that it decreases the flexibility to change a class's implementation once it has been released
- When a class implements Serializable, its byte-stream encoding becomes part of its exported API
- If you accept the default serialized form and later change a class's internal representation, an incompatible change in the serialized form will result
- A second cost of implementing Serializable is that it increases the likelihood of bugs and security holes
- A third cost of implementing Serializable is that it increases the testing burden associated with releasing a new version of the class
- Implementing Serializable is not a decision to be undertaken lightly
- Classes designed for inheritance should rarely implement Serializable, and interfaces should rarely extend it
- Extendable and serializable classes should beware of _finalizer attacks_ from subclasses
- Inner classes should not implement Serializable

#### 87. Consider using a custom serialized form

- When you are writing a class under time pressure, it is generally appropriate to concentrate your efforts on designing the best API; this might mean releasing a "throwaway" implementation to be replaced later, but accepting the default serialized form can mean you're stuck with an implementation forever
- Do not accept the default serialized form without first considering whether it is appropriate
- The default serialized form is likely to be appropriate if an object's physical representation is identical to its logical content (e.g. FullName class)
- Even if you decide that the default serialized form is appropriate, you often must provide a readObject method to ensure invariants and security
- Using the default serialized form when an object's physical representation differs substantially from the logical data content (e.g. StringList class) has four disadvantages:
  1. It permanently ties the exported API to the current internal representation
  2. It can consume excessive space
  3. It can consume excessive time
  4. It can cause stack overflows
- The 'transient' modifier indicates that an instance field is able to be omitted from a class's default serialized form
- Every instance field that can be declared transient should be
- Before deciding to make a field nontransient, convince yourself that its value is part of the logical state of the object
- You must impose any synchronization on object serialization that you would impose on any other method that reads the entire state of the object
- Regardless of what serialized form you choose, declare an explicit serial version UID in every serializable class you write
- Do not change the serial version UID unless you want to break compatibility with all existing serialized instances of a class

#### 88. Write readObject methods defensively

- The readObject method is effectively another public constructor, and demands all the same care
- readObject must also check its parameters for validity and make defensive copies of parameters where appropriate
- When an object is deserialized, it is critical to defensively copy any field containing an object reference that a client must not possess
- Every serializable immutable class containing private mutable components must defensively copy these components in its readObject method
- Like a constructor, a readObject method must not invoke an overridable method, directly or indirectly
- Check any invariants and throw an InvalidObjectException if a check fails; these checks should follow any defensive copying

#### 89. For instance control, prefer enum types to readResolve

- The readResolve feature allows you to substitute another instance for the one created by readObject
- readResolve can be used to maintain the singleton property for a class
- If you depend on readResolve for instance control, all instance fields with object reference types _must_ be declared transient; otherwise it is possible for a determined attacker to secure a reference to the deserialized object before its readResolve method is run
- The accessibility of readResolve is significant (private, package-private, etc)
- Use enum types to enforce instance control invariants wherever possible

#### 90. Consider serialization proxies instead of serialized instances

- The _Serialization Proxy_ pattern greatly reduces the risks from traditional serialization
- First, design a private static nested class that concisely represents the logical state of an instance of the enclosing class
  - This is known as the _Serialization Proxy_ of the enclosing class; it should have a single constructor whose parameter type is that of the enclosing class
  - The constructor merely copies the data from its argument, it need not do any consistency checking or defensive copying
- Both the enclosing class and its serialization proxy must be declared to implement Serializable
- Next, add a writeReplace method on the enclosing class that creates a SerializationProxy instance with the aforementioned constructor
- Add a readObject method to the enclosing class that throws an InvalidObjectException with a message saying the Proxy is required
- Finally, provide a readResolve method on the SerializationProxy class to return a logically equivalent instance of the enclosing class
- This readResolve method creates an instance of the enclosing class using only its public API and therein lies the beauty of the pattern; it largely eliminates the extralinguistic character of serialization, freeing you from having to separately ensure that deserialized instances obey the class's invariants
- The serialization proxy pattern allows the deserialized instance to have a different class than the originally serialized instance (think RegularEnumSet vs JumboEnumSet)
- Two limitations of the serialization proxy pattern:
  1. It is not compatible with classes that are extendable by their users
  2. It is not compatible with some classes whose object graphs contain circularities
- The added power and safety of the serialization proxy pattern are not free (~14% more expensive)

