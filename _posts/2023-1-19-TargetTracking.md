---
layout: post
title:  "Target Tracking using Deep RL"
date:   2022-4-15
excerpt: "Work published at SciTech 2023"
# project: false
# research: true
thumbnail: "/project_files/trackingRL/thumbnail_tracking.png"
tag:
- Robotics
- Reinforcement Learning
- Research
comments: true
---

## Deep Reinforcement Learning Controller for Autonomous Tracking of Evasive Ground Target [<a style="color:blue" href="https://arc.aiaa.org/doi/10.2514/6.2023-0128"><u>link</u></a>] 

<strong>Abstract: </strong>

Tracking the motion of an evasive, or even hostile, maneuvering ground target based on no
percepts other than aerial images is a challenge that is relevant to military and commercial
applications. This paper investigates the problem of tracking such a target using a multirotor
unmanned air system with a non-gimballed optical sensor. The fixed sensor makes the problem
challenging because the vehicle must use its dynamics and therefore attitude to change the
sensor’s field of view. A Soft Actor-Critic reinforcement learning controller is developed to
address the maneuvering ground target tracking problem for various target movement types
including a sinusoidal motion, random motion, and evasive motion. The addition of ground
occlusions which block the agent’s view of the target is also investigated. This is challenging
because the reinforcement learning agent does not have information about the location or the
existence of occlusions, but must still generate actions which track the target. Results in a
simulated environment demonstrate the effectiveness of the learned policy. Consistent indefinite
tracking is achieved with the mean distance of the target from the center of the image varying
from 23 to 27 pixels for all motion types tested. Selection of the command rate, the rate at which
the agent can generate actions, is also investigated. Faster command rates from the learned
policy are shown to be less effective for tracking a maneuvering ground target since the agent
needs to perform a greater number of correct sequential actions. Finally, the agent is able to
successfully track a randomly moving target with the presence of occlusions.

## Animations 

