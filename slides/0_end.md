---
layout: end
hideInToc: true
---

---
layout: center
---

<center>

# Backup Slides
</center>

---

# GAN Loss Function in Practice

* Discriminator
  * Maximize prediction probability of real as real and fake as fake
  * Remember maximizing log likelihood is the same as minimizing negative log likelihood (i.e., minimizing cross-entropy)
* Generator
  * Minimize likelihood of the discriminator to make correct predictions (predict fake as fake; real as real), which can be achieved by maximizing the cross-entropy
  * This doesn't work well in practice though because of gradient issues (zero gradient if the D makes correct predictions, which is not what we want for the G)
  * Better: flip labels and minimize cross entropy (force the D to output high probability for fake if an image is real, and high probability for real if an
image is fake)

---

# GAN Playground <a href="https://reiinakano.com/gan-playground/">[link]</a>

<iframe src="https://reiinakano.com/gan-playground/" width="1100" height="550" style="-webkit-transform:scale(0.8);-moz-transform-scale(0.8); position: relative; top: -65px; left: -120px"></iframe>

---
zoom: 0.9
---

# Which Generative Model Should You Use?

<br>

### Choose by your primary need

| Need | Best family |
|------|-------------|
| Fastest *inference* for simulation | GAN or Flow |
| Exact *likelihood* (anomaly, reweighting) | Normalizing Flow |
| Smooth, interpretable *latent space* | VAE |
| Highest *sample quality* (offline) | Diffusion |
| Embedded system / L1 trigger | VAE or tiny Flow |

---
zoom: 0.83
---

# Flow Matching: Learn Direction Along Noise → Data Paths

<figure>
  <img src="/img/lecture11/flow_matching.svg" class="h-82 mx-auto">
  <figcaption style="color:#b3b3b3ff; font-size: 11px; position: relative; top: 10px">Image source: original sampled-path schematic after <a href="https://arxiv.org/abs/2210.02747">Lipman et al., Flow Matching for Generative Modeling</a>; not a trained vector field</figcaption>
</figure>

<div class="grid grid-cols-2 gap-6 mt-3 text-center">
<div class="rounded-lg p-3 bg-purple-50 dark:bg-purple-900">

**Interpolate:** $x_t=(1-t)z+tx$  
Sample a time; ask the network for velocity
</div>
<div class="rounded-lg p-3 bg-orange-50 dark:bg-orange-900">

**Generate:** integrate the learned field from noise toward data  
Fewer / straighter steps are design goals, not guarantees
</div>
</div>

