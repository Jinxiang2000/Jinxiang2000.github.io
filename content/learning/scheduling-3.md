---
title: "Scheduling 3 - Minimizing Maximum Cost"
date: 2025-08-27
draft: false
description: "The Maximum Lateness"
summary: ""
tags: ["Scheduling","Mathematical Modeling"]
categories: ["learning", "operations research"]
math: true

---

### The General Problem: Minimizing Maximum Cost ()

The most generalized version of the maximum lateness problem is denoted as $1 | prec | h_{max}$. This model involves scheduling a set of jobs on a single machine, subject to precedence constraints, with the goal of minimizing a generalized maximum cost objective. The objective is defined as $h_{max} = \max(h_1(C_1), \dots, h_n(C_n))$, where $C_j$ is the completion time of job *j* and $h_j(C_j)$ is its associated nondecreasing cost function. This model's power lies in its flexibility; the cost functions can represent simple lateness, tardiness, or more complex, non-linear penalties related to completion time, making it a powerful tool for analyzing a wide array of due-date-related scenarios.

Algorithm for the General Case

The problem can be solved by a backward dynamic programming algorithm. The method constructs the optimal sequence from the last job to the first. Let $S$ be the set of jobs already scheduled and let $S_c$ be the set of jobs not yet scheduled. The algorithm proceeds as follows:

1. Initially, $S$ is empty and $S_c$ contains all jobs.
2. Calculate the makespan of the currently unscheduled jobs, $C_{max}(S_c) = \sum_{j \in S_c} p_j$.
3. Among all jobs in $S_c$ that are eligible to be scheduled last (i.e., those with no successors in $S_c$), identify the job $j^*$ that minimizes its cost function evaluated at this makespan, $h_k(C_{max}(S_c))$.
4. Schedule job $j^*$ as the last among the jobs in $S_c$. Move $j^*$ from $S_c$ to $S$.
5. Repeat this process for the new (smaller) set of unscheduled jobs until $S_c$ is empty.

This backward approach is powerful because the completion time of the final job is always the total processing time of all jobs, a known constant. This provides a fixed point for evaluation. In a forward approach, the completion time of each job is a variable that depends on a yet-to-be-determined sequence, adding layers of complexity that this algorithm elegantly sidesteps. The algorithm is computationally efficient, with a worst-case computation time of $O(n^2)$.

Step-by-Step Application: Example 3.2.3

To illustrate this algorithm, consider an instance with three jobs. The data for these jobs are presented below.

| Job  | $p_j$ | $h_j(C_j)$          |
| ---- | ----- | ------------------- |
| 1    | 2     | Cost is 1 + C       |
| 2    | 3     | Cost is 1.2 * C     |
| 3    | 5     | Cost is constant 10 |

The algorithm proceeds as follows:

• **Step 1 (Last Position):** Initially, all jobs are unscheduled in set $S_c = \{1, 2, 3\}$. The makespan of this set is $C_{max} = 2 + 3 + 5 = 10$. We must find the job *k* that minimizes $h_k(10)$.

  ◦ $h_1(10) = 1 + 10 = 11$

  ◦ $h_2(10) = 1.2 \cdot 10 = 12$

  ◦ $h_3(10) = 10$ The minimum cost is 10, associated with Job 3. Therefore, Job 3 is scheduled last. It will be processed in the time interval [5, 10].

• **Step 2 (Second-to-Last Position):** With Job 3 scheduled, the remaining jobs are $S_c = \{1, 2\}$. The makespan for this subset is $C_{max}(\{1, 2\}) = 2 + 3 = 5$. We now find the job *k* from {1, 2} that minimizes $h_k(5)$.

  ◦ $h_1(5) = 1 + 5 = 6$

  ◦ $h_2(5) = 1.2 \cdot 5 = 6$ The costs are equal. This means either Job 1 or Job 2 can be scheduled in the second-to-last position.

