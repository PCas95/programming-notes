# Patterns, regular expressions and their syntax:

*Patterns and regular expressions (REs) are very powerful tools to combine with such commands as* `grep`*. Regular expressions are usually written in quotes in order not to make the shell interpret certain characters as other objects or operators. Writing in quotes is also a good habit to have when referring to an object, so that if that object has a space in its name, or its name starts with a character that can also be an operator, the shell won't "misinterpret" our command. Some REs are greedy: it means they will try to match the highest number of items allowed by the specified pattern (more specific in each character analysis further down).*

## `\`

In the shell, the backslash symbol is used to continue the command on the next line and is simply used to increase readability. In Regular Expressions however, (as in many programming languages like in Perl and Python), `\` is an "escape character", used when we need to include a metacharacter is our search (like space ' ', '\' itself, '$', or punctuation like '.', for example).

Example:

`cat Linux\ commands`

In this case, we wanted to escape the metacharacter "space", since that is used by the shell to separate different arguments. In a case like the example, where we have a file name that contains a metacharacter, we either put the argument's name in quotes ('Linux commands'), which is the best practice for these situations, or we escape the metacharacter using `\`, which is often done in regular expressions. `\` can be followed by one or more character in "special sequences" derived from Perl that will match specific character classes; these sequences are often equivalent to one or more classes defined with `[]`, and they can be used in classes themselves.
List of some useful special sequences and expanded classes:

- `\w` matches any alphanumeric character (equivalent to `[a-zA-Z0-9_]`)
- `\W` matches any non-alphanumeric character (equivalent to `[^a-zA-Z0-9_]`)
- `\d` matches any decimal digit (equivalent to `[0-9]`)
- `\D` matches any non-digit character (equivalent to `[^0-9]`)
- `\s` matches any whitespace character (equivalent to `[ \t\n\r\f\v]`)
- `\S` matches any non-whitespace character (equivalent to `[^ \t\n\r\f\v]`)
- `\t` matches tab
- `\n` matches newline
- `\r` matches
- `\f` matches
- `\v` matches


Ex.: the following ERs are equivalent, but the latter requires less typing and is shorter.
20[0-9][0-9]\.TE\.[0-9]+\.[0-9]+\.[0-9]+
2\d+\.\w+\.\d+\.\d+\.\d+

## `[]`

We can use square brackets to set up patterns, (as in `grep "[ATCG]"`). They are used to match any one of the characters between them. The square brackets are also said to be "class-specifying" metacharacters, because they set up a class: a set of characters you want to match. A range of characters can be specified with the use of a dash (`-`).
Examples: 

    [A-Z]
    [0-9]
    [0-5][0-9] (will match all the two-digits numbers from 00 to 59)
    [0-9A-Fa-f] (will match any hexadecimal digit). 

> Note that usually any metacharacter inside `[]` is stripped of its special meaning (sometimes unless it is in a specific position).

## `^`

The caret symbol has two meanings: in regular expressions it anchors the *following* character or word (a pattern in general) to the start of a line. (*It matches the start of the string*).

### `[^]`

When it's the first character while in square brackets, the caret symbol assumes its second meaning: it matches everything that's not what follows it, (as in `grep "[^ATCG]"`).
> Note that it has no special meaning anywhere else while between `[]` and thus the class would simply contain the caret character to match.

## `$`

Like the caret symbol, when used in regular expressions `$` anchors the *preceding* expression to the end of a line (*it matches the end of a string*).

> Note: `^` and `$` can be used in combination to match only the empty string(s): `^$`, thus matching all blank lines in a file. `^` and `$` can also be used to match the entire string, forcing the pattern in the middle to be matched only if it's the only thing there is in the string (`^something$`).

## `.`

Matches any one character (except for the newline character).

## `*`

In regular expressions, `*` has a different meaning than its wildcard use in Bash: it matches zero or more times *the preceding character* (or class).

## `?`

Matches once or zero times the preceding character (or class). Appending `?` at the end of a regex will usually make it non-greedy, so it's often used with this purpose.

Example: `[ -]?` will match once ore zero times the class `[ -]`. Without `?`, `[ -]` would match one character that is either a space or a dash, but adding `?` will allow to match even if there isn't a space or dash (0 occurrences), so the regex is able to match less characters than its `[ -]` counterpart (*non-greedy*).

## `+`

Matches one or more times the preceding character (or class).

## `()`

Parentheses are used to group regular expressions. Grouping has 2 main effects:

1) the grouped expression is now considered one item, which also means that quantifier metacharacters, like `{n}`, `+` `?` or `*` will apply their effects on the whole grouped expression;
2) the match of the grouped expressions can now be retrieved with special variables.

In regard to point 2, a set of Perl-derived variables are available in programming languages such as Python and Perl: what is matched by the first occurrence of a grouped expression in the regex is automatically stored into the variable `$1`; subsequent matches for other groups in the same regex are consequently stored in `$2`, `$3` etc.
The following time a regex with a group matches something, the match will replace what was previously stored in the corresponding variables.

`()` can also include `?` as the first character, providing the "extension notation" `(?x)` (where "x" is a following pattern. A "?" inside parentheses has no special meaning otherwise).

Below are a few useful extension notations:

- `(?=x)`

Positive lookahead assertion: it matches if "x" matches next.

Example: `Isaac (?=Asimov)` will match "Isaac " only if it’s followed by "Asimov".

- `(?!x)`

Negative lookahead assertion: it matches if x doesn’t match next.

Example: `Isaac (?!Asimov)` will match "Isaac " only if it’s not followed by "Asimov".

- `(?<=x)`

Positive lookbehind assertion: it matches if the current position in the string is preceded by a match for "x" that ends at the current position. 

Example: `(?<=abc)def` will match "abcdef", since the lookbehind will back up 3 characters and check if the contained pattern matches. The contained pattern must only match strings of fixed length, (`abc` or `a|b` are allowed, but `a*` and `a{3,4}` are not).

- `(?<!x)`

Negative lookbehind assertion: it matches if the current position in the string is not preceded by a match for "x". It has the same restriction as its positive counterpart.

## `|`

The pipe works like an "OR" logical operator: it matches one of the words or regular expressions being processed. The pipe is never greedy: in `x|y` (where "x" and "y" are strings or patterns) the items are tested from left to right; if "x" matches completely, the "y" branch is not tested at all. More than 2 options may also be given (`a|b|c|x|y|z`).

The pipeline also allows for the lack of a second option (`x|`). This case implies the option of *not matching*; for example, `foot(clan|)` would match both "footclan" or just "foot".

## `{n}`

A number delimited by curly braces acts as a quantifier metacharacter, matching exactly n times the preceding item. The quantifier metacharacter will only apply for the preceding character, unless the preceding item is a class or a grouped expression.

There are also other ways to quantify the number of characters or patterns to match with `{}`: curly braces can also be used to define a minimum and a maximum number of allowed characters/patterns. In these cases a comma `,` is used to separate the number n of minimum allowed characters and the number m of maximum allowed characters.

- `{n,}` matches n or more times the preceding item.

- `{,m}` matches up to m times the preceding item.

- `{n,m}` matches from n up to m the preceding item. **Note:** this is a greedy quantifier, meaning that it will attempt to match the highest number of items allowed.

- `{n,m}?` matches from n up to m the preceding items. It is the non-greedy version of the `{n,m}` quantifier, meaning it will attempt to match the lowest possible number of repetitions.



Ex.: two more ERs, the first is greedy, the latter is non-greedy. Usually appending `?` at the end of the ER will make it non-greedy.
`.*`
`.*?`






* from `grep` manual for extended RE:

The symbols \< and \> respectively match the empty string  at
       the  beginning  and end of a word.  The symbol \b matches the
       empty string at the edge of a word, and \B matches the  empty
       string  provided  it's not at the edge of a word.