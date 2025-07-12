# Golang

> Go is an attempt to combine the ease of programming of an interpreted, dynamically typed language with the efficiency and safety of a statically typed, compiled language. - `Go FAQ`

---

## Goals of Go
- **Simplicity**: Go aims to be simple and easy to learn, with a clean syntax.
- **Efficiency**: It is designed for high performance, with fast compilation and execution.
- **Concurrency**: Go has built-in support for concurrent programming, making it easy to write programs that can perform multiple tasks simultaneously.
- **Robustness**: Go emphasizes safety and reliability, with features like garbage collection and strong typing.
- **Tooling**: Go comes with a rich set of tools for formatting, testing, and managing dependencies.

## Real world applications
Go is widely used in various domains like infrastructure, cloud services, web development, and more. Some notable applications include:

- **Docker**: A platform for developing, shipping, and running applications in containers.
- **Kubernetes**: An open-source system for automating deployment, scaling, and management of containerized applications.
- **Helm**: A package manager for Kubernetes that helps in managing Kubernetes applications.
- **Terraform**: An open-source tool for building, changing, and versioning infrastructure safely and efficiently.
- **Prometheus**: A monitoring and alerting toolkit designed for reliability and scalability.
- **gRPC**: A high-performance, open-source RPC framework that uses HTTP/2 for transport and Protocol Buffers as the interface description language.
- **Etcd**: A distributed key-value store used for configuration management and service discovery.
- **Other examples**: Drone, CockroachDB, InfluxDB, Hugo, DropBox, CloudFlare, CoreOS(etcd, flannel), etc.


---

## Hello World Example
```go title="main.go"
package main

import "fmt"

func main() {
    fmt.Println("Hello, World!")
}
```

---

## Keywords and Symbols
Only 25 keywords are reserved in Go; don't use them as identifiers:

| Keyword     | Description                                      |
|-------------|--------------------------------------------------|
| `break`     | Exits a loop or switch statement.                |
| `case`      | Defines a branch in a switch statement.          |
| `chan`      | Declares a channel type for communication.       |
| `const`     | Declares a constant value.                       |
| `continue`  | Skips the current iteration of a loop.           |
| `default`   | Specifies the default case in a switch statement.|
| `defer`     | Defers the execution of a function until the surrounding function returns. |
| `else`      | Defines an alternative branch in an if statement.|
| `fallthrough` | Allows execution to continue to the next case in a switch statement. |
| `for`       | Starts a loop.                                   |
| `func`      | Declares a function.                             |
| `go`        | Starts a goroutine for concurrent execution.     |
| `goto`      | Jumps to a labeled statement.                    |
| `if`        | Starts a conditional statement.                  |
| `import`    | Imports packages for use in the program.         |
| `interface` | Declares an interface type.                      |
| `map`       | Declares a map type for key-value pairs.         |
| `package`   | Declares a package name for the file.            |
| `range`     | Iterates over elements in a collection.          |
| `return`    | Exits a function and optionally returns a value. |
| `select`    | Waits on multiple channel operations.            |
| `struct`    | Declares a composite data type.                  |
| `switch`    | Starts a switch statement for multiple conditions.|
| `type`      | Declares a new type.                             |
| `var`       | Declares a variable.                             |


### Symbols/Operators
Some common symbols and operators in Go:

| Symbol | Description                                      |
|--------|--------------------------------------------------|
| `+, -, *, /, %` | Arithmetic operators for addition, subtraction, multiplication, division, and modulus. |
| `&, |, ^, <<, >>, &^` | Bitwise operators for AND, OR, XOR, left shift, right shift, and AND NOT. |
| `&=, |=, ^=, <<=, >>=, &^=` | Assignment operators for bitwise operations. |
| `==, !=, <, <=, >, >=` | Comparison operators for equality, inequality, and relational comparisons. |
| `&&, ||, !` | Logical operators for AND, OR, and NOT. |
| `=, :=` | Assignment operators; `=` for assigning values to variables, `:=` for short variable declaration. |
| `++, --` | Increment and decrement operators. |
| `<-` | Channel operator for sending and receiving values. |

---

## Basic Types
Go has several built-in types, including:

