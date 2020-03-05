---
date: ' 2020-03-05T00:40:56+01:00 '
draft: false
linktitle: Lab 12
menu: 
     MES623Econ:
          parent: Lab Material
          weight: 12
title:  Regulations Continued
toc: true
type: docs
weight: 12
---

Continuing the lab from last week we will now look into other regulatory mechanisms such as trades. We will also explore the effects of including new pollution sources into our calculation of the optimal tax.

```{r,echo=FALSE,results='hide'}
# Last weeks code
Data <- read.csv("lab_data.csv")
Model_A <- lm(Data$MC_A~0+Data$Abatement)
Model_B <- lm(Data$MC_B~0+Data$Abatement)
Model_C <- lm(Data$MC_C~0+Data$Abatement+I(Data$Abatement^2))

Qt_A = 220
Qt_B = 160
Qt_C = 240

linear.cost <- function(x0,x1,b) 
  {
  as.numeric(b/2*(x1^2-x0^2)) # here is the equation for the integral of the linear function
  }

quadratic.cost <- function(x0,x1,b,c) # same as the linear function
  { 
  as.numeric(b/2*(x1^2-x0^2)+c/3*(x1^3-x0^3)) # the integral of the quadratic function
  }

library(Rsolnp)

# Total cost funtion to be minimized
tot.cost = function(X){ # X is a vector of two abatement values, one for source A and one for source C
   # Total cost for Source A
  Cost_A = linear.cost(0,X[1],Model_A$coefficients[1]) # X[1] is the units of abatement for source A
  # Total cost for source C
  Cost_C = quadratic.cost(0,X[2],Model_C$coefficients[1],Model_C$coefficients[2])
  # Total cost
  TC <- Cost_A+Cost_C
  TC
}

# The total abatement has to be 1/4 of the total which is 115
# Function that calculates total abatement
tot.abatement <- function(X)
  { 
  TA <- sum(X) # Sum of the vector of values of abatement
  TA
}

X <- rep(0.1,2) # Create a set of starting values different than zero.

OO <- solnp(X, # Starting values
      fun = tot.cost, # Function to minimize
      eqfun = tot.abatement, # Total abatement achieved
      eqB = 115, # Total abatement goal
      LB = rep(0,2), # Lower bound for decision variables, must be a vector 
      UB = c(Qt_A,Qt_C)) # Upper bound for decision variables, must be a vector as well

X <- OO$pars # extract the level of abatement at the optimal price for both sources
X

# optimal marginal cost/tax calculated using Source C
opt.MC_C=Model_C$coefficients[1]*X[2]+Model_C$coefficients[2]*X[2]^2
opt.MC_C

tax = opt.MC_C 
  
# Cost of polluting for source A
# any units from the point of abatement to the current emissions level, are taxed
as.numeric((Qt_A-X[1])*tax) 
# Cost of polluting for Source C
as.numeric((Qt_C-X[2])*tax)
```



### Abatement Regulation: Cap and Trade
Each source (A and C) is given carbon permits equal to their current emissions (A =220 and C=240) minus 1/4th of their current emissions(A = 55 and  C= 60), thus achieving the total reduction of 115. Then, these two sources are allowed to trade with each other. What are the total cost of abatement and the cost of abatement for each source?

Last week figure out the number of units that should be abated by each source to satisfy the equimarginal principle, using an optimization function. Given these values, sources might choose to buy or sell carbon permits. We determine this by taking the initial abatement allocation and subtracting the optimal abatement level. The difference shows how many permits they buy (positive sign) or sell (negative sign).

```{r}
# Balance for Source A
55-X[1]
# Balance for Source C
60-X[2]
```
Given our results, it looks as though source A would sell ~9 units and source C would then buy those 9 units. Either way, we are still reaching the abatement goal of 1/4th of the total emissions which is 115 units abated.

Each source now sells or buys these permits at the price equal to the optimal tax we derived last week.

```{r}
permit.price = (60-X[2])*tax 
# I used source C to calculate the price becuase it was positive, 
# it is the same for source A just negative.
permit.price
```
The final cost of abating under this cap and trade regulation is calculated by taking the original cost of abatement for each source, then add the permit price (depending on if they are buying or selling permits).

