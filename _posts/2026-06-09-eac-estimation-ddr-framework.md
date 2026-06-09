---
layout: post
title: EAC Estimation with the DDR Framework
date: 2026-06-09 10:00:00-0400
description: Section-by-section summary of the DDR paper for calibrating Equivalence Acceptance Criteria in comparability studies.
tags: Statistics DesignOfExperiments
categories: my-posts
giscus_comments: true
related_posts: true
pretty_table: true
tabs: true
thumbnail: assets/img/eac/baseline.png
chart:
  plotly: true
authors:
  - name: Emmanuel A. Atindama
    url: "https://sites.google.com/view/emmanuelatindama-exploremath"
    affiliations:
      name: Department of Statistics & Mathematics, University of Toledo
---

<p class="post-lead">This post is a concise, section-by-section summary of the paper.</p>

This post is a concise, section-by-section summary of the paper:

**Decisive-Decision Rate (DDR): A Framework for Comparing and Calibrating Equivalence Acceptance Criteria**.

The goal is to explain the full argument of the manuscript in blog format while preserving the same scientific meaning.

---

## Abstract Summary

The paper addresses a core comparability challenge: how to choose an Equivalence Acceptance Criteria (EAC) margin $$\Delta$$ for TOST.

- If $$\Delta$$ is too wide, paradoxical conclusions appear (difference detected but equivalence also declared).
- If $$\Delta$$ is too narrow, decisions become inconclusive and overly conservative.

The DDR framework evaluates candidate margins through Monte Carlo cross-classification of D-test and E-test outcomes into four zones. From this, the paper defines three diagnostics:

$$
DDR,\quad PR,\quad GZR
$$

and introduces an integrated score (iDDR) to rank methods across shift ranges.

---

## 1. Introduction Summary

Section 1 motivates the work using pharmaceutical process changes (site transfers, scale-up, equipment updates), where regulators expect evidence of comparability.

Key message:

- TOST is standard, but margin selection is underdeveloped.
- Existing methods are often proposed in isolation.
- Existing frameworks (TST/zone interpretation, SGPV, post-hoc data-dependent margins) are useful but solve different problems.

The paper's contribution is not a new hypothesis test. It is a **calibration framework** that compares and ranks margin methods by decision coherence.

---

## 2. Preliminaries Summary

Section 2 defines the two-population setup and compares four EAC families:

1. $$\sigma$$-scaled: $$\Delta_A = \lambda\sigma_{pre}$$
2. Percent-of-mean: $$\Delta_B = \lambda_{\%}|\mu_{pre}|$$
3. Effect-size with variance inflation: $$\Delta_C = ES\,\hat\sigma_{upper}$$
4. Ppk-informed capability margin: $$\Delta_D$$ from specification-distance and capability target

The section derives the Ppk-informed margin for centered and off-center processes, then introduces a practical capability-retention form. It also highlights feasibility boundaries where target capability makes non-zero margin impossible.

<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/eac/baseline.png" class="img-fluid rounded z-depth-1" zoomable=true %}
  </div>
</div>
<div class="caption">
Baseline EAC comparison from the paper: methods imply very different standardized margins, which directly affects decision behavior.
</div>

---

## 3. DDR Framework Summary

Section 3 defines the decision matrix by crossing:

- D-test pass/fail
- E-test pass/fail

This yields four zones (concordant-equivalent, grey zone, paradox, concordant-different). The diagnostics are:

$$
DDR = \frac{Z_1 + Z_4}{N},\qquad PR = \frac{Z_3}{N},\qquad GZR = \frac{Z_2}{N}.
$$

Then the paper defines the DDR curve as a function of standardized shift $$\delta/\sigma_{pre}$$ and introduces:

$$
iDDR = \frac{1}{\delta_{max}/\sigma}\int_0^{\delta_{max}/\sigma} DDR(\delta/\sigma)\,d(\delta/\sigma)
$$

as a scalar ranking metric.

Interpretation:

