# `Be1 specs`

# `Variable Types`

**`ExPrimitive types:`**  
`bool (boolean)`  
`char (4 bytes, UTF-8)`

**`Integer types:`**  
`byte (8-bit signed int)`  
`int (32-bit signed int, default integer)`  
`long (64-bit signed int)`  
`qint (128-bit signed int)`  
`Add u at the beginning to make it unsigned.`

**`Float types:`**  
`float (IEEE-754 single precision)`  
`double (IEEE-754 double precision, default float)`

**`Containers:`**  
`string (a string of characters)`  
`bytestream (stream of bytes)`  
`arr<T, N> (static array of T, N has to be known at compile time)`  
`dynarr<T> (dynamic array of T, can be appended)`  
`adr (memory address, similar to void* in C/C++)`  
`ptr<T> (unique pointer, cannot be copied)`  
`rcptr<T> (reference-counted pointer, frees when count goes to zero)`  
`arcptr<T> (rcptr but atomic reference counting)`  
`weakptr<T> (non-owning reference counted)`  
`unsafeptr<T> (raw pointer, similar to T*)`  
`ref<T> (reference, similar to T&, has to be seated at initialization)`  
`res<T1, T2> (can store a T1, can store an error value T2)`  
`tup<T1, T2, T3, …> (can store varying types, can be duplicates)`  
`temp<T> (move type)`  
`opt<T> (optional type)`  
`variant<T1, T2, T3, …>`

# `Type Aliases`

`Type aliases can be declared with the keyword type and an equals sign:`

```
type MyInt = int;

var x: int = 9;
var y: MyInt = x;
```

`Type aliases can be strong by using strong. That means there is no conversion anymore and it still inherits all of the other type’s properties and methods:`

```
type strong Years = int;

var x: int = 9;
var y: Years = x; // compile error
```

# `Variable declaring`

`var varName: [optional hint, deduces automatically] = [value];`  
`var varName: [type];`

`No initialization requires you to specify the type.`  
`Mutability is part of the type. By default, variables are immutable, for example:`

```
func start(args: dynarr<string>) {
	var i = 9;
	i = 8; // compile error
	var m: mut = 9;
	var j: mut int = 9;
	j = 10; // success
	var b = true;
	exit 0;
}
```

`You can do ref to make a reference and infer the type of the reference aliases, mut to make a mutable value, and mut ref to make a mutable alias and determine the type to be aliased. mut and mut ref can also take an explicit type. All global variables cannot be mutable.`

`Source code is only guaranteed to compile if ASCII-only.`

# `Loops`

`while loops (run until the condition is false)`

```
while (condition) {
doSomething();
}
```

`for loops (runs until cond is false)`

```
for (init; cond; change) {
	doSomething();}
```

`do while loops (runs at least one)`

```
do {
	doSomething();} while (condition);
```

`for each loop (loops through each of the elements in a container)`

```
for (var el : container) {
	doSomething();
}
```

**`Special:`**  
`repeat loops (repeats x times)`

```
repeat (x) { // x is how many times to repeat
	doSomething();
}
```

# `Control Script/Useful functions`

`Wait until conditional blocks.`

```
wait(condition); 
```

`stops the current loop or script until a certain condition is met.`

`quit script, exits the program`

```
exit [exit code]; 
```

`if statement`

```
if (cond) {
doSomething();}
```

`functions`

```
func impure doSomething(arg1: type1, arg2: type2): [optional return type, is inferred] {	print("Doing something");
return "done"; // no need to return something, can return nothing. Empty return is allowed.}

func start(args: dynarr<string>) {
	doSomething(a1, a2);
	exit 0;
}
```

# `IO`

`Using the standard library, you can use print() to print a string to the console:`

```
import std.io;

func start(args: dynarr<string>) {
	var x: string = "Hello, world!\n";
	std.out.print(x);
	exit 0;
}
```

`If you need a newline, add a \n at the end of the string or use println.`  
`You can do this to read from the console:`

```
import std.io;

func start(args: dynarr<string>) {
	var str: mut = string;
	std.out.print("Enter a sentence: ");
	std.read(str);
	std.out.print("You entered: " + str);
	exit 0;
}
```

`We will cover more of the standard library in its own docs. This is not part of the standard, but it is used heavily in examples.`

# `Namespaces`

`You use namespaces like this:`

