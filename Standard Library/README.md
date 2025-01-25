Certainly! Let's break down the concepts discussed in Chapter 13 of the Go programming language, focusing on the standard library, particularly the `io`, `time`, and related packages. We'll use examples to illustrate each concept.

### 1. **The `io` Package and Interfaces**

The `io` package in Go provides basic interfaces for input and output operations. The most commonly used interfaces are `io.Reader` and `io.Writer`.

#### **`io.Reader` Interface**

The `io.Reader` interface is used for reading data from a source. It has a single method:

```go
type Reader interface {
    Read(p []byte) (n int, err error)
}
```

- **Example**: Reading from a string using `strings.NewReader`.

```go
package main

import (
    "fmt"
    "io"
    "strings"
)

func countLetters(r io.Reader) (map[string]int, error) {
    buf := make([]byte, 2048)
    out := map[string]int{}
    for {
        n, err := r.Read(buf)
        for _, b := range buf[:n] {
            if (b >= 'A' && b <= 'Z') || (b >= 'a' && b <= 'z') {
                out[string(b)]++
            }
        }
        if err == io.EOF {
            return out, nil
        }
        if err != nil {
            return nil, err
        }
    }
}

func main() {
    s := "The quick brown fox jumped over the lazy dog"
    sr := strings.NewReader(s)
    counts, err := countLetters(sr)
    if err != nil {
        fmt.Println("Error:", err)
        return
    }
    fmt.Println(counts)
}
```

**Output**:
```go
map[T:1 h:1 e:2 q:1 u:2 i:1 c:1 k:1 b:1 r:2 o:4 w:1 n:1 f:1 x:1 j:1 m:1 p:1 d:2 v:1 l:1 a:1 z:1 y:1 g:1]
```

#### **`io.Writer` Interface**

The `io.Writer` interface is used for writing data to a destination. It has a single method:

```go
type Writer interface {
    Write(p []byte) (n int, err error)
}
```

- **Example**: Writing to a file using `os.File`.

```go
package main

import (
    "fmt"
    "os"
)

func main() {
    file, err := os.Create("output.txt")
    if err != nil {
        fmt.Println("Error:", err)
        return
    }
    defer file.Close()

    data := []byte("Hello, World!")
    n, err := file.Write(data)
    if err != nil {
        fmt.Println("Error:", err)
        return
    }
    fmt.Printf("Wrote %d bytes to file\n", n)
}
```

**Output**:
```go
Wrote 13 bytes to file
```

### 2. **Chaining `io.Reader` and `io.Writer`**

You can chain `io.Reader` and `io.Writer` implementations together using decorators like `gzip.NewReader`.

- **Example**: Reading from a gzip-compressed file.

```go
package main

import (
    "compress/gzip"
    "fmt"
    "io"
    "os"
)

func buildGZipReader(fileName string) (*gzip.Reader, func(), error) {
    r, err := os.Open(fileName)
    if err != nil {
        return nil, nil, err
    }
    gr, err := gzip.NewReader(r)
    if err != nil {
        return nil, nil, err
    }
    return gr, func() {
        gr.Close()
        r.Close()
    }, nil
}

func main() {
    r, closer, err := buildGZipReader("data.txt.gz")
    if err != nil {
        fmt.Println("Error:", err)
        return
    }
    defer closer()

    counts, err := countLetters(r)
    if err != nil {
        fmt.Println("Error:", err)
        return
    }
    fmt.Println(counts)
}
```

### 3. **The `time` Package**

The `time` package provides functionality for measuring and displaying time.

#### **`time.Duration`**

A `time.Duration` represents a length of time, with nanosecond precision.

- **Example**: Representing a duration of 2 hours and 30 minutes.

```go
package main

import (
    "fmt"
    "time"
)

func main() {
    d := 2*time.Hour + 30*time.Minute
    fmt.Println("Duration:", d)
}
```

**Output**:
```go
Duration: 2h30m0s
```

#### **`time.Time`**

A `time.Time` represents a specific point in time, including a time zone.

- **Example**: Getting the current time and formatting it.

