---
title: "5 - Functions"
author: "Jonny Saunders"
date: "October 5, 2017"
output: 
  md_document:
    preserve_yaml: true
    toc: true
    toc_depth: 2
order: 2
---
# Functions

## Form

The general form of a function is

```r
function_name <- function(arguments){
    #Do something
    return(output)
}
```

Functions have:

1. An argument list, or `formals()` - what is passed to the function
2. A `body()`. Typically, this is the code between `{curly braces}`, but can be any syntactically complete statement - `{}` denote a code "block" that is not evaluated until the end of the block, rather than evaluated line-by-line as is otherwise the case.
3. An `environment()`, or the list of symbol (name)/value pairs known to the function.

For example...

```r
inverse <- function(invert_me){
    inverse <- 1/invert_me
    return(inverse)
}

inverse(2)
```

```
## [1] 0.5
```

We can interrogate the parts of a function


```r
formals(inverse)
```

```
## $invert_me
```

```r
body(inverse)
```

```
## {
##     inverse <- 1/invert_me
##     return(inverse)
## }
```

```r
environment(inverse)
```

```
## <environment: R_GlobalEnv>
```

## Environments and Scoping

### Function Environments 

Functions create their own private collection of variables that are distinct from those in the global environment. 

For example, what's going on here?


```r
x <- 1
y <- 3

whos_who <- function(){
  x <- 2
  print(paste("x is", x))
  print(paste("y is", y))
}

whos_who()
```

```
## [1] "x is 2"
## [1] "y is 3"
```

```r
print(paste("but x is also", x))
```

```
## [1] "but x is also 1"
```

We define x and y to be 1 and 3 respectively, and then a function that defines x to be 2. When we call the function, it tells us that x is 2 and 3, but outside the function x is 1. 

The assignment `x <- 2` is **scoped**, or defined locally in the function's environment. **Lexical scoping** is the process by which functions lookup the values associated with different names. In this case, `x` is defined locally, but `y` is not. Functions inherit the enviroment that they were defined in, or their **enclosing environment**, in this case the global environment -- a function and its attached environment is called a **closure**.


```r
environment(whos_who)
```

```
## <environment: R_GlobalEnv>
```

If a function doesn't find a variable locally, it ascends the environments it is nested within until it finds (or doesn't find) a match. If one defines a function within another function, the innermost function will preserve the environment of the outer function -- which can be used to make "function factories:"


```r
# example from http://adv-r.had.co.nz/Environments.html#function-envs

plus <- function(x){
  function(y){
    x + y
  }
}

plus_one <- plus(1)
plus_two <- plus(2)

plus_one(1)
```

```
## [1] 2
```

```r
plus_two(2)
```

```
## [1] 4
```

the `plus` function itself returns a function with the environment created when `plus` is called. Since `x` is defined in the returned function's (eg. `plus_one`) **enclosing environment** it will always be able to use that value even if there is another `x` in the global environment. 

While a function will always preserve its enclosing environment, its **execution environment**, or the environment created when the function is called, is created fresh each time. This **fresh start** principle gives functions their utility as dissociable building blocks for more complicated code -- if a function does one thing and does it well, it can be used in whatever context that operation needs to be performed without fear or prejudice from its previous experiences.

