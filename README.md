**P5-Vehicle Detection**
[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: https://github.com/christianreiser/P5-vehicle-detection/blob/master/output_images/data.png
[image2]: https://github.com/christianreiser/P5-vehicle-detection/blob/master/output_images/HOG.png
[image3]: ./examples/sliding_window.jpg
[image4]: ./examples/sliding_window.jpg
[image5]: https://github.com/christianreiser/P5-vehicle-detection/blob/master/output_images/heat.png
[image6]: ./examples/labels_map.png
[image7]: ./examples/output_bboxes.png
[video1]: https://github.com/christianreiser/P5-vehicle-detection/blob/master/output_images/video2.mp4

###Histogram of Oriented Gradients (HOG)

####1. Extraction of HOG features from the training images.

The code for this step is contained in the 5. code cell of the IPython notebook. I started by reading in all the `vehicle` and `non-vehicle` images. The images are from the [GTI vehicle image database](http://www.gti.ssr.upm.es/data/Vehicle_database.html) as well as the [KITTI vision benchmark suite](http://www.cvlibs.net/datasets/kitti/).

  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][image1]

I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed a images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `YCrCb` color space and HOG parameters of `orientations=10`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:


![alt text][image2]

####2. How I I chose the HOG parameters.
I tried to systematically explore which parameters I should use. Therefore I fixed every parameter except the one I testing and tried many different falues for this one parameter. Then I chose the value with the best result. I used this method for every parameter. 
This method had two downsides: First, the testing accuracy was always well above 099 and often even 1.0. Second, I didn't try every possible parameter combination, consequently there are probably better parameters.

####3. I also trained my classifier in the 5th cell of th Ipython notebook and used the SVM as a linear classifier to selected the HOG features.

###Sliding Window Search

####1. Do detect vehicles in larger images I used the sliding window technique. It's implemented in the 7th cell and I  decided to set the scale of the windwos to one because every other value other than one led a increased number of false positives as well as false negatives. I'm not sure why, because even a value of 1.00000000001 led horrible results. 
THe overlap between to windows is relatively big since the window moves one cell per step, only. This led to longer computing times but vewer false negatives.
Cars are not flying, yet ;) Therefore the sliding windows were only applied to areas where cars usually are. The first windows start slightly higher than the horizon and end close to the own cars hood. 

![alt text][image3]


![alt text][image4]
---

### Video Implementation

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](https://youtu.be/AJiiHgcJPfY)


####2. In cell 8 I implemented a heatmap which should reduce the number of false positives by applying a threshold.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

Here's an example result showing the heatmap, the result of `scipy.ndimage.measurements.label()` and the bounding boxes then overlaid:

![alt text][image5]





---

###Discussion

####1. Problems:
Sometimes a car gets detected twice and the rectangles are are not overlapping. Since they are not overlapping `scipy.ndimage.measurements.label()` also plots two new rectangles on just one car. The window size is probably no small, however, bigger images lead to worse results. 
Here is a example where the scale is set to 2:
[image5]: https://github.com/christianreiser/P5-vehicle-detection/blob/master/output_images/size2.png