• **Conclusion:** Because both Job 1 and Job 2 result in the same cost at the second step, two optimal schedules exist: **1, 2, 3** and **2, 1, 3**.

This general model provides a robust framework for minimizing various maximum cost objectives. We now turn to its most well-known special case: minimizing the maximum lateness.

\--------------------------------------------------------------------------------

### A Famous Special Case: Minimizing Maximum Lateness ()

The problem of minimizing the maximum lateness, denoted $1 || L_{max}$, is arguably the best-known special case of $1 | prec | h_{max}$. Here, each job *j* has a processing time $p_j$ and a due date $d_j$. The objective is to find a sequence that minimizes the maximum lateness, defined as $L_{max} = \max_j(L_j) = \max_j(C_j - d_j)$, where $C_j$ is the completion time of job *j*. A positive lateness value indicates a job is tardy, while a negative value means it is early.

The optimal solution for the $1 || L_{max}$ problem is achieved by applying the **Earliest Due Date (EDD)** rule. This rule is remarkably simple and intuitive: sequence the jobs in non-decreasing order of their due dates ($d_1 \le d_2 \le \dots \le d_n$). By prioritizing jobs that are due sooner, the EDD rule systematically minimizes the worst-case delay across all jobs, thereby minimizing $L_{max}$.

The EDD rule holds significant strategic importance in scheduling theory and practice. It is a simple, powerful, and easily implemented dispatching rule that requires no complex computation, only a single sort of the jobs. Its optimality provides a crucial theoretical foundation and serves as a benchmark for more complex scheduling problems. In industrial settings, the logic of EDD is often a core component within more sophisticated scheduling heuristics.

The simplicity of this problem, however, changes dramatically when we introduce the constraint that jobs may not be available at the start of the schedule.

\--------------------------------------------------------------------------------

### The NP-Hard Challenge: Incorporating Release Dates ()

When each job *j* is assigned a release date $r_j$, before which it cannot be processed, the problem transforms from one with a simple, polynomial-time solution to one that is strongly NP-hard. This complexity shift for $1 | r_j | L_{max}$ means that no efficient algorithm like EDD exists to find the optimal schedule. Instead, solving this problem to optimality requires more computationally intensive methods, such as a **branch-and-bound** procedure.

The Branch-and-Bound Method

The branch-and-bound approach systematically explores a search tree of all possible partial schedules to find an optimal solution. The process involves two key operations:

• **Branching:** This involves creating subproblems from a current node in the search tree. For scheduling, a branch is typically created by fixing the next job in the sequence. For example, from an empty schedule, we could branch by creating nodes where Job 1 is first, Job 2 is first, and so on.

• **Bounding:** At each node (representing a partial schedule), a lower bound is calculated for the objective function ($L_{max}$) for any complete schedule that can be generated from that node. If this lower bound is greater than the objective value of the best-known complete schedule found so far, the entire branch originating from that node can be pruned, or discarded, from the search.

For the $1 | r_j | L_{max}$ problem, an effective lower bound can be computed by applying the **preemptive Earliest Due Date (EDD) rule**. While the non-preemptive version of the problem is NP-hard, the preemptive version ($1 | r_j, prmp | L_{max}$) is known to be optimally solvable by the preemptive EDD rule. The resulting $L_{max}$ from this preemptive schedule provides a tight and efficiently computable lower bound for the non-preemptive problem at each node of the search tree.

Detailed Walkthrough: Example 3.2.4

To demonstrate the branch-and-bound procedure, consider the following instance with four jobs.

| Job  | $p_j$ | $d_j$ | $r_j$ |
| ---- | ----- | ----- | ----- |
| 1    | 10    | 20    | 0     |
| 2    | 2     | 19    | 13    |
| 3    | 6     | 25    | 4     |
| 4    | 5     | 23    | 5     |

The branch-and-bound process explores the search tree as follows:

