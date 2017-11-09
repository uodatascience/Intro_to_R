---
title: "Data Wrangling"
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

## Loading

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

## Saving

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

## Piping Syntax

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

## Reshaping

At the start of the data manipulation section, we talked about long vs wide data. Now let's talk about how to actually lengthen or widen or data. We'll use the same dataset we made up for the starting example: 4 year longitudinal study where the Big Five were measured annually. 

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
##  1     1         2.561910             2.226287       1.482124
##  2     2         3.764061             3.424002       2.976117
##  3     3         3.261961             2.416053       3.026659
##  4     4         3.773405             3.415036       3.163681
##  5     5         2.185621             1.454738       3.410008
##  6     6         2.561549             2.481250       2.372637
##  7     7         2.279778             2.720208       3.636918
##  8     8         3.230945             4.007457       1.798065
##  9     9         1.842271             2.530430       4.346542
## 10    10         3.247076             3.297897       2.401233
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
##  $ agreeableness_t1    : num  2.56 3.76 3.26 3.77 2.19 ...
##  $ conscientiousness_t1: num  2.23 3.42 2.42 3.42 1.45 ...
##  $ neuroticism_t1      : num  1.48 2.98 3.03 3.16 3.41 ...
##  $ extraversion_t1     : num  1.78 3.89 3.25 2.93 3.2 ...
##  $ openness_t1         : num  3.28 3.82 2.57 3.57 3.18 ...
##  $ agreeableness_t2    : num  3.13 4.38 3.58 4.62 3 ...
##  $ conscientiousness_t2: num  3.05 3.89 2.84 4.35 1.93 ...
##  $ neuroticism_t2      : num  2.46 3.62 3.35 3.82 4.17 ...
##  $ extraversion_t2     : num  2.33 4.72 3.95 3.36 3.67 ...
##  $ openness_t2         : num  3.77 4.14 2.88 4.32 4.04 ...
##  $ agreeableness_t3    : num  3.64 4.89 4.19 5.1 3.39 ...
##  $ conscientiousness_t3: num  3.35 4.09 3.64 5.14 2.63 ...
##  $ neuroticism_t3      : num  3.37 4.16 3.91 4.42 4.97 ...
##  $ extraversion_t3     : num  2.67 4.96 4.4 4.05 4.04 ...
##  $ openness_t3         : num  4.28 4.63 3.48 4.92 4.29 ...
##  $ agreeableness_t4    : num  4.21 5.49 4.75 5.44 3.44 ...
##  $ conscientiousness_t4: num  3.79 4.55 3.9 5.94 3.35 ...
##  $ neuroticism_t4      : num  3.52 4.55 4.1 4.67 5.34 ...
##  $ extraversion_t4     : num  3.49 5.43 4.99 4.78 4.5 ...
##  $ openness_t4         : num  4.87 5.22 3.98 5.54 4.91 ...
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
##  1     1    t1 agreeableness 2.561910
##  2     1    t2 agreeableness 3.129489
##  3     1    t3 agreeableness 3.637314
##  4     1    t4 agreeableness 4.207066
##  5     2    t1 agreeableness 3.764061
##  6     2    t2 agreeableness 4.377288
##  7     2    t3 agreeableness 4.889805
##  8     2    t4 agreeableness 5.487121
##  9     3    t1 agreeableness 3.261961
## 10     3    t2 agreeableness 3.575117
## # ... with 1,990 more rows
```


### Wide to Long
We'll start with lengthening our data. We currently have our data in a wide format that looks something like this for now:

```r
sample_b5_data_wide
```

```
## # A tibble: 100 x 21
##      pid agreeableness_t1 conscientiousness_t1 neuroticism_t1
##    <dbl>            <dbl>                <dbl>          <dbl>
##  1     1         2.561910             2.226287       1.482124
##  2     2         3.764061             3.424002       2.976117
##  3     3         3.261961             2.416053       3.026659
##  4     4         3.773405             3.415036       3.163681
##  5     5         2.185621             1.454738       3.410008
##  6     6         2.561549             2.481250       2.372637
##  7     7         2.279778             2.720208       3.636918
##  8     8         3.230945             4.007457       1.798065
##  9     9         1.842271             2.530430       4.346542
## 10    10         3.247076             3.297897       2.401233
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
##  1     1    t1      2.561910          2.226287     1.777716    1.482124
##  2     1    t2      3.129489          3.054222     2.330667    2.459631
##  3     1    t3      3.637314          3.350964     2.672625    3.374978
##  4     1    t4      4.207066          3.790548     3.487093    3.517931
##  5     2    t1      3.764061          3.424002     3.891194    2.976117
##  6     2    t2      4.377288          3.888895     4.719864    3.623774
##  7     2    t3      4.889805          4.092564     4.958199    4.163255
##  8     2    t4      5.487121          4.545136     5.434412    4.552172
##  9     3    t1      3.261961          2.416053     3.253923    3.026659
## 10     3    t2      3.575117          2.843559     3.951325    3.346487
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
##      pid         variable    score
##    <dbl>            <chr>    <dbl>
##  1     1 agreeableness_t1 2.561910
##  2     2 agreeableness_t1 3.764061
##  3     3 agreeableness_t1 3.261961
##  4     4 agreeableness_t1 3.773405
##  5     5 agreeableness_t1 2.185621
##  6     6 agreeableness_t1 2.561549
##  7     7 agreeableness_t1 2.279778
##  8     8 agreeableness_t1 3.230945
##  9     9 agreeableness_t1 1.842271
## 10    10 agreeableness_t1 3.247076
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
##      pid         trait  time    score
##  * <dbl>         <chr> <chr>    <dbl>
##  1     1 agreeableness    t1 2.561910
##  2     2 agreeableness    t1 3.764061
##  3     3 agreeableness    t1 3.261961
##  4     4 agreeableness    t1 3.773405
##  5     5 agreeableness    t1 2.185621
##  6     6 agreeableness    t1 2.561549
##  7     7 agreeableness    t1 2.279778
##  8     8 agreeableness    t1 3.230945
##  9     9 agreeableness    t1 1.842271
## 10    10 agreeableness    t1 3.247076
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
##      pid         trait  time    score
##  * <dbl>         <chr> <chr>    <dbl>
##  1     1 agreeableness    t1 2.561910
##  2     2 agreeableness    t1 3.764061
##  3     3 agreeableness    t1 3.261961
##  4     4 agreeableness    t1 3.773405
##  5     5 agreeableness    t1 2.185621
##  6     6 agreeableness    t1 2.561549
##  7     7 agreeableness    t1 2.279778
##  8     8 agreeableness    t1 3.230945
##  9     9 agreeableness    t1 1.842271
## 10    10 agreeableness    t1 3.247076
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
##  1     1    t1      2.561910          2.226287     1.777716    1.482124
##  2     1    t2      3.129489          3.054222     2.330667    2.459631
##  3     1    t3      3.637314          3.350964     2.672625    3.374978
##  4     1    t4      4.207066          3.790548     3.487093    3.517931
##  5     2    t1      3.764061          3.424002     3.891194    2.976117
##  6     2    t2      4.377288          3.888895     4.719864    3.623774
##  7     2    t3      4.889805          4.092564     4.958199    4.163255
##  8     2    t4      5.487121          4.545136     5.434412    4.552172
##  9     3    t1      3.261961          2.416053     3.253923    3.026659
## 10     3    t2      3.575117          2.843559     3.951325    3.346487
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
##  1     1         2.561910         3.129489         3.637314
##  2     2         3.764061         4.377288         4.889805
##  3     3         3.261961         3.575117         4.186379
##  4     4         3.773405         4.618759         5.095310
##  5     5         2.185621         3.003284         3.388888
##  6     6         2.561549         2.854060         3.408756
##  7     7         2.279778         2.853680         3.186240
##  8     8         3.230945         3.818676         4.285996
##  9     9         1.842271         2.166671         2.772455
## 10    10         3.247076         3.899260         4.298564
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
##  1     1    t1      2.561910          2.226287     1.777716    1.482124
##  2     1    t2      3.129489          3.054222     2.330667    2.459631
##  3     1    t3      3.637314          3.350964     2.672625    3.374978
##  4     1    t4      4.207066          3.790548     3.487093    3.517931
##  5     2    t1      3.764061          3.424002     3.891194    2.976117
##  6     2    t2      4.377288          3.888895     4.719864    3.623774
##  7     2    t3      4.889805          4.092564     4.958199    4.163255
##  8     2    t4      5.487121          4.545136     5.434412    4.552172
##  9     3    t1      3.261961          2.416053     3.253923    3.026659
## 10     3    t2      3.575117          2.843559     3.951325    3.346487
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
##  1     1         2.561910         3.129489         3.637314
##  2     2         3.764061         4.377288         4.889805
##  3     3         3.261961         3.575117         4.186379
##  4     4         3.773405         4.618759         5.095310
##  5     5         2.185621         3.003284         3.388888
##  6     6         2.561549         2.854060         3.408756
##  7     7         2.279778         2.853680         3.186240
##  8     8         3.230945         3.818676         4.285996
##  9     9         1.842271         2.166671         2.772455
## 10    10         3.247076         3.899260         4.298564
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
##  1     1    t1      2.561910          2.226287     1.777716    1.482124
##  2     1    t2      3.129489          3.054222     2.330667    2.459631
##  3     1    t3      3.637314          3.350964     2.672625    3.374978
##  4     1    t4      4.207066          3.790548     3.487093    3.517931
##  5     2    t1      3.764061          3.424002     3.891194    2.976117
##  6     2    t2      4.377288          3.888895     4.719864    3.623774
##  7     2    t3      4.889805          4.092564     4.958199    4.163255
##  8     2    t4      5.487121          4.545136     5.434412    4.552172
##  9     3    t1      3.261961          2.416053     3.253923    3.026659
## 10     3    t2      3.575117          2.843559     3.951325    3.346487
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
##  $ agreeableness    : num  2.56 3.13 3.64 4.21 3.76 ...
##  $ conscientiousness: num  2.23 3.05 3.35 3.79 3.42 ...
##  $ extraversion     : num  1.78 2.33 2.67 3.49 3.89 ...
##  $ neuroticism      : num  1.48 2.46 3.37 3.52 2.98 ...
##  $ openness         : num  3.28 3.77 4.28 4.87 3.82 ...
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
##  1     1         2.561910             2.226287        1.777716
##  2     2         3.764061             3.424002        3.891194
##  3     3         3.261961             2.416053        3.253923
##  4     4         3.773405             3.415036        2.934184
##  5     5         2.185621             1.454738        3.201466
##  6     6         2.561549             2.481250        5.477701
##  7     7         2.279778             2.720208        3.471753
##  8     8         3.230945             4.007457        4.326198
##  9     9         1.842271             2.530430        3.668598
## 10    10         3.247076             3.297897        2.862606
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
##  1     1    t1      2.561910          2.226287     1.777716    1.482124
##  2     2    t1      3.764061          3.424002     3.891194    2.976117
##  3     3    t1      3.261961          2.416053     3.253923    3.026659
##  4     4    t1      3.773405          3.415036     2.934184    3.163681
##  5     5    t1      2.185621          1.454738     3.201466    3.410008
##  6     6    t1      2.561549          2.481250     5.477701    2.372637
##  7     7    t1      2.279778          2.720208     3.471753    3.636918
##  8     8    t1      3.230945          4.007457     4.326198    1.798065
##  9     9    t1      1.842271          2.530430     3.668598    4.346542
## 10    10    t1      3.247076          3.297897     2.862606    2.401233
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

