---
author: ['Seah Shao Xuan']
title: 'Cross Entropy Loss'
date: 2024-06-25
draft: true
cover: 
    image: "images/"
description: "A Deeper Look into What the Loss Function Entails"
summary: "A Deeper Look into What the Loss Function Entails"
tags: ["loss functions", "cross entropy", "entropy", "classification", "kullback-leibler divergence"]
ShowToc: true
TocOpen: false
---

## But Why Do We Use Cross Entropy Loss?

In the realm of machine learning, the cross entropy loss is undoubtely one of the most popular loss functions when training a classification model.

$$ L_{\text{Cross-Entropy}}(y, \hat{y}) = - \sum_{k \in \mathcal{K}} y(k) \cdot \log \hat{y}(k) $$

If the true label of a data point is $c$, then $y_c = 1$ and all other $y_i = 0$, leading to the following simplification.

$$ L_{\text{Cross-Entropy}}(y, \hat{y}) = - \log \hat{y}(c) $$

Most courses or textbooks just assume this form when applying a loss function to a classification model. But isn't this a little strange? Why specifically this product of a class label and the log predictions?

## The Etymology of Entropy

The concept of entropy was borrowed from statistical physics, where the entropy of a system refers to the *disorder* of a system.

In probability and statistics, the most common form of entropy used is known as the Shannon entropy, which describes the disorder of a distribution at hand. 

Formally, given a distribution $p(x)$, the entropy of this distribution is:

$$ H(p) = - \int_x p(x) \log p(x) dx $$

In discrete distributions, an equivalent definition:

$$ H(p) = - \sum_x p(x) \log p(x) $$

A completely orderly distribution would have (*almost*) all of its probability centered around one point, and its entropy at zero. 

> Quick mathematical check: $H(p_{\text{Point Mass}}) = - 1.0 \cdot \log 1.0 = 0$.

A distribution with higher spread would therefore have entropies upwards of zero.

Notice the similarity with this form and the form of the cross entropy loss. Instead of using the same distribution $p$, we replace one distribution with the predicted labels instead. Why is that so?

## The Origins of Cross Entropy

The cross entropy loss is actually a result of applying the maximum likelihood estimator to a classification problem.

The likelihood function of a parameterised statistical model, denoted $\mathcal{L}(\theta; x)$, represents how likely the true parameters of the model are indeed $\theta$, given some observed data $x$.

Mathematically speaking, the likelihood is equivalent to the probability distribution of the data:

$$ \mathcal{L}(\theta; x) = p(x | \theta)$$

If we have a dataset $x$ that consists of $N$ independent data points, then the joint likelihood is a simple product of the individual likelihoods.

$$ \mathcal{L}(\theta; x_1,...,x_N) = \prod_{i=1}^N p(x_i | \theta)$$

## The Kullback Leibler Divergence