```go
package main

import (
    "fmt"
    "time"
)

func main() {
    now := time.Now()
    fmt.Println("Current time:", now.Format("2006-01-02 15:04:05 MST"))
}
```

**Output**:
```go
Current time: 2023-03-13 12:34:56 UTC
```

#### **Timers and Timeouts**

The `time` package also provides functions for creating timers and timeouts.

- **Example**: Using `time.After` to implement a timeout.

```go
package main

import (
    "fmt"
    "time"
)

func main() {
    ch := make(chan string)

    go func() {
        time.Sleep(2 * time.Second)
        ch <- "Message received"
    }()

    select {
    case msg := <-ch:
        fmt.Println(msg)
    case <-time.After(1 * time.Second):
        fmt.Println("Timeout!")
    }
}
```

**Output**:
```go
Timeout!
```

### 4. **Other Useful Functions**

#### **`io.Copy`**

The `io.Copy` function copies data from an `io.Reader` to an `io.Writer`.

- **Example**: Copying data from a file to another file.

```go
package main

import (
    "io"
    "os"
)

func main() {
    src, err := os.Open("source.txt")
    if err != nil {
        panic(err)
    }
    defer src.Close()

    dst, err := os.Create("destination.txt")
    if err != nil {
        panic(err)
    }
    defer dst.Close()

    _, err = io.Copy(dst, src)
    if err != nil {
        panic(err)
    }
}
```

#### **`io.ReadAll`**

The `io.ReadAll` function reads all data from an `io.Reader` into a byte slice.

- **Example**: Reading all data from a file.

```go
package main

import (
    "fmt"
    "io"
    "os"
)

func main() {
    file, err := os.Open("data.txt")
    if err != nil {
        panic(err)
    }
    defer file.Close()

    data, err := io.ReadAll(file)
    if err != nil {
        panic(err)
    }
    fmt.Println(string(data))
}
```

### Conclusion

The Go standard library is rich with functionality, especially in the `io` and `time` packages. By understanding and utilizing these packages, you can build robust and efficient applications. The examples provided should give you a good starting point for working with these packages in your own projects.



The `encoding/json` package in Go is used to encode and decode JSON data. JSON (JavaScript Object Notation) is a widely used data format for communication between services, especially in REST APIs. In this explanation, we'll cover how to use the `encoding/json` package to work with JSON data in Go, including struct tags, marshaling, unmarshaling, and custom JSON parsing.

---

### **1. Struct Tags for JSON Metadata**

Struct tags are used to control how Go structs are converted to and from JSON. They are written as backtick-enclosed strings after struct fields.

#### Example: Defining Structs with JSON Tags

```go
package main

import (
	"encoding/json"
	"fmt"
	"time"
)

type Order struct {
	ID          string    `json:"id"`
	DateOrdered time.Time `json:"date_ordered"`
	CustomerID  string    `json:"customer_id"`
	Items       []Item    `json:"items"`
}

type Item struct {
	ID   string `json:"id"`
	Name string `json:"name"`
}

func main() {
	// JSON data
	data := `{
		"id": "12345",
		"date_ordered": "2020-05-01T13:01:02Z",
		"customer_id": "3",
		"items": [
			{"id": "xyz123", "name": "Thing 1"},
			{"id": "abc789", "name": "Thing 2"}
		]
	}`

	// Unmarshal JSON into struct
	var order Order
	err := json.Unmarshal([]byte(data), &order)
	if err != nil {
		fmt.Println("Error:", err)
		return
	}

	// Print the struct
	fmt.Printf("%+v\n", order)
}
```

**Output**:
```go
{ID:12345 DateOrdered:2020-05-01 13:01:02 +0000 UTC CustomerID:3 Items:[{ID:xyz123 Name:Thing 1} {ID:abc789 Name:Thing 2}]}
```

#### Key Points:
- The `json` struct tag specifies the JSON field name.
- If no `json` tag is provided, the field name is used as-is (case-insensitive for unmarshaling).
- Use `,omitempty` to exclude empty fields from the JSON output.

---

### **2. Marshaling and Unmarshaling**

- **Marshaling**: Converting a Go struct to JSON.
- **Unmarshaling**: Converting JSON to a Go struct.

#### Example: Marshaling and Unmarshaling