Functions also have a **binding environment(s)**, or the environments that have a binding between a name and the function. We will skip these for now as they will become more relevant when it comes time to write packages, but more information can be found here: [http://adv-r.had.co.nz/Environments.html#function-envs](Advanced R - Function Environments)

### Querying environments

The global environment, which we are used to working in, is itself nested within a series of environments:

1. A series of package environments (or **namespaces**) that provide the names of any loaded (`library/require()`'d) packages
2. The base environment that provides the names of the base functions
3. The empty environment, the parent of all environments.

![http://adv-r.had.co.nz/Environments.html](guide_files/globalenv.png)

You can see the loaded packages, or search path, with `search`

```r
search()
```

```
## [1] ".GlobalEnv"        "package:stats"     "package:graphics" 
## [4] "package:grDevices" "package:utils"     "package:datasets" 
## [7] "Autoloads"         "package:base"
```

One can make a symbol to reference an environment with `as.environment`

```r
env <- as.environment('.GlobalEnv')
```

and query its contents with `ls.str()`


```r
ls.str(env)
```

```
## env : <environment: R_GlobalEnv> 
## inverse : function (invert_me)  
## logits2ps : function (x)  
## plus : function (x)  
## plus_one : function (y)  
## plus_two : function (y)  
## whos_who : function ()  
## x :  num 1
## y :  num 3
```

if you're lost deep in a twisted spire of environments and need to get something specifically, you can do so with `get` (although fixing the structure of your code is usually a better idea ;p)


```r
# create a new environment
new_env <- new.env()

# assignation works like a list
new_env$new_var <- 5

# attaching an environment adds it to our search path
attach(new_env)
search()
```

```
## [1] ".GlobalEnv"        "new_env"           "package:stats"    
## [4] "package:graphics"  "package:grDevices" "package:utils"    
## [7] "package:datasets"  "Autoloads"         "package:base"
```

```r
new_var
```

```
## [1] 5
```

```r
detach(new_env)

# we now make a new assignation in the global environment, which will be searched before any attached environment
new_var <- 10
new_var
```

```
## [1] 10
```

```r
attach(new_env)
```

```
## The following object is masked _by_ .GlobalEnv:
## 
##     new_var
```

```r
new_var
```

```
## [1] 10
```

```r
detach(new_env)

# we can skip the masking of the global environment with get
get("new_var", envir=new_env)
```

```
## [1] 5
```

## Arguments

The names of the variables used within the function and the structure of their input are the **formal arguments** of a function, but the variables that are given to a function each time are also called arguments. Where ambiguous, refer to the latter as arguments.

Arguments can be specified by position or name. Partial names also work, but that typically makes for confusing code. Arguments are first matched by name and then by position.


```r
profit_maker <- function(good_idea, stealing, depth){
  print(paste("Momma said i should", good_idea, 
              "but instead I stole", stealing, 
              "dollars and buried it in a hole", depth, "feet deep"))
}

profit_maker("say mean things about barn animals", 100, 1000)
```

```
## [1] "Momma said i should say mean things about barn animals but instead I stole 100 dollars and buried it in a hole 1000 feet deep"
```

```r
profit_maker(depth=100, stealing=1, good_idea="keep my bellybutton open")
```

```
## [1] "Momma said i should keep my bellybutton open but instead I stole 1 dollars and buried it in a hole 100 feet deep"
```

Formal arguments can be defined with default values, so if an argument is not passed the default is used.


```r
whats_it_cost <- function(cost=10){
  print(paste("i dunno like", cost, "bucks"))
}

whats_it_cost()
```

```
## [1] "i dunno like 10 bucks"
```

This is useful in situations where a function should do one thing most of the time, but if asked nicely should do something else.


```r
ice_cream <- function(scoops, round=FALSE){
  if (round == FALSE){
    print(paste("here ya go kid,", scoops, "scoops of hot fresh ice cream"))
  } else {
    print(paste("listen kid i'm gonna give you", floor(scoops), "scoops and you can get out of my shop"))
  }
}

what_i_want <- runif(1)*10

ice_cream(what_i_want)
```

```
## [1] "here ya go kid, 0.957180238328874 scoops of hot fresh ice cream"
```

```r
ice_cream(what_i_want, round=TRUE)
```

```
## [1] "listen kid i'm gonna give you 0 scoops and you can get out of my shop"
```

Default arguments can be used to make code more efficient while still being flexible. For example if the same value needs to be computed in a number of different functions, it can instead be passed. If the default is set to NULL you can check if you need to perform the computation.


```r
# precompute some image's x gradient stupidly
image <- matrix(runif(100), nrow=10, ncol=10)
grad <- diff(image)

image_operation_1 <- function(image, grad=NULL){
  if (is.null(grad)){
    grad <- diff(image)
  }
  # do some stuff to the image
}

image_operation_2 <- function(image, grad=NULL){
  if (is.null(grad)){
    grad <- diff(image)
  }
  # do some stuff to the image
}
```

Additionally, oen can use `...` to specify an arbitrary number of arguments. This is commonly used in the S3 object system where the arguments passed as `...` are simply passed on to the method calls from the generic method.


```r
print_whatever <- function(...){
  args <- list(...)
  anames <- names(args)
  for (a in anames){
    print(paste(a, ":", args[a]))
  }
}

print_whatever(apple="banana", coconut="hospital visit")
```

```
## [1] "apple : banana"
## [1] "coconut : hospital visit"
```










## Todo:

* Scoping from R Faq 3.3.1


```r
# S4 Methods are stored in environments 
nM <- asNamespace("Matrix")
sort(grep("^[.]__T__", names(nM), value=TRUE))
meth.Ops <- nM$`.__T__Ops:base`
head(sort(names(meth.Ops)))
```

