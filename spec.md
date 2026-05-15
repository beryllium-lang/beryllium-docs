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
- ref&lt;T, [optional lifetime name]&gt; (reference, similar to T&, has to be seated at initialization, lifetime checked)
- res&lt;T1, T2&gt; (can store a T1, can store an error value T2)
- tup&lt;T1, T2, T3, ...&gt; (can store varying types, can be duplicates)
- opt&lt;T&gt; (optional type)
- variant&lt;T1, T2, T3, ...&gt; (tagged union)
- slice&lt;T, [optional lifetime name]&gt; (a fat pointer with a pointer and length, lifetime checked)
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

# Variable Declaring

var varName: [optional hint, deduces automatically] = [value];
var varName: [type];

No initialization requires you to specify the type.
Mutability is part of the type. By default, variables are immutable, for example:

```
func main() {
    var i = 9;
    i = 8; // compile error
    var m: mut = 9;
    var j: mut int = 9;
    j = 10; // success
    var b = true;
    return 0;
}
```

You can use `ref` to make a reference and infer the type of the reference alias, `mut` to make a mutable value, and `mut ref` to make a mutable alias and determine the type to be aliased. `mut` and `mut ref` can also take an explicit type. All global variables cannot be mutable.

# Loops

while loops (run until the condition is false)

```
while (condition) {
    doSomething();
}
```

for loop (runs until cond is false)

```
for (init; cond; increment) {
    doSomething();
}
```

do while loops (runs at least once)

```
do {
    doSomething();
} while (condition);
```

for each loop (loops through each of the elements in a container)

```
for (var el : container) {
    doSomething();
}
```

## Special

repeat loops (repeats x times)

```
repeat (x) { // x is how many times to repeat
    doSomething();
}
```

# Control Script / Useful Functions

Wait until conditional blocks.

```
wait(condition);
```

stops the current loop or script until a certain condition is met.

quit script, exits the program

```
exit([exit code]);
```

if statement

```
if (cond) {
    doSomething();
}
```

functions

```
func impure doSomething(arg1: type1, arg2: type2): [optional return type, is inferred] {
    std.out.println("Doing something");
    return "done"; // no need to return something, can return nothing. Empty return is allowed.
}
```

# IO

Using the standard library, you can use print() to print a string to the console:

```
import std.io;

func main() {
    var x: string = "Hello, world!\n";
    std.out.print(x);
    return 0;
}
```

If you need a newline, add a \n at the end or use println.
You can do this to read from the console:

```
import std.io;

func main() {
    var str: mut string;
    std.out.print("Enter a sentence: ");
    std.in.read(str);
    std.out.println("You entered: " + str);
    return 0;
}
```

We will cover more of the standard library in its own docs. This is not part of the standard, but it is used heavily in examples.

# Namespaces

You use namespaces like this:

```
namespace nsp {
    func name() {
        // do something
    }
}

func main() {
    nsp.name();
    return 0;
}
```

You access symbols inside of it with nsp.[member]. They can be disjoint. This is how the Beryllium Standard Library works.

# System Settings

To figure out what the computer specs are, there are built-in constants. The constant `OS` has the following values: "windows" for Windows, "linux" for Linux, "macOS" for macOS,... The constant `ARCH` has the following values: "x86" for x86, "x86-64" for x86-64, "ARM" for ARM, "ARM64" for ARM64,...

# Casting

Casting is important. Implicit casts are done automatically. Explicit casts are explicit. The rules are:
- int to float is implicit
- You can convert between smaller to larger int types implicitly
- char conversions must be explicit to int and float types
- float to int is explicit
- smaller float to larger float is implicit
- bool to any has to be explicit
- Anything to bool has to explicit except in `if`, `while`, and `for` contexts

To cast explicitly, you use `as T`.
There are variations, so here is an example:

```
func main() {
    var x: int = 9;
    var y: byte = x; // implicit conversion NOT allowed because byte is smaller than int
    var z: char = y; // not allowed, because chars are not ints (they are integral, but semantically not int)
    var cursed_char = x as char; // explicit, allowed
    var dec: double = x;
    var pi = 3.14;
    var pi_int: int = pi; // not allowed because float types to int types has to be explicit
    var pi_int_legal: int = pi as int; // allowed
    var reinterpet = pi as ulong unsafe; // unsafe makes it a bit reinterpretation, no value conversion ever
    var def_illegal = pi as dynarr<string>; 

    return 0;
```

# Pointers and References

Pointers are useful. They can be used to pass around data without copying. There are four different types of pointers:

```
ptr
rcptr
arcptr
weakptr
unsafeptr
```

The outside `mut` makes the pointer itself mutable. The inside of the angle brackets has a `mut` too. That makes the value pointed to mutable. In fact, the pointer types are the only types that allow inside `mut`. References just have the outer `mut` declare the mutability of the value. You can use `valat()` to get the value a pointer points to and `ptrto()` to get a pointer to an address. Now for the specific types:`ptr`:
This one has unique ownership. You cannot copy it, and for classes, you must instantiate.
`rcptr`:
You can copy this one. It does reference counting, and if it goes to zero, it then frees the memory.
`arcptr`:
Similar to `rcptr` but reference counts atomically
`weakptr`:
This is a non-owning reference to memory. It does not change the reference count.
`unsafeptr`:
This is the unsafe C-style pointer. It does not have checks or deletion checks.
You can freely implicitly convert between `adr` and `unsafeptr`. However, there is no value conversion between them and other pointer types.

References are like pointers, but have to be seated and cannot move. You also don't have to explicitly dereference. Here is an example:

```
func main() {
    var x = 420;
    var y: ref<int> = x;
    var z: mut = 420;
    var w: ref = z; // immutable reference
    var mut_ref: mut ref = z; // mutable reference
    mut_ref = 69; // z is now 69
    var illegal_ref: mut ref = x; // illegal because x is immutable
    return 0;
}
```

The value `nil` has many uses, but in this context it represents a null pointer to a guaranteed invalid location in memory. Pointers convert to false in a conditional if it is `nil`, and true otherwise. References have a special coercion rule in function parameters. If they are immutable, you don't need to mark anything. If the reference parameter is mutable, you have to explicitly mark the object being passed in with an ampersand.
 
