# **Advanced Lane Line Detection**
---

**Advanced Lane Finding Project**

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[image1]: ./writeup_images/Chessboardcorners.jpg "Chessboardcorners"
[image2]: ./writeup_images/undistort_image.jpg "undistort_image"
[image3]: ./writeup_images/undistort_testimage.jpg "undistort_testimage"
[image4]: ./writeup_images/birds_eye_view.jpg "birds_eye_view"
[image5]: ./writeup_images/threshold.jpg "threshold"
[image6]: ./writeup_images/threshold_birds_eye_view.jpg "threshold_birds_eye_view"
[image7]: ./writeup_images/lane_line.jpg "Output"
[image8]: ./writeup_images/Capture.JPG "Radius of Curvature"
[video1]: ./project_video_output.mp4 "Video1"
[video2]: ./challenge_video_output/mp4 "Video2"
[video3]: ./harder_challenge_video_output.mp4 "Video3"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points


### How I implemented the rubric points and problems faced while implementing it
---

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for Camera_calibration and Distortion_correction is contained in the 3rd code and 5th cell and of the IPython notebook Advance_Lane_Finding_v1.1.ipyb

The output of the same is contained in the 4th and 7th cell of the same IPython notebook


I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the
world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are
the same for each calibration image. Thus, objp is just a replicated array of coordinates, and objpoints
will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.
imgpoints will be appended with the (x, y) pixel position of each of the corners in the image plane with
each successful chessboard detection.

I then used the output objpoints and imgpoints to compute the camera calibration and distortion
coefficients using the cv2.calibrateCamera() function. I applied this distortion correction to the test
image using the cv2.undistort() function and obtained this result:

![image1]

![image2]



### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

The distortion correction can be identified by the car hood. This can be seen in the below image.

![image3]

As we can see , the hood has become smaller in the Undistortd Image

#### 2. Has a binary image been created using color transforms, gradients or other methods?

After trying different combinations which includes hands-on with different color spaces ( LAB, LUV, HLS, HSV , RGB), applying sobel filters, and referring many projects online, I finally landed on the optimal combinations .

I have used L segment of HLS color spaces and B segment of LAB color space (normalizing the color space), along with X sobel filter, to finally detect maximum of lane pixels .


![image5]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for perspectiveTransform is in the 9th code cell of IPython file.
Here, we need 2 image points namely src (source image point) and dest ( destination image point). After various combinations I  figured out the perfect configuration to warp the image using opencv's getperspectiveTransform() function.

![image4]


#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Firstly after applying gradient Threshold and performing perspectiveTransform(). I have sliced the binary image from both ends so that the histogram of lane lines become finer.

Histogram is generated after multipying it with weights so that the lanes line in the center of the image has higher weight rather than the outlier.

The functions Sliding_window_demo() and search_lane_demo() function is used to identify the lane-lines. The code of the same is from 31 - 48 cell of IPython file.

Here search_lane_demo checks if there is already a fitted line , if none , it uses sliding_window_demo function to search using histogram . Sliding_window_demo() uses histogram and divides the image into 2 frames and search for the activated pixels using windows in both frames, and tries to fit the line along which the maximum pixels are detected. Since this line could possibly be a curve, we have used a 2nd order polynomial using polyfit function of numpy .

If there is already a fitted line in the previous frame, it uses the same lines coefficient and searches around the same line .

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

The code for Radius of Curvature is in 44th code cell of IPython file.
The formula for the same is mentioned below.

![image8]

`left_curvature = ((1 + (2* left_fit[0] * y_eval + left_fit[1])**2)**1.5) / (np.abs(2*left_fit[0]))`

`right_curvature = ((1 + (2* right_fit[0] * y_eval + right_fit[1])**2)**1.5) / (np.abs(2*right_fit[0]))`

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I followed below steps to finally get the result:

* Apply a distortion correction using the existing calibration data.
* Apply Threshold and masking to the distortion corrected Image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Perform imageslicing and histogram adjustment
* Detect lane pixels using search_lane_demo function or sliding_window_demo function.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.

![image7]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

[link to video 1](./project_video_output.mp4)


---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

1. My very first problem was to tweak the values for gradient threshold . I have even tried LAB , LUV and HSV values in hope that they might have greater efficiency in detecting edges and lines under low light . But to figure out the exact values was a tedious task .

2. Going forward in the project the was constantly failing in writing an efficient pipeline. My initial result video were way worse than now . I refered many github projects for this , but because of different implementations of sliding window and targeted search functions the pipeline would eventually Fail. After countless research and understanding of project i ended up writing a code which gave me optimal result.

3. After many fail videos , I realised the search_lane_demo function should look around the last line ( not the average of the last time ). There were many several observations and changes which was later understood after the failure of resultant video.

4. The pipeline would fail when there is a sharp turn and one of the lines slowly exists the frame. The Pipeline would still fail if their is a low light lane lines .

To tackle with this issue ,  following changes can be made:
 * use S segment of HLS in combination of magnitude binary and explore more combinations of color spaces to better detect lane line in low light conditions
 * enhance birds eye view in such a way , that curve lines appear to be parallel even when they are curving in birds eye view, maintaining same lane width throughout the video, this could help in removing outliers.
 * Selecting previous coefficients when the pixels found are less than threshold.
