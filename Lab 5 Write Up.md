##Introduction
The goal of this lab was to implement a hand detection algorithm using the GPU. We were provided with a variety of sample code which we could pick and choose from as a starting point. The ultimate goal was for the code to be able to output values so as to control a game of Pong.

##Design Methodology
I initially tested the provided hand.c file and found that it already performed adequately for me, detecting the motion without a problem. Because of this, I decided to port this code to the GPU. To achieve this, I would first have to port the code to C++ since the GPU openCV functions were based on the C++ functions while the provided code used C functions. The C code used a lot of hacks with the variables since it did not have access to things such as vectors making it overly complex.

The provided code was very robust, able to detect individual fingers and the palm deperately. I was only really interested in finding and tracking the convex hull, so I could ignore all of the finger detection portions of the code. This allowed me to only have to port the filtering, thresholding, and hull finding parts of the code. This was fortunate because because some of the functions to find the fingers did not have an exact analogue in C++.

##Testing Methodology
I first tested to see if the hand detection algorithm actually performed adequately as provided to see if any modifications would need to be made. I tested it by running the code on the CPU and printing out the variables that were of interest to me, specifically the X and Y positions of the hand it detected.

I found that the code worked really well. It detected my hand at various distances without a problem and I could move my hand and it would track it consistently. The only problem I found was that occasionally for a split second it would pick up another object as a hand before resetting back to my hand. I remedied this by requiring the hand to move fewer than 4% of the screen distance between frames. This allowed me to move my hand fairly fast and still detect, but objects in another region of the camera would not be picked up.

I then tested to see what kind of performance increase I was getting from running the code on the GPU. On the CPU, I was getting framerates hovering between 5 and 8 FPS. This was really unacceptable since it caused considerable lag between input motion and output coordinates. Furthermore the output resolution was greatly diminished since a fast moving object would seemingly jump many pixels at a time. On the GPU, I was initially getting framerates between 5 FPS and 22 FPS. This was better, but it would still occasionally dip as low as the CPU code. The biggest problem with this was that the frames were buffered, so after a lag spike, all of the queued up frames would be released in quick succession creating a wierd stutter step from too slow to too fast. I tried to remedy this by flushing the buffer constantly, but this made the framerate a constant 8 FPS, which while an improvement over the CPU code, was still unacceptably slow.

To lessen the problem, I lowered the rendering resolution of the camera output to 180 pixels by 120 pixels. This lowered the burden on the GPU allowing the code to run at more acceptable frame rates.


##Results and Discussion
A graph of the final framerates I achieved can be seen below. The algorithm usually runs between 20 and 40 FPS, which is acceptable and feels decently real time. However, it occasionally spikes to 55 and dips 10. These always come in tandem, so that there is a split second of lag followed by a split second of speed up, which feels very unnatural. Thus even though 10 FPS is better than the CPU code, it feels much worse because of its inconsitency.

![](https://raw.githubusercontent.com/SKrupa/E190u-Lab5/master/latency graph.png)

Simply increasing the framerate will never solve this issue since the stutterstep will exist regardless of framerate. To truly make the hand detection feel real time, the camera buffer issue would need to be resolved.

Since I could not port all of the code successfully, the code cannot detect find motion of fingers or hand rotations. For Pong this is not a problem, but if someone wanted to use this for a different motion control application, the code would need to be extended to accomodate their specific applications.

##Conclusion
In the end, the program managed to detect a hand and output its coordinates. The movement feels fairly jerky, so I personally wouldn't want to play any games that require fine movement and control, but for some casual applications it could work fairly well.

I spent about 4 hours porting the code to C++, 30 minutes porting that to the GPU and about 2 hours trying to optimize the the framerate.
