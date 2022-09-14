---
layout: post
title: Perceptron from Scratch
tags: [bert, jekyll, blog, stocks]
image: '/images/posts/pexels-pineapple-supply-co-139259.jpg'
---

The perceptron is a single neuron.

We sum up the inputs and pass that through a transfer function.

### Stochastic gradient descent

The weights are updated using stochastic gradient descent



# Training

Let's start with building a function to train the weight estimates.

Stochastic gradient requires two parameters:
1. Learning rate - used to limit the amount each weight is corrected
2. Epochs - number of times to run through the training data

The general framework is as follows.
1. Loop over each epoch
2. Loop over each row in the training data
3. Loop over each weight and update it


```
class Perceptron(object):
    def __init__(self, learning_rate=0.01, epochs=1000):
        self.lr = learning_rate
        self.epochs = epochs
        self.weights = None
        self.bias = None
        self.activation_func = self._unit_step_func

    def _unit_step_func(self, x):
        return np.where(x>=0, 1, 0)

    def fit(self,X, y):
        n_samples, n_features = X.shape

        #initialize parameters
        self.weights = np.zeros(n_features)
        self.bias = 0

        y_ = np.array([1 if i > 0 else 0 for i in y])

        for _ in range(self.epochs): #iterate through each epoch
            for i, x_i in enumerate(X):
                linear_output = np.dot(x_i, self.weights) + self.bias
                y_predicted = self.activation_func(linear_output)

                #update perceptron rule
                update = self.lr * (y_[i] - y_predicted)
                self.weights += update * x_i
                self.bias += update
        return self.weights

    def predict(self, X):
        linear_output = np.dot(X, self.weights) + self.bias
        y_predicted = self.activation_func(linear_output)
        return y_predicted
```


```
dataset = [[2.7810836,2.550537003,0],
    [1.465489372,2.362125076,0],
    [3.396561688,4.400293529,0],
    [1.38807019,1.850220317,0],
    [3.06407232,3.005305973,0],
    [7.627531214,2.759262235,1],
    [5.332441248,2.088626775,1],
    [6.922596716,1.77106367,1],
    [8.675418651,-0.242068655,1],
    [7.673756466,3.508563011,1]]
y = [x[-1] for x in dataset]
X = np.array([x[:-1] for x in dataset])
l_rate = 0.1
n_epoch = 5
p = Perceptron(learning_rate=l_rate, epochs=n_epoch)
weights = p.fit(X,y)
print(weights)
```


learning rate is between 0 and 1. Larger values makes the weight
change more volatile.

y = denotes the output from the perceptron for an input vector
let's call it z

D = training set of s samples. D is (x,y).
x of j and i is the ith feature of the jth training input vector.

x of j and 0 is 1 which is the bias.


Weights is ith value in the weight vector to be multiplied
by the value of the ith input feature.

Because x of j and 0
