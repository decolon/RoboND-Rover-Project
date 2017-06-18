## Project: Search and Sample Return
---

**The goals / steps of this project are the following:**  

**Training / Calibration**  

* Download the simulator and take data in "Training Mode"
* Test out the functions in the Jupyter Notebook provided
* Add functions to detect obstacles and samples of interest (golden rocks)
* Fill in the `process_image()` function with the appropriate image processing steps (perspective transform, color threshold etc.) to get from raw images to a map.  The `output_image` you create in this step should demonstrate that your mapping pipeline works.
* Use `moviepy` to process the images in your saved dataset with the `process_image()` function.  Include the video you produce as part of your submission.

**Autonomous Navigation / Mapping**

* Fill in the `perception_step()` function within the `perception.py` script with the appropriate image processing functions to create a map and update `Rover()` data (similar to what you did with `process_image()` in the notebook). 
* Fill in the `decision_step()` function within the `decision.py` script with conditional statements that take into consideration the outputs of the `perception_step()` in deciding how to issue throttle, brake and steering commands. 
* Iterate on your perception and decision function until your rover does a reasonable (need to define metric) job of navigating and mapping.  

[//]: # (Image References)

[image1]: ./misc/rover_image.jpg
[image2]: ./calibration_images/example_grid1.jpg
[image3]: ./calibration_images/example_rock1.jpg 

## [Rubric](https://review.udacity.com/#!/rubrics/916/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  

You're reading it!

### Notebook Analysis
#### 1. Run the functions provided in the notebook on test images (first with the test data provided, next on data you have recorded). Add/modify functions to allow for color selection of obstacles and rock samples.

To accomplish this I both used the provided color_threshold function, and wrote my own to help identify rocks.  The provided function was good for finding the ground.  To find obstacles I simply inverted the output which found the ground.  This was most easily accomplished by converting the ground output to a bool array and then invert that bool array.  

The rock thresholding was a little harder.  I wrote a new function that let me only select the values that were above one threshold and bellow another.  Then I used a color picker on the example rock image to choose the appropriate ranges.  The output was similar to the original color_threshold output, an array of 1's and 0's where the 1's are the pixels in the desired range.

#### 1. Populate the `process_image()` function with the appropriate analysis steps to map pixels identifying navigable terrain, obstacles and rock samples into a worldmap.  Run `process_image()` on your test data using the `moviepy` functions provided to create video output of your result. 

The basic process was to get the rover image, change the perspective, get the three thresholds, and then change each threshold into world coordinates.  I manually changed things to world coordinates by rotation, translating, scaling, and clipping, but I also could have used the provided function.  After I had each threshold in world coordinates I added them to the world map.  Eventually this map built up to represent the world.

Implementation also deals with the fact that the given script attempts to look at more images than there really are.  I check to make sure that the count in within the size of the image array.  If it is not, I return a black image.


### Autonomous Navigation and Mapping

#### 1. Fill in the `perception_step()` (at the bottom of the `perception.py` script) and `decision_step()` (in `decision.py`) functions in the autonomous mapping scripts and an explanation is provided in the writeup of how and why these functions were modified as they were.

Perception_step() was pretty much a direct copy of the notebook process_image() function.  Only real different was syntactic and a couple extra lines to convert things to polar coordinates.

There were two main changes to decision_step().  First, I keep a running list of the current velocity.  If the robot is in forward mode, but it not moving, I assume it is stuck and needs to stop/reposition itself.  I check if the robot is moving by looking at the first and last elements of the 10 element list.  If their difference is less that .01, then the robot is not moving.  

Second, when the robot is reorienting in the stop position, I make sure it keeps rotating until the mean navigable angle is +- 5 degrees.  This tells the robot to keep rotating until it is facing straight at the most open area.  


#### 2. Launching in autonomous mode your rover can navigate and map autonomously.  Explain your results and how you might improve them in your writeup.  

Results are pretty good.  I correctly map the open terrain, but at times think the walls are also open terrain.  Over time this error goes away though as I map more.  I do correctly get all the rocks, which is cool, but sometimes the robot can get stuck.  This happens two ways.  First, if a rock is straight in front of it, the robot will not move to avoid it because the mean angle is pointing straight ahead.  Eventually the robot usually gets out of this situation, but it takes time.  The second is sometimes it gets stuck going in circles because the mean angle always points to one direction, and it never hits anything to make it change course.  I could get around this by checking it the turn value has not changed in a while.  Then I could rotate until the mean angle is +- 5 degrees and start again.  

Otherwise, there are many ways to improve the rover.  I could do wall following, come up with a more effective mapping strategy, have the robot pick up rocks, have it all work while the robot moves faster, and many more.  Sadly, I am behind in the class and cant devote too much more time to this project.  Maybe another time.  

**Note: running the simulator with different choices of resolution and graphics quality may produce different results, particularly on different machines!  Make a note of your simulator settings (resolution and graphics quality set on launch) and frames per second (FPS output to terminal by `drive_rover.py`) in your writeup when you submit the project so your reviewer can reproduce your results.**

Resolution = 800x480
Graphics Quality = Good
FPS = 20
