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

LiDAR is a method for determining ranges (variable distance) by targeting an object with a laser and measuring the time for the reflected light to return to the receiver. LiDAR images are a freely available resource with multiple applications in various fields including but not limited to Agriculture, Archaeology, Geology and Climate Science, Remote Sensing, where it has been very successfully used with great results. While other forms of remote sensing and related imagery has been used in the domain of socio-economic analysis, LiDAR has not been explored in this domain before this research 1

We use the Transfer Learning approach on models pre-trained on Imagenet weights. The images used in Imagenet are of a different type than aerial laser images, but we hope that our models will learn the pattern using the large number of images we are providing and will be able to predict the index to some degree of accuracy. 

I am especially hopeful that we will have a decently low prediction error for Living Environment in comparison to the other indices because Living Environment can almost always be directly interpreted using the images of a particular area. LiDAR also captures some relevant information like the availability of green space, urban density which help in predicting the Living Environment. Previous work (Jean et al., Block et al.) on successfully predicting poverty using satellite imagery has been done 4 5 so it is possible to extend this work with LiDAR images.

My understanding is that the model prediction accuracy can be improved further if we use background geo-demographic information as input in addition to the images.

**Data**

We combine the CSV dataset and the LiDAR images into one single Pandas dataframe, and we split the dataframe into train and test set in a 70:30 ratio. 

