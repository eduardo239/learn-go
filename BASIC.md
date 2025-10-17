# Go Language Basics

Master the fundamental concepts of Go programming.

## 1. Hello World

```go
package main

import "fmt"

func main() {
    fmt.Println("Hello, World!")
}
```

## 2. Variables and Constants

### Variables
```go
// Explicit declaration
var name string = "John"
var age int = 30

// Type inference
var city = "New York"

// Short declaration (only in functions)
message := "Hello"

// Multiple declarations
var (
    firstName string = "John"
    lastName  string = "Doe"
    salary    int    = 50000
)
```

### Constants
```go
const PI = 3.14159
const (
    MONDAY = 0
    TUESDAY = 1
    WEDNESDAY = 2
)
```

## 3. Data Types

### Basic Types
```go
bool       // true or false
string     // text

int        // int8, int16, int32, int64
uint       // uint8, uint16, uint32, uint64
byte       // alias for uint8
rune       // alias for int32

float32    // 32-bit floating point
float64    // 64-bit floating point

complex64  // complex numbers
complex128
```

### Collections
```go
// Arrays (fixed length)
var arr [5]int
numbers := [3]int{1, 2, 3}

// Slices (dynamic length)
var slice []int
fruits := []string{"apple", "banana", "cherry"}
slice = append(slice, 1, 2, 3)

// Maps (key-value pairs)
person := map[string]string{
    "name": "John",
    "city": "NYC",
}

// Accessing
value := person["name"]
```

## 4. Functions

### Basic Function
```go
func add(a int, b int) int {
    return a + b
}

// Shorthand parameters of same type
func add(a, b int) int {
    return a + b
}
```

### Multiple Return Values
```go
func divide(a, b float64) (float64, error) {
    if b == 0 {
        return 0, fmt.Errorf("cannot divide by zero")
    }
    return a / b, nil
}

// Usage
result, err := divide(10, 2)
if err != nil {
    fmt.Println(err)
} else {
    fmt.Println(result)
}
```

### Named Return Values
```go
func swap(x, y string) (first, second string) {
    first = y
    second = x
    return
}
```

### Variadic Functions
```go
func sum(nums ...int) int {
    total := 0
    for _, num := range nums {
        total += num
    }
    return total
}

// Usage
sum(1, 2, 3, 4, 5)  // 15
```

## 5. Control Structures

### If Statement
```go
if age >= 18 {
    fmt.Println("Adult")
} else if age >= 13 {
    fmt.Println("Teen")
} else {
    fmt.Println("Child")
}

// If with short statement
if x := getValue(); x > 10 {
    fmt.Println(x)
}
```

### Switch Statement
```go
switch day {
case "Monday":
    fmt.Println("Start of week")
case "Friday":
    fmt.Println("Almost weekend")
default:
    fmt.Println("Middle of week")
}

// Switch with conditions
switch {
case age < 13:
    fmt.Println("Child")
case age < 18:
    fmt.Println("Teen")
default:
    fmt.Println("Adult")
}
```

### For Loop
```go
// Simple loop
for i := 0; i < 5; i++ {
    fmt.Println(i)
}

// While-like loop
for i < 10 {
    i++
}

// Infinite loop
for {
    if someCondition {
        break
    }
}

// Range loop
numbers := []int{1, 2, 3, 4, 5}
for index, value := range numbers {
    fmt.Println(index, value)
}

// Range over map
for key, value := range person {
    fmt.Println(key, value)
}
```

## 6. Pointers

```go
// Declare pointer
var ptr *int

// Get address of variable
x := 10
ptr = &x

// Dereference pointer
fmt.Println(*ptr)  // 10

// Pointer to pointer
var ptrPtr **int = &ptr
fmt.Println(**ptrPtr)  // 10

// Function with pointer
func increment(num *int) {
    *num++
}

increment(&x)  // x is now 11
```

## 7. Structs

```go
// Define struct
type Person struct {
    Name string
    Age  int
    City string
}

// Create instance
p := Person{
    Name: "John",
    Age:  30,
    City: "NYC",
}

// Access fields
fmt.Println(p.Name)

// Anonymous struct
person := struct {
    name string
    age  int
}{
    name: "Alice",
    age:  25,
}
```

## 8. Methods

```go
// Method on struct
func (p Person) Greet() string {
    return fmt.Sprintf("Hello, I'm %s", p.Name)
}

// Method with pointer receiver
func (p *Person) HaveBirthday() {
    p.Age++
}

// Usage
p := Person{"John", 30, "NYC"}
fmt.Println(p.Greet())
p.HaveBirthday()  // p.Age is now 31
```

## 9. Interfaces

```go
// Define interface
type Shape interface {
    Area() float64
    Perimeter() float64
}

// Implement interface
type Rectangle struct {
    Width, Height float64
}

func (r Rectangle) Area() float64 {
    return r.Width * r.Height
}

func (r Rectangle) Perimeter() float64 {
    return 2 * (r.Width + r.Height)
}

// Use interface
func printShape(s Shape) {
    fmt.Println("Area:", s.Area())
    fmt.Println("Perimeter:", s.Perimeter())
}

rect := Rectangle{10, 5}
printShape(rect)
```

## 10. Error Handling

```go
// Standard error pattern
func readFile(filename string) ([]byte, error) {
    return ioutil.ReadFile(filename)
}

// Usage
data, err := readFile("file.txt")
if err != nil {
    fmt.Println("Error:", err)
    return
}

// Create custom error
func validate(age int) error {
    if age < 0 {
        return fmt.Errorf("age cannot be negative: %d", age)
    }
    return nil
}
```

## 11. Goroutines and Channels

### Goroutines
```go
// Launch goroutine
go someFunction()

// Multiple goroutines
for i := 0; i < 5; i++ {
    go func(num int) {
        fmt.Println(num)
    }(i)
}
```

### Channels
```go
// Create channel
ch := make(chan string)

// Send value
ch <- "hello"

// Receive value
message := <-ch

// Buffered channel
ch := make(chan int, 10)

// Channel in function
func fetchData(ch chan string) {
    ch <- "data"
}

// Range over channel
for value := range ch {
    fmt.Println(value)
}
```

## 12. Defer, Panic, and Recover

```go
// Defer - execute at end of function
func example() {
    defer fmt.Println("Done!")
    fmt.Println("Start")
}
// Output: Start
//         Done!

// Panic - runtime error
if x < 0 {
    panic("x cannot be negative")
}

// Recover - catch panic
defer func() {
    if r := recover(); r != nil {
        fmt.Println("Recovered:", r)
    }
}()
```