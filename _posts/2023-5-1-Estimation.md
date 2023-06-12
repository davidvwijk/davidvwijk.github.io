---
layout: post
title:  "Estimation of Dynamical Systems"
date:   2023-5-1
excerpt: "Summary of estimation algorithms with examples"
project: true
# thumbnail: "/project_files/reuleaux/mechanism_thumbnail.jpg"
tag:
- Estimation
comments: true
---

---> paragraph about course, and what was covered

## Estimation Algorithms

<strong>The Extended Kalman Filter: </strong>

The EKF is a universal method to estimate the state of nonlinear systems, based on utilizing a first-order Taylor series expansion to linearize the dynamics and measurement functions about the estimate of the current state. The basic procedure of an EKF implementation can be boiled down to two main steps: 1) propagation of the mean and covariance and 2) updating the current mean and covariance estimates.

Consider the general case of a nonlinear system model governed by

$$
\begin{equation}
    \boldsymbol{\dot{x}}(t) = \boldsymbol{f}(\boldsymbol{x}(t), \boldsymbol{w}(t))
\end{equation}
$$

$$
\begin{equation}
    \boldsymbol{z}_k = \boldsymbol{h}(\boldsymbol{x}_k,\boldsymbol{v}_k)
\end{equation}
$$

where $$\boldsymbol{x}(t)$$ is the state of the system at time $$\textit{t}$$ and $$\boldsymbol{w}(t)$$ is a zero mean white-noise process providing stochastic excitation to the dynamics. This process is assumed to have a constant power spectral density, $$\boldsymbol{Q}_{\textit{ww}}$$. The measurements utilized by the filter ($$\boldsymbol{z}_k$$) are governed by $$\boldsymbol{h}(\boldsymbol{x}_k,\boldsymbol{v}_k)$$, where $$\boldsymbol{x}_k$$ is the state of the system at time $$t_k$$, and $$\boldsymbol{v}_k$$ is the measurement noise sampled from a zero mean, white-noise distribution with constant covariance. Both $$\boldsymbol{h}(\boldsymbol{x}_k,\boldsymbol{v}_k)$$ and $$\boldsymbol{f}(\boldsymbol{x}(t), \boldsymbol{w}(t))$$ are assumed to be differentiable, with uncorrelated process noise and measurement noise. 

To begin the estimation process using an EKF, an initial estimate for the mean and covariance of the state is required, denoted $$\boldsymbol{m}_{x,k-1}$$ and $$\boldsymbol{P}_{xx,k-1}$$ respectively. For the propagation phase, the mean and covariance estimates are propagated according to

$$
\begin{equation}
    \boldsymbol{\dot{m}}_x(t) = \boldsymbol{f}(\boldsymbol{m}_x(t),\boldsymbol{0}_\textit{w})
\end{equation}
$$

$$
\begin{equation}
    \boldsymbol{\dot{P}}_{xx}(t) = \boldsymbol{F}_{x}(\boldsymbol{m}_x(t))\boldsymbol{P}_{xx}(t) + \boldsymbol{P}_{xx}(t)\boldsymbol{F}^{T}_{x}(\boldsymbol{m}_x(t)) + \boldsymbol{F}_{\textit{w}}(\boldsymbol{m}_x(t))\boldsymbol{Q}_{\textit{ww}}\boldsymbol{F}^{T}_{\textit{w}}(\boldsymbol{m}_x(t))
\end{equation}
$$

where $$\boldsymbol{0}_\textit{w}$$ is a $$\textit{w}-by-1$$ column vector with $$\textit{w}$$ being the dimension of the process noise. Here, $$\boldsymbol{F}_{x}(\boldsymbol{m}_x(t))$$ is the dynamics Jacobian relative to the state (Eq.~\ref{eq:jacobianDyn}) evaluated at the mean. Similarly, $$\boldsymbol{F}_{\textit{w}}(\boldsymbol{m}_x(t))$$ is the dynamics Jacobian relative to the process noise, evaluated at the mean, given by 

$$
\begin{equation}
    \boldsymbol{F}_{\textit{w}}(t) = 
    \frac{\partial \boldsymbol{f}(\boldsymbol{x}(t), \boldsymbol{w}(t))}{\partial \boldsymbol{w}(t)}\Bigr|_{\substack{~~~\boldsymbol{x}(t)=\boldsymbol{m}_x(t)\\ \boldsymbol{w}(t) = \boldsymbol{0}_\textit{w}}}