```
namespace nsp {	func name() {
		// do something
}}

func start(args: dynarr<string>) {
	nsp.name();	exit 0;
}
```

`You access symbols inside of it with nsp.[member]. They can be disjoint. This is how the Beryllium Standard Library works.`

# `System Settings`

`To figure out what the computer specs are, there are built-in constants. The constant OS has the following values: “windows” for Windows, “linux” for Linux, “macOS” for macOS, …. The constant ARCH has the following values: “x86” for x86, “x86-64” for x86-64, “ARM” for ARM, “ARM64” for ARM64,...`

# `Casting`

`Casting is important. Implicit casts are done automatically. Explicit casts are explicit. The rules are:`  
`int to float is implicit`  
`You can convert between smaller to larger int types implicitly`  
`char conversions must be explicit to int and float types`  
`float to int is explicit`  
`smaller float to larger float is implicit`  
`bool to any has to be explicit`  
`Anything to bool has to be explicit except in if, while, and for contexts`  
`To cast explicitly, you use as T.`  
`There are variations, so here is an example:`

```
func start(args: dynarr<string>) {
	var x: int = 9;
	var y: byte = x; // implicit conversion allowed because byte and int are both integer types
	var z: char = y; // not allowed, because chars are not ints (they are integral, but semantically not int)
	var cursed_char = x as char; // explicit, allowed
	var dec: double = x; // allowed because int types to float types is legal
	var pi = 3.14;
	var pi_int: int = pi; // not allowed because float types to int types has to be explicit
	var pi_int_legal: int = pi as int; // allowed
	var reinterpet = pi as ulong unsafe; // unsafe makes it a bit reinterpretation, no value conversion never
	var def_illegal = pi as dynarr<string>;

	exit 0;
}
```

# `Pointers and References`

`Pointers are useful. They can be used to pass around data without copying. There are four different types of pointers:`

```
ptr
rcptr
weakptr
unsafeptr
```

`The outside mut makes the pointer itself mutable. The inside of the angle brackets has a mut too. That makes the value pointed to mutable. In fact, the pointer types are the only types that allow inside mut. References just have the outer mut declare the mutability of the value. You can use valat() to get the value a pointer points to and ptrto() to get a pointer to an address. Now for the specific types:`  
`ptr:`  
`This one has unique ownership. You cannot copy it, and for classes, you must instantiate.`  
`rcptr:`  
`You can copy this one. It does reference counting, and if it goes to zero, it then frees the memory.`  
`weakptr:`  
`This is a non-owning reference to memory. It does not change the reference count.`  
`unsafeptr:`  
`This is the unsafe C-style pointer. It does not have checks or deletion checks.`  
`You can freely implicitly convert between adr and unsafeptr. However, there is no value conversion between them and the other pointer types.`

`References are like pointers, but have to be seated and cannot be moved. You also don’t have to explicitly dereference. Here is an example:`

```
func start(args: dynarr<string>) {
	var x = 420;
	var y: ref<int> = x; // y is an immutable reference to x
	var z: mut = 420;
	var w: ref = z; // immutable reference
	var mut_ref: mut ref = z; // mutable reference
	mut_ref = 69; // z is now 69
	var illegal_ref: mut ref = x; // illegal because x is immutable
	exit 0;
}
	
```

`The value nil has many uses, but in this context it represents a null pointer to a guaranteed invalid location in memory. Pointers convert to false in a conditional if it is nil, and true otherwise. References have special coercion rules as function parameters. If they are immutable, you don’t need to mark anything. If the reference parameter is mutable, you have to explicitly mark the object being passed in with an ampersand.`

# `Stack and Heap Allocation`

`This section only applies to adr and unsafeptr.` 

`The heap for this spec is defined as a long-lasting, dynamically sized chunk of memory. It contains much more memory than the stack. It also lasts much longer. It does not automatically delete upon leaving a scope.`

`By default, all variables are stack-allocated. Stack-allocated memory is freed upon leaving the scope, so it is not good for long-lasting memory. You can allocate memory on the heap with heapalloc[n]<T>(). n is an unsigned int. heapalloc returns an adr. To free the memory, use free(adr). You have to use array allocation. For singles, use [1] in the heapalloc. It returns a result<adr, string>:`

```
import std.io;

func start(args: dynarr<string>) {
	var x = heapalloc[3]<int>();
	if (x.is_valid()) {
		std.out.print("Successful allocation!");
		free(x.get());
	}
exit 0;
}

/*
output
Successful allocation!
*/
```