## Summarizing

Typically, we are interested in some summary of our data rather than the individual values themselves. Summarization operations reduce a large number of observations to a single or few summarizing values.

### Basic Summarization

You're familiar with many summarization operations (and their often obviously named base r implementations):

| Operation | Function |
| ------- | ------------- |
| Mean/Average | `mean()` |
| Weighted Mean | `weighted.mean()` |
| Median | `median()` |
| Max/Min | `max()` / `min()`, `which.min()` or `.max()` returns the index, rather than the value |
| Variance | `var()` |
| Standard Deviation | `sd()` |

A simple way to get a global summary of all variables in a dataframe is to use  `psych::describe`


```r
df <- ggplot2::diamonds

pander::pander(
  psych::describe(df)
)
```


---------------------------------------------------------------------------
    &nbsp;      vars     n      mean      sd     median   trimmed    mad   
-------------- ------ ------- -------- -------- -------- --------- --------
  **carat**      1     53940   0.7979   0.474     0.7      0.735    0.4744 

   **cut***      2     53940   3.904    1.117      4       4.042    1.483  

  **color***     3     53940   3.594    1.701      4       3.553    1.483  

 **clarity***    4     53940   4.051    1.647      4       3.914    1.483  

  **depth**      5     53940   61.75    1.433     61.8     61.78    1.038  

  **table**      6     53940   57.46    2.234      57      57.32    1.483  

  **price**      7     53940    3933     3989     2401     3159      2476  

    **x**        8     53940   5.731    1.122     5.7      5.66     1.379  

    **y**        9     53940   5.735    1.142     5.71     5.663    1.364  

    **z**        10    53940   3.539    0.7057    3.53     3.495    0.8451 
---------------------------------------------------------------------------

Table: Table continues below

 
---------------------------------------------------------------------
    &nbsp;      min    max    range     skew     kurtosis      se    
-------------- ----- ------- ------- ---------- ---------- ----------
  **carat**     0.2   5.01    4.81     1.117      1.256     0.002041 

   **cut***      1      5       4     -0.7171    -0.3981    0.004808 

  **color***     1      7       6      0.1894    -0.8669    0.007324 

 **clarity***    1      8       7      0.5514    -0.3949    0.007092 

  **depth**     43     79      36     -0.08229    5.738     0.006168 

  **table**     43     95      52      0.7969     2.801     0.009621 

  **price**     326   18823   18497    1.618      2.177      17.18   

    **x**        0    10.74   10.74    0.3787    -0.6183    0.00483  

    **y**        0    58.9    58.9     2.434       91.2     0.004918 

    **z**        0    31.8    31.8     1.522      47.08     0.003039 
---------------------------------------------------------------------

We can also `apply` any function to a dataframe along a dimension. Typically functions that work on numerical data don't work on factorial or character data, so one has to be mindful about your call making sense.

For example, if we want to get the means of each of the numeric columns in our dataframe, we call `apply` on the 5th-9th columns of our dataframe (`df[,5:9]`), tell it to operate along the second dimension (columnwise), and apply `mean`:


```r
apply(df[,5:9], 2, mean)
```

```
##       depth       table       price           x           y 
##   61.749405   57.457184 3932.799722    5.731157    5.734526
```

Note that if we called it over the whole dataframe, `mean` will throw up warnings about being applied to non-numeric data, `apply` will quit after 10 warnings by default.


```r
apply(df, 2, mean)
```

```
## Warning in mean.default(newX[, i], ...): argument is not numeric or
## logical: returning NA

## Warning in mean.default(newX[, i], ...): argument is not numeric or
## logical: returning NA

## Warning in mean.default(newX[, i], ...): argument is not numeric or
## logical: returning NA

## Warning in mean.default(newX[, i], ...): argument is not numeric or
## logical: returning NA

## Warning in mean.default(newX[, i], ...): argument is not numeric or
## logical: returning NA

## Warning in mean.default(newX[, i], ...): argument is not numeric or
## logical: returning NA

## Warning in mean.default(newX[, i], ...): argument is not numeric or
## logical: returning NA

## Warning in mean.default(newX[, i], ...): argument is not numeric or
## logical: returning NA

## Warning in mean.default(newX[, i], ...): argument is not numeric or
## logical: returning NA

## Warning in mean.default(newX[, i], ...): argument is not numeric or
## logical: returning NA
```

```
##   carat     cut   color clarity   depth   table   price       x       y 
##      NA      NA      NA      NA      NA      NA      NA      NA      NA 
##       z 
##      NA
```

`apply` is the base function in several convenience/extension functions in base R, like `sweep` and `aggregate`, and functions similarly to `lapply`, `tapply`, `mapply`, `rapply`, `eapply`, etc.


### Grouped Summarization

If our data is in long format, the variable we are interested in summarizing is **grouped** by one or several others. For example, in our diamonds dataset we would expect `price` to be different depending on the diamond's `cut` and `carat`. `plyr` handles grouping elegantly with its `summarise` (`summarize` is an alias for us Americans) function. `summarize` can be used in a pipe, but since I am a stodgy old grandpa I prefer to use it in conjunction with one of the `plyr::*ply` functions (`ddply` when the input is a dataframe and the output is a dataframe, `ldply` for input lists output dataframes, `dlply` for input dataframes and output lists, etc.). `ddply` works like `apply`, but by first splitting the data by grouping variables. When `summarize` is used with `ddply`, the arguments afterwards are passed to `summarize`, and with them we can make whatever new variables we want. 


```r
# We use the .() syntax so we don't have to pass variable names as strings.
# .(cut, carat) is the same as c("cut", "carat")
library(plyr)
```

```
## -------------------------------------------------------------------------
```

```
## You have loaded plyr after dplyr - this is likely to cause problems.
## If you need functions from both plyr and dplyr, please load plyr first, then dplyr:
## library(plyr); library(dplyr)
```

```
## -------------------------------------------------------------------------
```

```
## 
## Attaching package: 'plyr'
```

```
## The following object is masked from 'package:purrr':
## 
##     compact
```

```
## The following objects are masked from 'package:dplyr':
## 
##     arrange, count, desc, failwith, id, mutate, rename, summarise,
##     summarize
```

```r
df_summary <- ddply(df, .(cut, carat), summarise,
                    mean_price = mean(price),
                    sd_price = sd(price),
                    n_samples = length(price))

pander::pander(
  df_summary
)
```


-------------------------------------------------------
    cut      carat   mean_price   sd_price   n_samples 