\end{equation}
$$

The propagation of the initial state and covariance estimates from $$t=t_{k-1}$$ to $$t=k$$, results in the $$\textit{a priori}$$ state estimate and covariance at time $$t_k$$, denoted
$$
\begin{equation}
    \boldsymbol{m}^{-}_{x,k} = \boldsymbol{m}_x(t_k)
\end{equation}
$$

$$
\begin{equation}
    \boldsymbol{P}^{-}_{xx,k} = \boldsymbol{P}_{xx}(t_k)
\end{equation}
$$

To update the state and covariance estimates, the cross covariance and innovation covariance must be computed, which also allow the construction of the Kalman gain, a linear gain that minimizes the mean square of the $$\textit{a posteriori}$$ state estimation error. The cross covariance, denoted $$\boldsymbol{P}^{-}_{xz,k}$$, and the innovation covariance, denoted $$\boldsymbol{P}^{-}_{zz,k}$$, are given by

$$
\begin{equation}
    \boldsymbol{P}^{-}_{xz,k} = 
    \boldsymbol{P}^{-}_{xx,k}\boldsymbol{H}^{T}_x(\boldsymbol{m}^{-}_{x,k})
\end{equation}
$$

$$
\begin{equation}
    \boldsymbol{P}^{-}_{zz,k} = \boldsymbol{H}_{x}(\boldsymbol{m}^{-}_{x,k})\boldsymbol{P}^{-}_{xx,k}\boldsymbol{H}^{T}_x(\boldsymbol{m}^{-}_{x,k}) + \boldsymbol{H}_{v}(\boldsymbol{m}^{-}_{x,k})\boldsymbol{P}_{vv,k}\boldsymbol{H}^{T}_{v}(\boldsymbol{m}^{-}_{x,k})
\end{equation}
$$

Here $$\boldsymbol{H}_x$$ is the measurement Jacobian relative to the state (Eq.~\ref{eq:jacobianMeas}), $$\boldsymbol{H}_v$$ is the measurement Jacobian relative to the noise and $$\boldsymbol{P}_{vv,k}$$ is the measurement noise covariance. For this study, $$\boldsymbol{P}_{vv,k} = \boldsymbol{\Sigma_v}$$.

The Kalman gain, $$\boldsymbol{K}_k$$, is then given by

$$
\begin{equation}
    \boldsymbol{K}_k = \boldsymbol{P}^{-}_{xz,k}(\boldsymbol{P}^{-}_{zz,k})^{-1}
\end{equation}
$$

Penultimately, using the previously defined measurement function, $$\boldsymbol{h}(\boldsymbol{x}_k,\boldsymbol{v}_k)$$, the expected measurement is computed based on the $$\textit{a priori}$$ state, $$\boldsymbol{m}^{-}_{x,k}$$ as 

$$
\begin{equation}
    \boldsymbol{m}^{-}_{z,k} = \boldsymbol{h}(\boldsymbol{m}^{-}_{x,k},\boldsymbol{0}_v)
\end{equation}
$$

where $$\boldsymbol{0}_v$$ is a $$\textit{v}-by-1$$ column vector with $$\textit{v}$$ being the dimension of the measurement noise. Finally, with the expected measurement, cross and innovation covariances, and the Kalman gain, the update step can be performed. The state and covariance estimates are updated as

$$
\begin{equation}
    \boldsymbol{m}^{+}_{x,k} = \boldsymbol{m}^{-}_{x,k} + \boldsymbol{K}_k[\boldsymbol{z}_k - \boldsymbol{m}^{-}_{z,k}]
\end{equation}
$$

$$
\begin{equation}
    \boldsymbol{P}^{+}_{xx,k} = \boldsymbol{P}^{-}_{xx,k} - \boldsymbol{P}^{-}_{xz,k}\boldsymbol{K}^{T}_k - \boldsymbol{K}_k(\boldsymbol{P}^{-}_{xz,k})^{T} + \boldsymbol{K}_k\boldsymbol{P}^{-}_{zz,k}\boldsymbol{K}^{T}_k
\end{equation}
$$

The updated mean and covariance estimates are then used as the initial conditions for the propagation phase of the next iteration, and this process continues until all the measurements are utilized. 

