# **Finding Lane Lines on the Road** 

## Henry Yau

## Sept. 8, 2017

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image2]: ./writeup_images/challenge_threshBinar_HSV.jpg "HSV"


---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

In the challenge video, the vehicle passes over a concrete bridge which is lighter than the rest of the asphalt and also encounters trees which cast shadows on the roadway which make the asphalt much darker. So the first step in the pipeline is to modify the image to make the painted lines more apparent. This is done using OpenCV function cv2.threshold with the THRESH_BINARY style. 

This produces a very high contrast image which shows the lane markings very clearly as shown below.

![alt text][image2]

The image is then converted to grayscale. A Gaussian blur is applied to the grayscale image and then Canny edge detection is applied to the resultant image. 

A triangular mask is applied to the Canny edge image which masks out the area outside of the targeted lane marking. 

Lines within the image are found using openCV function cv2.HoughLinesP.

To draw single lines for each of the left and right lanes, a new function hough_lines_ave_extrapolate() is made which divides the collection of lines computed by cv2.HoughLinesP between the left and right.

The squared length of each line segment is computed and is used as a weight to compute the weighted average of all slopes and end points of each line segment of both the left and right lanes. This means that longer lines (which are likely to be the actual lane markings) are weighed more heavily than short lines (which may be the result of noise or the horizontal sections of the lane markings). 

The average positions and slopes are then used to extrapolate a line segment defined by the extents of the y-coordinates (ymax/2+50 to ymax) by computing the corresponding x-coordinates.

The left and right line segments are drawn onto a blank frame which is then composited with the original frame to create the final output image.

### 2. Identify potential shortcomings with your current pipeline

All the test videos provide a clear view of the lane markings with no vehicles directly in front of the camera. If a vehicle was directly in the middle of the frame, the Canny edge detection would likely find many features which are not related to the lane markers. Vehicles may also obstruct the view of the lane markers when too close or when changing lanes.

Also if the lighting conditions were different, due to time of day or weather conditions, the current method would also likely have trouble as the settings were manually tweaked to perform well using the test videos. Any type of ground cover, such as rain or snow, would also cause trouble.


### 3. Suggest possible improvements to your pipeline

Dashed lines generally cause the drawn lane marker to be slightly jittery. One remedy may be to use a moving average as from frame to frame there is generally little variation.  

Also during tighter bends, a single straight line does not represent the lane well. A multi segmented line or spline could be used. 