----------- ------- ------------ ---------- -----------
   Fair      0.22       337          NA          1     

   Fair      0.23       369          NA          1     

   Fair      0.25      645.7       333.8         3     

   Fair      0.27       371          NA          1     

   Fair      0.29       1184       837.2         2     

   Fair       0.3      702.1       172.2        20     

   Fair      0.31      712.2       134.8         9     

   Fair      0.32       1076       565.9         3     

   Fair      0.33      807.5       161.9         2     

   Fair      0.34       1105       628.3         5     

   Fair      0.35       1055       304.6         6     

   Fair      0.36       810          NA          1     

   Fair      0.37       1044       468.5         3     

   Fair      0.39      925.7       283.9         3     

   Fair       0.4       823        131.3         9     

   Fair      0.41      826.8       162.4         5     

   Fair      0.42       675          NA          1     

   Fair      0.43       1046       86.97         2     

   Fair      0.45       968        139.6        12     

   Fair      0.46      982.7       94.67        11     

   Fair      0.47       1095        510          7     

   Fair      0.48       1121       209.2         4     

   Fair      0.49       1477       322.4         2     

   Fair       0.5       1216       370.9        80     

   Fair      0.51       1644        1002        16     

   Fair      0.52       1609       478.9         7     

   Fair      0.53       1457       219.2         4     

   Fair      0.54       1529       586.9         2     

   Fair      0.56       1599       432.8         7     

   Fair      0.57       1802       640.2         5     

   Fair      0.58       1374        454          8     

   Fair      0.59       1408       702.4         4     

   Fair       0.6       1592       587.8         6     

   Fair      0.61       1306        186          2     

   Fair      0.62       1870       578.7         8     

   Fair      0.63       1740        353          4     

   Fair      0.64       1736        326          3     

   Fair      0.65       1770        157          2     

   Fair      0.67       1539       145.7         2     

   Fair      0.68       1633         NA          1     

   Fair      0.69       1922       274.6         4     

   Fair       0.7       1962        539         137    

   Fair      0.71       2251       421.8        44     

   Fair      0.72       2267       443.8        17     

   Fair      0.73       2116       682.9        21     

   Fair      0.74       2475       612.6        12     

   Fair      0.75       2407       419.9        11     

   Fair      0.76       2280        602          6     

   Fair      0.77       2245       454.5         7     

   Fair      0.78       2497       193.8         5     

   Fair      0.79       2362         NA          1     

   Fair       0.8       2212       567.6         7     

   Fair      0.81       2342       547.2         6     

   Fair      0.82       2620       315.7         5     

   Fair      0.83       2365       222.7         3     

   Fair      0.84       2641       391.4         5     

   Fair      0.85       1706       667.2         8     

   Fair      0.86       3310       508.1         3     

   Fair      0.87       3166         NA          1     

   Fair      0.88       2716       234.8         2     

   Fair      0.89       2082       659.6         3     

   Fair       0.9       3279       633.9        104    

   Fair      0.91       3510       882.4        34     

   Fair      0.92       3367       572.4        10     

   Fair      0.93       3723        789          5     

   Fair      0.94       3564       425.1         3     

   Fair      0.95       2903       401.1         5     

   Fair      0.96       2587        830         26     

   Fair      0.97       2438       881.5         6     

   Fair      0.98       2794       871.1        11     

   Fair      0.99       2742       676.5         7     

   Fair        1        4130        1345        166    

   Fair      1.01       4502        1252        125    

   Fair      1.02       4425        1214        29     

   Fair      1.03       3697       884.1        20     

   Fair      1.04       3696        1205         9     

   Fair      1.05       4005        1628        10     

   Fair      1.06       2955       697.7         3     

   Fair      1.07       3742        1022         4     

   Fair      1.08       4697        1346         6     

   Fair      1.09       3993       866.2         4     

   Fair       1.1       4308       589.7         2     

   Fair      1.11       4459       678.8         4     

   Fair      1.12       5617        2017         3     

   Fair      1.13       4740        1416         7     

   Fair      1.14       4892        2106         2     

   Fair      1.15       6530         NA          1     

   Fair      1.16       4794        1347         6     

   Fair      1.17       4127        1752         5     

   Fair      1.18       4452       509.6         3     

   Fair      1.19       4571        1469         3     

   Fair       1.2       3579        1054        17     

   Fair      1.21       4402        1001        11     

   Fair      1.22       4189       477.4         3     

   Fair      1.23       3692         NA          1     

   Fair      1.24       5233        1455         4     

   Fair      1.25       3990         NA          1     

   Fair      1.26       4536       468.4         4     

   Fair      1.27       5334         NA          1     

   Fair      1.29       3899        1161         3     

   Fair       1.3       3524        1701         3     

   Fair      1.32       7268        3710         4     

   Fair      1.33       5208       607.5         4     

   Fair      1.34       5106         NA          1     

   Fair      1.35       5476        1091         5     

   Fair      1.36       4158         NA          1     

   Fair      1.38       4730         NA          1     

   Fair       1.4       8216         NA          1     

   Fair      1.41       4145         NA          1     

   Fair      1.42       7214         NA          1     

   Fair      1.43       6914       935.1         3     

   Fair      1.44       7338         NA          1     

   Fair      1.45       5115        1167         3     

   Fair      1.46       5614       154.9         2     

   Fair      1.47       5503       855.6         2     

   Fair      1.49       7602        1665         2     

   Fair       1.5       7798        2983        60     

   Fair      1.51       8079        2493        44     

   Fair      1.52       7044        1883        21     

   Fair      1.53       7828        1653         2     

   Fair      1.54       9097        2571         5     

   Fair      1.55       9631       828.7         2     

   Fair      1.56       7950         0           2     

   Fair      1.57       8133         NA          1     

   Fair      1.58       7333         NA          1     

   Fair      1.59       7080         NA          1     

   Fair       1.6       7469        1693         2     

   Fair      1.64       6902        2903         2     

   Fair      1.65       5914         NA          1     

   Fair       1.7      10039        3783         4     

   Fair      1.71       8757       60.81         2     

   Fair      1.72       9042        4245         3     

   Fair      1.73       6007         NA          1     

   Fair      1.76       9314         NA          1     

   Fair      1.81       7110        1770         2     

   Fair      1.82       5993         NA          1     

   Fair      1.83       5985        1276         2     

   Fair       1.9       8576         NA          1     

   Fair      1.91       9438        3968         2     

   Fair      1.93      17995         NA          1     

   Fair      1.96       5850       419.3         2     

   Fair      1.98      12923         NA          1     

   Fair      1.99      17713         NA          1     

   Fair        2       11592        2976        40     

   Fair      2.01      12421        3321        31     

   Fair      2.02      12351        4006        11     

   Fair      2.03      10107        4805         7     

   Fair      2.04      11685        1640         3     

   Fair      2.05      17408         NA          1     

   Fair      2.06      14750         NA          1     

   Fair      2.07       9760        4605         2     

   Fair       2.1      11772        3958         7     

   Fair      2.11      11645        4219         3     

   Fair      2.13      13370         NA          1     

   Fair      2.14       8519        4618         3     

   Fair      2.15       5430         NA          1     

   Fair      2.17       6817         NA          1     

   Fair       2.2       7934         NA          1     

   Fair      2.22       8226        3739         3     

   Fair      2.25       6861       294.2         2     

   Fair      2.27       6432       988.5         2     

   Fair      2.29      13256       629.3         2     

   Fair      2.31       7257         NA          1     

   Fair      2.32      12443        7896         2     

   Fair       2.4      15824         NA          1     

   Fair      2.43      14975         NA          1     

   Fair      2.48      15030         NA          1     

   Fair      2.49       6289         NA          1     

   Fair       2.5      12767        3372         7     

   Fair      2.51      18308         NA          1     

   Fair      2.52      13233        2899         3     

   Fair      2.53      17103         NA          1     

   Fair      2.58      12500         NA          1     

   Fair       2.6      17027         NA          1     

   Fair      2.72       6870         NA          1     

   Fair      2.74       8807         NA          1     

   Fair        3       12507        6312         2     

   Fair      3.01      15748        4319         3     

   Fair      3.02      10577         NA          1     

   Fair      3.11       9823         NA          1     

   Fair       3.4      15964         NA          1     

   Fair      3.65      11668         NA          1     

   Fair      4.13      17329         NA          1     

   Fair       4.5      18531         NA          1     

   Fair      5.01      18018         NA          1     

   Good      0.23      442.9       51.28        31     

   Good      0.24      458.1       62.66        14     

   Good      0.25      484.8       65.48        15     

   Good      0.26      527.2       94.64        18     

   Good      0.27      581.2       150.3         5     

   Good      0.28      487.8       86.32        12     

   Good      0.29      566.3       82.93         7     

   Good       0.3       584        140.6        217    

   Good      0.31      593.4       127.8        176    

   Good      0.32      574.5       145.5        94     

   Good      0.33      604.8       138.3        34     

   Good      0.34      616.2       126.8        48     

   Good      0.35      597.6       121.2        32     

   Good      0.36      698.6        164         19     

   Good      0.37      663.9       132.1        20     

   Good      0.38       808        182.5        25     

   Good      0.39      852.2       178.3        29     

   Good       0.4      791.7       172.1        141    

   Good      0.41      801.7       159.8        117    

   Good      0.42       861        219.5        28     

   Good      0.43      804.8       150.1        35     

   Good      0.44      986.8       385.2        17     

   Good      0.45       1156       190.1        19     

   Good      0.46       1285       348.5        19     

   Good      0.47       1239       240.7        14     

   Good      0.48       1202       230.3        11     

   Good      0.49       1394       192.7         6     

   Good       0.5       1396       348.8        181    

   Good      0.51       1460       309.9        104    

   Good      0.52       1387        251         49     

   Good      0.53       1394       313.1        51     

   Good      0.54       1569       374.3        38     

   Good      0.55       1486       551.2        21     

   Good      0.56       1617       293.5        12     

   Good      0.57       1618       446.5        21     

   Good      0.58       1704       388.2        17     

   Good      0.59       1583       270.6        15     

   Good       0.6       1702       341.3        15     

   Good      0.61       1806       573.1        21     

   Good      0.62       1717       437.6         7     

   Good      0.63       1696       517.2         6     

   Good      0.64       1780       583.2        10     

   Good      0.65       2078       306.9         6     

   Good      0.66       2090        141          9     

   Good      0.67       2097       289.1        17     

   Good      0.68       2216       703.4         5     

   Good      0.69       2285       620.1         5     

   Good       0.7       2403       469.4        290    

   Good      0.71       2380       462.5        121    

   Good      0.72       2390       468.4        51     

   Good      0.73       2571       628.6        23     

   Good      0.74       2657       722.9        20     

   Good      0.75       2565       525.8        22     

   Good      0.76       2703       617.6        16     

   Good      0.77       2661       437.2        25     

   Good      0.78       2595       537.8        14     

   Good      0.79       2572       461.7        11     

   Good       0.8       2836       572.8        27     

   Good      0.81       2808       284.6        14     

   Good      0.82       2592       483.8        10     

   Good      0.83       2498       417.4        11     

   Good      0.84       2993       620.4         7     

   Good      0.85       2400       313.5         5     

   Good      0.86       3498        1328         3     

   Good      0.88       2368       349.8         3     

   Good      0.89       3169       399.8         4     

   Good       0.9       3806       689.1        330    

   Good      0.91       3747       685.1        90     

   Good      0.92       3894       579.6        33     

   Good      0.93       3835       573.9        19     

   Good      0.94       3532        839          4     

   Good      0.95       3986        1256         6     

   Good      0.96       3766        633         13     

   Good      0.97       4050       598.8         6     

   Good      0.98       3562        939          2     

   Good      0.99       4052         NA          1     

   Good        1        4997        1363        333    

   Good      1.01       5197        1486        404    

   Good      1.02       4905        1296        90     

   Good      1.03       4368        1120        32     

   Good      1.04       4976        1358        32     

   Good      1.05       4820        1155        22     

   Good      1.06       5034        1174        12     

   Good      1.07       4893        1126        14     

   Good      1.08       5115       983.9        12     

   Good      1.09       5792        3216        14     

   Good       1.1       4395       424.7        14     

   Good      1.11       5280        1029        20     

   Good      1.12       5629        1799        12     

   Good      1.13       4102       684.5         5     

   Good      1.14       5900        3886        12     

   Good      1.15       4951       894.7         5     

   Good      1.16       4881       819.5         8     

   Good      1.17       4935        1184        10     

   Good      1.18       5308       929.8         6     

   Good      1.19       6453        1438         9     

   Good       1.2       6273        1576        56     

   Good      1.21       5983        1563        35     

   Good      1.22       5895        1405         9     

   Good      1.23       5350       728.5        15     

   Good      1.24       6952        1749         7     

   Good      1.25       5832        1625         8     

   Good      1.26       5323        1110         4     

   Good      1.27       4986       718.4         5     

   Good      1.28       5054       678.6         8     

   Good      1.29       6013        1803         5     

   Good       1.3       6574        2639         8     

   Good      1.31       6205        950          7     

   Good      1.32       6173        1145         4     

   Good      1.33       7762        2283         6     

   Good      1.34       8109         NA          1     

   Good      1.35       5016       839.3         2     

   Good      1.36       7196       565.5         4     

   Good      1.37       5449         NA          1     

   Good      1.38       4598         NA          1     

   Good      1.39       7032        89.8         2     

   Good       1.4       9046        2291         3     

   Good      1.41       9441        2743         6     

   Good      1.42       8333        1087         3     

   Good      1.44       7832        2609         2     

   Good      1.46      11851         NA          1     

   Good      1.48      15164         NA          1     

   Good      1.49       5407         NA          1     

   Good       1.5       8995        2537        156    

   Good      1.51       9380        2452        113    

   Good      1.52       9881        3133        34     

   Good      1.53       9359        3592         8     

   Good      1.54       8384        2731         9     

   Good      1.55       8135        3200         3     

   Good      1.56       7936        1311        11     

   Good      1.57      12547         NA          1     

   Good      1.58       9774       0.7071        2     

   Good      1.59       9752        1732         3     

   Good       1.6      12531        58.4         3     

   Good      1.61       9608       812.5         4     

   Good      1.62       8572        3020         4     

   Good      1.63      10844        1673         4     

   Good      1.64       8628        2085         2     

   Good      1.66       9871        4687         3     

   Good      1.67       8013         NA          1     

   Good      1.68       5818       74.95         2     

   Good      1.69      17369       43.84         2     

   Good       1.7      11759        3506        21     

   Good      1.71      10689        2636        12     

   Good      1.72      11782        2396         6     

   Good      1.73       9524        2787         5     

   Good      1.74      12329        3755         3     

   Good      1.75      11450        2655         3     

   Good      1.77       8109         NA          1     

   Good      1.78       9071         NA          1     

   Good      1.79       9122         NA          1     

   Good       1.8      11494        1314         3     

   Good      1.81      11336         NA          1     

   Good      1.86       9791         NA          1     

   Good      1.88       8048         NA          1     

   Good      1.91      10937         NA          1     

   Good      1.99      13338         NA          1     

   Good        2       14489        3199        47     

   Good      2.01      14647        2918        73     

   Good      2.02      14609        2215        15     

   Good      2.03      13723        2250         6     

   Good      2.04      15360        2862         5     

   Good      2.05      13156        5310         3     

   Good      2.06      12695        3749         6     

   Good      2.07      15940        2462         3     

   Good      2.08      13118        1564         3     

   Good      2.09      15447        3525         3     

   Good       2.1      12020       174.9         3     

   Good      2.13      16062         NA          1     

   Good      2.14      14424       40.31         2     

   Good      2.15      13317         NA          1     

   Good      2.16      14266         NA          1     

   Good      2.17      16036         NA          1     

   Good      2.18      15452        2277         3     

   Good      2.19      12419        1355         3     

   Good       2.2      13919         NA          1     

   Good      2.22      15945        1486         3     

   Good      2.25      15287         NA          1     

   Good      2.26      16241         NA          1     

   Good      2.27      15650        1981         2     

   Good      2.28      12546        3013         3     

   Good       2.3      12944         NA          1     

   Good      2.32      13654         NA          1     

   Good      2.33      13242         NA          1     

   Good      2.34      12961         NA          1     

   Good      2.36      12286         NA          1     

   Good       2.4      18541         NA          1     

   Good      2.41      17923         NA          1     

   Good       2.5      18325         NA          1     

   Good      2.51      14230        29.7         2     

   Good      2.52      17608         NA          1     

   Good      2.55      14775         NA          1     

   Good      2.56      15231         NA          1     

   Good      2.61      13784         NA          1     

   Good      2.66      16991        1303         3     

   Good      2.67      18686         NA          1     

   Good       2.8      18788         NA          1     

   Good        3       12443        2171         3     

   Good      3.01      18359       202.6         3     

 Very Good    0.2       367          NA          1     

 Very Good   0.21       386          NA          1     

 Very Good   0.23      482.7       61.24        197    

 Very Good   0.24      481.1       64.77        154    

 Very Good   0.25      539.6       73.02        100    

 Very Good   0.26       525        86.39        99     

 Very Good   0.27      549.3       84.24        98     

 Very Good   0.28      548.1       97.26        92     

 Very Good   0.29      574.2       93.25        61     

 Very Good    0.3      620.9       165.1        523    

 Very Good   0.31       635        153.5        341    

 Very Good   0.32      631.4        142         267    

 Very Good   0.33      674.8       149.1        174    

 Very Good   0.34      655.5       137.5        120    

 Very Good   0.35      721.5       150.8        90     

 Very Good   0.36      721.3       159.6        86     

 Very Good   0.37      732.1       138.9        64     

 Very Good   0.38      827.1       185.4        124    

 Very Good   0.39      847.5       165.4        96     

 Very Good    0.4      877.7       176.1        289    

 Very Good   0.41      886.7       174.6        244    

 Very Good   0.42      883.3       197.3        119    

 Very Good   0.43      907.6       187.2        89     

 Very Good   0.44       945        174.2        34     

 Very Good   0.45       1059       215.2        15     

 Very Good   0.46       1214        281         40     

 Very Good   0.47       1247       300.4        18     

 Very Good   0.48       1652       533.6        12     

 Very Good   0.49       1402       336.4         9     

 Very Good    0.5       1489       339.4        332    

 Very Good   0.51       1561       349.2        251    

 Very Good   0.52       1510       300.5        170    

 Very Good   0.53       1573       451.9        121    

 Very Good   0.54       1583       309.1        93     

 Very Good   0.55       1663       408.1        86     

 Very Good   0.56       1836       594.6        78     

 Very Good   0.57       1664       504.2        75     

 Very Good   0.58       1643       426.4        62     

 Very Good   0.59       1765       424.9        81     

 Very Good    0.6       1855       479.2        51     

 Very Good   0.61       1958       418.3        45     

 Very Good   0.62       1894       398.3        22     

 Very Good   0.63       2164       740.4        17     

 Very Good   0.64       1984       497.6        17     

 Very Good   0.65       2184       465.1        16     

 Very Good   0.66       2424       814.3        12     

 Very Good   0.67       2123       432.9        12     

 Very Good   0.68       2428       600.8         7     

 Very Good   0.69       2411       574.5         7     

 Very Good    0.7       2505       490.2        580    

 Very Good   0.71       2567       481.9        355    

 Very Good   0.72       2624       532.5        180    

 Very Good   0.73       2754       535.8        99     

 Very Good   0.74       2694       490.8        83     

 Very Good   0.75       2814       568.6        67     

 Very Good   0.76       2876       633.2        50     

 Very Good   0.77       2893       605.8        67     

 Very Good   0.78       2947        630         57     

 Very Good   0.79       2964       594.8        41     

 Very Good    0.8       3145       700.1        85     

 Very Good   0.81       3028       570.9        45     

 Very Good   0.82       3145       613.9        30     

 Very Good   0.83       3274       998.6        29     

 Very Good   0.84       3465       686.1        20     

 Very Good   0.85       3650       505.7         9     

 Very Good   0.86       3289       826.8         8     

 Very Good   0.87       2870       434.1         8     

 Very Good   0.88       3474       925.2         5     

 Very Good   0.89       3532       411.9         3     

 Very Good    0.9       4033       734.9        522    

 Very Good   0.91       4024       670.5        187    

 Very Good   0.92       4178       898.6        54     

 Very Good   0.93       3979        611         38     

 Very Good   0.94       4101       753.2        17     

 Very Good   0.95       4072        780         16     

 Very Good   0.96       4263       656.7        20     

 Very Good   0.97       4560        1382        11     

 Very Good   0.98       4094       642.2         6     

 Very Good   0.99       5046       624.3         8     

 Very Good     1        5674        1665        389    

 Very Good   1.01       5698        1721        639    

 Very Good   1.02       5703        1903        233    

 Very Good   1.03       5364        1477        98     

 Very Good   1.04       5839        2178        100    

 Very Good   1.05       5546        1573        78     

 Very Good   1.06       5883        1878        80     

 Very Good   1.07       6340        2727        62     

 Very Good   1.08       5340        1424        43     

 Very Good   1.09       6063        2510        49     

 Very Good    1.1       5834        1861        66     

 Very Good   1.11       5841        2160        58     

 Very Good   1.12       5579        1584        44     

 Very Good   1.13       5534        1776        39     

 Very Good   1.14       6360        2704        39     

 Very Good   1.15       5846        1767        36     

 Very Good   1.16       5994        1626        22     

 Very Good   1.17       5875        1833        15     

 Very Good   1.18       6141        2669        22     

 Very Good   1.19       6296        2359        24     

 Very Good    1.2       6531        1877        189    

 Very Good   1.21       6566        1742        103    

 Very Good   1.22       6607        1685        50     

 Very Good   1.23       7655        2463        51     

 Very Good   1.24       6799        2179        48     

 Very Good   1.25       7214        2301        32     

 Very Good   1.26       7683        2048        26     

 Very Good   1.27       6872        1896        29     

 Very Good   1.28       7916        2481        16     

 Very Good   1.29       6717        3217        20     

 Very Good    1.3       7176        2072        22     

 Very Good   1.31       6947        1867        22     

 Very Good   1.32       7407        2589        19     

 Very Good   1.33       6503        1050        12     

 Very Good   1.34       7003        1729        17     

 Very Good   1.35       7131        1374        14     

 Very Good   1.36       8811        3080        10     

 Very Good   1.37       7060        1620        10     

 Very Good   1.38       9917        4730         5     

 Very Good   1.39       8413        3030         6     

 Very Good    1.4       9254        2099        14     

 Very Good   1.41       9153        2633         8     

 Very Good   1.42      11236        3357         5     

 Very Good   1.43       8634        1449         5     

 Very Good   1.44       7721        2994         3     

 Very Good   1.45       9810        4321         6     

 Very Good   1.46       7915        1241         4     

 Very Good   1.47       8675        2046         8     

 Very Good   1.48       8085       791.4         3     

 Very Good   1.49       7796        1744         3     

 Very Good    1.5      10451        2596        218    

 Very Good   1.51      10638        2734        207    

 Very Good   1.52      10908        2555        77     

 Very Good   1.53      10761        2496        54     

 Very Good   1.54      10827        2965        27     

 Very Good   1.55      10288        2363        20     

 Very Good   1.56       9616        2408        18     

 Very Good   1.57       9893        1732        20     

 Very Good   1.58      11186        2451        15     

 Very Good   1.59      10206        1715        19     

 Very Good    1.6      10382        1652        16     

 Very Good   1.61       9540        2095        12     

 Very Good   1.62      10432        1909        10     

 Very Good   1.63      11415        1920        12     

 Very Good   1.64       9174        1978        10     

 Very Good   1.65      11046        3321         8     

 Very Good   1.66      11619        3160         7     

 Very Good   1.67      11623        3369         5     

 Very Good   1.68      14750       789.8         2     

 Very Good   1.69      11239        4542         5     

 Very Good    1.7      12351        3168        44     

 Very Good   1.71      12626        2560        25     

 Very Good   1.72      12158        3199        11     

 Very Good   1.73      12877        2830        11     

 Very Good   1.74      12745        5147         6     

 Very Good   1.75      13550        2730        11     

 Very Good   1.76      12793        3555         8     

 Very Good   1.77      12076        2796         4     

 Very Good   1.78      12457        1533         3     

 Very Good   1.79      15520        4115         2     

 Very Good    1.8      12514        4122         3     

 Very Good   1.81      13500       958.1         2     

 Very Good   1.82      12466        2229         5     

 Very Good   1.83      13848        2615         5     

 Very Good   1.85       5688         NA          1     

 Very Good   1.86      13466        2978         4     

 Very Good   1.87      14308         NA          1     

 Very Good   1.88      11912        1943         3     

 Very Good   1.89      15594         NA          1     

 Very Good    1.9       9540         NA          1     

 Very Good   1.91      16375        1604         2     

 Very Good   1.92      15364         NA          1     

 Very Good   1.93      13278         NA          1     

 Very Good   1.94      11916        3638         2     

 Very Good   1.95      15988        1959         2     

 Very Good   1.96      13099         NA          1     

 Very Good   1.97      17377         NA          1     

 Very Good   1.98      15083         NA          1     

 Very Good     2       14922        2436        62     

 Very Good   2.01      14816        2595        110    

 Very Good   2.02      15046        2358        41     

 Very Good   2.03      15704        2115        28     

 Very Good   2.04      14920        2587        24     

 Very Good   2.05      15421        2048        12     

 Very Good   2.06      14474        2516         9     

 Very Good   2.07      16234        1475         9     

 Very Good   2.08      15726        2561         7     

 Very Good   2.09      16306        2246        10     

 Very Good    2.1      14530        2221        11     

 Very Good   2.11      15071        3222        10     

 Very Good   2.12      15998        2777         4     

 Very Good   2.13      15304        1240         3     

 Very Good   2.14      16434        1631         8     

 Very Good   2.15      14262        2544         3     

 Very Good   2.16      16206        1661         6     

 Very Good   2.17      14722       992.6         3     

 Very Good   2.18      13847        2301        10     

 Very Good   2.19      16298        1351         4     

 Very Good    2.2      14250        2769         3     

 Very Good   2.21      16077        2810         4     

 Very Good   2.22      14463        2352         6     

 Very Good   2.23      11464        3998         3     

 Very Good   2.24      16318       340.1         2     

 Very Good   2.25      12271        3368         3     

 Very Good   2.26      14711        2241         4     

 Very Good   2.28      14474        2216         2     

 Very Good   2.29      14785        4643         2     

 Very Good    2.3      14603         NA          1     

 Very Good   2.31      15755        2177         5     

 Very Good   2.32      18440       96.87         2     

 Very Good   2.33      15473        2382         3     

 Very Good   2.34      11119         NA          1     

 Very Good   2.35      15122         NA          1     

 Very Good   2.38      16126         NA          1     

 Very Good   2.39      17356       798.3         2     

 Very Good    2.4      17955         NA          1     

 Very Good   2.43      16170         NA          1     

 Very Good   2.44      17478       948.5         3     

 Very Good   2.45      11836       9.192         2     

 Very Good   2.48      17445        1244         4     

 Very Good    2.5      17028         NA          1     

 Very Good   2.51      17420       741.2         4     

 Very Good   2.52      17689         NA          1     

 Very Good   2.54      15638        2191         5     

 Very Good   2.58      14749         NA          1     

 Very Good   2.63      10437         NA          1     

 Very Good   2.68       9665         NA          1     

 Very Good    2.7      14341         NA          1     

 Very Good   2.74      17174       14.14         2     

 Very Good     3        6512         NA          1     

 Very Good   3.04      15354         NA          1     

 Very Good     4       15984         NA          1     

  Premium     0.2      364.2       7.778         8     

  Premium    0.21      379.5        21.8         8     

  Premium    0.22       405        52.27         4     

  Premium    0.23      566.5       108.1        20     

  Premium    0.24      559.9       109.9        17     

  Premium    0.25      659.9        180         28     

  Premium    0.26      595.8       119.6        30     

  Premium    0.27      620.1       137.2        16     

  Premium    0.28      686.3       118.8        13     

  Premium    0.29      531.9       151.9         8     

  Premium     0.3      712.9       163.5        597    

  Premium    0.31      717.3       174.9        514    

  Premium    0.32      706.8       171.6        410    

  Premium    0.33      757.3       169.9        306    

  Premium    0.34      720.9       154.2        229    

  Premium    0.35      805.6       179.6        165    

  Premium    0.36      780.3       184.5        157    

  Premium    0.37      841.6       153.4        135    

  Premium    0.38      925.1        191         143    

  Premium    0.39      996.6       184.1        89     

  Premium     0.4       965        170.5        315    

  Premium    0.41      983.2       179.8        349    

  Premium    0.42      984.9       160.3        171    

  Premium    0.43       1013       201.6        141    

  Premium    0.44       1009       148.7        65     

  Premium    0.45       1129       249.5        22     

  Premium    0.46       1305       264.7        25     

  Premium    0.47       1133       247.2        18     

  Premium    0.48       1141       139.9        13     

  Premium    0.49       1162       241.1        22     

  Premium     0.5       1532       304.1        277    

  Premium    0.51       1591       349.2        231    

  Premium    0.52       1592       367.3        132    

  Premium    0.53       1615       369.1        104    

  Premium    0.54       1675       351.6        120    

  Premium    0.55       1646       348.8        87     

  Premium    0.56       1724       315.5        82     

  Premium    0.57       1698       355.8        90     

  Premium    0.58       1840       380.7        41     

  Premium    0.59       1716       420.4        43     

  Premium     0.6       1730       420.5        48     

  Premium    0.61       1784       379.1        31     

  Premium    0.62       1745       474.7        39     

  Premium    0.63       2109       563.3        26     

  Premium    0.64       1953       398.6        11     

  Premium    0.65       1939        446         14     

  Premium    0.66       2060       604.4         6     

  Premium    0.67       2066        264          9     

  Premium    0.68       2062       375.4         9     

  Premium    0.69       1862       152.7         5     

  Premium     0.7       2453       444.3        414    

  Premium    0.71       2587       454.6        275    

  Premium    0.72       2491       464.1        150    

  Premium    0.73       2526       439.6        106    

  Premium    0.74       2630       422.9        72     

  Premium    0.75       2529       422.1        59     

  Premium    0.76       2684       548.1        57     

  Premium    0.77       2760       504.7        60     

  Premium    0.78       2764        511         29     

  Premium    0.79       2785       519.3        36     

  Premium     0.8       2886       586.2        57     

  Premium    0.81       2881       511.2        48     

  Premium    0.82       2837       406.9        35     

  Premium    0.83       3010       464.7        29     

  Premium    0.84       3353       703.1        11     

  Premium    0.85       3188       956.1         9     

  Premium    0.86       2876       480.4         8     

  Premium    0.87       3136       802.2         7     

  Premium    0.88       3570       303.7         3     

  Premium    0.89       3012       340.2         5     

  Premium     0.9       3873       661.3        314    

  Premium    0.91       3961       713.9        151    

  Premium    0.92       3819       546.7        74     

  Premium    0.93       3918       696.9        45     

  Premium    0.94       4247       645.3        24     

  Premium    0.95       3793       617.3        24     

  Premium    0.96       3740       638.6        27     

  Premium    0.97       4376        718         19     

  Premium    0.98       3295        1212         7     

  Premium    0.99       4984        1286         2     

  Premium      1        5316        1528        462    

  Premium    1.01       5384        1586        648    

  Premium    1.02       5450        1832        259    

  Premium    1.03       5388        1559        148    

  Premium    1.04       5653        1985        147    

  Premium    1.05       5350        1805        104    

  Premium    1.06       5464        1310        104    

  Premium    1.07       5416        1710        106    

  Premium    1.08       5372        1294        64     

  Premium    1.09       5374        1355        79     

  Premium     1.1       5373        1604        78     

  Premium    1.11       5354        1586        96     

  Premium    1.12       5483        1870        89     

  Premium    1.13       5668        1950        82     

  Premium    1.14       5716        1716        66     

  Premium    1.15       5430        1336        51     

  Premium    1.16       5565        1753        49     

  Premium    1.17       5732        1974        24     

  Premium    1.18       6025        1906        41     

  Premium    1.19       5574        1337        41     

  Premium     1.2       6487        2087        189    

  Premium    1.21       6609        2013        164    

  Premium    1.22       6607        1789        117    

  Premium    1.23       6832        1860        85     

  Premium    1.24       6722        1967        81     

  Premium    1.25       6302        1689        69     

  Premium    1.26       6891        2479        43     

  Premium    1.27       6953        2312        43     

  Premium    1.28       7204        2456        36     

  Premium    1.29       7049        1748        33     

  Premium     1.3       7552        2360        51     

  Premium    1.31       6960        2241        53     

  Premium    1.32       6759        2041        29     

  Premium    1.33       7138        1734        37     

  Premium    1.34       7849        2909        19     

  Premium    1.35       7418        2018        28     

  Premium    1.36       7314        2201        18     

  Premium    1.37       7529        2025        16     

  Premium    1.38       6840        1726         8     

  Premium    1.39       7632        1712        15     

  Premium     1.4       8535        2189        19     

  Premium    1.41       9536        2853        15     

  Premium    1.42       9465        1816         7     

  Premium    1.43      11097        2567         3     

  Premium    1.44       7701        2768         9     

  Premium    1.45       9791        1365         5     

  Premium    1.46       8245        2698         8     

  Premium    1.47       7682        1385        10     

  Premium    1.48       6651       615.2         2     

  Premium    1.49      11498        1983         2     

  Premium     1.5      10399        3068        242    

  Premium    1.51      10772        2783        261    

  Premium    1.52      10633        2901        140    

  Premium    1.53      10699        2427        82     

  Premium    1.54      10689        2934        80     

  Premium    1.55      10165        2812        43     

  Premium    1.56      10001        2205        33     

  Premium    1.57      10287        2319        38     

  Premium    1.58      10722        2740        29     

  Premium    1.59      10667        3082        31     

  Premium     1.6      10797        2728        34     

  Premium    1.61      10382        2244        22     

  Premium    1.62      11576        2960        22     

  Premium    1.63      10573        1813        11     

  Premium    1.64      11054        2651        16     

  Premium    1.65       9573        1900         9     

  Premium    1.66      10433        2755         9     

  Premium    1.67      10225        1368         5     

  Premium    1.68      10982        2629         4     

  Premium    1.69      10578        4516         8     

  Premium     1.7      12187        3049        87     

  Premium    1.71      12258        2910        43     

  Premium    1.72      12461        3226        27     

  Premium    1.73      11960        3158        28     

  Premium    1.74      11388        3637        16     

  Premium    1.75      13088        3022        18     

  Premium    1.76      12522        2688         9     

  Premium    1.77      11293        2249         8     

  Premium    1.78      12107        2771         6     

  Premium    1.79      13835        3115         8     

  Premium     1.8      11018        1757         4     

  Premium    1.81      12454       967.6         4     

  Premium    1.82      14116        1136         6     

  Premium    1.83      11730        3513         8     

  Premium    1.84      11869        3418         3     

  Premium    1.86      11837        1450         3     

  Premium    1.87      12791        2980         3     

  Premium    1.89      10055         NA          1     

  Premium     1.9      12539        1535         3     

  Premium    1.91      12216        2123         4     

  Premium    1.93      17130       208.1         3     

  Premium    1.94      18735         NA          1     

  Premium    1.95       5045         NA          1     

  Premium    1.96       9769         NA          1     

  Premium    1.97      15496        1767         3     

  Premium    1.98      16171         NA          1     

  Premium    1.99      11486         NA          1     

  Premium      2       14100        2794        77     

  Premium    2.01      14782        2262        148    

  Premium    2.02      15021        2394        64     

  Premium    2.03      14687        3077        43     

  Premium    2.04      14650        2793        38     

  Premium    2.05      15320        2375        34     

  Premium    2.06      14466        2620        28     

  Premium    2.07      15051        2007        18     

  Premium    2.08      14615        2807        19     

  Premium    2.09      14706        2709        20     

  Premium     2.1      15013        1634        17     

  Premium    2.11      14383        2102        20     

  Premium    2.12      14808        3134        13     

  Premium    2.13      14895        1886        10     

  Premium    2.14      15455        2085        23     

  Premium    2.15      12976        3452         7     

  Premium    2.16      16260        2522         7     

  Premium    2.17      13201        2078         8     

  Premium    2.18      15188        2634        10     

  Premium    2.19      15279        2423         9     

  Premium     2.2      15642        2195        15     

  Premium    2.21      14364        2922        13     

  Premium    2.22      15476        2186         7     

  Premium    2.23      14692        1788        10     

  Premium    2.24      14336        2180         7     

  Premium    2.25      16260        2503         7     

  Premium    2.26      15041        2690         5     

  Premium    2.27      15845        1230         5     

  Premium    2.28      16557        1317         9     

  Premium    2.29      17168        2004        10     

  Premium     2.3      13331        3282         8     

  Premium    2.31      15599        1939         7     

  Premium    2.32      15260        2507         6     

  Premium    2.33      11523        3247         3     

  Premium    2.34      12154        5181         2     

  Premium    2.35      16252        1609         6     

  Premium    2.36      18745         NA          1     

  Premium    2.37      15429       837.2         2     

  Premium    2.38      15389        2993         7     

  Premium    2.39      16749       864.2         3     

  Premium     2.4      16340       439.6         3     

  Premium    2.41      14710       994.3         3     

  Premium    2.42      16972       881.1         7     

  Premium    2.43      14378        4498         3     

  Premium    2.44      13027         NA          1     

  Premium    2.46      10470         NA          1     

  Premium    2.47      15751        1105         2     

  Premium    2.48      17047       487.5         3     

  Premium    2.49      18325         NA          1     

  Premium     2.5      13730        4380         4     

  Premium    2.51      16503        1528         7     

  Premium    2.52      17984       402.1         3     

  Premium    2.53      16517        1555         5     

  Premium    2.54      14717         NA          1     

  Premium    2.55      16558        3122         2     

  Premium    2.56      17186         NA          1     

  Premium    2.57      17842       688.2         3     

  Premium    2.58      16195         NA          1     

  Premium     2.6      17209         NA          1     

  Premium    2.63      10628         NA          1     

  Premium    2.65      16314         NA          1     

  Premium    2.68       8419         NA          1     

  Premium    2.71      17146         NA          1     

  Premium    2.75      15415         NA          1     

  Premium    2.77      10424         NA          1     

  Premium     2.8      15030         NA          1     

  Premium      3       15086        2664         2     

  Premium    3.01      14641        4734         6     

  Premium    3.04      18559         NA          1     

  Premium    3.05      10453         NA          1     

  Premium    3.24      12300         NA          1     

  Premium    3.51      18701         NA          1     

  Premium    3.67      16193         NA          1     

  Premium    4.01      15223         0           2     

   Ideal      0.2       367          0           3     

   Ideal     0.23      498.2       71.12        44     

   Ideal     0.24       555        104.4        69     

   Ideal     0.25      532.6       110.1        66     

   Ideal     0.26      566.4       122.6        106    

   Ideal     0.27      591.9       129.5        113    

   Ideal     0.28      613.1       124.2        81     

   Ideal     0.29      625.8       109.9        52     

   Ideal      0.3       706        165.2       1247    

   Ideal     0.31      741.7        177        1209    

   Ideal     0.32      759.4       173.2       1066    

   Ideal     0.33      801.1        180         673    

   Ideal     0.34       806        234.9        508    

   Ideal     0.35      830.8       188.7        374    

   Ideal     0.36      803.7       187.6        309    

   Ideal     0.37       841        203.4        172    

   Ideal     0.38      928.4       227.5        378    

   Ideal     0.39      936.7       207.4        181    

   Ideal      0.4      981.7       234.3        545    

   Ideal     0.41       1032       215.6        667    

   Ideal     0.42       1043       210.3        387    

   Ideal     0.43       1049       234.6        221    

   Ideal     0.44       1141       319.9        96     

   Ideal     0.45       1094        294         42     

   Ideal     0.46       1252       346.7        83     

   Ideal     0.47       1383        509         42     

   Ideal     0.48       1345       458.7        23     

   Ideal     0.49       1547        446          6     

   Ideal      0.5       1609       368.3        388    

   Ideal     0.51       1701       374.8        525    

   Ideal     0.52       1757       398.3        459    

   Ideal     0.53       1768       435.8        429    

   Ideal     0.54       1758       417.9        372    

   Ideal     0.55       1783       415.8        302    

   Ideal     0.56       1873       442.2        313    

   Ideal     0.57       1891       414.1        239    

   Ideal     0.58       1870       477.8        182    

   Ideal     0.59       1945       587.3        139    

   Ideal      0.6       2146       587.7        108    

   Ideal     0.61       2268       648.1        105    

   Ideal     0.62       2198       606.2        59     

   Ideal     0.63       2453        1067        49     

   Ideal     0.64       2330       677.8        39     

   Ideal     0.65       2699        1015        27     

   Ideal     0.66       2241       651.4        21     

   Ideal     0.67       2058       584.3         8     

   Ideal     0.68       3365       905.7         3     

   Ideal     0.69       2415       827.2         5     

   Ideal      0.7       2769       614.4        560    

   Ideal     0.71       2904       600.9        499    

   Ideal     0.72       2898        596         366    

   Ideal     0.73       3001       599.2        243    

   Ideal     0.74       3058       575.5        135    

   Ideal     0.75       2971       536.5        90     

   Ideal     0.76       3046       599.9        122    

   Ideal     0.77       3069       598.9        92     

   Ideal     0.78       3035       549.8        82     

   Ideal     0.79       3199       478.3        66     

   Ideal      0.8       3423        746         108    

   Ideal     0.81       3345       786.8        87     

   Ideal     0.82       3317       600.4        60     

   Ideal     0.83       3390       748.1        59     

   Ideal     0.84       3359        919         21     

   Ideal     0.85       3596       887.7        31     

   Ideal     0.86       3509       497.7        12     

   Ideal     0.87       3702        1072        15     

   Ideal     0.88       3831       722.6        10     

   Ideal     0.89       4089       464.9         6     

   Ideal      0.9       4330       876.3        215    

   Ideal     0.91       4279       991.8        108    

   Ideal     0.92       4392        852         55     

   Ideal     0.93       4234       788.6        35     

   Ideal     0.94       4645        1396        11     

   Ideal     0.95       4735        1120        14     

   Ideal     0.96       4162        1128        17     

   Ideal     0.97       3841        1161        17     

   Ideal     0.98       4415       428.6         5     

   Ideal     0.99       5553       528.2         5     

   Ideal       1        5547        1728        208    

   Ideal     1.01       5996        1812        426    

   Ideal     1.02       6005        2033        272    

   Ideal     1.03       6144        2052        225    

   Ideal     1.04       6062        1987        187    

   Ideal     1.05       6477        2037        147    

   Ideal     1.06       6372        2236        174    

   Ideal     1.07       6421        2112        156    

   Ideal     1.08       6261        2065        121    

   Ideal     1.09       6427        1979        141    

   Ideal      1.1       6505        2171        118    

   Ideal     1.11       6719        2454        130    

   Ideal     1.12       6789        2072        103    

   Ideal     1.13       6985        2682        113    

   Ideal     1.14       6927        2118        88     

   Ideal     1.15       6596        2177        56     

   Ideal     1.16       6975        2323        87     

   Ideal     1.17       6947        1822        56     

   Ideal     1.18       7252        2436        51     

   Ideal     1.19       7459        2674        49     

   Ideal      1.2       7413        2477        194    

   Ideal     1.21       7784        2354        160    

   Ideal     1.22       7605        2225        121    

   Ideal     1.23       7672        2363        127    

   Ideal     1.24       7744        2499        96     

   Ideal     1.25       7996        2686        77     

   Ideal     1.26       8089        2577        69     

   Ideal     1.27       7597        2613        56     

   Ideal     1.28       8500        3106        46     

   Ideal     1.29       7540        2205        40     

   Ideal      1.3       9210        2793        38     

   Ideal     1.31       9068        2085        51     

   Ideal     1.32       8770        2384        33     

   Ideal     1.33       8761        3062        28     

   Ideal     1.34       8206        2236        30     

   Ideal     1.35       8163        2763        28     

   Ideal     1.36      10405        2740        17     

   Ideal     1.37       9133        2634        19     

   Ideal     1.38       8967        3484        11     

   Ideal     1.39       9311        2974        13     

   Ideal      1.4       9874        3562        13     

   Ideal     1.41      10133        2834        10     

   Ideal     1.42       9171        3780         9     

   Ideal     1.43       9783        2631         8     

   Ideal     1.44       9638        3143         3     

   Ideal     1.45       7477         NA          1     

   Ideal     1.46       9193       984.9         3     

   Ideal     1.47       7284         NA          1     

   Ideal     1.48       8818         NA          1     

   Ideal     1.49      11307        6498         3     

   Ideal      1.5      11192        2730        117    

   Ideal     1.51      11431        2724        182    

   Ideal     1.52      11285        2735        109    

   Ideal     1.53      10866        2642        74     

   Ideal     1.54      11738        3389        53     

   Ideal     1.55      11408        2913        56     

   Ideal     1.56      11305        2649        45     

   Ideal     1.57      10769        2501        46     

   Ideal     1.58      12178        2494        42     

   Ideal     1.59      10863        2664        35     

   Ideal      1.6      11777        3352        40     

   Ideal     1.61      12394        2882        26     

   Ideal     1.62      12831        2811        25     

   Ideal     1.63      12224        3076        23     

   Ideal     1.64      12468        2561        13     

   Ideal     1.65      12195        3258        14     

   Ideal     1.66      11609        3276        11     

   Ideal     1.67      13556        2145        14     

   Ideal     1.68      12612        2097        11     

   Ideal     1.69      12169        3511         9     

   Ideal      1.7      12083        3206        59     

   Ideal     1.71      13153        3021        37     

   Ideal     1.72      11831        3907        10     

   Ideal     1.73      12483        3252         7     

   Ideal     1.74      13010        2685        15     

   Ideal     1.75      11928        3159        18     

   Ideal     1.76      12281        3224        10     

   Ideal     1.77      10500        2195         4     

   Ideal     1.78      15486        3737         2     

   Ideal     1.79      11579        1677         4     

   Ideal      1.8      14374        2947        11     

   Ideal     1.82      15802         NA          1     

   Ideal     1.83      13231        1813         3     

   Ideal     1.84       8828         NA          1     

   Ideal     1.85      14310       91.92         2     

   Ideal     1.86      10312         NA          1     

   Ideal     1.87      12618        4455         3     

   Ideal     1.89      15114        3450         2     

   Ideal      1.9      12304       196.6         2     

   Ideal     1.91      12321       759.1         3     

   Ideal     1.92      15472         NA          1     

   Ideal     1.93      18306         NA          1     

   Ideal     1.98      14581         0           2     

   Ideal       2       14738        2741        39     

   Ideal     2.01      15533        2359        78     

   Ideal     2.02      14870        2470        46     

   Ideal     2.03      15008        2253        38     

   Ideal     2.04      15602        2234        16     

   Ideal     2.05      16435        1994        17     

   Ideal     2.06      14777        2785        16     

   Ideal     2.07      15825        2019        18     

   Ideal     2.08      16096        2530        12     

   Ideal     2.09      15368        1913        12     

   Ideal      2.1      15450        2609        14     

   Ideal     2.11      15293        1838        10     

   Ideal     2.12      14836        2215         8     

   Ideal     2.13      15890        2257         6     

   Ideal     2.14      14509        1758        12     

   Ideal     2.15      15975        1903        10     

   Ideal     2.16      15563        3559        11     

   Ideal     2.17      17375       441.7         5     

   Ideal     2.18      15372       975.5         8     

   Ideal     2.19      16918        1496         6     

   Ideal      2.2      15839        1685        12     

   Ideal     2.21      16764        1814         6     

   Ideal     2.22      16131        1829         8     

   Ideal     2.24      16599        1146         7     

   Ideal     2.25      13648        3489         5     

   Ideal     2.26      15933        1853         5     

   Ideal     2.27      15664        2702         3     

   Ideal     2.28      15477        1720         6     

   Ideal     2.29      13809       653.9         3     

   Ideal      2.3      16098        1862        11     

   Ideal     2.32      17939       394.3         5     

   Ideal     2.33      16288        1719         2     

   Ideal     2.34      11221         NA          1     

   Ideal     2.36      15837        2631         6     

   Ideal     2.37      17073        1031         4     

   Ideal     2.39      17660       417.2         2     

   Ideal      2.4      15910        2653         7     

   Ideal     2.41      16987         NA          1     

   Ideal     2.42      16826         NA          1     

   Ideal     2.43      17856         NA          1     

   Ideal     2.45      17351        1078         2     

   Ideal     2.46      16106       509.8         2     

   Ideal     2.47      15430         NA          1     

   Ideal     2.48      12883         NA          1     

   Ideal     2.49      16915         NA          1     

   Ideal      2.5      15673        1048         4     

   Ideal     2.51      16841        1385         3     

   Ideal     2.52      17231         NA          1     

   Ideal     2.53      16351       506.3         2     

   Ideal     2.54      14816        2351         3     

   Ideal     2.56      17753         NA          1     

   Ideal     2.59      16465         NA          1     

   Ideal      2.6      18369         NA          1     

   Ideal     2.61      17982        1094         2     

   Ideal     2.63      16914         NA          1     

   Ideal     2.64      17407         NA          1     

   Ideal     2.72      14698        4389         2     

   Ideal     2.75      13156         NA          1     

   Ideal     3.01      16288       354.3         2     

   Ideal     3.22      12545         NA          1     

   Ideal      3.5      12587         NA          1     
-------------------------------------------------------

Whoa, that's a lot of variables. Since `carat` is numeric, each value is treated as unique. We can use `dplyr::ntile` to bin the values of `carat`:


```r
df_summary_bin <- ddply(df, .(cut, dplyr::ntile(carat, 5)), summarise,
                    mean_price = mean(price),
                    sd_price = sd(price),
                    n_samples = length(price))

pander::pander(
  df_summary_bin
)
```


------------------------------------------------------------------------
    cut      dplyr::ntile(carat, 5)   mean_price   sd_price   n_samples 
----------- ------------------------ ------------ ---------- -----------
   Fair                1                768.9       345.9        49     

   Fair                2                 1194        481         171    

   Fair                3                 2174       632.9        406    

   Fair                4                 3932        1272        557    

   Fair                5                 8672        4042        427    

   Good                1                574.9       134.4        694    

   Good                2                 1094       392.5        893    

   Good                3                 2358       618.6        973    

   Good                4                 4713        1356       1450    

   Good                5                 9788        3992        896    

 Very Good             1                596.6       145.6       2294    

 Very Good             2                 1146       425.8       2136    

 Very Good             3                 2473       699.8       2569    

 Very Good             4                 5243        1728       2785    

 Very Good             5                10155        3948       2298    

  Premium              1                710.8       169.1       2304    

  Premium              2                 1143       375.7       2477    

  Premium              3                 2358       620.4       2182    

  Premium              4                 5090        1579       3077    

  Premium              5                10116        4054       3751    

   Ideal               1                733.5       184.2       5447    

   Ideal               2                 1243       464.3       5111    

   Ideal               3                 2547       809.1       4658    

   Ideal               4                 5882        2013       2919    

   Ideal               5                10284        3853       3416    
