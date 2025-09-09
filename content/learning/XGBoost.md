---
title: "XGBoost"
date: 2025-08-27
draft: false
description: ""
tags: ["machine learning", "algorithms"]
categories: ["learning"]
math: true


---

## XGBoost Explained 



XGBoost is an optimized implementation of Gradient Boosting and is a type of ensemble learning method that combines multiple weak models to form a stronger model. 

* It use decision tree as its base learner and combines them sequentially to improve the model’s performance. Each new tree is trained to correct the errors made by the previous tree and this process is call *`Boosting`*
* It has builting-in parallel processing to train models on large data. It support customization allowing users to adjust model parameters to optimizae performance based on the specific problem.

### Process

1. Start with the base learner: The first model decision tree is trained on the data. In regression tasks this base model predicts the average of the target variable. 
2. Calculate the errors: After training the first tree the errors between the prediction and actual value are calculated. 
3. Train the next tree: The next tree is trained on the errors of the previous tree. This step attempts to correct the errors made by the first tree. 
4. Repeat the process: This process continues with each new tree trying to correct the errors of the previous tree until a *`stoppping criterion`* is met. 
5. Combine the prediction: The final prediction is the sum of the prediction from all the trees. 



### Mathematic behind XGBoost

It can be viewed as iterative process where we start with an initial prediction often set to zero. After each tree is added to reduce errors, the model can be represented as: 
$$
\hat y_i = \sum_{k = 1}^K f_k (x_i)
$$

* $\hat y_i$  is the final predicted value  for the ith data point
* K is the number of the tree in the ensemble
* $f_k(x_i)$  represents the prediction of kth tree for the ith data point

The objective function in XGBoost consists of two parts: a loss function and a regularization term. 
$$
obj(\theta) = \sum_i^n l(y_i, \hat y_i) +\sum_{k = 1}^K \Omega(f_k)
$$
 

Instead of fitting the model all at once we optimize the model iteratively. We start with intial prediction $\hat y_i^{(0)} = 0$ and at each step add a new tree to improve the model. The updated prediction after adding the $t$ th tree can be expressed as :
$$
\hat y_i^{(t)} = \hat y_i^{(t-1)} + f_t(x_i)
$$

* $\hat y_i^{(t-1)}$ is the prediction from the previous iteration 
* $f_t(x_i)$ is the prediction of the t th tree for the i th data point.

The regularization term simplify the complex tree by penalizing the number of leaves in the tree and the size of the leaf.  
$$
\Omega(f_t) = \gamma T + \frac{1}{2} \lambda\sum_{t = 1}^T w_j^2
$$

* T is the number of leaves in the tree
* $\gamma$  is a regularization parameter that control the complexity of the tree
* $\lambda$ is a parameter that penalize the squared weight of the leaves $w_j$

When deciding how to split the node in the tree we compute the information gain for every possible split. The info gain for a split is: 
$$
Gain = \frac{1}{2} [\frac{G^2_L}{H_L+\lambda} + \frac{G^2_R}{H_R+\lambda} - \frac{G^2_L +G^2_R}{H_L+H_R+\lambda}] - \gamma
$$
Where: 

* $G_L, G_R$ are the sums of gradients in the left and right child nodes.
* $H_L, H_R$ are the sums of the Hessians in the left and right child nodes.

By calculating  the information gain for every possible split at each node XGBoost  select the split that results in the largest gain which effectively reduces the errors and improve the model’s performance.



For example: 

\- Mean Square Loss：

 \- $g_i = \hat{y}_i - y_i $

 \- $h_i = 1$

\- Log Loss：

 $g_i = \sigma(\hat{y}_i) - y_i$， $h_i = \sigma(\hat{y}_i)(1-\sigma(\hat{y}_i))$



For each root node \(I_L, I_R\)：  

$$G_L = \sum_{i \in I_L} g_i, \quad H_L = \sum_{i \in I_L} h_i$$  

$$G_R = \sum_{i \in I_R} g_i, \quad H_R = \sum_{i \in I_R} h_i$$

## Advantages of XGBoost

XGBoost includes several features and characteristics that make it useful in many scenarios:

- Scalable for large datasets with millions of records.
- Supports parallel processing and GPU acceleration.
- Offers customizable parameters and regularization for fine-tuning.
- Includes feature importance analysis for better insights.
- Available across multiple programming languages and widely used by data scientists.

## Disadvantages of XGBoost

XGBoost also has certain aspects that require caution or consideration:

- Computationally intensive; may not be suitable for resource-limited systems.
- Sensitive to noise and outliers; careful preprocessing required.
- Can overfit, especially on small datasets or with too many trees.
- Limited interpretability compared to simpler models, which can be a concern in fields like healthcare or finance.



## Implementation

|                            |                                                              |
| :------------------------: | :----------------------------------------------------------: |
|       Learning Rate        | An important variable that modifies how much each tree contributes to the final prediction. While more trees are needed smaller values frequently result in more accurate models. |
|         Max Depth          | Controls the depth of every tree, avoiding overfitting and being essential to controlling the model's complexity. |
|           Gamma            | Based on the decrease in loss it determines when a node in the tree will split. The algorithm becomes more conservative with a higher gamma value hence avoiding splits that don't decreases the loss. It helps in managing tree complexity |
|    ***\*Subsample\****     | Manages the percentage of data that is sampled at random to grow each tree hence lowering variance and enhancing generalization. Setting it too low could result in underfitting |
| ***\*Colsample Bytree\**** | Establishes the percentage of features that will be sampled at random for growing each tree |
|   ***\*n_estimators\****   |           Specifies the number of boosting rounds            |
|  Alpha(L1) and Lambda(L2)  | Control the strength of L1 and L2 regularization respectively. A higher value results in stronger regularization |
| ***\*min_child_weight\**** | Influences the tree structure by controlling the minimum amount of data required to create a new node |
| ***\*scale_pos_weight\**** | Useful in imbalanced class scenarios to control the balance of positive and negative weights |

### Data Preparation and Train-Test Split

```python
from sklearn.metrics import accuracy_score
import xgboost as xgb
from sklearn.model_selection import train_test_split
import numpy as np
import matplotlib.pyplot as plt
import pandas as pd

#Load and Split the data
dataset = pd.read_csv('Churn_Modelling.csv')
X = dataset.iloc[:, 3:13]
y = dataset.iloc[:, 13].values

#Convert catagorical columns
X['Geography'] = X['Geography'].astype('category')
X['Gender'] = X['Gender'].astype('category')

# Train-Test Split
X_train, X_test, y_train, y_test = train_test_split(X,y, test_size=0.25, random_state=0)
```

### Converting Dataset into DMatrix 

Next,  we will convert the dataset into DMatrix. DMatrix is a special data structure  in XGBoost for faster training and less memory use. 

We use *`enable_catagorical = True`* to handle Pandas catagorical columns automatically.

```python
xgb_train = xgb.DMatrix(X_train, y_train, enable_categorical=True)
xgb_test = xgb.DMatrix(X_test, y_test, enable_categorical=True)
```

### Creating the XGBoost Model

Initialize XGBoost model with hyper parameters like binary logistic objective, maximum tree depth and learning rate. It then trains the model using the “xgb_train” dataset for 50 boosting rounds.

```python
params = {
    'objective': 'binary:logistic',
    'max_depth': 3,
    'learning_rate': 0.1,
}
n=50
model = xgb.train(params=params,dtrain=xgb_train,num_boost_round=n)
```

