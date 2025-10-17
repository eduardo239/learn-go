# Go Environment Setup and Installation

Complete guide to setting up your Go development environment.

## 1. System Requirements

### Minimum Requirements
- **RAM**: 512 MB minimum (1 GB recommended)
- **Disk Space**: 500 MB for Go installation
- **Processor**: Any modern CPU
- **OS**: Windows, macOS, Linux, FreeBSD

### Supported Operating Systems
- Windows 7 or later (32-bit and 64-bit)
- macOS 10.8 or later
- Linux (various distributions)
- FreeBSD 11.2 or later

## 2. Installation

### Windows

#### Option 1: Using Installer (Recommended)
1. Download the installer from [golang.org/dl](https://golang.org/dl/)
2. Look for the `.msi` file for Windows
3. Double-click the installer
4. Follow the installation wizard
5. Default installation path: `C:\Program Files\Go`

#### Option 2: Using Chocolatey
```powershell
choco install golang
```

#### Option 3: Manual Installation
1. Download the `.zip` file
2. Extract to a location (e.g., `C:\Program Files\Go`)
3. Set environment variables (see Configuration section)

### macOS

#### Option 1: Using Installer
1. Download the `.pkg` file from [golang.org/dl](https://golang.org/dl/)
2. Double-click the file
3. Follow the installation wizard
4. Default installation path: `/usr/local/go`

#### Option 2: Using Homebrew
```bash
brew install go
```

#### Option 3: Using MacPorts
```bash
sudo port install go
```

### Linux

#### Ubuntu/Debian
```bash
# Using apt
sudo apt-get update
sudo apt-get install golang-go

# Or download binary
cd /tmp
wget https://golang.org/dl/go1.21.linux-amd64.tar.gz
sudo tar -C /usr/local -xzf go1.21.linux-amd64.tar.gz
```

#### Fedora/RHEL/CentOS
```bash
sudo dnf install golang

# Or download binary
cd /tmp
wget https://golang.org/dl/go1.21.linux-amd64.tar.gz
sudo tar -C /usr/local -xzf go1.21.linux-amd64.tar.gz
```

#### Arch Linux
```bash
sudo pacman -S go
```

## 3. Configuration

### Environment Variables

#### Windows (Using GUI)
1. Press `Win + X`, select "System"
2. Click "Advanced system settings"
3. Click "Environment Variables"
4. Under "System variables", click "New"
5. Add the following:
   - Variable name: `GOROOT`, Value: `C:\Program Files\Go`
   - Variable name: `GOPATH`, Value: `C:\Users\YourUsername\go`
   - Variable name: `GO111MODULE`, Value: `on`
6. Edit `Path` variable and add: `C:\Program Files\Go\bin`

#### Windows (PowerShell)
```powershell
# Set GOROOT
[Environment]::SetEnvironmentVariable("GOROOT", "C:\Program Files\Go", "Machine")

# Set GOPATH
[Environment]::SetEnvironmentVariable("GOPATH", "$env:USERPROFILE\go", "Machine")

# Add to PATH
$path = [Environment]::GetEnvironmentVariable("PATH", "Machine")
$path += ";C:\Program Files\Go\bin"
[Environment]::SetEnvironmentVariable("PATH", $path, "Machine")
```

#### macOS/Linux (Bash)
```bash
# Add to ~/.bash_profile or ~/.bashrc
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export PATH=$PATH:$GOROOT/bin:$GOPATH/bin
export GO111MODULE=on
```

#### macOS/Linux (Zsh)
```bash
# Add to ~/.zshrc
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export PATH=$PATH:$GOROOT/bin:$GOPATH/bin
export GO111MODULE=on
```

### Environment Variables Explanation

| Variable | Purpose |
|----------|---------|
| `GOROOT` | Installation directory of Go |
| `GOPATH` | Workspace directory for Go projects |
| `GO111MODULE` | Enable module mode (`on` recommended) |
| `GOOS` | Target operating system for build |
| `GOARCH` | Target architecture for build |

## 4. Verification

### Verify Installation
```bash
# Check Go version
go version

# Output should be similar to:
# go version go1.21 linux/amd64
```

### Check Environment
```bash
# Display Go environment
go env

# Check specific variable
go env GOROOT
go env GOPATH
go env GOOS
go env GOARCH
```

### Create and Run Test Program

**hello.go**
```go
package main

import "fmt"

func main() {
    fmt.Println("Hello, Go!")
}
```

Run it:
```bash
go run hello.go

# Output: Hello, Go!
```

## 5. IDE and Editor Setup

### VS Code (Recommended)

1. **Install VS Code**: Download from [code.visualstudio.com](https://code.visualstudio.com/)

2. **Install Go Extension**:
   - Open VS Code
   - Go to Extensions (Ctrl+Shift+X)
   - Search for "Go"
   - Install the official Go extension by Google

3. **Configuration** (`settings.json`):
```json
{
    "go.useLanguageServer": true,
    "go.lintOnSave": "package",
    "go.lintTool": "golangci-lint",
    "go.formatTool": "goimports",
    "go.formatOnSave": true,
    "[go]": {
        "editor.defaultFormatter": "golang.go",
        "editor.formatOnSave": true
    }
}
```

4. **Install Tools**:
```bash
go install github.com/golangci/golangci-lint/cmd/golangci-lint@latest
go install github.com/go-delve/delve/cmd/dlv@latest
```

### GoLand (JetBrains IDE)

1. Download from [jetbrains.com/go](https://www.jetbrains.com/go/)
2. Install and run
3. GoLand automatically detects Go installation
4. Create a new Go project

### Other Editors

- **Vim/Neovim**: Use vim-go plugin
- **Emacs**: Use go-mode
- **Sublime Text**: Use GoSublime
- **Atom**: Use go-plus

## 6. Project Structure

### Create Your First Project

```bash
# Create a new directory
mkdir hello-go
cd hello-go

# Initialize module
go mod init github.com/username/hello-go

# Create main.go
cat > main.go << 'EOF'
package main

import "fmt"

func main() {
    fmt.Println("Hello, Go!")
}
EOF

# Run the program
go run main.go

# Build executable
go build

# Run the executable
./hello-go      # On Linux/macOS
./hello-go.exe  # On Windows
```

### Standard Project Layout

```
myproject/
├── cmd/
│   └── myapp/
│       └── main.go           # Application entry point
├── pkg/
│   └── mylib/
│       └── mylib.go          # Public library code
├── internal/
│   └── config/
│       └── config.go         # Private code
├── go.mod                    # Module definition
├── go.sum                    # Dependency checksums
├── README.md
└── Makefile
```

## 7. Essential Commands

### Module Commands
```bash
go mod init <module>          # Initialize module
go mod tidy                   # Clean dependencies
go mod download               # Download dependencies
go mod vendor                 # Create vendor directory
go mod verify                 # Verify dependencies
go mod graph                  # View dependency graph
```

### Build and Run
```bash
go run main.go                # Run program directly
go build                      # Build binary
go build -o myapp            # Build with custom name
go install                    # Build and install to GOPATH/bin
```

### Manage Dependencies
```bash
go get <package>              # Download package
go get -u <package>           # Update package
go get -d <package>           # Download without installing
go get <package>@latest       # Get latest version
go get <package>@v1.2.3       # Get specific version
```

### Testing
```bash
go test ./...                 # Run all tests
go test -v ./...              # Verbose output
go test -cover ./...          # Show coverage
go test -run TestName         # Run specific test
```

### Code Quality
```bash
go fmt ./...                  # Format code
go vet ./...                  # Check for errors
go doc <package>              # View documentation
```

## 8. Package Management with go.mod

### Example go.mod File
```
module github.com/username/myproject

go 1.21

require (
    github.com/gorilla/mux v1.8.0
    github.com/sirupsen/logrus v1.9.0
)
```

### Add Dependency
```bash
go get github.com/gorilla/mux
```

### Update Dependencies
```bash
# Update all dependencies
go get -u ./...

# Update specific package
go get -u github.com/gorilla/mux

# Update to specific version
go get github.com/gorilla/mux@v1.8.0
```

### Remove Unused Dependencies
```bash
go mod tidy
```

## 9. Cross-Compilation

### Build for Different OS/Architecture

```bash
# Linux 64-bit
GOOS=linux GOARCH=amd64 go build

# Windows 64-bit
GOOS=windows GOARCH=amd64 go build -o myapp.exe

# macOS ARM64 (Apple Silicon)
GOOS=darwin GOARCH=arm64 go build

# List available platforms
go tool dist list
```

### Batch Build Script (bash)
```bash
#!/bin/bash

PLATFORMS="linux/amd64 windows/amd64 darwin/amd64 darwin/arm64"

for platform in $PLATFORMS
do
    GOOS="${platform%/*}"
    GOARCH="${platform#*/}"
    output_name='myapp'
    if [ $GOOS = "windows" ]; then
        output_name+='.exe'
    fi
    echo "Building for $GOOS/$GOARCH..."
    GOOS=$GOOS GOARCH=$GOARCH go build -o $output_name
done
```

## 10. Troubleshooting

### Problem: "go command not found"
**Solution:**
- Verify Go is installed: `which go` or `where go`
- Check PATH environment variable
- Restart terminal/IDE
- Reinstall if necessary

### Problem: "cannot import package"
**Solution:**
```bash
go mod tidy      # Clean dependencies
go get ./...     # Download all dependencies
```

### Problem: "GOPATH issues"
**Solution:**
```bash
# Verify GOPATH is set
go env GOPATH

# Set if missing
export GOPATH=$HOME/go  # Linux/macOS
# Or use GUI settings on Windows
```

### Problem: "Module not found"
**Solution:**
```bash
# Make sure you're in the correct directory
go mod init github.com/username/projectname
go mod tidy
```

### Problem: Permission Denied (Linux/macOS)
**Solution:**
```bash
sudo chown -R $(whoami) /usr/local/go
```

## 11. Keeping Go Updated

### Check Latest Version
Visit [golang.org/dl](https://golang.org/dl/) to check the latest version.

### Update Go

#### Windows
- Download latest installer and run it
- Or use: `choco upgrade golang`

#### macOS
```bash
brew upgrade go
```

#### Linux
```bash
# Download and extract
cd /tmp
wget https://golang.org/dl/go1.21.linux-amd64.tar.gz
sudo tar -C /usr/local -xzf go1.21.linux-amd64.tar.gz
```

## 12. Next Steps

After setup is complete:

1. ✅ Create your first Go program
2. ✅ Learn the [Basics](BASICS.md)
3. ✅ Explore [Features](FEATURES.md)
4. ✅ Master [Imports](IMPORTS.md)
5. ✅ Apply [Tips](TIPS.md) in your projects

Happy coding! 🚀