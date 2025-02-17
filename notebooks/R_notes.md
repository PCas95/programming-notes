# R scripting notes

*Each R script begins with the header* `#!/usr/bin/env Rscript`

## Basic R syntax

- Variables
  ```R
  var <- 42
  ```
- Comments
  ```R
  # This is a comment
  ```
- Keywords
  `if`, `else`...

- Errors
- Warnings
- Messages

### Basic operations

The kinds of operations that can be done in R fall in one of the following categories:
    - Arithmetic (*e.g.* `2 + 5`) 
    - Relational (*e.g.* `2 < 5`)
    - Logical TRUE (*e.g.* `TRUE | FALSE`, `TRUE & FALSE`...)
    - Commands (*e.g.* `getwd()` and other functions)

### Working directory

```R
> getwd()
[1] "/home/IZSNT/p.castelli"
> setwd("/home/IZSNT/p.castelli/Documents/R_course_20250217")
> getwd()
[1] "/home/IZSNT/p.castelli/Documents/R_course_20250217"
```

### Objects and data types

- Objects: "Everything that exists in R is an object";
- Values: "Everything that happens in R is a funciton call";
- Classes: (numeric, integer, character, logical, function, missing value) each type of object (vectors, dataframes *etc* can store values of one or more classes. Some have limitations regarding the type of classes can be stored in that object.

> Use `mode()` to show the class of an object.

#### Naming objects

- DOs
    - lowercase and uppercase
    - underscore
    - numbers 

- DON'Ts
    - non-alphanumeric characters in general, especially not `-` and spaces
    - reserved words (keywords and functions)
    - start with a number

## Packages

### `install.packages()`

R comes with more than 6000 additional packages; to use them, they must be downloaded from a repository (like CRAN) and installed (in a tmp directory) using the `install.packages()` command. The argument between parentheses is the name of the package (between double quotes).

Some important packages:
- `ggplot2`
- `ggpmisc`
- `ggtree`
- `plyr`
- `dplyr`

## Help and documentation

### `help()`

Gives access to R's built-in documentation about the function or object typed between parentheses. There's also the `?` syntactic shortcut (ex.: `?round`, `?ggplot`). Operators must be between single quotes.

### `help.search()`

R help system's search fuction, useful when we don't know the actual name of the function. It requires the argument to be in double quotes and, like `help()`, has a sytactic shortcut (`??`).

### `apropos()`

Finds functions by name (ex.: `apropos(norm)`).

### `library()`

Lists all functions in a package (ex.: `library(help="base")`).


## Calculations and mathematic functions

| calculation | operator |
|-------------------|-------|
| addition          |  `+`  |
| subtraction       |  `-`  |
| multiplication    |  `*`  |
| division          |  `/`  |
| square root       |  `sqrt(x)`    |
| exponentiation    |  `exp(x)` |
| factorial         |  `factorial(x)`   |
| absolute value    |  `abs(x)` |
| logarithms        |  `log(x, base=exp(1))`, `log10()`, `log2()`   |
| trigonometric f.  |  `sin(x)`, `cos(x)`, `tan(x)` |
| binomial coeff.   |  `choose(n, k)`   |


## Significant digits

R prints by default 7 significant digits, but we can change that with the `print()` function: `print(sqrt(3.5), digits=3)`.

### `round()`

Rounds up the argument number. By default it keeps 0 decimal places, since `round()`'s second argument (`digits`) has a default value of 0. Ex.: `round(sqrt(3.5), digits=3)` or `round(sqrt(3.5), 3)`.

## Variables

R doesn't have scalar variables: only Vectors and

### `<-`

Assignment operator also (`=`).

### `ls()`

Lists objects created in the global environment.

### `search()`

Returns where R looks when searching for the value of a variable.

### `str()`

Compactly display the internal **str**ucture of an R object, like a Vector. It's a diagnostic function.

## Vectors

Vectors are containers of contiguous data. R, contrary to other programming languages, doesn't have a type of single value (often called *scalar* values), rather it has vector values, and single data are stored in vectors of length 1. Unlike Python's lists, R's vectors must contain values of the same data type. Vectors can belong to any of the following types:

- **Numeric** (R imports numbers as numeric values by default. This vector type encompasses integers, floats and negative numbers. Numeric vectors are also called "double vectors");
- **Integer** (number values can be treated as integers by appending an `L` to them, if they are integers);
- **Character** (strings);
- **Logical** (the 2 Boolean values TRUE and FALSE).

So a vector is an ordered set of entries, usually defined with the function [`c()`](#c). Vectors can be concatenated to each other.

> Vectors can also be crated using other functions like, `rep()` and `seq()`.

### `c()`

Returns a vector of the values used as arguments.

### `rownames()`
### `colnames()`

# Recycling

A feature of vectors is *recycling*: if operations are being made with vectors of different lengths, R will reiterate them for each element in the vector. This allows, for example, to add a single value to all the elements of a longer vector. Since this is an intentional behavior, R will warn us only if the longer vector's length is not a multiple of the shorter vector's length. Many of R's mathematical functions and all of its operators are vectorized (able to deal with vectors, thanks to recycling). 

# Type coercion
Since all elements in a vector must have homogenous data type, R silently "coerces" elements so that they all have the same type, doing so in the way that causes the least amount of information loss (ex.: if a vector would contain numbers and booleans, `TRUE` and `FALSE` are converted to 1 and 0; if it would contain numbers and integers, integers are transformed into numbers).

# Indices
Just like lists in Python, we can retrieve single values from a vector through indexing: `my_vector[4]`, but in contrast to Python, indexing starts from 1 rather than 0.
By combining assignment with indexing we can change specific values of a vector (ex.: `my_vector[4] <- 42`). The values in a vector can have names, which are set and accessed with `c()` and `names()`, and can be accessed just like with indexing through their name (ex.: my_vector['bulba']). Even indexing is vectorized, so we can access multiple values in a vector (*subsetting*): `my_vector[c(2, 3)]`, or slice contiguous elements in a vector (identical to Python's slices): `my_vector[2:5]`.
Negative indexes are used in R to exclude specific elements or slices (but in the latter case the syntax requires the `-` to be outside a parentheses grouping: `my_vector[-(3:6)]`).
Thanks to indexing's vector nature we can also repeat specific values in vectors.

# Logical vectors
Comparison operators are also vectorized, so they can be used on vectors to generate logical vectors whose values are the Boolean values calculated for the comparison of each value of the vector.

# Vectors and comparison operators
We can subset a vector using comparison operators (ex.: `my_vector[my_vector > 2]`) or  vectorized logical operations (using AND `&`, OR `|` and NOT `!` operators).

# Rearranging elements
There's more than one way to change the items order in a vector:
Rearranging with the combine function: `my_vector[c(2, 3, 1)]`;
Reverse the elements of a vector with slices: `my_variable[5:1]`;
Using the `order()` function to reorder the vector's value. 


`length()`
    returns the length of a vector.

`c()`
    the "combine" function allows to create a long vector by listing the items to be stored in the variable separated by a comma and a space.
    Ex.: `> new_variable <- c(1.2, 56.3, 7.5, 892.34)`.
    Combine is also used to set names for the vector: `c(bulba=1, char=2, squir=3)`.

`seq(n, m)`
    the `seq()` function is used to create vectors of integer sequences. The sequence will contain all integer numbers from n to m (included). A syntactic shortcut is `n:m`, where
    n and m are integers.

`names()`
    used to access (`names(my_vector)`) or set a vector's names when combined with `c()` (`names(my_vector) <- c("venus", "chariz", "blast")`).

`order()`
    returns a vector of indexes representing the ascending order of the argument vector's items (`order(vector)`) or the descending order of those items 
    (`order(my_vector, decreasing=TRUE)`); is used to reorder the vector's value in ascending (`my_vector[order(my_vector)]`) or descending 
    (`my vector[order(my_vector, decreasing=TRUE)]`) order.

## Logical Operators

| Operator | Meaning                  |
| -------- | ------------------------ |
| >        | greater than             |
| <        | less than                |
| >=       | greater than or equal to |
| <=       | less than or equal to    |
| ==       | equal to                 |
| !        | not equal to             |
| &        | logical AND              |
| |        | logical OR               |
| !        | logical NOT              |
| &&       | logical AND              |
| ||       | logical OR               |

## Special values

    `NA`              "Not Assigned". Represents missing data. Can be handled with `na.exclude()`, or you can check which elements are `NA` with `is.na()`.

    `NULL`            Just like Python's `None` data type. You can test if a value is `NULL` with `is.null()`.

    `-Inf, Inf`       negative and positive infinite values. Values can be tested to check if they're finite with `is.finite()` and `is.infinite()`.

    `NaN`             "Not a Number". Can occur in computations that don't return a number. You can check if a value is `NaN` with `is.nan()`.


# Factors
Factors are an additional type of vector that store data about categories (in bioinformatics those could be chromosomes or samples). Factors can be created from vectors with the `factor()` function. Typing the name of the object (variable) containing the factor will list all the elements of the original sequence (vector) plus its `levels`.



# Calculation of p-value
## (syntax of some important statistical models)

### Kruskal-Wallis Rank Sum test

- Function:
```R
kruskal.test(list(short_brcov$dairy, short_brcov$fruit))
```

- Output of interest:
```R
chi-squared = 0.58193, df = 1, p-value = 0.4456
```

### Wilcoxon test

- Function:
```R
wilcox.test(short_brcov$dairy, short_brcov$fruit, paired = FALSE)
```

- Output of interest:
```R
W = 23602, p-value = 0.4458
```

### Kolmogorov-Smirnov test

- Function:
```R
ks.test(short_brcov$dairy, short_brcov$fruit)
```

- Output of interest:
```R
D = 0.098157, p-value = 0.2409
```








