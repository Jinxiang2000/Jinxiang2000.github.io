---
title: "Scheduling 2 - Single Machine Model"
date: 2024-08-27
draft: false
description: "Deterministic Model: Preliminaries"
summary: "Single-machine scheduling models are fundamental building blocks of production planning, offering powerful insights for complex systems."
tags: ["Scheduling","Mathematical Modeling"]
categories: ["learning", "operations research"]
math: true
---

# A Practical Guide to Job Scheduling

Single-machine scheduling models are the fundamental building blocks of production planning. While seemingly simple, they offer powerful, foundational insights that can be extended to understand more complex, real-world systems like multi-stage production lines and entire job shops.

<!--more-->

Imagine a custom print shop managing multiple orders. An order for 10,000 high-margin brochures for a major corporate client is far more critical than a small, low-margin order for 200 business cards. By assigning a higher weight ($w_j$) to the corporate order, the $ \sum w_j C_j $ objective ensures it receives priority, reflecting its greater contribution to revenue and customer satisfaction. The central challenge is to find a sequence that optimally balances these priorities across all jobs. Fortunately, for the most common version of this problem, a simple and elegant rule provides the optimal solution.

## The Foundational Principle: The Weighted Shortest Processing Time (WSPT) Rule

The most fundamental single-machine scheduling problem, denoted `1 || sum(wjCj)`, involves a set of independent jobs that are all available at time zero, waiting to be processed on a single machine. For this ubiquitous problem, a simple, intuitive, and mathematically optimal rule exists.

**Theorem 3.1.1.** The WSPT rule is optimal for $1 \ || \ \sum w_j C_j$.

### The "So What?" of the WSPT Rule

The **Weighted Shortest Processing Time (WSPT)** rule is a powerful prioritization logic. It directs the scheduler to calculate an efficiency ratio for each job - its weight divided by its processing time ($w_j / p_j$) - and then sequence the jobs in decreasing order of this ratio.

The business intuition behind this is profound: it's a "bang for the buck" metric. The rule gives the highest priority to jobs that deliver the most value (weight) for the least amount of machine time (processing time). This contrasts with simpler, less effective rules like basic Shortest Processing Time (SPT), which ignores job value, or sequencing purely by weight, which ignores processing time. The WSPT ratio is the critical insight because it optimally balances both value and time. By front-loading these highly efficient jobs, the schedule minimizes the accumulation of weighted completion time across the entire job set.

The optimality of this rule is formally proven using a simple but powerful technique called an "adjacent pairwise interchange" argument. The proof works by showing that in any non-WSPT schedule, there must be a pair of adjacent jobs where the less efficient job comes first. Swapping them is proven to reduce the total weighted completion time, demonstrating that any deviation from the WSPT order can be improved, making WSPT the only optimal sequence.

### How to Apply the WSPT Rule

Let's apply this rule to a practical example using data from a scheduling problem with four jobs.

- **Job Data:**
  - **Jobs:** 1, 2, 3, 4
  - **Weights ($w_j$):** 6, 11, 9, 5
  - **Processing Times ($p_j$):** 3, 5, 7, 4

**Step 1: Calculate the WSPT Ratio**

First, we compute the efficiency ratio ($w_j/p_j$) for each job.

| Job ID | Weight ($w_j$) | Processing Time ($p_j$) | WSPT Ratio ($w_j/p_j$) |
| ------ | -------------- | ----------------------- | ---------------------- |
| 1      | 6      | 3               | 2.00       |
| 2      | 11     | 5               | 2.20       |
| 3      | 9      | 7               | 1.29       |
| 4      | 5      | 4               | 1.25       |



**Step 2: Determine the Optimal Sequence**

Next, we sequence the jobs in descending order of their calculated WSPT ratio (2.20 > 2.00 > 1.29 > 1.25).

The optimal sequence is: **Job 2 → Job 1 → Job 3 → Job 4**.

**Step 3: Calculate Completion Times**

With the optimal sequence determined, we can calculate the completion time for each job. A job's completion time is the sum of its own processing time and the completion times of all preceding jobs.

| Job in Sequence (ID) | Processing Time ($p_j$) | Completion Time ($C_j$) |
| -------------------- | ----------------------- | ----------------------- |
| 2                    | 5                       | 5                       |
| 1                    | 3                       | 5 + 3 = 8               |
| 3                    | 7                       | 8 + 7 = 15              |
| 4                    | 4                       | 15 + 4 = 19             |

**Step 4: Compute the Final Objective Value**

Finally, we calculate the total weighted completion time by summing the product of each job's weight and its completion time, using the original weights for each job in the sequence.

$ \sum w_j C_j = (w_2 \times C_{Job 2}) + (w_1 \times C_{Job 1}) + (w_3 \times C_{Job 3}) + (w_4 \times C_{Job 4}) $ $ \sum w_j C_j = (11 \times 5) + (6 \times 8) + (9 \times 15) + (5 \times 19) $ $ \sum w_j C_j = 55 + 48 + 135 + 95 = \textbf{333} $

This value represents the minimum possible total weighted completion time for this set of jobs. While this basic case is powerful, real-world operations often involve dependencies between jobs, which adds another layer of complexity.

## Handling Complexity: Scheduling with Precedence Chains

The next level of scheduling complexity arises when jobs are subject to precedence constraints, meaning one job must be completed before another can begin. While solving for the optimal schedule under general precedence constraints (`1 | prec | sum(wjCj)`) is computationally very difficult, a common and solvable case occurs when these constraints form parallel chains of operations.

### Generalizing the WSPT Logic to Chains

The core logic of the WSPT rule can be extended from individual jobs to entire chains. When faced with a choice between two independent chains of jobs, we can treat each chain as a single "macro-job" and apply the WSPT logic at the chain level. A similar efficiency ratio can guide the decision, as confirmed by the following lemma.

**Lemma 3.1.2.** If $ \frac{\sum_{j=1}^{k} w_j}{\sum_{j=1}^{k} p_j} > (<) \frac{\sum_{j=k+1}^{n} w_j}{\sum_{j=k+1}^{n} p_j} $, then it is optimal to process the chain of jobs 1, . . . , k before (after) the chain of jobs k + 1, . . . , n.

This lemma provides a clear rule for prioritizing complete chains, stating that we should process the one with the higher aggregate ratio of total weight to total processing time first.

### A More Powerful Concept for Interleaving Chains

An optimal schedule might not process an entire chain at once; it may be better to process part of one chain, switch to another, and then return. This raises the question: if we can't schedule a whole chain, what's the most profitable *part* of a chain to schedule right now? The answer lies in a more nuanced metric called the **rho-factor**.

The ratio on the left-hand side is called the rho-factor of chain 1, . . . , k and is denoted by rho(1, . . . , k). Job $l^*$ is referred to as the job that determines the rho-factor of the chain.

The rho-factor is found by calculating the WSPT ratio for every possible prefix of a chain (the first job, the first two jobs, the first three, etc.) and identifying the maximum value. The job that ends the prefix with the highest ratio is the one that "determines" the rho-factor. The following lemma confirms that this prefix is the most efficient "bang for the buck" block of work available in that chain and should be scheduled without interruption.

