---
title: "Scheduling 2 - Deterministric Model: Single Machine Model"
date: 2025-08-27
draft: false
description: "Deterministric Model: Preliminaries"
tags: ["Scheduling","Mathematical Modeling"]
categories: ["learning", "operations research"]
math: true

---

# A Practical Guide to Job Scheduling: Minimizing Total Weighted Completion Time

Single-machine scheduling models are the fundamental building blocks of production planning. While seemingly simple, they offer powerful, foundational insights that can be extended to understand more complex, real-world systems like multi-stage production lines and entire job shops. A critical objective in this field is the minimization of **Total Weighted Completion Time**, denoted as $\sum w_j C_j$. This objective is not merely an academic exercise; it carries direct strategic importance for any business. It provides a formal way to balance two competing goals: finishing jobs quickly and prioritizing the most important ones. The "weight" ($w_j$) of a job can represent its economic value, a penalty for late delivery, or the strategic importance of a customer. The "completion time" ($C_j$) directly correlates to the time that capital is tied up in work-in-process inventory. Minimizing this objective, therefore, aligns with key business goals like satisfying high-value customers, reducing inventory holding costs, and improving cash flow by completing valuable jobs faster.

Imagine a custom print shop managing multiple orders. An order for 10,000 high-margin brochures for a major corporate client is far more critical than a small, low-margin order for 200 business cards. By assigning a higher weight ($w_j$) to the corporate order, the $ \sum w_j C_j $ objective ensures it receives priority, reflecting its greater contribution to revenue and customer satisfaction. The central challenge is to find a sequence that optimally balances these priorities across all jobs. Fortunately, for the most common version of this problem, a simple and elegant rule provides the optimal solution.

## The Foundational Principle: The Weighted Shortest Processing Time (WSPT) Rule

The most fundamental single-machine scheduling problem, denoted `1 || ∑wjCj`, involves a set of independent jobs that are all available at time zero, waiting to be processed on a single machine. For this ubiquitous problem, a simple, intuitive, and mathematically optimal rule exists.

**Theorem 3.1.1.** The WSPT rule is optimal for $1 \ || \ \sum w_j C_j$.

### The "So What?" of the WSPT Rule

The **Weighted Shortest Processing Time (WSPT)** rule is a powerful prioritization logic. It directs the scheduler to calculate an efficiency ratio for each job—its weight divided by its processing time ($w_j / p_j$)—and then sequence the jobs in decreasing order of this ratio.

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
| 1      | 6              | 3                       | 2.00                   |
| 2      | 11             | 5                       | 2.20                   |
| 3      | 9              | 7                       | 1.29                   |
| 4      | 5              | 4                       | 1.25                   |

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

The next level of scheduling complexity arises when jobs are subject to precedence constraints, meaning one job must be completed before another can begin. While solving for the optimal schedule under general precedence constraints (`1 | prec | ∑wjCj`) is computationally very difficult, a common and solvable case occurs when these constraints form parallel chains of operations.

### Generalizing the WSPT Logic to Chains

The core logic of the WSPT rule can be extended from individual jobs to entire chains. When faced with a choice between two independent chains of jobs, we can treat each chain as a single "macro-job" and apply the WSPT logic at the chain level. A similar efficiency ratio can guide the decision, as confirmed by the following lemma.

**Lemma 3.1.2.** If $ \frac{\sum_{j=1}^{k} w_j}{\sum_{j=1}^{k} p_j} > (<) \frac{\sum_{j=k+1}^{n} w_j}{\sum_{j=k+1}^{n} p_j} $, then it is optimal to process the chain of jobs 1, . . . , k before (after) the chain of jobs k + 1, . . . , n.

This lemma provides a clear rule for prioritizing complete chains, stating that we should process the one with the higher aggregate ratio of total weight to total processing time first.

### A More Powerful Concept for Interleaving Chains

An optimal schedule might not process an entire chain at once; it may be better to process part of one chain, switch to another, and then return. This raises the question: if we can't schedule a whole chain, what's the most profitable *part* of a chain to schedule right now? The answer lies in a more nuanced metric called the **ρ-factor**.