------------------------------------------------------------------------

We can do whatever we want in our summaries, like if we want some R^2 value from a model... 


```r
df_summary_rsq <- ddply(df, .(cut), summarise,
                        rsquared <- summary(lm(price~carat))$adj.r.squared)

pander::pander(
  df_summary_rsq
)
```


--------------------
    cut       ..1   
----------- --------
   Fair      0.7382 

   Good      0.8509 

 Very Good   0.8582 

  Premium    0.8556 

   Ideal     0.8671 
--------------------

### Mutation

A related function, `mutate`, works similarly but rather than summarizing (taking multiple values and returning one), it creates a value for each input row


```r
df_mutate <- ddply(df, .(cut), mutate,
                   timesten = price*10,
                   n = seq(1, length(price)))

pander::pander(
  head(df_mutate)
)
```


-----------------------------------------------------------------------------
 carat   cut    color   clarity   depth   table   price    x      y      z   
------- ------ ------- --------- ------- ------- ------- ------ ------ ------
 0.22    Fair     E       VS2     65.1     61      337    3.87   3.78   2.49 

 0.86    Fair     E       SI2     55.1     69     2757    6.45   6.33   3.52 

 0.96    Fair     F       SI2     66.3     62     2759    6.27   5.95   4.07 

  0.7    Fair     F       VS2     64.5     57     2762    5.57   5.53   3.58 

  0.7    Fair     F       VS2     65.3     55     2762    5.63   5.58   3.66 

 0.91    Fair     H       SI2     64.4     57     2763    6.11   6.09   3.93 
-----------------------------------------------------------------------------

Table: Table continues below

 
--------------
 timesten   n 
---------- ---
   3370     1 

  27570     2 

  27590     3 

  27620     4 

  27620     5 

  27630     6 
--------------


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

