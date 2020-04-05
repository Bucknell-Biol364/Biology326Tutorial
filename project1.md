BIOL 326 Tutorial
================

## Introduction

Hello, and welcome to R Studio. Right now we are writing in an Rmd file,
which allows us to easily seperate the code that is actually going to be
run from comments like this. Too add a code chunk, just press the Insert
button above this window and select the R option. After you have
finished writing in this box, you can press the arrow on the right hand
side of the box to run your code.

The goal of this tutorial is to be able to use basic R skills in a
biological context, such as reading in datasets and graphing data. We
also introduce skills such as if statements and for loops, in order to
do basic operations on the data so that the proper data for the graphs
is generated.

## Packages

Our first objective is to read in the packages we need for our project.
This section should come early in your Rmd file, but you may have to
actually examine your data before we know what packages you have to use.
After you have decided what packages are necessary, you can come back to
this code and load in your packages.

We use tidyverse in order to have ggplot, a helpful package for created
graphs. We use cowplot in order to style our graphs to make them easily
readable. We use UsingR for basic statistics.

## Dataset

Now we have to read in our dataset. The paper we are using is title
“Traction Force on a Kinetochore at Metaphase Acts as a Linear
Function of Kinetochore Fiber Length” by Thomas S. Hays. In this
particular project, we are looking at the relationship between the
length of the kinetochore fibers of trivalent chromosomes duirng mitosis
and the resultant force upon the kinetochores done by the spindle
fibers. In our dataset, we have a list of the cells and their
trivalents, and the lengths of each of the kinetochore fibers of the
trivalent. We took the data from Table I of the paper.

We use the code below to read in our dataset and look at it using the
str() function.

``` r
dataset<-read.csv("hays_data.csv")
str(dataset)
```

    ## 'data.frame':    7 obs. of  6 variables:
    ##  $ Cell                    : int  1 2 3 4 5 6 7
    ##  $ Multivariant            : Factor w/ 7 levels "Trivalent 1",..: 1 2 3 4 5 6 7
    ##  $ A1                      : num  4.5 8 5.5 6 7.5 4 6
    ##  $ B1                      : num  6 8 5 6.5 2 6 7
    ##  $ A2                      : num  10.5 10 11.5 13 9.5 9.5 9
    ##  $ Spindle.length..micro.m.: num  27.5 25.5 25 27 25 25 24

## Paper Background

In the paper, it was discussed that there would be an exponential
relationship between the resulant for (RF) on the kinetochore and the
length of the kinetochore fiber itself. Exponential relationships take
the form F=kA^exponent. To take into account each of the three
kinetochores on the trivalent, the paper uses the formula RF =
((A1^exponent) + (B1^exponent)) - (A2^exponent), whith A1, B1, and A2
being the lengths of each of the kinetochore fibers.

The paper’s hypothesis is that the resultant force will be at 0 when the
exponent is set to 1, as the resultant force on the chromosome should be
0 when the chromosome is in metaphase. They are essentially predicting
that at metaphase, the model predicting resultant force can be linear
instead of exponential.

Question: Will the resultant force be 0 when the exponent is 1?

## Code explanation

In the paper, they stated that to get each data point, they plugged each
number from their data table (A1, A2, and B1) into their formula: RF =
((A1^exponent) + (B1^exponent)) - (A2^exponent). The exponent changes
value from 0.1 to 4.0. When each exponent has been used in the formula
for that trivalent, those data points can be plotted. This process is
repeated for each of the seven trivalents in the data table.

To code this quickly and plot it into a table, a for loop is required.
First, we had to create a list of the exponent values from 0.1 to 4.0.
To do this, we gave it the name “exponents” and then put each value into
“c()”. Next, we had to create an empty list for the data points we were
generating to go. This was just called “RF”. Then, we needed to call to
the .csv file with the data in it. Each column had a different variable
(A1, A2, and B1) inside of it. To be able to easily put those into the
formula in the for loop, we had to create those variable names and then
use the code “dataset\[row number, column name\]”. The column name would
stay the same for each trivalent, but the row number would increase by
one every trivalent.

Now, the for loop can be started. For loops start with “for (val in list
name) {”. Then, the code that you want the program to loop through goes
inside of this bracket. The list name is “exponents” in this case
because we want to filter through those values in the formula each time.
We then had to assign the arbitrary variable i to the variable name b to
be plugged into the RF list we made before. Now, we change RF from an
empty list, to “RF \<- c(RF, b)”, meaning that the new list is the old
list contents with the value of b added onto the end.

Then, we had to put an if statement into the loop. This allows us to
check to make sure that we get through all the values in our for loop.
So it is essentially saying “if the value in the list ‘exponents’ is
less than 4.0, i = ((A1^val) + (B1^val)) - (A2^val)” which is the
formula used in the paper. Remember that A1, B1, and A2 were assigned
values from the .csv file outside of the for loop and if statement. Now,
the for loop can be closed with a “}”.

Finally, we were able to plot the points we made. Inside aes, we were
able to set the x and y axes as “exponents”, which was the list we made
first, and “RF” which was the list of points we generated with the for
loop. We then labeled the axes with xlab and ylab and then titled it
depending on the trivalent number with ggtitle.

