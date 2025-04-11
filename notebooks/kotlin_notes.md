# Kotlin Programming

## Comments

Kotlin's comments are borrowed from JavaScript, so there are 2 types of them:

- **Single-line comments** (double forward slash)
	```kotlin
	// this is a single-line comment
	```
- **Multi-line comments** (start and end with one forward slash and a star)
	```kotlin
	/*
	this is 
	a multi-line
	comment
	*/
	```

## Variables

Kotlin differentiates between **read-only** and **mutable** data.
Mutable variables can be reassigned to a different value later on.
Read-only variables cannot be reassigned, once initialised.

- To declare a mutable variable, use the `var` keyword:
	```kotlin
	var year = 1984
	```

- To declare a read-only variable, use the `val` keyword:
	```kotlin
	val theAnswer = 42
	```

> Read-only variables should be preferred whenever possible, that is whenever we know the value will not have to change in the code.

### Data types

There are 4 kinds of values in Kotlin, each represented by specific data types, as summarised in the table below.
Generally speaking, Kotlin uses different data types to store values of increasing size.

<table>
	<tr>
		<th>Values</th>
		<th>Data Types</th>
		<th>Example</th>
		<th>Description</th>
	</tr>
	<tr>
		<td rowspan="4">Integers</td>
		<td>byte</td>
		<td>127</td>
		<td>The shortest data type for integers: up to 3 digits</td>
	</tr>
	<tr>
		<td>short</td>
		<td>32767</td>
		<td>Up to 5 digits</td>
	</tr>
	<tr>
		<td>int</td>
		<td>2147483647</td>
		<td>Up to 10 digits</td>
	</tr>
	<tr>
		<td>long</td>
		<td>9223372036854775807</td>
		<td>The longest data type for integers (any number of digits). By appending a capital `L` to a number, inference as long data type is coerced</td>
	</tr>
	<tr>
		<td rowspan="2">Floating Point Numbers</td>
		<td>float</td>
		<td>3.4028235e38f</td>
		<td>The shortest data type for floats, or the less precise. Uses `e` for exponentiation and has a trailing `f`, to mark the value as a float</td>
	</tr>
	<tr>
		<td>double</td>
		<td>1.7976931348623157e308</td>
		<td>The longest data type for floats, or the more precise. Uses `e` for exponentiation. Any floating point number that does not have a trailing `f` is automatically inferred as double by Kotlin</td>
	</tr>
		<tr>
		<td rowspan="2">Text</td>
		<td>char</td>
		<td>'#'</td>
		<td>Single character. For values of char data type, single quotes are used</td>
	</tr>
	<tr>
		<td>string</td>
		<td>"This is a string of text"</td>
		<td>Strings of text: sequence of any number of characters. For strings, Kotlin uses double quotes</td>
	</tr>
	</tr>
	<tr>
		<td rowspan="2">Boolean</td>
		<td>---</td>
		<td>true</td>
		<td>---</td>
	</tr>
	<tr>
		<td>---</td>
		<td>false</td>
		<td>---</td>
	</tr>
</table>

### Kotlin's Variable Assignment and Type Inference

Declaring a variable to store a value would normally require to declare the data type.

**Syntax:**

```kotlin
// for integer values 
val byte: Byte = 127
val short: Short = 32767
val int: Int = 2147483647
val long: Long = 9223372036854775807
// for floating point values
val float: Float = 3.4028235e38f
val double: Double = 1.7976931348623157e308
// for text
val character: Char = '#'
val text: String = "El Barto was here!"
// for Boolean values
val yes: Boolean = true
val no: Boolean = false
```

The data type of a value that is going to be stored in the variable is declared after the `val` or `var` keyword, and followed by a semicolon. After the semicolon the actual assignment is written, with the name of the variable on the left side of the assignment operator (`=`), and the value on its right side.

**Kotlin's type inference** allows to omit declaring types in the code, because the compiler is able to infer the data type of the value.

> Since Kotlin's compiler is able to infer the data type of most values, adding the type at variable declaration is optional in Kotlin.

For integer values, the compiler infers `int` by default. Using the `L` suffix will transform the value into a `long` data type (example: `42L`). For floating point numbers the compiler infers `double`by default, unless a trailing `f` is added as suffix (example: `1.23f`), in which case it is considered a `float`. There's no shortcut suffix for `byte` or `short` data types.

> **Kotlin's type inference works also with objects.**

### String Interpolation

A variable can be included in a string and still be evaluated (interpolated) in the same way as in Bash: by prepending a `$` to the variable name. The same is achieved by prepending the variable with `$` and wrapping it in curly braces (`{}`), which is necessary for more complex constructs.

```kotlin
val pokemon = "Mewtwo"
println("No data for pokemon $pokemon")
println("No data for pokemon ${pokemon}")
```

## Comparison Operators

| Operator | Meaning |
| -------- | ------- |
| `==`     | Structural equality (standard equality operator) |
| `!=`     | Structural inequality |
| `===`    | Referential equality (same memory address) |
| `!==`    | Referential inequality |
| `>`      | Greater than |
| `<`      | Less than |
| `>=`     | Greater than or Equal to |
| `<=`     | Less than or Equal to |

## Conditionals: `if` and `else` Statements

> As JavaScript, Kotlin does not have an `elif` keyword like Bash or Python: it uses the combination of the two conditional keywords: `else if`.

Conditionals can be set up with `if`, `else` and `else if` blocks of code. After the `if` and `else if` keywords, the condition is written inside round parentheses. The block of code for any conditional keyword is delimited by curly braces.

```kotlin
if (pokemon == "Bulbasaur") {
	println("It's Bulbasaur! It's a great Pokèmon to train!")
} else if (pokemon == "Charmander") {
	println("It's Charmander! It's a difficult Pokèmon to train.")
} else {
	println("It's Squirtle! It's a good Pokèmon to train!")
}
```

- `if` block is always the first one;
- there can be any number of `else if` blocks, but only after an `if` block;
- there can only be up to one `else` block, and has to be the last one. 
