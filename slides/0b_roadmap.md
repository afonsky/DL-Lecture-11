---
hideInToc: true
zoom: 0.91
---

# Today: From Random Noise to Useful Generators

<div class="grid grid-cols-4 gap-4 text-center mt-8">
<div class="rounded-lg p-4 bg-blue-50 dark:bg-blue-900">
<div class="text-3xl font-bold text-blue-700 dark:text-blue-200">01</div>

### Model
data distribution → samples

**Likelihood or a learning signal?**
</div>
<div class="rounded-lg p-4 bg-orange-50 dark:bg-orange-900">
<div class="text-3xl font-bold text-orange-700 dark:text-orange-200">02</div>

### Train
GAN · VAE · flow · diffusion

**Watch failure modes, not only losses**
</div>
<div class="rounded-lg p-4 bg-green-50 dark:bg-green-900">
<div class="text-3xl font-bold text-green-700 dark:text-green-200">03</div>

### Control + evaluate
prompts · guidance · samplers

**Quality + coverage + speed**
</div>
<div class="rounded-lg p-4 bg-purple-50 dark:bg-purple-900">
<div class="text-3xl font-bold text-purple-700 dark:text-purple-200">04</div>

### Deploy in 2026
image · video · audio · 3D

**Cost + license + provenance**
</div>
</div>

<div class="mt-8 text-center text-xl">

**Guiding question:** how do we turn randomness into data we can **control, evaluate and trust**?
</div>

<span class="refs">Main source: [d2l.ai, Chapter 20: Generative Adversarial Networks](https://d2l.ai/chapter_generative-adversarial-networks/index.html) · Foundations: **See Lectures 2, 4, 5, 6, 8 and 10**</span>

<!--
Timing plan: 80 minutes total, including the separate 10-minute quiz.
- Opening and roadmap: 4 min.
- GAN mechanics, DCGAN and failure modes: 17 min.
- WGAN and WGAN-GP intuition: 7 min.
- VAE, flows and diffusion practice: 21 min.
- Guidance, fast sampling and evaluation: 10 min.
- Current systems, responsible deployment and model choice: 6 min.
- Closing: 1 min.
Teaching total: 66 min + 10 min quiz = 76 min, leaving 4 min for questions.

Default cuts if running late; every inherited slide file remains unchanged:
1. In the introduction, skip “Standard Technique Using Explicit Density”, the cave allegory and one of the two latent-arithmetic slides.
2. Skip the inherited discriminator/generator gradient derivations, the KL playground, and the WGAN theorem/dual-formulation derivations. Keep the GAN objective, Nash intuition, training problems and the new Wasserstein visual.
3. In “Beyond GANs”, compress the inherited VAE and flow formula slides; the new visual slides carry the mechanisms. Skip the inherited diffusion formula slide only after the new static and interactive denoising slides.
4. Skip the new flow-matching slide if necessary; it is enrichment for recognizing current model descriptions, not a quiz dependency.
5. Treat Stable Audio 3 and TRELLIS.2 as optional current-system examples. Keep the image and video examples.
6. Do not read the expert and further-reading tables aloud.

The quiz is external: shared/DL-L11_quiz.md. Main questions use only the guaranteed route, not optional cuts.
-->
