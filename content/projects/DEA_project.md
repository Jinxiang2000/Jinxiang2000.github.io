---
title: "Optimizing Wind Power Plant Locations in Iran Using DEA"
date: 2025-06-20
description: "A comprehensive study using Data Envelopment Analysis (DEA) to determine the most efficient and socially beneficial cities in Iran for wind power plant development."
tags: ["DEA", "Optimization", "Renewable Energy", "Wind Power", "Iran", "Sustainability", "Energy Planning"]
math: true
---

{{< katex >}}

## Introduction

As the global energy landscape shifts towards sustainability, nations like Iran face both opportunities and challenges in deploying clean energy infrastructure. With its geographic diversity and significant untapped wind resources, Iran holds promise for large-scale wind energy deployment. However, selecting optimal sites for wind power plants requires more than just measuring wind speed—it demands a multi-dimensional evaluation.

In this study, we apply **Data Envelopment Analysis (DEA)** to assess 31 candidate cities across Iran. The goal: identify those locations that not only maximize wind energy output but also align with socio-economic and infrastructural objectives such as land cost, power grid access, disaster risk, quality of life, and unemployment rate.

## Data Overview

We analyzed the following variables across all Decision Making Units (DMUs), i.e., cities:

* **Wind Speed** (Output): Core indicator of energy generation potential
* **Land Cost** (Input): Capital investment factor
* **Transportation Cost** (Input): Operational and logistics cost
* **Distance to Power Grid** (Input / Non-discretionary): Infrastructure connectivity
* **Natural Disaster Risk** (Input): Environmental vulnerability
* **Quality of Life** (Output): Socioeconomic desirability
* **Unemployment Rate** (Output): Workforce potential

Each variable was analyzed using multiple DEA models to assess the impact from both a technical and governmental planning perspective.

## Methodology

We adopted a **two-phase DEA framework**, tailored to evaluate the wind farm siting problem from two different perspectives:

1. **Private Wind Farm Developer (Phase 1)**: Focused on energy yield and cost-efficiency.
2. **Public Policy Maker / Government (Phase 2)**: Focused on societal impact and return on public investment.

Each phase involved custom-defined input-output metrics and experimented with both traditional and extended DEA models (BCC, Super Efficiency, and Non-Discretionary).

### Phase 1: Wind Farm Developer Perspective

In this phase, we seek to identify the most technically efficient wind power sites for private developers. Two DEA model variants were implemented:

#### Option 1: Maximize Wind Speed

* **Inputs**:

  * Land Cost (K Rial/m²)
  * Distance to Power Distribution Network (miles)
* **Output**:

  * Average Wind Speed (m/s)

This model captures a baseline technical efficiency where high-wind-speed sites that are inexpensive and well-connected are favored.

#### Option 2: Maximize Adjusted Revenue

Incorporates a more realistic revenue-based efficiency score:

**Adjusted Revenue Formula:**

$$
\text{Adjusted Revenue}_{DMU} = \text{Expected Wind Power} \times \text{Electricity Price} - \text{Land Cost} \times \text{Wind Farm Area} - \text{Power Distribution Cost}
$$

Where:

* **Expected Wind Power** is calculated using:

$$
P = \frac{1}{2} \rho A v^3
$$

with $\rho = 1.224\ kg/m^3$, $A = 452.16\ m^2$, and $v$ being average wind speed for each DMU.

* **Operational Hours**: 8760 hours/year
* **Electricity Price**: 5000 Rial/KWh
* **Area Requirement**: 21 acres = 84,984 m²
* **Power Distribution Cost**: $\text{Distance} \times C$, where $C \in \{1, 2, 2.5\} \times 10^7$ Rial/mile/year (sensitivity tested)

By substituting these into the adjusted revenue metric, we can rank cities by projected profitability rather than just raw wind speed.

### Phase 2: Government Perspective

From the public sector standpoint, we aim to identify cities where minimal government intervention (in the form of subsidies or loans) yields maximal societal benefit.

* **Inputs**:

  * Government Subsidy (unit normalized cost proxy)
  * Tax Incentives
  * Loans or Grants

* **Outputs**:

  * Safety Index (proxy for disaster risk mitigation)
  * Unemployment Rate (negative value to reflect improvement)
  * Quality of Life Index

We employ BCC Input-Oriented and Super-Efficiency models to differentiate among top-tier locations. This step is crucial for national infrastructure planning, where maximizing social ROI is essential.

## Python & Gurobi Implementation

To operationalize our DEA framework, I built a custom Python class using **Gurobi** for linear programming. Below is a simplified version of our CCR input-oriented model implementation:

```python
import gurobipy

class DEA:
    def __init__(self, DMUs_Name, X, Y):
        self.m1, self.m2 = X.shape[1], Y.shape[1]
        self.X = {DMU: X.loc[DMU].tolist() for DMU in DMUs_Name}
        self.Y = {DMU: Y.loc[DMU].tolist() for DMU in DMUs_Name}
        self.DMUs = DMUs_Name

    def CCR_Input(self):
        results = {}
        for k in self.DMUs:
            model = gurobipy.Model()
            theta = model.addVar(name="theta")
            lambdas = model.addVars(self.DMUs, name="lambda")
            model.setObjective(theta, gurobipy.GRB.MINIMIZE)

            for j in range(self.m1):
                model.addConstr(
                    gurobipy.quicksum(lambdas[i] * self.X[i][j] for i in self.DMUs) <= theta * self.X[k][j]
                )
            for r in range(self.m2):
                model.addConstr(
                    gurobipy.quicksum(lambdas[i] * self.Y[i][r] for i in self.DMUs) >= self.Y[k][r]
                )
            model.setParam('OutputFlag', 0)
            model.optimize()
            results[k] = model.objVal
        return results
```

This implementation allowed us to run all variants (CCR/BCC, input/output orientation) efficiently, while also supporting extensions such as super-efficiency models.

## Key Findings

### Consistent High Performers (All Models)

* **Manjil (DMU 17)**: High wind speed, strong adjusted revenue, robust social metrics
* **Ardestan (DMU 4)**: Technically viable with grid proximity, performs well under constraints
* **Shahrbabak (DMU 21)** and **Zarrineh (DMU 31)**: Strong output efficiency

### Super-Efficient Cities

* **Baft**, **Khodabandeh**, **Malayer**, **Tabriz**, and **Tehran**: Exceeded DEA efficiency frontier in multiple models

### Policy-Aligned Sites

Cities such as **Tehran**, **Kermanshah**, **Karaj**, and **Yazd** scored well in government-oriented models, suggesting high return on public investment.

## Final Recommendation

Based on cross-model analysis, we recommend **Manjil (DMU 17)** as the optimal site for wind power deployment. It balances:

* **Technical strength**: High wind output, low land/grid costs
* **Economic feasibility**: Favorable adjusted revenue
* **Social impact**: Employment generation, community readiness, and environmental resilience

Establishing a wind power plant in Manjil not only ensures optimal energy generation but also supports national sustainability, social inclusion, and economic diversification goals.

## Conclusion

This DEA-based framework demonstrates a replicable, data-driven method for evaluating renewable energy investments. By incorporating both output maximization and social impact, we help decision-makers ensure infrastructure choices are efficient, equitable, and sustainable.

---

“This project was part of the IOE 551 Final Project at the University of Michigan.”
