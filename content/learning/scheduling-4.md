---
title: "Scheduling 4 - Single Machine Model"
date: 2025-08-27
draft: false
description: "The Maximum Lateness"
summary: ""
tags: ["Scheduling","Mathematical Modeling"]
categories: ["learning", "operations research"]
math: true


---

## Minimizing the Number of Tardy Jobs : The Importance of On-Time Delivery

In modern manufacturing and service industries, the ability to meet promised delivery dates is not just a tactical goal—it is a strategic necessity. In a competitive marketplace, effective scheduling is crucial for survival, as a failure to meet customer commitments can lead to a significant loss of goodwill and market share. While some scheduling strategies focus on minimizing the average or maximum *degree* of lateness, a more common and often more critical business objective is to maximize the *percentage* of orders that are completed on time. This metric, frequently tied to service level agreements (SLAs), directly measures a firm's reliability.

This guide provides a detailed analysis of a foundational problem in scheduling theory: minimizing the total number of tardy jobs on a single machine. Formally denoted as $1 || \sum U_j$, this problem seeks the optimal sequence to complete the maximum number of jobs by their due dates. We will dissect the elegant and efficient algorithm for solving this problem, walk through a practical step-by-step example of its application, and explore its real-world implications and limitations. We begin by establishing a formal model of the problem and introducing the core algorithm that guarantees an optimal solution.

1. The Core Problem: Minimizing Tardy Jobs ($1 || \sum U_j$)

To rigorously analyze and solve any scheduling challenge, we must first translate the business objective into a formal model. The problem of minimizing the number of tardy jobs on a single machine is captured by the standard notation $1 || \sum U_j$. This triplet conveys the essential structure of the problem:

• **1**: Specifies a **single machine** environment.

• **||**: Indicates there are **no special constraints**, such as sequence-dependent setup times or preemption.

• **$\sum U_j$**: Defines the objective function, which is to minimize the sum of the variables $U_j$ for all jobs $j$. Each $U_j$ is a binary variable defined as:

  ◦ $U_j = 1$ if job $j$ is tardy (i.e., its completion time $C_j$ is after its due date $d_j$).

  ◦ $U_j = 0$ if job $j$ is on time (i.e., $C_j \le d_j$).

Minimizing this sum is therefore equivalent to minimizing the count of late jobs. The following algorithm provides an optimal solution to this problem.

The following algorithm finds an optimal schedule by first putting all jobs in a set J that are candidates for being on time. The jobs are considered one by one in increasing order of their due dates. If including job k to the set of scheduled jobs implies that job k would be completed late, then the scheduled job with the longest processing time, say job l, is marked late and discarded.

The Intuition Behind the Algorithm

The logic of this algorithm is both powerful and intuitive. It begins by sorting jobs in **Earliest Due Date (EDD)** order, a common and effective starting point for due-date-related objectives. It then iteratively builds a set of jobs that can be completed on time. The critical insight comes when adding a new job makes the schedule late. Instead of simply rejecting the current job, the algorithm looks at the entire set of jobs currently scheduled to be on time (including the one just added) and removes the one with the **longest processing time**.

Why is this the optimal strategy? Removing the longest job frees up the maximum possible amount of processing time, creating the most opportunity for other, shorter jobs to be completed by their due dates. By always making this "best" trade-off—sacrificing the job that consumes the most time to save others—the algorithm greedily constructs an optimal set of on-time jobs. This type of locally optimal decision-making is characteristic of a greedy algorithm. The effectiveness of this approach is formally stated in the following theorem.

**Theorem 3.3.3.** Algorithm 3.3.1 is optimal for $1 || \sum U_j$.

A key advantage of this algorithm is its efficiency. The most computationally intensive step is the initial sorting of jobs by their due dates, which can be performed in $O(n \log(n))$ time. This makes the algorithm highly practical for real-world applications, even with a large number of jobs. To see how this works in practice, we will now apply the algorithm to a concrete example.

### Step-by-Step Application: A Worked Example

The most effective way to understand an algorithm is to see it in action. This section provides a detailed walkthrough of Algorithm 3.3.1 using the data from Example 3.3.2 in the source text. By following each decision point, the logic of sorting by due date and removing the longest job will become clear.

#### Problem Data

Consider a set of four jobs to be processed on a single machine. Each job has a specific processing time ($p_j$) and a due date ($d_j$), as detailed in the table below.

| Job ($j$) | Processing Time ($p_j$) | Due Date ($d_j$) |
| --------- | ----------------------- | ---------------- |
| 1         | 2                       | 4                |
| 2         | 3                       | 5                |
| 3         | 5                       | 6                |
| 4         | 6                       | 10               |

