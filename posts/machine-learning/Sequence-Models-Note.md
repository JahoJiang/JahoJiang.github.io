---
title: Cousera Sequence Models Note
date: 2019-03-07 14:15:00
tags: [Deep Learning, RNN, NLP]
categories: NLP
---

> Here is my note about the online course: https://www.coursera.org/learn/nlp-sequence-models

### 1. Why sequence models

For below tasks:

| TaskName                   | Input X                   | Output Y          |
| -------------------------- | ------------------------- | ----------------- |
| Speech Recognition         | An audio clip             | A text transcript |
| Music Generation           | Empty or Few words        | A sequence        |
| Sentiment Classification   | A phrase                  | A value           |
| DNA Analysis               | A DNA sequence            | A sequence        |
| Machine Translation        | A sentence                | A sentence        |
| Video Activity Recognition | A sequence of video frams | A value           |
| Name Entity Recognition    | A sentence                | A sequence        |

There  are some problems:

1. Length of input can be varied.
2. Length of output can be varied.
3. Words are not discrete.
4. When dealing with samples, the context is not negligible.

So, we need sequence models to handle these problems.



### 2. Notation

#### 2.1 Input

We use $x^{<1>},  x^{<2>},  x^{<3>}...  x^{<8>},  x^{<9>}$ to index every word.

**So, $x^{(i)<t>}$ denotes the element  *t* of sequence *i*.**



#### 2.2 Output

We use $y^{<1>},  y^{<2>},  y^{<3>}...  y^{<8>},  y^{<9>}$ to index every word in our output.

**So, $y^{(i)<t>}$ denotes the element  *t* of sequence *i*.**



#### 2.3 Lengths of sequences

$T_x$ denotes the length of input sequence.

$T_x^{(i)}$ denotes the length of input sequence *i*.

$T_y$ denotes the length of output sequence.

$T_y^{(i)}$ denotes the length of output sequence *i*.



#### 2.4 Representing words

1. Make a **Vocabulary** containing the words we will use.
2. Use Index to represent each word in our Vocabulary.
3. Use a special token -\<UNK> to represent words that are not in our Vocabulary.



### 3. Recurrent neural network model

#### 3.1 Why not a dtandard network ?

