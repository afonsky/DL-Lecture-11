---
zoom: 0.94
---

# Classifier-Free Guidance: Two Predictions, One Knob

<div class="grid grid-cols-[1fr_1.2fr] gap-8 mt-3 items-center">
<div>
<div class="rounded-lg p-4 bg-purple-50 dark:bg-purple-900 text-center">

### During training
Randomly **drop the condition** on some examples  
One denoiser learns both conditional and unconditional predictions
</div>
<div class="rounded-lg p-4 bg-blue-50 dark:bg-blue-900 text-center mt-4">

### During sampling
Evaluate with prompt and without prompt  
Combine the two predictions at every guided step
</div>
</div>
<div>
<div class="rounded-lg p-5 text-center bg-gray-50 dark:bg-gray-800 text-xl">

$\hat\epsilon = \epsilon_\text{uncond} + s\,(\epsilon_\text{cond}-\epsilon_\text{uncond})$

<div class="grid grid-cols-3 gap-2 mt-5 text-sm">
<div class="rounded p-2 bg-gray-100 dark:bg-gray-700"><span class="font-semibold">s = 0</span><br>ignore prompt</div>
<div class="rounded p-2 bg-blue-100 dark:bg-blue-800"><span class="font-semibold">s = 1</span><br>conditional only</div>
<div class="rounded p-2 bg-orange-100 dark:bg-orange-800"><span class="font-semibold">s &gt; 1</span><br>amplify prompt direction</div>
</div>
</div>
<div class="mt-4 rounded p-3 bg-yellow-50 dark:bg-yellow-900 text-center text-sm">

#### **Trade-off:** stronger guidance can improve prompt adherence but reduce diversity or introduce artifacts. A naïve implementation uses **two denoiser evaluations per step**.
</div>
</div>
</div>

