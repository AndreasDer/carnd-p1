# **Finding Lane Lines on the Road** 

## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./test_images_output/grey/solidWhiteCurve.jpg "Grayscale"
[image2]: ./test_images_output/gaussian/solidWhiteCurve.jpg "Gaussian"
[image2]: ./test_images_output/canny/solidWhiteCurve.jpg "Canny"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 5 steps. 

First, I converted the images to grayscale,
![Greyscale Image][image1]

after that I performed a gaussion smoothing 
![Gaussian][image2]
then I used the Canny edge detection algorithm with a low threshold of 75 and a high threshold of 150. 
![Canny Edge detection][image3]

After that I masked the region of interest to avoid unnecessary computation. 

In order to draw a single line I started to apply a Hugh transformation to the measked image which gave me a set of lines. Finding the right parameters for the Hough transformation was the hardest part.
I experienced best results with a threshold of 10,a min_line_len of 16 and a max_line_gap of 9.
To differentiate between left and right lines I calculated the slope of the lines. To filter out lines which are not in the correct range I did some filtering on the slope itself.
In order to be able to extrapolate the lines I did some research and came across linear regression. Therefor I pass the lines separated by left and right to my newly created function extrapolate_lines.
This function takes the start and endpoint of all the lines and performs a linear regression on the points to get a slope and an intercept. Given the slope and the intercept I can calculate a line which covers the complete region of interest.

This approach worked very good for the test images but in the video I had some artifacts. After some analysis I figured out that there are results from the linear regression which have a different slope or where there are no lines from the Hough transformation for one side of the lane lines.
To handle this issue I avaraged the slope and used the slope from the previous image if the difference between the average and the current slope is higher than a threshold of 0.15.

### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when the road gets more curved as the complete pipeline is optimized for straight roads. 

Another shortcoming could be the detection of multiple lines withing the masked area, as it could be seen in the challenge video.

Also when the lines are not so bright and the contrast of lines and road is lower it would be harder to detect the lines.


### 3. Suggest possible improvements to your pipeline

To get a more stable line, it would be an option to add some weighted memory to the drawing funtion

Another potential improvement could be to replace the linear regression with a polyfit approach.
