---
title: ImageNet Classification with Deep Convolutional Neural Networks
date: 2018-09-12 16:06:00
tags: [Deep Learning, Image]
categories: Papers Notes
---

The authors of this paper trained one of the largest convolutional neural networks and achieved the best results (at that time) on ILSVRC-2010 and ILSVRC-2012 datasets.

Their final network:

>contains five convolutional and three fully-connected layers, and this depth seems to be important: *we found that removing any convolutional layer (each of which contains no more than 1% of the model's parameters) resulted in inferior performance.*



### 1. Features

**To improve its performance and reduce its training time, authors used some "new" and "unusual" features:**

1. **Use ReLU as activation function.**

>Deep convolutional neural networks with *ReLU*s train several times faster than their equivalents with *tanh* units.

![Figure.01 Training error rate with Epochs change.png](https://i.loli.net/2018/09/12/5b98d1b47ee14.png)

*A four-layer convolutional neural network with ReLUs (solid line) reaches a 25% training error rate on CIFAR-10 six times faster than an equivalent network with tanh neurons (dashed line). The learning rates for each network were chosen independently to make training as fast as possible.*



2. **Training on Multiple GPUs**

Since we can use TensorFlow, which support us to train our model on single machine with multiple CPUs/GPUs or multiple machine with multiple CPUs/GPUs, we have no need to focus on the implemention of authors.



3. **Local Response Normalization**

>**ReLUs have the desirable property that they do not require input normalization to prevent them from saturating.** If at least some training examples produce a positive input to a ReLU, learning will happen in that neuron. However, we still find that the following local normalization scheme aidsgeneralization.Denoting by $a^i_{x,y}$ the activity of a neuron computed by applying kernel *i* at position *(x, y)* and then applying the ReLU nonlinearity, the response-normalized activity $b^i_{x,y}$ is given by the expression :


$$
b_{x,y}^i = a_{x,y}^i / \lgroup k+\alpha \sum_{j=max(0, i-n/2)}^{min(N-1, i+n/2)}(a_{x,y}^j)^2\rgroup^\beta
$$


> where the sum runs over n “adjacent” kernel maps at the same spatial position, and N is the total number of kernels in the layer. The ordering of the kernel maps is of course arbitrary and determined before training begins. ... The constants k, n, α, and β are hyper-parameters whose values are determined using a validation set; we used k = 2, n = 5, α = 10−4, and β = 0.75. We applied this normalization after applying the ReLU nonlinearity in certain layers.
>
> Response normalization **reduces our top-1 and top-5 error rates by 1.4% and 1.2%**, respectively. We also verified the effectiveness of this scheme on the CIFAR-10 dataset: a four-layer CNN **achieved a 13% test error rate without normalization and 11% with normalization**.



4. **Overlapping Pooling**

>Traditionally, the neighborhoods summarized by adjacent pooling units do not overlap...This scheme reduces the top-1 and top-5 error rates by 0.4% and 0.3%, respectively, as compared with the non-overlapping scheme s = 2, z = 2, which produces output of equivalent dimensions. **We generally observe during training that models with overlapping pooling find it slightly more difficult to overfit.**



This is really weird, as authors said, we normally set stride equal to the size of our kernel. But, by overplapping, the model could perform better. I think, this trick may only be adopted in the deep networks.

Also, as far as we know from this paper, we don’t know how this trick affects performance, e.g. how much should we overlap?



5. **Overall Architecture**

>The net contains eight layers with weights; the first five are convolutional and the remaining three are fully-connected. The output of the last fully-connected layer is fed to a 1000-way softmax which produces a distribution over the 1000 class labels. 



### 2. Techniques

**Since this is one of the largest convolutional neural networks, which made overfitting a significant problem. To solve this, the authors used several effective techniques:**

1. **Data Augmentation**

It is well known that one of the most important measures to avoid overfitting is to expand the data set. We can use the *ImageDataGenerator* to augment the data in TensorFlow.

In this paper, the authors use:

>The first form of data augmentation consists of generating image translations and horizontal reflections. 
>
>The second form of data augmentation consists of altering the intensities of the RGB channels in training images.



2. **Dropout**

>The recently-introduced technique, called “dropout” [10], consists
>of setting to zero the output of each hidden neuron with probability 0.5. The neurons which are "dropped out" in this way do not contribute to the forward pass and do not participate in back-
>propagation. So every time an input is presented, the neural network samples a different architecture, but all these architectures share weights.

The significance of doing this is：

>This technique reduces complex co-adaptations of neurons, since a neuron cannot rely on the presence of particular other neurons. It is, therefore, forced to learn more robust features that are useful in conjunction with many different random subsets of the other neurons.

Also, for now, we can just import Dropout layer from keras to use this technique, like below:

```python
from keras.layers import Dropout
    x = input
    ...
    x = Dropout(0.3)(x)
    ...
    predictions = Dense(num_classes, activation='softmax')(x)
    model = Model(inputs=base_model.input, outputs=predictions)
```



### 3. Details of learning

> We trained our models using **stochastic gradient descent** with a batch size of 128 examples, momentum of 0.9, and weight decay of 0.0005. We found that this small amount of weight decay was important for the model to learn. In other words, weight decay here is not merely a regularizer: **it reduces the model’s training error**.
>
> The update rule for weight *w* was:

$$
v_{i+1} := 0.9\cdot v_i - 0.0005 \cdot \epsilon \cdot w_i - \epsilon \cdot \langle \frac{\partial L}{\partial w}\mid _{w_i}\rangle _{D_i}
\\
w_{i+1} := w_i + v_{i+1}
$$

>where $i$ is the iteration index, $v$ is the momentum variable, $\epsilon$ is the learning rate, and $ \langle \frac{\partial L}{\partial w}\mid _{w_i}\rangle _{D_i} $ is the average over the *i*th batch $D_i$ of the derivative of the objective with respect to $w$, evaluated at $w_i$. 

**Initialization :**

>We initialized the weights in each layer from a zero-mean Gaussian distribution with standard deviation 0.01. We initialized the neuron biases in the second, fourth, and fifth convolutional layers, as well as in the fully-connected hidden layers, with the constant 1.This initialization accelerates the early stages of learning by providing the ReLUs with positive inputs. We initialized the neuron biases in the remaining layers with the constant 0.

**Learning rate :**

>We used an equal learning rate for all layers, which we adjusted manually throughout training. The heuristic which we followed was to divide the learning rate by 10 when the validation error rate stopped improving with the current learning rate. The learning rate was initialized at 0.01 and reduced three times prior to termination.



### 4. Summarize

This paper lets us know some techniques to avoid overfitting. More important, the work of this paper shows that a large, deep convolutional neural network is capable of performing some extremely complex tasks.



-- Jaho