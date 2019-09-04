# effective-java-notes
A collection of the major points made in the book Effective Java by Joshua Bloch (Third Edition). 

Disclaimer: All content is taken from the book and should credited to Joshua Bloch.

# TODO
- All details for items
- Tables
- Special formatting for code, method names, etc
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
#### 11. Always override hashcode when you override equals
#### 12. Always override toString
#### 13. Override clone judiciously
#### 14. Consider implementing Comparable

# 4. Classes and Interfaces

#### 15. Minimize the accessability of classes and members
#### 16. In public classes, use accessor methods, not public fields
#### 17. Minimize mutability
#### 18. Favor composition over inheritance
#### 19. Design and document for inheritance or else prohibit it
#### 20. Prefer interfaces to abstract classes
#### 21. Design interfaces for posterity
#### 22. Use interfaces only to define types
#### 23. Prefer class hierarchies to tagged classes
#### 24. Favor static member classes over nonstatic
#### 25. Limit source files to a single top-level class

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
