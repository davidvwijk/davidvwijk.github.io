---
layout: post
title:  "Packing Puzzle Solution using Beam Search"
date:   2022-4-15
excerpt: "Coursework for CSCE625: Artificial Intelligence"
project: true
tag:
- AI
- Coursework
comments: true
---

In the spring of 2022, I had the privelege of taking the course <a href="https://cse-robotics.engr.tamu.edu/dshell/cs625/"><b>CSCE625: Artificial Intelligence</b></a> at Texas A&M University, taught by Professor Dylan Shell. We learned fundamental concepts and techniques of intelligent systems, representation and interpretation of knowledge on a computer, search strategies and control, and we discussed active research areas and applications such as notational systems, natural language understanding, vision systems, planning algorithms, intelligent agents and expert systems. 

As part of the course, we were tasked with addressing the following prompt:

Given some rectangle of height <em>h</em> and length <em>l</em>, such that area <em>h</em> × <em>l</em> = 20 · <em>k</em> for some <em>k</em> ∈ N, is there a way to pack exactly <em>k</em> collections of tetrominoes into the rectangle? The tetrominoes are shapes made from four squares (here ‘tetra’ meaning four, ‘-ominoes’ from dominoes). There are five basic shapes and you are likely familiar with them from the classic game tetris. Like tetris, we will consider shifts
and rotations (by multiples of 90◦); unlike tetris, we also allow the inclusion of a reflection. 

{% capture images %} https://raw.githubusercontent.com/davidvwijk/davidvwijk.github.io/master/assets/img/packingpuzzle_prompt.png {% endcapture %} {% include gallery images=images cols=1 %}

For this geometric problem, local search can be an effective solution technique since the path to reach the goal is irrelevant. Besides the hint to use local search, we were given no further instruction, so initially, much of my time was spent in problem representation. To tackle this problem I decided to use MATLAB, since I wanted to represent all the possible tiles using a 4 by 4 matrix of either zeros or ones, and I like to use MATLAB when I'm mostly dealing with matrices. This allows all of the possible pieces along with reflections and rotations to be represented in a uniform format. For example, 

      
## Report

<a href="/project_files/625_packing.pdf" class="btn"> Report PDF </a>