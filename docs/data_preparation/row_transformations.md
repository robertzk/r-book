**[Previous Section](column_transformations.md)** | **[Index](../../README.md)** | **[Next Section](multi_column_transformations.md)**
  



Row transformations
========
  
  Transforming columns in a `data.frame` has a cousin: transforming rows. For example, we can write
a function that takes as input a criterion over each row, and filters out the rows not satisfying
this criterion from our input `data.frame`:


```r
filter_rows <- function(dataframe, condition)
  eval.parent(substitute(dataframe <- dataframe[apply(dataframe, 1, condition), ]))
filter_rows(iris, function(x) x[1] < 5)
cat(nrow(datasets::iris) - nrow(iris), "rows were removed")
```

```
## 128 rows were removed
```


This can of course be accomplished with the straightforward `iris <- iris[apply(iris, 1, function(x) x[1] < 5), ]`,
but we will see how the above functional approach leads to a more general solution to the training versus
prediction problem.

Exercises
-------
  
1. Write a row transformation that accepts a dataframe and a string, and removes all rows containing
   that string. (For extra credit, use a regular expression that looks for that string as a subtring.)
  
**[Previous Section](column_transformations.md)** | **[Index](../../README.md)** | **[Next Section](multi_column_transformations.md)**
