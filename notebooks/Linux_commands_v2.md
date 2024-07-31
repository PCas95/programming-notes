# LIST OF GNU/LINUX BASH COMMANDS AND FEATURES

## Wildcards, expansions and autofill

### `*`

The asterisk wildcard is used in place of zero or more characters in a name. 

It can be used in a file name when retrieving files, or in a command to designate as targets all files or program names that match the rest of the expression; (*e.g.* files with the specified extension or partial name in current directory or specified path). It ignores hidden files.

Examples:

```sh
ls *.txt
ls Pictures/image*
ls picture*.png
cp Documents/* /media/pierluigi/Sony_16/backup/.
```

In the first example the `ls` command will return all files with a `.txt` extension, regardless of their name.

In the second example all files in the specified path with "image" in their name are retrieved, regardless of file type or any other character in the name (so `image_01.png`,  `image_02.jpg` and `images/` are all eligible targets).

The third example uses `*` in-between characters: since the asterisk expands to zero or more characters, all `.png` files whose name start with "picture" are retrieved, so `picture_01.png`, `picture_3a.png` and even just `picture.png` would be eligible targets.

The last example uses the asterisk to copy all contents of a directory.

### `?`

The question mark wildcard works just like `*`, but only expands to one character. It also ignores hidden files.

### `[]`

The square brackets wildcard is used to match all objects with names that have characters  among those listed inside or in a specified alphanumeric range.

Example: `zmays[A-F].fastq` would retrieve `zmaysA.fastq`, `zmaysB.fastq`, `zmaysC.fastq` etc. all the way to "F".

It can be used either with an alphanumeric range (`[A-Z]` or `[0-9]`) or by specifying the characters in the range (*e.g.*: `zmays[UVWX].fastq`). Since `[]` only supports characters, it doesn't work for numbers higher than 9, which contain more than one digit.

The syntax for `[]` is more strict than that of other wildcards: to use this wildcard to the fullest, it's necessary to work in a reproducible way.

### `{}`

