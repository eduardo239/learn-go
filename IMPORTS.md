# Packages and Imports in Go

Master Go's package and import system for better code organization and reusability.

## 1. Understanding Packages

A package is a collection of Go source files in a single directory. Every Go file belongs to a package.

### Package Declaration
```go
// Every file must start with package declaration
package main  // Executable package
package utils // Library package
```

### Package Types
- **main**: Special package - only this can have a `main()` function (entry point)
- **Library packages**: Regular packages for reusable code

## 2. Basic Imports

### Single Import
```go
import "fmt"
import "os"
```

### Multiple Imports
```go
import (
    "fmt"
    "os"
    "strings"
)
```

### Grouped Imports (Best Practice)
```go
import (
    // Standard library
    "fmt"
    "io/ioutil"
    "os"

    // Third-party packages
    "github.com/google/uuid"
    "github.com/sirupsen/logrus"
)
```

## 3. Standard Library Packages

### Most Common Packages

| Package | Purpose |
|---------|---------|
| `fmt` | Formatting and printing |
| `os` | Operating system interface |
| `io` | Input/output primitives |
| `bufio` | Buffered I/O |
| `strings` | String manipulation |
| `strconv` | String conversion |
| `time` | Time and duration |
| `math` | Mathematical functions |
| `sort` | Sorting collections |
| `sync` | Synchronization primitives |
| `context` | Context for goroutines |
| `net` | Network I/O |
| `http` | HTTP client/server |
| `encoding/json` | JSON encoding/decoding |
| `crypto` | Cryptographic functions |
| `database/sql` | SQL database interface |
| `testing` | Unit testing support |
| `log` | Logging |

## 4. Import Aliases

### Aliasing Standard Library
```go
import (
    f "fmt"  // Alias to 'f'
    "os"
)

func main() {
    f.Println("Hello")  // Using alias
}
```

### Aliasing Third-Party Packages
```go
import (
    jwt "github.com/golang-jwt/jwt/v4"
    "github.com/golang-jwt/jwt/v4/request"
)

// Use the alias
token, err := jwt.ParseWithClaims(...)
```

### Blank Import
```go
import _ "image/png"  // Imported for side effects (init functions)
```

### Dot Import (Not Recommended)
```go
import . "fmt"

// Now you can use functions directly
Println("Hello")  // Instead of fmt.Println()
```

## 5. Organizing Your Own Packages

### Project Structure
```
myproject/
├── main.go              // Package main
├── go.mod              // Module definition
├── go.sum              // Dependency checksums
├── utils/
│   └── helpers.go      // Package utils
├── models/
│   └── user.go         // Package models
└── handlers/
    └── api.go          // Package handlers
```

### Creating a Package

**utils/helpers.go**
```go
package utils

import "strings"

// Public function (starts with uppercase)
func ToUpperCase(s string) string {
    return strings.ToUpper(s)
}

// Private function (starts with lowercase)
func privateHelper() {
    // Only accessible within utils package
}
```

**main.go**
```go
package main

import (
    "fmt"
    "myproject/utils"  // Import local package
)

func main() {
    result := utils.ToUpperCase("hello")
    fmt.Println(result)  // HELLO
}
```

## 6. Module System (Go 1.11+)

### Go Modules Overview
Go modules manage dependencies and versions.

### Initialize a Module
```bash
go mod init github.com/username/projectname
```

This creates `go.mod`:
```
module github.com/username/projectname

go 1.21
```

### Adding Dependencies
```bash
go get github.com/google/uuid
```

Updates `go.mod` and `go.sum`:
```
module github.com/username/projectname

go 1.21

require github.com/google/uuid v1.3.0
```

## 7. Using Third-Party Packages

### Install a Package
```bash
go get github.com/gorilla/mux
go get github.com/sirupsen/logrus
```

### Use in Code
```go
package main

import (
    "net/http"
    "github.com/gorilla/mux"
)

func main() {
    router := mux.NewRouter()
    router.HandleFunc("/", homeHandler)
    http.ListenAndServe(":8080", router)
}

func homeHandler(w http.ResponseWriter, r *http.Request) {
    w.Write([]byte("Hello!"))
}
```

## 8. Package Visibility

### Exported vs Unexported

In Go, capitalization determines visibility:

```go
package utils

// Exported - accessible from other packages
func PublicFunction() {
    privateHelper()  // Can call private function
}

var PublicVariable = "visible"

// Unexported - only accessible within package
func privateFunction() {
}

var privateVariable = "hidden"

type PublicStruct struct {
    PublicField  string  // Exported field
    privateField string  // Unexported field
}
```

## 9. Working with HTTP Packages

### HTTP Server
```go
package main

import (
    "fmt"
    "net/http"
)

func main() {
    http.HandleFunc("/", handler)
    http.ListenAndServe(":8080", nil)
}

func handler(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, "Hello, %s!", r.URL.Path[1:])
}
```

### HTTP Client
```go
import "net/http"

resp, err := http.Get("https://api.example.com/data")
if err != nil {
    // Handle error
}
defer resp.Body.Close()

body, _ := ioutil.ReadAll(resp.Body)
```

## 10. JSON Encoding/Decoding

### JSON Package
```go
import "encoding/json"

type User struct {
    Name  string `json:"name"`
    Email string `json:"email"`
    Age   int    `json:"age"`
}

// Marshal (Go to JSON)
user := User{"John", "john@example.com", 30}
jsonData, _ := json.Marshal(user)
fmt.Println(string(jsonData))

// Unmarshal (JSON to Go)
var userData User
json.Unmarshal(jsonData, &userData)
```

## 11. Testing Packages

### Testing
```go
// math_test.go
package math

import "testing"

func TestAdd(t *testing.T) {
    result := Add(2, 2)
    if result != 4 {
        t.Errorf("Expected 4, got %d", result)
    }
}
```

### Run Tests
```bash
go test ./...           # Test all packages
go test -v ./...       # Verbose output
go test -run TestAdd   # Run specific test
```

## 12. Useful Commands

```bash
go mod init <module>        # Initialize module
go get <package>            # Download package
go get -u <package>         # Update package
go mod tidy                  # Remove unused dependencies
go mod graph                 # View dependency graph
go list -m all              # List all dependencies
go doc <package>            # View documentation
```

## 13. Best Practices

1. **Group Related Imports**
   ```go
   import (
       // Standard library
       "fmt"
       "os"
       
       // Third-party
       "github.com/package"
   )
   ```

2. **Avoid Wildcard Imports**
   - Don't use `import . "package"` in production code

3. **Use Version Constraints**
   - In `go.mod`, pin to stable versions

4. **Keep Packages Focused**
   - One responsibility per package

5. **Document Your Packages**
   - Add package-level comments

6. **Use Meaningful Package Names**
   - Short, lowercase, no underscores

## 14. Common Import Errors

### Error: Cannot find package
```
cannot find package
```
**Solution:** Run `go get <package>`

### Error: Unused import
```
imported and not used
```
**Solution:** Remove or use the import

### Error: Import cycle
```
import cycle not allowed
```
**Solution:** Reorganize packages to eliminate circular dependencies