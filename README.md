# effective-java-notes
A collection of the major points made in the book Effective Java by Joshua Bloch (Third Edition). 

Disclaimer: All content is taken from the book and should credited to Joshua Bloch.

# TODO
- All details for items
- Tables
- Special formatting for code, method names, etc
- Table of contents
- Spell check

# 2. Creating and Destroying Objects

#### 1. Consider static factory methods

Pros:
- Named, unlike constructors
- Not required to return a -new- object, which allows for instance controlling
- Can return an object of any subtype of their return type
- Class of returned object can vary from call to call as a function of the input parameters
- Class of returned object need not exist when the class containing the method is written, which is the basis of Service Provider Frameworks

Cons:
- No public/protected constructor means no subclassing
- Hard for programmers to find

Common names:
- from() - type conversion
- of() - aggregation
- valueOf() - more verbose
- instance() or getInstance() - returns instance, but not the same value
- create() or newInstance() - call returns new instance
- getType() or newType() - if in different class
- type() - more concise

#### 2. Consider a builder when faced with many constructor parameters

- Telescoping constructors pattern hard to read, doesn't scale well
- JavaBeans pattern allows inconsistency, mandates mutability
- Builder simulates named parameters from languages like Python
- Well-suited to class hierarchies
- Downside: have to create Builder objects

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

- Finalizers are unpredictable, often dangerous, and generally unneccessary
- Cleaners are less dangerous than finalizers, but are still unpredictable and generally unneccessary
- Never do anything time-critical in a finalizer or cleaner
- Never depend on a finalizer to update critical persistent state
- There is a -severe- performance penalty for using finalizers
- Instead, provide an explicit termination method (e.g. file.close())
- Explicit termination methods are typically used in combination with the try-finally construct to ensure termination
- Two valid uses of finalizers and cleaners:
  - Can be used as a fail-safe if explicit termination method is forgotten by programmer
  - Finalizer should log warning if it finds that the resource has not been terminated
  - Objects with native peers
  - Remember super.finalize()
  - Finalizer Guardian with public nonfinal class
- In sum, don't use either of these except as a safety net or to terminate noncritical native resources. Even then, beware indeterminancy and performance hits

#### 9. Always use try-with-resources in preference to try-finally when working with resources that must be closed

- Code shorter and cleaner, and better exceptions provided to programmer


# 3. Methods Common to All Objects

#### 10. Obey the general contract when overriding equals

- Don't override if:
  - each instance is inherently unique
  - no need for logical equality test
  - superclass overrode equals and still applies
  - certain that equals will never be invoked
- Once you've violated the equals contract, you don't how how other objects will behave when confronted with yours
- There is no way to extend an instantiable class and add a value component while preserving the equals contract
- Workaround: favor composition over inheritance (e.g. give ColorPoints a private Point field and a public view method: asPoint())
- But: can add a value component to a subclass of an abstract class without violating equals contract
- Do NOT write an equals method that relies on unreliable resources (e.g. network access)
- Recipe for a high-quality equals method:
  1. Use == operator to check if argument is reference to this object
  2. Use instanceof to check if argument has correct type
  3. Cast argument to correct type (since it's an Object to start)
  4. For each "significant" field in the class, check if the field in the argument matches the corresponding one in the object. Compare fields most likely to differ first, or the ones that are less expensive to compare
  5. When done writing, check 1) symmetry, 2) transitive, 3) consistent
- Always override hashcode when you override equals. Don't be too clever
- Make sure param is Object type

#### 11. Always override hashcode when you override equals

- Equal objects must have equal hashcodes
- Do not be tempted to exclude significant fields from the hashcode computation to improve performance
- Don't provide a detailed specification of the value returned by hashcode, so clients can't depend on it and you can change it

#### 12. Always override toString

- Makes your class more pleasant to use and makes systems using the class easier to debug
- When practical, toString should return _all_ the interesting info contained in the object
- Whether or not you decide to specify a format (and corresponding static factory for converting back) you should clearly document your intentions
- Provide programmatic access to the info contained in the value returned by toString (e.g. getters)

#### 13. Override clone judiciously

- Cloneable interface means protected clone method on Object returns field-by-field copy of the object
- A class implementing Cloneable is expected to provide a properly functioning, public clone method
- By convention, object should be obtained by calling super.clone(), not constructor
- Immutable classes should never provide a clone method (wasteful copying)
- Must ensure that the new object does no harm to the original object and properly establishes invariants on the clone
- The Cloneable architecture is incompatible with normal use of final fields referring to mutable objects
- Try a deepCopy method for objects with complex mutable state
- A clone method must never invoke an overrideable method on the clone under construction
- Public clone methods should omit the throws clause
- A better approach to object copying is to provide a "copy constructor" or "copy factory" because 1) they don't conflict with proper use of final fields and 2) don't throw unnecessary checked exceptions, etc
- New interfaces should not extend Cloneable
- Arrays are better with clone, everything else is better with copy constructors or factories

#### 14. Consider implementing Comparable

- Should generally agree with equals
- Use of < and > in compareTo methods is verbose, error-prone, and not reccommended
- Start with the most significant fields
- Do not use difference-based comparators

# 4. Classes and Interfaces

#### 15. Minimize the accessability of classes and members

- Cleanly separate API from implementation (encapsulation)
- Make each class or member as inaccessible as possible
- Accessibility levels:
  - private - accessible only from top-level class where it's declared
  - package-private: accessible from any class in the package where it's declared
  - protected: accessible from subclasses of the class and from any class in the package where it's declared
  - public: accessible from anywhere
