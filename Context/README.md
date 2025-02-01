### **Understanding Context in Go**

In Go, the `context` package is used to manage request-scoped values, cancellation signals, and deadlines across API boundaries and between processes. It is particularly useful in concurrent programming, such as in HTTP servers, where you need to pass metadata (like request IDs, authentication tokens, or deadlines) through multiple layers of middleware and business logic.

Let’s break down the concepts and usage of `context` in Go with examples.

---

### **1. What is a Context?**

A `context` in Go is an instance of the `context.Context` interface. It is used to carry deadlines, cancellation signals, and other request-scoped values across API boundaries and between goroutines.

#### **Key Characteristics of Context:**
- **Immutable**: Once a context is created, it cannot be modified. Instead, you create a new context by wrapping an existing one.
- **Explicit**: Contexts are passed explicitly as the first parameter to functions, following Go's idiomatic style.
- **Hierarchical**: Contexts form a tree-like structure. Each context can have a parent, and when a parent is canceled, all its children are also canceled.

---

### **2. Creating a Context**

#### **2.1. `context.Background()`**
This function returns an empty context, which is typically used as the root context in your application. It is never canceled and has no values.

```go
ctx := context.Background()
```

#### **2.2. `context.TODO()`**
This function also returns an empty context, but it is intended for temporary use during development when you’re unsure which context to use. It should not be used in production code.

```go
ctx := context.TODO()
```

---

### **3. Adding Values to a Context**

You can add key-value pairs to a context using `context.WithValue`. This creates a new context that wraps the parent context and includes the new key-value pair.

#### **Example: Adding a User ID to a Context**

```go
package main

import (
	"context"
	"fmt"
)

type userKey string

const key userKey = "userID"

func main() {
	// Create a root context
	ctx := context.Background()

	// Add a user ID to the context
	ctx = context.WithValue(ctx, key, "12345")

	// Retrieve the user ID from the context
	if userID, ok := ctx.Value(key).(string); ok {
		fmt.Println("User ID:", userID)
	} else {
		fmt.Println("User ID not found")
	}
}
```

**Output:**
```
User ID: 12345
```

#### **Key Points:**
- The key should be of an unexported type to avoid collisions with other packages.
- Use `context.WithValue` sparingly. Prefer passing values explicitly as function arguments when possible.

---

### **4. Cancellation and Timeouts**

Contexts are often used to manage cancellation and timeouts in long-running operations, such as HTTP requests or database queries.

#### **4.1. `context.WithCancel`**
This function returns a new context and a cancel function. Calling the cancel function cancels the context and all its children.

```go
package main

import (
	"context"
	"fmt"
	"time"
)

func main() {
	// Create a context with cancellation
	ctx, cancel := context.WithCancel(context.Background())

	// Simulate a long-running operation
	go func() {
		time.Sleep(2 * time.Second)
		cancel() // Cancel the context after 2 seconds
	}()

	// Wait for the context to be canceled
	select {
	case <-ctx.Done():
		fmt.Println("Context canceled:", ctx.Err())
	}
}
```

**Output:**
```
Context canceled: context canceled
```

#### **4.2. `context.WithTimeout`**
This function returns a new context that is automatically canceled after a specified duration.

```go
package main

import (
	"context"
	"fmt"
	"time"
)

func main() {
	// Create a context with a timeout of 1 second
	ctx, cancel := context.WithTimeout(context.Background(), 1*time.Second)
	defer cancel() // Ensure the context is canceled

	// Simulate a long-running operation
	select {
	case <-time.After(2 * time.Second):
		fmt.Println("Operation completed")
	case <-ctx.Done():
		fmt.Println("Operation canceled:", ctx.Err())
	}
}
```

**Output:**
```
Operation canceled: context deadline exceeded
```

#### **4.3. `context.WithDeadline`**
This function is similar to `context.WithTimeout`, but it allows you to specify an exact time when the context should be canceled.

