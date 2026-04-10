## 1. Paper at a Glance

- **Task**: Open-vocabulary 3D occupancy prediction
- **Core idea**: Progressive Gaussian refinement for sparse yet fine-grained scene modeling
- **Keywords**: Gaussian representation, densification, anisotropy-aware sampling, open-vocabulary occupancy

## 2. Overview

![Figure 1. Overview of the PG-Occ framework.](../assets/images/folio/pg-occ-fig1.png)
*Figure 1. Overview of the PG-Occ framework.*

### Main intuition

- Start from coarse base Gaussians
- Progressively densify under-modeled regions
- Use anisotropy-aware sampling for better feature aggregation
- Improve fine-grained occupancy prediction while keeping efficiency

## 3. Method Breakdown

![Figure 2. Architecture of the proposed PG-Occ framework.](../assets/images/folio/pg-occ-fig2.png)
*Figure 2. Architecture of the proposed PG-Occ framework.*

### 3.1 Base Gaussian Representation *(Fig. 2, left-bottom)*

PG-Occ represents the 3D scene as a set of sparse **feature Gaussian blobs**:

$$G = \{G_i : (\mu_i, s_i, r_i, \sigma_i, f_i) \mid i=1,\dots,N \}$$

| Symbol | Meaning |
|---|---|
| $\mu_i$ | 3D center of the Gaussian blob |
| $s_i$ | Axis-wise scale of the Gaussian blob |
| $r_i$ | Rotation quaternion defining Gaussian orientation |
| $\sigma_i$ | Opacity / density term |
| $f_i$ | 512-d text-aligned semantic feature |
| $N$ | Total number of Gaussian blobs |

> In the default PG-Occ setting, the model starts from **4,000** base Gaussian queries and uses two progressive densification layers, each adding **1,000** new queries.

![Figure 3. Illustration of the Progressive Online Densification and Anisotropy-aware Feature Sampling modules.](../assets/images/folio/pg-occ-fig3.png)
*Figure 3. Illustration of the Progressive Online Densification and Anisotropy-aware Feature Sampling modules.*

### 3.2.1 Progressive Feed-forward Densification *(Fig. 2 center + Fig. 3 left)*

This work proposes an efficient feed-forward strategy for real-time Gaussian densification.

**Base Initialization (×1)**

$$P=\bigcup_{l=1}^{L} T_l \cdot \left(K_l^{-1}\cdot D_l\right) \;\ldots\;\text{per-pixel back-projection using estimated depth}$$

$$P_0=\mathrm{FPS}(P) \;\ldots\;\text{sample } N \text{ well-spread points}$$

**Feed-forward Densification (×2)**

$$\hat{D}=\frac{\sum_{i\in N} d_i \alpha_i \prod_{j=1}^{i-1}(1-\alpha_j)}{\sum_{i\in N} \alpha_i \prod_{j=1}^{i-1}(1-\alpha_j)} \;\ldots\;\text{render the expected depth map}$$

$$\mathcal{U}_{\mathrm{select}}=\left\{(u,v)\in\Omega \;\middle|\; \hat{D}(u,v)-D(u,v)>\gamma\right\} \;\ldots\;\text{select candidates with large depth discrepancy}$$

$$\mu^{b}=\mu^{b-1}\oplus\mu_{\mathrm{add}}^{b}, \qquad q^{b}=q^{b-1}\oplus q_{\mathrm{add}}^{b} \;\ldots\;\text{append new Gaussians}$$

### 3.2.2 Asymmetric Self-Attention (ASA) *(Fig. 2 self-attention / masked self-attention blocks)*

$$q_{\mathrm{asa}}^{b}=\mathrm{ASA}\!\left(q^{b}+\mathrm{PE}(\mu^{b}),\; q^{b}+\mathrm{PE}(\mu^{b}),\; q^{b},\; M\right) \;\ldots\;\text{apply asymmetric self-attention with positional encoding}$$

$$M_{i,j}=\begin{cases}-\infty, & i < x_{b-1}\ \text{and}\ j \ge x_{b-1} \\0, & \text{otherwise}\end{cases} \;\ldots\;\text{block old queries from attending to newly added ones}$$

