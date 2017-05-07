## SDCND Project 4:  Advanced Lane Finding

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

[image1]: ./camera_cal/calibration2.jpg "Undistorted"
[image2]: ./camera_cal/corners_found.png "Undistorted"
[image3]: ./camera_cal/undistorted.png "Undistorted"
[image4]: ./camera_cal/test_undistort.png "Undistorted"
[image5]: ./examples/HSL1.png "Binary Example"
[image6]: ./examples/HSL2.png "Binary Example"
[image7]: ./examples/HSL3.png "Binary Example"
[image8]: ./examples/sobel.png "Binary Example"
[image9]: ./examples/original.png "PersTrans"
[image10]: ./examples/sourcepoint.png "PersTrans"
[image11]: ./examples/perspectivetrans.png "PersTrans"
[image12]: ./examples/dimension.png "PersTrans"
[image13]: ./examples/processed1.png "processed"
[image14]: ./examples/processed2.png "processed"
[image15]: ./examples/processed3.png "processed"
[image16]: ./examples/processed4.png "processed"
[image17]: ./examples/processed5.png "processed"
[image18]: ./examples/processed6.png "processed"
[image19]: ./examples/processed7.png "processed"
[image20]: ./examples/processed8.png "processed"

[image21]: ./examples/greenbox1.png "greenbox"
[image22]: ./examples/greenbox2.png "greenbox"
[image23]: ./examples/greenbox3.png "greenbox"
[image24]: ./examples/greenbox4.png "greenbox"
[image25]: ./examples/greenbox5.png "greenbox"
[image26]: ./examples/greenbox6.png "greenbox"
[image27]: ./examples/greenbox7.png "greenbox"
[image28]: ./examples/greenbox8.png "greenbox"
[image29]: ./examples/greenbox9.png "greenbox"

[image30]: ./examples/fit1.png "curve fit"
[image31]: ./examples/fit2.png "curve fit"
[image32]: ./examples/fit3.png "curve fit"
[image33]: ./examples/fit4.png "curve fit"
[image34]: ./examples/fit5.png "curve fit"
[image35]: ./examples/fit6.png "curve fit"
[image36]: ./examples/fit7.png "curve fit"
[image37]: ./examples/fit8.png "curve fit"
[image38]: ./examples/fit9.png "curve fit"

[image39]: ./examples/result1.png "result"
[image40]: ./examples/result2.png "result"
[image41]: ./examples/result3.png "result"
[image42]: ./examples/result4.png "result"
[image43]: ./examples/result5.png "result"
[image44]: ./examples/result6.png "result"
[image45]: ./examples/result7.png "result"
[image46]: ./examples/result8.png "result"

[image50]: ./examples/histogram1.png "histogram"
[image51]: ./examples/histogram2.png "histogram"
[image52]: ./examples/histogram3.png "histogram"
[image53]: ./examples/histogram4.png "histogram"
[image54]: ./examples/histogram5.png "histogram"
[image55]: ./examples/histogram6.png "histogram"
[image56]: ./examples/histogram7.png "histogram"
[image57]: ./examples/histogram8.png "histogram"
[image58]: ./examples/histogram9.png "histogram"
[image59]: ./examples/histogram10.png "histogram"

[video1]: ./output_video_FINAL.mp4 "Video"
[video2]: ./output_challenge_video.mp4 "Video"
[video3]: ./output_harder_challenge_video_trial3.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it.

### Camera Calibration with Square Chessboard using OpenCV


#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first code cell of the IPython notebook "pipeline.ipynb".  

OpenCV functions were used to calculate the correct camera matrix and distortion coefficients using 20 calibration chessboard images provided in the repository. Below is one example.

![alt text][image1]

First chessboard images were converted to grayscale. Then `cv2.findChessboardCorners()` function was used to find corners where two black square corners meet, and x, y, position of these corners were recorded. Out of the given 20 chessboard images, the corners were found in 17 images and were used for calibration.

![alt text][image2]

These x, y, positions in 2D camera images were compared with actual x, y, z, corner positions in real 3D world. All z values were zero because chessboard corners were all located in the same flat plane in the 3D space.

The function `cv2.calibrateCamera()` was then used to calculate camera matrix and distortion coefficients that can transform distorted camera images to undistorted images.