```go
package main

import (
	"encoding/json"
	"fmt"
)

type Person struct {
	Name string `json:"name"`
	Age  int    `json:"age"`
}

func main() {
	// Marshaling
	p := Person{Name: "Alice", Age: 30}
	jsonData, err := json.Marshal(p)
	if err != nil {
		fmt.Println("Error:", err)
		return
	}
	fmt.Println("Marshaled JSON:", string(jsonData))

	// Unmarshaling
	var p2 Person
	err = json.Unmarshal(jsonData, &p2)
	if err != nil {
		fmt.Println("Error:", err)
		return
	}
	fmt.Printf("Unmarshaled Struct: %+v\n", p2)
}
```

**Output**:
```go
Marshaled JSON: {"name":"Alice","age":30}
Unmarshaled Struct: {Name:Alice Age:30}
```

---

### **3. JSON, Readers, and Writers**

The `encoding/json` package provides `json.Decoder` and `json.Encoder` for working with `io.Reader` and `io.Writer`.

#### Example: Using `json.Encoder` and `json.Decoder`

```go
package main

import (
	"encoding/json"
	"fmt"
	"os"
)

type Person struct {
	Name string `json:"name"`
	Age  int    `json:"age"`
}

func main() {
	// Create a temporary file
	tmpFile, err := os.CreateTemp("", "sample-")
	if err != nil {
		panic(err)
	}
	defer os.Remove(tmpFile.Name())

	// Encode JSON to file
	toFile := Person{Name: "Bob", Age: 25}
	err = json.NewEncoder(tmpFile).Encode(toFile)
	if err != nil {
		panic(err)
	}
	tmpFile.Close()

	// Decode JSON from file
	tmpFile2, err := os.Open(tmpFile.Name())
	if err != nil {
		panic(err)
	}
	var fromFile Person
	err = json.NewDecoder(tmpFile2).Decode(&fromFile)
	if err != nil {
		panic(err)
	}
	tmpFile2.Close()

	fmt.Printf("Read from file: %+v\n", fromFile)
}
```

**Output**:
```go
Read from file: {Name:Bob Age:25}
```

---

### **4. Encoding and Decoding JSON Streams**

When working with multiple JSON objects in a stream, you can use `json.Decoder` to process them one at a time.

#### Example: Decoding a JSON Stream

```go
package main

import (
	"encoding/json"
	"errors"
	"fmt"
	"io"
	"strings"
)

type Person struct {
	Name string `json:"name"`
	Age  int    `json:"age"`
}

func main() {
	// JSON stream
	streamData := `{"name": "Fred", "age": 40}
{"name": "Mary", "age": 21}
{"name": "Pat", "age": 30}`

	dec := json.NewDecoder(strings.NewReader(streamData))
	for {
		var p Person
		err := dec.Decode(&p)
		if err != nil {
			if errors.Is(err, io.EOF) {
				break
			}
			panic(err)
		}
		fmt.Printf("Decoded: %+v\n", p)
	}
}
```

**Output**:
```go
Decoded: {Name:Fred Age:40}
Decoded: {Name:Mary Age:21}
Decoded: {Name:Pat Age:30}
```

---

### **5. Custom JSON Parsing**

Sometimes, you need to customize how JSON is parsed or generated. You can do this by implementing the `json.Marshaler` and `json.Unmarshaler` interfaces.

#### Example: Custom Time Format

```go
package main

import (
	"encoding/json"
	"fmt"
	"time"
)

type RFC822ZTime struct {
	time.Time
}

func (rt RFC822ZTime) MarshalJSON() ([]byte, error) {
	out := rt.Time.Format(time.RFC822Z)
	return []byte(`"` + out + `"`), nil
}

func (rt *RFC822ZTime) UnmarshalJSON(b []byte) error {
	if string(b) == "null" {
		return nil
	}
	t, err := time.Parse(`"`+time.RFC822Z+`"`, string(b))
	if err != nil {
		return err
	}
	*rt = RFC822ZTime{t}
	return nil
}

type Order struct {
	ID          string      `json:"id"`
	DateOrdered RFC822ZTime `json:"date_ordered"`
}

func main() {
	// Marshaling
	order := Order{ID: "123", DateOrdered: RFC822ZTime{time.Now()}}
	jsonData, err := json.Marshal(order)
	if err != nil {
		panic(err)
	}
	fmt.Println("Marshaled JSON:", string(jsonData))

	// Unmarshaling
	var newOrder Order
	err = json.Unmarshal(jsonData, &newOrder)
	if err != nil {
		panic(err)
	}
	fmt.Printf("Unmarshaled Struct: %+v\n", newOrder)
}
```

**Output**:
```go
Marshaled JSON: {"id":"123","date_ordered":"01 Jan 23 15:04 -0700"}
Unmarshaled Struct: {ID:123 DateOrdered:{Time:2023-01-01 15:04:00 -0700 -0700}}
```

---

### **6. Using Maps for Dynamic JSON**

For dynamic or unknown JSON structures, you can use `map[string]any`.

#### Example: Using `map[string]any`

```go
package main

import (
	"encoding/json"
	"fmt"
)

func main() {
	// JSON data
	data := `{"name": "Alice", "age": 30, "is_student": true}`

	// Unmarshal into map
	var result map[string]any
	err := json.Unmarshal([]byte(data), &result)
	if err != nil {
		panic(err)
	}

	// Access values
	fmt.Println("Name:", result["name"])
	fmt.Println("Age:", result["age"])
	fmt.Println("Is Student:", result["is_student"])
}
```

**Output**:
```go
Name: Alice
Age: 30
Is Student: true
```

---

### **Conclusion**

The `encoding/json` package is a powerful tool for working with JSON in Go. By using struct tags, marshaling, unmarshaling, and custom parsing, you can easily integrate JSON into your Go applications. Whether you're working with simple structs or complex JSON streams, Go provides the tools you need to handle JSON effectively.




The `net/http` package in Go is a powerful and production-ready library for building HTTP clients and servers. It supports HTTP/2 and provides a robust set of tools for handling HTTP requests and responses. Below, we'll break down the key concepts and provide examples for each.

---

### **1. HTTP Client**

The `net/http` package provides a `Client` type for making HTTP requests. It is recommended to create your own `http.Client` instance instead of using the default `http.DefaultClient`, as the default client has no timeout, which can lead to resource leaks.

#### Example: Creating a Custom HTTP Client

```go
package main

import (
	"context"
	"fmt"
	"net/http"
	"time"
)

func main() {
	// Create a custom HTTP client with a timeout
	client := &http.Client{
		Timeout: 30 * time.Second,
	}

	// Create a new HTTP request
	req, err := http.NewRequestWithContext(
		context.Background(),
		http.MethodGet,
		"https://jsonplaceholder.typicode.com/todos/1",
		nil,
	)
	if err != nil {
		panic(err)
	}

	// Set custom headers
	req.Header.Add("X-My-Client", "Learning Go")

	// Send the request
	res, err := client.Do(req)
	if err != nil {
		panic(err)
	}
	defer res.Body.Close()

	// Check the response status
	if res.StatusCode != http.StatusOK {
		panic(fmt.Sprintf("unexpected status: got %v", res.Status))
	}

	// Print the response headers
	fmt.Println("Content-Type:", res.Header.Get("Content-Type"))

	// Decode the JSON response
	var data struct {
		UserID    int    `json:"userId"`
		ID        int    `json:"id"`
		Title     string `json:"title"`
		Completed bool   `json:"completed"`
	}
	err = json.NewDecoder(res.Body).Decode(&data)
	if err != nil {
		panic(err)
	}

	// Print the decoded data
	fmt.Printf("%+v\n", data)
}
```

**Output**:
```go
Content-Type: application/json; charset=utf-8
{UserID:1 ID:1 Title:delectus aut autem Completed:false}
```

#### Key Points:
- Use `http.NewRequestWithContext` to create a request with a context for cancellation and timeouts.
- Set custom headers using the `Header` field of the request.
- Use `client.Do(req)` to send the request and get the response.
- Always close the response body using `defer res.Body.Close()`.
- Decode JSON responses using `json.NewDecoder`.

---

### **2. HTTP Server**

