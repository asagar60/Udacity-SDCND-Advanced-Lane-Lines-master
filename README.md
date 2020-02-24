# **Udacity_SDCND-Advanced-Lane-Lines-master**

[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)

 Project - 2: Advance Lane Finding . This Project uses Computer Vision Techniques extensively to isolate Lane Lines in different Lightening conditions

![Output_gif](./project_video_output.gif)

 Overview
 ---
 When we drive, we use our eyes to decide where to go.  The lines on the road that show us where the lanes are act as our constant reference for where to steer the vehicle.  Naturally, one of the first things we would like to do in developing a self-driving car is to automatically detect lane lines using an algorithm.

 In the Last Project we saw how we can identify lane lines on a basic level.
 In this Project we will explore the possible ways to identify the complete Lane Line on higher level

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
 [image9]: ./writeup_images/plot_histogram.JPG "plot_histogram"
 [image10]: ./writeup_images/plot_histogram_weight.JPG "plot_histogram_weight"
 [image11]: ./writeup_images/plot_histogram_weighted.JPG "plot_histogram_weighted"
 [video1]: ./project_video_output.mp4 "Video1"
 [video2]: ./project_video_output.gif "Output_gif"
 **Installing Dependencies**
 ---

 - opencv -  `pip install opencv-python`
 - imageio - `pip install imageio`
 - ffmpeg - `pip install imageio-ffmpeg`
 - moviepy - `pip install moviepy`

 ---

### Advanced Lane Line Detection Pipeline

To effectively detect lane lines using Computer Vision Techniques, I chose the following sequence

 * **Apply a distortion correction using the existing calibration data.**

    Using `cv2.undist()` we correct the distortion with existing camera calibration data to correct Tangential Distortion

    Calibration using Chessboardcorners

    ![image1]

    Distortion Correction on Chessboardcorners

    ![image2]

    Distortion Correction on Real Test data

    ![image3]

* **Apply Threshold and masking to the distortion corrected Image**

    Apply different color space Threshold to isolate yellow and white Lane lines.

    ![image5]

* **Apply a perspective transform to rectify binary image ("birds-eye view")**

    Apply perspective transform to get a birds_eye_view of the image

    ![image4]

    Applying Birds Eye View along with Gradient threshold

    ![image6]

* **Perform image slicing and histogram adjustment**

    Perform Image slicing of the binary output image ( Birds Eye View + Gradient Threshold) , to have a cleaner Histogram .
    To enhance the histogram output , multiply the histogram with weights.

    ![image9]

    ![image10]

    ![image11]

* **Detect lane pixels using search_lane function or sliding_window function**

    Sliding_window function computes lane lines in case there is no lane lines detected in previous frame.
    Search_lane function computes lane lines if lane lines are detected in the previous frame


* **Determine the curvature of the lane and vehicle position with respect to center**

    measure_curvature_pixels() function is used to measure curvature of the lane , and determine the center offset

* **Warp the detected lane boundaries back onto the original image**

    Finally , the Lane line is drawn using `cv2.fillpoly` function and warping the image back to original from birds eye birds_eye_view.
