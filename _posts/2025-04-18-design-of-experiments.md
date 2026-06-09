---
layout: post
title: Designing Bioassays to Detect Dose-Dependent Drug Toxicity 🧪
date: 2025-04-28 14:24:00-0400
description: This experimental design was part of a larger bioassay to evaluate the toxic effects of a drug administered intravitreally at varying dosages.
tags: DesignOfExperiments Statistics
categories: my-posts
giscus_comments: true
related_posts: true
pretty_table: true
tabs: true

thumbnail: assets/img/bioassay.png
chart:
  plotly: true
authors:
  - name: Emmanuel A. Atindama
    url: "https://sites.google.com/view/emmanuelatindama-exploremath"
    affiliations:
      name: Department of Statistics & Mathematics, University of Toledo

bibliography: 2018-12-22-distill.bib
---

<p class="post-lead">A strong toxicity study design should make dose-response conclusions clear, reproducible, and statistically defensible.</p>

This experimental design was part of a preclinical bioassay, but it can be adapted to clinical settings.  
The goal was to determine whether drug "X" could be administered at higher doses without causing toxic side effects.  
Drug "X" was delivered through intravitreal injection (injection into the eye), and administration occurred once every **4 weeks**.

---
#### 🎯 Objective

To determine whether drug "X" exhibits **dose-dependent toxicity** in a controlled population of experimental units (e.g., animal models).

---

#### 📊 Measured Response Variables

| Quantity                                     | Description                                                                                      |
| :------------------------------------------- | :----------------------------------------------------------------------------------------------- |
| Length of Rod Outer Segments $$(\mu m)$$     | A measure of retinal detachment                                                                  |
| Retinal Thickness $$(\mu m)$$                | A measure of photoreceptor function                                                              |
| "Y" levels in vitreous humor $$(pg/ml)$$     | An inflammation biomarker—**primary toxicity measure**                                           |
| Mortality rate                               | Ratio of deaths to initial sample size                                                           |
| Weight change                                | Body mass gained or lost after treatment                                                         |

<p></p>

Although several response variables were measured, this blog focuses on <b>toxicity</b> levels—quantified via “Y” concentration in vitreous humor, measured in picograms per milliliter (pg/ml).

<p></p>

##### ℹ️ What is pg/ml?

**pg/ml** stands for *picograms per milliliter*. A picogram is one-trillionth of a gram, and a milliliter is one-thousandth of a liter.  
This unit reflects the **concentration** of "Y" in the fluid sample.

---

#### 🧪 Methodology

- **Blocking:** Subjects were divided into 3 age groups: $$\alpha_1$$, $$\alpha_2$$, and $$\alpha_3$$.
- **Randomization:** Subjects were randomly assigned to each treatment group.
- **Blinding:** Double-blinded protocol for both treatment and response measurement.
- **Treatment Frequency:** Once every 4 weeks.
- **Monitoring:**
  - Daily physical and behavioral checks
  - Weekly body weight
  - Periodic anatomical measurements (retina & rod length)
  - "Y" levels measured at irregular but predefined intervals: 3h, 9h, 24h, 48h, etc.
- **Replication:** Each treatment was independently replicated on multiple units.
We also perform replication by independently assigning the treatments to several experimental units to ensure reproducibility of the results.
---

#### 🧮 Summary Statistics for "Y" Levels (Toxicity Marker)

<table>
  <thead>
    <tr>
      <th>Treatment Dose</th>
      <th style="text-align:right;">Mean Y Level</th>
      <th style="text-align:right;">Std Dev</th>
      <th style="text-align:center;">N</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>$$\tau_0$$ (Control)</td>
      <td style="text-align:right;">7.2</td>
      <td style="text-align:right;">1.1</td>
      <td style="text-align:center;">12</td>
    </tr>
    <tr>
      <td>$$\tau_1$$</td>
      <td style="text-align:right;">7.9</td>
      <td style="text-align:right;">1.3</td>
      <td style="text-align:center;">12</td>
    </tr>
    <tr>
      <td>$$\tau_2$$</td>
      <td style="text-align:right;">9.8</td>
      <td style="text-align:right;">1.5</td>
      <td style="text-align:center;">12</td>
    </tr>
    <tr>
      <td>$$\tau_3$$</td>
      <td style="text-align:right;">12.5</td>
      <td style="text-align:right;">2.1</td>
      <td style="text-align:center;">12</td>
    </tr>
    <tr>
      <td>$$\tau_4$$</td>
      <td style="text-align:right;">15.7</td>
      <td style="text-align:right;">2.5</td>
      <td style="text-align:center;">12</td>
    </tr>
  </tbody>
</table>

---

#### 🎞️ Dose-Response Animation (Illustrative)