<span class="refs">Optional bridge to current flow-based systems · Rectified flow: [Liu et al. (2022)](https://arxiv.org/abs/2209.03003) · No transformer mechanism is introduced here</span>

<!--
Ninety seconds; optional in the timing plan. Diffusion commonly predicts noise; flow matching instead regresses a vector field that points along a probability path from a simple source to data. For the straight conditional path shown, x_t = (1-t)z + tx and the pair's target velocity is x-z.

The gray lines are arbitrary sampled noise/data pairings for illustration, not a claim that training finds an optimal-transport matching between finite samples. Training draws many data samples, noises and times. At generation, an ODE solver follows the learned field.

This vocabulary helps read current model cards: rectified flow, flow matching and diffusion are related continuous generative formulations, not just product names. The slide deliberately does not explain a diffusion transformer or attention.
-->

---
layout: center
---

<center>

# 2026 SOTA Generative Models
</center>

---
zoom: 0.9
---

# FLUX 3 Image Klein: Open-Weight Generation + Editing

<div class="grid grid-cols-[1.1fr_1fr] gap-7">
<div>
<div class="rounded-lg p-4 bg-purple-50 dark:bg-purple-900 text-center text-lg">

**Text + up to 10 reference images + layout boxes**  
↓  
**FLUX 3 Image Klein**  
↓  
**Generate · edit · preserve selected regions**
</div>

<div class="grid grid-cols-2 gap-3 mt-4 text-sm">
<div class="rounded p-3 bg-gray-50 dark:bg-gray-800">

### **5.6 B parameters**
Base for fine-tuning + **16-step distilled** variant
</div>
<div class="rounded p-3 bg-gray-50 dark:bg-gray-800">

### **Native 4K**
Generation, multi-reference editing and bounding-box control
</div>
</div>

<div class="mt-4 rounded p-3 bg-orange-50 dark:bg-orange-900 text-sm">

#### **Access:** FLUX Kommunity License; open weights, not Apache 2.0. Base supports LoRA fine-tuning.
</div>
</div>
<div class="text-sm">

### Practical specification

- **11.2 GB** generator weights in BF16; **5.6 GB** in FP8
- INT8 build targets NVIDIA GPUs with **8 GB VRAM**
- Vendor-reported distilled 1 MP generation: **~4 s on RTX 5090**, **<9 s on RTX 4070**
- Available through ComfyUI, Diffusers, Hugging Face and BFL API

<div class="mt-4 rounded p-3 bg-blue-50 dark:bg-blue-900">

#### **Why it matters here:** one current checkpoint combines latent generation, conditioning, editing and step distillation.
</div>
</div>
</div>

<span class="refs">Official sources checked November 2026: [release and model weights](https://bfl.ai/blog/flux-3-image-klein) · [16-step / 50-step variants and API specifications](https://docs.bfl.ai/release-notes) · Backbone internals are left to the dedicated transformer lecture</span>

<!--
Two minutes. FLUX 3 Image Klein is a current open-weight example rather than a universal quality winner. It combines text-to-image, editing and multi-reference generation, with base and step-distilled checkpoints.

The 4-second and under-9-second values are NVIDIA/vendor-reported GPU timings for the distilled model at 1 MP, 16 steps, one image per run and each GPU's quickest tested build. They are not end-to-end API latencies and were not measured here. The 11.2 GB and 5.6 GB figures are generator weight sizes, not guaranteed peak process VRAM. BFL's release notes say consumer GPUs down to RTX 3090; the detailed release describes the INT8 8 GB configuration.

Do not call the license Apache: FLUX 3 Image Klein uses the FLUX Kommunity License. Always read current weights and dependency licenses before commercial deployment. Attention/backbone architecture is deliberately not taught.
-->

---
zoom: 0.9
---

# LTX-2.5: Generate Video and Sound Together

<div class="grid grid-cols-[1.1fr_1fr] gap-7">
<div>
<div class="rounded-lg p-4 bg-blue-50 dark:bg-blue-900 text-center text-lg">

**Text / image / audio / video conditions**  
↓  
**Joint audio–video latent generation**  
↓  
**Frames + synchronized speech, effects and ambience**
</div>

<div class="grid grid-cols-2 gap-3 mt-4 text-sm text-center">
<div class="rounded p-3 bg-gray-50 dark:bg-gray-800"><strong>22 B</strong> generator parameters</div>
<div class="rounded p-3 bg-gray-50 dark:bg-gray-800">Up to <strong>4K</strong>, <strong>50 fps</strong></div>
<div class="rounded p-3 bg-gray-50 dark:bg-gray-800">Native <strong>multi-shot</strong> scenes</div>
<div class="rounded p-3 bg-gray-50 dark:bg-gray-800">Dev + distilled checkpoints; LoRA tooling</div>
</div>

<div class="mt-4 rounded p-3 bg-orange-50 dark:bg-orange-900 text-sm">

**Not “a GAN for each frame”:** temporal structure and both modalities are generated in one coordinated pipeline.
</div>
</div>
<div class="text-sm">

### Deployment specification

- Fast API: **6–20 s**, depending on resolution/FPS; Pro: **6–10 s**
- Basic open-weight quick-start download: **about 66 GiB** across generator, text encoder, VAEs and upscaler
- Quantization/offloading options; production DFR adds detailing passes
- Code: **Apache 2.0**; weights: **LTX-2.x Community License**, with separate model terms

<div class="mt-3 rounded p-3 bg-blue-50 dark:bg-blue-900">

**Evaluation must include motion, identity, audio sync and temporal consistency—not just one attractive frame.**
</div>
</div>
</div>

<span class="refs">Official sources: [LTX-2 repository, weights and pipelines](https://github.com/Lightricks/LTX-2) · [LTX-2.5 output support matrix](https://docs.ltx.io/models/ltx-2-5) · Current comparison context: [Artificial Analysis video leaderboard](https://artificialanalysis.ai/video/leaderboard/text-to-video)</span>

<!--
Two minutes. LTX-2.5 is selected as a current open-weight audio-video system, not claimed as the top of the human-preference video leaderboard. Its importance for this lecture is that the output is a coordinated multimodal sequence, with native multi-shot generation and synchronized sound.

The support matrix distinguishes variants and settings: fast supports up to 20 seconds at some resolution/FPS combinations, while Pro is listed for 6–10 seconds. Do not simplify this to “every output is 20 seconds at 4K/50 fps.” The 4K/50 fps combination is listed for 6–10 seconds.

The roughly 66 GiB figure is the repository's basic quick-start download, not a universal memory footprint or complete production package. Code and model weights have separate licenses. Hugging Face access may require accepting terms. No checkpoint was downloaded or run.

For evaluation, sample whole clips. Measure frame quality, action and camera prompt adherence, identity persistence, motion defects, speech intelligibility and audio-video synchronization, then end-to-end latency and memory.
-->

---
zoom: 0.95
---

# Stable Audio 3: Latent Diffusion for Long, Editable Audio

<div class="rounded-lg p-4 bg-purple-50 dark:bg-purple-900 text-center text-lg">

**Text + duration + optional edit mask** → semantic-acoustic encoder → **latent generator** → decoder → **44.1 kHz stereo**
</div>

<div class="grid grid-cols-3 gap-4 mt-5 text-sm">
<div class="rounded-lg p-4 bg-blue-50 dark:bg-blue-900">

### **Representation**
**4096× audio downsampling** into a 256-dimensional latent sequence  
Variable-length generation avoids padding every request to maximum length
</div>
<div class="rounded-lg p-4 bg-orange-50 dark:bg-orange-900">

### **Fast inference**
Flow-matching pretraining + distillation + adversarial post-training  
**8 ping-pong steps** in the post-trained configuration
</div>
<div class="rounded-lg p-4 bg-green-50 dark:bg-green-900">

### **Current family**
Small / medium / large; **small + medium weights released**  
Generation, continuation and multi-region inpainting
</div>
</div>

<div class="grid grid-cols-2 gap-5 mt-4 text-sm text-center">
<div class="rounded p-3 bg-gray-50 dark:bg-gray-800">Medium/large: up to <strong>6 min 20 s</strong>; small: up to <strong>120 s</strong></div>
<div class="rounded p-3 bg-gray-50 dark:bg-gray-800">Reported <strong>&lt;2 s on H200</strong> for supported lengths; trained on licensed + Creative Commons data</div>
</div>

<span class="refs">Source: [Stable Audio 3 technical report, May 2026](https://arxiv.org/abs/2605.17991) · [Official code and model repository](https://github.com/Stability-AI/stable-audio-3)</span>

<!--
Ninety seconds; optional current-system example. Stable Audio 3 combines nearly every mechanism in this lecture: an autoencoder for compression, a latent generative model, conditioning, distillation and adversarial post-training. It supports variable-length generation and inpainting rather than treating audio as a fixed image-shaped array.

The 44.1 kHz figure refers to stereo output. The autoencoder compresses the temporal sequence by 4096 and produces 256-dimensional latents. Small supports up to 120 seconds; medium and large support up to 380 seconds. Small and medium weights and the pipeline are released; do not imply that large is open-weight.

The under-2-second H200 claim is the authors' accelerated inference result for supported lengths, not a measurement on arbitrary hardware. The report's evaluation also shows that quality depends on duration and dataset; long-form prompt adherence can degrade. “Licensed and Creative Commons data” is a data-provenance design choice, not proof that every use is legally or ethically settled.
-->

---
zoom: 0.88
---

# TRELLIS.2: One Image → a Textured 3D Asset

<div class="grid grid-cols-[1fr_1.1fr] gap-7">
<div>
<div class="rounded-lg p-4 bg-green-50 dark:bg-green-900 text-center text-lg">

**Input image**  
↓  
**Compact structured 3D latents + generative shape/material pipeline**  
↓  
**Mesh + base color, roughness, metallic and opacity**
</div>

<div class="mt-4 rounded p-3 bg-blue-50 dark:bg-blue-900 text-sm">

**O-Voxel representation:** open surfaces, non-manifold geometry and enclosed interiors; output supports physically based rendering.
</div>
<div class="mt-4 rounded p-3 bg-orange-50 dark:bg-orange-900 text-sm">

**This is not a 2D image with a depth label:** the output is an editable 3D asset.
</div>
</div>
<div class="text-sm">

### Specification

- **4 B parameters**; sparse 3D VAE with **16× spatial downsampling**
- Published grid range: **512³–1536³**
- H100 shape + material generation: **~3 s at 512³**, **~60 s at 1536³**
- NVIDIA GPU with **≥24 GB** required by the reference setup; Linux + CUDA
- Model and code: **MIT License**; some dependencies have separate terms

<div class="mt-3 rounded p-3 bg-gray-50 dark:bg-gray-800">

Evaluate geometry, unseen views, topology, materials and export—not only the input view.
</div>
</div>
</div>

<span class="refs">Official sources: [TRELLIS.2 project](https://microsoft.github.io/TRELLIS.2/) · [code, checkpoints, hardware and license](https://github.com/microsoft/TRELLIS.2) · CNN spatial representations: **See Lectures 4 and 10**</span>

<!--
Ninety seconds; optional current-system example. TRELLIS.2 turns one image into a 3D asset with physically based materials. It uses compact structured latents and a generative shape/material pipeline; this lecture does not open its transformer-based backbone.

The resolution values describe the published voxel grid, not a rendered image's pixel dimensions. The ~3-second and ~60-second timings sum shape and material generation on an NVIDIA H100 under the authors' setup; they are not laptop timings or independent measurements. The reference setup requires at least 24 GB NVIDIA VRAM and Linux/CUDA.

The MIT statement covers Microsoft's model and code; dependencies have their own licenses. Evaluate unseen-side plausibility, topology, material maps and the exported GLB in a renderer. A single attractive render cannot establish 3D correctness.
-->
