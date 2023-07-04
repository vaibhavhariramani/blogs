---
title: "Essentials of the Machine Learning"
excerpt: "Essentials of the Machine Learning algorithm in Python"
header:
  image: /assets/images/machine_learning/machine-learning.jpg
  teaser: /assets/images/machine_learning/t1.jpg
sidebar:
  - title: "About the Author"
    image: https://vaibhavhariramani.github.io/blogs/images/bio-pic-2.jpg
    image_alt: "logo"
    text: "I am a Machine Learning enthausiast by profession and a Blogger by passion. I have been working on machine learning projects for more than 2 years."
  - title: "Other Blogs"
    text: " [Here](https://vaibhavhariramani.github.io/blogs/) you will find articles on “Machine Learning, Statistics, Deep Learning, NLP and Artificial Intelligence”."
gallery:
  - url: /assets/images/unsplash-gallery-image-1.jpg
    image_path: assets/images/unsplash-gallery-image-1-th.jpg
    alt: "placeholder image 1"
  - url: /assets/images/unsplash-gallery-image-2.jpg
    image_path: assets/images/unsplash-gallery-image-2-th.jpg
    alt: "placeholder image 2"
  - url: /assets/images/unsplash-gallery-image-3.jpg
    image_path: assets/images/unsplash-gallery-image-3-th.jpg
    alt: "placeholder image 3"
---

This article introduces the basics of machine learning theory, laying down the common concepts and techniques involved. This post is intended for the people starting with machine learning, making it easy to follow the core concepts and get comfortable with machine learning basics.

## What is Machine Learning?
In 1959, **Arthur Samuel**, a computer scientist who pioneered the study of artificial intelligence, described machine learning as “the study that gives computers the ability to learn without being explicitly programmed.”

Alan Turing’s seminal paper (Turing, 1950) introduced a benchmark standard for demonstrating machine intelligence, such that a machine has to be intelligent and responsive in a manner that cannot be differentiated from that of a human being.

Machine Learning is an application of artificial intelligence where a computer/machine learns from the past experiences (input data) and makes future predictions. The performance of such a system should be at least human level.

<img src="/assets/images/machine_learning/machineLearning3.jpg">

A more technical definition given by Tom M. Mitchell’s (1997) : “A computer program is said to learn from experience E with respect to some class of tasks T and performance measure P, if its performance at tasks in T, as measured by P, improves with experience E.” Example:

```python
    A handwriting recognition learning problem:

Task T: recognizing and classifying handwritten words within 
images

Performance measure P: percent of words correctly classified, 
accuracy

Training experience E: a data-set of handwritten words with
 given classifications
```

In order to perform the task T, the system learns from the data-set provided. A data-set is a collection of many examples. An example is a collection of features.

# Machine Learning Categories
Machine Learning is generally categorized into three types: **Supervised Learning**, **Unsupervised Learning**, **Reinforcement learning**

## Supervised Learning:
In supervised learning the machine experiences the examples along with the labels or targets for each example. The labels in the data help the algorithm to correlate the features.

<img src="/assets/images/machine_learning/supervised-machine-learning.jpg">

Two of the most common supervised machine learning tasks are **classification** and **regression**.



In **classification** problems the machine must learn to predict discrete values. That is, the machine must predict the most probable category, class, or label for new examples. Applications of classification include predicting whether a 
stock's price will rise or fall, or deciding if a news article belongs to the politics or leisure section.



In **regression** problems the machine must predict the value of a continuous response variable. Examples of regression problems include predicting the sales for a new product, or the salary for a job based on its description.

<img src="/assets/images/machine_learning/regression-vs-classification-in-machine-learning.jpg">

## Unsupervised Learning:
When we have unclassified and unlabeled data, the system attempts to uncover patterns from the data . There is no label or target given for the examples. One common task is to group similar examples together called clustering.
<img src="/assets/images/machine_learning/clustering.jpg">

## Reinforcement Learning:

Reinforcement learning refers to goal-oriented algorithms, which learn how to attain a complex objective (goal) or maximize along a particular dimension over many steps. This method allows machines and software agents to automatically determine the ideal behavior within a specific context in order to maximize its performance. Simple reward feedback is required for the agent to learn which action is best; this is known as the reinforcement signal. For example, maximize the points won in a game over many moves.