The ratio on the left-hand side is called the ρ-factor of chain 1, . . . , k and is denoted by ρ(1, . . . , k). Job $l^*$ is referred to as the job that determines the ρ-factor of the chain.

The ρ-factor is found by calculating the WSPT ratio for every possible prefix of a chain (the first job, the first two jobs, the first three, etc.) and identifying the maximum value. The job that ends the prefix with the highest ratio is the one that "determines" the ρ-factor. The following lemma confirms that this prefix is the most efficient "bang for the buck" block of work available in that chain and should be scheduled without interruption.

**Lemma 3.1.3.** If job $l^*$ determines ρ(1, . . . , k), then there exists an optimal sequence that processes jobs 1, . . . , $l^*$ one after another without any interruption by jobs from other chains.

This lemma is the key to the algorithm. It tells us that the optimal strategy is to always identify the chain with the highest overall efficiency (the highest ρ-factor) and then schedule its most efficient part (the prefix ending in $l^*$) without interruption. This insight leads directly to an elegant algorithm.

### The Algorithm for Chains

**Algorithm 3.1.4 (Total Weighted Completion Time and Chains)** Whenever the machine is freed, select among the remaining chains the one with the highest ρ-factor. Process this chain without interruption up to and including the job that determines its ρ-factor.

### Example Application: Scheduling Two Parallel Chains

Let's walk through an example to see how Algorithm 3.1.4 works in practice.

- **Source Data:**
  - **Chain 1:** 1 → 2 → 3 → 4
  - **Chain 2:** 5 → 6 → 7

| Job ID | Weight ($w_j$) | Processing Time ($p_j$) |
| ------ | -------------- | ----------------------- |
| 1      | 6              | 3                       |
| 2      | 18             | 6                       |
| 3      | 12             | 6                       |
| 4      | 8              | 5                       |
| 5      | 8              | 4                       |
| 6      | 17             | 8                       |
| 7      | 18             | 10                      |

**Step 1: Initial ρ-Factor Calculation**

We calculate the ρ-factor for each chain by finding the maximum cumulative ratio.

- **Chain 1:**
  - `ρ(1) = 6/3 = 2.00`
  - `ρ(1,2) = (6+18)/(3+6) = 24/9 = 2.67` **(Maximum)**
  - `ρ(1,2,3) = (24+12)/(9+6) = 36/15 = 2.40`
  - `ρ(1,2,3,4) = (36+8)/(15+5) = 44/20 = 2.20`
  - The ρ-factor for Chain 1 is **2.67**, determined by job 2.
- **Chain 2:**
  - `ρ(5) = 8/4 = 2.00`
  - `ρ(5,6) = (8+17)/(4+8) = 25/12 = 2.08` **(Maximum)**
  - `ρ(5,6,7) = (25+18)/(12+10) = 43/22 = 1.95`
  - The ρ-factor for Chain 2 is **2.08**, determined by job 6.

**Step 2: First Scheduling Decision**

We compare the ρ-factors: Chain 1 (2.67) > Chain 2 (2.08). The algorithm dictates we schedule the prefix of Chain 1 that determines its ρ-factor, which is jobs 1 and 2.

- **Current Schedule:** `(1, 2)`

**Step 3: Update and Re-evaluate**

After scheduling jobs 1 and 2, the remaining chains are `{3 → 4}` and `{5 → 6 → 7}`. We recalculate the ρ-factor for the new, shorter version of Chain 1.

- **Chain {3 → 4}:** `ρ(3) = 12/6 = 2.00` is the maximum.
- **Chain {5 → 6 → 7}:** The ρ-factor is unchanged at **2.08**.

**Step 4: Second Scheduling Decision**

We compare the new ρ-factors: Chain {5 → 6 → 7} (2.08) > Chain {3 → 4} (2.00). We schedule the prefix of Chain 2 that determines its ρ-factor, which is jobs 5 and 6.

- **Current Schedule:** `(1, 2, 5, 6)`

**Step 5: Continue the Process**

