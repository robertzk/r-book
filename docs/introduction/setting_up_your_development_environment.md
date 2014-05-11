---
layout: page
---


**[Previous Section](what_is_a_production_level_classifier.md)** | **[Index](../../README.md)** | **[Next Section](review_of_R.md)**

Setting up your development environment
=====

To begin, ensure that your R version is installed and up to date. You can download
the latest from CRAN's website: http://cran.org.

Once you have installed the latest version of R, you need to obtain the current collection of
syberia packages. Begin with


```r
install.packages("devtools")
```


to install the `devtools` package, a collection of utilities written by [Hadley](http://github.com/hadley)
that aim to help R programmers with package development.

Installing syberia and its dependencies can be accomplished with


```r
install_github("syberia", "robertzk")
library(syberia)
```


On loading the package, it will fetch all of its external Github dependencies. If you are
having problems installing dependencies, see the Troubleshooting section.

You can verify that syberia is working by typing `syberia_version()`. 

**[Previous Section](what_is_a_production_level_classifier.md)** | **[Index](../../README.md)** | **[Next Section](review_of_R.md)**
