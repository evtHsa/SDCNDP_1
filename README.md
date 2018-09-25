# **Finding Lane Lines on the Road** 



**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.



My pipeline consisted of 5 steps applied to each image(video frame)

- convert to grayscale, then
- perform gaussian blur
- perform canny edge detection
- calculate the vertices of the region of interest(polygonal, nearly triangular quadrilateral with the base at the bottom of the image) that contains our lane
- call **hough_line**s() which does the following
	- cv2.HougLInes() and then calls
	- 	draw_lines which has been modified to
		- 	**flip_y_ULO_LLO**() to convert from cv2's upper left origin to lower left origin so the derivations look more familiar
		- 		**discard_lines_with_unlikely_slopes**() which 
			- 	discards vertical lines with infinite slopes to avoid NaN and lines where the slope is more than the passed in slope(0.2 worked best)
			- 	segregates the remaining lines based on positive and negative slope into right and left side sets
		- 		for each of the right and left line sets
			- **line_parms**()  calculates the average slope, average intercept, min and max y values
			- **avg_line**()  calculates the x coordinates so the  line passes through the minimum and maximum y values
	- 	return the image with the lane lines
- compose the lane lines onto the original image

The progressive transformations look like: 


![gray scale](/home/evt/tmp/sdc_test_out/solidYellowCurve2.jpg_1_gray_bumble_Mon_092418_205858.png  "gray scale")

![after gaussian blur](/home/evt/tmp/sdc_test_out/solidYellowCurve2.jpg_2_blur_gray_bumble_Mon_092418_205858.png "after gaussian blur")
![region of interest](/home/evt/tmp/sdc_test_out/solidYellowCurve.jpg_4.roi_overlay_bumble_Mon_092418_205856.png  "region of interest")

![cropped](/home/evt/tmp/sdc_test_out/solidYellowCurve2.jpg_4_cropped_region_bumble_Mon_092418_205859.png  "cropped")
![lane lines after houg_lines](/home/evt/tmp/sdc_test_out/solidYellowCurve2.jpg_5_houghed_bumble_Mon_092418_205900.png  "lane lines after houg_lines")
![composite](/home/evt/tmp/sdc_test_out/solidYellowCurve2.jpg_6_composite_bumble_Mon_092418_205900.png  "composite")

### 2. Shortcomings with your current pipeline


- currently the solid yellow curve 2 image shows the lane markers clearly to the left of where they should be because my naive average calculation weights the distant(curving) lines equally to the nearer ones.
- the naive algorithis also humbled by the black sedan in the lane to the right. this seems confirmed that, as the sedan pulls farther and farther ahead our error becomes less egregious 


### 3. Suggest possible improvements to your pipeline

- in calculation of the average line, reduce the weight(currently line length) by the vertical distance from the bottom of the image and perhaps clip the top of the lane line where we notice turning
- analyze the challenge video framewise in the early, middle and late stages to see why we're being confused by the black sedan
