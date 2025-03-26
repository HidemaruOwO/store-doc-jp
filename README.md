# Store Library: Configuration Management for Go Applications

## Overview
The `store` library is a simple configuration management package for Go applications that supports multiple file formats and platform-independent configuration storage.

## Features
- Supports configuration storage in multiple formats:
  - JSON
  - YAML
  - TOML
- Platform-independent configuration file paths
  - Linux: `$XDG_CONFIG_HOME` or `$HOME/.config`
  - Windows: `%APPDATA%`
- Easy configuration loading and saving
- Extensible with custom file formats

## Installation
```bash
go get github.com/[repository]/store
```

## Quick Start

### 1. Initialize the Library
Before using the library, initialize it with your application name:
```go
store.Init("MyAwesomeApp")
```

### 2. Define a Configuration Struct
```go
type AppConfig struct {
    DatabaseURL string `json:"database_url"`
    LogLevel    string `json:"log_level"`
    MaxRetries  int    `json:"max_retries"`
}
```

### 3. Loading Configuration
```go
config := &AppConfig{}
err := store.Load("config.json", config)
if err != nil {
    // Handle error
}
```

### 4. Saving Configuration
```go
config := &AppConfig{
    DatabaseURL: "postgres://localhost/mydb",
    LogLevel:    "info",
    MaxRetries:  3,
}
err := store.Save("config.json", config)
if err != nil {
    // Handle error
}
```

## Advanced Usage

### Custom File Formats
You can register custom file formats:
```go
store.Register("xml", 
    xml.Marshal,     // Marshaling function
    xml.Unmarshal   // Unmarshaling function
)
```

### Loading with Custom Unmarshaler
```go
err := store.LoadWith("config.custom", config, customUnmarshalFunc)
```

### Saving with Custom Marshaler
```go
err := store.SaveWith("config.custom", config, customMarshalFunc)
```

## Important Notes
- Always call `store.Init()` before using other library functions
- Supports `.json`, `.yaml`, `.yml`, and `.toml` out of the box
- Configuration files are created automatically if they don't exist
- Panics if application name is not set before usage

## Error Handling
- Handles file read/write errors
- Provides detailed error messages for marshaling/unmarshaling failures

## Platform Support
- Fully compatible with Linux and Windows
- Automatically uses appropriate configuration directories

## Deprecated Methods
- `SetApplicationName()` is deprecated; use `Init()` instead

## Function Examples

### `Init(application string)`
```go
// Initialize the store for your application
store.Init("MyAwesomeApp")
// This sets up configuration storage in:
// - Linux: ~/.config/MyAwesomeApp/
// - Windows: %APPDATA%/MyAwesomeApp/
```

### `Load(path string, v interface{}) error`
```go
type ServerConfig struct {
    Host string `json:"host"`
    Port int    `json:"port"`
}

config := &ServerConfig{}
err := store.Load("server.json", config)
if err != nil {
    log.Fatal(err)
}
// If server.json doesn't exist, an empty configuration 
// file will be created automatically
```

### `Save(path string, v interface{}) error`
```go
config := &ServerConfig{
    Host: "localhost",
    Port: 8080,
}

err := store.Save("server.json", config)
if err != nil {
    log.Fatal(err)
}
// This will create server.json in the application's config directory
```

### `LoadWith(path string, v interface{}, um UnmarshalFunc) error`
```go
// Custom unmarshaling example
customConfig := &MySpecialConfig{}
err := store.LoadWith("custom.cfg", 
    customConfig, 
    func(data []byte, v interface{}) error {
        // Implement custom unmarshaling logic
        return customUnmarshalFunc(data, v)
    }
)
```

### `SaveWith(path string, v interface{}, m MarshalFunc) error`
```go
err := store.SaveWith("custom.cfg", 
    myConfig, 
    func(v interface{}) ([]byte, error) {
        // Implement custom marshaling logic
        return customMarshalFunc(v)
    }
)
```

### `Register(extension string, m MarshalFunc, um UnmarshalFunc)`
```go
// Adding support for a new configuration format
store.Register("xml", 
    xml.Marshal,     // Marshaling function
    xml.Unmarshal   // Unmarshaling function
)

// Now you can use .xml files with Load and Save
type XMLConfig struct {
    XMLName xml.Name `xml:"config"`
    Key     string   `xml:"key"`
}
```

## Complete Example
```go
package main

import (
    "log"
    "github.com/yourname/store"
)

type AppConfig struct {
    DatabaseURL string `json:"database_url"`
    LogLevel    string `json:"log_level"`
    MaxRetries  int    `json:"max_retries"`
}

func main() {
    // Initialize the store
    store.Init("MyApp")

    // Load existing configuration
    config := &AppConfig{}
    err := store.Load("config.json", config)
    if err != nil {
        log.Fatal(err)
    }

    // Modify configuration
    config.LogLevel = "debug"

    // Save updated configuration
    err = store.Save("config.json", config)
    if err != nil {
        log.Fatal(err)
    }
}
```

## Key Considerations
- Always call `store.Init()` before using other functions
- Supports JSON, YAML, and TOML out of the box
- Automatically creates configuration files if they don't exist
- Platform-independent configuration storage
