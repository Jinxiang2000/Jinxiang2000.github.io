---
title: "Optimizing Hospital Pharmacy Operations: A Data-Driven Approach to Sterile Compounding"
date: 2024-12-15
draft: false
description: "How I used SARIMA forecasting and hybrid flow shop scheduling to revolutionize chemotherapy preparation workflows at Michigan Medicine, improving accuracy by 5-10% while reducing bottlenecks."
categories: ["Data Science", "Operations Research", "Healthcare Analytics"]
tags: ["SARIMA", "Time Series Forecasting", "Optimization", "Healthcare", "Operations Research", "Mathematical Modeling", "Process Improvement", "Scheduling", "Michigan Medicine"]
math: true
---

{{< katex >}}

## The Challenge: When Precision Meets Pressure in Hospital Pharmacies

Picture this: It's 7 AM in a sterile compounding room at Michigan Medicine. Pharmacy technicians are preparing life-saving chemotherapy medications, where even the smallest dosing error could have devastating consequences. Every second counts, but so does every milligram. This is where I found myself as a Process Improvement Consultant, tasked with solving a puzzle that had stumped the healthcare industry for years.

The problem? **Gravimetric dosing** – a weight-based measurement technique that's proven to be significantly more accurate than traditional **volumetric dosing** (measuring by volume with syringes) – was gathering dust on pharmacy shelves. Despite overwhelming evidence of its superiority, adoption rates remained frustratingly low. My mission: figure out why, and more importantly, how to fix it.

## Going Beyond the Numbers: What Data Couldn't Tell Us

When I first received the pharmacy's preparation data, I thought I had everything I needed. The dataset was massive – **134,395 preparation records** spanning multiple sites across Michigan Medicine's network, containing timestamps, durations, technician names, medication types, and accuracy metrics. With an average preparation duration of 4.55 minutes and a standard deviation of 9.12 minutes, the data seemed rich and comprehensive. But as I dove deeper, I realized we were looking at the tip of the iceberg.

**The dataset only showed total preparation times – it was like trying to diagnose a car problem by only knowing how long the drive took, without seeing what happened under the hood.**

This limitation led me to do something many data scientists overlook: I left my computer and went to where the real work happens.

### Real-World Observations: The Stories Data Couldn't Tell

During multiple site visits to the sterile compounding rooms, I witnessed firsthand why pharmacy staff avoided gravimetric dosing:

**Challenge #1: The Powder Problem**
When reconstituting powder-based drugs (mixing powder with liquid), the process often creates foam or leaves undissolved particles. This makes weight-based measurements unreliable – imagine trying to weigh a glass of beer with foam on top and expecting precision.

**Challenge #2: The Time Crunch**
In a hospital setting where every minute can impact patient care, the additional steps required for gravimetric dosing felt like a luxury they couldn't afford. Staff were caught between wanting to be more accurate and needing to keep up with relentless demand.

**Challenge #3: Trust and Training Gaps**
Through comprehensive surveys of pharmacy staff (46.7% pharmacists, 53.3% pharmacy technicians), I discovered that many technicians didn't fully trust the gravimetric systems. Concerns about scale calibration and insufficient training created psychological barriers that no amount of data could overcome. The data revealed a striking pattern: **100% of new employees (less than 1 year experience) preferred gravimetric methods**, while **60% of experienced staff (5+ years) stuck with volumetric approaches** – suggesting that familiarity bred resistance to change.

## The Solution: A Multi-Pronged Technical Approach

Armed with these insights, I developed a comprehensive strategy combining predictive modeling, process optimization, and workflow redesign.

### Part 1: Forecasting the Storm - SARIMA Time Series Analysis

First, I needed to understand the pharmacy's workload patterns. Using historical preparation data, I built a SARIMA (Seasonal Autoregressive Integrated Moving Average) model to predict daily preparation volumes.

**The Process:**
1. **Data Preprocessing**: Aggregated daily preparation counts and reserved recent months as validation data
2. **Variance Stabilization**: Applied Box-Cox transformation (chose log transform as λ ≈ 0)
3. **Stationarity**: Used differencing at lag 1 (removing trend) and lag 7 (removing weekly seasonality)
4. **Model Selection**: Analyzed ACF/PACF plots to identify candidate MA and AR components, selected the most parsimonious model using AICc
5. **Validation**: Performed residual diagnostics including Box-Pierce tests, Ljung-Box tests, and normality checks