Brace expansion. A type of Shell expansion to include multiple elements in an argument. It's used like the `[]` wildcard, but the syntax is a bit different:
- it can include words and elements formed by more than one character, special characters and numbers with more than one digit;
- different elements of the list are divided by `,` without spaces, while alphanumeric ranges use `..` instead of `-` (`{10..13}` as opposed to `[0-9]`);
- does not care whether a matching item exists or not (see examples in [`mkdir` section](#mkdir)).

### TAB

Autofill: autocompletes the command, reserved word or file name if enough characters have been written (in case of words with the same characters, TAB will autocomplete up to the closest "branch" to distinguish words/names).

---

> **NOTE 1:** the wildcards (`*`, `?` and `[]`) only expand to existing files that match them, while brace expansions (`{}`) will always expand regardless of whether corresponding files or directories exist or not (which is why the latter can also be used to create new directories, directory structure and files).

> **NOTE 2:** hidden files in Linux are all those files whose names start with a period (*e.g.:* `.hidden_file.txt`).

## Keybard shortcuts (distro-dependent)

### System-wide

#### **`Ctrl`+`Alt`+`T`**

Opens Terminal.

#### **`Ctrl`+`Alt`+`up_arrow`**

Shows all workspaces. Press either `Ctrl`+`Alt`+`down_arrow` or `Ctrl`+`Alt`+`up_arrow` again to go back.

#### **`Ctrl`+`Alt`+`left`/`right_arrow`**

Navigate workspaces.

#### **`Ctrl`+`Alt`+`down_arrow`**

Shows windows in current workspace. Press either `Ctrl`+`Alt`+`up_arrow` or `Ctrl`+`Alt`+`down_arrow` again to go back or `left`/`right` arrows to navigate windows.

#### **`Super_Key`+ arrows**

Anchors active window to matching desktop side.

#### **`Super_Key`+`D`**

Reduces all windows.

#### **`Alt`+`F4`**

Closes active window.

#### **`Alt`+`TAB`**

Cycles windows in current workspace.

#### **`Ctrl`+`Alt`+`L`**

Locks current session (also **`Super_Key`+`L`** in some distros).

#### **`Alt`+`F10`**

Maximise/minimise window (to minimise window **`Alt`+`F5`** is equivalent).

### Shell shortcuts

#### **`Ctrl`+`C`**

Quits a running program and gives back the command prompt (soft kill). Usually for programs that hang or keep giving output.

Some running programs may require a different input to exit (*e.g.*: `q` for `man` or `htop`, `Ctrl`+`X` or `Esc`+`X` for `Nano`, *etc*...).

#### **`Ctrl`+`Z`**

Suspends a running foreground program (check [`bg` command](#bg)).

#### **`Alt`+`A`**

Go to the beginning of the line (equal to `Fn`+`left_arrow`. Depending on the device they may be alternative or both available).

#### **`Alt`+`E`**

Go to end of the line (equal to `Fn`+`right_arrow`. Depending on the device they may be alternative or both available).

#### **`Shift`+`Ctrl`+`C`/`V`**

Copy/paste in terminal.

#### **`Ctrl`+`L`**

Executes the [`clear`](#clear) command.

#### **`Ctrl`+`R`**

Searches in Bash's [history](#history) (**history's reverse search**).

## System Variables

*System variables are special variables that are reserved to the system, since they hold a special meaning for the OS. They are all written in capital letters.*

### `$HOME`

Current user's home directory path.

### `$PATH`

A list of directories to search for commands (meaning where commands *reside* and where the system *looks for* commands). In order for a script (Bash or any language) to be run simply by typing its name in the terminal, the directory in which it is stored must be added to the system's `$PATH` (check [`export`](#export) to do that).

### `$PS1`

The primary command prompt (normally `$`, `#` for root, in bash also `[\u@\h\W]$`, which is more complex and tells additional information).

### `$PS2`

A secondary prompt, usually `>`, used when prompting for additional information, like when prompting for further input, like a password, or after a `\`.

### `$O`

The name of the shell script (`bash` for systems with Bourne-Again Shell).

### `$#`

Number of parameters passed.

### `$IFS`

**I**nternal **F**ield **S**eparator: a special shell variable used to determine the delimiter used to split strings into words. The default delimiter is blank space, but any other character or metacharacter can be used.

After assigning the value to the `$IFS` variable, the string value can be read by two options:

- `-r` is used to read backslash (`\`) as a character rather than escape character;
- `-a` is used to store the splitted words into an array.

Examples:

    $ head -n 3 Documents/work/ML-Pyseer/CMP_GENPAT.csv
    Codice;Codice Interno
    2022.EXT.1017.1416.54294;SRR13079936
    2022.EXT.1017.1416.54293;SRR13079919

    $ while IFS=';' read -a line; do
    > echo ${line[0]}
    > echo ${line[1]}
    > done < <(head -n 3 Documents/work/ML-Pyseer/CMP_GENPAT.csv)
    Codice
    Codice Interno
    2022.EXT.1017.1416.54294
    SRR13079936
    2022.EXT.1017.1416.54293
    SRR13079919

The first command in the examples above shows the line structure of the file used as argument in the second command.

The second command (a `while` loop) reads each line from the input, splits it accordingly to the character stored in `$IFS` and finally prints the values at indexes 0 and 1 of the array in which the splitted lines are stored, at each iteration of the loop.

> **NOTE:** the syntax `${#arraynamehere[*]}` can be used to return the total number of words stored in the array.

<!-- insert links to array and variable declaration []() -->

### `$$`

[PID](#pids) of the shell script.

### `$?`

It stands for the exit status of the last run command (often used with `echo` in `echo $?` to check the exit status).

> In Bash and in scripting in general, commands and programs have an exit status which tells whether the process was executed successfully (exit status 0) or an error occurred (process execution unsuccessful, exit status >0). This is the general practice, but for scripts we can actually include in the code a customised and more explicative error management with different error codes and associated messages).

## - Streams and redirections

### `|`

Turns the output of the command written before it into the input for the command that follows.

Examples:

```sh
ps -aux | grep <program_name>

apt-cache search | grep <package_name>
```

### `&`

Runs the preceding invoked program on the background, so that the prompt is still ready to receive other instructions.

Example:

```sh
firefox &
```

> See the [Managing processes section](#managing-processes) for more information about the management of background processes.

### `&&`

Instructs the terminal to execute commands in succession (with a single command line and regardless of [exit status](#6)). 

Example:

```sh
sudo apt-get update && sudo apt-get upgrade
```

### `;`

Just like `&&`, but the second command is executed only if the previous command had [exit status](#6) `0` (successful).

### `>` and `>>`

The `>` (*greater than*) operator redirects standard output to a file. `>` redirects output to a file and it overwrites any existing contents in it. `>>` redirects output to a file but doesn't overwrites its contents: it appends new output at the end of any pre-existing information/data.

Both `>` and `>>` do not care if the output file exists or not: if the specified output file does not exist already, they create it.

### `2>` and `2>>`

Redirect standard error to a file. Syntax is the same as standard output redirection.

Example:

```sh
program1 > st_output.txt 2> st_error.txt
```

### `<`

Standard input redirection: it allows to use a file as input for a program that can take an input argument, such as `grep`, `awk` or `sort`.

Examples:

```sh
program1 < input_file.txt >> st_output.txt
```

Be aware that sometimes it's more common to use pipes for the same task:

```sh
cat input_file | program1 > st_output.txt 2> st_error.txt
```

The `<` operator is also important for [Process Substitution](#process-substitution), for which the syntax uses the `<` operator combined with the [subshell](#subshell-commands_here) `()`.

### `2>&1`

Redirects standard error to standard output.

If there is the need to use standard error as an input (like if we want to search its contents through `grep`), `|` can't be used just like that, since it only takes standard output and ignores standard error; thus we first need to redirect standard error to standard output, only then the merged stream can be piped: `program1 2>&1 | grep 'error'`.

### Subshell `(commands_here)`

*Subshell*: putting sequential commands in parentheses executes them in a subshell, grouping sequential commands together. This is important when pipelining commands or when using [Process Substitution](#process-substitution-and-fifos) to use the output of one command as input for another, for example.

### `tee`

`tee` is used as a "T junction" to write a copy of the standard output being fed to pipeline to an intermediate file, while still passing the output to pipeline and, in the end, to the program that uses it as standard input.

Example:

```sh
program1 input_file | tee intermediate_file.txt | program2 > results.txt
```

## - Command Substitution

In UNIX systems, commands (and their outputs) can be integrated into other lines of commands; to accomplish that, we use *Command Substitution*, which means that we put a command inside another command using a specific syntax: the `$` symbol followed a [subshell](#subshell-commands_here), which means by parentheses containing the command we want to put into the second one.

Examples:

```sh
echo "There are $(grep -c '^>' input.fasta) entries in my FASTA file." > notes.txt

mkdir results-$(date +%F)
```

The first example uses Command Substitution in an `echo` command to return the number of headers in a fasta file inside a message. This works as an example, but since `grep` returns the output to terminal, we actually need only to run `grep -c` to get the number of matches.

The second example uses `mkdir` to create a directory with current date as suffix, using command substitution with the command [`date`](#date), which returns current date in the specified format (in this case, `+%F` means *yyyy-mm-dd* format). A good idea is to merge this trick with the [creation of directory structure](#mkdir).

## - Process Substitution and FIFOs

### FIFOs

Some programs take more than one input  and/or provide more than one output. With such programs we can't use normal piping, which only works when the output to be piped as input in the following program is univocal.

To interface those programs with others in the pipeline, UNIX provides **FIFOs** (**F**irst **I**n, **F**irst **O**ut): named pipes that behave like files (*i.e.* are persistent in the filesystem). A named pipe is treated as any other file: is created with the `mkfifo` command, removed with [`rm`](#rm) and any process can access and read from it; however, like a normal pipe, data that has been read from it is no longer there. Named pipes are recognised from the `p` when they are listed with `ll`:

    $ mkfifo "testfifo"
    $ ll "testfifo"
    prw-rw-r-- 1 pierluigi pierluigi 0 Apr 29 12:24 testfifo|
    $ rm "testfifo"

*When using named pipes, we're not writing to the disk: FIFOs provide the computational benefits of standard pipes with the flexibility of interfacing with files.*

#### `mkfifo`

Creates a named pipe with the specified name. Named pipes behave like files and are deleted with the `rm` command.

### Process Substitution

To avoid creating and removing named pipes, the actual syntactic shortcut named "Process Substitution" is often used. This allows to invoke a process and have its standard output go directly into a named pipe, without creating it first and without removing it later.

Example 1:

    $ program_1 --in1 <input_1> --in2 <input_2> --out1 <output_1> --out2 <output_2> &

> In this case input_1 and input_2, as well as the 2 outputs, are usually files. However, we can instruct the program to read from named pipes whose content is the data from the upstream program, as well as to output data to named pipes, so that input redirection can be used with the downstream program(s) to read data from the named pipe(s), as long as those named pipes were previously created.

Example 2:

    $ downstream_pr --in1 <(upstream_pr1 "raw_data1.txt") --in2 <(upstream_pr2 "raw_data2.txt") --out1 >(gzip > out1.txt.gz) --out2 >(gzip > out2.txt.gz)

> This example presents **Process Substitution**: the program named "downstream_pr", which requires 2 inputs, is being fed the output of "upstream_pr1" and "upstream_pr2" (each getting its own input from a file) through the shortcut `<(...)`, which redirects the output of the program inside parentheses as input to "downstream_pr" by using an "anonymous named pipe", with no need to create them beforehand or to remove them after the run. \
> Since the same shortcut can be used to capture output too, in the example above we use `>(...)` (the analog to `<(...)` for output) to compress the output data in the output stream before writing it to the disk.

Further examples:

```bash
perl ~/Documents/scripts/perl_scripts/diff-lst.pl \
    <(sort ~/Documents/work/ML-Pyseer/downloaded_samples-FILT.tsv) \
    <(sort ~/Documents/work/ML-Pyseer/clinical_in_downloaded.tsv) > perltest.tsv

join -1 1 -2 1 <(sort downloaded_samples-FILT.tsv) \
    <(sort samples_check/sample_list4larus/Clinical_\ Samples.csv | uniq)   
    > downloaded_samples-FILT-NH.tsv 

while read line; do \
    grep -v "${line}" downloaded_samples-FILT.tsv >> downloaded_samples-FILT-no_cl.tsv; \
done < <(sort samples_check/sample_list4larus/Clinical_\ Samples.csv | uniq)

diff -y tot-pdf-samn.lst <(cut -f1 downloaded_samples-FILT.tsv)
```

> NOTES: Process Substitution only works if we use a command in the [subshell](#subshell-commands_here); furthermore some syntaxes that use already a `<` to define input, like the `while` loop, may require to use `< <(process-substitution)`, doubling the `<`.

## Variables, arrays and loops


### Variables

Variables work like in any scripting language, being assigned with the assignment operator `=`. In Bash though, variables don't require a prefix when they are being set, but they do require a prefix `$` symbol when they are being referenced/invoked.

Thus, a variable is called "naked" when it lacks the `$` in front, which happens when it's being assigned. The `$` is necessary to reference the variable after assignment.

```bash
a=42  # assignment

echo ${a}  # reference
```

#### Variable assignment

Assignment can happen in different ways:

1) using the `=` assignment operator: `a=42`
2) using the `let` keyword (which works just as Perl's `my`): `let a=16+5`


3) in a `for` loop (a type of disguised assignment):

    ```bash
    for a in 7 8 9 11
    do
        echo -n "$a "
    done
    ```

4) in a `read` statement:

    ```bash
    echo -n "Enter 'a'"
    read a
    echo "$a"
    ```

Other than directly storing values, **variable assignment can also capture output of command subsitutions (backticks and `$()`)**,  so that in the variable there will be the output value of the substituted command:

```bash
for i in $(cat test.tsv); do
    a=$(cut -f1 ${i})
    echo ${a}
done
```

#### Referencing a variable

There are different ways to reference variables in Bash: `$a`, `"$a"`, `${a}` and `"${a}"`.

Using `$` is always necessary to reference variables after they have been assigned. Many programs will require to use quotes to expand variables: in such cases double quotes are to be used (single quotes prevent variable expansion)

As for the curly braces, most of the times are used for need of delimiters, so that if a variable has to be written attached a string, Bash can distinguish the variable from the rest of the string:

```bash
for i in $(cat prj-directories.lst); do
    echo "/home/pierluigi/Documents/work/prj-test/${i}/*.fastq.gz"
done

${ID}.stdout
```

Using both double quotes and curly braces may be necessary for the same purpose, but the braces are sufficient for expansion.

#### Parameter Expansion

Another important use of braces is parameter expansion: some symbols can be put inside braces, together with the variable name, to perform some actions on the value stored in the variable:

```bash
${a%.fasta}
${ID%.fasta}.stdout
```

The `%` symbol after the variable name removes the specified suffix from the end of the string value in the variable. Only the first match going back from the end of the string is removed this way.

Writing text before or after the variable will result in giving as output the value in the variable attached to the added string. This is an example of the importance of curly braces for separation of the variable from surrounding text.

```bash
${name^}
${name^^}

${name,}
${name,,}
```

The `^` symbol is used to convert the first character of any string to uppercase, while the `^^` symbol is used to convert the whole string to uppercase.

The `,` symbol is used to convert the first character of the string to lowercase, while `,,` converts the whole string to lowercase.

### Arrays

Arrays are data types equal to Perl's arrays and Python's lists. They store a list of values and their assignment is similar to Perl's:

```bash
tmnts=("Leo" "Don" "Raph" "Mikey")
```

Bash does not typically require curly braces to reference variables, but it does for arrays, which are consequently referenced as `${array}` or `${array[@]}`. The `@` symbol in square brackets instructs to access all the elements in the array. Without it, only the first element in the array would be accessed.

Like for all scripting languages, specific elements of an array are retrieved using the corresponding index number in square brackets. Using `[@]` will retrieve the whole array.

To Access an Array in Bash, it can be given as output in its entirety, a specific element can be accessed through its index, or the array can be looped through.

There are 2 main ways to loop through an array:

* loop through the elements themselves;
* loop through the indices.

A `for` loop is the tool used to loop through the array elements or indices:

```bash
# looping through array elements:
for i in ${array[@]}; do
    echo $i
done

# looping through array indices:
for i in ${!array[@]}; do
    echo "${i}: ${array[$i]}"
done
```

> NOTE 1: In the first example the `@` symbol in square brackets instructs to loop through all of the elements in the array. Only the first element of the array would be printed if referencing the array just as `${array}`.

> NOTE 2: In the second example the `!` at the beginning of the array name instructs to access the indices of the array and not the elements themselves.

## Managing processes

*Appending an* [`&` (ampersand)](#8) *to a program's command will execute it in background, allowing the user to have the shell prompt still ready to get input.*

*In this section are described commands to retrieve PIDs, kill processes and manage background and foreground programs.*

Quick links to main arguments:

- [`ps` and `htop`](#commands-to-retrieve-pids-ps-htop);
- [`jobs`](#jobs-related-commands);
- [`kill`](#killing-processes);

### PIDs

**PIDs** are unique **P**rocess **Id**entifiers. Each process ([including the shell](#5)) has a PID, which can be used to manage or kill the process.

### - Commands to retrieve PIDs (`ps`, `htop`)

#### `ps`

Provides a snapshot list of current processes. \
Common syntaxes:

```bash
ps -ef
ps -aux
ps -aux | grep <program_name>
ps -f | grep <user_name>
```

> `ps` can provide a list of all processes (`a` or `e` flag) in full-list format (`f` flag). Both `ps -aux` and  `ps -ef` are often used together with `grep` to filter the desired process, in order to know its PID or other information. \
>The syntaxes in the first 2 examples are largely equivalent, but output mildly different field format (`-aux` is legacy BSD-like syntax added to facilitate transition to more modern options).

`ps -aux`:

    USER    PID %CPU    %MEM    VSZ RSS TTY STAT    START   TIME    COMMAND

`ps -ef`:

    UID PID PPID    C   STIME   TTY TIME    CMD

> `ps`'s output fields, however, are widely customisable (check manual). 

If `ps` is invoked with no flags, a simple list of main processes rooted at current user are listed together with their PID and execution time. This is especially useful to get in a simple and effective way the parent process in a process tree or forked processes, so that it can be killed together with all the child processes.

```
    PID TTY          TIME CMD
   4318 pts/1    00:00:00 bash
   4324 pts/1    00:00:00 ps
```

>When using `ps -ef`, the column CMD shows the actual process or command, which could be useful to get the command to invoke the program from terminal (needs confirmation).

The `-p` flag can be used to get the name of a program, by specifying a PID:

```bash
ps -p <PID>
```

> The command above will print the name of the process corresponding to the specified PID. \
>`ps -p <PID> -o <format>` lets the user specify the format of the output, usually seen as `ps -p <PID> -o comm=`, which prints the command name, same as the process name.

#### `pstree`

Lists all current processes in a tree layout. If a user is specified, it shows all processes rooted to such owner:

    $ pstree pierluigi

#### `top`

Shows a real-time view of Linux processes, similarly to Windows' Task Manager, with the processes that occupy more memory at the top.

#### `htop`

Enhanced version of the `top` command, it's an interactive viewer, but it's not installed by default on Mint.

#### `pidof <program_name>`

Returns the PID of the specified running program. The name a program has while running may be different from its full commercial name (*e.g.*: `subl` for SublimeText).

#### `pgrep <process_name>`

Finds the PID of the specified process (note: process name can be different than the program name).

### - Killing processes

#### `kill <PID>`

Kills the process with the specified PID. The standard `kill` command will try to finish the program in a clean manner (terminate the process), letting it close any file connections opened by it and performing a terminate routine; that is achieved by sending a "terminate" (, *i.e.* a `TERM`) signal to the specified process. When that doesn't work, use `kill -9 <PID>`. This is the deadlier version of the `kill`command, that will actually kill the process (*i.e.* sends a `KILL` signal), no questions asked: even hanging and unresponsive processes will be killed by it.

#### `pkill <program_name>`

Kills the process with the specified program name.

#### `xkill`

Kills a process through UI interaction.

#### `killall`

Kills all the running processes that match the specified name or characteristic (like user). See manual for flags and modifiers.

### `jobs`-related commands

#### `jobs`

It shows a list of processes running in the background, together with their respective *job ID* in square brackets. Job IDs are different than PIDs, and are used to manage background processes through commands like `fg` and `bg`.

Example:

    $ firefox &
    [1] 50759
    $ jobs
    [1]+  Running                 firefox &

#### `fg`

`fg` (foreground) is used to get a program running in the background to the foreground again. By deafult it brings to the foreground the last launched program, but if we have more than one, we can provide `fg` with the job ID of the target program with the following syntax: `fg %<job_ID>`, (like `fg %1`).

Example:

    $ firefox &
    [1] 50759
    $ jobs
    [1]+  Running                 firefox &
    $ fg %1
    firefox

#### `bg`

`bg` (background) has the same syntax as `fg`, but it pushes a foreground process in the background. To be able to do that, the running program has to be suspended, which is done through the keybord shell shortcut [Ctrl+Z](#ctrlz).

Example:

    $ firefox
    firefox
    ^Z
    [1]+  Stopped                 firefox
    $ bg %1
    [1]+ firefox &
    $

> To kill a process using its job ID, use the command `kill` followed by `%<jobID>` (like for `fg` and `bg`).

## - Commands for file and system management

### `cal`

Shows calendar.

### `date`

Displays the current time in the given format (check manual), or sets the system date and time.

### `shutdown`, `reboot`

They shutdown or reboot (respectively) the computer. They are part of a set of commands that can be used to suspend, shutdown or reboot the machine no matter which one is used, since they all use the same options.

`shutdown`, for example, accepts a time string in 24h clock format `"hh:mm"` to specify the time to execute the shutdown at, or in the syntax `"+m"`, referring to the specified number of minutes from now. `"now"` is an [alias](#alias) for `"+0"`, *i.e.* immediate shutdown. If no time argument is specified, `"+1"` is implied.

All commands of the group also allow to leave warning messages to users before turning off the machine.

### `history`

Lists the command history. Commands launched from terminal are stored in a hidden text file (`.bash_history`, one for each user); 3 environment variables (`$HISTSIZE`, `$HISTFILESIZE` and `$HISTFILE`) define the 3 main characteristics of the file: update frequency, maximum number of commands stored in it and location of the file.

Launching the `history` command means to print to *stdout* the contents of `.bash_history`.

The history is also accessed by using the up and down arrow keys, or by pressing [Ctrl+R](#ctrlr), which will give access to history's "search mode". In search mode the user can type to look for the most recent corresponding commands in the history file and scroll the matches by pressing R while holding down Ctrl. It is always a good idea to check what commands are usually run by a different user on a PC or a server.

An important feature of `history` is **history substitution**: by typing in the terminal `!` followed by an argument, the **!n** construct is expanded to a corresponding command stored in the command history. \
We can use *event designators* for n: they can be a *numeric argument*, a *keyword* or a *string replacement*.

    $ !1013
    $ !-2
    $ !!

> *Numeric argument:* the first !n construct executes command number 1013 in the command history (the one at the 1013th line of the text file); a negative n value will execute the command corresponding to the one executed -n commands back, so in the example the one executed 2 commands back. `!!` stands for the previous command, so it's equivalent to `!-1`.

    $ !ps
    $ !?grep

> *Keyword:* the first construct will search and execute the previous command that *starts with n*. Using the construct !?n will search for the previous command that *contains n*.

    $ ^ll^less^

> *String replacement:* it executes the previous command but replaces one command/string with another. It's syntax requires to type the two commands/strings between two caret symbols (first the one to replace and then the one to replace it with), separated by another caret symbol. It's very useful when we don't want to write twice the name of a file or to execute again a long one-liner with a substitution.

> `history`'s !n construct can also be used with *word designators* (they specify a word for the search and are often used together with event designators) or with *modifiers*. Check out `history`'s manual page for reference on those.

### `cd` and notable forms

The `cd` command allows the user to **c**hange **d**irectory:

#### `cd`

Go to home directory. Also `cd ~`.

#### `cd ..`

Go to parent directory. Also `cd ../` or, if going up multiple directories, `../` can be concatenated: `cd ../../`; `cd ../../../` and so forth.

#### `cd </path>` and `cd <path/>`

`cd </path>`: go to directory in specified **absolute path** (leading `/` for "Root"). \
`cd <path/>`: go to directory in specified **relative path** (no leading `/`); so only if the specified directory it's located in current directory.

#### `cd -`

Go back one directory (note: like for any GUI's "back" button, it doesn't necessarily means that you're going back to *parent* directory).

> **NOTE:** in the shell, the `~` symbol (alt gr + ì) represents the home directory, a double dot `..` the parent directory, a single dot `.` represents current directory and the forward slash `/` the root.

### `ls`

Lists files in current directory. Use `-a` flag to display hidden files. `la` and `ll` are important [aliases](#alias) for `ls` with specific flags appended (*list all*, *long list*; check with `alias`).

A common flag combination is `ls -lrt`: `-l` displays files in list format, while `-rt` displays them in reverse chronological order (-r is for **r**everse).

In the form `ls *` it lists directories with files.

### `pwd`

Prints the absolute path of current directory.

### `realpath`

Prints the absolute path of the specified file or directory.

### `basename`

Strips directory/path and suffix from the argument's filename.

### `sh <file_name>`

Runs specified file (if executable from shell: shell scripts, auto-installing packages...). It's the basic command to run shell (Sh, Bash) scripts.

### `./<program_name>`

Prefixing an executable file with its path will run it (with the default program for opening that kind of file). In this case, the command runs the specified program or script (if executable) in working directory.

### `chmod`

Changes access rights of the specified file(s) or directory/ies.

When seen in list form through the `ls -l` command, file and directory names are preceded by a string in the form `-rw-rw-r-x`. Dashes and letters in the string's slots are organised into 3 groups of 3 columns/slots each (excluding the first slot from the left). \
From left to right the groups are: "owner", "users of the same group as owner" and "other users not of the same group". \
The letters stand for "read", "write" and "execute/explore files" permissions, while dashes are to be interpreted as zeros or empty spaces (the corresponding user is not granted that permission).

The first slot can be a `d` if the item is a directory, a `p` if the file is a named pipe or a dash if it's a normal file.

`chmod` changes the bit corresponding to the specified type of access granted to the specified class of users.


The syntax is: `chmod <option><operator><mode> <file_name>`

Where:

- "option" is a letter in the `ugoa` group (`u` for owner **u**ser, `g` for users of the same **g**roup, `o` for **o**thers and `a` for **a**ll);
- "operator" is `+` (to give a permission), `-` (to remove a permission) or `=` (to give a permission and remove all those that are not specified);
- "mode" is `r` for "**r**ead", `w` for "**w**rite" and `x` for "e**x**ecute"/"e**x**plore". 

More than one option and mode can be given at once, by separating them with commas (no spaces). If no option is given, by default `chmod` executes as `a`.

**Use `chmod +x <file_name>` to simply make the file executable (for all users).**

Examples:

```sh
chmod +x file.deb
chmod o-wx file.py
chmod u=rwx,g=rx,o=r file.py
```

### `chown`

Change ownership of the specified file(s) or directory/ies to you (requires sudo). Useful for tranferred files.

### `find`

Searches for the specified file in the directory structure rooted at the given starting directory.

Syntax: `find <path> <options_and_patterns>`.

Examples:

```sh
find ~ -name '*.txt'

find ~ (-iname '*.jpg' -o -iname '*.jpeg')

find ~/Documents/ -iname 'markdown' -type f
find ~/Documents/ -iname 'markdown' -type d
```

As demonstrated in the examples above, `find` will require a path (can go from root to any path) as starting point and then options to restrict the search. Some of the most important options are:
* `-name`, which allows to specify a string or pattern to match in the file name. A search for a pattern or name with `find` will not work if an option able to accept a pattern is not used;
* `iname`, which is identical to `name` but is case-insensitive;
* `-o` znd `-a`, which stand for the logical operators 'OR' and 'AND'. They allow to specify alternativesor combined conditions, if used in parentheses;
* `-type`, which allows to specify the item's type (`f` for file, `d` for directory, for example);
* `-regex`, which allows to match a file name with a regular expression pattern. This is a match on the whole path, not a search.

### `sudo`

"**S**uper **u**ser **do**": executes the following command or program with super user privileges.

### `su`

Allows to run a command with **s**ubstitute **u**ser (syntax `su [options] [user [argument]]`).

Designed for unprivileged users; for the root user the same action is performed with `runuser`. If executed without arguments, it runs an interactive shell as root (`sudo su root`).

#### `su - root` and `sudo su` 

Commands to be granted root privileges.

Both `sudo su -` and `su root` switch to superuser; the former simulates a real log in, the latter doesn't. Root's username can be specified.

### `sudo passwd <username_or_root>`

Changes the password of the specified user (or root user).

### `whoami`

Returns the user name.

### `who`

Shows other users working on the system.

### `gedit`

Gedit (GNU terminal text editor) is the default text editor in Ubuntu and Ubuntu-related distros. Calling it from terminal runs the program Gedit; following the command `gedit` with a path to a text file will edit the specified file.

In Linux Mint, Gedit is pre-installed alongside Xed, Mint's default text editor and a fork of Gedit, so Xed too can be used the same way as Gedit, by invoking it with the command `xed`.

### `clear`

Clears the terminal screen. (Keyboard shortcut [Ctrl+L](#ctrll)).

### `touch`

Creates an empty file with the specified name, if it doesn't already exist. If such a file does exist, it changes the file's last access time and modification time to current time.

### `cat`

Prints on screen the contents of the specified file or folder. Also used to pass those data as input for another command through piping and input redirection (important for those commands that only take input from *stdin*).

### `hexdump`

Prints a file or stdin to stdout in ASCII, decimal, hexadecimal or octal dump. With the flag `-c` (one-byte character display) is very useful to infer the origin (UNIX or DOS) of a text file (UNIX has escape characters for newline in the form "\n", while Windows files have "\r\n").

### `dos2unix`

Utility to convert DOS metacharacters in a text file to UNIX metacharacters. Not pre-installed (`sudo apt-get install dos2unix`).

### `seq`

Prints a sequence of numbers from the first to the second specified number. Like list-returning functions in Python, it is very useful to create the list through which to iterate in a `for` loop in Bash:

```bash
for i in $(seq 1 $col); do
    #something
done
```

### `exif`

A small command-line utility to show and change EXIF information in JPEG files. Most digital cameras produce EXIF files: JPEG files with extra tags that contain information about the image. `exif` allows to read information from and write information to those EXIF files.

### `compgen`

`compgen` isn't actually a command, but a shell built-in utility. Many of Bash's built-ins don't have a manual page.

`compgen` can list all available commands (beware: it does not list commands only, but also linkers, keywords for scripting and [aliases](#alias)).

- to list all aliases: `compgen -a`
- to list all shell built-ins: `compgen -b`
- to list all commands: `compgen -c`
- to list all keywords: `compgen -k`

> Always pipe `compgen` into `more`.

> Note: shell built-ins include things like `alias`, `wait`, `continue` or `break`, but also "commands" like `echo`, `exit`, `bg` and `fg`.

### `mkdir`

Creates the directory/ies used as argument or arguments. With the `-p` flag it can create an entire directory structure and it creates directories only if they don't already exist.

Example:

```sh
mkdir -p example/{one/one_one,one/one_two,two,three}
```

In this case the brace expansion allows for the usage of multiple arguments and for creation of the directory structure, so the `example/` directory is created, alongside the structure `example/one/one_one`, `example/one/one_two`, `example/two` and `example/three`. The `example/one` folder is only created once, as intended for the wanted structure, due to the `-p` option, which is also required to make such a command work, because it instructs to create directories *as needed*.

A more complex example to demonstrate the power of brace expansions in such commands:

```sh
mkdir -p name-YYYYMMDD/{input/{reference,fastqgz,fastqgz40X,fasta,vcf,gff,nwk,Rtab,kmers,matrix,pheno},output/{genes,variants,kmers},cmd,lists}
```

`mkdir -p` also works with command substitution:

```sh
mkdir -p test-$(date +%F)/{inputs,results}	# 'test-2023-02-27/'

mkdir -p test-$(date +%Y%m%d)/{inputs,results}	# 'test-20230227/'
```

### `ln`

The `ln` command is used to create different kinds of links, using different options; the most common (and arguably the most important) of which is the *symlink*.

#### `ln -s`

Creates a symbolic link (or symlink), a special type of file which points to another file or directory, so that anything that is saved in the symlink directory, for example, is actually saved in the directory the symlink points to (and can be accessed from the symlink).

The syntax is: `ln -s <target_file> <link_name>`. If only one file is given as an argument, that argument will be the target, so `ln` creates a link to target file in the current directory, the link having the same name as the target file.

### `rm`

Removes argument files or directories. By default doesn't remove directories: use `-d` flag to remove empty directories and `-r` to recursevely remove directories and their contents.

### `rmdir`

Removes empty directory/ies. Does not work if the directory has contents.

### `cp`

Copies a source file or a file from a source path to a destination path:

```sh
cp *.txt ~/Documents/My_texts/
cp ~/Downloads/source_file ~/Documents/
```

### `mv`

Used to **move** or **rename** files.

Notable options:

- `-i` (prompt before overwriting existing files);
- `-n` (do not overwrite existing files);
- `-u` (move only if source file is newer than existing file in destination);
- `-b` (creates a backup of a file already existing in destination, with a name in the form `file.txt~`).

Usage:

- Use `mv -t <path_to_directory> <source_file(s)>` to move one or more files from the specified path to the given destination directory (for which the `-t`, **t**arget option is needed);
- Use `mv <source_files_or_dirs> <directory>` to move from source to target directory (multiple source files can be provided, separated by empty spaces: only the last argument (`<directory>`) is interpreted as destination;
- Use `mv -T <source> <destination>` to treat destination as a normal file (`-T` flag);
- Use `mv <existing_file_or_dir> <new_name>` to rename a file or directory. The new name can't be the same as another file or directory in current directory, as that would cause to move that target to a destination directory. Always provide the directory name with the trailing forward slash.


### `gzip` & `gunzip`

`gzip` compresses the argument file or *stdin* to a .gz archive. Its companion program `gunzip` de-compresses a .gz archive or lists its contents.

Examples:

```sh
gzip input_file.fastq
program1 input_file.fastq | gzip output_file.fastq.gz
```

By default `gzip` substitutes the original file (if the input is a file on the disk); if the input comes from *stdin*, it compresses to .gz while the data is in the memory, before it is written to the disk.
                     
Both commands can also output to *stdout* by using the `-c` option.

Data to be added to an existing compressed file can be appended directly, without decompressing the file first.

Example:

```sh
gzip -c input_2.fastq >> input.fastq.gz
```

> **NOTE 1:** `gzip` does not separate concatenated compressed files: to compress multiple different files into a single archive and mantain the files separated (non-concatenated), use [`tar`](#tar) instead.

> **NOTE 2:** other than gzip, there is also the bzip2 file format. It has even better compression, but its slower, thus [`bzip2`](#bzip2--bunzip2) is more commonly used for long-term data archiving.

> **NOTE 3:** many UNIX tools support working with compressed data, through equivalent *z-tools*: there are `zgrep`, `zcat`, `zless`, `zdiff`, among others. If a program cannot work with compressed files and we are working with compressed file streams, we can use `zcat` to redirect from .gz files to standard input.

### `tar`

Compresses files into a .tar archive. Use the `-c` option to compress to a new archive and the `-x` option to decompress an existing archive.

`tar` is a powerful compression tool, which allows for concatenation, appending of an archive to another archive, comparison (`diff` execution), listing, creation, deletion, extraction and update of a .tar archive ("tarball"). Check manual for usage of each option.

The most basic syntax requires the `-c` or `-x` flags, followed by the archive name first and then the file to be compressed/extracted.

The usual command is `tar -xf`. `tar` can automatically understand the compression type and thus the kind of archive, so such a command will work on .tar, .tar.gz, .tar.xf files and so on.

### `zip` & `unzip`

File compression and de-compression tool pair to produce .zip archives.

`zip` can also add files to an existing archive. Its syntax requires first the name for the archive (either a new one or an existing one) and then the file(s) to compress.

Many options are mantained identical to `gzip`, like the `-c` flag.

Use `unzip -l` to list the contents of a .zip archive.

Use the `-0` option to add files to an archive without compressing it (useful to create an archive when size it's not important, but file extension is, like for sharing files as e-mail attachments, when size would be below the limit anyway). Avoiding actual compression speeds up the process of archive creation.

Examples:

``` sh
zip -0 `ml-izs_1202.zip SRR*_R?.fastq.gz`
unzip -l ml-izs_1202.zip
```

### `bzip2` & `bunzip2`

A file compressor that uses a different algorithm which grants better compression than other archiving utilities, making it better suited for long-term data storage. `bzip2` produces archives with the .bz2 file extension.

Many flags remain identical to `gzip` (like `-c`).

`bzip2`  and  `bunzip2`  will by default not overwrite existing files (use the `-t` option to overwrite instead).

The `bzip2` package does not include just the de-compression companion program `bunzip2`, but also `bzcat`, which decompresses files to *stdout* and `bzip2recover`, which recovers data from damaged .bz2 files.

`bzip2` syntax expects a list of file names after the  command-line flags. No archive name is required, since each file is replaced by a compressed version of itself, with the name "original_name.bz2".

### `man`

Shows the manual entry for the specified command. The manual page explains what the command does, its syntax and any additional modifier and flag that can be used with it.

Using `<command_name> --help` or `<command_name> -h` prints the help page: a short version of the manual.

### `apropos`

Each manual page has a short description available within it. `apropos` searches such description sections of all manual pages matching the string/s (called "keyword") specified, for instances of the specified keyword.

The keyword searched for by `apropos` can be a command name or partial name, a command-line modifier or a Regular Expression.

The output is a list of all manual pages containing the search term in their name or description. This is often useful if one knows the action that is desired, but does not remember the exact command or page name.

`apropos` search is case insensitive.

Example:

    $ apropos 'compress'
    7z (1)               - A file archiver with high compression ratio format
    7za (1)              - A file archiver with high compression ratio format
    7zr (1)              - A file archiver with high compression ratio format
    bunzip2 (1)          - a block-sorting file compressor, v1.0.8
    bzcat (1)            - decompresses files to stdout
    
    # ...

    zip (1)              - package and compress (archive) files
    zless (1)            - file perusal filter for crt viewing of compressed...
    zlib (3)             - compression/decompression library
    zmore (1)            - file perusal filter for crt viewing of compressed...
    znew (1)             - recompress .Z files to .gz files

### The all-powerful `grep`

`grep` searches input file, *stdin* or files and directories for matches to the specified pattern.

Standard syntax: `grep <regular_expression_or_pattern> <input_file>`.

`grep` becomes increasingly more powerful with the usage of wildcards and Regular Expressions, and its behaviour changes depending on the command-line options used:

* `-v` inverts matches;
* `-n` prefixes each line of output with the line number where the match appears;
* `-i` enables case-insensitive mode (ignores upper- and lower-case distinction);
* `-c` counts the number of lines with a match instance;
* `-E` enables Extended Regular Expression support;
* `-P` enables Perl-compatible Regular Expression support;
* `-o` prints only the matching part of the matching line(s);
* `-r` reads all files under each directory, recursively.

`grep` can also enable search starting from or ending at specified line.

## System environment, hardware and OS specs

### `printenv`

Prints environment information in terminal.

### `lsb_release -a`

Prints Linux version information in terminal.

### `uname`

Prints info about the OS in terminal. With no options it only prints the OS kind (no version or distro). If one or more options are used, `uname` will give information about the pertaining topic.

Options:

- `-m`: machine architecture;
- `-n`: hostname;
- `-v`: kernel version;
- `-r`: kernel release;
- `-a`: all.

### `lshw`

List HardWare command. It prints information about the system hardware (should be run with `sudo`, so that complete information is printed).

Notable Options:

- `-short`: prints a summary in table-like format on terminal;
- `-html`: generates output as html, which can be redirected to html file.

### `lscpu`

List CPU command: it prints information about the CPU.

### `lsblk`

Prints information about storage devices ('block devices'). Use `-a` to show all block devices.

### `lsusb`

Reports information about USB controllers and all the devices that are connected to them. Use the `-v` flag (verbose) to get detailed information.

### `lspci`

Prints information about PCI devices. Use `-v` to get detailed info for each connected device and `-t` to show in tree format.

### `lsscsi`

Shows all scsi/sata devices (SATA disks and cd/dvd units). Use `-s` flag to show size too.



### `which` 

Prints the path of a shell program. Useful to find if a command is a program ([aliases](#alias) are not programs on their own, so they return no path).

Examples:

    $ which 'grep'
    usr/bin/grep

    $ which 'ls'
    usr/bin/ls

### `df -h`

Prints to terminal information about disk and partitions usage.

### `free -h`

Prints to terminal information about the RAM.

### `cat /proc/cpuinfo`

Prints to terminal the contents of the cpuinfo file, which contains information about the system' cpu.

### `export`

Sets an export attribute for shell variables. It's used to add something to a shell variable, like a path to the [`$PATH`](#path) variable.

Syntax: `export PATH=$PATH:<an_absolute_path>`.

In the example above, `export` is used to add a new path to the list of those where Linux searches for commands (the `$PATH` shell variable); this way a script or program can be run by simply typing its name in the terminal.

The example below shows another usage of `export`, put in a loop to perform a variable assignment:

```bash
for path in `cat lists/fasta-new.lst`; do
    export ID=`basename $path`;
    # ...
done
```

### `locate` and `updatedb`

`locate` reads one or more databases prepared by `updatedb` and writes to standard output a list of file names matching at least one of the patterns.

Example:

```sh
locate 'CIS_'
```

`updatedb` creates or updates a database used by `locate`. 

Using `locate` is similar to running the `find` command, but much faster if the database had already been created.

### `file`

Determines the file type of the argument(s) and it prints it to the terminal.

### `hexdump`

Returns the hexadecimal value of each character in the argument file or from standard input. Check `man` page for display options.

### `alias`

Running `alias` on a command creates an alias with the name of choice. An alias is a command name that can be run from terminal to invoke an aliased program (with flags too, if they were assigned to the alias alongside the command). An alias is not in itself a command, rather a shortcut, or a synonym for the aliased command.

An alias can be created by running the command in the following form:

    alias <chosen_command_name>="<full_command_to_be_aliased>"

Examples:

```bash
alias mkpr="mkdir -p {data/seqs,scripts,analysis}"

alias today="date +%F"
```

In the first example, an alias named `mkpr` was created, that will execute the actual `mkdir -p {data-seqs,scripts,analysis}` command, when invoked. The second example sets the `today` alias for `date +%F`, which prints the current date in yyyy/mm/dd format.

Used as above, the alias commands that were created will only last for the current session. To create a permanent alias, it must be saved in the `.bashrc` file located either in root's "etc"  directory (`/etc`) or in the user's home directory:

    $ ll ~/.bashrc
    -rw-r--r-- 1 pierluigi pierluigi 3771 Jan 24 22:23 /home/pierluigi/.bashrc

    $ ls -a /etc | grep "bashrc"
    bash.bashrc

The `.bashrc` file in the user's home (`~`) is a user's settings file, thus any setting and alias saved there will only apply and be available for that user. 

The `bash.bashrc` file located in the `/etc` directory (`/etc/bash.bashrc`), on the other hand, applies to the system, so by saving an alias there it will be available to all users. Saving an alias in `/etc/bash.bashrc` requires super user privileges.

> An alternative to permanent aliases is saving all personal commands in a directory so that it is portable and backup-able.

GNU/Linux comes with some pre-defined aliases: the very `ll` and `la` commands are in fact not programs, but aliases of the `ls` command, with specific options already appended.

`alias` also lists the known permanent aliases (that are present in `~/.bashrc` or `/etc/bash.bashrc`): typing only `alias` on the terminal provides a list of known aliases (preset and set by user), while typing `alias` followed by an alias name provides information on what that alias stands for.

### `fsck`

"File system check" (requires `sudo`).

Flags:

* `-a` automatically repairs the file system without prompting;
* `-r` repairs the file system interactively;
* `-N` does not modify checked files, it only shows what should be done;
* `-V` verbose output.

### `wget`

It downloads files from the argument URL (HTTP or FTP) specified as object. If an authentication is needed, `wget` provides the `--user=<user_name>` and the `--ask-password` options.

The strenght of `wget` is its ability to download recursively (`-r` option), meaning `wget` can follow links on the page and go deeper (or higher) in directory structure, thus the need to restrain it with options like `-A` (accept) and `-R` (reject), often with a suffix or pattern with wildcards.

Other options include:

* `-nd` ("**n**o **d**irectory"): does not save files with the original directory hierarchy;
* `-np` ("**n**o **p**arent"): doesn't move above the parent directory;
* `--limit-rate` (with a value in bytes): limits download to specified bandwidth);
* `-O` (with **o**utput file name);
* `-l` ("**l**evel"): sets the maximum depth at which `wget` will be allowed to download data.

Example:

```bash
wget --no-parent -r https://ftp-eurllmsup:9CMNHgFMKbQH3OITWT2c@bioinfoweb.izs.it/bioinfonas/download/20220512_104141531/ --no-check-certificate -nd -P 20220512_104141531/
```

### `curl`

Like `wget`, it downloads files from an URL, but it writes the file to standard output by default.

It is able to transfer data using more protocols than `wget`.

To save `curl`'s output to a file, usage of the `-O <output_filename>` option or redirection to a file are needed  (`curl <desired_URL> > output_file.gz`).

### `rsync`

A remote and local file-copying tool. It can copy files locally, from local to host or from host to local, but not between two hosts.

It can be very fast, because it only copies differences in the files if a copy already exists; because of that, to ensure `rsync` ran smoothly, it should be run again (an action that acts as a check).

It can create the directory structure specified in the destination path if it doesn't already exist.

To copy files locally the syntax is the following:

    rsync <flags> <source_path> <destination_path>

If only one path is specified, it is interpreted as destination path and `rsync` will copy all files that match from current directory to destination path:

    rsync *.txt /Documents
    
If copying from or to a remote host, a `:` separator is required: the destination or source path can be the remote host specified in the format `user@host:/path/to/directory/`, just like for [`scp`](#scp).

Example:

    rsync -avz -e ssh /local/path root@192.168.237.42:/home/user_name/path
    
As in the example above, the most common options combination is `-avz`, where `-a` enables the archive mode, which preserves file attributes like ownerhsip, permissions, modification time and links; `-v` is for verbose; `-z` enables compression of tranferred files. In addition, if connecting to a remote shell, the `-e` option will be needed, in the format `rsync -avz -e ssh`, if connecting through `ssh` (this option is not required if tranferring to or from servers).

`rsync` is sensitive for trailing forward slashes in paths:

* `~/Documents/directory` copies the directory *and* its contents;
* `~/Documents/directory/` copies the contents of the directory.

### `scp`

"**S**ecure **c**o**p**y": a different version of the [`cp`](#cp) command able to transfer single files to or from host by specifying both host and path. Syntax is almost identical to [`rsync`](#rsync)'s:

    scp cool_stuff.txt 192.168.237.42:/home/user_name/folder_for_cool_stuff/.

    scp p.castelli@gtc-collab-int:/home/IZSNT/p.castelli/20220923-Machine-Learning/ML-samples/dataframe/total_dataframe-h.tsv /home/pierluigi/Downloads/.


### `ssh`

Used to log into a server (all GNU/Linux distros usually come with the open source version of `ssh`: `openssh-client`).

Syntax: `ssh <user>@<server_name>` or `ssh <IP_address>`.

`ssh` can also be run with the `-X` option, to allow graphic programs to be executed, and it has options to run commands on the host (output can be redirected to guest too).

If the server uses a different port than the default port 22, it will be necessary to specify the port with the `-p` flag.

To avoid having to type long commands, especially if remote server's port, IP address or guest name must be specified, an SSH config file can be created in `~/.ssh/config`. In it there will be pieces of information about the host connection:

    Host <alias_name>
        <Host_name> <IP_address>
        User <user_name_for_remote_server>
        Port <port_number>

Once the config file has been created, the server can be accessed simply with `ssh <alias_name>`.

### `hostname`

Returns the host's name.

### `ssh-keygen`

Generates an SSH key pair (public key at `~/.ssh/id_rsa.pub` and private key at `~/.ssh/id_rsa`).

The public key has to be appended to `~/.ssh/authorized_keys` on the host server (in some systems it can be done automatically with `ssh-copy-id`), while the private key has to be added, in local machine, to the keys managed by the `ssh-agent` process with `ssh-add`, so that it can be used to log in into the paired server without entering the password. `ssh-agent` is usually always running by default in Unix systems; if not, `eval ssh-agent` is used to start it.
                
### `nohup`

"**No** **h**ang**up** signal".

Syntax: `nohup ./my_script.sh &`.

`nohup` catches all hangup signals sent from the terminal to the program, so that the program will keep running. It is used to make a program run on the server even after logging out of the terminal session, so that scripts that will have to perform complex calculation can be executed even if they require long times for execution and even if we disconnect from the server.

### `screen`

While connected to a server, it allows to create a virtual console from where programs and commands can run smoothly even in case of disconnection from the server. Often used to run processes that can take a while to complete. More than one screen can be created.

The following is a list of commands to interact with screens:

* `screen -S MyScreen`: create a screen named "MyScreen";
* `screen -r MyScreen`: resume the screen named "MyScreen";
* `screen -ls`: lists all available screens;
* `screen -X -S MyScreen quit`: closes the screen named "MyScreen";
* Ctrl+A+D : unattaches from current screen and gives back the actual terminal (the screen remains active).

### `time`

Prefixing a command with `time` will report the execution time when the command or program's run ends:

    $ time sh cmd-copy-fastqgz.sh
    real	0m12.716s
    user	0m0.030s
    sys     0m8.320s

### `shasum`

"**SHA**-1 check**sum**". Checksums are compressed summaries of data in hexadecimal format, used to check data integrity: they are deterministic (regardless of time or system, the hexadecimal string associated to a file will always be the same) and will only change (and will change entirely) if there is even the slightest difference in bit between two files.

`shasum` accepts standard input (*e.g.*: `echo "this is test" | shasum`) or an argument file (*e.g.*: shasum test.txt) and will calculate the SHA-1 checksum of its input file.

`shasum` can create a checksum file and validate one or more checksums against another checksum or checksum file (`-c` option).

Example:

```bash
shasum data/*fastq > fastq_checksums.sha`
shasum -c 
```

### `md5sum`

Similar to [`shasum`](#shasum): it calculates the MD5 checksum of files. MD5 checksums are older but still widely used. They are found most often as checksums on official sites to check the integrity of drivers or softwares available for download. The syntax is the same as `shasum`.

### `diff`

Validating checksums only tells if the files differ; `diff` allows to know how they differ from one another.

Syntax: `diff <file_v1> <file_v2>`.

The `-u` flag will display the `diff` output in *unified diff format*. `diff` output can be redirected to a file, creating a *patch file*: a set of instructions on how to update a plain-text file, by making the changes in the `diff` output. Updating a file thanks to a *patch* can be done automatically with the [`patch`](#patch) command.

### `patch`

Automatically operates the changes listed in a file generated by [`diff`](#diff) to a file.

### `exit`

Exits the terminal.

### The `apt` command:

### `apt`

High level Update Manager interface (`apt` stands for **Apt**itude Update Manager). Can be used in various forms to perform a specific task.

#### `apt-get`

Syntax: `sudo apt-get <mode> <additional_argument>`.

Executes Update Manager (requires `sudo`) to perform the desired task:

* `update`: updates repositories by downloading package information from all configured sources (does not apply changes to the system). Additional repositories and PPAs can be added through terminal or with Package Manager (GUI);
* `upgrade`: checks repositories for currently installed udpatable softwares and it updates all of them (includes official updates, software updates, kernel updates, system updates);
* `full-upgrade`: same as `upgrade` but it can remove packages if that is needed to upgrade the whole system;
* `install <package_name>`: installs the software with the specified name, if that software is present in the available repositories (check [`apt-cache search`](#apt-cache-search));
* `reinstall <package_name>`: removes and reinstalls argument software;
* `remove <package_name>`: removes the specified package(s) but not configuration files related to them;
* `purge <package_name>`: removes the specified package(s) and any configuration files related to them;
* `clean` -> clears out the local repository of retrieved package files.
* `autoclean` -> clears out the local repository of retrieved package files that can no longer be downloaded, and are largely useless.
* `autoremove` -> removes packages that were installed to satisfy dependencies and that are no longer needed;
* `check` -> updates the package cache and checks for broken dependencies.

#### `apt-cache search`

Lists all packages available in the repositories.

With the syntax `apt-cache search <keyword>`, it searches in the list of available packages those with the specified keyword.

#### `apt list`

`apt list` is used to show a list of programs belonging to a certain category, like `apt list --installed` or `apt list --upgradable`. 

Check `man apt` for complete list of options. 

The list prodiced is often quite long, so it may be needed to filter the output.

To check if a certain program is installed, for example, can be run either `apt list --installed | grep <program_name>` or `apt -qq list <program_name> --installed`.

#### `apt show <package_name>`

Shows information about the specified package, like type, name, dependencies and more.

> NOTE: running the Update Manager or Synaptic Package Manager (GUIs) to manage packages and installation residues can be more convenient, especially if the desire is to manage updates singularly. Synaptic is not available by default an all GNU/Linux distros: if it's not already installed, it can be installed with `sudo apt-get install synaptic`. While it may be convenient for some distros, on Mint it may serve no purpose, since Mint's Update Manager is just as good, but more intuitive and simple.

## Managing text files

### `sed`

**S**tream **ed**itor: it can edit, filter and transform text from *stdin* or from text file. `sed` reads from the input stream one line at a time, it matches text with the provided command(s) passed to `sed`, changes text accordingly to the provided command(s) and returns the modified text to standard output.

Basic syntax: `sed <options> <text_file>`.

More than one argument can be given, by using the `-e` or `-f` flag.

`sed` has 2 main important modes: substitution (`s`) and delete (`d`), plus 2 more notable modes (write `w` and transliterate `y`).

> **NOTE:** `sed` does not change the source file, it only changes standard output. To apply changes to the source file, use `-i` (`--in-place` option) to overwrite source file.

#### `s` mode

When editing text the syntax is `sed 's/pattern/replacement/' file.txt`.

Examples:

```sh
echo "change this text" | sed 's/change/Edit/'
# output: `Edit this text`
sed -i 's@.fastq.gz@@' paths_to_fastq.lst
```

In the examples above the substitution mode (`s`) is used. This replaces the first provided pattern, word or string with the second one and only changes the first instance of each line. In the latter example `-i` is used to overwrite source file.

In the former example, the forward slash is used as a separator, but almost any symbol can be used to separate the matching pattern from the replacement, like `@` in the second example, for instance.

Beware of characters that can hold a special meaning in the shell though, like `!`, which triggers [history's substitution mode](#history), or `$`, used to call variables. `@` is usually a good separator, but keep in mind that it's also used in Bash's [array](#array) syntax.

#### `d` mode

`sed` can also delete pattern-matching lines from a string or text, with the syntax `sed '/<pattern>/d'`.

#### General uses and syntax for `sed`

* To specify which instance of each line to change, use numbers after the last separator: `sed 's/change/Edit/2'`. 
* To change all instances of each line use the `g` option ("global"): `sed 's/change/Edit/g'`. 
* To change from a specified instance to the last of each line use a number followed by `g`: `sed 's/change/Edit/2g'`.
* To make the pattern-matching case-insentitive use the `i` option after the last separator: `sed 's/change/Edit/i'`.
* To restrict the `sed` command to a specific line use the number of the line before its options: `sed '3 s/change/Edit/'`, `sed '6d'`, or use numbers separated by a comma to
specify a range of lines (`sed 3,6 's/change/Edit'`, `sed '3,$d')`. 
* Use the `$` character to match the last line (example above). 
* To duplicate the modified line(s) use the `p` option ("print"): `sed 's/change/Edit/p'`; if used together with the `-n` flag (`sed -n 's/change/Edit/p'`) it only prints the lines that were modified instead. `p` and `n` can also be used without separators, when we're not doing pattern matching (*e.g.*: `sed -n '1,10p'`).
* Use the `-E` flag to enable extended regular expressions.
* To remove empty lines from a file, `sed` supports the regex `^$` only in delete mode, so the full command would be `sed '/^$/d`, without `-E`. In this case we can't use `@` as separator.

> **NOTE 1:** `sed` does not have support for REs at the same level of `grep`, so most syntaxes (like Perl-specific REs) can't be used.

> **NOTE 2:** we can use variables in place of the pattern match, replacement, or specified line in `sed` syntax. In those cases, usage of double quotes instead of single quotes is required, which also means that some special characters, like the "last line" character `$` can't be used in the same command, unless it's possible to use backslash escapes (which is not always the case, due to problems in recognition of separators).

**Also to note is that some use cases of** `sed`**, like** `s` **mode, support the ability to output the matched pattern.**
- `&` refers to the whole pattern-match, just like Perl's `${0}` or `$0` special variables or Python's `.group(0)` method plus special variable.
- `\1`, will output the first group of the matched expression,just like Perl's `$1` and Python's `.group(1)`. Matched groups from 1 to 9 can be called.

Examples:
```bash
sed -E 's/(NC_)(003210_)(251)/&/g' example_file-1.tsv
sed -E 's@(NC_)(003210_)(251)@\1@g' example_file-1.tsv
sed -E 's@(\t)([0-9])@\1v\2@g' example_file-2.tsv
```

The first example would replace the whole match with itself, since it uses `&`, while the second only outputs the first group: "NC_". The third example uses the tags for groups 1 and 2 separately, in order to insert a character between the two of them (*i.e.* a "v" between a tab and a number).

#### write `w` and transliterate `y` modes

`w` "write" mode has the same syntax as `d`, but writes the current pattern space to specified file.

`y` "transliterate" mode has the same syntax as `s`. It transliterates the characters in the pattern slot which appear in source to the corresponding character in destination: `y/<source>/<dest>/`

### `tr`

Translates/transforms all occurrences of its first argument to its second argument. Syntax:

    tr <options> <argument_1> <argument_2>

> **NOTE:** `tr` only accepts input from *stdin*.

### `awk` and `gawk`

`awk` (the original program) and `gawk` (GNU's implementation) are a language designed for processing and pattern scanning tabular data. They provide a programming-like environment used to reorganize, modify or format data in a file. `awk` processes data one record (line) at a time, and each record is separated into fields, which are the column entries for each record:

|   | field $1 | field $2 | field $3 | field $4 | field $5 |
|--------|----------|------|----------|----------|---------|
| record | sample-00001 | A | 20 | 38.13 | 21 |
| record | sample-00002 | A | 40 | 38.12 | 41 |
| record | sample-00003 | A | 60 | 38.14 | 60 |
| record | sample-00004 | A | 80 | 38.15 | 81 |
| record | sample-00005 | A | 100 | 38.12 | 101 |

`awk` assigns a variable to each field: `$<integer_number>`.

`$0` refers to the whole record (all the lines in the whole tabular file).

`awk` syntax uses the structure `<pattern> {action}` ("pattern-action pair"). The pattern is made of an expression or regular expression. If the expression evaluates to `True` or if there is a match for the RE, then `awk` runs the `{action}`. 

Multiple pattern-action pairs can be chained together by separating them with semicolons (`:`). If the pattern is omitted, `awk` will run the command on all records. If the action is omitted, `awk` will print to *stdout* all records that match the pattern.

`awk` supports string concatenation:

    awk '{print $1 "\t" $2}' input_file.tsv
    
In this example `awk` has been instructed to print the first field, a tab and the second field).

`awk` also supports arithmetics with standard operators (identical to Python). Logical operators are Bash's standards:

* `~` matches RE/pattern;
* `!~` does not match RE/pattern;
* `&&` logical AND operator;
* `||` logical OR operator;
* `!` logical NOT operator.

REs are specified by placing them between forward slashes:

    awk '$1 ~ /dog/ {print $1 "\t" $2}'

Parentheses can be used to group REs: 

    awk '!($1 ~ /dog/) {print $1 "\t" $2}'

`awk`has the 2 patterns `BEGIN` and `END`. The `BEGIN` pattern allows to specify an action to perform before reading the first record, while the `END` pattern specifies what to do after processing the last record. Syntax: 

    awk 'BEGIN{do_something}; {some_action}; END{some_action};' <input_file>`

`awk` can also process tabular data with a different separator (like .csv files) if the field sepatator is specified with `-F`. Such files can also be converted by `awk` to a file with a different separator by setting the variables RS, OFS and ORS (Record Separator, Output Field Separator and Output Record Separator, respectively) with the `-v` flag.

> If we consider `awk` as a command, it's powerful and complex, with claims of a full-fledged scripting language. But if we consider it as a scripting language, it's needlessly complicated and pretty limited. Most of the times it's simpler and more fruitful to use piping, redirection or substitution of other Bash tool to obtain the same result, and when those come short, switch directly to a little Perl script.

#### Useful `awk` one-liner tricks

* Replace comma separators with TABs: `awk -F"," -v OFS="\t" {print $1,$2,$3}`
* Extract a range of lines using the `NR` variable: `awk 'NR >= <line_number> && NR <= <line_number>' <input_file>`
* Count the number of columns/fields: `awk -F "\t" '{print NF; exit}' <input_file>`
* Convert .gtf to .bed (useful for other formats too). This conversion requires subtraction (`$4-1`): `awk '!/^#/ {print $1 "\t" $4-1 "\t" $5}' input_file.gtf`

> With `bioawk` (`awk` for biological formats) we can specify the type of file with `-c` to perform some useful actions like conversion from FASTQ to FASTA.

### `cut`

Prints selected parts of lines in a file (usually text and tabular) to standard output. It is used to separate columns in tabular files.

Since it's designed to work with tabular data, it uses TABs as default separators; the character to use as separator can be set with the option `-d<separator_character>` (d = **d**elimiter).

Use the `-f` (**f**ield) option to isolate a column from the rest (`-f1`). Ranges (`cut -f3-8`) or sets (`cut -f1,3,7`) are also supported.

Use the option `--complement` to invert selection.

It cannot reorder columns. To perform that, it's necessary to use a combination of [`paste`](#paste) and [command substitutions](#command-substitution) with `cut`:

```sh
paste <(sort dataframe/depth_cover.tsv | cut -f1) \
    <(sort dataframe/depth_cover.tsv | cut -f2) \
    <(sort dataframe/breadth_cover.tsv | cut -f2) \
    > dataframe/dc-bc.tsv
```

> In such cases a wise use of [`sort`](sort) is imperative, in order avoid breaking correspondence among strings of each column. This is also an example of how standard UNIX tools can accomplish a result without having to resort to a needlessly complicated [`awk`](#awk-and-gawk) command.

### `paste`

Merges lines of files, writing sequentially corresponding lines from each argument file, separated by TABs, to standard output. If no file is provided, reads from standard input.

Examples:

```bash
paste my_first_field.lst my_second_field.lst > dataframe.tsv

paste <(sort dataframe.tsv | cut -f1) <(sort dataframe.tsv | cut -f2) > inverted_df.tsv
```

### `column`

`column` is a tool for displaying tabular files.

Sometimes tabular data can look offset:

    $ zcat Mus_musculus.GRCm39.105.chr.gtf.gz | grep -v "^#" | cut -f 1-5 | head -n 6
    1	havana	gene	150956201	150958296
    1	havana	transcript	150956201	150958296
    1	havana	exon	150956201	150958296
    1	havana	gene	150983666	150984611
    1	havana	transcript	150983666	150984611
    1	havana	exon	150983666	150984611

This happens because the separator used in the file (TAB in this case) is always the same width, but the strings in the fields are not.

`column` formats the input into columnate lists, so that the data in the tabular file stack up well:

    $ zcat Mus_musculus.GRCm39.105.chr.gtf.gz | grep -v "^#" | cut -f 1-5 | column -t | head -n 6
    1   havana          gene             150956201  150958296
    1   havana          transcript       150956201  150958296
    1   havana          exon             150956201  150958296
    1   havana          gene             150983666  150984611
    1   havana          transcript       150983666  150984611
    1   havana          exon             150983666  150984611

Like for [`cut`](#cut), the separator can be specified.

> **NOTE:** `column` is to be used only to visualise data (making it human-readable), not to re-format the file. This is because `column` fixes the offset in tabular data display by adding blank spaces, but TAB-separated data (whatever it may look like) is preferable to data delimited by a variable number of spaces: data must be machine-readable, we shouldn't manipulate it at the expense of making it "not-machine-readable".

### Vim

**`vim` executes the text editor for terminal "Vim".**

The terminal text editor Vim is an updated version of the original Vi UNIX text editor, and is not installed by default on Debian-based systems (`sudo apt-get vim`).

Vim starts in "normal" mode, in which writing is disabled. To write text press "i" (for "**insert**") or "a" (as "**a**ppend"). To go back from writing mode to normal mode, press Esc.

Enter Vim's third mode (command-line mode) to quit, save, *etc.*: press ":" while in normal mode to enter command-line mode. This moves the cursor to the message line at the bottom, where a ":" appears. From there Vim accepts the following commands:

* `:q` -> quit (if no changes have been made);
* `:q!` -> quit and dicard changes/quit without saving;
* `:w` -> save as file_name;
* `:wq` -> save and exit;
* `:x` -> save and exit only if there's modified data.

In command-line mode there's also an additional find-replace function: `:% s/regexp/replacement/options`. This find-replace syntax is identical, in its second part, to [`sed`](#sed). The specified regular expression is searched for in each line of the document and replaced.

In normal mode is also available a search function to find words or patterns in the text file. To do that, type "/" followed by the search pattern while in normal mode to search forward, or use "?" to search backwards.

*An additional benefit of Vim is that it can provide the Regular Expression for a specific searched string.*

### `more`

Displays text files or other output in a scrollable viewer. It can't scroll back.

### `less`

A terminal text displayer like `more`, but has more functions, better scrolling management and a search function ("less is more").

| Controls:  |           |
| ---------- | --------- |
| q          | quit |
| h          | help page |
| space      | next page |
| b          | previous page |
| g          | first line |
| G          | last line |
| j          | down |
| k          | up |
| /pattern   | search down (forward) for pattern |
| ?pattern   | search up (back) for pattern |
| n          | repeat last search forward |
| N          | repeat last seach upward |

### `sort`

It sorts lines in a text file (designed to work with columns). Running without arguments will sort a file alphanumerically by line on column 1.

Notable options:

* `-t`: specify field delimiter. By default `sort` treats blank characters (TABs and spaces) as field delimiters;
* `-k`: key argument. It specifies the column by which to sort (*e.g.:* `-k2`). *Should be able to accept ranges (*`-k1,2`*).* More options can be appended to a specific column, like `V` or `r`;
* `-c`: check if a file is sorted. Used to save time if operating on a long file that would be computationally intensive to sort. If the file is sorted, `sort` has an exit status of 0 (True), otherwise it exits with 1 (False);
* `-V`: by appending to a key, it allows to sort strings that have numbers in them (alphanumerically);
* `-n`: sort the column numerically (when there are both letters and numbers). Can be either used as an argument (`sort -k1,1 -k2,2 -n test.txt`) or appended to a key (-k1,1n -k2,2 test.txt`);
* `-r`: "reverse sort" option. Can also be appended to a sorting key argument (*e.g.*: `sort -k1,1r -k2,2 test.txt` or `sort -k1,1 -k2,2 -r test.txt`).
* `-S`: allows to specify the amount of memory to use (*i.e.* to toggle the memory buffer used by `sort`) either in bytes (K, G, or M) or in percentage of memory (*e.g.*: `-S 50%` or `-S2G`;
* `-i`: by default, `sort` does not modify the input file. To redirect `sort`'s output to the input file and overwrite it use the `-i` option. (*Note: trying to redirect with* `>` *to the input file will cause to lose all its contents, just like for* `sed`).

### `uniq`

Removes consecutive duplicate lines from file or *stdin* and prints to *stdout*. Requires the file to be sorted, so it's alway used in pipelines after `sort`.

`uniq` is case-sensitive, but it can be made case-insensitive with the `-i` option.

The `-c` option prints the number of occurences of each line.

With the `-d` option, `uniq` only prints the duplicates to *stdout*.

Example:

```bash
apropos man > new_file.txt | sort | uniq | less
```

`uniq` always after `sort` and `less` or `more` always as last).

### `join`

Joins two files together by a common column. It only works if the files are sorted.

Basic syntax:

    join -1 <n_of_common_field> -2 <n_of_common_field> <file_1> <file_2>

The `-1` and `-2` arguments are integer identifiers for the 
2 files to be joined; they are followed by an integer number corresponding to the column to be used as merging point (common field).

Example:

```bash
join -1 1 -2 1 \
    <(sort dataframe/filt-samples-with-ftp.tsv) \
    <(sort confindr/confindr_report.csv | cut -d',' -f1,3 | tr ',' "\t") \
    | tr ' ' "\t" > dataframe/filt-samples-ftp-conf.tsv
```

Notable options:

* `-a`: also print unpairable lines;
* `-v`: only prints unpairable lines (like `-a` but suppresses paired lines, so it's in fact an inverse `join`).

### `wc`

"**W**ord **C**ount". By default prints newline, word and byte count for specified file.

Options allow for restriction in count, allowing for a count of lines, words or characters, for example.

`wc -w` only counts words, `wc -l` counts lines.

### `head`

Displays the first lines of a text file or *stdin* (default is 10 lines).

Using the option `-n <number>` the number of top lines to print can be specified. If `-n` is followed by `-<number>`, `head` will print lines leaving out (cutting off) a number of lines at the bottom equal to the one specified. 

It can be used together with `tail` to look at the first and last lines of a file, with the following one-liner:

```bash
head < core.vcf ; tail < core.vcf
```

### `tail`

Opposite of [`head`](#head). Displays the last lines of a file or *stdin*.

Using `tail -n <number>` will allow to specify how many of the last lines to include (default is 10) just like for `head`. If `-n` is followed by `+<number>`, `tail` will exclude (chop off) that number of lines *from the top* (*e.g.*: `tail -n +2 test.txt` will start from the 2nd line of the document, excluding the header line).

Since when writing output and error into files nothing is printed on screen, to follow the lines written to a file in real time, `tail -f` can be used (`tail -f <output_file>`). In this case, using Ctrl+C only closes the `tail` process.

## Manual installation of packages

*Softwares can be installed in different ways in GNU/Linux:*

1) *by retrieving them from repositories (through terminal or package manager);*
2) *from software manager;*
3) *from installation packages.*

*In the latter case, dependencies have to be taken care of first-hand:*

- *first the package with the collected files has to be unpacked;*
- *read the Readme file, which contains information about dependencies that must be installed manually before the actual installation process. The Readme file should also contain a guide on where and how to retrieve those dependencies;*
- *check if all dependencies are satisfied by executing the command* `./configure`*, while being in the directory where the installation package has been unpacked. If the configure file is missing, it can be fixed with the* `./autogen` *command, which will also return information about the expected location of the package.*
*The installation path can be changed by adding to the command* `./configure` *the line* `--prefix =/usr/lib`*, where* `/usr/lib` *is the expected installation location;*
- *If all dependencies are satisfied, the command* [`sudo make install`](#sudo-make-install-package_name) *can finally be executed. That will install the actual package. To uninstall such a package, use the* `sudo make uninstall` *command.*

*Since* `.deb` *packages now have a really simple way to be installed thanks to the* [`gdebi`](#gdebi) *command, available also from UI, installation with* `make` *is now to be used only for* `tar.gz` *packages.*

### `sudo dpkg -i </path_to_.deb_file>`

Extracts and installs the `.deb` package at the argument path (requires `sudo`).

### `sudo make install <package_name>`

The `install` program copies files (often just compiled) into destination locations of choice. It is used (always in the form provided, together with the `make` command, to install manually program packages from source (tar.gz packages).

Usually the process of installation from source is the following:

1) run the configure program provided in the package with the `./configure` command;
2) run the `make` command;
3) run the `make install` command.

Additionally, if after installation the program can't be run, it may be necessary to run `sudo ldconfig`, which creates the necessary links and cache to the most recent shared libraries, basically telling the system that a new package is there and that's ok to use it.

If some error with the `make` command occurs, the build-essential package may not be present, so the first attempt to solve the problem would be running `sudo apt-get install build-essential`.

### `gdebi`

Simple tool to install .deb packages from local. It automatically installs and resolves the package dependencies.

It's always worth to try install a .deb package with `gdebi` before going through the hassle of installing it with [`make install`](#sudo-make-install-package_name), since some softwares are distributed without any `configure` or `autogen` file nor any information on how to retrieve the package dependencies (a very bad practice from some who bild installation packages from source).





more on: <https://www.ubuntubeginner.com/basic-ubuntu-commands-for-beginners/>
         <https://www.linux.org/pages/download/>


(from LEGO links)

<https://tldp.org/LDP/Linux-Filesystem-Hierarchy/html/the-root-directory.html>
<https://tldp.org/LDP/Linux-Filesystem-Hierarchy/html/the-root-directory.html>
<https://en.wikipedia.org/wiki/Chmod#Numerical_permissions is easier>
<https://www.openvim.com/>
<http://manpages.ubuntu.com/manpages/focal/man1/touch.1.html>
<https://linuxconfig.org/bash-scripting-tutorial-for-beginners>

Bash Guide for Beginners
<https://tldp.org/LDP/Bash-Beginners-Guide/html/index.html>

Advanced Bash-Scripting Guide
<https://tldp.org/LDP/abs/html/index.html>

GNU/Linux Command-Line Tools Summary
<https://tldp.org/LDP/GNU-Linux-Tools-Summary/html/index.html>

<https://hackr.io/tutorials/learn-awk?sort=upvotes&type_tags%5B%5D=1>
(here you can choose free tutorials at different knowledge levels)

<https://linuxtechlab.com/bash-scripting-learn-use-regex-basics/>

(R language)
https://f1000research.com/articles/5-1492




