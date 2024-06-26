---
layout: post
title:  "Packing Puzzle Solution using Stochastic Beam Search"
date:   2022-04-15
excerpt: "CSCE625: Artificial Intelligence at TAMU"
project: true
thumbnail: "/project_files/packingpuzzle/packingpuzzle_thumbnail.png"
tag:
- AI
- Coursework
- Search
comments: true
---

In the spring of 2022, I took the course <a href="https://cse-robotics.engr.tamu.edu/dshell/cs625/"><b>CSCE625: Artificial Intelligence</b></a> at Texas A&M University, taught by Professor Dylan Shell. We learned fundamental concepts and techniques of intelligent systems, representation and interpretation of knowledge on a computer, search strategies and control, and we discussed active research areas and applications such as notational systems, natural language understanding, vision systems, planning algorithms, intelligent agents and expert systems. 

As part of the course, we were tasked with addressing the following prompt:

## Problem

Given some rectangle of height <em>h</em> and length <em>l</em>, such that area <em>h</em> × <em>l</em> = 20 · <em>k</em> for some <em>k</em> ∈ N, is there a way to pack exactly <em>k</em> collections of tetrominoes into the rectangle? The tetrominoes are shapes made from four squares (here ‘tetra’ meaning four, ‘-ominoes’ from dominoes). There are five basic shapes and you are likely familiar with them from the classic game tetris. Like tetris, we will consider shifts
and rotations (by multiples of 90&deg;); unlike tetris, we also allow the inclusion of a reflection. 

{% capture images %} https://raw.githubusercontent.com/davidvwijk/davidvwijk.github.io/master/assets/img/packingpuzzle_prompt.png {% endcapture %} {% include gallery images=images cols=1 %}


## Preamble: Graph Theory Proof

Although we were asked to use local search explicitly in the assignment, I was inspired by the proof shown <a href="https://mindyourdecisions.com/blog/2018/06/21/can-you-solve-the-tetris-riddle-aka-tetromino-puzzle/"><b>here</b></a> to see if I could determine if a packing existed, given only the height of the board <em>h</em> and the length of the board <em>l</em>. Using a graph theory coloring proof, it can be shown that a board will only have a solution if it's dimensions satisfy the following two conditions:

<ol>
  <li>(<em>h</em> &ge; 3) and (<em>l</em> &ge; 3)</li>
  <li>(<em>h</em>*<em>l</em>)/20 (<em>k</em> collections of pieces) is even</li>
</ol>

If we think of the board as an <em>h</em>*<em>l</em> checkerboard, with alternate light and dark squares, and the pieces are colored in this alternating way, we come to an interesting observation. It can be see that for all the tetrominoes except for the "T" shape, it is possible for each piece to be colored with precisely 2 light and 2 dark squares. The T shaped tetrominoe however, can only be colored with either 3 dark squares and 1 light, or the opposite - 3 light and 1 dark. This is illustrated in the figures below.

<!-- <img src="/project_files/packingpuzzle/tetrominoes.png" alt="Tetrominoes 1">
<img src="/project_files/packingpuzzle/tshape.png" alt="Tetrominoes 2" > -->

<figure>
  <img src="/project_files/packingpuzzle/tetrominoes.png" alt="Tetrominoes 1" style="width:871px;height:363px;">
  <figcaption>Figure 1: Pieces colored in checkerboard pattern, excluding T shaped piece</figcaption>
</figure>

<figure>
  <img src="/project_files/packingpuzzle/tshape.png" alt="Tetrominoes 2" style="width:350px;height:224px;">
  <figcaption>Figure 2: T shaped piece colored in checkerboard pattern</figcaption>
</figure>

When coloring the board in the checkerboard manner, the board will always have the same number of dark and light colored squares. Therefore, for a perfect packing solution to exist, we need to be able to have the same number of light pieces as dark pieces on in the set of puzzle pieces to fill the board exactly. Since in either configuration shown above, the T shaped piece will have an odd number of light and dark pieces, the only way we can find a perfect packing for a given board, is if we have an even number of T shaped pieces. It follows then, that since a valid solution is only satisfied if we used exactly <em>k</em> collections of pieces, we have a valid packing solution for only even values of <em>k</em>. Therefore, to answer the prompt: "Is there a way to pack exactly <em>k</em> collections of tetrominoes into the rectangle?" we only need to satisfy the condition that <em>k</em> is even, and that both of the dimensions are greater than or equal to 3. 