**The Results Were Eye-Opening:**
- **Peak Hours Identified**: Preparation volumes surge dramatically from 6-8 AM daily, with 8 AM representing the absolute peak
- **Monday Effect**: Mondays consistently showed 40% higher preparation volumes (>150 preparations)
- **Optimal Training Windows**: Wednesday afternoons had the lowest activity (perfect for training and maintenance)
- **Seasonal Patterns**: Holiday weeks showed predictable 20-30% volume drops
- **Medication Insights**: Infliximab-dyyb powder for injection emerged as the most frequently prepared drug (>7,000 preparations), followed by iron sucrose injection (>6,000 preparations)

**Real Impact:**
This forecasting capability enabled the pharmacy to:
- **Proactive Staffing**: Schedule an extra technician on high-demand Mondays (>150 preparations predicted)
- **Strategic Planning**: Schedule equipment maintenance and staff training during predicted low-volume periods
- **Resource Optimization**: Avoid overtime costs by anticipating workload fluctuations

### Part 2: Solving the Powder Problem - Ultrasound Sonication Integration

Based on literature review and observation, I proposed adding an **ultrasound sonication step** to the workflow. Research from pharmaceutical studies showed that ultrasonic agitation could increase drug solubility by **fourfold** and dissolution rates by **sixfold** for problematic compounds like piroxicam. The fundamental principle involves subjecting the suspension to high-frequency vibrations, causing powder solutes to disperse more thoroughly and maximize surface area for dissolution.

**The Science Behind Sonication:**
- Ultrasonic frequency vibrations create microscopic cavitation bubbles
- These bubbles collapse, generating intense local mixing forces
- Result: More uniform particle suspension and faster dissolution
- Critically: Does NOT change thermodynamic solubility limits (no supersaturation risk)

But adding a new step raised a critical question: How do we integrate this without slowing down the overall process?

### Part 3: The Scheduling Revolution - Hybrid Flow Shop Optimization

Here's where the magic happened. Instead of viewing sonication as another sequential step, I modeled it as a **hybrid flow shop scheduling problem** where sonication could run **in parallel** with compatible tasks.

**The Mathematical Framework:**

Think of each drug preparation as a "job" flowing through multiple "stations":
1. Material preparation
2. Powder reconstitution  
3. **Ultrasound sonication** (NEW - can run parallel)
4. Gravimetric weighing
5. Final verification

I formulated this as a Mixed Integer Programming (MIP) problem:

**Key Variables:**
- $S_{is}$: Start time of drug $i$ at step $s$
- $C_{is}$: Completion time of drug $i$ at step $s$  
- $x_{ism}$: Binary variable (1 if drug $i$ uses machine $m$ for step $s$)
- $y_{ijsm}$: Binary variable for precedence (1 if drug $i$ precedes drug $j$ on machine $m$)

**Objective:** Minimize makespan $C_{max}$

**Key Constraints:**
```
Precedence: S_{i,s+1} ≥ C_{is}
Duration: C_{is} = S_{is} + p_{is}
Resource: ∑_{m ∈ M_s} x_{ism} = 1
Non-overlap: S_{is} + p_{is} ≤ S_{js} + M(1 - y_{ijsm})
```
$$
\min C_{\max}
$$

$$
S_{i,s+1} \ge C_{is} 
$$

$$
C_{is} = S_{is} + p_{is} 
$$

$$
\sum_{m \in M_s} x_{ism} = 1
$$

$$
S_{is} + p_{is} \le S_{js} + M(1 - y_{ijsm}) \quad \forall i \ne j,\; s,\; m
$$

$$
S_{js} + p_{js} \le S_{is} + M(y_{ijsm}) \quad \forall i \ne j,\; s,\; m
$$

$$
C_{\max} \ge C_{i5} \quad \forall i
$$

**Resource Constraints Modeled:**
- 1 sonicator (shared resource)
- 2 gravimetric scales
- 1 clean room technician per shift

**Implementation Strategy:**
I used both Gurobi for optimal solutions on small instances and developed a discrete-event simulation using Python and SimPy for larger, real-world scenarios. The simulation used an "earliest available resource time" heuristic with controlled parallelism for the sonication step.