```go
package main

import (
	"context"
	"fmt"
	"time"
)

func main() {
	// Set a deadline 1 second from now
	deadline := time.Now().Add(1 * time.Second)

	// Create a context with the deadline
	ctx, cancel := context.WithDeadline(context.Background(), deadline)
	defer cancel()

	// Simulate a long-running operation
	select {
	case <-time.After(2 * time.Second):
		fmt.Println("Operation completed")
	case <-ctx.Done():
		fmt.Println("Operation canceled:", ctx.Err())
	}
}
```

**Output:**
```
Operation canceled: context deadline exceeded
```

---

### **5. Using Context in HTTP Servers**

In HTTP servers, contexts are used to pass request-scoped values (like user IDs or request IDs) through middleware and handlers.

#### **Example: Middleware for Adding a User ID to the Context**

```go
package main

import (
	"context"
	"fmt"
	"net/http"
)

type userKey string

const key userKey = "userID"

// Middleware to extract user ID from a cookie and add it to the context
func userMiddleware(next http.Handler) http.Handler {
	return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		// Simulate extracting a user ID from a cookie
		userID := "12345"

		// Add the user ID to the context
		ctx := context.WithValue(r.Context(), key, userID)

		// Create a new request with the updated context
		r = r.WithContext(ctx)

		// Call the next handler
		next.ServeHTTP(w, r)
	})
}

// Handler to retrieve the user ID from the context
func userHandler(w http.ResponseWriter, r *http.Request) {
	ctx := r.Context()

	// Retrieve the user ID from the context
	if userID, ok := ctx.Value(key).(string); ok {
		fmt.Fprintf(w, "User ID: %s\n", userID)
	} else {
		http.Error(w, "User ID not found", http.StatusUnauthorized)
	}
}

func main() {
	// Create a new HTTP server with the middleware and handler
	http.Handle("/user", userMiddleware(http.HandlerFunc(userHandler)))

	// Start the server
	http.ListenAndServe(":8080", nil)
}
```

**Testing the Server:**
- Run the server and visit `http://localhost:8080/user`.
- The server will respond with `User ID: 12345`.

---

### **6. Best Practices for Using Context**

1. **Pass Context Explicitly**: Always pass the context as the first parameter to functions.
2. **Avoid Overusing `context.WithValue`**: Use it only for request-scoped values that cannot be passed explicitly.
3. **Use Cancellation and Timeouts**: Use `context.WithCancel`, `context.WithTimeout`, and `context.WithDeadline` to manage long-running operations.
4. **Keep Context Keys Unique**: Use unexported types for context keys to avoid collisions.

---

### **7. Real-World Example: Tracking Requests with a GUID**

Let’s implement a system where each request is assigned a unique GUID, which is passed through the context and used for logging and tracing.

```go
package main

import (
	"context"
	"fmt"
	"net/http"
	"github.com/google/uuid"
)

type guidKey string

const key guidKey = "guid"

// Middleware to add a GUID to the context
func guidMiddleware(next http.Handler) http.Handler {
	return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		// Generate a new GUID
		guid := uuid.New().String()

		// Add the GUID to the context
		ctx := context.WithValue(r.Context(), key, guid)

		// Create a new request with the updated context
		r = r.WithContext(ctx)

		// Call the next handler
		next.ServeHTTP(w, r)
	})
}

// Handler to log the GUID
func guidHandler(w http.ResponseWriter, r *http.Request) {
	ctx := r.Context()

	// Retrieve the GUID from the context
	if guid, ok := ctx.Value(key).(string); ok {
		fmt.Fprintf(w, "GUID: %s\n", guid)
	} else {
		http.Error(w, "GUID not found", http.StatusInternalServerError)
	}
}

func main() {
	// Create a new HTTP server with the middleware and handler
	http.Handle("/guid", guidMiddleware(http.HandlerFunc(guidHandler)))

	// Start the server
	http.ListenAndServe(":8080", nil)
}
```

**Testing the Server:**
- Run the server and visit `http://localhost:8080/guid`.
- The server will respond with a unique GUID for each request.

---

### **Conclusion**

The `context` package in Go is a powerful tool for managing request-scoped values, cancellation, and timeouts. By following best practices and using contexts effectively, you can build robust and maintainable applications.



### **Understanding Context Cancellation in Go**

