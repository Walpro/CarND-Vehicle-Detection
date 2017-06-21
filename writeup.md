
** Vehicle Detection Project **

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./img1.png
[image2]: ./img2.png
[image3]: ./img3.jpg

[video1]: ./test_output.mp4
[video2]: ./project_output.mp4


## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

### Histogram of Oriented Gradients (HOG)

#### 1. Extracttion HOG features.

The code for this step is contained in the first code cell of the IPython notebook(1. Histogram of Oriented Gradients (HOG))

I started by reading in all the test images and using the `skimage.hog()` function, I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I appllied the transform on all the test images and displayed them to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `YCrCb` color space and HOG parameters of `orientations=9`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:


![HOG Features][image1]

#### 2.Final choice of HOG parameters.

I tried various combinations of parameters and selected the parameters that allows to detect the dominant features of a car that makes it stand out from non cars images.
To extend the detected features of the images I combined the HOG features with a spatial binning of color and histograms of the three color channels.

#### 3. Training a classifier using the selected HOG features and color features

In `2. Trainning the classifier` I trained a linear SVM using a data base of cars and non-cars.
The data base contains 8792  vehicles and 8968  non-vehicles, to train the classifier I normalized at first the data and then applied the above detailed HOG and color features on the images. After that I splitted the data randomly into training and testing sets, the testing set consists of 20% of the images.
Finally I Trained the classifier and got a Test Accuracy of 0.9918.

### Sliding Window Search

####1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

In `3. Sliding window search` I slided trhough the down part of the image where cars mostly exist, I applied the above described features and used the already trained classifier to detect whether the window contains a car or not, once it contains one I stored the coordinates of the box to draw on that window.

![alt text][image2]

####2. pipeline example

Ultimately I searched on two scales using YCrCb 3-channel HOG features plus spatially binned color and histograms of color in the feature vector, which provided a nice result.  Here are some example images:

![alt text][image3]
---

### Video Implementation

#### 1. Filter for false positives and method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.

To reduce the number of false positive I summed the boxes positions for many frames, computed the heatmap and applied a high threshold that allows to reject the boxes that does not appear on many frames.

I tested this method on the test_video and below is the pipeline output.
[link to my test result](./test_output.mp4)

#### 2. Link to the final video output.
Here's a [link to my video result](./project_output.mp4)

### Discussion
In this project I used at first only one HOG channel which affected the classifier accuracy and made it hard for it to differentiate between car and non-cars, I resolved the problem by using three HOG channels and combining it with with a spatial binning of chistograms of color. The database I used is rich but it does not contains all the possible vehicles we may encounter on the streets like vehicles for street works or tractors and the classifier would mostly fail to identify them, to remediate that it is possible the database must be extended.
The boxe is sometimes around more than a vehicle when they are near each others, to resolve that a more rigorious approach must be used to analyse the heatmpas.