Detailed Walkthrough

We will now apply the algorithm step-by-step to find the schedule that minimizes the number of tardy jobs. Let `S` be the set of jobs scheduled to be on time.

1. **Sort Jobs by Earliest Due Date (EDD)** The first step is to sort the jobs in non-decreasing order of their due dates. In this case, the jobs are already in EDD order: **1, 2, 3, 4**.
2. **Iteration 1 (Job 1)** We begin with the first job in the EDD sequence.

  ◦ Add Job 1 to the set of on-time jobs: `S = {1}`.

  ◦ Cumulative processing time: $p_1 = 2$.

  ◦ Check against due date: The completion time of 2 is $\le$ Job 1's due date of 4. Job 1 is on time.

3. **Iteration 2 (Job 2)** Next, consider Job 2.

  ◦ Add Job 2 to the set: `S = {1, 2}`.

  ◦ Cumulative processing time: $p_1 + p_2 = 2 + 3 = 5$.

  ◦ Check against due date: The completion time of 5 is $\le$ Job 2's due date of 5. The jobs in `S` remain on time.

4. **Iteration 3 (Job 3)** Now, consider Job 3.

  ◦ Temporarily add Job 3: `S = {1, 2, 3}`.

  ◦ Cumulative processing time: $p_1 + p_2 + p_3 = 2 + 3 + 5 = 10$.

  ◦ Check against due date: The completion time of 10 exceeds Job 3's due date of 6. The schedule is now tardy.

  ◦ Resolve tardiness: Remove the job with the *longest processing time* from the current set `{1, 2, 3}`. This is Job 3 ($p_3=5$). The set of on-time jobs reverts to `S = {1, 2}`.

5. **Iteration 4 (Job 4)** Finally, consider Job 4.

  ◦ Temporarily add Job 4 to the current on-time set: `S = {1, 2, 4}`.

  ◦ Cumulative processing time: $p_1 + p_2 + p_4 = 2 + 3 + 6 = 11$.

  ◦ Check against due date: The completion time of 11 exceeds Job 4's due date of 10. The schedule is tardy.

  ◦ Resolve tardiness: Remove the job with the *longest processing time* from the current set `{1, 2, 4}`. This is Job 4 ($p_4=6$). The set of on-time jobs reverts to `S = {1, 2}`.

Final Solution

The algorithm has processed all jobs. The final sets are:

• **On-Time Jobs**: `{1, 2}`

• **Tardy Jobs**: `{3, 4}`

The minimum number of tardy jobs for this problem instance is **2**. The on-time jobs, {1, 2}, would be scheduled in that order, completing at times 2 and 5, respectively.

This manual walkthrough demonstrates the algorithm's mechanics. To make it more concrete, the next section provides a code implementation that automates this process.

### Python Implementation of the Algorithm

To make this solution more tangible and reusable, this section provides a Python implementation of Algorithm 3.3.1. 

> [Algorithm 3.3.1 Minimizing Number of Tardy Jobs]
>
> Step 1: 
>
> *Set $J = \emptyset$, $J^c = \{1, \ldots, n\}$, and $J^d = \emptyset$.* 
>
> *Set the counter $k = 1$.*
>
> *Step 2.  
> 		Add job $k$ to $J$.  
> 		Delete job $k$ from $J^c$.  
> 		Go to Step 3.*  
>
> *Step 3.  
> 		If*  
> $$
> \sum_{j \in J} p_j \le d_k,
> $$
> *go to Step 4.*  
>
> *Otherwise, let $\ell$ denote the job that satisfies*  
> $$
> p_\ell = \max_{j \in J}(p_j).
> $$
> *Delete job $\ell$ from $J$.  Add job $\ell$ to $J^d$.*  
>
> *Step 4.  
> 		Set $J_k = J$.  If $k = n$ STOP,  otherwise set $k = k + 1$ and go to Step 2.*

This code serves as a practical template that can be adapted to solve similar scheduling problems, translating the theoretical steps into executable logic.

