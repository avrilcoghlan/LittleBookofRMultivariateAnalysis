Using R for Multivariate Analysis
=================================

Multivariate Analysis
---------------------

This booklet itells you how to use the R statistical software to carry out some simple multivariate analyses.

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

    > text(wine$V4, wine$V5, wine$V1, cex=0.5, pos=4, col="red")

If you look at the help page for the "text" function, you will see that "pos=4" will plot the text just to the
right of the symbol for a data point. The "cex=0.5" option will plot the text at half the default size, and
the "col=red" option will plot the text in red. This gives us the following plot:

|image4|

We can see from the scatterplot of V4 versus V5 that the wines from cultivar 2 seem to have
lower values of V4 compared to the wines of cultivar 1. 

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
.. |image2| image:: ../_static/image2.png
.. |image4| image:: ../_static/image4.png