It is important to bear in mind that, as formulated above, the general EKF procedure is an \textit{unconstrained} estimator, meaning that the sum of two valid states results in another valid state. For the case of inertia estimation, this is problematic, as the inertia tensor must satisfy the physical constraints described in Sec.~\ref{section:Jconstraint}. With this, it is therefore important to consider constrained estimators, which account for the constraints on the states explicitly in the update formulation through the selection of the linear gain. 

<strong>The Constrained Update Extended Kalman Filter: </strong>

<u>Quadratic Programming Approach</u>

The constraints on the principle moments of inertia can be satisfied using a quadratic programming based approach, or using an analytical approach. Denoted the constrained update EKF (CUEKF), in both approaches, the constraints are applied on the state updates through the modification of the optimal gain. 

To proceed, recall it can be proven that the optimal gain is defined as a linear gain that minimizes the mean square of the \textit{a posteriori} state estimation error. This is equivalent to minimizing the trace of the posterior covariance matrix, which leads to the standard cost function, 

$$
\begin{equation}
    J_c = \text{tr}\{\boldsymbol{P}^{+}_{xx,k}\}
\end{equation}
$$

Thus, the problem boils down to finding a linear gain $$\boldsymbol{K}_{c_{k}}$$ that satisfies the following constrained optimization problem at a given timestep, $$k$$ \cite{QP_gupta2007},

$$
\begin{equation}
    \boldsymbol{K}_{c_{k}}
    =
    \argminA_{\boldsymbol{K}} ~ 
    \text{tr}\{[\boldsymbol{I} - \boldsymbol{K}\boldsymbol{H}_{x,k}]\boldsymbol{P}^{-}_{xx,k}[\boldsymbol{I} - \boldsymbol{K}\boldsymbol{H}_{x,k}]^{T} + \boldsymbol{K}\boldsymbol{H}_{v,k}\boldsymbol{P}_{vv,k}\boldsymbol{H}^{T}_{v,k} \boldsymbol{K}^{T}\}  
\end{equation}
$$

$$
\begin{equation}
    \text{s.t.} ~~ \boldsymbol{D}\boldsymbol{K}_{c_{k}}[\boldsymbol{z}_k - \boldsymbol{m}^{-}_{z,k}] \le \boldsymbol{C}_{k}
\end{equation}
$$

Here, $$\boldsymbol{D}$$ is a constant linear constraint matrix, where the number of rows represent the number of constraints and the number of columns correspond to the number of states. The constraint matrix, $$\boldsymbol{C}_k$$, contains the constraints that are applied to the state update, and is based on the prior state estimate. To satisfy both the positivity constraint and the triangle inequality constraint,

$$
\begin{equation}
    \boldsymbol{D} 
    = 
    \begin{bmatrix}
         0 & 0 & 0 & 1 & -1 & -1 \\
         0 & 0 & 0 & -1 & 1 & -1 \\
         0 & 0 & 0 & -1 & -1 & 1 \\
         0 & 0 & 0 & -1 &  0 & 0 \\
         0 & 0 & 0 &  0 & -1 & 0 \\
         0 & 0 & 0 &  0 &  0 & -1 \\
    \end{bmatrix}, ~~
    \boldsymbol{C}_k = 
    \begin{bmatrix}
        
        J^{-}_{2,k} + J^{-}_{3,k} - J^{-}_{1,k} \\
        J^{-}_{1,k} + J^{-}_{3,k} - J^{-}_{2,k} \\
        J^{-}_{1,k} + J^{-}_{2,k} - J^{-}_{3,k} \\
        J^{-}_{1,k} - \epsilon\\
        J^{-}_{2,k} - \epsilon \\
        J^{-}_{3,k} - \epsilon
    \end{bmatrix}
\end{equation}
$$

where the moment of inertia components of the $$\textit{a priori}$$ estimate are $$J^{-}_{1,k}$$, $$J^{-}_{2,k}$$ and $$J^{-}_{3,k}$$ and where $$\epsilon$$ is a small positive constant. With the above formulation, it is straightforward to use an out-of-the-box solver, such as <code>fmincon</code> in <span class="smallcaps">Matlab</span>, to execute the optimization problem when the Kalman gain will cause an update that violates the inequality constraints. The above constraint formulation will fully constrain the states and guarantee that every estimate of the state will correspond to a physically sound estimate. During implementation, the initial mean is sampled until all constraints are satisfied. 

<u>Analytical Approach</u>

