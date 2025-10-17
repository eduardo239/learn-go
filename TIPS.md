# Go Language Best Practices and Pro Tips

Learn industry best practices and advanced tips for writing excellent Go code.

## 1. Code Organization and Structure

### Project Layout
```
myapp/
├── cmd/
│   └── myapp/
│       └── main.go              # Application entry point
├── pkg/
│   ├── models/                  # Data models
│   ├── handlers/                # HTTP handlers
│   ├── services/                # Business logic
│   └── utils/                   # Utility functions
├── internal/                    # Private packages
├── tests/                       # Integration tests
├── config/                      # Configuration files
├── docker-compose.yml
├── Dockerfile
├── go.mod
├── go.sum
├── README.md
└── Makefile
```

### Package Organization
```go
// Group related functionality
pkg/
├── user/
│   ├── model.go        // User struct and constants
│   ├── service.go      // Business logic
│   ├── handler.go      // HTTP handlers
│   └── repository.go   // Database queries
```

## 2. Naming Conventions

### Variables and Functions
```go
// Good: Clear, concise names
userName := "john"
isActive := true
maxRetries := 3

// Bad: Too short or unclear
u := "john"
a := true
mr := 3

// Functions: Action-oriented
func (u *User) Save() error
func (u *User) Delete() error
func GetUserByID(id int) (*User, error)
func ValidateEmail(email string) bool
```

### Constants
```go
// Good: Uppercase for constants
const (
    MaxConnections = 100
    DefaultTimeout = 30 * time.Second
    APIVersion     = "v1"
)

// Bad
const max_connections = 100
const default_timeout = 30
```

### Interfaces
```go
// Good: Single method interfaces (io.Reader, io.Writer)
type Reader interface {
    Read(p []byte) (n int, err error)
}

type Writer interface {
    Write(p []byte) (n int, err error)
}

// Good: Suffixed with 'er'
type Encoder interface{}
type Decoder interface{}
type Formatter interface{}

// Bad: Too many methods
type AllInOne interface {
    Read()
    Write()
    Close()
    // ... many more
}
```

## 3. Error Handling

### Explicit Error Handling
```go
// Good: Explicit error handling
file, err := os.Open("file.txt")
if err != nil {
    log.Printf("Error opening file: %v", err)
    return err
}
defer file.Close()

// Bad: Ignoring errors
file, _ := os.Open("file.txt")

// Bad: Not checking errors
file, err := os.Open("file.txt")
defer file.Close()  // File might not be open!
```

### Custom Errors
```go
// Good: Descriptive errors
type ValidationError struct {
    Field   string
    Message string
}

func (e ValidationError) Error() string {
    return fmt.Sprintf("validation error in %s: %s", e.Field, e.Message)
}

// Usage
if email == "" {
    return ValidationError{
        Field:   "email",
        Message: "email is required",
    }
}

// Better: Wrapped errors (Go 1.13+)
import "fmt"

func Process() error {
    if err := someOperation(); err != nil {
        return fmt.Errorf("process failed: %w", err)
    }
    return nil
}

// Check specific error
import "errors"

if errors.Is(err, sql.ErrNoRows) {
    // Handle specific error
}
```

## 4. Performance Optimization

### String Concatenation
```go
// Bad: Creates new string each iteration
result := ""
for i := 0; i < 1000; i++ {
    result += fmt.Sprintf("item %d\n", i)
}

// Good: Use strings.Builder
var sb strings.Builder
for i := 0; i < 1000; i++ {
    fmt.Fprintf(&sb, "item %d\n", i)
}
result := sb.String()

// Also good: Use slices
var lines []string
for i := 0; i < 1000; i++ {
    lines = append(lines, fmt.Sprintf("item %d", i))
}
result := strings.Join(lines, "\n")
```

### Memory Management
```go
// Good: Pre-allocate slices
data := make([]string, 0, 1000)
for i := 0; i < 1000; i++ {
    data = append(data, fmt.Sprintf("item %d", i))
}

// Bad: No pre-allocation (grows exponentially)
var data []string
for i := 0; i < 1000; i++ {
    data = append(data, fmt.Sprintf("item %d", i))
}

// Efficient map initialization
data := make(map[string]int, 1000)

// Good: Reuse buffers
b := make([]byte, 4096)
for {
    n, err := file.Read(b)
    if err != nil {
        break
    }
    // Process b[:n]
}
```

### Struct Field Ordering
```go
// Good: Align fields for memory efficiency
type User struct {
    ID    int64      // 8 bytes
    Age   int32      // 4 bytes
    Name  string     // 16 bytes (pointer, len, cap)
    Email string     // 16 bytes
}

// Less efficient: Poor alignment
type User struct {
    Name  string     // 16 bytes
    ID    int64      // 8 bytes (padding added)
    Age   int32      // 4 bytes
    Email string     // 16 bytes
}
```

## 5. Concurrency Best Practices

### Goroutine Safety
```go
// Good: Use sync.WaitGroup
var wg sync.WaitGroup
for i := 0; i < 10; i++ {
    wg.Add(1)
    go func(id int) {
        defer wg.Done()
        // Do work
    }(i)
}
wg.Wait()

// Good: Use context for cancellation
ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
defer cancel()

go func(ctx context.Context) {
    select {
    case <-ctx.Done():
        return
    case <-time.After(1 * time.Second):
        // Do work
    }
}(ctx)
```

