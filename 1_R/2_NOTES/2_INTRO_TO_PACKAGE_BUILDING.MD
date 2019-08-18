## Key learnings

**Why?**

+ share code across your business 

+ structure way to develop for yourself

**R package can include:**

+ functions
+ data
+ documentation
+ vignettes
+ tests

**Minimum:**

+ R directory - R code
+ man directory - all documentation
+ NAMESPACE file 
+ DESCRIPTION file - metadata

**Packages for easily creating packages:**

+ devtools - help to stick to right package structure
+ roxygen2 - description and namespace

Setting Up an R Package:

**Devtools:**

+ create() - create basic package structure
+ document() - 
+ check()
+ build()
+ test()



Use the dump() function to write the numeric_summary function

dump("numeric_summary", file = "datasummary/R/numeric_summary.R")



**DESCRIPTION file**

- General package information
- Author and maintainer details `person()`
- Package dependencies
- License



**NAMESPACE file**

The NAMESPACE file controls:

- Functions or packages imported for use by our package
- Functions exported by our package

**We do NOT edit by hand!**



**Optional Directories**

We can also include:

- Data
- Vignettes
- Tests
- Compiled code
- Translations
- Demos (being replaced by vignettes)

**Data**

```r
 sim_dat <- data.frame(
     ID = 1:10,
     Value = sample(1:11, 10),
     Apples = sample(c(TRUE, FALSE), 10, replace = TRUE)
 )
```

```R
use_data(sim_dat, pkg = "simutils")
```

```R
use_vignette("my_first_vignette", pkg = "simutils")
```

**Best Practice for Structuring Code**

Guidelines for the R directory:

- No subdirectories

- Don't have everything in one script

- Don't have a large number of small files

- Group similar functions together

  

```R
use_data(weather, pkg = "datasummary", overwrite = TRUE)
```

+ Nice! In this exercise, we explicitly set the `overwrite`argument to `TRUE` to ensure that running the code multiple times won't result in an error.



**roxygen2**

**Roxygen headers**

```R
#' Title goes here
#'
#' Description goes here
#'
#' Details go here
#'
#' @param - identify each of the function arguments + help text that a user will see in the help documentation
#'
#' @import dplyr - by doing this you make package namespace and functions available to #' your package
#'
#' @importFrom
```



**Exported Functions**

Exported functions:

- visible to the end user
- key package functionality

Non-exported functions:

- not visible to end user
- utility functions

 Non Exported Functions example 

```r
#' Count NAs in a vector
#'
#' @param x A vector
#'
#' @return Number of NAs in x
#'
#' @examples
#' sumNa(airquality$Ozone)
sum_na <- function(x) {
  sum(is.na(x))
}
```



Calling non-exported function with **package:::function()**

```
simutils:::sum_na(airquality$Ozone)
```



Exporting a function makes it available for the end users of your package. Functions that are not exported are not directly available to your end users. It's a really useful way to hide low level functions that you write to break up the main functionality.

**Exporting functions**

Although the `NAMESPACE` file is used to identify functions that are exported you use `roxygen`tags to create this file. This makes it really easy to see, right next to the function, if your users will be able to work with it or not.

We mark a function for exporting using the tag `@export`.



**Minimum Level of Documentation**

**For each function, document:**

- Title
- Description
- Arguments
- Exported (for exported functions only)


  **Creating man Files**

```r
document("simutils")
```

**Package  level documentation**

#' datasummary: Custom Data Summaries
#'
#' Easily generate custom data frame summaries
#'
#' @docType package
#' @name datasummary
"_PACKAGE"



**Data level documentation**

With a data set, once again, there is no function object to document. You instead give the name of the dataset as a character string, for instance `"airquality"`. Additionally there are two tags that you need to use:

- `@format` to describe the format of the data. This is often used with the describe format.
- `@source` to identify where the data came from.

Example:

#' Random Weather Data
#'
#' A dataset containing randomly generated weather data.
#'
#' @format A data frame of 7 rows and 3 columns
#' \describe{
#'  \item{Day}{Numeric values giving day of the week, 1 = Monday, 7 = Sunday}
#'  \item{Temp}{Numeric values giving temperature in degrees Celsius}
#'  \item{Weather}{Character values describing the weather on that day}
#' }
#' @source Randomly generated data
"weather"

**Creating man files**

document("package")



**What Should You Check?**

An R package check includes:

- If the package can be installed
- Description information is correct
- Dependencies
- Code syntax errors
- Documentation is complete
- Tests run
- Vignettes build



When you run `check()`, if the argument `cran` is set to TRUE, you run exactly the same checks which are run when you submit a package to CRAN. As the `cran` argument has a default value of TRUE, you do not need to do change it unless you do not wish to run all of the CRAN checks.



**Undefined global variables**

```
utils::globalVariables(c("dataset_name", "col_name_1", "col_name_2"))
```

This defines `dataset_name`, `col_name_1`, and `col_name_2` as global variables, and now you shouldn't get the undefined global variables error.



**Differences in Package Dependencies**

**Adding a Dependency**

```r
use_package("dplyr")  ## adds to imports
```