## Techniques of Supervised Machine Learning

Regression is a technique used to predict the value of a response (dependent) variables, from one or more predictor (independent) variables.

Most commonly used regressions techniques are: Linear Regression and Logistic Regression. We will discuss the theory behind these two prominent techniques alongside explaining many other key concepts like Gradient-descent algorithm, Over-fit/Under-fit, Error analysis, Regularization, Hyper-parameters, Cross-validation techniques involved in machine learning.

## Linear Regression
In linear regression problems, the goal is to predict a real-value variable y from a given pattern X. In the case of linear regression the output is a linear function of the input. Letŷ be the output our model predicts: ŷ = WX+b

Here X is a vector (features of an example), W are the weights (vector of parameters) that determine how each feature affects the prediction andb is bias term. So our task T is to predict y from X, now we need to measure performance P to know how well the model performs.

Now to calculate the performance of the model, we first calculate the error of each example i as:

<img src="/assets/images/machine_learning/eqn1.png">

we take the absolute value of the error to take into account both positive and negative values of error.

Finally we calculate the mean for all recorded absolute errors (Average sum of all absolute errors).

**Mean Absolute Error (MAE)** = Average of All absolute errors

<img src="/assets/images/machine_learning/eqn2.png">

More popular way of measuring model performance is using

**Mean Squared Error (MSE)**: Average of squared differences between prediction and actual observation.

<img src="/assets/images/machine_learning/eqn3.png">

The mean is halved (1/2) as a convenience for the computation of the gradient descent [discussed later], as the derivative term of the square function will cancel out the 1/2 term. For more discussion on the MAE vs MSE please refer [1] & [2].

'''
The main aim of training the ML algorithm is to adjust the weights W to reduce the MAE or MSE.
'''

To minimize the error, the model while experiencing the examples of the training set, updates the model parameters W. These error calculations when plotted against the W is also called **cost function J(w)**, since it determines the cost/penalty of the model. So minimizing the error is also called as *minimization the cost function J*.

## Gradient descent Algorithm:

When we plot the cost function J(w) vs w. It is represented as below:
<img src="/assets/images/machine_learning/eqn4.png">

As we see from the curve, there exists a value of parameters W which has the minimum cost Jmin. Now we need to find a way to reach this minimum cost.

In the gradient descent algorithm, we start with random model parameters and calculate the error for each learning iteration, keep updating the model parameters to move closer to the values that results in minimum cost.

repeat until minimum cost: {
         <img src="/assets/images/machine_learning/eqn5.png">
  }

In the above equation we are updating the model parameters after each iteration. The second term of the equation calculates the slope or gradient of the curve at each iteration.

The gradient of the cost function is calculated as partial derivative of cost function J with respect to each model parameter wj, j takes value of number of features [1 to n]. α, alpha, is the learning rate, or how quickly we want to move towards the minimum. If α is too large, we can overshoot. If α is too small, means small steps of learning hence the overall time taken by the model to observe all examples will be more.

There are three ways of doing gradient descent:

**Batch gradient descent**: Uses all of the training instances to update the model parameters in each iteration.

**Mini-batch Gradient Descent**: Instead of using all examples, Mini-batch Gradient Descent divides the training set into smaller size called batch denoted by ‘b’. Thus a mini-batch ‘b’ is used to update the model parameters in each iteration.

**Stochastic Gradient Descent (SGD)**: updates the parameters using only a single training instance in each iteration. The training instance is usually selected randomly. Stochastic gradient descent is often preferred to optimize cost functions when there are hundreds of thousands of training instances or more, as it will converge more quickly than batch gradient descent .

## Logistic Regression
In some problems the response variable is not normally distributed. For instance, a coin toss can result in two outcomes: heads or tails. The Bernoulli distribution describes the probability distribution of a random variable that can take the positive case with probability P or the negative case with probability 1-P. If the response variable represents a probability, it must be constrained to the range {0,1}.

