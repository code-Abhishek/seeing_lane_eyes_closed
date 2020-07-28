# **Finding Lane Lines on the Road** 


---

The goal for me in the project is to understand the algorithms of image processing, and using up-to-date libraries of openCV to implement them in finding the lane lines on images. Finally constructing a pipeline that facilitates the same work for a video. Optimizing the pipeline for a cleaner lane description for the self-driving car, is the last and final goal.



[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

### 1. The Pipeline - .

Creating the pipeline has to be understood with the goal in mind, which is to detect the lanes. 

Step 1 --> Firstly, convert the images to grayscale to simplify getting the gradient change in the image, and apply gaussian blur to smooth images.

Step 2 --> Applying Canny edge detection algorithm with canny() function to obtain the most strongest gradients alongwith the lower threshold pixel to obtain thicker lane lines. 

Step 3 --> I applied a mask on the region of interest in the image to focus only on the lane lines. Now applying hough_transform function does 2 things, it converts the cartesian co-ordinates into hough co-ordiantes and gives back (x,y)points that have multiple hough-lines passing through a rho & theta value range, provided by us.

Step 4 --> Within the same function, using the draw_lines function, we generate lines based on the (x,y) co-ordinates presented to us. I changed the function to factor in creation of a single left and right lane, using getStraightLineCods()

Step 5 --> getStraightLineCods() calculates slopes and bifurcates the 'lines' (or set of co-ordinate points) provided into left or right half. It utilizes 'lines' list and finds slope of the points forming the lines. Uses the slope and xcenter, ycenter which are the center of our masked region to divide the lines in the two halfs. I then extrapolate the x-axis values for the points at the base of the two lines, and the top of the two lines. 

This gives me (rxbase,rybase, rxtop, rytop) right lane, and (lxbase,lybase, lxtop, lytop) left lane which can be drawn using cv2.lines() with desired thickness. 

Step 6 --> The final step is to integrate the hough_image on top of the original image to see the effectiveness of my pipeline, and add all the steps in the pipeline in a definite manner. 

![alt text][image1]


### 2. Potential shortcomings with current pipeline


One potential shortcoming would be what would happen when I there is some part of the roads that are sunny and some which are not sunny. How to go about calculating gradident for lines that are going across the lanes, (not parallel to them) 

Another shortcoming could be that curved lane lines are not yet incorporated in my pipeline, rendering it useless in real-world scenarios


### 3. Suggest possible improvements to the pipeline

A possible improvement would be to add logic for curved lanes using np.polyfit (i.e. interpolating on the range of points in between, and fitting a polynomial line, instead of a straight line).

Another potential improvement could be to average out the slopes for the left and right lane and add a range of variation, and ignore lines with slopes that vary by a large margin from the average slopes. This may eliminate the sudden dark and light (sunny and shady) parts of the road that are are crossing across the lanes.
