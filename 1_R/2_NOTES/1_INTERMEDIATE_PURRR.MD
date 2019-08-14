## Key learnings

![1565817670112](.\imgs\functional_programming.png)



**About "pure functions"**

In a pure function:

- output only depends on input
- no "side-effect"

```
# Output depends only on inputs
# No side effect
sum(1:10)
[1] 55

mean(1:100)
[1] 50.5
```

**Impure functions are useful**

Impure functions:

- Depend on environment
- Have "side-effects"

```
# Outputs depends of environment 
Sys.Date()
[1] "2018-10-04"

# Side effect only
write.csv(iris, "iris.csv")
```



### How to use `purrr` to easily create functions?

![1565816761082](.\imgs\purrr_map.png)

**Function creation** via one sided formulas (~) - what do I want to do with each element of the list you are iterating on.

+ lambda (or anonymous) functions 

  ```R
  map(1:3, ~ .x * 10)
  
  [[1]]
  [1] 10
  
  [[2]]
  [1] 20
  
  [[3]]
  [1] 30
  ```

+ mapper: anonymous function with a one-sided formula

  ```
  # With two parameters
  map2(visits2016, visits2017, ~ .x + .y)
  
  # Is equivalent to 
  map2(visits2016, visits2017, ~ ..1 + ..2)
   # With more than two parameters
  pmap(list, ~ ..1 + ..2 + ..3)
  ```

+ reusable mappers - `as_mapper()` creates a reusable mapper from a lambda function 

  ```r
  ten_times <- (1:3, ~ .x * 10)
  map(1:5, ten_times)
  
  [[1]]
  [1] 10
  
  [[2]]
  [1] 20
  
  [[3]]
  [1] 30
  ```

**Function manipulation:**

+ **functionals** - take a function as an input and return a vector

  + `map()` and friends - take a list and a function and apply this function on each element of a list

  + `keep()`: extract elements that satisfy a condition, and `discard()`: remove elements that satisfy a condition

    + Using `map()` & `keep()` : *keep returns an empty object rather than a NULL

    + ```R
      df_list <- list(iris, airquality) %>% map(head)
      
      map(df_list, ~ keep(.x, is.factor))
      
      [[1]]
        Species
      1  setosa
      2  setosa
      3  setosa
      4  setosa
      5  setosa
      6  setosa
      
      [[2]]
      data frame with 0 columns and 6 rows
      ```

      

  + `some()`: do some elements satisfy a conditionand `every()`: does every element satisfy a condition

+ **function operators **- take a function and return another function modified

  Handling errors and warning:

  + `safely()` and `possibly()` - adverbs in purrr

    + Use `safely() `to handle error.

      `safely()` returns a function that will return:

      - `$result`
      - `$error`

    + Extracting elements from safely() results `map()` & `"result"` or `"error"`

      ```R
      safe_log <- safely(log)
      
      map( list("a", 2),  safe_log) %>% 
        map("result")
      
      [[1]]
      NULL
      
      [[2]]
      [1] 0.6931472
      ```

      Cleaning safely results

      Transform the result with `transpose()` :

      ```r
      # Transpose turn a list of n elements a and b 
      # to a list of a and b, with each n elements
      l <- list("a", 2, 3)
      
      map(l, safe_log) %>% length()
      [1] 3
      map(l, safe_log) %>% transpose() %>% length()
      [1] 2
      ```

    + Use `possibly()` to return either the result or the value of otherwise. It can return a logical, a character, a NA, a number

      ```
      library(purrr)
      
      possible_sum <- possibly(sum, otherwise = "nop")
      
      possible_sum(1)
      [1] 0
      
      possible_sum("a")
      [1] "nop"
      ```

    + `compact()`

      `compact()` removes the `NULL`:

      ```r
      list(1, NULL, 3, 4, NULL) %>%
        compact()
      [[1]]
      [1] 1
      
      [[2]]
      [1] 3
      
      [[3]]
      [1] 4
      ```

    + `possibly()` and `compact()`

      `otherwise = NULL %>% compact()`:

      ```r
      l <- list(1,2,3,"a")
      
      possible_log <- possibly(log, otherwise = NULL)
      
      map(l, possible_log) %>% compact()
      [[1]]
      [1] 0
      
      [[2]]
      [1] 0.6931472
      
      [[3]]
      [1] 1.098612
      ```

  + `partial()`

  + `compose()`

  + `negate()`

    

