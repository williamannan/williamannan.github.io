---
layout: post
title: EAC Estimation with the DDR Framework
date: 2026-06-09 10:00:00-0400
description: A practical guide to estimating Equivalence Acceptance Criteria (EAC) using Decisive-Decision Rate diagnostics.
tags: Statistics DesignOfExperiments
categories: my-posts
giscus_comments: true
related_posts: true
pretty_table: true
tabs: true
thumbnail: assets/img/sampling/sampling.png
chart:
  plotly: true
authors:
  - name: Emmanuel A. Atindama
    url: "https://sites.google.com/view/emmanuelatindama-exploremath"
    affiliations:
      name: Department of Statistics & Mathematics, University of Toledo
---

This post summarizes a practical workflow for estimating an Equivalence Acceptance Criteria (EAC) when evaluating pre-change vs post-change manufacturing behavior.

The content is based on my local LaTeX paper draft and computational notebook for the DDR framework.

## Why EAC Estimation Is Hard

When you run a TOST-style equivalence test, your final decision depends critically on the margin $$\Delta$$.

- If $$\Delta$$ is too wide, you may claim equivalence even when a meaningful shift exists.
- If $$\Delta$$ is too narrow, you may reject acceptable changes and create unnecessary operational burden.

A useful margin should produce coherent decisions between:

- a difference test (D-test), and
- an equivalence test (E-test / TOST).

## DDR Idea in One Figure

The DDR framework classifies each simulation run into four zones:

1. Concordant-equivalent (good)
2. Grey zone / inconclusive
3. Paradox (difference detected but equivalence also declared)
4. Concordant-different (good)

From this, we compute:

$$
DDR = P(\text{concordant outcomes}),\qquad
PR = P(\text{paradox}),\qquad
GZR = P(\text{grey zone}).
$$

A strong EAC setting should maximize DDR while controlling PR and GZR.

## Four EAC Methods Compared

I compared four EAC families used in practice:

1. $$\sigma$$-scaled: $$\Delta = \lambda\sigma_{pre}$$
2. Percent-of-mean: $$\Delta = \lambda_\% |\mu_{pre}|$$
3. Effect-size based with variance inflation
4. Ppk-informed margin from process capability targets

Baseline setup from the notebook:

- $$\mu/\sigma = 50$$, $$S/\sigma = 5$$
- $$n_{pre}=n_{post}=20$$
- $$\sigma_{post}/\sigma_{pre}=1$$
- $$\alpha_d=\alpha_e=0.05$$

Expected baseline ordering for margin width:

$$
\Delta_D < \Delta_C < \Delta_A < \Delta_B.
$$

## Interactive Shift Animation

<div class="l-page">
  <iframe src="{{ '/assets/plotly/eac_shift_sweep.html' | relative_url }}" frameborder='0' scrolling='no' height="560px" width="100%" style="border: 1px dashed #999;"></iframe>
</div>
<div class="caption">
  Shift sweep animation generated from my EAC experiments. It shows how decision zones and operating characteristics evolve as true mean shift increases.
</div>

## Illustrative DDR Curves

```plotly
{
  "data": [
    {"x": [0, 0.5, 1.0, 1.5, 2.0, 2.5], "y": [0.92, 0.90, 0.85, 0.79, 0.73, 0.68], "mode": "lines+markers", "name": "A: sigma-scaled"},
    {"x": [0, 0.5, 1.0, 1.5, 2.0, 2.5], "y": [0.88, 0.84, 0.76, 0.67, 0.59, 0.50], "mode": "lines+markers", "name": "B: percent-mean"},
    {"x": [0, 0.5, 1.0, 1.5, 2.0, 2.5], "y": [0.90, 0.88, 0.82, 0.75, 0.68, 0.62], "mode": "lines+markers", "name": "C: effect-size"},
    {"x": [0, 0.5, 1.0, 1.5, 2.0, 2.5], "y": [0.94, 0.92, 0.87, 0.80, 0.73, 0.66], "mode": "lines+markers", "name": "D: Ppk-informed"}
  ],
  "layout": {
    "title": "Illustrative DDR vs standardized shift",
    "xaxis": {"title": "True shift (delta/sigma_pre)"},
    "yaxis": {"title": "DDR", "range": [0.45, 1.0]}
  }
}
```

## Practical Takeaways

1. For broad, robust use across many scenarios, $$\sigma$$-scaled EAC performs consistently well.
2. Ppk-informed EAC is very strong when specification limits and capability targets are reliable.
3. Percent-of-mean EAC can become miscalibrated when mean scale is disconnected from process variability.
4. DDR-style diagnostics give an actionable way to tune EAC rather than relying only on one-off heuristics.

## Download Companion Material

- Paper draft (PDF): [DDR EAC Framework]({{ '/assets/pdf/ddr_eac_framework_paper.pdf' | relative_url }})

## Source Check Notes

- Equivalence testing foundations are based on TOST (Schuirmann, 1987) and subsequent pharmaceutical comparability usage.
- NIST acceptance sampling terminology and OC-curve interpretation: <a href="https://www.itl.nist.gov/div898/handbook/pmc/section2/pmc22.htm">NIST/SEMATECH e-Handbook, 6.2.2</a>.
- ICH Q5E remains a standard international reference for comparability after manufacturing changes.
