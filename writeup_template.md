
## **Advanced Lane Finding Project**

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

[image1]: ./writeup_images/undistort-calibration.png "Undistorted"
[image2]: ./writeup_images/undistort-test.png "Road Transformed"
[image3]: ./writeup_images/threshold1.png "Binary Example"
[image3a]: ./writeup_images/threshold.png "Threshold- All images"
[image4]: ./writeup_images/perspective-transform.png "Warp Example"
[image5a]:./writeup_images/histogram.png "Histogram"
[image5]: ./writeup_images/sliding-window.png "Find Lane"
[image6]: ./writeup_images/image-pipeline.png "Output"
[video1]: ./project_video_output.mp4 "Video"


### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the third code cell of the IPython notebook `P2-Advanced Lane Finding.ipynb`.

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I used the OpenCV function `findChessBoardCorners` to identify the locations of corners on chessboard photos given in `camera_cal` folder.

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained following result:

![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.
Applied a distortion correction to images in test_images folder.
* Input: Use chessboard images to obtain image points and object points (done in previous step)
* Use the calculated camera calibration matrix and distortion coefficients to remove distortion from an image
* Output: Undistorted Image

This is an example of a distortion-corrected image.
![alt text][image2]

The code is in 5th cell of notebook: `P2-Advanced Lane Finding.ipynb`

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image (thresholding steps at code cells 6, 7 and 8 in `P2-Advanced Lane Finding`).  Here's an example of my output for this step.  

![alt text][image3]

Output detailing the various color and gradient thresholds added. 

![alt text][image3a]



#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `warp()`, which appears in 9th code cell of the Ipython notebook `P2-Advanced Lane Finding.ipynb`. The `warp()` function takes as inputs an image (`img`). 

The hard coded `src` and `dst` are hardcoded in the function. These values were detected by 


| Source        | Destination   |
|:-------------:|:-------------:|
| 220, 720      | 320, 720        |
| 1110, 720      | 920, 720      |
| 570, 470     | 320, 1      |
| 722, 470      | 920, 1        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

Warp function is defined in 9th Code Cell of Jupyter notebook `P2-Advanced Lane Finding.ipynb`, following examples of images in 10th and 11th code cell.

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

I have used Sliding window method to detect lane pixels, starting with the base likely position of both both lanes calculated from histogram.
![alt text][image5a]

Steps:
1. Loop through each window in nwindows
2. Find the boundaries of our current window. This is based on a combination of the current window's starting point (leftx_current and rightx_current), as well as the margin you set in the hyperparameters.
3. Use cv2.rectangle to draw these window boundaries onto our visualization image out_img. This is required for the quiz, but you can skip this step in practice if you don't need to visualize where the windows are.
4. Now that we know the boundaries of our window, find out which activated pixels from nonzeroy and nonzerox above actually fall into the window.
5. Append these to our lists left_lane_inds and right_lane_inds.
6. If the number of pixels you found in Step 4 are greater than your hyperparameter minpix, re-center our window (i.e. leftx_current or rightx_current) based on the mean position of these pixels.

![alt text][image5]

Code is provided on code cell 13 of Jupyter notebook `P2-Advanced Lane Finding.ipynb`.

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in code cells 15 and 16 in my code in  `P2-Advanced Lane Finding.ipynb`.
The mean of the lane pixels closest to the car gives us the center of the lane. The center of the image gives us the position of the car. The difference between the 2 is the offset from the center. Pixels are converted to meters.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

* I implemented this step using `pipeline_final` function described in code cell: 18 in `P2-Advanced Lane Finding.ipynb` .
* The area between the lane is painted Green. Performed inverse tranform, and combined the processed image with the original image in `pipeline_final` to obtain the following result.
* Radius of Curvature and Center offset are also shown.

![alt text][image6]
---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video_output.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

* The pipeline did well on detecting the lane lines on `project_video.mp4`. This implies, the pipeline works on fairly good conditions.

* The pipeline came without major catastrophy for `challenge_video.mp4` but failed miserably on harder challenge video. 

* For further improvement, I would research on applying CNN for better detection and in further tuning the threshold parameters.
