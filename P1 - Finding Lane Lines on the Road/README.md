# **Finding Lane Lines on the Road** 
[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)

<img src="examples/laneLines_thirdPass.jpg" width="480" alt="Combined Image" />

Overview
---

When we drive, we use our eyes to decide where to go.  The lines on the road that show us where the lanes are act as our constant reference for where to steer the vehicle.  Naturally, one of the first things we would like to do in developing a self-driving car is to automatically detect lane lines using an algorithm.

In this project you will detect lane lines in images using Python and OpenCV.  OpenCV means "Open-Source Computer Vision", which is a package that has many useful tools for analyzing images.  


Refelection
---


## Pipeline

My pipeline concludes 6 step:

1. Turn RGB image into gray image.
2. Add gauss blur to cut noise in the gray image.
3. Use canny edge detector to find edges.
    - calculate images gradient
    - use non-maximum suppression to thin image
    - use double threshold to filter edges
4. Fix the region of interest using polygon
5. Use hough transform to find lines
6. Extract line from hough transformation

## Shortcomings observed in the current pipeline

1. Since the first step is converting the image to grayscale from RGB, shadows and light variations in the environment are difficult to capture. This can be gleaned from the fact that the current pipeline while working reasonably well for the first two test videos breaks down for the challenge video.

1. The lane lines detected in the resulting output are not as stable as the ones in "P1_example" video. This is not desirable since it is difficult to follow rapidly changing steering commands.

## Possible improvements to the current pipeline

1. Instead of converting the image to grayscale directly, the test image can be preprocessed using RGB normalization. This can help in mitigating effect of shadows and lighting variations and make the current pipeline more robust. A function definition for producing a normalized RGB image is shown below. This approach did not produce an improvement when implemented in it's current form and can be further improved. 

```
# Reference: http://akash0x53.github.io/blog/2013/04/29/RGB-Normalization/
def normalized_rgb(img):
        imshape = img.shape
        ysize = imshape[0]
        xsize = imshape[1]
        norm=np.zeros((ysize,xsize,3),np.float32)
        norm_rgb=np.zeros((ysize,xsize,3),np.uint8)
        b=img[:,:,0]
        g=img[:,:,1]
        r=img[:,:,2]
        sum = b + g + r
        norm[:,:,0]=b/sum*255.0
        norm[:,:,1]=g/sum*255.0
        norm[:,:,2]=r/sum*255.0
        norm_rgb=cv2.convertScaleAbs(norm)
        return norm_rgb
```

2. Another way of dealing with shadows and variations in light is to use a different colorspace, for example, say HSV instead of RGB. A function definition for applying RGB to HSV transform is shown below. The test image before being fed to the pipeline is given an RGB to HSV transform. This approach while producing acceptable results for the first two test videos did not result in any significant improvements in the performance of identifying lane lines for challenge video.

```
def rgbtohsv(img):
    "Applies rgb to hsv transform"
    return cv2.cvtColor(img, cv2.COLOR_RGB2HSV)
```

3. Another improvement in the pipeline can be to include a running average of slopes for identified lane lines so that there is a smooth transition from one frame to the next. This avoids rapid changes in commands to the steering control system.

1. Further, machine learning approaches can be explored to make the lane finding pipeline more robust in future.

We encourage using images in your writeup to demonstrate how your pipeline works.  

All that said, please be concise!  We're not looking for you to write a book here: just a brief description.

You're not required to use markdown for your writeup.  If you use another method please just submit a pdf of your writeup. Here is a link to a [writeup template file](https://github.com/udacity/CarND-LaneLines-P1/blob/master/writeup_template.md). 


The Project
---

## If you have already installed the [CarND Term1 Starter Kit](https://github.com/udacity/CarND-Term1-Starter-Kit/blob/master/README.md) you should be good to go!   If not, you should install the starter kit to get started on this project. ##

**Step 1:** Set up the [CarND Term1 Starter Kit](https://classroom.udacity.com/nanodegrees/nd013/parts/fbf77062-5703-404e-b60c-95b78b2f3f9e/modules/83ec35ee-1e02-48a5-bdb7-d244bd47c2dc/lessons/8c82408b-a217-4d09-b81d-1bda4c6380ef/concepts/4f1870e0-3849-43e4-b670-12e6f2d4b7a7) if you haven't already.

**Step 2:** Open the code in a Jupyter Notebook

You will complete the project code in a Jupyter notebook.  If you are unfamiliar with Jupyter Notebooks, check out <A HREF="https://www.packtpub.com/books/content/basics-jupyter-notebook-and-python" target="_blank">Cyrille Rossant's Basics of Jupyter Notebook and Python</A> to get started.

Jupyter is an Ipython notebook where you can run blocks of code and see results interactively.  All the code for this project is contained in a Jupyter notebook. To start Jupyter in your browser, use terminal to navigate to your project directory and then run the following command at the terminal prompt (be sure you've activated your Python 3 carnd-term1 environment as described in the [CarND Term1 Starter Kit](https://github.com/udacity/CarND-Term1-Starter-Kit/blob/master/README.md) installation instructions!):

`> jupyter notebook`

A browser window will appear showing the contents of the current directory.  Click on the file called "P1.ipynb".  Another browser window will appear displaying the notebook.