Another version of the CUEKF is formulated as an analytical approach to applying constraints on the state updates, again through the modification of the optimal gain. The aid of my lab-mate James McElreath in this derivation is gratefully acknowledged. The constraint is accounted for by a modification of the cost function by superposing the optimal cost function with a constraint term,

$$
\begin{equation}
    J_c = \text{tr}\{\boldsymbol{P}^{+}_{xx,k}\} + \boldsymbol{\lambda}^{T}_{k}\boldsymbol{g}(\Delta\boldsymbol{x}_{k})
\end{equation}
$$

where $$\boldsymbol{\lambda}^{T}_k$$ is a column vector of Lagrange multipliers, $$\boldsymbol{g}$$ is the constraint function, and $$\Delta\boldsymbol{x}_{k}$$ is the state update at timestep $${k}$$. Using the Karush-Kuhn-Tucker conditions, the method of Lagrange multipliers is generalized to allow inequality constraints \cite{KKT_conditions}. The constraint function is subject to,

$$
\begin{equation}
    \boldsymbol{\lambda}^{T}_{k}\boldsymbol{g}(\Delta\boldsymbol{x}_{k}) \le 0
\end{equation}
$$

where, for $$i$$ spanning from 1 to the total number of constraints, 

<!-- $$
\begin{enumerate}
    \item If $$\lambda_{k_i} > 0$$ the constraint is active
    \item If $$\lambda_{k_i} < 0$$ the constraint is unfeasible and is removed by setting it to zero
\end{enumerate}
$$ -->

<ol>
<li> If $$\lambda_{k_i} > 0$$ the constraint is active </li>
<li> If $$\lambda_{k_i} < 0$$ the constraint is unfeasible and is removed by setting it to zero </li>
</ol>

Constraints are thus applied to the state update as,

$$
\begin{equation} \label{eq:constraintFun}
    \boldsymbol{g}_k = 2(\boldsymbol{D}\Delta\boldsymbol{x}_{k} - \boldsymbol{C}_k) \le 0
\end{equation}
$$

which, in terms of the modified gain, $$\boldsymbol{K}_{c_k}$$, and the difference in expected measurement and true measurement, $$\Delta\boldsymbol{z}_k = [\boldsymbol{z}_k - \boldsymbol{m}^{-}_{z,k}]$$ is,

$$
\begin{equation}
    \boldsymbol{g}_k = 2(\boldsymbol{D}\boldsymbol{K}_{c_k}\Delta\boldsymbol{z}_{k} - \boldsymbol{C}_k) \le 0
\end{equation}
$$

Here, $$\boldsymbol{D}$$ is a constant linear constraint matrix, where the number of rows represent the number of constraints and the number of columns correspond to the number of states. The constraint matrix, $$\boldsymbol{C}_k$$, contains the linear constraints that are applied to the state update. Using this formulation, the modified gain, $$\boldsymbol{K}_{c_k}$$, and the Lagrange multipliers can be derived, such that the following cost function is minimized,

$$
\begin{equation}
    J_c = \text{tr}\{\boldsymbol{P}^{+}_{xx,k}\} + 2\boldsymbol{\lambda}^{T}_k(\boldsymbol{D}\boldsymbol{K}_{c_k}\Delta\boldsymbol{z}_{k} - \boldsymbol{C}_k)
\end{equation}
$$

First, the partial of $$J_c$$ with respect to $$\boldsymbol{\lambda}_k$$ is taken and is set to zero in order to minimize the cost function. While this doesn't technically guarantee that a maximum will not be found, the formulation of the cost function ensures that the local extrema found will indeed be a minimum.

$$
\begin{equation}
    \frac{\partial J_c}{\partial\boldsymbol{\lambda}_k} = 0 = \boldsymbol{D}\boldsymbol{K}_{c_k}\Delta\boldsymbol{z}_{k} - \boldsymbol{C}_k
\end{equation}
$$

thus, 
$$
\begin{equation} \label{eq:constraintEq}
    \boldsymbol{D}\boldsymbol{K}_{c_k}\Delta\boldsymbol{z}_{k} = \boldsymbol{C}_k
\end{equation}
$$

Next, to obtain an expression for the modified gain, the partial of $$J_c$$ is taken with respect to $$\boldsymbol{K}_{c_k}$$ and is again set to zero in order to minimize the cost function.