The remaining chains are now `{3 → 4}` and `{7}`.

- **Chain {3 → 4}:** The ρ-factor is still `ρ(3) = 2.00`.
- **Chain {7}:** `ρ(7) = 18/10 = 1.80`.

We compare: Chain {3 → 4} (2.00) > Chain {7} (1.80). We schedule job 3.

- **Current Schedule:** `(1, 2, 5, 6, 3)`

**Step 6: Final Decision**

The remaining jobs are `{4}` and `{7}`, which are single-job chains. In this case, the algorithm defaults to the standard WSPT rule. We compare their individual $w_j/p_j$ ratios.

- **Job 7:** $w_7/p_7 = 18/10 = 1.80$
- **Job 4:** $w_4/p_4 = 8/5 = 1.60$

Since Job 7 has the higher ratio, it is scheduled next, followed by Job 4.

**Step 7: Final Optimal Sequence**

The complete optimal sequence is: **1, 2, 5, 6, 3, 7, 4**.

This powerful algorithm allows us to find optimal solutions for a more complex and realistic class of scheduling problems. Translating these logical rules into executable code makes them even more powerful.

## Python Implementation of the Scheduling Algorithms

Translating these scheduling rules into code is a straightforward process that enables rapid, repeatable, and scalable optimization. Below are Python implementations for both the basic WSPT rule and the more advanced algorithm for handling precedence chains.

### Code for the WSPT Rule ()

This function implements the basic WSPT rule for a set of independent jobs available at time zero.

```python
def solve_wspt(jobs):
    """
    Solves the 1 || sum(wjCj) problem using the WSPT rule.
    
    Args:
        jobs (list): A list of job dictionaries. Each dict should have
                     'id', 'w' (weight), and 'p' (processing_time).

    Returns:
        tuple: A tuple containing the optimal sequence (list of job ids)
               and the minimum total weighted completion time (float).
    """
    # Step 1: Calculate the WSPT ratio (w/p) for each job.
    # This ratio represents the "bang for the buck" or efficiency of each job.
    for job in jobs:
        job['ratio'] = job['w'] / job['p']

    # Step 2: Sort jobs in descending order of the ratio to find the optimal sequence.
    sorted_jobs = sorted(jobs, key=lambda x: x['ratio'], reverse=True)
    
    optimal_sequence = [job['id'] for job in sorted_jobs]
    
    # Step 3 & 4: Iterate through the sorted sequence to calculate completion times
    # and sum the weighted completion times to get the final objective value.
    total_weighted_completion_time = 0
    current_completion_time = 0
    for job in sorted_jobs:
        current_completion_time += job['p']
        total_weighted_completion_time += job['w'] * current_completion_time
        
    return optimal_sequence, total_weighted_completion_time

# --- Example Usage ---
# Data from Section 2
job_data_simple = [
    {'id': 1, 'w': 6, 'p': 3},
    {'id': 2, 'w': 11, 'p': 5},
    {'id': 3, 'w': 9, 'p': 7},
    {'id': 4, 'w': 5, 'p': 4}
]

sequence, value = solve_wspt(job_data_simple)
print(f"Optimal Sequence (WSPT): {sequence}")
print(f"Minimum Total Weighted Completion Time: {value}")
```

### Code for WSPT with Precedence Chains ()

This implementation handles jobs arranged in parallel precedence chains, using the ρ-factor logic from Algorithm 3.1.4. The main function implements the algorithm's loop: in each iteration, it calculates the ρ-factor for the current state of every chain, picks the winner, schedules the optimal prefix from that chain, and then updates the chains for the next iteration.

