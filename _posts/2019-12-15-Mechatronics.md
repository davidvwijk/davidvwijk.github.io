---
layout: post
title:  "ASML Sponsored Mechatronics Competition"
date:   2019-12-15
excerpt: "Robotics competition associated with MAE 3780 at Cornell"
project: true
tag:
- Robotics
comments: true
---

## Task

The objective of the MAE 3780 Mechatronics Final Project was to design and build a robot to participate in the tournament style “Cube Craze” competition. Using identical design and cost constraints, groups of two and three students put their robots head to head in each match, ultimately attempting to bring back a larger number of cubes onto their respective starting zones after a period of one minute. The robots were required to be autonomous (ie. could not receive control inputs from the participants during the matches) besides starting and stopping. To control the robot’s actions, each group made use of an Arduino Uno and a number of sensors. 
      
## Mechanical Design

Our objectives for the functionality of the robot were as follows:

<ol>
  <li>The robot should be able to grab blocks in addition to pushing blocks.</li>
  <li>The robot's arms should be able to move after they are deployed (full positional control).</li>
  <li>Sensor placement should allow for the robot to know if it is near a black edge at all times.</li>
  <li>The robot's arms should be able to move independently of each other.</li>
</ol>

The objectives listed above are in order of most important to least important in our design priority. After watching the competition videos from the previous year, we saw that the teams who were most effective used a large arm and a pushing method to get the blocks on the other team's side. While our competition objective is the opposite (we need to "retrieve" blocks), the method of pushing would still be effective if we could first clear a path to get to their side and begin pushing towards ours. Thus, we decided that our arms needed to be able to grab blocks at the start and subsequently push blocks, which inspired our first design objective. Figure 1 shows the robot in "collection mode" with its arms past perpendicular to the robot. At the ends of the arms, we added small pieces of acrylic to be sure that the blocks would stay in the vicinity of the robot.

<figure>
  <img src="/project_files/mechatronics/collectionMode.jpg" alt="">
  <figcaption>Figure 1: Final Robot, Collection Mode </figcaption>
</figure>

The second and fourth objectives are closely related; our goal was to be able to switch the arms between modes of collecting and pushing. Therefore, we could not have static arms that deployed at the start of the round and never moved afterwards. The second objective could be met using one motor attached to a gear train that moves both arms the same amount, at the same time. Although our overall strategy would work with both arms being rotated the same amount at all times (which would be the case with 1 motor), we also wanted to attempt to allow the arms to be at different positions relative to each other (i.e. one parallel to the chassis and one perpendicular to it), hence the fourth objective. We achieved this goal by using two motors, one for each arm after ensuring that we had enough money in our budget and ports on our Arduino to achieve this. Figure 2 clearly shows the connections between the motors and arms. Gears fixed to the motors mesh with gears built into the arm design, allowing them to rotate over 180 degrees. Figure 3 shows a more in-depth look at the intricacies of the arm design.

<figure>
  <img src="/project_files/mechatronics/underbelly.jpg" alt="">
  <figcaption>Figure 2: Arm Actuation Mechanism </figcaption>
</figure>

<figure>
  <img src="/project_files/mechatronics/arm.jpg" alt="">
  <figcaption>Figure 3: CAD Render, Arm Assembly </figcaption>
</figure>

The third objective was important because when the arms were fully extended in front of the chassis and the QTI sensor was underneath the chassis, the robot would have deposited all of the collected blocks off of the arena before sensing the black border. There would also have been losses in algorithm efficiency if the robot was pushing blocks until a sensor underneath the robot recognized the black border. To achieve this objective, we attached one QTI sensor at the end of each arm. In Figure 1, the duct taped QTI sensors can be easily seen. A third QTI sensor was mounted at the back of the chassis to prevent the robot from driving off of the board backwards.

## Algorithm

For our final algorithm, we split up our code into three different phases. Figure 4 features the strategy flow chart, where the colored boxes indicate the start of a phase. The first phase of our algorithm was intended to move approximately a third of the blocks onto the starting side within the first 5-10 seconds. Based on if the robot started on the left, right or center, the robot moved forward and turned ninety degrees at the interface of the colors, and then traveled along the border collecting cubes for 2.5 seconds. Then, the robot turned and brought cubes back to the starting side. This initialized the second phase, in which the robot retracted the arms to the starting position and reversed. Retracting the arms before reversing onto the opposite color made sure no stray blocks were accidentally pushed onto the opposite side during this maneuver. After reaching the opposite side, the third phase began. In this infinite loop, we intended we to angle the robot to one side slightly, and then perform a series of angled forward and reverse motions, effectively having an infinite pushing sequence. Segmenting our code into different phases was very helpful as it allowed us to reuse different parts of our code even if the starting positions varied. 

<figure>
  <img src="/project_files/mechatronics/flowchart.jpg" alt="">
  <figcaption>Figure 4: Final Competition Flowchart</figcaption>
</figure>

## Report

<a href="/project_files/mechatronics/3780.pdf" class="btn"> Report PDF </a>