`If you need a dynamic stack-allocated array to avoid the compile-time constraint in arr<T, N>, use stackalloc[n]<T>. Because it is on the stack, you don’t delete it.`

# `Passing Functions`

`Functions can be passed around. To declare a function type, do:`

```
func return_type([param types])
```

`You can only pass in functions to a variable of that type with the same return types and parameter types. To call them, use normal function-calling syntax. This is if you want any function of that signature. Each function is its own type, and it automatically coerces to the generic type. In generics, however, the function does not coerce to the function pointer, but instead to the singular function type.`

# `Function Modifiers`

`By default, Beryllium assumes most functions are pure. That is, they only use from the outside world what is passed into them and the immutable global variables. If you want to say, print something to the console, that is an impure operation and you need to mark your function impure. start is implicitly impure. The impure keyword is put after the func but before the name. This is the same for any other modifiers.`

# `Bitwise Operations`

`Beryllium also supports bitwise operations. band does a bitwise and, bor does a bitwise or, bxor does a bitwise xor, bnot returns a bitwise not, and bxnot does a not and xor in that order. To do bit shifts, use << for left shift and >> for right shift. All of these only apply to integral types (that includes char, all the normal int types, and bool). For signed ints, left shift does not protect the sign bit, but right shift does.`

# `File Extension`

`.by - source files`

# `Static and Dynamic Arrays`

`Beryllium supports array types. They are arr and dynarr. arr is a statically sized array. The size must be known at compile time, and cannot be a runtime value. You use it like so:`

```
func start(args: dynarr<string>) {
	var arr_int3: arr<int, 3> = {0, 2, 4};
	var ARRAY_SIZE: int = 4;
	var arr_int_const_size: arr<int, ARRAY_SIZE> = {3, 2, 9, 11};
	exit 0;
}
```

`If you need a runtime array, either use stackalloc on an unsafeptr or use dynarr, as shown here:`

```
func start(args: dynarr<string>) {
	var dynamic_array: dynarr<int> = {1, 2, 3, 4};
	exit 0;
}
```

`To get the size of an array, use .size():`

```
import std.io;

func start(args: dynarr<string>) {
	var argc = args.size();
	std.out.println(f"You put in {argc - 1} command-line arguments");
	exit 0;
}
```

`You can access elements in an array with []:`

```
import std.io;

func start(args: dynarr<string>) {
	var argc = args.size();
	for (var i = 0; i < argc; ++i) {
		std.out.println(f"Argument {i + 1}: {argv[i]});
	}
	exit 0;
}
```

`It does not do bound checking. If you want to do that, use .at(). It returns a res<T, string>:`

```
import std.io;

func start(args: dynarr<string>) {
	var argc = args.size();
	for (var i = 0; i < argc; ++i) {
		var el = args.at(i);
		if (el.is_valid()) {
			std.out.println(f"Argument {i + 1}: {el.get()});
		} else {
			std.out.println(f"Invalid element at index {i}");
		}
	}
	exit 0;
}
```

`Both work with range-based for loops:`

```
import std.io;

func start(args: dynarr<string>) {
	for (var el : args) {
		std.out.println(f"Argument {el}");
	}
	exit 0;
}
```

`You can append elements to a dynarr using .append(), +=, or +. It can be individual elements, static arrays, or dynamic arrays, but it only applies to dynamic arrays. To remove the last element, use .pop(). It also returns the element that was popped:`

```
import std.out;

func start(args: dynarr<string>) {
	var arr: dynarr<int> = {1, 2, 3};
	for (var el : arr) {
		std.out.println(f"{el}");
	}
	var removed_el = arr.pop();
	std.out.println(f"Removed element: {removed_el}");
	for (var el : arr) {
		std.out.println(f"{el}");
	}
	exit 0;
}

/*
Prints:
1
2
3
Removed element: 3
1
2
*/
```

# `Logic`

`Operators:`

```
import std.io;

func start(args: dynarr<string>) {
// most C++ operators are allowed except for &, ~, |, and ^
var x = 5;
var y = 2;

var a = x + y;
var b = x - y;
var c = x * y;
var d: double = x as double / y; 
/* integer division is rounded down, hence the cast */
var e = x % y;
print(f"{a} \n {b} \n {c} \n {d} \n {e}");
exit 0;
}
/* output is 

7
3
10
2.5
1

*/

```

