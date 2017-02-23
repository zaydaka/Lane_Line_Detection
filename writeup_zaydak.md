#**Finding Lane Lines on the Road**
##Andrew Zaydak

The goals of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on the assumptions and shortcomings of the pipeline

[//]: # (Image References)


[example_1]: ./report_images/example_1.jpg "Original Image"
[example_2]: ./report_images/example_2.jpg "Canny Image"
[example_3]: ./report_images/example_3.jpg "Masked Canny Image"
[example_4]: ./report_images/example_4.jpg "Final Image with Hough Line Detection"


The pipeline consisted of 5 steps. 
* Conversion to Grayscale
* Gaussian Blurring
* Canny Edge Detection
* Image Masking
* Hough Line Detection

A number of parameters needed to be set to fully define the pipeline:
First, a kernel size of 3 pixels was used for Gaussian blurring to remove high frequency noise from the image. Secondly, after a little bit of testing it was found that thresholds of 100 and 200 for the Canny edge detection were sufficient for this application. Next, vertices were defined to bound the area of interest.  This is the area lane lines are expected to fall within the image and is dependent on camera perspective. It was assumed that the camera position and orientation would not change with respect to the car.  Additionally, it is assumed that the car would generally stay between the left and right lane lines.  The final assumption assumes that the road generally extends to the horizon and the horizon’s position within the image does not change much. Finally, Hough Line detection was used to find potential lane lines from the Canny edges.  The distance and angular resolution were kept at default 1 pixel and 1 degree respectively. From trial and error testing, threshold, minimum line length, and maximum line gap were set to 1 vote, 15 pixels, and 2 pixels respectively.

In order to draw one left and one right lane line, which extends the to the extents of the lanes, I modified the draw_lines helper function using the following steps:
* Computed the slope of each line
* Lines with negative slopes were marked as potential lines associated with the left lane line
* Lines with positive slopes were marked as potential lines associated with the right lane line
* Lines close to horizontal (i.e. slopes close to zero) were filtered out
* The remaining lines were used as input to the numpy polyfit function to fit a first order polynomial to represent each the left and right lanes
* The polynomials were used to find the point which the lane lines reach the y-limits of the area of interest.

Examples of the pipeline are as follows:

![Raw Image][example_1]
![Canny Edge Image][example_2]
![Masked Image][example_3]
![Final Result][example_4]


###2. Pipeline Shortcomings
A number of assumptions were made that make this pipeline. First, there is an assumption about the camera's potion and orientation with respect to the car.  Generally this is ok because a camera calibration process normally used in sensory systems such as this.  However, if the car were to change lanes then the lanes may move outside of the area of interest. Additionally, very steep hills or very sharp turns move the lane lines out of the area of interest.  This pipeline is also not resilient to occlusions in the field of view such as another car passing closely in front of the camera.  Other environmental factors may affect the pipeline's performance such as lighting and wheatear conditions.


###3. Suggest possible improvements to your pipeline
The assumption that the slopes of the lane lines within the image only fall into a specific range can be removed if a RANSAC algorithm is used to remove outliers.  Also, averaging of the lines between frames can be used to improve stability of the lines.  Color masking to remove edges that fall along colors that are not white or yellow (typical colors of lane lines) can be used to remove outliers in the Hough output.  More advanced methods can be used such as using multiple cameras and other sensors can make this system more robust.