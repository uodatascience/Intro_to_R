---
title: "1 - Introduction "
author: "Cory Costello"
date: "September 25, 2017"
output: 
  md_document:
    preserve_yaml: true
    toc: true
    toc_depth: 2
order: 1
---

-   [What the F\#$% is R and how do I get started with
    it?](#what-the-f-is-r-and-how-do-i-get-started-with-it)
    -   [Installing R and R Studio](#installing-r-and-r-studio)
    -   [How do I R?!](#how-do-i-r)
    -   [Getting help](#getting-help)
-   [What is R?](#what-is-r)
    -   [Objects](#objects)
    -   [Functions](#functions)
    -   [Packages](#packages)
    -   [Some more useful basic syntax](#some-more-useful-basic-syntax)

What the F\#$% is R and how do I get started with it?
=====================================================

Installing R and R Studio
-------------------------

First, you will need to download R if you haven't already. You can do
that at this link: <https://www.r-project.org/>

Second, you'll want to download R studio. You can use R without R
studio, but you probably don't want to. R studio is an integrated
development environment (IDE;
<https://en.wikipedia.org/wiki/Integrated_development_environment>). R
studio can be downloaded at the following link:
<https://www.rstudio.com/>

Okay, that is all you need to start your gloRious adventuRe.

How do I R?!
------------

### Using RStudio

There are four main panels in the RStudio window.

The **top right** panel is the **workspace**. Here you will find a list
of all objects currently in memory.

The **bottom right** window displays files in your working directory,
plot output, available packages, and help documentation.

The **bottom left** panel is the **console**. You can type code directly
into the console and hit enter to execute it.

The **top left** panel is the **code editor**. This is where you will
write most of your code. In the code editor, you can write multiple
lines of code and run chunks or the full script. The toolbar at the top
of the panel allows you to interact with the code in the editor in some
important ways. Hover your mouse pointer over the icons to see what each
button does.

There are also shortcuts that allow you to use the toolbar functions
without ever taking your fingers from the keyboard. Here are some of the
most commonly used:

<table>
<thead>
<tr class="header">
<th>Function</th>
<th>Keys</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>Run highlighted code</td>
<td><code>Cmd+Enter</code></td>
</tr>
<tr class="even">
<td>Insert &lt;-</td>
<td><code>option+-</code></td>
</tr>
<tr class="odd">
<td>(Un)comment lines</td>
<td><code>Cmd+Shift+C</code></td>
</tr>
<tr class="even">
<td>Knit document</td>
<td><code>Cmd+Shift+K</code></td>
</tr>
<tr class="odd">
<td>Run from start to current line</td>
<td><code>Cmd+Option+B</code></td>
</tr>
<tr class="even">
<td>Keyboard shortcut quick reference</td>
<td><code>Option+Shift+K</code></td>
</tr>
</tbody>
</table>

Another important shortcut is `Tab` to autocomplete functions or file
names.

For a more complete list, check out the RStudio IDE Cheat Sheet. Find it
online by going to `Help>Cheatsheets>RStudio IDE Cheatsheet`, or by
clicking the following URL
<https://www.rstudio.com/wp-content/uploads/2016/01/rstudio-IDE-cheatsheet.pdf>

Getting help
------------

Check out the [r-project site](https://www.r-project.org/help.html) for
more on help documentation.

Within R, use `?functionName` or `??functionName`. The single `?` will
search for a function of that name whereas `??` will use your input as a
keyword to search in all R documentation.

What is R?
==========

R is a programming language designed for statistical computing. So, it
is (in many ways) more similar to another programming language (e.g.,
Python) than it is to statistical software you may have experience with
(e.g., SPSS). If you have no experience with programming languages and
have only ever used statistical software (e.g., SPSS) for your stats
then you will probably feel overwhelmed and confused by R for a little
while. That's fine and normal. Speaking for myself, I think I installed
and opened R studio about a half dozen times over the course of a year
before I ever used it becuase I had no idea where to get started.
Hopefully this (and the next few weeks of the Data Science Club) helps
you with that.

How do we use R. Lots of ways! For starters, it's like a calculator.
Sort of like the graphing calculators that you may have been fascinated
by at 16 (or at least I was for some reason). Because it's like a
calculator, we can use it like one.

    5+6

    ## [1] 11

Okay, but now we all have calculators in our phones, so this isn't that
cool. What else can R do?

Lots of stuff. Almost all of it revolves around *assigning* and then
later *calling* and *manipulating* objects. What is an object? It's just
R's way of representing information. And then we can use that
information later.

Objects
-------

How do we get R to represent some information as an object? We tell it
to, using the assignment operator '&lt;-'. Whatever we put on the left
side of the arrow becomes the name of the object and whatever is on the
right side of the arrow becomes the definition of that object. For
example, we may want to store what we had done earlier into an object.

    spinal_tap_amps <- 5 + 6

Now, we can tell R to do something with this object. For example, we
could get its sqaure root:

    sqrt(spinal_tap_amps)

    ## [1] 3.316625

Or we could use it in a logical statement

    if (spinal_tap_amps == 11) {
      print("But these go up to 11")
    }

    ## [1] "But these go up to 11"

    # And we could do this with other objects:
    other_amps <- 10

    if (other_amps == 11) {
      print("But these go up to 11")
    }

I know the above had some new stuff in it (like wtf is with the curly
brackets), but it's just to demonstrate that we can do more with objects
than just math.

We now have 2 different objects: spinal\_tap\_amps and other\_amps. We
can put these together into an object called amps. We'll do this like
so:

    amp_volumes <- c(spinal_tap_amps, other_amps)
    amp_volumes

    ## [1] 11 10

What happens if we take the sqrt of our new object?

    sqrt(amp_volumes)

    ## [1] 3.316625 3.162278

Would you look at that! It gave us the square roots of each of the
values in our new object. How did it know?

It knows because when we took two single-element objects and put them
together, R recognized this new object as a *vector*. When we tell R to
perform an operation on a vector, it will tend to perform that operation
on each element. This will not always be the case, as certain functions
will tell R to do something else, but it is generally the case.

Now I know what you're thinking. I don't work with vectors I work with
datasets! Just like we combined individual elements into a vector, we
can combine vectors into a dataset. Since we've been working with the
volume capacity of guitar amps (in case this hasn't been obvious, this
has all been a reference to spinal tap; see
<https://en.wikipedia.org/wiki/Spinal_Tap_(band)>), let's make a vector
for the names of the bands we're considering.

    Bands <- c("Spinal Tap", "Everyone Else")

Now let's put that and our vector of numbers together

    amp_volume_dataset <- cbind(amp_volumes, Bands)

And voila! We have a very simple dataset. Notice in the environment, R
studio knows that this new object is data rather than values (values
includes single objects/elements and vectors).

R has a few different formats for data. We'll likely talk more about
these in later weeks as they become relevant. But check this out anyway:

    amp_volume_dataset <- as.data.frame(amp_volume_dataset)

See, now it's a data frame. We could also change it to a data table
(with as.table()) or other formats.

The above illustrates another thing about r. If you use the same name
for a new object, it will overwrite the old object. Now we can't get our
old unformatted data object. We will have to make it again if we want
to:

    amp_volume_dataset <- cbind(amp_volumes, Bands)

And if we wanted both, we can just assign the dataframe to a new object

    amp_volume_dataframe <- as.data.frame(amp_volume_dataset)

Functions
---------

-   c() is a function for putting things together
    -   it stands for concatenate(mneumonic: it also shares an initial
        with combine)
    -   it takes objects as the input, combines them, and provides the
        combined object as an output
-   sqrt() is a function
    -   it takes numeric objects as inputs (elements, vectors,
        matrices), calculates square-root of each, and outputs the
        calculated square root.
-   as.data.frame() is a function
    -   it takes some object and formats it as a dataframe
-   print() is a function
    -   it takes some object as input, and the output is that object
        printed to the console
-   cbind() is a fucntion
    -   it takes at least two objects, and combines them column-wise
    -   this is a good example of arguments separated by a comma. If you
        glance back to the cbind() function we used, we passed it two
        vectors, separated by a comma. cbind() expects you to provide
        two objects that you want to combine column-wise; these two
        objects are the first two arguments of cbind, and we separate
        them with a comma.

Some of the functions that you'll use are already in your install of R.
You can write your own functions (more on this later on) or you can use
one's others have written, like sqrt().

Say we are confused about how a function works. We can google it (and
you should) and we can also use R's built in help. To access the help
file of a function, you enter a '?' followed by the package name:

    ?sqrt

    ## starting httpd help server ... done

The help documentation can be pretty confusing if you aren't quite
familiar with the package/function. The examples (which are typically at
the bottom) can be very helpful, even for beginners. And some are
simpler than others:

This one basically just says it computes square root of x, where x is a
vector or array. It also tells you (subtly) what the function works
with; square root expects a numerical vector (including a vector of 1,
or an element). If we give it a vector of non-number objects (like the
Bands list above), it won't work. Sometimes checking the help
documentation can tell you if you've given the function the wrong type
of object.

Packages
--------

Often times, functions are part of *packages* or *libraries* in R. These
are usually made by other people and have lots of functions you might
find useful. One that we will start using soon is called the tidy verse.
It's actually a set of packages that the authors put together (you can
also get each of the packages separately, butyou don't need to). To use
a new package, you'll first need to install it. Let's install the
tidyverse:

    #install.packages("tidyverse")

We just used the install.packages() function. This goes to cran, and
downloads the package into our user library (which is where we store all
of the packages we install). It requires you to enter the package name
in quotes, like we did above.

How does `install.packages()` know where to download the package from,
and where does it go? Check its documentation with `?install.packages`.
In the list of arguments we see `lib` and `repos = getOption("repos")`.
Further down we see that `lib` defaults to the first element of
`.libPaths()`. What do we get when we run those commands?

    getOption("repos")

    ##                                 CRAN                            CRANextra 
    ##                             "@CRAN@" "http://www.stats.ox.ac.uk/pub/RWin"

    .libPaths()

    ## [1] "C:/Users/Cory/Documents/R/win-library/3.4"
    ## [2] "C:/Program Files/R/R-3.4.1/library"

Most packages we use will come from cran, and if you ever need to modify
or find package files for any reason, there they shall be.

Now we have the package tidyverse, and can use it whenever we want. I'm
so excited that I'm going touse one of the packages within the tidyverse
called ggplot to make a simple plot of our spinal tap influenced
dataset!!!!!

    ggplot(data=amp_volume_dataframe, aes(x=Bands, y = amp_volumes))+
      geom_col()

OMG, it didn't work!!! Why?!?! I guess I'll have to go back to my
unsatisfying life in SPSS.

Wait, its no big deal, we just forgot one simple step. In order to use a
package, we have to call the library, or tell R that we're going to be
using this library (we're basically telling it to keep it in its working
memory). We load libraries into our environment with the library()
function. We don't need to put quotes (ie. make it a string) around the
name in the library function (side note: you will make the mistake of
quoting things in library and not quoting things in install.packages
upwards of 100 times). This puts the library in the list of packages to
search. For example, compare the output of `search()` before and after
loading a library:

    search()

    ## [1] ".GlobalEnv"        "package:stats"     "package:graphics" 
    ## [4] "package:grDevices" "package:utils"     "package:datasets" 
    ## [7] "package:methods"   "Autoloads"         "package:base"

    library(tidyverse)

    ## Loading tidyverse: ggplot2
    ## Loading tidyverse: tibble
    ## Loading tidyverse: tidyr
    ## Loading tidyverse: readr
    ## Loading tidyverse: purrr
    ## Loading tidyverse: dplyr

    ## Conflicts with tidy packages ----------------------------------------------

    ## filter(): dplyr, stats
    ## lag():    dplyr, stats

    search()

    ##  [1] ".GlobalEnv"        "package:dplyr"     "package:purrr"    
    ##  [4] "package:readr"     "package:tidyr"     "package:tibble"   
    ##  [7] "package:ggplot2"   "package:tidyverse" "package:stats"    
    ## [10] "package:graphics"  "package:grDevices" "package:utils"    
    ## [13] "package:datasets"  "package:methods"   "Autoloads"        
    ## [16] "package:base"

Importantly, you have to call all of the libraries you will use each
time you start a new session in R. You may want to get in the habit of
putting a bunch of library calls at the top of your script.

    library(tidyverse)

And now let's try to use ggplot

    ggplot(data=amp_volume_dataframe, aes(x=Bands, y = amp_volumes))+
      geom_col()

![](01_Introduction_files/figure-markdown_strict/GGPlot%20works!-1.png)
It worked! We'll spend some time talking about ggplot (led by Jonny) a
little later on, so don't worry about what that code means. Soon you'll
know.

What happens when two packages have functions with the same name? for
example, both dplyr and plyr have functions named "summarise" that
behave slightly differently. We can solve these "namespace conflicts" by
being specific about where we load the library in our search paths. If
we want to load the functions from plyr, but keep all our existing
references to dplyr, we could do something like this (although if you've
already loaded tidyverse, this won't do anything because these packages
are loaded by tidyverse)

    library(plyr, pos=100) # We can put it at an arbitrarily high number, it will be after everything but the base functions

    ## -------------------------------------------------------------------------

    ## You have loaded plyr after dplyr - this is likely to cause problems.
    ## If you need functions from both plyr and dplyr, please load plyr first, then dplyr:
    ## library(plyr); library(dplyr)

    ## -------------------------------------------------------------------------

    ## 
    ## Attaching package: 'plyr'

    ## The following objects are masked _by_ 'package:dplyr':
    ## 
    ##     arrange, count, desc, failwith, id, mutate, rename, summarise,
    ##     summarize

    ## The following object is masked _by_ 'package:purrr':
    ## 
    ##     compact

    library(dplyr)

Or, we could make our code more explicit. We can directly reference a
package's functions without loading the library by using `::`, for
example with

    ggplot2::ggplot(data=amp_volume_dataframe, ggplot2::aes(x=Bands, y = amp_volumes))+
      ggplot2::geom_col()

![](01_Introduction_files/figure-markdown_strict/direct%20function%20reference-1.png)

Some more useful basic syntax
-----------------------------

We've covered some of the basics of R syntax (e.g., the assignment
operator '&lt;-')

Here are a few more aspects of R syntax that are helpful to keep in mind

-   R is case sensitive. So a and A would be two different objects
    -   A very common syntax mistake is using the wrong case (e.g., you
        assigned an object to a, and try to call it with A)
-   A '\#' in R used for commenting. Once you place a '\#', everything
    on that lines that follows will be ignored by R. There are two main
    uses for this:
    1.  Putting comments into your code to explain it to people reading
        it (including your future self)
    2.  Removing code you don't want to run but want to keep in the
        script for record keeping's sake.
-   R is newline terminated, meaning that things on the same line are
    considered a single statement. However, if a statement is incomplete
    (e.g., ends in an open parenthesis, or ends with a comma), R will
    search on the next line for the resolution. See the example below

<!-- -->

    # Create 2 example vectors
    example_part1 <- runif(100)
    example_part2 <- runif(100)

    # All three of these are equivalent:

    example <- cbind(example_part1, example_part2)
    example <- cbind(example_part1, 
                example_part2)
    example <- cbind(example_part1
                ,example_part2)

-   You can typically subset objects using \[\]. If the object is a
    matrix (i.e., has rows and columns), the argument order is
    \[rows, columns\]. If the object is a vector, then only one number
    goes in the bracket \[rows\]. Here are some examples of subsetting
    the example object from above
    -   example\[1:50,\]
        -   This would give us the first 50 rows, and all columns
    -   example\[1:50, 1\]
        -   This would give us the first 50 rows and just the first
            column
    -   example\[,1\]
        -   This would give us all rows, just the first column
-   If you are using logical statements, you use "==" for is equal to.
    So, if we wanted to see if any elements in example is equal to 1, we
    would use the following:
    -   example == 1
        -   This would provide us with an object the same dimensions as
            example, with TRUE or FALSE in every cell, where TRUE means
            the value in the cell was equal to 1 and FALSE means the
            value in the cell was not equal to 1.
    -   One reason for this is that '=' can be used as an assignment
        operator (it is equivalent to '&lt;-') and is also used in
        defining arguments in functions (e.g., instead of sqrt(example)
        we could write sqrt(x=example) because the first argument in
        sqrt is called x, so we can use an '=' to define it).
-   Some other logical statements:
    -   "==" means is equal to (from above)
    -   "!=" means not equal to
    -   "&gt;" means greater than
    -   "&lt;" means less than
    -   "&lt;=" means less than or equal to
    -   "&gt;=" means greater than or equal to

That is all for this basic overview of R. Hopefully it has started to
make youR adventuRe moRe toleRable!