Example of a cleaner code (easier to read, easier to understand and easier to maintain):

```R
library(purrr)
rounded_mean <- compose(
	partial(round, digits = 1),
	partial(mean, trim = 2, na.rm = TRUE)
	)
map(
 	list(airquality, mtcars),
	~ map_dbl(.x, .f = rounded_mean)
)

[[1]]
  Ozone Solar.R    Wind    Temp   Month     Day 
   31.5   205.0     9.7    79.0     7.0    16.0 

[[2]]
  mpg   cyl  disp    hp  drat    wt  qsec    vs    am  gear  carb 
 19.2   6.0 196.3 123.0   3.7   3.3  17.7   0.0   0.0   4.0   2.0 
```



**Setting names with `map()`:**

```
all_visits <- list(visits2015, visits2016, visits2017)

named_all_visits <- map(all_visits, ~ set_names(.x, month.abb))
```



**`detect_index()`**

```R
# Which is the first element with a mean above 1000?
detect_index(visits2016, ~ mean(.x) > 1000)
[1] 1
```



**`has_element()`** :

```
# What is the value of the first element with a mean above 1000?
detect(visits2016, ~ mean(.x) > 1000, .right = TRUE)
 [1] 1289  782 1432 1171 1094 1015  582  946 1191 1393 1307 1125 1267
[14] 1345 1066  810  583  733  795  766  873  656 1018  645  949  938
[27] 1118 1106 1134 1126
```

 **`detect()`**

```
# Does one month has a mean of 981?
visits2016_mean <- map(visits2016, mean)
has_element(visits2016_mean,981)
[1] TRUE
```



How to write compose?

When you use `compose()`, the functions are passed from right to left — that is to say in the same order as the one you would use in a nested call in base R: the first function to be executed is the function on the right.

In other words, if you are used to the pipe, the order is the opposite one:

```
# With the pipe
1:28 %>% 
    mean() %>% 
    round()

# In base R
round(mean(1:28))

# With compose
rounded_mean <- compose(round, mean)
rounded_mean(1:28)
```

Using `partial()`

Prefill a function:

```R
mean_na_rm <- partial(mean, na.rm = TRUE)
mean_na_rm( c(1,2,3,NA) )
[1] 2
lm_iris <- partial(lm, data = iris)
lm_iris(Sepal.Length ~ Sepal.Width)
Call:
lm(formula = ..1, data = iris)

Coefficients:
(Intercept)  Sepal.Width  
     6.5262      -0.2234
```



Using `partial()` & `compose()`:

```R

rounded_mean <- compose(
  partial(round, digits = 2), 
  partial(mean, na.rm = TRUE)
)
rounded_mean(airquality$Ozone)
[1] 42.13
```



nest() and unnest()

```R
summary_lm <- compose(summary, lm)

iris %>% 
  group_by(Species) %>% 
  nest() %>%
  mutate(data = map(data, ~ summary_lm(Sepal.Length ~ Sepal.Width, 
                                       data = .x)), 
         data = map(data, "r.squared")) %>%
  unnest()
# A tibble: 3 x 2
  Species     data
  <fct>      <dbl>
1 setosa     0.551
2 versicolor 0.277
3 virginica  0.209
```



A Gentle introduction to httr

- [httr: a friendly http package for R](http://httr.r-lib.org/)
  H. Wickham
- [Getting started with httr](https://cran.r-project.org/web/packages/httr/vignettes/quickstart.html)
  H. Wickham