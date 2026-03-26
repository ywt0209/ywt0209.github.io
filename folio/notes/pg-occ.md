---
title: "Progressive Gaussian Transformer with Anisotropy-aware Sampling for Open Vocabulary Occupancy Prediction"
author: "Chi Yan et al."
venue: "ICLR 2026"
category: "Semantic Occupancy Prediction"
arxiv: "https://arxiv.org/abs/2510.04759"
github: "https://github.com/yanchi-3dv/PG-Occ"
---

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

### 3.1 Base Gaussian Representation

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

### 3.2 Progressive Feed-forward Densification

### 3.3 Anisotropy-aware Sampling

### 3.4 Open-vocabulary Prediction Head

## 4. Position in the Literature

### Open-vocabulary 3D Occupancy: Evolution

| Stage | Representative Works | Key Idea |
|---|---|---|
| **1. Open-vocabulary alignment** | [OVO (2023)](https://arxiv.org/abs/2305.16133), [POP-3D (NeurIPS 2023)](https://arxiv.org/abs/2401.09413), [VEON (ECCV 2024)](https://arxiv.org/abs/2407.12294) | Distill knowledge from 2D foundation models and CLIP-style supervision into 3D occupancy. |
| **2. Vision-only modeling** | [LangOcc (2024)](https://arxiv.org/abs/2407.17310) | Move toward purely vision-based open-vocabulary occupancy via NeRF-style scene representation. |
| **3. Efficient sparse representation** | [GaussTR (CVPR 2025)](https://arxiv.org/abs/2412.13193) | Replace dense text-vision 3D features with sparse Gaussian blobs for better efficiency. |
| **4. Progressive refinement** | [PG-Occ (ICLR 2026)](https://arxiv.org/abs/2510.04759) | Introduce progressive online densification and anisotropy-aware sampling for finer scene modeling. |

> **Takeaway.** The evolution can be viewed as:
> *language-aligned occupancy → vision-only modeling → sparse Gaussian efficiency → progressive Gaussian refinement*
