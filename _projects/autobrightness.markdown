---
layout: post
title:  "Auto Screen Brightness"
date:   2019-12-12 12:12:00 +0100
categories: jekyll update
---
This repo provides the tools to implement an automatic control of the screen brightness.

My (naive) approach is to consider that the desired screen brightness depends on
 - the **ambient light**: dark room vs broad daylight
 - the **screen content**: reading text black on white? white on black? watching a movie?]
 - the OS native **night-light**

The tools for taking the screenshots and webcam pictures are, of course, system dependent. The corresponding functions for Ubuntu 18.04 are stored in functions.py
It would be desirable to have a couple of light sensors to check the light conditions in the front and back of the screen, maybe one day I'll set up an arduino, but for now I'll just use the built-in webcam to take picture in order to get the ambient light brightness.

![Model Scheme](/assets/images/autobrightness/auto_brightness.png)

Now, the problem to correlate these variables with the desired screen brightness can be addressed in many ways. Probably it's easy to set some thresholds but I wanted to experiment with TensorFlow 2.0 and learn a bit more about neural networks.

In order to use this codes we'll have to go through three phases:
1. Recollection of data (at least a few days)
1. Training of the model (minutes)
1. Enjoy (forever)

## Recollecting Data
Every supervised learning model requires some data to learn from. In this case this need is a great chance to let the system learn about our habits.
smart_take.py is a script that will register an input-output tuple into the data file every time there is a change in the screen brightness.
If the data collection is too slow you can set a script to turn up and down the screen brightness a little bit so several very similar events are registered.
This procedure will be also useful to fix particular situations in which the neural network does not perform well enough.

## The model
Not every color is equally easy to see in certain light conditions. For this reason I decided to feed the neural network (NN) information coming from every color channel (as opposed to processing a black and white picture).

We are interested merely in the brightness of the screen and the ambient light so my first guess is to consider just some statistical quantities as input for the model, and the desired screen brightness as output.

In particular the system will take a screenshot and a picture from the webcam. After that it will compute the mean, variance and standard deviation of the pixel distribution for every channel.
This procedure results in as many as

`3 (mean, var, std) * 3 (RGB channels) * 2 (screenshot and webcam) + 1 (night-light status) = 19 numbers`

which will be the input of the NN.
The expected output will be simply the desired screen brightness. Naturally everything is normalized to the interval [0,1].

The architecture used is probably an enormous overkill, but the whole thing is fast enough, so I didn't try to optimize it yet.

## Enjoy
An easy and cheap way to use this system is to set up a shortcut to execute tryit.py. For instance in my case I just have to press `Ctrl+b` to automatically set the screen brightness.