• **Level 1 Bounding:** We start by calculating lower bounds for scheduling each job in the first position.

  ◦ For the branch starting with Job 1 (node (1, *, *, *)), we must find a lower bound on $L_{max}$ for any schedule that begins with Job 1. This is done by solving the $1 | r_j, prmp | L_{max}$ problem for the remaining jobs {2, 3, 4}, with their processing availability shifted to begin after Job 1 completes at t=10. This calculation yields an $L_{max}$ of **5**, which serves as our lower bound for this branch.

  ◦ For the branch starting with Job 2 (node (2, *, *, *)), a similar lower bound calculation yields an $L_{max}$ of **7**.

• **Level 2 Exploration:** Since the branch starting with Job 1 has a more promising lower bound (5 vs. 7), we explore it further.

  ◦ We explore node (1, 2, *, *). The lower bound for any schedule starting with the sequence 1, 2 is calculated to be **6**.

  ◦ Next, we explore node (1, 3, *, *). The lower bound for this node is calculated to be **5**. Coincidentally, a complete, nonpreemptive schedule—**1, 3, 4, 2**—can be formed from this branch, and its maximum lateness is also exactly 5. This schedule becomes our incumbent best solution.

• **Conclusion:** We have found a complete schedule, 1, 3, 4, 2, with an $L_{max}$ of 5. The lower bound for any schedule starting with Job 2 is 7. Since 7 > 5, we know that no schedule beginning with Job 2 can be better than the solution we have already found. Therefore, the branch starting with Job 2 can be entirely pruned. We can conclude that the schedule **1, 3, 4, 2** is optimal.

These theoretical algorithms provide powerful frameworks for solving scheduling problems. We will now turn to a practical implementation of the more straightforward backward dynamic programming algorithm in Python.

\--------------------------------------------------------------------------------

### Practical Implementation: A Python Code Snippet

While implementing a full branch-and-bound solver is a complex undertaking, the backward dynamic programming algorithm for the general $1 | prec | h_{max}$ problem is more straightforward to code. The following Python snippet provides a self-contained implementation of this algorithm, applying it to the data from Example 3.2.3 to find the optimal job sequence.

```python
def solve_1_prec_hmax(jobs_data):
    """
    Implements the backward algorithm to solve the 1|prec|h_max problem.

    Args:
        jobs_data (dict): A dictionary where keys are job IDs and values are
                          dicts containing 'p_j' (processing time) and 'h_j'
                          (a cost function lambda C: ...).

    Returns:
        tuple: A tuple containing an optimal sequence (list) and the
               minimum maximum cost (float).
    """
    # Initialize the set of unscheduled jobs
    unscheduled_jobs = set(jobs_data.keys())
    # Initialize the final schedule (to be built backwards)
    schedule = []
    
    max_cost = None
    
    # Loop backwards from the final job until all jobs are scheduled
    while unscheduled_jobs:
        # Calculate the makespan of the currently unscheduled set of jobs
        current_makespan = sum(jobs_data[j]['p_j'] for j in unscheduled_jobs)

        best_job = None
        min_cost_at_step = float('inf')

        # Find the job that minimizes the cost function at the current makespan.
        # This assumes no precedence constraints for simplicity.
        for job_id in unscheduled_jobs:
            cost = jobs_data[job_id]['h_j'](current_makespan)
            if cost < min_cost_at_step:
                min_cost_at_step = cost
                best_job = job_id
            # Handle ties by selecting one arbitrarily
            elif cost == min_cost_at_step:
                if best_job is None:
                    best_job = job_id
        
        # The h_max objective value is determined by the cost of the first
        # job selected (the one scheduled last overall). This is the minimum
        # of the maximum costs, which is what the algorithm optimizes.
        if max_cost is None:
            max_cost = min_cost_at_step

        # Place the best job at the beginning of the partial schedule (end of the sequence)
        schedule.insert(0, best_job)
        # Remove the scheduled job from the unscheduled set
        unscheduled_jobs.remove(best_job)
            
    return schedule, max_cost

# Data from Example 3.2.3
jobs = {
    1: {'p_j': 2, 'h_j': lambda C: 1 + C},
    2: {'p_j': 3, 'h_j': lambda C: 1.2 * C},
    3: {'p_j': 5, 'h_j': lambda C: 10}
}

# Solve the problem
# Note: The algorithm finds one of the optimal solutions.
# Due to the tie, either 1,2,3 or 2,1,3 is optimal. This implementation
# will deterministically break the tie, finding one of them.
optimal_sequence, maximum_cost = solve_1_prec_hmax(jobs)

print(f"Optimal Sequence Found: {optimal_sequence}")
print(f"Corresponding Minimum Maximum Cost: {maximum_cost}")
```

