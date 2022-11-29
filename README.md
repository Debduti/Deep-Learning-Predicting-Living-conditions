**Objective**  

With this coursework, we aim to use LiDAR images of several neighborhoods in London, UK and try to predict the deprivation indices of those localities based on these images. The deprivation indices are:  
• Income: Measures the proportion of the population experiencing deprivation relating to low income  
• Employment: Measures the proportion of the working-age population in an area involuntarily excluded from the labour market  
• Education: Measures the lack of attainment and skills in the local population  
• Health: Measures the risk of premature death and the impairment of quality of life through poor physical or mental health  
• Crime: Measures the risk of personal and material victimization at the local level  
• Barriers to Housing: Measures the physical and financial accessibility of housing and local services  
• Living Environment: Measures the quality of both the indoor and outdoor local environment    

**Approach**  

We have been provided with two sets of data  
1. A dataset (csv format) that contains the geographic details of the neighborhoods  
2. The corresponding LiDAR images of each of these neighborhoods   

We will examine a couple of Deep Learning Models, viz, VGG16 and Renet50v2 to understand how well these images can predict deprivation indices. The index we will be exploring is ‘Living Environment’.

**Hypothesis**
LiDAR is a method for determining ranges (variable distance) by targeting an object with a laser and measuring the time for the reflected light to return to the receiver. LiDAR images are a freely available resource with multiple applications in various fields including but not limited to Agriculture, Archaeology, Geology and Climate Science, Remote Sensing, where it has been very successfully used with great results. While other forms of remote sensing and related imagery has been used in the domain of socio-economic analysis, LiDAR has not been explored in this domain before this research <sup>1</sup>

We use the Transfer Learning approach on models pre-trained on Imagenet weights. The images used in Imagenet are of a different type than aerial laser images, but we hope that our models will learn the pattern using the large number of images we are providing and will be able to predict the index to some degree of accuracy. 

I am especially hopeful that we will have a decently low prediction error for Living Environment in comparison to the other indices because Living Environment can almost always be directly interpreted using the images of a particular area. LiDAR also captures some relevant information like the availability of green space, urban density which help in predicting the Living Environment. Previous work (Jean et al., Block et al.) on successfully predicting poverty using satellite imagery has been done <sup> 4 5 </sup> so it is possible to extend this work with LiDAR images.

My understanding is that the model prediction accuracy can be improved further if we use background geo-demographic information as input in addition to the images.

**Data**

We combine the CSV dataset and the LiDAR images into one single Pandas dataframe, and we split the dataframe into train and test set in a 70:30 ratio. 


Model Architecture

A. VGG16

VGG16 is a simple and widely used CNN Architecture used for ImageNet, a large visual database project used in visual object recognition software research 2 . VGG16 abbreviation for Visual Geometry Group, the group of researchers that developed this architecture, is named as such because it has 16 CNN layers. Below is the layer structure for VGG16.

 


VGG16 is a sequential model that has 16 stacked layers. Unlike earlier CNN models like AlexNet, VGG16 uses 3x3 sized kernels throughout the architecture. The final max-pooling layer is followed by 3 fully connected layers or dense layers and a final output layer with an activation function. This model was pre-trained on ImageNet data, and for this coursework, we will leverage these already trained weights and we follow the following steps to generate our model object:
a.	We import the model VGG16 on the fly, but we do not want to include the top layers, so we set the option ‘include_top = False’

b.	We use the Imagenet weights as the weights for our model, but at this point the whole model is trainable. We do not want to train the whole model and will just customize the final few layers, so we copy our model object to a new model object and set everything to untrainable in this cloned copy.

c.	We set the last two CNN layers as trainable, using the following code:

 

d.	We then add 2 dense layers with 64 neurons each and a final output layer with 1 neuron. The activation function added to each of these layers is ‘relu’.

e.	We add 50% dropout in the dense layers to avoid overfitting, which neural networks are quite prone to do. The output layer also has Relu activation function, which sets all negative values to 0 which suits our needs because Living Environment has no negative values 
 

f.	With this architecture in place, we compile the model. We choose ‘Adam’ as our optimizer, as it is an efficient, general-purpose optimizer that can be used for different applications. We set the learning rate low, at 1e-5, as we are using pre-trained models for the training to converge. Since we are predicting continuous variables (‘living condition index’), we use Mean Squared Error as our loss function.

B. RESNET50V2

ResNet-50 model is a convolutional neural network (CNN) that is 50 layers deep. Resnet stacks residual blocks on top of each other to form a network. ResNet tackles two primary challenges faced by very deep neural nets- the problem of overfitting (and model complexity) and the problem of vanishing gradients. The vanishing gradient problem occurs when the backpropagation algorithm moves back through all the neurons of the neural net to update their weights 3.  The residual model is created by using skip connections, where the output of a particular layer (say layer A) is fed forward to a layer (say layer D) which is a few layers ahead of the current layer, skipping the layers in between (layers B and C). Layer D, therefore, gets input from layer C as well as layer A. 
Residual layer:

 
**Architecture:**

  
Resnet comes with built-in batch normalization layers after every few CNN layers to avoid the data getting scaled up too much.

Resnet50 was pre-trained on Imagenet data, and we will use the Imagenet weights to train the model. We follow these steps:
a.	The model is downloaded on the fly and saved as a base model, freezing all the weights and setting it as untrainable.
b.	We then build the top layer by adding two dense layers with 64 neurons each and one output layer with one neuron. Each layer has the activation function ‘relu’.
 

**Model summary:**
 

c.	We now compile the model and as before, choose ‘Adam’ as our optimizer, and set the learning rate at 1e-5. We choose Mean Squared Error as our loss function.
d.	We create an Image Data Generator object (described in the next section) to add the images to the model in batches, 
e.	We do a warm start with two epochs.
f.	Next, we set the base model as trainable. This way all its layers except the batch normalization layers will be set to trainable.
 

g.	We now add two callbacks and train the model.
EarlyStopping: This monitors the training and validation losses and automatically stops training the model once the validation error stays within 0.00001(tolerance) of the previous epoch flat for 3 epochs(patience).
Modelcheckpoint: Saves the weights of the best performing models. In case we need to retrain our model- we can always start by calling the last saved model and not have to start from scratch.
 

**Image Data Generation:** 

We will create Image Data Generators to be used for both our models, which take images from a directory, and feed them to the model as needed. The input size is always set to 224X224.
We do data augmentation to improve the accuracy of prediction by making the models search more complex patterns. For VGG16, the generator has been set to rescale = 1./255 to normalize the inputs. For Resnet, the preprocessor normalizes the inputs, so we don’t rescale. For both models, we choose not to do any shearing, choose to zoom about 80-120%, as we think it might be beneficial to look more closely considering it is an aerial image and do both horizontal and vertical flips. We create a validation subset using 20% data from the train set.
The number of images in each set is:
 


