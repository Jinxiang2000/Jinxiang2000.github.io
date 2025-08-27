---
title: "Statistical Testing & Hypothesis Testing"
date: 2025-08-20
draft: false
description: "A comprehensive guide to statistical hypothesis testing, p-values, and practical considerations for data science"
tags: ["statistics", "data-science", "hypothesis-testing", "p-values"]
categories: ["learning", "data-science"]
series: "Statistical Foundations"
---

## Introduction

Statistical hypothesis testing is a fundamental tool for making data-driven decisions. It provides a framework for determining whether observed patterns in data are statistically significant or could have occurred by chance.

## Core Concepts

### 1. Null and Alternative Hypotheses

- **Null Hypothesis (H₀)**: The default assumption (no effect, no difference)
- **Alternative Hypothesis (H₁)**: What we're trying to prove

**Example:**
- H₀: The new website design has no effect on conversion rate
- H₁: The new website design increases conversion rate

### 2. Type I and Type II Errors

| Decision | H₀ True | H₀ False |
|----------|---------|----------|
| Reject H₀ | Type I Error (α) | Correct |
| Fail to Reject H₀ | Correct | Type II Error (β) |

- **Type I Error (α)**: False positive - rejecting true null hypothesis
- **Type II Error (β)**: False negative - failing to reject false null hypothesis
- **Power (1-β)**: Probability of correctly rejecting false null hypothesis

### 3. P-Values

The p-value is the probability of observing test results at least as extreme as the observed results, assuming the null hypothesis is true.

**Interpretation:**
- p < 0.05: Strong evidence against H₀
- p < 0.01: Very strong evidence against H₀
- p ≥ 0.05: Insufficient evidence to reject H₀

## Common Statistical Tests

### 1. One-Sample T-Test

Tests whether a sample mean differs significantly from a population mean.

```python
import numpy as np
from scipy import stats

def one_sample_ttest(sample, population_mean, alpha=0.05):
    """
    Perform one-sample t-test
    """
    t_stat, p_value = stats.ttest_1samp(sample, population_mean)
    
    critical_value = stats.t.ppf(1 - alpha/2, len(sample) - 1)
    
    result = {
        't_statistic': t_stat,
        'p_value': p_value,
        'critical_value': critical_value,
        'reject_null': p_value < alpha,
        'sample_mean': np.mean(sample),
        'sample_std': np.std(sample, ddof=1)
    }
    
    return result

# Example usage
sample_data = np.random.normal(105, 15, 30)  # Sample of 30 IQ scores
result = one_sample_ttest(sample_data, 100)
print(f"T-statistic: {result['t_statistic']:.4f}")
print(f"P-value: {result['p_value']:.4f}")
print(f"Reject null hypothesis: {result['reject_null']}")
```

### 2. Two-Sample T-Test

Compares means between two independent groups.

```python
def two_sample_ttest(group1, group2, equal_var=True, alpha=0.05):
    """
    Perform two-sample t-test
    """
    t_stat, p_value = stats.ttest_ind(group1, group2, equal_var=equal_var)
    
    # Degrees of freedom
    if equal_var:
        df = len(group1) + len(group2) - 2
    else:
        # Welch's t-test
        s1, s2 = np.var(group1, ddof=1), np.var(group2, ddof=1)
        n1, n2 = len(group1), len(group2)
        df = (s1/n1 + s2/n2)**2 / ((s1/n1)**2/(n1-1) + (s2/n2)**2/(n2-1))
    
    critical_value = stats.t.ppf(1 - alpha/2, df)
    
    # Effect size (Cohen's d)
    pooled_std = np.sqrt(((len(group1)-1)*np.var(group1, ddof=1) + 
                         (len(group2)-1)*np.var(group2, ddof=1)) / 
                        (len(group1) + len(group2) - 2))
    cohens_d = (np.mean(group1) - np.mean(group2)) / pooled_std
    
    result = {
        't_statistic': t_stat,
        'p_value': p_value,
        'degrees_of_freedom': df,
        'critical_value': critical_value,
        'reject_null': p_value < alpha,
        'cohens_d': cohens_d,
        'group1_mean': np.mean(group1),
        'group2_mean': np.mean(group2)
    }
    
    return result
```

### 3. Chi-Square Test

Tests for independence between categorical variables.

```python
def chi_square_test(observed, alpha=0.05):
    """
    Perform chi-square test of independence
    """
    chi2_stat, p_value, dof, expected = stats.chi2_contingency(observed)
    
    critical_value = stats.chi2.ppf(1 - alpha, dof)
    
    # Cramér's V (effect size)
    n = np.sum(observed)
    cramers_v = np.sqrt(chi2_stat / (n * (min(observed.shape) - 1)))
    
    result = {
        'chi2_statistic': chi2_stat,
        'p_value': p_value,
        'degrees_of_freedom': dof,
        'critical_value': critical_value,
        'reject_null': p_value < alpha,
        'cramers_v': cramers_v,
        'expected_frequencies': expected
    }
    
    return result

# Example: Testing if gender is independent of product preference
observed = np.array([[10, 20, 30],   # Male preferences
                     [20, 25, 15]])  # Female preferences

result = chi_square_test(observed)
print(f"Chi-square statistic: {result['chi2_statistic']:.4f}")
print(f"P-value: {result['p_value']:.4f}")
```

