---
title: "2.2 - Data Wrangling"
author: "Jonny Saunders"
date: "10/2/2017"
output: 
  md_document:
    preserve_yaml: true
    toc: true
    toc_depth: 2
order: 3
---
# Data

## Loading data

### One day we will have a summary table here

Most of the time we have a datafile (either on our hard drive or on the internet) that we want to work with in R. To do that, we can use a function to read the data into R. For instance, the **`read.table()`** is a flexible function for reading in data in various formats. 

### .csv files
In order to use **`read.table()`**, you will typically need to specify some of the arguments, such as the argument **`sep = `**, which is where you specify the thing that seperates values in your data file.   

For example, if you are working with a data file in the comma seperated values (csv) format, you would need to specify that commas are the seperator with **`sep = ","`**. 

### Defaults 
Thankfully, other people have done this for us, and have created functions that have the proper defaults to work with common formats like csv fles. For csv files, To see the defaults, simply check the documentation with e.g.  **`?read.table`**  

**`read.csv()`** is basically a version of **`read.table()`** with the defaults set up to work well with csv files. These defaults include setting **","** as the seperator, and setting **`header = TRUE`**, which means that R will read in the first row in the csv file as being a header row, and treat that row as the row of column names. These defaults can be changed though, so if you had a file with no header, you would indicate that with **`header = FALSE`**, like so  

`read.csv(file_path/file_name.csv, header = FALSE)` 

where file_path is the full path to the file, and file_name is the name of the data file. Take a look at the documentation for `read.csv()` with `?read.csv()` to see the different arguments and defaults.


### A brief interlude into formats for data in R
There are lots of options for reading data into R, even for a single format.  

For example, we could instead use **`read_csv()`** from the **`readr`** package. The main difference between **`read.csv()`** and **`read_csv()`** is that they load the data into different formats in R. **`read.csv()`** will read your data in as a **`data.frame`**, which is a common format of data in R.  

**`read_csv()`** will read your data in as a **`tibble`**, which is the data format associated with the tidyverse. There are a couple of differences between these formats. They have different printing defaults, there are differences in subsetting, and differences in recycling. Check out the documentation by running the code **`vignette("tibble")`** or by checking out the chapter on tibbles in the R for Data Science Book <http://r4ds.had.co.nz/tibbles.html>. 

Outside of these differences, they are mostly interchangeable (for most purposes), but occassionally a function will only work properly with one or the other. For example, the **`reshape()`** function only works properly with *data.frames* (unless that has changed in an update).  

### Format issues?
In some rare cases, you may be getting an error due to differences between how your data is formatted in R and what format the function you're attempting to use works with.  

This will sometimes be spelled out in the documentation (e.g., **`reshape`** says it takes data frames as the first argument), but its not always clear; sometimes, you just need to test it out. If you do need to go between different formats, that is easy to do in R with a couple of functions. **`as.data.frame()`** will turn an object that can be a dataframe into a dataframe. **`as_tibble`** will turn an object that can be a tibble into a tibble. 

### .sav files

R can also work with datafiles that are formatted for SPSS, which have the extension '.sav'.  

Like csv's, there are a few options. The one that seems to work the best is **`read_spss()`** from the haven package (also a part of the tidyverse). There is also a function called **`read.spss()`** from the **`foreign`** package, but it is no longer being maintained and may stop working; it also had some frustrating qualities, so probably best to stick to **`read_spss()`**. It's worth noting that **`read_spss()`** will format your data as a **`tibble`**. If you don't want it as a **`tibble`**, you can always use the **`as.data.frame()`** function to turn it into a generic **`data.frame`**.

### `rio` for general import