|Camera Matrix|
|:---------------------:|
|  1.15396093e+03|   0.00000000e+00|   6.69705357e+02   |
|  0.00000000e+00|   1.14802496e+03|   3.85656234e+02   |
|  0.00000000e+00|   0.00000000e+00|   1.00000000e+00   ||


|Distortion Coefficients|
|:---------------------:|
|  -2.41017956e-01|  -5.30721173e-02|  -1.15810355e-03|-1.28318856e-04|   2.67125290e-02  ||


For this project, all the images from video files were undistorted using `cv2.undistort()` function with found camera matrix and distortion coefficients.

 ![alt text][image3]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

Below are the distorted and undistorted images.

![alt text][image4]

It is easy to see how the image is distorted when you compare the traffic sign at the right top corner in the distorted image and undistorted image.

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image. Provide an example of a binary image result.

The code for this step is contained in the "HLS Color Threshold" and "Sobel Threshold" section in the IPython notebook "pipeline.ipynb".  

I have tried HLS color thresholds, gradient magnitude thresholds, and direction thresholds. Below is the example HLS thresholds images. From left, H images, magnitude thresholds H images, L images, S images, magnitude thresholds S images.

![alt text][image5]  
![alt text][image6]  
![alt text][image7]  

Below is the example gradient magnitude thresholds and direction thresholds.

![alt text][image8]

For this project, H and S color thresholds was combined and used to detect lane lines in the images.

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform is in the section "Perspective Transform" in my ipython notebook.

Perspective transform was applied to convert images from driviers-eye view to birds-eye view. An image with relatively flat ground and straight lane lines was chosen so to find a good approximated rectangular shape in the real 3D world projected onto 2D camera images.

![alt text][image9]

Four corners along straight lane lines were used as source points and warped to rectangular destination points in a new 2D image.

![alt text][image10]

Destination points and the size of the warped images were chosen so that the top half of the images were cropped.

| Source        | Destination   |
|:-------------:|:-------------:|
| 1200x720      | 1200x2000     |
| [595,450]      | [430,0]      |
| [680,450]      | [770,0]      |
| [1030,670]     | [770,2100]   |
| [275,670]      | [430,2100]   |


OpenCV function `cv2.getPerspectiveTransform()` was used to calculate transform matrix, and `cv2.warpPerspective()` function was used to apply transform matrix to the image.

![alt text][image11]

Also, x and y scales in this warped image were compared with estimated width of the U.S. highway lane (3.7m) and estimated length of the dashed lines (3.0m).
They were 320 pixels and 200 pixels respectively.

![alt text][image12]

The conversion from pixels to meters were then given as:  
`ym_per_pix = 3/200   # meters per pixel in y dimension`  
`xm_per_pix = 3.7/320 # meters per pixel in x dimension`

Reference:  
 "U.S. regulations that require a minimum lane width of 12 feet or 3.7 meters, and the dashed lane lines are 10 feet or 3 meters long each."
