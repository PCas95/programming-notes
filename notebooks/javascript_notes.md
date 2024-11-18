# JavaScript Notes

## Basics

### Comments

In JavaScript, there are 2 kinds of comments:

```js
// in-line comment

/* multi
line
comments */
```

### Semicolons

*Similarly to languages like Perl, statements in JavaScript need to end with a semicolon (*`;`*).*

### Variables and Data Types

There are 8 different **data types** in JavaScript:

- `undefined`
- `null`
- `boolean`
- [`string`](#string-data-type)
- `symbol`
- `bigint`
- [`number`](#number-data-type)
- `object`

Like in other programming languages, **variables** are used to point to the data, in order to store and manipulate it in a dynamic fashion. They can store different values at different times.

#### Declaration keywords and the assignment operator `=`

To declare (initialise) a variable in JavaScript we use the `var` keyword.

To assign a variable, the *assignment operator* `=` is used:

```js
var myVariable;

myVariable = 'Ninja Turtle';
```

It is common to initialize a variable to an initial value in the same line as it is declared:

```js
var myVar = 0;
```

In the major JavaScript update ES6, another keyword to declare veriables was added: `let`. Its usage is the same as `var`, with one major difference in behaviour:

- with `var` we can declare the same variable twice, overriding the contents of the old variable with the same name. This will not throw an error.
- with `let` we cannot declare the same variable twice, because this would throw an error. A variable can only be declared once.

```js
let newVar;
let myVar = 42;
```

When variables are initially declared and no value has been assigned to them, they have an initial value of `undefined`. 

> Numerical operations on `undefined` variables will result in `NaN` ("Not a Number").

#### Rules for variable names

- can contain letters, numbers, `$` and `_`;
- cannot contain blank spaces;
- cannot start with a number.

#### Best Practices

- In JavaScript write multi-word variable names in *camelCase*:

```js
var myVar;
var newVariableName;
var differentNameForVar;
```

- Use `let` instead of `var` if the codebase is large or to avoid debug issues.

> As a general rule of thumb, it is best to use `let` unless `var` is strictly necessary.

#### Constant Values

A third keyword to declare variables in JavaScript is `const`, which has the same features as `let`, but creates a **constant value**, *i.e.* a **read-only** variable. Read-only variables cannot be reassigned (updated or otherwise changed with the assignment operator).

**Best Practices:**

- Read-only (immutable) variables are written in uppercase;
- Mutable values are written in lowercase or camelCase.

#### Properties

Characteristics of values can be consulted by using that value's **properties**, which are not dissimilar in concept from Python's object attributes. Properties are consulted by appending a period `.` and the name of the property to a value.

See properties for each data type at the appropriate section:

- [String properties](#string-properties)

#### Mutable VS Immutable

Values can be **mutable** (*i.e.* can be modified once created), or **immutable** (*i.e.* they cannot be altered once created).

The proper way to "modify" an immutable value is to assign to a new variable the results of that value's processing. For example, creating a new string from an original one.

These are the characteristics of values, regarding mutability:

| Value | Mutable/Immutable |
| --- | --- |
| strings | immutable |
| array entries | mutable |

Note that immutability of a value means that that value **cannot be changed in place**, but it doesn't mean that one cannot reassign a new value to the variable:

```js
let myString = "Bunny";
myString[0] = "H";  // not allowed, will throw an error
myString = "Hunny";  // allowed, does not try to modify string
```

Entries of an array are mutable, and can be changed even if the array was originally declared with [`const`](#constant-values).

### `number` Data Type

Numbers have the `number` data type and can be classified into:

- Integers or whole numbers;
- Decimals or Floating Point Numbers or Floats;

> Floating Point Numbers use the dot (`.`) as decimal separator.

#### Numeric Operators

| Operator | Meaning |
| --- | --- |
| `+` | Addition |
| `-` | Subtraction |
| `*` | Multiplication |
| `/` | Division (quotient) |
| `%` | Division (remainder) |

#### Shorthands

| Operator | Meaning |
| --- | --- |
| `++` | Increment by 1 |
| `--` | Decrease by 1 |

Example:

```js
let myVar = 4;
console.log(myVar);
4
myVar++;
console.log(myVar);
5
```

#### Augmented Operators

JavaScript uses **augmented operators** as shortcuts for **compound assignments**, allowing to modify a numeric value stored in a variable, without the need to write the full line (*e.g.* `myVar = myVar + 40`).

| Augmented Operator | Meaning |
| --- | --- |
| `+=` | Add value to variable |
| `-=` | Subtract value from variable |
| `*=` | Multiply variable by value |
| `/=` | Divide variable by value |

Example:

```js
let myVar = 4;
myVar += 38;
console.log(myVar);
42
```

### `string` Data Type

Strings are written between single (`''`) or double (`""`) quotes. Quotes inside the string itself can be added as simple characters by escaping them with backslash (`\`).

> **NOTE:** Unlike other programming languages, single and double quotes work the same way in JavaScript.
>
> We are allowed to use any quote because in some cases it may be needed to use both in a string, for example when saving an `<a>` tag with various attributes in quotes, all within a string.

Another way to circumvent the "quotes within quotes issue" is to use different kind of quotes for inside and outside the string.

Examples:

```js
const stringOne = '<a href="http://www.example.com" target="_blank">Link</a>';
let stringTwo = "Mikey said: \"Kawabunga!!!\"";
```

Characters that need to be escaped inside strings:

| Code | Output |
| --- | --- |
| \\' | single quote |
| \\" | double quote |
| \\\ | backslash |
| \n | newline |
| \t | tab |
| \r | carriage return |
| \b | backspace |
| \f | form feed |

#### String Operators

| Operator/Augmented Operator | Meaning |
| --- | --- |
| `+` | Concatenation |
| `+=` | Concatenation to string variable |
| `*` | Multiplication |

> String variables are added to a string in the same way as Python: by concatenating the string variable outside the quotes, to allow **variable interpolation**.

#### String Properties

##### `.length`

Returns the string's number of characters.

```js
let myVal = "somestringverylongandwithoutspaces!!";
console.log(myVal.length);
36
```

### Bracket notation 

A string can be seen as list of characters. As such, any character in the string can be returned using bracket notation with the appropriate index (starting from 0):

```js
const name = "Leonardo";
let letter = name[0];
console.log(letter);
L
```

To get the last letter of a string, we can subtract 1 from the string's length:

```js
var tName = "Michelangelo";
let letter = name[name.length - 1];
console.log(letter);
o

var tName = "Raffaello";
let letter = name[name.length - 3];
console.log(letter);
l
```

The bracket notation is also used to access entries of an array, which is in fact a list of elements (see [`array` variables](#array-variables).

### `array` Variables

JavaScript arrays are variables able to store lists of elements, called *entries*. Just like arrays in Perl and lists in Python, their notation require to enclose entries between square brackets `[]`, separated by commas.

Arrays can store values of different types simultaneously, even other arrays (**nested arrays** or **multi-dimensional arrays**). Entries are accessed through indexing, starting from 0. Entries of *multi-dimensional arrays* are accessed with *multi-indexing*.

```js
var myArray = ["Bulbasaur", "Charmander", "Squirtle"];
var pokeDex = [["Bulbasaur", 1], ["Charmander", 4], ["Squirtle", 7]];

console.log(myArray[2]);
Squirtle

console.log(pokeDex[0][1]);
1
```

#### Array Methods

##### `.push()`

The  `.push()` method allows to add to the end (right side), or append to the array, the argument values. It returns the new length of the array.

```js
pokeDex.push(["Caterpie", 10]);
```

##### `.pop()`

The `.pop()` method is used to remove the value at the end (right side) of an array. When using `.pop()`, not only it removes the last element of the array, it also retuns it, so it can be used to assign that element to a new variable.

```js
let myFirstBug = pokeDex.pop();
```

##### `.shift()`

The `.shift()` method removes the first element of an array and works in the same way as `.pop()`.

##### `.unshift()`

The `.unshift()` method adds an element at the beginning (left side) of an array. It works just like `.push()`.

## Functions

Functions are reusable parts of code: they can be defined and later called or *invoked*. The syntax to define a function is halfway between Python's functions and Perl's subroutines, and requires the **`function`** keyword:

```js
function myFunction() {
  console.log(pokeDex);
}

myFunction();
```

Each time the function is called, it will execute the code between curly braces.

Additional arguments can be passed to a function by using *parameters*, placeholders for values that are used as input for a function. A function can be defined along with one or more parameters (separated by comma if more than one). 

```js
function myFunction(arr, ix) {
  console.log(arr[ix]);
}

myFunction(pokeDex, 1);
```

### The `return` Statement

The `return` statement is used to send a value back out of a function:

```js
function myFunction(arr, ix) {
  return arr[ix];
}

let myOutcome = myFunction(pokeDex, 1);
```



