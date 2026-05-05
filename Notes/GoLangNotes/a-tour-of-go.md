# A Tour of Go

[Where to download](https://go.dev/tour/welcome/3?utm_source=chatgpt.com)

The tour is bilt on [Go Playground](https://go.dev/play/) and some more info on that [https://go.dev/blog/playground](https://go.dev/blog/playground)

## Packages

Go programs are made of packages. Programs start running in package `main`.

Using the example below, it's good to note that the package name is the same as the last element of the import path.

```go
import (
	"fmt"
	"math/rand"
)
```

and the usage:

```go
	fmt.Println("My favorite number is", rand.Intn(10))
```

## Imports

Allows a factored import, or an import statment with the line seperated imports within parenthesis.

```go
import (
  "fmt"
  "math"
)
```

Can also use regular import statement one by one.

## Export Names

All exports start with a capital letter. For instance:

```go
func main() {
	fmt.Println(math.Pi)
}
```

Where Pi is the export.

## Functions

```go
func add(x int, y int) int {
	return x + y
}

func main() {
	fmt.Println(add(42, 13))
}
```

Notice that the types come after the names.

If consecutive arguments have the same type, you can leave the type out accept for the last one.

```go
func add(x, y int) int {
	return x + y
}
```

Functions can return multiple results:

```go
func swap(x, y string) (string, string) {
	return y, x
}

func main() {
	a, b := swap("hello", "world")
	fmt.Println(a, b)
}
```

Go's return values can be named and if so, are treated like variabels defined at the top of hte function:

```go
func split(sum int) (x, y int) {
	x = sum * 4 / 9
	y = sum - x
	return
}

func main() {
	fmt.Println(split(17))
}
```

A `return` statemenr without arguments returns the named return valeus. Thsi is known as a "naked" reutrn. This is best for short fucntions as it can harm readability in longer functions.

## Variables

`var` statments delcare a list of variables with the type at the end. These can be declared at package or function level.

```go
var c, python, java bool

func main() {
	var i int
	fmt.Println(i, c, python, java)
}
```

Variable delcaratoin statments can include initializers. One per variable. The type can be ommited if there are initial values.

```go
var i, j = 1, 2

func main() {
	var c, python, java = true, false, "no!"
	fmt.Println(i, j, c, python, java)
}
```

The `:=` short assignement can be used at funtion level. This is a shorter syntax for `var`ar declarations.

```go
func main() {
	var i, j int = 1, 2
	k := 3
	c, python, java := true, false, "no!"

	fmt.Println(i, j, k, c, python, java)
}
```

## Basic Types

Go's basic types are:

```
bool

string

int  int8  int16  int32  int64
uint uint8 uint16 uint32 uint64 uintptr

byte // alias for uint8

rune // alias for int32
     // represents a Unicode code point

float32 float64

complex64 complex128
```

here is an example of the variables being declared. This is a "factored" var declaration.

```go
var (
ToBe bool = false
MaxInt uint64 = 1<<64 - 1
z complex128 = cmplx.Sqrt(-5 + 12i)
)

func main() {
fmt.Printf("Type: %T Value: %v\n", ToBe, ToBe)
fmt.Printf("Type: %T Value: %v\n", MaxInt, MaxInt)
fmt.Printf("Type: %T Value: %v\n", z, z)
}

```

Any variable declared without and explicit initial values are given their zero value.

The xero value is:

```
0 for numeric types,
false for the boolean type, and
"" (the empty string) for strings.
```

## Type Conversion

The experssoin `T(v)` converts the value `v` into type `T`. Type conversions are not implicit in Go and are required.

## Type inference

When declaring a variable without specifying an explicit type (either by using the := or the `var =` expression syntax), the variable's type is inferred from the valu on the right hand side.

If it is a variable on the right, the variable on the left will be of the same type.

## Contsants

Constants are declared like variables but with the `const` key word. Constants can be character, string, boolean, or numeric values. Constants can not be declared with the ':=' syntax.

## For

For loop is the only loop.

Similar to C# Syntax but no parenthesis:

```go
func main() {
	sum := 0
	for i := 0; i < 10; i++ {
		sum += i
		fmt.Println(sum)
	}
	fmt.Println(sum)
}
```

The init and post statmetns in the for declaration are optional.

```go
func main() {
	sum := 1
	for ; sum < 1000; {
		sum += sum
	}
	fmt.Println(sum)
}
```

So it's basically a while statement at this point:

```go
func main() {
	sum := 1
	for sum < 1000 {
		sum += sum
	}
	fmt.Println(sum)
}
```

For some reason you can do an infinite loop:

```go
func main() {
	for {
	}
}
```

### if

If statements are as expected accept there are no parenthese. The braces are needed.

```go
func sqrt(x float64) string {
	if x < 0 {
		return sqrt(-x) + "i"
	}
	return fmt.Sprint(math.Sqrt(x))
}

func main() {
	fmt.Println(sqrt(2), sqrt(-4))
}
```

## If with a short statement

Like the for loops initial statement, if statements can also start with a short statement to execute before the condition.

```go
func pow(x, n, lim float64) float64 {
	if v := math.Pow(x, n); v < lim {
		return v
	}
	return lim
}

func main() {
	fmt.Println(
		pow(3, 2, 10),
		pow(3, 3, 20),
	)
}

```

Variables declared inside an if short statement are also available inside any of the else blocks.

## Switch

Switch statements are available as well.

```go
func main() {
	fmt.Print("Go runs on ")
	switch os := runtime.GOOS; os {
	case "darwin":
		fmt.Println("macOS.")
	case "linux":
		fmt.Println("Linux.")
	default:
		// freebsd, openbsd,
		// plan9, windows...
		fmt.Printf("%s.\n", os)
	}
```

A switch without a condition is the same as `switch true`. This construct can be a clean way to write long if-then-else chains.

```go
func main() {
	t := time.Now()
	switch {
	case t.Hour() < 12:
		fmt.Println("Good morning!")
	case t.Hour() < 17:
		fmt.Println("Good afternoon.")
	default:
		fmt.Println("Good evening.")
	}
}
```

A defer statement defers the execution of a function until the surrounding function returns.

The deferred call's arguments are evaluated immediately, but the function call is not executed until the surrounding function returns.

```go
func main() {
	defer fmt.Println("world")

	fmt.Println("hello")
}
```

returns

```
hello
world
```

Note: Deferred function calls are pushed onto a stack. When a function returns, its deferred calls are executed in last-in-first-out order.

## Pointers

Holds the memory address of a value.

The type `*T` is a pointer to a `T` value. It's zero value is `nil`

```go
var p *int
```

the `&` operator generatees a pointer to it's operand.

```go
i := 42
p = &i
```

The `*` operator denotes the pointer's underlying value.

```go
fmt.Println(*p) // read i through the pointer p
*p = 21         // set i through the pointer p
```

This is known as "dereferencing" or "indirecting"

Unlice C, Go has no pointer arithmetic.

## Struct

A collection of fields

```go
type Vertex struct {
	X int
	Y int
}

func main() {
	fmt.Println(Vertex{1, 2})
}
```

structs fields are accessed using the `.` operator

Struct fields can be accessed through a struct pointer.

To access the field X of a struct when we have the struct pointer p we could write (\*p).X. However, that notation is cumbersome, so the language permits us instead to write just p.X, without the explicit dereference.

```go
type Vertex struct {
	X int
	Y int
}

func main() {
	v := Vertex{1, 2}
	p := &v
	p.X = 1e9
	fmt.Println(v)
}
```

A struct literal denotes a newly allocated struct value by listing the values of its fields.

You can list just a subset of fields by using the Name: syntax. (And the order of named fields is irrelevant.)

The special prefix & returns a pointer to the struct value.

```go
A struct literal denotes a newly allocated struct value by listing the values of its fields.

You can list just a subset of fields by using the Name: syntax. (And the order of named fields is irrelevant.)

The special prefix & returns a pointer to the struct value.
```

## Arrays

The type [n]T is an array of `n` values of type `T`

The expression

`var a [10]int`

declars a variable `a` as an array of ten integers.

The length is part of it's type so arrays can not be resized.

A slice is a dynamically-sized, flexible view into the array.

It's defined with a high and low end.

```go
a[low:high]
```

This selects a half-open range which includes the first element, but excludes the last one.

The following expression creates a slice which includes elements 1 through 3 of a:

```go
a[1:4]
```

A slice does not store any data, it just describes a section of an underlying array. Changing the elements of a slice midifies the corresponding element of its underlying array. Other slices that share the same underlying array will see those changes.

### Slice litteral

A slice literal is like an array literal without the length.

This is an array literal:

```go
[3]bool{true, true, false}
```

And this creates the same array as above, then builds a slice that references it:

```go
[]bool{true, true, false}
```

When slicing you can omit the high or low bounds and get the default in it's palce. 0 is the default low bounds and the length of the array is the upper bounds.

A slice has a length and a capacity where the length is the number of elements it contains and the capacity is the number of elements in the underlying array starting at the first element of the slice.

### Len and Cap of a slice

You can find these with `len(s)` and `cap(s)`

### Slice zero value

The zero value of a slice is `nil`.

A nil slice has a length and cap of 0 and no underlying array.

### Creating a slice with make

Slices can be created with the built-in make function; this is how you create dynamically-sized arrays.

The make function allocates a zeroed array and returns a slice that refers to that array:

```go
a := make([]int, 5)  // len(a)=5
To specify a capacity, pass a third argument to make:

b := make([]int, 0, 5) // len(b)=0, cap(b)=5

b = b[:cap(b)] // len(b)=5, cap(b)=5
b = b[1:]      // len(b)=4, cap(b)=4
```

### Slice of a slice

Slices can contain any type, including other slices

### Appending to a slice

It is common to append new elements to a slice, and so Go provides a built-in append function. The documentation of the built-in package describes append.

```go
func append(s []T, vs ...T) []T
```

The first parameter s of append is a slice of type T, and the rest are T values to append to the slice.

The resulting value of append is a slice containing all the elements of the original slice plus the provided values.

If the backing array of s is too small to fit all the given values a bigger array will be allocated. The returned slice will point to the newly allocated array.

(To learn more about slices, read the Slices: usage and internals article.)

## Range

The `range` form of the `for` loop iterates over a slice or map.

Two values are returned in this case, the index and the element.

You can skip the index or value by assigning to \_.

```go
for i, _ := range pow
for _, value := range pow
```

If you only want the index, you can omit the second variable.

```go
for i := range pow
```

## Maps

A map maps keys to values

The zero value of a map is `nil`. A `nil` map has no keys nor can keys be added.

The `make` function returns a map of the given type, initialized and ready for use.

```go
type Vertex struct {
	Lat, Long float64
}

var m map[string]Vertex

func main() {
	m = make(map[string]Vertex)
	m["Bell Labs"] = Vertex{
		40.68433, -74.39967,
	}
	fmt.Println(m["Bell Labs"])
}
```

### Map Litterals

Map literals are like struct literals, but the keys are required.

```go
type Vertex struct {
	Lat, Long float64
}

var m = map[string]Vertex{
	"Bell Labs": Vertex{
		40.68433, -74.39967,
	},
	"Google": Vertex{
		37.42202, -122.08408,
	},
}

func main() {
	fmt.Println(m)
}
```

If the top-level type is just a type name, you can omit it from the elements of the literal.

```go
type Vertex struct {
	Lat, Long float64
}

var m = map[string]Vertex{
	"Bell Labs": {40.68433, -74.39967},
	"Google":    {37.42202, -122.08408},
}

func main() {
	fmt.Println(m)
}
```

### Mutating Maps

Insert or update an element in map m:

```go
m[key] = elem
```

Retrieve an element:

```go
elem = m[key]
```

Delete an element:

```go
delete(m, key)
```

Test that a key is present with a two-value assignment:

```go
elem, ok = m[key]
```

If key is in m, ok is true. If not, ok is false.

If key is not in the map, then elem is the zero value for the map's element type.

Note: If elem or ok have not yet been declared you could use a short declaration form:

```go
elem, ok := m[key]
```

## Function values

Functions are values too. They can be passed around just like other values.

Function values may be used as function arguments and return values.

```go
func compute(fn func(float64, float64) float64) float64 {
	return fn(3, 4)
}

func main() {
	hypot := func(x, y float64) float64 {
		return math.Sqrt(x*x + y*y)
	}
	fmt.Println(hypot(5, 12))

	fmt.Println(compute(hypot))
	fmt.Println(compute(math.Pow))
}
```

## Function closures

Go functions may be closures. A closure is a function value that references variables from outside its body. The function may access and assign to the referenced variables; in this sense the function is "bound" to the variables.

For example, the adder function returns a closure. Each closure is bound to its own sum variable.

```go
func adder() func(int) int {
	sum := 0
	return func(x int) int {
		sum += x
		return sum
	}
}

func main() {
	pos, neg := adder(), adder()
	for i := 0; i < 10; i++ {
		fmt.Println(
			pos(i),
			neg(-2*i),
		)
	}
}
```

## Methods

Go has no classes but you can define methods on types.

Methods are functions with a special reciever argument.

The reciever appears in its own argument list between the `func` key workd and the method name.

```go
type Vertex struct {
	X, Y float64
}

func (v Vertex) Abs() float64 {
	return math.Sqrt(v.X*v.X + v.Y*v.Y)
}

func main() {
	v := Vertex{3, 4}
	fmt.Println(v.Abs())
}
```

Methods can be defined on non-struct types as well.

```go
func (f MyFloat) Abs() float64 {
	if f < 0 {
		return float64(-f)
	}
	return float64(f)
}

func main() {
	f := MyFloat(-math.Sqrt2)
	fmt.Println(f.Abs())
}
```

## Pointer Receivers

You can declare methods with pointer recievers.

This means the reciever type has the literal syntax `*T` for some type `T`. (Also, `T` cannot itself be a pointer as `*int`)

Methods with pointer recievers can modify the value to which the receiver points. Since methods often need to modify their reciever, pointer reveivers are more common that value receivers.

## Pointers and Functions

Fucntions can also take pointers as arguments but they must be passed a pointer unlice recievers in methods which can have a value passed and it will take it like a pointer.

## Choosing a value or pointer reciever

There are two reasons to use a pointer receiver.

The first is so that the method can modify the value that its receiver points to.

The second is to avoid copying the value on each method call. This can be more efficient if the receiver is a large struct, for example.

In general, all methods on a given type should have either value or pointer receivers, but not a mixture of both.

## Interfaces

An interface type is defined as a set of method signatures.

A value of interface type can hold any value that implements those methods.

```go
type Abser interface {
	Abs() float64
}

func main() {
	var a Abser
	f := MyFloat(-math.Sqrt2)
	v := Vertex{3, 4}

	a = f  // a MyFloat implements Abser
	a = &v // a *Vertex implements Abser

	// In the following line, v is a Vertex (not *Vertex)
	// and does NOT implement Abser.
	a = v

	fmt.Println(a.Abs())
}

type MyFloat float64

func (f MyFloat) Abs() float64 {
	if f < 0 {
		return float64(-f)
	}
	return float64(f)
}

type Vertex struct {
	X, Y float64
}

func (v *Vertex) Abs() float64 {
	return math.Sqrt(v.X*v.X + v.Y*v.Y)
}
```

## Interfaces are implemented implicitly

A type implements an interface by implementing its methods. There is no explicit declaration of intent, no "implements" keyword.

Implicit interfaces decouple the definition of an interface from its implementation, which could then appear in any package without prearrangement.

```go
type I interface {
	M()
}

type T struct {
	S string
}

// This method means type T implements the interface I,
// but we don't need to explicitly declare that it does so.
func (t T) M() {
	fmt.Println(t.S)
}

func main() {
	var i I = T{"hello"}
	i.M()
}
```

## Interface values

Under the hood, interface values can be thought of as a touple of a value and a concrete type:
`(value, type)`

An interfeace value holds a value of a specific underlying concrete type.

Calling a method on an interface value executes the method of the same name on it's underlying type.

If that concrete underlyling value is `nil`, the method will be called with a nil receiver.

## Empty interface

The interface type that specifies zero methods is known as the empty interfaces:

```go
interface{}
```

An empty interface may hold values of any type (Every type implements at least zero methods).

```go
func main() {
	var i interface{}
	describe(i)

	i = 42
	describe(i)

	i = "hello"
	describe(i)
}

func describe(i interface{}) {
	fmt.Printf("(%v, %T)\n", i, i)
}
```

## Type assertions

A type assertion provides access to an interface value's underlying concrete value.

```go
t := i.(T)
```

This statement asserts that the interface value i holds the concrete type T and assigns the underlying T value to the variable t.

If i does not hold a T, the statement will trigger a panic.

To test whether an interface value holds a specific type, a type assertion can return two values: the underlying value and a boolean value that reports whether the assertion succeeded.

```go
t, ok := i.(T)
```

If i holds a T, then t will be the underlying value and ok will be true.

If not, ok will be false and t will be the zero value of type T, and no panic occurs.

Note the similarity between this syntax and that of reading from a map.

```go
func main() {
	var i interface{} = "hello"

	s := i.(string)
	fmt.Println(s)

	s, ok := i.(string)
	fmt.Println(s, ok)

	f, ok := i.(float64)
	fmt.Println(f, ok)

	f, ok = i.(float64) // panic
	fmt.Println(f)
}
```

### Type Switch

A Type switch is a construct that permist serveral type assertions in series.

A type switch is like a regular switch, but the cases in a stype switch specify types (not values), and those values are compared agains the type fot he value held by the givin interface value.

```go
switch v := i.(type) {
  case T:
    // here v has type t
  case S:
    // here v has type S
  default:
    // no match; here v has the same type as i
}
```

## Stringers

One of the most ubiquitose interfaces is `Stringer` defined by the `fmt` package.

```go
type Stringer interface {
  String() string;
}
```

A `Stringer` is a type that can describe itself as a string. The `fmt` package (and many others) look for this interface to print values.

```go
type Person struct {
	Name string
	Age  int
}

func (p Person) String() string {
	return fmt.Sprintf("%v (%v years)", p.Name, p.Age)
}

func main() {
	a := Person{"Arthur Dent", 42}
	z := Person{"Zaphod Beeblebrox", 9001}
	fmt.Println(a, z)
}
```

### Side Notes

Note that there is also a reader for reading files in or out.

Can also handle and generate imagese.

## Goroutines

A goroutine is a lightweight thread manage by the go runtime.

```go
go f (x, y, z)
```

starts a new goroutine running

```go
f(x, y, x)
```

The evaluation of f, x, y and z happens in the current goroutine and the execution of `f` happens if the new goroutine.

Goroutines run in the same address space, so access to shared memory must be synchronized. The sync package provides useful primitives, although you won't need them much in Go as there are other primitives. (See the next slide.)

## Channels

Channels are a typed conduit through which you can send and recieve values with the channel operator, `<-`

```go
ch <- v // Send v to chanel ch.
v := <-ch // Receive from ch, and
          // assign value to v
```

Like maps and slices, channels must be created before use:

```go
ch := make(chan int)
```

By default, sends and recieves block until the other side is ready. This allows goroutines to syncronize without explicit locks or condition variables.

The example code sums the numbers in a slice, distributing the work between two goroutines. Once both goroutines have completed their computation, it calculates the final result.

### Channel Buffers

Channels can be buffered. Provide the buffer length as the second argument to make to initialize a buffered channel:

```go
ch := make(chan int, 100)'
```

Sends to a buffered channel block only when the buffer is full. Receives block when the buffer is empty.

Modify the example to overfill the buffer and see what happens.

### Range and Close

A sender can `close` a channel to indicate that no more values will be sent. Receivers can test whether a chennel has been closed by assigning a second parameter to the receive expression: after

```go
v, ok := <-Ch
```

`ok` is false if there are no more values to recieve and the channel is closed.

The loop `for i := range c` recieves values from the channel repeatedly until it is closed.

**Note:** Only the sender should close a channel, never the receiver. Sending on a closed channel will cause a panic.

**Another Note** Channels aren't like files; you don't usually need to close them. Closing is only necessary when the receiver must be told there are no more values coming, such as to terminate a `range` loop.

```go
func fibonacci(n int, c chan int) {
	x, y := 0, 1
	for i := 0; i < n; i++ {
		c <- x
		x, y = y, x+y
	}
	close(c)
}

func main() {
	c := make(chan int, 10)
	go fibonacci(cap(c), c)
	for i := range c {
		fmt.Println(i)
	}
}
```