- **Boolean**: `bool` - Represents true or false values.
- **Numeric**: Includes integers (`int`, `int8`, `int16`, `int32`, `int64`), unsigned integers (`uint`, `uint8`, `uint16`, `uint32`, `uint64`), and floating-point numbers (`float32`, `float64`).
- **String**: `string` - Represents a sequence of characters.
- **Complex**: `complex64`, `complex128` - Represents complex numbers with real and imaginary parts.
- **Array**: A fixed-size sequence of elements of the same type.
- **Slice**: A dynamically-sized, flexible view into the elements of an array.
- **Map**: A collection of key-value pairs, where keys are unique.
- **Struct**: A composite type that groups together variables (fields) under a single name.
- **Interface**: A type that specifies a contract for methods that a type must implement.

```go title="basic_types.go"
package main

import "fmt"

func main() {
    // Declare variables
    var x int = 10
    var y float64 = 20.5
    var name string = "Go Language"
    
    // Print values
    fmt.Println("Integer:", x)
    fmt.Println("Float:", y)
    fmt.Println("String:", name)
    
    // Basic arithmetic operations
    sum := x + int(y) // Convert float to int for addition
    fmt.Println("Sum:", sum)
}
```

**Notes**

- `bool`
    - (boolean) has two values: `true` and `false`.
    - These values are not convertible to/from integers.
- `error`:
    - A special type with one function, `Error() string`
    - An error may be nil or non-nil
- `pointers`: 
    - Pointers are physical addresses, logically opaque
    - A pointer may be nil or non-nil
    - No pointer manipulation except through package `unsafe`


---

## Declarations
```go title="declarations.go"
package main

// Anywhere
var a int
var (
    b = 42
    c = "Hello, Go"
    d, e = 3.14, true
)

// Inside a function
func main() {
    var f int = 10
    g := 20.3
    fmt.Printf("f: %8T %v\n", f, f)
    fmt.Printf("g: %8T %[1]v\n", g)
}
```

## Initialization
Go intialzies all variables to `zero` value of their type by default if you don't.

### Zero Values
- All numberical types get 0 (`float` 0.0, `complex` 0i)
- `bool` gets `false`
- `string` gets `""` (empty string, length 0)
- Everything else gets `nil`:
    - `pointers`
    - `slices`
    - `maps`
    - `channels`
    - `functions(function variables)`
    - `interfaces`
- For aggregate types, all members get their `zero` values.

### Constants
- Only numbers, strings, and booleans can be contants (immutable).
- Constant can be a literal or a compile-time function of a constant

```go title="constants.go"
const (
    a = 1
    b = 4 * 12
    c = b << 2 // Shift left by 2 bits

    d = "Hello, World!"
    e = len(d) // Length of the string

    f = true
    g = 3.14 // Pi value
    h uint8 = 0xFF
    
    // i = d[2:] // SYNTAX ERROR: can't do complicated string operations
)
```

## Comments
Go supports two types of comments:

- **Single-line comments**: Start with `//` and continue to the end of the line.
- **Multi-line comments**: Enclosed between `/*` and `*/`, allowing for comments that span multiple lines.

```go title="comments.go"
// This is a single-line comment

/*
This is a multi-line comment
It can span multiple lines
*/
```

## Strings
Types related to strings:
- `byte`: a synonym for uint8
- `rune`: a synonym for int32, represents a Unicode code point
- `string`: immutable sequence of `characters`
    - physically a sequence of bytes (UTF-8 encoded)
    - logically a sequence of (`unicode`) `runes`

Runes (characters) are enclosed in single quotes: `'a'`

`Raw` strings use backticks quotes: ``string with "quotes"``
They also don't evalute escape sequences like `\n` or `\t`.

```go title="main.go"
package main

import "fmt"

func main() {
    // String literals
    str1 := "Hello, World!" // Regular string
    str2 := `This is a raw string literal.` // Raw string

    // String concatenation
    combined := str1 + " " + str2

    // Print strings
    fmt.Println(str1)
    fmt.Println(str2)
    fmt.Println(combined)

    // Length of a string
    fmt.Println("Length of str1:", len(str1))
}
```

### String structure
- The internal string representation is a pointer and a length
- Strings are immutable and can share the underlying storage
- Strings are passed by reference, thus they aren't copied

```go title="string_structure.go"
s := "Hello, World!"
hello := s[:5] // "Hello"
world := s[7:] // "World!"
```

