---
layout: post
title: EAC Estimation with the DDR Framework
date: 2026-06-09 10:00:00-0400
description: A simulation-based framework that quantifies and calibrates equivalence acceptance criteria (EAC) in pharmaceutical comparability studies — with an interactive four-method case study.
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

<p class="post-lead">How do you know whether a chosen equivalence margin is actually a <em>good</em> one? The Decisive-Decision Rate (DDR) framework answers that question — not by proposing yet another margin, but by quantifying how coherently any proposed margin makes decisions.</p>

When a pharmaceutical process changes — a site transfer, a scale-up, a new piece of equipment — regulators expect evidence that the product is **comparable** before and after. The standard tool is the **Two One-Sided Test (TOST)**, which requires a pre-specified **Equivalence Acceptance Criterion (EAC)**, the margin $$\Delta$$ within which two process means are considered "the same."

Choosing that margin is where practice gets shaky. Too wide, and you wave through a meaningfully shifted process; too narrow, and you raise false alarms on processes that are genuinely fine. Yet margin selection remains under-developed: competing recipes ($$\sigma$$-scaled, percent-of-mean, effect-size, capability-linked) are each advocated in isolation, with no common yardstick.

> **The key idea.** This work does **not** introduce a new hypothesis test or a new margin. It is a **calibration framework** that takes *any* proposed EAC value and scores how well it supports coherent equivalence decisions — then lets you rank and tune competing margins on a level playing field.

### What you'll find below

- **The DDR framework** — a 2×2 decision matrix that cross-classifies a difference test against an equivalence test, yielding three diagnostic rates and a single scalar score (iDDR).
- **A four-method case study** — $$\sigma$$-scaled, percent-of-mean, effect-size, and Ppk-informed margins, evaluated across a **13,824-combination factorial design** (10,368 valid scenarios).
- **Actionable guidance** — which margin to use when, backed by Monte Carlo evidence and an ANOVA attribution of what really drives decision quality.

---

## 2. The DDR Framework

The framework is **method-agnostic**: feed it any candidate margin $$\Delta$$ (however it was derived) and it returns a quantitative diagnostic of that margin's calibration quality.

### The two tests

For a quality attribute measured before and after a change, two tests are applied to the same paired data:

- **Difference test (D-test)** — a two-sided $$t$$-test asking *"is there any detectable difference?"*
- **Equivalence test (E-test)** — the TOST procedure asking *"is the difference small enough to be practically irrelevant?"* (i.e. within $$\pm\Delta$$).

### The decision matrix

Crossing the two pass/fail outcomes yields four mutually exclusive zones:

<table style="text-align:center; border-collapse:collapse;">
  <thead>
    <tr style="background-color:#D6EAF8;">
      <th style="padding:10px; border:1px solid #ccc;"></th>
      <th style="padding:10px; border:1px solid #ccc;">E-test pass <br><span style="font-weight:normal;">(equivalent)</span></th>
      <th style="padding:10px; border:1px solid #ccc;">E-test fail <br><span style="font-weight:normal;">(not equivalent)</span></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th style="padding:10px; border:1px solid #ccc; background-color:#D6EAF8;">D-test fail <br><span style="font-weight:normal;">(no difference)</span></th>
      <td style="padding:12px; border:1px solid #ccc; background-color:#D5F5E3;"><strong>Z₁ ✓</strong><br>Concordant-equivalent</td>
      <td style="padding:12px; border:1px solid #ccc; background-color:#FDEBD0;"><strong>Z₂ ?</strong><br>Grey zone</td>
    </tr>
    <tr>
      <th style="padding:10px; border:1px solid #ccc; background-color:#D6EAF8;">D-test pass <br><span style="font-weight:normal;">(difference)</span></th>
      <td style="padding:12px; border:1px solid #ccc; background-color:#FADBD8;"><strong>Z₃ !</strong><br>Paradox</td>
      <td style="padding:12px; border:1px solid #ccc; background-color:#D5F5E3;"><strong>Z₄ ✓</strong><br>Concordant-different</td>
    </tr>
  </tbody>
</table>

<div class="caption">The two green zones are coherent; the amber grey zone is inconclusive; the red paradox zone is an outright contradiction — the data are "different" yet "equivalent" at the same time.</div>

### Three diagnostic rates

From $$N$$ Monte Carlo replicates at a given true shift, the zone counts define:

$$
DDR = \frac{Z_1 + Z_4}{N},\qquad PR = \frac{Z_3}{N},\qquad GZR = \frac{Z_2}{N},
$$

with $$DDR + PR + GZR = 1$$ at every shift. Reading them is intuitive:

- **High DDR** → the margin produces **coherent** decisions.
- **High PR (paradox rate)** → the margin is **too wide** (declares equivalence despite a detected difference).
- **High GZR (grey-zone rate)** → the margin is **too narrow** or the study is **underpowered**.

### The DDR curve and the iDDR score

Sweeping the true standardized shift $$\delta/\sigma_{pre}$$ traces a **DDR curve**. It is $$\approx 1$$ for very small and very large shifts (where both tests agree) and dips near the margin boundary, where the tests most often contradict. The depth, width, and location of that dip are the diagnostic signature of the margin.

Collapsing the curve into one number gives the **integrated DDR**:

$$
iDDR = \frac{1}{\delta_{max}/\sigma_{pre}}\int_0^{\delta_{max}/\sigma_{pre}} DDR(\delta/\sigma_{pre})\,d(\delta/\sigma_{pre}),
$$

a scalar in $$[0,1]$$ that enables **direct ranking** of competing margins. Higher iDDR = better overall calibration across the operating range.

---

## 3. EAC Methods: A Case Study

To demonstrate the framework end-to-end, the paper applies it to four structurally distinct margins. These are **not** contributions of the paper — they are a case study chosen because they are among the most widely used in practice, and each encodes a different philosophy of "how much shift is tolerable."

| Method | Formula | Philosophy | Key driver |
| :-- | :-- | :-- | :-- |
| **A. $$\sigma$$-Scaled** | $$\Delta_A = \lambda\,\sigma_{pre}$$ | margin scales with process noise | tuning $$\lambda$$ |
| **B. Percent-of-Mean** | $$\Delta_B = \lambda_\%\,\lvert\mu_{pre}\rvert$$ | margin is a % of the mean level | $$\mu_{pre}/\sigma_{pre}$$ ratio |
| **C. Effect-Size** | $$\Delta_C = ES\cdot\hat\sigma_{upper}$$ | $$\sigma$$-scaled **+** $$\chi^2$$ variance correction | $$ES$$, $$n_{pre}$$ |
| **D. Ppk-Informed** | $$\Delta_D = \max\!\big(d_{pre}-3\,Ppk_{target}\,\sigma_{pre},\,0\big)$$ | anchored to process capability & spec limits | spec width, $$\eta$$, $$\rho$$ |

A few structural points the paper emphasizes:

- **Method C is Method A with a twist.** It replaces $$\sigma_{pre}$$ with a $$\chi^2$$-corrected upper confidence bound $$\hat\sigma_{upper}=\sqrt{(n_{pre}-1)s_{pre}^2/\chi^2_{\alpha_{\chi^2},\,n_{pre}-1}}$$. That inflation is large at small $$n_{pre}$$ and decays toward 1 as $$n_{pre}\to\infty$$, so A-vs-C is a clean controlled test of the variance correction itself.
- **Method D has a feasibility boundary.** Using the relative target $$Ppk_{target}=\rho\,Ppk_{pre}$$, the margin simplifies to $$\Delta_D = d_{pre}(1-\rho)$$. A fixed regulatory target instead (e.g. $$Ppk_{target}=1.33$$) creates a cliff where the margin collapses to zero — more on this below.

<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/eac/baseline.png" class="img-fluid rounded z-depth-1" zoomable=true %}
  </div>
</div>
<div class="caption">
<strong>Figure 1.</strong> Baseline standardized margins (\(\mathrm{EAC}/\sigma_{pre}\)): D = 0.95, A = 1.50, C = 2.06, B = 2.51. The four methods imply very different margins from the <em>same</em> data — and that difference drives everything downstream.
</div>

---

## 4. Experimental Setup

The engine is a **factorial Monte Carlo** simulation over *dimensionless* process descriptors. Because every test decision depends only on ratios — $$\Delta/\sigma_{pre}$$, $$\delta/\sigma_{pre}$$, sample sizes, and the variance ratio — the study fixes $$\sigma_{pre}=1$$ without loss of generality and varies the ratios that actually matter.

