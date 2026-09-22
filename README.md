# Weakly-Supervised Image Denoising with Transformers

> Academic research project — ENSAI · 2023–2024

A research project on image denoising under **Poisson–Gaussian noise**, exploring whether transformer-based denoisers can improve a weakly/self-supervised denoising pipeline without relying on clean reference images during the Noise2VST stage.

## Research question

Can a transformer-based Gaussian denoiser, conditioned on the noise level, improve denoising under realistic Poisson–Gaussian noise while remaining compatible with a weakly-supervised variance-stabilization framework?

## Approach

The work builds on **Noise2VST** and introduces **SCUNet2**, an adaptation of SCUNet that receives a noise-level map, inspired by the conditioning mechanism used in DRUNet.

```text
Noisy image
    │
    ▼
Variance Stabilizing Transform (GAT / learned spline)
    │
    ▼
SCUNet2
Transformer-based Gaussian denoiser
    │
    ▼
Inverse transform
    │
    ▼
Denoised image
```

The study combines:

- Poisson–Gaussian noise modelling
- Generalized Anscombe Transform (GAT)
- learned variance-stabilizing splines
- blind-spot / self-supervised optimization
- SCUNet / Swin Transformer blocks
- noise-level conditioning

## Experimental protocol

SCUNet2 was trained on random **128 × 128** patches with rotations and flips, using images from Waterloo, DIV2K, Flickr2K and BSD400. Gaussian noise levels were sampled in the interval **[5, 50]**.

For the Noise2VST stage, the denoiser was frozen and used inside a blind-spot optimization procedure. Per-image optimization used overlapping **64 × 64** patches and Adam optimization.

## Results

### Standard Gaussian denoising — Set12

| Model | σ = 15 | σ = 25 | σ = 50 |
|---|---:|---:|---:|
| DRUNet | 33.27 | 30.94 | 27.90 |
| SCUNet | **33.43** | **31.12** | **28.06** |
| SCUNet2 | 33.19 | 30.90 | 27.81 |

SCUNet2 does **not** outperform SCUNet on the standard Gaussian benchmark.

### Synthetic Poisson–Gaussian noise with GAT

| Method | PSNR |
|---|---:|
| DRUNet + GAT | 29.25 dB |
| SCUNet + GAT | 27.49 dB |
| SCUNet2 + GAT | **29.27 dB** |

SCUNet2 + GAT obtains the highest PSNR in this experiment, with only a **0.02 dB** difference relative to DRUNet + GAT. This result is therefore treated as a small empirical difference rather than evidence of general superiority.

### Learned spline transform

On Set12 with Poisson noise (`a = 50`):

- DRUNet + Spline: **29.90 dB**
- SCUNet2 + Spline: **29.92 dB**

## Main takeaways

The experiments suggest that transformer-based denoisers can be integrated into a weakly-supervised denoising pipeline, but the gains depend on the noise regime. The project also highlights the computational cost of per-image optimization: the original protocol uses up to 2,000 iterations, while the study suggests that fewer iterations may sometimes be sufficient.

## Limitations

- gains over strong CNN baselines are modest in the reported Poisson–Gaussian experiments;
- SCUNet2 is weaker than SCUNet on the standard Gaussian Set12 benchmark;
- real-image evaluation is mainly qualitative because clean references are unavailable;
- per-image optimization remains computationally expensive.

## Repository status

This repository currently documents the research work and its experimental results. The original academic implementation will only be added where it can be shared cleanly and reproducibly.

## Portfolio

[View the visual case study in my ML/AI Engineering portfolio](https://portfolio-git-v2-premium-esanonpro-8143.vercel.app/research/image-denoising-transformers).

## Authors

Elie Gislain Sanon · Mahama Stéphane Gbane  
Tutor: Sébastien Herbreteau  
ENSAI · 2023–2024
