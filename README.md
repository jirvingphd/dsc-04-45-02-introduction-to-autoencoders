
# Introduction To Auto-Encoders

## Introduction

Earlier, we looked at PCA and SVD for the the task of data compression using dimensionality reduction techniques. Data compression is an important topic in many areas of data science, used for convert input into a smaller representations with some degree of quality. This smaller representation is what would be passed around, and, when anyone needed the original, they would reconstruct it from the smaller representation.

In this lesson we will look at autoencoders and with some key application areas. Autoencoders are unsupervised neural networks that use machine learning to do data compression for us. The aim of an autoencoder is to learn a compressed, distributed representation for the given data.

## Objectives
- Understand and describe the role of auto encoders for data compression
- Explain how AEs use the Latent Space model to create a latent representation of data
- Distinguish between undercomplete and overcomplete AEs and their applications
- Learn important properties, architectures and loss functions for typical AEs


## What are Autoencoders?

Autoencoders use unsupervised learning technique  that uses feedforward neural networks. In autoencoders, __the input is the same as the output__. They compress the input into a lower-dimensional code called the summary or __Representation__. Autoencoders then reconstruct the output from this representation, instead of the input. This “compression” representation of the input is called the __Latent-space Representation__, and this task is described as __Representation Learning__. 

__Specifically, AEs are neural network architecture such that impose a "Bottleneck" in the network which forces a compressed knowledge representation of the original input.__

In the following image, we san see the original inputs $x_1 .. x_6$ are first compressed in a bottleneck which is simply a layer with much less number of neurons. The Latent speace representation $a_1 .. a_3$ is learnt from the input data and is used to reconsutruct the outputs $\hat{x_1} .. \hat{x_6}$.  

<img src="rep1.png" width=600>

### Encoder and Decoder
An autoencoder consists of 3 components: 
1. The Encoder 
2. The Code 
3. The Decoderdecoder. 

>__Encoder__: This is the part of the network that compresses the input into a latent-space representation. It can be represented by an encoding function $h=f(x)$.

> __Decoder__: This part aims to reconstruct the input from the latent space representation. It can be represented by a decoding function $r=g(h)$.


<img src ="ed1.png" width=500>

Above the image input gets encoded into a latent representation which is identified as "Code" (as the latest representation becomes just a code after encoding process). The low dimensional latent representation can then be used to reproduce an __approximation__ of the input.  

The autoencoder as a whole can thus be described by the function:
$$g(f(x)) = r$$ 
where we want $r$ as close as the original input $x$



### Why do we need a model that just copies input to the output ?

If the soul purpose of autoencoders was to copy the input to the output, they would be pretty useless. 
It is actually the latent representation $h$ above that is interest to us as it can help us learn some useful properties of underlying data.

One way to obtain useful features from the autoencoder is to constrain $h$ to have smaller dimensions than $x$ to create an __Undercomplete AE__, thus forcing force the autoencoder to learn the most salient features of the training data. 

If the autoencoder is given too much capacity, it can copy $x$ to $r$ task without extracting any useful information about the distribution of $x$. This can also occur if the dimension of the latent representation is the same as the input, and in the __Overcomplete AE__, where the dimension of the latent representation is greater than the input. 

<img src="comp.jpg" width=500>

Above we see examples of undercomplete and overcomplete AEs as shallow and deep (with hidden layers) networks. We can train any architecture of autoencoders successfully, choosing the code dimension and the capacity of the encoder and decoder based on the complexity of distribution to be modeled.



## Learning the Latent Representation

With AEs, we can take an unlabeled dataset and turn it into a supervised learning problem tasked with outputting $x̂$ ,a reconstruction of the original input $x$. Such a network can be trained by minimizing the __Reconstruction Error__, $L(x,x̂)$, which measures the differences between our original input and the consequent reconstruction. 

The bottleneck is a key attribute of an AE network design; without the presence of an information bottleneck, our network could easily learn to simply memorize the input values by passing these values along through the network. A bottleneck constrains the amount of information that can traverse the full network, forcing a learned compression of the input data. 

<img src="nobn.png" width=600>



Above image shows a latent layer, with similar number of neurons which would simply memorize the input in the relevant lower layered neuron. This is simply learning a linear model, something very close to what we saw with PCA. Because neural networks are capable of learning nonlinear relationships, AEs can be thought of as a more powerful (nonlinear) generalization of PCA. Whereas PCA attempts to discover a lower dimensional hyperplane which describes the original data, autoencoders are capable of learning nonlinear __manifolds__ (a continuous, non-intersecting surface). The difference between these two approaches is visualized below.