In Go, the `context` package is not only used for passing request-scoped values but also for managing the lifecycle of operations, particularly in concurrent programming. One of the key features of the `context` package is **cancellation**, which allows you to stop ongoing operations gracefully when they are no longer needed or when an error occurs.

Let’s break down the concepts of **cancellation**, **timeouts**, and **deadlines** in Go with examples.

---

### **1. What is Context Cancellation?**

Cancellation is a mechanism to signal that an operation should be stopped. This is particularly useful in scenarios where:
- A request is no longer needed (e.g., the client disconnected).
- An error occurs, and further processing is unnecessary.
- A timeout or deadline is reached.

The `context` package provides tools to implement cancellation in a clean and idiomatic way.

---

### **2. Creating a Cancellable Context**

To create a cancellable context, use the `context.WithCancel` function. It takes a parent context and returns:
- A new context (child context).
- A `cancel` function that can be called to cancel the context.

#### **Example: Basic Cancellation**

```go
package main

import (
	"context"
	"fmt"
	"time"
)

func main() {
	// Create a cancellable context
	ctx, cancel := context.WithCancel(context.Background())
	defer cancel() // Ensure the context is canceled when done

	// Launch a goroutine that listens for cancellation
	go func() {
		select {
		case <-ctx.Done(): // Wait for the context to be canceled
			fmt.Println("Goroutine canceled:", ctx.Err())
		}
	}()

	// Simulate some work
	time.Sleep(2 * time.Second)

	// Cancel the context
	cancel()

	// Give the goroutine time to process the cancellation
	time.Sleep(1 * time.Second)
}
```

**Output:**
```
Goroutine canceled: context canceled
```

#### **Key Points:**
- The `cancel` function must be called to release resources.
- The `ctx.Done()` channel is closed when the context is canceled, signaling that the operation should stop.

---

### **3. Using Cancellation in Concurrent Operations**

Cancellation is particularly useful when you have multiple goroutines working on a task, and you want to stop all of them if one fails or if the task is no longer needed.

#### **Example: Canceling Multiple Goroutines**

```go
package main

import (
	"context"
	"fmt"
	"sync"
	"time"
)

func worker(ctx context.Context, id int, wg *sync.WaitGroup) {
	defer wg.Done()

	for {
		select {
		case <-ctx.Done(): // Check if the context is canceled
			fmt.Printf("Worker %d canceled: %v\n", id, ctx.Err())
			return
		default:
			fmt.Printf("Worker %d is working...\n", id)
			time.Sleep(500 * time.Millisecond)
		}
	}
}

func main() {
	// Create a cancellable context
	ctx, cancel := context.WithCancel(context.Background())
	defer cancel()

	var wg sync.WaitGroup

	// Launch multiple workers
	for i := 1; i <= 3; i++ {
		wg.Add(1)
		go worker(ctx, i, &wg)
	}

	// Simulate some work
	time.Sleep(2 * time.Second)

	// Cancel the context
	fmt.Println("Canceling all workers...")
	cancel()

	// Wait for all workers to finish
	wg.Wait()
}
```

**Output:**
```
Worker 1 is working...
Worker 2 is working...
Worker 3 is working...
Worker 1 is working...
Worker 2 is working...
Worker 3 is working...
Worker 1 is working...
Worker 2 is working...
Worker 3 is working...
Canceling all workers...
Worker 1 canceled: context canceled
Worker 2 canceled: context canceled
Worker 3 canceled: context canceled
```

#### **Key Points:**
- The `ctx.Done()` channel is used to detect cancellation in each goroutine.
- Calling `cancel()` stops all goroutines that are listening to the same context.

---

### **4. Context with Timeout**

Sometimes, you want to cancel an operation if it takes too long. The `context.WithTimeout` function allows you to set a maximum duration for an operation.

#### **Example: Using a Timeout**

```go
package main

import (
	"context"
	"fmt"
	"time"
)

func main() {
	// Create a context with a 1-second timeout
	ctx, cancel := context.WithTimeout(context.Background(), 1*time.Second)
	defer cancel()

	// Simulate a long-running operation
	select {
	case <-time.After(2 * time.Second): // This will take longer than the timeout
		fmt.Println("Operation completed")
	case <-ctx.Done(): // Wait for the context to timeout
		fmt.Println("Operation canceled:", ctx.Err())
	}
}
```