$$
\begin{equation}
    \frac{\partial J_c}{\partial\boldsymbol{K}_{c_k}} 
    = 0
    = \frac{\partial \text{tr}\{\boldsymbol{P}^{+}_{xx,k}\}}{\partial\boldsymbol{K}_{c_k}}
    +
    2\frac{\partial (\boldsymbol{\lambda}^{T}_k(\boldsymbol{D}\boldsymbol{K}_{c_k}\Delta\boldsymbol{z}_{k} - \boldsymbol{C}_k))}{\partial \boldsymbol{K}_{c_k}}
\end{equation}
$$

Expanding each numerator, this becomes,

$$
\begin{equation}
    0 =
    \frac{\partial (\text{tr}\{[\boldsymbol{I} - \boldsymbol{K}_{c_k}\boldsymbol{H}_{x,k}]\boldsymbol{P}^{-}_{xx,k}[\boldsymbol{I} - \boldsymbol{K}_{c_k}\boldsymbol{H}_{x,k}]^{T} + \boldsymbol{K}_{c_k}[\boldsymbol{H}_{v,k}\boldsymbol{P}_{vv,k}\boldsymbol{H}^{T}_{v,k}] \boldsymbol{K}^{T}_{c_k}\})}{\partial\boldsymbol{K}_{c_k}} 
    + 
    2\frac{\partial (\boldsymbol{\lambda}^{T}_k(\boldsymbol{D}\boldsymbol{K}_{c_k}\Delta\boldsymbol{z}_{k} - \boldsymbol{C}_k))}{\partial \boldsymbol{K}_{c_k}}
\end{equation}
$$

Evaluating the partials, and grouping like terms, the modified gain in terms of the Lagrange multipliers is given by,

$$
\begin{equation} \label{eq:modifiedGain}
    \boldsymbol{K}_{c_k} = [\boldsymbol{P}^{-}_{xz,k} - \boldsymbol{D}^{T}\boldsymbol{\lambda}_k\Delta\boldsymbol{z}^{T}_k][\boldsymbol{P}^{-}_{zz,k}]^{-1}    
\end{equation}
$$

Plugging Eq.~\ref{eq:modifiedGain} into  Eq.~\ref{eq:constraintEq}, $\boldsymbol{\lambda}_k$ can be isolated, yielding,

$$
\begin{equation} \label{eq:Lambda}
    \boldsymbol{\lambda}_k = \frac{1}{\Delta\boldsymbol{z}^{T}_k[\boldsymbol{P}^{-}_{zz,k}]^{-1}\Delta\boldsymbol{z}_k}[\boldsymbol{D}\boldsymbol{D}^T]^{-1}[\boldsymbol{D}\boldsymbol{P}^{-}_{xz,k}[\boldsymbol{P}^{-}_{zz,k}]^{-1}\Delta\boldsymbol{z}_k - \boldsymbol{C}_k]
\end{equation}
$$

Finally, plugging Eq.~\ref{eq:Lambda} into Eq.~\ref{eq:modifiedGain} results in the modified gain. The state is updated exactly like the EKF, and the covariance is updated via the Joseph form (a form valid for any linear gain), both using the modified gain,

$$
\begin{equation}
    \boldsymbol{m}^{+}_{x,k} = \boldsymbol{m}^{-}_{x,k} + \boldsymbol{K}_{c_k}[\boldsymbol{z}_k - \boldsymbol{m}^{-}_{z,k}]
    =
    \boldsymbol{m}^{-}_{x,k} + \boldsymbol{K}_{c_k}\Delta\boldsymbol{z}_k
\end{equation}
$$

$$
\begin{equation}
    \boldsymbol{P}^{+}_{xx,k} =
    [\boldsymbol{I} - \boldsymbol{K}_{c_k}\boldsymbol{H}_{x,k}]\boldsymbol{P}^{-}_{xx,k}[\boldsymbol{I} - \boldsymbol{K}_{c_k}\boldsymbol{H}_{x,k}]^{T} + \boldsymbol{K}_{c_k}[\boldsymbol{H}_{v,k}\boldsymbol{P}_{vv,k}\boldsymbol{H}^{T}_{v,k}] \boldsymbol{K}^{T}_{c_k}
\end{equation}
$$