```python
import copy

def calculate_rho_factor(chain):
    """
    Calculates the rho-factor and the prefix that determines it for a given chain.
    The rho-factor is the maximum cumulative weight/processing time ratio.

    Args:
        chain (list): A list of job dictionaries representing the chain.

    Returns:
        tuple: A tuple containing (rho_value, prefix_length).
    """
    if not chain:
        return (-1, 0) # Return a value that won't be chosen as the max

    max_rho = -1
    prefix_len_for_max_rho = 0
    
    cumulative_w = 0
    cumulative_p = 0
    
    # Iterate through all possible prefixes of the chain
    for i, job in enumerate(chain):
        cumulative_w += job['w']
        cumulative_p += job['p']
        current_rho = cumulative_w / cumulative_p
        
        # If the current prefix has a higher ratio, update the max
        if current_rho > max_rho:
            max_rho = current_rho
            prefix_len_for_max_rho = i + 1
            
    return (max_rho, prefix_len_for_max_rho)

def solve_wspt_chains(chains):
    """
    Solves the 1 | chains | sum(wjCj) problem using Algorithm 3.1.4.

    Args:
        chains (list): A list of lists, where each inner list contains job
                       dictionaries representing a chain.

    Returns:
        list: The optimal sequence of job ids.
    """
    # Using deepcopy is good practice to prevent modifying the original input data,
    # which can be a common source of bugs in complex programs.
    remaining_chains = copy.deepcopy(chains)
    optimal_sequence = []

    # Continue scheduling until all chains are empty
    while any(remaining_chains):
        # In each iteration, calculate rho-factors for the current state of all chains
        rho_factors = [calculate_rho_factor(chain) for chain in remaining_chains]
        
        # Find the chain with the maximum rho-factor
        max_rho = -1
        best_chain_idx = -1
        for i, (rho_val, _) in enumerate(rho_factors):
            if rho_val > max_rho:
                max_rho = rho_val
                best_chain_idx = i

        # Get the length of the optimal prefix for the winning chain
        prefix_length = rho_factors[best_chain_idx][1]
        
        # Append the jobs from that prefix to the final schedule
        prefix_to_schedule = remaining_chains[best_chain_idx][:prefix_length]
        for job in prefix_to_schedule:
            optimal_sequence.append(job['id'])
            
        # Update the chain by removing the scheduled prefix
        remaining_chains[best_chain_idx] = remaining_chains[best_chain_idx][prefix_length:]

        # Clean up by removing any chains that are now empty
        remaining_chains = [chain for chain in remaining_chains if chain]
        
    return optimal_sequence

# --- Example Usage ---
# Data from Section 3
chain_1 = [
    {'id': 1, 'w': 6, 'p': 3}, {'id': 2, 'w': 18, 'p': 6},
    {'id': 3, 'w': 12, 'p': 6}, {'id': 4, 'w': 8, 'p': 5}
]
chain_2 = [
    {'id': 5, 'w': 8, 'p': 4}, {'id': 6, 'w': 17, 'p': 8},
    {'id': 7, 'w': 18, 'p': 10}
]
job_data_chains = [chain_1, chain_2]

sequence_chains = solve_wspt_chains(job_data_chains)
print(f"\nOptimal Sequence (Chains): {sequence_chains}")
```

## Practical Applications and Industry Q&A

The true value of these academic models lies in their application to real-world industrial problems. While the assumptions may seem simplified, the underlying logic is robust and provides a powerful starting point for improving operational performance. This section addresses common questions practitioners have when trying to bridge the gap between scheduling theory and daily practice.

### **Q1: How does minimizing total weighted completion time ($\sum w_j C_j$) create tangible business value in a factory or service center?**

Minimizing the $ \sum w_j C_j $ objective translates directly into three key areas of business value:

1. **Customer Prioritization:** In any business, not all orders are created equal. The weight, $w_j$, can be set to represent the value of a key customer, the profit margin of an order, or a contractual penalty for lateness. By minimizing $\sum w_j C_j$, the system naturally prioritizes finishing high-weight jobs sooner. This leads to improved on-time delivery performance for critical orders, enhancing customer satisfaction, loyalty, and retention.
2. **Financial Efficiency:** The completion time, $C_j$, is the exact moment a job turns from work-in-process (WIP) inventory into a finished good that can be shipped and invoiced. The objective $\sum w_j C_j$ can be interpreted as minimizing the total value-weighted time that jobs spend in the system. This directly reduces WIP inventory holding costs, which frees up working capital. Furthermore, by completing high-value ($w_j$) jobs faster, it accelerates revenue recognition and improves the organization's cash-to-cash cycle.
3. **Operational Throughput:** While not its primary goal, the WSPT rule's preference for jobs with a small processing time $p_j$ in the denominator of the $w_j/p_j$ ratio often has a positive side effect on throughput. By clearing out short jobs quickly, the machine can process a higher number of total jobs in a given time window, which can increase overall system output and efficiency.

