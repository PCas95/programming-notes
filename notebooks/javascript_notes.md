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

### `number` Data Type

Numbers have the `number` data type and can be classified into:

- Integers or whole numbers;
- Decimals or Floating Point Numbers or Floats;

> Floating Point Numbers use the dot (`.`) as decimal separator.

#### Operators

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

JavaScript uses **augmented operators** as shortcuts cor **compound assignments**, allowing to modify a numeric value stored in a variable, without the need to write the full line (*e.g.* `myVar = myVar + 40`).

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















