---
layout: post
title: Tree from scratch
tags: [bert, jekyll, blog, stocks]
image: '/images/posts/pexels-pineapple-supply-co-139259.jpg'
---



thoughts

* best split feature
* the math is entropy or uncertainty
* minus the sum of p of x times the log of p of x
* p of x is number of occurences over the total number of sample
* 1 is the worst 0 is the best case
* information gain is the entropy of the parent minus the weighted average of the child entropy

# Steps
1. Create entropy method with y as the input
2. Create a Node class
3. The init method with feature, threshold, left, right, value
4. Create a DecisionTree class
5. The init method with min_sample_split, max_depth, n_features
6. Set self.root to None
7. Create a fit method with X and y as the input
8. First thing to do is grow the tree
9. Create a grow_tree method with X and y and depth set to 0 as the input
10. Set n_sample and n_features from X.shape
11. Set n_labels
12. Find stopping criteria then find leaf value
13. To find leaf value create a most_common method with y as the input
14. The most common method takes a counter and takes the most_common method
15. Ok so back to leaf value then return it as a leaf Node
16. Ok so back to stopping criteria if we don't meet it then we need to split
17. Create a list of the indices for the features. Randomize it.
18. Create a greedy search method called best_criteria with X and y and list of indices
19. So this method will create the IG so first create best_gain as - inf or -1
20. Set best_feature and best_threshold to None and None
21. Loop through each feature
22. ok for each feature first create a 1 column vector and list of unique values (thresholds)
23. then loop through each threshold
24. for each threshold calculate the IG so let's create an IG method
25. Create an IG method with X vector and y and threshold as the input
26. First compute the parent entropy
27. Find the left and right lists so create a split method with X vector and threshold as input
28. Edge case if either are 0 then return IG of 0
29. Find weighted average so get length and length of left and right
30. Then get child entropy of left and right lists
31. Calculate IG of parent - child
32. ok so back to calculate the IG, so compare this IG to the best and if it
33. then update the best_gain, best feature and best threshold
34. ok by now we looped through each threshold and each feature
35. ok back to the end of greedy search, let's create left and right split
36. left and right is split method of X on the best feature and best threshold
37. So now let's traverse left
38. left is the recursive call of grow tree with X of left and y of left and depth + 1
39. then traverse right
40. right is the recursive call of grow tree with X of right and y of right and depht + 1
41. now return Node of best feature best threshold left and right

# Approach

Training the algorithm := Build the tree

1. Start at the top node and at each node select the best split based on the best information gain
2. Greedy search: Loop over all features and over all thresholds (all possible values)
3. Save the best feature split and split threshold at each node.
4. Build the tree recursively.
5. Apply some stopping criteria to stop growing.
6. When we have a leaf node, store the most common class label of this node.


Predict:= Traverse the tree

1. Traverse the tree recursively.
2. At each node, look at the best split feature of the test feature and go left or right
3. When we reach the leaf node we return the stored class label


