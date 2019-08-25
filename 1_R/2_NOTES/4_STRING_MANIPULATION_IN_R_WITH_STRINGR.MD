## KEY LEARNINGS

### String Manipulation in R with stringr

- [`str_c()`](https://www.rdocumentation.org/packages/stringr/topics/str_c)
- [`str_length()`](https://www.rdocumentation.org/packages/stringr/topics/str_length)
- [`str_sub()`](https://www.rdocumentation.org/packages/stringr/topics/str_sub)
- [`str_detect()`](https://www.rdocumentation.org/packages/stringr/topics/str_detect)
- [`str_subset()`](https://www.rdocumentation.org/packages/stringr/topics/str_subset)
- [`str_count()`](https://www.rdocumentation.org/packages/stringr/topics/str_count)
- [`str_split()`](https://www.rdocumentation.org/packages/stringr/topics/str_split)
- [`str_replace()`](https://www.rdocumentation.org/packages/stringr/topics/str_replace)

**Quotes**

There are cases where you need both `'` and `"` inside the string. In this case, fall back to the first guideline and use `"` to define the string, but you'll have to escape any double quotes inside the string using a backslash (i.e. `\"`).

`wroteLines()`

You can pass [`writeLines()`](https://www.rdocumentation.org/packages/base/topics/writeLines) a vector of strings and it will print them to the screen, each on a new line. This is a great way to check the string you entered really does represent the string you wanted.

Should display: To have a \ you need \\
`writeLines("To have a \\ you need \\\\")`
To have a \ you need \\

`writeLines("This is a really \nreally really \nlong string")`

This is a really

really really

long string

`paste(c("Here", "There", "Everywhere"), "a")`

"Here a" "There a" "Everywhere a"

`paste(c("Here", "There", "Everywhere"), "a", collapse = ", ")`

"Here a, There a, Everywhere a"



### stringr

`str_c()` the `c` is short for **c**oncatenate, a function that works like [`paste()`](https://www.rdocumentation.org/packages/base/topics/paste). It takes vectors of strings as input along with `sep` and `collapse` arguments. `str_c()` propagates missing values. 

`str_length()`handles factors in an intuitive way, whereas `nchar()` will just return an error.

`str_sub(string, start = 1L, end = -1L) ` You might have seen `substr()` a base R function that is similar to `str_sub()`. The big advantage of `str_sub()` is the ability to use negative indexes to count from the end of a string.

### hunting for matches

+ all take a pattern argument

  + `str_detect(string, pattern, negate = FALSE)` Does the string contain the pattern? 

    + equivalent to `grepl(pattern, x)`

  + `str_subset(string, pattern, negate = FALSE)`

    +  Find girl_names that contain "U"
      starts_U <- str_subset(girl_names, fixed("U"))

       Find girl_names that contain "U" and "z"
      `str_subset(starts_U, fixed("z"))`

  + `str_count(string, pattern = "")` "How many times does the pattern occur in each string?"

    

    *fixed() function under the argument pattern is very important if string  contains punctuation 

### Splitting strings

  `str_split()`  pull apart raw string data into more useful variables.

* if specifying n = 2 argument it will split only first  occurrence of the pattern
* Remember, if the `simplify` argument is `FALSE` (the default) you'll get back a list of the same length as that of the input vector. More commonly, you'll want to pull out the first piece (or second piece etc.) from every element, which is easier if you specify `simplify = TRUE` and get a matrix as output. You'll explore both of these output types in this exercise.

* str_split(string = "Tom & Jerry", pattern = " & ")

* [[1]]

   [1] "Tom" "Jerry"

### Replacing matches in strings

`str_replace(string, pattern, replacement)`

​	`str_replace_na()` to turn missing values into "NA"  



### Regular expressions

Language for describing patterns.
example   **^.[\d]+**    "the start of the string, followed by any single character, followed by one or more digits"

**rebus**

START %R%

​	ANY_CHAR %R%

​	one_or_more(DGT)

**VS**

**Regular expression**

 *^.[\d]+

If we wanna check it out in HTML viewer use `str_view()`  - helpful for testing your patterns



**REBUS**

`rebus` provides [`START`](https://www.rdocumentation.org/packages/rebus.base/topics/Anchors) and `END` shortcuts to specify regular expressions that match the start and end of the string. These are also known as *anchors*. You can try it out just by typing

The special operator provided by `rebus`, `%R%` allows you to compose complicated regular expressions from simple pieces. When you are reading `rebus` code, think of `%R%` as "then". For example, you could combine `START` with `c`,

**START** - match the start of the string

**END** - match the end of the string

**ANY_CHAR - matching any character** 