<img src="pca.png" width=600>



### AE Architecture

Following image shows a typical AE architecture. More complicated and non-symmetric architectures are also possible for special cases.

<img src="und.png" width=500>

Here we see an example of a "Deep" AE, where the input is sequentially compressed into a latent representation, and the output is also being approximated in a series of steps. First the input passes through the encoder, which is a fully-connected ANN, to produce the code. The decoder, which has the similar ANN structure, then produces the output only using the code.

Note that the decoder architecture is the mirror image of the encoder. This is not a requirement but it’s typically the case. The only requirement is the dimensionality of the input and output needs to be the same.

### AE Loss Function

The ideal autoencoder model balances the following:

- Sensitive enough to accurately build a reconstruction from inputs.
- Insensitive enough not to directly memorize/overfit to the inputs. 

This trade-off forces the model to maintain __only the necessary variations__ in the data required to reconstruct the input without holding on to redundancies in data. For this, we use a loss function where one term encourages our model to be sensitive to the inputs (ie. __reconstruction loss__ $L(x,x̂)$ and a second term discourages memorization/overfitting (ie. an added regularizer).

$$L(x,x̂ )+ regularizer$$

We typically add a scaling parameter in front of the regularization term so that we can adjust the trade-off between the two objectives.

In the commonly used undercomplete AE, penalizing the network according to the reconstruction error allows the model can learn the most important attributes of the input data and how to best reconstruct the original input from an "encoded" state. Ideally, this encoding will learn and describe latent attributes of the input data.


The loss functions typically used in these architectures is mean squared error: 

$$J(x,z)=‖x−z‖^2$$ 

This measures how close the reconstructed input $z$ is to the original input $x$. When the data resembles a vector binary values or a vector of probabilities (which are both values in the range of [0,1]), we can also use the cross-entropy of reconstruction loss function, which calculates how many “bits” of information are preserved in the reconstruction compared to the original. This loss function is:

$$J(x,z)=−\sum_k^d[x_k log(z_k)+(1−x_k)log(1−z_k)]$$

> Autoencoders are trained the same way as ANNs via backpropagation.

### AE Hyper Parameters

There are 4 hyperparameters that we need to set before training an autoencoder:

<img src="params.png" width=400>


__Code size__: number of nodes in the middle layer. Smaller size results in higher compression.

__Number of layers__: the autoencoder can be as deep as we like. In the figure above we have 3 hidden layers in both the encoder and decoder, without considering the input and output.

__Number of nodes per layer__: Typically, the number of nodes per layer decreases with each subsequent layer of the encoder, and increases back in the decoder. Also the decoder is symmetric to the encoder in terms of layer structure. *Note: this is not __necessary__ and we have total control over these parameters.*

__Loss function__: we either use mean squared error (mse) or binary crossentropy. If the input values are in the range [0, 1] then we typically use cross entropy, otherwise we use the mean squared error. 



## Properties of AEs

Autoencoders have a few important properties:

- **Data-specific**: Autoencoders are only able to meaningfully compress data similar to what they have been trained on. Since they learn features specific for the given training data, they are different than a standard data compression algorithm like zip. We can’t expect an autoencoder trained on handwritten digits to compress facial photos.

- **Lossy**: The output of the autoencoder will not be exactly the same as the input, it will be a close but degraded representation.

-  **Unsupervised**: To train an autoencoder we don’t need to do anything fancy, just throw the raw input data at it. Autoencoders are considered an unsupervised learning technique since they don’t need explicit labels to train on. But to be more precise they are __self-supervised__ because they generate their own labels from the training data.

ABove provides you with a brief introduction to autoencoders and how they function. Next, we shall try to implement a simple autoencoder built from ANNs that we have already seen. 


## Additional Resources

- [A Beginner's Guide to AutoEncoders](https://www.edureka.co/blog/autoencoders-tutorial/)
- [How to Auto Encode your Pokemon](https://hackernoon.com/how-to-autoencode-your-pok%C3%A9mon-6b0f5c7b7d97) - Recommended

## Summary 

In this lesson, we looked at what autoencoders are , and how they hdelp with data compression using a latent representation of data. Next, we shall build a vanilla AE in keras and see this in action. 