**Output:**
```
Operation canceled: context deadline exceeded
```

#### **Key Points:**
- The context is automatically canceled after the specified timeout.
- The `ctx.Err()` method returns `context.DeadlineExceeded` when the timeout occurs.

---

### **5. Context with Deadline**

The `context.WithDeadline` function is similar to `context.WithTimeout`, but it allows you to specify an exact time when the context should be canceled.

#### **Example: Using a Deadline**

```go
package main

import (
	"context"
	"fmt"
	"time"
)

func main() {
	// Set a deadline 1 second from now
	deadline := time.Now().Add(1 * time.Second)

	// Create a context with the deadline
	ctx, cancel := context.WithDeadline(context.Background(), deadline)
	defer cancel()

	// Simulate a long-running operation
	select {
	case <-time.After(2 * time.Second): // This will take longer than the deadline
		fmt.Println("Operation completed")
	case <-ctx.Done(): // Wait for the context to be canceled
		fmt.Println("Operation canceled:", ctx.Err())
	}
}
```

**Output:**
```
Operation canceled: context deadline exceeded
```

#### **Key Points:**
- The context is canceled at the specified deadline.
- If the deadline is in the past, the context is created already canceled.

---

### **6. Nested Contexts and Timeouts**

When using nested contexts, the timeout of a child context cannot exceed the timeout of its parent context. If the parent context times out, the child context is also canceled.

#### **Example: Nested Contexts**

```go
package main

import (
	"context"
	"fmt"
	"time"
)

func main() {
	// Create a parent context with a 2-second timeout
	parentCtx, cancelParent := context.WithTimeout(context.Background(), 2*time.Second)
	defer cancelParent()

	// Create a child context with a 3-second timeout
	childCtx, cancelChild := context.WithTimeout(parentCtx, 3*time.Second)
	defer cancelChild()

	// Wait for the child context to be canceled
	start := time.Now()
	<-childCtx.Done()
	end := time.Now()

	fmt.Println("Child context canceled after:", end.Sub(start).Truncate(time.Second))
}
```

**Output:**
```
Child context canceled after: 2s
```

#### **Key Points:**
- The child context cannot exceed the parent context's timeout.
- The child context is canceled when the parent context times out.

---

### **7. Real-World Example: HTTP Request with Timeout**

Let’s implement an HTTP client that cancels a request if it takes too long.

```go
package main

import (
	"context"
	"fmt"
	"net/http"
	"time"
)

func main() {
	// Create a context with a 1-second timeout
	ctx, cancel := context.WithTimeout(context.Background(), 1*time.Second)
	defer cancel()

	// Create an HTTP request with the context
	req, err := http.NewRequestWithContext(ctx, http.MethodGet, "https://httpbin.org/delay/2", nil)
	if err != nil {
		fmt.Println("Error creating request:", err)
		return
	}

	// Send the request
	resp, err := http.DefaultClient.Do(req)
	if err != nil {
		fmt.Println("Error making request:", err)
		return
	}
	defer resp.Body.Close()

	fmt.Println("Response status:", resp.Status)
}
```

**Output:**
```
Error making request: Get "https://httpbin.org/delay/2": context deadline exceeded
```

#### **Key Points:**
- The HTTP request is canceled if it exceeds the 1-second timeout.
- The `http.NewRequestWithContext` function associates the context with the request.

---

### **8. Best Practices for Context Cancellation**

1. **Always Call the Cancel Function**: Use `defer cancel()` to ensure resources are released.
2. **Use Timeouts and Deadlines**: Set reasonable timeouts for operations to prevent resource leaks.
3. **Propagate Contexts**: Pass the context through function calls to ensure cancellation signals are propagated.
4. **Avoid Overusing Context Values**: Use context values only for request-scoped data, not for passing function parameters.

---

### **Conclusion**

