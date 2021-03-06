## Report

**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./report_images/1.png
[image2]: ./report_images/2.png
[image3]: ./report_images/3.png
[image4]: ./report_images/4.png
[image5]: ./report_images/5.png
[image6]: ./report_images/6.png
[image7]: ./report_images/7.png
[image8]: ./report_images/8.png
[image9]: ./report_images/9.png
[image10]: ./report_images/10.png
[image11]: ./report_images/11.png

[video1]: ./out_project_video.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Vehicle-Detection/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Histogram of Oriented Gradients (HOG)

#### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in the first section of the IPython notebook named 'Histogram of Oriented Gradients (HOG)' .  

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][image1]

I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `RGB` `YCrCb` color space and HOG parameters of `orientations=8`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:


![alt text][image2]

![alt text][image3]

#### 2. Explain how you settled on your final choice of HOG parameters.

I tried various combinations of parameters and get the final params map as follows:

	'resizeh': 64, # resize image height before feat extraction     
	'resizew': 64, # resize image height before feat extraction
	'colorspace': 'YCrCb', # Can be RGB, HSV, LUV, HLS, YUV, YCrCb 
	'orient': 9, # HOG orientations 
	'pixpercell': 8, # HOG pixels per cell 
	'cellperblock': 2, # HOG cells per block 
	'hogchannel': "ALL", # Can be 0, 1, 2, or "ALL" 
	'spatialsize': (32, 32), # Spatial binning dimensions 
	'histbins': 16 # Number of histogram bins 

#### 3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

To train the classifier includes several steps:

1. Read all images from cars and not-cars floders.
2. Extract hog features, spital bin and color histogram features from the images.
3. Shuffle the dataset and train the SVM model.

The code is included in the 3 sub-part of 'Histogram of Oriented Gradients (HOG)' section.

### Sliding Window Search

#### 1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

In this section we mainly discuss the way of sliding window search and detect the cars in the windows. Here are some points we should look into:

1. The cars usually come out in the lower region of the images, so we will focus on detecting cars in the lower region.

2. The hog features extraction is a resource-consuming operation but luckily we can sub-sample the hog features of the whole image and get the result we want.

At first we extract the features for each image crop and see what we can get.

![alt text][image4]

Then we extract the hog feature out of the whole image and sub-sample the hog feature of the image crops out of the result we get to see what the performance can change.

![alt text][image5]

#### 2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

Ultimately I searched on several scales (1.0, 1.5, 2.0, 2.5) using YCrCb 3-channel HOG features plus spatially binned color and histograms of color in the feature vector, which provided a fine result.  Here are some example images:

![alt text][image6]

![alt text][image7]

![alt text][image8]

From the examples we can find out that the cars are detected but in the mean time there are false alerms in the images and we can try to get rid of them in the video implementation using heatmap and threshold.

---

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./project_video.mp4)


#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

Here's an example result showing the heatmap from a series of frames of video, the result of `scipy.ndimage.measurements.label()` and the bounding boxes then overlaid on the last frame of video:

### Here are one frame and its corresponding heatmap:

![alt text][image9]

### Here is the output of `scipy.ndimage.measurements.label()` on the heatmap:
![alt text][image10]

### Here the resulting bounding boxes:
![alt text][image11]


---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

I used a 3 frame buffer to record the heatmaps of each frame and add them as the combined heatmap to build a pipeline with memory. But with many param tuning there are always some false positive situations I cannot get rid of. Maybe using some deep learning method I can get a better result.