`Additional operators`

```
import std.io;

func start(args: dynarr<string>) {
	var x = "hello";
	var y = " world";
	var hello_world = x + y;
	var exc = '!';
	hello_world += exc;
	std.out.print(hello_world);
	exit 0;
}

/*
outputs "hello world!"
*/
```

# `Enumerations`

`Most programming languages have a way to declare enumerations. Beryllium provides them too. You can declare enumerations with enum:`

```
enum EnumExample {}
```

`You can list names in it and separate them with commas. Those are the values an enumeration can take. The compiler will change the size of the underlying type with the amount of elements the enumeration has. Unlike in C++, you cannot set the value of any value in an enumeration. To access a constant in an enumeration, you must qualify with the enumeration name. If the compiler knows the type of the variable is an enum, you can drop the name and just put a .:`

```
enum Color { RED, ORANGE, YELLOW, GREEN, BLUE, INDIGO, VIOLET }

func start(args: dynarr<string>) {
	var c: mut Color = .RED;
	c = .ORANGE;
	var c2 = .VIOLET; // does not work, have to do Color.VIOLET
	exit 0;
}
```

`You can mark an enumeration unsafe after the enum keyword to allow implicit conversions to integer types, but the constants are still scoped. The name dropping rule also applies to switch statements. Fallthrough is explicit with fallthrough. Otherwise, it automatically breaks:`

```
import std.io;

enum Color { RED, ORANGE, YELLOW, GREEN, BLUE, INDIGO, VIOLET }

func start(args: dynarr<string>) {
	var c: Color = .BLUE;
	switch(c) {
	case .RED:
		std.out.println("The color is red!");
	case .ORANGE:
		std.out.println("The color is orange!");
	case .YELLOW:
		std.out.println("The color is yellow!");
	case .GREEN:
		std.out.println("The color is green!");
	case .BLUE:
		std.out.println("The color is blue!");
	case .INDIGO:
		std.out.println("The color is indigo!");
	case .VIOLET:
		std.out.println("The color is violet!");
	default:
		std.out.println("Do not know color");
	}
	exit 0;
}

/*
Prints
The color is blue!
*/
```

`You can set a default case. If the enum is not exhaustive, it will return an error.`

# `Result, Optional Types, and Variants`

`Beryllium has all of these built-in, as well as methods to use them. res is either a no-error value type, or an error type (usually a string). This is the only form of error Beryllium provides; Java/C++-style exception stack backtrace does not exist. To get a value, use .get(). If you want to make sure that there is no error, use .is_valid(). To get the error out of it, use .err():`

```
import std.io;

func ret_if_positive(n: int): res<int, string> {
	if (n > 0) {
		return n;
	} else {
		return "Not positive";	}
}

func start(args: dynarr<string>) {
	var x = -2;
	var res = ret_if_positive(x);
	if (res.is_valid()) {
		std.out.println(f"The value is {res.get()}");
	} else {
		std.out.println(f"Could not get value: error {res.err()}");
	}
	exit 0;
}
```

`opt is very similar, except that there is no error type, so there is no .err() method.`  
`variant, however, cannot be accessed like that. To assign a variant, you can use = just fine. However, to read from it, you cannot use it directly. Instead, you use visit blocks. You can declare a type case, and each block is run. You can have comma-separated type cases to have multiple types go to a case. Uses of the variant in the visit statement automatically convert to the type in the block. They must be exhaustive:`

```
import std.io;

func visit(v: variant<int, double, bool>) {
	visit(v) {
	int {
		std.out.println(f"It is an int, value {v}!");
	} double {
		std.out.println(f"It is a double, value {v}!");
	} bool {
		std.out.println(f"It is a bool, value {v}!");
	}
	}
}

func start(args: dynarr<string>) {
	var v: variant<int, double, bool>;
	v = 8;
	visit(v);
	v = 3.14;
	visit(v);
	v = false;
	visit(v);
	exit 0;
}

/*
Prints:
It is an int, value 8!
It is a double, value 3.14!
It is a bool, value false!
*/
```

# `Unsafe Blocks`

`Unsafe blocks can be declared with the unsafe keyword:`

```` ``` ````  
`unsafe {}`  
```` ``` ````

`This allows you to run functions that are marked as unsafe.`

# `Overload Resolution`

