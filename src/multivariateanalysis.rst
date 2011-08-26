Using R for Multivariate Analysis
=================================

Multivariate Analysis
---------------------

This booklet itells you how to use the R statistical software to carry out some simple multivariate analyses,
with a focus on principal components analysis (PCA) and linear discriminant analysis (LDA).

This booklet assumes that the reader has some basic knowledge of multivariate analyses, and
the principal focus of the booklet is not to explain multivariate analyses, but rather 
to explain how to carry out these analyses using R.

If you are new to multivariate analysis, and want to learn more about any of the concepts
presented here, I would highly recommend the Open University book 
"Multivariate Analysis" (product code M249/03), available from
from `the Open University Shop <http://www.ouw.co.uk/store/>`_.

In the examples in this booklet, I will be using data sets from the UCI Machine
Learning Repository, `http://archive.ics.uci.edu/ml <http://archive.ics.uci.edu/ml>`_.

There is a pdf version of this booklet available at
`https://github.com/avrilcoghlan/LittleBookofRTimeSeries/raw/master/_build/latex/MultivariateAnalysis.pdf <https://github.com/avrilcoghlan/LittleBookofRTimeSeries/raw/master/_build/latex/MultivariateAnalysis.pdf>`_.

If you like this booklet, you may also like to check out my booklet on using
R for biomedical statistics, 
`http://a-little-book-of-r-for-biomedical-statistics.readthedocs.org/
<http://a-little-book-of-r-for-biomedical-statistics.readthedocs.org/>`_,
and my booklet on using R for time series analysis,
`http://a-little-book-of-r-for-time-series.readthedocs.org/
<http://a-little-book-of-r-for-time-series.readthedocs.org/>`_.

Reading Multivariate Analysis Data into R
-----------------------------------------

The first thing that you will want to do to analyse your multivariate data will be to read
it into R, and to plot the data. You can read data into R using the read.table() function.

For example, the file `http://archive.ics.uci.edu/ml/machine-learning-databases/wine/wine.data
<http://archive.ics.uci.edu/ml/machine-learning-databases/wine/wine.data>`_
contains data on concentrations of 13 different chemicals in wines grown in the same region in Italy that are
derived from three different cultivars.

The data set looks like this:

.. highlight:: r

::

    1,14.23,1.71,2.43,15.6,127,2.8,3.06,.28,2.29,5.64,1.04,3.92,1065
    1,13.2,1.78,2.14,11.2,100,2.65,2.76,.26,1.28,4.38,1.05,3.4,1050
    1,13.16,2.36,2.67,18.6,101,2.8,3.24,.3,2.81,5.68,1.03,3.17,1185
    1,14.37,1.95,2.5,16.8,113,3.85,3.49,.24,2.18,7.8,.86,3.45,1480
    1,13.24,2.59,2.87,21,118,2.8,2.69,.39,1.82,4.32,1.04,2.93,735
    ... 

There is one row per wine sample.
The first column contains the cultivar of a wine sample (labelled 1, 2 or 3), and the following thirteen columns
contain the concentrations of the 13 different chemicals in that sample.
The columns are separated by commas. 

When we read the file into R using the read.table() function, we need to use the "sep="
argument in read.table() to tell it that the columns are separated by commas.
That is, we can read in the file using the read.table() function as follows:

.. highlight:: r

::

    > wine <- read.table("http://archive.ics.uci.edu/ml/machine-learning-databases/wine/wine.data", sep=",")
    > wine
         V1    V2   V3   V4   V5  V6   V7   V8   V9  V10       V11   V12  V13  V14
     1    1 14.23 1.71 2.43 15.6 127 2.80 3.06 0.28 2.29  5.640000 1.040 3.92 1065
     2    1 13.20 1.78 2.14 11.2 100 2.65 2.76 0.26 1.28  4.380000 1.050 3.40 1050
     3    1 13.16 2.36 2.67 18.6 101 2.80 3.24 0.30 2.81  5.680000 1.030 3.17 1185
     4    1 14.37 1.95 2.50 16.8 113 3.85 3.49 0.24 2.18  7.800000 0.860 3.45 1480
     5    1 13.24 2.59 2.87 21.0 118 2.80 2.69 0.39 1.82  4.320000 1.040 2.93  735
     ...
     176  3 13.27 4.28 2.26 20.0 120 1.59 0.69 0.43 1.35 10.200000 0.590 1.56  835
     177  3 13.17 2.59 2.37 20.0 120 1.65 0.68 0.53 1.46  9.300000 0.600 1.62  840
     178  3 14.13 4.10 2.74 24.5  96 2.05 0.76 0.56 1.35  9.200000 0.610 1.60  560
     
In this case the data on 178 samples of wine has been read into the variable 'wine'.

Plotting Multivariate Data
--------------------------

Once you have read a multivariate data set into R, the next step is usually to make a plot of the data.

A Matrix Scatterplot
^^^^^^^^^^^^^^^^^^^^

One common way of plotting multivariate data is to make a "matrix scatterplot", showing each pair of
variables plotted against each other. We can use the "scatterplotMatrix()" function from the "car"
R package to do this. To use this function, we first need to install the "car" R package 
(for instructions on how to install an R package, see `How to install an R package 
<./installr.html#how-to-install-an-r-package>`_).

Once you have installed the "car" R package, you can load the "car" R package by typing:

.. highlight:: r

::

    > library("car")

You can then use the "scatterplotMatrix()" function to plot the multivariate data. 

To use the scatterplotMatrix() function, you need to give it as its input the variables
that you want included in the plot. Say for example, that we just want to include the
variables corresponding to the concentrations of the first five chemicals. These are stored in 
columns 2-6 of the variable "wine". We can extract just these columns from the variable
"wine" by typing:

::

    > wine[2:6]
             V2   V3   V4   V5  V6  
      1   14.23 1.71 2.43 15.6 127 
      2   13.20 1.78 2.14 11.2 100
      3   13.16 2.36 2.67 18.6 101 
      4   14.37 1.95 2.50 16.8 113
      5   13.24 2.59 2.87 21.0 118 
      ...

To make a matrix scatterplot of just these 13 variables using the scatterplotMatrix() function we type:

::

    > scatterplotMatrix(wine[2:6])


|image1|


In this matrix scatterplot, the diagonal cells show histograms of each of the variables, in this
case the concentrations of the first five chemicals (variables V2, V3, V4, V5, V6). 

Each of the off-diagonal cells is a scatterplot of two of the five chemicals, for example, the second cell in the
first row is a scatterplot of V2 (y-axis) against V3 (x-axis). 

A Scatterplot with the Data Points Labelled by their Group
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If you see an interesting scatterplot for two variables in the matrix scatterplot, you may want to
plot that scatterplot in more detail, with the data points labelled by their group (their cultivar in this case).

For example, in the matrix scatterplot above, the cell in the third column of the fourth row down is a scatterplot
of V5 (x-axis) against V4 (y-axis). If you look at this scatterplot, it appears that there may be a 
positive relationship between V5 and V4. 

We may therefore decide to examine the relationship between V5 and V4 more closely, by plotting a scatterplot
of these two variable, with the data points labelled by their group (their cultivar). To plot a scatterplot
of two variables, we can use the "plot" R function. The V4 and V5 variables are stored in the columns
V4 and V5 of the variable "wine", so can be accessed by typing wine$V4 or wine$V5. Therefore, to plot
the scatterplot, we type:

::

    > plot(wine$V4, wine$V5)

|image2|

If we want to label the data points by their group (the cultivar of wine here), we can use the "text" function
in R to plot some text beside every data point. In this case, the cultivar of wine is stored in the column
V1 of the variable "wine", so we type:

::

    > text(wine$V4, wine$V5, wine$V1, cex=0.7, pos=4, col="red")

If you look at the help page for the "text" function, you will see that "pos=4" will plot the text just to the
right of the symbol for a data point. The "cex=0.5" option will plot the text at half the default size, and
the "col=red" option will plot the text in red. This gives us the following plot:

|image4|

We can see from the scatterplot of V4 versus V5 that the wines from cultivar 2 seem to have
lower values of V4 compared to the wines of cultivar 1. 

A Profile Plot
^^^^^^^^^^^^^^

Another type of plot that is useful is a "profile plot", which shows the variation in each of the
variables, by plotting the value of each of the variables for each of the samples. 

The function "makeProfilePlot()" below can be used to make a profile plot. This function requires
the "RColorBrewer" library. To use this function, we first need to install the "RColorBrewer" R package 
(for instructions on how to install an R package, see `How to install an R package 
<./installr.html#how-to-install-an-r-package>`_).

::

    > makeProfilePlot <- function(mylist,names)
      {
         require(RColorBrewer)
         # find out how many variables we want to include
         numvariables <- length(mylist)   
         # choose 'numvariables' random colours
         colours <- brewer.pal(numvariables,"Set1")
         # find out the minimum and maximum values of the variables:
         mymin <- 1e+20
         mymax <- 1e-20
         for (i in 1:numvariables)
         {
            vectori <- mylist[[i]]
            mini <- min(vectori)
            maxi <- max(vectori)
            if (mini < mymin) { mymin <- mini }
            if (maxi > mymax) { mymax <- maxi }
         }
         # plot the variables
         for (i in 1:numvariables)
         {
            vectori <- mylist[[i]]
            namei <- names[i]
            colouri <- colours[i]
            if (i == 1) { plot(vectori,col=colouri,type="l",ylim=c(mymin,mymax)) }
            else         { points(vectori, col=colouri,type="l")                                     }
            lastxval <- length(vectori)
            lastyval <- vectori[length(vectori)]
            text((lastxval-10),(lastyval),namei,col="black",cex=0.6)
         }
      }

To use this function, you first need to copy and paste it into R. The arguments to the
function are a vector containing the names of the varibles that you want to plot, and
a list variable containing the variables themselves. 

For example, to make a profile plot of the concentrations of the first five chemicals in the wine samples
(stored in columns V2, V3, V4, V5, V6 of variable "wine"), we type:

::

    > library(RColorBrewer)
    > names <- c("V2","V3","V4","V5","V6")
    > mylist <- list(wine$V2,wine$V3,wine$V4,wine$V5,wine$V6)
    > makeProfilePlot(mylist,names)

|image5|

It is clear from the profile plot that the mean and standard deviation for V6 is
quite a lot higher than that for the other variables.

.. xxx why did they do quite a different profile plot in the assignment answer?

Calculating Summary Statistics for Multivariate Data
----------------------------------------------------

Another thing that you are likely to want to do is to calculate summary statistics such as the
mean and standard deviation for each of the variables in your multivariate data set.

This is easy to do, using the "mean()" and "sd()" functions in R. For example, say we want
to calculate the mean and standard deviations of each of the 13 chemical concentrations in the
wine samples. These are stored in columns 2-14 of the variable "wine". So we type:

::

    > mean(wine[2:14]) 
              V2          V3          V4          V5          V6          V7 
      13.0006180   2.3363483   2.3665169  19.4949438  99.7415730   2.2951124 
              V8          V9         V10         V11         V12         V13 
       2.0292697   0.3618539   1.5908989   5.0580899   0.9574494   2.6116854 
              V14 
     746.8932584 
      
This tells us that the mean of variable V2 is 13.0006180, the mean of V3 is 2.3363483, and so on.

Similarly, to get the standard deviations of the 13 chemical concentrations, we type:

::

    > sd(wine[2:14]) 
              V2          V3          V4          V5          V6          V7 
       0.8118265   1.1171461   0.2743440   3.3395638  14.2824835   0.6258510 
              V8          V9         V10         V11         V12         V13 
       0.9988587   0.1244533   0.5723589   2.3182859   0.2285716   0.7099904 
              V14 
     314.9074743 

We can see here that it would make sense to standardise in order to compare the variables because the variables
have very different standard deviations - the standard deviation of V14 is 314.9074743, while the standard deviation
of V9 is just 0.1244533. Thus, in order to compare the variables, we need to standardise each variable so that
it has a sample variance of 1 and sample mean of 0. We will explain below how to standardise the variables.

Means and Variances Per Group
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

It is often interesting to calculate the means and standard deviations for just the samples
from a particular group, for example, for the wine samples from each cultivar. The cultivar
is stored in the column "V1" of the variable "wine".

To extract out the data for just cultivar 2, we can type:

::

    > cultivar2wine <- wine[wine$V1=="2",] 

We can then calculate the mean and standard deviations of the 13 chemicals' concentrations, for
just the cultivar 2 samples:

::

    > mean(cultivar2wine[2:14])
            V2         V3         V4         V5         V6         V7         V8         V9        V10        V11        V12 
      12.278732   1.932676   2.244789  20.238028  94.549296   2.258873   2.080845   0.363662   1.630282   3.086620   1.056282 
            V13        V14 
       2.785352 519.507042 
    > sd(cultivar2wine[2:14]) 
            V2          V3          V4          V5          V6          V7          V8          V9         V10         V11 
      0.5379642   1.0155687   0.3154673   3.3497704  16.7534975   0.5453611   0.7057008   0.1239613   0.6020678   0.9249293 
           V12         V13         V14 
      0.2029368   0.4965735 157.2112204 

You can calculate the mean and standard deviation of the 13 chemicals' concentrations for just cultivar 1 samples,
or for just cultivar 3 samples, in a similar way.

However, for convenience, you might want to use the function "printMeanAndSdByGroup()" below, which
prints out the mean and standard deviation of the variables for each group in your data set:

::

    > printMeanAndSdByGroup <- function(variables,groupvariable)
      {
         # find out how many variables we have
         variables <- as.data.frame(variables)
         numvariables <- length(variables)   
         # find out how many values the group variable can take
         groupvariable2 <- as.factor(groupvariable[[1]])
         levels <- levels(groupvariable2)
         numlevels <- length(levels)
         for (i in 1:numlevels)
         {
            leveli <- levels[i]
            levelidata <- variables[groupvariable==leveli,]
            groupsize <- nrow(levelidata)
            print(paste("Group",leveli,"Group size:",groupsize))
            print(paste("Group",leveli,"Means:"))
            print(mean(levelidata))
            print(paste("Group",leveli,"Standard Deviations:"))
            print(sd(levelidata))
         }
      }

To use the function "printMeanAndSdByGroup()", you first need to copy and paste it into R. The 
arguments of the function are the variables that you want to calculate means and standard deviations for,
and the variable containing the group of each sample. For example, to calculate the mean and standard deviation
for each of the 13 chemical concentrations, for each of the three different wine cultivars, we type:

::

    > printMeanAndSdByGroup(wine[2:14],wine[1])
      [1] "Group 1 Group size: 59"
      [1] "Group 1 Means:"
         V2          V3          V4          V5          V6          V7          V8          V9         V10         V11 
      13.744746    2.010678    2.455593   17.037288  106.338983    2.840169    2.982373    0.290000    1.899322    5.528305 
         V12         V13         V14 
       1.062034    3.157797 1115.711864 
      [1] "Group 1 Standard Deviations:"
         V2           V3           V4           V5           V6           V7           V8           V9          V10 
      0.46212536   0.68854886   0.22716598   2.54632245  10.49894932   0.33896135   0.39749361   0.07004924   0.41210923 
         V11          V12          V13          V14 
      1.23857281   0.11648264   0.35707658 221.52076659 
      [1] "Group 2 Group size: 71"
      [1] "Group 2 Means:"
         V2         V3         V4         V5         V6         V7         V8         V9        V10        V11        V12 
      12.278732   1.932676   2.244789  20.238028  94.549296   2.258873   2.080845   0.363662   1.630282   3.086620   1.056282 
         V13        V14 
      2.785352 519.507042 
      [1] "Group 2 Standard Deviations:"
         V2          V3          V4          V5          V6          V7          V8          V9         V10         V11 
      0.5379642   1.0155687   0.3154673   3.3497704  16.7534975   0.5453611   0.7057008   0.1239613   0.6020678   0.9249293 
         V12         V13         V14 
      0.2029368   0.4965735 157.2112204 
      [1] "Group 3 Group size: 48"
      [1] "Group 3 Means:"
         V2          V3          V4          V5          V6          V7          V8          V9         V10         V11 
      13.1537500   3.3337500   2.4370833  21.4166667  99.3125000   1.6787500   0.7814583   0.4475000   1.1535417   7.3962500 
         V12         V13         V14 
      0.6827083   1.6835417 629.8958333 
      [1] "Group 3 Standard Deviations:"
         V2          V3          V4          V5          V6          V7          V8          V9         V10         V11 
      0.5302413   1.0879057   0.1846902   2.2581609  10.8904726   0.3569709   0.2935041   0.1241396   0.4088359   2.3109421 
         V12         V13         V14 
      0.1144411   0.2721114 115.0970432 