### **Q2: My facility is a complex job shop, not a single machine. Can I still use these models?**

Yes, absolutely. While your facility may have many machines, it is highly likely that one machine or work center acts as the primary constraint or **bottleneck** that governs the overall throughput of the entire system. A powerful and widely-used heuristic is to apply the single-machine model ($1 \ || \ \sum w_j C_j$) to schedule *only the bottleneck resource*.

In this approach, the sequence of jobs on the bottleneck is optimized using the WSPT rule. The completion times of jobs at upstream operations determine their arrival times (or release dates `$r_j$`) at the bottleneck. By optimizing the schedule at the single most critical point in the entire process, you can often achieve a disproportionately large improvement (an "80/20" effect) in the overall system's performance, such as total throughput and on-time delivery. (This is the core logic behind widely-used job shop heuristics like the Shifting Bottleneck procedure, described in Chapter 7 of Pinedo's text).

### **Q3: The WSPT rule requires knowing the exact processing time$p_j$. What if our processing times are variable and uncertain?**

This is a common and important consideration. When processing times are not fixed values but are instead random variables, the problem enters the realm of stochastic scheduling. For this scenario, the WSPT rule is generalized to the **Weighted Shortest Expected Processing Time (WSEPT)** rule.

Under the WSEPT rule, you sequence jobs in decreasing order of the ratio $w_j / E(X_j)$, where $E(X_j)$ is the *expected* or average processing time for job $j$. Remarkably, this adapted rule is also optimal for minimizing the expected total weighted completion time under many common stochastic assumptions. The key takeaway is that the fundamental principle—prioritizing jobs based on an efficiency ratio—remains the most effective strategy. Even with uncertainty, using the best available estimate for processing time (e.g., the historical average) is a robust and powerful` approach. (The optimality of WSEPT is formally established in Chapter 10 of the source text).

### **Q4: How does this model handle dynamic events, like a new high-priority "rush order" arriving unexpectedly?**

The WSPT rule is perfectly suited for dynamic environments. It can be implemented as a **dynamic dispatching rule**. This means that instead of creating a fixed schedule for all known jobs at the beginning of a shift, the rule is applied locally in real-time.

Here's how it works: whenever the machine finishes a job and becomes free, the scheduler re-evaluates *all jobs currently waiting in the queue*. It then calculates the $w_j/p_j$ ratio for each of these waiting jobs and selects the one with the highest ratio to be processed next. This is a non-preemptive policy, meaning once a job starts, it is completed. This dynamic application of the WSPT rule is proven to be optimal for minimizing total weighted completion time in a system with random job arrivals, making it an ideal choice for managing dynamic, real-world shop floors. (This is formally proven in Theorem 11.1.1 of Pinedo's analysis of systems with random arrivals).

## Conclusion

The principle of the Weighted Shortest Processing Time (WSPT) rule provides a simple, optimal, and deeply intuitive foundation for tackling single-machine scheduling problems. Its "bang for the buck" logic offers a clear and defensible method for prioritizing work to meet critical business objectives like minimizing in-process inventory and satisfying high-value customers. We have seen that this core principle is not brittle; it can be logically extended to handle more complex scenarios, such as jobs with precedence constraints, by applying the same efficiency-ratio concept to entire chains of operations. Furthermore, the fundamental logic of the WSPT rule holds its power even when faced with the realities of industrial environments, providing a robust basis for scheduling in both stochastic systems with variable processing times and dynamic systems with unexpected arrivals. Understanding and implementing these fundamental models is the essential first step toward achieving effective, efficient, and data-driven production scheduling in any industrial setting.