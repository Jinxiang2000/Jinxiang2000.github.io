---
title: "Six Sigma Green Belt - Part 2"
date: 2025-08-27
draft: false
description: "Six Sigma Green Belt Study Note"
tags: ["six sigma","lean"]
categories: ["learning", "six sigma"]
math: true

---

{{< katex >}}

## DMAIC Framework

### Define

“Define”: What is the real problem we are facing? How can it be described?

### Measure

Measure is to better understand the problem and collect data.  Sometimes the data is readily available, but at other times it may have to be collected through observation, accessed by retrieving data stored in a database, or by distributing questionnaires and surveys

### Analyze

Analyzed  the data to better understand the problem, further reveal its root cause and point to a solution.  

More advance statistical method are also used, such as confidence interval estimation, hypothesis  testing and control charts.

### Improve

Action is taken to improve the process. 

Depending on the nature of the problem, this can be as simple as eliminating  steps, or purchasing new equipment, or as complex as redesigning almost every step in the process. 

> The real challenge here is “resistance to change”, where those involved in the old process resist the change that have become routine and familiar



### Control

Establishing monitoring and control procedures to ensure that process outputs conform to expected performance levels. The project closes with establishing and implementing these controls.

### Gage R&R Study

A Gage R&R study focuses on whether an individual collecting the data or performing the test is consistent  across many measurements, and whether the variation between different people involved in the data collection is consistent. 

* Repeatability 
* Reproducibility

There are two types of Gage R&R studies: Crossed and Nested 

#### Crossed Gage R&R study

In Crossed Gage R&R study, parts are sampled from a process, and several operators are selected to participate  in the measurement study. Each operator measures each part.  

The problem with this approach is cause carryover effect. It occurs when the experience gained by operator one when measuring part one carryover and influence the operator’s measurement of part two this can bias the results.

#### Nested Gage R&R study

The strategy is particularly relevant when testing past destroyed it. For example, when testing a shipment of air packs before they are assembled into an auto. Testing the airbag destroys it. Using this strategy, there's no carryover effects since each operator only measures one part.

The disadvantage is that the strategy may require many more operators. For example 10 parts would be tested, then additional operator would be needed

#### Conducting the Gage R&R Study

When conducting a gauge R&R study, the following steps are taken.

1. A fixed number of parts, objects, or individuals are identified
2. A fixed number of operators or observers are identified
3. Each operator of observers initiate measurements of the objects a fixed number of times

When studies completed, the results are analyze, using computer based software that identify the measurement problems’source.

#### Precision and Accuracy

Measure systems must be both accurate emphasize is important to distinguish between these two words

**Persistent** refers to the closeness of two or more measurements to each other if they are very close, then it might be the thought that the data are truly representative of the process of parts being measured yet this measurement by itself may not be sufficient.

**Accuracy** is the proximity of measurements results to the true value. It means that the data are indeed true representations of what it is we are trying to measure.

Bias is the difference between an observed measurement, and a reference value or standard. Bias is not limited to those measurements taken with an instrument. Human Bias is even more common.

## Improving Process Flow

There are several tools, considered part of the six sigma toolkit, that help define design and improve process flow

* SIPOC Diagram
* Value Stream Map
* Process Map

### SIPOC

![SIPOC](/img/learning/image-20250828141546134.png)

There are 5 component for SIPOC

* Suppliers
* Inputs
* Process
* Outputs
* Customers

By engaging  a broad range of stakeholders, the SIPOC will represent how different individual  involved  in the Six Sigma Project

![image-20250828142627117](/img/learning/image-20250828142627117.png)

### Productive Time and Elasped Time

A major goal of lean is to eliminate process steps that a little or no customer value. A systematic method for evaluating the  necessary steps is to create a value stream map which identified all steps are certainly major steps in the process. This represents the measurement steps associated with DMAIC. This steps are analyze to determined where in the process delay occurred. Once this is determined steps are taken if appropriate to implement a solution to the problem by eliminating this delays. Finally, controls are established to ensure that delays to not be emerge to interfere with the efficiency of the process. Analysis phase is certainly critical in this process because it is here that the steps that at no values are identified. The goal is such that when these steps are eliminated only value added steps will remain.

### What is meant by value added?

In lean, value-added is specifically linked to the value the customer received from the product or service. Value is defined from a customer, not organization’s perspective

### Value Stream Mapping

The purpose of Batre mapping is to document this process, identify where they need improvement and then improve them. It can be used to establish abroad view of a process and to identify ways and delay between the process steps.

![image-20250828143721645](/img/learning/image-20250828143721645.png)

### Process Mapping

In contrast, a process map takes a more detailed views of the steps in the process. It begins with the outputs associated with the process and the process input. Then it further breaks the process down into inputs and outputs associated with each step.

The level of detail associated with this process map will depends upon the problems or opportunity that must be addressed as well as objected of the project.

![image-20250828165513384](/img/learning/image-20250828165513384.png)

Decision in Process Maps:

![image-20250828165543426](/img/learning/image-20250828165543426.png)

Cross Departmental Process Map:

![image-20250828165719902](/img/learning/image-20250828165719902.png)

Process Map with Expected Activity Times: 

![image-20250828165801101](/img/learning/image-20250828165801101.png)

To create an effective process map, it is recommended that the process mapping session will be held with those who will be involved in the project.

Here are the steps that should be followed:

* Defined a problem
* Identify the start and end point
* Determine the level of detail required
* Engage in the walk-through to ensure an accurate view of the of  process
* Draw the map
* Undertake additional draft if necessary

Besides process map, another useful map use in analyzing process flow is a **spaghetti diagram**.

![image-20250828170209300](/img/learning/image-20250828170209300.png)

Once this is done, the team can analyze the diagrams to eliminate unnecessary movement through the system.