`With function overloading, the compiler needs to know what function is being called, with implicit conversion. This is why there are overload resolution rules:`

1. `If a type matches exactly, it is prioritized`  
2. `If the parameter type is instead an immutable reference to the type, that is used`  
3. `If the value being passed in is a const ref, and the function parameter takes it by value, that is prioritized`  
4. `Then, there is a max of only one level of implicit conversion`  
5. `If a non-generic function and a generic have equal match for resolution, the non-generic function is preferred.`

# `Object-Oriented Programming`

`Beryllium also supports object-oriented programming. To declare a class, you use:`

```
class Name {}
```

`You can declare fields in it like so:`

```
class Dog {
	public var name: string;
	public var age: double;
}
```

`Fields marked public can be accessed outside the class. Fields not marked public can’t be accessed outside of the class. Methods marked with private cannot be accessed outside of the class. Methods marked public can:`

```
class Example {
	var f: double;
	var w: string;
	public var pInt: int;
	public var name: string;}

func start(args: dynarr<string>) {
	var ex: Example;
	ex.f = 3.14; // wrong
	ex.w = "hello"; // wrong
	ex.pInt = 5; // no error
	ex.name = "brain damage"; // no error
	exit 0;
}
```

`Member functions are declared as follows:`

```
import std.out;

class Dog {
	public var name: string;
	public var age: int;
	
	func toHuman(self: mut ref<Dog>) {
		return self.age * 7;
	}
	
	private func impure privFunc(self: mut ref<Dog>) {
		std.out.print("The age of the dog is an integer");
	}
	
	public func impure staticFunc() { // static functions have no mut ref<Class> as the first parameter
		std.out.print("Static function");
	}
}

func start(args: dynarr<string>) {
	var dog: Dog;
	dog.name = "Max";
	dog.age = 3;
	var humanAge = dog.toHuman();
	dog.privFunc(); // fails because the method is private
	Dog.staticFunc(); // not called on a dog instance
	exit 0;
}
```

`You don’t have to name the argument self, but it is what we will use in this documentation. To allow outside items to use private fields and methods, use friend:`

```
import std.io;

class Example {
	var f: double;
	var w: string;
	public var pInt: int;
	public var name: string;

	func impure print(self: ref<Example>) {
		std.out.println(self.w);
	}

	private func impure private_print(self: ref<Example>) {
		std.out.println(f"Number: {self.f}\nString: {self.w}");
	}

friend func impure violator(inst: mut ref<Example>);}

func impure violator(inst: mut ref<Example>) {
	inst.f = 3.14; // works
	inst.w = "private member changed"; // works
	inst.private_print(); // works
}

func start(args: dynarr<string>) {
	var ex: Example;
	ex.f = 3.14; // wrong
	ex.w = "hello"; // wrong
	ex.pInt = 5; // no error
	ex.name = "brain damage"; // no error
	ex.print(); // works
	ex.private_print(); // does not work
	violator(ex);
	exit 0;
}
```

`You cannot declare the start() function as a friend. You also cannot have cross-module friends.`  
`C++-style constructors don’t exist in Beryllium. Instead, you use a static function (which can be marked as a constructor with the keyword constructor), or directly construct one by listing the struct name:`

```
import std.io;

class Dog {
	public var name: string;
	public var age: int;
	
	constructor func new(name: ref<string>, age: int): Dog {
		return Dog {
name: name,
age: age
};
	}
}

func start(args: dynarr<string>) {
	var d(3, "Max"): Dog;
	std.out.print(f"{d.age}");
	std.out.print(d.name);
	exit 0;
}
```

`You can mark a constructor implicit to allow implicit conversions with it.` 

## `Operator overloading`

`Operator overloading is useful. You can do it with your own classes. They have to be defined in a class, though.`

