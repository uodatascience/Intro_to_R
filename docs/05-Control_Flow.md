---
title: "Control Flow"
author: "Jonny Saunders"
date: "04/19/2018"
output: 
  md_document:
    preserve_yaml: true
    toc: true
    toc_depth: 2
---
# Control Flow


Stolen without changes from [Krista's lesson](https://github.com/kdestasio/2018_dataSciSem). A stub, a work-in-progress.


## For Loops

```
# General form

for (variable in sequence){
    Do something
}
```


```r
for (i in (1:10)) {
  x <- i^2
  print(x)
}
```

```
## [1] 1
## [1] 4
## [1] 9
## [1] 16
## [1] 25
## [1] 36
## [1] 49
## [1] 64
## [1] 81
## [1] 100
```

## While Loops

```
# General form:

while (condition){
    Do something
}
```


```r
i <- 1

while (i < 11){
  print(i)
  i <- i + 1
}
```

```
## [1] 1
## [1] 2
## [1] 3
## [1] 4
## [1] 5
## [1] 6
## [1] 7
## [1] 8
## [1] 9
## [1] 10
```


## If/Then Statements

```
# General form:

if (condition){
    Do something
} else {
    Do something different
}
```


```r
if (i > 5) {
  print("This number is greater than 5.")
} else {
  print("This number is less than 5.")
}
```

```
## [1] "This number is greater than 5."
```


## Apply functions
[R-bloggers, lapply() and sapply()](https://www.r-bloggers.com/using-apply-sapply-lapply-in-r/)

FUNCTION | INPUT          | OUTPUT
-------- | -------------- | -------
apply    | matrix         |	vector or matrix
sapply	 | vector or list | vector or matrix
lapply	 | vector or list | list

`apply()` functions are a type **functional**, i.e. a function that takes a function as an input and returns a vector or a list as output. This can be used as an alternative to for loops. 


```r
X <- matrix(data = c(1,2,3, 1,2,3, 1,2,3), nrow = 3, ncol = 3, byrow = T)
X
```

```
##      [,1] [,2] [,3]
## [1,]    1    2    3
## [2,]    1    2    3
## [3,]    1    2    3
```

```r
# Sum the values of each column with `apply()`
apply(X, 2, sum) # the second argument refers to a vector giving the subscripts which the function will be applied over, e.g. for a matrix 1 indicates rows, 2 indicates columns. The third argument specifies the function to be applied
```

```
## [1] 3 6 9
```

```r
# Now sum the values of each row
apply(X, 1, sum)
```

```
## [1] 6 6 6
```

`lapply()` takes a function, applies it to each element in a list, and returns the results in the form of a list. Recall the for loop from the above example:

```
for (i in (1:10)){
  x <- i^2
  print(x)
}
```

We can accomplish the same result without using a for loop by instead using `lapply()`. Notice that `lapply()` returns a list. 


```r
lapply(1:10, function(x) x^2)
```

```
## [[1]]
## [1] 1
## 
## [[2]]
## [1] 4
## 
## [[3]]
## [1] 9
## 
## [[4]]
## [1] 16
## 
## [[5]]
## [1] 25
## 
## [[6]]
## [1] 36
## 
## [[7]]
## [1] 49
## 
## [[8]]
## [1] 64
## 
## [[9]]
## [1] 81
## 
## [[10]]
## [1] 100
```

If we want to get back an atomic vector instead of a list, we can use `sapply()`. 


```r
sapply(1:10, function(x) x^2)
```

```
##  [1]   1   4   9  16  25  36  49  64  81 100
```

