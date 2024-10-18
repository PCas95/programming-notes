# Notes on PERL scripting

*For more info on PERL and its functions, there's full documentation at* <https://perldoc.perl.org/>.

## Executing Perl scripts

As with any programming language, Perl scripts can be executed by typing the `perl` command followed by the name (or path, if not in working directory) of the Perl script. This executes perl (the program) and uses it to read the set of instructions contained in the Perl (the language) script.

Adding the *shebang* line (**interpreter directive**) at the top of the code will mark the file as one that can be read with the program located in `/usr/bin/perl`. The interpreter directive for Perl scripts is `#!/usr/bin/perl`.

To make the script executable by simply typing its name from anywhere, 3 conditions are to be met:

1) the script must contain the `#!/usr/bin/perl` interpreter directive as the first line of the file;

2) the script must be executable (if it isn't, it has to be changed with `chmod`);

3) the path to the script has to be included in the `$PATH`.
To add a path in the `$PATH` system variable:

            export PATH=$PATH:path-to-directory

*NOTE: It may be a good idea to create an already executable template "PERL_script_file", containing just the interpreter directive. This file would be copied every time a new script is written: since copying a file also copies file permissions, doing this would save the time used to write the interpreter directive and to make it executable.*

## Main facts

`;`: every line of code in Perl ends with a semicolon ";" (with [a few exceptions](#conditional-statements)).

`$`: the dollar sign is used to set [variables](#variables). It stands for "scalar" since Perl's variables are also called "scalar variables", due to the fact that they can be changed (like in almost any other scripting language). In Perl you can either set a variable and assign a value to it with `=` in a single line (like in Python), or you can set an empty variable by simply typing its name preceded by `$` ("unassigned variables") and assign a value to it later on, on a different line. Naming variables works the same way as in Python.

`=`: the variable assignment operator.

`@`: like for variables, the "at" symbol is used to set [arrays](#arrays).

`%`: the percentage symbol is used to set Perl's third data type: [hashes](#hashes).

`\`: backslashes are escape characters, used, as in other programming languages, to escape the meaning of special characters such as `$`, quotes, or other `\`.

`()`: parentheses are used to contain a function's arguments (like in Python), but they are not a mandatory formatting in Perl. They are also used to contain [lists](#lists), [array elements](#arrays), to set precedence in [calculations](#mathematical-operators) and in [`if` statements](#the-if-statement).

`''`: quotes are used for string values. Strings inside single quotes are printed as they are, so variables are not expanded and special characters lose their special status.

`""`: strings inside double quotes undergo *variable interpolation*, which means that all the variables inside are expanded to their respective values (this includes escape characters).

`$_`: unless stated otherwise, loops will assign the iteration item of each cycle to the pre-set variable `$_` (see more in [loop section](#the-foreach-loop)).

**Pragmas**
Pragmas are rules that can be applied to the script. They are valid from the moment they are called (so they are often written at the very beginning of the script). Pragmas are activated by using the `use` keyword and can be turned off by using the `no` keyword (`no warnings;`, `no strict;`, for example).

`use warnings`: this line of code enables Perl to check if the code is written in an undesirable way; if it is, Perl will print warning messages. Scripts should always contain the `use warnings;` line of code ("*pragma*"), and good code should not report any warning message.

[`use strict`: check out this pragma in the "variables" section](#variables).

**spacing**: most of the times Perl treats different spacings the same way, thus adding spaces to the code to align it can be used to make the code appear clearer.

**evaluating truth**: like in Python, a 0 value or an empty string evaluate to False, while all other values are logically True. This also applies to values that are stored in variables, and can be used in `if` statements as a condition equivalent to `if (True)`, or to check code (to see if there are empty variables after importing the variable contents from a file).

`@ARGV`: [`@ARGV` is a special pre-defined array](#the-argv-array) and it's how Perl is able to get arguments from the command line.

`<>`: Perl's file operator; it's used to [read lines of text from files](#the-file-operator). It reads the file line by line and keeps track of the number of lines as they go by.

`<STDIN>`: Perl's standard input operator; it's usage and functions are identical to Python's `input()` function: it allows to [receive standard input](#reading-from-standard-input) from the user (the script will wait until the user types in some text).

`$!`: another of Perl's special variables, it contains pieces of information about the error, in the case a function or action had an exit status of False. More on the `$!` variable [here](#checking-inputoutput-files).

`` ` ``: [Perl's backtick operator](#interaction-with-other-programs). Whatever is written in between backticks will be executed in the UNIX shell.

## Variables

A scalar variable is both created and assigned a value by using the assignment operator (`=`). However, in Perl variables are created whenever are needed: this means that it's sufficient to mention them at any point of the script and, if the variable doesn't already exist, it's "automagically" created. Thanks to that, a variable can be simply stated to create it without assigning a value to it (which is not ideal because deliberately unassigned variables are often undesirable), or created when stated in a function that generates a value to assign to it.

### The `my` keyword

Another way to create a variable is by using the `my` keyword:

    my $shorts;

or

    my $shorts = 'eat';

In the first case, `my` creates an *undefined variable*, which means that the variable is empty and so the assignment will happen later on; in the latter example, on the other hand, the variable is assigned a value right away with `=`. A variable that is assigned a value is therefore called a *defined variable*. We can check whether a variable is defined using the `defined()` function (see [Scalar variable functions](#scalar-variable-functions)).

**Variable scope**
Variables created with `my` are called *lexical variables* and will exist from the moment they are created to the closing curly brace (the end of the block of code). Note that a file or a program (even the Perl script itself) are considered blocks. So, just as in Python, each variable has a scope depending on where it was created: if it was created inside a statement, it will exist until the end of that block of code ("local scope"), while if it was created outside any block of code it has a "global scope", meaning that it exists for the entire program. Note that variables of different scopes can have the same name.

`use strict`: this pragma prevents variables from being created automagically and forces to create lexical variables beforehand with the `my` keyword.

### Symbol tables or packages

Scalar variables, as well as arrays, hashes and subroutines, are stored in the program's symbol table, also called a *package*. The name of the program's package is "*main*" and the program can interact with other packages too. The complete name of a variable is the variable's name, preceded by the package name and 2 colons: `$main::shorts` for the `$shorts` variable in the program's symbol table and `$Toolbox::x` for the variable `$x` belonging to the "Toolbox" package. Variables in the symbol table of the program are global variables, while local variables (and lexical variables in general) are not stored in the symbol table.

## Mathematical operators

`+`: addition;
`-`: subtraction;
`*`: multiplication;
`/`: division;
`%`: integer divide remainder (or modulo);
`**`: power of.

**operator precedence:** bear in mind that, just like in mathematical expressions, Perl uses precendence rules in maths: in general, multiplication takes precedence on everything else and to give precedence to a specific operation, parentheses are used.

### Mathematical shortcuts

Just like Python's augmented operators, Perl also has shortcuts to perform mathematics.

In general, following the maths operator with an assignment operator (`=`) will give a shortcut to perform the calculation between a variable and the number following the operator.

`+=`: `$n += 3` equals `$n = $n + 3`;
`-=`: `$n -= 3` equals `$n = $n - 3`;
`*=`: `$n *= 3` equals `$n = $n * 3`;
`/=`: `$n /= 3` equals `$n = $n / 3`;
...

Another kind of shortcut allows to increase or decrease by 1 the number stored in a variable:

`$n++`: equals `$n = $n + 1`;
`$n--`: equals `$n = $n - 1`.

## Operators for strings

`.`: the string concatenation operator is represented in Perl by a single dot, and it works the same way as Python's `+` concatenation operator. Since a dot is also used to access an object's "methods" (like in Python), it is important to never forget using spaces on either side of the string concatenation operator.

`x`: repetition operator. It repeats the preceding string a number of times equal to the following number. Variables containing strings and numbers respectively can also be used.

**String comparison operators**
The 3 string comparison operators compare strings based on their ASCII values, not based on their length.

`eq`: the "equal to" operator tests whether 2 strings are identical.

`gt`: the "greater than" operator tests if the first string has a greater ASCII value than the second.

`lt`: the "lower than" operator tests if the first string has a lower ASCII value than the second.

### String operator shortcuts

Just like for mathematical operators, string operators can have shortcuts too.

`.=`: concatenates on end of the string stored in the variable. Usage: `$some_string .= 'more text'`.

## Comparison operators

`==`: equal to;
`!=`: not equal to;
`>`: greater than;
`<`: less than;
`>=`: greater than or equal to;
`<=`: less than or equal to.

### The comparison operators `<=>` and `cmp`

In addition to standard comparison operators, Perl also has the 2 operators `<=>` and `cmp`, which are used to compare numbers and strings, respectively and are also used to compare the 2 special variables `$a` and `$b` (see [`$a` and `$b` in the `sort()` function](#the-sort-function)). Differently than standard comparison operators, `<=>` and `cmp` are evaluated to 0, -1 or +1, depending on `$a` being equal to, less than or greater than `$b`, respectively.
Examples:

    "c" cmp "d";   # -1 (c is less than d alphabetically)
    "d" cmp "c";   # +1
    "c" cmp "c";   #  0
    1 <=> 2;       # -1 (1 is less than 2 numerically)
    2 <=> 1;       # +1
    1 <=> 1;       #  0
    "c" <=> "d";   #  0 (strings are numerically 0)
    100 cmp 2;     # -1 (1 and 0 are less than 2 alphabetically)

## Boolean operators

Perl provides the 3 Boolean operators `and`, `or` and `not`, which are used to link together conditions in conditional statements. Perl can also adopt the following equivalent shortcuts:

`&&`: `and`
`||`: `or`
`!`: `not`

## Matching operators

Perl provides a very useful and simple way to match a string in a text, allowing for exact *matching*, *fuzzy search* (a search pattern that allows one or more possibilities), *substitution* and *transliteration*. To perform all these actions, Perl uses the **binding operator**, which behaves in different ways depending on the code that follows it.

`=~`: the binding operator, used for string manipulation.

> **NOTE: Alternative syntaxes for matching operators**
Just as Bash's `tr` and `sed` commands, all 3 of Perl's matching operators allow many different characters to be used as separators in place of forward slashes (`/`). Usually a good choice is the "at" symbol `@`, since it's usually not expanded to anything else. However, do note that if such a thing is true for Bash, it may not be the same case in Perl, which uses `@` to define arrays.

### Matching strings

**Matching operator:** `=~ m//`
The `m//` pattern turns the binding operator into a matching operator (`=~ m//`. The pattern or string to match is written in between the two forward slashes and it's searched for anywhere in the string in an exact and case-sensitive way. Be aware that Perl allows to omit the "m" of the pattern match.

**The "not match" operator:** `!~ m//`
The pattern match can also be used to ask to *not match* a string or pattern. In that case, `m//` is preceded by `!~`: `!~ m//`.

> Note that if we try to match without a string or pattern (just `m//`), anything in the variable will match, even if the variable is empty.

#### Location of the match

> If a match would occur multiple times in a text, Perl will try to match at the earliest position possible.

This behaviour is fine when we only care if a match does occur, but Perl also allows to loop through all  matches and to identify the parts of the parsed string that match (or don't match) through Regular Expressions. It also allows to change the default match to just the first occurrence to a global match and the case-sensitive behaviour to case-insensitive through the usage of options.

### Substitution

**Substitution operator**: `=~ s///`
The substitution operator works just like Bash's `sed` command: the pattern or string to match is written in between the first and second forward slashes, while the text it will be replaced with is written between the second and third forward slashes. Just like `sed` and in accordance to Perl's matching behaviour, only the first match per string is substituted, but this approach can be changed using options.

### Transliteration

**Transliteration operator**: `=~ tr///`
Just like Bash's `tr` command, the transliteration operator will allow to transform all instances of each item in the list of characters between the first two forward slashes to a character in the corresponding position in a second list, written between the second and third separator.
Example:
`$text =~ tr/ab/xy/;`: all occurrences of "a" are transformed into "x" and all those of "b" into "y".

### Options

The behaviour of Perl's matching operators can be changed by using options that work just the same way as Bash `sed` command's options:

`g`: *global* option. The match or substitution now applies to all matches in the text, not just the first occurrence.

`i`: *ignore case* option. It makes the match case-insensitive.

`m`: *multiple line* option. Usually Perl assumes that the variable contains a single line of text, but if we know the text contains multiple newline characters, using the `m` option Perl will turn off that optimization and consider the text as having multiple lines.

`s`: *single line* option. Tells Perl to treat the string as a single line.

`x`: *extended regular expressions* option. Enables usage of extended regular expressions.

Options are written right after the last forward slash (just like in `sed`) and multiple options can be used at once (ex.: `$nucl_acid =~ s/U/T/gi`).

### Matching operators, Regular Expressions and Loops

When using regular expressions  in Perl, the matching operator can be used to allow to loop through all the matches for a certain regex; to do that, it is important to use the `g` option in the match operator syntax, so that it's actually possible to go through all the matching strings in a string/file/variable.

Example:

    while ($text =~ m/(regex_here)/g) {
        ...
    }

In the example above we used parentheses to remind that to extract the match of a regex we use the special variables `$1`, `$2`, `$3` etc, so parentheses are needed to create one ore more groups in the RE, so that the matches are actually retrievable.

## Functions

`print()`: the print function works largely the same way as Python's. Strings are written between quotes while numbers are not, and it supports printing a list of items (separated by commas, in Perl's case). Bear in mind that *variable interpolation* only takes place if double quotes are used. In Perl, since not all formatting is mandatory, parentheses can be omitted and the string to print may just be separated from the `print` function by a space.

`exit()`: it stops the program. It works just like Python's `exit()`, so it doesn't print anything (if I want to leave a message, I'll have to call `print()` before the `exit()` call).

`die()`: stops the program and leaves a message (same syntax of the `print()` function). Should be used when the script has to be stopped because of a problem.

`reverse()`: reverses the order of elements in an array or reverses a string.

`system()`: launches the command used as argument in the UNIX shell. Requires either quote delimiters or the command's argument, options and flags to be listed separately, as a list separated by commas.
Examples:

    system(command, argument_1, argument_2);
    system("command argument_1 argument_2");

More in [the `system()` function section](#the-system-function).

### Numeric functions

`abs()`: identical to Python's function of the same name, it returns the absolute (non-negative) value of a number.

`int()`: similarly to Python's function of the same name, it returns the integer value of the argument. Just like Python's `int()` function, it also converts strings to number values and it will always round down.

`log()`: natural log of the argument.

`rand()`: generates a random number in the range from 0 to the argument.

`sin()`: sin of argument.

`sqrt()`: square root of argument.

> *NOTE: regarding numeric precision, Perl goes through the same overflow and underflow errors as a calculator, thus it's not wise to compare if 2 floating point values are equal. To avoid problems is always best to use the* `abs()` *function to compare absolute values or to get the absolute value of the result of calculations on floats.*

### String functions

#### `length()`

Returns the length of the argument string (it counts all characters, even those that are not printed).

#### `substr()`

The *substring* function extracts text from a string. It accepts up to 3 arguments, separated by a comma and a space: the first one is the string argument (mandatory), the second is the offset or starting position (mandatory) and the third is the length (optional). So `substr()` extracts text from the first argument, starting at the position provided by the second argument, of the length of characters provided by the third argument or to the end of the string argument (if no length argument was provided). Just like in Python's lists, positions are counted starting from 0, not 1, and negative numbers can be used as second argument to access the string characters from the right (the end); so -1 would be the last character of the string and -2 the second to last. `substr()` does not change the original string, but it can be used to do so by combining it with an assignment operator:

```pl
substr($some_string, 19, 7) = "something else";
```

In the example above, the text starting at position 19 and ending at position 26 (length 7) is substituted with the string at the right of the assignment operator. The same syntax can be used to delete parts of a string (using an empty string value) or to insert a string at a certain position.

#### `qq{}`

Encloses the argument string in double quotes, allowing interpolation of variables. The functions [`qw`](#), `qq` and [`qx`](#qx) normally use curly braces as delimiters for their arguments, but they can use any delimiter (`()`, `//`...).

```pl
my $cmd = qq{grep -i '$regExpr' $f};
```

The example above also shows that `qq` allows interpolation of `$regExpr` even if that variable is also between single quotes. 

#### `qx{}`

Encloses the argument string in backticks, used to directly run a system command.

```py
my $ris = qx{$cmd};
```

In the example above, `qx` encloses the command stored in the `$cmd` variable in backticks: the system command is executed and its results captured in the `$ris` variable.

#### Case-conversion functions

`uc()`: converts an entire string to upper-case characters.

`lc()`: converts an entire string to lower-case characters.

`ucfirst()`: converts the first character of a string to upper-case.

`lcfirst()`: converts the first character of a string to lower-case.

### Scalar variable functions

`defined()`: returns True if the argument variable is defined and False if it's undefined (see [defined and undefined variables](#the-my-keyword)).

`undef()`: makes a variable undefined. It can be used both as a function or in an assignment context. I can also be used with arrays: doing so destroys all the elements of the array (it becomes an empty array, but it cannot be made undefined, since only scalar variables can be undefined).
Example:

    undef($var_1);   # as a function
    $var_1 = undef;  # as an assignment

### Array functions

`qw()`: automatically adds single quotes to a list of elements while creating an array (see [Array's quote word function](#arrays-quote-word-function-qw)).

`scalar()`: forces the evaluation of the array argument in scalar context (see [Scalar context vs List context](#scalar-context-vs-list-context) in the Array section).

`push()`: adds an element to the end (right side) of the list. It accepts two arguments: first the array and then the value to be added to the array.
Examples:

    push(@starters, 'Pikachu');
    push(@values, 42);
    my $character_4 = 'Mr. Popo';
    push(@characters, $character_4);

`pop()`: it's the reverse function of push: it removes the last element (on the right side) from the argument array (`pop(@starters)`).

`shift()`: it removes a value from the front (left side) of the list. Can be used together with an assignment operator to assign the shifted value to a scalar variable, rather than simply discard the item.
Examples:

    shift(@starters);
    $shifted_Bulba = shift(@starters);

`unshift()`: as the reverse function of `shift()`, it adds a value to the front (left side) of an array. Like `push()`, it also requires two arguments (first the value to add and then the argument array).

`join()`: joins together all the elements of the argument array(s) in a single string, in which the elements can be separated by a specified separator. Requires 2 arguments: first the separator, which can be any character, string, special character or value contained in a variable (variables and special characters, space characters included, require double quotes for interpolation); then one or more arrays (more than one array can be used as argument, as long as they are separated by a comma and a space) or even a list of items. `join()` doesn't affect the original arrays, lists or items that it's joining.
Example:

    my $csv_file = join(',', @characters, 'C-17', 'C-18');

> The `join()` function is of great importance in Bioinformatics, because it is an incredibly efficient tool to create CSV or TSV files.

> *NOTE:* `join()`*'s outptut is a string, so it has to be assigned to a scalar variable, not another array.*

`split()`: it's the opposite of the `join()` function: it chops a string into elements of an array. It needs two arguments: first the character or pattern to use to split (i.e. to use as separator), then the argument string (it can also be a scalar variable containing a string).
The character specified as separator is not kept; furthermore, we can use an empty string (`''`) if we want to split a string at every possible position.
Example:

    my @fields = split(',', $line);

The `split()` function can also be called without arguments (`split()` or `split`): in this case, one blank space (' ') is used as the default separator and `$_` as the argument string to split.

`splice()`: adds or remove elements at any position in the array. Has a number of optional arguments that change how it functions, adding levels of complexity.

- Using only the argument array, `splice()` will erase everything from the array.
- A second argument (a number or number-containing variable) will act as starting point for the function, thus erasing everything from that position onwards. *Remember that array indexes start from 0.*
- A third argument (another number) will provide the number of elements that will be removed starting from the position specified by the second argument.
- More values (or scalars or arrays) used as arguments specify items that will replace the elements in the specified positions.

Examples:

    splice(@array);
    splice(@array, 3);
    splice(@array, 3, 2);
    splice(@array, 3, 2, 'Leonardo', 'Raffaello');

### Hashes functions

`exists()`: checks whether a specified [hash key](#hashes) exists.
Example:

    if (exists($hash{$key})) {
        print("the hash key $key exixts\n");
    }

`keys()`: extracts all the keys from a hash:

    my @list_o_keys = keys(%hash);   # evaluated in list context: @list_o_keys stores a list of all the values in %hash
    my $num_o_keys = keys(%hash);    # evaluated in scalar: $num_o_keys stores the number of keys in %hash

`values()`: extracts all the values from a hash:

    my @list_o_values = values(%hash);
    my $num_o_values = values(%hash);

`each()`: returns the key-value pairs of the argument hash for each time it is called on it, thus is a fundamental element to use in conjunction with `while` loops to loop through a hash using `while` insted of `foreach`:

    while ( my ($key, $value) = each(%hash) ) {
        ...
    }

#### The `sort()` function

`sort()`: both arrays and lists can be sorted  using the `sort()` function. This function compares each item by a rule or set of rules defined in between curly braces. Perl reserves 2 special variables to comparisons: `$a` and `$b`, used in that order to identify the first and second element of a comparison; Perl compares them by using either one of two comparison operators: `<=>` (which compares numbers) and `cmp` (which compares strings). Details on `<=>` and `cmp` can be found in the [comparison operators section](#the-comparison-operators--and-cmp).
By default (not using the rules between braces), `sort()` orders the elements of an array or the items in a list by their ASCII value (`sort(@array)` is thus equivalent to `sort({$a cmp $b} @array)`).
Examples:

    @sorted_data = sort(@my_data);
    @sorted_data = sort({$a cmp $b} @my_data);
    @sorted_data = sort({$a <=> $b} @my_data);

The comparison rules defined between curly braces can also be more imaginative, since usage of functions, Boolean operators, etc. is accepted:

    @sorted_data = sort({$a <=> $b or $a cmp $b} @my_data);
    @sorted_data = sort({length($a) <=> length($b)} @my_data); 

Above, the first example uses the `or` operator (also `||`) to sort the items numerically, but if they are strings they'll be sorted alphabetically. This works because when strings are compared numerically with `<=>` they give output 0 (see more on `<=>` and `cmp` output [here](#the-comparison-operators--and-cmp)).
The second example uses `<=>` to sort numerically based on the length (in number of characters) of the items.

Reverse-sorting can be performed by switching places of `$a` and `$b`:

    @sorted_data = sort({$b <=> $a} @my_data);

[Perl also has a `reverse()` function](#functions) that can accomplish that, but is not equivalent.

## Conditional statements

### The `if` statement

Basic structure of the `if` statement:

    if (condition) {
        code to be executed if the condition evaluates to True;
    }

The basic code consists of:

- the `if` keyword, followed by the condition in parentheses and an opening curly brace to mark the beginning of a block of code;

- an indented block of code to be executed if the condition evaluates to True;

- a closing curly brace on its own line to demark the end of the indented block of code.

> Note that lines that end with a curly brace do not get a semicolon (only the indented block of code does in this case).

#### `else` and `elsif` statements

`else` and `elsif` statements behave just like the corresponding statements in Python (so `elsif` requires a condition too, `else` must always be the last of the conditional statements, even if it's not always necessary to end with an `else` statement).

Adding the `elsif` and `else` statements to `if`'s basic syntax:

    if (condition_1) {
        code to be executed if condition_1 evaluates to True;
    } elsif (condition_2) {
        code to be executed if condition_2 evaluates to True;
    } else {
        code to be executed if other conditions all evaluate to False;
    }

> *NOTE: the* `if` *and* `elsif` *statements can be formatted in a different way than the standard block structure, if that makes them more readable. Below is an example of such alternate formatting, used to simplify the code in case of statements with very short conditions and block of code. Note that, since each line ends with the curly braces, none of them has semicolons in this syntax:*

    if ($x = 1) {print("Eat my shorts")}
    elsif ($x = 2) {print("Get bent")}
    else ($x = 3) {print("Don't have a cow")}

### The `unless` statement

The `unless` statement is a way to test if something does not evaluate to True, thus is equivalent to `if (not something)` or `if (!something)`.

Basic structure of the `unless` statement:

    unless (condition) {
        code to be executed if the condition evaluates to False;
    }

`elsif` statements **can't be used** with `unless`, but `else` statements can:

    unless (condition) {
        code to be executed if the condition evaluates to False;
    } else {
        code to be executed if the unless conditon is True;
    }

> *NOTE: conditional statements in PERL can also use different syntaxes called* "postfix notation" *and* "trinary operator", *however using such notations rarely makes the code clearer or more readable; some may find them useful under certain circumstances, but it's not a good idea to stray from the standard syntax, since that would make the code less consistent.*

## Lists

Differently from Python, in Perl Lists are a common way to assign a set of values to a set of variables (another powerful way to use them is when working with *arrays*).
A *list context* is when you have multiple scalar variables separated by commas between parentheses:

    my ($a, $b, $c) = ('sword', 'shield', 'wand');

> A list can also be made up of just 1 or even 0 scalar variables.

Using a *list context* saves a few lines of code when dealing with multiple variable assignments and also provides an alternative syntax to assign the same value to more than one variable:

    my ($x, $y, $z);
    $x=$y=$z=42;

> NOTE: variables (and thus lists) can be assigned not only numbers or strings, but also the output of a function.

### List Balance

If a list isn't balanced (the number of variables and the number of values to assign are not the same), Perl will still work regardless, but that can lead to problems in the script. If the number of values exceeds that of the scalar values, the excess values are not assigned, since the variables are already full. If there are less values than there are variables, the exceeding variables receive no value at all.

### Swapping values with lists

    ($x, $y) = ($y, $x);

In the example above, lists are being used to swap the values stored in two variables in a single line of code. This is possible because assignments in lists occur simultaneusly.

## Arrays

Arrays are just like Python's lists (beware of the fact that Perl's lists *are not* the equivalent of Python's lists: that title goes to Perl's arrays). As such, an array contains a set of items, i.e. scalar variables, which take the name of *elements* of an array. Arrays are indexed with integer numbers starting from 0 (just like Python). Just as Perl's variables are preceded by `$`, arrays are named using `@`. A notable array is the pre-defined [`@ARGV` array](#the-argv-array).

An array can contain many elements, thus having a *start*, an *end* and a certain *length* given by the total number of elements, but an array can also contain a single variable (in this case *start* and *end* of the array coincide), or no elements whatsoever.

The syntax to create an array is the same as when declaring and assigning a variable, only it uses parentheses to contain the comma-separated elements that are being assigned:

    my @characters = ('Goku', 'Vegeta', 'Bulma', 'Gohan');

> When interpolating a whole array (such as in a `print()` function or when putting the array between double quotes), perl will automatically separate the printed elements with spaces.

### Index

Elements are accessed using their index number in square brackets just like in Python. Note the usage of `$` instead of `@` when referring to or extracting a *scalar variable* from the array:

    print("$characters[0]\n");

> NOTE: trying to access an array index that exceeds the number of elements in an array will raise an error.

*We can also use a variable containing a number value as an index.*

> NOTE: Only integer values are to be used, however Perl won't stop us from using floats, but they will always be rounded down, so it's pretty much stupid in *almost* any situation to do such a thing.

### Adding and changing elements in an array

More variables can be added to an array through an assignment statement that uses the array index at which the variable will be assigned:

    my @characters = ('Goku', 'Vegeta', 'Bulma', 'Gohan');

    $characters[4] = 'Mr.Popo';

The same syntax can be used to change an element with another.

### Array's quote word function `qw()`

The quote word function (`qw()`) is used to avoid typing many single quoted elements when creating an array that consists of strings. Using it allows to only type the elements separated by a space (no commas), and `qw()` will automatically add the single quotes:

    my @X_Men = qw(Wolverine Rogue Cyclops Angel Nightcrawler Colossus Shadowcat Storm Prof-X Jubilee Beast);

Which is equivalent to:

    my @X-Men = ('Wolverine', 'Rogue', 'Cyclops', 'Angel', 'Nightcrawler', 'Colossus', 'Shadowcat', 'Storm', 'Prof-X', 'Jubilee', 'Beast');

### Length of a list or array and assigning it to a variable

Unlike Python, the `length()` function *does not* return an array's total number of elements (array's length) if an array is used as its argument. In Perl,  the `length()` function is designed to work with scalars.

*If a list or array is assigned to a scalar variable, the scalar variable becomes the length of the list.*
This is very useful in coding, as it means that in any place where Perl is expecting a numerical value, we can usually specify an array.

    my $array_length = @X-Men;
    print($array_length);

The output of the `print()` function in the example above will be the array's length (so `$array_length` would be assigned a value of 11, since in the examples above we assigned to the `@X-Men` array a total of 11 elements).

#### Scalar context vs List context

Perl can evaluate lines of code in *scalar context* or in *list context*. For example, the line

    my $array_length = @X-Men;

will be evaluated in *scalar context* since we are assigning an array to a *scalar* variable, but if we were to add parentheses to `$array_length` we would be talking about another array with only one element, and thus the line would be evaluated in *list context*. This can be important because some functions and operators in Perl behave differently between *list context* and *scalar context*.

This leads to another way to determine the length of an array: using [the `scalar()` function](#array-functions), which forces evaluation in scalar context.

### Array manipulation

Perl has a number of functions to manipulate arrays.
All the functions to manipulate arrays hereby listed are described in the [Array functions](#array-functions) subsection of the functions section:

- `push()` is used to add an element at the end of an array;

- `pop()` allows to remove an element from the end of an array;

- `shift()` removes a value from the front of the array;

- `unshift()` adds a value to the front of an array;

- `join()` joins the elements of an array in a single string, separated by a specified separator;

- `splice()` adds or removes elements at any position in the array. It behaves differently depending on the optional arguments provided.

In addition, a certain number of array manipulations can be done without the use of functions, just by using assignments:

**Copying an array:**

    my @array_copy = @array;

An array can be copied by simply using another assignment statement to create another array.

**Joining arrays:**

    my @starters_kanto-johto = (@starters_kanto, @starters_johto); 

Arrays can be joined by listing them as items to put inside another array: since evaluation of the right side of the assignment happens first, the 2 arrays are first expanded and then assigned as elements to the new one.

**Emptying an array:**

    my @starters = qw(Bulbasaur Charmander, Squirtle);
    @starters = ();

Assigning nothing to a pre-existing array will empty it. Note that the array (even though it contains nothing) will still exist. Another way of emptying an array is by using [the `undef()` function](#scalar-variable-functions).

### The `@ARGV` array

A special type of array is the pre-defined `@ARGV` array (**Arg**ument **V**ector). It automatically stores the arguments given to a Perl script from command line (`perl_script-01.pl 'argument_1' 'argument_2'`). It is how Perl is able to take arguments from command line.

Arguments are stored in the same order they are written in the terminal and can be accessed through the use of [indexes](#index) (`@ARGV[0]`, `@ARGV[1]`...).

There are 2 habits regarding `@ARGV` that will always have to be followed, when writing code:

1) **Assign the elements of** `@ARGV` **to variables**. One of the first things (in almost all cases the very first thing) the script should do, is to assign the elements of `@ARGV` to other variables (or another array, if needed) that are representative of what they contain. Very short scripts can avoid that, but it's mandatory for longer scripts or for scripts that accept many arguments from terminal.

2) **Always check what's inside** `@ARGV` in the first part of the script's code, which should be dedicated to checking the input and, if the arguments are not what they are supposed to be, terminating the program with a `die()` function accompanied by a message.
Example:

```perl
    #!/usr/bin/perl
    #some comment

    use strict;
    use warnings;

    my ($sample, $year) = @ARGV;
    if ((@ARGV != 2) or ($year !~ m/[0-9]+/)) {
        die("Please provide exactly 2 arguments: first the sample name and then the sampling year.\n");
    }

    if ($year !~ m/[1-9][9][0-9][0-9]/) {
        die("Year of sampling not valid: accepted years range from 1900-9999\n")
    }
```

## Data Input and Output

### The file operator

The easiest way to use a file as input in Perl, is by specifying its name in the terminal, after the script's name, so that the file is added to the `@ARGV` array. A file used as input like this can be read using [Perl's file operator (`<>`)](#main-facts), which reads a file line by line.
Example:

    my $line = 0;
    my $characters = 0;
    while (<>) {
        $line++;
        $line_length = length($_);
        $characters += $line_length;
        print("Line $line: $tot_lenght\n");
    }
    print("$characters total characters\n")

The example above demonstrates that, by default, `<>` *reads one line at a time from an input file specified as* `$ARGV[0]`: the template script, in this case, reads the file line by line, then counts and prints the number of characters in each line (iteration item, `$_`), until the end of the file is reached.

The `<>` operator can be used in different ways depending on the context:

    my $line = <>;
    
    my @lines = <>;

    $ perl file-op.pl array-exercise01.pl array-exercise02.pl

    <>;

    <IN>;

**First example:** assignment to a scalar variable outside a loop to read just one line from the input file.
**Second example:** assignment to an array to read all the lines from the file. This allows to **read multiple lines**.
**Third example:** if in a loop, the file operator can also loop through all files in `@ARGV`, one after the other. This allows to **read multiple files**.
**Fourth example:** reads a line from a file and does nothing with it (its then discarded). This can be used to skip lines when reading from a file (since the `<>` operator keeps track of the lines it processed, the next time it's used it will read the following line).
**Fifth example:** `<>` can also be used to contain a [filehandle](#the-open-function), like when specifying one of the `open()` function's filehandle or the standard input filehandle (see ["Reading from standard input"](#reading-from-standard-input) further down). This way we specify which of the files we opened we want to use.

### Reading from standard input

Just like Python's `input()` function, which allows the user to type some input from command line, Perl too has a way to use standard input as a program's input: the `<STDIN>` [filehandle](#the-open-function).
Example:

    print("Who are you?\n");
    my $name = <STDIN>;
    print('Greetings, $name\n');

### The `open()` function

Another way to access files in Perl is by using the `open()` function, which allows to specify a file to open either for reading or for writing, but not both at the same time.

To open a file with the `open()` functions, we need to specify 3 things:

- the name of the file to open;
- a *filehandle*: a special type of variable to associate the file's name to. Convention is to use all capital letters;
- a *mode*: one of the Unix-derived controls to read or write (or also append) to a file (`<`, `>` and `>>`);

Example:

    open(IN, "< $file");
    open(OUT, "> $outfile");

    # "< $file" are the mode and file name. 
    # In this case the file's name is stored into a variable.
    # Double quotes are needed just for variable interpolation.

    # IN and OUT are filehandles and can actually be any name.

    close(IN);
    close(OUT);

    # Each open() function is paired with a close() function.

> *NOTE:* Each filehandle should be closed with the `close()` function as soon as we finish working with it.

> *NOTE:* Bear in mind that Perl assumes the file used in the `open()` function is an existing file, thus [Perl also provides ways to check the file used as input](#checking-inputoutput-files).

> *NOTE: Perl allows to use many different syntaxes for the* `open()` *function: there can be many spaces between the mode and the file name or none at all, the mode can be a separate argument and, in read mode,* `<` *can be omitted.*

Another syntax feature is the ability to use standard scalar variables as filehandles (*indirect filehandles*):

    open(my $in, "<$file");
    # is equal to using
    open(IN, "<$file");

> The `open()` function can also be used ot receive or send output to a program. Check the [`open()` function subsection](#executing-commands-with-the-open-function) in the [program interaction section](#interaction-with-other-programs) for more information on this particular use of `open()`.

#### Checking input/output files

Since `open()`, like all Perl functions, returns True if it was successful and False if it wasn't, the easiest way to check that a file has been opened successfully and the filehandle was created, is to use a one-liner syntax for the `die()` statement:

    open(IN, "<$file") or die("Couldn't open $file. $!");

The example above also contains Perl's `$!` special variable, which is used by Perl to store some useful information about the nature of a failure, so it can be included in `die()` error messages. `$!` also has a newline character at the end by default.

#### Writing output with `open()`

When a file is opened (or created) with writing mode through the `open()` function, to write output we just need to use the `print()` function while specifying the output file as an additional argument (with filehandles or indirect filehandles):

    while (<>) {
        chomp;
        print(OUT);
    }
    print($out @ord_fields);
    print (OUT $something);
    print($out "This is output\n");

## Hashes

Hashes are the third data type in Perl (the other 2 being scalars and arrays); they are used to store information about 2 items (*key*-*value* pairs) that share a relation, as a more computationally-efficient way than using 2 different arrays and in a similar way to how a real-life dictionary works. A value in a hash can consequently be accessed if we know the key.

Hashes are denoted by using the percentage symbol `%` (when referring to the entire hash), while when referring to one item in the hash, we use the dollar sign instead (like with arrays) and curly braces containing the *key*.

    my %turtles;
    $turtles{'Leo'} = 'katana';
    print("$turles{Leo}\n");

In the example above:

- hashes are declared just like arrays and scalars;
- in the second line a key-value pair is created. The key is always put right after the hash name, between curly braces;
- If we were to print the hash value corresponding to the key 'Leo', we would get 'katana' as output.

> Hashes cannot contain multiple entries that use the same key, so if a new value was to be added for a pre-existing key, that key's value would be overwritten.

> As the last line in the previous example demonstrates, the string used as key doesn't necessarily have to be quoted, when in curly braces.

To add multiple key-value pairs to a hash we can use different methods; in all cases, though, the key must be immediately followed by its value:

    my %turtles2weapon = ('Leo', 'katana', 'Raph', 'sais', 'Don', 'bo', 'Mickey', 'nunchaku');

    my %StarW2Fside = ('Luke Skywalker',   'Light side',
                       'Darth Vader',      'Dark side',
                       'Obi-wan Kenobi',   'Light side',
                       'Darth Maul',       'Dark side');

    my %Spongebob_houses = (Spongebob   => 'pineapple',
                            Squiddi     => 'Moai statue',
                            Patrick     => 'rock',
                            'Mr. Krabs' => 'anchor');

All 3 of the methods listed above are equivalent.
In the first example we create a hash by simply listing each key, followed by its value.
The second method is just like the first, but whitespaces allow for a cleaner layout.
The third method uses the characters `=>`, which replace the comma between the key and its value. `=>` also allows to avoid using quotes for the keys (unless they contain spaces, special characters, functions or variables).

### Adding and removing key-value pairs

To add a key-value pair in a pre-existing hash, simply assign a new key value pair. If a new value for an existing key is added to a hash, that value overrites the older one.

To remove a key-value pair from a hash, use the `delete`  keyword:

    delete $StarW2Fside{'Darth Maul'};

To delete all the key-value pairs stored in a hash, simply assign an empty list to that hash:

    %Spongebob_houses();

### Checking hashes

To check if a certain key exists in a hash, use [the `exists` function](#hashes-functions). Note that `exists` only cares about the *existence* of a key, it does not care if there is no value associated to it; to check if there actually is a value associated to the specified key, use [the `defined()` function](#scalar-variable-functions) on the desired key:

    defined($urtles2weapon{Mickey});

[The functions `keys()` and `values()`](#hashes-functions) extract a list of keys and values respectively, which can be evaluated both in scalar or array context. Evaluating in array context gives an array containing a list of all the key or values, while evaluating in scalar context will give scalar variables containing the number of keys or values in the argument hash.

### Looping through hashes

Unlike arrays' items, key-value pairs in a hash have no defined order, so there's no guarantee looping through a hash's pairs will come out to be in the same order they were added or in any other specific order.

    foreach my $key ( keys(%hash) ) {
        ...
    }

The following examples are templates of `foreach` loops where the hash is preemtively [sorted](#the-sort-function). The first example sorts the keys, while the second example sorts on the values (it's trickier but can be useful when the value are numbers and we want to list them in ascending or descending order).

    foreach my $key ( sort(keys(%hash)) ) {
        ...
    }

    foreach my $value (sort( { $hash{$a} <=> hash{$b} } keys(%hash) ) ) {
        ...
    }

Even though the most common way to loop through a hash is with a `foreach` loop, the `while` loop can also be used, in combination with [the `each()` function](#hashes-functions), which returns a key-value pair every time it is called on a hash:

    while ( my ($key, $value) = each(%hash) ) {
        print("$key, $value\n");
    }

## Loops

### The `for` loop

Basic syntax of the `for` loop:

    for (initialisation; validation; update) {
        code executed for each iteration through the loop;
    }

**1) initialisation:** the item (variable) from which the loop will start. As a convention the variable is usually called `$i`, as a mathematics reference, but can be changed to anything is deemed more descriptive in the context;
**2) validation:** the condition that will define when the loop should end (or better yet, the loop keeps going as long as this condition is met);
**3) update:** specifies how the loop counter should change at each loop cycle.

Example:

    for (my $i = 0; $i < @gym_medals; $i++) {
        print("$i: $gym_medals[$i]\n");
    }

In the example above, the `$i` loop initialisation variable is set to 0 (a good habit is to count from 0 to 9, since arrays are counted that way). If the `use strict` pragma is not active, `$i` can be declared without `my` (which might be desirable). The code enclosed in the `for` loop will be executed as long as `$i` is less than the length of the `@gym_medals` array (thus up to the number of elements of the array) and, with each iteration, the loop counter will increase by 1 (`++` increment by 1 shortcut).
The `print()` function uses `$i` as the array index, to keep it updated with the loop count/array element.

### The `foreach` loop

The `foreach` loop allows to iterate through a list without using a loop counter variable. `foreach` *is a synonym of* `for`.
Basic syntax of the `foreach` loop:

    foreach temporary_variable (array_or_list) {
        code executed for each iteration through the loop;
    }

Examples:

    foreach my $turtle (@TMNTs) {
        print("$turtle\n");
    }

    foreach my $turtle (Leo, Raph, Don, Mikey) {
        print("$turtle\n");
    }

    foreach (pop(@TMNTs)) {
        print("$_\n");
    }

As demonstrated in the second example, it is not necessary to specify an array for the `foreach` loop: it can also be a list, something that **contains a list** or **returns a list** (last example, which would only print 'Mikey' since that would be the output array of `pop()`).

> NOTE: As demontrated in the third example, unless stated otherwise, each element of the list will be assigned to the pre-set variable `$_`, but it's always better to specify a more descriptive variable name.

#### The range `(..)` operator

Perl provides a *range* operator `(..)`, often used in conjunction with `foreach`, to easily specify a range of consecutive numbers or letters:

    foreach my $i (1 .. 10) {
        if ($i == 1) {
            print("You have $i free sample for a punch\n");
        } else {
            print("You have $i free samples for a punch\n");
        }
    }

The range operator works also with `for` loops, `for` and `foreach` being synonyms. In this case, it's like the range operator is replacing all the 3 conditions of a `for` loop:

    for my $letter (a .. z) {
        print("$letter\n");
    }

### The `while` loop

Basic syntax of the `while`loop:

    while (condition) {
        code executed as long as condition is True;
    }

`while`loops allow to execute code as long as the specified condition is met. This means we can create infinite loops the same ways we can do with Bash or Python.
The `while` loop can be used to loop through an array:

    while (@TMNTs) {
        my $TMNT = shift(@TMNTs);
        if (length($TMNT) == 3) {
            print("$TMNT it's a calm turtle\n");
        } else {
            print("$TMNT it's a wild turtle\n");
        }
    }

> NOTE: using `shift()` on the array will change its contents (eventually emptying it), which is not what we want when looping through an array. This example has only didactic purpuses and will be changed when I'll learn a better way to avoid an infinite `while` loop.

#### The `do` loop

Also known as the `do-while` loop, the rare `do` loop is a variation of the `while` loop, the main difference being that *it gets executed at least once*.
Basic syntax of the `do` loop:

    do {
        code to be executed;
    } while (condition);

### Loop control

#### `next`, `redo` and `last`

The 3 keywords `next`, `redo` and `last` are the main tools to carry out loop control. They are used as part of the code inside the loop and are often seen as part of `if` or `unless` statements in their alternative syntax or alone in their own line.

`next`: the `next` keyword immediately restarts the loop and advances the loop count. It allows to skip processing the current iteration of the loop.
Examples:

    foreach my $number (1 .. 20) {
        next unless ($number % 2 == 0);
    }

    next if ($number < 0);

`redo`: less common than `next` it also skips the loop, but it doesn't advance the loop count, effectively forcing the loop back to repeat itself.
Example:

    for (...) {
        ...;
        redo if (...);
    }

`last`: it quits the loop before it can finish, stopping it immediately, similarly to Python's "`break`" keyword.
Examples:

    for (...) {
        ...;
        if (...){
            print(...);
            last;
        }
    }

    last if (...);

> Note that `skip` and `redo` have different behaviours, thus `skip` may be more common than `redo` (which can lead to infinite loops), but it can't be used in the same situations (for example where `redo` is more appropriate while `skip` would potentially not produce any output at all).

#### Loop labels and the `goto` keyword

Normally, when using a keyword like `last` or `next`, that only applies to the current loop the program is in, which means that if the keyword is in a loop that is inside another loop (nested loops), only the inner loop will be affected. Perl, though, also provides a way to label specific loops so that one can also specify which loop to affect with a certain keyword, regardless of position: **Loop Labels**.

Loop labels are written in front of the desired loop keyword, they are always written in capital letters followed by a colon and can be any name:

    OUTER: for (my $i ...) {
        INNER: for (my $j ...) {
            do something;
            if (something happens) {
                next OUTER;
            }
        }
    }

This way the `next` keyword will affect the outer loop (aptly named `OUTER`) instead of the inner loop (`INNER`), as it would do normally, since a Loop Label has been specified.

Loop Labels are also used in combination with the `goto` keyword:

`goto`: the execution goes directly to the specified label.

Example:

    if (something happens) {
        goto TARGET_BLOCK;
    }

> NOTE: using labels can be a necessity sometimes, but should be kept at a minimum to avoid making the code less readable. Furthermore, use of the `goto` keyword should be avoided, and used only if really necessary, to prevent writing confusing scripts.

## Interaction with other programs

### The backtick operator

Perl provides an easy way to interface with the UNIX shell: everything that is written in between backticks (`` ` ``) will be executed as a command in the UNIX shell (example: `grep 'Bart'`).

### The `qx` operator

As an alternative, in Perl is also present the `qx//` operator, which works the same way of backticks, but it allows to choose any pair of delimiters (`qx//`, `qx''`, `qx()`, `qx@@`, `qx[]`, `qx{}`...).

    my @in_files = `ls ~/Documents/prj/data/`
    my @in_files = qx(ls ~/Documents/prj/data/)
    my @in_files = qx'ls ~/Documents/prj/data/'

In the examples above, the second and third lines are equivalent to the first. Being able to choose the delimiters is a valuable tool, since `/` would be in conflict with the syntax for paths and directory structure in UNIX-based systems.
Note that using single quotes will prevent variable interpolation (double quotes could be a better delimiter if a variable is included in the Bash command).

### The `system()` function

Usually the backticks (or `qx`) are used when the focus is into capturing the output of a command. When the interest is to run other commands or **other Perl scripts** that do not return an output, Perl provides also the [`system()` function](#functions).

Examples:

    system(perl, ~/Documents/scripts/perl_scripts/myscript.pl);

    system("mkdir -p $prj_name/{input/data,output,ref}");

The Bash command's arguments, flags and option have to be listed as a list separated by commas or have to be delimited by quotes. Note that using double or single quotes does make a difference, just like for `qx`.

### Checking execution with the `$?` variable

Perl can access UNIX's special variable `$?`, which stores the *exit status* of the last executed program, so the execution of a command can be known by checking `$?` (which will be 0 if the execution was successful and != 0 if the execution failed).

Example:

    my ($pattern, $dir) = @ARGV;
    
    foreach $i (`ls $dir`) {
        my @in_files = qx"grep $pattern";

        if ($?) {
            print("Bash command execution failed.\n");
        } else {
            print("Bash command executed correctly.\n");
        }
    }

> Note that in the example above is possible to build a simple `if` statement to check `$?` because the value 0 is considered logically False, while values != 0 are logically True.

### Checking execution with `or` and `system()`

If a command is launched in the shell with `system()`, the logical operator `or` and the `==` operator can be used to test the exit status of the command right away, without calling `$?`, using `or` and `==` on their own:

    system("$command") == 0 or die("Bash command execution failed\n");

### Executing commands with the `open()` function

Perl's `open()` function can also be used to send or receive output from a program in addition to reading or writing to a file.