```
import numpy as np
from collections import Counter

def entropy(y):
  hist = np.bincount(y) #histogram
  ps = hist / len(y)
  entropy  = -np.sum([p * np.log2(p) for p in ps if p > 0])
  return entropy

class Node:
  def __init__(self, feature=None, threshold=None, left=None, right=None, *, value=None):
    self.feature=feature
    self.threshold=threshold
    self.left=left
    self.right=right
    self.value = value

  def is_leaf_node(self):
    return self.value is not None:


class DecisionTree:
  def __init__(self, min_sample_split=2, max_depth = 100, n_feats= None):
    self.min_sample_split = min_sample_split
    self.max_depth = max_depth
    self.n_feats = n_feats
    self.root = None

  def fit(self, X, y):
    #grow tree
    self.n_feats = X.shape[1] if not self.n_feats else min(self.n_feats, X.shape[1])
    self.root = self._grow_tree(X, y)

  def _grow_tree(self, X, y, depth=0):
    n_samples, n_features = X.shape
    n_labels = len(np.unique(y))

    ### stopping criteria
    if (depth >= self.max_depth) or n_labels ==1 or n_samples < self.min_sample_split):
      leaf_value = self._most_common_label(y)
      return Node(value=leaf_value)

    feat_idxs = np.random.choice(n_features, self.n_feats, replace=False)

    # greedy search
    best_feat, best_thresh = self._best_criteria(X,y, feat_idxs)
    left_idxs, right_idxs = self._split(X[:, best_feat], best_thresh)

    left = self._grow_tree(X[left_idxs, :], y[left_idxs], depth+1)
    right = self._grow_tree(X[right_idxs, :], y[right_idxs], depth+1)
    return Node(best_feat, best_thresh, left, right)

  def _best_criteria(self, X,y, feat_idxs):
    best_gain = -1
    split_index, split_threshold = None, None
    for feat_index in feat_indxs:
      X_column = X[:, feat_idx]
      thresholds = np.unique(X_column)
      for threshold in thresholds:
        gain = self._information_gain(y, X_column, threshold)

        if gain > best_gain:
          best_gain = gain
          split_index = feat_idx
          split_threshold = threshold
    return split_index, split_threshold

  def _information_gain(self, y, X, threshold):
    # parent entropy
    parent_entropy = entropy(y)
    # generate a split
    left_index, right_index = self._split(X, split_threshold)

    if len(left_index) == 0 or len(right_index) == 0:
      return 0

    ## weighted average of children entropy
    n = len(y)
    n_left = len(left_index)
    n_right = len(right_index)

    left_entropy, right_entropy = entropy(y[left_index]), entropy(y[right_index])
    child_entropy = (n_left/n)* left_entropy + (n_right/n)*right_entropy
    ## return information gain

    ig = parent_entropy - child_entropy
    return ig

  def _split(self, X, split_threshold):
    left_idxs = np.argwhere(X <= split_threshold).flatten()
    right_idxs = np.argwhere( X > split_threshold).flatten()
    return left_idxs,right_idxs


  def _most_common_label(self, y):
    counter = Counter(y)
    most_common = counter.most_common(1)[0][0]
    return most_common


  def predict(self, X):
    #traverse tree

    return np.array([self._traverse_tree(x) for x in X], self.root)

  def _traverse_tree(self, x, node):
    if node.is_leaf_node():
      return node.value

    if x[node.feat_idx]  <= node.threshold:
      return self._traverse_tree(x, node.left)
    return self._traverse(x, node.right)
```

So now that I built 1 DecisionTree. Now let's a collection of trees
to build Random Forest.

Combine multiple trees. Each tree gets a random subset of the data.

Avoids overfitting.


# Steps

1. Import DecisionTree class
2. Create a RandomForest class and
3. The init method has number of trees, same features as decision tree so min_sample_split, max_depth, and number of features
4. Also set self.tree to an empty list
5. Create a fit method with X and y
6. Loop through range of number of trees
7. Create a DecisionTree instance and set the parameters to self
8. Then let's sample the data so create a method called bootstrap
9. The bootstrap method takes in X and y as the input and
10. we can use np.random.choice of 0 to n_samples and size is n_samples and replace is True
11. Then we return the X_sample and y_sample
12. ok so we then fit the sample using the fit method
13. Then append the tree to the self.trees variable
14. Now that's fit. Let's look at predict
15. Create a predict method with X as the input
16. loop through self.trees and call the predict method of each DecisionTree and put that into an np array
17. Then we need to swap the predictions so use np.swapaxes
18. Then for each set of trees call the most_common_labels method and pick a winner
19. Return the array of winning predictions
```
import numpy as np
from collection import Counter
from decision_tree import DecisionTree

def bootstrap_sample(X, y):
  n_samples = X.shape[0]
  idxs = np.random.choice(n_samples, size=n_samples, replace=True)# make a random choice between 0 to n_samples and size is n_samples
  return X[idxs], y[idxs]

def most_common_label(y):
  counter = Counter(y)
  most_common = counter.most_common(1)[0][0]
  return most_common
class RandomForest:
  def __init__(self, n_trees=100, min_sample_split=2, max_depth = 100, n_feats=None):
    self.n_trees = n_trees
    self.min_sample_split = min_sample_split
    self.max_depth = max_depth
    self.n_feats = n_feats
    self.trees = []


  def fit(self, X, y):
    self.trees = []
    for _ in range(self.n_trees):
      tree = DecisionTree(min_sample_split=self.min_sample_split, max_depth=self.max_depth, n_feats=self.n_feats)
      X_sample, y_sample = boostrap_sample(X,y)
      tree.fit(X_sample, y_sample)
      self.trees.append(tree)
  def predict(self, X):
    tree_predictions = np.array([tree.predict(X) for tree in self.trees])
    # [111 000 111]
    # [101 101 101]
    tree_predictions = np.swapaxes(tree_predictions, 0, 1)
    y_predictions = [most_common_label(tree_prediction) for tree_prediction in tree_predictions]
    return np.array(y_predictions)

```