The `net/http` package also provides tools for building HTTP servers. The server is built around the `http.Server` type and the `http.Handler` interface.

#### Example: Creating a Simple HTTP Server

```go
package main

import (
	"fmt"
	"net/http"
	"time"
)

type HelloHandler struct{}

func (hh HelloHandler) ServeHTTP(w http.ResponseWriter, r *http.Request) {
	w.Write([]byte("Hello!\n"))
}

func main() {
	// Create a new HTTP server
	s := http.Server{
		Addr:         ":8080",
		ReadTimeout:  30 * time.Second,
		WriteTimeout: 90 * time.Second,
		IdleTimeout:  120 * time.Second,
		Handler:      HelloHandler{},
	}

	// Start the server
	fmt.Println("Server is listening on :8080...")
	err := s.ListenAndServe()
	if err != nil {
		panic(err)
	}
}
```

**Output**:
```bash
Server is listening on :8080...
```

#### Key Points:
- The `http.Server` struct is used to configure the server (e.g., timeouts, address, and handler).
- The `http.Handler` interface requires implementing the `ServeHTTP` method.
- Use `s.ListenAndServe()` to start the server.

---

### **3. Request Routing with `http.ServeMux`**

The `http.ServeMux` type is a request router that dispatches requests to handlers based on the URL path.

#### Example: Using `http.ServeMux` for Routing

```go
package main

import (
	"fmt"
	"net/http"
)

func main() {
	// Create a new ServeMux
	mux := http.NewServeMux()

	// Register handlers
	mux.HandleFunc("/hello", func(w http.ResponseWriter, r *http.Request) {
		w.Write([]byte("Hello!\n"))
	})

	mux.HandleFunc("/goodbye", func(w http.ResponseWriter, r *http.Request) {
		w.Write([]byte("Goodbye!\n"))
	})

	// Start the server
	fmt.Println("Server is listening on :8080...")
	err := http.ListenAndServe(":8080", mux)
	if err != nil {
		panic(err)
	}
}
```

**Output**:
```bash
Server is listening on :8080...
```

#### Key Points:
- Use `http.NewServeMux()` to create a new router.
- Register handlers using `mux.HandleFunc()`.
- Pass the `mux` instance to `http.ListenAndServe()`.

---

### **4. Middleware**

Middleware is used to perform cross-cutting concerns like logging, authentication, and timing. Middleware is a function that takes an `http.Handler` and returns an `http.Handler`.

#### Example: Creating Middleware

```go
package main

import (
	"fmt"
	"log"
	"net/http"
	"time"
)

// Middleware to log request time
func RequestTimer(h http.Handler) http.Handler {
	return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		start := time.Now()
		h.ServeHTTP(w, r)
		duration := time.Since(start)
		log.Printf("Request to %s took %v\n", r.URL.Path, duration)
	})
}

// Middleware to check a secret password
func TerribleSecurityProvider(password string) func(http.Handler) http.Handler {
	return func(h http.Handler) http.Handler {
		return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
			if r.Header.Get("X-Secret-Password") != password {
				w.WriteHeader(http.StatusUnauthorized)
				w.Write([]byte("You didn't give the secret password\n"))
				return
			}
			h.ServeHTTP(w, r)
		})
	}
}

func main() {
	// Create a new ServeMux
	mux := http.NewServeMux()

	// Register a handler with middleware
	terribleSecurity := TerribleSecurityProvider("GOPHER")
	mux.Handle("/hello", terribleSecurity(RequestTimer(
		http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
			w.Write([]byte("Hello!\n"))
		}),
	)))

	// Start the server
	fmt.Println("Server is listening on :8080...")
	err := http.ListenAndServe(":8080", mux)
	if err != nil {
		panic(err)
	}
}
```

**Output**:
```bash
Server is listening on :8080...
```

#### Key Points:
- Middleware is a function that wraps an `http.Handler`.
- Use closures to create configurable middleware (e.g., `TerribleSecurityProvider`).
- Chain middleware by nesting function calls.

---

### **5. Structured Logging with `log/slog`**

The `log/slog` package provides structured logging, which is useful for large-scale applications.

#### Example: Using `log/slog` for Structured Logging