Another function worth knowing about for reading in data is the **`import()`** function from the **`rio`** package. The nice thing about **`import()`** is that it works with lots of different types of datafiles, and very little has to be changed for each type of datafile. For example, if our data (let's say its name is 'df') were a .sav file, we would read it in with the following command:  

`import(df.sav)`

If it were instead a csv, we would use the following command:  

`import(df.csv)`

Note that virtually nothing changed, just the file extension (which is part of the name of the file). **`import()`** is able to figure out the format of the file based on that extension.

**Remember to assign your data to a variable in the environment!**

A pretty common and frustrating mistake people make when starting with R is not saving their data file as an object. For example, if we were loading in our datafile called 'df', we may want to run some code like this:

`import(df.csv)`

However, this is telling R to just read the data. If you run code like that, it will simply read the data, and print out the data (or some subset of it) into the console. Then, when you go to work with the data, it won't be in your global environment. That's because you didn't save it as an object. You would do that the same way you save anything as an object, with the assignment operator: `<-`. So instead of the above, you'd instead want to run something like the following:

`df <- import(df.csv)`

Now R has read our data and stored it as an object called `df`, that we can start running our models on.

## Saving data

Most of the above functions have complementary `write` functions, for example `write.csv` or `haven::write_sav`. There are two other common ways you may want to save your data

### .RData files

Because all objects in R descend from a precious few C structures, they can all be easily **serialized**, or converted into a string of bits that can be written to disk (not always true in other languages, like Python). These files typically can't be read by anything but R (except if that thing is another programming language where someone has written a specific routine to do so!), so if your mom tries to look at your cool data in openoffice they'll be out of luck.

Saving (and loading) .RData files works with the ... `save` and ... `load` functions

```r
cant_forget <- c("you have to stop wiping your nose with just whatever is close at hand",
                 "see you did it just now that was mine")
save(cant_forget, file="cant_forget.RData")

# Now delete the object so we know our load is real
rm(cant_forget)

# here we load to the .GlobalEnv, don't worry we'll cover environments later
load("cant_forget.RData")
cant_forget
```

```
## [1] "you have to stop wiping your nose with just whatever is close at hand"
## [2] "see you did it just now that was mine"
```

Note: as is true of all files, the extension (ending it with 'RData') doesn't effect the way that a file is saved or represented on the disk, it just serves to tell the operating system which programs can open it, and to tell other programs how they should open it. You could just as easily save your object like


```r
save(cant_forget, file="cant_forget.butiwantto")
```

There is one special .RData file, and that's the file that's just called ".RData" in your user directory (for osx `/Users/Username` -- probably hidden by default). R automatically loads this file every time it opens -- this is the file that is made whenever you accept RStudio's frequent offers to save your workspace image.

`save` is also capable of using a few common compression algorithms. This is most useful for large objects that have many repeated values -- common with "long" format data.


```r
save(cant_forget, file="cant_forget.RData",
     compress=TRUE, # or "gzip", "bzip2", "xz"
     compression_level=6) # more is smaller, but slower
```

If you're curious, check out the `saveRDS` function - it's designed to save single objects (rather than `save`, which can save arbitrary numbers of objects by keeping them in an environment). Since `save` saves the object and its environment, when it is loaded it will have the same name -- this can be troublesome when you have already used that name, or when you forget what the name is. Loading objects (`readRDS`) saved by `saveRDS` works like other loading functions, so you can assign the loaded object to whatver name you'd like.


```r
saveRDS(cant_forget, file="cant_forget.rds")

# We can rewrite our fate.
sad_news <- readRDS("cant_forget.rds")
```

### `sink`

Perhaps common is an overstatement. Consider these the option of last resort when all you want to do is put what you see in this window in some other window and ya don't care how ya do it.

Any output that would go to the console (specifically to `stdout` - try `?stdout` - so it's a bit tricky to open a sink in a code chunk) can be diverted to a file by using the `sink` function. You must call `sink` with a filename to start diverting data, and then call `sink` again to stop. 


```r
# Open the sink
sink("save-everything-here.txt")

life_story <- c("what we need at the store 2day guys", "it's always just apples and bananas", "we have severe vitamin deficiencies")
life_story
```

```
## [1] "what we need at the store 2day guys"
## [2] "it's always just apples and bananas"
## [3] "we have severe vitamin deficiencies"
```

```r
# turn off the sink
sink()

# now see what's in our file
readLines("save-everything-here.txt")
```

```
## character(0)
```

`sink` doesn't work with graphical output, to do that we have to use a function that dumps to a graphics driver, which then saves it as a file. We can see our current graphics device by getting that option...


```r
getOption("device")
```

```
## function (width = 7, height = 7, ...) 
## {
##     grDevices::pdf(NULL, width, height, ...)
## }
## <environment: namespace:knitr>
```

The devices available in the base package are:

* `pdf()`
* `postscript()`
* `xfig()`
* `bitmap()`
* `pictex()`

For example

```r
# make n save a cool plot to show your friends
pdf("put-plots-here.pdf")
plot(seq(1,5))
dev.off()
```

```
## quartz_off_screen 
##                 2
```

```r
# open the pdf however yer system wants to
abs_path <- file.path(getwd(), "put-plots-here.pdf")
system2('open', args=abs_path, wait=FALSE)
```

Notice how we have to use `dev.off()` rather than calling `pdf()` again as would be expected from `sink`

## Indexing

Or, how to get specific pieces of stuff.

We've already seen a bunch of basic indexing


```r
# try ?seq
x <- seq(5, 25, 5)
x
```

```
## [1]  5 10 15 20 25
```

```r
y <- array(1:24, dim=c(3,8))
y
```

```
##      [,1] [,2] [,3] [,4] [,5] [,6] [,7] [,8]
## [1,]    1    4    7   10   13   16   19   22
## [2,]    2    5    8   11   14   17   20   23
## [3,]    3    6    9   12   15   18   21   24
```

```r
z <- list("scores"=c(1,2,3,4,5), 
          "numbers"=c(6,7,8,9,10))

# good ole positional indexing
x[3]
```

```
## [1] 15
```

```r
# indexing with a vector to select multiple positions
x[c(3,5)]
```

```
## [1] 15 25
```

```r
# higher dimensional objects have more dimensions to index
y[1,5]
```

```
## [1] 13
```

```r
# both together
y[3, c(3,4,7)]
```

```
## [1]  9 12 21
```

```r
# leaving a position blank selects all of its indices
y[2,]
```

```
## [1]  2  5  8 11 14 17 20 23
```

```r
# with names
names(x) <- c("adam", "steve", "harry", "ted", "watson")
x["harry"]
```

```
## harry 
##    15
```

```r
z["numbers"]
```

```
## $numbers
## [1]  6  7  8  9 10
```

```r
# Using double brackets to return a vector instead of a list
z[["numbers"]]
```

```
## [1]  6  7  8  9 10
```

```r
# and the equivalent dollar sign
z$scores
```

```
## [1] 1 2 3 4 5
```

Negative values deselect


```r
x[-3]
```

```
##   adam  steve    ted watson 
##      5     10     20     25
```

```r
# useful to select starting from the end
-(1:3) # quick way to construct sequences of negative numbers
```

```
## [1] -1 -2 -3
```

```r
x[-(1:3)] # get the last two
```

```
##    ted watson 
##     20     25
```

We have also seen chained indexing, think of it like indexing the product of each successive indexing operation


```r
x[c(1,3,5)][2]
```

```
## harry 
##    15
```

```r
z[["numbers"]][4]
```

```
## [1] 9
```

```r
# one reason why it's important what class is returned from your indexing operation
# aka knowing why we use double brackets
z["numbers"][4]
```

```
## $<NA>
## NULL
```

dplyr has a `select` function and a collection of tools ("select helpers") to make selecting variables by name a bit more flexible:

* `starts_with()`
* `ends_with()`
* `contains()`
* `matches()` - variables that match a regular expression
* `num_range()` - eg `num_range("x",1:5)` to select `x1, x2, ...`
* `one_of()` - find variables in a list, eg. `one_of(c("calcium", "potassium", "magnesium"))`
* `everything()` - all variables, don't know why you'd use this.

A few examples...

```r
library(dplyr)
```

```
## 
## Attaching package: 'dplyr'
```

```
## The following objects are masked from 'package:stats':
## 
##     filter, lag
```

```
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
```

```r
df <- ggplot2::diamonds
names(df)
```

```
##  [1] "carat"   "cut"     "color"   "clarity" "depth"   "table"   "price"  
##  [8] "x"       "y"       "z"
```

```r
head(select(df, starts_with("c")))
```

```
## # A tibble: 6 x 4
##   carat       cut color clarity
##   <dbl>     <ord> <ord>   <ord>
## 1  0.23     Ideal     E     SI2
## 2  0.21   Premium     E     SI1
## 3  0.23      Good     E     VS1
## 4  0.29   Premium     I     VS2
## 5  0.31      Good     J     SI2
## 6  0.24 Very Good     J    VVS2
```

```r
head(select(df, one_of(c("color", "hue", "luminance", "clarity"))))
```

```
## Warning: Unknown variables: `hue`, `luminance`
```

```
## # A tibble: 6 x 2
##   color clarity
##   <ord>   <ord>
## 1     E     SI2
## 2     E     SI1
## 3     E     VS1
## 4     I     VS2
## 5     J     SI2
## 6     J    VVS2
```

### Logical indexing

In addition to specifying what we want by position, we can specify what we want by using a vector of booleans (`TRUE`, `FALSE`) the same length as the object


```r
x[3]
```

```
## harry 
##    15
```

```r
x[c(FALSE, FALSE, TRUE, FALSE, FALSE)]
```

```
## harry 
##    15
```

This is useful when combined with logical statements (see `?base::Logic`) and comparisons (see `?Comparison`). One makes logical statements or comparisons with these operators:

| Operator | Use |
| ------- | ------------- |
| `!x` | not x |
| `x & y`, `x && y` | x and y (vectorized, single comparison) |
| `x | y`, `x || y` | x or y (vectorized, single comparison) |
| `xor(x, y)` | exclusive or (x or y but not both) |
| `x < y` | x less than y |
| `x <= y` | x less than or equal to y | 
| `x > y` | x greater than y | 
| `x >= y` | x greater than or equal to y | 
| `x == y` | x is equal to y |
| `x != y` | x is not equal to y |

These will all output boolean vectors, more examples


```r
x > 10
```

```
##   adam  steve  harry    ted watson 
##  FALSE  FALSE   TRUE   TRUE   TRUE
```

```r
x >= 10
```

```
##   adam  steve  harry    ted watson 
##  FALSE   TRUE   TRUE   TRUE   TRUE
```

```r
# logical operators can be combined
(x > 10) & (x < 20)
```

```
##   adam  steve  harry    ted watson 
##  FALSE  FALSE   TRUE  FALSE  FALSE
```

```r
# the single & will compare each element
cbind((x>10), (x<20),((x>10) & (x<20)))
```

```
##         [,1]  [,2]  [,3]
## adam   FALSE  TRUE FALSE
## steve  FALSE  TRUE FALSE
## harry   TRUE  TRUE  TRUE
## ted     TRUE FALSE FALSE
## watson  TRUE FALSE FALSE
```

```r
# double &&s will only compare the first element - use it when you are trying to evaluate whether a single thing is true or false, eg. is this value greater than one? are my vectors the same length?
(max(x) > 20) && (min(x) <= 5)
```

```
## [1] TRUE
```

This can be used in indexing, either by explicitly referring to the object within the indexing operator, or by using the `with` function



```r
# Get all the rows in diamonds who are Good or Very Good
# remember the comma at the end, we want to select all columns
df[df$cut=="Good" | df$cut=="Very Good",]
```

```
## # A tibble: 16,988 x 10
##    carat       cut color clarity depth table price     x     y     z
##    <dbl>     <ord> <ord>   <ord> <dbl> <dbl> <int> <dbl> <dbl> <dbl>
##  1  0.23      Good     E     VS1  56.9    65   327  4.05  4.07  2.31
##  2  0.31      Good     J     SI2  63.3    58   335  4.34  4.35  2.75
##  3  0.24 Very Good     J    VVS2  62.8    57   336  3.94  3.96  2.48
##  4  0.24 Very Good     I    VVS1  62.3    57   336  3.95  3.98  2.47
##  5  0.26 Very Good     H     SI1  61.9    55   337  4.07  4.11  2.53
##  6  0.23 Very Good     H     VS1  59.4    61   338  4.00  4.05  2.39
##  7  0.30      Good     J     SI1  64.0    55   339  4.25  4.28  2.73
##  8  0.30      Good     J     SI1  63.4    54   351  4.23  4.29  2.70
##  9  0.30      Good     J     SI1  63.8    56   351  4.23  4.26  2.71
## 10  0.30 Very Good     J     SI1  62.7    59   351  4.21  4.27  2.66
## # ... with 16,978 more rows
```

```r
# you can combine boolean vectors arbitrarily

df[df$cut=="Good" & df$carat>1,]
```

```
## # A tibble: 1,579 x 10
##    carat   cut color clarity depth table price     x     y     z
##    <dbl> <ord> <ord>   <ord> <dbl> <dbl> <int> <dbl> <dbl> <dbl>
##  1  1.01  Good     I      I1  63.1    57  2844  6.35  6.39  4.02
##  2  1.01  Good     H      I1  64.2    61  2846  6.25  6.18  3.99
##  3  1.03  Good     J     SI1  63.6    57  2855  6.38  6.29  4.03
##  4  1.04  Good     I     SI2  59.9    64  2970  6.51  6.45  3.88
##  5  1.01  Good     E      I1  63.8    57  3032  6.40  6.33  4.06
##  6  1.02  Good     E      I1  63.1    60  3051  6.31  6.40  4.01
##  7  1.01  Good     J     SI2  63.7    60  3088  6.40  6.29  4.05
##  8  1.29  Good     I      I1  64.2    54  3098  6.93  6.83  4.42
##  9  1.52  Good     E      I1  57.3    58  3105  7.53  7.42  4.28
## 10  1.52  Good     E      I1  57.3    58  3105  7.53  7.42  4.28
## # ... with 1,569 more rows
```

```r
# select columns in the usual way too
head(df[df$cut=="Good" & df$carat>1, 2])
```

```
## # A tibble: 6 x 1
##     cut
##   <ord>
## 1  Good
## 2  Good
## 3  Good
## 4  Good
## 5  Good
## 6  Good
```

```r
head(df[df$cut=="Good" & df$carat>1, "color"])
```

```
## # A tibble: 6 x 1
##   color
##   <ord>
## 1     I
## 2     H
## 3     J
## 4     I
## 5     E
## 6     E
```

```r
head(df[df$cut=="Good" & df$carat>1,]$color)
```

```
## [1] I H J I E E
## Levels: D < E < F < G < H < I < J
```

```r
# %in% is a useful way to select multiple levels of a factor
head(df[df$cut %in% c("Good", "Very Good"),])
```

```
## # A tibble: 6 x 10
##   carat       cut color clarity depth table price     x     y     z
##   <dbl>     <ord> <ord>   <ord> <dbl> <dbl> <int> <dbl> <dbl> <dbl>
## 1  0.23      Good     E     VS1  56.9    65   327  4.05  4.07  2.31
## 2  0.31      Good     J     SI2  63.3    58   335  4.34  4.35  2.75
## 3  0.24 Very Good     J    VVS2  62.8    57   336  3.94  3.96  2.48
## 4  0.24 Very Good     I    VVS1  62.3    57   336  3.95  3.98  2.47
## 5  0.26 Very Good     H     SI1  61.9    55   337  4.07  4.11  2.53
## 6  0.23 Very Good     H     VS1  59.4    61   338  4.00  4.05  2.39
```

```r
# with temporarily attaches our dataframe so we don't have to keep referring to it explicitly
head(with(df, df[cut %in% c("Good", "Very Good"),]))
```

```
## # A tibble: 6 x 10
##   carat       cut color clarity depth table price     x     y     z
##   <dbl>     <ord> <ord>   <ord> <dbl> <dbl> <int> <dbl> <dbl> <dbl>
## 1  0.23      Good     E     VS1  56.9    65   327  4.05  4.07  2.31
## 2  0.31      Good     J     SI2  63.3    58   335  4.34  4.35  2.75
## 3  0.24 Very Good     J    VVS2  62.8    57   336  3.94  3.96  2.48
## 4  0.24 Very Good     I    VVS1  62.3    57   336  3.95  3.98  2.47
## 5  0.26 Very Good     H     SI1  61.9    55   337  4.07  4.11  2.53
## 6  0.23 Very Good     H     VS1  59.4    61   338  4.00  4.05  2.39
```

A boolean vector can be converted back to a numerical position vector with `which`


```r
x > 10
```

```
##   adam  steve  harry    ted watson 
##  FALSE  FALSE   TRUE   TRUE   TRUE
```

```r
which(x > 10)
```

```
##  harry    ted watson 
##      3      4      5
```

### Assignment with Indexing

Sometimes we need to change some values in an existing object, but all assignments to existing objects have to have the same shape and class. If you give something too small, it will be repeated Rather than having to create a new object, we can assign to an indexed subset of an object


```r
friends <- data.frame(names=c("toothbrush", "vacuum", "blinds"),
                      hobbies=c("brushing", "spinning", "darkening"),
                      favorites=c(1,2,3))

# too small :(
friends$favorites <- c(4,5)
```

```
## Error in `$<-.data.frame`(`*tmp*`, favorites, value = c(4, 5)): replacement has 2 rows, data has 3
```

```r
# toothbrush and vacuum get a demotion
friends[c(1,2),]$favorites <- c(4,5)
```
    
## Manipulating Data

Now that you have your data in R, and have maybe subsetted it in some manner, you may want to manipulate the data. In this section, we'll cover some ways that you might want to manipulate data, including manipulating the format of the data and creating new variables (both from combining/splitting existing variables, and creating new ones if you need to).

### Wide vs. Long Data
Let's start by talking about one of the more common data format issues, which is whether data are structured in a *long* or *wide* format. These, like basically all things, exist on a continuum (data is almost always somewhere in between the extremes of long and wide). So, it's probably helpful to start with an example. 

One common design that leads to data easily represented as wide or long is longitudinal data. Let's say we administer the Big Five Inventory - 2 (BFI-2; Soto & John, 2016) to a sample of incoming freshman annually for 4 years. To keep things simple, let's say we only have that data (nothing else was administered). In this case, you may have a dataset that looks like this:

```
## Loading tidyverse: ggplot2
## Loading tidyverse: tibble
## Loading tidyverse: tidyr
## Loading tidyverse: readr
## Loading tidyverse: purrr
```

```
## Conflicts with tidy packages ----------------------------------------------
```

```
## filter(): dplyr, stats
## lag():    dplyr, stats
```

```
## # A tibble: 100 x 21
##      pid agreeableness_t1 conscientiousness_t1 neuroticism_t1
##    <dbl>            <dbl>                <dbl>          <dbl>
##  1     1        3.5710991             2.012590      2.2739385
##  2     2        2.4861884             2.716682      0.7230190
##  3     3        2.7991269             2.960659      1.5547346
##  4     4        2.2912445             1.921681      2.3406418
##  5     5        3.5952506             4.121713      2.5913933
##  6     6        2.0502889             2.498218      1.8027481
##  7     7        3.2338666             1.726816      4.0037362
##  8     8        4.6193345             2.605050      0.7286003
##  9     9        3.1226442             2.001192      4.5970994
## 10    10        0.3826938             1.717316      2.1325427
## # ... with 90 more rows, and 17 more variables: extraversion_t1 <dbl>,
## #   openness_t1 <dbl>, agreeableness_t2 <dbl>, conscientiousness_t2 <dbl>,
## #   neuroticism_t2 <dbl>, extraversion_t2 <dbl>, openness_t2 <dbl>,
## #   agreeableness_t3 <dbl>, conscientiousness_t3 <dbl>,
## #   neuroticism_t3 <dbl>, extraversion_t3 <dbl>, openness_t3 <dbl>,
## #   agreeableness_t4 <dbl>, conscientiousness_t4 <dbl>,
## #   neuroticism_t4 <dbl>, extraversion_t4 <dbl>, openness_t4 <dbl>
```

```
## Classes 'tbl_df', 'tbl' and 'data.frame':	100 obs. of  21 variables:
##  $ pid                 : num  1 2 3 4 5 6 7 8 9 10 ...
##  $ agreeableness_t1    : num  3.57 2.49 2.8 2.29 3.6 ...
##  $ conscientiousness_t1: num  2.01 2.72 2.96 1.92 4.12 ...
##  $ neuroticism_t1      : num  2.274 0.723 1.555 2.341 2.591 ...
##  $ extraversion_t1     : num  3.03 3.16 4.2 3.74 3.78 ...
##  $ openness_t1         : num  3.01 2.73 4.77 5.51 2.18 ...
##  $ agreeableness_t2    : num  4.05 3.47 3.13 3.23 4.21 ...
##  $ conscientiousness_t2: num  2.43 3.11 3.42 2.67 4.6 ...
##  $ neuroticism_t2      : num  2.71 1.49 2.23 3.11 2.75 ...
##  $ extraversion_t2     : num  3.21 3.74 4.51 4.19 4.5 ...
##  $ openness_t2         : num  3.49 3.15 5.31 5.95 2.88 ...
##  $ agreeableness_t3    : num  4.59 3.96 3.59 3.74 4.83 ...
##  $ conscientiousness_t3: num  2.62 3.6 4.06 3 4.83 ...
##  $ neuroticism_t3      : num  3.03 2.02 2.66 3.54 3.12 ...
##  $ extraversion_t3     : num  3.8 4.14 5.08 4.52 5.01 ...
##  $ openness_t3         : num  4.05 3.38 5.96 6.44 3.33 ...
##  $ agreeableness_t4    : num  5.02 3.94 4.02 4.46 5.13 ...
##  $ conscientiousness_t4: num  3.19 4.26 4.82 3.7 5.34 ...
##  $ neuroticism_t4      : num  3.58 2.63 3.33 4.2 3.59 ...
##  $ extraversion_t4     : num  4.33 4.73 5.5 5.16 5.68 ...
##  $ openness_t4         : num  4.68 4.03 6.9 6.65 4.11 ...
```

As you can see in the above, we have a row for each participant, and a column corresponding to each Big 5 score at each time point. For example, column 2 contains each participant's score on Agreeableness at time 1, column 7 contains each participant's score on Agreeableness at time 2, etc.

This data is in a *wide* format. It's also in basically the widest format it could be in for any reasonable purpose; the only way to make it wider would be to have a single row, and a column for each participant X Big 5 score X time point.

What would make this data *long*? There are two dimensions that we could lengthen the data on, but the more common one you might see is time. That is, we'd have a row for each participant X time point combination, like the following:


So we now have a row for each participant at each time point, and a column for each of the Big 5. We could also make it a little longer, by making each row correspond to a score on each Big Five trait for each participant at each time point, like so:


```r
sample_b5_data_long %>%
  gather(trait, score, agreeableness:openness)
```

```
## # A tibble: 2,000 x 4
##      pid  time         trait    score
##    <dbl> <chr>         <chr>    <dbl>
##  1     1    t1 agreeableness 3.571099
##  2     1    t2 agreeableness 4.049370
##  3     1    t3 agreeableness 4.587412
##  4     1    t4 agreeableness 5.020025
##  5     2    t1 agreeableness 2.486188
##  6     2    t2 agreeableness 3.469271
##  7     2    t3 agreeableness 3.963154
##  8     2    t4 agreeableness 3.935214
##  9     3    t1 agreeableness 2.799127
## 10     3    t2 agreeableness 3.127289
## # ... with 1,990 more rows
```

### Piping syntax

Before we get into reshaping data, its importantt we cover a type of syntax called piping. Piping is not unique to R, but it is highly useful in R, especially for data wrangling and manipulation. The pipe itself is this symbol `%>%`, and in R, it comes from the *magrittr* package (this is a references to the famous painging by Magritte,the treachery of images). *magrittr* is part of the tidyverse, so it will load when you call the tidyverse library.

#### A quick sidenote about the term pipe

As mentioned above, a pipe in piping syntax is symbolized by `%>%`. However, another character is sometimes called a pipe, which is the vertical bar |, and this is used quite a bit in logical/boolean operations (| means or in logical statements).

#### The logic of piping syntax

The general idea of piping syntax, is that we have some function on the lefthand and righthand side of the pipe. The function on the leftside is evaluated, and then the **output** of that function is passed to the function on the righthand side of the pipe as the first argument of that function. Let's start with a simple example. We'll create a vector of values and take the mean.


```r
# first set.seed, because we're going to be sampling values. 
# this will make it so we always get the same result.
set.seed(100)
# first create a vector using rnorm. This is saying give me a random sample of 20 observations, drawn from a normal distribution with a mean of 5 and sd of 1.
rnorm(n = 20, mean = 5, sd = 1) %>% mean()
```

```
## [1] 5.107867
```
As you can see, on the lefthand side of the pipe `%>%`, we have a function that create a vector of 20 values (sampled from a normal distribution,  with a mean of 5 and an sd of 1). On the righthand side, we simply have the function mean(), which calculates the mean of a vector of values. And so what's happening, is first the function on the righthand side creates a vector; the output of this function is simply the vector of values:

```r
set.seed(100)
rnorm(n=20, mean = 5, sd = 1)
```

```
##  [1] 4.497808 5.131531 4.921083 5.886785 5.116971 5.318630 4.418209
##  [8] 5.714533 4.174741 4.640138 5.089886 5.096274 4.798366 5.739840
## [15] 5.123380 4.970683 4.611146 5.510856 4.086186 7.310297
```

So the output of this fucntion, the vector of 20 values, is then passed along to the mean function. The mean function requires a vector of values for its first argument (see for yourself by running `?mean`). So, we end the pipe with simply `mean()`, and this provides the mean. 

#### A quick tip about style

It's typically considered good practice to not have more than one pipe per line. So what I have above is considered bad coding style. So instead of

```r
set.seed(100)
rnorm(n = 20, mean = 5, sd = 1) %>% mean()
```

```
## [1] 5.107867
```
You should instead do this:

```r
set.seed(100)
rnorm(n = 20, mean = 5, sd = 1) %>%
  mean()
```

```
## [1] 5.107867
```

#### Logic of Piping Syntax continued

As we covered above, the function following the pipe takes the output of the preceding function as its **first** argument. In the preceding example, we didn't supply another argument, and so the righthand side of the pipe was simply `mean()`. Let's say, we wanted to specify an additional argument. One option in `mean()` is trim, which indicates the proportion of values we want to trim from the vector before calculating the mean. Let's say we wanted only half the data points; I have no idea why someone would do something like this, especially with a vector of 20 values, but let's do it anyway.


```r
set.seed(100)
# This is the same as before
rnorm(n = 20, mean = 5, sd = 1) %>%
# Notice that the mean() function now contains the argument trim = 0.5
    mean(trim = 0.5)
```

```
## [1] 5.09308
```

You'll see we got a slightly different value; a value a little closer to the population mean of 5, since 50% of the values in the tails were removed from the vectore before calculating the mean.

This can be pretty confusing when you're new to piping, because our `mean()` function is basically missing an argument, namely, the argument with the name of the vector of values. Importantly, the argument isn't missing, it's just sort of hidden under the hood. The `mean()` line above, to R, is actually looking something like this: `mean(vector_output_above, trim = 0.5)`, it's just that the first argument is hidden from view. We could do this in two steps, without pipes.


```r
set.seed(100)
# we could create the vectore, and save it as an object
vector_from_pipe <- rnorm(n = 20, mean = 5, sd = 1)
# and then take the mean
mean(vector_from_pipe, trim = .5)
```

```
## [1] 5.09308
```
And we get the same trimmed mean. We could also do this with one step:

```r
set.seed(100)
mean(rnorm(n = 20, mean = 5, sd = 1), trim = .5)
```

```
## [1] 5.09308
```
And we can see that we get the same result. Some of you may have noticed that we have three different routes to the same destination. So why use pipes?

### Why use pipes?

There are probably lots of reasons to use or not use pipes. The most important and most often mentioned reasons to use pipes are *cleanliness* (which I hear is next to *godliness*) and efficiency. We can think of three related advantages:

1. Cleaner code
    * This is nice, because it helps make your code more readable by other humans.
    * Other humans include your future self, who may return to a code file 6 months or a year later, and want to know wtf the code is doing.

2. Cleaner environment
    * When you use pipes, you have basically no reason to save objects from intermediary steps in your data wrangling / analysis workflow, because you can just pass output from function to function without saving it (recall when we broke apart the pipe into two steps above).

3. Efficiency
    * This is efficiency for you, the person doing the coding (not more efficient computing)
    * This advantage primarily comes from the fact that withhout pipes, you have to save objects from intermediary steps. This can create ineffencies and/or errors:
        * Inefficiences stem from having to keep coming up with names for objects from intermediary steps.
        * errors can crop up from re-using names (to be more efficient), which can easily lead to calling the wrong object in an analysis or over-writing one that you need later.

#### Cleaner code

With pipes, its often easier to make code that is cleaner, and easier for other humans to read. The last part is important, because R doesnt care if your code is clean (as long as its syntactically correct), but clean code is easier for your collaborators (including future you) to understand.

Why do pipes make cleaner code? An example may help with this. Compare this:

```r
set.seed(100)

mean(rnorm(n = 20, mean = 5, sd = 1), trim = .5)
```
to this:

```r
set.seed(100)

rnorm(n = 20, mean = 5, sd = 1) %>%
    mean(trim = 0.5)
```

The second, piped, example is a little easier to parse. One reason is it more closely matches the way we tend to think about analysis. In the former example, we're saying take a mean, and what I want you to take a mean of is this vector, and this is how I want you to make this vector (random sample from a normal dist, etc.). In the latter, we're saying make a vector like this (random sample, etc.), and then take its mean. I find the latter is more similar to the way I think about the steps in an analysis.

#### A cleaner environment

This is a smaller and less important advantage, but  an advantage none the less. Not having to save objects from intermediary steps means less stuff crowding up you environment. Now, you don't have to save those intermediary steps, but doing so will often make cleaner code. Pipes maximize cleanliness of both your code and environment, so with pipes you don't have to make this trade-off. Recall this example:

```r
set.seed(100)
# we could create the vectore, and save it as an object
vector_from_pipe <- rnorm(n = 20, mean = 5, sd = 1)
# and then take the mean
mean(vector_from_pipe, trim = .5)
```
We saved the vector me made with `rnorm()` so that we could pass that vector to the `mean` function. Now this code is a little easier to read than this code, that doesn't save the object:

```r
mean(rnorm(n = 20, mean = 5, sd = 1), trim = .5)
```
So the first chunk is easier to read, but clutters our environment, while the second chunk is harder to read but creates less clutter. Which is better? Who know. Pipes avoid making this tough decision, by making cleaner code that doesn't clutter up our environment:

```r
rnorm(n = 20, mean = 5, sd = 1) %>%
  mean()
```

You may be wondering why a cluttered environment matters. The main thing is that it can create ineffeciencies in two ways: 
1. sometimes you want to quickly glance over to your environment window to get some information (like what you named that dataset); this is obviously harder in a busier visual environment. 
2. You can hit tab to autocomplete; the more objects with similar names you have, the less efficient autocomplete is.

####Efficiency

This one is pretty straight forward. By not having to save objects from intermediary steps, we have to come up with less names and remember less names. Coming up with names is hard; remembering what names correspond to what is harder. Both of these things will slow you down.

For example, we had to name the vector when we saved it in the above example; this added about 2 seconds to my work in preparing this code. Say we needed a new vector, that was the log transformed values of the previous vetor. We might name that `log_vector_from_pipe`; this probably added another 2 seconds. Now obviously, these are small additions of time, but they add up. Plus, sometimes coming up with unique names gets tough (when you're on the 5th or 6th or 10th vector that you have to name)

#### A note about Scaling
One more thing to keep in mind is that gains in cleanliness and efficiency scale with the complexity of what you're doing. Let's say, we had a dataset that we wanted to take a subset of cases/rows from (those where age is > 25), and just a couple of just a couple of columns (a, b, c, d), then we wanted to log transform that data, and then wanted to get descriptives for all of the remaining variables.

Without pipes, you'll either end up with some difficult to read code:

```r
library(psych)
describe(log(select(filter(data, age > 5), a, b, c, d)))
```
or some throwaway objects:

```r
library (psych)
data_subset_age <- filter(data, age > 5)
data_subset_age_abcd <- select(data_subset_age, a, b, c, d)
log_data_subset_age_abcd <- log(data_subset_age_abcd)
describe(log_data_subset_age_abcd)
```
With pipes, we can avoid these issues:

```r
data %>%
  filter(age > 25) %>%
  select(a, b, c, d) %>%
  describe()
```
See, so much easier to read, and not flooding our enviornment with clutter and not taxing our already taxed minds with having to come up with a bunch of damn names.

### Saving the output of your pipe
So far, we've been runing with example where we just ran functions in pipes without saving the output. Sometimes this is useful, like if we just wanted to see the descriptives in the above example (and didn't want to do anything else with those descriptives). But, often we want to take the output from the pipe and save it to an object so we can do analyses with it later. For example, instead of just getting descriptives, maybe we want a dataset that has only people above 25 and columns a, b, c, d to use in analyses. We just add the assignment and object name at the top, like so:


```r
age25plus_abcd <- data %>%
  filter(age > 25) %>%
  select(a, b, c, d)
```

## Reshaping Data

At the start of the data manipulation section, we talked about long vs wide data. Now let's talk about how to actually lengthen or widen or data. We'll use the same dataset we made up for the starting example: 4 year longitudinal study where the Big Five were measured annually. 

### Wide to Long
We'll start with lengthening our data. We currently have our data in a wide format that looks something like this for now:

```r
sample_b5_data_wide
```

```
## # A tibble: 100 x 21
##      pid agreeableness_t1 conscientiousness_t1 neuroticism_t1
##    <dbl>            <dbl>                <dbl>          <dbl>
##  1     1        3.5710991             2.012590      2.2739385
##  2     2        2.4861884             2.716682      0.7230190
##  3     3        2.7991269             2.960659      1.5547346
##  4     4        2.2912445             1.921681      2.3406418
##  5     5        3.5952506             4.121713      2.5913933
##  6     6        2.0502889             2.498218      1.8027481
##  7     7        3.2338666             1.726816      4.0037362
##  8     8        4.6193345             2.605050      0.7286003
##  9     9        3.1226442             2.001192      4.5970994
## 10    10        0.3826938             1.717316      2.1325427
## # ... with 90 more rows, and 17 more variables: extraversion_t1 <dbl>,
## #   openness_t1 <dbl>, agreeableness_t2 <dbl>, conscientiousness_t2 <dbl>,
## #   neuroticism_t2 <dbl>, extraversion_t2 <dbl>, openness_t2 <dbl>,
## #   agreeableness_t3 <dbl>, conscientiousness_t3 <dbl>,
## #   neuroticism_t3 <dbl>, extraversion_t3 <dbl>, openness_t3 <dbl>,
## #   agreeableness_t4 <dbl>, conscientiousness_t4 <dbl>,
## #   neuroticism_t4 <dbl>, extraversion_t4 <dbl>, openness_t4 <dbl>
```
And say we want it to lengthen it to look like this:

```
## # A tibble: 400 x 7
##      pid  time agreeableness conscientiousness extraversion neuroticism
##  * <dbl> <chr>         <dbl>             <dbl>        <dbl>       <dbl>
##  1     1    t1      3.571099          2.012590     3.027438    2.273939
##  2     1    t2      4.049370          2.427314     3.206960    2.706199
##  3     1    t3      4.587412          2.615689     3.798053    3.029448
##  4     1    t4      5.020025          3.185043     4.327147    3.584915
##  5     2    t1      2.486188          2.716682     3.157628    0.723019
##  6     2    t2      3.469271          3.108198     3.740212    1.492760
##  7     2    t3      3.963154          3.595192     4.138764    2.023065
##  8     2    t4      3.935214          4.257813     4.732740    2.632811
##  9     3    t1      2.799127          2.960659     4.200874    1.554735
## 10     3    t2      3.127289          3.421974     4.507929    2.230967
## # ... with 390 more rows, and 1 more variables: openness <dbl>
```

We can use a function called `gather()` from the *tidyr* package (another package in the tidyverse). `gather()` requires at least three arguments:

* data
    * This one is easy. It is the dataset that we want to lengthen
* key
    * This is the name for the new variable (or new column) that will contain the information that was previously in the column names. In most cases, it will be the name for the new variable that contains the name for what used to be variables.
    * In our example, this column will contain the variables agreeableness_t1, conscientiousness_t1, etc.
    * You can give any name you want here.
* value
    * this is the name of the new variable (or new column) that will contain the scores that used to be spread out across different columns.

And that's all that is required.

The most important remaining part of this syntax is the columns that you want to *gather*, or the variables that you want to become key-value pairs. Another way to put this is which columns do you want to become rows (lengthening the data)? 

If you don't name any columns, it will select all of them. The syntax for this part works similarly to `select()`. You can name a series of variables, like `agreeableness_t1:openness_t4` , or name each column individually separated by a comma `agreeabeleness_t1, conscientiousness, ..., openness_t4`, or you can say which columns you *do not* want with a minus sign in front of the column name. Since we want to include all of our columns except participant id (`pid`), we'll use the minus sign to tell `gather()` include everything but `pid` 

```r
sample_b5_data_wide %>%
  gather(variable, score, -pid)
```

```
## # A tibble: 2,000 x 3
##      pid         variable     score
##    <dbl>            <chr>     <dbl>
##  1     1 agreeableness_t1 3.5710991
##  2     2 agreeableness_t1 2.4861884
##  3     3 agreeableness_t1 2.7991269
##  4     4 agreeableness_t1 2.2912445
##  5     5 agreeableness_t1 3.5952506
##  6     6 agreeableness_t1 2.0502889
##  7     7 agreeableness_t1 3.2338666
##  8     8 agreeableness_t1 4.6193345
##  9     9 agreeableness_t1 3.1226442
## 10    10 agreeableness_t1 0.3826938
## # ... with 1,990 more rows
```

Okay, so this brings us to our first issue when trying to lengthen a dataset. The key column (what we called variable) contains the trait that was measured and the time point. That is, this column contains 2 pieces of information that we probably want kept separately. We want these to be in separate columns, so that we can select certain traits or time points, or model slopes across time points.

Thankfully, the authors of tidyr anticipated this problem, and created a useful function called `separate()`, which basically takes data within a single column, and splits it into multiple columns. Let's add that into the pipeline on the variable column. We're going to throw a pipe on the end of gather into separate. This will put the dataset above as the first argument in the separate function. We then tell it the columns we want to separate (variable in this case) and provide it the names of the new columns, which we'll call trait and time, for Big Five trait and time point.

```r
sample_b5_data_wide %>%
  gather(variable, score, -pid) %>%
  separate(variable, c("trait", "time"))
```

```
## # A tibble: 2,000 x 4
##      pid         trait  time     score
##  * <dbl>         <chr> <chr>     <dbl>
##  1     1 agreeableness    t1 3.5710991
##  2     2 agreeableness    t1 2.4861884
##  3     3 agreeableness    t1 2.7991269
##  4     4 agreeableness    t1 2.2912445
##  5     5 agreeableness    t1 3.5952506
##  6     6 agreeableness    t1 2.0502889
##  7     7 agreeableness    t1 3.2338666
##  8     8 agreeableness    t1 4.6193345
##  9     9 agreeableness    t1 3.1226442
## 10    10 agreeableness    t1 0.3826938
## # ... with 1,990 more rows
```

You'll notice we didn't tell separate anything other than the column we wanted separated (`variable`) and the name of two new columns (`trait` and `time`). How did it know how to separate that column?

Well, basically, separate looks for something called *regular expressions*. We'll get into more detail about regular expressions later on, but one regular expression is the underscore for separating things. So it recognized that every string in the variable column contained a underscore. Then, it put the character string on the left hand side of the underscore into the first column name we gave it (trait) and put the character string on the right hand side of the underscore into the second column name we gave it (time). If we wanted to be more explicit, we could specify the `sep=` argument like so:


```r
# We're going to save this to an object called sample_b5_data_long
sample_b5_data_long<- sample_b5_data_wide %>%
  gather(variable, score, -pid) %>%
  separate(variable, c("trait", "time"), sep = "_")
sample_b5_data_long
```

```
## # A tibble: 2,000 x 4
##      pid         trait  time     score
##  * <dbl>         <chr> <chr>     <dbl>
##  1     1 agreeableness    t1 3.5710991
##  2     2 agreeableness    t1 2.4861884
##  3     3 agreeableness    t1 2.7991269
##  4     4 agreeableness    t1 2.2912445
##  5     5 agreeableness    t1 3.5952506
##  6     6 agreeableness    t1 2.0502889
##  7     7 agreeableness    t1 3.2338666
##  8     8 agreeableness    t1 4.6193345
##  9     9 agreeableness    t1 3.1226442
## 10    10 agreeableness    t1 0.3826938
## # ... with 1,990 more rows
```
See, that did the same thing. Now, let's save this long data, and see what we have to do to get it wide again.

### Long to wide

I will say, that my one mild frustration with `tidyr()`, with all due respect, is that it seems to be optimized to getting data into long format. I take it, from reading R for Data Science <r4ds.had.co.nz> , that Hadley Wickham and company prefer long data (my sense is that they consider data *tidy* when its in its longest format, though perhaps I misread).

Long data is great and all, but sometimes we need wide data. Specifically, we need wide data for **SEM** and **Path Analysis**, which we use pretty regularly in psychology.

All of that said, you can use `spread()` from tidyr to widen your data. There is also the `reshape()` function from base R, which I sometimes find makes widening data a little easier than `spread()`. Let's go over each in turn.

#### Spread

Spread is part of the `tidyr()` package, and is the function from that package for reshaping data from long to wide format. It's syntactically pretty similar to `spread()` making it pretty easy to go between the two, which is nice. Spread requires 3 arguments, which you'll see mirror gather:

* Data
    * The data that you want to widen
* key
    * The column that contains the values that you want to be columns. This would be either the variable or time column from our above example dataset.
* value
    * The column that contains the values you want to fill the cells beneath the new columns with. This would be the score column from above.
    
Let's say we're starting with our data in a long format like the one above, and we wanted to get it to a wide format, like the one we started with (where each row was a participant, and each column was a trait at a particular time, like `agreeableness_t1`). We might start with this

```r
sample_b5_data_long2 <- sample_b5_data_long %>%
  spread(trait, score)

sample_b5_data_long2 
```

```
## # A tibble: 400 x 7
##      pid  time agreeableness conscientiousness extraversion neuroticism
##  * <dbl> <chr>         <dbl>             <dbl>        <dbl>       <dbl>
##  1     1    t1      3.571099          2.012590     3.027438    2.273939
##  2     1    t2      4.049370          2.427314     3.206960    2.706199
##  3     1    t3      4.587412          2.615689     3.798053    3.029448
##  4     1    t4      5.020025          3.185043     4.327147    3.584915
##  5     2    t1      2.486188          2.716682     3.157628    0.723019
##  6     2    t2      3.469271          3.108198     3.740212    1.492760
##  7     2    t3      3.963154          3.595192     4.138764    2.023065
##  8     2    t4      3.935214          4.257813     4.732740    2.632811
##  9     3    t1      2.799127          2.960659     4.200874    1.554735
## 10     3    t2      3.127289          3.421974     4.507929    2.230967
## # ... with 390 more rows, and 1 more variables: openness <dbl>
```
Okay, that got us about halfway there. We now have a separate column for each trait, but time is still row-wise. Maybe we could try spreading time, but what would be the value column? We currently have 5. Let's try passing it all of them.

```r
sample_b5_data_long %>%
  spread(time, score) %>%
  spread(trait, agreeableness:openness) # note that if you pass it a vector of names like c("agreeableness", etc.) it also won't work
```

We can't do that; we can only give exactly **one** column for the value argument in spread. Herein lies my frustration with spread. Now there is a workaround. 

If you're data is in the longer format from above (where each row was a participant X trait X time combo), then you can first use `unite()` (the complement to separate). `unite()` requires an argument for data and the column name you want it to add (once it unites the columns you tell it to unite). You'll probably also want to name specific columns, which can be done just like other tidyr functions, by listing their unquoted names (or if you have a range of columns, you can use column_name_at_start_of_range:column_name_at_end_of_range). 

So, we'll add in a unite before we get to spread, and see if that fixes it:


```r
sample_b5_data_long %>%
  unite(variable, trait, time) %>%
  spread(variable, score)
```

```
## # A tibble: 100 x 21
##      pid agreeableness_t1 agreeableness_t2 agreeableness_t3
##  * <dbl>            <dbl>            <dbl>            <dbl>
##  1     1        3.5710991         4.049370         4.587412
##  2     2        2.4861884         3.469271         3.963154
##  3     3        2.7991269         3.127289         3.589278
##  4     4        2.2912445         3.230835         3.735091
##  5     5        3.5952506         4.212267         4.834904
##  6     6        2.0502889         2.663883         3.054800
##  7     7        3.2338666         3.812506         4.474634
##  8     8        4.6193345         5.033322         5.655779
##  9     9        3.1226442         3.884274         4.459365
## 10    10        0.3826938         1.137798         1.476757
## # ... with 90 more rows, and 17 more variables: agreeableness_t4 <dbl>,
## #   conscientiousness_t1 <dbl>, conscientiousness_t2 <dbl>,
## #   conscientiousness_t3 <dbl>, conscientiousness_t4 <dbl>,
## #   extraversion_t1 <dbl>, extraversion_t2 <dbl>, extraversion_t3 <dbl>,
## #   extraversion_t4 <dbl>, neuroticism_t1 <dbl>, neuroticism_t2 <dbl>,
## #   neuroticism_t3 <dbl>, neuroticism_t4 <dbl>, openness_t1 <dbl>,
## #   openness_t2 <dbl>, openness_t3 <dbl>, openness_t4 <dbl>
```
Okay, that got our data into the right format. So, spread isn't *that* bad, but you typically need to start with data in (just about) the longest format possible, and to use `unite()` function as well.

But, sometimes our data are in between the longest format possible and the wide format that we want. Sometimes we get data like we accidentally got above, where each row is a participant X time point and each column is trait. Like this:


```r
sample_b5_data_long2
```

```
## # A tibble: 400 x 7
##      pid  time agreeableness conscientiousness extraversion neuroticism
##  * <dbl> <chr>         <dbl>             <dbl>        <dbl>       <dbl>
##  1     1    t1      3.571099          2.012590     3.027438    2.273939
##  2     1    t2      4.049370          2.427314     3.206960    2.706199
##  3     1    t3      4.587412          2.615689     3.798053    3.029448
##  4     1    t4      5.020025          3.185043     4.327147    3.584915
##  5     2    t1      2.486188          2.716682     3.157628    0.723019
##  6     2    t2      3.469271          3.108198     3.740212    1.492760
##  7     2    t3      3.963154          3.595192     4.138764    2.023065
##  8     2    t4      3.935214          4.257813     4.732740    2.632811
##  9     3    t1      2.799127          2.960659     4.200874    1.554735
## 10     3    t2      3.127289          3.421974     4.507929    2.230967
## # ... with 390 more rows, and 1 more variables: openness <dbl>
```

What do we do here? Well, if we want to use gather, we'll have to first lengthen the data, and unite the columns that we need to unite, and then spread. So it will something like this:


```r
sample_b5_data_long2 %>%
  # first gather (you'll have to remember to not gather pid AND time)
  gather(trait, score, -pid, -time) %>%
  # Then unite trait and time into a column called variable
  unite(variable, trait, time) %>%
  # then spread it
  spread(variable, score)
```

```
## # A tibble: 100 x 21
##      pid agreeableness_t1 agreeableness_t2 agreeableness_t3
##  * <dbl>            <dbl>            <dbl>            <dbl>
##  1     1        3.5710991         4.049370         4.587412
##  2     2        2.4861884         3.469271         3.963154
##  3     3        2.7991269         3.127289         3.589278
##  4     4        2.2912445         3.230835         3.735091
##  5     5        3.5952506         4.212267         4.834904
##  6     6        2.0502889         2.663883         3.054800
##  7     7        3.2338666         3.812506         4.474634
##  8     8        4.6193345         5.033322         5.655779
##  9     9        3.1226442         3.884274         4.459365
## 10    10        0.3826938         1.137798         1.476757
## # ... with 90 more rows, and 17 more variables: agreeableness_t4 <dbl>,
## #   conscientiousness_t1 <dbl>, conscientiousness_t2 <dbl>,
## #   conscientiousness_t3 <dbl>, conscientiousness_t4 <dbl>,
## #   extraversion_t1 <dbl>, extraversion_t2 <dbl>, extraversion_t3 <dbl>,
## #   extraversion_t4 <dbl>, neuroticism_t1 <dbl>, neuroticism_t2 <dbl>,
## #   neuroticism_t3 <dbl>, neuroticism_t4 <dbl>, openness_t1 <dbl>,
## #   openness_t2 <dbl>, openness_t3 <dbl>, openness_t4 <dbl>
```
So that isn't sooooo bad. But, it can be a little cumbersome, because you're using several different functions together. So to do this right, you have to remember a pretty complex sequence (or look it up each time). Plus, when your data is more complicated than this data (which was intentionally made pretty simple), it becomes pretty easy to make a mistake in one of these steps (not remembering which columns not to gather in step 2 above, remembering to unite the right columns, etc.). So, `reshape()` from base R is sometimes a little easier in these cases. Let's talk about that now.

#### Using reshape(): long to wide

In general, `reshape()` is a little more complicated. It has more arguments, and more of the arguments are required. But, like I said, it is sometimes a little easier for widening data than `spread()`. One thing to keep in mind is, unlike `spread()` and `gather()`, `reshape()` is used for widening or lengthening data, and you have to specify the direction in the function.

Let's say our data look like the intermediate data we've used before. I mean this data shape:

```r
sample_b5_data_long2
```

```
## # A tibble: 400 x 7
##      pid  time agreeableness conscientiousness extraversion neuroticism
##  * <dbl> <chr>         <dbl>             <dbl>        <dbl>       <dbl>
##  1     1    t1      3.571099          2.012590     3.027438    2.273939
##  2     1    t2      4.049370          2.427314     3.206960    2.706199
##  3     1    t3      4.587412          2.615689     3.798053    3.029448
##  4     1    t4      5.020025          3.185043     4.327147    3.584915
##  5     2    t1      2.486188          2.716682     3.157628    0.723019
##  6     2    t2      3.469271          3.108198     3.740212    1.492760
##  7     2    t3      3.963154          3.595192     4.138764    2.023065
##  8     2    t4      3.935214          4.257813     4.732740    2.632811
##  9     3    t1      2.799127          2.960659     4.200874    1.554735
## 10     3    t2      3.127289          3.421974     4.507929    2.230967
## # ... with 390 more rows, and 1 more variables: openness <dbl>
```

And we wanted it to look like our dataset above, where each row is a unique participant. We could use reshape like so

```r
reshape(sample_b5_data_long2,
        # This one is straight forward; just the column with participant id's
        idvar = "pid",
        # This won't always be time. timevar is the variable you want to move from rows into column names.
        # It's equivalen to key in the spread() function
        timevar = "time",
        # Gotta tell it what direction to go.
        direction = "wide",
        # Note, this is fully optional, but the defailt is ".", and I wanted it to look like the above, with a "_"
        sep = "_")
```

```
## # A tibble: 100 x 6
##      pid `agreeableness_c("t1", "t2", "t3", "t4")`
##    <dbl>                                     <dbl>
##  1     1                                        NA
##  2     2                                        NA
##  3     3                                        NA
##  4     4                                        NA
##  5     5                                        NA
##  6     6                                        NA
##  7     7                                        NA
##  8     8                                        NA
##  9     9                                        NA
## 10    10                                        NA
## # ... with 90 more rows, and 4 more variables: `conscientiousness_c("t1",
## #   "t2", "t3", "t4")` <dbl>, `extraversion_c("t1", "t2", "t3",
## #   "t4")` <dbl>, `neuroticism_c("t1", "t2", "t3", "t4")` <dbl>,
## #   `openness_c("t1", "t2", "t3", "t4")` <dbl>
```

Holy sh**, what happened!?!?! What did I do

This brings us to what has got to be the most common mistake made with `reshape()` in modern times (post tidyverse times, that is). The reason we got that hot mess above is beecause reshape *only* works with data.frames **NOT** tibbles. Now our data set `sample_b5_data_long2` is probably a tibble. Let's check that out with the structure function `str()`


```r
str(sample_b5_data_long2)
```

```
## Classes 'tbl_df', 'tbl' and 'data.frame':	400 obs. of  7 variables:
##  $ pid              : num  1 1 1 1 2 2 2 2 3 3 ...
##  $ time             : chr  "t1" "t2" "t3" "t4" ...
##  $ agreeableness    : num  3.57 4.05 4.59 5.02 2.49 ...
##  $ conscientiousness: num  2.01 2.43 2.62 3.19 2.72 ...
##  $ extraversion     : num  3.03 3.21 3.8 4.33 3.16 ...
##  $ neuroticism      : num  2.274 2.706 3.029 3.585 0.723 ...
##  $ openness         : num  3.01 3.49 4.05 4.68 2.73 ...
```

Yep, it looks like it's class is tbl_df, tbl, and data.frame, which means its a tibble. Thankfully, this is an easy fix, you just need to turn the tibble into a data.frame with `as.data.frame()`. So let's add that into the pipeline.


```r
sample_b5_data_long2 %>%
  as.data.frame() %>%
  # Note, because we're piping things, we start with 'idvar=' instead of the name of the data
  reshape(idvar = "pid",
          timevar = "time",
          direction = "wide",
          # Note, this is fully optional, but the defailt is ".", and I wanted it to look like the above, with a "_"
          sep = "_") %>%
  # Turning it back into a tibble, because they print a little nicer
  as_tibble()
```

```
## # A tibble: 100 x 21
##      pid agreeableness_t1 conscientiousness_t1 extraversion_t1
##  * <dbl>            <dbl>                <dbl>           <dbl>
##  1     1        3.5710991             2.012590        3.027438
##  2     2        2.4861884             2.716682        3.157628
##  3     3        2.7991269             2.960659        4.200874
##  4     4        2.2912445             1.921681        3.737496
##  5     5        3.5952506             4.121713        3.776066
##  6     6        2.0502889             2.498218        1.473851
##  7     7        3.2338666             1.726816        3.319454
##  8     8        4.6193345             2.605050        2.681124
##  9     9        3.1226442             2.001192        2.825523
## 10    10        0.3826938             1.717316        3.911606
## # ... with 90 more rows, and 17 more variables: neuroticism_t1 <dbl>,
## #   openness_t1 <dbl>, agreeableness_t2 <dbl>, conscientiousness_t2 <dbl>,
## #   extraversion_t2 <dbl>, neuroticism_t2 <dbl>, openness_t2 <dbl>,
## #   agreeableness_t3 <dbl>, conscientiousness_t3 <dbl>,
## #   extraversion_t3 <dbl>, neuroticism_t3 <dbl>, openness_t3 <dbl>,
## #   agreeableness_t4 <dbl>, conscientiousness_t4 <dbl>,
## #   extraversion_t4 <dbl>, neuroticism_t4 <dbl>, openness_t4 <dbl>
```

Whew, it worked! By learning this limitation of `reshape()`, you're avoiding a very confusing rabbit hole that I spent almost a full week in!

Side note: tidyverse functions will turn your data into tibbles, so be aware (the one exception is pipes; they don't do that).

#### Using reshape(): wide to long

Now, you can use `reshape()` to go from wide to long too. I'd recommend using `gather()` from tidyr for that instead, but for the sake of completeness, this is how it would work:


```r
sample_b5_data_wide %>%
    #never forget to make it a data.frame!
    as.data.frame() %>%
    # when going from wide to long, you have to tell it the varying variables.
    # These are the columns that contain the information you want in rows.
    # This is a little confusing, but basically, you give it a list, with vectors for each thing that shares that information.
    # So, all the t1 variables go into the same vector
    # then, all the t2 variables go into the same vector, etc.
    reshape(varying = c(c("agreeableness_t1", "conscientiousness_t1", "extraversion_t1", "neuroticism_t1", "openness_t1"),
                        c("agreeableness_t2", "conscientiousness_t2", "extraversion_t2", "neuroticism_t2", "openness_t2"),
                        c("agreeableness_t3", "conscientiousness_t3", "extraversion_t3", "neuroticism_t3", "openness_t3"),
                        c("agreeableness_t4", "conscientiousness_t4", "extraversion_t4", "neuroticism_t4", "openness_t4")),
            # sep is really important here, because it tells it where to split the columns
            sep = "_",
            # and of course, tell it the direction
            direction = "long") %>%
    # Turning it back into a tibble, because they print a little nicer
    as_tibble()
```

```
## # A tibble: 400 x 8
##      pid  time agreeableness conscientiousness extraversion neuroticism
##  * <dbl> <chr>         <dbl>             <dbl>        <dbl>       <dbl>
##  1     1    t1     3.5710991          2.012590     3.027438   2.2739385
##  2     2    t1     2.4861884          2.716682     3.157628   0.7230190
##  3     3    t1     2.7991269          2.960659     4.200874   1.5547346
##  4     4    t1     2.2912445          1.921681     3.737496   2.3406418
##  5     5    t1     3.5952506          4.121713     3.776066   2.5913933
##  6     6    t1     2.0502889          2.498218     1.473851   1.8027481
##  7     7    t1     3.2338666          1.726816     3.319454   4.0037362
##  8     8    t1     4.6193345          2.605050     2.681124   0.7286003
##  9     9    t1     3.1226442          2.001192     2.825523   4.5970994
## 10    10    t1     0.3826938          1.717316     3.911606   2.1325427
## # ... with 390 more rows, and 2 more variables: openness <dbl>, id <int>
```
There, that worked, but it was a little inefficient compared to `gather()` I think.

### Final note on reshaping data

So now you how to reshape your data. Some general tips to keep in mind:

* `gather()` is great for lengthening data; I would recommend it over `reshape()` everytime.
* `separate()` can be used to separate a column that really contains multiple pieces of information
* `spread()` works for getting widening your data, but it can require some in between steps.
* `gather()` can be used to combine information kept in two separate columns
    * remember, this is a big one for getting data into wide format with tidyr and `spread()`
* `reshape()` works really well for widening your data; I recommend it over `spread()` in most cases.
    * the exception being when your dataset is already in a long (and really, the longest reasonable) format.
* `reshape()` can be used to lengthen your data, but it's syntax is a little wonky

###

Things left for manipulating

* Manipulating
    * creating new variables with mutate()
    * creating summary/aggregate datasets with summarize()
        * combining group_by() with summarize, which is where summarize gets powerful.
    * reordering (incl. reordering factor levels, talking abt factors)
    * combining dataframes (if this makes sense to do/there's time to do it) (see relational data in r4ds)

* Annoyances
    * dealing with `NaN`s
    * typing your data (mainly to cover factors, ordered v. unordered)

### Resources

* R Data Import/Export Manual: https://cran.r-project.org/doc/manuals/R-data.pdf
* http://zoonek2.free.fr/UNIX/48_R/02.html#6
* https://www.statmethods.net/interface/io.html
* http://adv-r.had.co.nz/Subsetting.html#applications
* https://www.rstudio.com/wp-content/uploads/2015/02/data-wrangling-cheatsheet.pdf


```
## [1] TRUE
```

```
## [1] TRUE
```

```
## [1] TRUE
```

```
## [1] TRUE
```

```
## [1] TRUE
```