- High DDR: coherent decisions.
- High PR: margin too wide.
- High GZR: margin too narrow or underpowered.

---

## 4. Experimental Setup Summary

Section 4 describes a factorial Monte Carlo engine over dimensionless process descriptors. The paper uses:

- standardized shifts,
- varying sample sizes and sample-size imbalance,
- variance-ratio stress tests,
- process off-centering,
- specification-to-variability and mean-to-variability ratios.

The design separates method structure from arbitrary scale choices, and includes tuning sweeps to ensure methods are not unfairly judged by poor default hyperparameters.

---

## 5. Results Summary

Section 5 has two layers of findings.

### Baseline behavior

At the baseline scenario, method behavior differs sharply in paradox-vs-grey-zone tradeoff.

<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/eac/methods_baseline_performance.png" class="img-fluid rounded z-depth-1" zoomable=true %}
  </div>
</div>
<div class="caption">
Baseline DDR/GZR/PR diagnostic curves from the paper.
</div>

### Robustness and ranking across the factorial grid

Across thousands of valid scenarios, the paper reports:

- $$\sigma$$-scaled as strongest general-purpose performer by mean iDDR.
- Ppk-informed as excellent in capability-anchored settings with very low paradox risk.
- Effect-size method as highly conservative (near-zero paradox, higher grey-zone burden).
- Percent-of-mean as structurally vulnerable to paradox inflation when disconnected from variability.

<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/eac/power_curves.png" class="img-fluid rounded z-depth-1" zoomable=true %}
  </div>
</div>
<div class="caption">Operating characteristic curves used in the paper's baseline analysis.</div>

<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/eac/violin_plots.png" class="img-fluid rounded z-depth-1" zoomable=true %}
  </div>
</div>
<div class="caption">Distribution of iDDR over the full factorial set.</div>

<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/eac/sample_size_robustness.png" class="img-fluid rounded z-depth-1" zoomable=true %}
  </div>
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/eac/unequal_variance_robustness.png" class="img-fluid rounded z-depth-1" zoomable=true %}
  </div>
</div>
<div class="caption">Robustness trends with sample size and variance mismatch.</div>

<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/eac/parameter_tuning_optimization.png" class="img-fluid rounded z-depth-1" zoomable=true %}
  </div>
</div>
<div class="caption">Tuning optimization confirms ranking gaps are largely structural, not only parameter-choice artifacts.</div>

---

## 6. Discussion Summary

Section 6 translates diagnostics into practical guidance:

- Use method A when you need robust, interpretable default behavior across broad operating conditions.
- Use method D when specification limits and capability framing are central.
- Use method C for safety-critical settings where paradox risk should be minimized.
- Avoid method B as a standalone rule when process mean scale is a weak proxy for process variability.

The section also discusses relationship to TST, SGPV, and data-dependent margins, emphasizing complementarity rather than replacement.

---

## 7. Conclusion Summary

The paper's conclusion is that DDR provides a quantitative calibration lens for EAC selection.

- It separates wide-margin failure modes (paradox) from narrow-margin failure modes (grey zone).
- It supports direct method ranking via iDDR.
- It provides sample-size and robustness insight unavailable from one-off equivalence tests.

In short, DDR is a decision-quality framework for margin specification, not just a testing workflow.

---

## Appendix Summary

The appendices provide:

- metric-space properties for DDR-derived distances,
- ANOVA effect-size attribution for factorial drivers,
- Monte Carlo validation of sample-size requirements under estimated-variance conditions.

These sections strengthen the theoretical and empirical rigor behind the practical recommendations.

---

## Download

- Full paper PDF: [DDR EAC Framework]({{ '/assets/pdf/ddr_eac_framework_paper.pdf' | relative_url }})

## Source Notes

- This post summarizes the attached manuscript `ddr_paper.tex` and uses its corresponding figure set.
- Foundational equivalence-testing context follows TOST literature and comparability guidance cited in the paper bibliography.
