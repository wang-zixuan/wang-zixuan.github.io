---
layout: post
title: "<span class='nav-date'>[Jun 25]</span> Event Stereo Using a Spiking Neural Network"
parent: 2023
grand_parent: Posts
permalink: /posts/2023/event_stereo
nav_order: 0
---

## Dense Event Stereo Depth Estimation by a CNN-guided Spiking Neural Network

### Background
This project was done when I was a Graduate Research Assistant at Nanjing University. 
I had the chance to learn what is a Spiking Neural Network, how to train a Spiking Neural Network, and how to empower an SNN with a CNN.

The paper is available [here](https://drive.google.com/file/d/1HT9fj10f4s3iTmjqChPuPgspGkV9XXZz/view).

### Introduction
Inspired by human retina, event-based sensors (event cameras), such as Dynamic Vision Sensor (DVS) and Dynamic and Active-pixel Vision Sensor (DAVIS), are designed to detect logarithm illumination change and output asynchronous binary event stream, which can achieve high dynamic range (∼100dB), microsecond temporal resolution, low latency, and much lower power consumption. With the wide application of event cameras in different fields, the binocular event camera systems are proposed for stereo estimation, and many CNN-based algorithms have got promising results. However, the extensive computational cost of CNN-based algorithms greatly impairs the main advantages of event cameras, i.e., the high speed, low latency and low power consumption caused by the sparse events.