```
import std.io;

class Vec3 {
	public var x: double;
	public var y: double;
	public var z: double;

	constructor func new(): Vec3 {
		return Vec3 {
			x: 0,
			y: 0,
			z: 0
		};
	}

	constructor func from(x: double, y: double, z: double): Vec3 {
		return Vec3 {
			x: x,
			y: y,
			z: z
		};
	}
	
	op(+) func unary_plus(a: Vec3) {
		return a;
	}
	op(-) func unary_minus(a: Vec3): Vec3 {
		return (-a.x, -a.y, -a.z);
	}
	op(+) func add(a: Vec3, b: Vec3): Vec3 {
		return (a.x + b.x, a.y + b.y, a.z + b.z);
	}
	op(-) func sub(a: Vec3, b: Vec3): Vec3 {
		return (a.x - b.x, a.y - b.y, a.z - b.z);
	}
	op(*) rev func mul(a: Vec3, b: double): Vec3 {
		return (a.x * b, a.y * b, a.z * b);
	} // rev creates another that just flips the operators and goes back to this one
	op(/) func div(a: Vec3, b: double): Vec3 {
		var inv_b = 1 / b;
		return (a.x * inv_b, a.y * inv_b, a.z * inv_b);
	}
	op(+=) func add(self: mut ref<Vec3>, other: Vec3): mut ref<Vec3> {
		self.x += other.x;
		self.y += other.y;
		self.z += other.z;
		return self;
	}
	op(-=) func sub(self: mut ref<Vec3>, other: Vec3): mut ref<Vec3> {
		self.x -= other.x;
		self.y -= other.y;
		self.z -= other.z;
		return self;
	}
	op(*=) func mul(self: mut ref<Vec3>, other: double): mut ref<Vec3> {
		self.x *= other;
		self.y *= other;
		self.z *= other;
		return self;
	}
	op(/=) func div(self: mut ref<Vec3>, other: double): mut ref<Vec3> {
		var inv_other = 1 / other;
		self.x *= inv_other;
		self.y *= inv_other;
		self.z *= inv_other;
		return self;
	}
	op(==) func equals(a: Vec3, b: Vec3) {
		return a.x == b.x && a.y == b.y && a.z == b.z;
	}
	op(!=) func not_equals(a: Vec3, b: Vec3) {
		return !(a == b);
	}
}

func impure print_vec3(v: Vec3) {
	print(f"{v.x} {v.y} {v.z}\n");
}

func start(args: dynarr<string>) {
	{
	var a(1, 2, 3): Vec3;
	var b(4, 5, 6): Vec3;
	var c = a + b;
	print_vec3(c);
	}
	{
		var a: Vec3;
		var b(1, 2, 3): Vec3;
		var c(0.5, 0.2, 1.2): Vec3;
		a += b -= c;
		print_vec3(a);
	}
	{
		var a(0.23, 0.46, 1.21): Vec3;
		var b = 3.14;
		print_vec3(a);
	}
	exit 0;
}

/*
outputs
5 7 9
0.5 1.8 1.8
0.7222 1.4444 3.7994
*/
```

`Conversion operators can be defined as follows:`

```
class IntWrapper {
	var raw: int;
	constructor func new(int num): IntWrapper {
		self.raw = num;
	}
	constructor func init(): IntWrapper {
		return IntWrapper {
			raw: 0
		}
	}
	op(as) func to_raw(self: ref<IntWrapper>) {
		return raw;
	}
}

func start(args: dynarr<string>) {
	var num(42): IntWrapper;
	var num_raw = num as int;
	var num_raw2 = num; // fails because conversion operator is not marked as implicit
	exit 0;
}
```

## `Destructors`

`Beryllium allows you to say what happens when an instance of a class is destroyed. It can be done by marking a method with no arguments other than the self parameter destructor:`

```
import std.out;

class DestructorExample {
	constructor func impure new(): Vec3 {
		std.out.print("Created object\n");
		return DestructorExample {};
	}
	destructor func impure destroy(self: mut ref<DestructorExample>) {
		std.out.print("Destroyed object\n");
	}
}

func start(args: dynarr<string>) {
	var d: DestructorExample;
}

/*
Prints:Created object
Destroyed object
*/
```

`There can only be one per class.`  
`You cannot overload &&, ||. They are short-circuited. However, the bool conversion operator will automatically convert in this context too.`

## `Special Constructors and Assignment Operators`

`The compiler generates these automatically if you don’t define them explicitly, but here are the signatures anyway, in case your class manages resources. The exact function names do not matter, but they are required:`

```
class SomeClass {
	constructor func new(): SomeClass {
		// default constructor
	}
	
	copier func clone(): SomeClass {
		// clones
	}

	/*
	copier = delete;
	*/ // that line removes copy entirely
	/*
	move = delete;
	*/ // that line removes move

	destructor func destroy(self: mut ref<SomeClass>) {
		// destructor
	}
}
```