Context cancellation is a powerful feature in Go that helps you manage the lifecycle of operations, especially in concurrent and distributed systems. By using `context.WithCancel`, `context.WithTimeout`, and `context.WithDeadline`, you can build responsive and resource-efficient applications. Always remember to propagate contexts and handle cancellation gracefully to avoid resource leaks and ensure your programs behave as expected.



### **Understanding Context Cancellation in Go**

Go’s `context` package provides a way to manage deadlines, cancellations, and request-scoped values across API calls. Context is particularly useful when dealing with long-running operations, such as database queries, HTTP requests, or computations that should stop once the user cancels the request.

In this explanation, we will cover:

1. **Why Context Cancellation Matters**
2. **How Context Works in Go**
3. **Implementing Context Cancellation in Code**
4. **Exercises with Examples**
   - Middleware to enforce timeouts
   - Random number addition with cancellation
   - Logging system using context

---

## **1. Why Context Cancellation Matters**

Imagine you are writing a web service that fetches data from an external API. If the API takes too long to respond or the user cancels the request, you don’t want your server to waste resources waiting indefinitely. Instead, you should **cancel** the request and free up resources.

Common scenarios where you should handle context cancellation:
- **HTTP Requests:** If a user closes their browser, you should cancel any in-progress database or API requests.
- **Database Queries:** Prevent unnecessary queries if the request times out.
- **Long-Running Computations:** Stop processing once the request is no longer needed.

---

## **2. How Context Works in Go**

Go’s `context` package provides several ways to create and manage context:

- **`context.Background()`** → Root context, used when there is no existing context.
- **`context.WithCancel(parentContext)`** → Creates a derived context that can be manually canceled.
- **`context.WithTimeout(parentContext, duration)`** → Creates a derived context with a timeout.
- **`context.WithDeadline(parentContext, time)`** → Similar to `WithTimeout`, but allows specifying an exact deadline.
- **`context.WithValue(parentContext, key, value)`** → Stores a value in the context (use sparingly).

### **Basic Example of Context Cancellation**

```go
package main

import (
	"context"
	"fmt"
	"time"
)

func main() {
	ctx, cancel := context.WithTimeout(context.Background(), 2*time.Second)
	defer cancel() // Ensure that resources are released

	ch := make(chan string)

	// Start a goroutine that simulates a long-running task
	go func() {
		time.Sleep(3 * time.Second)
		ch <- "Task completed"
	}()

	select {
	case <-ctx.Done():
		fmt.Println("Operation timed out:", ctx.Err())
	case result := <-ch:
		fmt.Println(result)
	}
}
```

### **Explanation**
- `context.WithTimeout(context.Background(), 2*time.Second)` creates a context that will be automatically canceled after 2 seconds.
- A goroutine simulates a long-running task that takes **3 seconds**.
- The `select` statement:
  - If `ctx.Done()` is triggered (timeout reached), it prints `"Operation timed out"`.
  - Otherwise, if the goroutine finishes first, it prints the result.

Since the task takes **3 seconds** and our timeout is **2 seconds**, the operation will be **canceled** before completion.

---

## **3. Implementing Context Cancellation in Code**

### **Scenario 1: Handling Cancellation in a Long-Running Loop**

When running a long computation, periodically check the **context status** using `context.Cause(ctx)`:

```go
package main

import (
	"context"
	"fmt"
	"time"
)

func longRunningComputation(ctx context.Context) {
	i := 0
	for {
		if err := context.Cause(ctx); err != nil {
			fmt.Println("Cancelled after", i, "iterations:", err)
			return
		}

		// Simulate work
		time.Sleep(500 * time.Millisecond)
		fmt.Println("Iteration", i)
		i++
	}
}

func main() {
	ctx, cancel := context.WithTimeout(context.Background(), 3*time.Second)
	defer cancel()

	longRunningComputation(ctx)
}
```

### **Explanation**
- Every **500ms**, the function checks if `context.Cause(ctx)` has an error.
- If the context is canceled, it **exits** the loop and prints a message.
- The `main()` function sets a **3-second timeout**, so the loop runs for about 6 iterations before stopping.

---

## **4. Exercises with Examples**

### **Exercise 1: Creating Middleware for Timeout Handling**

