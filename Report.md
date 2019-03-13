# **Project 1: Finding Lane Lines on the Road** 

In this project we detect lane lines in images using Python and OpenCV! The final output on one of the test videos is shown below.

![Random Agent](test_videos_output/solidWhiteRight.mp4)

---

### Reflection

### 1. Pipeline:

The pipeline broadly consists of 5 steps. The output after each stage is shown:

1) The images are converted to grayscale:

![gray](/Users/aman/Documents/Personal/projects/CarND-LaneLines-P1/examples/gray.png)

2) Gaussian Smoothing is applied with a kernel size of 5.

![blur](/Users/aman/Documents/Personal/projects/CarND-LaneLines-P1/examples/blur.png)

3) Canny Edge detection is used next with low and high thresholds 50 and 150 respectively.

![canny](/Users/aman/Documents/Personal/projects/CarND-LaneLines-P1/examples/canny.png)

4) We specify a Region of interest (RoI) and mask all the values of the image returned by the previous step outside this RoI as 0. Hough transform is then applied on top of the masked output to get the detected lines. However, these lines are disjoint on either side and we so, modify the `drawLines()` functions (explained later) to extrapolate the detected lines so that they form one continuous chunk on both sides.

![hough](/Users/aman/Documents/Personal/projects/CarND-LaneLines-P1/examples/hough.png)

5) Finally, we superimpose this on top of the image to get the final result.

![final](/Users/aman/Documents/Personal/projects/CarND-LaneLines-P1/examples/final.png)

As mentioned in Step 4 above, Hough transform returns various lines which are discontinuous on either side. But we want a single lane line. There are various ways to do this. The approach I followed was to first identify the lane line with the maximum length on both sides. We already specify the RoI and using the RoI vertices, we identify the top horizontal line and the bottom horizontal line. Then, we find the intersection of the largest left and right lane lines with both the top and bottom horizontal lines. An example is shown below:

![draw lines](/Users/aman/Documents/Personal/projects/CarND-LaneLines-P1/examples/draw_lines_explain.jpg)

To find the intersection of two lines, we use the following formulation of a line:

$$ y = m x + c ; m = \frac{y2 - y1}{x2 - x1}; c = y_1 - m * x_1​$$

The intersection of two lines: $y = m _1x + c_1$ and $y = m _2x + c_2$ is given by:

$$ x_* = \frac{c2 - c1}{m1 - m2}; y_* =m_1 x_* + c_1$$

I join the bottom left and top left intersection points to get the left lane line and the bottom right

and top right intersection points to get the right lane line. Also, when working with videos, computing different bottom intersection points can make the lane lines look unstable. So, I set the bottom intersection points for all the frames same as that of the first frame.


### 2. Identify potential shortcomings with your current pipeline

There are several shortcomings with the current pipeline:

- The RoI is hard-coded. That means that if the view of the vehicle changes or the roads appear at a different part of the image, this would fail.
- The lane still lines are very sensitive to the parameters of the canny edge detector. 


### 3. Suggest possible improvements to your pipeline

- I could use more sophisticated segmentation algorithms to segment the lane lines by using a deep neural network. That would give the result in a single forward pass without having multiple stages. Also, it would make the algorithm robust to the position of the roads in the image.
- Erosion/dilation could be applied after the edge detection step to get a better result.