```
def minimize_tardy_jobs(jobs):
    """
    Finds the optimal schedule to minimize the number of tardy jobs on a single machine.

    Args:
        jobs: A list of dictionaries, where each dictionary represents a job and has
              'id', 'pj' (processing time), and 'dj' (due date) keys.

    Returns:
        A tuple containing two lists:
        - on_time_jobs: The list of jobs that can be completed on time.
        - tardy_jobs: The list of jobs that will be tardy.
    """
    # Step 1: Sort jobs by their due dates in non-decreasing order.
    sorted_jobs = sorted(jobs, key=lambda x: x['dj'])

    on_time_jobs = []
    current_time = 0

    # Step 2-5: Iterate through the sorted jobs.
    for job in sorted_jobs:
        # Add the current job to the set of on-time candidates.
        on_time_jobs.append(job)
        current_time += job['pj']

        # Check if adding the current job makes it tardy.
        if current_time > job['dj']:
            # If tardy, find the job in the current on-time set with the longest processing time.
            longest_job = max(on_time_jobs, key=lambda x: x['pj'])
            
            # Remove the longest job and update the current time.
            on_time_jobs.remove(longest_job)
            current_time -= longest_job['pj']

    # Identify the tardy jobs by comparing the full list with the on-time list.
    all_job_ids = {j['id'] for j in jobs}
    on_time_job_ids = {j['id'] for j in on_time_jobs}
    tardy_job_ids = all_job_ids - on_time_job_ids
    
    tardy_jobs = [j for j in jobs if j['id'] in tardy_job_ids]

    return on_time_jobs, tardy_jobs

# Demonstrate the code with the correct data from the source text
problem_data = [
    {'id': 1, 'pj': 2, 'dj': 4},
    {'id': 2, 'pj': 3, 'dj': 5},
    {'id': 3, 'pj': 5, 'dj': 6},
    {'id': 4, 'pj': 6, 'dj': 10}
]

on_time, tardy = minimize_tardy_jobs(problem_data)

print("--- Optimal Solution ---")
print(f"On-Time Jobs: {[job['id'] for job in on_time]}")
print(f"Tardy Jobs: {[job['id'] for job in tardy]}")
print(f"Minimum Number of Tardy Jobs: {len(tardy)}")

# Expected Output:
# --- Optimal Solution ---
# On-Time Jobs: [1, 2]
# Tardy Jobs: [3, 4]
# Minimum Number of Tardy Jobs: 2
```

This implementation correctly finds the optimal set of on-time jobs, confirming the result from our manual walkthrough. While this elegant algorithm efficiently handles the unweighted version of the problem, business realities often require prioritizing certain jobs over others. As we will see next, adding weights to the objective fundamentally changes the problem's complexity.

### A Complication: The Weighted Case ($1 || \sum w_j U_j$)

In the real world, not all jobs are created equal. Some may be for high-value customers, carry higher penalties for lateness, or represent greater revenue. To model this, we introduce a weight, $w_j$, for each job. The objective then becomes minimizing the sum of the weights of the tardy jobs, a problem denoted as $1 || \sum w_j U_j$. This seemingly small change moves the problem into a completely different category of difficulty.

#### The Jump to NP-Hard

The problem $1 || \sum w_j U_j$ is **NP-hard**. In practical terms, this means there is no known efficient algorithm (like the one for the unweighted case) that can guarantee an optimal solution for all instances. As the number of jobs grows, the time required to find a proven optimal solution explodes, making exact methods infeasible for large-scale problems.

#### Connection to the Knapsack Problem

The complexity of the weighted problem becomes clear through its relationship to a classic problem in computer science: the **knapsack problem**. When all jobs have an identical due date, $d$, the problem $1 || \sum w_j U_j$ is equivalent to the knapsack problem. The analogy is as follows:

• The **knapsack capacity** is the common due date, $d$.

• The **items** are the jobs.

• The **size of each item** is the job's processing time, $p_j$.

• The **value of each item** is its weight, $w_j$.

The goal is to select a subset of jobs (items) to complete on time (place in the knapsack) such that their total processing time does not exceed the due date, and the total weight of the jobs *left out* is minimized. This is equivalent to maximizing the value of the items inside the knapsack.

#### Analyzing a Common Heuristic: WSPT

Given the problem's difficulty, managers often rely on heuristics, or rules of thumb, to find good-enough solutions. A popular heuristic for this problem is the **Weighted Shortest Processing Time (WSPT)** rule, which sequences jobs in decreasing order of their $w_j/p_j$ ratio. The logic is to prioritize jobs that deliver the most "value" ($w_j$) per unit of processing "cost" ($p_j$). While intuitive, this heuristic can perform very poorly in certain situations.

A Cautionary Example: The Failure of a Heuristic

Consider the following three-job instance from Example 3.3.4, where all due dates are 100.

| Job ($j$) | Processing Time ($p_j$) | Weight ($w_j$) | Due Date ($d_j$) | $w_j/p_j$ |
| --------- | ----------------------- | -------------- | ---------------- | --------- |
| 1         | 11                      | 12             | 100              | 1.09      |
| 2         | 9                       | 9              | 100              | 1.00      |
| 3         | 90                      | 89             | 100              | 0.99      |

