---
layout: post
title:  "Target Tracking using Deep RL"
date:   2022-4-15
excerpt: "Work published & presented at SciTech 2023"
# project: false
thumbnail: "/project_files/trackingRL/thumbnail_tracking.png"
tag:
- Robotics
- Reinforcement Learning
- Research
comments: true
---

## Deep Reinforcement Learning Controller for Autonomous Tracking of Evasive Ground Target

During the fall semester of 2021, I spent a large chunk of my time tackling the problem of using deep reinforcement learning (RL) for target tracking of an evasive agent with and without the presence of occlusions. This problem is challenging for a few reasons: 

<ol>
  <li>A 6-Degree-of-Freedom (DOF) system is considered, with dynamics modelled after a real hexarotor. </li>
  <li>The visual sensor used by the agent is non-gimballed, meaning the agent must directly control its body dynamics to change camera frame. </li>
  <li>The agent does not have information about the location or the existence of occlusions. </li>
  <li>The agent does not have access to maps, terrain features, or landmarks. </li>
</ol>

## Abstract

Tracking the motion of an evasive, or even hostile, maneuvering ground target based on no percepts other than aerial images is a challenge that is relevant to military and commercial applications. This paper investigates the problem of tracking such a target using a multirotor unmanned air system with a non-gimballed optical sensor. The fixed sensor makes the problem challenging because the vehicle must use its dynamics and therefore attitude to change the sensor’s field of view. A Soft Actor-Critic reinforcement learning controller is developed to address the maneuvering ground target tracking problem for various target movement types including a sinusoidal motion, random motion, and evasive motion. The addition of ground occlusions which block the agent’s view of the target is also investigated. This is challenging because the reinforcement learning agent does not have information about the location or the existence of occlusions, but must still generate actions which track the target. Results in a simulated environment demonstrate the effectiveness of the learned policy. Consistent indefinite tracking is achieved with the mean distance of the target from the center of the image varying from 23 to 27 pixels for all motion types tested. Selection of the command rate, the rate at which the agent can generate actions, is also investigated. Faster command rates from the learned policy are shown to be less effective for tracking a maneuvering ground target since the agent needs to perform a greater number of correct sequential actions. Finally, the agent is able to successfully track a randomly moving target with the presence of occlusions.

Below, I will summarize the problem, theory and key results of this paper. For details please read the full paper <a style="color:blue" href="https://arc.aiaa.org/doi/10.2514/6.2023-0128"><u>here</u></a>.

## Reinforcement Learning

A learning based approach was used to tackle the target tracking problem for a few reasons: 

<ol>
  <li>In a learning based approach, the bulk of the computation can be done offline, making the deployed controller lightweight and efficient when deployed online. </li>
  <li>Terrain features, maps, or landmarks are not required for successful tracking as is the case in some of the literature for traditional control applications. </li>
  <li>Only the target location in the image frame is required. </li>
</ol>

With that established, I will give a very brief overview of the type of learning method used in this work: reinforcment learning. In reinforcement learning, the goal is to train an agent to learn the parameters $$\theta$$ of a policy (or controller) $$\pi_{\theta}$$ which maps the observation vector $$\boldsymbol{o}$$ of a partially-observable environment to the action vector $$\boldsymbol{a}$$ of the agent. In a fully-observable environment, this mapping is done from the state vector $$\boldsymbol{s}$$ to actions $$\boldsymbol{a}$$. The agent is evaluated using the scalar reward signal <em>r</em>, which, along with the states, is outputted by the environment. This is illustrated in the following figure.

<figure>
  <img src="/project_files/trackingRL/generalRL_diagram2.png" wdith="300">
  <figcaption> <b> Diagram of general reinforcement learning problem. </b> </figcaption>
</figure>

For the research herein, the environment is assumed to be fully observable.

## Problem Formulation

The target tracking problem and the approach taken can be formalized as a reinforcement learning problem. The specific RL algorithm used is Soft Actor-Critic (SAC) from <em> Stable Baselines3 </em> [1]. SAC is an off-policy actor-critic algorithm that is designed to maximize expected reward while also maximizing entropy [2]. The environment is represented by the UAS, camera, ground target, and occlusions in certain cases. The state-space contains the aircraft states and target pixel position in the camera frame, $$X_T$$ and $$Y_T$$. The aircraft states are internal North-East-Down (NED) position (<em>x</em>, <em>y</em>, <em>z</em>), Forward-Right-Down (FRD) body frame translational velocities (<em>u</em>, <em>v</em>, <em>w</em>), FRD body frame rotational rates (<em>p</em>, <em>q</em>, <em>r</em>), and a 3-2-1 Euler angle rotation sequence from the NED frame to the FRD frame (yaw $$\psi$$, pitch $$\theta$$, and roll $$\phi$$). The agent makes high-level commands, with actions in the set given below, where $$z_{cmd}$$ is the commanded inertial down position (negative altitude), $$u_{cmd}$$ is the commanded body frame forward velocity, $$v_{cmd}$$ is the commanded body frame lateral velocity, and $$r_{cmd}$$ is commanded rotational rate about the body frame down axis (yaw rate when the vehicle is level).

$$ \begin{equation}
\mathcal{S} = \{X_T,Y_T,x,y,z,u,v,w,p,q,r,\phi,\theta,\psi\}
\end{equation} $$ 

$$ \begin{equation}
\mathcal{A} = \{z_{cmd},u_{cmd},v_{cmd},r_{cmd}\}
\end{equation} $$

## Animations 

Below are select animations for the various possible environment and target combinations that were considered. While I only show two here, the rest of the animations can be found <a style="color:blue" href="https://drive.google.com/drive/u/2/folders/1ICLiRIn7pDjm0Jz6ZIfZYwaQ-mF_zwDG"><u>here</u></a>.

Things to note about the animations: 
<ol>
  <li>The agent is plotted in blue in the 3D plot, and the target is plotted in red. Additionally, the camera frame projection is plotted on the plane.</li>
  <li>The low-level controls, outputted by the NZSP controller are plotted as the actions.</li>
  <li>In the top right corner, the location of the target in the agent's image frame is plotted - ideal performance has the agent keeping the target near the center of the image frame. </li>
  <li>The animations are significantly sped up - the RL agent is able to conistently track the target for over 12 minutes.</li>
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

## References

[1] Hill, A., Raffin, A., Ernestus, M., Gleave, A., Kanervisto, A., Traore, R., Dhariwal, P., Hesse, C., Klimov, O., Nichol, A.,
Plappert, M., Radford, A., Schulman, J., Sidor, S., and Wu, Y., “Stable Baselines,” https://github.com/hill-a/stable-baselines,
2018.

[2] Haarnoja, T., Zhou, A., Abbeel, P., and Levine, S., “Soft Actor-Critic: Off-Policy Maximum Entropy Deep Reinforcement
Learning with a Stochastic Actor,” , 2018. https://doi.org/10.48550/ARXIV.1801.01290, URL https://arxiv.org/abs/1801.01290.