```R
use_package("ggplot2", "suggests") ## adds to suggests
```

The `Depends` and `Imports` fields in the DESCRIPTION file can cause a lot of confusion to those new to package building! Both of these fields contain package dependencies which are installed when you install the package. However, the difference between them is that the packages listed in depends are attached when the package is loaded, whereas the packages listed in imports are not.



Therefore, to make sure you are using the correct function, it is best practice to use `imports` to list your dependencies and then in your code explicitly name the package and function it's from in the form `package::function()`, e.g. `dplyr::select()`.

In the majority of cases, you should only list the version of R required in the `Depends` field and the package in the `Imports` field.



**Building Packages with Continuous Integration**



Building with devtools

Build the source:

```r
build("simutils")
```

Build the binary:

```r
build("simutils", binary = TRUE)
```



**What is Continuous Integration?**

- Automatically runs checks when code changed

- Used with version control

- Runs every time you make an update

  

Adding Continuous Integration for a Package

```r
use_travis("simutils")
```



**Building an R package**

Once you have created the correct structure, included all of your functions, created the package documentation and ensured that the checks pass you can then build your package so that it is in a shareable format.

You can build your package using the [`build()`](https://www.rdocumentation.org/packages/devtools/topics/build) function from `devtools`. You can use this function to build either a source version of the package, or a Windows/Mac specific binary version. The source version of the package will have a file ending of ".tar.gz" and the binary will take the ending ".zip" (Windows) or ".tgz" (Mac). You can only build binaries for the current platform that you're using. The `build()` function builds the source version of the package by default



You can run `use_github()` and `use_travis()` to set up your package for use with GitHub and Travis CI. GitHub is a popular website used for storing code and version control, and Travis CI is used for continuous integration,



**What are unit tests and why write them?**

Why Write Unit Tests?

A function that works correctly now may not behave as expected in the future if:

- Supporting or connected code could be added or modified
- A later version of R and/or later versions of packages are used
- The code is run on new data
- The code is run on a different operating system


  **Setting up the test structure**

- Call `use_testthat` to set up the test framework
- This creates a `test` directory in the package root directory
- Within the `test` directory, there is a script `testthat.R` which contains code to run the tests
- Within the `test` directory is a directory `testthat` where you save all of your test scripts



**Writing an individual test**

Some of the most common expect statements:

- `expect_identical` - Checks for exact equality
- `expect_equal` - Checks for equality with numerical tolerance
- `expect_equivalent` - More relaxed version of equals
- `expect_error` - Checks that an expression throws an error
- `expect_warning` - Checks that an expression gives a warning
- `expect_output` - Checks that output matches a specified value



**expect_identical**

- Strictest numerical comparison
- Compares **values**, **attributes**, and **type**

Passes

```R
library(testthat)

my_vector <- c("First" = 1, "Second" = 2)

expect_identical(my_vector, c("First" = 1, "Second" = 2))
```



**expect_equal**

- Compares **values** and **attributes**
- Doesn't compare **type**

Passes

```r
expect_equal(my_vector, c("First" = 1L, "Second" = 2L))
```



**expect_equivalent**

- Least strict numerical comparison
- Compares **values** only
- Doesn't compare **attributes** or **type**

Passes

```r
expect_equivalent(my_vector, c(1, 2))
```



**Setting up the test structure**

You can set up a test framework in a package using the function `use_testthat()`.

This will create a tests directory that contains:

- A script `testthat.R`.
- A directory `testthat`.

You save your tests in the `tests/testthat/` directory in files with filenames beginning with `test-`. So, for example, the `simutils` package has tests named:

- `test-na_counter.R`
- `test-sample_from_data.R`

There are no other strict rules governing the filenames, but you may find it easier to keep track of which functions you are testing if you name your tests after the functions like in the examples above. Alternatively, you can name your tests after areas of package functionality, for example, putting tests for multiple summary functions in `test-summaries.R`.



**Testing for equality**

You can use `expect_equal()`, `expect_equivalent()` and `expect_identical()` in order to test whether the output of a function is as expected.

These three functions all have slightly different functionality:

- `expect_identical()` checks that the **values**, **attributes**, and **type** of both objects are the same.
- `expect_equal()` checks that the **values**, and **attributes** of both objects are the same. You can adjust how strict `expect_equal()` is by adjusting the `tolerance` parameter.
- `expect_equivalent()` checks that the **values**, of both objects are the same.



**Test for error or warnings**

**Testing Warnings**

Passes

```r
expect_warning(sqrt(-1))
```



**Testing Errors**

Passes

```r
expect_error(sqrt("foo"))
```

Fails

```r
expect_error(sqrt(-1))
```

 

```
Error: sqrt(-1) did not throw an error.
In addition: Warning message:
In sqrt(-1) : NaNs produced
```



**Testing Specific Warning and Error Messages**

Passes

```r
expect_error(sqrt("foo"), "non-numeric argument to mathematical function")
```

Fails

```r
expect_error(sqrt("foo"), "NaNs produced")
```

 

```
Error: error$message does not match "NaNs produced".
Actual value: "non-numeric argument to mathematical function"
```



