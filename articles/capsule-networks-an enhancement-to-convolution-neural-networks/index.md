### Prerequisites

Understanding how Convolutional Neural Networks (CNNs) work is key to understanding Capsule Networks as it improves CNNs. If you are not familiar with how CNN's work, please feel free to read my previous [article](https://www.section.io/engineering-education/basics-of-convolution-neural-networks/) on the basics of CNNs. 

### Table of Contents

1. [The Orientation Problem in Convolutional Neural Networks (CNNs)](#the-orientation-problem-in-convolutional-neural-networks-cnns) 

2. [The Pooling problem in CNNs](#the-pooling-problem-in-cnns) 

3. [What are Capsules in Capsule Networks (CapsNet)?](#what-are-capsules-in-capsule-networks-capsnet) 

4. [What are Capsule Networks (CapsNet)?](#what-are-capsule-networks-capsnet)

5. [The CapsNet Architecture](#the-capsnet-architecture) 

6. [Key Takeaways](#key-takeaways)  

The key ideas behind Capsule Networks (CapsNets) were originally introduced in 2011 by [Geoffrey E. Hinton](https://en.wikipedia.org/wiki/Geoffrey_Hinton) in a research paper called [Transforming Autoencoders](http://www.cs.toronto.edu/~bonner/courses/2020s/csc2547/papers/capsules/transforming-autoencoders,-hinton,-icann-2011.pdf). But, he did have a difficult time making the network work properly.  

In 2017, Geoffrey E. Hinton, alongside Sara Sabour and Nicholas Frosst, managed to publish a paper called [Dynamic Routing Between Capsules](https://www.cs.toronto.edu/~hinton/absps/DynamicRouting.pdf/). The three researchers built on the key ideas first published in 2011, and managed to achieve state-of-the-art performance on the MNIST dataset, and demonstrated better results than Convolutional Neural Networks (CNNs) on highly overlapping digits. The capsule model was also tested on the CIFAR10 dataset, and it achieved a 10.6% error, which is not state-of-the-art, but it's a good start.   

### The Orientation Problem in Convolutional Neural Networks (CNNs)

Convolutional Neural Networks have been used in the Computer Vision domain for several years now. These networks have produced state-of-the-art results when solving image classification, image segmentation, and object detection tasks. 
Yet, these networks are not perfect. CNN's tend to fail when they are fed with images that differ in orientation and sizes. 

Let's look at an example to understand what I mean. Suppose I take an original image of a dog and flip it upside down, as shown below. 

![Original image of a dog](/engineering-education/capsule-networks-an-enhancement-to-convolution-neural-networks/original-image.jpg)<br>

![Flipped image of the original](/engineering-education/capsule-networks-an-enhancement-to-convolution-neural-networks/flipped-image.jpg)<br>

If we train the CNN on the original image and then feed in the dog's flipped image as the new input to the CNN, it will fail to identify the dog's features such as the eyes, nose, and mouth. This means that CNNs fail to learn the patterns of an image when its orientation is altered.

### The Pooling Problem in CNNs

In CNN's, we have the Max-Pooling layer. Its function is to reduce the dimensions of input data. This layer is applied to each Convolution layer, and it takes the most active neurons and continues to pass them through the network. This means that the less active neurons are dropped, getting rid of important information that might tell us more about other important spatial features in the network. 

Geoffrey E. Hinton considers this a disaster. In one of his lectures, he is quoted saying, 

"The pooling operation used in Convolution Neural Networks is a big mistake, and the fact that it works so well is a disaster."

He says that computers can probably use this data that is being discarded to detect the relationship between the eyes and the nose and the distance between it. This is in the case of a cat. 

So, how do we solve this problem?

This is where Capsule Networks (CapsNet) come into play.

### What are Capsules in Capsule Networks (CapsNet)?

Before we understand what a CapsNet is, let's first understand what a capsule is.

A capsule is a collection of neurons that learn to detect an object or parts of an object in an image. Its output is a vector whose length represents the existence of an entity being present in an image. The vector orientation represents instantiation parameters such as the object's rotation, size, or its exact location in an image. A slight adjustment to the object's rotation or its size changes the vector's orientation with proportion to its input, but its length remains the same.

Below is an example of an output that denotes an object's location in a CapsNet. The vectors' length where the object is placed is longer than those of the vectors where the object isn't placed. The vectors' orientation could represent their position, rotation, size, or scale in the image.

![Example of a vector with length and orientation in a CapsNet](/engineering-education/capsule-networks-an-enhancement-to-convolution-neural-networks/vector-length-orientation.PNG)<br>

*[Image Source: Intel](https://software.intel.com/content/www/us/en/develop/articles/understanding-capsule-network-architecture.html/)*

I hope that's clear. Let's now understand what a CapsNet is.

### What are Capsule Networks (CapsNet)?

A CapsNet consists of capsules instead of neurons in its architecture. Unlike neurons, which outputs scalars with no direction, capsules outputs vectors that have a direction. This feature in capsules helps it solve the orientation problem in CNNs. When the image's orientation is changed, the vector's direction will also move following that position.

In computer graphics, computers render images on screens by taking into account the internal representations of an object, such as its scale, rotation, and position. On the other hand, the human brain process images in the opposite way; this process is known as inverse graphics. We first identify an object, deconstruct it into different parts, and establish a relationship between these parts of an object. This is how we humans identify objects.
A CapsNet aims to borrow this idea of inverse graphics, which is the reverse process of how computers render images. Here, the network first starts with an image; it tries to determine the objects it contains and its instantiation parameters.

One key feature of Capsule Networks is preserving detailed information about the object's location and its pose throughout the network. This process is known as Equivariance. 
In CNN's, we have several pooling layers. Researchers have found that these pooling layers tend to lose information, such as the objects' precise location and pose. Capsule Networks are Equivariance, making them suitable for applications such as object detection and image segmentation.

CapsNet has four main components:

1. Matrix Multiplication.

This operation is performed on the input layer. We take an image that we want to convert into vector values to understand the spatial layout.

2. Scalar weighting of the inputs

The inputs' weighting helps determine the direction where the current capsules should go in the next layer. It works alongside the third component mentioned below. 

3. Dynamic Routing Algorithm

The role of this algorithm is to facilitate communication between the Primary Capsule and the DigitCaps layer. 

4. Applying a non-linear function to condense the information

We use the squashing (non-linear) function shown below to condense the information to a value between 0 and 1. The short vectors are squashed to an almost zero value, while the long vectors are squashed to a length of slightly less than one. Thus, the length of the output vector of a capsule denotes the probability of whether an entity is present in the current input.
Instead of adding the squashing function to each layer as in CNNs, the function is applied to a nested set of layers in a CapsNet.

It is important to note that the squashing function preserves the vectors' orientation, but it squashes it to ensure its length is between 0 and 1. 

The [paper](https://www.cs.toronto.edu/~hinton/absps/DynamicRouting.pdf/) introduces a new squashing (non-linearity) function shown below, which works best with capsules:

![The squashing function used in ConvNets](/engineering-education/capsule-networks-an-enhancement-to-convolution-neural-networks/squashing-function.PNG)<br>

*[Image Source: University of Toronto](https://web.cs.toronto.edu/)*

The term vj refers to the output after applying the squashing function; the term sj refers to the previous step's output.

### The CapsNet Architecture

The capsule network architecture contains an encoder and a decoder, with each layer consisting of 3 layers.

![A simple CapsNet with 3 layers](/engineering-education/capsule-networks-an-enhancement-to-convolution-neural-networks/caps-net-3-layers.PNG)<br>

*[Image Source: University of Toronto](https://web.cs.toronto.edu/)*

The encoder contains three layers:

1. Convolution layer (Conv1)

This layer has 256 convolutional kernels with 9 x 9 that slide with a stride of 1. The ReLU activation function is also used. This layer is responsible for converting pixel intensities to the activities of local feature detectors. These outputs are then fed as inputs to the primary capsule layer.

2. Primary Capsule layer

This layer is a convolutional layer that contains 32 primary capsules, each capsule having dimensions of 6 x 6 with shared weights. Thus, the total capsule output from this layer is 32 x 6 x 6. This layer is responsible for capturing entities at the lowest level. It performs the inverse rendering process mentioned earlier. 

3. DigitCaps layer

This layer contains one 6D capsule per digit class. Every single capsule receives its inputs from capsules in the layers below it. The output of this layer is then fed in as input to the decoder network.

A good point to note in the network's encoder part is that there is no routing between Conv1 and the Primary Capsule layer. Routing only happens between the Primary Capsule and the DigitCaps Layer.

![Decoder structure to reconstruct a digit from the DigitCaps layer representation](/engineering-education/capsule-networks-an-enhancement-to-convolution-neural-networks/caps-net-digicaps-reconstruct.PNG)<br>

*[Image Source: University of Toronto](https://web.cs.toronto.edu/)*

The decoder network aims to reconstruct MNIST digits from the DigitCaps layer representation. It reconstructs the MNIST digits by minimizing the euclidean distance between the input image and the decoder's output. 

It contains three fully-connected (FC) layers:

1. The first layer has a total of 512 neurons.
2. The second layer has a total of 1024 neurons.
3. The third layer is a sigmoid layer that contains 784 neurons, which correspond to the pixel intensities of the reconstructed image (28 X 28) for MNIST. 

### Key Takeaways

1. Capsule networks (CapsNet) work by adding structures (capsules) to a Convolutional Neural Network (CNN).

2. The Routing-By-Agreement algorithm replaces max-pooling, which performs routing by pooling. It is more effective than the conventional form that is implemented by max-pooling. It is also great for segmenting digits that highly overlap.

3. CapsNet preserves detailed information about the object's location and its pose (position, size, orientation) throughout the network. This process is known as Equivariance. 

4. The scalar-output feature detectors of a CNN are replaced with vector-output capsules. It solves the orientation problem in CNNs as when the orientation of the image is changed, the vector's direction will also move following that position.

5. CapsNet aims to borrow the idea of inverse graphics, which is the reverse process of how computers render images. 

6. CapsNet requires less training data than CNN, which requires lots of data to generalize.

### Wrapping Up

This article briefly introduces the main key ideas behind Capsule Networks (CapsNet). CapsNet is a better approach than CNN, but it's still at an infant stage. The researchers in the paper term CapsNet being in a similar stage as Recurrent Neural Networks (RNN) at the start of this century. More research still needs to be done before it can outperform current advanced technologies. I will be writing a follow-up article where I'll go in-depth on the implementation of CapsNet using the [Keras](https://keras.io/) API and the [TensorFlow](https://www.tensorflow.org/) back-end. I hope you found this article helpful.

### References

1. [Dynamic Routing Between Capsules](https://www.cs.toronto.edu/~hinton/absps/DynamicRouting.pdf/) 
2. [Transforming Autoencoders](http://www.cs.toronto.edu/~bonner/courses/2020s/csc2547/papers/capsules/transforming-autoencoders,-hinton,-icann-2011.pdf)
3. [Geoffrey E. Hinton](https://en.wikipedia.org/wiki/Geoffrey_Hinton)
4. [Convolutional Neural Network](https://www.section.io/engineering-education/basics-of-convolution-neural-networks/)
5. [TensorFlow](https://www.tensorflow.org/)
6. [Keras](https://keras.io/)
