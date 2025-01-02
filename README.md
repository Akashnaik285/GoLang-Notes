# GoLang-Notes

Go Notes 
Here are some coding examples for each type of Go literal:



Predeclared Types and Declarations
Integer Literals
Integer literals can be written in different bases:

Base 10: Default representation.
Binary: Prefixed with 0b.
Octal: Prefixed with 0o or a leading 0.
Hexadecimal: Prefixed with 0x.
You can use underscores _ to improve readability.

Code Example:
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
Floating-Point Literals
Floating-point literals can:

Have a fractional portion indicated by a decimal point.
Use scientific notation with e (base 10) or p (base 2 for hexadecimal).
Code Example:
package main

import "fmt"

func main() {
    decimal := 3.14159
    scientific := 6.02e23 // Avogadro's number
    hexadecimalFloat := 0x1.91eb851eb851fp+1 // 3.14 in base 10

    fmt.Println(decimal, scientific, hexadecimalFloat)
}
Rune Literals
Rune literals represent single Unicode characters enclosed in single quotes (').

Can be specified in Unicode or using escape sequences.
Code Example:
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
String Literals
Interpreted Strings: Enclosed in double quotes (").
Backslashes are used for escape sequences.
Raw Strings: Enclosed in backquotes (`).
No escape sequences are interpreted.
Code Example:
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
Untyped Literals
Go literals are considered untyped until assigned to a variable. The default types are:

Integer literals: int
Floating-point literals: float64
Rune literals: rune
String literals: string
Code Example:
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
These examples cover all the primary kinds of literals in Go. Let me know if you want explanations for complex literals or specific use cases!

Here are detailed revision notes for Booleans and Numeric Types in Go, complete with examples for quick reference.

Booleans
Type: bool
Values: true or false.
Zero value: false.
Examples:
var flag bool         // Default value: false
var isAwesome = true  // Boolean variable assignment

// Conditional usage
if isAwesome {
    fmt.Println("Go is awesome!")
}
Numeric Types
Go provides a rich set of numeric types categorized into integers, floating-point numbers, and complex numbers.

Integer Types
Signed Integers
Type	Size	Value Range
int8	8-bit	-128 to 127
int16	16-bit	-32,768 to 32,767
int32	32-bit	-2,147,483,648 to 2,147,483,647
int64	64-bit	-9,223,372,036,854,775,808 to 9,223,372,036,854,775,807
Unsigned Integers
Type	Size	Value Range
uint8	8-bit	0 to 255
uint16	16-bit	0 to 65,535
uint32	32-bit	0 to 4,294,967,295
uint64	64-bit	0 to 18,446,744,073,709,551,615
Special Integer Types
int
Size depends on the architecture:
32-bit CPU: 32-bit signed integer (int32).
64-bit CPU: 64-bit signed integer (int64).
Default type for integer literals.
uint
Similar to int, but unsigned.
byte
Alias for uint8, often used for raw byte data.
rune
Alias for int32, used for Unicode characters.
Choosing Integer Types
Use a specific size/type when working with:
Binary file formats.
Network protocols.
Use int in general cases.
Use generics to support multiple integer types in libraries.
Floating-Point Types
Type	Size	Precision
float32	32-bit	Approximately 7 digits
float64	64-bit	Approximately 15 digits
Special Literals:
Scientific Notation: 6.02e23
Hexadecimal: 0x1.9p3
0x1.9p3 = .
1.9×23=13.51.9 \times 2^3 = 13.5

Examples:
var pi float32 = 3.14
var avogadro = 6.02e23  // Defaults to float64
Complex Numbers
Represented as complex64 and complex128.
Syntax: real + imaginary parts using i.
Examples:
var c1 = complex(5, 3)   // 5 + 3i
var c2 = 7 + 2i          // Alternative syntax

fmt.Println(c1 + c2)     // Outputs: (12+5i)
fmt.Println(real(c1))    // Outputs: 5
fmt.Println(imag(c1))    // Outputs: 3
Type Conversion
Go requires explicit type conversion between different types.

Examples:
var a int32 = 10
var b int64 = int64(a) // Explicit conversion

var f float64 = 3.14
var i int = int(f)    // Truncates decimal, i = 3
Underscores in Literals
Improve readability.
Supported for both integers and floating-point numbers.
Examples:
var num = 1_000_000   // Same as 1000000
var bin = 0b1010_1010 // Binary literal
var hex = 0xDE_AD_BE_EF // Hexadecimal literal
Usage Tips
Use interpreted string literals (") unless you need multi-line or special character inclusion.
Avoid octal literals (0o or 0). They are rarely used.
Use int or float64 unless you have specific constraints.
These notes provide a solid foundation for revision and quick reference! Let me know if you'd like to expand on any section or add related concepts.

Go Data Types: Integers, Floating-Point, and Complex Numbers
1. Integer Operators
Arithmetic Operators: +, , , /, %.
Integer division (/) truncates towards zero.
Example:
fmt.Println(10 / 3)  // Output: 3
Compound Assignment: Combine arithmetic with =:
Examples:
var x int = 10
x *= 2  // x is now 20
x -= 5  // x is now 15
Comparison Operators: ==, !=, >, <, >=, <=.
Bit Manipulation:
Shift: << (left shift), >> (right shift).
Bitwise: & (AND), | (OR), ^ (XOR), &^ (AND NOT).
Compound operators: <<=, >>=, &=, |=, ^=, &^=.
Example:
var y int = 6  // 0110 in binary
y = y << 1     // Left shift, y is now 12 (1100 in binary)
fmt.Println(y) // Output: 12
2. Floating-Point Types
Types:
float32: ~6-7 decimal digits precision.
float64: ~15-16 decimal digits precision (preferred).
Default: Floating-point literals are float64 by default.
Math Operators: All except %.
Special Division Cases:
Divide by 0: Returns +Inf, Inf, or NaN.
fmt.Println(1.0 / 0.0)  // Output: +Inf
fmt.Println(0.0 / 0.0)  // Output: NaN
Float Comparison:
Avoid direct == and != due to precision issues.
Use epsilon for approximate comparison:
const epsilon = 1e-9
a, b := 0.1+0.2, 0.3
fmt.Println(math.Abs(a-b) < epsilon)  // Output: true
3. Complex Numbers
Types:
complex64: Uses float32 for real and imaginary parts.
complex128: Uses float64 for real and imaginary parts.
Creation:
Use complex(real, imag) function.
Default type is complex128.
var c = complex(2.5, 3.1)
fmt.Println(c)  // Output: (2.5+3.1i)
Operators:
Standard arithmetic (+, , , /).
Comparison (==, !=) - use epsilon for precision.
Extract Parts:
real(c): Real part.
imag(c): Imaginary part.
Example:
c := complex(3.0, 4.0)
fmt.Println(real(c)) // Output: 3
fmt.Println(imag(c)) // Output: 4
Mathematical Functions:
Use math/cmplx for complex functions like magnitude.
fmt.Println(cmplx.Abs(c))  // Output: 5 (magnitude)
4. Floating-Point and Complex Number Notes
Avoid floating-point numbers for exact values like money.
Use libraries like Gonum for numerical computing in Go.
Example Code for All Concepts
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
Here’s a detailed breakdown of the concepts covered in the text:

A Taste of Strings and Runes
Strings in Go:
Built-in Type: Strings are a built-in type in Go, with a zero value of an empty string ("").
Unicode Support: Go supports Unicode, allowing any Unicode character in a string.
Comparison:
Equality (==) and inequality (!=).
Ordering (>, >=, <, <=).
Concatenation: Use the + operator to combine strings.
Immutability: Strings are immutable, meaning their content cannot be modified. However, the string variable itself can be reassigned.
Runes:
Definition: The rune type represents a single code point and is an alias for int32.
Usage:
Preferred for character representation over int32.
Clarifies intent in code.
Example:
var myFirstInitial rune = 'J' // Correct usage
var myLastInitial int32 = 'B' // Legal but less clear
Explicit Type Conversion
No Automatic Promotion:
Go does not perform automatic type promotion (e.g., between integers and floats).
Explicit type conversion is required for operations involving different types.
Examples:
Numeric Type Conversion:
var x int = 10
var y float64 = 30.2
var sum1 float64 = float64(x) + y // Converts x to float64
var sum2 int = x + int(y)        // Converts y to int
fmt.Println(sum1, sum2)          // Output: 40.2 40
Integer Type Conversion:
var x int = 10
var b byte = 100
var sum3 int = x + int(b) // Converts b to int
var sum4 byte = byte(x) + b // Converts x to byte
fmt.Println(sum3, sum4)  // Output: 110, 110
Booleans and "Truthy" Values:
Go has no concept of "truthy" values (non-zero numbers or non-empty strings as true).
To determine truthiness, explicitly use comparison operators:
if x == 0 { ... }      // Check if x is zero
if s == "" { ... }     // Check if string s is empty
Why Explicit Conversion:
Adds verbosity for clarity and reduces confusion about type rules.
Ensures developers explicitly define their intent.
Literals in Go
Untyped Literals:
Integer and floating-point literals are untyped until assigned.
Example:
var x float64 = 10      // Integer literal assigned to a float
var y float64 = 200.3 * 5 // Expression with float literals
Limits:
Type mismatches (e.g., assigning a float literal to an int) result in compilation errors.
Size limitations apply:
var b byte = 1000 // Error: Value exceeds byte limit
Variable Declarations: var vs :=
Using var:
With Type:
var x int = 10
Type Inference:
var x = 10
Zero Value Initialization:
var x int // Zero value: 0
Multiple Declarations:
Same Type:
var x, y int = 10, 20
Different Types:
var x, y = 10, "hello"
Declaration Lists:
var (
    x int
    y = 20
    z int = 30
    d, e = 40, "hello"
    f, g string
)
Using :=:
Short declaration for type inference inside functions:
x := 10
x, y := 30, "hello" // Reassigns x, declares y
Limitations:
Only works within functions.
Cannot declare package-level variables.
Choosing Between var and :=
Use :=:
Default for declaring variables within functions.
Use var:
Declaring at the package level.
Assigning zero value explicitly.
When assigning a literal that needs a specific type.
Avoid Common Pitfalls:
Shadowing variables with :=.
Overusing var or := for package-level variables.
This structured summary captures the essence of the concepts and practices around strings, runes, type conversions, and variable declarations in Go.
