# Udacity Project - Advanced Lane Finding
The original source files for this project can be found from this [link](https://github.com/udacity/CarND-Advanced-Lane-Lines).

The goal of this project is to create a pipeline for lane finding algorithm that reads an image and estimates the possible lane in the images and plots the information back on the image. The process should be able to find the lanes and extract numerical information about the vehicle position and lane curvature parameters. The steps of this project are described below.

### Computing the camera calibration matrix and distortion coefficients for given set of chessboard images
The camera calibration is done with the image set provided with the project. All photos are read in and the `cv2.findChessboardCorners` is used to find the grid points in the images. All the data is accumulated to `image points` and `object points`. This is done through an automated loop that tries to find the maximum number of image points in the checkboard images. `cv2.calibrateCamera` fucntion is then used to produced the calibration matrix.
``
### Applying the distortion correction to the raw image
The distortion matrix can be used for any of the images provided. Example image is plotted in the Jupyter Notebook and saved to output folder.

### Color transforms and gradients to create a thresholded binary image
HLS (saturation, lightness and hue) image format is used to calculate the Sobel derivative in x direction to see the lines away from the horizal direction. This gradient is then thresholded to adjusted limits and transformed to a binary image (inside limits 1, outside limits 0). Similar thershold is used just for the saturation channel of the image. This means that when in limits, the saturation is 1 and not 0. Both of these outputs is then stored to an image format, into separate channels.

### Apply a perspective transform to rectify binary image ("birds-eye view")
Image perspective transform is done with the `cv2.getPerspectiveTransform` function. The source coordinates for the trapezoidal shape are estimeted from the test images and the destination coordinates are the image size. Coordinates are then used with the `cv2.getPerspectiveTransform` function and applied to the image with `cv2.warpPerspective` function. Also the reverse trasform is done in a similar manner. Example image is plotted in the Jupyter Notebook and saved to output folder.

### Detecting lane pixels and fitting to find lane boundary and determining the curvature
To keep track of previous lane findings, a `deque` function is used from python collections. This defines a list with certain history length and when a new entry is appended the last from the history will drop out. To find the lanes the binary image created in the previous section is used. With the first few frames a histogram is used to find a likely location to both left and right lanes. If the cumulated value are above a limit value, then it is determined that lane has been found. Also the lane search limit is defined with this infomarion as "peak width". If a fit has already been made earlier, it is assumed that the lane is close by the fitted lane and that area is used for the lane pixel identification. The found lane pixels are separated to two channels of an image.  


Second function is defined to create a fit to these pixels. The lane fit is done by fitting the left and right lanes separetly. Pixels of the one lane are used to make a second order polynomial fit. The fitted parameters are stored into a history list. Another fit is made with scaled pixel values. This was done to estimate the lane parameters in meters. The fit is done in a same manner, but only the pixels are scaled to meter scale. This could be done with one fit, but as it is necesarry to convert the found lanes later back into the image in pixels, it was convienient to use pixel and metric fits separetly. In the Jupyter Notebook the metric varibles have a `_cr` addition to the variable name.

From the pixel data also an extra feature is created. The average of the two lanes is used to create a center lane. This center lane is plotted on to the image as an arrow to show direction and average lane.

### Warping the detected lane boundaries back onto the original image
The output of the lane finding function provides the necessary input for the warping the lanes back to the image. Earlier calculated reverse perspective transformation is used to transform the lane pixels and the center lane arrow back to the original form. These are then plotted over the image.

### Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position
The output process for the video file has been implemented with the same logic as in the first lane finding project, with the image processing pipeline created earlier. Each image is processed one by one and the final video is shown in the Jupyter Notebook and saved to disk. From the project video file it can be seen that the each frame shows the implemented features and the numerical lane parameters are plotted to the frames.
