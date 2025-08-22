# Go Database Project

A simple, file-based JSON database implementation in Go. This project provides a lightweight database solution that stores data as JSON files on the filesystem.

## Features

- **File-based Storage**: Data is stored as JSON files in a directory structure
- **CRUD Operations**: Create, Read, Update, and Delete operations for data records
- **Thread-safe**: Uses mutexes to ensure thread safety for concurrent operations
- **Simple API**: Easy-to-use interface for database operations
- **JSON Format**: Human-readable JSON storage format
- **No External Dependencies**: Minimal dependencies, only uses standard library and logging

## Project Structure

```
golang-database/
├── main.go          # Main application with example usage
├── go.mod           # Go module file
├── go.sum           # Dependency checksums
├── README.md        # This file
└── users/           # Database directory (created at runtime)
    ├── 1_john.json
    ├── 2_jane.json
    ├── 3_jim.json
    └── ...
```

## Installation

1. **Prerequisites**: Make sure you have Go 1.25.0 or later installed
   ```bash
   go version
   ```

2. **Clone or navigate to the project directory**
   ```bash
   cd golang-database
   ```

3. **Install dependencies**
   ```bash
   go mod tidy
   ```

## Usage

### Running the Example

```bash
go run main.go
```

This will:
- Create a database in the current directory
- Add 7 sample users with duplicate names (handled with unique IDs)
- Read all users and display them
- Demonstrate the database operations

### Database Operations

#### Creating a Database Instance

```go
db, err := New("./database", nil)
if err != nil {
    log.Fatal(err)
}
```

#### Writing Data

```go
user := User{
    Name:    "John Doe",
    Age:     "30",
    Contact: "1234567890",
    Company: "ABC Corp",
    Address: Address{
        City:    "New York",
        State:   "NY",
        Country: "USA",
        PinCode: "10001",
    },
}

err := db.Write("users", "john_doe", user)
```

#### Reading Data

```go
var user User
err := db.Read("users", "john_doe", &user)
```

#### Reading All Records

```go
records, err := db.ReadAll("users")
if err != nil {
    log.Fatal(err)
}

for _, record := range records {
    var user User
    json.Unmarshal([]byte(record), &user)
    fmt.Println(user)
}
```

#### Deleting Data

```go
err := db.Delete("users", "john_doe")
```

## Data Models

### User Structure

```go
type User struct {
    Name    string      `json:"name"`
    Age     json.Number `json:"age"`
    Contact string      `json:"contact"`
    Company string      `json:"company"`
    Address Address     `json:"address"`
}
```

### Address Structure

```go
type Address struct {
    City    string      `json:"city"`
    State   string      `json:"state"`
    Country string      `json:"country"`
    PinCode json.Number `json:"pin_code"`
}
```

## Key Features Explained

### 1. Unique ID Generation
The project handles duplicate names by generating unique identifiers:
```go
uniqueID := fmt.Sprintf("%d_%s", i+1, value.Name)
```
This ensures that even users with the same name get separate records.

### 2. Thread Safety
The database uses mutexes to ensure thread-safe operations:
- Global mutex for managing collection mutexes
- Per-collection mutexes for individual operations

### 3. File-based Storage
- Each record is stored as a separate JSON file
- Files are organized in collection directories
- Atomic writes using temporary files and rename operations

## Dependencies

- **github.com/jcelliott/lumber**: For structured logging
- **Standard library packages**: 
  - `encoding/json` for JSON operations
  - `os` for file operations
  - `path/filepath` for path handling
  - `sync` for thread safety

## Example Output

When you run the program, you'll see output like:
```
[7/8]0xc00007c400
[{john 25 1234567890 ABC Inc {New York NY USA 10001}} {jane 30 0987654321 XYZ Corp {Los Angeles CA USA 90001}} {jim 35 1111111111 DEF Inc {Chicago IL USA 60601}} {jill 40 2222222222 GHI Corp {Houston TX USA 77001}} {jack 45 3333333333 JKL Inc {Miami FL USA 33101}} {jane 50 4444444444 MNO Corp {Seattle WA USA 98101}} {jim 55 5555555555 PQR Inc {San Francisco CA USA 94101}}]
```

This shows all 7 users being successfully stored and retrieved, including the two users named "jane" and two users named "jim".

## Future Improvements

Potential enhancements for this project:

1. **REST API**: Add HTTP endpoints for database operations
2. **Search & Filtering**: Implement search by name, age, company, etc.
3. **Data Validation**: Add input validation and constraints
4. **Indexing**: Add indexes for better query performance
5. **Authentication**: Add user authentication and authorization
6. **Backup & Recovery**: Implement data backup and recovery features
7. **Configuration**: Add configuration file support
8. **Testing**: Add comprehensive unit and integration tests

## License

This project is open source and available under the MIT License.

## Contributing

Feel free to submit issues, feature requests, or pull requests to improve this project.
