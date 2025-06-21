---
title: "Impact of Memory Size on Quasi-Newton Methods"
date: 2025-06-20
draft: false
description: "A detailed exploration of how memory size affects the convergence and runtime performance of L-BFGS versus full-memory BFGS using a comprehensive MATLAB optimization package."
tags: ["Optimization", "Numerical Methods", "Quasi-Newton", "BFGS", "L-BFGS", "MATLAB"]
categories: ["Mathematical Modeling"]
featuredImage: "assets/img/quasi_newton.jpg"
math: true
---

{{< katex >}}

## Introduction

In large-scale optimization problems, especially those arising in machine learning and engineering design, traditional second-order methods like Newton’s method can be prohibitively expensive due to the need to compute and store the full Hessian matrix. Quasi-Newton methods provide a powerful alternative by approximating the Hessian matrix iteratively using only first-order information, significantly reducing computational cost while maintaining fast convergence.

Among these methods, the **Broyden-Fletcher-Goldfarb-Shanno (BFGS)** algorithm is widely recognized for its robustness and accuracy. However, its full-memory variant requires \( O(n^2) \) memory, making it unsuitable for high-dimensional problems. To address this limitation, the **Limited-memory BFGS (L-BFGS)** algorithm was developed. Instead of storing and updating the full Hessian approximation, L-BFGS maintains only the latest \( m \) pairs of update vectors. This parameter \( m \), known as the memory size, is a crucial hyperparameter—but its optimal choice remains problem-dependent and poorly understood in practice.

This project, completed as part of an advanced optimization course (IOE 511), focuses on understanding **how the memory size \( m \) affects the performance of L-BFGS** in terms of convergence speed, accuracy, and computational efficiency. We developed a comprehensive MATLAB-based software package that implemented ten optimization algorithms from scratch—including gradient-based methods, Newton-type methods, and a wide spectrum of quasi-Newton methods such as BFGS, DFP, SR1, and L-BFGS.
## Implemented Algorithms

To systematically compare optimization strategies, we implemented 10 unconstrained optimization algorithms in MATLAB. These include gradient-based, Newton-type, and quasi-Newton methods, each with distinct update rules and line search strategies. Below is a detailed explanation of each algorithm, including its mathematical foundation, update formula, and practical implications.

---

### 1. Gradient Descent with Backtracking Line Search (GradientDescent)

This is a **first-order optimization method** that updates the current iterate in the negative direction of the gradient:

$$
x_{k+1} = x_k - \alpha_k \nabla f(x_k)
$$

The step size \( \alpha_k \) is determined by **Armijo backtracking**, which reduces \( \alpha \) geometrically (e.g., \( \alpha \leftarrow \beta \alpha \)) until:

$$
f(x_k - \alpha \nabla f(x_k)) \le f(x_k) - c_1 \alpha \|\nabla f(x_k)\|^2
$$

Where \( 0 < c_1 < 1 \), typically \( c_1 = 10^{-4} \), and \( 0 < \beta < 1 \).

- **Pro**: Simple, widely applicable
- **Con**: Converges slowly on ill-conditioned or curved surfaces
- **Use Case**: Small-scale or convex problems with well-scaled gradients

---

### 2. Gradient Descent with Wolfe Line Search (GradientDescentW)

Similar to the backtracking version, but satisfies the **Wolfe conditions**, which add a curvature criterion:

- **Armijo (sufficient decrease)**:
  $$
  f(x_k + \alpha d_k) \le f(x_k) + c_1 \alpha \nabla f(x_k)^T d_k
  $$

- **Curvature condition**:
  $$
  \nabla f(x_k + \alpha d_k)^T d_k \ge c_2 \nabla f(x_k)^T d_k
  $$

Where \( d_k = -\nabla f(x_k) \), \( c_1 < c_2 < 1 \).

- **Pro**: More stable steps, prevents overly short steps
- **Con**: Costlier than backtracking
- **Use Case**: More robust convergence in non-convex surfaces

---

### 3. Modified Newton Method with Backtracking Line Search (Newton)

Newton’s method leverages **second-order information** by using the Hessian matrix \( H_k = \nabla^2 f(x_k) \):

$$
x_{k+1} = x_k - \alpha_k H_k^{-1} \nabla f(x_k)
$$

We use **backtracking line search** to prevent overshooting. If the Hessian is not positive definite, we add a damping term to ensure descent:

$$
H_k \leftarrow H_k + \delta I \quad (\delta > 0)
$$

- **Pro**: Quadratic convergence near local minima
- **Con**: Expensive due to Hessian and inversion
- **Use Case**: Medium-scale problems where high precision is needed

---

### 4. Modified Newton with Wolfe Line Search (NewtonW)

Same update as Newton, but step size \( \alpha \) satisfies **Wolfe conditions**, improving robustness:

- Guarantees adequate descent and avoids overly aggressive steps
- Especially useful when Hessian curvature varies significantly

---

### 5. Trust Region Newton with Conjugate Gradient Solver (TRNewtonCG)

Instead of line search, **Trust Region methods** define a neighborhood (trust region) and solve:

$$
\min_s \quad m_k(s) = f(x_k) + \nabla f(x_k)^T s + \frac{1}{2} s^T H_k s \quad \text{s.t.} \quad \|s\| \le \Delta_k
$$

This quadratic subproblem is solved using **Conjugate Gradient (CG)**. If the solution improves the objective enough, we expand the region \( \Delta_k \), else shrink it.

- **Pro**: Stable even when Hessian is indefinite
- **Con**: Requires CG iterations and careful radius control
- **Use Case**: Ill-conditioned or non-convex problems

---

### 6. SR1 Quasi-Newton with CG Subproblem (TRSR1CG)

SR1 (Symmetric Rank-One) updates an approximate Hessian matrix \( B_k \) via:

$$
B_{k+1} = B_k + \frac{(y_k - B_k s_k)(y_k - B_k s_k)^T}{(y_k - B_k s_k)^T s_k}
$$

where \( s_k = x_{k+1} - x_k \), \( y_k = \nabla f(x_{k+1}) - \nabla f(x_k) \).

- Unlike BFGS, SR1 does **not guarantee positive definiteness**, making it better for capturing negative curvature.
- Combined with a **trust region CG solver**, SR1 is flexible for **non-convex optimization**.

---

### 7. BFGS with Backtracking Line Search (BFGS)

BFGS updates the inverse Hessian approximation \( H_k \) directly using:

$$
H_{k+1} = (I - \rho_k s_k y_k^T) H_k (I - \rho_k y_k s_k^T) + \rho_k s_k s_k^T
$$

with \( \rho_k = \frac{1}{y_k^T s_k} \). This update ensures \( H_{k+1} \succ 0 \) if \( y_k^T s_k > 0 \).

Step:

$$
x_{k+1} = x_k - \alpha_k H_k \nabla f(x_k)
$$

Backtracking ensures descent at each step.

- **Pro**: Excellent balance of performance and stability
- **Con**: Needs \( O(n^2) \) memory
- **Use Case**: Medium-scale problems where memory is not a constraint

---

### 8. BFGS with Wolfe Line Search (BFGSW)

Same Hessian update as above, but step size chosen via Wolfe conditions, improving convergence and numerical stability in difficult terrains.

- **Use Case**: High-precision needs, or surfaces with oscillations

---

### 9. DFP with Backtracking Line Search (DFP)

DFP updates the **inverse Hessian** as:

$$
H_{k+1} = H_k + \frac{s_k s_k^T}{s_k^T y_k} - \frac{H_k y_k y_k^T H_k}{y_k^T H_k y_k}
$$

Although historically important, it is less robust than BFGS.

- **Pro**: Simpler math
- **Con**: Sensitive to numerical errors
- **Use Case**: Educational or low-dimensional applications

---

### 10. DFP with Wolfe Line Search (DFPW)

DFP with Wolfe step size enhances numerical robustness:

- Improves step size control on curved or noisy objectives
- Still inferior to BFGS in general-purpose use

---

Each method offers a different trade-off between **convergence speed**, **memory cost**, and **stability**. By implementing and benchmarking them systematically, we gained insight into how algorithm structure and line search interact with L-BFGS memory configuration.

### Research Focus

Our core research question was:

> _How does the memory size in L-BFGS influence its performance relative to full-memory BFGS, especially in terms of iteration count, CPU runtime, and overall convergence behavior?_

To answer this, we designed a robust benchmark framework that tested all algorithms on 12 canonical unconstrained optimization problems—including high-dimensional quadratic functions, the Rosenbrock function, and the Genhump function. For L-BFGS, we varied memory size \( m \in \{3, 7, 11, 15, 20, 50\} \) and systematically recorded metrics such as:

- Total number of iterations until convergence  
- CPU execution time  
- Number of function and gradient evaluations  
- Final objective value achieved  

To make sense of the trade-offs between computation time and memory usage, we proposed a new metric called \( P_{\text{inc}} \): the average increase in time efficiency per unit increase in memory. This metric allowed us to pinpoint the most cost-effective memory setting for different problem classes.

### Key Findings

- **BFGS** consistently required the fewest iterations, due to its full and accurate Hessian approximation, but its per-iteration cost was much higher.
- **L-BFGS with small \( m \) (e.g., 3 or 7)** often ran faster in total wall time and scaled better with problem size.
- Optimal memory size is **problem-specific**, but \( m = 11 \) generally offered a strong balance between accuracy and runtime.
- Our new metric \( P_{\text{inc}} \) revealed that beyond a certain point, increasing memory brings diminishing returns in efficiency.

## Technologies Used

{{< badge >}}MATLAB{{< /badge >}}
{{< badge >}}Quasi-Newton{{< /badge >}}
{{< badge >}}BFGS{{< /badge >}}
{{< badge >}}L-BFGS{{< /badge >}}
{{< badge >}}Trust Region Methods{{< /badge >}}

---

## Performance Profiling & Analysis

We benchmarked performance across:

- **12 canonical test functions**
- **10 optimization algorithms**
- **6 L-BFGS memory sizes (m = 3 to 50)**

The key comparison metrics included iteration count, runtime, and convergence rate. We visualized performance using log-scale convergence plots and performance profiles.

We found that while BFGS achieves the fewest iterations, its total runtime is often worse than L-BFGS due to per-iteration overhead. L-BFGS with a medium memory size (e.g., \( m = 11 \)) often provides the best trade-off.

---

## Metric: Efficiency per Unit Memory

To guide practical use, we designed a new evaluation metric:

$$
P_{\text{inc}} = \frac{T_{\text{BFGS}} - T_m}{m \cdot T_{\text{BFGS}}} \times 100\%
$$

Where:
- \( T_m \): runtime of L-BFGS with memory size \( m \)
- \( T_{\text{BFGS}} \): runtime of full-memory BFGS

This helps identify the "sweet spot" for memory size in terms of time efficiency per unit memory consumed.

---

## Conclusion

This project demonstrated that L-BFGS, when properly tuned, can rival or even outperform BFGS on large-scale problems in both speed and convergence quality. It also provides a practical framework for choosing the memory size in L-BFGS, backed by empirical benchmarking and theoretical intuition. Our MATLAB software package is reusable for future algorithm development and educational use.

**GitHub Repository**: [Impact of Memory Size on Quasi-Newton Methods](https://github.com/Jinxiang2000/IOE-511) 

{{< button href="/projects" target="_self" >}}Back to Projects{{< /button >}}