```plotly
{
  "data": [
    {
      "x": [0, 1, 2, 3, 4],
      "y": [7.2, 7.9, 9.8, 12.5, 15.7],
      "mode": "lines+markers",
      "name": "Mean Y",
      "line": {"color": "#d35400", "width": 3}
    }
  ],
  "layout": {
    "title": "Illustrative increase in toxicity marker Y with dose",
    "xaxis": {"title": "Dose group"},
    "yaxis": {"title": "Y concentration (pg/ml)"}
  },
  "frames": [
    {"name": "f1", "data": [{"x": [0, 1], "y": [7.2, 7.9]}]},
    {"name": "f2", "data": [{"x": [0, 1, 2], "y": [7.2, 7.9, 9.8]}]},
    {"name": "f3", "data": [{"x": [0, 1, 2, 3], "y": [7.2, 7.9, 9.8, 12.5]}]},
    {"name": "f4", "data": [{"x": [0, 1, 2, 3, 4], "y": [7.2, 7.9, 9.8, 12.5, 15.7]}]}
  ],
  "updatemenus": [
    {
      "type": "buttons",
      "buttons": [
        {
          "label": "Play",
          "method": "animate",
          "args": [null, {"frame": {"duration": 550, "redraw": true}, "fromcurrent": true}]
        }
      ]
    }
  ]
}
```

---

#### 📈 Statistical Analysis & Hypothesis Testing

We are analyzing **"Y" levels**, a continuous variable, across multiple treatments and blocking for age.  
Thus, we use a two-way Analysis of Variance (**two-way ANOVA** Model). If weight change is suspected to affect toxicity, we could also use a **two-way ANCOVA**.

> If mortality is observed, a **Chi-square test** for homogeneity may be used to compare mortality rates across treatment groups.

Using a two-way ANOVA, we define our hypothesis as follows:

**Hypotheses:**

- <b>Null (H₀):</b> Drug "X" has no toxic effect at any dose, after controlling for the age of the experimental units. 
- <b>Alternative (H₁):</b> Drug "X" exhibits toxicity at one or more of the tested dose levels, even after accounting for age.

Rejecting the null hypothesis in favor of the alternate hypothesis means that there is an age-independent difference in the effect across different 
treatment levels. This indicates the presence of dose-dependent toxicity.
Failing to reject the null hypothesis means that there is no difference in the toxicity level.

---

#### 📋 Experimental Design Table

|            |              |                                 Treatments                                      |
| ---------- | :----------- | :-------------------------: | :--------: | :--------: | :--------: | :--------: |
|            |              | $$\tau_0 \text{(Control)}$$ | $$\tau_1$$ | $$\tau_2$$ | $$\tau_3$$ | $$\tau_4$$ |
|            | $$\alpha_1$$ |              4              |      4     |      4     |      4     |      4     |
| Age Groups | $$\alpha_2$$ |              4              |      4     |      4     |      4     |      4     |
|            | $$\alpha_3$$ |              4              |      4     |      4     |      4     |      4     |

<p> </p>

Here, I employed a randomized complete block design with a full factorial design.
Also, since I chose and equal number of experimental units in each factor-level combination, we have a balanced design.
Thus, this is a **randomized complete block design** with **balanced replication** (equal $$n$$ in each group).  
In real-world settings, subject mortality may lead to an **unbalanced design**, reducing statistical power.

---

#### 📐 ANOVA Effects Model

The Effects model of the two-way ANOVA is given by:

$$
y_{ijk} = \mu + \tau_j + \alpha_k + (\tau\alpha)_{jk} + \varepsilon_{ijk}
$$

Where:
- $$\mu$$ is the overall mean
- $$\tau_j$$ is the treatment effect
- $$\alpha_k$$ is the block (age group) effect
- $$(\tau\alpha)_{jk}$$ is the interaction effect
- $$\varepsilon_{ijk}$$ is the residual error of the model at each observation
$$\varepsilon_{ijk}$$ is normally distributed with $$0$$ mean .

---


#### 🔀 Understanding Factor Interactions
Here is an analogy of <b>interaction</b> my manager once shared with me:

<p> </p>