We need a middleware function that:
- Takes a timeout in milliseconds.
- Applies this timeout to all incoming HTTP requests.

#### **Implementation**

```go
package main

import (
	"context"
	"fmt"
	"net/http"
	"time"
)

// Middleware function to set a timeout
func TimeoutMiddleware(timeoutMs int) func(http.Handler) http.Handler {
	return func(next http.Handler) http.Handler {
		return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
			ctx, cancel := context.WithTimeout(r.Context(), time.Duration(timeoutMs)*time.Millisecond)
			defer cancel()

			// Pass the new context to the next handler
			next.ServeHTTP(w, r.WithContext(ctx))
		})
	}
}

func handler(w http.ResponseWriter, r *http.Request) {
	select {
	case <-r.Context().Done():
		http.Error(w, "Request timed out", http.StatusGatewayTimeout)
		return
	case <-time.After(2 * time.Second):
		fmt.Fprintln(w, "Hello, world!")
	}
}

func main() {
	mux := http.NewServeMux()
	mux.Handle("/", TimeoutMiddleware(1000)(http.HandlerFunc(handler)))

	http.ListenAndServe(":8080", mux)
}
```

### **Explanation**
- `TimeoutMiddleware(1000)` sets a **1-second timeout**.
- If the handler takes longer than 1 second, it **times out** and returns `504 Gateway Timeout`.

---

### **Exercise 2: Random Number Addition Until Timeout**

Write a program that:
1. Generates random numbers between `0` and `100,000,000`.
2. Stops if it **generates `1234`** or **after 2 seconds**.
3. Prints the total sum and reason for stopping.

#### **Implementation**

```go
package main

import (
	"context"
	"fmt"
	"math/rand"
	"time"
)

func main() {
	ctx, cancel := context.WithTimeout(context.Background(), 2*time.Second)
	defer cancel()

	sum := 0
	iterations := 0

	for {
		select {
		case <-ctx.Done():
			fmt.Println("Stopped due to timeout")
			fmt.Println("Total Sum:", sum, "| Iterations:", iterations)
			return
		default:
			num := rand.Intn(100000000)
			sum += num
			iterations++

			if num == 1234 {
				fmt.Println("Stopped because 1234 was found")
				fmt.Println("Total Sum:", sum, "| Iterations:", iterations)
				return
			}
		}
	}
}
```

---

### **Exercise 3: Logging with Context**

1. Define a `Level` type for logging (`Debug`, `Info`).
2. Store and extract log levels using **context**.
3. Implement middleware that reads a query parameter `log_level`.

#### **Implementation**

```go
package main

import (
	"context"
	"fmt"
	"net/http"
)

type Level string

const (
	Debug Level = "debug"
	Info  Level = "info"
)

func WithLogLevel(ctx context.Context, level Level) context.Context {
	return context.WithValue(ctx, "log_level", level)
}

func Log(ctx context.Context, level Level, message string) {
	inLevel, _ := ctx.Value("log_level").(Level)

	if level == Debug && inLevel == Debug {
		fmt.Println("DEBUG:", message)
	}
	if level == Info && (inLevel == Debug || inLevel == Info) {
		fmt.Println("INFO:", message)
	}
}

func LogMiddleware(next http.Handler) http.Handler {
	return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		level := Level(r.URL.Query().Get("log_level"))
		ctx := WithLogLevel(r.Context(), level)
		next.ServeHTTP(w, r.WithContext(ctx))
	})
}

func handler(w http.ResponseWriter, r *http.Request) {
	Log(r.Context(), Info, "Processing request")
	fmt.Fprintln(w, "Hello, world!")
}

func main() {
	mux := http.NewServeMux()
	mux.Handle("/", LogMiddleware(http.HandlerFunc(handler)))

	http.ListenAndServe(":8080", mux)
}
```

### **Explanation**
- `LogMiddleware` reads `log_level` from the query string (`?log_level=debug`).
- `Log()` only prints messages that match the current level.

---

## **Conclusion**
- Use `context.WithTimeout()` to enforce timeouts.
- Check `context.Cause(ctx)` in loops.
- Pass context through middleware for HTTP handling.

Let me know if you need clarifications! 🚀