```go
package main

import (
	"log/slog"
	"net/http"
	"os"
	"time"
)

func main() {
	// Create a structured logger
	options := &slog.HandlerOptions{Level: slog.LevelDebug}
	handler := slog.NewJSONHandler(os.Stderr, options)
	logger := slog.New(handler)

	// Log a message with fields
	logger.Info("user login",
		"id", "fred",
		"login_count", 20,
		"last_login", time.Now(),
	)

	// Log a debug message
	logger.Debug("debug message",
		"id", "fred",
		"last_login", time.Now(),
	)
}
```

**Output**:
```json
{"time":"2023-04-22T23:30:01.170243-04:00","level":"INFO","msg":"user login","id":"fred","login_count":20,"last_login":"2023-04-22T23:30:01.170243-04:00"}
{"time":"2023-04-22T23:30:01.170269-04:00","level":"DEBUG","msg":"debug message","id":"fred","last_login":"2023-04-22T23:30:01.170269-04:00"}
```

#### Key Points:
- Use `slog.NewJSONHandler` to create a JSON logger.
- Log messages with key-value pairs for structured data.
- Use `slog.LevelDebug` to control the logging level.

---

### **Conclusion**

The `net/http` package is a comprehensive library for building HTTP clients and servers in Go. By understanding its core components—such as `http.Client`, `http.Server`, `http.ServeMux`, middleware, and structured logging—you can build robust and scalable web applications. The examples provided should give you a solid foundation for working with HTTP in Go.



Let’s go through the exercises step by step and provide examples for each.

---

### **Exercise 1: Write a Web Server That Returns the Current Time in RFC 3339 Format**

The goal is to create a web server that responds to a `GET` request with the current time in RFC 3339 format.

#### Example Code:

```go
package main

import (
	"fmt"
	"net/http"
	"time"
)

func timeHandler(w http.ResponseWriter, r *http.Request) {
	if r.Method != http.MethodGet {
		http.Error(w, "Invalid request method", http.StatusMethodNotAllowed)
		return
	}

	// Get the current time in RFC 3339 format
	currentTime := time.Now().Format(time.RFC3339)

	// Write the time to the response
	w.Write([]byte(currentTime))
}

func main() {
	// Register the handler
	http.HandleFunc("/time", timeHandler)

	// Start the server
	fmt.Println("Server is listening on :8080...")
	err := http.ListenAndServe(":8080", nil)
	if err != nil {
		fmt.Println("Error starting server:", err)
	}
}
```

**How It Works**:
- The server listens on port `8080`.
- When a `GET` request is made to `/time`, it returns the current time in RFC 3339 format (e.g., `2023-10-05T14:30:00Z`).
- If the request method is not `GET`, it returns a `405 Method Not Allowed` error.

**Output**:
```bash
$ curl http://localhost:8080/time
2023-10-05T14:30:00Z
```

---

### **Exercise 2: Add Middleware for Structured Logging of IP Addresses**

The goal is to add middleware that logs the IP address of each incoming request using structured logging.

#### Example Code:

```go
package main

import (
	"fmt"
	"log/slog"
	"net"
	"net/http"
	"time"
)

// Middleware to log the IP address of each request
func loggingMiddleware(next http.Handler) http.Handler {
	return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		// Get the IP address of the client
		ip, _, err := net.SplitHostPort(r.RemoteAddr)
		if err != nil {
			slog.Error("Failed to parse IP address", "error", err)
			http.Error(w, "Internal Server Error", http.StatusInternalServerError)
			return
		}

		// Log the IP address
		slog.Info("Incoming request", "ip", ip, "path", r.URL.Path)

		// Call the next handler
		next.ServeHTTP(w, r)
	})
}

func timeHandler(w http.ResponseWriter, r *http.Request) {
	if r.Method != http.MethodGet {
		http.Error(w, "Invalid request method", http.StatusMethodNotAllowed)
		return
	}

	// Get the current time in RFC 3339 format
	currentTime := time.Now().Format(time.RFC3339)

	// Write the time to the response
	w.Write([]byte(currentTime))
}

func main() {
	// Create a structured logger
	logger := slog.New(slog.NewJSONHandler(os.Stderr, nil))
	slog.SetDefault(logger)

	// Wrap the timeHandler with the logging middleware
	http.Handle("/time", loggingMiddleware(http.HandlerFunc(timeHandler)))

	// Start the server
	fmt.Println("Server is listening on :8080...")
	err := http.ListenAndServe(":8080", nil)
	if err != nil {
		slog.Error("Error starting server", "error", err)
	}
}
```