**Note**: In above example, `hello` and `world` share the same underlying storage as `s`, but they are not mutable. Any modification to `hello` or `world` will result in a new string being created.


### String functions
Package `strings` provides many useful functions for string manipulation:

```go title="string_functions.go"
package main

import (
    "fmt"
    "strings"
)

func main() {
    str := "Hello, World!"

    // Check if string contains a substring
    fmt.Println("Contains 'World':", strings.Contains(str, "World"))

    // Count occurrences of a substring
    fmt.Println("Count of 'o':", strings.Count(str, "o"))

    // Find the index of a substring
    fmt.Println("Index of 'World':", strings.Index(str, "World"))

    // Replace a substring
    replaced := strings.ReplaceAll(str, "World", "Go")
    fmt.Println("Replaced string:", replaced)

    // Split a string into a slice
    parts := strings.Split(str, ", ")
    fmt.Println("Split parts:", parts)

    // Join a slice into a string
    joined := strings.Join(parts, " - ")
    fmt.Println("Joined string:", joined)
}
```


#### String find and replace example

```go title="string_find_replace.go"
package main

import (
    "buffio"
    "fmt"
    "os"
    "strings"
)

func main() {
    my_string := `
    John is a software engineer.
    John loves coding in Go.
    John also enjoys solving problems.
    `

    if len(os.Args) < 3 {
        fmt.Println("Usage: go run string_find_replace.go <old> <new>")
        return
    }

    old, new := os.Args[1], os.Args[2]
    scan := bufio.NewScanner(os.Stdin)

    fmt.Println("Enter text (Ctrl+D to end):")
    for scan.Scan() {
        s := strings.Split(scan.Text(), old)
        t := strings.Join(s, new)
        fmt.Println(t)
    }
}
```

```plaintext title="sample_file.txt"
John is a software engineer.
John loves coding in Go.
John also enjoys solving problems.
```

```bash
go run string_find_replace.go John Jane < sample_file.txt
```

## Arrays, Slices, and Maps
- **Arrays**
    - Fixed-size, homogeneous collections of elements.
    - Example: `[5]int`

- **Slices**
    - Dynamic, flexible views into arrays.
    - Can grow and shrink in size.
    - Example: `[]int` (slice of integers)

- **Maps**
    - Unordered collections of key-value pairs.
    - Keys must be unique and of a comparable type.
    - Example: `map[string]int` (map from string to int)


### Arrays
* Array are typed by size, which is fixed at compile time.
- Arrays are passed by value, thus elements are copied.

```go title="arrays.go"
package main

func main() {
	var a [5]int                    // Array of 5 integers
	var b [3]int = [3]int{1, 2, 3}  // Array with initial values
	var c = [...]int{1, 2, 3, 4, 5} // Array with inferred size

	var d [3]int
	d = b // Elements are copied

	var m = [...]int{1, 2, 3, 4, 5} // Array with inferred size
	c = m                           // Type mismatch
}
```

### Slices
- Slices have variable length, backed by some array; they are copied when they outgrow their backing array.
- Slices are passed by reference, thus elements are not copied, updating OK

```go title="slices.go"
package main

func main() {
    var a []int               // Slice of integers
    b := []int{1, 2, 3}       // Slice with initial values
    
    a = append(a, 1)          // append to nil, OK
    b = append(b, 4)          // []int{1, 2, 3, 4}

    a = b                     // overwrites a

    d := make([]int, 5)       // []int{0, 0, 0, 0, 0}
    e := a                    // same storage (alias)

    e[0] == b[0]              // true, same storage
}
```

### Array vs Slice
In Go, slices are most commonly used instead of arrays.

| Array                        | Slice                          |
|------------------------------|-------------------------------|
| Fixed size at compile time   | Dynamic size                  |
| Passed by value              | Passed by reference           |
| Elements are copied          | Elements are not copied       |
| Comparable                   | Not comparable                |
| Can be used as map keys      | Cannot be used as map keys    |
| ---------------              | Has `copy` and `append` functions |
| Useful as `pseudo` constants | Useful for function parameters   |


### Maps
- Maps are dictionaries: indexed by key, returning a value.
- We can read from a nil map, but cannot write to it.
- The type used for the key must be comparable, (`==`, `!=` operators) and the value can be any type.

