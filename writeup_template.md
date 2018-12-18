# **Finding Lane Lines on the Road** 

## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image_grayscale]: ./test_images_output/grayscale.jpg "Grayscale"
[image_gaussian]: ./test_images_output/gaussian.jpg "Gaussian blur"
[image_canny]: ./test_images_output/canny.jpg "Canny edges"
[image_masked]: ./test_images_output/masked.jpg "Masked edges"
[image_hough]: ./test_images_output/hough.jpg "Hough transformation"
[image_extrapolated]: ./test_images_output/extrapolated.jpg "Extrapolated lines"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline as implemented in `process_image(image)` consists of 6 steps: 
1. In `apply_grayscale(image)` the original image is converted to grayscale. ![alt text][image_grayscale]
2. In `apply_gaussian(image_gray, kernel_size)` a Gaussian blur is applied (this step is optional, as `cv2.Canny()` applies a 5x5 Gaussian internally). ![alt text][image_gaussian]
3. In `apply_canny_edge(image_blur_gray, low_threshold, high_threshold)` the edges of the image are detected using Canny edge detection. ![alt text][image_canny]
4. In `mask(image_edges, min_x_left, max_x_left, min_x_right, max_x_right, min_y, max_y, ignore_mask_color)` a polygon mask (region of interest) is applied to the image. It is crucial to select the right region of interest. A region of interest that is too small will keep valuable information from being used by the Hough transformation. This leads to inferior results particularly in the case of dashed lane markings. If curvatures are present, a region of interest that is too large will take into account the curved lane markings at the very end of the region of interest. This will lead to inferior results as these lane markings negatively affect the average of all lines detected by the Hough transformation. ![alt text][image_masked]
5. In `calculate_hough_transform(image_masked_edges, rho, theta, threshold, min_line_length, max_line_gap`, the Hough transform is calculated and in `apply_hough_transform(image_masked_edges, image, lines)` it is applied to the image. In particular the parameters `threshold`, `min_line_length` and `max_line_gap` influence the quality of the result obtained. I assume, that there is a variety of parameter combinations that yield satisfactory results. When applied to the two videos, the parameters I chose produce somewhat more "jumpy" lines than the lines in the example solutions. I have the feeling however, that my lines are a little bit more "accurate" that the lines in the example solutions. ![alt text][image_hough]
6. In `separate_lane_boundaries(lines)`, the set of lines as obtained from the previous step is separated into 3 sets according to the respective slope of the line: A set of lines that is part of the right lane boundary (slope > 0.5), a set of lines that is part of the left lane boundary (slope < -0.5) and a set of nearly horizontal lines that is ignored (|slope| < 0.5). In `calculate_polynomial(lines)` the median of all lines belonging to a lane boundary is calculated. This is done by passing the start and end points of all lines to the `numpy.polyfit()` function, which then fits a polynomial of degree 1 to these points and returns the vector of coefficients that minimises the squared error. x and y coordinates are passed to the function in iverse order and thus the inverse polynomial is obtained. The inverse definition is helpful later when y_start and y_end of the line to be drawn are known but the corresponding x_start and x_end are unknown. ![alt text][image_extrapolated]
In general, one could use a more sophisticated way of averaging the lines, e.g. uses the lenghts of the lines to calculate some sort of weighted average and thus give longer lines a higher weight than shorter lines. However, the simple approach as described above already yields satisfactory results. 
Finally, in `draw_lines(image, (f_right, f_left), y_start, y_end` the obtained lines corresponding with the left and right lane boundaries are drawn on the original image.

### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when ... 

Another shortcoming could be ...


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to ...

Another potential improvement could be to ...