For the example considered, the constraints must be applied such that the components of $$\boldsymbol{m}^{+}_{x,k}$$ do not violate the positivity constraints and the triangle inequality constraints given by Eq.~\ref{eq:constraint1}. Taking the moment of inertia components of the previous estimate to be $$J^{-}_{1,k}$$, $$J^{-}_{2,k}$$ and $$J^{-}_{3,k}$$, and the updates on each component to be $$\Delta J^{-}_{1,k}$$, $$\Delta J^{-}_{2,k}$$, and $$\Delta J^{-}_{3,k}$$, the triangle inequality constraints on each component are formulated as,

$$
\begin{equation}\label{eq:updateConstraintsIntermed}
        [J^{-}_{1,k} + \Delta J^{-}_{1,k}] \le [J^{-}_{2,k} + \Delta J^{-}_{2,k}] + [J^{-}_{3,k} + \Delta J^{-}_{3,k}]
\end{equation}
$$

$$
\begin{equation}
    [J^{-}_{2,k} + \Delta J^{-}_{2,k}] \le [J^{-}_{1,k} + \Delta J^{-}_{1,k}] + [J^{-}_{3,k} + \Delta J^{-}_{3,k}]
\end{equation}
$$

$$
\begin{equation}
    [J^{-}_{3,k} + \Delta J^{-}_{3,k}] \le [J^{-}_{1,k} + \Delta J^{-}_{1,k}] + [J^{-}_{2,k} + \Delta J^{-}_{2,k}]
\end{equation}
$$

Grouping the update terms and the terms depending on the previous state estimate, Eqns.~\ref{eq:updateConstraintsIntermed} can be formulated as Eq.~\ref{eq:constraintFun}. 

$$
\begin{equation}
    [\Delta J^{-}_{1,k} - \Delta J^{-}_{2,k} - \Delta J^{-}_{3,k}] \le [J^{-}_{2,k} + J^{-}_{3,k} - J^{-}_{1,k}]
\end{equation}
$$

$$
\begin{equation}
    [\Delta J^{-}_{2,k} - \Delta J^{-}_{1,k} - \Delta J^{-}_{3,k}] \le [J^{-}_{1,k} + J^{-}_{3,k} - J^{-}_{2,k}]
\end{equation}
$$

$$
\begin{equation}
    [\Delta J^{-}_{3,k} - \Delta J^{-}_{2,k} - \Delta J^{-}_{1,k}] \le [J^{-}_{1,k} + J^{-}_{2,k} - J^{-}_{3,k}]
\end{equation}
$$

Converting into matrix form, recalling that $$\Delta\boldsymbol{x}_{k}$$ is a column vector with 6 rows, the constraint function for this case will be discretized for each constraint case. Due to the nature of the problem, multiple triangle inequality constraints cannot be violated simultaneously, and thus it is necessary to check each constraint individually.

Similarly, the positivity constraints are formed by ensuring that the update terms plus the previous state estimates are greater than or equal to a small positive constant, $$\epsilon$$, resulting in,

$$
\begin{equation}
    -\Delta J^{-}_{1,k} \le J^{-}_{1,k} - \epsilon
\end{equation}
$$

$$
\begin{equation}
    -\Delta J^{-}_{2,k} \le J^{-}_{2,k} - \epsilon
\end{equation}
$$

$$
\begin{equation}
    -\Delta J^{-}_{3,k} \le J^{-}_{3,k} - \epsilon
\end{equation}
$$

Ensuring that only the active constraints are applied, the formulation becomes,
$$
\begin{equation}
    \boldsymbol{g}_{k,i} = 2(\boldsymbol{D}_i\Delta\boldsymbol{x}_{k} - \boldsymbol{C}_{k,i}) \le 0
\end{equation}
$$

$$
\begin{equation}
    \boldsymbol{g}_{k,i} = 2(\boldsymbol{D}_i\boldsymbol{K}_{c_k}\Delta\boldsymbol{z}_{k} - \boldsymbol{C}_{k,i}) \le 0
\end{equation}
$$

$$
\begin{equation*}
    \forall ~~ i \in \{1,2,3,4,5,6\}
\end{equation*}
$$

with 

$$
\begin{equation}
    \boldsymbol{D}_1 = 
    [
         0 ~ 0 ~ 0 ~ 1 ~ \text{-1} ~ \text{-1}
    ], ~~
    \boldsymbol{C}_{k,1} = 
    [
        J^{-}_{2,k} + J^{-}_{3,k} - J^{-}_{1,k}
    ]
