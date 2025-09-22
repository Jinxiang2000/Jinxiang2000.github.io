---
title: "Scheduling 1 - Deterministric Model: Preliminaries"
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



##### Machines in parallel with different speeds($Q_m$) 

Threre are $m$ machines in parallel with different speeds. The speed of machine $i$ is denoted by $v_i$. The time $p_{ij}$ that job $j$ spends on machine $i$ is equal to $\frac{p_j}{v_i}$ (Assuming job $j$ received all its processing from machine $i$). This environment is referred to as uniform machines. If all machines have the same speed, i.e., $v_i = 1$ for all $i$ and $p_{ij}= p_{j}$, then the environment is identical to the previous one.



##### Unrelated machine in parallel($R_m$)

This environment is a further generalization of the previous one. There are $m$ different machines in parallel. Machine $i$ can process job $j$ at speed $v_{ij}$. The time $p_{ij}$ that job $j$ spends on machine $i$ is equal to $\frac{p_j}{v_i}$ (Assuming job $j$ received all its processing from machine $i$).. If the speed of the machines are independent of the jobs, i.e. $v_{ij} = v_{i}$, $\forall i, j$, then the environment is identical to the previous one($Q_m$)



##### Flow Shop ($F_m$)

There are $m$ machine in series. Each job has to me processed on each one of the m machines. All jobs have to follow the same route, i.e., they have to be processed first on machine 1, then on machine 2, and so on. After completion on one machine a job joins the queue at the next machine, Usually, all queues are assumed to operate under the `First in Fist Out (FIFO)` discipline: a job cannot “pass” another while waiting in a queue. If the FIFO discipline is in effect the flow shop is referred  to as a `permutation` flow shop and the $\beta$ field should include  `prmu`

##### Flexible flow shop ($FFc$)

A flexible flow shop is a generalization of the flow shop and the parallel machine environments. Instead of m machines in series there are c stages in series with at each stage a number of identical machines in parallel. Each job has to be processedfirst at stage 1, then at stage 2, and so on. A stage functions as a bank of parallel machines; at each stage job j requires processing on only one machine and any machine can do. The queues between the various stages may or may not operate according to the First Come First Served (FCFS) discipline. (Flexible flow shops have in the literature at times also been referred to as hybrid flow shops and as multi-processor flow shops.)

##### Job Shop  (Jm)

In a job shop with m machines each job has its own predetermined route to follow. A distinction is made between job shops in which each job visits each machine at most once and job shops in which a job may visit each machine more than once. In the latter case the $\beta$-field contains the entry `rcrc` for recirculation.



##### Flexible job shop (FJc)

A flexible job shop is a generalization of the job shop and the parallel machine environments. Instead of m machines in series there are c work centers with at each work center a number of identical machines in parallel. Each job has its own route to follow through the shop; job j requires processing at each work center on only one machine and any machine can do. If a job on its route through the shop may visit a work center more than once,then the $\beta$-field contains the entry `rcrc` for recirculation



##### Open Shop (Om)

There are $m$ machines. Each job has to be processed again on each one of the $m$ machines. However, some of these processing times may be zero. There are no restrictions with regard to the routing of each job through the machine environment. The scheduler is allowed to determine a route for each job and diﬀerent jobs may have diﬀerent routes.





































































