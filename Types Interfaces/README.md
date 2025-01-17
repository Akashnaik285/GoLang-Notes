
CHAPTER 7
Types, Methods, and Interfaces
As you saw in earlier chapters, Go is a statically typed language with both built-in
types and user-defined types. Like most modern languages, Go allows you to attach
methods to types. It also has type abstraction, allowing you to write code that invokes
methods without explicitly specifying the implementation.
However, Go’s approach to methods, interfaces, and types is very different from that
of most other languages in common use today. Go is designed to encourage the best
practices advocated by software engineers, avoiding inheritance while encouraging
composition. In this chapter, you’ll take a look at types, methods, and interfaces, and
see how to use them to build testable and maintainable programs.
Types in Go
Back in “Structs” on page 61, you saw how to define a struct type:
type Person struct {
FirstName string
LastName string
Age int
}
This should be read as declaring a user-defined type with the name Person to have
the underlying type of the struct literal that follows. In addition to struct literals, you
can use any primitive type or compound type literal to define a concrete type. Here
are a few examples:
type Score int
type Converter func(string)Score
type TeamScores map[string]Score
143
Go allows you to declare a type at any block level, from the package block down.
However, you can access the type only from within its scope. The only exceptions are
types exported from other packages. I’ll talk more about those in Chapter 10.
To make it easier to talk about types, I’ll define a couple of terms.
An abstract type is one that specifies what a type should do but not
how it is done. A concrete type specifies what and how. This means
that tthe type has a specified way to store its data and provides
an implementation of any methods declared on the type. While all
types in Go are either abstract or concrete, some languages allow
hybrid types, such as abstract classes or interfaces with default
methods in Java.
Methods
Like most modern languages, Go supports methods on user-defined types.
The methods for a type are defined at the package block level:
type Person struct {
FirstName string
LastName string
Age int
}
func (p Person) String() string {
return fmt.Sprintf("%s %s, age %d", p.FirstName, p.LastName, p.Age)
}
Method declarations look like function declarations, with one addition: the receiver
specification. The receiver appears between the keyword func and the name of the
method. Like all other variable declarations, the receiver name appears before the
type. By convention, the receiver name is a short abbreviation of the type’s name,
usually its first letter. It is nonidiomatic to use this or self.
There is one key difference between declaring methods and functions: methods can
be defined only at the package block level, while functions can be defined inside any
block.
Just like functions, method names cannot be overloaded. You can use the same
method names for different types, but you can’t use the same method name for
two different methods on the same type. While this philosophy feels limiting when
coming from languages that have method overloading, not reusing names is part of
Go’s philosophy of making clear what your code is doing.
144 | Chapter 7: Types, Methods, and Interfaces
I’ll talk more about packages in Chapter 10, but be aware that methods must be
declared in the same package as their associated type; Go doesn’t allow you to add
methods to types you don’t control. While you can define a method in a different file
within the same package as the type declaration, it is best to keep your type definition
and its associated methods together so that it’s easy to follow the implementation.
Method invocations should look familiar to those who have used methods in other
languages:
p := Person{
FirstName: "Fred",
LastName: "Fredson",
Age: 52,
}
output := p.String()
