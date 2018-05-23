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
Not every line in the road is straight and clear. Therefore, At first, every selected line segment belongs to left lane or right lane, should be decided. The criterien for are
#from http://jeffwen.com/2017/02/23/lane_finding. And some conditions are added for deciding the line belongs to left or right lane.
  def avg_lines(lines):
    x_CheckPoint = imshape[1]/2
    leftLines = np.empty([1,3])
    rightLines = np.empty([1,3])

    ## calculate slopes for each line to identify the positive and negative lines
    for line in lines:
        for x1,y1,x2,y2 in line:
            slope = (y2-y1)/(x2-x1)
            intercept = y1 - slope*x1
            length = ((y2-y1)**2+(x2-x1)**2)
            if slope < -0.5 and slope > -2 and x1 < x_CheckPoint and x2 < x_CheckPoint:
                leftLines = np.append(leftLines,np.array([[slope, intercept, length]]),axis = 0)
            elif slope > 0.5 and slope < 2 and x1 > x_CheckPoint and x2 > x_CheckPoint:
                rightLines = np.append(rightLines,np.array([[slope, intercept, length]]),axis = 0)

    ## just keep the observations with slopes with 2 std dev
    leftLines = leftLines[to_keep_index(leftLines[:,0])]
    rightLines = rightLines[to_keep_index(rightLines[:,0])]

    ## weighted average of the slopes and intercepts based on the length of the line segment
    leftLane = np.dot(leftLines[1:,2],leftLines[1:,:2])/np.sum(leftLines[1:,2]) if len(leftLines[1:,2]) > 0 else None
    rightLane = np.dot(rightLines[1:,2],rightLines[1:,:2])/np.sum(rightLines[1:,2]) if len(rightLines[1:,2]) > 0 else None

    return leftLane, rightLane
    
My pipeline consisted of 5 steps. First, I converted the images to grayscale, then I .... 
in order to find the line segments in the image. 

In order to draw a single line on the left and right lanes, 
I modified the draw_lines() function by ...




### 2. Identify potential shortcomings with your current pipeline

One short coming of the current pipeline is that the interesting area has big effect on the result. As I chose the triangle area, except the bottom left point and bottom right point of the image, the third point is somehow important for the calculation. If the interesting region includes many line segments, like the trees, isolation belt, which we are not interested in. 

Another shortcomin is that, when the lane lines are under the sun light, the line segment can not be identified, which means, the pipeline can not work with high reliability in special circumstance.



### 3. Suggest possible improvements to your pipeline

A possible improvement would be to ...

Another potential improvement could be to ...
