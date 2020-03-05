---
date: ' 2020-03-05T00:40:57+01:00 '
draft: false
linktitle: Lab 7
menu: 
     MES623Econ:
          parent: Lab Material
          weight: 7
title:  Cost-Effectiveness'
toc: true
type: docs
weight: 7
---


According to wikipedia, **Cost-effectiveness analysis** is "a form of economic analysis that compares the relative costs and outcomes of different courses of action. Cost-effectiveness analysis is distinct from cost-benefit analysis, which assigns a monetary value to the measure of effect."

In our conservation problem of protecting species A and speceies B by purchasing land, we can come up with a couple of courses of action:

1) We can purchase as much land as we need to fulfil our goal, which can be to maximize survival, as long as we stay within our budget. This is what we have been doing since lab 4.

2) We can also fix the level of survival that we want, and purchase the cheapest amount of parcels that fulfil that goal.

Today we will focus on modeling the 2nd course of action. We are asking the question-what fraction of parcels to purchase if we wanted $P(A) \ge X$, $P(B) \ge X$, or $f(P(A),P(B))\ge X$, where $X$ is a predetermined value?

Let's start with one species. Say we want to purchase parcels that acheive a survival probability of 0.7 or greater for species A. 

```{r}
PS_A = 0.7
```

We need all of our species information from the previous labs. Let's add our objects for number of parcels, habitat potential per plot, cost per plot, and the alpha scaling factor.

```{r}
set.seed(10) # we will get different answers, with different seeds

N = 10 # Ten lots

# Habitat potential for sp a, if you do not set seed this will change each time
hp_a = sample(0:2,N,replace=T) 
c = sample(1:10,N,replace=T)  # cost of each full plot
alpha_a = 1/(sum(hp_a))^.5 # Scaling factor for sp a
```

Let's think about what we are optimizing in this case, and how we can translate the optimization word problem into R code.

1) We will be using the same optimization function as before, `solnp()` - think of the arguments we will need, some are optional. Checkout the help menu.

2) If we are looking to minimize the total cost of conserving this species, what should our "`fun`" argument be? Rememeber this is the function to get the value we wish to minimize.

3) What are some of the constraints on our optimization function?


```{r}
# Function to minimize: total cost 
total.cost = function(F){ 

# Survival has to be greater than or equal to 0.7 
# so we have/need an inequality constraint/function
surv.prob = function(F){ 
# Notice the PS is not negative, we are no longer trying to maximize survival

# load the library for optimization
library("Rsolnp") 

Obj = solnp(rep(1e-3,N), # Starting values

FracOfParcel = Obj$pars 
FracOfParcel

TotCost = Obj$values[length(Obj$values)] # Taking the value from the last iteration
TotCost
```
The `$pars` we get from this code still represent the fraction of land we should purchase. What changes are the `$values` that we get. These now represent the minimum price we can pay to have a survival probability of 0.7 or greater for species A.  

Now let's try for two species with a total probability of 0.7 or greater. We are still minimizing the total cost but now we need to set how much we want the total survival to be, given that we care equally about each species.

Does this sound familiar? Look back in your labs (cough cough, Lab 6) to find a function that represents total survival, and allows you to include the level of species importance. Does the function below look familiar?
```{r, eval=FALSE}
opt.surv = function(ff){

} # end opt.surv func
```


All we need to do is update this function so it can be our inequality constraint function (`ineqfun`) in solnp().

1) We need a survival function that can take inputs from multiple species.

2) We need to make sure we set the apprpriate $w$. This can depend on your conservation preferences.

3) We need to make sure we have the right sign for the return probability, since we are no longer minimizing it, it is now an equality function.


Our new functions for optimization should look like the following:
```{r}
```

Let's give R the data for species B, and set the probability of total survival.

```{r}
hp_b = sample(0:2,N,replace=T) # Habitat potential for species A

alpha_b = 1/(sum(hp_b))^.5 # Scaling factor for species B

TotSurProb = .70 # New fixed probability
```

Everything should now be placed in our solnp() function. Similar to the last example, except we have a new inequality constraint function:

```{r}
Obj.AB = solnp(rep(1e-3,N), # Starting values
```

To get a total survival probability of 0.7 or greater, we would purchase the fractions of parcels indicated by `Obj.AB$pars`. This would cost us a minimum of `Obj.AB$values[length(Obj.AB$values)]`.

```{r}
FracToBuy = Obj.AB$pars
FracToBuy

TotalCost = Obj.AB$values[length(Obj.AB$values)]
TotalCost
```

Let's see the survival probability of each species given the fractions of parcels we were told to purchase by the object `FracToBuy`.

```{r}
# use the survival function and our estimated fractions to calculate survival probability 
TPS_A = updated.surv.prob(FracToBuy,hp_a,alpha_a) 
TPS_A

TPS_B = updated.surv.prob(FracToBuy,hp_b,alpha_b)
TPS_B
```

You have now explored two different actions for conservation, which one would you choose and why?