\end{equation}    
$$
$$
\begin{equation}
    \boldsymbol{D}_2 = 
    [
         0 ~ 0 ~ 0 ~ \text{-1} ~ 1 ~ \text{-1}
    ], ~~
    \boldsymbol{C}_{k,2} = 
    [
        J^{-}_{1,k} + J^{-}_{3,k} - J^{-}_{2,k}
    ]
\end{equation}  
$$
$$  
\begin{equation}
    \boldsymbol{D}_3 = 
    [
         0 ~ 0 ~ 0 ~ \text{-1} ~ \text{-1} ~ 1
    ], ~~
    \boldsymbol{C}_{k,3} = 
    [
        J^{-}_{1,k} + J^{-}_{2,k} - J^{-}_{3,k}
    ]
\end{equation}  
$$
$$
\begin{equation}
    \boldsymbol{D}_4 = 
    [
         0 ~ 0 ~ 0 ~ \text{-1} ~ 0 ~ 0
    ], ~~
    \boldsymbol{C}_{k,4} = 
    [
        J^{-}_{1,k} - \epsilon
    ]
\end{equation}  
$$
$$
\begin{equation}
    \boldsymbol{D}_5 = 
    [
         0 ~ 0 ~ 0 ~ 0 ~ \text{-1} ~ 0
    ], ~~
    \boldsymbol{C}_{k,5} = 
    [
        J^{-}_{2,k} - \epsilon
    ]
\end{equation}  
$$
$$
\begin{equation}
    \boldsymbol{D}_6 = 
    [
         0 ~ 0 ~ 0 ~ 0 ~ 0 ~ \text{-1} 
    ], ~~
    \boldsymbol{C}_{k,6} = 
    [
        J^{-}_{3,k} - \epsilon
    ]
\end{equation}  
$$

Once the active constraints are determined, a concatenated $$\boldsymbol{D}$$ and $$\boldsymbol{C}_k$$ matrix are constructed and are applied. In this way, the analytical solution for the optimization problem is obtained and can be easily and quickly computed on hardware. The above constraint formulation will constrain the states using the triangle inequality and positivity constraints if the update from the Kalman gain would result in a state estimate that violates physical constraints.

<strong>Nonlinear Least Squares Estimation: </strong> \label{sec:NLS}

While Kalman filter variations are extremely useful for estimating the current state of a system, a nonlinear least squares (NLS) approach is a widely used method to estimate the initial state of a nonlinear system. The technique of nonlinear least squares uses some assumptions similar to that of an EKF. However, the two techniques differ in that while the EKF processes data sequentially, the NLS approach processes data in a batch process. In this section, the necessary equations and general NLS technique will be outlined, but the reader is encouraged to refer to \cite{optimalEstimation_Junkins_Crass} and \cite{DeMarsNotes} for more detail.

Again, consider the general case of a nonlinear system model governed by 

$$
\begin{equation}
    \boldsymbol{\dot{x}}(t) = \boldsymbol{f}(\boldsymbol{x}(t))
\end{equation}
$$

$$
\begin{equation} \label{eq:measdynNLS}
    \boldsymbol{z}_i = \boldsymbol{h}(\boldsymbol{x}_i,\boldsymbol{v}_i)
\end{equation}
$$

Beginning with an initial estimate for the initial state, $$\boldsymbol{\hat{x}}_c$$, a set of states corresponding to each measurement time can be generated by integrating the estimate forward in time. Denoting the reference state row vector at measurement number $$k$$ as $$\boldsymbol{\hat{x}}_k$$, the expected measurement row vector, $$\boldsymbol{\hat{z}}_k$$ can be computed using \ref{eq:measdynNLS}. Using the measurement at $$k$$, $$\Tilde{\boldsymbol{z}}_k$$, the residual error can be computed as 

$$
\begin{equation}
    \boldsymbol{e}_k = \Tilde{\boldsymbol{z}}_k - \hat{\boldsymbol{z}}_k
\end{equation}
$$

This process can be done for all measurements, where the number of measurements equal $m$, and the results are stacked as,
$$
\begin{equation}
    \boldsymbol{\hat{z}} =
    \begin{bmatrix}
        \boldsymbol{\hat{z}}_1 ~~ \boldsymbol{\hat{z}}_2 ~~ \cdots ~~ \boldsymbol{\hat{z}}_m
    \end{bmatrix}^{T}
    = \text{expected measurements}