**How It Works**:
- The `loggingMiddleware` logs the IP address and request path using structured logging.
- The `slog` package is used for JSON-structured logging.
- The middleware is applied to the `/time` endpoint.

**Output**:
```bash
$ curl http://localhost:8080/time
2023-10-05T14:30:00Z
```

**Log Output**:
```json
{"time":"2023-10-05T14:30:00Z","level":"INFO","msg":"Incoming request","ip":"127.0.0.1","path":"/time"}
```

---

### **Exercise 3: Add Support for Returning Time as JSON**

The goal is to modify the server to return the time in JSON format when the `Accept` header is set to `application/json`. The JSON structure should include details like the day of the week, day of the month, month, year, hour, minute, and second.

#### Example Code:

```go
package main

import (
	"encoding/json"
	"fmt"
	"log/slog"
	"net"
	"net/http"
	"time"
)

// Middleware to log the IP address of each request
func loggingMiddleware(next http.Handler) http.Handler {
	return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		ip, _, err := net.SplitHostPort(r.RemoteAddr)
		if err != nil {
			slog.Error("Failed to parse IP address", "error", err)
			http.Error(w, "Internal Server Error", http.StatusInternalServerError)
			return
		}

		slog.Info("Incoming request", "ip", ip, "path", r.URL.Path)
		next.ServeHTTP(w, r)
	})
}

func timeHandler(w http.ResponseWriter, r *http.Request) {
	if r.Method != http.MethodGet {
		http.Error(w, "Invalid request method", http.StatusMethodNotAllowed)
		return
	}

	// Get the current time
	now := time.Now()

	// Check the Accept header
	accept := r.Header.Get("Accept")
	if accept == "application/json" {
		// Return JSON response
		response := map[string]interface{}{
			"day_of_week":  now.Weekday().String(),
			"day_of_month": now.Day(),
			"month":        now.Month().String(),
			"year":         now.Year(),
			"hour":         now.Hour(),
			"minute":       now.Minute(),
			"second":       now.Second(),
		}

		w.Header().Set("Content-Type", "application/json")
		json.NewEncoder(w).Encode(response)
	} else {
		// Return plain text response
		w.Write([]byte(now.Format(time.RFC3339)))
	}
}

func main() {
	// Create a structured logger
	logger := slog.New(slog.NewJSONHandler(os.Stderr, nil))
	slog.SetDefault(logger)

	// Wrap the timeHandler with the logging middleware
	http.Handle("/time", loggingMiddleware(http.HandlerFunc(timeHandler)))

	// Start the server
	fmt.Println("Server is listening on :8080...")
	err := http.ListenAndServe(":8080", nil)
	if err != nil {
		slog.Error("Error starting server", "error", err)
	}
}
```

**How It Works**:
- The server checks the `Accept` header in the request.
- If the `Accept` header is `application/json`, it returns the time in JSON format.
- Otherwise, it defaults to returning the time in RFC 3339 format.

**Output for Plain Text**:
```bash
$ curl http://localhost:8080/time
2023-10-05T14:30:00Z
```

**Output for JSON**:
```bash
$ curl -H "Accept: application/json" http://localhost:8080/time
{
  "day_of_week": "Thursday",
  "day_of_month": 5,
  "month": "October",
  "year": 2023,
  "hour": 14,
  "minute": 30,
  "second": 0
}
```

---

### **Conclusion**

These exercises demonstrate how to:
1. Create a simple HTTP server in Go.
2. Add middleware for structured logging.
3. Use the `Accept` header to control the response format (plain text or JSON).

By completing these exercises, you’ve reinforced your understanding of Go’s `net/http` package, middleware, and structured logging with `slog`. These skills are essential for building robust and scalable web applications in Go.