In logistic regression, the response variable describes the probability that the outcome is the positive case. If the response variable is equal to or exceeds a discrimination threshold, the positive class is predicted; otherwise, the negative class is predicted.

The response variable is modeled as a function of a linear combination of the input variables using the logistic function.

Since our hypotheses ŷ has to satisfy 0 ≤ ŷ ≤ 1, this can be accomplished by plugging logistic function or “Sigmoid Function”.

<img src="/assets/images/machine_learning/eqn6.png">

The function g(z) maps any real number to the (0, 1) interval, making it useful for transforming an arbitrary-valued function into a function better suited for classification. The following is a plot of the value of the sigmoid function for the range {-6,6}:

<img src="/assets/images/machine_learning/eqn7.png">

Now coming back to our logistic regression problem, Let us assume that z is a linear function of a single explanatory variable x. We can then express z as follows: <img src="/assets/images/machine_learning/eqn8.png">

And the logistic function can now be written as: <img src="/assets/images/machine_learning/eqn9.png">

Note that *g(x)* is interpreted as the probability of the dependent variable.

*g(x)* = 0.7, gives us a probability of 70% that our output is 1. Our probability that our prediction is 0 is just the complement of our probability that it is 1 (e.g. if probability that it is 1 is 70%, then the probability that it is 0 is 30%).

## Cost Function
We cannot use the same cost function that we used for linear regression because the Sigmoid Function will cause the output to be wavy, causing many local optima. In other words, it will not be a convex function.

<img src="/assets/images/machine_learning/eqn10.png">

'''
Non-convex cost function
'''
In order to ensure the cost function is convex (and therefore ensure convergence to the global minimum), the cost function is transformed using the logarithm of the sigmoid function. The cost function for logistic regression looks like:
<img src="/assets/images/machine_learning/eqn11.png">
<img src="/assets/images/machine_learning/eqn12.png">
Which can be written as:
<img src="/assets/images/machine_learning/eqn13.png">
<img src="/assets/images/machine_learning/eqn14.png">
So the cost function for logistic regression is:
<img src="/assets/images/machine_learning/eqn15.png">
Since the cost function is a convex function, we can run the gradient descent algorithm to find the minimum cost.

## Under-fitting & Over-fitting
We try to make the machine learning algorithm fit the input data by increasing or decreasing the models capacity. In linear regression problems, we increase or decrease the degree of the polynomials.

Consider the problem of predicting y from x ∈ R. The leftmost figure below shows the result of fitting a line to a data-set. Since the data doesn’t lie in a straight line, so fit is not very good (left side figure).

<img src="/assets/images/machine_learning/eqn16.png">

To increase model capacity, we add another feature by adding term x² to it. This produces a better fit ( middle figure). But if we keep on doing so ( x⁵, 5th order polynomial, figure on the right side), we may be able to better fit the data but will not generalize well for new data. The first figure represents under-fitting and the last figure represents over-fitting.

### Under-fitting:
When the model has fewer features and hence not able to learn from the data very well. This model has high bias.

#### Over-fitting:
When the model has complex functions and hence able to fit the data very well but is not able to generalize to predict new data. This model has high variance.

There are three main options to address the issue of over-fitting:

1. **Reduce the number of features**: Manually select which features to keep. Doing so, we may miss some important information, if we throw away some features.

2. **Regularization**: Keep all the features, but reduce the magnitude of weights W. Regularization works well when we have a lot of slightly useful feature.

3. **Early stopping**: When we are training a learning algorithm iteratively such as using gradient descent, we can measure how well each iteration of the model performs. Up to a certain number of iterations, each iteration improves the model. After that point, however, the model’s ability to generalize can weaken as it begins to over-fit the training data.

<img src="/assets/images/machine_learning/graph1.jpg">

## Regularization
**Regularization** can be applied to both linear and logistic regression by adding a penalty term to the error function in order to discourage the coefficients or weights from reaching large values.

#### Linear Regression with Regularization
The simplest such penalty term takes the form of a sum of squares of all of the coefficients, leading to a modified linear regression error function:
<img src="/assets/images/machine_learning/eqn17.png">

where lambda is our regularization parameter.

