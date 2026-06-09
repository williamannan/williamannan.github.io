---
layout: distill
title: Total Variation for Restoration of EBSD Maps
description: In this blog, I decribe a Total Variation model for denoising microscopy data for materials. It is an old model that has been repurposed and proposed for this work.
tags: Mathematics Imaging numerical-methods
giscus_comments: false
date: 2025-04-27 14:24:00-0400
featured: true

authors:
  - name: Emmanuel A. Atindama
    url: "https://sites.google.com/view/emmanuelatindama-exploremath"
    affiliations:
      name: Department of Statistics & Mathematics, University of Toledo

bibliography: bib_posts.bib

thumbnail: assets/img/tv_res/noisy.png
chart:
    plotly: true

# Optionally, you can add a table of contents to your post.
# NOTES:
#   - make sure that TOC names match the actual section names
#     for hyperlinks within the post to work correctly.
#   - we may want to automate TOC generation in the future using
#     jekyll-toc plugin (https://github.com/toshimaru/jekyll-toc).

toc:
  - name: Introduction
    # if a section has subsections, you can add them as follows:
    # subsections:
    #   - name: Example Child Subsection 1
    #   - name: Example Child Subsection 2
  - name: Weighted Total Variation (TV) flow
    subsections:
      - name: Total Variation Minimization
      - name: Weighted TV Minimization
  - name: Selecting Optimal Parameters
    subsections:
        - name: Tuning Sensitivity Parameter
  - name: Data-Driven Stopping Criterion
    subsections:
        - name: Noise Variance Estimation
        - name: Comparison of Noise Variance Estimators (2D vs 3D)
  
  - name: Concluding Remarks

---

<!--Math macros-->
<div style="display:none">
$$
\newcommand{\R}{\mathbb{R}}
\newcommand{\X}{\mathcal{X}}
\newcommand{\Y}{\mathcal{Y}}
\newcommand{\B}{\mathcal{B}}
\newcommand{\T}{\mathcal{T}}
\newcommand{\N}{\mathbb{N}}
\newcommand{\Z}{\mathcal{Z}}
\newcommand{\Q}{\mathbb{Q}}
\newcommand{\pr}{\mathbb{P}}
\newcommand{\bfx}{\mathbf{x}}
\newcommand{\bfy}{\mathbf{y}}
\newcommand{\bfz}{\mathbf{z}}
\newcommand{\bfa}{\mathbf{a}}
\newcommand{\bfw}{\mathbf{w}}
\DeclareMathOperator{\var}{Var}
\DeclareMathOperator{\ex}{\mathbb{E}}
\DeclareMathOperator{\argmax}{arg\,max}
\DeclareMathOperator{\argmin}{arg\,min}
$$
</div>

## Introduction
<p class="post-lead">This article explains how weighted total variation flow restores noisy EBSD orientation maps while preserving grain boundaries and structural detail.</p>

Metals like titanium, aluminium, and steel have crystalline microstructures composed of crystal grains—regions of similar atomic orientation. These grains strongly influence material properties such as elasticity, strength, and fracture behavior <d-cite key="Microstructure-OxfordInstruments"></d-cite>. 

To study these properties, crystal orientations are often measured using **Electron Backscatter Diffraction (EBSD)**, which provides Euler angles describing each grain's orientation. However, EBSD measurements can suffer from noise, missing regions, or inaccuracies—sometimes within $$1^{\circ}$$ of error <d-cite key="Microstructure-OxfordInstruments"></d-cite> which may compromise conclusions about material integrity.

Efforts to improve EBSD data accuracy fall into three categories:
1. **Preprocessing**: Sample preparation for data collection.
2. **Device Optimization**: Tuning device parameters and model tuning for solving electron diffraction patterns.
3. **Postprocessing**: Cleaning and restoring crystallographic data.