### 3.2.3 Anisotropy-aware Sampling *(Fig. 2 sampling/aggregation blocks; Fig. 3 right)*

$$\mu^{\mathrm{sample}}_{i,j} = \mu_i + R(r_i)\cdot \left(s_i \odot \mu^\delta_{i,j}\right), \quad j=1,\ldots,n \;\ldots\;\text{sample anisotropy-aware points inside each Gaussian}$$

$$f_i^{a} = \mathrm{Aggregation}\Big(\{\mathrm{Interp}(\mu^{\mathrm{sample}}_{i,j}, F)\}_{j=1}^{n}\Big) \;\ldots\;\text{project to 2D feature planes and aggregate sampled features}$$

$$f_i = \mathrm{MLP}_{\mathrm{feat}}(f_i^{a}), \qquad (\Delta \mu_i, s_i, r_i, \sigma_i) = \mathrm{MLP}_{\mathrm{geo}}(f_i^{a}) \;\ldots\;\text{decode semantic feature and geometric attributes}$$

$$\mu_i = \mu_i^{b-1} + \Delta \mu_i \;\ldots\;\text{refine Gaussian position by residual update}$$

### 3.3 Efficient Training via Rasterization *(Fig. 2 right, rasterization branch)*

$$\hat{D}^{\,b},\hat{F}^{\,b}=\mathrm{Rasterize}(G^{b}) \;\ldots\;\text{render depth and feature maps from layer-}b\text{ Gaussians}$$

$$L_{\mathrm{depth}}=L_{L1}(D,\hat{D})+\lambda_{\mathrm{SILog}}L_{\mathrm{SILog}}(D,\hat{D})+\lambda_{\mathrm{temp}}L_{\mathrm{temp}}(D,\hat{D}) \;\ldots\;\text{enforce geometric consistency with depth supervision}$$

$$L_{\mathrm{feat}}=L_{\mathrm{cos}}(F,\hat{F})+\lambda_{\mathrm{mse}}L_{\mathrm{mse}}(F,\hat{F}) \;\ldots\;\text{align rendered features with image-derived text-aligned features}$$

$$L_{\mathrm{total}}=\lambda_{\mathrm{depth}}L_{\mathrm{depth}}+\lambda_{\mathrm{feat}}L_{\mathrm{feat}} \;\ldots\;\text{jointly optimize geometry and semantic feature alignment}$$

## 4. Experiments

**Table 1 (Occupancy).** **15.15 mIoU**, better than VEON (**13.95**), GaussTR (**13.25**), and LangOcc (**12.04**).

![Table 1. Occupancy prediction results.](../assets/images/folio/pg-occ-exp1.png)

## 5. Position in the Literature

### Open-vocabulary 3D Occupancy: Evolution

| Stage | Representative Works | Key Idea |
|---|---|---|
| **1. Open-vocabulary alignment** | [OVO (2023)](https://arxiv.org/abs/2305.16133), [POP-3D (NeurIPS 2023)](https://arxiv.org/abs/2401.09413), [VEON (ECCV 2024)](https://arxiv.org/abs/2407.12294) | Distill knowledge from 2D foundation models and CLIP-style supervision into 3D occupancy. |
| **2. Vision-only modeling** | [LangOcc (2024)](https://arxiv.org/abs/2407.17310) | Move toward purely vision-based open-vocabulary occupancy via NeRF-style scene representation. |
| **3. Efficient sparse representation** | [GaussTR (CVPR 2025)](https://arxiv.org/abs/2412.13193) | Replace dense text-vision 3D features with sparse Gaussian blobs for better efficiency. |
| **4. Progressive refinement** | [PG-Occ (ICLR 2026)](https://arxiv.org/abs/2510.04759) | Introduce progressive online densification and anisotropy-aware sampling for finer scene modeling. |

> **Takeaway.** The evolution can be viewed as:
> *language-aligned occupancy → vision-only modeling → sparse Gaussian efficiency → progressive Gaussian refinement*

> Unlike existing approaches, this work focuses on progressive Gaussian modeling, employing a coarse-to-fine approach to enhance scene perception capabilities.