```{R}
# Since Source A sells ~9 units, their cost of abatment decreases
cap.trade.cost_A = as.numeric(linear.cost(0,X[1],Model_A$coefficients[1]))+((55-X[1])*tax)
cap.trade.cost_A

# Since Source C must buy permits to acheive the necessary level of abatment 
# their costs increase
cap.trade.cost_C = as.numeric(quadratic.cost(0,X[2],Model_C$coefficients[1],Model_C$coefficients[2]))+permit.price
cap.trade.cost_C
cap.trade.TotCost = cap.trade.cost_A+cap.trade.cost_C
cap.trade.TotCost
```
Here, We calculate a total cost of $2242.92 to abate under a cap and trade regulation structure. Previously under an optimal tax regulation the total cost to abate was $1887.33.

Let's calculate the level of emissions for each source.
```{r}
# Emissions for Source A
Qt_A - X[1]

# Emissions for Source C
Qt_C - X[2]
```

Given our previous work, how do you think we would go about calculating the cost of pollution/emissions for each of these sources? Does it change from what we calculated previously?

### Regulating additional sources
What if Source B is in a country that does not regulate emissions (no caps, no trades).But we want Source B to join Source A and C in reducing carbon emissions by requiring a cap. Joining in reducing emissions will allow Source B to also trade permits with Sources A and C. 

Let's answer the following questions:

1) Will there be an incentive for Source B to join in the regulation?
2) Is it worth it for Sources A and C to try and Attract Source B?

To answer these questions we need to figure out what our optimal tax will be, and how it affects the levels of abatement and emissions from each source. We now have an additional source so we need to run our optimization function again with this new source.

```{r}
# Total cost function to be minimized
tot.cost = function(X){ 
# X is a vector of two abatement values, one for source A and one for source C
  
  # Total cost for Source A
  Cost_A = linear.cost(0,X[1],Model_A$coefficients[1]) 
 # X[1] is the units of abatement for Source A
 
  # Total cost for Source C
  Cost_C = quadratic.cost(0,X[2],Model_C$coefficients[1],Model_C$coefficients[2]) 
  # X[2] is the units of abatement for Source C
  
  # NEW PEICE
  # Total cost for Source B
  Cost_B = linear.cost(0,X[3],Model_B$coefficients[1]) 
  # X[3] is the units of abatement for source B
  
  # Total cost
  TC <- Cost_A+Cost_C+Cost_B # NEW PEICE: added Cost_B to the total
  TC
}

# The total abatement has to be 1/4 of the total which is 115
# Function that calculates total abatement
tot.abatement <- function(X)
  { 
  TA <- sum(X) # Sum of the vector of values of abatement
  TA
}

X <- rep(0.1,3)
# UPDATED PEICE: 3 instead of 2 
# Create a set of starting values different than zero

OO <- solnp(X, # Starting values
      fun = tot.cost, # Function to minimize
      eqfun = tot.abatement, # Total abatement achieved
      eqB = 115, # Total abatement goal 
      #NOTE: Abatement goal stays the same in the country with Source A and C
      LB = rep(0,3), # Lower bound for decision variables, must be a vector 
      UB = c(Qt_A,Qt_C,Qt_B)) # Upper bound for decision variables

X <- OO$pars # extract the level of abatement at the optimal price for both sources
X
```

What will our new tax or optimal marginal cost be? Remember, we calculate this by using the marginal cost functions we derived earlier. Also, remember that due to the equimarginal principle the optimal marginal cost for each source should be the same.
```{r}
# Source A
New.opt.MC_A= Model_A$coefficients[1]*X[1]
New.opt.MC_A

# Source C
New.opt.MC_C=Model_C$coefficients[1]*X[2]+Model_C$coefficients[2]*X[2]^2
New.opt.MC_C
```
It looks like the tax in Source A and C's country went down due to bringing in Source B. That good for them but how did it affect trade? Remember, the difference between the allocated permits and the optimal level of abatement show how many permits they buy (positive sign) or sell (negative sign).

```{r}
# Balance for Source A
55-X[1]

# Balance for Source C
60-X[2]

# The balance for Source B is tricky. 
# Since their country does not require a cap, they also have no allocated number of permits.
-X[3]
```

It looks like our new Source B will now be able to sell over 55 permits, while sources A and C will be buying permits from Source B now. If I were the owner of Source B, I would be excited!

Finally, let's look at the number of emissions each source will now be producing.
```{r}
# New level of emissions for Source A
Qt_A-X[1]

# New level of emissions for Source C
Qt_C-X[2]
```
Everyone can now emit more pollution. Any idea as to why? If you were thinking because of the wider distribution of abatement, you were correct. So are there really any incentives and if so for who?
