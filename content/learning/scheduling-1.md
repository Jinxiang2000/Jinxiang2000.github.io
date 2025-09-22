---
title: "Scheduling - Part 1"
date: 2025-08-27
draft: false
description: "Deterministric Model: Preliminaries"
tags: ["Scheduling","Mathematical Modeling"]
categories: ["learning", "operations research"]
math: true
---

## Framework and Notation 

For most of the scheduling problems, we consider  finite number of jobs and finite number of machines. The number of jobs is denoted by n and the number of machines by $m$. Ususally, the subscript $j$ refer to a job while the subscript $i$ refers to a machine. If a jon requires a number of processing steps or operations, then the pair $(i,j)$ refer to the processing step or operation of job $j$ on machine $i$. A scheduling problem is typically defined using a triplet $\alpha | \beta| \gamma$



**Processing Time** $p_{ij}$: it represent the processing time of job j on machine i. The subscript  i is omitted if the processing time of job j does not depend on the machine or if job j is only to process on one single machine.

**Release date** ($r_j$):  The release date $r_j$ of job j may also be referred to as the ready date. It is the time the job arrives at the system, for example, the earliest time at which job $j$ can start its processing.

**Due date**($d_j$). The due date  of job j represent the committed shipping or completion date (i.e. the date of the job is promised to the customer). Completion of a job after its due date is allowed, but then a penalty is incurred. When a due date must be met it is referred to as a deadline and denoted $\bar{d_j}$

**Weight ($w_j$)**: The weight $w_j$ of job $j$ is basically a priority factor, denoting the importance of job j relative to the other job in the system. 

| Field    | Description                                                  | Example                                                      |
| -------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| $\alpha$ | Machine Environment (Single Entry)                           | $1$ (single machine), $P_m$ (Parallel Identical Machines), $J_m$ (Job Shop) |
| $\beta$  | Processing Charateristics/Constraint (Zero or multiple entries) | $r_j$ (Release Dates), $prec$ (Precedence constraints), $s_{jk}$ (Sequence Depenedent Set up time) |
| $\gamma$ | OBJ Function (Single Entry to be minimized)                  | $C_{max}$ (Makespan), $L_{max}$ (Maximum Lateness)           |

### Possible Machine Environment ($\alpha$)

##### Single Machine (1)

The case of a single machine is the simplest of all possible machine environments and is a special case of all other more complicated machine environments.

##### Identical Machine in Parallel ($P_m$)

There are $m$ identical machines in parallel. Job $j$ requires a single operation and may be processed on any one of the $m$ machines or on any one that belongs to a given subset. If job $j$ cannot be processed on just any machine, but only on any one belonging to a specific subset $M_j$, then the entry $M_j$ appears in the $\beta$ field.



















































