### Channel Patterns
```go
// Good: Buffered channel with timeout
ch := make(chan string, 1)
select {
case result := <-ch:
    fmt.Println(result)
case <-time.After(5 * time.Second):
    fmt.Println("Timeout")
}

// Good: Closing channels properly
func worker(jobs <-chan int, results chan<- int) {
    for job := range jobs {
        results <- job * 2
    }
}

jobs := make(chan int, 100)
results := make(chan int, 100)

go worker(jobs, results)

for i := 1; i <= 10; i++ {
    jobs <- i
}
close(jobs)

for i := 0; i < 10; i++ {
    fmt.Println(<-results)
}
```

## 6. Testing

### Unit Tests
```go
// math.go
package math

func Add(a, b int) int {
    return a + b
}

// math_test.go
package math

import "testing"

func TestAdd(t *testing.T) {
    tests := []struct {
        name     string
        a, b     int
        expected int
    }{
        {"positive", 2, 3, 5},
        {"negative", -2, -3, -5},
        {"mixed", 5, -3, 2},
    }

    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            result := Add(tt.a, tt.b)
            if result != tt.expected {
                t.Errorf("Add(%d, %d) = %d, want %d", tt.a, tt.b, result, tt.expected)
            }
        })
    }
}
```

### Benchmarking
```go
// Run with: go test -bench=. -benchmem

func BenchmarkAdd(b *testing.B) {
    for i := 0; i < b.N; i++ {
        Add(2, 3)
    }
}

// Benchmarking with sub-benchmarks
func BenchmarkStringConcat(b *testing.B) {
    b.Run("concatenation", func(b *testing.B) {
        for i := 0; i < b.N; i++ {
            s := "" + "hello" + " " + "world"
            _ = s
        }
    })

    b.Run("builder", func(b *testing.B) {
        for i := 0; i < b.N; i++ {
            var sb strings.Builder
            sb.WriteString("hello")
            sb.WriteString(" ")
            sb.WriteString("world")
            _ = sb.String()
        }
    })
}
```

## 7. Code Formatting and Tools

### gofmt
```bash
# Format all Go files
gofmt -w ./...

# Check formatting (don't modify)
gofmt -l ./...
```

### goimports
```bash
# Automatically format and organize imports
goimports -w ./...
```

### golangci-lint
```bash
# Lint with multiple linters
golangci-lint run ./...

# Fix issues automatically
golangci-lint run --fix ./...
```

### go vet
```bash
# Check for suspicious code
go vet ./...
```

## 8. Documentation

### Package Documentation
```go
// Package utils provides utility functions for the application.
// It includes string manipulation, file operations, and data processing.
package utils

// IsEmail checks if the provided string is a valid email address.
// It uses a simple regex pattern for validation.
//
// Parameters:
//   email: The string to validate
//
// Returns:
//   bool: true if email is valid, false otherwise
func IsEmail(email string) bool {
    // Implementation
}
```

### Generating Documentation
```bash
# View documentation in terminal
go doc utils.IsEmail

# Start documentation server
godoc -http=:6060
# Visit http://localhost:6060
```

## 9. Common Mistakes to Avoid

### 1. Defer in Loops
```go
// Bad: Defer accumulates
for _, file := range files {
    f, _ := os.Open(file)
    defer f.Close()  // All closes happen at end of function
}

// Good: Close immediately or use function
for _, file := range files {
    func() {
        f, _ := os.Open(file)
        defer f.Close()
    }()
}
```

### 2. Goroutine Loop Variable
```go
// Bad: All goroutines use final value
for i := 0; i < 10; i++ {
    go func() {
        fmt.Println(i)  // Always prints 10
    }()
}

// Good: Pass as argument
for i := 0; i < 10; i++ {
    go func(id int) {
        fmt.Println(id)
    }(i)
}
```

### 3. Nil Slice vs Empty Slice
```go
// Different: nil slice vs empty slice
var s1 []int        // nil
var s2 = []int{}    // empty but not nil

fmt.Println(s1 == nil)  // true
fmt.Println(s2 == nil)  // false

// Both can be iterated: for range works on both
for _, v := range s1 {} // Works
for _, v := range s2 {} // Works
```

### 4. Interface{} Type Assertions
```go
// Risky
value := data.(string)  // Panics if not string

// Safe
value, ok := data.(string)
if !ok {
    // Handle error
}
```

## 10. Useful Go Tools

### go mod
```bash
go mod init               # Initialize module
go mod tidy              # Remove unused dependencies
go mod download          # Download dependencies
go mod vendor            # Vendor dependencies
go mod graph             # View dependency graph
```

### go build/run
```bash
go build                 # Build binary
go build -o app         # Build with custom name
go run main.go          # Run directly
go install              # Build and install
```

### Profiling
```bash
# CPU profiling
go test -cpuprofile=cpu.prof -memprofile=mem.prof ./...

# Analyze profile
go tool pprof cpu.prof
```

## 11. Production Readiness Checklist

- [ ] Error handling implemented throughout
- [ ] Logging configured (structured logging preferred)
- [ ] Configuration externalized (environment variables, config files)
- [ ] Graceful shutdown implemented
- [ ] Health check endpoints added
- [ ] Metrics and monitoring integrated
- [ ] Rate limiting implemented
- [ ] Input validation implemented
- [ ] Security headers set (if HTTP service)
- [ ] Database connections pooled
- [ ] Tests written (unit, integration, end-to-end)
- [ ] API documentation complete
- [ ] Docker container created
- [ ] CI/CD pipeline configured

## 12. Resources

- **Official Docs**: https://golang.org/doc/
- **Effective Go**: https://golang.org/doc/effective_go
- **Go Code Review**: https://github.com/golang/go/wiki/CodeReviewComments
- **awesome-go**: https://awesome-go.com/
- **Go by Example**: https://gobyexample.com/