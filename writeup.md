##Writeup
###Luke Walker

---

**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector.
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./output_images/car_not_car.png
[image2]: ./output_images/HOG_example.png
[image3]: ./output_images/sliding_windows.png
[image4]: ./output_images/sliding_window.png
[image5]: ./output_images/bboxes_and_heat.png
[video1]: ./project_video.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
###Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
###Writeup / README

####1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Vehicle-Detection/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.

You're reading it!

The code for all steps is contained in the P5 Ipython notebook layed out into sections.

###Histogram of Oriented Gradients (HOG)

####1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in the first several sections code cell of the IPython notebook P5.  

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes. There were 8792 vehicle images and 8968 non vehicle images.

![alt text][image1]

I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `YCrCb` color space and HOG parameters of `orientations=6`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`, and `hog_channel = 6`' for a '`vehicle` and `non-vehicle` image:


![alt text][image2]

####2. Explain how you settled on your final choice of HOG parameters.

I tried various combinations of parameters and found that based on reading, the lessons, project walkthrough, and experimentation I was able to make out that the car hog features resembled a car and the not car hog image looked nothing alike. This was repeatable and I thought would work well for tracking vehicles.

####3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I trained a linear SVM using the scikit learn LinearSVC() function. For features I used the HOG as well as spatial and histogram features. It took 48.78 seconds to train the classifier using 9 orientations, 8 pixels per cell, 2 cells per block, 32 histogram bins, and 32x32 spatial sampling. This made a feature vector that was 8460 and I got an accuracy of 0.9916.

I then tested the linear SVC on the YCrCb color space with a 50% overlap. I found that this accurately identified the cars but did result in some false positives.

###Sliding Window Search

####1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

For the sliding window search after some experimentation I decided to search with a 50% overlap. I also decdied to use a 64x64 window since it picked up the cars at the scale they were generally seen in the video without getting to many false positives. I also decided to only search from y = 400 to y = 656 to eliminate the number of windows to search and speed up the analysis. A picture of the sliding windows I used can be seen below.

![alt text][image3]

####2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

Ultimately I searched on a single scales using YCrCb 3-channel HOG features plus spatially binned color and histograms of color in the feature vector, which provided a nice result. I was able to search 100 windows in less than 1 second. For real time video this would not be fast enough, but for this project processing time is not a limiting factor.  Here are some example images:

![alt text][image4]
---

### Video Implementation

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./project_video_output.mp4)


####2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

Here's an example result showing the heatmap from a series of frames of video, the result of `scipy.ndimage.measurements.label()` and the bounding boxes then overlaid on the last frame of video:

### Here are six frames and their corresponding heatmaps and bounding boxes:

![alt text][image5]



---

###Discussion

####1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

I enjoyed this project and found the results to be very interesting. I had never played around with HOG features before, but can see how useful they would be.

I think one big improvement I could make would be to try to reduce the size of my feature vector so that the processing time would go down.

I could search also change the overlap in the x and y direction so that the number of windows to search went down.

To make my pipeline more robust in addition to using the heatmap and thresholding to eliminate false positives I could also use multiple frames and only draw the bounding boxes if I got a positive on multiple sucessive frames.
