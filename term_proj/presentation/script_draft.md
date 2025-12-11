# script outline draft

## title slide:
hi im leigh and I will be presenting on isolation forests.

## background:
<!-- Background ---
who developed the model, what type of problems the model was created to solve, and any other necessary information. (15 pts.)
---
- The type of machine learning problem solved (e.g., regression or classification)
- The model used by the method -->

### what is outlier detection:
<!-- outlier == anomaly == abnormal for my use case

outlier detection is also called anomaly detection sometimes. I'll be using the term outlier for my use, to keep things consistent and avoid confusion, since for my purposes, they have the same functional meaning. -->

An outlier is an observation that is unusually different from the rest of the dataset in some way. typically, outlier detection is modelled as a classification problem.

Outliers can come about in a couple different ways. 
A common one is errors in data collection. This could be things like an error in the measurement device or the experimental setup. It can also be an error when the data is logged or processed. These factors would be considered data contamination. The data associated with the outlier is irrelevant, incorrect, or corrupted in some way.
An outlier might also occur if the occurance of a specific event is rare. These outliers are legitimate data points, but they are uncommon, so they stand out from the rest of the data.

outliers can be of interest for a couple reasons.
The first one is data cleaning. For example, if the source of a set outliers is noise in a sensor, you want to remove outliers so that the model has a cleaner signal to learn off. It's a similar motivation to dimensionality reduction. You want data that is relevant and representative of the problem you are solving.
The other motivation is if the outliers are actually the interesting part of your dataset. For example, things like security, fault detection, fraud dectection, or disease detection. You want to know if there's someone is crypto mining on rosie, or using your credit card, because that activity is going to have a difference usage profile than normal.

### approaches to outlier detection:
There are a couple different approaches to outlier detection.
The first is you could model what 'normal' looks like statistically, and flag points that are statistically unlikely to occur according to that model. This can get complex quickly, especially in high-dimensional spaces.
Another approach is to cluster the data, and identify points that are further away from clusters. This can also struggle in high-dimensional spaces due to the curse of dimensionality.
You could also use supervised learning, but you'd need labeled anomalies.

### isolation forests (history):
The isolation forest algorithm was introduced in 2008 in a paper by Liu(le-ew), Ting, and Zhou(zow), and it was really big at the time because it:
- it did not need labelled data
- did not model normal data distribution
- handled high-dimensional data well
- and ran in linear time relative to dataset size

The algorithm relies on a core assumption that outliers are easier to isolate than normal points.

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
[formula]

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

- random feature selection at each split (over weighted feature selection)
- Split values sampled uniformly between min and max values of selected feature
- stopping criteria:
  1. max_height = ceil(log2(max_samples)) (average height of a balanced binary search tree built with n points)
  2. Node contains only 1 sample
  3. All samples in the node have identical feature values (no split possible)

**Hyperparameters:**
- n_trees=100
- max_samples=512 (subsampling trees)
- contamination
- random_state


### application example:


#### spine dataset:
classifying spinal abnormalities based on biomechanical features. 5 features, 10% contamination

##### evaluation:
results sucked

#### why?
outliers were acutally in the main clusters and the dataset had global outliers that were not part of the anomaly class

#### malware dataset:
The TUANDROMD dataset contains Android malware detection features from static analysis.

199 features after removing missing data
binary features based on app permissions, API calls, and system behaviors
contamination rate of ~20.5% malware samples (larger class)

##### evaluation:

- Precision of 58.4% (half of flagged samples were actual malware)
- Recall of 33.9% (catches about 1/3 of all malware)
- ROC AUC of 0.68 ( moderate discriminative ability)

##### improving my model with feature selection
I implemented feature importance scoring based on average path length reduction across all trees.

I used this to prune the feature set 


## Discussion
<!-- Discussion ---
Advantages and disadvantages of the model/algorithm should be discussed. Consider discussing topics such as time or space complexity for the model fitting and prediction
---
- Discussion of advantages/disadvantages of the model/algorithm
- Analysis of time and space complexity for both training and prediction
- Comparison of custom implementation performance against standard libraries
- Discussion of optimization techniques used -->

### time complexity:

basic tree building and traversal time complexity (win, very efficient :D)

### space complexity:

Trees are efficient! esp with my trees subsampling only 512 samples each (max depth 9)

### comparison to standard libraries:
I compared my implementation against scikit-learn's IsolationForest on the TUANDROMD malware dataset (4,464 samples, 199 features).
For training runtime, my implementation was slightly faster than scikit-learn's. This is likely because my implementation omits some features that scikit-learn includes.

For prediction runtime, scikit-learn's implementation was slightly faster than mine. This is likely because scikit-learn uses Cython optimizations, while I used pure numpy and list comprehension. This is understandable, since the prediction step involves path tracing an observation through every tree in the forest, so  lower-level optimizations really add up.

Prediction agreement: 80.5% between implementations
- Scikit-learn better at ranking (higher AUC) despite lower raw precision

<!-- Pure Python/NumPy (sklearn uses Cython optimizations)
List comprehension for path tracing vs vectorized operations -->

### optimization techniques:
I used the following optimization techniques in my implementation of isolation forest:
subsampling
 - Limit max_samples (512) instead of full dataset
 - Reduces training complexity from $O(n \log n)$ to $O(\psi \log \psi)$
 - Makes training time independent of dataset size

numpy
 - NumPy boolean masking for node partitioning: `X[X[:, feature] < split_value]`
 - Vectorized path length calculations where possible
 - avoided Python loops (yay!)

depth limit
 - Limit tree depth to $ceil(\log_2(max\_samples))$
 - don't need depth pas average, since shorter paths are of interest 

### advantages:
efficient
unsupervised
works well in high-dimensional data
linear memory

### disadvantages:
sensitive to only global anomalies
need contamination param