With all this said, I still implemented a local search algorithm to determine if a packing solution could be found, because if the prompt was changed to: "<it>How</it> can you pack exactly <em>k</em> collections of tetrominoes into the rectangle?" then the above method would get us nowhere. My code first determines if a packing solution can be found using the above criteria, displays this information to the user, and then proceeds in attempting to find the solution using local search.


## Representation

For this problem, local search can be an effective solution technique since the path to reach the goal is irrelevant. Besides the hint to use local search, we were given no further instruction, so initially, much of my time was spent in problem representation. To tackle this problem I decided to use MATLAB, since I wanted to represent all the possible tiles using a 4x4 matrix of either zeros or ones, and I like to use MATLAB when I'm mostly dealing with matrices. This allows all of the possible pieces along with reflections and rotations to be represented in a uniform format. For example, I would represent a T shape, with a single 90 degree rotation as:

$$\begin{bmatrix} 1 & 0 & 0 & 0 \\ 1 & 1 & 0 & 0 \\ 1 & 0 & 0 & 0 \\ 0 & 0 & 0 & 0 \end{bmatrix}$$

and I would represent any O block as: 

$$\begin{bmatrix} 1 & 1 & 0 & 0 \\ 1 & 1 & 0 & 0 \\ 0 & 0 & 0 & 0 \\ 0 & 0 & 0 & 0 \end{bmatrix}$$

This way, each puzzle piece and its possible orientation was represented uniquely. Thus, all the possible orientations of the pieces can be represented with 19 4x4 matrices, and in my code I have kept these pieces separate. The only key point is that each piece should be contained in the 4x4 matrix as close to the top and left sides of the matrix as possible. This is because I will use the (1,1) position of the 4x4 matrices to describe the location of the pieces.

The board is represented by an (<em>h</em>+3) by (<em>l</em>+3) matrix. The pieces are placed on the board according to their grid location, and thus given the 4x4 matrices and their location on the board, it is fairly straight forward to generate the board. The reason for the additional 3 rows and columns in the board matrix is to allow for “overhang” where the pieces may be located at row <em>h</em> or column <em>l</em>, and thus part of the piece could be “hanging off” of the <em>h</em> by <em>l</em> section of the board. With this representation, the goal state will be all ones in the <em>h</em> by <em>l</em> section of the board, and 0’s in the remainder of the board. In my code and report I call this overhang section of the board the “gutter”.

## Algorithm

To solve the packing puzzle problem, I used a modifed stochastic beam search. I wrote everything from scratch including all the necessary subfunctions to deal with creating the board representation, generating the puzzle pieces, getting the successors, and evaluating the board. 

The main script will prompt the user for the vertical board dimension, <em>h</em> and the horizontal dimension, <em>l</em>. Then, before doing anything, the script checks the conditions described above, and if <em>h</em> and <em>l</em> don't satisfy those criteria, the script will return "No solution possible". If this check is passed, then I will initialize 50 random beams, where each beam represents <em>k</em> collections of puzzle pieces. Using an evaluation function that will be described in the heuristic section below, each collection of initially random puzzle pieces is scored. Within each collection, we will have <em>k</em>*5 pieces, which are represented by 4x4 matrices, with an associated (x,y) position corresponding to the location of that particular piece on the board, and two identifiers which encode which type of piece it is (i.e. T, O, S/Z etc.) and which version of that piece it is (i.e. rotated 90&deg;, reflected, etc.). I also then compute the score that would result in a successful packing using the evaluation function, which is used in the end condition. 

After the initial set of random beams are generated, we enter a while loop that will only terminate upon finding a board that has the same score as the end condition score calculated above (or upon timeout). Now, we finally begin the local beam search. For each beam, we iterate through each piece it contains, and will generate 5 successors for each piece, generated through actions which are described below. Each successor to the piece will result in a new, slightly different board, which will be evaluated using the evaluation function, and the score will be stored along with the new modified collection. The terminating condition is checked here, and if any of the successors form a solution board, the while loop will exit, and the script will display "Solution found in (number) beam iterations", where (number) is a count that keeps track of the iterations. This is done for all the pieces in all of the collections for all of the beams, which will (number of beams)*(5*<em>k</em>)*5 successors. Each successor here is <em>k</em> collections of pieces, with an associated score. 

The successors are then sorted in ascending order, since a lower score indicates a better board. The next set of beams are selected by choosing a certain proportion of beams randomly from the successors, and the remainder of the beams are selected greedily. The proportion of randomly selected beams will decrease with the number of iterations, with it starting extremely high and decreasing by .01 with each iteration, with a minimum random proportion of .1. I found this to be crucial to the performance of the algorithm, since when I had a purely greedy beam selection, I quickly converged on one collection of pieces. Of course this method doesn't work so well if the number of beams is not fairly high, which is why I generally kept the number of beams between 20 and 100 when testing the algorithm. The process continues until we either achieve the goal state, or until the minimum score has remained the same for 20 iterations. If this is the case, we perform a random restart, populate the beams with entirely random collections, and start again. This process will run until we reach the goal state, or until we reach the timeout time.

## Action Space

The successors in the local search space will be generated using 5 possible actions for each puzzle piece in <em>k</em> collections for each beam. The detailed code can be found on my github.

<ol>
  <li>Moves the piece up on the board by a random step between 1 and <em>k</em> slots</li>
  <li>Moves the piece down on the board by a random step between 1 and <em>k</em> slots</li>
  <li>Moves the piece right on the board by a random step between 1 and <em>k</em> slots</li>
  <li>Moves the piece left on the board by a random step between 1 and <em>k</em> slots</li>
  <li>Pick the next version of the current piece, in a cyclic pattern</li>
</ol>

It should be noted that the moves will only be executed if they do not take the piece out of the board (i.e. the new piece location calculated by the action must be within the <em>h</em> x <em>l</em> matrix making up the valid board). To expand further on action 5, we examine the piece we are generating successors for, and from an ordered list of possible versions of that piece, we select the next in the list, or loop back to the first possible version of the piece. For the T piece, we have 4 versions of this piece, and thus if the piece we are generating successors for is the 3rd version of the T shape, one of the successors of that piece will be the next version of that piece, without changing the location of the piece. In this way we are able to capture all the possible types of movements, rotations and reflections as actions. The reason for making the step of the movement of pieces range from 1 to <em>k</em> is so that the successors can be slightly more diverse, which helped with addressing local minima. I also found it useful for the range of steps to scale with board size for greater generality. 

## Heuristic

The heuristic is a cost function that is used to score each <em>k</em> collection of pieces that make up a valid board. Here, the lower the score, the better the board, or closer to the solution the board is. There are four components to the score which are enumerated below.

<ol>
  <li>(c<sub>1</sub>): Cost associated with overlapping pieces which is calculated by the maximum number on the board, times a tunable scalar value</li>
  <li>(c<sub>2</sub>): Cost associated with gaps in the board (excluding the gutter). Sums up all the zeros on the board, times some tunable scalar value</li>
  <li>(c<sub>3</sub>): Cost associated with overlap in the gutter. This cost starts off very low and gradually increases with the number of iterations. The idea here is to first allow pieces to move around more freely with some overhang but slowly converge onto the tightly packed board</li>
  <li>(c<sub>4</sub>): Reward associated with filled rows, which is largest for the lowest row. The purpose is to incentivize filling from the bottom row first, which I found to be helpful. Since this is a reward, it subtracts from the cost,</li>
</ol>

$$ \begin{equation} \label{heuristiceqt}
c_{total} = c_{1} + c_{2} + c_{3} - c_{4}
\end{equation} $$

It took quite a lot of trial and error to tune the scalars associated with each of the score components, and I tried to do what I could to reduce the number of tunable parameters, in an attempt to make my algorithm as general as possible. The specific values for the scalars associated with each component can be found in the report.

## Report

If interested, you can access my full report with all the details of my solution approach using the link below: 

<a href="/project_files/packingpuzzle/625_packing.pdf" class="btn"> Report PDF </a>

## Code 

<a href="https://github.com/davidvwijk/packing-puzzle"><b> Access the code </b></a>