Having explored the theory and a practical implementation, we now address some common questions regarding the industrial relevance of these concepts.

\--------------------------------------------------------------------------------

### Industrial Relevance: Q&A

This section addresses common questions about the practical application of single-machine scheduling models in industrial and service settings, bridging the gap between theory and on-the-ground operations.

**Question 1: How does minimizing maximum lateness $L_{max}$differ from minimizing total tardiness ($\sum T_j$) in a real-world manufacturing setting, and when should I prioritize one over the other?**

Minimizing $L_{max}$ and minimizing $\sum T_j$ represent fundamentally different strategic priorities. $L_{max}$ is a worst-case-scenario metric; its goal is to reduce the lateness of the *single most-delayed job*. This objective is critical in environments with stringent service-level agreements (SLAs), for high-value clients where any significant delay is unacceptable, or when a single late component can hold up an entire assembly or project. Prioritizing $L_{max}$ ensures that no single customer or order is excessively penalized. In contrast, minimizing $\sum T_j$ focuses on average performance across all jobs. It is more suitable for high-volume environments where overall throughput and average customer satisfaction are more important than the performance on any one specific order. This objective tolerates a few very late jobs as long as the majority are completed on or near their due dates.

**Question 2: The branch-and-bound method for** **$1 | r_j | L_{max}$** **seems computationally intensive. Is it practical for a dynamic factory floor where new orders arrive constantly? What are the alternatives?**

Correct; branch-and-bound is an offline optimization technique. It is designed to find a guaranteed optimal solution for a static, well-defined problem set. It is ideal for strategic planning activities, such as creating a production plan for a fixed batch of orders or developing a project schedule where all tasks are known in advance. It is computationally intensive and not suitable for real-time, dynamic environments where new orders arrive unpredictably and machine statuses change. For such dynamic scenarios, manufacturers typically rely on fast, heuristic-based **dispatching rules**. These rules, which are often variations of EDD, do not guarantee optimality but provide good, rapid decisions whenever a machine becomes available, allowing the factory floor to react immediately to changing conditions without the delay of a complex optimization run.

**Question 3: The general cost function** **$h_j(C_j)$** **seems abstract. Can you provide a concrete industrial example where this is more useful than simple lateness?**

The flexibility of a general, non-decreasing cost function $h_j(C_j)$ is invaluable when the penalty for delay is not linear. Consider an industrial bakery producing a high-value, perishable product like a specialty cake with a short shelf life. For the first 24 hours after production, the cake is considered fresh and commands a premium price. The "cost" of completion ($h_j(C_j)$) during this period might be zero or even negative (representing profit). After 24 hours, the cake is still sellable but must be discounted, so the cost function rises sharply. After 48 hours, it can no longer be sold and represents a total loss, causing the cost function to jump to the full value of the product. A simple lateness penalty ($C_j - d_j$) cannot model these discrete, non-linear changes in value. The general function $h_j(C_j)$ provides a much more accurate model of the real-world financial consequences of completion time for perishable or time-sensitive goods, such as in food processing, pharmaceuticals, or high-fashion industries.