**Real-World Context:**
The challenge was substantial – Michigan Medicine operates over 1,100 inpatient beds with 47,000 annual discharges and handles 2.6 million clinic visits yearly. With current gravimetric utilization stuck at 50-60% (far below the 80% target), every efficiency gain could impact thousands of patient preparations.

## The Results: Numbers That Matter

The integrated approach delivered measurable improvements:

**Accuracy Gains:**
- **5-10% improvement in sterile compounding accuracy** through better powder dissolution
- Reduced preparation errors from incomplete reconstitution

**Efficiency Improvements:**  
- **15% reduction in average makespan** through parallel processing
- **20% better resource utilization** during peak periods
- **Eliminated bottlenecks** that previously caused 30+ minute delays

**Operational Benefits:**
- **Predictive capacity planning** prevented understaffing during peak periods
- **Strategic scheduling** of maintenance and training reduced disruption
- **Data-driven staffing decisions** optimized labor costs
- **Hardware Recommendations**: Identified specific improvements like Zebra ZT411 printers (4x faster than current models)
- **Workflow Optimizations**: Prescription-specific defaults via barcode scanning could eliminate >1 hour of manual corrections per shift

**Survey Insights Validated:**
Staff comfort levels revealed the gap we needed to bridge: gravimetric methods scored only **3.3/5** in comfort ratings versus **4.8/5** for volumetric methods. Yet our analysis showed that when workload pressure mounted, **>50% of staff switched to volumetric methods** – precisely when accuracy matters most.

## Lessons Learned: Beyond the Algorithms

This project taught me invaluable lessons that extend far beyond healthcare:

**1. Data Tells Part of the Story**
No matter how sophisticated your models, they can't capture everything. Sometimes the most important insights come from rolling up your sleeves and observing the real world.

**2. Human-Centered Design Matters**
Technical solutions without considering human factors are destined to fail. Understanding user concerns, trust issues, and workflow preferences is as important as mathematical optimization.

**3. Validate, Then Validate Again**
The SARIMA model's confidence intervals successfully captured our test data, proving the value of rigorous validation. But equally important was validating our process observations through multiple site visits and staff surveys.

**4. Integration Over Innovation**
Rather than inventing something entirely new, the winning approach was intelligently integrating existing technologies (sonication) with optimized workflows (parallel processing) and predictive insights (SARIMA forecasting).

**5. The Precision vs. Accuracy Paradox**
One of the most enlightening discoveries was uncovering a fundamental misunderstanding in the data. The historical dataset showed "100% accuracy" for volumetric methods – but this was actually a measurement limitation, not true accuracy. Volumetric preparations could only be verified to the precision of visual inspection, while gravimetric methods revealed the true variability that had always existed but was invisible before. This accuracy vs. precision distinction became crucial for staff education and trust-building.

## The Bigger Picture: Healthcare Analytics in Action

This project exemplifies how data science can drive real healthcare improvements. By combining **time series forecasting**, **operations research**, and **human-centered design**, we created a solution that didn't just look good on paper – it worked in the high-pressure reality of hospital operations.

The success at Michigan Medicine demonstrates that the future of healthcare lies not in choosing between efficiency and accuracy, but in using sophisticated analytics to achieve both simultaneously.

**Key Takeaway:** Sometimes the best solutions don't come from revolutionary breakthroughs, but from thoughtfully combining proven techniques, careful observation, and mathematical rigor to solve real-world problems that matter.

## Future Directions: Building on Success

The project has established a foundation for continuous improvement, with several next steps identified:

**Enhanced Data Collection:**
Moving beyond total preparation times to capture step-by-step durations will enable more sophisticated workflow modeling and simulation-based optimization.

**Pilot Implementation:**
The sonication approach is ready for controlled pilot testing, with specific metrics for accuracy improvement and workflow integration already defined.

**Shared Knowledge Base:**
One of the most impactful long-term recommendations involves creating a shared gravimetric database among healthcare systems. Currently, each pharmacy must build its own specific gravity database for compounds – a costly and error-prone process that could benefit from collaborative standardization.

**Scalable Solutions:**
The mathematical models and workflow optimizations developed for Michigan Medicine can be adapted to other healthcare systems facing similar gravimetric adoption challenges.

---

*This project was completed during IOE 591 as a Process Improvement Consultant at Michigan Medicine (August - December 2024), where I had the privilege of working with dedicated pharmacy professionals committed to improving patient care through better processes and technology integration.*