We repeated this process for all 7 of the
trivalents.

``` r
exponents <- c(0.1,0.2,0.3,0.4,0.5,0.6,0.7,0.8,0.9,1.0,1.1,1.2,1.3,1.4,1.5,1.6,1.7,1.8,1.9,2.0,2.1,2.2,2.3,2.4,2.5,2.6,2.7,2.8,2.9,3.0,3.1,3.2,3.3,3.4,3.5,3.6,3.7,3.8,3.9,4.0)
RF <- c()
i = 0
A1 <- dataset[1, "A1"]
B1 <- dataset[1, "B1"]
A2 <- dataset[1, "A2"]
for (val in exponents){
  b <- i
  RF <- c(RF,b)
  if(val < 4.0) i = ((A1^val) + (B1^val)) - (A2^val)
                     
}

ggplot() + aes(x = exponents, y = RF) + geom_point()  +
  theme_cowplot()  +
  ylim(-8,8) +
  xlab("Exponents") + 
  ylab("Resultant Force") + ggtitle("Trivalent 1")
```

    ## Warning: Removed 25 rows containing missing values (geom_point).

![](project1_files/figure-gfm/Triv%201-1.png)<!-- -->

``` r
exponents <- c(0.1,0.2,0.3,0.4,0.5,0.6,0.7,0.8,0.9,1.0,1.1,1.2,1.3,1.4,1.5,1.6,1.7,1.8,1.9,2.0,2.1,2.2,2.3,2.4,2.5,2.6,2.7,2.8,2.9,3.0,3.1,3.2,3.3,3.4,3.5,3.6,3.7,3.8,3.9,4.0)
RF <- c()
i = 0
A1 <- dataset[2, "A1"]
B1 <- dataset[2, "B1"]
A2 <- dataset[2, "A2"]
for (val in exponents){
  b <- i
  RF <- c(RF,b)
  if(val < 4.0) i = ((A1^val) + (B1^val)) - (A2^val)
                     
}

ggplot() + aes(x = exponents, y = RF) + geom_point()  +
  theme_cowplot()  +
  ylim(-8,8) +
  xlab("Exponents") + 
  ylab("Resultant Force") + ggtitle("Trivalent 2")
```

    ## Warning: Removed 27 rows containing missing values (geom_point).

![](project1_files/figure-gfm/Triv%202-1.png)<!-- -->

``` r
exponents <- c(0.1,0.2,0.3,0.4,0.5,0.6,0.7,0.8,0.9,1.0,1.1,1.2,1.3,1.4,1.5,1.6,1.7,1.8,1.9,2.0,2.1,2.2,2.3,2.4,2.5,2.6,2.7,2.8,2.9,3.0,3.1,3.2,3.3,3.4,3.5,3.6,3.7,3.8,3.9,4.0)
RF <- c()
i = 0
A1 <- dataset[3, "A1"]
B1 <- dataset[3, "B1"]
A2 <- dataset[3, "A2"]
for (val in exponents){
  b <- i
  RF <- c(RF,b)
  if(val < 4.0) i = ((A1^val) + (B1^val)) - (A2^val)
                     
}

ggplot() + aes(x = exponents, y = RF) + geom_point()  +
  theme_cowplot()  +
  ylim(-8,8) +
  xlab("Exponents") + 
  ylab("Resultant Force") + ggtitle("Trivalent 3")
```

    ## Warning: Removed 26 rows containing missing values (geom_point).

![](project1_files/figure-gfm/Triv%203-1.png)<!-- -->

``` r
exponents <- c(0.1,0.2,0.3,0.4,0.5,0.6,0.7,0.8,0.9,1.0,1.1,1.2,1.3,1.4,1.5,1.6,1.7,1.8,1.9,2.0,2.1,2.2,2.3,2.4,2.5,2.6,2.7,2.8,2.9,3.0,3.1,3.2,3.3,3.4,3.5,3.6,3.7,3.8,3.9,4.0)
RF <- c()
i = 0
A1 <- dataset[4, "A1"]
B1 <- dataset[4, "B1"]
A2 <- dataset[4, "A2"]
for (val in exponents){
  b <- i
  RF <- c(RF,b)
  if(val < 4.0) i = ((A1^val) + (B1^val)) - (A2^val)
                     
}

ggplot() + aes(x = exponents, y = RF) + geom_point()  +
  theme_cowplot()  +
  ylim(-8,8) +
  xlab("Exponents") + 
  ylab("Resultant Force") + ggtitle("Trivalent 4")
```

    ## Warning: Removed 26 rows containing missing values (geom_point).

![](project1_files/figure-gfm/Triv%204-1.png)<!-- -->

