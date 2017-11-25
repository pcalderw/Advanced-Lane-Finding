## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

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

[image1]: ./images/undistorted.png "Undistorted"
[image2]: ./images/activation.png "Binary Activated"
[image3]: ./images/region.png "Region bounds"
[image4]: ./images/skewed.png "Skewed image"
[image5]: ./images/lane_drawn.png "Output frame"

[video1]: ./project_video_out.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

You can see the undistortion under the Camera Calibration section. The code is basically a copy of my solutions to the quizzes but wrapped into a simple function call. Almost all the images were correctly identified. You can see this in the cells with the checkerboard drawn on them. The checkerboard corners are found then the camera calibration function is called to get the resulting constants. 
The blank images are the ones where no checkerboard was found. Below is the undistorted images of the checkerboard. 




### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

![alt text][image1]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

Under the 'Line detection' section a number of transformations were explored, HLS, HSV, LAB, LUV, & RGB. You can see each channel on an example image. I coded a widget so that I could play with and fine tune the threshold values and see their interactions easily. 
The resulting activation function makes up a mask using thresholding on 

1. S & V in HSV
2. V in LUV
3. B in LAB
4. and R in RGB (bgr)

Magnitude sobel line detection is then run on the combination of each mask to reduce it to just major lines. 

![alt text][image2]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The skew code is demo'd in the 'Perspective shift' section

The skew region is visible here as the green box. Notice that no box would be on the line so we must use a slightly larger box and adjust the skew to pull some surrounding area.

![alt text][image3]

The resulting skew is not calibrated to give a perfectly top down view. I did not adjust the bounds to force this behavior because it correclty draws the lane and we are not using the measured curve to make an action.

![alt text][image4]


#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

The code is the same as the quiz code except it is converted to a function. The only project specific change is the lane curve is actually an exponential drop off of the history. The current curve guess is weighted most at 1/2, the previous frame is halved again for 1/4 value, the one prior at 1/8, etc. This led to a much smoother curve and allowed for frames with no scene curves, so long as it wasn't the first one.

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

The code is the same as the quiz code except it is converted to a function. There are no project specific changes as I have no time.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

![alt text][image5]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video_out.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

I found that the S channel was extremely difficult to not grab shadows so it fails around the underpass in the challege video. Likewise I wish I had coded something to detect all lanes and pick them based off the spacing which we would expect to be fairly regular in the birds eye perspective. Once the lanes were found it would use either the previous image as a starting point for the search.