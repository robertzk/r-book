---
layout: page
---


**[Previous Section](filtering_out_values.md)** | **[Index](../../README.md)** | **[Next Section](column_transformations.md)**




Transformations
======

Before we examine the need to address differences in data preparation for training versus
prediction, we will try to understand how to use `lapply` on a `data.frame` without performance
overhead.

By default, R implements a copy-on-write mechanism. Any changes inside of a function body
to one of its input arguments creates a full duplicate of the value.

```r
drop_some_columns <- function(dataframe) { dataframe[, some_cols] <- NULL; dataframe }
convert_cols_to_numeric <- function(dataframe) {
  dataframe[, other_cols] <- as.numeric(dataframe[, other_cols]); dataframe }
discretize_cols <- function(dataframe) {
  dataframe[, disc_cols] <- discretizer_function(dataframe[, disc_cols]) }
dataframe <- discretize_cols(convert_cols_to_numeric(drop_some_columns(dataframe)))
```

This means the above code will suffer from a severe performance deficit: every call to a
function that makes a slight modification to the `data.frame` will result in a full copy of
the input `data.frame`, resulting in three full copies in the above code. Ideally, zero
copies should have to be made. There are two immediate solutions to this problem.

  1. Pass environments to the functions, which will mimic C++'s pointers.
  
  2. Use non-standard evaluation to modify the calling environment directly
     and by-pass the copy on value mechanism.
     
While neither solution is ideal, the latter will balance the complexity towards
the modifying functions rather than the calling mechanism; in the former scenario, the user
will have to remember to wrap their `data.frame`s with environments before calling
any modification function. Below we examine what the two methods would look like for
doubling the first column in a `data.frame`.


```r
using_environments <- function(env) env$data[[1]] <- 2 * env$data[[1]]
env <- new.env()
env$data <- iris
using_environments(env)
print(t(cbind(head(iris[[1]]), head(env$data[[1]]))))
```

```
##      [,1] [,2] [,3] [,4] [,5] [,6]
## [1,]  5.1  4.9  4.7  4.6    5  5.4
## [2,] 10.2  9.8  9.4  9.2   10 10.8
```



```r
using_nonstandard_evaluation <- function(dataframe) eval.parent(substitute(dataframe[[1]] <- 2 * 
    dataframe[[1]]))
data <- iris
using_nonstandard_evaluation(data)
print(t(cbind(head(iris[[1]]), head(data[[1]]))))
```

```
##      [,1] [,2] [,3] [,4] [,5] [,6]
## [1,]  5.1  4.9  4.7  4.6    5  5.4
## [2,] 10.2  9.8  9.4  9.2   10 10.8
```


```r
library(microbenchmark)
microbenchmark(using_environments(env), using_nonstandard_evaluation(data))
## Unit: microseconds
#                                expr   min    lq median    uq    max neval
#             using_environments(env) 24.59 34.88  37.28 40.26 1198.4   100
#  using_nonstandard_evaluation(data) 26.17 35.30  39.46 42.61  108.3   100
```

As we can see, the execution times are comparable, and the method we choose
will depend on whether we would like easier use on the calling side or on the processing side.

Column transformations
--------

The package mungebitsTransformations, included with the syberia set of packages, comes
with a helper function `column_transformation` that uses the non-standard evaluation
approach defined above.


```r
doubler <- column_transformation(function(x) x * 2)
doubler(iris, 1:4)  # There is no need to assign the output -- iris gets modified directly
print(head(iris))
```

```
##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
## 1         10.2         7.0          2.8         0.4  setosa
## 2          9.8         6.0          2.8         0.4  setosa
## 3          9.4         6.4          2.6         0.4  setosa
## 4          9.2         6.2          3.0         0.4  setosa
## 5         10.0         7.2          2.8         0.4  setosa
## 6         10.8         7.8          3.4         0.8  setosa
```


The `column_transformation` takes an input function `f` as its first argument, and *produces another function* (
in this case `doubler`). This output function will take a `data.frame` and column indices and apply `f` to
each of those columns. All of the standard subsetting possibilities apply.

```r
# The following are all equivalent.
doubler(iris, 1:4)
doubler(iris, -5)
doubler(iris, colnames(iris)[1:4])
doubler(iris, c(T, T, T, T, F))
```

However, there is also another possibility.

```r
doubler(iris, is.numeric) # Also the same as the above examples.
doubler(iris, sapply(iris, is.numeric)) # And equivalent to this call.
```

We can subset by passing in a function that is applied to each column. Only the columns that return
`TRUE` will ultimately be doubled.

The fundamental operations on our data can be categorized in several ways: 

  - Modifications column-by-column
  - Modifications row-by-row
  - Other modifications (on the whole dataframe)

Any modification that is column-independent, that is, whose computation relies only on the value
of that column, can be performed using a `column_transformation`. For example, we can perform mean
imputation like in our earlier example.


```r
imputer <- column_transformation(function(x) {
    x[is.na(x)] <- mean(x, na.rm = TRUE)
    x
})
iris[1, 1] <- NA
imputer(iris, 1)
print(head(iris[[1]]))
```

```
## [1] 11.7  9.8  9.4  9.2 10.0 10.8
```


We can also replicate the example from the previous section.


```r
cleanup_feed <- function(feed) {
    factor(ifelse(feed %in% c("horsebean", "soybean"), "bean", ifelse(feed %in% 
        c("sunflower", "linseed"), "seed", "other")))
}
cleanup_transform <- column_transformation(cleanup_feed)
cleanup_transform(chickwts, "feed")
chickwts[floor(seq_len(6) * nrow(chickwts)/6), ]
```

```
##    weight  feed
## 11    309  seed
## 23    243  bean
## 35    158  bean
## 47    297  seed
## 59    258 other
## 71    332 other
```


This re-framing of manipulating data column-by-column does not become interesting until
we understand how it fits into the training versus prediction obstacle.

Exercises
---------

1. Write a `column_transformation` for performing `median` imputation and test your result on
   the following data set:

   `iris2 <- iris; iris2[1, ] <- NA`

   (Note you cannot use this for factor columns.)

2. Assuming you know what the levels will be for the transformed `chickwts` example (`"bean"`, 
   `"seed"`, and `"other"`), write a `column_transformation` that correctly transforms 
   the `feed` column *and* restores the levels from training. (For example, a one-row dataframe
   with `feed` `"bean"` must produce
   
   `factor("bean", levels = c("bean", "other", "seed")))`
   
3. Produce a `column_transformation` that removes outliers by receiving optional arguments
   `max` and `min`, and replaces values outside these bounds with `NA`. You can use the 
   fact that `column_transformation` will dutifully pass along any additional arguments, for example:
   

```r
adder <- column_transformation(function(x, val) x + val)
iris2 <- iris
adder(iris2, "Sepal.Length", val = 2)
head(iris2$Sepal.Length - iris$Sepal.Length)
```

```
## [1] 2 2 2 2 2 2
```


**[Previous Section](filtering_out_values.md)** | **[Index](../../README.md)** | **[Next Section](column_transformations.md)**