`If you define a non-default constructor that is not any one of these, it will remove the default constructor. If you need to delete any one of these, make it private. The default constructor is called when you instantiate an object with no parameters. Move is always a memcpy. Same with assignments. If you define custom copy or destroy, things implicitly move instead of copy. To explicitly copy, use the copy() function.`

## `Traits`

`Unlike most object-oriented languages, Beryllium does not support traditional inheritance. Instead, it supports traits. To declare a trait, use:`

```
trait Trait {}
```

`To put methods in it, just drop the beginning self:`

```
trait Trait {
	func f1();
	func f2(int);
}
```

`Classes implicitly implement traits:`

```
import std.io;

trait CanSpeak {
	func speak();
}

class Dog {
	func impure speak(self: ref<Dog>) {
		std.out.println("Woof woof!");
	}
}

class Cat {
	func impure speak(self: ref<Cat>) {
		std.out.println("Meow!");
	}
}
```

`To make a generic trait object, do:`

```
import std.io;

trait CanSpeak {
	func speak();
}

class Dog(CanSpeak) {
	func impure speak(self: ref<Dog>) {
		std.out.println("Woof woof!");
	}
}

class Cat(CanSpeak) {
	func impure speak(self: ref<Cat>) {
		std.out.println("Meow!");
	}
}

func main(args: dynarr<string>) {
	var speaker: mut ptr<CanSpeak> = Dog();
	speaker.speak();
	speaker = Cat();
	speaker.speak();
	exit 0;
}
```

`To turn a trait object into a concrete type, you can use as T if you are sure it is valid and as T runtime if you are not sure. It returns a result<T, string>:`

```
import std.out;

trait CanSpeak {
	func speak();
}

class Dog(CanSpeak) {
	func speak(self: ref<Dog>) {
		std.out.println("Woof woof!");
	}
}

class Cat(CanSpeak) {
	func speak(self: ref<Cat>) {
		std.out.println("Meow!");
	}
}

func main(args: dynarr<string>) {
	var speaker: mut ptr<CanSpeak> = Dog();
	speaker.speak();
	speaker = Cat();
	speaker.speak();
	var cat_from_trait_static = speaker as Cat;
	var cat_from_trait_check_at_runtime = speaker as Cat runtime;
	if (cat_from_trait_check_at_runtime.is_valid()) {
		var cat = cat_from_trait_check_at_runtime.get();
		std.out.println("Correct!");
	} else {
		std.out.println("Wrong!");
	}
	exit 0;
}
```

`To combine traits, you can make a trait with trait Name, then =, then a list of traits separated by pipe symbols. This is the only way to have a class implement multiple traits. They can be done inline in a trait implementation declaration:`

```
trait A {
	func a();
}

trait B {
	func b();
}

trait C {
	func c();
}

trait Comb = A | B | C;

class Impl {
	func a() {}
	func b() {}
	func c() {}}func start(args: dynarr<string>) {
	var obj: ptr<Comb> = Impl();
	obj.a();
	obj.b();
	obj.c();
	exit 0;
}
```

`Trait combination also works with concrete types; in that case, they just intersect the implemented methods. All built-in traits end in BITrt, so those are reserved.`  
`Objects that implement + and += get the AddBITrT trait. Objects that implement - and -= get the SubBITrT trait. Objects that implement * and *= get the MulBITrT trait. Objects that implement / and /= get the DivBITrT trait. Objects that implement % and %= get the ModBITrT trait.`  
`Objects that are copy-by-default get the ImpCopyBITrT trait. Objects that can copy get the CopyBITrT trait.`

# `Concurrency`

`Functions can be run on a new thread by using the spawn keyword.`  
`Data can be passed between threads with channels. The chan<T> type is a channel that stores data of type T. Data is accessed in a FIFO pattern. If a channel is overread, it is blocked. If the channel is not read enough, it hangs. You can write to a channel with .send() and read with .receive(). Channels cannot be copied or moved. Both operations require mutability.`

# `Generics`

`Generics in Beryllium are similar to templates in C++. Generics apply to both functions and classes. A type argument is declared with type:`

```
func add<type T>(a: T, b: T) {
	return a + b;
}
```

`You can list traits the type implements and require it to implement those with parentheses, then put a comma-separated list of traits. You cannot have non-type template arguments at the moment. Future standards can implement other template arguments. A similar syntax exists for classes. For functions, you don’t need to specify the template arguments, but if you do allow the compiler to deduce types, all the arguments have to be the same type. You have to do manual types for classes, though. You can specialize generics by just specifying the type instead of using type, and it can bypass any trait restrictions.`

