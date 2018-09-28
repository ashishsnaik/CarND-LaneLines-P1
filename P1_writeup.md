# **Finding Lane Lines on the Road** 
---
**The goals / steps of this project are the following:**
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image10]: ./examples/grayscale.jpg "Grayscale"
[image0]: ./test_images/solidWhiteRight.jpg
[image1]: ./pipeline_step_images/solidWhiteRight_gray.jpg
[image2]: ./pipeline_step_images/solidWhiteRight_blur_gray.jpg
[image3]: ./pipeline_step_images/solidWhiteRight_edges.jpg
[image4]: ./pipeline_step_images/solidWhiteRight_masked_edges.jpg
[image5]: ./pipeline_step_images/solidWhiteRight_line_image.jpg
[image6]: ./pipeline_step_images/solidWhiteRight_lane_lines_image.jpg

---

### **Reflection**

#### **1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.**

**The Pipeline**

The pipeline takes a color image as input and returns a copy of the image with lane lines drawn on it. My pipeline consisted of the following 5 sequential steps: 

1. convert the image copy to grayscale

2. apply Gaussian smoothing in order to remove the noise in the image

3. use Canny Edge Detection algorithm to find edges in the image

4. define and apply a region-of-interest mask to the image, so that only the detected edges in the region of interest persist

5. apply Hough transform to find line segments from the detected edges and then extrapolate the line segments to map out the left and right lane lines (the latter done using the draw_lines( ) function described below)

**The draw_lines( ) Function**

The purpose of the draw_lines( ) function, is to draw a single, solid line over the left lane line and a single, solid line over the right lane line, both lines starting from the bottom of the image and extending out to the top of the region of interest. The function, as provided, drew all the Hough transform line segments. In order to achieve what was needed, I modified the function to do the following:

1. separate the Hough line segments into set of segments with negative and positive slopes. Given the origin (0, 0) is at the left top corner, negative slope segments represent the left lane and positive slope segments the right.

2. for both, left-lane and right-lane segment sets, fit a least-squares first-degree polynomial, to approximate a single line for each lane.

3. define first-degree polynomials for left and right lanes, with respective slopes and intercepts, to be used later to plot lane lines on the image.

4. calculate the x-coordinate min and max values required for both lane lines, based on the coefficients of the fitted lines

5. draw the left and right lane lines with min and max x-coordinates and y-coordinate calculated from the first-degree polynomial defined in #3

Below images show outputs of the pipeline steps run on a sample image:



Test Image|
:--------:|
![][image0]|

Gray-scaling               |Gaussian Smoothing
:-------------------------:|:-------------------------:
![][image1]                |![][image2]

Canny Edge Detection       |Region Masking
:-------------------------:|:-------------------------:
![][image3]                |![][image4]

Extrapolated Line Image    |Superimposed Lane Lines
:-------------------------:|:-------------------------:
![][image5]                |![][image6]

#### **2. Identify potential shortcomings with your current pipeline**

**Potential shortcomings of the current pipeline**

The pipeline finds and marks lane lines based on a first-degree polynomial, essentially working with only straight lines in the image. One potential shortcoming here would be the inability of the current pipeline to find lanes that curve. Also, with curved lanes, the current way of identifying the sets of left and right line segments using the slope will be erroneous as the line segments identified by the Houghs transform for one (or both) of the lane lines could have positive as well as negative slopes, depending on how the curve turns.  

Another potential shortcoming could be that the pipeline has a fixed region of interest (RoI), with the 'line of sight' fixed to a certain height in the image. This will limit how far ahead the vehicle can "see" when approaching a gradient. For example, when approaching a steep up-hill gradient, the vehicle would see the road only till a shorter distance and might want to have the RoI height a little higher so as to see as much of the road ahead as possible in order to make better decisions.

Also, the pipeline is currently tailored to the test images/videos that show images with similar road, weather, and lighting conditions. The parameters set for Canny edge detection and Hough transform are currently hard-coded to specific values and may not work best for images/videos with varied environmental conditions - for eg. with worn-out (or missing) road markings, cloudy/foggy weather, low-light or night time with vehicle head-lights turned on.

#### **3. Suggest possible improvements to your pipeline**

**Possible improvements**

One possible improvement would be to use a higher degree polynomial to detect the lanes, instead of a first degree polynomial, so it would be possible to detect curved lane lines as well.

Another potential improvement could be to use some technique to dynamically change the region of interest based on some parameters or input/sensor data. Similarly, improvements could also be used to have dynamically adjustable Canny and Hough thresholds as well.

