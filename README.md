# **Advanced Lane Finding**
[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)


In this project, my goal is to write a software pipeline to identify the lane boundaries in a video, and create a detailed writeup of the project.

---
**Build a Lane Finding Project**


The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

The images for camera calibration are stored in the folder called `camera_cal`.  The images in `test_images` are for testing the pipeline on single frames.  

[//]: # (Image References)
[UndistImage]: ./Undistorted.png
[RbinaryImg]: ./Rbinary.png
[SbinaryImg]: ./Sbinary.png
[OutputImg]: ./output.png
[GradDirBinaryImg]: ./GradDirBinary.png
[GradMagBinaryImg]: ./GradMagBinary.png
[mixtureImg]: ./mixture.png
[maskedImg]: ./masked_image.png
[birdseyeImg]: ./birds_eye.png
[linedetImg]: ./line_detection.png
[fresImg]: ./final_result.png

### Writeup / README

You're reading it! and here is a link to my [project code](https://github.com/code-Abhishek/seeing_lane_eyes_closed/blob/master/CarND-Advanced-Lane-Lines/Advanced_Attempt.ipynb)


### 1.Pipeline:

##### -> Camera Calibration.
   Computing the camera calibration required reading chessboard images through glob library and using them to find the image points which we can then connect to object points of the real world. We use cv2.calibrateCamera(objpoints, imgpoints, image_size) function to get these:
    - mtx : the Camera Matrix
    - dist: the Distortion Coefficients
    - rvec: the rotational vectors
    - tvec: the transational vectors
   
##### -> Undistortion of real camera images.
   Now we can undistort images from the camera using the camera matrix (mtx) and distortion coefficients(dist). Here's the outcome of those images.

![alt text][UndistImage]

##### -> Color Thresholding
   The next part is to use color thresholds to highlight the lane lines in the images. There are many ways we can go about doing these. This is my preferred color threshold combination:
   - R color threshold:
       Since all images are made up of 3 color, i.e. R(ed)G(reen)B(lue) color channels, it makes a lot of sense to try and utilize these channels that have high intensity for colors like yellow and white, and use them to single out the pixels with these thresholds.
       
        ![alt text][RbinaryImg]

   - Saturation threshold:
          Each image can be also presented in some other color-space apart from the RGB, such as HLS color space, i.e. Hue, Light, Saturation. Saturation helps us to remove the effect of Light & shade, and highlight the lanes by thresholding it as follows:
         
        ![alt text][SbinaryImg]
         
   - Finally I applied the S threshold, alongwith R & Y threshold to put out a much more effective filter. Here's the image.
        ![alt text][OutputImg]


##### -> Gradient Thresholding
  Now I focus on using gradient thresholds to aid the already good highlighted Output image shown above. Gradients are derivatives (or the change) with respect to any aspect of the pixels. Sobel filters use this concept, take in a gray image and provide us with pixels that show sudden change in the pixel intensities, such as the lane lines, and many other parts of the image.
  I have used the following ones:
  
   - Gradient Magnitude threshold:
         I take the Magnitude of the Gradient by taking the magnitude of the combined filters of sobel X & sobel Y. Thresholding this produced the following.
       ![alt text][GradDirBinaryImg]

   - Gradient Direction threshold:
         When we perform the "arctan2" function on the absolute values of Sobel X & Sobel Y filters, I get the directions for the gradients, explained earlier. This looks like this
         
        ![alt text][GradMagBinaryImg]

         
##### -> All Thresholds:
   Finally I applied all the thresholds, generating a very useful filter to single out the lane lines clearly. I then apply a mask on the image to remove the environmental noise, and focus on the lane lines. 
        ![alt text][mixtureImg]
        ![alt text][maskedImg]
        
##### -> Warping image & line detection:
   The next step is to get a birds eye view of the masked image so that we can fit a polynomial line on the warped image.
   
   -Warping is done using cv2.warpPerspective() and determining the source and destination points, as shown in the image here.
       ![alt text][birdseyeImg]
   
   - I have used the sliding windows methods to identify lane line pixels in the rectified warped binary image. The left and right line have been identified and fit with a curved functional form by utilizing the class 'Line' and once I have found the pixels for the lane lines, for finding the lane line pixels in the next frame, I search along the area aroun the fit polynomial of the previous frame. The line fit is shown here. 

     ![alt text][linedetImg]


##### -> Detection of Radius of Curvature and vehicle position 
   For detection of vehicle position, I calculated the points on the base of the left lane & right lane and used the mid-point as the a reference, and subtracted that from the main image center for every frame to get the vehicle's position.
   
   The radius of curvature was easy to calculate once we have the polynomial that fits the lanes, and use that to calculate the ROC by using the average conversion of 'X meters per pixel' and the formula.
   

## The Result
The final result after processing the frames of the video through all of the above looks like this:

![alt text][fresImg]


The video is added alongwith the repo as "output_project_simple.mp4" [./output_project_simple.mp4]



###  Potential shortcomings with current pipeline
One potential shortcoming would be what would happen when I there is some part of the roads that are so sunny that the lanes are blocked or are not picked up by the camera itself. What to do when we have no lane lines on the left, and only on the right. What happens, when there is a sharp turn and my pipeline looses the track of the lane near the curve. It becomes seemingly very difficult for my pipeline to go through these tasks.


### Possible improvements to the pipeline
A possible improvement would be to add logic for curved lanes when they go out of the frame, by calculating the parabolic / circular exit of the lane, just like I did with calculation of the radius of curvature. 

Another potential improvement could be to use Deep Learning methods and implement them for the pipeline to understand what features are really important in an image, and couple that with the existing thresholding techniques to gain a smoother or much more accurate lane lines before warping the image.




