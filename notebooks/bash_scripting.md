# Bash Scripting  Syntax

> Each Bash script begins with the "Shebang line": `#!/usr/bin/bash`.

## Test Command

- **`[]`**

The test command. Used to test the condition between brackets (classic `sh` shell).

- **`[[]]`**

Test command in Bash shell (less portable, but much more powerful).

In Bash, either single brackets or double brackets can be used in `if` conditions, but there are important differences between them.

1. Single Brackets are the traditional POSIX-compliant `test` command. This syntax requires spaces between brackets and operators and **does not** support the logical operators `&&` and `||`, nor regex matching. Also, string comparisons using `<` and `>` require escaping or using the explicit `test` command instead.

2. Double Brackets is the Bash-specific enhancement (not POSIX). This syntax is more flexible powerful, since it allows logical operators (`&&` and `||`) as well as regex matching (`=~`). Furthermore, there is no need to escape `>` and `<` for string comparisons.

| Feature                     | `[ ... ]` (POSIX)        | `[[ ... ]]` (Bash)   |
| --------------------------- | ------------------------ | -------------------- |
| Logical operators           | Not supported            | Yes                  |
| Regex matching (`=~`)       | Not supported            | Yes                  |
| `<` and `>` for strings     | Needs escaping (`\>`)    | No escaping needed   |
| Supports wildcards (`*`)    | Not supported            | Yes                  |
| Works in all shells         | Yes                      | Bash/Ksh only        |

## String Comparison

### `=`

"Equal to". Evaluates to True if the two strings are equal. (Syntax: `<string_1> = <string_2>`).

### `!=`

"Not equal to". Evaluates to True if the two strings are not equal. (Syntax: `<string_1> != <string_2>`).

### `-n`

"Not null". Evaluates to True if the string is not null. (Syntax: `-n <string_1>`).

### `-z`

"Null" or "empty". Evaluates to True if the string is null or empty. (Syntax: `-z <string_1>`).

## Expressions for Numeric Comparison

### `-eq`

"equal to". Evaluates to True if the two expressions are equal. (Syntax: `<expression_1> -eq <expression_2>`).

### `-ne`

"not equal". Evaluates to True if the two expressions are not equal. (Syntax: `<expression_1> -ne <expression_2>`).

### `-gt`

"greater than". Evaluates to True if the the first expression is greater than the second. (Syntax: `<expression_1> -gt <expression_2>`).

### `-ge`

"greater than or equal to". Evaluates to True if the the first expression is greater than or equal to the second. (Syntax: `<expression_1> -ge <expression_2>`).

### `-lt`

"lower than". Evaluates to True if the the first expression is lower than the second. (Syntax: `<expression_1> -lt <expression_2>`).

### `-le`

"lower than or equal to". Evaluates to True if the the first expression is lower than or equal to the second. (Syntax: `<expression_1> -le <expression_2>`).

## Logical Expressions

### `!`

Logical NOT. Evaluates to True if the expression is False and *vice versa*. (Syntax: `! <expression_1>`).

### `||`

Logical OR (for conditions). Evaluates to True if either one of the expressions at the sides evaluates to True.

### `&&`

Logical AND (for conditions). Evaluates to True if both the expressions at the sides evaluate to True.

## Conditionals to Check Files

### `-d`

Evaluates to True if the file is a directory.

### `-e`

Evaluates to True if the file exist. *Note: the* `-e` *option is not portable, and is usually replaced with `-f`.*

### `-f`

Evaluates to True if the file is a regular file.

### `-g`

Evaluates to True if set-group-id is set on the file.

### `-r`

Evaluates to True if the file is readable.

### `-s`

Evaluates to True if the file has a non-zero size.

### `-u`

Evaluates to True if set-user-id is set on the file. 

### `-w`

Evaluates to True if the file is writable.

### `-x`

Evaluates to True if the file is execuatable.

## Control Statements

- **Loop statements** (keyword `for`)

