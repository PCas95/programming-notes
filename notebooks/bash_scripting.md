**Bash Scripting  Syntax**

Each Bash script begins with the header `#!/usr/bin/bash


`[]`-> the test command. Used to test the condition between brackets.


*Strings*

`=` -> "equal to". Evaluates to True if the two strings are equal. (Syntax: `<string_1> = <string_2>`).

`!=` -> "not equal to". Evaluates to True if the two strings are not equal. (Syntax: `<string_1> != <string_2>`).

`-n` -> "not null". Evaluates to True if the string is not null. (Syntax: `-n <string_1>`).

`-z` -> "null" or "empty". Evaluates to True if the string is null or empty. (Syntax: `-z <string_1>`).


*Expressions*

`-eq` -> "equal to". Evaluates to True if the two expressions are equal. (Syntax: `<expression_1> -eq <expression_2>`).

`-ne` -> "not equal". Evaluates to True if the two expressions are not equal. (Syntax: `<expression_1> -ne <expression_2>`).

`-gt` -> "greater than". Evaluates to True if the the first expression is greater than the second. (Syntax: `<expression_1> -gt <expression_2>`).

`-ge` -> "greater than or equal to". Evaluates to True if the the first expression is greater than or equal to the second. (Syntax: `<expression_1> -ge <expression_2>`).

`-lt` -> "lower than". Evaluates to True if the the first expression is lower than the second. (Syntax: `<expression_1> -lt <expression_2>`).

`-le` -> "lower than or equal to". Evaluates to True if the the first expression is lower than or equal to the second. (Syntax: `<expression_1> -le <expression_2>`).

`!` -> "not". Evaluates to True if the expression is False and *vice versa*. (Syntax: `! <expression_1>`).


*File conditional results*

`-d` -> Evaluates to True if the file is a directory.

`-e` -> Evaluates to True if the file exist. *Note: the* `-e` *option is not portable, and is usually substituted dy `-f`.*

`-f` -> Evaluates to True if the file is a regular file.

`-g` -> Evaluates to True if set-group-id is set on the file.

`-r` -> Evaluates to True if the file is readable.

`-s` -> Evaluates to True if the file has a non-zero size.

`-u` -> Evaluates to True if set-user-id is set on the file. 

`-w` -> Evaluates to True if the file is writable.

`-x` -> Evaluates to True if the file is execuatable.


*Control statements*

**- Loop statements** (keyword `for`)

**- Selection instructions** (keyword `select`)

**- Conditional statements** (keywords `if`, `then`, `elif`, `else`, `fi`)


*Commands*

`read`

`case`

`while`
    like in python, it sets up a while loop.
    MORE: Sometimes there is the need to read a file line by line. The following syntax is used for bash shell to read a file using while loop:

    while read -r line;
    do
       echo "$line" ;
    done < input.file
    
    The `-r` option passed to the `read` command prevents the backslash escapes from being interpreted.
    The IFS (Internal Field Separator) option can be used before the `read` command and it prevents leading or trailing whitespace from being trimmed by setting it to 
    a null string (`IFS= `). Most of the time those 2 options are not necessary, but using them as a precaution prevents badly formatted input files from causing problems during
    the line-by-line read.

    while IFS= read -r line;
    do
        echo $line;
    done < input.file

    `line` is a variable. What is inside that variable is controlled by the while loop's input, defined, as required in Bash, after the call with the `<` symbol. 
    In the following example the while loop is passed 2 arguments. To be able to do that, we need to use file descriptors (i.e. >& and <& together with 0, 1, 2 and so on)
    to distinguish the different inputs. 0, 1 and 2 are the file descriptors for stdin, stdout and stderr in Bash, respectively, but inside a loop or a function they can be used
    anyway, since Bash will interpret them as the file descriptors of the specified files and not of the standard flows. This holds true unless there is the need to refer to
    standard flows in the loop or function (and there normally isn't); however it is a better practice to start from 3 when using descriptors.
    In the following example we use the while loop to rename files: we created 2 lists, one containing the old (to be changed) file names and one with the new names we want for 
    each of those files; those 2 lists are taken as input (file descriptors 3 and 4) and put into their respective variables, which are later passed as arguments to `mv`, so that
    for each line that is put into the 2 variables for each iteration, the old name (corresponding to the file name in the working directory) is changed to the new one.

    while IFS= read -r old_name <&3 && IFS= read -r new_name <&4; 
    do
        mv "$old_name" "$new_name"
    done 3<IDs-old.lst 4<IDs-new.lst

    The following is another example of how to set up a while loop to extract specific lines from a file, while using a second file as a list. The while loop will read from a list (file2) the lines we want to isolate from file1. This case illustrates also that using file descriptors is not always necessary:

    while read line; do
        grep "${line}" file1
    done < file2

The same loop can be used to save the output to a file, by using stdout redirection after the `grep` command line.





more on: <https://www.youtube.com/channel/UCuJu9kuwgXP1_Gd1d-Yx6wQ>














