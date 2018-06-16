# Vehicle Detection Project README

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./examples/car_not_car.png
[image2]: ./examples/HOG_example.jpg
[image3]: ./examples/sliding_windows.jpg
[image4]: ./examples/sliding_window.jpg
[image5]: ./output.jpg
[image6]: ./examples/labels_map.png
[image7]: ./examples/output_bboxes.png
[video1]: ./project_video.mp4

## Feature extraction 

The images were converted into different colorspaces, and the HOG of all three channels were taken as input into the feature vector. Sample images from the vehicle and non-vehicle data set were visualized to determine the colorspace which provided greatest contrast when the HOG was applied. 

I then explored different color spaces and  different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

After experimenting with several colorspaces, the YCrCb colorspace was found to produce the best results. The feature vector was scaled and normalized to zero mean and unit variance. 

Using the HOG features as input to the feature vector, an SVM was used to train a classifier. Both data from the vehicle and non-vehicle datasets were used and split into a training and test set for the classifier. The accuracy of the classifier on the test set was 97.3%. 

## Pipeline 
The logic below describes the pipeline used to detect vehicles on the road. 

### Sliding Window Search
A sliding window search was implemented to grid search the bottom half of the image for sections of the image which resembled a car. This was determined through the classifier trained in the section previously. 

Ultimately I searched using the 3-channel YCrCb HOG features. To prevent false positives, the windows detected as vehicles were stored into a list, and superimposed to form a heatmap. From the positive regions, a heatmap was created and thresholded to identify vehicle positions. Individual blobs in the heatmap was identified using the label function in scipy.ndimage.measurements. Assuming each blob to correspond to a vehicle, regions of high overlap were determined to be vehicles. A bounding box was drawn across the boundary of the region with high overlap. Here are some examples of the heatmap: 

Here's an example result showing the output of the bounding box from a test image: 

![alt text][image5]

In addition, the final labelled boxes from each video frame was stored in a queue for 5 consecutive frames. A heatmap was then formed for these labelled boxes, to find the identified vehicle areas over 5 frames. Areas in which a vehicle was constantly detected were identified as vehicular areas and were then marked. 

---

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./project_video.mp4)

---

### Discussion

This section will discuss some challenges faced in the pipeline. 

1. Like the lane-finding project, the position of the bounding boxes changes each frame, possibly due to the lighting changes each frame. This may be stabilized by overlapping newly found bounding boxes with the existing bounding boxes in the heatmap, and adjusting the threshold accordingly. 

2. The test accuracy of the SVM may be high, i.e. close to 100%, but this may be due to overfitting. This is noticeable during the video processing, as the SVM does not detect the vehicle regions as accurately as observed during test accuracy. 