[Radias of curvature of highway road](http://onlinemanuals.txdot.gov/txdotmanuals/rdw/Table_2-3_m.pdf)

Below are the example warped binary images after image preprocessing, un-distortion, HLS color thresholding, and perspective transform.

![alt text][image13]
![alt text][image14]
![alt text][image15]
![alt text][image16]
![alt text][image17]
![alt text][image18]
![alt text][image19]


#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Here are the steps to find lane lines from warped binary images.

1. Slice the image horizontally and search lanes in each layer starting from bottom to top. The image was sliced into 30 layers.
2. In a sliced layer, count pixel values in a column and sums up. This gives a histogram of nonzero values in the layered image. Below are the histogram of the bottom layer.

 ![alt text][image50]
 ![alt text][image51]
 ![alt text][image52]
 ![alt text][image55]
 ![alt text][image56]
 ![alt text][image57]
 ![alt text][image58]
 ![alt text][image59]

3. Apply convolution with a filter size of 50 pixels to the obtained histogram.
4. The maximum values in the output from the convolution indicates the highest density of the nonzero pixels at that location. So find the location of maximum filtered values using `np.argmax()`.  
5. Repeat steps 2-4 in the next top layer. The search area was defined by the previously found lane center positions and specified margin.  
6. This process was continued until 15th layer and center position of the found lane lines in each layer was recorded.

 Next step is to find x and y coordinates of all lane line pixels in binary image to fit a curve.

7. Based on the layer hight and center positions found in the previous steps, rectangular windows were created, which are shown in green box in the image below.

 ![alt text][image21]
 ![alt text][image22]
 ![alt text][image23]
 ![alt text][image24]
 ![alt text][image25]
 ![alt text][image26]
 ![alt text][image27]
 ![alt text][image28]

8. The x and y positions of all the nonzero pixels within the defined green windows were recorded.
9. Fit a curve on the found nonzero pixels using `np.polyfit` function. Coefficients of variance were also obtained, and curve fit errors were calculated.
10. When the width of the detected lane lines were far apart or too close, they were considered to be wrong curve fit. In such a case, the errors of left and right curve fit were compared, and the lane with less error was used as good curve and the other lane was assumed to be parallel to the good curve.

 ![alt text][image30]
 ![alt text][image31]
 ![alt text][image32]
 ![alt text][image33]
 ![alt text][image34]
 ![alt text][image35]
 ![alt text][image36]
 ![alt text][image37]
 ![alt text][image38]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

The code for the calculation of the radius of curvature and the position of the vehicle with respect to lane center can be found in the "Analyze Found Lane Lines" section in ipython notebook.

Given coefficients of quadratic equation for found lane lines in image dimensions, new coefficients in the real world dimensions were calculated using converted x and y positions.

The conversion from pixels to meters were then given as:  
`ym_per_pix = 3/200   # meters per pixel in y dimension`  
`xm_per_pix = 3.7/320 # meters per pixel in x dimension`

Suppose quadratic equation is given as follows:  
f(y) = Ay^2+By+C  

The radius of curvature for second order differential equation is given as follows:  
((1+(2Ay(ym_per_pix)+B)^2)^1.5) / |2A|  

The curvature was evaluated at the bottom of the image.

Assuming the camera was place in the center of the car and facing straight ahead, the position of the car with respect to the side lanes was calculated from the offset of the center of the image from the center of the lane.
The position was also evaluated at the bottom of the image.

Both the radius of curvature and the position of the car were displayed in the video output.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

The code for this step is contained in the section "Draw Found Lane Lines on Original Images" in the IPython notebook "pipeline.ipynb".  

The lanes found in the warped images were transformed back to original perspective and added to the original colored images. The source points used in the first perspective transform were now the destination points, and the destination points used in the first perspective transform were now the source points.

![alt text][image39]
![alt text][image40]
![alt text][image41]
![alt text][image42]
![alt text][image43]
![alt text][image44]
![alt text][image45]
![alt text][image46]

As you can see from the images above, some of the images indicating wrong lane lines. These errors were compensated by comparing found lane lines in the current image with previous image. Once the pipeline finds a good lane, it uses the curve fit of this good lane as a reference to find lanes in the next consecutive image, resulting in less errors.

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./output_video_FINAL.mp4)

The image processing pipeline successfully processed the video. In the output video, the lanes were identified and colored in green. The radius of curvature of the lane and vehicle position within the lane were shown in the bottom portion of the video. The warped binary images were shown in the right portion of the video. The video shows that the pipeline successfully found lane lines and did not fail even when shadows or pavement color changes are present.

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Watching the challenge video results best explains the short comings of my pipeline.

Here's a [link to my challenge video result](./output_challenge_video.mp4) and here is a [link to harder challenge video result](./output_harder_challenge_video_trial3.mp4)

For the "chellenge_video.mp4," the HLS color thresholds were not capturing the lane lines. In most of the binary warped images, only the bottom of the yellow lines can be seen. The pipeline should have directional thresholds using sobel function to capture the lane lines in addition to the color thresholds.

In the "harder_chellenge_video.mp4," there were too many noise in the preprocessed binary warped images. For examples, in some images, right lanes were covered by leaves and were not detected. Lighting conditions were not good. There were shadows from trees that made the pipeline hard to find lanes. On the other hand, there were too bright spots that also made the pipeline hard to find lanes. Furthermore, lanes were wavy and curves were steep so that only center lane line was shown in some of the images. Guard rails were sometimes mistaken as lane lines.

Also, this pipeline most likely fails to detect lanes when the car is on the hill or downhill where the ground is not flat. When the perspective transform was applied, I assumed the ground to be flat, so the lanes will appear parallel in the warped images. However, this assumption fails when the ground is not flat.

If depth information is available, such as from LIDAR, then the pipeline can be improved further. For example, the pipeline should be able to distinguish if the detected edge lines are lane lines or guard rails, and can know if the ground is flat, or uphill, or downhill.