# `Modules`

`You can mark free functions and global variables publish to publish them. In classes, the access markers also apply to importing, so publish is not allowed.`  
`You can do:`

```
import .my_pack; // . necessary to do a local check

func start(args: dynarr<string>) {
	var a: ClassA;
	func_a();
	var b: ClassB;
	func_b(2, "hello world");
	exit 0;
}
```

`You can do selective imports with from. There is no automatically inserted module namespace, unlike in Python. Modules are purely for visibility.`

```
import .my_pack from ClassB, func func_b(int, string);

func start(args: dynarr<string>) {
	var obj: ClassA;
	func_b();
// var obj2: ClassB;
// func_a();
// not imported	exit 0;
}
```

`To do selective imports with functions, you have to specify the full signature. For generics, you have to include the generic arguments, including the names and signature with arguments.`

`System packages are imported without a leading dot, and are located either in the system or directly in the directory with a potential virtual environment. The standard packages are like so. To go through directories, put the name and then dots. You can also import directories directly. In that case, it imports all of the modules directly into that directory.` 

# `Beryllium Standard Name Mangling Scheme`

`The Beryllium Standard Name Mangling Scheme, or the BerstdNMS for short, is a name mangling scheme to keep cross-compatibility across compilers with the same OS and architecture. It is platform-agnostic. Here are the rules:`

1. `All symbols begin with an underscore`  
2. `Then the version, which is a 5 character string, in this case BNMS2`  
3. `All identifiers are prepended with their length`  
4. `All namespaces/classes are put in the order they are nested in.`  
5. `There are no underscores`  
6. `Generic argument regions begin and end with _`  
7. `Functions are appended with parameter types`  
8. `Mutable types are prepended with a m`  
9. `The primitive types get their own mapping:`  
   1. `bool -> b`  
   2. `char -> c`  
   3. `byte -> by`  
   4. `int -> i`  
   5. `long -> l`  
   6. `qint -> q`  
   7. `ubyte -> ub`  
   8. `uint -> ui`  
   9. `ulong -> ul`  
   10. `uqint -> uq`  
   11. `float -> f`  
   12. `double -> d`  
   13. `string -> s`  
   14. `bytestream -> byt`  
   15. `arr -> a_[type]_sz_[size]_`  
   16. `dynarr -> dy_[type]_`  
   17. `adr -> ad`  
   18. `ptr -> p_[type]_`  
   19. `rcptr -> rc_[type]_`  
   20. `arcptr -> arc_[type]_`  
   21. `weakptr -> w_[type]_`  
   22. `unsafeptr -> un_[type]_`  
   23. `ref -> r_[type]_`  
   24. `result -> re_[type][error type]_`  
   25. `tup -> t_[types in order]_`  
   26. `temp -> te_[type]_`  
   27. `opt -> o_[type]_`  
   28. `variant -> v_[types in order]_`  
   29. `Function pointers are just return type, parameter types`  
   30. `The singleton function types are just fu and then the standard function ABI format.`

`BerstdNMS does not define calling conventions, but for the container primitive types, these are the memory layouts, in the order specified:`

1. `bytestream`  
   1. `pointer to bytes`  
   2. `size`  
   3. `capacity`  
2. `arr`  
   1. `stack allocated chunk of memory`  
3. `dynarr`  
   1. `pointer to bytes`  
   2. `size`  
   3. `capacity`  
4. `adr`  
   1. `pointer`  
5. `ptr`  
   1. `pointer`  
6. `rcptr`  
   1. `pointer to control block`  
   2. `pointer to actual type`  
7. `weakptr`  
   1. `pointer to control block`  
   2. `pointer to actual type`  
8. `unsafeptr`  
   1. `pointer`  
9. `ref`  
   1. `pointer`  
10. `result`  
    1. `boolean flag`  
    2. `union of the result and error type`  
11. `string`  
    1. `pointer to unicode characters`  
    2. `size`  
    3. `capacity`  
12. `tup`  
    1. `types in order`  
13. `temp`  
    1. `pointer`  
14. `opt`  
    1. `boolean flag`  
    2. `actual type`  
15. `variant`  
    1. `tag`  
    2. `union of the types`

`The only exception is for the start function, which is unmangled.`