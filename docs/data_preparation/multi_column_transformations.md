---
layout: page
---


**[Previous Section](row_transformations.md)** | **[Index](../../README.md)** | **[Next Section](dataframe_transformations.md)**
  



Multi column transformations
========
  
  Occasionally, we may wish to transform a column by using other columns as input, or create a 
whole new derivative column altogether. For example, consider the built-in `Orange` dataset.


```r
head(Orange)
```

```
##   Tree  age circumference
## 1    1  118            30
## 2    1  484            58
## 3    1  664            87
## 4    1 1004           115
## 5    1 1231           120
## 6    1 1372           142
```


We could wonder whether there is a pattern to the `age / circumference` ratio.


```r
Orange$ratio <- with(Orange, age / circumference)
head(Orange$ratio)
```

```
## [1]  3.933  8.345  7.632  8.730 10.258  9.662
```


To continue with our functional-style tools, we will use the `multi_column_transformation` helper from
the `mungebitsTransformations` package. This works exactly like `column_transformation`, except the
output function will take three arguments: the input `data.frame`, a character vector of input columns,
and the name of the output column.


```r
ratio_transform <- multi_column_transformation(`/`)
ratio_transform(Orange, c("age", "circumference"), "ratio")
head(Orange$ratio)
```

```
## [1]  3.933  8.345  7.632  8.730 10.258  9.662
```


Note we used the trick of passing the primitive function `` `/` `` into `multi_column_transformation`. If
you haven't seen this before and it looks confusing, mentally replace `` `/` `` with `function(x, y) x / y`.

Exercises
-------
  
1. Write a multi-column transformation that accepts a dataframe and a string, and concatenates two
   character columns into one column using the string as the separator. For example, if the input
   columns were `c("a", "b", "c")` and `c("d", "e", "f")` and the separator was `"."`, the output
   would be `c("a.d", "b.e", "c.f")`. (Hint: Use `paste` as the input to the `multi_column_transformation`)
   
   Note that `multi_column_transformation` has the same behavior as `column_transformation` with additional
   arguments:
   

```r
df <- data.frame(one = seq_len(10), two = seq_len(10))
weighted_sum <- multi_column_transformation(function(x, y, weights) x + weights * y)
weighted_sum(df, 1:2, 'three', weights = c(1, 0))
df$three # Only the odd terms will have the second column added on.
```

```
##  [1]  2  2  6  4 10  6 14  8 18 10
```

  
**[Previous Section](row_transformations.md)** | **[Index](../../README.md)** | **[Next Section](dataframe_transformations.md)**