- **Selection instructions** (keyword `select`)

- **Conditional statements** (keywords `if`, `then`, `elif`, `else`, `fi`)

### Keywords and Related Commands

- **`read`**

- **`case`**

### `while` Loop

It sets up a while loop.

Examples:

```bash
# with read -r
while read -r line; do
    echo "$line";
done < input.file

# with IFS
while IFS= read -r line; do
    echo $line;
done < input.file
```

> - The `-r` option passed to the `read` command prevents the backslash escapes from being interpreted.
> - The `IFS` (Internal Field Separator) option can be used before the `read` command and it prevents leading or trailing whitespace from being trimmed, by setting it to a null string (`IFS= `).
> 
> Most of the time those 2 options are not necessary, but using them as a precaution prevents badly formatted input files from causing problems during the line-by-line read.

In the example below, the while loop is passed 2 arguments. To be able to do that, we need to use file descriptors (i.e. >& and <& together with 0, 1, 2 and so on) to distinguish the different inputs. 0, 1 and 2 are the file descriptors for `stdin`, `stdout` and `stderr` in Bash, respectively, but inside a loop or a function Bash will interpret them as the file descriptors of the specified files and not of the standard flows. Nevertheless, it's good practice to start from 3 when using descriptors.

```bash
while IFS= read -r old_name <&3 && IFS= read -r new_name <&4; do
    mv "$old_name" "$new_name";
done 3<IDs-old.lst 4<IDs-new.lst
```

> The example above uses the while loop to rename files an file descriptors to tag the files from which the old and new file names will be taken from.

### `if`-`else` Statements

Basic syntax of `if`-`else` statement:

```bash
num=10
if [[ $num -gt 5 ]]; then
    echo "The number is greater than 5."
else
    echo "The number is 5 or less."
fi
```

More examples:

- **Checks on argument**

Bash's `-d` flag can be used to check if an argument is a valid directory, while `-z` returns True if the variable is empty:

```bash
#!/bin/bash

# Check if an argument is provided
if [[ -z "$1" ]]; then
	echo "Usage: $0 <directory>";
	exit 1;
fi

# Check if the argument is a directory
if [[ -d "$1" ]]; then
	echo "'$1' is a directory.";
else
	echo "'$1' is NOT a directory.";
fi
```

## Command-Line Arguments

Short Bash snippet on command-line arguments managed in a script:

```bash
#!/bin/bash

# Access command-line arguments
echo "First argument: $1"
echo "Second argument: $2"

# Count the number of arguments
echo "Total arguments passed: $#"

# Print all arguments
echo "All arguments: $@"
```

## Command-Line Arguments and Flags in a Bash Script 

To handle flags and provide a help message in a Bash script, you can use `getopts` to parse command-line options. The example below also handles a "help" `-h` flag, for printing the help message

```bash
#!/bin/bash

# Define function to display help
show_help() {
    echo "Usage: $0 [-h] [-d <directory>] [-f <file>]"
    echo
    echo "Options:"
    echo "  -h            Show this help message"
    echo "  -d <dir>      Specify a directory"
    echo "  -f <file>     Specify a file"
}

# Default values for variables
directory=""
file=""

# Parse options using getopts
while getopts "hd:f:" opt; do
    case $opt in
        h)
            show_help
            exit 0
            ;;
        d)
            directory="$OPTARG"
            ;;
        f)
            file="$OPTARG"
            ;;
        *)
            show_help
            exit 1
            ;;
    esac
done

# Example of using the provided arguments
echo "Directory: $directory"
echo "File: $file"
```

> **NOTE:** `getopts "hd:f:"` parses the flags `-h` (help), `-d` (directory), and `-f` (file). The **colon** (`:`) indicates that the **flag requires an argument**. To set a flag that requires no argument, don't follow it up with `:` (like `h` in the `getopts` line).

---

> More on: <https://www.youtube.com/channel/UCuJu9kuwgXP1_Gd1d-Yx6wQ>
