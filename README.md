## Deep Learning Project ##

The purpose of this project is to train a deep neural network to identify and track a target in simulation. So-called “follow me” applications like this are key to many fields of robotics and the very same techniques that could be extended to scenarios like advanced cruise control in autonomous vehicles or human-robot collaboration in industry.

[image_0]: ./docs/misc/sim_screenshot.png
[image_1]: ./misc_images/network.PNG
![alt text][image_0] 

### Summary

The purpose of this neural network is to performed scene processing and understanding. This is done by examining and classifying each pixel in a given image.

In order to perceive the input objects the neural network needs to have convolution layers. Furthermore three of these layers (A, B, C) are connected, A is connected to B, and B one to C. Where the A layer receives the input. The width and height of each layer are reduced by half with respect to their previous layer. Hence, the width and height of A is twice the with and height of B, respectively. And the same of B with respect to C.

The convolution layer also servers another purpose, it is able to group adjacent pixels and characterize features present on such pixels based on this adjacency. This depictions is achieved by exchanging a set of parameters across the image. Because these are shared, a determined type of features can be characterized, e.g., lines with a determined inclination, curves. The objective of training a convolutional network is to learn these common features.

To characterize features hierarchically, we connect three convolution layers. For example, the third layer will characterize the features composed of characterizes features form the second layer and this one can characterize features already characterized by the first layer. Thus, creating a nice chain reaction which adds complexity and collective work between layers. A simple example of this is the first layer looking out for curves and lines, second layer looking for circles and ovals and finally third looking for eyes composed of circles, lines, and ovals. 

The third convolution layer's output is fed to a 1x1 convolution layer. This is done to avoid losing information, as it happens when using a fully connected layer, which reduces dimensionality to 2D. The output of the 1x1 convolution layer is up-sampled until the original input image size is obtained. This is done to be able to perform pixel-wise classification. Three convolution layers are added to the network after the 1x1 convolution layer, to obtain the original size. 

We use skip connections to be able to do precise pixel-wise classification. Skip connections allows the network to recover features that were lost while the images where convoluted in the first three layers. At the end a pair of convolution layers are added to the end of the transposed convolution layers with skip-connections, to extract some more dimensional information.

### Architecture
![alt text][image_1] 

- Input layer: Input to the system
- block1: Result of applying 3x3 convolution with same padding and a stride of 2x2, relu activation. Output size is result by half, depth is increased  by 32 filters, output is batch-normalized
- block2,block3: result of 3x3 convolution with same padding, stride of 2x2, relu activation, depth are set to 64 and 128 filters
- convo_layer: result of applying 1x1 convolution with same padding and stride of 1x1, width and hight same as block 3 but depth of 64, batch normalized
- upsample_4: convo_layer is bilinearly up-sample to twice its size to obtain this later, this is concatenated to block2 layer to produce skip connection
- block4: 3x3 separable convolution with same padding, stride of 1x1, rule activation, depth of 138 filters applied to skip connection, result is batch normalized
- block4,block5: up sampled, convoluted and concatenated the same way as convo_layer except in this case the depth is 64 and 32filter
- X: 3x3 convolution with same padding, 1x1 stride and SoftMax activation. Depth is set by num_classes to 3

### Parameters
- learning_rate = 0.01 In the classroom, a low learning rates converge better than high learning rates, although they are slower.
- batch_size = 60 Throught testing it was found that 60 provided the required passing score, therefore there is no need to reduce its value.
- num_epochs = 10 The number of times that the network is trained on the trained set.

The remaining of the parameters were left as was recommended
- steps_per_epoch = 200
- validation_steps = 50
- workers = 2

### Discussion
1x1 convolutions are used instead of FC layers because they preserve the spatial information of the image, which is important to us, since we not only are interested in classifying the object of interest, but also its location in the image. Furthermore, fully connected layers should be used when the spatial information is to be disregarded and when we are only interested in the classification of an object.
This neural network design can be used to follow other objects such as dogs, cat, car. It just needs to be trained to do so. The reason it can be trained is because it is a fully convolutional neural network and can manage to extract the necessary features to characterize the objects at hand. This is done by the extraction of basic features on the first layer, and its build upon into more complex features on the next layers. As mentioned earlier the model need to be properly trained and validated with a good set of images contained the set target to be followed.

### Future Work

Various improvements can be implemented on this model in order to improve its performance. From our observations, we can see that the majority of the errors are produced when either a target is too far or when we have poor lighting conditions that affect the color of the target.
When target is very distant, a very small amount of pixels is associated to the target. This means that the neural network has a harder time associating the small amount off pixel to the actual target; especially because the amount of detail available are heavily degraded in a smaller number of pixels. This is a similar concept that happens in real life, as objects are further, we humans might have a harder time distinguishing unique features between different objects. 
In order to improve classification of very distant objects, we might need to look into more features or specifically more unique attributes. For example, every human as a unique walking pattern. Perhaps we can learning the walking patterns or gaits and use this as a method for classification. 
The lighting issue could be alleviated by applying filters that could potentially remove the illumination changes before the image is fed to the neural network.



