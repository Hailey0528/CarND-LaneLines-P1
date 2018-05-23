# **Finding Lane Lines on the Road** 

**Finding Lane Lines on the Road**

In this project, the goal is to detect the lane line in the road with openCV. Canny Edge Detection and Hough Transformation are used to detect the line segments in the image. Then the line segments are averaged with slope and intercept, then interpolated to only two lane lines of the road.



[//]: # (Image References)

[image1]: ./test_images_output/grayImage.jpg "Grayscale"
[image2]: ./test_images_output/blurGrayImage.jpg "BlurGrayscale"
[image3]: ./test_images_output/edges.jpg "edges"
[image4]: ./test_images_output/masked_edges.jpg "masked_edges"
[image5]: ./test_images_output/solidYellowCurve.jpg "LineSegment"

---

### Reflection

### 1. pipeline to detect the lane line
The first part of the project is to get the interesting line segments with canny edge detection and hough line transformation, which is realized in 5 steps
1. read the image and convert the corful image from RGB image to gray image with cvtColor in openCV.

![alt text][image1]
2. Gaussian smoothing of the gray image.

![alt text][image2]
3. use canny edge detection to detect line edges of the gray image.

![alt text][image3]
4. select the interesting region in the image to get the lane lines. I choose a triangle region as interesting region. But it is affected strongly by the installed position of the camera. one point is the bottom left point of the image, second is the bottom right point of the image. The third point is selected based on the not interesting lines can be excluded.

![alt text][image4]
5. use Hough Transform to get the line segments in the interesting region. In HoughLinesP there are five important parameters. As the line of the road is not always long, we cant select the min_line_len or max_line_gap to very large. 

        rho = 1             # distance resolution in pixels of the Hough grid
        theta = np.pi/180   # angular resolution in radians of the Hough grid
        threshold = 10       # minimum number of votes (intersections in Hough grid cell)
        min_line_len = 10   # minimum number of pixels making up a line
        max_line_gap = 5    # maximum gap in pixels between connectable line segments
    
![alt text][image5]

With the pipeline the line segments in the road can be captured. Now the lane line of the driving car in full extent can be identified from the captured line segments with Hough Transform. 

Now the function drawline() is changed in order to get only two lane lines in the road. Not every line in the road is straight and clear. Therefore, At first, every selected line segment belongs to left lane or right lane, should be decided. For example, the criterien for selecting the left lane are: The slopes of the line segment should be in the range of -2 to -0.5; And the x coordinates of the end points of a line should be both in the left half part of the image. Then in order to get rid of the lines, which we dont really want, for example, the horizontal line, the deviation of the slope of every line is compared with the average of the slope. If the deviation is above the threshold, then it is concluded, that this line segment doesn't belong to the left lane. After that, the average of the slope and intercept of the line is calculated.


    #from http://jeffwen.com/2017/02/23/lane_finding. And some conditions are added for deciding the line belongs to left or    right lane.
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
 
Then the endpoints of the lane line, which we want to draw in the image, will be calculated with Function Endpoints with the calculated slope and intercept.

    def Endpoints(y1, y2, line):
        if line is None:
            return None
        slope, intercept = line
        x1 = int((y1-intercept)/slope)
        x2 = int((y2-intercept)/slope)
        return [[x1, y1, x2, y2]] 


### 2. Identify potential shortcomings with your current pipeline

One short coming of the current pipeline is that the interesting area has big effect on the result. As I chose the triangle area, except the bottom left point and bottom right point of the image, the third point is somehow important for the calculation. If the interesting region includes many line segments, like the trees, isolation belt, which we are not interested in. 

Another shortcomin is that, when the lane lines are under the sunlight, the line segment can not be identified, which means, the pipeline can not work with high reliability in special circumstance.


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to select the specific color range of the image. The lane lines are either yellow or white. However, I am not sure, if it is the same under special circumstance, for example, under the sunlight. 