![sequence model.png](https://i.loli.net/2019/03/08/5c81d50637af5.png)

Problems:

1. Inputs, outputs can be different lengths in different examples.
2. Doesn't share features learned across different positions of text. (不能将从文本不同位置习得的feature进行共享)



#### 3.2 Basic RNNs

RNN scan throughs every word in our sentence from left to right.

![basic rnn.png](https://i.loli.net/2019/03/08/5c81d505eac5a.png)

##### Problem

Since the information is transmitted in one direction, $x^{<t-1>}$ cannot take into account $x^{<t>}$. To handle this problem, we may use Bidirectional RNN (BRNN).



##### Computation

* $a^{<0>} =\vec{0}$
* $a^{<1>} = g_1(W_{aa}a^{<0>} + W_{ax}x^{<1>} + b_a)$
* $a^{<t>} = g_1(W_{aa}a^{<t-1>} + W_{ax}x^{<t>} + b_a)$
* $\hat{y}^{<1>} = g_2(W_{ya}a^{<1>} + b_y)$
* $\hat{y}^{<t>} = g_2(W_{ya}a^{<t>} + b_y)$

$t$:
Time Step

$W_{ij}$:

1. The 1st index means that $W_{ij}$ is going to compute some i-like quantity.
2. The second index means that this $W_{ij}$ is going to be multiplied by some j-like quantity.
3. And, $W_{ij}$ are shared.

$g_1, g_2$:

​	Activation function, usually $\tanh$ function



##### Simplified RNN notation

* $a^{<t>} = g_1(W_{a}[a^{<t-1>}, x^{<t>}] + b_a)$

Where:
* $[W_{aa} \lvert W_{ax}] = W_a$

* $[a^{<t-1>}, x^{<t>}]= \left[\begin{matrix}a^{<t-1>} \\ x^{<t>} \end{matrix} \right]$

Obviously:
$$[W_{aa} \lvert W_{ax}]\left[\begin{matrix}a^{<t-1>} \\ x^{<t>} \end{matrix} \right]  = W_{aa}a^{<t-1>} + W_{ax}x^{<t>}$$


### 4. Backpropagation through time（BPTT）

#### 4.1 Forward propagation

![forward.png](https://i.loli.net/2019/03/08/5c81d505f0344.png)

To get $\hat{y}^{<t>}$, we need to calculate *t* time steps forward, from  Time *1* to Time *t*

#### 4.2 Backpropagation

To do the backpropagation, we need a loss function:

##### Cross entropy loss function

$\mathcal{L}^{<t>}(\hat{y}^{<t>}, {y}^{<t>}) = - {y}^{<t>}\log \hat{y}^{<t>} - (1-{y}^{<t>}) \log (1-\hat{y}^{<t>} )$

$\mathcal{L} = \sum_{t=1}^{T_y}\mathcal{L}^{<t>}(\hat{y}^{<t>}, {y}^{<t>})$

So, we need to back through time, from  Time *t* to Time *1* to update our parameters.



### 5. Different types of RNNs

#### 5.1 Many to Many v1

![basic rnn.png](https://i.loli.net/2019/03/08/5c81d505eac5a.png)

The length of Input is equal to the length of Output.

#### 5.2 Many to One

![many to one.png](https://i.loli.net/2019/03/08/5c81d505e4140.png)

#### 5.3 One to Many

![one to many.png](https://i.loli.net/2019/03/08/5c81d505ee7e2.png)

An output could be the input of next time step.

#### 5.4 Many to Many v2

![many to many v2.png](https://i.loli.net/2019/03/08/5c81d505ec7fb.png)



### 6. Language model and sequence generation

#### 6.1 What is language modelling?

For speech recognition:

How to tell from sentences with the same pronunciation?

* The apple and <u>pair</u> salad.
* The apple and <u>pear</u> salad.

**A language model will evaluate the probability of each sentence (usually our output of our model).**



#### 6.2 Language modelling with an RNN

* Training set: large corpus of English text.
* Tokenize our training sample and form a Vocabulary (SOS, EOS, etc)
* For words that are not in our Vocabulary: UNK



#### 6.3 RNN model

**Sample:**

> Cats average 15 hours of sleep a day. \<EOS>



**For example:**	

* Time step 1

  $x<1> = \vec{0}$, compute $\hat{y}^{<1>}$   ($P(a) P(abandon)…P(cats)$)

* Time step 2

  $x<2> = \hat{y}^{<1>}$, compute $\hat{y}^{<2>}$   ($P(\_ \lvert cats)$)

* Time step 3

  $x<3> = \hat{y}^{<2>}$, compute $\hat{y}^{<3>}$   ($P(\_ \lvert cats \ average)$)

* …...

* Time step 9

  $x<9> = \hat{y}^{<8>}$, compute $\hat{y}^{<3>}$   ($P(EOS \lvert cats \ average...)$)


$P(y^{<1>)},y^{<2>)}, y^{<3>)}) = P(y^{<1>)}) P(y^{<2>)}\lvert y^{<1>)})P(y^{<3>)}\lvert y^{<1>)}, y^{<2>)})$



### 7. Sampling novel sequences

#### 7.1 Sampling a sequence from a trained RNN (randomly)

