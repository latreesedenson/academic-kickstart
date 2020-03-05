---
date: ' 2020-03-05T00:40:57+01:00 '
draft: false
linktitle: Lab 6
menu: 
     MES623Econ:
          parent: Lab Material
          weight: 6
title:  Conservation Strategies'
toc: true
type: docs
weight: 6
---
As a continuation of last week, we are still working with the conservation example of purchasing lots to increase the survival of two species. 

Last week, we explored different variations of species' importance to conservation, where $0\leq w\leq 1$ is the weight or level of importance given to species a, and by default $1-w$ as the weight given to species b, accordingly. But what if we did not know which species was more important to the environment but we want to know what the attainable survival probabilities would be for the different values of $w$ for both species?

We would need the maximum probabilty of survival for each species given the various levels of importance. From here we could creat the **efficiencey frontier** with the survival probabilites for both species. 

Let's bring in some code from last week:
```{r}
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

```


Now for some new code. First, we will generate a sequence of $w$'s and estimate the optimal purchases for each of them. 

```{r}
w = seq(0,1,.05) # create a sequence from 0 to 1 with steps of .05
```

We need to loop through these $w$'s and save the results from each loop to a matrix. If you recall from last week, our estimated parameters were 10 different factions, so we need 10 rows in our matrix. Now we will have a set of fractions for each value of $w$, so we need a column for each value of $w$. The number of columns needed is determined by `length(w)`.

```{r}
K = length(w) # Number of times we need to run the loop

TF = matrix(0,N,K) # Empty matrix to store the optimal values for each iteration. Each column is a diferent iteration.  
```

Here is the loop format. Next, we will work on what goes inside the loop.
```{r,eval = FALSE}
for(k in 1:K)

```

To estimate the optimal survival of each species for each of value of $w$, we have to calculate surival for both species for each value of $w$. Basically, we need to create an optimization function that updates the value of $w$ every time by indexing by k:

```{r}
library("Rsolnp") # for the optimizer

for(k in 1:K){ # start loop



# Did we get what we wanted?
TF

# Name the column so you know what you are looking at
# colnames(TF) =paste("w=", seq(0,1,0.05)

```

Now we want to calculate the probabilities of survival using those estimated optimal fractions of land to purchase given the different levels of importance. 

We use the apply function here, `apply` works with applying functions to an entire matrix. You can use `lapply` if you want to apply the function to a list or `tapply` for an array. Examine the help file for `apply` to understand the inputs.


```{r}
# calculate the probobility of survival for each species 
# given the estimated fraction paramters
PS_A = apply(TF,2,surv.prob,hp=hp_a,alpha=alpha_a)
PS_B = apply(TF,2,surv.prob,hp=hp_b,alpha=alpha_b)

# Make into a dataframe for plotting in ggplot
Data = as.data.frame(cbind(PS_A,PS_B))


```

To plot our results, we can do the exact same process as before. The graph should look like a standard production possibilities frontier plot as showb below.

```{r}
library(ggplot2) # for plots
library(tidyverse) # for pipping "%>%"

Data %>% ggplot(aes(x=PS_B,y=PS_A)) + 
```


Here is your efficiency frontier representing the range of survival probabilities that maximize the total survival for the 21 different importance levels. You can't see all 21 on the graph because some overlap. Any place on the efficiency frontier represents an optimal combination of survival for each species, that maximizes the total survival given various levels of importance. Any place inside of this curve indicates that there is need for improvement in your conservation strategy and you are not maximizing the probabilty of survival for either species.