### 4. ANOVA (Analysis of Variance)

Compares means across multiple groups.

```python
def one_way_anova(*groups, alpha=0.05):
    """
    Perform one-way ANOVA
    """
    f_stat, p_value = stats.f_oneway(*groups)
    
    # Degrees of freedom
    k = len(groups)  # Number of groups
    n = sum(len(group) for group in groups)  # Total sample size
    df_between = k - 1
    df_within = n - k
    
    critical_value = stats.f.ppf(1 - alpha, df_between, df_within)
    
    # Effect size (eta-squared)
    group_means = [np.mean(group) for group in groups]
    grand_mean = np.mean(np.concatenate(groups))
    
    ss_between = sum(len(groups[i]) * (group_means[i] - grand_mean)**2 
                     for i in range(k))
    ss_total = sum((x - grand_mean)**2 for group in groups for x in group)
    
    eta_squared = ss_between / ss_total if ss_total > 0 else 0
    
    result = {
        'f_statistic': f_stat,
        'p_value': p_value,
        'df_between': df_between,
        'df_within': df_within,
        'critical_value': critical_value,
        'reject_null': p_value < alpha,
        'eta_squared': eta_squared,
        'group_means': group_means
    }
    
    return result
```

## A/B Testing Framework

```python
class ABTest:
    def __init__(self, alpha=0.05, power=0.8):
        self.alpha = alpha
        self.power = power
    
    def sample_size_calculation(self, effect_size, baseline_rate=None):
        """
        Calculate required sample size for A/B test
        """
        if baseline_rate is None:
            # For continuous metrics (Cohen's d)
            z_alpha = stats.norm.ppf(1 - self.alpha/2)
            z_beta = stats.norm.ppf(self.power)
            n = 2 * ((z_alpha + z_beta) / effect_size)**2
        else:
            # For conversion rates
            p1 = baseline_rate
            p2 = baseline_rate + effect_size
            p_pooled = (p1 + p2) / 2
            
            z_alpha = stats.norm.ppf(1 - self.alpha/2)
            z_beta = stats.norm.ppf(self.power)
            
            n = (z_alpha * np.sqrt(2 * p_pooled * (1 - p_pooled)) + 
                 z_beta * np.sqrt(p1 * (1 - p1) + p2 * (1 - p2)))**2 / (p2 - p1)**2
        
        return int(np.ceil(n))
    
    def analyze_conversion_test(self, control_conversions, control_visitors,
                               treatment_conversions, treatment_visitors):
        """
        Analyze A/B test for conversion rates
        """
        # Conversion rates
        control_rate = control_conversions / control_visitors
        treatment_rate = treatment_conversions / treatment_visitors
        
        # Pooled probability
        total_conversions = control_conversions + treatment_conversions
        total_visitors = control_visitors + treatment_visitors
        pooled_prob = total_conversions / total_visitors
        
        # Standard error
        se = np.sqrt(pooled_prob * (1 - pooled_prob) * 
                    (1/control_visitors + 1/treatment_visitors))
        
        # Z-statistic
        z_stat = (treatment_rate - control_rate) / se
        p_value = 2 * (1 - stats.norm.cdf(abs(z_stat)))
        
        # Confidence interval for difference
        diff = treatment_rate - control_rate
        se_diff = np.sqrt(control_rate * (1 - control_rate) / control_visitors +
                         treatment_rate * (1 - treatment_rate) / treatment_visitors)
        
        z_critical = stats.norm.ppf(1 - self.alpha/2)
        ci_lower = diff - z_critical * se_diff
        ci_upper = diff + z_critical * se_diff
        
        # Relative lift
        relative_lift = (treatment_rate - control_rate) / control_rate * 100
        
        return {
            'control_rate': control_rate,
            'treatment_rate': treatment_rate,
            'difference': diff,
            'relative_lift_percent': relative_lift,
            'z_statistic': z_stat,
            'p_value': p_value,
            'statistically_significant': p_value < self.alpha,
            'confidence_interval': (ci_lower, ci_upper)
        }
```

## Multiple Testing Correction

When performing multiple tests, the probability of Type I error increases. Common corrections include:

### 1. Bonferroni Correction
```python
def bonferroni_correction(p_values, alpha=0.05):
    """
    Apply Bonferroni correction for multiple testing
    """
    adjusted_alpha = alpha / len(p_values)
    corrected_significant = [p < adjusted_alpha for p in p_values]
    return adjusted_alpha, corrected_significant
```

### 2. False Discovery Rate (FDR) - Benjamini-Hochberg
```python
def benjamini_hochberg_correction(p_values, alpha=0.05):
    """
    Apply Benjamini-Hochberg FDR correction
    """
    p_values = np.array(p_values)
    sorted_indices = np.argsort(p_values)
    sorted_p_values = p_values[sorted_indices]
    
    m = len(p_values)
    significant = np.zeros(m, dtype=bool)
    
    for i in range(m-1, -1, -1):
        if sorted_p_values[i] <= (i + 1) / m * alpha:
            significant[sorted_indices[:i+1]] = True
            break
    
    return significant
```

