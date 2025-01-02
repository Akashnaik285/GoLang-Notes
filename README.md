# GoLang-Notes


  
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
