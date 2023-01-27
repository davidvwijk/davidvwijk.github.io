---
layout: post
title:  "Target Tracking using Deep RL"
date:   2022-4-15
excerpt: "Work published at SciTech 2023"
# project: false
thumbnail: "/project_files/trackingRL/thumbnail_tracking.png"
tag:
- Robotics
- Reinforcement Learning
- Research
comments: true
---

## Deep Reinforcement Learning Controller for Autonomous Tracking of Evasive Ground Target

[Give introduction to problem, mention scitech etc.]

## Abstract

Tracking the motion of an evasive, or even hostile, maneuvering ground target based on no percepts other than aerial images is a challenge that is relevant to military and commercial applications. This paper investigates the problem of tracking such a target using a multirotor unmanned air system with a non-gimballed optical sensor. The fixed sensor makes the problem challenging because the vehicle must use its dynamics and therefore attitude to change the sensor’s field of view. A Soft Actor-Critic reinforcement learning controller is developed to address the maneuvering ground target tracking problem for various target movement types including a sinusoidal motion, random motion, and evasive motion. The addition of ground occlusions which block the agent’s view of the target is also investigated. This is challenging because the reinforcement learning agent does not have information about the location or the existence of occlusions, but must still generate actions which track the target. Results in a simulated environment demonstrate the effectiveness of the learned policy. Consistent indefinite tracking is achieved with the mean distance of the target from the center of the image varying from 23 to 27 pixels for all motion types tested. Selection of the command rate, the rate at which the agent can generate actions, is also investigated. Faster command rates from the learned policy are shown to be less effective for tracking a maneuvering ground target since the agent needs to perform a greater number of correct sequential actions. Finally, the agent is able to successfully track a randomly moving target with the presence of occlusions.

Below, I will summarize the problem, theory and key results of this paper. For details please read the full paper <a style="color:blue" href="https://arc.aiaa.org/doi/10.2514/6.2023-0128"><u>here</u></a>.

## Problem Formulation

[add background to problem + relevant set up and perhaps RL introduction]
...

## Animations 

Below are select animations for the various possible environment and target combinations that were considered. While I only show two here, the rest of the animations can be found <a style="color:blue" href="https://drive.google.com/drive/u/2/folders/1ICLiRIn7pDjm0Jz6ZIfZYwaQ-mF_zwDG"><u>here</u></a>.

Things to note about the animations: 
<ol>
  <li>The agent is plotted in blue in the 3D plot, and the target is plotted in red. Additionally, the camera frame projection is plotted on the plane.</li>
  <li>The low-level controls, outputted by the NZSP controller are plotted as the actions.</li>
  <li>In the top right corner, the location of the target in the agent's image frame is plotted - ideal performance has the agent keeping the target near the center of the image frame. </li>
  <li>The animations are significantly sped up - the RL agent is able to conistently track the target for over 12 minutes.</li>
  <!-- <li></li> -->
</ol>

<video width="720" height="405" controls>
  <source src="/project_files/trackingRL/rand_evasive_noOcclusions_vanWijk.mp4" type="video/mp4">
</video>
<center> <font size="+.6"> Randomly Moving Evasive Target without Occlusions </font> </center>

<video width="720" height="405" controls>
  <source src="/project_files/trackingRL/rand_customOcclusions_vanWijk.mp4" type="video/mp4">
</video>
<center> <font size="+.6"> Randomly Moving Target with Occlusions </font> </center>

## Acknowledgment

This research was sponsored by CCDC-ARL and was accomplished under Cooperative Agreement Number W911NF-21-2-0064. The views and conclusions contained in this document are those of the authors and should not be interpreted as representing the official policies, either expressed or implied, of the ARL or the U.S. Government. The U.S. Government is authorized to reproduce and distribute reprints for Government purposes notwithstanding any copyright notation herein.