---
layout: page
---


**[Previous Section](transformations_exercises.md)** | **[Index](../../README.md)** | **[Next Section](mungebits.md)**

Mungebits
========

We have finished our basic training and are ready to begin solving our very first formidable
problem as a data engineer. The trade-off to consider when developing a data preparation procedure for
your classifier is whether or not to split your training and prediction code. If this 
road is taken, additional prediction code will have to be written for almost every data manipulation
step we make during training.

Instead, we will take a different path. We will force ourselves to think about the difference
between training and prediction before even running the training code by restricting our
data preparation to a certain structure.

The trick is this: we would like an object that we can throw our data set into,
and

   1. Will run the training code the first time it is executed.
   2. Will run the prediction code in subsequent executions.
   
With this one abstraction, we can forget about running different prediction code on our data set.
Instead, we can pass this object to some server that will be
running predictions, and it can use the exact same calling procedure that was use for training; 
the object itself will track whether to run the training or prediction code. 

In data science, manipulating data in small steps is called munging, and the object defined above
is the smallest atomic unit that respects the training versus prediction distinction, so we
will call it a **mungebit**.


```r
mungebit <- setRefClass("mungebit",
  # Create a reference class object to represent our "mungebit".
  # For a refresher on these, see http://adv-r.had.co.nz/OO-essentials.html
  fields = c(train_function = "function", predict_function = "function", trained = "logical"),
  methods = list(
    initialize = function(train_fn = identity, predict_fn = train_function) {
      trained <<- FALSE # Recall that modifying reference class fields is done with <<-
      train_function <<- train_fn
      predict_function <<- predict_fn
    },
    
    run = function(dataframe) {
      if (trained) predict_function(dataframe)
      else {
        out <- train_function(dataframe)
        trained <<- TRUE
        out
      }
    }
  )
)
```


Looking past the scaffold that sets up the object-oriented programming, we see one main idea: 
the `trained` member is a `logical` flag which keeps track of whether or not we have already
run the `training_function`. If so, we will instead run the `predict_function`.

This has almost solved our problem, but there is one critical flaw. Imagine we tried to re-write
our imputation code from an earlier section to use our mungebit:


```r
imputer_mungebit <- mungebit$new(function(dataframe)
  within(dataframe, Sepal.Length[is.na(Sepal.Length)] <- mean(Sepal.Length, na.rm = TRUE))
)
iris2 <- iris; iris2[1, ] <- NA
iris2 <- imputer_mungebit$run(iris2)
if (imputer_mungebit$trained) cat("Mungebit was trained.\n")
```

```
## Mungebit was trained.
```

```r
head(iris2$Sepal.Length)
```

```
## [1] 5.848 4.900 4.700 4.600 5.000 5.400
```


Our mungebit will still fail to produce the correct behavior during prediction:


```r
iris3 <- iris[1, ]
iris3[1, 1] <- NA
imputer_mungebit$run(iris3)
```

```
##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
## 1          NaN         3.5          1.4         0.2  setosa
```


Adding memory to our mungebits
--------

Our problem is that the mungebit still does not have *memory*. To correctly restore missing values
during prediction, we need to remember what the mean of our training sample was and use this value.

We will add a reference class field `inputs` which aims to fill this need and will allow us to
pass information from training to prediction.



```r
mungebit2 <- setRefClass("mungebit2",
  # Create a reference class object to represent our "mungebit".
  # For a refresher on these, see http://adv-r.had.co.nz/OO-essentials.html
  fields = c(train_function = "function", predict_function = "function",
             trained = "logical", inputs = "environment"),
  methods = list(
    initialize = function(train_fn = identity, predict_fn = train_function) {
      trained <<- FALSE
      train_function <<- train_fn
      predict_function <<- predict_fn
      inputs <<- new.env(parent = emptyenv())
    },
    
    run = function(dataframe) {
      on.exit(trained <<- TRUE) # Another way to do this.
      fn <- if (trained) predict_function else train_function
      parent.env(inputs) <<- environment(fn)
      environment(fn)$inputs <- inputs
      fn(dataframe)
    }
  )
)
```


We can now store the training sample mean from our training computation.



```r
imputer_mungebit <- mungebit2$new(
  train_fn = function(dataframe)
    within(dataframe, Sepal.Length[is.na(Sepal.Length)] <- (inputs$mean <<- mean(Sepal.Length, na.rm = TRUE)))
, predict_fn = function(dataframe)
    within(dataframe, Sepal.Length[is.na(Sepal.Length)] <- inputs$mean)
)
iris2 <- iris; iris2[1, ] <- NA
iris2 <- imputer_mungebit$run(iris2)
if (imputer_mungebit$trained) cat("Mungebit was trained.\n")
```

```
## Mungebit was trained.
```

```r
head(iris2$Sepal.Length)
```

```
## [1] 5.848 4.900 4.700 4.600 5.000 5.400
```

```r
imputer_mungebit$inputs$mean
```

```
## [1] 5.848
```


Success! Our `imputer_mungebit` now remembers our training sample mean. It will use this to impute
incoming prediction data.


```r
iris3 <- iris[1, ]; iris3[1, 1] <- NA
imputer_mungebit$run(iris3)
```

```
##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
## 1        5.848         3.5          1.4         0.2  setosa
```




**[Previous Section](transformations_exercises.md)** | **[Index](../../README.md)** | **[Next Section](mungebits.md)**