```go title="maps.go"
package main

func main() {
    var m map[string]int          // nil map, no storage
    n := make(map[string]int)     // empty map, storage allocated

    a := n["hi"]                  // returns 0, no panic
    b := m["hi"]                  // returns 0, no panic
    n["hi"] = 1                   // OK, write to non-nil map
    // m["hi"] = 42               // panic: cannot write to nil map

    m = n                         // Ok, m now points to p's storage
    m["hi"]++                     // Increase value from 1 to 2
    c := n["hi"]                  // Returns 2, value for key "hi"
}
```

**Note**: Maps can't be compared to one another; map can be compared to `nil` as a special case.
```go title="map_comparison.go"
var m = map[string]int{"a": 1, "b": 2}

var n map[string]int

a := m == n         // Syntax error
b := n == nil       // true, n is not nil
d := len(m)         // 2, length of map m
e := cap(m)         // type mismatch, cap() not defined for maps
```

#### Map Lookup
- Maps have a special two-result lookup function.
- The second result is a boolean indicating if the key exists in the map.

```go title="map_lookup.go"
package main

func main() {
    m := map[string]int{}               // non-nil but empty map

    a := m["hi"]                        // returns 0, no panic
    b, ok := m["hi"]                    // b = 0, ok = false (key not found)

    m["hi"]++                           // hi not present, so hi is added with its zero value and incremented to 1
    c, ok := m["hi"]                    // c = 1, ok = true
    if v, ok := m["hi"]; ok {
        fmt.Println("Key 'hi' exists with value:", v)
    } else {
        fmt.Println("Key 'hi' does not exist")
    }
}
```

## Built-ins
Each type has certain built-in functions

| Function         | Type                             | Description                               |
|------------------|----------------------------------|-------------------------------------------|
| `len(s)`         | `string`, `array`, `slice`, `map`| Returns length.                           |
| `cap(s)`         | `slice`, `array`, `chan`         | Returns capacity.                         |
| `new(T)`         | `T`                              | Allocates zeroed `T`, returns pointer.    |
| `make(T, x)`     | `slice`                          | Slice with len & cap = `x`.               |
| `make(T, x, y)`  | `slice`                          | Slice with len `x`, cap `y`.              |
| `copy(dst, src)` | `slice`                          | Copies `min(len(dst), len(src))` elements.|
| `append(c, d)`   | `slice`                          | Appends `d` to `c`, returns new slice.    |
| `make(T)`        | `map`, `chan`                    | Creates empty map or channel.             |
| `make(T, x)`     | `map`                            | Map with room for `x` items.              |
| `delete(m, key)` | `map`                            | Removes `key` from map `m`.               |


## Make nil useful
> Make the zero value useful. - Rob Pike

- `nil` is a type of zero: it indicates the absence of something.
- Many built-ins are safe: len, cap, range

```go title="nil_useful.go"
package main

func main() {
    var s []int             // nil slice, no storage
    var m map[string]int    // nil map, no storage

    l := len(s)             // 0, safe to call on nil slice
    c := cap(s)             // 0, safe to call on nil slice

    i, ok := m["key"]       // i = 0, ok = false, safe to call on nil map
    
    for _, v := range s {   // safe to range over nil slice, no iteration
        fmt.Println(v)      // won't execute
    }
}
```

## Control Statements; Declarations and Types
Go has several control statements for flow control:

- **if-else**: Conditional execution.
- **for**: Looping construct.
- **switch**: Multi-way branching.

### If-else
```go title="if_else.go"
package main

import "fmt"

func something() error {
    return nil 
}

func main() {
    x := 10

    // Basic if statement
    if x > 0 {
        fmt.Println("x is positive")
    }

    // If-else statement
    if x < 0 {
        fmt.Println("x is negative")
    } else {
        fmt.Println("x is non-negative")
    }

    // If-else if-else statement
    if x < 0 {
        fmt.Println("x is negative")
    } else if x == 0 {
        fmt.Println("x is zero")
    } else {
        fmt.Println("x is positive")
    }

    // Short declaration
    if err := something(); err != nil {
        fmt.Println("Error:", err)
    } else {
        fmt.Println("No error")
    }
}
```

### For Loop
- The only loop construct in Go is the `for` loop.
- Three parts: initialization, condition, and post statement. `(all optional)`
- Loop ends when the condition evaluates to false.