• **WSPT Schedule:** The WSPT rule produces the sequence `1, 2, 3`.

  ◦ Job 1 completes at $C_1 = 11$ (on time).

  ◦ Job 2 completes at $C_2 = 11 + 9 = 20$ (on time).

  ◦ Job 3 completes at $C_3 = 20 + 90 = 110$ (tardy).

  ◦ The total weight of tardy jobs is $w_3 = \textbf{89}$.

• **Optimal Schedule:** Now consider the sequence `2, 3, 1`.

  ◦ Job 2 completes at $C_2 = 9$ (on time).

  ◦ Job 3 completes at $C_3 = 9 + 90 = 99$ (on time).

  ◦ Job 1 completes at $C_1 = 99 + 11 = 110$ (tardy).

  ◦ The total weight of tardy jobs is $w_1 = \textbf{12}$.

The large gap between the heuristic's result (89) and the optimal solution (12) highlights a critical lesson: simple, intuitive rules can produce arbitrarily bad results for NP-hard problems. Understanding the underlying complexity is essential for avoiding costly scheduling errors. This leads us to consider further practical implications of these models.

### Industrial Applications and Q&A

The theoretical models for minimizing tardy jobs provide a powerful foundation for decision-making, but their application in complex industrial settings requires careful consideration of context. This section explores the practical relevance of these concepts through a series of common questions.

**In a factory setting, when would a manager prioritize minimizing the** **number** **of tardy jobs ($ \sum U_j $) over minimizing the** **total tardiness** **($ \sum T_j $)?**

The choice of objective depends entirely on the business's performance metrics and penalty structures.

• **Minimizing Number of Tardy Jobs ($\sum U_j$)** is critical when performance is measured by a **service level**, such as the percentage of on-time shipments. Many contracts include Service Level Agreements (SLAs) that stipulate, for example, that "95% of all orders must ship by their due date." In this scenario, it doesn't matter if a late job is one day or one month late; it simply counts as a single failure. The goal is to maximize the count of successes.

• **Minimizing Total Tardiness ($\sum T_j$)** is more relevant when penalties are **proportional to the length of the delay**. For instance, a contract might impose a penalty of $500 for each day an order is late. Here, having two orders that are each one day late (total tardiness = 2) is far preferable to having one order that is five days late (total tardiness = 5). This objective aims to minimize the cumulative customer dissatisfaction or financial penalty across all late jobs.

**The algorithm assumes all jobs are known in advance. How can its principles be applied in a dynamic shop floor where rush orders arrive unexpectedly?**

While the formal algorithm requires a static list of jobs, its underlying logic remains valuable in dynamic environments. A common industrial practice is to implement a **periodic rescheduling** policy.

1. **Scheduled Resets:** The scheduling algorithm is run at regular intervals (e.g., at the start of every shift) using the currently known set of jobs.
2. **Event-Triggered Rescheduling:** When an unexpected high-priority or "rush" order arrives, it triggers an immediate re-run of the scheduling algorithm. The new job is added to the list, and a new optimal sequence is generated for the remaining work.

In this dynamic context, the core principles of the algorithm—sorting by due date and intelligently removing the "least valuable" job (the one with the longest processing time, $p_j$) to make room—provide a robust framework for making consistent, data-driven decisions in response to new information.

**What is the primary risk of using this algorithm if jobs also have significant, sequence-dependent setup times?**

The primary risk is that the algorithm's recommendations could be severely suboptimal, potentially leading to *more* tardy jobs than a less theoretically "optimal" sequence. The $1 || \sum U_j$ model completely ignores setup times, which can be a fatal flaw in many industries (e.g., printing, chemical processing, paper manufacturing).

An EDD-based sequence might require frequent changeovers between very different job families, leading to long and costly setup times. For example, scheduling a small, urgent print job between two large, dissimilar ones could introduce hours of setup, delaying not only that job but all subsequent jobs as well. In such cases, a non-EDD sequence that groups similar jobs into batches would reduce total setup time, shorten the overall makespan, and likely result in fewer tardy jobs. More complex heuristics, like the **Apparent Tardiness Cost with Setups (ATCS)** rule, are designed specifically to balance due dates, processing times, and setup times, and would be far more appropriate in such an environment.

\--------------------------------------------------------------------------------



The problem of minimizing the number of tardy jobs offers a clear illustration of core scheduling principles. The unweighted case is elegantly solved with an efficient, greedy algorithm that provides a powerful tool for service-level-driven operations. However, the introduction of weights transforms the problem into a much harder, NP-hard challenge, cautioning against the blind application of simple heuristics. Ultimately, the key to effective scheduling lies in selecting a model that accurately reflects the specific constraints and objectives of the industrial environment.