The function "printMeanAndSdByGroup()" also prints out the number of samples in each group. In this case,
we see that there are 59 samples of cultivar 1, 71 of cultivar 2, and 48 of cultivar 3.

Between-groups Variance and Within-groups Variance for a Variable
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If we want to calculate the within-groups variance for a particular variable (for example, for a particular
chemical's concentration), we can use the function "calcWithinGroupsVariance()" below:

::

    > calcWithinGroupsVariance <- function(variable,groupvariable) 
      {
         # find out how many values the group variable can take
         groupvariable2 <- as.factor(groupvariable[[1]])
         levels <- levels(groupvariable2)
         numlevels <- length(levels)
         # get the mean and standard deviation for each group:
         numtotal <- 0
         denomtotal <- 0
         for (i in 1:numlevels)
         {
            leveli <- levels[i]
            levelidata <- variable[groupvariable==leveli,]
            levelilength <- length(levelidata)
            # get the mean and standard deviation for group i:
            meani <- mean(levelidata)
            sdi <- sd(levelidata)
            numi <- (levelilength - 1)*(sdi * sdi)
            denomi <- levelilength
            numtotal <- numtotal + numi
            denomtotal <- denomtotal + denomi 
         } 
         # calculate the within-groups variance
         Vw <- numtotal / (denomtotal - numlevels) 
         return(Vw)
      }

xxx need to check that this formula is correct.

You will need to copy and paste this function into R before you can use it.
For example, to calculate the within-groups variance of the variable V2 (the concentration of the first chemical),
we type:

::

    > calcWithinGroupsVariance(wine[2],wine[1]) 
      [1] 0.2620525

Thus, the within-groups variance for V2 is 0.2620525. 

We can calculate the between-groups variance for a particular variable (eg. V2) using the function
"calcBetweenGroupsVariance()" below:

::

    > calcBetweenGroupsVariance <- function(variable,groupvariable) 
      {
         # find out how many values the group variable can take
         groupvariable2 <- as.factor(groupvariable[[1]])
         levels <- levels(groupvariable2)
         numlevels <- length(levels)
         # calculate the overall grand mean: 
         grandmean <- mean(variable) # xxx is this formula ok?
         # get the mean and standard deviation for each group:
         numtotal <- 0
         denomtotal <- 0
         for (i in 1:numlevels)
         {
            leveli <- levels[i]
            levelidata <- variable[groupvariable==leveli,]
            levelilength <- length(levelidata)
            # get the mean and standard deviation for group i:
            meani <- mean(levelidata)
            sdi <- sd(levelidata)
            numi <- levelilength * ((meani - grandmean)^2)
            denomi <- levelilength
            numtotal <- numtotal + numi
            denomtotal <- denomtotal + denomi 
         } 
         # calculate the between-groups variance
         Vb <- numtotal / (denomtotal - numlevels) 
         Vb <- Vb[[1]]
         return(Vb)
      }

xxx need to check the formula

Once you have copied and pasted this function into R, you can use it to calculate the between-groups
variance for a variable such as V2:

::

    > calcBetweenGroupsVariance (wine[2],wine[1])
      [1] 0.404542

Thus, the between-groups variance of V2 is 0.404542.

We can calculate the "separation" achieved by a variable as its between-groups variance devided by its
within-groups variance. Thus, the separation achieved by V2 is calculated as:

::

    > 0.404542/0.2620525
      [1] 1.543744

If you want to calculate the separations achieved by all of the variables in a multivariate data set,
you can use the function "calcSeparations()" below:

::

    > calcSeparations <- function(variables,groupvariable)
      {
         # find out how many variables we have
         variables <- as.data.frame(variables)
         numvariables <- length(variables)
         # find the variable names
         variablenames <- colnames(variables)
         # calculate the separation for each variable
         for (i in 1:numvariables)
         {
            variablei <- variables[i]
            variablename <- variablenames[i]
            Vw <- calcWithinGroupsVariance(variablei, groupvariable)
            Vb <- calcBetweenGroupsVariance(variablei, groupvariable)
            sep <- Vb/Vw
            print(paste("variable",variablename,"Vw=",Vw,"Vb=",Vb,"separation=",sep))
         }
      }

xxx need to check this is correct

For example, to calculate the separations for each of the 13 chemical concentrations, we type:

::

    > calcSeparations(wine[2:14],wine[1])
      [1] "variable V2 Vw= 0.262052469153907 Vb= 0.404541999545934 separation= 1.54374427706057"
      [1] "variable V3 Vw= 0.887546796746581 Vb= 0.37473163985053 separation= 0.422210571007813"
      [1] "variable V4 Vw= 0.0660721013425184 Vb= 0.0100527012256999 separation= 0.152147442285612"
      [1] "variable V5 Vw= 8.00681118121156 Vb= 3.2733342441496 separation= 0.408818713226392"
      [1] "variable V6 Vw= 180.65777316441 Vb= 25.6628688901645 separation= 0.142052392435999"
      [1] "variable V7 Vw= 0.191270475224227 Vb= 0.204895511934682 separation= 1.07123439566134"
      [1] "variable V8 Vw= 0.274707514337437 Vb= 0.734413657412161 separation= 2.67343854493199"
      [1] "variable V9 Vw= 0.0119117022132797 Vb= 0.00375394465670427 separation= 0.315147624536753"
      [1] "variable V10 Vw= 0.246172943795542 Vb= 0.0851656629460314 separation= 0.345958664802601"
      [1] "variable V11 Vw= 2.28492308133354 Vb= 3.15094858082634 separation= 1.37901735361146"
      [1] "variable V12 Vw= 0.0244876469432414 Vb= 0.0283543990278662 separation= 1.157906233032"
      [1] "variable V13 Vw= 0.160778729560982 Vb= 0.349068666907718 separation= 2.17111223518731"
      [1] "variable V14 Vw= 29707.6818705169 Vb= 70592.3693975409 separation= 2.37623284459632"

xxx should probably say something about the interpretation of these values.

Between-groups Covariance and Within-groups Covariance for Two Variables
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

xxx should also include functions for calculating between-groups covariance and within-groups covariance.
Also see Q3 part (a)(ii) of assignment 3 for interpretation of this.

Calculating Correlations for Multivariate Data
----------------------------------------------

It is often of interest to investigate whether any of the variables in a multivariate data set are
significantly correlated.

To calculate the linear (Pearson) correlation coefficient for a pair of variables, you can use
the "cor.test()" function in R. For example, to calculate the correlation coefficient for the first
two chemicals' concentrations, V2 and V3, we type:

::

    > cor.test(wine$V2, wine$V3)
      Pearson's product-moment correlation
      data:  wine$V2 and wine$V3 
      t = 1.2579, df = 176, p-value = 0.2101
      alternative hypothesis: true correlation is not equal to 0 
      95 percent confidence interval:
      -0.05342959  0.23817474 
      sample estimates:
       cor 
      0.09439694 

This tells us that the correlation coefficient is about 0.094, which is a very weak correlation.
Furthermore, the P-value for the statistical test of whether the correlation coefficient is 
significantly different from zero is 0.21. This is much greater than 0.05 (which we can use here
as a cutoff for statistical significance), so there is very weak evidence that that the correlation is non-zero.

If you have a lot of variables, you can use "cor.test()" to calculate the correlation coefficient
for each pair of variables, but you might be just interested in finding out what are the most highly
correlated pairs of variables. For this you can use the function "mosthighlycorrelated()" below.

The function "mosthighlycorrelated()" will print out the linear correlation coefficients for
each pair of variables in your data set, in order of the correlation coefficient. This lets you see
very easily which pair of variables are most highly correlated.

::

    > mosthighlycorrelated <- function(mydataframe,numtoreport)
      {
         # find the correlations
         cormatrix <- cor(mydataframe)
         # set the correlations on the diagonal or lower triangle to zero, so they will not be reported as the highest ones:
         diag(cormatrix) <- 0
         cormatrix[lower.tri(cormatrix)] <- 0
         # find the dimensions of the matrix, and the row names:
         numrows <- nrow(cormatrix)
         therownames <- rownames(cormatrix)
         # find the highest correlations
         sorted <- sort(abs(cormatrix),decreasing=TRUE)
         for (i in 1:numtoreport)
         {
            corri <- sorted[i]
            # find the pair of variables with this correlation
            for (j in 1:(numrows-1))
            {
               for (k in (j+1):numrows)
               {
                  corrjk <- cormatrix[j,k]
                  if (corri == abs(corrjk))
                  {
                     rowname <- therownames[j]
                     colname <- therownames[k]
                     print(paste("i=",i,"variables",rowname,"and",colname,"correlation=",corrjk))
                  }
               }
            }
         }
      }

To use this function, you will first have to copy and paste it into R. The arguments of the function
are the variables that you want to calculate the correlations for, and the number of top correlation
coefficients to print out (for example, you can tell it to print out the largest ten correlation coefficients, or
the largest 20).

For example, to calculate correlation coefficients between the concentrations of the 13 chemicals
in the wine samples, and to print out the top 10 pairwise correlation coefficients, you can type:

::

    > mosthighlycorrelated(wine[2:14], 10)
      [1] "i= 1 variables V7 and V8 correlation= 0.864563500095115"
      [1] "i= 2 variables V8 and V13 correlation= 0.787193901866952"
      [1] "i= 3 variables V7 and V13 correlation= 0.699949364791186"
      [1] "i= 4 variables V8 and V10 correlation= 0.652691768607515"
      [1] "i= 5 variables V2 and V14 correlation= 0.643720037178213"
      [1] "i= 6 variables V7 and V10 correlation= 0.612413083780036"
      [1] "i= 7 variables V12 and V13 correlation= 0.565468293182659"
      [1] "i= 8 variables V3 and V12 correlation= -0.561295688664945"
      [1] "i= 9 variables V2 and V11 correlation= 0.546364195083704"
      [1] "i= 10 variables V8 and V12 correlation= 0.54347856648999"

This tells us that the pair of variables with the highest linear correlation coefficient are
V7 and V8 (correlation = 0.86 approximately). 

Standardising Variables
-----------------------

If you want to compare different variables that have different units, are very different variances,
it is a good idea to first standardise the variables. 

For example, we found above that the concentrations of the 13 chemicals in the wine samples show a wide range of 
standard deviations, from 0.1244533 for V9 (variance 0.01548862) to 314.9074743 for V14 (variance 99166.72).
This is a range of approximately 6,402,554-fold in the variances. 

As a result, it is not a good idea to use the unstandardised chemical concentrations as the input for a
principal component analysis (PCA, see below) of the
wine samples, as if you did that, the first principal component would be dominated by the variables
which show the largest variances, such as V14.

Thus, it would be a better idea to first standardise the variables so that they all have variance 1 and mean 0, 
and to then carry out the principal component analysis on the standardised data. This would allow us to 
find the principal components that provide the best low-dimensional representation of the variation in the
original data, without being overly biased by those variables that show the most variance in the original data.

You can standardise variables in R using the "scale()" function. 

For example, to standardise the concentrations of the 13 chemicals in the wine samples, we type:

::

    > standardisedconcentrations <- as.data.frame(scale(wine[2:14]))

Note that we use the "as.data.frame()" function to convert the output of "scale()" into a
"data frame", which is the same type of R variable that the "wine" variable.

We can check that each of the standardised variables stored in "standardisedconcentrations"
has a mean of 0 and a standard deviation of 1 by typing:

::

    > mean(standardisedconcentrations) 
           V2            V3            V4            V5            V6            V7            V8            V9           V10 
      -8.591766e-16 -6.776446e-17  8.045176e-16 -7.720494e-17 -4.073935e-17 -1.395560e-17  6.958263e-17 -1.042186e-16 -1.221369e-16 
           V11           V12           V13           V14 
      3.649376e-17  2.093741e-16  3.003459e-16 -1.034429e-16 
    > sd(standardisedconcentrations)
      V2  V3  V4  V5  V6  V7  V8  V9 V10 V11 V12 V13 V14 
      1   1   1   1   1   1   1   1   1   1   1   1   1 

We see that the means of the standardised variables are all very tiny numbers and so are
essentially equal to 0, and the standard deviations of the standardised variables are all equal to 1.

Principal Component Analysis
----------------------------

The purpose of principal component analysis is to find the best low-dimensional representation of the variation in a
multivariate data set. For example, in the case of the wine data set, we have 13 chemical concentrations describing
wine samples from three different cultivars. We can carry out a principal component analysis to investigate 
whether wine samples from different cultivars can be distinguished by a smaller number of new variables (principal
components), where each of these new variables is a linear combination of all or some of the 13 chemical concentrations.

To carry out a principal component analysis (PCA) on a multivariate data set, the first step is often to standardise
the variables under study using the "scale()" function (see above). This is necessary if the input variables
have very different variances, which is true in this case as the concentrations of the 13 chemicals have
very different variances (see above).

Once you have standardised your variables, you can carry out a principal component analysis using the "prcomp()"
function in R.

For example, to standardise the concentrations of the 13 chemicals in the wine samples, and carry out a 
principal components analysis on the standardised concentrations, we type:

::

    > standardisedconcentrations <- as.data.frame(scale(wine[2:14])) # standardise the variables
    > wine.pca <- prcomp(standardisedconcentrations)                 # do a PCA

You can get a summary of the principal component analysis results using the "summary()" function on the
output of "prcomp()":

::

    > summary(wine.pca)
      Importance of components:
                              PC1   PC2   PC3    PC4    PC5    PC6    PC7    PC8    PC9   PC10
      Standard deviation     2.169 1.580 1.203 0.9586 0.9237 0.8010 0.7423 0.5903 0.5375 0.5009
      Proportion of Variance 0.362 0.192 0.111 0.0707 0.0656 0.0494 0.0424 0.0268 0.0222 0.0193
      Cumulative Proportion  0.362 0.554 0.665 0.7360 0.8016 0.8510 0.8934 0.9202 0.9424 0.9617
                              PC11   PC12    PC13
      Standard deviation     0.4752 0.4108 0.32152
      Proportion of Variance 0.0174 0.0130 0.00795
      Cumulative Proportion  0.9791 0.9920 1.00000

This gives us the standard deviation of each component, and the proportion of variance explained by
each component. The standard deviation of the components is stored in a named element called "sdev" of the output 
variable made by "prcomp":

::

    > wine.pca$sdev
      [1] 2.1692972 1.5801816 1.2025273 0.9586313 0.9237035 0.8010350 0.7423128 0.5903367
      [9] 0.5374755 0.5009017 0.4751722 0.4108165 0.3215244

The total variance explained by the components is the sum of the variances of the components:

::

    > sum((wine.pca$sdev)^2)
      [1] 13
    
In this case, we see that the total variance is 13, which is equal to the number of standardised variables (13 variables). 
This is because for standardised data, the variance of each standardised variable is 1. The total variance is equal to the sum 
of the variances of the individual variables, and since the variance of each standardised variable is 1, the 
total variance should be equal to the  number of variables (13 here). 

Deciding How Many Principal Components to Retain
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

In order to decide how many principal components should be retained, 
it is common to summarise the results of a principal components analysis by making a scree plot, which we
can do in R using the "screeplot()" function:

::

    > screeplot(wine.pca, type="lines")

|image6|

The most obvious change in slope in the scree plot occurs at component 4, which is the "elbow" of the
scree plot. Therefore, it cound be argued based on the basis of the scree plot that the first three
components should be retained.

Another way of deciding how many components to retain is to use Kaiser's criterion:
that we should only retain principal components for which the variance is above 1 (when principal
component analysis was applied to standardised data).  We can check this by finding the variance of each
of the principal components:

::

    > (wine.pca$sdev)^2
      [1] 4.7058503 2.4969737 1.4460720 0.9189739 0.8532282 0.6416570 0.5510283 0.3484974
      [9] 0.2888799 0.2509025 0.2257886 0.1687702 0.1033779

We see that the variance is above 1 for principal components 1, 2, and 3 (which have variances
4.71, 2.50, and 1.45, respectively). Therefore, using Kaiser's criterion, we would retain the first
three principal components.

A third way to decide how many principal components to retain is to decide to keep the number of
components required to explain at least some minimum amount of the total variance. For example, if
it is important to explain at least 80% of the variance, we would retain the first five principal components,
as we can see from the output of "summary(wine.pca)" that the first five principal components
explain 80.2% of the variance (while the first four components explain just 73.6%, so are not sufficient).

Loadings for the Principal Components
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The loadings for the principal components are stored in a named element "rotation" of the variable
returned by "prcomp()". This contains a matrix with the loadings of each principal component, where
the first column in the matrix contains the loadings for the first principal component, the second
column contains the loadings for the second principal component, and so on.

Therefore, to obtain the loadings for the first principal component in our
analysis of the 13 chemical concentrations in wine samples, we type:

::

    > wine.pca$rotation[,1]
          V2           V3           V4           V5           V6           V7 
      -0.144329395  0.245187580  0.002051061  0.239320405 -0.141992042 -0.394660845 
          V8           V9          V10          V11          V12          V13 
      -0.422934297  0.298533103 -0.313429488  0.088616705 -0.296714564 -0.376167411 
         V14 
      -0.286752227 

This means that the first principal component is a linear combination of the variables:
-0.144*Z2 + 0.245*Z3 + 0.002*Z4 + 0.239*Z5 - 0.142*Z6 - 0.395*Z7 - 0.423*Z8 + 0.299*Z9
-0.313*Z10 + 0.089*Z11 - 0.297*Z12 - 0.376*Z13 - 0.287*Z14, where Z2, Z3, Z4...Z14 are
the standardised versions of the variables V2, V3, V4...V14 (that each
have mean of 0 and variance of 1).

Note that the square of the loadings sum to 1, as this is a constraint used in calculating the loadings:

::

    > sum((wine.pca$rotation[,1])^2)
      [1] 1

To calculate the values of the first principal component, we can define our own function:

::

    > calcpc1 <- function(variables,loadings)
      {
         # find the number of samples in the data set
         as.data.frame(variables)
         numsamples <- nrow(variables)
         # make a vector to store the first component
         pc1 <- numeric(numsamples)
         # find the number of variables 
         numvariables <- length(variables)
         # calculate the value of the first component for each sample
         for (i in 1:numsamples)
         {
            valuei <- 0
            for (j in 1:numvariables)
            {
               valueij <- variables[i,j]
               loadingj <- loadings[j]
               valuei <- valuei + (valueij * loadingj)
            } 
            pc1[i] <- valuei
         }
         return(pc1)
      }

We can then use the function to calculate the values of the first principal component for each sample in our
wine data:

::

    > calcpc1(standardisedconcentrations, wine.pca$rotation[,1])
      [1] -3.30742097 -2.20324981 -2.50966069 -3.74649719 -1.00607049 -3.04167373 -2.44220051
      [8] -2.05364379 -2.50381135 -2.74588238 -3.46994837 -1.74981688 -2.10751729 -3.44842921
      [15] -4.30065228 -2.29870383 -2.16584568 -1.89362947 -3.53202167 -2.07865856 -3.11561376
      [22] -1.08351361 -2.52809263 -1.64036108 -1.75662066 -0.98729406 -1.77028387 -1.23194878
      [29] -2.18225047 -2.24976267 -2.49318704 -2.66987964 -1.62399801 -1.89733870 -1.40642118
      [36] -1.89847087 -1.38096669 -1.11905070 -1.49796891 -2.52268490 -2.58081526 -0.66660159
      ...   

In fact, the values of the first principal component are stored in the variable wine.pca$x[,1]
that was returned by the "prcomp()" function, so we can compare those values to the ones that we
calculated, and they should agree:

::

    > wine.pca$x[,1]
      [1] -3.30742097 -2.20324981 -2.50966069 -3.74649719 -1.00607049 -3.04167373 -2.44220051
      [8] -2.05364379 -2.50381135 -2.74588238 -3.46994837 -1.74981688 -2.10751729 -3.44842921
      [15] -4.30065228 -2.29870383 -2.16584568 -1.89362947 -3.53202167 -2.07865856 -3.11561376
      [22] -1.08351361 -2.52809263 -1.64036108 -1.75662066 -0.98729406 -1.77028387 -1.23194878
      [29] -2.18225047 -2.24976267 -2.49318704 -2.66987964 -1.62399801 -1.89733870 -1.40642118
      [36] -1.89847087 -1.38096669 -1.11905070 -1.49796891 -2.52268490 -2.58081526 -0.66660159
      ...
      
We see that they do agree.

The first principal component has highest (in absolute value) loadings for V8 (-0.423), V7 (-0.395), V13 (-0.376),
V10 (-0.313), V12 (-0.297), V14 (-0.287), V9 (0.299), V3 (0.245), and V5 (0.239). The loadings for V8, V7, V13,
V10, V12 and V14 are negative, while those for V9, V3, and V5 are positive. Therefore, an interpretation of the
first principal component is that it represents a contrast between the concentrations of V8, V7, V13, V10, V12, and V14,
and the concentrations of V9, V3 and V5.

Similarly, we can obtain the loadings for the second principal component by typing:

::

    > wine.pca$rotation[,2]
          V2           V3           V4           V5           V6           V7 
      0.483651548  0.224930935  0.316068814 -0.010590502  0.299634003  0.065039512 
          V8           V9          V10          V11          V12          V13 
      -0.003359812  0.028779488  0.039301722  0.529995672 -0.279235148 -0.164496193 
         V14 
      0.364902832 

This means that the second principal component is a linear combination of the variables:
0.484*Z2 + 0.225*Z3 + 0.316*Z4 - 0.011*Z5 + 0.300*Z6 + 0.065*Z7 - 0.003*Z8 + 0.029*Z9
+ 0.039*Z10 + 0.530*Z11 - 0.279*Z12 - 0.164*Z13 + 0.365*Z14, where Z1, Z2, Z3...Z14
are the standardised versions of variables V2, V3, ... V14 that each have mean 0 and variance 1.

Note that the square of the loadings sum to 1, as above:

::

    > sum((wine.pca$rotation[,2])^2)
      [1] 1

The second principal component has highest loadings for V11 (0.530), V2 (0.484), V14 (0.365), V4 (0.316), 
V6 (0.300), V12 (-0.279), and V3 (0.225). The loadings for V11, V2, V14, V4, V6 and V3 are positive, while
the loading for V12 is negative. Therefore, an interpretation of the second principal component is that
it represents a contrast between the concentrations of V11, V2, V14, V4, V6 and V3, and the concentration of
V12. Note that the loadings for V11 (0.530) and V2 (0.484) are the largest, so the contrast is mainly between
the concentrations of V11 and V2, and the concentration of V12.

Scatterplots of the Principal Components
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The values of the principal components are stored in a named element "x" of the variable returned by
"prcomp()". This contains a matrix with the principal components, where the first column in the matrix
contains the first principal component, the second column the second component, and so on.

Thus, in our example, "wine.pca$x[,1]" contains the first principal component, and 
"wine.pca$x[,2]" contains the second principal component. 

We can make a scatterplot of the first two principal components, and label the data points with the cultivar that the wine
samples come from, by typing:

::

    > plot(wine.pca$x[,1],wine.pca$x[,2]) # make a scatterplot
    > text(wine.pca$x[,1],wine.pca$x[,2], wine$V1, cex=0.7, pos=4, col="red") # add labels

|image7|

The scatterplot shows the first principal component on the x-axis, and the second principal
component on the y-axis. We can see from the scatterplot that wine samples of cultivar 1
have much lower values of the first principal component than wine samples of cultivar 3.
Therefore, the first principal component separates wine samples of cultivars 1 from those
of cultivar 3.

We can also see that wine samples of cultivar 2 have much higher values of the second
principal component than wine samples of cultivars 1 and 3. Therefore, the second principal
component separates samples of cultivar 2 from samples of cultivars 1 and 3.

Therefore, the first two principal components are reasonably useful for distinguishing wine
samples of the three different cultivars.

Above, we interpreted the first principal component as a contrast between the concentrations of V8, V7, V13, V10, V12, and V14,
and the concentrations of V9, V3 and V5. We can check whether this makes sense in terms of the
concentrations of these chemicals in the different cultivars, by printing out the means of the
standardised concentration variables in each cultivar, using the "printMeanAndSdByGroup()" function (see above): 

::

    > printMeanAndSdByGroup(standardisedconcentrations,wine[1])
      [1] "Group 1 Means:"
           V2         V3         V4         V5         V6         V7         V8         V9        V10 
      0.9166093 -0.2915199  0.3246886 -0.7359212  0.4619232  0.8709055  0.9541923 -0.5773564  0.5388633 
           V11        V12        V13        V14 
      0.2028288  0.4575567  0.7691811  1.1711967 
      [1] "Group 2 Means:"
           V2          V3          V4          V5          V6          V7          V8          V9 
      -0.88921161 -0.36134241 -0.44370614  0.22250941 -0.36354162 -0.05790375  0.05163434  0.01452785 
           V10         V11         V12         V13         V14 
      0.06880790 -0.85039994  0.43239084  0.24460431 -0.72207310 
      [1] "Group 3 Means:"
           V2          V3          V4          V5          V6          V7          V8          V9 
       0.18862653  0.89281222  0.25721896  0.57544128 -0.03004191 -0.98483874 -1.24923710  0.68817813 
           V10         V11         V12         V13         V14 
      -0.76413110  1.00857281 -1.20199161 -1.30726231 -0.37152953 
 
Does it make sense that the first principal component can separate cultivar 1 from cultivar 3?
In cultivar 1, the mean values of V8 (0.954), V7 (0.871), V13 (0.769), V10 (0.539), V12 (0.458) and V14 (1.171)
are very high compared to the mean values of V9 (-0.577), V3 (-0.292) and V5 (-0.736).
In cultivar 3, the mean values of V8 (-1.249), V7 (-0.985), V13 (-1.307), V10 (-0.764), V12 (-1.202) and V14 (-0.372)
are very low compared to the mean values of V9 (0.688), V3 (0.893) and V5 (0.575). 
Therefore, it does make sense that principal component 1 is a contrast between the concentrations of V8, V7, V13, V10, V12, and V14,
and the concentrations of V9, V3 and V5; and that principal component 1 can separate cultivar 1 from cultivar 3.

Above, we intepreted the second principal component as a contrast between the concentrations of V11, 
V2, V14, V4, V6 and V3, and the concentration of V12.
In the light of the mean values of these variables in the different cultivars, does 
it make sense that the second principal component can separate cultivar 2 from cultivars 1 and 3?
In cultivar 1, the mean values of V11 (0.203), V2 (0.917), V14 (1.171), V4 (0.325), V6 (0.462) and V3 (-0.292)
are not very different from the mean value of V12 (0.458). 
In cultivar 3, the mean values of V11 (1.009), V2 (0.189), V14 (-0.372), V4 (0.257), V6 (-0.030) and V3 (0.893)
are also not very different from the mean value of V12 (-1.202). 
In contrast, in cultivar 2, the mean values of V11 (-0.850), V2 (-0.889), V14 (-0.722), V4 (-0.444), V6 (-0.364) and V3 (-0.361)
are much less than the mean value of V12 (0.432). 
Therefore, it makes sense that principal component is a contrast between the concentrations of V11, 
V2, V14, V4, V6 and V3, and the concentration of V12; and that principal component 2 can separate cultivar 2 from cultivars 1 and 3.

Linear Discriminant Analysis
----------------------------

The purpose of principal component analysis is to find the best low-dimensional representation of the variation in a
multivariate data set. For example, in the wine data set, we have 13 chemical concentrations describing wine samples
from three different cultivars. By carrying out a principal component analysis, we found that the three cultivars can
be distinguished based on the first two principal components, where each of the principal components is a particular linear
combination of the 13 chemical concentrations.

The purpose of linear discriminant analysis (LDA) is to find the linear combinations of the original variables (the 13
chemical concentrations here) that gives the best possible separation between the groups (wine cultivars here) in our
data set. 

If we want to separate the wines by cultivar, the wines come from three different cultivars, so the number of groups (G) is 3, 
and the number of variables is 13 (13 chemicals' concentrations; p = 13).  The maximum number of useful discriminant
functions that can separate the wines by cultivar is the minimum of G-1 and p, and so in this case it is the minimum of 2 and 13, 
which is 2. Thus, we can find at most 2 useful discriminant functions to separate the wines by cultivar, using the 
13 chemical concentration variables.

You can carry out a linear discriminant analysis using the "lda()" function from the R "MASS" package.
To use this function, we first need to install the "MASS" R package 
(for instructions on how to install an R package, see `How to install an R package 
<./installr.html#how-to-install-an-r-package>`_).

For example, to carry out a linear discriminant analysis using the 13 chemical concentrations in the wine samples, we type:

::

    > library("MASS")                                                # load the MASS package
    > standardisedconcentrations <- as.data.frame(scale(wine[2:14])) # standardise the variables
    > wine.lda <- lda(wine$V1 ~ standardisedconcentrations$V2 + standardisedconcentrations$V3 + standardisedconcentrations$V4 +
                                standardisedconcentrations$V5 + standardisedconcentrations$V6 + standardisedconcentrations$V7 + 
                                standardisedconcentrations$V8 + standardisedconcentrations$V9 + standardisedconcentrations$V10 +
                                standardisedconcentrations$V11 + standardisedconcentrations$V12 + standardisedconcentrations$V13 +
                                standardisedconcentrations$V14, prior=c(1/3,1/3,1/3))
                    
As for the PCA, it is a good idea to standardise your variables before carrying out a LDA, if the
original variables have very different variances (which is the case of the 13 concentration variables, see above), so
we use "scale()" to standardise variables before running "lda()" on the standardised variables.
The "prior=c(1/3,1/3,1/3)" argument tells "lda()" that we want to assume that it is equally likely that a wine sample
belongs to each of the groups (cultivars). 

Loadings for the Discriminant Functions
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

To get the values of the loadings of the discriminant functions based on the standardised data, we type:

::

    > wine.lda
      Coefficients of linear discriminants:                                      
                                          LD1          LD2
      standardisedconcentrations$V2  -0.28930985  0.724190975
      standardisedconcentrations$V3   0.20253117  0.330831040
      standardisedconcentrations$V4  -0.06681410  0.648051143
      standardisedconcentrations$V5   0.49017234 -0.515701775
      standardisedconcentrations$V6  -0.03120840 -0.004953265
      standardisedconcentrations$V7   0.38518458 -0.040744671
      standardisedconcentrations$V8  -1.68312608 -0.402314982
      standardisedconcentrations$V9  -0.19671169 -0.192768856
      standardisedconcentrations$V10  0.06727363 -0.179604486
      standardisedconcentrations$V11  0.85323534  0.542363156
      standardisedconcentrations$V12 -0.20517529 -0.335974619
      standardisedconcentrations$V13 -0.81875175  0.080084913
      standardisedconcentrations$V14 -0.79840001  0.942311575

This means that the first discriminant function is a linear combination of the variables:
-0.289*Z2 - 0.203*Z3 - 0.067*Z4 + 0.490*Z5 - 0.031*Z6 + 0.385*Z7 - 1.683*Z8
- 0.197*Z9 + 0.067*Z10 + 0.853*Z11 - 0.205*Z12 - 0.205*Z12 - 0.819*Z13 - 0.798*Z14,
where Z2, Z3,...Z14 are group-standardised versions of V2,V3...V14 (standardised so that
the within-group variance is 1 for each variable).
xxx are the loadings in R given for group-standardised versions??? get different loadings in R and SPSS.
xxx check if I can get out the values using these loadings.

In the first discriminant function, the largest loadings (in absolute) value are given to V8 (-1.683), V11 (0.853),
V13 (-0.819) and V14 (-0.798). The loadings for V8, V13 and V14 are negative, while that for V11 is positive.
Therefore, the discriminant function seems to represent a contrast between the concentrations of V8, V13 and V14,
and the concentration of V11.

We saw above that the individual variables which gave the greatest separations between the groups were V8
(separation 2.67), V14 (2.38), V13 (2.17), V2 (1.54) and V11 (1.38). These were the same variables that had the largest
loadings in the linear discriminant function (loading for V8: -1.683, for V14: -0.798, for V13: -0.819, for V11: 0.853). 

xxx Should check whether the variables V8 and V11/V14?? have a negative between-groups variance and a positive within-groups variance.
When the between-groups covariance and within-groups covariance for two variables have opposite signs, it 
indicates that a better separation between groups can be obtained by using
a linear combination of those two variables than by using either variable on its own.

xxx Thus, given that the two variables V8 and
V11/V14?? have between-groups and within-groups covariances of opposite signs, and that these are the two variables that
gave the greatest separations between groups when used individually, it is not surprising that these are the two variables that
have the largest loadings in the first discriminant function.

Separation Achieved by the Discriminant Functions
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

To calculate the separation achieved by each discriminant function, we first need to calculate the
value of each discriminant function, by substituting the variables' values into the linear combination for
the discriminant function (eg. -0.289*V2 - 0.203*V3 - 0.067*V4 + 0.490*V5 - 0.031*V6 + 0.385*V7 - 1.683*V8
- 0.197*V9 + 0.067*V10 + 0.853*V11 - 0.205*V12 - 0.205*V12 - 0.819*V13 - 0.798*V14 for the first
discriminant function).

We can do this using the "predict()" function in R. For example,
to calculate the value of the discriminant functions for the wine data, we type:

::

    > wine.lda.values <- predict(wine.lda, standardisedconcentrations) 

The returned variable has a named element "x" which is a matrix containing the linear discriminant functions:
the first column of x contains the first discriminant function, the second column of x contains the second
discriminant function, and so on (if there are more discriminant functions).
    
We can therefore calculate the separations achieved by the two linear discriminant functions for the wine data by using the
"calcSeparations()" function (see above), which calculates the separation as the ratio of the between-groups
variance to the within-groups variance:

::

    > calcSeparations(wine.lda.values$x,wine[1])
      [1] "variable LD1 Vw= 1 Vb= 9.06767320353267 separation= 9.06767320353267"
      [1] "variable LD2 Vw= 1 Vb= 4.14253527714928 separation= 4.14253527714928"

Thus, the separation achieved by the first (best) discriminant function is 9.07, and the separation
achieved by the second (second best) discriminant function is 4.14.

Therefore, the total separation is the sum of these, which is (9.06767320353267+4.14253527714928=13.21021) 
13.21, rounded to two decimal places. Therefore, the percentage separation achieved by the
first discriminant function is (9.07*100/13.21=) 68.64%, and the percentage separation achieved by the 
second discriminant function is (4.14*100/13.21=) 31.36%.
   
xxx The "proportion of trace" that is printed is the proportion of between-class variance that is explained by successive
discriminant functions. xxx get 72.98% and 27.02% - does not agree with the percentage separation, which I get.

Therefore, the first discriminant function does achieve a good separation between the three groups (three cultivars), but the second
discriminant function does improve the separation of the groups by quite a large amount, so is it worth using the 
second discriminant function as well. Therefore, to achieve a good separation of the groups (cultivars), 
it is necessary to use both of the first two discriminant functions.

We found above that the largest separation achieved for any of the individual variables (individual chemical concentrations)
was 2.67 for V8, which is quite a lot less than 9.07, the separation achieved by the first discriminant function. Therefore,
the effect of using more than one variable to calculate the discriminant function is that we can find a discriminant function
that achieves a far greater separation between groups than achieved by any one variable alone.

xxx note that wine.lda$svd should be the "ratio of between- and within-group standard deviations", but
this doesn't seem to be the square root of separation

% xxx Write down the separation and percentage separation achieved by each discriminant function. 

A Stacked Histogram of the LDA Values
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

A nice way of displaying the results of a linear discriminant analysis (LDA) is to make a stacked histogram of the
values of the discriminant function for the samples from different groups (different wine cultivars in our example).

We can do this using the "ldahist()" function in R. For example, to make a stacked histogram of the first discriminant
function's values for wine samples of the three different wine cultivars, we type: 

::

    > ldahist(data = wine.lda.values$x[,1], g=wine$V1)

|image8|

We can see from the histogram that cultivars 1 and 3 are well separated by the first
discriminant function, since the values for the first cultivar are between -6 and -1,
while the values for cultivar 3 are between 1 and 6, and so there is no overlap in values.

However, the separation achieved by the linear discriminant function on the training
set may be an overestimate. To get a more accurate idea of how well the first discriminant function 
separates the groups, we would need to see a stacked histogram of the values for the three
cultivars using some unseen "test set", that is, using
a set of data that was not used to calculate the linear discriminant function.

We see that the first discriminant function separates cultivars 1 and 3 very well, but
does not separate cultivars 1 and 2, or cultivars 2 and 3, so well.

We therefore investigate whether the second discriminant function separates those cultivars,
by making a stacked histogram of the second discriminant function's values:

::

    > ldahist(data = wine.lda.values$x[,2], g=wine$V1)

|image9|

We see that the second discriminant function separates cultivars 1 and 2 quite well, although
there is a little overlap in their values. Furthermore, the second discriminant function also
separates cultivars 2 and 3 quite well, although again there is a little overlap in their values so 
it is not perfect.

Thus, we see that two discriminant functions are necessary to separate the cultivars, as was
discussed above (see the discussion of percentage separation above).

Scatterplots of the Discriminant Functions
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

We can obtain a scatterplot of the best two discriminant functions, with the data points labelled by cultivar, by typing:

::

    > plot(wine.lda.values$x[,1],wine.lda.values$x[,2]) # make a scatterplot
    > text(wine.lda.values$x[,1],wine.lda.values$x[,2],wine$V1,cex=0.7,pos=4,col="red") # add labels

|image10|

From the scatterplot of the first two discriminant functions, we can see that the wines from the three 
cultivars are well separated in the scatterplot. The first discriminant function (x-axis)
separates cultivars 1 and 3 very well, but doesn't not perfectly separate cultivars
1 and 3, or cultivars 2 and 3. 

The second discriminant function (y-axis) achieves a fairly good separation of cultivars
1 and 3, and cultivars 2 and 3, although it is not totally perfect.

To achieve a very good separation of the three cultivars, it would be best to use both the first and second 
discriminant functions together, since the first discriminant function can separate cultivars 1 and 3 very well, 
and the second discriminant function can separate cultivars 1 and 2, and cultivars 2 and 3, reasonably well.

xxx Note: in SPSS I get different values of the LDA than in R, I think because I have different loadings xxx

Allocation Rules and Misclassification Rate
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

We can calculate the mean values of the discriminant functions for each of the three cultivars using the
"printMeanAndSdByGroup()" function (see above):

::

    > printMeanAndSdByGroup(wine.lda.values$x,wine[1])
      [1] "Group 1 Means:"
           LD1       LD2 
      -3.615421  1.620929 
      [1] "Group 2 Means:"
           LD1        LD2 
      -0.4993242 -2.7156185
      [1] "Group 3 Means:"
           LD1      LD2 
      4.114745 1.094689 

We find that the mean value of the first discriminant function is -3.615421 for cultivar 1, -0.4993242 for cultivar 2,
and 4.114745 for cultivar 3. The mid-way point between the mean values for cultivars 1 and 2 is (-3.615421-0.4993242)/2=-2.057373,
and the mid-way point between the mean values for cultivars 2 and 3 is (-0.4993242+4.114745)/2 = 1.807710.

Therefore, we can use the following allocation rule:
* if the first discriminant function is <= -2.057373, predict the sample to be from cultivar 1
* if the first discriminant function is > -2.057373 and <= 1.807710, predict the sample to be from cultivar 2
* if the first discriminant function is > 1.807710, predict the sample to be from cultivar 3

We can examine the accuracy of this allocation rule by using the "calcAllocationRuleAccuracy()" function below:

::

    > calcAllocationRuleAccuracy <- function(ldavalue, groupvariable, cutoffpoints)
      {
         # find out how many values the group variable can take
         groupvariable2 <- as.factor(groupvariable[[1]])
         levels <- levels(groupvariable2)
         numlevels <- length(levels)
         # calculate the number of true positives and false negatives for each group
         numlevels <- length(levels)
         for (i in 1:numlevels)
         {
            leveli <- levels[i]
            levelidata <- ldavalue[groupvariable==leveli]
            # see how many of the samples from this group are classified in each group
            for (j in 1:numlevels)
            {
               levelj <- levels[j]
               if (j == 1) 
               { 
                  cutoff1 <- cutoffpoints[1]
                  cutoff2 <- "NA"
                  results <- summary(levelidata <= cutoff1)
               }
               else if (j == numlevels)
               {
                  cutoff1 <- cutoffpoints[(numlevels-1)]
                  cutoff2 <- "NA"
                  results <- summary(levelidata > cutoff1) 
               }
               else
               {
                  cutoff1 <- cutoffpoints[(j-1)]
                  cutoff2 <- cutoffpoints[(j)]
                  results <- summary(levelidata > cutoff1 & levelidata <= cutoff2)
               }
               trues <- results["TRUE"]
               trues <- trues[[1]]
               print(paste("Number of samples of group",leveli,"classified as group",levelj," : ",trues,"(cutoffs:",cutoff1,",",cutoff2,")"))
            }
         }
      }

For example, to calculate the accuracy for the wine data based on the allocation
rule for the first discriminant function, we type:

::

    > calcAllocationRuleAccuracy(wine.lda.values$x[,1], wine[1], c(-2.057373, 1.807710))
      [1] "Number of samples of group 1 classified as group 1  :  55 (cutoffs: -2.057373 , NA )"
      [1] "Number of samples of group 1 classified as group 2  :  4 (cutoffs: -2.057373 , 1.80771 )"
      [1] "Number of samples of group 1 classified as group 3  :  NA (cutoffs: 1.80771 , NA )"
      [1] "Number of samples of group 2 classified as group 1  :  7 (cutoffs: -2.057373 , NA )"
      [1] "Number of samples of group 2 classified as group 2  :  63 (cutoffs: -2.057373 , 1.80771 )"
      [1] "Number of samples of group 2 classified as group 3  :  1 (cutoffs: 1.80771 , NA )"
      [1] "Number of samples of group 3 classified as group 1  :  NA (cutoffs: -2.057373 , NA )"
      [1] "Number of samples of group 3 classified as group 2  :  NA (cutoffs: -2.057373 , 1.80771 )"
      [1] "Number of samples of group 3 classified as group 3  :  48 (cutoffs: 1.80771 , NA )"

This can be displayed in a "confusion matrix":

+------------+----------------------+----------------------+----------------------+
|            | Allocated to group 1 | Allocated to group 2 | Allocated to group 3 |
+============+======================+======================+======================+
| Is group 1 |        55            |         4            |         0            |
+------------+----------------------+----------------------+----------------------+
| Is group 2 |         7            |        63            |         1            |
+------------+----------------------+----------------------+----------------------+
| Is group 3 |         0            |         0            |        48            |
+------------+----------------------+----------------------+----------------------+

There are 4+7+1=12 wine samples that are misclassified, out of (55+4+7+63+1+48=) 178 wine samples: 
4 samples from cultivar 1 are predicted to be from cultivar 2, 7 samples from cultivar 2 are predicted 
to be from cultivar 1, and 1 sample from cultivar 2 is predicted to be from cultivar 3.
Therefore, the misclassification rate is 12/178, or 6.74%. The misclassification rate is quite low,
and therefore the accuracy of the allocation rule appears to be relatively high.

However, this is probably an underestimate of the misclassification rate, as the allocation rule was based on this data (this is
the "training set"). If we calculated the misclassification rate for a separate "test set" consisting of data other than that
used to make the allocation rule, we would probably get a higher estimate of the misclassification rate.

Links and Further Reading
-------------------------

Here are some links for further reading.

For a more in-depth introduction to R, a good online tutorial is
available on the "Kickstarting R" website,
`cran.r-project.org/doc/contrib/Lemon-kickstart <http://cran.r-project.org/doc/contrib/Lemon-kickstart/>`_.

There is another nice (slightly more in-depth) tutorial to R
available on the "Introduction to R" website,
`cran.r-project.org/doc/manuals/R-intro.html <http://cran.r-project.org/doc/manuals/R-intro.html>`_.

To learn about multivariate analysis, I would highly recommend the book "Multivariate
analysis" (product code M249/03) by the Open University, available from `the Open University Shop
<http://www.ouw.co.uk/store/>`_.

There is a book available in the "Use R!" series on using R for multivariate analyses, 
`An Introduction to Applied Multivariate Analysis with R <http://www.springer.com/statistics/statistical+theory+and+methods/book/978-1-4419-9649-7>`_
by Everitt and Hothorn.

Acknowledgements
----------------

Many of the examples in this booklet are inspired by examples in the excellent Open University book,
"Multivariate Analysis" (product code M249/03), 
available from `the Open University Shop <http://www.ouw.co.uk/store/>`_.

I am grateful to the UCI Machine Learning Repository, 
`http://archive.ics.uci.edu/ml <http://archive.ics.uci.edu/ml>`_, for making data sets available
which I have used in the examples in this booklet.

Contact
-------

I will be grateful if you will send me (`Avril Coghlan <http://www.ucc.ie/microbio/avrilcoghlan/>`_) corrections or suggestions for improvements to
my email address a.coghlan@ucc.ie 

License
-------

The content in this book is licensed under a `Creative Commons Attribution 3.0 License
<http://creativecommons.org/licenses/by/3.0/>`_.

.. |image1| image:: ../_static/image1.png
            :width: 600
.. |image2| image:: ../_static/image2.png
            :width: 400
.. |image4| image:: ../_static/image4.png
            :width: 400
.. |image5| image:: ../_static/image5.png
            :width: 400
.. |image6| image:: ../_static/image6.png
            :width: 400
.. |image7| image:: ../_static/image7.png
            :width: 400
.. |image8| image:: ../_static/image8.png
            :width: 400
.. |image9| image:: ../_static/image9.png
            :width: 400
.. |image10| image:: ../_static/image10.png
            :width: 400
