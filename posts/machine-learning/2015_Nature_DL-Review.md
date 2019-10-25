---
title: Deep Learning
date: 2018-09-06 10:03:36
tags: [Deep Learning]
categories: Papers Notes
---

### 0. Before starting - Machine learning

*Since this is my first note about machine learning, I will introduce some basic concepts and terms of  AI.*

#### What is Machine learning?

>**Machine learning** is a field of computer science that uses statistical techniques to give computer systems the ability to "learn" (e.g., progressively improve performance on a specific task) with data, without being explicitly programmed.
>
>-- Wikipedia

**According to composition of data and training strategy, generally, there are three categories of Machine learning algorithms:**

![Fig.01 Machine learning categories](https://i.loli.net/2018/09/06/5b90ed4084cd3.png)

##### 0. Supervised learning & Unsupervised learning

###### Some Terms

1. **Sample(Instance)** - a set of attributes(features) for a specific object, e.g. (170cm, long hair, 50kg) is a sample for a person.
2. **Dimensionality** - the quantity of attributes in a sample. e.g. the dimensionality of (170cm, long hair, 50kg) is 3.
3. **Label** - a tag for a sample. e.g. we give a tag - "female" to the sample (170cm, long hair, 50kg), which means this sample is from a female.
4. **Example** - a sample with a label.
5. **Model** - the product of learning algorithm, that's what we really need

##### 1. Supervised learning

>**Supervised learning** is the machine learning task of learning a function that maps an input to an output based on example input-output pairs.In supervised learning, each example is a pair consisting of an input object (typically a vector) and a desired output value (also called the supervisory signal).
>
>-- Wikipedia

###### Architecture of Supervised learning

![Fig.02 Supervised learning architecture](https://i.loli.net/2018/09/06/5b90dbb86098b.png)

**As the diagram above, Supervised learning algorithm:**

1. Taking training data as inputs, which is a set of examples that we want our model to know to train the model

   *Mention: Training Data must have nothing in common with Testing Data, which means they do not have  intersection !!!*

2. After each epoch of training, we use Testing / Validation Data to test / validate our model, to see if our model is good enough for us to stop training.

3. Finally, we got the model with satisfactory accuracy. So we can use it to process new data. This procedure is called 'predict' - the model give us a result according to the input and what it have learned.

Namely, **supervised learning** means our data that used for training and testing is a set of **examples**. We tell the model what kind of thing is for every single **sample**. e.g. we want build a model that could predict this is a male or female simply by (height, long / short hair,  weight). What we need to do firstly is getting a large amount of examples. Each example contains **attributes** - (height, long / short hair,  weight) and the **label** - male / female.

##### 2. Unsupervised learning

> **Unsupervised machine learning** is the machine learning task of inferring a function that describes the structure of "unlabeled" data (i.e. data that has not been classified or categorized)
>
> -- Wikipedia

Contrast with above, this concept is understandable - our data that used for training and testing is a set of **samples** instead examples. An example of unsupervised learning is clustering classification: we just feed our samples to our model, and algorithm will try to put some similar things in a same cluster, the concept of similarity i.e. how to judge two things are simillar or dissimilar, depends on a similarity measure.

##### 3. Reinforcement learning

>**Reinforcement learning** (**RL**) is an area of machine learning concerned with how software agents ought to take actions in an environment so as to maximize some notion of cumulative reward.
>
>-- Wikipedia

RL is totally different from above categories of learning algorithm, so introduce some terms:

1. **Agent** - we train, test agent in reinforcement learning.
2. **Environment** - where we are going to train and use our agent
3. **Action** - what agent can do in the environment.
4. **Reward** - an evaluation for actions.

###### Architecture of Reinforcement learning

![Fig.03 Reinforcement learning architecture](https://i.loli.net/2018/09/06/5b9121c6d36c7.png)

> Reinforcement learning problems involve learning what to do—how to map situations to actions—so as to maximize a numerical reward signal.
>
> -- Reinforcement Learning: An Introduction

The renowned Alpha Go is actually implemented by using Reinforcement learning (more specificly, Deep Reinforcement Learning). The game - Go is the **environment**, Alpha Go is actually the **agent**, **actions** at here are placing pieces on the board, **reward** is whether or not win the game.



### 1. What is Deep learning?

>**Deep learning** is a kind of machine learning methods based on learning data representations, as opposed to task-specific algorithms. **Learning can be supervised, semi-supervised or unsupervised.**
>
>-- Wikipedia

![Fig.04 AI history](https://blogs.nvidia.com/wp-content/uploads/2016/07/Deep_Learning_Icons_R5_PNG.jpg.png)

In this paper, the definition of deep learning is:

>Deep learning methods are representation-learning methods with multiple levels of representation, obtained by composing simple but non-linear modules that each transform the representation at one level (starting with the raw input) into a representation at a higher, slightly more abstract level.

To understand this, we need some prerequisite knowledge.

#### 1.1 Linear regression

In linear regression, we assume:

1. There is correlation between the dependent (y) and independent (x) variables.

2. y depends on x through a linear equation:


$$
   y_i = ax_i + b\tag{1}
$$

3. However there is random noise e in the observations of the dependent variable:


$$
   y_i=ax_i+b+e_i\tag{2}
$$

4. Our task is thus to find a and b. 

But firstly, we must test if there is indeed a relationship between x and y. We can use "covariance" and "correlation coefficient" to do this.

After that, we can build the model by equation (1), for example, initialize a and b to 1, and then we provide the data to our model. In each epoche, our model will tune a and b until our model achieves tolerable accuracy.

There are also other statistical methods, such as **Naive Bayesian Classification** and **Support Vector Machines**. However, we only need to maintain this important idea: suppose a **representation** with multiple **parameters**, and **tune** these parameters according to the training data until we get satisfactory test data **accuracy**.

#### 1.2 Artificial Neurons

![Fig.05 Biological neuron](https://machineslearn.co.uk/wp-content/uploads/2018/01/biological-neuron.png)

Inspired by the structure of biological neural networks composed of a lot of neurons, scientists designed the Artificial neurons:

![Fig.06 Artificial neuron](https://upload.wikimedia.org/wikipedia/commons/6/60/ArtificialNeuronModel_english.png)

1. Like the biological neurons, artificial neurons also sum **inputs** through **weights**, which is the responsibility of "**transfer functions**":
   $$
   \sum_{1}^{n}w_{nj} * x_n\tag{3}
   $$




2. The neuron will be **"activated"** (outputs a "1") if the sum exceeds a **threshold**, which is the responsibility of "**activation function**". 

So, that's our artificial neurons, with more abstract, we can denote a neuron by the formula:
$$
f(X\cdot W^T)\tag{4}
$$
Where $f$ is the activation function, $W$ is a $1*n$ matrix of weights, and $X$ is a $1 * n$ matrix of inputs, $X\cdot W^T$ is  a dot product.

#### 1.3 Perceptron Neurons

Perceptron Neuron is just a specific neuron:

![Fig.07 Perceptron](https://cdn-images-1.medium.com/max/1600/1*pk33l-ocIw0WGwSPp9JLpw.jpeg)

1. A set of inputs - x
2. A set of weights - w
3. A unit that does a dot product $w^Tx$
4. An activation function that does outputs 1 when $x\cdot w^T + b > 0$  and -1 otherwise.(other functions are  possible, e.g. the *identity function* $id(x)=x$)
5. The *bias* b is normally modelled as a unit input, with weight $w_0$ become *bias*. So, we just add 1 to inputs as $x_0$. So we can still use $w^Tx$ to calculate.

**The learning algorithm is really simple:**

1. Data: 

   Assume that we have *i* vectors as inputs, each vector has *j* columns, the additional "1" element is for calculating *bias*.


$$
   Inputs = X = \begin{bmatrix} 
   1& x_{11} & ... & x_{1j}\\
   1& x_{21} & ... & x_{2j}\\
   ...& ... & ... &...\\
   1& x_{i1} & ... & x_{ij}\\
   \end{bmatrix}\\
   \tag{5}
$$

$$
   Weights = W = [bias,w_1,w_2,..w_j]
$$

2. FEEDFORWARD:

$$
y_{out} = f(\sum_{j=0}^{n}w_jx_{ij}) = X W^T\tag{6}
$$

	Here, *f* is the activation function.

3. UPDATE:
   $$
   w_j = w_j + \alpha(y_n - y_{out})x_{nj}\tag{7}
   $$
   Here, $\alpha$ is called learning rate, $y_n$ is actually the label, so, this is supervised learning. So, if our $y_{out}$ is bigger than $y_n$, we willadd a negative number to $w_j$, which decrease the value of $w_j$. Otherwise, we increase $w_j$. **That is how the perceptron learns from given data.**

#### 1.4 Multi-layer perceptrons (MLP)

A single perceptron is really simple and useless, it cannot handle a slightly complex problem, e.g "xor" problem.

![Fig.08 XOR problem](https://cdn-images-1.medium.com/max/1600/1*RE6XwUu9YA6DwFKIJ8ImWQ.png)

To handle more complex problem, we come to "MLP" - Multi-layer perceptrons:

![Fig.09 Multi-layer perceptrons](https://image.ibb.co/j9jgpm/first_example_copia.png)

The layers between input layer and output layer are called "**hidden layers**". As you can see, it is a network of neurons, that is ''**neural network**".

**An MLP consists of:**

1. A set of inputs $x$ with $n + 1$ elements. As before, the additional "1" element is for computing *bias*.
2. A set of *m* Perceptron nodes called "hidden nodes", *h*  .
3. A set of weights $w_h$ connecting *x* to *h*  .
4. A set of weights *q* Perceptron nodes *p* for the output layer.
5. A set of weights $w_p$ conntecting $h$ to *p*  .

Learning algorithm:

1. FEEDFORWARD:


$$
   h_j = f(\sum_{i=0}^n w_{ij}x_i)\\
   p_k = g(\sum_{k=0}^n w_{jk}h_p)\tag{8}
$$


**But, more neurons also bring more problems.**

1. How do we compute errors? 

In single perceptron, it is easy to compute the error, which is $y_n - y_{out}$

2. How do we assign errors to hidden layer?

**So, in MLP, we use a technique called "Gradient Descent"**

1. In the output layer, we use **"cost function"** to compute error. e.g. find the quadratic error for output *k*:
   $$
   E = \frac{1}{2}(p_k-y_t)^2
   \tag{9}
   $$




2. We want to minimize E :
   $$
   \frac{dE}{dw_{jk}} = 0
   $$



3. Update:

$$
w_{jk} = w_{jk} - \alpha \frac{\partial E}{\partial w_{jk}} \tag{10}
$$
![Fig.10 Gradient descent](https://cdn-images-1.medium.com/max/1600/0*rBQI7uBhBKE8KT-X.png)

* This points us in the direction of the maximum.

* We therefore go in the OPPOSITE direction of the derivative, towards to minimum.

**This means that our activation function must be differentiable.**

There are a lot of activation functions. So, which one should we use? In this paper, authors told us:

> At present, the most popular non-linear function is the rectified linear unit (**ReLU**), which is simply the half-wave rectifier *f(z) = max(z, 0)*. In past decades, neural nets used smoother non-linearities, such as *tanh(z) or 1/(1 + exp(−z))*, but the ReLU typically learns much faster in networks with many layers, allowing training of a deep supervised network without unsupervised pre-training.

![Fig.11 - ReLU funciton](https://www.safaribooksonline.com/library/view/neural-networks-with/9781788397872/assets/3df87f1b-ecb1-4e6f-9d83-7d2b082a9853.png)

**Differentiating ReLU is simple, but tricky:**
$$
\frac{df(x)}{dx}=1,when\ x>0\\
\frac{df(x)}{dx}=0,when\ x<0\\
\tag{11}
$$
*When x = 0, we can assign an arbitrary value, like 0 or 1.*

**For now, to assign the errors to hidden layers, we use another new technique - "backpropagate":**

In this paper, the authors give us an example to explain how "backpropagate" algorithm works:

![Fig.12 Backpropagation](https://i.loli.net/2018/09/10/5b9608a249ff5.png)

**Assumue we have a network as above, there are four layers: i, j, k, l, where i is the input layer, j and k are hidden layers and l is the output layer.**

Left:

>At each layer, we first compute the **total input z** to each unit, which is a weighted sum of the outputs of the units in the layer below. Then a non-linear **function f(.)** is applied to z to get the output of the unit. 

Right:

>At each hidden layer we compute the error derivative with respect to the output of each unit, which is a weighted sum of the error derivatives with respect to the total inputs to the units in the layer above. We then convert the error derivative with respect to the output into the error derivative with respect to the input by multiplying it by the gradient of *f(z)*. At the output layer, the error derivative with respect to the output of a unit is computed by differentiating the cost function. This gives $y_l - t_l$ if the cost function for units l is $0.5(y_l-t_l)^2$,where $t_l$ is the target value. Once the $\frac{\partial E}{\partial z_k}$ is known, the error-derivative for the weight $w_{jk}$ on the connection from unit *j* in the layer below is just $y_j \frac{\partial E}{\partial z_k}$.

**Let's take a node of k layer as  an example, we want to update the weight $w_{jk}$:**

We need to compute the total error caused by node k:

1. We look up, there are several weights connect node k to the output layer. So, we need to compute error of each unit in the output layer.

2. For a specific node in the output layer, its output is y, its total input is z. The error derivative is:


$$
\frac{\partial E}{\partial z_l} =  \frac{\partial E}{\partial y_l}\frac{\partial y_l}{\partial z_l}\tag{12}
$$


   Where first part of right is "cost function" and the second part of right is actually "activation function".

3. So, for the node k:


$$
\frac{\partial E}{\partial z_k}
   = \frac{\partial E}{\partial y_k}\frac{\partial y_k}{\partial z_k}
   = \frac{\partial y_k}{\partial z_k}
   \sum_{l\epsilon out}w_{kl}\frac{\partial E}{\partial z_l}
   \tag{13}
$$


4. Update:


$$
w_{jk} = w_{jk} - \alpha \frac{\partial E}{\partial w_{jk}}= w_{jk} - \alpha y_j\frac{\partial E}{\partial z_k}
   \tag{14}
$$

Where the $\frac{\partial E}{\partial z_k}$ is called **"residual"**.

**Particularly, if we use:**

1. Cost function - Eqn. 9
   $$
   E = \frac{1}{2}(y_l-t_l)^2
   $$

2. Activation function - sigmoid function

The sigmoid has a very convenient derivative:
$$
f(x) = \frac{1}{1+e^{-x}}\\
\frac{df(x)}{dx} = f(x)(1-f(x))
\tag{15}
$$
And,
$$
\frac{\partial E}{\partial z_k}
= \frac{\partial y_k}{\partial z_k}
   \sum_{l\epsilon out}w_{kl}\frac{\partial E}{\partial z_l}
= \frac{\partial y_k}{\partial z_k}
   \sum_{l\epsilon out}w_{kl}\frac{\partial E}{\partial y_l}\frac{\partial y_l}{\partial z_l}\\
 = f(z_k)(1-f(z_k))\sum_{l\epsilon out}w_{kl}(y_l-t_l)f(z_l)(1-f(z_l))
   \tag{16}
$$
So,
$$
w_{jk} = w_{jk} - \alpha y_jf(z_k)(1-f(z_k))\sum_{l\epsilon out}w_{kl}(y_l-t_l)f(z_l)(1-f(z_l))\tag{17}
$$
A network with one hidden layer is already hard to train, a network with more than one hidden layer, that is '**Deep learning**'. As you can see, to update single weight, we need to do a lot of work even if we just have 2 hidden layers. But, with ReLU function, we can simplify the above equation:
$$
w_{jk} = w_{jk} - \alpha y_j\sum_{l\epsilon out}w_{kl}(y_l-t_l)\tag{18}
$$
**This makes it possible to train a complex deep learning network.**



#### 1.5 CNNs - Convolutional neural networks

>ConvNets are designed to process data that come in the form of multiple arrays, for example a colour image composed of three 2D arrays containing pixel intensities in the three colour channels. Many data modalities are in the form of multiple arrays: 1D for signals and sequences, including language; 2D for images or audio spectrograms; and 3D for video or volumetric images. There are four key ideas behind ConvNets that take advantage of the properties of natural signals: local connections, shared weights, pooling and the use of many layers.

![Fig.13 Convolutional neural networks](http://www.mdpi.com/entropy/entropy-19-00242/article_deploy/html/images/entropy-19-00242-g001.png)

**Convolutional layer:**

>Units in a convolutional layer are organized in feature maps, within which each unit is connected to local patches in the feature maps of the previous layer through a set of weights called a filter bank. The result of this local weighted sum is then passed through a non-linearity such as a ReLU. All units in a feature map share the same filter bank. Different feature maps in a layer use different filter banks.

**Pooling layer:**

>The role of the pooling layer is to merge semantically similar features into one. Because the relative positions of the features forming a motif can vary somewhat, reliably detecting the motif can be done by coarse-graining the position of each feature. A typical pooling unit computes the maximum of a local patch of units in one feature map (or in a few feature maps).

In short, CNN is a combination with:

1. One input layer
2. Several hidden layers composed consisting of convolutional layers and pooling layers
3. One output layer, whichi is a fully-connected layer

It is conceivable that after a convolutional layer, the data will be several times the original. So, the another benefit of a pooling layer is reducing the scale of data.

**Personal understanding:**

CNN is designed to solve the very complex problems like image recognition, the essential idea behind this algorithm is "abstracting". Using convolutional layer to produce feature maps, using pooling layers to coarse-grain the position of each feature, which is intended to abstract the image data. Therefore, through several convolutional and pooling layers, we continue to abstract until we find common feature maps and reasonable weights to identify different categories of images.

#### 1.6 RNNs - Recurrent neural networks

>When backpropagation was first introduced, its most exciting use was for training recurrent neural networks (RNNs). For tasks that involve sequential inputs, such as speech and language, it is often better to use RNNs. RNNs process an input sequence one element at a time, maintaining in their hidden units a 'state vector' that implicitly contains information about the history of all the past elements of the sequence.

![Fig.14 A recurrent neural network and the unfolding in time of the
computation involved in its forward computation.](https://upload.wikimedia.org/wikipedia/commons/b/b5/Recurrent_neural_network_unfold.svg)For example, hidden units grouped under node h with values $h_t$ at time t, get inputs from other neurons at previous time steps (this is represented with the black square V, representing a delay of one time step, on the left). 

In this way, a recurrent neural network can map an input sequence with elements $x_t$ into an output sequence with elements $o_t$, with each $o_t$ depending on all the previous $x_{t'}$ (for tʹ ≤ t). The same parameters (matrices U,V,W ) are used at each time step. The backpropagation algorithm can be directly applied to the computational graph of the unfolded network on the right, to compute the derivative of a total error with respect to all the states $s_t$ and all the parameters.

*(The above two paragraphs are from the paper and have been partially modified).*

 

Deep learning has been widely used in many complex areas, and it is indeed very powerful and promising.



-- Jaho