- Instance fields of public classes should rarely be public; classes with public mutable fields are not generally thread-safe
- It is _wrong_ for a class to have a public static final array field or an accessor that returns such a field

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
- You should never provide a clone method or a copy constructor for an immutable class
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
- Composition also known as the "Decorator" pattern
- Disadvantage of wrapper classes:
  - Not suited for use in callback frameworks; callback elude wrapper (SELF problem)
  - Tedious to write forwarding methods
- If you use inheritance where composition is preferable, you needlessly expose implementation details

#### 19. Design and document for inheritance or else prohibit it

- The class must document its self-use of overrideable methods
- A class may have to provide hooks into its internal workings in the form of judiciously chosen protected methods
- The _only_ way to test a class designed for inheritance is to write subclasses (~3 sufficient)
- You must test your class by writing subclasses _before_ you release it
- Constructors must not invoke overrideable methods (leads to program failures)
- Neither clone nor readObject may invoke an overrideable method directly or indirectly
- The best solution is to prohibit subclassing in classes that are not designed and documented to be safely subclassed

#### 20. Prefer interfaces to abstract classes

- Existing classes can easily be retrofitted to implement a new interface
- Interfaces are ideal for defining mixins
  - mixin: type that a class can implement in addition to its "primary type" to declare that it provides some optional behavior (e.g. Comparable)
- Interfaces allow for the construction of nonhierarchical type frameworks
- Interfaces enable safe, powerful functionality enhancements via the wrapper class idiom
- Can provide a "skeletal implementation class" to go with an interface
  - The interface defines the type, and the skeletal implementation class implements the remaining non-private interface methods atop the primitive interface methods ("Template Method" pattern)
  - Called "Abstract{InterfaceName}" by convention
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
- Common use: public helper class (e.g. Operation enum with calculator class)
- Common use of non-static version: define an "Adapter" that allows an instance of the outer class to be viewed as an instance of some unrelated class
- If you declare a member class that does not require access to an enclosing instance, _always_ make it static
- Anonymous class: has no name, not a member of its enclosing class.
  - They're permitted at any point an expression is legal
  - Can't instantiate them except at point they're declared
  - Can't use instanceof
  - Can't implement multiple interfaces
  - Must be kept short

#### 25. Limit source files to a single top-level class

- Multiple top-level classes in same file means it's possible to provide multiple definitions
- Can change behavior based on the order files are passed to the compiler (!)
- Never put multiple top-level classes or interfaces in a single source file

# 5. Generics

#### 26. Don't use raw types
#### 27. Eliminate unchecked warnings
#### 28. Prefer lists to arrays
#### 29. Favor generic types
#### 30. Favor generic methods
#### 31. Use bounded wildcards to increase API flexibility
#### 32. Combine generics and varargs judiciously
#### 33. Consider typesafe heterogeneous containers

# 6. Enums and Annotations

#### 34. Use enums instead of int constants
#### 35. Use instance fields instead of ordinals
#### 36. Use EnumSet instead of bit fields
#### 37. Use EnumMap instead of ordinal indexing
#### 38. Emulate extensible enums with interfaces
#### 39. Prefer annotations to naming patterns
#### 40. Consistently use the @Override annotation
#### 41. Use marker interfaces to define types

# 7. Lambdas and Streams

#### 42. Prefer lambdas to anonymous classes
#### 43. Prefer method references to lambdas
#### 44. Favor the use of standard functional interfaces
#### 45. Use streams judiciously
#### 46. Prefer side-effect-free functions in streams
#### 47. Prefer Collection to Stream as a return type
#### 48. Use caution when making streams parallel

# 8. Methods

#### 49. Check parameters for validity
#### 50. Make defensive copies when needed
#### 51. Design method signatures carefully
#### 52. Use overloading judiciously
#### 53. Use varargs judiciously
#### 54. Return empty collections or arrays, not nulls
#### 55. Return optionals judiciously
#### 56. Write doc comments for all exposed API elements

# 9. General Programming

#### 57. Minimize the scope of local variables
#### 58. Prefer for-each loops to traditional for-loops
#### 59. Know and use the libraries
#### 60. Avoid float and double if exact answers are required
#### 61. Prefer primitive types to boxed primitives
#### 62. Avoid strings where other types are more appropriate
#### 63. Beware the performance of string concatenation
#### 64. Refer to objects by their interfaces
#### 65. Prefer interfaces to reflection
#### 66. Use native methods judiciously
#### 67. Optimize judiciosly
#### 68. Adhere to generally accepted naming conventions

# 10. Exceptions

#### 69. Use exceptions only for exceptional conditions
#### 70. Use checked exceptions for recoverable conditions and runtime exceptions for programming errors
#### 71. Avoid unneccessary use of checked exceptions
#### 72. Favor the use of standard exceptions
#### 73. Throw exceptions appropriate to the abstraction
#### 74. Document all exceptions thrown by each method
#### 75. Include failure-capture information in detail messages
#### 76. Strive for failure atomicity
#### 77. Don't ignore exceptions

# 11. Concurrency

#### 78. Synchronize access to shared mutable data
#### 79. Avoid excessive synchronization
#### 80. Prefer executors, tasks, and streams to threads
#### 81. Prefer concurrency utilities to wait and notify
#### 82. Document thread safety
#### 83. Use lazy initialization judiciously
#### 84. Don't depend on the thread scheduler

# 12. Serialization

#### 85. Prefer alternatives to Java serialization
#### 86. Implement Serializable with great caution
#### 87. Consider using a custom serialized form
#### 88. Write readObject methods defensively
#### 89. For instance control, prefer enum types to readResolve
#### 90. Consider serialization proxies instead of serialized instances
