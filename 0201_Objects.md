---
title: "2.1 - Objects"
author: "Jonny Saunders"
date: "October 5, 2017"
output: 
  md_document:
    preserve_yaml: true
    toc: true
    toc_depth: 2
order: 2
---

-   [What are Objects?](#what-are-objects)
    -   [Object terminology](#object-terminology)
-   [Objects in R](#objects-in-r)
-   [Base Types](#base-types)
    -   [Atomic Vectors](#atomic-vectors)
    -   [Lists & Matrices](#lists-matrices)
    -   [Data Frames](#data-frames)
-   [S3 Objects](#s3-objects)
    -   [Example: Extending S3 Objects](#example-extending-s3-objects)
-   [S4 Objects](#s4-objects)
-   [References](#references)

What are Objects?
=================

Objects are, roughly, data (or more generally a stored state) that knows
what it can do.

We know what happens when we put this troublesome `+` guy between
numbers

    1 + 1 # ud better b sitting down

    ## [1] 2

But it's less clear what it means to `+` letters

    "a" + "b"

    ## Error in "a" + "b": non-numeric argument to binary operator

Let's see what `typeof` variables `1` and `"a"` are :

    typeof(1)

    ## [1] "double"

    typeof("a")

    ## [1] "character"

> (note this is a little misleading, `typeof` determines the base object
> class that an R object is stored as. All R objects are composed of
> base objects, we'll get to the types of objects in the next section)

Object terminology
------------------

A **class** is the description, or 'blueprint' of how individual
**objects** or **instances** are made, including their **attributes** -
which data should be kept and what it should be named, and **methods**,
the functions that they are capable of calling on their stored data or
attributes. Objects can have a nested structure, and sub-classes can
**inherit** the attributes and methods of their parent classes.

For example: As a class, trucks have attributes like engine\_size,
number\_of\_wheels, or number\_of\_jumps\_gone\_off. Trucks have the
method go\_faster(), but only individual instances of trucks can
go\_faster() - the concept/class of trucks can't. As a subclass,
monster\_trucks also have the attributes engine\_size, etc. and the
method go\_faster(), but they also have additional attributes like
mythical\_backstory and methods like monster\_jam().

Objects in R
============

> "In R functions are objects and can be manipulated in much the same
> way as any other object." - *R language guide 2.1.5*

> "S3 objects are functions that call the functions of their objects" -
> *Also R*

R has base types and three object-oriented systems.

-   **Base types:** Low-level C types. Build the other object systems.

-   **S3 - "Casual objects":** Objects that use **generic functions**.
    Rather than asking an object what method to call, the generic
    function decides which method to call based on the argument classes.
    Functions contain the **UseMethod("function\_name", object)**
    function (see `?UseMethod`), which searches the object's namespace
    for "function\_name" and passes computation to it. \*\* Find example
    of S3 object

-   **S4 - "Formal objects":** Formal classes with inheritance and means
    by which methods can be shared between classes.

-   **Reference classes:** Objects that use **message passing** - or the
    method 'belongs to' the class rather than a function. This is the
    common `dataframe$column_name` syntax.

R has a useful package `pryr` for inspecting objects and other
meta-linguistic needs. Let's get that now.

    # install.packages("pryr")

We can query any object's type with pryr's `otype`

    pryr::otype(c(1,2,3,4,5)) # A vector is a base object

    ## [1] "base"

    pryr::otype(data.frame(x=c(1,2,3,4,5))) # A dataframe is an S3 object

    ## [1] "S3"

Base Types
==========

Every R object is built out of basic C structures that define how it is
stored and managed in memory.

This table from [Advanced R - Data
Structures](http://adv-r.had.co.nz/Data-structures.html#data-structures)
summarizes them:

<table>
<thead>
<tr class="header">
<th></th>
<th>Homogenous data</th>
<th>Heterogenous data</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>1-Dimensional</td>
<td>Atomic Vector</td>
<td>List</td>
</tr>
<tr class="even">
<td>2-Dimensional</td>
<td>Matrix</td>
<td>Data frame</td>
</tr>
<tr class="odd">
<td>N-Dimensional</td>
<td>Array</td>
<td></td>
</tr>
</tbody>
</table>

Atomic Vectors
--------------

Lists & Matrices
----------------

Data Frames
-----------

S3 Objects
==========

S3 objects "belong to" functions, S4 objects "have" functions (methods).
S3 classes don't really "exist," but are assigned as an object's "class"
attribute.

    x <- 1
    attr(x, "class")

    ## NULL

    class(x) <- "letters"
    attr(x, "class")

    ## [1] "letters"

S3 objects are defined by a series of functions that themselves contain
the `UseMethod()` function - this is described briefly above, try
`?UseMethod` for more detail. These functions extend the generic
function, typically using the syntax `generic.class()` as in the case of
`mean.Date()` for taking the mean of dates. One can list the objects
that have a generic method, and the methods that an object has with
`methods()`

    methods(mean)

    ## [1] mean.Date     mean.default  mean.difftime mean.POSIXct  mean.POSIXlt 
    ## see '?methods' for accessing help and source code

    methods(class="Date")

    ##  [1] -             [             [[            [<-           +            
    ##  [6] as.character  as.data.frame as.list       as.POSIXct    as.POSIXlt   
    ## [11] Axis          c             coerce        cut           diff         
    ## [16] format        hist          initialize    is.numeric    julian       
    ## [21] Math          mean          months        Ops           pretty       
    ## [26] print         quarters      rep           round         seq          
    ## [31] show          slotsFromS3   split         str           summary      
    ## [36] Summary       trunc         weekdays      weighted.mean xtfrm        
    ## see '?methods' for accessing help and source code

By default, the source code of S3 methods is not visible to R, one can
retreive it with \`utils::getS3method\`\`

The `plot` base function is an s3 generic method.

    pryr::ftype(plot) # get a function's type

    ## [1] "s3"      "generic"

By default, if the first argument is a base type compatible with being
points on a scatterplot, the actual function that is called is
`plot.default`, whose source behaves like you'd expect:

    plot.default

    ## function (x, y = NULL, type = "p", xlim = NULL, ylim = NULL, 
    ##     log = "", main = NULL, sub = NULL, xlab = NULL, ylab = NULL, 
    ##     ann = par("ann"), axes = TRUE, frame.plot = axes, panel.first = NULL, 
    ##     panel.last = NULL, asp = NA, ...) 
    ## {
    ##     localAxis <- function(..., col, bg, pch, cex, lty, lwd) Axis(...)
    ##     localBox <- function(..., col, bg, pch, cex, lty, lwd) box(...)
    ##     localWindow <- function(..., col, bg, pch, cex, lty, lwd) plot.window(...)
    ##     localTitle <- function(..., col, bg, pch, cex, lty, lwd) title(...)
    ##     xlabel <- if (!missing(x)) 
    ##         deparse(substitute(x))
    ##     ylabel <- if (!missing(y)) 
    ##         deparse(substitute(y))
    ##     xy <- xy.coords(x, y, xlabel, ylabel, log)
    ##     xlab <- if (is.null(xlab)) 
    ##         xy$xlab
    ##     else xlab
    ##     ylab <- if (is.null(ylab)) 
    ##         xy$ylab
    ##     else ylab
    ##     xlim <- if (is.null(xlim)) 
    ##         range(xy$x[is.finite(xy$x)])
    ##     else xlim
    ##     ylim <- if (is.null(ylim)) 
    ##         range(xy$y[is.finite(xy$y)])
    ##     else ylim
    ##     dev.hold()
    ##     on.exit(dev.flush())
    ##     plot.new()
    ##     localWindow(xlim, ylim, log, asp, ...)
    ##     panel.first
    ##     plot.xy(xy, type, ...)
    ##     panel.last
    ##     if (axes) {
    ##         localAxis(if (is.null(y)) 
    ##             xy$x
    ##         else x, side = 1, ...)
    ##         localAxis(if (is.null(y)) 
    ##             x
    ##         else y, side = 2, ...)
    ##     }
    ##     if (frame.plot) 
    ##         localBox(...)
    ##     if (ann) 
    ##         localTitle(main = main, sub = sub, xlab = xlab, ylab = ylab, 
    ##             ...)
    ##     invisible()
    ## }
    ## <bytecode: 0x7fbe28a9a0e0>
    ## <environment: namespace:graphics>

If the first argument to `plot` has its own `plot` method (ie. that it
is exported by the object's package namespace, more about this in
section 5), that function is called instead. That's why

    aq <- datasets::airquality
    plot(lm(Ozone ~ Month, data=aq))

![](0201_Objects_files/figure-markdown_strict/unnamed-chunk-10-1.png)![](0201_Objects_files/figure-markdown_strict/unnamed-chunk-10-2.png)![](0201_Objects_files/figure-markdown_strict/unnamed-chunk-10-3.png)![](0201_Objects_files/figure-markdown_strict/unnamed-chunk-10-4.png)

is different than this nonsensical model

    plot(lme4::lmer(Ozone ~ 0 + (Day | Month), data=aq))

![](0201_Objects_files/figure-markdown_strict/unnamed-chunk-11-1.png)

Example: Extending S3 Objects
-----------------------------

> <http://adv-r.had.co.nz/OO-essentials.html> "Creating new methods and
> generics"

    pryr::ftype(mean) # mean is an s3 generic function

    ## [1] "s3"      "generic"

    x <- 1
    class(x) <- "just_one"

    # We give our "just_one" class a mean method:
    mean.just_one <- function(x, ...) print("that's just a one you maniac")

    # Mean behaves like it should for numbers and lists of numbers
    mean(1)

    ## [1] 1

    mean(c(1,1.5))

    ## [1] 1.25

    # Other objects have their own mean() method
    methods(mean)

    ##  [1] mean,ANY-method          mean,Matrix-method      
    ##  [3] mean,sparseMatrix-method mean,sparseVector-method
    ##  [5] mean.Date                mean.default            
    ##  [7] mean.difftime            mean.just_one           
    ##  [9] mean.POSIXct             mean.POSIXlt            
    ## see '?methods' for accessing help and source code

    # like Date objects
    dates <- c("01jan2000","15jan2000")
    attr(dates,"class")

    ## NULL

    mean(dates) # Don't work

    ## Warning in mean.default(dates): argument is not numeric or logical:
    ## returning NA

    ## [1] NA

    # turn it into "Date" object
    dates <- as.Date(dates, "%d%b%Y") # base has a set of "as" methods to convert types
    attr(dates,"class")

    ## [1] "Date"

    mean(dates) # will call its method

    ## [1] "2000-01-08"

    mean.Date(dates) # which can also be called directly

    ## [1] "2000-01-08"

S4 Objects
==========

References
==========

-   <http://manuals.bioinformatics.ucr.edu/home/programming-in-r#TOC-Object-Oriented-Programming-OOP->
-   <http://www.stat.ucla.edu/%7Ecocteau/stat202a/resources/docs/S4Objects.pdf>
-   <http://adv-r.had.co.nz/OO-essentials.html>

------------------------------------------------------------------------

    # S4 Methods are stored in environments 
    nM <- asNamespace("Matrix")
    sort(grep("^[.]__T__", names(nM), value=TRUE))

    ##   [1] ".__T__-:base"                ".__T__!:base"               
    ##   [3] ".__T__[:base"                ".__T__[<-:base"             
    ##   [5] ".__T__*:base"                ".__T__/:base"               
    ##   [7] ".__T__&:base"                ".__T__%*%:base"             
    ##   [9] ".__T__%/%:base"              ".__T__%&%:Matrix"           
    ##  [11] ".__T__%%:base"               ".__T__^:base"               
    ##  [13] ".__T__+:base"                ".__T__all:base"             
    ##  [15] ".__T__all.equal:base"        ".__T__any:base"             
    ##  [17] ".__T__anyNA:base"            ".__T__Arith:base"           
    ##  [19] ".__T__as.array:base"         ".__T__as.integer:base"      
    ##  [21] ".__T__as.logical:base"       ".__T__as.matrix:base"       
    ##  [23] ".__T__as.numeric:base"       ".__T__as.vector:base"       
    ##  [25] ".__T__band:Matrix"           ".__T__BunchKaufman:Matrix"  
    ##  [27] ".__T__cbind2:methods"        ".__T__chol:base"            
    ##  [29] ".__T__chol2inv:base"         ".__T__Cholesky:Matrix"      
    ##  [31] ".__T__coerce:methods"        ".__T__coerce<-:methods"     
    ##  [33] ".__T__colMeans:base"         ".__T__colSums:base"         
    ##  [35] ".__T__Compare:methods"       ".__T__cov2cor:stats"        
    ##  [37] ".__T__crossprod:base"        ".__T__determinant:base"     
    ##  [39] ".__T__diag:base"             ".__T__diag<-:base"          
    ##  [41] ".__T__diff:base"             ".__T__dim:base"             
    ##  [43] ".__T__dim<-:base"            ".__T__dimnames:base"        
    ##  [45] ".__T__dimnames<-:base"       ".__T__drop:base"            
    ##  [47] ".__T__expand:Matrix"         ".__T__expm:Matrix"          
    ##  [49] ".__T__facmul:Matrix"         ".__T__forceSymmetric:Matrix"
    ##  [51] ".__T__format:base"           ".__T__head:utils"           
    ##  [53] ".__T__image:graphics"        ".__T__initialize:methods"   
    ##  [55] ".__T__is.finite:base"        ".__T__is.infinite:base"     
    ##  [57] ".__T__is.na:base"            ".__T__isDiagonal:Matrix"    
    ##  [59] ".__T__isSymmetric:base"      ".__T__isTriangular:Matrix"  
    ##  [61] ".__T__kronecker:base"        ".__T__length:base"          
    ##  [63] ".__T__Logic:base"            ".__T__lu:Matrix"            
    ##  [65] ".__T__Math:base"             ".__T__Math2:methods"        
    ##  [67] ".__T__mean:base"             ".__T__nnzero:Matrix"        
    ##  [69] ".__T__norm:base"             ".__T__Ops:base"             
    ##  [71] ".__T__pack:Matrix"           ".__T__print:base"           
    ##  [73] ".__T__prod:base"             ".__T__qr:base"              
    ##  [75] ".__T__qr.coef:base"          ".__T__qr.fitted:base"       
    ##  [77] ".__T__qr.Q:base"             ".__T__qr.qty:base"          
    ##  [79] ".__T__qr.qy:base"            ".__T__qr.R:base"            
    ##  [81] ".__T__qr.resid:base"         ".__T__rbind2:methods"       
    ##  [83] ".__T__rcond:base"            ".__T__rep:base"             
    ##  [85] ".__T__rowMeans:base"         ".__T__rowSums:base"         
    ##  [87] ".__T__Schur:Matrix"          ".__T__show:methods"         
    ##  [89] ".__T__skewpart:Matrix"       ".__T__solve:base"           
    ##  [91] ".__T__sum:base"              ".__T__summary:base"         
    ##  [93] ".__T__Summary:base"          ".__T__symmpart:Matrix"      
    ##  [95] ".__T__t:base"                ".__T__tail:utils"           
    ##  [97] ".__T__tcrossprod:base"       ".__T__toeplitz:stats"       
    ##  [99] ".__T__tril:Matrix"           ".__T__triu:Matrix"          
    ## [101] ".__T__unname:base"           ".__T__unpack:Matrix"        
    ## [103] ".__T__update:stats"          ".__T__updown:Matrix"        
    ## [105] ".__T__which:base"            ".__T__writeMM:Matrix"       
    ## [107] ".__T__zapsmall:base"

    meth.Ops <- nM$`.__T__Ops:base`
    head(sort(names(meth.Ops)))

    ## [1] "abIndex#abIndex" "abIndex#ANY"     "ANY#abIndex"     "ANY#ddiMatrix"  
    ## [5] "ANY#ldiMatrix"   "ANY#Matrix"
