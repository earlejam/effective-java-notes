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
#### 2. Consider a builder when faced with many constructor parameters
#### 3. Enforce the singleton property with a private constructor or an enum type
#### 4. Enforce noninstantiability with a private constructor
#### 5. Prefer dependency injection to hardwiring resources
#### 6. Avoid creating unnecessary objects
#### 7. Eliminate obsolete object references
#### 8. Avoid finalizers and cleansers
#### 9. Always use try-with-resources in preference to try-finally when working with resources that must be closed

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
