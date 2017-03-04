## Advanced Lane Finding Project Writeup
[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)

---

**Advanced Lane Finding Project**

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[image1]: ./output_images/DistVsUndistChess.png "Undistorted"
[image2]: ./output_images/DistVsUndistRoad.png "Road Transformed"
[image3]: ./output_images/BinaryThresholded.png "Binary Example"
[image4]: ./output_images/PersTransTest.png "Warp Example"
[image5]: ./output_images/SlidingWindow.png "Fit Visual"
[image6]: ./output_images/pipelineOutput.png "Output"
[video1]: ./project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points
###Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
###Camera Calibration

####1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first code cell of the IPython notebook located in "./Project-Notebook.ipynb".  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

###Pipeline (single images)

####1. Distortion-Corrected Road Image
I used the camera calibration adn distortion coefficiets to be undistort the road image as shown in the example below:

![alt text][image2]

####2. Binary Thresholed Image
I used a combination of color and gradient thresholds to generate a binary image (thresholding steps in third code cell in IPython notebook).  I used interactive sliders to be able to tune the different thresholds and parameters to come up with the bes possible binary image that clearly shows the lane lines. The parameters and thresholds chosen are in the following table:

| Parameter/Threhshold   | Value   | 
|:----------------------:|:-------:| 
| ksize                  | 15      | 
| min x gradient         | 23      |
| max x gradient         | 180     |
| min y gradient         | 27      |
| max y gradient         | 187     |
| min magnitude          | 16      |
| max magnitude          | 180     |
| min direction          | 0.7     |
| max direction          | 1.3     |
| min color threshold    | 142     |
| max color threshold    | 255     |

Here's an example of my output for this step:

![alt text][image3]

####3. Perspective Transformation

The code for my perspective transform includes a function called `perspectiveTransform()`, which appears in 4th code cell of the IPython notebook. The `perspectiveTransform()` function takes as inputs an image (`image`).  I chose the hardcode the source and destination points in the following manner:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 160, 720      | 300, 720      | 
| 1150, 720     | 300, 720      |
| 580, 450      | 300, 0        |
| 700, 450      | 900, 0        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

####4. Lane-Line Pixels Identifications and Polynomial Fitting

After creating the binary-thresholded image and applying perspective transform, I used a histogram across the x-axis to detect peaks that would indicate the left and right lines of the lane. Afterwards, a sliding window was used to follow the line from the bottom to the top of the image. A second degree polynomial is then used to fit the lane lines. The visulaization of the sliding window and fitted polynomials is shown below.

![alt text][image5]

####5. Radius of Curvature and Car-Lane Center Offset Calculation 

For calculating the radius of curvature, I followed the tutorial in this [link](http://www.intmath.com/applications-differentiation/8-radius-curvature.php). The offset is calculated by subtracting the lane center from the center of the image. When calculating both values, I had to take into consideration transforming the pixel space into the real world space. The code is in code cell 10 in the iPython notebook.


####6. Pipeline Output

After finding the left and right polynomial fits, the radius of curvature, and offset, I projected the measurements back onto the original undistorted road image. I implemented this step in cell code 11 in the IPython notebook in the function `drawResult()`.  Here is an example of my result on a test image:

![alt text][image6]

---

###Pipeline (video)

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video.mp4)

---

###Discussion

####1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.  

