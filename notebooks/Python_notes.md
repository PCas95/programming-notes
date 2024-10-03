# Python notes

## Main facts

### Taking input

#### Reading files

Python's way to read files is very much like Perl's: it has an [`open()` function](#open) to obtain a reference to the specified file, that must be assigned to a special variable called a "filehandle".

To read a file line by line, a `for` loop with the `in` operator is used. We also have an equivalent for Perl's `chomp()`: the method [`.rstrip()`](#methods).

To read the whole file we use another method in Python ([`.read()`](#methods)), together with the assignment to a [variable](#variables). This will read the whole file as a single line (newlines are in between the original lines as escape characters `\n`). The whole file can also be read using the `.readlines()` method, generating a list; each item of the list is a string: the file's lines.

> **Note:** `.readlines()` and `.read()` will read empty strings if the filehandle's contents have already been parsed.

Examples:

```py
# open filehandle ('r' is for 'read' and is default, so it can be omitted)
fhandle = open('Python_notes.md', 'r')
# read line by line
for line in fhandle:
    strline = line.rstrip()
# read whole file as a single line
whole_file = fhandle.read()
# read whole file as a list of strings
lines_as_list = fh.readlines()
```

#### Getting arguments from command line

Python can [import](#import) the `sys` module to use the `sys.argv()` function, which allows Python to take inputs from command line:

```py
import sys

inp = sys.argv[1]
fhandle = open(inp)
```

or:

```py
import sys

inp_1, inp_2 = sys.argv[1:]
fhandle = open(inp_1)
```

In the examples above, the elements of the ARGV array are assigned to variables just like in Perl. Then a line with the `open()` function is used to create the filehandles.

Like in Perl, in the ARGV array the index 0 is reserved to the script, so the arguments passed through terminal will start at index 1.

The second example uses a shortcut to assign multiple variables at once.

Using `sys.argv()` is suited for small scripts, but for full-fledged programs that may have multiple options or need to be shared, a much better way to get inputs is [`argparse()`](#argparse-module).

### Argument

An argument is a value being passed to a function when the function is called (what we type between parentheses).

### Parameter

Variables that have arguments assigned to them.

### Backslash

The `\` character, as in Bash coding, can be used as a "line continuation character", telling Python that the line of code continues on the following line. In that case indentation is not relevant. `\` can be used to make long lines of code more readable.

## Variables

The character `=` is called the "commute operator": it allows to set a variable, so that every time the variable is called, it will be expanded (or interpolated) to the data stored into it.

Example: `my_var = "bananas"`

An input can also be a variable: `std_in = input()`

> Setting a variable name has some restrictions:
>
>- it can't start with a number;
>- can't be more than one word (no blank spaces allowed);
>- can only contain letters, numbers and underscore (`_`).

## Values

*SEQUENCE DATA TYPES:*

*In Python sequence data types include lists, strings, range objects returned by* `range()`*, and tuples. Many of the things that can be done with lists can also be done with strings, for example. This include using the* `in` *and* `not in` *operators, slicing, indexing, using in* `for` *loops and using them as arguments for the* `len()` *function.*

*MUTABLE AND IMMUTABLE DATA TYPES:*

*In Python, some values can be changed, others cannot. A list is a mutable data type, since it can have values added, removed, or changed. A string, on the other hand, is immutable, because it cannot be changed. For example, although strings support indexing (being sequences), indices and slices cannot be used to change part of the string value, since that would raise a* `TypeError` *[exception](# <!-- here link to paragraph on try-except -->). The proper way to "mutate" a string is by using slicing and concatenations to build a new string, or to use [string methods](#methods-for-strings-string-library).*

### Integers

Integers are integer (non-decimal) numbers.

> When Python is asked to perform math using integer and [floating point numbers](#floats), it automatically evaluates in floating point numbers, so the result will be a float.

### Floats

Floats (or floating point numbers) are numbers with decimals.

### Strings

Strings are text values, always written between quotes (`''` or `""`).

> **Note:** input from shell is always a string value.

### Boolean values

`True` or `False`, always with capital letter.

> **Note**: when used in conditions, 0, 0.0 and "" (empty string) are considered logically False, while all other values are considered True.

### The `None` value

`None`, always with capital letter, represents the absence of a value, and is the only value of the `NoneType` data type.

### Lists

Lists are values that contain other values. They are delimited by square brackets (`[]`), with the items inside separated by a comma and a space.

Examples: `[1, 2, 3]` or `["bulbasaur", "charmander", "squirtle"]`.

A list can contain values of different types together, even other list values (**nested lists**). An empty list `[]` is an empty value (same as `""`).

Lists (or variables that contain a list, or functions that return a list, like `range()`) are commonly iterated through in [`for` loops](#the-for-loop).

> **Note**: lists can ignore indentation: Python knows that a list is not over until there is the closing square bracket, so a list can be written in multiple lines to make it more readable. Just like hashes in Perl, also Python [dictionaries](#dictionaries) can be treated the same way.

#### Indices

A reference to an item stored in a list is obtained by using an index value, which is a number corresponding to the position of that item in the list. Indices (or indexes) start from number 0, so an index is an integer from 0 to n-1, where n is the number of items of the list.

Examples:

```py
    >>> starters = ["bulbasaur", "charmander", "squirtle"]  
    >>> starters[2]
    squirtle
    >>> starters[0]
    bulbasaur
```

Negative numbers can be used to access the items of a list from the right-hand of the list:

```py
    >>> starters = ["bulbasaur", "charmander", "squirtle"]  
    >>> starters[-2]
    charmander
    >>> starters[-1]
    squirtle
```

Nested list items are accessed with multiple indices:

```py
    >>> donut = [1, 2, ["a", "b"]]
    >>> donut[2][0]
    a
```

A range or part of the items in a list can be obtained using **slices** (examples: `[1:4]`, `[:3]` or `[2:]`). Slices will create a list out of the items of another list, **up to the second integer (not included)**. Omitting the first or second index number means "from the start of the list" and "to the end of the list", respectively, while the usage of just `[:]` matches the whole list.
  
Example:
  
``` py
    >>> num = [1, 2, 3, 4, 5]
    >>> num[1:3]
    [2, 3]
```

The commute operator can be used to change the value of an item in a list:

```py
    >>> starters = ['bulbasaur', 'charmander', 'squirtle']
    >>> print(starters)
    ['bulbasaur', 'charmander', 'squirtle']
    >>> starter[0] = 'pikachu'
    >>> print(starters)
    ['pikachu', 'charmander', 'squirtle']
```

### Dictionaries

Dictionaries in Python are the same thing as Perl's hashes: objects that store *key-value pairs*. Differently than a list, order doesn't matter in a dictionary.

*Syntax:*

```py
>>> pokes = { }
# or 
>>> pokes = dict()

>>> pokes['fire'] = 'charmander'
>>> pokes['grass'] = 'bulbasaur'
>>> pokes['water'] = 'squirtle'
>>> print(pokes)
{'fire': 'charmander', 'grass': 'bulbasaur', 'water': 'squirtle'}
>>> pokes['grass'] = 'oddish'
```

> The first and second examples show 2 ways of initialising an empty dictionary.

> The other examples show how to create a new key-value pair and how to change the value associated to a key.

> **Note:** using the [`in`](#in) keyword on an empty dictionary will raise an error, so to iterate on them is better to use the [`not in`](#not-in) keyword: this way we can check for existence (with [`if` statements](#if-statement) or in [`while` loops](#the-while-loop)) without raising an error.

#### Histograms and counts with dictionaries

Due to their nature, dictionaries are the perfect tools to store data to create histogram-like graphs, since they are perfect to keep count of items (the keys) and are well integrated in [`for` loops](#the-for-loop):

```py
# we create an empty dictionary
hist = dict()
# we create (or import) a list of items
nucleotides = ['A', 'T', 'C', 'G', 'N']

# for each item in the list
for base in nucleotides:
    # if it's the first time we encounter it
    if base not in hist:
        # create key-value pair for it with value 1
        hist[base] = 1
    # if it's not the first time
    else:
        # increase the count by 1
        hist[base] = hist[base] + 1
```
 The 4 lines of the if-else statement above are so common to use on dictionaries, that they have a [method](#methods) just to do that: [the `.get()` method](#get). What `.get()` actually does is check if something is in a list, if it isn't, it returns a default value. The example below provides a reference for the same `for` loop above, but with collapsed syntax due to the usage of the `.get()` method.

```py
for base in nucleotides:
    hist[base] = hist.get(base, 0) + 1
```

#### Looping through dictionaries

When looping through a dictionary, we walk the keys, not the values. Depending on how we generate the iteration variables though, we may be able to get both (see [.items()](#items)):

```py
# this is how to create a pre-populated dictionary
pokes = {'fire': 'charmander',
        'grass': 'bulbasaur',
        'water': 'squirtle'
        }

# looping through a dictionary walks the keys
# We can obtain the same result by using pokes.keys(), which returns the keys of a dictionary
for key in pokes:
    print(i, pokes[i])
# .items() returns a tuple: the key and the value, so we need 2 iteration variables
for key, value in pokes.items():
    print(key, value)
```

Also to notice is that if we run the loop again we may not walk the keys in the same order as before, because *in dictionaries order doesn't count* and the order of the key-value pairs can change as we add or remove them.

### Tuples

Tuples are objects identical to lists, except they are an immutable data type and are written between round parentheses rather than square brackets.

When only one value is in a tuple, in order to let Python know it is an actual tuplet and not some other value between parentheses, a trailing comma is required after the value (example: `my_tuple('only one',)`).

The reason why tuples are immutable is effieciency: they are faster to read and take up less storage in memory, so **if a list should not be modified**, but just read, **a tuplet should be used**.

**Tuples are comparable** in the same way strings are (both compare each item or character in order and they do not care about the following ones if the current comparison is discriminatory). This feature is exceptionally useful to sort dictionaries by key:

```py
>>> d = {'b':42, 'c':56, 'a':1}
>>> print(d)
{'b': 42, 'c': 56, 'a': 1}
>>> sorted(d.items())
[('a', 1), ('b', 42), ('c', 56)]
```

```py
for (k,v) in sorted(d.items()):
    print(k, v)
```

Or based on values instead of keys:

```py
>>> d = {'b':42, 'c':133, 'a':22}
>>> tmp_lst = list()
>>> for (k,v) in d.items():
...     tmp_lst.append( (v,k) )
... 
>>> print(tmp_lst)
[(42, 'b'), (133, 'c'), (22, 'a')]
>>> tmp_lst = sorted(tmp_lst, reverse=True)
>>> print(tmp_lst)
[(133, 'c'), (42, 'b'), (22, 'a')]
```

#### Tuple unpacking

Tuples also allow to use the [commute operator](#variables) in a multiple assignment statement, also called **tuple unpacking**: a shortcut to assign multiple items, or the values in a tuple or list, to multiple variables with just one line of code. The items will be assigned to the variables in the order in which both are listed. The variable names will have to be separated by a comma and a space and will have to be the exact same number of the items on the right side of the statement:

```py
# using parentheses (base tuples syntax)
(x, y) = ('abscissa', 'ordinate')
# if cat is a list of three items
size, color, disposition = cat
```

*Tuples can also be used to iterate through dictionaries in* `for` *loops, allowing to advance 2 iteration variables at the same time with the* [`.items` method](#items).

#### List comprehension

Python allows to put an expression inside square brackets, to write loops in a more compact way. That expression will act as a generator of all the elements of a list. Such feature is called **list comprehension** and it creates a **dinamic list**:

```py
print(sorted([(v,k) for (k,v) in d.items()]))
# which is equivalent to:
lst = list()
for (k,v) in d.items:
    tup = (v,k)
    lst.append(tup)
print(sorted(lst))

# more examples:
my_list = [x.rstrip() for x in f.readlines()]
my_list = [dc[i] for i in dc.keys()]
```

List comprehension is less easy to understand at first glance, but offers the advantages of being more succint and of avoiding to create extra variables (like in the standard `for` block of code).

## Operators

| Operators | numeric values | strings | lists | Augmented Assignment Operators | Equivalent assignment statement |
| --------- | ------------- |-------------- | ----- | ------------------------------ | ------------------------------- |
| +  | addition                      |  concatenate   |  concatenate   |       spam += 1         |     spam = spam + 1 (can concatenate strings and lists) |
| -  | subtraction                   |                |                |       spam -= 1         |     spam = spam - 1 |
| *  | multiplication                |  replicate     |  replicate     |       spam *= 1         |     spam = spam * 1 (can replicate strings and lists) |
| /  | division                      |                |                |       spam /= 1         |     spam = spam / 1 |
| // | division (integer quotient)   |                |                |       -------           |     ----------      |
| %  | division's remainder          |                |                |       spam %= 1         |     spam = spam % 1 |
| ** | exponentiation/power          |                |                |       -------           |     ----------      |

### `=`: commute operator (only for variables)

Stores (assigns) a value into a variable.

### Comparison operators (evaluate down to a single Boolean value)

| Operator | meaning |
| ---- | ---- |
| `==` | equal to |
| `!=` | not equal to |
| `<`  | less than |
| `>`  | greater than |
| `<=` | less than or equal to |
| `>=` | greater than or equal to |

### Boolean operators (evaluate down to a single Boolean value)

| Operator | kind | evaluation |
| ---- | ---- | ---- |
| `and` | binary operator (takes 2 Boolean values or expressions and evaluate them into a single Boolean value). | Evaluates down expressions to `True` only if both values or expressions are True. |
| `or` | binary operator (takes 2 Boolean values or expressions and evaluate them into a single Boolean value). | Evaluates down expressions to `True` if either of them is True. |
| `not` | unary operator (operates on only 1 expression or value). | Evaluates down to the opposite Boolean value. |

### `in` and `not in` operators (evaluate down to a single Boolean value)

Binary operators which are used to check if a value is an item in a list (or string).

| Operator | syntax | evaluation
| --- | --- | --- |
| `in` | `<value> in <list>` | Evaluates down to `True` if the value is in the list/string, or to `False` if it isn't |
| `not in` | `<value> not in <list>` | Evaluates down to `True` if the value isn't in the list/string, or to `False` if it is |

### `is` and `is not` operators (evaluate down to a single Boolean value)

Logical operators which are used to check if a value corresponds to something.

`is` and `is not` *are stronger than* `==` and `!=`, because **they mean identity** (both in value and type), rather than equality (just value).

> It is wise not to overuse `is` and `is not`, and to reserve them for evaluation of Boolean values and None value.

## Functions

> **Note:** functions can have keyword arguments that are used to change how the function behaves. For example, the `print()` function can be called with the keyword arguments `sep=''`, `end=''` or `file=`.

### `open()`

It opens the file used as argument. Such reference to a file has to be assigned to a variable, called a "filehandle" and it is not in itself the contents of the file. While in Perl's syntax the name of the filehandle is specified inside the parentheses, in Python we use the standard variable assignment syntax:

```sh
fhandle = open('Python_notes.md')
```

`open()` accepts additional string arguments to specify the mode in which the file is opened, for example read mode (`open('file.txt', 'r')`) or write mode (`open('file.txt', 'w')`).

### `exit()`

Exits from Python's interactive interpreter.

### `print()`

Prints the arguments to standard output.

Keyword arguments:

- `sep=''`: changes the separator used when passing multiple string values to `print()` with the character specified in quotes;

```py
>>> print("print", "this", "message")
print this message
>>> print("print", "this", "message" sep=':')
print:this:message
```

- `end=''`: removes the newline character that is automatically set at the end of a `print()` function call, or replaces it with a different character; 

```py
print("Hello")
print("There")

Hello
There

print("Hello" end='')
print("There")

HelloThere
```

### `int()`, `str()` and `float()`

These functions are used to transform the arguments into their corresponding integer, string or floating point values. Python can only concatenate string values with other strings, or do math with integer and floating point values. Trying to concatenate an integer to strings would [raise an error](#try-except): it must be told to treat integers (or floats) like string values beforehand. Note that if floats are converted to integers through `int()`, the `int()` function will always round down.

### `type()`

Returns information about the type of the variable used as argument. Can be used in [`if` statements](#if-statement) to check for data type, together with the `is` and `is not` operators.

### `list()` and `tuple()`

`list()` and `tuple()` will return list and tuple versions of the values passed to them.

`list()` also creates an empty list, when used without arguments:

```py
my_empty_list = list()
```

### `dict()`

Creates an empty dictionary.

### `round()`

Rounds up or down floats to the specified number of decimals:

```py
# second argument specifies number of decimals.
# Default is the nearest integer, if no number of decimals is given
round(12.367, 1)
```

### `abs()`
        
Returns the absolute (non-negative value) value of a number:

```py
>>> abs(-5)
5
>>> abs(-89,23)
89,23
```

### `id()`

All values in Python have a unique identity (the numeric memory address where the value is stored) that can be returned with the `id()` function. 

### `len()`

Returns the number of characters (in integer) of the specified values, or the number of items in a list.

### `range()`

Returns a list of items of length *x*, where *x* is the argument (an integer number), starting from 0 to *x*-1.

In loops, this results in effectively counting to the value in parentheses, so it's often used to set up a `for` loop.

`range()` can be called with up to 3 arguments in parentheses, separated by commas:

```py
# 1st argument: starting point
# 2nd argument: end point
# 3rd argument: step or interval or pace

## count onward from 0 to 10, by 2
range(0, 10, 2)
## count backwards, from 5 to -1, by 1
range(5, -1, -1)
```

> **Note:** `range()` always counts from the first argument (included) to the second argument **not included** (in the first example `range()` stops counting at 8).

A common trick is to use the construction `for i in range(len(mylist)):`: which gives the additional benefit of being able to access both the index value (through the iteration variable), and the item in the list, because the index can be used to extract the value. See also [`enumerate()`](#enumerate).

```py
>>> mylist = ['charmander', 'squirtle', 'bulbasaur', 'pikachu', 'eevee']
# iteration variable gets list items
>>> for i in mylist:
...     print(i)
... 
charmander
squirtle
bulbasaur
pikachu
eevee
# with range(len()), iteration variable gets list indices
>>> for i in range(len(mylist)):
...     print(i)
...     print(mylist[i])
... 
0
charmander
1
squirtle
2
bulbasaur
3
pikachu
4
eevee
```

### `enumerate()`

Used with a list as argument, it returns two values: the index of the item in the list, and the item in the list itself.

Very helpful in `for` loops, it requires the usage of 2 iteration variables.

### `min()`

Returns the smallest item in a list.

### `max()`

Returns the largest item in a list.

### `sum()`

Sums up all the values in the argument list (items in the list have to be values belonging to the same type).

To get the average in a list containing number values: `sum(mylist)/len(mylist)`

### `dir()`

Lists all the methods available for the argument value OR all functions and methods from the argument module.

## Statements

### Conditional statements

### `if` statement

**Syntax requirements:**

- the `if` keyword
- a condition
- `:`
- an indented "if clause"

**Example:**

```py
if input() == 42:
    print("do this")
```

### `else` statement

**Syntax requirements:**

- the `else` keyword
- `:`
- an indented "else clause"

**Example:**

```py
if input() == 42:
    print("do this")
else:
    print("do that")
```

### `elif` statement

**Syntax requirements:**

- the `elif` keyword
- a condition
- `:`
- an indented "elif clause"

**Example:**

```py
if input() == 42:
    print("do this")
elif input() < 42:
    print("do that")
elif input() > 42:
    print("dance for me babe")
```

### Loop statements

### The `while` loop

**Syntax requirements:**

- the `while` keyword
- a condition
- `:`
- an indented "while clause"

**Example:** 

```py
while password != 42:
    print("you shall not pass")
```

### The `for` loop

**Syntax requirements:**

- the `for` keyword
- an iteration variable
- the `in` keyword
- an iterable object or a generator of an iterable object
- `:`
- an indented `for` clause

**Example:**

```py
for i in range(10)
    print(i)
```

### `break` and `continue`

`continue` is used inside a `while` or `for` block of code to skip to next iteration (Monopoly's "Go to Start");

`break` is used inside a `while` or `for` block of code to instantly get out the loop and go to the code downstream (Monopoly's "Got to jail, you don't pass through Start.").

```py
for i in list_of_characters:
    print('scanning...')
    if i == "Yugi's grandpa":
        print(i)
        break
    else
        continue  
```

### `def`

Creates a new function.

**Syntax requirements:**

- the `def` keyword
- a name for the function, followed by `()` placeholder(s) for argument(s), if any
- :
- an indented block of code, which will be the function code

**Example:**

```py
def ():
```

### `return`
    used in def blocks of code, it sets a value or expression the function evaluates to.
    syntax:
    - the return keyword
    - the value or expression the function should return

    ex.: def answer(number):
            if number == 1:
                return "Congratulations!"
            elif number == 2:
                return "Don't push your luck..."
            ...

*NOTE: use `return` compulsorily as the last string of a function. Following it up with more code has caused the functions to behave abnormally.*


global
    variables can be either *local* (they exist only in a "local scope", which is the function that defines them, they cannot be accessed by other functions and when the function
    returns, those variables are forgotten) or *global* (when those variables exist outside all functions, or in the "global scope" and can be accessed by all functions in the
    program). 
    There are 4 rules that define how functions interact with local and global variables:
    - Local variables cannot be used in the Global Scope;
    - Local Scopes cannot use variables in other Local Scopes;
    - Global variables can be read from a Local Scope;
    - Local and Global variables can have the same name (even though that's not a very good idea). 
    The `global` statement is used at the beginning of functions when we want to modify a global variable, thus telling the function that the specified variable refers to the global
    variable, so it doesn't have to create a local variable with that name. In the following example, the `global` statement is used to tell the defined function `stitch()` that the
    `shoes` variable inside the function's code refers to the global `shoes` variable, so when `stitch()` is called, it does not create a local variable named `shoes`, but it modifies
    the global variable `shoes`, thus when we pass it to the `print()` function, it will return 1.

    ex.: shoes = 2
         def stitch():
            global shoes
            shoes = 1

         stitch()

         print(shoes)

    There are four rules to tell whether a variable is in a local scope or global scope:

    - If a variable is being used in the global scope (outside of all functions), then it is always a global variable.
    - If there is a global statement for that variable in a function, it is a global variable.
    - Otherwise, if the variable is used in an assignment statement in the function, it is a local variable.
    - But if the variable is not used in an assignment statement, it is a global variable.


### `try` & `except`
    error handling is done through a `try` statement which points to an `except` statement.
    syntax:
    - the `try` keyword
    - :
    - an indented try clause. The code that could potentially have an error is put in the try clause
    - the `except` keyword
    - the error type raised by Python (optional)
    - :
    - an indented except clause. The code that has to be executed if an error occurs is put in the except clause

    ex.: try:
	        input_number = int(input())

         except ValueError:
	        print("Error: invalid argument." end="")
	        print(" Please enter an integer number as argument. Decimals will be rounded down." end="")
	        print(" Text is not allowed.")
	        exit()


    *note: there can be one or more `except` clauses, like when there is the need to try handling different error types in the code in different ways. Furthermore, the `except`
    keyword may not be followed by an error type (like `TypeError`, `ValueError`...) and in that case, whatever the error, the same `except` clause is executed.*


del
    deletes an item from a list.
    syntax:
    - the `del` keyword
    - the variable in which is stored the list, with the index number of the item to be removed

    ex.: >>> starter = ["bulbasaur", "charmander", "squirtle"]
         >>> del starter[1]
         >>> starter
         ["bulbasaur", "squirtle"]

    *note: the `del` statement can also be used on a simple variable to delete it, as a sort of "unassignment" statement, but it's hardly ever needed.*

### `import`

The `import` statement imports the argument modules, granting access to functions from them.

The syntax requires the `import` keyword followed by the module(s) name(s), separated by commas if more than one is imported (*e.g.* `random`, `os`, `sys`, `re`, `math`...).

Example:

```py
import sys, re
```

The [`dir()`](#dir) function can list all functions and methods from the argument module (as long as teh module has been imported).

## Modules

*Below is a list of selected modules with short reminders of important functions from them.*


### `sys.exit()`
        To make the program terminate; requires import of sys module.


### `random.choice()`
        Requires import of random module; accepts lists as arguments and chooses an item in the list at random.


### `random.shuffle()`
        Requires import of random module; accepts lists as arguments and rearranges the items in the list at random.


copy.copy()
copy.deepcopy()
        Require import of copy module; the copy() and deepcopy() functions can be used to make a duplicate copy of a mutable value like a list or dictionary, not just a copy of a 
        reference. Normally Python treats mutable and immutable values stored in variables differently: if the value is immutable and more than one variable refer to it, reassigning 
        one of those variables will not affect the others (the original value is not changed, but a new value, with different id is created, and the variable now *refers* to that); 
        if the value is mutable, changing it will modify it in *place*, affecting all the variables that refer to it. If we don't want to change the original mutable value, copy() 
        and deepcopy() can be used to create a copy of it, so that now there are two values with different ids. The deepcopy() function is the same as copy(), but is needed in case 
        of lists that contain other lists (if those inner lists are to be copied).

### `argparse` module

Check the [guide to `argparse`](#parsing-arguments-in-python---the-argparse-module).

#### `argparse.ArgumentParser()`

#### `.add_argument()` method

#### `.parse_args()` method

### `re` module

#### `re.search()`

Scans through a string looking for the first location where the regular expression pattern produces a match, and returns `True` or `False` depending on wether the string matches the regex. Useful as condition in `if`, `for` and `while` statements.

Syntax: `re.search(regex, string)`

#### `re.findall()`

Extracts the string(s) matching the regex as a list object.

Syntax: `re.findall(regex, string)`

#### `re.fullmatch()`

The same as `re.search()` but returns `True` if the whole string matches.

#### `re.split`

Splits the string by the occurrences of the regex and returns a list (only if capturing parentheses are used in the pattern).

Syntax: `re.split(regex, string)`

#### `.group` method

Extracts from a match object the group corresponding to the argument number.

Example:

```py
>>> import re
>>> str = 'Bulbasaur, Squirtle, Charmander'
>>> re.search('(\w+)(, )', str)
<re.Match object; span=(0, 11), match='Bulbasaur, '>
>>> match_lst = re.search('(\w+)(, )', str)
>>> match_lst.group(0)
'Bulbasaur'
```

### `os` module

#### `os.system()`

Allows to execute system commands. If the called command generates any output, it will be sent to stdout stream. [`subprocess.call()`](#subprocesscall) does the same thing.

```py
>>> import os
>>> cmd = 'date'
>>> returned_value = os.system(cmd)
Sat 20 May 12:34:29 CEST 2023
>>> print(returned_value)
0
```

In the example above, output from the used command is automatically printed to stdout. The variable to which the function call's output was assigned only contains the exit status.

#### `os.listdir()`

Returns a list of the files and directories in the directory at the path used as argument.

#### `os.path.isdir()`

Returns `True` if the item at the argument's path is a directory, `False` if it isn't.

#### `os.getcwd()`

Returns the path to current working directory.

### `subprocess` module

#### `subprocess.call()`

Calls a shell command. If the called command generates any output, it will be sent to stdout stream. Identical to [`os.system()`](#ossystem).

```py
>>> import subprocess
>>> cmd = 'date'
>>> returned_value = subprocess.call(cmd)
Sat 20 May 12:34:29 CEST 2023
>>> print(returned_value)
0
```

#### `subprocess.check_output()`

Calls a shell function but it allows to catch the function's output instead of the exit status.

```py
>>> import subprocess
>>> cmd = 'date'
>>> returned_value = subprocess.check_output(cmd)
>>> print(returned_value)
b'Sat 20 May 12:41:45 CEST 2023\n'
```

### `sys` module

## Methods

Methods are like functions, but called on a specific value. They are called by appending a period, the method's keyword and the parentheses (with the argument, if needed) to the name of the variable. Ex.: `My_List.index(item1)`.

The [`dir()`](#dir) function can list all the methods available for the argument value.

### Methods for strings (string library)

> Note: all string methods returns new values. They do not change the original string.

<!-- check what to remove in this list -->
<!-- sed this file to put backticks around all functions/methods and to add (#){2,} before them in their description (especially string methods section). -->

#### `capitalize()`

Converts the first character to upper case

#### `casefold()`

Converts string into lower case

#### `center()`

Returns a centered string

#### count()
Returns the number of times a specified value occurs in a string

#### encode()
Returns an encoded version of the string

#### endswith()
Returns true if the string ends with the specified value

#### expandtabs()
Sets the tab size of the string

#### find()
Searches the string for a specified value and returns the position of where it was found

#### format()
Formats specified values in a string

#### format_map()
Formats specified values in a string

#### index()
Searches the string for a specified value and returns the position of where it was found

#### isalnum()
Returns True if all characters in the string are alphanumeric

#### isalpha()
Returns True if all characters in the string are in the alphabet

#### isascii()
Returns True if all characters in the string are ascii characters

#### isdecimal()
Returns True if all characters in the string are decimals

#### isdigit()
Returns True if all characters in the string are digits

#### isidentifier()
Returns True if the string is an identifier

#### islower()
Returns True if all characters in the string are lower case

#### isnumeric()
Returns True if all characters in the string are numeric

#### isprintable()
Returns True if all characters in the string are printable

#### isspace()
Returns True if all characters in the string are whitespaces

#### istitle()
Returns True if the string follows the rules of a title

#### isupper()
Returns True if all characters in the string are upper case

#### join()
Converts the elements of an iterable into a string

#### ljust()
Returns a left justified version of the string

#### lower()
Converts a string into lower case

#### lstrip()
Returns a left trim version of the string

#### maketrans()
Returns a translation table to be used in translations

#### partition()
Returns a tuple where the string is parted into three parts

#### replace()
Returns a string where a specified value is replaced with a specified value

#### rfind()
Searches the string for a specified value and returns the last position of where it was found

#### rindex()
Searches the string for a specified value and returns the last position of where it was found

#### rjust()
Returns a right justified version of the string

#### rpartition()
Returns a tuple where the string is parted into three parts

#### rsplit()
Splits the string at the specified separator, and returns a list

#### rstrip()
Returns a right trim version of the string

#### `split()`

Splits the string at the specified separator, and returns a list (works similarly to Perl's own `split()`).

Example:

```py
fhandle = open('Python_notes.md')

count = dict()
for line in fhandle:
    words = line.split()
    for i in words:
        count[i] = counts.get(i, 0) + 1
```

#### splitlines()
Splits the string at line breaks and returns a list

#### startswith()
Returns true if the string starts with the specified value

#### strip()
Returns a trimmed version of the string

#### swapcase()
Swaps cases, lower case becomes upper case and vice versa

#### title()
Converts the first character of each word to upper case

#### translate()
Returns a translated string

#### upper()
Converts a string into upper case

#### zfill()
Fills the string with a specified number of 0 values at the beginning





### Methods for list values
<!-- check if these are correct -->
*NOTE: the following methods modify the list in place and their return value is `None`, thus you can't write code by setting variables with the return value of one of these methods.*

index()
    returns the index value of the value used as argument, if that value exists in the list on which the `index()` method is used. 
    If the value passed as argument isn’t in the list, Python produces a `ValueError` error.

    ex.: >>> starter = ["bulbasaur", "charmander", "squirtle"]
         >>> starter.index("bulbasaur")
         0


append()
    adds a new value at the end of a list.

    ex.: >>> starter = ["bulbasaur", "charmander", "squirtle"]
         >>> starter.append("pikachu")
         >>> starter
         ["bulbasaur", "charmander", "squirtle", "pikachu"]


insert()
    adds a new value at any index position in a list. The other values following the index in which the new item is to be inserted are budged over, not overwritten. It requires 2
    arguments: first the index value at which to insert the new value and then the new value. The 2 arguments are separated by a comma and a space.

    ex.: >>> starter = ["bulbasaur", "charmander", "squirtle"]
         >>> starter.insert(1, "pikachu")
         >>> starter
         ["bulbasaur", "pikachu", "charmander", "squirtle"]


remove()
    removes a value from a list. Attempting to delete a value that does not exist in the list will raise a `ValueError`. If the value appears multiple times in the list, only the
    first instance of the value will be removed. It does the same thing as the `del` statement, but `del` is good when you know the index, while `remove()` is useful if you know
    the value.

    ex.: >>> starter =  ["bulbasaur", "pikachu", "charmander", "squirtle"]
         >>> starter.remove("pikachu")
         >>> starter
          ["bulbasaur", "charmander", "squirtle"]


sort()
    it sorts a list containing number values in increasing order, or a list containing string values in ASCII order (it's alphabetical but uppercase letters come first and lowercase
    letters are sorted after "Z"). `sort()` has 2 optional keyword arguments. If a reverse order is needed, `sort()` has the optional `reverse=` argument that can be passed the
    Boolean value `True` to sort items in reverse order (`list.sort(reverse=True)`). In a list containing strings, if you don't want lowercase letters to be listed after uppecase
    letters, `sort()`can be passed the `key=` argument followed by `str.lower` (`list.sort(key=str.lower)`), which will tell the method to treat all letters as if they were lowercase
    (this doesn't actually change the values in the list). `sort()` does not work on lists that contain both number and string values, since Python can't compare those values.

    ex.: >>> starter =  ["bulbasaur", "pikachu", "charmander", "squirtle"]  |   >>> numbers = [1, 5.89, -8, 3, 42]  |   >>> letters = ["A", "B", "C", "a", "b", "c"]
         >>> starter.sort()                                                 |   >>> numbers.sort()                  |   >>> letters.sort()
         >>> starter                                                        |   >>> numbers                         |   >>> letters
         ['bulbasaur', 'charmander', 'pikachu', 'squirtle']                 |   [-8, 1, 3, 5.89, 42]                |   ['A', 'B', 'C', 'a', 'b', 'c']
         >>> starter.sort(reverse=True)                                     |   >>> numbers.sort(reverse=True)      |
         >>> starter                                                        |   >>> numbers                         |
         ['squirtle', 'pikachu', 'charmander', 'bulbasaur']                 |   [42, 5.89, 3, 1, -8]                |
         --------------------------------------------------------------------------------------------------------------------------------------------
         >>> mixcase = ["Seto Kaiba", "hat", "Broly", "Thea", "spoon", "lowercase", "Linux", "tea", "Articuno", "axe", "rocket-launcher", "Han Solo"]
         >>> mixcase.sort(key=str.lower)
         >>> mixcase
         ['Articuno', 'axe', 'Broly', 'Han Solo', 'hat', 'Linux', 'lowercase', 'rocket-launcher', 'Seto Kaiba', 'spoon', 'tea', 'Thea']


reverse()
    it reverses the order of the items in a list.

    ex.: >>> starter =  ["bulbasaur", "pikachu", "charmander", "squirtle"]
         >>> starter.reverse()
         >>> starter
         ['squirtle', 'charmander', 'pikachu', 'bulbasaur']

remove()
pop()
count()

### Methods for dictionaries

#### `.get()`

Checks if something is in a dictionary and returns a default value if it isn't. Can be called on a dictionary with 1 or 2 parameters: `my_dictionary.get(key, defualt_value)`.

The parameters are:
- the key name (mandatory) - the keyname of the item you want to return the value from;
- default value (optional) - a value to return if the specified key does not exist. If none is provided, the default value is `None`.

Usage:

```py
x = dictionary.get(i, 0)
```

Specifically, what `.get()` does is execute the following 4 lines of if-else statement on the dictionary, without raising an error (check the [dictionary section](#dictionaries)):

```py
if i not in dictionary:
    x = dictionary[i]
else:
    x = 0
```

Example:

```py
bases = ['A', 'C', 'G', 'T']

count = dict()

for i in bases:
    count[i] = count.get(i, 0) + 1
print(count)
```

#### `.keys()`

Returns the keys of a dictionary.

#### `.values()`

Returns the values of a dictionary.

#### `.items()`

Returns a list of key-value pairs of a dictionary in tuples form.

The `.items()` method allows to iterate `for` loop using **2 iteration variables**, which advance together:

```py
dct = {'Lou' : 1 , 'Frankie' : 24 , 'Bonnie' : 12}
for k,v in dct.items():
    print(k,v)
# OR
for (k,v) in dct.items():
    print(k, v)
```

> The 2 syntaxes in the example work because (k,v) is a [**tuplet**](#tuples). 

Using the same tuplet syntax is also possible to return a list of tuples from a dictionary:

```py
tups = dct.items()
```

## Parsing arguments in Python - the `argparse` module

Arguments can be managed in 3 main ways in Python:
- For short, simple scripts, [`sys.argv()`](#getting-arguments-from-command-line) is ideal;
- For more complicated code are available the modules:
    * `optparse` (older, simpler, deprecated because lacked some features);
    * `argparse` (current standard, shipped starting with Python3, more complicated and powerful).

In order to use `argparse`, there are 4 main steps to follow:

1. Import `argparse`;
2. Create an argument parser by instantiating `ArgumentParser`;
3. Add arguments and options to the parser using the `.add_argument()` method from the `argparse` module;
4. Call `.parse_args()` on the parser to get the `Namespace` of arguments.

A `Namespace` it's a class for `argparse` to create (and return) an object with attributes.

Example:

```py
# 1. Import argparse
import argparse
from pathlib import Path

# 2. Create an argument parser by instantiating ArgumentParser
parser = argparse.ArgumentParser()

# 3. Add arguments and options to the parser using the `.add_argument()` method
#    from the `argparse` module
parser.add_argument("path")

# 4. Call `.parse_args()` on the parser
args = parser.parse_args()
```



# returns output as byte string
returned_output = subprocess.check_output(cmd)

# using decode() function to convert byte string to string
print('Current date is:', returned_output.decode("utf-8"))

It will produce output like the following

Current date is: Thu Oct  5 16:31:41 IST 2017









https://stackoverflow.com/questions/20802056/python-regular-expression-1