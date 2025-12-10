# script outline draft

## title slide:
hi im xyz and my algorithm is isolation forests.

## background:
<!-- Background ---
who developed the model, what type of problems the model was created to solve, and any other necessary information. (15 pts.)
---
- The type of machine learning problem solved (e.g., regression or classification)
- The model used by the method -->
### isolation forst intro:
isolation forest is a ml algorithm that uses random trees, where each leaf is a single example. It is used for outlier detection

wow, that's a lot! let's break this down. First, what is outlier detection? why are isolation forests used for this domain?

### outlier detection slide:
<!-- outlier == anomaly == abnormal for my use case

outlier detection is also called anomaly detection sometimes. I'll be using the term outlier for my use, to keep things consistent and avoid confusion, since for my purposes, they have the same functional meaning. -->

An outlier is an observation that is unusually different from the rest of the dataset in some way. typically, outlier detection is modelled as a classification problem.

Outliers can about in a couple different ways. 
A common one is errors in data collection. This could be things like an error in the measurement device or the experimental setup. It can also be an error when the data is logged or processed. These factors would be considered data contamination. The data is irrelevant, incorrect, or corrupted in some way.
An outlier might also occur if the occurance of that event is rare. [rare events]

outliers can be of interest for a couple reasons.
The first one is data cleaning. For example, if the source of a set outliers is noise in a sensor, you want to remove outliers so that the model has a cleaner signal to learn off. It's a similar motivation to dimensionality reduction. You want data that is relevant and representative of the problem you are solving.
The other motivation is if the outliers are actually the interesting part of your dataset. For example, things like security or fault detection, fraud dectection, or disease detection. You want to know if there's someone is crypto mining on rosie, or using your credit card, because that activity is going to have a difference usage profile than normal.

There are a couple different approaches to outlier detection.
The first being using a modelling or statistical approach. The normal data is modelled, and if a data point is statistically unlikely to occur according to that model, it would be a possible outlier.
Another is clustering and distance-based approaches. Some clustering algorithm is performed, and observations that are further away or less likely to be in a cluster is a possible outlier.
The last is using a superived model, using labelled data.

### islolation forests (history):
so how do isolation forests fall into all of this?

The isolation forest algorithm was introduced in 2008 by by Fei Tony Liu, Kai Ming Ting, and Zhi-Hua Zhou (Liu et al.).
It was a really big deal because it was unsupervised, so it did not need labelled data,
and it did not use modelling the normal data, so it was much more efficient for high-dimensional datasets.


## Isolation Forest Algorithm:
<!-- Algorithm Description ---
A step-by-step description of the “algorithm in action” is presented. Use of data visualization is highly recommended. (20 pts.)
---
- The cost function that is optimized to fit the model
- How the model is fit (describe the algorithm)
- How a fitted model is used to make predictions -->

### algorithm:
<!-- and works off the assumption , using the path length (number of random feature splits before isolation) to measure the likelihood of a data point to be abnormal, called the outlier score. -->
So, how does Isolation Forest work?

First, an ensemble of isolation trees are created. Whattt? An isolation forest is made up of isolation trees. 
An isolation tree is a random binary tree where each leaf is a single example (or is at the max depth). A random binary tree, meaning the features and thresholds that it splits on are random AND each node in the tree either has two children or is a leaf node. Addionally, I use the term example instead of observation because observations with identical features will end up in the same leaf node, since there is nothing to seperate them on.

Once the isolation forest is created, the path length for each observation is calculated. The path length is the number of random feature splits for the observation to be isolated.

Once you have the path length for each observation 
Finally, the outlier score for each observation is calculated based on the average path length across all trees in the forest. 
The outlier score is calculated using the formula:
$$s(x, n) = 2^{-\frac{E(h(x))}{c(n)}}$$

where E(h(x)) is the average path length of observation x across all trees in the forest, n is the number of observations in the dataset, and c(n) is the average path length of unsuccessful searches in a binary search tree, which is used to normalize the outlier score.

The isolation forest algorithm works on the assumption that outliers will be easier to isolate than inliers, so they will have shorter path lengths and higher outlier scores.


## Model Applicaton
<!-- Model Application ---
model parameters, hyperparameters, and evaluation metrics. (20 pts.)
---
- Detailed explanation of implementation decisions
- An example of applying the model to the chosen example data set
- Evaluation of the predictions using appropriate metrics -->

### implementation decisions:

### application example:
I applied my implementation of isolation forest to two datasets: a spine dataset and malware dataset.

#### spine dataset:
The spine dataset ...

##### evaluation:

#### malware dataset:
The malware dataset ...
##### evaluation:

## Discussion
<!-- Discussion ---
Advantages and disadvantages of the model/algorithm should be discussed. Consider discussing topics such as time or space complexity for the model fitting and prediction
---
- Discussion of advantages/disadvantages of the model/algorithm
- Analysis of time and space complexity for both training and prediction
- Comparison of custom implementation performance against standard libraries
- Discussion of optimization techniques used -->

### time complexity:
My implementation of isolation forest has a time complexity of 

### space complexity:
My implementation of isolation forest has a space complexity of

### comparison to standard libraries:
My implementation of isolation forest performs similarly to standard libraries such as scikit-learn.

To compare my implementation to scikit-learn's, I evaluated both implementations on my malware dataset.
For training runtime, my implementation was slightly faster than scikit-learn's. This is likely because my implementation omits some features that scikit-learn includes.
For prediction runtime, scikit-learn's implementation was slightly faster than mine. This is likely because scikit-learn uses Cython optimizations, while I used pure numpy and list comprehension. This is understandable, since the prediction step involves path tracing an observation through every tree in the forest, so  lower-level optimizations really add up.

Comparing how both implementations performed on the malware dataset, 

<!-- Pure Python/NumPy (sklearn uses Cython optimizations)
List comprehension for path tracing vs vectorized operations -->

### optimization techniques:
I used the following optimization techniques in my implementation of isolation forest:
- 

### advantages:
Some advantages of isolation forests are:

### disadvantages:
Some disadvantages of isolation forests are:

