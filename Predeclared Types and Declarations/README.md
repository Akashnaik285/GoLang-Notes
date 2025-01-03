# Predeclared Types and Declarations


  
Here’s a breakdown of Go literals with code examples to make each type easier to understand:

---

### **Integer Literals**
Integer literals can be written in different bases:
- **Base 10**: Default representation.
- **Binary**: Prefixed with `0b`.
- **Octal**: Prefixed with `0o` or a leading `0`.
- **Hexadecimal**: Prefixed with `0x`.

You can use underscores `_` to improve readability.

#### Code Example:
```go
package main

import "fmt"

func main() {
    // Base 10
    base10 := 1234
    base10WithUnderscore := 1_234

    // Binary
    binary := 0b1101 // 13 in decimal

    // Octal
    octal := 0o755   // POSIX permission: rwxr-xr-x
    legacyOctal := 0755 // Old style (not recommended)

    // Hexadecimal
    hexadecimal := 0x1A3F
    hexadecimalWithUnderscore := 0x1_A3_F

    fmt.Println(base10, base10WithUnderscore)
    fmt.Println(binary, octal, legacyOctal)
    fmt.Println(hexadecimal, hexadecimalWithUnderscore)
}
```

---

### **Floating-Point Literals**
Floating-point literals can:
- Have a fractional portion indicated by a decimal point.
- Use scientific notation with `e` (base 10) or `p` (base 2 for hexadecimal).

#### Code Example:
```go
package main

import "fmt"

func main() {
    decimal := 3.14159
    scientific := 6.02e23 // Avogadro's number
    hexadecimalFloat := 0x1.91eb851eb851fp+1 // 3.14 in base 10

    fmt.Println(decimal, scientific, hexadecimalFloat)
}
```

---

### **Rune Literals**
Rune literals represent single Unicode characters enclosed in single quotes (`'`).
- Can be specified in Unicode or using escape sequences.

#### Code Example:
```go
package main

import "fmt"

func main() {
    char := 'a'           // Single character
    unicodeChar := '\u03A9' // Omega (Ω)
    hexChar := '\x41'      // 'A'
    newline := '\n'        // Newline escape
    tab := '\t'            // Tab escape

    fmt.Printf("char: %c, unicodeChar: %c, hexChar: %c\n", char, unicodeChar, hexChar)
    fmt.Printf("Special characters: [%c] [%c]\n", newline, tab)
}
```

---

### **String Literals**
1. **Interpreted Strings**: Enclosed in double quotes (`"`). 
   - Backslashes are used for escape sequences.

