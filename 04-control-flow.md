# Control Flow Statements in Go

## If Statements

The `if` statement is used to execute a block of code based on a condition.

```go
if condition {
    // code to be executed if condition is true
} else {
    // code to be executed if condition is false
}
```

## For Loops

The `for` loop is used to iterate over a range, slice, or perform a repeated action.

```go
for i := 0; i < 10; i++ {
    fmt.Println(i)
}
```

## Switch Statements

The `switch` statement is used to execute one block of code among many alternatives.

```go
switch variable {
case value1:
    // code to be executed if variable == value1
case value2:
    // code to be executed if variable == value2
default:
    // code to be executed if variable doesn't match any case
}
```

## Loops

Loops allow you to execute a block of code multiple times. In Go, you can use the `for` loop for this purpose.

### Example of a Loop

```go
for {
    // code to be executed repeatedly
}
```

You can also use `break` to exit a loop and `continue` to skip to the next iteration.