---
title: Deep Residual Learning for Image Recognition
date: 2018-09-13 10:02:36
tags: [Deep Learning]
categories: Papers Notes
---

## Deep Residual Learning for Image Recognition

Actually, this note includes two papers:

\[1] [Kaiming He, Xiangyu Zhang, Shaoqing Ren, Jian Sun; Deep Residual Learning for Image Recognition, CVPR, 2016.](https://arxiv.org/abs/1512.03385) 

\[2] [He K., Zhang X., Ren S., Sun J; Identity Mappings in Deep Residual Networks.](https://arxiv.org/abs/1603.05027) 



### 0. Prologue

In deep learning, things seem "easy": deeper means more powerful. If you need a stronger neural network, you just need to make it deeper and deeper.

**However, things are not always as we expected.**

Due to the deeper structure, the neural network is difficult to train and converge to a satisfactory error rate. Overfitting becomes a very difficult-to-handle problem. I attribute this phenomenon to the power of deeper neural networks, which means that it is not because of lack of power, but because it is too powerful. With more and more convolutional layers, the feature maps will be more and more abstract, and a little bit of error in previous layers may lead to great errors in subsequent layers, which cause overfitting.

In [1], authors came up with a very 'beautiful' solution - make every layer be able to receive the signals from all of the previous layers by using residual shortcut, this is ResNet - Residual Neural Networks. (By contrast, conventional CNN model is a 'linear' logic model, i.e. a layer could only receive the signal from the single previous neighboring layer.)  Actually, ResNet is a parallel network of numerous sub-networks. I will explain this at **4.Analysis**.

### 1. Introduction

First, the author did an experiment to explore the relationship between the above problem and the depth of the network.

>Let us consider a shallower architecture and its deeper counterpart that adds more layers onto it. There exists a solution by construction to the deeper model: the added layers are identity mapping, and the other layers are copied from the learned shallower model. The existence of this constructed solution indicates that **a deeper model should produce no higher training error than its shallower counterpart**. 

This means that the problem is not caused by depth, but other factors, we may need to modify the way we build the model. **The solution is to use this:**



![](https://i.loli.net/2018/09/26/5bab2dc464c5d.png)



<center>Fig 1. Residual learning: a building block</center>
>In-stead of hoping each few stacked layers directly fit a desired underlying mapping, we explicitly let these layers fit a residual mapping. Formally, denoting the desired underlying mapping as $H(x)$, we let the stacked nonlinear layers fit another mapping of $F (x) := H(x) − x.$ The original mapping is recast into $ F(x)+x$. We hypothesize that it is easier to optimize the residual mapping than to optimize the original, unreferenced mapping. To the extreme, if an identity mapping were optimal, it would be easier to push the residual to zero than to fit an identity mapping by a stack of nonlinear layers.
>
>-- [1]

So, how we realize this formulation? We use shortcut connections(Fig 1).

>Shortcut connections are those skipping one or more layers. In our case, the shortcut connections simply perform identity mapping, and their outputs are added to the outputs of the stacked layers.
>
>-- [1]

But, with respect to implementation, there is so much details to be sure. Where should we place Batch Normalization and ReLU? What size and number of kernel should we use in the convolutional layer? ...

### 2. Prev-activation or Post-activation?

**In [2], the author experimented with the implementation of several different shortcuts, as follows:**

![](https://i.loli.net/2018/09/26/5bab327390b7e.png)

<center>Fig 2. Variants of residual blocks </center>
In [1], The implementation adopted by the author is as (a). After this experiment, authors concluded that **it is always recommended to use full pre-activation.**

### 3. About kernel and unit

First, let's take a look at the architectures that the author uses on ImageNet.

![](https://i.loli.net/2018/09/26/5bab38d1e5de1.png)

<center>Fig 3. Architectures of ResNet</center>
Typically, each structure begins with a convolutional layer with 64 kernels of size 7x7 and a stride of 2. Then, we apply **Max pooling** with a stride of 2.

**As you can see, after the max pooling, there are two types of subsequent layers:**

1. Consist of 2 convolution layer with kernels of size 3x3.
2. Consist of 2 convolution layer with kernels of size 1x1 and 1 convolution layer with kernels of size 3x3.

Type 2 is called **bottleneck** unit [1] , relatively, I will call type 1 basic unit.

![](https://i.loli.net/2018/09/26/5bab3baca466c.png)



<center>Fig 4. Basic unit (left) and bottleneck unit (right)</center>
The reason for designing the bottleneck unit is actually because of the economize:

A bottleneck unit consist of a 1×1 layer for reducing dimension, a 3×3 layer, and a 1×1 layer for restoring dimension. For example, our input is 256-dimensional data, **3x3 convolution calculations are really expensive when dealing with such high dimensional data**, so, we use 64 filters to reduce the dimension to 64d, after the 3x3 convolution, we use 256 filters to restore the dimension.

So, we usually use bottleneck unit in 50 or deeper architectures.



You may noticed that the number of filters is changing, there are the rules:

**For basic unit:**

> (i) for the same output feature map size, the layers have the same number of filters; and 
>
>(ii) if the feature map size is halved, the number of filters is doubled so as to preserve the time complexity per layer. 
>
>We perform downsampling directly by convolutional layers that have a stride of 2.
>
>-- [1]

**For bottleneck unit:**

We halved the dimension of inputs in the first layer, used the same number of filters in the 3x3 convolutional layer, and then used 4 times the number of filters in the third layer, so we can double the dimensions compared to the input.

**Eventually, we end with an average pooling layer and a fully connected layer.**

### 4. Analysis

Benefit from the shortcuts, ResNet made a huge progress !!! ResNet won all the first place in the major image recognition competitions and achieved a significant lead in error rate.

So, why? Why identity mapping is so powerful in deep convolutional neural networks?

The authors gives some answers in [2].

First, let's see a resididual unit:
$$
y_l = h(x_l) + F(x_l, w_l)\tag{1}
$$

$$
x_{l+1} = f(y_l)\tag{2}
$$

Here, F is the 3x3 convolution, h is the identity mapping, f is the activation function - ReLU.

For simplify the analysis, we can assume f is the identity mapping, which is:
$$
x_{l+1} = x_l + F(x_l, w_l)\tag{3}
$$
Recursively, we will have:
$$
x_L = x_l + \sum_{i=l}^{L-1}F(x_l, w_l)\tag{4}
$$
for any deeper unit L and any shallower unit l.

At here, we are already able to figure out why residual unit is so powerful - each unit can affect all other units.

Denoting the loss function as E, from the chain rule of backpropagation we have: 
$$
\frac{\partial E}{\partial x_l} = \frac{\partial E}{\partial x_L}
\frac{\partial x_L}{\partial x_l} = \frac{\partial E}{\partial x_L}( 1 + \frac{\partial}{\partial x_l}\sum_{i=l}^{L-1}F(x_l, w_l))\tag{5}
$$

>a term of $\frac{\partial E}{\partial x_L}$ that propagates information directly without concerning any weight layers, and another term of $\frac{\partial}{\partial x_l}\sum_{i=l}^{L-1}F(x_l, w_l)$ that propagates through the weight layers. The additive term of $\frac{\partial E}{\partial x_L}$  directly propagated back to any shallower unit l......
>
>**Eqn.(4) and Eqn.(5) suggest that the signal can be directly propagated from any unit to another, both forward and backward.**



Authors also did some experiments on the importance of Identity Skip Connections. Results shown **it is always recommended to use full pre-activation (Fig 2.)**

### 5. Conclusion

This paper is really beautiful and marvelous work. The method for solving the problem is really simple and extremely elegant. Shortcut connections transform the linear logical networks into parallell subnetworks, which is easy to implement but makes Deep Residual Learning behave prodigious potential.



-- Jaho