![randomly sample.png](https://i.loli.net/2019/03/08/5c81d505e8891.png)

##### End

1. When model generates an EOS token
2. Only sample a limited length of words or time steps

##### UNK

1. Reject any sample that came out as unknown word token and just keep resampling from the rest of the vocabulary until you get a word that's not an unknown word. 
2. Just leave it in the output as well if you don't mind having an unknown word output. 



### 8. Vanishing gradients with RNNs

**Languages have long-term dependencies**

1. The <u>cat</u>, which ….., <u>was</u> full.
2. The <u>cats</u> which ……, <u>were</u> full.

However, for backpropagation of deep neural networks, the last few layers are difficult to affect the first few layers.

Thus, the basic RNN model could hardly handle this problem.

**Exploding gradients**

Solution:

Gradient clipping — Take a look at the gradient vectors, and if it’s getting bigger than a set threshold, you can rescale the gradients.

### 9. Gated Recurrent Unit (GRU)

#### 9.1 RNN Unit

![rnn unit.png](https://i.loli.net/2019/03/08/5c81dbc9108de.png)

##### Computation

$g = \tanh \\ a^{<t>} = g(W_{a}[a^{<t-1>}, x^{<t>}] + b_a)$


#### 9.2 GRU Unit

##### 9.2.1 Simplified

![gru unit.png](https://i.loli.net/2019/03/08/5c81dbc8ec5c8.png)

###### Notation & Computation

* C = memory cell
* $C^{<t>} = a^{<t>}$

**And every time-step, we're going to consider overwriting the memory cell with a value $\tilde{C}^{<t>}$. So this is going to be a candidate for replacing $C^{<t>}$.**

* $\tilde{C}^{<t>} = \tanh(W_c[c^{<t-1>}, x^{<t>}] + b_a)$

* $\Gamma_u$ = Gate for Update​ (0 - Not update or 1- Update)
* $\Gamma_u = \sigma(W_u[c^{<t-1>}, x^{<t>}] + b_a)$

**The job of Gate is to decide when to update the value $C^{<t>}​**$

* $C^{<t>} = \Gamma_u * \tilde{C}^{<t>} + (1-\Gamma_u) * C^{<t-1>} $

##### 9.2.2 Full Unit

* $\tilde{C}^{<t>} = \tanh(W_c[\Gamma_u * c^{<t-1>}, x^{<t>}] + b_c)$

* $\Gamma_u = \sigma(W_u[c^{<t-1>}, x^{<t>}] + b_u)$

* $\Gamma_r = \sigma(W_r[c^{<t-1>}, x^{<t>}] + b_r)$

* $C^{<t>} = \Gamma_u * \tilde{C}^{<t>} + (1-\Gamma_u) + C^{<t-1>} $

### 10. Long Short Term Memory(LSTM)

![lstm unit.png](https://i.loli.net/2019/03/08/5c81dbc8ae79b.png)

**Computation**

$$
\tilde{c}^{<t>} = \tanh(W_c[h^{<t-1>}, x^{<t>}] + b_c) \tag{1}
$$

$$
Update \ Gate: \Gamma_u = \sigma(W_u[h^{<t-1>}, x^{<t>}] + b_u) \tag{2}
$$

$$
Forget\ Gate: \Gamma_f = \sigma(W_f[h^{<t-1>}, x^{<t>}] + b_f) \tag{3}
$$

$$
Output \ Gate: \Gamma_o = \sigma(W_f[h^{<t-1>}, x^{<t>}] + b_o)\tag{4}
$$

$$
c^{<t>} = \Gamma_u * \tilde{c}^{<t>} + \Gamma_f * {c}^{<t-1>}  \tag{5}
$$

$$
h^{<t>} = \Gamma_o * \tanh c^{<t>} \tag{6}
$$



* $\tilde{c}^{<t>} = \tanh(W_c[a^{<t-1>}, x^{<t>}] + b_c)$

* Update gate: $\Gamma_u = \sigma(W_u[a^{<t-1>}, x^{<t>}] + b_u)$

* Forget gate: $\Gamma_f = \sigma(W_f[a^{<t-1>}, x^{<t>}] + b_f)$
* Output gate: $\Gamma_o = \sigma(W_f[a^{<t-1>}, x^{<t>}] + b_o)$

* $c^{<t>} = \Gamma_u * \tilde{c}^{<t>} + \Gamma_f * \tilde{c}^{<t-1>} $
* $a^{<t>} = \Gamma_o * \tanh c^{<t>}$



### 11. Bidirectional RNN

To take information from ealier and later time steps.

![bidirectional rnn.png](https://i.loli.net/2019/03/08/5c82100210341.png)

This is call an Acyclic graph.

First, we calculate all forward components $a^{<t>}$ (black color in above graph) from left to right, then we calculate all backward components $a^{<t>}$(red color) from right to left.

**For every block, can be GRU or LSTM.**

#### 11.1 Computation

* $\hat{y}^{<t>} = g(W_{y}[a_{forward}^{<t>}, a_{backward}^{<t>}] + b_y)$



#### 11.2 Disadvantage

Must scan the whole sentence before the model generates a sentence.



### 12. Deep RNNs

Just like CNNs, more hidden layers.

![deep rnn.png](https://i.loli.net/2019/03/08/5c82172c1f87d.png)

**Computation**

* $a^{[2]<3>} = g(W_a^{[2]}[a^{[2]<2>}, a^{[3]<1>}] + b_a^{[2]})$



### 13. Notes for Assignments

#### 13.1 Notation

| Symbol | Meaning                       |
| ------ | ----------------------------- |
| $x_t$  | input data at time step 't'   |
| $a_t$  | $\tanh W(x_t, h_{t-1} )$      |
| $h_t$  | hidden state at time step 't' |
| $f_t$  | forget gate at time step 't'  |
| $i_t$  | inpute gate at time step 't'  |
| $o_t$  | Output gate at time step 't'  |
| W      | matrix of weights             |

#### 13.2 Deriving Forward Feed and Back Propagation on RNN

[Source file](http://slazebni.cs.illinois.edu/spring17/lec03_rnn.pdf)

![](https://i.loli.net/2019/03/12/5c8750cbb0f41.png)

<center>Fig 01. A simple RNN cell</center>
![](https://i.loli.net/2019/03/12/5c8751954a069.png)

<center>Fig 02. Forward Feed on simple RNN</center>
![](https://i.loli.net/2019/03/12/5c8751edce11d.png)

<center>Fig 03. Backward Propagation on simple RNN</center>
#### 13.3 Deriving Forward Feed and Back Propagation on LSTM

[Source Graphic](http://arunmallya.github.io/writeups/nn/lstm/index.html#/)
