---
permalink: /
title: "Wenqi Ye"
author_profile: true
redirect_from: 
  - /about/
  - /about.html
---

I am an M.S. student in Computer Engineering at the **University of California, San Diego**, advised by [Prof. Zhijian Liu](https://zhijianliu.com). I also work as an **Inference Engineer at [Inco AI](https://inco.ai)** on [Splash](https://github.com/incoai/splash), a local inference engine for LLMs and video generation on Apple silicon.

I work on making large models run fast and fit in memory: speculative decoding, recurrent-state (Gated DeltaNet / SSM) inference, sparse attention for video diffusion, and GPU kernels in Metal and CUDA. I received my B.Eng. in Electronic Information Engineering from the joint program of UESTC and the University of Glasgow.

**I am looking for ML systems / inference engineering internships.** [CV (PDF)](files/Wenqi_Ye_Resume.pdf) · [Email](mailto:w4ye@ucsd.edu) · [GitHub](https://github.com/Qiqi723)

News
======

- **Nov 2026** — Our [MiniMax-H3 showcase](https://h3-showcase.pages.dev/) shows text/image/video-to-video with audio running on a single 48 GB MacBook Pro.
- **Oct 2026** — Integrated [ReplaySSM](https://arxiv.org/abs/2610.13778) into Splash's Gated DeltaNet decode path.
- **Sep 2026** — Started my M.S. at UC San Diego and joined Inco AI as an Inference Engineer.
- **Feb 2026** — [DiffuCell](https://doi.org/10.1016/j.engappai.2025.113507) published in *Engineering Applications of Artificial Intelligence*.
- **Sep 2025** — My first-author paper on multi-active-IRS wireless networks appeared at IEEE PIMRC 2025.

Work Experience
======

### [Inco AI](https://inco.ai) — Inference Engineer
*Sep 2026 – Present · Project: [Splash](https://github.com/incoai/splash)*

#### ReplaySSM for Gated DeltaNet speculative decoding

Hybrid models such as Qwen3.5/3.8 mix full attention with Gated DeltaNet (GDN) layers, whose recurrent state is a large matrix per head. During speculative decoding the target model verifies a block of draft tokens and may reject a suffix, so the state must be recoverable. Splash's baseline stores the full GDN state directly, with two state cells per lane: one committed, one being written. I integrated [ReplaySSM](https://arxiv.org/abs/2610.13778), which instead keeps **one checkpoint plus the accepted rank-1 updates** (k, u, α) and simply drops the records of rejected drafts.

Setup: Qwen3.5-4B target with a DFlash2 draft, FP32 GDN state, 8-row verify blocks, Apple M4 (16 GB).

- **Single checkpoint.** Accepted records are folded into the checkpoint inside the next round's fused GDN kernel, so there is no separate full-state flush pass after acceptance. That old pass cost 0.98 ms at batch 1 and 4.12 ms at batch 4 and was purely bandwidth-bound (about 93–98 GiB/s).
- **Correctness.** A bug that first looked like numerical drift at larger record windows turned out to be a cross-threadgroup race on the record ring: a flush round overwrote slot 0 while other GDN layers were still reading it. I fixed it by writing tentative records to a separate staging tail and promoting only the accepted prefix after all layers finish. Afterwards, token traces matched the baseline on **1,920/1,920 rows** across clean ABBA runs at batch 1–4, record windows 8/16/32/64 all passed strict traces, and the full engine regression suite passed.
- **Speed.** Full decode cycles are on par with the baseline (B1: 52.73 → 52.93 ms; B4: 188.56 → 188.86 ms), within the ~1 ms run-to-run noise. GDN is only about 3.4% of a Qwen3.5-4B cycle, so the main gain is memory, not speed.
- **Memory.** Per-lane GDN state drops from 141 MiB to 92 MiB. Net of the extra record ring and workspace, the reported total falls by **162 MiB at batch 4**. My capacity model predicted 161.81 MiB (measured 161.77 MiB) and projects net savings for larger models:

| Model | Checkpoint per lane | Net saving at B4 | Net saving at B16 |
|---|---:|---:|---:|
| Qwen3.5-4B | 48 MiB | 162 MiB | 647 MiB |
| Qwen3.6-35B-A3B | 60 MiB | 202 MiB | 809 MiB |
| Qwen3.8-27B | 144 MiB | 491 MiB | 1.92 GiB |

- **Record window sweep.** Larger windows write the checkpoint less often, but each round must replay more records, so GDN time grows (B4: 6.6 ms at window 8 vs. 22.1 ms at window 64). Window 8 is the current choice; output-only replay is the next step to remove the O(window) re-scan.

#### MiniMax-H3 video generation with audio on Metal

Splash runs [MiniMax-H3](https://huggingface.co/MiniMaxAI/MiniMax-H3) text-, image- and video-conditioned video generation with sound on one Mac, using an int8 Metal engine. My work focuses on the quality models VDN-8 and Pika-6.

- **Training-free block-sparse attention.** Pika-6 runs dense attention at every clip length, and short VDN-8 clips are nearly dense. Following Sol-H3, I score 64-token block pairs from pooled queries and keys, keep blocks above a per-query-block threshold (mean + τ·std), always keep keyframe, text and audio tokens dense, and approximate skipped blocks with centroids merged by log-sum-exp. By our estimate this cuts Pika-6 DiT compute from about 95 to about 38 PFLOP per 15 s clip at 80% sparsity; measurements are in progress.
- **Block-level cancellation.** Cancel used to be checked only between denoising steps, so a cancelled 15 s job could hold the GPU for about 220 s. The DiT now checks before each of its 50 blocks and the VAE before each temporal clip. It waits for in-flight GPU work and returns weight-stream slots so the SSD reader cannot deadlock, which bounds the wait to about one block (~4 s).
- **Results on a 48 GB M5 Pro** (from the [showcase](https://h3-showcase.pages.dev/); peak memory of the largest process):

| Task | Splash | Other Mac apps |
|---|---|---|
| Reference image, 832×480, 15 s | Pika-6: 967 s, 10.1 GB | FreeVideo: 1,834 s, 42.2 GB |
| Reference voice, 832×480, 5 s | Pika-6: 582 s, 9.1 GB | mlx-serve: 1,447 s, 38.7 GB |
| Reference video, 832×480, 10 s | Pika-6: 1,908 s, 12.9 GB | mlx-serve: 2,842 s, 46.3 GB |
| Text to video, 1344×768, 10 s | VDN-8: 1,275 s, 12.7 GB | FreeVideo: 1,645 s, 44.4 GB |
| First frame to video, 1344×768, 8 s | VDN-8: 1,134 s, 11.1 GB | FreeVideo: 1,477 s, 43.4 GB |

### Transcomputing — AI Inference Engineer
*2026*

Deployed the Qwen3-VL-4B vision encoder on the company's proprietary AI accelerator (Step4Edge / TopsInference stack).

- Implemented the full ViT execution path: patch and position embedding, layer normalization, multi-head attention, MLP, transformer blocks, spatial downsampling and the multimodal projector.
- Extended checkpoint conversion and loading for the vision weights (`patch_embed`, `position_embed`, `vblk.*`, `downsample`, `mm_model.s_proj`).
- Integrated context-stage multimodal fusion: `pixel_values` of shape [B, 720, 720, 3] become image embeddings [B, 529, H], placed at the image-token positions of the text sequence.
- Built an end-to-end validation and debugging pipeline across model construction, checkpoint preparation, generation, the model runner and example scripts.

Research Experience
======

### Performance evaluation of EAGLE speculative decoding on vLLM
*Research Assistant · Feb 2026 – May 2026*

- Reproduced EAGLE v1/v2/v3 and studied draft-target generation, tree attention, acceptance/rejection and how the versions differ.
- Built an EAGLE-3 evaluation pipeline on vLLM 0.10.1 with DeepSeek-R1-Distill-Llama-8B and its draft model.
- Evaluated 12 tree configurations on 32 HumanEval problems at batch sizes 1/8/16/32/64/128 with up to 32 proposal nodes per iteration; analyzed TTFT, TPOT, output tokens/s, speedup and acceptance to find high-throughput settings and negative-return breakpoints.
- Profiled EAGLE-3 with PyTorch Profiler and Nsight Systems down to CUDA kernels and API calls to isolate overhead in proposal, verification and acceptance.
- Built an analytical speedup model (draft proposal, target verification, rejection sampling, runtime overhead) and validated it on HumanEval, MBPP, GSM8K, MATH, MT-Bench and Alpaca.

### [DiffuCell](https://doi.org/10.1016/j.engappai.2025.113507): cell localization with diffusion models
*Algorithm Engineer · Advisor: [Prof. Ruxiang Xu](https://www.med.uestc.edu.cn/info/1310/2343.htm), UESTC · Nov 2022 – May 2023*

- Reformulated density-map regression as recovering narrow-kernel density maps from Gaussian noise, which avoids background-noise accumulation and unstable gradients under sparse supervision.
- Designed Attention Map Guidance: intermediate U-Net self-attention maps combined with Gaussian-blurred masks guide denoising toward low-contrast and ambiguous cells.
- Fused repeated samples with SSIM-based ranking and a dynamic rejection radius; outperformed baselines on three datasets with **93.6% precision and 91.5% F1** on the Nuclei grading test set.

![cell](./images/cell.png)

### Earlier research in wireless communications

**Multi-active-IRS aided wireless networks** · Advisors: [Prof. Weidong Mei](https://faculty.uestc.edu.cn/meiweidong/zh_CN/index.htm) and Prof. Zhi Chen, UESTC · from Mar 2024

- Modeled an all-active intelligent reflecting surface (IRS) network and showed in simulation where active IRSs outperform passive ones.
- Designed an IRS-user association algorithm for the downlink that approximates the optimum with polynomial complexity. Published at IEEE PIMRC 2025 (first author).

![MAP4](./images/MAP4.png)

**RF-3DGS: wireless channel modeling** · Advisor: [Prof. Haijian Sun](https://esi.uga.edu/), University of Georgia · from May 2025

- Represented the radio-frequency field by combining visual and radio data, with an active view-selection mechanism that helps Gaussian Splatting models in both sparse- and dense-view regimes.

![3DGS](./images/3DRF.png)

Publications
======

- **Wenqi Ye**, Dong Wang, Weidong Mei, and Zhi Chen. "Multi-Active-IRS-Aided Wireless Network: Performance Analysis and IRS-User Association." *IEEE International Symposium on Personal, Indoor and Mobile Radio Communications (PIMRC)*, 2025. **First author.**
- Wei Liu, Chao Xu, Ying Yuan, **Wenqi Ye**, Huan Xiong, Wenqiao Qiu, Lili Guo, Xinda Li, XuLong Li, and Ruxiang Xu. "[Improving Cell Localization with Attention-Guided Diffusion Models](https://doi.org/10.1016/j.engappai.2025.113507)." *Engineering Applications of Artificial Intelligence*, vol. 165, 2026.

Honors
======

- Mathematical Contest in Modeling (MCM), Finalist (top 1%)
- UESTC Second-Class Scholarship (top 20%)
- UESTC Academic Progress Award (top 3%)

Skills
======

- **Languages:** Python, C++, Metal Shading Language, MATLAB
- **Inference systems:** Splash (Metal on Apple silicon), vLLM, TopsInference / Step4Edge
- **Topics:** speculative decoding (EAGLE, DFlash), Gated DeltaNet / SSM inference, video diffusion (DiT, VAE), sparse attention, quantization
- **Tools:** PyTorch Profiler, Nsight Systems, Metal shader validation, Git