Now in order to minimize the error, we use gradient descent algorithm. We keep updating the model parameters to move closer to the values that results in minimum cost.

repeat until convergence ( with regularization): {

<img src="/assets/images/machine_learning/eqn18.png">
<img src="/assets/images/machine_learning/eqn19.png">  

}

With some manipulation the above equation can also be represented as:
<img src="/assets/images/machine_learning/eqn20.png">  
The first term in the above equation,<img src="/assets/images/machine_learning/eqn21.png"> will always be less than 1. Intuitively you can see it as reducing the value of the coefficient by some amount on every update.

## Logistic Regression with Regularization
The cost function of the logistic regression with Regularization is:
<img src="/assets/images/machine_learning/eqn22.png"> 
repeat until convergence ( with regularization): {
  <img src="/assets/images/machine_learning/eqn23.png"> 
  <img src="/assets/images/machine_learning/eqn24.png"> 
}

## L1 and L2 Regularization
The regularization term used in the previous equations is called L2 or Ridge regularization.

<img src="/assets/images/machine_learning/eqn25.png"> The L2 penalty aims to minimize the squared magnitude of the weights.

There is another regularization called **L1 or Lasso**:
The L1 penalty aims to minimize the absolute value of the weights

**Difference between L1 and L2**

L2 shrinks all the coefficient by the same proportions but eliminates none, while L1 can shrink some coefficients to zero, thus performing feature selection. For more details read this.

#### Hyper-parameters
Hyper-parameters are “higher-level” parameters that describe structural information about a model that must be decided before fitting model parameters, examples of hyper-parameters we discussed so far:

Learning rate *alpha* , *Regularization lambda*.

#### Cross-Validation
The process to select the optimal values of hyper-parameters is called model selection. if we reuse the same test data-set over and over again during model selection, it will become part of our training data and thus the model will be more likely to over fit.

The overall data set is divided into:

1. the training data set

2. validation data set

3. test data set.

The training set is used to fit the different models, and the performance on the validation set is then used for the model selection. The advantage of keeping a test set that the model hasn’t seen before during the training and model selection steps is that we avoid over-fitting the model and the model is able to better generalize to unseen data.

In many applications, however, the supply of data for training and testing will be limited, and in order to build good models, we wish to use as much of the available data as possible for training. However, if the validation set is small, it will give a relatively noisy estimate of predictive performance. One solution to this dilemma is to use cross-validation, which is illustrated in Figure below.

<img src="/assets/images/machine_learning/eqn26.png">

Below Cross-validation steps are taken from here, adding here for completeness.

### Cross-Validation Step-by-Step:
These are the steps for selecting hyper-parameters using K-fold cross-validation:

1. Split your training data into K = 4 equal parts, or “folds.”

2. Choose a set of hyper-parameters, you wish to optimize.

3. Train your model with that set of hyper-parameters on the first 3 folds.

4. Evaluate it on the 4th fold, or the”hold-out” fold.

5. Repeat steps (3) and (4) K (4) times with the same set of hyper-parameters, each time holding out a different fold.

6. Aggregate the performance across all 4 folds. This is your performance metric for the set of hyper-parameters.

7. Repeat steps (2) to (6) for all sets of hyper-parameters you wish to consider.

Cross-validation allows us to tune hyper-parameters with only our training set. This allows us to keep the test set as a truly unseen data-set for selecting final model.



## Conclusion
We’ve covered some of the key concepts in the field of Machine Learning, starting with the definition of machine learning and then covering different types of machine learning techniques. We discussed the theory behind the most common regression techniques (Linear and Logistic) alongside discussed other key concepts of machine learning.

Thanks for reading.

# Made with ❤️by Vaibhav Hariramani