```go title="for_loop.go"
package main

import "fmt"

func main() {
    // Basic for loop
    for i := 0; i < 5; i++ {
        fmt.Println("Iteration:", i)
    }

    // While-like loop
    j := 0
    for j < 5 {
        fmt.Println("While-like iteration:", j)
        j++
    }

    // Infinite loop with break condition
    k := 0
    for {
        if k >= 5 {
            break // Exit the loop
        }
        fmt.Println("Infinite loop iteration:", k)
        k++
    }

    // Range over a slice
    slice := []string{"a", "b", "c"}
    for index, value := range slice {
        fmt.Printf("Index: %d, Value: %s\n", index, value)
    }
}
```

#### Labels and loops
Sometimes we need to break or continue outer loops from within inner loops. `quadratic search` is a good example.

```go title="labeled_loops.go"
outer:
    for k := range testItemsMap {           // keys
        for _, v := range returnItems {     // values in list
            if k == v {
                continue outer              // Continue the outer loop
            }
        }

        t.Errorf("Item %s not found in return items", k)
    }
```

### Switch 
- It is a shortcut replacing a series of if-then statements.
- Alternatively may be empty and do not fall through (break is not required).

**Simple switch**
```go title="switch_statement.go"
switch a := f.Get(); a {
    case 0, 1:
        fmt.Println("Good")
    case 2, 3, 4, 5, 6:
        fmt.Println("Bad")
    case 7, 8, 9:             // Intentionally left empty, no operations
    default:
        fmt.Println("Unknown")
}
```

**Switch on true**
- Arbitrary comparisons may be made for an switch with no arguments.

```go title="switch_on_true.go"
a := 5

switch {
    case a < 0:
        fmt.Println("Negative")
    case a == 0:
        fmt.Println("Zero")
    case a > 0 && a < 10:
        fmt.Println("Positive and less than 10")
    default:
        fmt.Println("Greater than or equal to 10")
}
```

### Package-level declarations
- Can declare anything at package scope
- Can't use short declaration `:=` at package scope
- Package-level variables are initialized before `main()` runs
```go title="package_declarations.go"
package main

import "fmt"

var (
    a = 1
    b = 2
)

func init() {
    fmt.Println("Package-level init function")
    a = 3 // Can modify package-level variables
}

func main() {
    fmt.Println("a:", a) // Outputs: a: 3
    fmt.Println("b:", b) // Outputs: b: 2
}
```

#### Packages control visibility
- Every name that's capitalized is exported (public)
- Within a package, everything is visible across files

```go title="package_visibility.go"
package secrets

func GetSecret(user string) (string, error) {
    if validateUser(user) {
        return generateSecret(user), nil // Calls an unexported function
    }
    return nil, fmt.Errorf("invalid user")
}

func validateUser(user string) bool {
    return user != "" // Unexported function, not visible outside this package
}

func generateSecret(user string) string {
    return "Super secret key for " + user // Unexported function
}
```

**Note**: `validateUser` and `generateSecret` are unexported functions but `GetSecret` is exported and can be called from other packages.


### Imports
- Each source file must declare its package and imports.
- Only import what it needs; unused imports are an error.
- Generally, files of the same package is placed in the same directory.
- No cycles in imports, i.e., package A cannot import package B if B imports A.


### Shadowing short declarations
- Short declarations `:=` can shadow variables in outer scopes.
```go title="shadowing_short_declarations.go"
package main

import "fmt"

func main() {
    n, err := fmt.Println("Hello, World!") 

    if _, err := fmt.Println(n); err != nil {
        fmt.Println("Error:", err)
    }
    // The above line shadows the outer 'err' variable
    // The outer 'err' is still accessible here
    fmt.Println("Outer err:", err) // Outputs: Outer err:
}
```

**Note**: Compile error -> the first err is unused.

### Structural typing
- It's the same type if it has the same structure or behavior.

```go title="structural_typing.go"
a := [...]int{1, 2, 3} // Array of integers
b := []int{1, 2, 3}     // Slice of integers

```

### Named typing
- It's the only the same type if it has the same declared type name

```go title="named_typing.go"
type MyInt int

func main() {
    var a MyInt
    
    b := 12

    a = b // type mismatch
    a = 12 // OK, 12 is an int literal
    a = MyInt(b) // OK, type conversion
}
```
