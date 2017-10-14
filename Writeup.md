##Writeup Template
###You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

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
[image1]: ./examples/car_not_car.png
[image2]: ./examples/hog.png
[image3]: ./examples/norm.png
[image4]: ./examples/sliding_windows.png
[image5]: ./examples/sliding_window.png
[image6]: ./examples/bboxes.png
[image7]: ./examples/label.png
[video1]: ./project_output_video.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
###Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
###Writeup / README

####1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Vehicle-Detection/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

###Histogram of Oriented Gradients (HOG)

####1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in the first code cell of the IPython notebook.  

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][image1]

I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `YCrCb` color space and HOG parameters of `orientations=9`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:


![alt text][image2]

####2. Explain how you settled on your final choice of HOG parameters.

I tried 27 combinations of parameters for the above car image. I found that the combination No.13-15(orient:9,pix:8,cell:1;orient:9,pix:8,cell:2;orient:9,pix:8,cell:3) can give me more reasonable details. I judged it by my eyes and I chose the combination No.14 at last

####3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I chose the feature and trained my classifier in the code block no.5-7 of my notebook.

In the block no.5, I transfer my image to 'YCrCb' color space. I extracted the HOG features from all the channels with the parameter picked up before. In addition, I also extracted (32,32) spatial information and the color histogram information with the hist bins of 16 and range of (0,1)
I apply this strategy to both the extraction of car images and notcar images. After that, as you see in code block no.6, I normalized the feature so it is easier to train a classifier.

![alt text][image3]

At last, I randomly split my data to training and test sets and used the GridSearchCV strategy to train my SVC(). The range of kernel is "linear" or "rbf" and the range of 'C' is 1 or 10.

The training time is 3779.58 seconds and the test accuracy is 0.9944.
###Sliding Window Search

####1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

I decided to use hog sub-sampling window search strategy. The search size is controlled by the scale. The following image is the scale of 1.5:

![alt text][image4]

I also limited the y-direction searching range to (400,656). Finally I decided to run different scales (np.linspace(1.5, 2.2, 7)) for each image and I think this is good to find cars both in close place and far away.

####2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

Ultimately I searched on two scales using YCrCb 3-channel HOG features plus spatially binned color and histograms of color in the feature vector, which provided a nice result.  Here are some example images:

![alt text][image5]
---

### Video Implementation

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./project_output_video.mp4)


####2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

Here's an example result showing the heatmap from a series of frames of video, the result of `scipy.ndimage.measurements.label()` and the bounding boxes then overlaid on the last frame of video:

### Here are six frames and their corresponding heatmaps:

![alt text][image6]

### Here the resulting bounding boxes are drawn onto the last frame in the series:
![alt text][image7]



---

###Discussion

####1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.  

I spent much time training a classifier. However, I found that the searching strategy is really important for a classifier to work very well. I found that even though my classifier performed very well

on the test set, it may fail when car moves further. In the future, I will find and try more efficient searching strategy to detect more cars. In addition, it takes a long time to work on an image because

of very dense searching strategy. This will also be optimized in the future.

