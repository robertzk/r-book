**[Previous Section](index.md)** | **[Index](../../README.md)** | **[Next Section](filtering_out_values.md)**




Training versus prediction
=====

The difference between preparing data for a one-time statistics project and for real-time usage
has one important pitfall: our code may not always look the same for training as for prediction.

For example, consider mean imputation--taking a variable, i.e., a column of data in a `data.frame`, and
replacing missing values with the mean.


```r
example_data <-
  data.frame(one = c(rnorm(100, 0, 1), NA),
             two = c(NA, rnorm(100, 10, 5)))
# example_data is now a data.frame with two columns, the first 100 values drawn from
# a normal distribution with mean 0 and standard deviation 1 followed by a missing value,
# and the latter column a missing value followed by a normal distribution with mean 10
# and standard deviation 5.
print(rbind(head(example_data, 2), tail(example_data, 2)))
```

```
##         one    two
## 1    0.1925     NA
## 2   -1.4467 11.050
## 100  0.9120  5.284
## 101      NA 24.645
```



```r
for (col in seq_along(example_data)) # Now impute each column with its mean
  example_data[is.na(example_data[[col]]), col] <- mean(example_data[[col]], na.rm = TRUE)
print(rbind(head(example_data, 2), tail(example_data, 2)))
```

```
##         one    two
## 1    0.1925 10.205
## 2   -1.4467 11.050
## 100  0.9120  5.284
## 101  0.2371 24.645
```


However, when we are predicting on single data points in real-time, our `data.frame`s will be
only one row: imputing will make no sense. We must *remember* the mean that we 
used during training. The implication is that future data points are assumed to come
from the same distribution as the training data points, so their expected value is
the mean of the distribution, which is best approximated by the mean of our training sample.


```r
prediction_row <- data.frame(one = NA, two = rnorm(1, 10, 5))
# Impute the missing value using the mean from example_data
for (col in seq_along(prediction_row)) 
  prediction_row[is.na(prediction_row[[col]]), col] <- mean(example_data[[col]], na.rm = TRUE)
print(prediction_row)
```

```
##      one   two
## 1 0.2371 15.81
```


We have now come to our first fork in the road. We can choose two paths: we can decide that whenever we have to perform some pre-processing operations on portions of a dataset, we resolve to also write any necessary prediction function. We will use the former code for training, and the latter code for prediction, and will keep the two
in separate places.

The other option is to force ourselves to develop a convention that will make it impossible to forget
about the distinction between pre-processing for training and pre-processing for prediction, and allow
us to use the same code to perform either step.

The rest of this chapter is dedicated to one example of such a convention--the **mungebits** package 
that comes with the syberia set of packages.

**[Previous Section](index.md)** | **[Index](../../README.md)** | **[Next Section](filtering_out_values.md)**
