**[Previous Section](training_versus_prediction.html)** | **[Index](../index.html)** | **[Next Section](transformations.html)**

Some manual exercises
======

Before diving into the *mungebits* package and its use for data preparation, let us try to
manually play with some operations.

Filtering out values
-------

Consider the `chickwts` dataset that comes with base R:


```r
attach(chickwts)
print(rbind(head(chickwts, 2), tail(chickwts, 2)))
```

```
##    weight      feed
## 1     179 horsebean
## 2     160 horsebean
## 70    283    casein
## 71    332    casein
```

```r
print(unique(chickwts$feed))
```

```
## [1] horsebean linseed   soybean   sunflower meatmeal  casein   
## Levels: casein horsebean linseed meatmeal soybean sunflower
```


Our job is to condense the information in the second column for an algorithm that does not 
deal well with too many levels. We would like to turn to turn the `horsebean` and `soybean`
levels into a `bean` category, the `linseed` and `sunflower` into a `seed` category,
and the other two into `other`. We could do this manually:


```r
cleanup_feed <- function(feed) {
  factor(ifelse(feed %in% c('horsebean', 'soybean'), 'bean',
         ifelse(feed %in% c('sunflower', 'linseed'), 'seed', 'other')))
}

clean_chickwts <- chickwts
clean_chickwts$feed <- cleanup_feed(clean_chickwts$feed)
print(rbind(head(clean_chickwts, 2), tail(clean_chickwts, 2)))
```

```
##    weight  feed
## 1     179  bean
## 2     160  bean
## 70    283 other
## 71    332 other
```

```r
print(unique(clean_chickwts$feed))
```

```
## [1] bean  seed  other
## Levels: bean other seed
```


What happens when we try to apply our preparation function to a single row for prediction?


```r
print(cleanup_feed(chickwts[1, ]$feed))
```

```
## [1] bean
## Levels: bean
```


Although the `character` value is correct, the `levels` are no longer the same. Therefore, we cannot
use the same function for prediction and training.

The prediction function must *remember* the levels that were used during training, just like
the prediction step for imputation had to remember the mean.

This is one of the problems we face when performing data preparation on a training set
versus single-row prediction data that is streaming in real-time. In the next section,
we will learn how transformations, and later mungebits, help us make this process
more manageable.

**[Previous Section](training_versus_prediction.html)** | **[Index](../index.html)** | **[Next Section](transformations.html)**
