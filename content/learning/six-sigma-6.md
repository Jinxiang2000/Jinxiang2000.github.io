---
title: "Six Sigma Green Belt - Part 6"
date: 2025-08-27
draft: false
description: "Six Sigma Green Belt Study Note"
tags: ["six sigma","lean"]
categories: ["learning", "six sigma"]
math: true



---

### Sampling and Process Control

A population includes all members of defined group that is being studied.  A sample is a part or a subset of the population. $N$ represents the size of the sample. In most circumstances, the output of a process(the population) is monitored by taking a sample.



### Subgroups and Rational Subgroups

When samples are taken, they create what we can also call a subgroup of the population. 

> For example,  an assumbly process produce 100 units per hour. To monitor quality, five randomly selected units are taken at the beginning of every hour and subjected to several tests. Each sample of five part is called a subgroup. Generally, a subgroup contains data from a similar setting, such as a shift, lot, or operator.

A rational subgroup is a sample group generated under the same set of conditions. To confirm a subgroup is a rational subgroup, the difference in the data within a subgroup should be less than the difference between subgroups. 

> For example, the city and highway subgroup would be considered rational subgroup as long as the difference within the city and highway subgroups are less than the difference between the city and highway groups.

In Lean Six sigma, every effort must be made to seperate process data into rational subgroups. Otherwise, when we analyze and monitor these processes, the failure to create rational subgroups could mask important information. 



## Sampling from the Population

When samples of size n are taken from a population with mean $\mu$, every sample mean, $\bar{X}$, will vary. 

The mean of the distribution of Sample Mean will coincide with the population means. These sample means will cluster around the mean of the populatio, $\mu$. Few sample means will fall far from this mean. This is expressed in the distribution shown to the right.  

Why? Because a sample is unlikely to include responses from customers who all expressed satisfaction or who all expressed dissatisfaction. Indeed, sample means at the extremes would be vary rare. So, the sample will likely include some with positive , some with average, and others with negative experience at the concert. 

> **Central Limit Theorem**
>
> The CLT informs us that when sample size are large, the distribution  of sample mean takes the shape of a normal distribution 



The variation in this distribution of sample means can be expressed as the Standard Error of the Mean(SE). It can be calculated as:
$$
SE = \frac{\sigma}{\sqrt{n}}
$$
We need to keep in mind that: The SE measures the variation in a Population distribution, but the SE of mean measures the variation in the distribution of Sample Means.



In a Six Sigma quality control environment, and when a process is in control, a larger size sample is more likely to fall close to the process mean than would occur if a smaller sample had been taken.



### Probability and Convenience Sampling

Sampling can be catagorized into two basic ways:

**Probability Sampling** follow satisitical principles to ensure that we have a representative and unbiased sample. e.g. when a new product is market tested,  these chosen to test the product will represent an unbiased sample of the population of all potential customers.



**Convenience Sampling** take shortcuts and ask friends. e.g. ask the first 20 people who walk into a store, or colleagues. Convenience is often based on the acessibility. We don’t go out of our way! So if we take this approach to sampling, the sample will likely be **biased**.





### Types of Probability Sampling

* **Simple Random Sampling** - Every item in the population has an equal likely chance of being selected for inclusion in the sample.
* **Stratified Random Sampling** - The population is divided into homogeneous subgroups called **strata**. This is a common practice in political polling  where likely voters are divided into serveral strata, including geographic region and racial/ethnic groups.
* **Systematic Sampling** - every $n$th unit is sampled. For example, when taking a survey of customers who recently purchased an automoblie, every tenth customer might be asked to complete a questionnaire that would be used to determine their satisfaction with the dealership.
* **Cluster Sampling** a group of items are sampled at periodic intervals. e.g ice cream manufacturer might sample the first three pints of ice cream at the beginning of every hour to ensure that they meet quality standards.





















