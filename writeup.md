# **Traffic Sign Recognition** 


**Build a Traffic Sign Recognition Project**

The goals / steps of this project are the following:
* Load the data set (see below for links to the project data set)
* Explore, summarize and visualize the data set
* Design, train and test a model architecture
* Use the model to make predictions on new images
* Analyze the softmax probabilities of the new images
* Summarize the results with a written report


[//]: # (Image References)

[image1]: ./examples/visualization.jpg "Visualization"
[image2]: ./examples/grayscale.jpg "Grayscaling"
[image3]: ./examples/random_noise.jpg "Random Noise"
[image4]: ./examples/placeholder.png "Traffic Sign 1"
[image5]: ./examples/placeholder.png "Traffic Sign 2"
[image6]: ./examples/placeholder.png "Traffic Sign 3"
[image7]: ./examples/placeholder.png "Traffic Sign 4"
[image8]: ./examples/placeholder.png "Traffic Sign 5"

## Rubric Points
#### Here I will consider the [rubric points](https://review.udacity.com/#!/rubrics/481/view) individually and describe how I addressed each point in my implementation.  

---
Here is a link to my [project code](https://github.com/fvmassoli/fvmassoli-CarND-Traffic-Sign-Classifier-Project-2/blob/master/Traffic_Sign_Classifier.ipynb)

### Data Set Summary & Exploration

#### 1. Provide a basic summary of the data set and identify where in your code the summary was done. In the code, the analysis should be done using python, numpy and/or pandas methods rather than hardcoding results manually.

In the second and third cell of the notebook I used numpy and pandas to print some infos about the data. Here is a summary:

* The size of training set is 34799
* The size of test set is 12630
* The shape of a traffic sign image is (32, 32, 3)
* The number of unique classes/labels in the data set is 43

#### 2 Include an exploratory visualization of the dataset and identify where the code is in your code file.

In cell 5 I implemented a method to draw a signal image. 

I use that method to print an input sign image as well as a preprocessed greyscaled one. Here is an example of traffic sign input image before preprocessing.

![alt text](https://github.com/fvmassoli/fvmassoli-CarND-Traffic-Sign-Classifier-Project-2/blob/master/signal_before_preprocessing2.png " ")

In the cell number 7 I explored the distribution of the traffic signs among the variuos classes.

A summary bar chart is shown
below. 

On the x-axis there is the id of the sign class while on the y-axis it is reported the number of entries for each
class.

![alt text](https://github.com/fvmassoli/fvmassoli-CarND-Traffic-Sign-Classifier-Project-2/blob/master/sample_distribution.png " ")


### Design and Test a Model Architecture

#### 1. Describe how, and identify where in your code, you preprocessed the image data. What tecniques were chosen and why did you choose these techniques? Consider including images showing the output of each preprocessing technique. Pre-processing refers to techniques such as converting to grayscale, normalization, etc.

For what concerns the next steps and in the particular data preprocessing and augmentation I have been inspired by the
following [article](https://pdfs.semanticscholar.org/2065/99bd66cf9b2679f05945637e6bb32a8fb127.pdf). 

As we can see from the previous figure, there isn't an uniform population among the 43 sign classes. In order to avoid
biases towards more populated classes we need to augment the data (see next section). 

Before data augmentation I preprocessed the data by normalizing and greyscaling them. This helps the training phase since, for example, 
our neural net will have to deal with only one color channel instead of three (we are looking for shape features not colored
ones).

The code I used to preprocess the data is in the cells number 9, 10 and 11.
An example of preprocessed data is shown below.

![alt text](https://github.com/fvmassoli/fvmassoli-CarND-Traffic-Sign-Classifier-Project-2/blob/master/signal_after_preprocessing.png " ")


#### 2. Describe how, and identify where in your code, you set up training, validation and testing data. How much data was in each set? Explain what techniques were used to split the data into these sets. (OPTIONAL: As described in the "Stand Out Suggestions" part of the rubric, if you generated additional data for training, describe why you decided to generate additional data, how you generated the data, identify where in your code, and provide example images of the additional data)

In order to flatten the distribution among the various signal classes, I augmented the data by producing new images by implementing a rotation of the already available pictures. The code is available in the cell number 14. The final image distribution is the following.

![alt text](https://github.com/fvmassoli/fvmassoli-CarND-Traffic-Sign-Classifier-Project-2/blob/master/augmented_sample_distribution.png " ")

The following picture shows an example of a newly generated signal image.

![alt text](https://github.com/fvmassoli/fvmassoli-CarND-Traffic-Sign-Classifier-Project-2/blob/master/newly_generated_traffic_sign_image.png " ")

The code for splitting the data into training and validation sets is contained in the cell number 16.

To cross validate my model, I randomly split the training data into a training set and validation set. I did that by using the train_test_split() method of the sklearn library.
After the split, I ended up with the following two datasets:

* Training set: 60501 images
* Validation set: 25929 images

#### 3. Describe, and identify where in your code, what your final model architecture looks like including model type, layers, layer sizes, connectivity, etc.) Consider including a diagram and/or table describing the final model.

The code for my final model is located in the cells number 17, 18 and 19.

My final model consisted of the following layers:

| Layer         		|     Description	        					| 
|:---------------------:|:---------------------------------------------:| 
| Input         		| 32x32x1 (grey image)   							| 
| Convolution 5x5     	| 1x1 stride, valid padding, outputs 28x28x6 	|
| Relu		|       									|
| Max pooling	      	| 2x2 stride,  outputs 14x14x6 				|
| Convolution 5x5     	| 1x1 stride, valid padding, outputs 10x10x16 	|
| Relu		|      									|
| Max pooling	      	| 2x2 stride,  outputs 5x5x16 				|
| Flatten	      	| input 5x5x16,  outputs 400 				|
| Fully connected		| input 400, output 120       									|
| Relu		|      									|
| Dropout		| prob = 0.5 (only for training)        									|
| Fully connected		|   input 120, output 84     									|
| Relu		|        									|
| Dropout		|  prob = 0.5 (only for training)          									|
| Fully connected		|      input 84, output 43									|

I set the dropout probability to 0.5 during the training phase, while it is 1 (i.e. no dropout) during test and validation.

#### 4. Describe how, and identify where in your code, you trained your model. The discussion can include the type of optimizer, the batch size, number of epochs and any hyperparameters such as learning rate.

The training pipeline is implemented in the cells number 20-24.

I considered 10 epochs and a batch size equals to 128. I used the reduce_mean() function to evaluate the loss and the Adam optimizer from the tensorflow library. 

The learning rate has an initial value of 0.0005 that decreases of 10% every 4 epochs.

#### 5. Describe the approach taken for finding a solution. Include in the discussion the results on the training, validation and test sets and where in the code these were calculated. Your approach may have been an iterative process, in which case, outline the steps you took to get to the final solution and why you chose those steps. Perhaps your solution involved an already well known implementation or architecture. In this case, discuss why you think the architecture is suitable for the current problem.

The code for calculating the accuracy of the model is located in the cell 25 of the Ipython notebook.

My final model results were:
* training set accuracy of 95%
* validation set accuracy of 94%
* test set accuracy of 88%

Using the experience I got from the LeNet lab I used the same model to implement the cnn. I chose such a model since I 
had some familiarities with it and since its performace is actually good. Moreover, since I am facing an image recognition
problem a cnn is much more efficinet than a simple neural net.

I did several trials by changing the number of convolutional and linear layers as well as by changing the number of epochs, 
the batch size and the training rate. I noticed that with a lower batch size the cnn accuracy was lower. In particular
I needed much more epochs in order to reach a good enough accuracy value of the cnn. I got the same
result by enhancing the learning rate (wihtout updating it) and keeping the same value for the bact size. After all that trials I fixed the hyperparameters value at the ones I reported above.

### Test a Model on New Images

#### 1. Choose five German traffic signs found on the web and provide them in the report. For each image, discuss what quality or qualities might be difficult to classify.

Here are five German traffic signs that I found on the web:

![alt text](https://github.com/fvmassoli/fvmassoli-CarND-Traffic-Sign-Classifier-Project-2/blob/master/web_images/sign1.jpg " ") ![alt text](https://github.com/fvmassoli/fvmassoli-CarND-Traffic-Sign-Classifier-Project-2/blob/master/web_images/sign2.jpg " ") ![alt text](https://github.com/fvmassoli/fvmassoli-CarND-Traffic-Sign-Classifier-Project-2/blob/master/web_images/sign3.jpg " ") 
![alt text](https://github.com/fvmassoli/fvmassoli-CarND-Traffic-Sign-Classifier-Project-2/blob/master/web_images/sign4.jpg " ") ![alt text](https://github.com/fvmassoli/fvmassoli-CarND-Traffic-Sign-Classifier-Project-2/blob/master/web_images/sign5.jpg " ")

The first image might be difficult to classify because since there is second sign just behind the front oen and that can confuse the neural net about the real shape of the sign.

The second, third and fourth images might be difficult to classify because of the background of the image.

The fifth image might be difficult to classify because of its orientation with respect to the front view.

#### 2. Discuss the model's predictions on these new traffic signs and compare the results to predicting on the test set. Identify where in your code predictions were made. At a minimum, discuss what the predictions were, the accuracy on these new predictions, and compare the accuracy to the accuracy on the test set (OPTIONAL: Discuss the results in more detail as described in the "Stand Out Suggestions" part of the rubric).

The code for making predictions on my final model is located in the cells number 28-30 of the Ipython notebook.

Here are the results of the prediction:

| Image			        |     Prediction	        					| 
|:---------------------:|:---------------------------------------------:| 
| No entry      		| No entry   									| 
| Yield    			| Yield										|
|Right-of-way at the next intersection					| Right-of-way at the next intersection											|
| Priority road	      		| Priority road					 				|
| roundabout mandatory			|  End of no passing      							|

The model was able to correctly guess 4 of the 5 traffic signs, which gives an accuracy of 80%. Even though such a number seems to compare favorably to the accuracy on the test set of 88% I would not say any final word on such a result. This is 
mainly due to the following reason:

the cnn has been trained and tested using thousands of samples menaning that the error on its accuracy is quite small. Different is the case of the test on the 5 new traffic signals. Wihout knowing any other error on other parameters we can say that the error 
on the accuracy of the 5 new signals has a "step" of 20% since each signal coorectly recognized can give a contribution of +/- 20% to the total
accuracy. Having a such huge error I would say that the new sample is not statistically significant when compared to the ones used during the cnn training and validation.


#### 3. Describe how certain the model is when predicting on each of the five new images by looking at the softmax probabilities for each prediction and identify where in your code softmax probabilities were outputted. Provide the top 5 softmax probabilities for each image along with the sign type of each probability. (OPTIONAL: as described in the "Stand Out Suggestions" part of the rubric, visualizations can also be provided such as bar charts)

The code used to print the top 5 softmax probabilities is located in the cell number 31.

The highest soft max probability for each sign was

| Probability         	|     Prediction	        					| 
|:---------------------:|:---------------------------------------------:| 
| 34.1%         			| No entry  									| 
| 12.8%     				| Yield 										|
| 21.8%					| Right-of-way at the next intersection											|
| 7.4%	      			| Priority road					 				|
| 5.0%				    | End of no passing      							|

The following images show the top 5 softmax probabilities for each image

![alt text](https://github.com/fvmassoli/fvmassoli-CarND-Traffic-Sign-Classifier-Project-2/blob/master/softmax_fig_1.png " ") 


![alt text](https://github.com/fvmassoli/fvmassoli-CarND-Traffic-Sign-Classifier-Project-2/blob/master/softmax_fig_2.png " ") 


![alt text](https://github.com/fvmassoli/fvmassoli-CarND-Traffic-Sign-Classifier-Project-2/blob/master/softmax_fig_3.png " ") 


![alt text](https://github.com/fvmassoli/fvmassoli-CarND-Traffic-Sign-Classifier-Project-2/blob/master/softmax_fig_4.png " ") 


![alt text](https://github.com/fvmassoli/fvmassoli-CarND-Traffic-Sign-Classifier-Project-2/blob/master/softmax_fig_5.png " ") 


