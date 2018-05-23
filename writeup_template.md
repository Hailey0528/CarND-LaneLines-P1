# **Finding Lane Lines on the Road** 

## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**

In this project, the goal is to detect the lane with openCV.
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./test_images_output/grayImage.jpg "Grayscale"
[image2]: ./test_images_output/blurGrayImage.jpg "BlurGrayscale"
[image3]: ./test_images_output/edges.jpg "edges"
[image4]: ./test_images_output/masked_edges.jpg "masked_edges"
[image5]: ./test_images_output/solidYellowCurve.jpg "LineSegment"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.
The first part of the project is to get the interesting line segments with canny edge detection and hough line transformation, which is realized in 5 steps
1. read the image and convert the corful image from RGB image to gray image.use the cvtColor in openCV to 
![alt text][image1]
2. Gaussian smoothing in the 
![alt text][image2]
3. use canny edge detection to detect th edges of the gray image
![alt text][image3]
4. select the interesting region in the image to get the lane lines. I choose a triangle region as interesting region. But it is affected by the installed position of the camera. one point is the bottom left point of the image, second is the bottom right point of the image.
![alt text][image4]
5. use Hough Transform to get the lines 
![alt text][image5]

With the pipeline the line segments in the road can be captured. Now the lane line of the driving car in full extent can be identified from the captured line segments with Hough Transform. 
Not every line in the road is straight and clear. Therefore, 

My pipeline consisted of 5 steps. First, I converted the images to grayscale, then I .... 
in order to find the line segments in the image. 

In order to draw a single line on the left and right lanes, 
I modified the draw_lines() function by ...




### 2. Identify potential shortcomings with your current pipeline

One short coming of the current pipeline is that the interesting area has big effect on the result. 

Another shortcoming could be ...


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to ...

Another potential improvement could be to ...