<table style="text-align:center; border-collapse:collapse;">
  <thead>
    <tr style="background-color:#D6EAF8;">
      <th style="padding:8px; border:1px solid #ccc;">Factor</th>
      <th style="padding:8px; border:1px solid #ccc;">Grid values</th>
      <th style="padding:8px; border:1px solid #ccc;"># levels</th>
    </tr>
  </thead>
  <tbody>
    <tr><td style="padding:8px; border:1px solid #ccc;">Mean-to-variability $$\mu_{pre}/\sigma_{pre}$$</td><td style="padding:8px; border:1px solid #ccc;">5, 10, 15, 25, 35, 50</td><td style="padding:8px; border:1px solid #ccc;">6</td></tr>
    <tr><td style="padding:8px; border:1px solid #ccc;">Spec-to-variability $$S/\sigma_{pre}$$</td><td style="padding:8px; border:1px solid #ccc;">2.5, 5, 10</td><td style="padding:8px; border:1px solid #ccc;">3</td></tr>
    <tr><td style="padding:8px; border:1px solid #ccc;">Pre-change sample size $$n_{pre}$$</td><td style="padding:8px; border:1px solid #ccc;">4, 8, 12, 16, 20, 24, 28, 30</td><td style="padding:8px; border:1px solid #ccc;">8</td></tr>
    <tr><td style="padding:8px; border:1px solid #ccc;">Sample-size ratio $$n_{post}/n_{pre}$$</td><td style="padding:8px; border:1px solid #ccc;">⅓, ½, 1, 2</td><td style="padding:8px; border:1px solid #ccc;">4</td></tr>
    <tr><td style="padding:8px; border:1px solid #ccc;">Variance ratio $$\sigma_{post}/\sigma_{pre}$$</td><td style="padding:8px; border:1px solid #ccc;">0.5, 0.75, 1.0, 1.25, 1.5, 2.0</td><td style="padding:8px; border:1px solid #ccc;">6</td></tr>
    <tr><td style="padding:8px; border:1px solid #ccc;">Off-centre fraction $$\eta$$</td><td style="padding:8px; border:1px solid #ccc;">0.05, 0.10, 0.20, 0.35</td><td style="padding:8px; border:1px solid #ccc;">4</td></tr>
  </tbody>
</table>

<div class="caption">This grid yields \(6\times3\times8\times4\times6\times4 = 13{,}824\) nominal combinations; after enforcing \(4\le n_{post}\le 30\), <strong>10,368 valid scenarios</strong> remain. Each is evaluated at 20 standardized shifts over \([0,\,4\sigma_{pre}]\) with 5,000 Monte Carlo replicates per shift.</div>

Two design choices keep the comparison fair:

- **Common random numbers.** All four methods are scored on the *same* simulated datasets, so differences reflect the margin, not Monte Carlo noise.
- **Off-the-shelf defaults *and* tuned optima.** The main comparison uses conventional default settings ($$\lambda=1.5$$, $$\lambda_\%=0.05$$, $$ES=1.5$$, $$\rho=0.8$$) to reflect untuned practical use. A separate two-stage sweep (coarse grid, then a fine search **centred on each method's own coarse optimum**) reports the best achievable performance, so no method is unfairly judged by a poor default.

---

## 5. Results

### 5.1 Baseline behaviour — one scenario, four very different profiles

At the baseline scenario ($$\mu/\sigma=50$$, $$S/\sigma=5$$, $$n_{pre}=n_{post}=20$$, equal variance), the operating-characteristic curves already foreshadow the trade-offs: the wide-margin methods keep declaring equivalence over a broad range of shifts, while the tight-margin Method D's TOST power drops off quickly.

<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/eac/power_curves.png" class="img-fluid rounded z-depth-1" zoomable=true %}
  </div>
</div>
<div class="caption"><strong>Figure 2.</strong> Analytical operating-characteristic curves (exact non-central \(t\)). Left: TOST pass probability vs. true shift; dotted lines mark each method's margin. Right: the shared D-test power curve.</div>

Running the DDR engine at this scenario produces the signature U-shaped curves — coherent at the extremes, dipping near each margin's boundary:

<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/eac/methods_baseline_performance.png" class="img-fluid rounded z-depth-1" zoomable=true %}
  </div>
</div>
<div class="caption"><strong>Figure 3.</strong> Baseline DDR / GZR / PR curves. The tight-margin Method D produces the highest grey-zone rate; the wide-margin Methods C and B produce the highest paradox rates.</div>

The single-scenario ranking is unambiguous — and notice that the **tight** Ppk-informed margin wins here, with a near-zero paradox rate:

{% tabs iddr-rankings %}

{% tab iddr-rankings Baseline (single scenario) %}

```plotly
{
  "data": [
    {"x": ["D: Ppk-Informed", "A: σ-Scaled", "C: Effect-Size", "B: %-of-Mean"],
     "y": [0.945, 0.916, 0.777, 0.662],
     "type": "bar",
     "marker": {"color": ["#E91E63", "#2196F3", "#4CAF50", "#FF9800"]},
     "text": ["0.945", "0.916", "0.777", "0.662"], "textposition": "outside"}
  ],
  "layout": {
    "title": "Baseline iDDR (higher = better calibrated)",
    "yaxis": {"title": "iDDR", "range": [0, 1.05]},
    "margin": {"t": 40, "b": 40}
  }
}
```

{% endtab %}

{% tab iddr-rankings Across the full grid (mean) %}

```plotly
{
  "data": [
    {"x": ["A: σ-Scaled", "C: Effect-Size", "D: Ppk-Informed", "B: %-of-Mean"],
     "y": [0.872, 0.822, 0.808, 0.800],
     "type": "bar",
     "marker": {"color": ["#2196F3", "#4CAF50", "#E91E63", "#FF9800"]},
     "text": ["0.872", "0.822", "0.808", "0.800"], "textposition": "outside"}
  ],
  "layout": {
    "title": "Mean iDDR over 10,368 scenarios",
    "yaxis": {"title": "mean iDDR", "range": [0, 1.05]},
    "margin": {"t": 40, "b": 40}
  }
}
```

{% endtab %}

{% tab iddr-rankings At each method's tuned optimum %}

```plotly
{
  "data": [
    {"x": ["C: Effect-Size", "A: σ-Scaled", "D: Ppk-Informed", "B: %-of-Mean"],
     "y": [0.930, 0.908, 0.842, 0.836],
     "type": "bar",
     "marker": {"color": ["#4CAF50", "#2196F3", "#E91E63", "#FF9800"]},
     "text": ["0.930", "0.908", "0.842", "0.836"], "textposition": "outside"}
  ],
  "layout": {
    "title": "Peak iDDR after tuning each method",
    "yaxis": {"title": "peak iDDR", "range": [0, 1.05]},
    "margin": {"t": 40, "b": 40}
  }
}
```

{% endtab %}

{% endtabs %}

<div class="caption">Toggle the tabs: the ranking <em>reshuffles</em> as we move from one scenario, to the full grid, to tuned optima — which is exactly why a single benchmark scenario can mislead.</div>

### 5.2 Ranking across the grid — robustness changes the story

Averaged over all 10,368 scenarios at off-the-shelf defaults, the **$$\sigma$$-scaled** Method A is the most robust general-purpose choice (mean iDDR **0.872**). Method D, the baseline winner, slips to third (**0.808**) because its tight margin produces a large grey zone in many off-baseline scenarios. No method dominates every criterion:

| Method | mean iDDR | mean iPR (paradox) | mean max GZR (grey) |
| :-- | :--: | :--: | :--: |
| **A. $$\sigma$$-Scaled** | **0.872** | 0.036 | 0.220 |
| **C. Effect-Size** | 0.822 | 0.165 | 0.028 |
| **D. Ppk-Informed** | 0.808 | **0.020** | 0.596 |
| **B. %-of-Mean** | 0.800 | 0.054 | 0.501 |

<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/eac/violin_plots.png" class="img-fluid rounded z-depth-1" zoomable=true %}
  </div>
</div>
<div class="caption"><strong>Figure 4.</strong> Full iDDR distribution across all scenarios. Method A concentrates near 0.95 with a thin lower tail; D and B spread widely.</div>

### 5.3 Robustness — what makes or breaks each margin

**Sample size** is the single biggest driver of decision quality, but it cuts both ways. As $$n$$ grows the point-null D-test becomes *overpowered*, flagging trivially small shifts as significant and inflating the paradox rate:

<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/eac/sample_size_robustness.png" class="img-fluid rounded z-depth-1" zoomable=true max-width="88%" %}
  </div>
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/eac/overpowered_dtest.png" class="img-fluid rounded z-depth-1" zoomable=true max-width="88%" %}
  </div>
</div>
<div class="caption"><strong>Figure 5.</strong> Left: mean iDDR vs. \(n_{pre}\). Right: the overpowered-D-test effect; at a fixed small shift, every method's paradox rate climbs toward 1 as \(n\) grows, with the tight-margin Method D resisting longest.</div>

**Unequal variance** splits the methods. As the post-change spread inflates, Methods A, B, and D degrade — but Method C *improves*, because its margin depends only on pre-period information while the inflating standard error robs the D-test of the power that fuels its paradoxes:

<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/eac/unequal_variance_robustness.png" class="img-fluid rounded z-depth-1" zoomable=true max-width="88%" %}
  </div>
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/eac/ppk_feasibility_cliff.png" class="img-fluid rounded z-depth-1" zoomable=true max-width="88%" %}
  </div>
</div>
<div class="caption"><strong>Figure 6.</strong> Left: mean iDDR vs. variance ratio; A, B, and D fall while C rises. Right: Method D's feasibility cliff under a <em>fixed</em> target \(Ppk_{target}=1.33\); both margin and iDDR collapse at \(S/\sigma=4\). The relative target \(\rho\,Ppk_{pre}\) avoids this entirely.</div>

Finally, the iDDR ranking is **stable across the integration range** — choosing $$\delta_{max}$$ anywhere from $$2\sigma$$ to $$6\sigma$$ preserves the D > A > C > B ordering at baseline:

<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/eac/iddr_range_sensitivity.png" class="img-fluid rounded z-depth-1" zoomable=true %}
  </div>
</div>
<div class="caption"><strong>Figure 7.</strong> iDDR vs. the integration upper bound \(\delta_{max}/\sigma_{pre}\). Absolute values rise with the range, but the ranking is invariant.</div>

### 5.4 Tuning — the gaps are structural, and the variance correction *helps*

A natural question: once every method is tuned to its best setting, do they converge? **They don't.** A ≈9 percentage-point gap persists between the best and worst, confirming the differences are structural rather than artefacts of default hyperparameters.

<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/eac/parameter_tuning_optimization.png" class="img-fluid rounded z-depth-1" zoomable=true %}
  </div>
</div>
<div class="caption"><strong>Figure 8.</strong> Fine-tuned iDDR vs. each method's primary tuning parameter, with the optimizing value marked.</div>

> **The most interesting reversal.** At their respective optima, **Method C (0.930) overtakes Method A (0.908)** — even though C's optimal effect size ($$ES^{*}\approx1.00$$) sits *below* A's coefficient ($$\lambda^{*}\approx1.43$$). Because the two methods are identical apart from the $$\chi^2$$ variance correction, this is direct evidence that the correction *improves* the calibrated margin. Method C's bad reputation at default settings is purely an artefact of naively matching $$ES$$ to A's $$\lambda=1.5$$, which over-widens the realized margin to $$2.06\sigma$$ and inflates paradoxes — not a flaw in the correction itself.

### A note on sample size for TOST power

Tighter margins demand more data. Validating the analytical (known-$$\sigma$$) sample sizes against Monte Carlo with *estimated* variance shows the analytical figures are optimistic lower bounds:

| Method | $$\mathrm{EAC}/\sigma$$ | $$n$$ for 80% (analytical / MC) | $$n$$ for 90% (analytical / MC) |
| :-- | :--: | :--: | :--: |
| A. $$\sigma$$-Scaled | 1.50 | 7 / 9 | 9 / 11 |
| B. %-of-Mean | 2.51 | 3 / 4 | 4 / 5 |
| C. Effect-Size | 2.06 | 4 / 5 | 5 / 6 |
| D. Ppk-Informed | 0.95 | 15 / 20 | 20 / 25 |

---

## 6. Discussion

### What drives decision quality? An ANOVA attribution

A Type-II ANOVA on the factorial iDDR results pinpoints the dominant effects:

| Effect | $$\eta^2$$ | Interpretation |
| :-- | :--: | :-- |
| Sample size $$n_{pre}$$ | **0.337** | Large — the single biggest driver |
| Method × variance-ratio | 0.114 | Medium — methods diverge under heteroscedasticity |
| Method (main effect) | 0.064 | Medium — the margin formula genuinely matters |
| Method × $$\mu/\sigma$$ | 0.031 | Medium — Method B's mean-level dependence |
| Off-centre fraction $$\eta$$ | <0.001 | Negligible — confirms scale-agnosticism |

That the off-centre fraction barely registers confirms the dimensionless parameterisation is doing its job; that **method choice** is a substantive main effect is exactly what justifies a systematic comparison framework in the first place.

### A practical decision guide

Translating the evidence into guidance — and note this corrects a common intuition: the effect-size method is **not** a safety-critical default, because at off-the-shelf settings it is the most paradox-prone, not the least.

> **Choosing a margin**
> - **No specification limits?** → **Method A ($$\sigma$$-scaled)** with $$\lambda\in[1.0,1.5]$$ — the most robust, interpretable general-purpose choice.
> - **Specs available and wide ($$S/\sigma > 3\,Ppk_{target}$$)?** → **Method D (Ppk-informed)** with a relative target $$Ppk_{target}=\rho\,Ppk_{pre}$$ — best-calibrated and lowest paradox rate when capability framing is central.
> - **Specs available but narrow?** → **Method A** with a tight $$\lambda$$ (the Ppk margin becomes impractically small).
> - **High-sensitivity / safety-critical attribute?** → prefer the **tighter-margin branch** (Method D, or tight-$$\lambda$$ A) to minimize false-equivalence (paradox) risk — at the cost of a wider grey zone and larger samples.
> - **Method B (%-of-mean)** is best avoided as a standalone rule: its margin tracks the mean level, a weak proxy for variability.

### Relationship to existing work

The DDR framework is **complementary**, not a replacement, to:

- **Three-sided testing / zone interpretation** — same four-outcome logic, but used as a simulation-based *calibration* tool rather than a single-study interpretive guide.
- **Second-generation $$p$$-values (SGPV)** — measures CI-to-margin overlap within one test; DDR cross-classifies two separate decisions.
- **Data-dependent / post-hoc margins** — solve the margin-choice problem at analysis time; DDR evaluates *pre-specified* margins.

### Limitations & future work

The framework assumes normality and a classical point-null D-test, which is what makes it *overpowered* at large $$n$$. The paper's headline future direction is a **practical-significance gate**: declare a difference only when the D-test is significant **and** the observed shift exceeds a meaningful fraction of the margin (e.g. $$\lvert\bar{Y}-\bar{X}\rvert > 0.75\,\Delta$$, with effect size measured by Cohen's $$d = (\bar{Y}-\bar{X})/s_p$$). Because the shifts within $$0.75\Delta$$ are precisely those most likely to be genuinely equivalent, such a gate would directly suppress the large-$$n$$ paradoxes while preserving the zone classification. Other directions include Bayesian and adaptive margins, non-normal extensions, and multivariate quality attributes.

---

## 7. Conclusion

The Decisive-Decision Rate framework is a **decision-quality lens** for equivalence-margin specification — not a new test, and not a new margin.

- It cleanly separates **wide-margin failure** (paradox) from **narrow-margin failure** (grey zone).
- It collapses a margin's full shift-response into a single, rankable score (**iDDR**).
- It surfaces sample-size and robustness insight that one-off equivalence tests cannot.

Applied as a case study to four common margins, it shows that **$$\sigma$$-scaled** is the most robust general-purpose default (mean iDDR 0.872), **Ppk-informed** is best when specification limits anchor the decision (baseline 0.945, lowest paradox), the **effect-size** margin's $$\chi^2$$ correction genuinely *helps* once calibrated (highest tuned peak, 0.930), and **percent-of-mean** is structurally handicapped by its dependence on the mean level. Crucially, tuning does not erase these gaps — they are structural.

The takeaway for practitioners: rather than defending a margin by tradition, you can now **justify it with quantitative evidence** — run a targeted DDR study and compare the iDDR of your candidate margin against the alternatives, for your own process and sample sizes.

---

## Appendix Summary

The appendices reinforce the theory and empirics behind the recommendations:

- **A. Proofs** — derivation of the Ppk-informed margin for centred and off-centre processes.
- **B. ANOVA** — effect-size attribution of the factorial drivers of iDDR.
- **C. Monte Carlo validation** — sample-size requirements under realistic *estimated-variance* conditions, confirming the analytical values are optimistic lower bounds.

---

## Download

- Full paper PDF: [DDR EAC Framework]({{ '/assets/pdf/ddr_eac_framework_paper.pdf' | relative_url }})

## Source Notes

- Foundational equivalence-testing context follows the TOST literature and pharmaceutical comparability guidance cited in the paper bibliography.
- All figures and the interactive charts are generated from the paper's reproducible Monte Carlo notebook.
