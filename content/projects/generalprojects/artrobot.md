---
title: "Art Robot"
date: 2022-08-06T16:01:01-04:00
draft: False
portfoliocover: "/img/bart300x300.jpg"
cover: "/img/bart.jpg"
---

[Github Organization for whole project](https://github.com/uart-appstate-robotics-team/)

# Project Summary

This project started in 2019 during my undergrad at Appalachian State University, the goal was to explore creative algorithms for making physical art and we used a robot arm to make drawings.
The robot arm we used uArm Swift Pro and was intended to only be a prototype for the project, but between covid, graduating and lack of funding never got past this point.

The robot did a great job with drawings but had a very limited range so making paintings was too tedious to be reproducable.

# uArt API

This is an API that was the engine for the whole project. First you have to calibrate your setup, by letting the API know where the corners of your canvas are in 3D space.
Put simply, you need to tell the API where the end affector is in 3D space when it touches the four corners of the canvas.
This can be saved to a file.
With this information, the API can now draw/paint using the draw_point and draw_line functions.
The API will calculate where 2D coordinates on the canvas are in the 3D space and move the end affector of the robot. E.G. (0,0) is the top left corner of the canvas in 3D space etc.

# Line Drawings

[Github](https://github.com/uart-appstate-robotics-team/line_generators/blob/master/edge_points/edgepoints.py)

This algorithm uses Canny Edge Detection to find edges in a 2D image. 

Here is an example of a Canny edge image:

![A canny edge image](/img/cannyedge.png)

This edge image is treated as a graph of sorts, and a walk is performed to all connected white pixels. 


The algorithm starts in a corner of the edge image and iterates over them until it finds a white pixel. Once a white pixel is found it checks neighboring pixels in all directions to see if they are also white, recursively repeating this process until no neighbors are white. The coordinates of these white points are recorded and stored as a list. This list of points is one line used to make a drawing.

Here are some examples:
![](/img/linedrawing1.jpg)

![](/img/linedrawing2.jpg)

Sometimes the algorithm would add connections to the end and start points of completely different lines, this resulted in slashes across areas that were supposed to otherwise be left alone. You can see one at the top of the man's hat in the first image and across a few of the drawings in the bottom image. I later realized this was caused by the algorithm glitching out when there was a white pixel with more than 1 unvisited neighbor. Later on I fixed this, but this was late in the projects lifespan.


# Stippling

[Github](https://github.com/uart-appstate-robotics-team/line_generators/blob/master/stippling_points/stippling.py)

![](/img/stippling2.jpg)

First, we convert the image to grayscale so each pixel is a single float from 0.0 to 1.0. 
Then we invert this by subtracting 1 and taking the absolute value, so 0.0 turns into 1.0 and .8 turns into .2 etc.
This new value is treated as the percentage chance that the algorithm will place a dot at that coordinate.

We found that stippling performed by humans usually emphasizes the edges and our algorithm wasn't replicating that effect. To recreate this, we use Canny Edge Detection to generate an edge image. 
We darken the edge pixels in this edge from 1.0 to some lower value like 0.5. We then subtract this edge image from the grayscale image, resulting in a grayscale image with all the edges darkened.
Once this is fed through the algorithm, it is more likely that a dot will be placed on the edges, because they are darker in the input image.

Finally, we apply a slight jitter to the pixel values ((0,0) might become (-0.02, 0.04) etc.) to prevent the dots from being too grid-like as seen below.

![](/img/stippling_grid.jpg)

The results are very realistic and detailed stippling pieces, even more emphasized by using a very fine point felt pen as opposed to the sharpie that we used for line drawings.

![](/img/stippling1.png)

![](/img/stippling3.jpg)

![](/img/stippling4.jpg)

In this rose image you can really see the darkened edge effect!

![](/img/stippling5.jpg)


# Accolades

![](/img/nth.jpg)

We presented the robot at the Nth Degree Gallery Halloween Show 2019 (left) and the State of North Carolina Undergraduate Research and Creativity Symposium at Duke University (right).