``` r
exponents <- c(0.1,0.2,0.3,0.4,0.5,0.6,0.7,0.8,0.9,1.0,1.1,1.2,1.3,1.4,1.5,1.6,1.7,1.8,1.9,2.0,2.1,2.2,2.3,2.4,2.5,2.6,2.7,2.8,2.9,3.0,3.1,3.2,3.3,3.4,3.5,3.6,3.7,3.8,3.9,4.0)
RF <- c()
i = 0
A1 <- dataset[5, "A1"]
B1 <- dataset[5, "B1"]
A2 <- dataset[5, "A2"]
for (val in exponents){
  b <- i
  RF <- c(RF,b)
  if(val < 4.0) i = ((A1^val) + (B1^val)) - (A2^val)
                     
}

ggplot() + aes(x = exponents, y = RF) + geom_point()  +
  theme_cowplot()  +
  ylim(-8,8) +
  xlab("Exponents") + 
  ylab("Resultant Force") + ggtitle("Trivalent 5")
```

    ## Warning: Removed 24 rows containing missing values (geom_point).

![](project1_files/figure-gfm/Triv%205-1.png)<!-- -->

``` r
exponents <- c(0.1,0.2,0.3,0.4,0.5,0.6,0.7,0.8,0.9,1.0,1.1,1.2,1.3,1.4,1.5,1.6,1.7,1.8,1.9,2.0,2.1,2.2,2.3,2.4,2.5,2.6,2.7,2.8,2.9,3.0,3.1,3.2,3.3,3.4,3.5,3.6,3.7,3.8,3.9,4.0)
RF <- c()
i = 0
A1 <- dataset[6, "A1"]
B1 <- dataset[6, "B1"]
A2 <- dataset[6, "A2"]
for (val in exponents){
  b <- i
  RF <- c(RF,b)
  if(val < 4.0) i = ((A1^val) + (B1^val)) - (A2^val)
                     
}

ggplot() + aes(x = exponents, y = RF) + geom_point()  +
  theme_cowplot()  +
  ylim(-8,8) +
  xlab("Exponents") + 
  ylab("Resultant Force") + ggtitle("Trivalent 6")
```

    ## Warning: Removed 24 rows containing missing values (geom_point).

![](project1_files/figure-gfm/Triv%206-1.png)<!-- -->

``` r
exponents <- c(0.1,0.2,0.3,0.4,0.5,0.6,0.7,0.8,0.9,1.0,1.1,1.2,1.3,1.4,1.5,1.6,1.7,1.8,1.9,2.0,2.1,2.2,2.3,2.4,2.5,2.6,2.7,2.8,2.9,3.0,3.1,3.2,3.3,3.4,3.5,3.6,3.7,3.8,3.9,4.0)
RF <- c()
i = 0
A1 <- dataset[7, "A1"]
B1 <- dataset[7, "B1"]
A2 <- dataset[7, "A2"]
for (val in exponents){
  b <- i
  RF <- c(RF,b)
  if(val < 4.0) i = ((A1^val) + (B1^val)) - (A2^val)
                     
}

ggplot() + aes(x = exponents, y = RF) + geom_point()  +
  theme_cowplot()  +
  ylim(-8,8) +
  xlab("Exponents") + 
  ylab("Resultant Force") + ggtitle("Trivalent 7")
```

    ## Warning: Removed 16 rows containing missing values (geom_point).

![](project1_files/figure-gfm/Triv%207-1.png)<!-- -->

## Analysis

After we have generated our graphs, we want to look at each of the
intercepts to see if the exponents are 1 when the resultant force is 0,
as the hypothesis said. We created a file with the intercepts, and read
it in below.

``` r
intercepts<-read.csv("Trivalent intercepts.csv")
str(intercepts)
```

    ## 'data.frame':    7 obs. of  2 variables:
    ##  $ ï..Trivalent: int  1 2 3 4 5 6 7
    ##  $ Intercept   : num  1.5 3.6 1.4 1.4 1.7 1.7 2.4

We run the summary function on our intercepts to find the mean, and the
sd function on the intercepts to find the standard deviation.

``` r
summary(intercepts)
```

    ##   ï..Trivalent   Intercept    
    ##  Min.   :1.0   Min.   :1.400  
    ##  1st Qu.:2.5   1st Qu.:1.450  
    ##  Median :4.0   Median :1.700  
    ##  Mean   :4.0   Mean   :1.957  
    ##  3rd Qu.:5.5   3rd Qu.:2.050  
    ##  Max.   :7.0   Max.   :3.600

``` r
sd(intercepts$Intercept)
```

    ## [1] 0.8017837

We compared the mean and standard deviation generated by our data
analysis with those found in Table II of the paper.

## Conclusion

The paper found that, with the exception of trivalent 2, the mean of the
exponent when the resultant force was 0 was very close to 1. Our mean
was higher, as most of our measurements of the exponent when the
resultant force was 0 were higher. Our results on trivalent 2 were
similar to the paper’s as its exponent was higher than the rest;
however, our results on trivalent 7 were higher as well, differing from
the paper.

Importantly, it is not recommended that mitotic processes be measured
across various cells, rendering the paper’s results questionable.

## Sources Used

datamentor.io/r-programming/for-loop/
ggplot2.tidyverse.org/reference/lims/html
<https://campus.datacamp.com/courses/free-introduction-to-r/chapter-5-data-frames?ex=6>

## Acknowledgements

Work split evenly between Alyssa Peeples and Owen LaFramboise
