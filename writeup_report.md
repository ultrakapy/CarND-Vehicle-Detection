## Summary

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
[image1a]: output_images/image0240.png
[image1b]: output_images/image3223.png
[image2a]: output_images/hog_image_before.png
[image2b]: output_images/hog_image_after.png
[image3]: output_images/sliding_window_search1.png
[image4]: output_images/detection_only_cars.png
[image5]: output_images/heatmap1.png
[image6]: output_images/heatmap2.png
[image7]: ./examples/output_bboxes.png
[video1]: ./project_video.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Histogram of Oriented Gradients (HOG)

The code for this step is contained in the training code cell (#298) of the IPython notebook `P5.ipynb`.  

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][image1a]
![alt text][image1b]

I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `YCrCb` color space and HOG parameters of `orientations=9`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:


![alt text][image2a]
![alt text][image2b]

I tried various combinations of parameters and settled on the following because they gave a high accuracy during training and testing (testing accuracy of 0.9832):
* Color Space: 'YCrCb'
* HOG orientations: 9 
* HOG pixels per cell: 8
* HOG cells per block: 2 
* HOG channel: ALL
* Spatial size: (32, 32)
* Histogram bins: 32

I trained a linear SVM using the above HOG features and color features and got a testing accuracy of 0.9832.

### Sliding Window Search

I decided to use the find_cars method provided during class to implement the sliding window search using two scales: 1.5 and 2 with 75% overlap. I chose to use the find_cars method as it is efficient by allowing us to only have to extract the Hog features once. Here's an example of the result of applying the sliding window search using find_cars (before applying any heat map thresholding to filter out false positives):

![alt text][image3]

I searched on two scales using YCrCb 3-channel HOG features plus spatially binned color and histograms of color in the feature vector, which provided a nice result.  Here is an example:

![alt text][image4]
---

### Video Implementation

Here's a [link to my video result](./test_videos_output/project_video_output.mp4)


I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

Here's an example result showing the heatmap from a frame of video, the result of `scipy.ndimage.measurements.label()` and the bounding boxes then overlaid on the last frame of video:

### Here is a frame and its corresponding heatmap:

![alt text][image5]

### Here is the output of `scipy.ndimage.measurements.label()` on the integrated heatmap from the frames:
![alt text][image6]

### Here the resulting bounding boxes are drawn onto the last frame in the series:
![alt text][image4]



---

### Discussion

In order to minimize false positives, I applied the heat map across multiple frames (giving higher weight to the current frame and lower weight to the previous frames). It was challenging to find the right threshold that reduces false positives while accurately detecting actual vehicles. Finally, I was able to integrate my lane finding functionality from the last project into this vehicle detection project.