This blog discusses **postprocessing**, particularly restoration algorithms for orientation data. Existing tools found in the [MTEX](https://mtex-toolbox.github.io/EBSDDenoising.html) toolbox <d-cite key="mtex"></d-cite> provide deterministic denoising methods and are publicly available.

The goal of restoration is made up of noise removal (correcting erroneous data) and inpainting (filling missing data). I explore a few EBSD data cleaning techniques to enhance microstructure modeling accuracy from my earlier work <d-cite key="eatindama_thesis"></d-cite>.
In this blog post, we employ the theory of variational filters. Variational filters involve finding an object $$u$$ within the space of bounded variation ($$BV$$) functions that minimizes the total variation (TV) norm of the input data, $$f$$ with respect to some constraints. The image space is a subset of the bounded variation space. Variational filters are able to effectively remove noise, while preserving edges and other fine detatils.


<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/tv_res/ebsd_illustration.png" class="img-fluid rounded z-depth-1" zoomable=true %}
        <div class="caption">Synthetic map<i>a</i></div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/tv_res/noisy.png" class="img-fluid rounded z-depth-1" zoomable=true %}
        <div class="caption">Noisy Synthetic map <i>b</i></div>
    </div>
</div>
<div class="caption">
    <i>a</i> Visual representation of a crystal orientation map denoting the Euler Angle value at individual grains <d-cite key="athavale_ebsd_2023"></d-cite> generated with dream3d software <d-cite key="dream3d2014"></d-cite>. <i>b</i> is a corresponding noisy orientation map.
</div>



## Weighted Total Variation (TV) flow

The <b>Weighted Total Variation Flow</b> is a variational filtering method using partial differential equations (PDEs) derived from the Total Variation (TV) minimization problem. This approach is particularly useful for denoising data like grain orientation maps represented by rotations in 3D.


Let $\mathbf{u}_0$ be a crytall orientation map: $$\mathbf{u}_0: \mathbb{R}^2 \to SO(3)$$

At each point in the domain $\Omega \subset \mathbb{R}^2$, $\mathbf{u}_0$ is a vector-valued function: $$\mathbf{u}_0 = \langle u_0^{(1)}, u_0^{(2)}, u_0^{(3)} \rangle$$

In the presence of noise $\eta$, we observe  $$\mathbf{f} = \mathbf{u}_0 + \eta$$

---

#### Total Variation Minimization

Inspired by the ROF model (Rudin, Osher, Fatemi) <d-cite key="ROF"></d-cite> for image denoising is the solution to the TV norm minimization below:

$$
\mathcal{J}[\mathbf{u}] := \arg\min_{\mathbf{u} \in BV(\Omega)^3} \left\{ \Vert \mathbf{u} \Vert_{TV} + \lambda \Vert \mathbf{f} - \mathbf{u} \Vert_{L^2}^2 \right\}
$$

where 

$$
 \Vert \mathbf{u} \Vert_{TV(\Omega)} := \left[ \sum_{k=1}^3 \left( \int_{\Omega} |\nabla u^{(k)}| \right)^2 \right]^{\frac{1}{2}}
$$

is the vector-valued TV norm.

Consider the equation, 
\begin{equation} \label{euler-lagrange}
  F(x,y,u,u_x,u_y) = \sqrt{u_x^2 + u_y^2} + \lambda(f - u)^2
\end{equation}

so that $$F_u = 2\lambda(f - u)$$, $$F_{u_x} = \frac{u_x}{\sqrt{u_x^2 + u_y^2}}$$, and $$F_{u_y} = \frac{u_y}{\sqrt{u_x^2 + u_y^2}}$$.


An extremum (minimmum - for our minimization problem) of the functional $$\eqref{euler-lagrange}$$ must satisfy the Euler-Lagrange equation <d-cite key="ROF"></d-cite> <d-cite key="meyer2001oscillating"></d-cite>:

$$
\lambda(f - u) - div\left(\frac{\nabla u}{|\nabla u|}\right) = 0.
$$

We employ a <b>time evolution form</b> to solve using a gradient descent PDE:

$$
\frac{\partial u^{(k)}}{\partial t} = div\left(\frac{\nabla u^{(k)}}{|\nabla u^{(k)}|}\right) - \lambda(u^{(k)} - f^{(k)}), \quad k = 1,2,3
$$

with initial conditions:
- $$u^{(k)}(\cdot, 0) = f^{(k)}$$

$$
\frac{\partial u^{(k)}}{\partial \hat{n}} |_{\partial \Omega} = 0
$$

---

#### Weighted TV Minimization
To avoid tuning $$\lambda$$, we proposed a different minimization problem which uses a self-determined weight function $$\alpha(x)$$ to regulate the flow:

$$
\mathcal{J}[\mathbf{u}] := \arg\min_{\mathbf{u} \in BV(\Omega)^3} \left\{ \Vert \mathbf{u} \Vert_{TV_\alpha} \right\}
$$

with: $$\Vert \mathbf{u} \Vert_{TV_\alpha} := \left[ \sum_{k=1}^3 \left( \int_\Omega \alpha |\nabla u^{(k)}| \right)^2 \right]^{1/2}$$

where $$\alpha(x) \approx 1$$ in homogeneous regions for higher flow rate, $$\approx 0$$ near edges for lower flow rate, and $$\partial_t \alpha = 0$$.

For each channel,

$$
\frac{\partial u^{(k)}}{\partial t} = div\left(\frac{\alpha \nabla u^{(k)}}{|\nabla u^{(k)}|}\right), \quad k = 1,2,3
$$


Thus, we define the Vector-Valued Weighted TV Flow as:

$$
\Vert \mathbf{u} \Vert_{TV_\alpha} := \sqrt{ \sum_{k=1}^m \left( \int_\Omega \alpha |\nabla u^{(k)}| \right)^2}.
$$

Let
- $$\mathcal{J}(\mathbf{u}) = \Vert \mathbf{u} \Vert_{TV_\alpha}$$.
- $$\mathcal{G}(u^{(k)}) = \Vert u^{(k)} \Vert_{TV_\alpha}$$.

Then,

$$
\frac{\partial \mathcal{J}}{\partial u^{(k)}} = \frac{\mathcal{G}(u^{(k)})}{\mathcal{J}(\mathbf{u})} \cdot div\left( \frac{\alpha \nabla u^{(k)}}{|\nabla u^{(k)}|} \right)
$$

So the evolution equation becomes:

$$
\frac{\partial u^{(k)}}{\partial t} = \frac{\Vert u^{(k)} \Vert_{TV_\alpha}}{\Vert \mathbf{u} \Vert_{TV_\alpha}} \cdot div\left( \frac{\alpha \nabla u^{(k)}}{|\nabla u^{(k)}|} \right)
$$

This formulation retains coupling between different channels, and respects structure in vector-valued data such as RGB images or crystal orientation maps.


<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/tv_res/denoising.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    Illustration of processing grain artifacts in the Euler angles prior to denoising:
    (a) clean image, (b) shows map of corresponding local deviations, (c) is a histogram of deviations, (d)  is a noisy map containing artifacts (<i>circled</i>); (e) local standard deviations of the noisy map, with corresponding histogram in (f). (g) isolates region with  artefacts. (h) shows map with corrected artefacts and further artefact removal in (h). <d-cite key="athavale_ebsd_2023"></d-cite>.
</div>
---


## Selecting Optimal Parameters

The weight function $$\alpha(x)$$ is constructed to differentiate between homogeneous regions and grain boundaries in the orientation map, allowing for effective noise reduction while preserving important features such as edges.

In short, $$\alpha(x)$$ is designed to identify grain boundaries during the denoising process. The weight function $$\alpha(x)$$ is defined so that it is $$1$$ if $x$ is a grain boundary and $0$ otherwise. Since $$\alpha(x)$$ is obtained from a noisy orientation map, it may identify false grain boundaries within a noisy grain. Thus, we need to set a threshold to determine grain boundaries. The definition of $$\alpha(x)$$ is not unique.

For each channel $$k$$:

$$
\alpha^{(k)}(x):=\dfrac{1}{\sqrt{1+\frac{|\nabla(G_{\sigma}*f^{(k)})|^2}{\beta^2}}}
$$

where $$G_{\sigma}$$ is a Gaussian filter with parameter $$\sigma$$, that controls the extent of smoothing, $$\nabla(G_{\sigma}*f^{(k)})$$ is an edge detector, and $$\beta$$ is a sensitivity parameter.

A smaller $$\beta$$ increases sensitivity, making $$\alpha^{(k)}(x)$$ closer to zero at edges and closer to one in homogeneous regions.
For near homogeneous regions, the gradient $$|\nabla(G_{\sigma}*f^{(k)})|$$ is small. Consequently, $$\alpha(x) \approx 1$$, indicating less penalization in the TV norm, thereby allowing for more significant smoothing to reduce noise.

The weight function $$\alpha(x)$$ is spatially varying based on local characteristis, and is stationary with respect to time, i.e., $$\alpha_t(x) = 0$$.This ensures consistent weighting throughout the denoising process.

We solve the time-dependent PDE numerically, starting with initial data $$\mathbf{f}$$ until a denoised solution $$\hat{\mathbf{u}}$$ has been attained.

---

#### Tuning Sensitivity Parameter
While $$\sigma$$ and $$\beta$$ influence the degree of smoothing and edge sensitivity, the model is still considered parameter-independent since it is robust to a wide range of these parameters.
Minor variations in $$\sigma$$ do not significantly affect overall performance, making the model flexible and reliable across different images and noise levels.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/tv_res/betas_degrees.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    Figure 1: Misorientation (in degrees) is calculated by averaging across 64 samples, with 16 samples at four noise levels (2, 4, 6, and 8 degrees)$$, each using 125 different $\beta$ values (ranging from $0.001$ to $0.5$) <d-cite key="eatindama_thesis"></d-cite>.
</div>

From the figure above, we observe that $$\beta$$ is monotone with respect to misorientation. Furthermore, $$\beta$$ is an inverse parameter. A small $$\beta$$ means higher sensitivity, while a larger $$\beta$$ indicates lower sensitivity.

Although $$\beta$$ influences the weights $$\alpha$$, this effect is not arbitrary. Therefore, selecting a very small $$\beta$$ is crucial to ensure higher sensitivity, optimal weight selection, and accurate denoising/restoration.

In practice, certain default values for $$\sigma$$ and $$\beta$$ can be chosen based on empirical studies or common practices, which work well for a wide variety of orientation maps. This eliminates the need for parameter tuning.

The stopping criterion for the denoising process can be based on objective measures such as convergence of the solution or reaching a pre-defined noise level. This reduces dependence on $$\sigma$$ and $$\beta$$ during the denoising iterations.

Therefore, the vectorized weighted TV flow model, incorporating the weight function $$\alpha$$, remains largely parameter-independent due to its adaptive nature and robustness to parameter variations.

The model's ability to dynamically adjust to image characteristics, combined with the potential for default parameter values and objective stopping criteria, ensures effective denoising with no manual tuning needed.

---

## Data-Driven Stopping Criterion

To ensure effective denoising, the process should stop when all noise  $$\eta $$ is removed, such that  $$\hat{u} - f = \eta $$.  
Since  $$\eta $$ is unknown, we use a **variance-based stopping criterion**.

Terminate when:
 $$
\|u_i - f\|_2^2 \geq \hat{\sigma}_\eta^2
 $$
where  $$\hat{\sigma}_\eta^2 $$ is an estimate of the noise variance.


#### Noise Variance Estimation

Given an image  $$ f = u + \eta  $$ with zero-mean noise and dimensions  $$W \times H $$:

- Mean:  $$\frac{1}{W \cdot H} \int_{\Omega} u = \frac{1}{W \cdot H} \int_{\Omega} f $$
- Noise Variance:  $$\sigma^2(\eta) = \frac{1}{W \cdot H} \int_{\Omega} (u - f)^2$$

A 2D Noise Variance Kernel proposed by Immerkaer <d-cite key="immerkaer1996fast"></d-cite>, used a Laplacian difference kernel:

$$
N = \begin{bmatrix}
1 & -2 & 1 \\
-2 & 4 & -2 \\
1 & -2 & 1
\end{bmatrix}
$$
as a convolution filter for noise estimation. This filter is robust to local gradients (edges not mistaken for noise), with noise variance estimate:
$$\hat{\sigma}_{\eta}^2 = \frac{1}{36(W-2)(H-2)} \sum_{\text{image } u} (u * N)^2.$$


To improve estimation in RGB/multichannel images, I proposed a 3D Laplacian difference kernel <d-cite key="eatindama_thesis"></d-cite>  $$\mathcal{N}_3 $$ that incorporates inter-channel relationships.

 $$
\mathcal{N}_3 = \frac{1}{6}(L_1 - L_2)
 $$
where the composite 3D kernel is formed by a sequence of 2D slices.

- Variance:  
   $$
  \text{Var}(\mathcal{N}_3) = 360\sigma_{\eta}^2
   $$

- Global Estimate:
   $$
  \hat\sigma_{\eta}^2 = \frac{1}{360(L-2)(W-2)(H-2)} \sum (u(x,y,z) \ast \mathcal{N}_3)^2
   $$

- Local Estimate:
   $$
  \hat\sigma_{\eta}^2(x,y,z) = \frac{1}{360} (u(x,y,z) \ast \mathcal{N}_3)^2
   $$

---

*Theoretical Justification: Rotational Invariance of the Laplacian*

**Theorem:** The Laplacian operator is invariant under rotation:  $$\nabla^2 f(x, y) = \nabla^2 f(u, v)$$

*Proof Summary:*
- Given a rotation  $$R_\theta $$, express partial derivatives in the new coordinates  $$(u, v) $$.
- Show that second-order derivatives under rotation preserve the form of the Laplacian:
   $$
  \frac{\partial^2 f}{\partial x^2} + \frac{\partial^2 f}{\partial y^2} = \frac{\partial^2 f}{\partial u^2} + \frac{\partial^2 f}{\partial v^2}
   $$
- Uses chain rule and rotation matrix properties.

The data-driven stopping criterion, combined with robust 2D and 3D noise variance estimation, ensures adaptive termination of the denoising algorithm. The invariance of the Laplacian supports rotational robustness in variance detection.

---

#### Comparison of Noise Variance Estimators (2D vs 3D)

In this section, we compare two methods for estimating noise variance in multichannel images: a classical 2D approach and a proposed 3D extension. Our goal is to determine which method provides more accurate estimates relative to the true noise variance, and whether any observed differences are statistically significant.

Accurate noise estimation is essential for high-quality image analysis and processing, especially in scientific imaging applications. We compare the 2D variance estimation method of Immerkaer <d-cite key="immerkaer1996fast"></d-cite> with a 3D variance estimator that extends spatial differencing into the third dimension.

The central hypothesis is that the 3D method—by incorporating spatial differences across the  $$X $$,  $$Y $$, and  $$Z $$ dimensions—provides more accurate estimates of noise variance than the 2D method, which is limited to two spatial dimensions.

To test this hypothesis, we generated ten synthetic orientation maps with white noise added at six levels:  $$0.00 $$,  $$0.01 $$,  $$0.05 $$,  $$0.10 $$,  $$0.15 $$, and  $$0.20 $$. For each noisy image, the true noise variance was manually computed and used as the ground truth. Due to clipping effects from data truncation during noise addition, the actual noise variance may differ from the nominal noise level.

The true variance,  $$\sigma^2_{\eta} $$, was computed using:

$$
\sigma^2_{\eta} = \sum_{i,j,k} \left( \eta_{i,j,k} - \frac{\sum_{i,j,k}\eta_{i,j,k}}{n} \right)^2
$$

where  $$n$$ is the total number of voxels in the noisy image and  $$\eta $$ denotes the noise.

We implemented the two estimators as follows:

- **2D Variance Estimate**: Uses a second-order finite difference kernel in the  $$X $$ and  $$Y $$ directions. Variance is computed independently per channel and then averaged.
  
- **3D Variance Estimate**: Extends the 2D kernel to three dimensions, convolving across  $$X $$,  $$Y $$, and  $$Z $$. The variance is computed similarly but incorporates depth information.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/tv_res/boxplot_noiseestimates.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="caption"><i>a</i></div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/tv_res/boxplot_noiseestimates_noiselevels.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="caption"><i>b</i></div>
</div>
<div class="caption">
    <i>a</i> shows boxplots comparing noise variance estimates from the 2D and 3D kernels to the true noise variance. <i>b</i> shows boxplots comparing noise variance estimates across noise levels of the true values and estimates. <d-cite key="eatindama_thesis"></d-cite>
</div>


| Noise Value | True Variance | 2D Estimate | 3D Estimate |
| :---------- | ------------: | ----------: | ----------: |
| 0.00        |        0.0000 |      0.0019 |      0.0010 |
| 0.01        |        0.0091 |      0.0107 |      0.0103 |
| 0.05        |        0.0382 |      0.0383 |      0.0390 |
| 0.10        |        0.0662 |      0.0641 |      0.0656 |
| 0.15        |        0.0882 |      0.0831 |      0.0855 |
| 0.20        |        0.1057 |      0.0979 |      0.1010 |

Using a *Randomized Complete Block Design* for an experiment design, and a two-way ANOVA, we concluded that there was no significant difference between the three groups. This suggests that both the 2D and 3D estimators provide reliable and comparable noise variance estimates in a statistical sense.

However, as shown in Table 1 and *Figure 2*, observe that the 3D estimator consistently yields values closer to the true variance across all noise levels. This trend supports the hypothesis that the additional spatial dimension in the 3D estimator improves accuracy (although insignificant in this case).



In summary, although both estimators perform similarly in a statistical sense, the 3D variance estimator offers more accurate noise measurements by leveraging the full 3D structure of the image data. This justifies its use in applications where precise noise estimation is critical.

## Animated Tradeoff View

<div class="media-frame">
  <iframe src="{{ '/assets/plotly/tv_tradeoff.html' | relative_url }}" frameborder="0" scrolling="no" title="Animated denoising tradeoff"></iframe>
</div>
<div class="caption">Animated tradeoff between edge preservation and noise removal as smoothing intensity increases.</div>

## Source Check Notes

- MTEX documents denoising options for EBSD orientation maps, including total variation style filtering and tradeoffs in edge preservation: <a href="https://mtex-toolbox.github.io/EBSDDenoising.html">MTEX EBSD Denoising</a>.
- The role of variational denoising and TV regularization is consistent with the classical ROF framework cited in this article.


## Concluding Remarks

This blog post gives a detailed introduction to variational filters.
Variational filters—particularly Weighted TV Flow, offer powerful tools for restoring noisy EBSD orientation maps. With adaptive weights , parameter-insensitive design, and robust stopping criteria, these methods yield high-quality denoising suitable for practical applications in imaging.

* [Emmanuel Atindama and Peter Lef and Günay Doğan and Prashant Athavale. *Restoration of noisy orientation maps from electron backscatter diffraction imaging*. IMMI 2023](https://link.springer.com/article/10.1007/s40192-023-00304-8)
* [Emmanuel Atindama. *Crystallographic Data Reconstruction Using Weighted Total Variation Flow and a Hybrid Deep Learning Method*. ProQuest 2024.](https://www.proquest.com/openview/753bacd9f456a079dab381fe80f192cc/1?cbl=18750&diss=y&pq-origsite=gscholar)

For more reading on more *efficiently* estimating the gradients of diffusion models please check out our recent paper.

The insights gained here are not only relevant to grain mapping, but also other image restoration or data filling tasks. 

If you would like to cite this post in an academic context, you can use this BibTex snippet:
```bibtex
@article{eatindama_wTV2023,
  title={Restoration of Noisy Orientation Maps from Electron Backscatter Diffraction Imaging},
  author={Atindama, Emmanuel and Lef, Peter and Do{\u{g}}an, G{\"u}nay and Athavale, Prashant},
  journal={Integrating Materials and Manufacturing Innovation},
  volume={12},
  number={3},
  pages={251--266},
  year={2023},
  publisher={Springer},
  doi= {10.1007/s40192-023-00304-8},
  url= {https://doi.org/10.1007/s40192-023-00304-8}
}
```