\end{equation}
$$

$$  
\begin{equation}
    \boldsymbol{\hat{x}} =
    \begin{bmatrix}
        \boldsymbol{\hat{x}}_1 ~~ \boldsymbol{\hat{x}}_2 ~~ \cdots ~~ \boldsymbol{\hat{x}}_m
    \end{bmatrix}^{T}
    = \text{estimated states}
\end{equation}
$$

$$
\begin{equation}
    \boldsymbol{{e}} =
    \begin{bmatrix}
        \boldsymbol{e}_1 ~~ \boldsymbol{e}_2 ~~ \cdots ~~ \boldsymbol{e}_m
    \end{bmatrix}^{T}
    = \text{residual errors}
\end{equation}
$$

The measurement Jacobian at each step can be obtained by a first-order Taylor series expansion about the current estimated state, denoted $$\boldsymbol{H}_{\hat{x}_{k}}$$ (Eq.~\ref{eq:jacobianMeas}). Consider a Jacobian matrix, $$\boldsymbol{H}$$ which is a concatenation of the measurement Jacobians at each step, multiplied by the state transition matrix, $$\boldsymbol{\Phi}(t_k,t_c)$$. 

$$
\begin{equation}
    \boldsymbol{H} = 
    \begin{bmatrix}
        \boldsymbol{H}_{\hat{x}_{1}}\boldsymbol{\Phi}(t_1,t_c) ~~
        \boldsymbol{H}_{\hat{x}_{2}}\boldsymbol{\Phi}(t_2,t_c) ~~
        \cdots
    \end{bmatrix}^{T}
\end{equation}
$$

It is worth noting that the state transition matrix is generally numerically integrated along the reference trajectory simultaneously with the reference states as,

$$
\begin{equation}
    \boldsymbol{\dot{\hat{x}}}(t) = \boldsymbol{f}(\boldsymbol{\hat{x}}(t))
\end{equation}
$$

$$
\begin{equation}
    \boldsymbol{\dot{\Phi}}(t,t_c) = \boldsymbol{F}_{\hat{x}}(t)\boldsymbol{{\Phi}}(t,t_c) 
\end{equation}
$$

$$
\begin{equation}
        \text{Subject to initial conditions: ~} \boldsymbol{\hat{x}}(t_c) = \boldsymbol{\hat{x}}_c, ~~ \boldsymbol{{\Phi}}(t_c,t_c) = \boldsymbol{I}_{6\times6}
\end{equation}
$$

where $$\boldsymbol{F}_{\hat{x}}(t)$$ is the dynamics Jacobian (Eq.~\ref{eq:jacobianDyn}). An estimate that minimizes the following cost function $$J_c$$ is sought,

$$
\begin{equation}
    J_c = \frac{1}{2}\boldsymbol{e}^{T}\boldsymbol{W}\boldsymbol{e}
\end{equation}
$$

where $$\boldsymbol{W}$$ is a ($$n\times m)\times(n\times m$$) weight matrix used to scale the importance of individual measurements, where $$n$$ is the dimension of a measurement. Here, a judicious choice for the weight matrix is based on the measurement noise covariance matrices at each step,

$$
\begin{equation}
    \boldsymbol{W} = 
    \begin{bmatrix}
        \boldsymbol{P}_{vv,1} & \boldsymbol{0} & \cdots \\
        \boldsymbol{0} & \boldsymbol{P}_{vv,2} & \cdots \\
        \vdots & \vdots & \ddots
    \end{bmatrix}^{-1}
\end{equation}
$$

With the concatenated matrices  formulated, the estimate for the initial state, $$\boldsymbol{\hat{x}}_c$$, is computed as 

$$
\begin{equation}
    \boldsymbol{\hat{x}}_c = \boldsymbol{\hat{x}}_c + \Delta\boldsymbol{x}
\end{equation}
$$

$$
\begin{equation}
    \Delta\boldsymbol{x} = (\boldsymbol{H}^{T}\boldsymbol{W}\boldsymbol{H})^{-1}\boldsymbol{H}^{T}\boldsymbol{W}\boldsymbol{e}
\end{equation}
$$

Using the updated estimate for the initial state, the concatenated matrices are recomputed, and a new update is calculated. This process continues until the difference in the cost, $$J_c$$, between subsequent iterations is less than a threshold value, or the maximum number of iterations (a user specified value) is reached. 
