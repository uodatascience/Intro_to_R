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

> Scoping from R Faq 3.3.1

NOT REAL YET!


```r
# S4 Methods are stored in environments 
nM <- asNamespace("Matrix")
sort(grep("^[.]__T__", names(nM), value=TRUE))
```

```
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
```

```r
meth.Ops <- nM$`.__T__Ops:base`
head(sort(names(meth.Ops)))
```

```
## [1] "abIndex#abIndex" "abIndex#ANY"     "ANY#abIndex"     "ANY#ddiMatrix"  
## [5] "ANY#ldiMatrix"   "ANY#Matrix"
```
