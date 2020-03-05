---
date: ' 2020-03-05T00:40:57+01:00 '
draft: false
linktitle: Lab 5
menu: 
     MES623Econ:
          parent: Lab Material
          weight: 5
title:  Environmental Conservation'
toc: true
type: docs
weight: 5
---

## Constrained Optimization Recap

Last week, we learned that real world conservation problems can be solved with the help of computers and optimization. Optimization allowed us to find the set of elements that minimized our objective function.

Last week, we wanted to determine what fraction of 10 different lots we could purchase to increase the survival of a species. We simulated costs and habitat survival potentials for each lot. We also had a budget of 12 million.
```{r}
N = 10 # Ten lots.

set.seed(0) # Set seed so R remembers the random numbers that we are going to generate.

hp = sample(0:2,N,replace=T) # Habitat potential

c = sample(1:10,N,replace=T)  # The cost of each lot

B = 12 # Budget is 12 million

THP = sum(hp) # Total habitat potential when all lots are purchased.

alpha = 1/(THP)^.5 # Scaling factor so the probability adds to one.

```

Since our total cost changes as a function of the fraction of land that was purchased, we changed this into a function. We also create our objective function, the goal of which is to maximize the probability of survival of a species (i.e., minimize the negative probability).
```{r}
# Function that calculates the total cost, it ONLY takes F as a parameter
total.cost = function(F){ 

surv.prob = function(F){
} 

```
We then used the package  `Rsolnp` to determine what fraction of each lot should be purchased so that we stay within our budget and acheive our goal.

```{r}
# Load the package
library("Rsolnp")

# Create a set of starting values and make them slightly greater than zero.
F = rep(1e-3,N) 

# Run the optimization function
OO = solnp(F, # Starting values

# Print the results

# Retrieve the optimal lot purchase fractions 
F_star <- OO$par

```


## More Optimization With Constraints

Usually, there is more than one thing that enters the decision-making process of a policy maker when discussing environmental coservation. For example, what if there is more than one species that is affected by the conservation efforts and we still don't know what fraction of each lot to buy? You guessed it! Optimization! Following a similar approach to last week, let's create some values to turn the previous analysis into a two-species problem:

```{r}
# Simulate data for the new species "species b"
hp_b = sample(0:2,N,replace=T) # Habitat potential for species b

THP_b = sum(hp_b) # Total habitat potential when all lots are purchased for species b.

alpha_b = 1/(THP_b)^.5 # Scaling factor so the probability adds to one.

# rename vectors to differentiate between species 
alpha_a = alpha

hp_a = hp
```

Next, we have to update our survival prob function and create an additional objective function that takes into account both species and how important we deem one or the other. Let's define $0\leq w\leq 1$ as the weight or level of importance given to species a, and by default $1-w$ as the weight given to species b, accordingly. Our new objective function is the weighted sum of the probability of survival of both species for any given fraction of land, $\pmb{F}$, and weight of a species, $w$. In R, we can code this as follows:

```{r}
# since we have two species now hp and alpha will be changing 
# We need to make adjustments for this in the arguments of our function
updated.surv.prob = function(F, hp, alpha){ 


# Creating an objective function that focuses on 
# the fraction (ff) of land purchased as a changing argument 
# but also includes our ideas about the importance of a species (50-50 currently).
obj.agg.surv = function(ff){
}
```


```{r}
agg.OO <- solnp(F, # Starting values

# Print the results
agg.OO
```
Using this setup, you can conduct the analysis for any value of $w$ we may want, including single-species cases only (w =1 or w=0). To conduct the optimization process for these new specifications, all we need to do is update the objective function with the new values and run the optimizer. We would do this for each value of w and save the results. I will let you figure out how to do this.


## Further Analysis
We can also evaluate our optimization results from last week (F_star) as follows:

```{r}
# Calculate aggregated survival probabilty over any combination of F and w
agg.surv <- function(F,w,hp_a,hp_b,alpha_a,alpha_b){
}

# Aggregate probability of survival if we consider the fractions from last week
# and only care about species a,thus w=1
AS = agg.surv(F_star,w=1,hp_a,hp_b,alpha_a,alpha_b)
AS

# All we care about is species b, thus w=0.
BS = agg.surv(F_star,w=0,hp_a,hp_b,alpha_a,alpha_b) 
BS

# We care equally about both probabilities, thus w=.5.
ABS = agg.surv(F_star,w=.5,hp_a,hp_b,alpha_a,alpha_b) 
ABS

```
These are the total probabilities of survival given last week's choices of what fractions (F or ff) of lots to purchase and our ideas on how important the survival of one species is over another. 

