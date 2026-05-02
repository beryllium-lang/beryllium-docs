# Be1 specs

## Variable Types

### ExPrimitive Types

- bool (boolean)
- char (4 bytes, UTF-8)

### Integer Types

- byte (8-bit signed int)
- int (32-bit signed int)
- long (64-bit signed int)
- qint (128-bit signed int)
Add u to the beginning to make it unsigned.

### Float Types

- float (IEEE-754 single precision)
- double (IEEE-754 double precision)

### Containers

- string (a string of characters)
- bytestream (a stream of bytes)
- arr&lt;T, N&gt; (a static array of T, N has to be known at compile time)
- dynarr&lt;T&gt; (dynamic array of T, can be appended)
- adr (memory address, similar to void* in C/C++)
- ptr&lt;T&gt; (unique pointer, cannot be copied)
- rcptr&lt;T&gt; (reference counted pointer, frees when count goes to 0)
- arcptr&lt;T&gt; (rcptr but atomic referencing counting)
- weakptr&lt;T&gt; (non-owning reference counted)
- unsafeptr&lt;T&gt; (raw pointer, similar to T* )
- ref&lt;T&gt; (reference, similar to T&, has to be seated at initialization)
- res&lt;T1, T2&gt; (can store a T1, can store an error value T2)
- tup&lt;T1, T2, T3, ...&gt; (can store varying types, can be duplicates)
- opt&lt;T&gt; (optional type)
- variant&lt;T1, T2, T3, ...&gt; (tagged union)
- slice&lt;T&gt; (a fat pointer with a pointer and length)
- ref&lt;string&gt; (similar to slice&lt;byte&gt; but with string semantics)

## Type Aliases

Type aliases can be declared with the keyword `type` and an equals sign:

```
type MyInt = int;
var x: int = 9;
var y: MyInt = x;
```

Type aliases can be strong by using `strong`. That means there is no conversion anymore and it still inherits all the other type's properties and methods:

```
type strong Years = int;

var x: int = 9;
var y: Years = x; // compile error
```

Note that all types starting with a lowercase letter are reserved for the language.