2. **Raw Strings**: Enclosed in backquotes (`` ` ``).
   - No escape sequences are interpreted.

#### Code Example:
```go
package main

import "fmt"

func main() {
    interpreted := "Hello, \"World\"!\nThis is Go."
    raw := `Hello, "World"!
This is Go.`

    fmt.Println("Interpreted string:")
    fmt.Println(interpreted)

    fmt.Println("\nRaw string:")
    fmt.Println(raw)
}
```

---

In Go, **typed literals** refer to literals that are explicitly assigned a type during their definition or initialization. When you specify the type of a literal, it becomes "typed." This contrasts with **untyped literals**, which don't have a specific type until they are assigned to a variable or used in a context that requires a specific type.

### Examples of Typed Literals

Here are some cases of explicitly **typed literals**:

#### Integer Example
```go
package main

import "fmt"

func main() {
    var typedInt int32 = 42 // explicitly specifying int32
    fmt.Printf("typedInt: %T\n", typedInt)
}
```
In this case, the literal `42` is typed as `int32`.

#### Floating-Point Example
```go
package main

import "fmt"

func main() {
    var typedFloat float32 = 3.14 // explicitly specifying float32
    fmt.Printf("typedFloat: %T\n", typedFloat)
}
```
Here, the literal `3.14` is typed as `float32`.

#### Rune Example
```go
package main

import "fmt"

func main() {
    var typedRune rune = 'A' // explicitly specifying rune
    fmt.Printf("typedRune: %T\n", typedRune)
}
```
The rune literal `'A'` is typed as `rune`.

#### String Example
```go
package main

import "fmt"

func main() {
    var typedString string = "Hello, World!" // explicitly specifying string
    fmt.Printf("typedString: %T\n", typedString)
}
```
The string literal `"Hello, World!"` is typed as `string`.

### Key Difference Between Typed and Untyped Literals
- **Untyped literals** take on a default type only when needed (e.g., when assigned to a variable or used in an expression).
- **Typed literals** have their type explicitly declared, either by assignment to a variable of a specific type or by type casting.

### Why is This Important?
Typed literals give you more control over the behavior of your code. For instance, when working with type-specific operations or interfacing with functions expecting particular types, specifying the literal type ensures compatibility and avoids unintended type promotion or type mismatch errors.



### **Untyped Literals**
Go literals are considered untyped until assigned to a variable. The default types are:
- Integer literals: `int`
- Floating-point literals: `float64`
- Rune literals: `rune`
- String literals: `string`

#### Code Example:
```go
package main

import "fmt"

func main() {
    var defaultInt = 42       // int
    var defaultFloat = 3.14   // float64
    var defaultRune = 'A'     // rune
    var defaultString = "Hi"  // string

    fmt.Printf("defaultInt: %T, defaultFloat: %T, defaultRune: %T, defaultString: %T\n", 
        defaultInt, defaultFloat, defaultRune, defaultString)
}
```

---

These examples cover all the primary kinds of literals in Go. Let me know if you want explanations for complex literals or specific use cases!



Here are **detailed revision notes** for **Booleans and Numeric Types** in Go, complete with examples for quick reference.  

---

## **Booleans**
- **Type**: `bool`  
- **Values**: `true` or `false`.  
- **Zero value**: `false`.  

### Examples:
```go
var flag bool         // Default value: false
var isAwesome = true  // Boolean variable assignment

// Conditional usage
if isAwesome {
    fmt.Println("Go is awesome!")
}
```

---

## **Numeric Types**
Go provides a rich set of numeric types categorized into **integers**, **floating-point numbers**, and **complex numbers**.

---

### **Integer Types**
#### **Signed Integers**
| Type   | Size   | Value Range                     |
|--------|--------|---------------------------------|
| `int8` | 8-bit  | -128 to 127                    |
| `int16`| 16-bit | -32,768 to 32,767              |
| `int32`| 32-bit | -2,147,483,648 to 2,147,483,647|
| `int64`| 64-bit | -9,223,372,036,854,775,808 to 9,223,372,036,854,775,807 |

#### **Unsigned Integers**
| Type    | Size   | Value Range                   |
|---------|--------|-------------------------------|
| `uint8` | 8-bit  | 0 to 255                      |
| `uint16`| 16-bit | 0 to 65,535                   |
| `uint32`| 32-bit | 0 to 4,294,967,295            |
| `uint64`| 64-bit | 0 to 18,446,744,073,709,551,615 |

#### **Special Integer Types**
1. **`int`**  
   - Size depends on the architecture:  
     - 32-bit CPU: 32-bit signed integer (`int32`).  
     - 64-bit CPU: 64-bit signed integer (`int64`).  
   - Default type for integer literals.  
2. **`uint`**  
   - Similar to `int`, but unsigned.  
3. **`byte`**  
   - Alias for `uint8`, often used for raw byte data.  
4. **`rune`**  
   - Alias for `int32`, used for Unicode characters.  

### **Choosing Integer Types**
- Use a **specific size/type** when working with:
  - Binary file formats.
  - Network protocols.  
- Use **`int`** in general cases.
- Use **generics** to support multiple integer types in libraries.  

---

### **Floating-Point Types**
| Type      | Size  | Precision            |
|-----------|-------|----------------------|
| `float32` | 32-bit| Approximately 7 digits|
| `float64` | 64-bit| Approximately 15 digits|

#### **Special Literals**:
- **Scientific Notation**: `6.02e23`  
- **Hexadecimal**: `0x1.9p3`  
  - `0x1.9p3` = \( 1.9 \times 2^3 = 13.5 \).  

#### Examples:
```go
var pi float32 = 3.14
var avogadro = 6.02e23  // Defaults to float64
```

---

### **Complex Numbers**
- Represented as `complex64` and `complex128`.
- Syntax: `real + imaginary` parts using `i`.  

#### Examples:
```go
var c1 = complex(5, 3)   // 5 + 3i
var c2 = 7 + 2i          // Alternative syntax

fmt.Println(c1 + c2)     // Outputs: (12+5i)
fmt.Println(real(c1))    // Outputs: 5
fmt.Println(imag(c1))    // Outputs: 3
```

---

### **Type Conversion**
Go requires **explicit type conversion** between different types.

#### Examples:
```go
var a int32 = 10
var b int64 = int64(a) // Explicit conversion

var f float64 = 3.14
var i int = int(f)    // Truncates decimal, i = 3
```

---

### **Underscores in Literals**
- Improve readability.  
- Supported for both integers and floating-point numbers.  

#### Examples:
```go
var num = 1_000_000   // Same as 1000000
var bin = 0b1010_1010 // Binary literal
var hex = 0xDE_AD_BE_EF // Hexadecimal literal
```

---

### **Usage Tips**
1. Use **interpreted string literals** (`"`) unless you need multi-line or special character inclusion.  
2. Avoid octal literals (`0o` or `0`). They are rarely used.  
3. Use `int` or `float64` unless you have specific constraints.  

--- 

These notes provide a solid foundation for **revision** and quick reference! Let me know if you'd like to expand on any section or add related concepts.



### **Go Data Types: Integers, Floating-Point, and Complex Numbers**

#### **1. Integer Operators**
- **Arithmetic Operators**: `+`, `-`, `*`, `/`, `%`.
  - Integer division (`/`) truncates towards zero.
  - Example:
    ```go
    fmt.Println(10 / 3)  // Output: 3
    ```
- **Compound Assignment**: Combine arithmetic with `=`:
  - Examples:
    ```go
    var x int = 10
    x *= 2  // x is now 20
    x -= 5  // x is now 15
    ```
- **Comparison Operators**: `==`, `!=`, `>`, `<`, `>=`, `<=`.
- **Bit Manipulation**:
  - Shift: `<<` (left shift), `>>` (right shift).
  - Bitwise: `&` (AND), `|` (OR), `^` (XOR), `&^` (AND NOT).
  - Compound operators: `<<=`, `>>=`, `&=`, `|=`, `^=`, `&^=`.
  - Example:
    ```go
    var y int = 6  // 0110 in binary
    y = y << 1     // Left shift, y is now 12 (1100 in binary)
    fmt.Println(y) // Output: 12
    ```

---

#### **2. Floating-Point Types**
- **Types**:
  - `float32`: ~6-7 decimal digits precision.
  - `float64`: ~15-16 decimal digits precision (preferred).
- **Default**: Floating-point literals are `float64` by default.
- **Math Operators**: All except `%`.
- **Special Division Cases**:
  - Divide by `0`: Returns `+Inf`, `-Inf`, or `NaN`.
    ```go
    fmt.Println(1.0 / 0.0)  // Output: +Inf
    fmt.Println(0.0 / 0.0)  // Output: NaN
    ```
- **Float Comparison**:
  - Avoid direct `==` and `!=` due to precision issues.
  - Use epsilon for approximate comparison:
    ```go
    const epsilon = 1e-9
    a, b := 0.1+0.2, 0.3
    fmt.Println(math.Abs(a-b) < epsilon)  // Output: true
    ```

---

#### **3. Complex Numbers**
- **Types**:
  - `complex64`: Uses `float32` for real and imaginary parts.
  - `complex128`: Uses `float64` for real and imaginary parts.
- **Creation**:
  - Use `complex(real, imag)` function.
  - Default type is `complex128`.
    ```go
    var c = complex(2.5, 3.1)
    fmt.Println(c)  // Output: (2.5+3.1i)
    ```
- **Operators**:
  - Standard arithmetic (`+`, `-`, `*`, `/`).
  - Comparison (`==`, `!=`) - use epsilon for precision.
- **Extract Parts**:
  - `real(c)`: Real part.
  - `imag(c)`: Imaginary part.
  - Example:
    ```go
    c := complex(3.0, 4.0)
    fmt.Println(real(c)) // Output: 3
    fmt.Println(imag(c)) // Output: 4
    ```
- **Mathematical Functions**:
  - Use `math/cmplx` for complex functions like magnitude.
    ```go
    fmt.Println(cmplx.Abs(c))  // Output: 5 (magnitude)
    ```

---

#### **4. Floating-Point and Complex Number Notes**
- Avoid floating-point numbers for exact values like money.
- Use libraries like [Gonum](https://gonum.org/) for numerical computing in Go.

---

#### **Example Code for All Concepts**
```go
package main

import (
	"fmt"
	"math"
	"math/cmplx"
)

func main() {
	// Integer Operations
	var x int = 10
	x += 5
	x *= 2
	fmt.Println("Integer operations:", x)  // Output: 30

	// Floating-point operations
	var a float64 = 10.0
	var b float64 = 3.0
	fmt.Println("Float division:", a/b)   // Output: 3.3333333333333335

	// Float comparison
	const epsilon = 1e-9
	f1, f2 := 0.1+0.2, 0.3
	fmt.Println("Float comparison:", math.Abs(f1-f2) < epsilon)  // Output: true

	// Complex numbers
	c1 := complex(2.5, 3.1)
	c2 := complex(1.5, -1.1)
	fmt.Println("Complex addition:", c1+c2)                     // Output: (4+2i)
	fmt.Println("Real part of c1:", real(c1))                   // Output: 2.5
	fmt.Println("Magnitude of c1:", cmplx.Abs(c1))              // Output: 3.982461550347975
}
```



Here’s a detailed breakdown of the concepts covered in the text:

---

### **A Taste of Strings and Runes**

#### **Strings in Go**:
- **Built-in Type**: Strings are a built-in type in Go, with a zero value of an empty string (`""`).
- **Unicode Support**: Go supports Unicode, allowing any Unicode character in a string.
- **Comparison**:
  - Equality (`==`) and inequality (`!=`).
  - Ordering (`>`, `>=`, `<`, `<=`).
- **Concatenation**: Use the `+` operator to combine strings.
- **Immutability**: Strings are immutable, meaning their content cannot be modified. However, the string variable itself can be reassigned.

#### **Runes**:
- **Definition**: The `rune` type represents a single code point and is an alias for `int32`.
- **Usage**:
  - Preferred for character representation over `int32`.
  - Clarifies intent in code.
  - Example:
    ```go
    var myFirstInitial rune = 'J' // Correct usage
    var myLastInitial int32 = 'B' // Legal but less clear
    ```

---

### **Explicit Type Conversion**

#### **No Automatic Promotion**:
- Go does not perform automatic type promotion (e.g., between integers and floats).
- Explicit type conversion is required for operations involving different types.

#### **Examples**:
1. **Numeric Type Conversion**:
   ```go
   var x int = 10
   var y float64 = 30.2
   var sum1 float64 = float64(x) + y // Converts x to float64
   var sum2 int = x + int(y)        // Converts y to int
   fmt.Println(sum1, sum2)          // Output: 40.2 40
   ```

2. **Integer Type Conversion**:
   ```go
   var x int = 10
   var b byte = 100
   var sum3 int = x + int(b) // Converts b to int
   var sum4 byte = byte(x) + b // Converts x to byte
   fmt.Println(sum3, sum4)  // Output: 110, 110
   ```

#### **Booleans and "Truthy" Values**:
- Go has no concept of "truthy" values (non-zero numbers or non-empty strings as `true`).
- To determine truthiness, explicitly use comparison operators:
  ```go
  if x == 0 { ... }      // Check if x is zero
  if s == "" { ... }     // Check if string s is empty
  ```

#### **Why Explicit Conversion**:
- Adds verbosity for clarity and reduces confusion about type rules.
- Ensures developers explicitly define their intent.

---

### **Literals in Go**

#### **Untyped Literals**:
- Integer and floating-point literals are untyped until assigned.
- Example:
  ```go
  var x float64 = 10      // Integer literal assigned to a float
  var y float64 = 200.3 * 5 // Expression with float literals
  ```

#### **Limits**:
- Type mismatches (e.g., assigning a float literal to an int) result in compilation errors.
- Size limitations apply:
  ```go
  var b byte = 1000 // Error: Value exceeds byte limit
  ```

---

### **Variable Declarations: `var` vs `:=`**

#### **Using `var`**:
1. **With Type**:
   ```go
   var x int = 10
   ```
2. **Type Inference**:
   ```go
   var x = 10
   ```
3. **Zero Value Initialization**:
   ```go
   var x int // Zero value: 0
   ```

4. **Multiple Declarations**:
   - **Same Type**:
     ```go
     var x, y int = 10, 20
     ```
   - **Different Types**:
     ```go
     var x, y = 10, "hello"
     ```

5. **Declaration Lists**:
   ```go
   var (
       x int
       y = 20
       z int = 30
       d, e = 40, "hello"
       f, g string
   )
   ```

#### **Using `:=`**:
- Short declaration for type inference inside functions:
  ```go
  x := 10
  x, y := 30, "hello" // Reassigns x, declares y
  ```
- **Limitations**:
  - Only works within functions.
  - Cannot declare package-level variables.

---

### **Choosing Between `var` and `:=`**
- **Use `:=`**:
  - Default for declaring variables within functions.
- **Use `var`**:
  - Declaring at the package level.
  - Assigning zero value explicitly.
  - When assigning a literal that needs a specific type.

#### **Avoid Common Pitfalls**:
- Shadowing variables with `:=`.
- Overusing `var` or `:=` for package-level variables.

--- 

This structured summary captures the essence of the concepts and practices around strings, runes, type conversions, and variable declarations in Go.


Let’s delve deeper into the concepts from the provided material with additional explanations and examples. This will include constants, typed and untyped constants, immutability, and naming conventions in Go. Here's a breakdown of each topic:

---

### **Constants in Go**

**Definition:**
Constants in Go are fixed values that cannot be changed during runtime. They are evaluated at compile time.

#### **Example 1: Simple Constant Declaration**
```go
package main

import "fmt"

const x int64 = 10
const (
    idKey   = "id"
    nameKey = "name"
)
const z = 20 * 10

func main() {
    const y = "hello"
    fmt.Println(x)    // Output: 10
    fmt.Println(y)    // Output: hello

    // x = x + 1      // This will cause a compilation error
    // y = "bye"      // This will cause a compilation error
}
```

#### **Key Points:**
1. Constants are immutable and cannot be reassigned.
2. Constants are evaluated at compile time, making them more efficient for literals or simple expressions.
3. They can be defined at both the package level and function level.

---

### **Typed vs Untyped Constants**

**Typed Constants:**
- Have a specific type and can only be assigned to variables of the same type.

**Untyped Constants:**
- Behave like literals and can be assigned to variables of compatible types.

#### **Example 2: Typed and Untyped Constants**
```go
package main

import "fmt"

const untypedConst = 10
const typedConst int = 20

func main() {
    var x int = untypedConst    // Allowed
    var y float64 = untypedConst // Allowed
    var z int = typedConst       // Allowed

    // var w float64 = typedConst // Compilation error: type mismatch

    fmt.Println(x, y, z) // Output: 10 10 20
}
```

---

### **Limitations of `const`**

Go's `const` is limited to values computable at compile time.

#### **Example 3: Constants vs Variables**
```go
package main

func main() {
    x := 5
    y := 10
    // const z = x + y // Compilation error: x and y are variables

    const a = 20
    const b = 30
    const c = a + b // Works: Constants used in expression
}
```

#### **Allowed Constant Values:**
- Numeric literals (`int`, `float64`, etc.)
- `true` and `false`
- Strings
- Runes
- Results from built-in functions (`len`, `cap`, `real`, `imag`, etc.)
- Compile-time computable expressions.

---

### **Immutability in Go**

Go doesn't support declaring runtime values as immutable. For example:
```go
func main() {
    var x = 5
    const y = 10
    // const z = x + y // Compilation error: x is not constant
}
```

**Workaround:**
Immutability can be mimicked using encapsulation and controlled access patterns.

---

### **Unused Variables and Constants**

#### **Variables:**
Go does not allow unused local variables but allows unused package-level variables.

```go
package main

func main() {
    x := 10
    // fmt.Println() // Uncomment this to avoid unused variable error
}
```

#### **Constants:**
Unused constants are removed during compilation, so they don't affect the binary size.

```go
package main

const unusedConst = 42 // Allowed, even if not used
```

---

### **Variable and Constant Naming Conventions**

1. **Short Names in Small Scopes:**
   - Use single-letter names (`i`, `j`, `k`) for loops.
   - Use meaningful names (`key`, `value`) in larger scopes.

2. **CamelCase for Multi-Word Names:**
   - Example: `indexCounter`, `numberTries`.

3. **Special Character `_`:**
   - `_` is a special identifier for ignoring values.
   - Example:
     ```go
     package main

     func main() {
         _, val := getValues()
         fmt.Println(val) // Output: 42
     }

     func getValues() (int, int) {
         return 10, 42
     }
     ```

4. **Package-Level Names:**
   - Exported identifiers start with an uppercase letter.
   - Example: `PublicName`.

---

### **Exercises with Solutions**

#### **Exercise 1: Integer to Float Conversion**
```go
package main

import "fmt"

func main() {
    var i int = 20
    var f float64 = float64(i) // Explicit type conversion
    fmt.Println(i, f)          // Output: 20 20.0
}
```

#### **Exercise 2: Constant Assignment**
```go
package main

import "fmt"

const value = 50

func main() {
    var i int = value
    var f float64 = value
    fmt.Println(i, f) // Output: 50 50.0
}
```

#### **Exercise 3: Overflow Example**
```go
package main

import "fmt"

func main() {
    var b byte = 255
    var smallI int32 = 2147483647
    var bigI uint64 = 18446744073709551615

    fmt.Println(b, smallI, bigI) // Output: 255 2147483647 18446744073709551615

    // Uncommenting below will cause overflow errors
    // b = b + 1
    // smallI = smallI + 1
    // bigI = bigI + 1
}
```

---

By understanding and using these concepts effectively, you can write more idiomatic and robust Go code! Let me know if you'd like further exploration into any specific area.

