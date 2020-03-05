---
date: ' 2020-03-05T00:40:56+01:00 '
draft: false
linktitle: Lab 13
menu: 
     MES623Econ:
          parent: Lab Material
          weight: 13
title:  Review'
toc: true
type: docs
weight: 13
---
## Intro to R (Labs 1 & 2)
#### Install packages and  Change Directories
Here are the functions to "install" and "load" packages for all of the packages we used in lab this semester.
```{r, eval=FALSE}
install.packages("tidyverse")

library(tidyverse)
library(ggplot2)
library(Rsolnp)
```
Remember, to set the directory to a specific folder:

1. From your desktop or start menu, navigate to the folder you wish to save it, and open it.
2. Right-click in any empty space inside the folder, select properties.
3. Select and copy the location (e.g. "C:\\\\Users\\\\lxd\\\\Desktop").
4. Paste the location name inside the setwd() parentheses and add your file name (e.g."\\\\MES623_Lab").

```{r,eval = FALSE}
setwd("/Users/lxd312/Desktop/MES623_Lab")
```
**Note:** In this character string, you can only use a "/" or "\\\\" between folder names.  

```{r, eval=FALSE}
# Check to see if you are in the right place using getwd(), 
# then use the function dir() to see what is in the folder.
getwd()
dir()

# Function to create a folder named "Cash"
dir.create("Cash")

```

#### Read, Manipulate, and Export Data
Most of the manipulation functions come from the tidyverse package, click [here](https://s3.amazonaws.com/assets.datacamp.com/blog_assets/Tidyverse+Cheat+Sheet.pdf) for the cheat sheet.
```{r, eval = FALSE}
# Read in your data
read.csv()

# Data stats
summary()
head()
tail()
names()
dim()

# Minipulation functions using tidyverse
select()
filter()
arrange()

#Export your data
write.csv()

```

#### Functions and Loops
```{r}
func.name = function(arg1,arg2)


for (i in 0:5) 

```

## Simulation and Visualization (Lab 3)
Simulating the market for a certain item, and then plotting the demand function for a certain item.
```{r}
prices = seq(1,100,by= 5) # generate a vector of prices
p_n = length(prices) # save the length as an object to be used later
r.prices =  rnorm(p_n,prices,5) # generate random error around the prices 

eval = function(slope = -.4,intercept=50,p =10) 

# Use the eval function to calculate quantities and add random error
r.quant = rnorm(p_n,eval(p = r.prices),1) 

# Create a dataframe to use in ggplot
r.dem = as.data.frame(cbind(r.prices,r.quant))

# Plot the demand curve 
library(ggplot2)

Plot.OG = ggplot(r.dem, aes(x = r.quant, y = r.prices)) +  # input the data and identify x and y
# label the graph using x and y as placements

Plot.OG
```

## Environmental Conservation (Labs 4 & 5)
#### Optimization
This semester we learned that we can use optimization to solve many of our environmental conservation problems in economics. Recall the function we used most often `solnp()` and its format.
```{r, eval= FALSE}

OO = solnp(F, # Starting values
OO

```


## Conservation Strategies (Lab 6)
#### Efficiency Frontier 

What was an efficiency frontier? Easy, a curve representing the range of possibilities that maximize the goal. 

For our example in lab 6, the survival of species A and B, any place on the efficiency frontier represented an optimal combination of survival for each species, that maximized the total survival given various levels of importance. Any place inside of this curve indicated that there was need for improvement in your conservation strategy and you are not maximizing the probabilty of survival for either species.
```{r, warning=FALSE, echo = FALSE}
N = 10 # Ten lots.

set.seed(0) # Set seed so R remembers the random numbers that we are going to generate.

hp_a = sample(0:2,N,replace=T) # Habitat potential for sp a
hp_b = sample(0:2,N,replace=T) # Habitat potential for sp b

c = sample(1:10,N,replace=T)  # The cost of each lot
B = 12 # Budget is 12 million

alpha_a = 1/(sum(hp_a))^.5 # Scaling factor for sp a
alpha_b = 1/(sum(hp_b))^.5 # Scaling factor for sp b

# function to calculate survival probabilities
surv.prob = function(F, hp, alpha){ 

# function to calculte total cost
total.cost = function(F){ 

w = seq(0,1,.05) # create a sequence from 0 to 1 with steps of .05

K = length(w) # Number of times we need to run the loop

TF = matrix(0,N,K) # Empty matrix to store the optimal values for each iteration. Each column is a diferent iteration.

library("Rsolnp") # for the optimizer

for(k in 1:K){ # start loop


# calculate the probobility of survival for each species 
# given the estimated fraction paramters
PS_A = apply(TF,2,surv.prob,hp=hp_a,alpha=alpha_a)
PS_B = apply(TF,2,surv.prob,hp=hp_b,alpha=alpha_b)

# Make into a dataframe for plotting in ggplot
Data = as.data.frame(cbind(PS_A,PS_B))

library(ggplot2) # for plots
library(tidyverse) # for pipping "%>%"

Data %>% ggplot(aes(x=PS_B,y=PS_A)) + 

```

## Cost-effectiveness (Lab 7)

**Cost-effectiveness analysis** is "a form of economic analysis that compares the relative costs and outcomes of different courses of action. Cost-effectiveness analysis is distinct from a cost-benefit analysis, which assigns a monetary value to the measure of effect."

In our conservation problem of protecting species A and species B by purchasing land, we can come up with a couple of courses of action:

1) We can purchase as much land as we need to fulfill our goal, which can be to maximize survival, as long as we stay within our budget. This is what we have been doing since lab 4.

2) We can also fix the level of survival that we want, and purchase the cheapest amount of parcels that fulfill that goal.

Remember, we tackled these by changing what our optimization function would be. For action 1 we optimized the survival probability, for option 2 we optimized the total cost.

## Willingness to Pay (Labs 8, 9, & 10)
Remember what a regression was and how we can use the regression line to evaluate willingness to pay?

Regression is a technique that can be used for prediction, estimation, hypotheses testing, and modeling casual relationships.

The equation for a basic linear regression is $Y=\alpha+\beta X+\varepsilon$, where $\alpha$ is the intercept, $\beta$ is the coefficient or slope, and $\varepsilon$ represents the error structure of the data.

Specifically, $\beta$ represents the relationship between $X$ and $Y$ and is verbally interpreted as "the expected change in the average response variable ($Y$) that is associated with an additional unit of the explanatory variable ($X$)." Sound familiar? This is similar to the **marginal** cost/benefit of purchasing/producing 1 additional unit. If $\beta$ is zero then the regression is not significant and there is no relationship between $X$ and $Y$ (i.e. no change in marginal cost or benefit).

## Regulations (Labs 11 & 12)
The goals of these final labs were to determine how different regulations would affect the cost of carbon emissions as well as the cost of abatement. The regulations we explored were "caps", "taxes", and "caps & trades."

For this lab we had to pull a few skills from old labs as well as some new skills:

1) We created Marginal Cost Functions by fitting a regression line to the cost data.
2) We created Marginal Benefit Functions by manipulationg our cost functions and level of current emissions.
3) Calculated total cost from marginal cost by calculating the area under the curve using an integral.
4) Applied the equimarginal principle to determine the optimal cost of emissions across all sources.
5) Calculated the total cost of emissions and abatement for all sources involved in pollution under all regulations.

**Given labs 1-12, you have now learned how to answer questions concerning economics and environmental conservation using models in R. Congratulations!**