*   *If you enjoyed this,* [*follow me on Medium*](https://medium.com/@vaibhavji) *for more*
*   *Follow me on* [*Kaggle*](https://www.kaggle.com/vaibhavhariramani) *for more content!*
*   *Let’s connect on* [*LinkedIn*](https://www.linkedin.com/in/vaibhav-hariramani-087488186/)
*   [*Interested in collaborating?*](https://api.whatsapp.com/send?phone=+917790991077&text=Hi,%20I%20contacted%20you%20Through%20your%20website.) 
*   *Check out my* [*website*](https://vaibhavhariramani.github.io/)*.*
*   *Check out my* [*website*](https://sites.google.com/view/geeky-traveller/home)*.*

### Don't forget to tag us

if you use this repo in  your project don't forget to mention us as Contributer in it . And Don't forget to tag us [Linkedin](https://www.linkedin.com/in/vaibhav-hariramani-087488186/),[ instagram](https://www.instagram.com/geeky_baba_/?hl=en),[ facebook](https://www.facebook.com/jayesh.hariramani.3) ,[ twitter](https://www.linkedin.com/in/vaibhav-hariramani-087488186/), [ Github](https://github.com/vaibhavhariramani) 


# More Resources 

To learn more about these Resources you can Refer to some of these articles written by Me:-

- [Medium](https://medium.com/geeky-bawa)
- [geeky Traveller](https://sites.google.com/view/geeky-traveller/)
- [Blogs](https://github.com/vaibhavhariramani/blogs)
- [Youtube](https://www.youtube.com/channel/UCy7amUpLnsRLEMIaJGGBYog)[![Youtube Badge](https://img.shields.io/badge/-Geeky_Bawa-1ca0f1?style=flat-circle&labelColor=d54b3d&logo=youtube&logoColor=white&link=https://www.youtube.com/channel/UCy7amUpLnsRLEMIaJGGBYog)](https://www.youtube.com/channel/UCy7amUpLnsRLEMIaJGGBYog)
- Email: [vaibhav.hariramani01@gmail.com](mailto:vaibhav.hariramani01@gmail.com)


# Download [THE VAIBHAV HARIRAMANI APP](https://play.google.com/store/apps/details?id=com.geeky.developer)

# [![image](/images/gh-bannner-light.jpg)](https://play.google.com/store/apps/details?id=com.geeky.developer) 


[<img width="95" align='center' src="https://archive.org/download/download-button-png/download-button-png.png">The Vaibhav Hariramani App (Latest Version) ](https://play.google.com/store/apps/details?id=com.geeky.developer)

Download [THE VAIBHAV HARIRAMANI APP](https://play.google.com/store/apps/details?id=com.geeky.developer) consist of Tutorials,Projects,Blogs and Vlogs of our Site developed Using Android Studio with Web View try installing it in your android device.

### Follow me
  
[![Linkedin Badge](https://img.shields.io/badge/-VaibhavHariramani-blue?style=flat-circle&logo=Linkedin&logoColor=white&link=https://www.linkedin.com/in/vaibhav-hariramani-087488186/)](https://www.linkedin.com/in/vaibhav-hariramani-087488186/) [![Instagram Badge](https://img.shields.io/badge/-VaibhavHariramani-e02c73?style=flat-circle&labelColor=e02c73&logo=Instagram&logoColor=white&link=https://www.instagram.com/vaibhav.hariramani/?hl=en)](https://www.instagram.com/vaibhav.hariramani/?hl=en) [![Twitter Badge](https://img.shields.io/badge/-VaibhavHariramani-1ca0f1?style=flat-circle&labelColor=1ca0f1&logo=twitter&logoColor=white&link=https://twitter.com/vaibhavhariram2)](https://twitter.com/vaibhavhariram2) [![GitHub Badge](https://img.shields.io/badge/-@vaibhavhariramani-24292e?style=flat-circle&labelColor=24292e&logo=github&logoColor=white&link=https://github.com/vaibhavhariramani)](https://github.com/vaibhavhariramani) [![Gmail Badge](https://img.shields.io/badge/-VaibhavHariramani-d54b3d?style=flat-circle&labelColor=d54b3d&logo=gmail&logoColor=white&link=mailto:vaibhav.hariramani01@gmail.com)](mailto:vaibhav.hariramani01@gmail.com) [![Medium Badge](https://img.shields.io/badge/-VaibhavHariramani-d54b3d?style=flat-circle&labelColor=d54b3d&logo=medium&logoColor=white&link=https://medium.com/geeky-bawa)](https://medium.com/geeky-bawa) 

Happy coding ❤️ .
  
