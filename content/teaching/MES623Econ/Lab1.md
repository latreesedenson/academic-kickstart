---
date: "2020-01-09T00:00:00+01:00"
draft: false
linktitle: Lab 1
menu:
  MES623Econ:
    parent: Lab Material
    weight: 1
title: "R Intro"
toc: true
type: docs
weight: 1
---
## Installation 

1. Click [here](https://cran.r-project.org) to download R.
2. Click [here](https://www.rstudio.com/products/rstudio/) to download "RStudio," the Integrated Development Environment (IDE) that allows users to interact with R.


## Determine, Set, and Create Directories

Determine which directory you are in:
```{r, echo = TRUE}
getwd()
```

**ProTip:** Use Ctrl + Enter (Windows) or Cmd + Enter (Mac) to run the current line of code using a keyboard shortcut.

Not the right directory? That's fine--let's change it!  
  
To change or set the directory in R use the setwd() function. 

For example, to set the directory to a specific folder:


1. From your desktop or start menu, navigate to the folder you wish to save in, and open it.
2. Right-click in any empty space inside the folder, select properties.
3. Select and copy the location (e.g. "C:\\\\Users\\\\lxd\\\\Desktop").
4. Paste the location name inside the setwd() parentheses and add your file name (e.g."\\\\MES623_Lab").
In this tutorial, I'll share my top 10 tips for getting started with Academic:

```{r}
setwd("/Users/lxd312/Desktop/MES623_Lab")
```
**Note:** In this character string, you can only use a "/" or "\\\\" between folder names.  
  
  
To keep things tidy, create another folder specifically for today's lab. This can be done by creating the object "todays.file" where you will change the name from day to day.
```{r}
todays.file = "Lab_1"
```

```{r, eval = FALSE}
dir.create(todays.file)
```


Set the directory to "todays.file" to keep things saved in the right place.
```{r, eval = FALSE}
setwd(todays.file)
```
**ProTip:** R does not like spaces. Use underscores instead.


Check to see if you are in the right place using getwd(), then use the function dir() to see what is in the folder.
```{r}
dir()
```
**ProTip:** Messed up? Want to move up one directory? No problem! Run "setwd("..")."

Good to go? Let's move on.

## Install and Load Packages

There are millions of R packages designed to do all kinds of data analyses. Two packages that I have grown fond of for manipulating and visualizing data are dplyr and ggplot2. Fortunately, somebody decided to bundle these packages and a few others in what is called the "tidyverse". We can install each package individually using multiple install.packages() functions or we can just install the tidyverse package.

**dplyr:** a package for data manipulation

**ggplot2:** a package for creating graphics

**tidyverse:** the data analysis super package!

```{r, eval = FALSE}

#install.packages("ggplot2")
#install.packages(c("ggplot2","dplyr"))

# All necessary packages can be installed using this line
install.packages("tidyverse")
```
**Note:** Installing the package only needs to be done once.

Load the package every time a new R session is started using the library() function.
```{r, echo = TRUE}
library(tidyverse)
```
**Note:** If you are ever wondering how to use these packages check out the cheat sheets:


[dplyr cheat sheet](https://www.rstudio.com/wp-content/uploads/2015/03/ggplot2-cheatsheet.pdf)

[ggplot2 cheat sheet](https://ugoproto.github.io/ugo_r_doc/dplyr.pdf)

... or Google it!

## Data
### Load Data
Download the [mtcars](https://vincentarelbundock.github.io/Rdatasets/csv/datasets/mtcars.csv) data set and save it to the newly created lab folder as a .csv file: 


Now read it in using the function read.csv() and give it the object name "data":
```{r}
data = read.csv("mtcars.csv")
```

### Explore Data
Make sure the data was read in correctly using the function head(). 
```{r}
head(data)
```
**ProTip:** Not sure what a function does? Type "?head()" into the console and see what pops up in the help menu. Scroll down to the bottom--there are often examples of how to use the function that you can practice with.

Looks good to me!

What if we want to see the first eleven rows of the dataset?
```{r}
head(data, n = 11)
```

What if we want to see the bottom 5 rows of the dataset?
```{r}
tail(data, n = 5)
```

What are the column names?
```{r}
names(data)
```

What are the dimensions of the dataset, how many rows and columns?
```{r}
dim(data)
```



Last but not least, summerize the data using the function summary().
```{r}
summary(data)
```

Here we have information about the minimum and the maximum from each column.


We often just want to know about one variable. To grab a single variable in R (from a data frame or matrix), use the \$\. To grab the horsepower (hp) from the mtcars dataset type data$hp. To summarize the horsepower variable:
```{r}
summary(data$hp)
```

### Manipulate Data

What if we only cared about the car name (X), the miles per gallon (mpg), the number of cylinders on the engine (cyl), what kind of transmission it was (am), the weight (wt), and the gross horsepower (hp)?

We could subset the data and create a new object using the select() function in the package dplyr. I also show how to do this manipulation using base R (no package).

```{r}
data_sub = select(data,X,mpg,cyl, hp, wt, am)

# Check the data
head(data_sub)

# Using Base R
data_sub_Old = data[,c("X","mpg","cyl","hp","wt","am")]

# Check the data
head(data_sub_Old)
```
We get the same answer!


  
Let's arrange by mpg, smallest to largest (asending order). Here we use the arrange() function in dplyr. Asending order is the default for arrange; if we wanted desending we would use the function desc().
Remember to save this arranged table to an object (e.g. data_sub_mpg)

```{r, echo = TRUE}
data_mpg = arrange(data_sub,desc(mpg))

#check the data
head(data_mpg)

# Using Base R
data_mpg_Old = data_sub_Old[order(-data_sub_Old$mpg),]

#check the data
head(data_mpg_Old)

```
Looks like my Corolla beats everybody!


I can't drive a manual so I only want an automatic transmission (am = 0), but I still want a car that has a good mpg. This can be done using the filter() function in dplyr on the data we just sorted.

```{r}
auto_data = filter(data_mpg,am == "0" )

#check the data
head(auto_data)

# Using base R
# Think of this as rows where am is equal to "0"
auto_data_Old = data_mpg_Old[data_mpg_Old$am =="0",]

head(auto_data_Old)
```
**Note:** "==" is a [logical operator](https://www.statmethods.net/management/operators.html). It poses the question -- is something "exactly equal to" something else? We are looking for the rows where "am" is "exactly equal to" 0.


**All together now!** Use the pipe operator "%>%" from the magrittr package within the tidyverse package. This syntax leads to code that is easier to write and to read.USe the keyboard shortcut: Ctrl + Shift + M (Windows) or Cmd + Shift + M (Mac).

```{r}
All_auto_data = data_sub %>% arrange(desc(mpg)) %>% filter(am == "0")

head(All_auto_data)
```

### Export Data
We want save this newly manipulated data to a csv file on our hard drive. This is done using the function write.csv()
```{r}
write.csv(All_auto_data,"AutomaticTrans.csv", row.names = FALSE)
```
**Note:** Don't forget the quotes around the file name and the ".csv" in the file name. If you don't put "row.names = FALSE" you will have row numbers printed in your excel file.


### Visualize Data
_I show how to do all visualizations in ggplot and base R._

Let's look at our original data set and draw a scatter plot. 
```{r}
ggplot(data = mtcars, mapping = aes(x = wt, y = mpg)) + geom_point()

# Base R
plot(mtcars$wt, mtcars$mpg, pch=19, ylab = "mpg", xlab ="wt") 
```

Lets add a smooth line using stat_smooth().
```{r}
ggplot(data = mtcars, mapping = aes(x = wt, y = mpg)) + geom_point() + 
  stat_smooth(method = 'lm')

#Base R
plot(mtcars$wt, mtcars$mpg, pch=19, ylab = "mpg", xlab ="wt") 
abline(lm(mtcars$mpg~mtcars$wt), col="slateblue")
```  

Use the facet_wrap() function to seperate graphs by the level of a certain factor--cyl in our case.
```{r}
ggplot(data = mtcars, mapping = aes(x = wt, y = mpg)) + geom_point() + 
  stat_smooth(method = 'lm') + facet_wrap(~ cyl)

# Base R
# 1st subset the data, change the graphical prameters, then plot the seperate graphs
cyl_4 = mtcars %>% filter(cyl == "4")
cyl_6 = mtcars %>% filter(cyl == "6")
cyl_8 = mtcars %>% filter(cyl == "8")

par(mfrow = c(1,3))
plot(cyl_4$wt, cyl_4$mpg, pch=19, ylab = "mpg", xlab ="wt") 
abline(lm(cyl_4$mpg~cyl_4$wt), col="slateblue")

plot(cyl_6$wt,cyl_6 $mpg, pch=19, ylab = "mpg", xlab ="wt") 
abline(lm(cyl_6 $mpg~cyl_6 $wt), col="slateblue")

plot(cyl_8$wt, cyl_8$mpg, pch=19, ylab = "mpg", xlab ="wt") 
abline(lm(cyl_8$mpg~cyl_8$wt), col="slateblue")

#Reset graphic parameters
par(mfrow = c(1,1))
# dev.off() # to start graphing in a totally new device 
```
**ProTip:** Remember to use dev.off() or reset the graphic parameters if not, all of your graphs will print in the (1, 3) format.

**Note:** The range of the X-axes are the same using ggplot2 across number of cylinders but not for base R. The ggplot2 graphic is a lot easier to understand quickly.


Attempt a box plot using the geom_boxplot() function.
```{r}
ggplot(mtcars, aes(x=as.factor(cyl), y=mpg)) + 
  geom_boxplot(fill="slateblue", alpha=0.2) +  xlab("cyl")

# Base R
boxplot(mpg~cyl,data=mtcars,xlab="cyl", ylab="mpg") 
```

Attempt a histogram of the data using the geom_histogram() function. Change the binwidth to see what happens.
```{r}
ggplot(mtcars, aes(x = mpg)) +
  geom_histogram(binwidth = 1)

# Base R
hist(mtcars$mpg, breaks=34, col="darkgrey", main = "", xlab="mpg") 
```

# Final ProTips
1. Comment your code! ( Use the hashtag # )
2. R is case-sensitive. For example, "head()" is not the same as "Head()." 
3. Be smart when naming files, folders, etc.. You will be looking for them later. Adding the date or sequential numbers to a folder name is helpful.


# Additional Resources
[Ed Rubin's Econometrics Course Notes](http://edrub.in/ARE212/resources.html)[^1]

[Quick-R By DataCamp](https://www.statmethods.net/)

[Fundamentals of Data Visualization](https://serialmentor.com/dataviz/)


[^1]: The flow and content included in this document were heavily inspired by Ed Rubin's course notes.

