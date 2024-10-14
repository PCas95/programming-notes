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
- `string`
- `symbol`
- `bigint`
- `number`
- `object`

Like in other programming languages, **variables** are used to point to the data, in order to store and manipulate it in a dynamic fashion. They can store different values at different times.

#### Declaration keywords and the assignment operator `=`

To declare (initialise) a variable in JavaScript we use the `var` keyword.

To assign a variable the *assignment operator* `=` is used

```js
var myVariable;

myVariable = 'Ninja Turtle';
```

It is common to initialize a variable to an initial value in the same line as it is declared:

```js
var myVar = 0;
```

In the major JavaScript update ES6, another keyword to declare veriable was added: `let`. Its usage is the same as `var`, with one major difference in behaviour:

- with `var` we can declare the same variable twice, overriding the contents of the old variable with the same name. This will not throw an error.
- with `let` we cannot declare teh same variable twice, because this would throw an error. A variable can only be declared once.

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