<span class="refs">Source: [Ho &amp; Salimans, Classifier-Free Diffusion Guidance](https://arxiv.org/abs/2207.12598) · Conditioning and validation choices: **See Lecture 2**</span>

<!--
Two minutes. Classifier-free guidance avoids training a separate noisy-image classifier. During training, the same model sees both present and dropped conditions. At sampling, compute unconditional and conditional predictions and extrapolate from unconditional toward and beyond conditional.

At s=0 the prompt disappears; at s=1 the formula reduces to the conditional prediction. Values above 1 amplify the difference. There is no universally best scale: it changes prompt adherence, sample diversity and visual artifacts. Distilled or guidance-free variants may change the usual two-evaluation cost, so inspect the actual pipeline.

CFG is a sampling choice, not a new training loss and not a transformer mechanism. The formula uses noise predictions; equivalent formulations exist for other prediction parameterizations.
-->

---
zoom: 0.95
---

# Fast Sampling: Spend Fewer Denoiser Calls

<div class="grid grid-cols-4 gap-4 mt-5 text-sm">
<div class="rounded-lg p-4 bg-gray-50 dark:bg-gray-800">

### Ancestral DDPM
Many stochastic updates  
Simple reference sampler
</div>
<div class="rounded-lg p-4 bg-blue-50 dark:bg-blue-900">

### DDIM / ODE solvers
Reuse a trained model with a different update rule  
Fewer steps can trade quality
</div>
<div class="rounded-lg p-4 bg-purple-50 dark:bg-purple-900">

### Consistency / distillation
Learn a direct or few-step mapping from a teacher trajectory
</div>
<div class="rounded-lg p-4 bg-orange-50 dark:bg-orange-900">

### Adversarial post-training
Current systems can combine distillation and adversarial refinement
</div>
</div>

<div class="mt-7 rounded-lg p-4 text-center bg-yellow-50 dark:bg-yellow-900 text-lg">

**“One-step model” is a different inference contract, not a free improvement.**  
Report sampler + steps + guidance + precision + hardware + measured quality.
</div>

<div class="grid grid-cols-2 gap-5 mt-5 text-sm text-center">
<div class="rounded p-3 bg-gray-50 dark:bg-gray-800">More steps: more network calls, not automatically better</div>
<div class="rounded p-3 bg-gray-50 dark:bg-gray-800">Distillation: extra training to make inference cheaper</div>
</div>

<span class="refs">[DDIM](https://arxiv.org/abs/2010.02502) · [Consistency Models](https://arxiv.org/abs/2303.01469) · Current example: [Stable Audio 3, 8-step post-trained inference](https://arxiv.org/abs/2605.17991)</span>

<!--
Ninety seconds. There are two distinct routes to faster sampling. DDIM and numerical ODE solvers change the sampling procedure of an existing diffusion model; distillation, consistency training and adversarial post-training change or adapt the model to support fewer steps.

Do not quote “1000 steps” as a property of every diffusion model. The inherited slide uses it as a DDPM-era example. Actual current pipelines use varied step counts and multiple stages. Stable Audio 3 is a concrete 2026 example: its post-trained models use eight ping-pong sampling steps; that is not the same as claiming every current model uses eight.

Always measure quality under the new sampler. Fewer calls may change fidelity, diversity and prompt adherence. Report end-to-end latency, including encoder, decoder, upsampling and hardware, not only denoiser calls.
-->

---
zoom: 0.85
---

# Evaluation: Quality, Coverage, Alignment and Cost

<figure>
  <img src="/img/lecture11/evaluation_coverage.svg" class="h-62 mx-auto">
  <figcaption style="color:#b3b3b3ff; font-size: 11px; position: relative; top: 10px">Image source: original toy example after <a href="https://arxiv.org/abs/1904.06991">Kynkäänniemi et al., Improved Precision and Recall Metric for Assessing Generative Models</a>; not measured metric values</figcaption>
</figure>

<div class="grid grid-cols-4 gap-3 mt-3 text-xs">
<div class="rounded-lg p-3 bg-blue-50 dark:bg-blue-900">

### **FID ↓**
Distance between real/generated feature statistics  
**Not prompt alignment**
</div>
<div class="rounded-lg p-3 bg-green-50 dark:bg-green-900">

### **Precision + recall ↑**
Plausibility + distribution coverage in a feature space
</div>
<div class="rounded-lg p-3 bg-purple-50 dark:bg-purple-900">

### **Human / task study**
Blind pairwise preference, prompt success, downstream utility
</div>
<div class="rounded-lg p-3 bg-orange-50 dark:bg-orange-900">

### **Systems + safety**
Latency, memory, cost, memorization, bias and failure rate
</div>
</div>

<div class="mt-3 text-center text-sm">No single number proves that a generator is “good”. Use <strong>held-out real data</strong> and a fixed protocol.</div>

<span class="refs">FID: [Heusel et al. (2017)](https://arxiv.org/abs/1706.08500) · GAN evaluation practice: [DeepLearning.AI GAN Specialization](https://www.deeplearning.ai/specializations/generative-adversarial-networks-gans) · Validation: **See Lecture 2**</span>

<!--
Two minutes. Read the figure first: collapsed outputs can be plausible but miss a mode; scattered outputs can visit both regions but include many implausible points; balanced outputs improve both in this toy. These are conceptual precision/recall notions in a learned feature space, not detection precision/recall from Lecture 2.

FID compares Gaussian statistics of features extracted from real and generated samples; lower is better under a fixed implementation and sample protocol. It does not directly measure text-image alignment, downstream utility or per-image quality. Feature extractor, sample count and preprocessing matter.

For conditional generation, add task or human evaluation. Randomize and blind pairwise comparisons where possible. For synthetic training data, the decisive test may be downstream validation performance on real held-out examples. Memorization, privacy and bias need separate audits.
-->

---
zoom: 0.85
---

# A Minimal Evaluation Protocol for a Generator

<div class="grid grid-cols-5 gap-3 mt-5 text-sm text-center">
<div class="rounded-lg p-4 bg-blue-50 dark:bg-blue-900">
<div class="text-2xl font-bold">1</div>

### Freeze the task
Version, modality, conditions, prompt suite and real reference data
</div>
<div class="rounded-lg p-4 bg-purple-50 dark:bg-purple-900">
<div class="text-2xl font-bold">2</div>

### Fix the run
Seeds, sample count, sampler, steps, guidance, precision and post-processing
</div>
<div class="rounded-lg p-4 bg-green-50 dark:bg-green-900">
<div class="text-2xl font-bold">3</div>

### Measure outputs
Quality, coverage, alignment and downstream utility; inspect failures
</div>
<div class="rounded-lg p-4 bg-orange-50 dark:bg-orange-900">
<div class="text-2xl font-bold">4</div>

### Measure the system
End-to-end latency, VRAM, energy/cost and safety on your workload
</div>
<div class="rounded-lg p-4 bg-gray-50 dark:bg-gray-800">
<div class="text-2xl font-bold">5</div>

### Compare fairly
Same protocol, several runs/seeds, blinded preferences; final test untouched
</div>
</div>

<div class="mt-7 rounded-lg p-4 text-center bg-yellow-50 dark:bg-yellow-900 text-lg">

**Model checkpoint ≠ complete generative system.** The autoencoder, condition encoder, sampler and post-processing are part of the result.
</div>

<div class="mt-5 text-center">Select settings on validation data; report the final test once. <strong>See Lectures 2 and 8</strong></div>

<span class="refs">Model selection without data snooping: [Y. Abu-Mostafa, Learning From Data, Lecture 13](https://work.caltech.edu/lectures.html) · Debugging discipline: [A. Karpathy, A Recipe for Training Neural Networks](https://karpathy.github.io/2019/04/25/recipe/) · [Artificial Analysis methodology and leaderboards](https://artificialanalysis.ai/image/leaderboard/text-to-image)</span>

<!--
Ninety seconds. This is the practical closing workflow for the technical half. A model name is not enough to reproduce a generated result. Freeze all inputs and inference choices, then report several complementary metrics and system costs.

Use validation prompts and validation reference data to select sampler, guidance or checkpoint. Keep a final test suite untouched. Human evaluation should randomize left/right placement and hide model names. For stochastic generators, repeated runs matter; for human votes, report participation and uncertainty rather than only rank.

A current public arena is useful evidence about human preference under its own protocol, not a substitute for your task's prompt suite, licenses, deployment constraints or safety evaluation.
-->