## Practical Considerations

### 1. Power Analysis
```python
def power_analysis(effect_size, sample_size, alpha=0.05):
    """
    Calculate statistical power given effect size and sample size
    """
    z_alpha = stats.norm.ppf(1 - alpha/2)
    z_beta = z_alpha - effect_size * np.sqrt(sample_size/2)
    power = 1 - stats.norm.cdf(z_beta)
    return power
```

### 2. Effect Size Guidelines (Cohen's Conventions)

| Effect Size | Cohen's d | Correlation (r) | Eta-squared (η²) |
|-------------|-----------|----------------|------------------|
| Small | 0.2 | 0.1 | 0.01 |
| Medium | 0.5 | 0.3 | 0.06 |
| Large | 0.8 | 0.5 | 0.14 |

### 3. Common Pitfalls

1. **P-hacking**: Manipulating analysis to achieve significance
2. **Multiple Comparisons**: Not correcting for multiple tests
3. **Small Effect Sizes**: Statistical significance ≠ practical significance
4. **Assumption Violations**: Not checking test assumptions
5. **Sample Size**: Insufficient power to detect meaningful effects

## Best Practices

1. **Plan analyses before collecting data**
2. **Check assumptions** (normality, independence, equal variance)
3. **Consider effect sizes** alongside p-values
4. **Use appropriate corrections** for multiple testing
5. **Report confidence intervals** not just p-values
6. **Validate with replication** when possible

## Code Example: Complete A/B Test Analysis

```python
def complete_ab_test_analysis(control_data, treatment_data, alpha=0.05):
    """
    Complete A/B test analysis with diagnostics
    """
    # Basic statistics
    control_mean = np.mean(control_data)
    treatment_mean = np.mean(treatment_data)
    
    # Check assumptions
    # 1. Normality (Shapiro-Wilk test)
    _, control_normal_p = stats.shapiro(control_data[:5000])  # Max 5000 samples
    _, treatment_normal_p = stats.shapiro(treatment_data[:5000])
    
    # 2. Equal variances (Levene's test)
    _, equal_var_p = stats.levene(control_data, treatment_data)
    
    # Choose appropriate test
    if control_normal_p > 0.05 and treatment_normal_p > 0.05:
        # Both normal - use t-test
        if equal_var_p > 0.05:
            t_stat, p_value = stats.ttest_ind(treatment_data, control_data, equal_var=True)
            test_used = "Independent t-test (equal variances)"
        else:
            t_stat, p_value = stats.ttest_ind(treatment_data, control_data, equal_var=False)
            test_used = "Welch's t-test (unequal variances)"
    else:
        # Non-normal - use Mann-Whitney U test
        u_stat, p_value = stats.mannwhitneyu(treatment_data, control_data, alternative='two-sided')
        test_used = "Mann-Whitney U test (non-parametric)"
    
    # Effect size (Cohen's d)
    pooled_std = np.sqrt(((len(control_data)-1)*np.var(control_data, ddof=1) + 
                         (len(treatment_data)-1)*np.var(treatment_data, ddof=1)) / 
                        (len(control_data) + len(treatment_data) - 2))
    cohens_d = (treatment_mean - control_mean) / pooled_std
    
    # Confidence interval for mean difference
    se_diff = np.sqrt(np.var(control_data, ddof=1)/len(control_data) + 
                     np.var(treatment_data, ddof=1)/len(treatment_data))
    
    df = len(control_data) + len(treatment_data) - 2
    t_critical = stats.t.ppf(1 - alpha/2, df)
    
    mean_diff = treatment_mean - control_mean
    ci_lower = mean_diff - t_critical * se_diff
    ci_upper = mean_diff + t_critical * se_diff
    
    return {
        'test_used': test_used,
        'control_mean': control_mean,
        'treatment_mean': treatment_mean,
        'mean_difference': mean_diff,
        'p_value': p_value,
        'statistically_significant': p_value < alpha,
        'cohens_d': cohens_d,
        'confidence_interval_95': (ci_lower, ci_upper),
        'assumptions': {
            'control_normal': control_normal_p > 0.05,
            'treatment_normal': treatment_normal_p > 0.05,
            'equal_variances': equal_var_p > 0.05
        }
    }
```

## Further Reading

- [Statistical Power Analysis for the Behavioral Sciences](https://www.amazon.com/Statistical-Power-Analysis-Behavioral-Sciences/dp/0805802835) by Jacob Cohen
- [The Art of Statistics](https://www.amazon.com/Art-Statistics-How-Learn-Data/dp/1541618513) by David Spiegelhalter
- [Trustworthy Online Controlled Experiments](https://www.amazon.com/Trustworthy-Online-Controlled-Experiments-Practical/dp/1108724264) by Kohavi, Tang & Xu

---

**Last Updated**: January 2024  
**Next Topics**: Bayesian hypothesis testing, Sequential testing