> Consider gasoline as one factor (amount in gallons) and lit matchsticks as another (number of matches). Each of these factors affect room temperature independently. But together—they ignite a **dramatic** interaction (🔥). This is how interaction effects can amplify outcomes. (Don't try this experiment at home!)

---

{% tabs interaction-plots %}

{% tab interaction-plots No Interaction %}

```plotly
{
  "data": [
    {"x": [1, 2, 3, 4, 5], "y": [5, 10, 13, 14, 14.5], "type": "scatter", "name": "Treatment 1"},
    {"x": [1, 2, 3, 4, 5], "y": [8.2, 12.8, 16.3, 17.1, 17.2], "type": "scatter", "name": "Treatment 2"}
  ]
}
```

{% endtab %}

{% tab interaction-plots Inference Interaction %}

```plotly
{
  "data": [
    {"x": [1, 2, 3, 4, 5], "y": [5, 12.2, 13, 14, 14.5], "type": "scatter", "name": "Treatment 1"},
    {"x": [1, 2, 3, 4, 5], "y": [8.2, 8.1, 12.3, 17.1, 17.2], "type": "scatter", "name": "Treatment 2"}
  ]
}
```

{% endtab %}

{% tab interaction-plots Reinforcement Interaction %}

```plotly
{
  "data": [
    {"x": [1, 2, 3, 4, 5], "y": [4.3, 6.4, 9, 14, 14.5], "type": "scatter", "name": "Treatment 1"},
    {"x": [1, 2, 3, 4, 5], "y": [7.2, 10.8, 15.9, 22.1, 23], "type": "scatter", "name": "Treatment 2"}
  ]
}
```

{% endtab %}

{% endtabs %}

---

#### 🔍 Type II Error & Power

- **Type II Error ($$\beta$$):** is the probability of failing to detect toxicity at any level when it exists.
- **Power:** is the probability of detecting the presence toxicity for at least one of the treatment levels if it exists.

> Higher power is desired in this context, since missing signs of toxicity could be dangerous. Dosage safety is paramount to future human subjects of the experiments.

$$
Power = 1- \beta
$$

---

#### 🧪 Optional Code: Two-Way ANOVA (R)

In our "df_toxicity" dataframe, <b>Y_concentration</b> contains the toxicity measurement, <b>Dosage</b> contains the drug treatment groups (factor 1), and <b>Age</b> contains the age groups (factor 2). 

The original work was performed in `JMP`, although here I provide the `r code` since it is open source, and so more easily accessible for most users.
One can also use the `proc GLM` function in `SAS` to do the same.
Follow the `SAS` documentation for easy implementation.

We use a generalized linear model function to perform the <b>two-way ANOVA</b> in a regression context.

The output:

$$
\hat{Y} = \beta_o + {\beta}_1X_1 + {\beta}_2X_2 + \cdots + {\beta}_nX_n
$$

can be interperated as follows:
- $$\beta_0$$ is the baseline, represents the mean effect of the treatment control group,($$\tau_0$$) at age block 1.
- $$\beta_1$$ repesents the mean effect of the second treatment group,($$\tau_1$$) at age block 1 after including the treatment control.
That is, the mean effect of the second treatment group at age block 1 is represented by $$\beta_0 + \beta_1$$.

This interpretation however, is not particularly relevant since our goal is not prediction.
Instead, we wish to determine whether the effects of any of the treatments is significant.

As such if significant a significant effect is detected, a natural follow-up would be to determine where the difference exists.



<b>Two-way ANOVA model with no interaction</b>

```r
library(stats)
library(ggplot2)
library(dplyr)

# Example data frame format
df_toxicity = pd.read_csv("toxicity_data.csv")
twoway_lmodel <- lm(formula = Y_concentration ~ Dosage + Age, data = df_toxicity)
print(summary(twoway_lmodel))
```

<b>Two-way ANOVA model with interaction</b>

```r
library(stats)
library(ggplot2)
library(dplyr)

twoway_interaction_lmodel <- lm(formula = Y_concentration ~ Dosage + Age + Dosage:Age, data = df_toxicity)
print(summary(twoway_interaction_lmodel))
```

<b>Post-hoc test for multiple comparison</b>

If the null hypothesis is rejected in favor of the alternate (that is, if toxicity is detected), we neewould need to perform a post-hoc analysis.

Depending on the application, it may be important to execute <b>planned comparisons</b>.
We do this by defining contrast and ensuring that the defined constrast are <b>orthogonal</b>.

Performing all possible multiple comparisons can increase the probability of error, and lead to incorrect and sometimes contradicting conclusions.

Assuming the specified significance level (probability of rejecting a true null hypothesis), $$\alpha=0.05$$.
Say we choose to perform 5 other post-hoc comparisons, then the probability of making this error is no longer $$5\%$$. Instead, it is:

$$
P(error) = 1 - (1-0.05)^6 \approx 0.26
$$

which is about 26%.


```r
multiple_comparison <- pairwise.t.test(df_toxicity$Y_concentration, df_toxicity$Dosage, p.adjust.method = 'bonferroni', conf.level=0.95)

print(multiple_comparison)
```
---


#### ⚖️ Ethical Considerations

- Follow [NIH guidelines](https://www.niaid.nih.gov/grants-contracts/human-subjects) for human subjects.
- For animal subjects, adhere to [IACUC regulations](https://olaw.nih.gov/resources/tutorial/iacuc.htm).
- Use the **minimum number of animals** required for statistical power.
- Prioritize animal welfare—minimize pain and suffering throughout the study.

#### Source Check Notes

- Regulatory comparability and process-change assessments in biomanufacturing are framed in ICH Q5E guidance (internationally recognized benchmark for post-change comparability).
- NIST defines producer/consumer risks and OC-curve-based acceptance sampling interpretation in manufacturing QA: <a href="https://www.itl.nist.gov/div898/handbook/pmc/section2/pmc22.htm">NIST/SEMATECH e-Handbook, 6.2.2</a>.
- Power and Type I/II error interpretation aligned with standard biostatistical guidance: <a href="https://www.ncbi.nlm.nih.gov/books/NBK557530/">StatPearls overview</a>.

---



