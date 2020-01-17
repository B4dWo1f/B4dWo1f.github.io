---
layout: post
title:  "RASP"
date:   2019-12-12 12:12:00 +0100
categories: jekyll update
---
In this project I try to build a ML-based corrector for the RASP calculations.

## RASP
[**RASP**](http://www.drjack.info/RASP/) (The **R**egional **A**tmospheric **S**oaring **P**rediction software) is a weather forecast program developed by Dr. John W. Glendening (DrJack). 
It is based on the [WRF-AWK](https://www.mmm.ucar.edu/weather-research-and-forecasting-model) model, which was developed primarily by the National Center for Atmospheric Research (**NCAR**), the National Oceanic and Atmospheric Administration (**NOAA**) and the Air Force Weather Agency (**AFWA**).  
It is a mesoscale numerical weather prediction system which can simulate real or ideal atmospheric conditions. It has been proven very to be quite accurate and it is widely used by weather researchers and forecast agencies all over the world.

WRF calculations are quite accurate, but its installation, use, and post-process are kind of complicated. RASP aims to easy the technical details so it is easier for regular users to use the WRF and obtain accurate forecasts of small regions.

<details style="width:90%;margin-left:20px;">
<summary>TECHNICAL DETAILS</summary>
<p>The RASP calculations are performed in a 3-D grid, with shape <code>N<sub>x</sub> x N<sub>y</sub> x N<sub>z</sub></code>.
Common values for these numbers are <code>N<sub>x</sub> ~ N<sub>y</sub> ~ 100</code> and <code>N<sub>z</sub> ~ 40</code>.
The output of the WRF calculations is kind of complex. There are libraries to extract and explore the results, but providing a meaningful representation has proven to be a challenge.<br/>
<img align="right" src="/assets/images/RASP/sfcwind.jpg" style="width:50%; padding:5px;">
The correct way would be to set up an actual geographic information system (GIS) but building a custom GIS server is not trivial and including all the data (scalar and vector fields) may take me some time.
In the mean time, there are some solutions out there to visualize the results, but I built my own library to plot the data. It can be checked <a href="https://github.com/B4dWo1f/RASPlots/">here</a> and the results are shown in a <a href="http://meteonube.hopto.org/">personal website</a> where a bit of javascript allows some tuning of the maps.</p>

RASP calculates several properties ranging from temperature or wind speed/direction to complex thermodynamic properties like the <a href="https://en.wikipedia.org/wiki/Convective_available_potential_energy">CAPE</a> or the <a href="https://en.wikipedia.org/wiki/Convective_inhibition">CIN</a>. Since my main interest is paragliding, the first properties I will try to improve are the wind speed and direction (although the rest are equally important!).
</details>

## ML-correction
The equations for Weather forecast are known to show [chaotic behaviour](https://www.ecmwf.int/en/elibrary/16927-chaos-and-weather-prediction). There is no way around it, that is just the way nature works.  
Of course, this does not mean that weather predictions cannot be improved!  
One of the most known effects in chaotic systems is the decay of their prediction capabilities over time.

This can be checked using a number of on-line services, here I include an example in which multiple models are confronted for 1 full week predictions.

<figure class='fig_plot'>
   <img src="/assets/images/RASP/meteoblue.png">
   <figcaption>
      Meteoblue multi-model forecast for Boston. The models differ more and more for longer predictions.
   </figcaption>
</figure>

(you can find a similar one [here](https://www.meteoblue.com/en/weather/forecast/multimodelensemble/boston_united-states-of-america_4930956))  
For the first day, all models agree reasonably well. For the second day a few models show a strong discrepancy, but most of them remain very similar. After that, chaos unleashes, making a reasonable prediction impossible. Actually, the reason for the discrepancy on the second day can be explained by the transition of a cyclone over the area, but that is another issue.

## The idea
Every day we get calculations for the state of the atmosphere for N days (typically N~4, longer than that is meaningless). This means that the forecast for any given day is calculated N times. The first time (let us call it *T-N* prediction) will be the less accurate, then it will improve over time until it is calculated on the same day (*T-0*) which should yield the most accurate forecast possible.  
The idea is to use the T-N prediction as input and the T-0 prediction as output of a Neural Network.  

Let me point out that I am not sure if this procedure should work. Weather is a very complex system, which is known to be chaotic, and ML is not guaranteed to work in these circumstances.
My line of thought is that the "real" weather (T-0) is somewhat similar to that predicted T-N days before. If the general situation is anticyclonic, the similarity T-N/0 is usually higher, if there are cyclones, rain, storms, it may differ quite a bit.  
Knowing this, and providing enough "general information" there is a chance a model can learn to correct the T-N calculations based on the experience of previous forecasts.

<details style="width:90%;margin-left:20px;">
<summary>TECHNICAL DETAILS</summary>
For now I will focus on improving the prediction for the surface wind. RASP provides a matrix for the surface wind with dimensions (N<sub>x</sub>, N<sub>y</sub>). Actually it provides one such matrix for the wind speed and another one for its direction.
Each of the elements of these two matrices represent the wind (vector) in a certain geographical point.
<figure class='fig_plot'>
<img src="/assets/images/RASP/comparison.png">
<figcaption>
Output-Input sample. The figure on the left is the most accurate calculation for a given day, the figure on the right is the less accurate one, calculated one day before.
</figcaption>
</figure>
In order to generate the training data-set I will get those two matrices, namely <code>sfcwindspd</code> and <code>sfcwinddir</code>, at two different levels of prediction, T-1 and T-0. The first one will be the input, and the second one will be the output.
I have 2394 examples which I will split ~80-20% for the training/testing sets. It is not much, but it is what I got so far.<br/>
This procedure results in a <code>93x123x2=22878</code> input and the same size output.
</details>

## First attempts
Let us start easy and grow in difficulty. As a first test I will build a model that directly connects the input and output through a series of fully connected layers, a model commonly known as MultiLayer Perceptron (MLP).


### MLP
I consider this a good starting point since, given the necessary depth, it should be able to capture *any* correlation among inputs. This simple model works fine for simple cases but it may not be enough for this case.

I chose the wind speed and direction

<figure class='fig_plot'>
   <img src="/assets/images/RASP/mlp.png">
   <figcaption>
      MLP test performance. The left column shows the input (top) and output (bottom) used. The bottom right panel shows the Neural Network expected result.
      The upper right panel is a visual aid to locate easily the biggest errors.
   </figcaption>
</figure>

### CNN
The previous model is way to simple and shallow to capture the complexity of the system. In addition the computer I am using for this is a bit limited so I am not far from the RAM limit considering the size of the model and the training data.



### CNN + RNN
