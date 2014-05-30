---
layout: page
---


**[Previous Section](transformations.md)** | **[Index](../../README.md)** | **[Next Section](row_transformations.md)**





Column transformations
========

The package mungebitsTransformations, included with the syberia set of packages, comes
with a helper function `column_transformation` that uses the non-standard evaluation
approach defined in the previous section.


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


**[Previous Section](transformations.md)** | **[Index](../../README.md)** | **[Next Section](row_transformations.md)**
