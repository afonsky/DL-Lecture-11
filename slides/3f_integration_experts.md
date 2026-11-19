---
zoom: 0.94
---

# Ethics of Generative Models

<div class="grid grid-cols-4 gap-4 mt-5 text-sm">
<div class="rounded-lg p-4 bg-blue-50 dark:bg-blue-900">

### Data + privacy
Check consent, copyright and license  
Test memorization and sensitive-data leakage  
**Synthetic ≠ automatically anonymous**
</div>
<div class="rounded-lg p-4 bg-orange-50 dark:bg-orange-900">

### People + misuse
Audit representation and harmful outputs  
Control impersonation, sexualized or deceptive content  
Provide appeal and human review
</div>
<div class="rounded-lg p-4 bg-purple-50 dark:bg-purple-900">

### Provenance
Record model/version, prompts, sources and edits  
Use signed provenance such as [**C2PA**](https://en.wikipedia.org/wiki/Content_Credentials) where appropriate  
Preserve metadata through the pipeline
</div>
<div class="rounded-lg p-4 bg-green-50 dark:bg-green-900">

### Operations
Moderate inputs and outputs  
Log incidents and evaluate on real use  
Monitor updates, drift and rollback paths
</div>
</div>

<div class="mt-7 rounded-lg p-4 text-center bg-yellow-50 dark:bg-yellow-900 text-lg">

**A watermark or provenance record is evidence about origin/history—not proof that the content is true, safe or authorized.**
</div>

<span class="refs">[C2PA specifications: certifying media source and history](https://c2pa.org/specifications/specifications/2.2/index.html) · GAN bias and privacy practice: [DeepLearning.AI GAN Specialization](https://www.deeplearning.ai/specializations/generative-adversarial-networks-gans) · Current data-provenance example: [Stable Audio 3](https://arxiv.org/abs/2605.17991)</span>

<!--
Ninety seconds. Keep this practical: quality metrics do not capture privacy, consent, bias or misuse. Synthetic data can leak training records or memorize a distinctive example; test this rather than assuming privacy from the word “synthetic.”

C2PA supplies technical standards for certifying source and history through signed provenance. It does not establish semantic truth, and provenance can be absent after an unsuitable export or re-upload. Treat it as one layer alongside access control, moderation, human review and incident response.

For course projects, require a model/version card, data/license notes, fixed prompts and seeds, an explicit failure gallery and a statement of who could be harmed. Do not ask students to generate deceptive depictions of real people.
-->

---
zoom: 0.94
---

# Choosing a Generative Model: Start from the Requirement

<div class="text-sm">

| Primary requirement | Useful starting point | What to measure / watch |
|---|---|---|
| Exact density evaluation | **Normalizing flow** | Jacobian cost, invertibility, likelihood on held-out data |
| Encode → manipulate → decode | **VAE** | Reconstruction, latent usefulness, ELBO versus sample quality |
| Fast one-pass implicit sampling | **GAN** | Oscillation, mode coverage, discriminator/generator balance |
| High-quality conditioned media | **Latent diffusion / flow matching** | Prompt success, coverage, sampler calls, autoencoder detail |
| Few-step / interactive generation | **Distilled or consistency model** | Quality after acceleration, end-to-end latency, teacher/protocol |

</div>

<div class="grid grid-cols-4 gap-3 mt-5 text-center text-sm">
<div class="rounded p-3 bg-blue-50 dark:bg-blue-900"><strong>1 · Output</strong><br>Data type, condition, control</div>
<div class="rounded p-3 bg-purple-50 dark:bg-purple-900"><strong>2 · Baseline</strong><br>Same data and prompt suite</div>
<div class="rounded p-3 bg-green-50 dark:bg-green-900"><strong>3 · Evidence</strong><br>Quality + coverage + speed + failures</div>
<div class="rounded p-3 bg-orange-50 dark:bg-orange-900"><strong>4 · Constraints</strong><br>Hardware + license + safety + provenance</div>
</div>

<div class="mt-4 text-center">The strongest family in a paper may not be the simplest system that meets your requirement.</div>

<span class="refs">Model-choice and controlled-comparison discipline: **See Lectures 2, 8 and 10** · [A. Karpathy, Training Recipe](https://karpathy.github.io/2019/04/25/recipe/) · [Y. Abu-Mostafa, Validation](https://work.caltech.edu/lectures.html)</span>

<!--
Ninety seconds. This table is a starting-point guide, not a universal ranking. Flows are the natural exact-density baseline; VAEs expose an encoder and probabilistic latent space; GANs retain fast one-pass generation; latent diffusion and flow matching dominate many current conditioned-media pipelines; distilled or consistency models address tight inference budgets.

Current systems combine ideas. Stable Audio 3 uses latent generation plus distillation and adversarial post-training; FLUX and LTX combine autoencoders, condition encoders and multi-stage samplers. A family label does not specify the complete implementation.

Use the four decisions along the bottom as the answer to “which model should I use?” Start with the smallest reproducible baseline and evaluate it on your own data and constraints. Validation selects; the final test reports.
-->

---
zoom: 0.75
---

# Learn More from the Experts

<div class="experts">

| Expert | Watch / read | Today's connection |
|---|---|---|
| Andrew Ng | [CS229: Generative Learning Algorithms](https://cs229.stanford.edu/notes2020spring/cs229-notes2.pdf) | generative versus discriminative learning; class-conditionals and likelihood |
| Yaser Abu-Mostafa | [Learning From Data, Lecture 13: Validation](https://work.caltech.edu/lectures.html) | selecting checkpoints, samplers and guidance without test-set snooping |
| Yann LeCun | [NYU Deep Learning: EBMs, autoencoders and GANs](https://atcold.github.io/NYU-DLSP21/) · [Energy-Based Models](https://cs.nyu.edu/~yann/research/ebm/) | latent-variable and adversarial views of generation |
| Yoshua Bengio | [Goodfellow, Bengio &amp; Courville, Deep Learning, Ch. 20](https://www.deeplearningbook.org/contents/generative_models.html) | explicit and implicit distributions; deep generative model foundations |
| Andrej Karpathy | [CS231n: Generative Models](https://cs231n.github.io/generative-models/) · [Training Recipe](https://karpathy.github.io/2019/04/25/recipe/) | VAE/GAN mechanics and disciplined implementation |
| Sebastian Raschka | [STAT 453: VAEs](https://sebastianraschka.com/blog/2021/dl-course.html#l17-variational-autoencoders) · [GANs](https://sebastianraschka.com/blog/2021/dl-course.html#l18-introduction-to-generative-adversarial-networks) · [Diffusion overview](https://magazine.sebastianraschka.com/p/ahead-of-ai-1-a-diffusion-of-innovations) | PyTorch implementations and visual intuition |
| Josh Starmer | [StatQuest: Maximum Likelihood for the Normal Distribution](https://www.youtube.com/watch?v=Dn6b9fCIUpM) · [Video index](https://statquest.org/video-index/) | likelihood and Gaussian intuition behind generative objectives |

</div>

<style>
.experts td, .experts th { padding-top: 0.3em; padding-bottom: 0.3em; }
</style>

<span class="refs">Main text: [d2l.ai, Chapter 20 — Generative Adversarial Networks](https://d2l.ai/chapter_generative-adversarial-networks/index.html), especially §20.1–20.2</span>

<!--
Self-study slide; do not spend lecture time reading the table. Ng supplies the classic generative/discriminative distinction; Abu-Mostafa the validation discipline; LeCun latent-variable, energy-based and adversarial perspectives; Bengio the broad probabilistic foundation; Karpathy and Raschka implementation-first explanations; Starmer a likelihood refresher.

The DeepLearning.AI GAN specialization is linked elsewhere in the deck as a practical course; its listed instructors are Sharon Zhou, Eda Zhou and Eric Zelikman, so it is not misattributed to Andrew Ng as its instructor. No unverified quotation is attributed to any expert.
-->

---
zoom: 0.92
hideInToc: true
---

# What to Remember

<div class="grid grid-cols-5 gap-3 text-center mt-6 text-sm">
<div class="rounded-lg p-4 bg-blue-50 dark:bg-blue-900">

### **Distribution**
Learn what the data looks like—not only a label
</div>
<div class="rounded-lg p-4 bg-red-50 dark:bg-red-900">

### **Signal**
GAN: D learns real/fake; G learns to fool D
</div>
<div class="rounded-lg p-4 bg-purple-50 dark:bg-purple-900">

### **Latent space**
VAE / flow / latent systems make randomness useful
</div>
<div class="rounded-lg p-4 bg-orange-50 dark:bg-orange-900">

### **Sampler**
Diffusion and flow turn noise into data through learned updates
</div>
<div class="rounded-lg p-4 bg-green-50 dark:bg-green-900">

### **Evidence**
Measure quality, coverage, control, cost and failure modes
</div>
</div>

<div class="mt-8 rounded-lg p-5 text-center bg-gray-50 dark:bg-gray-800 text-xl">

**A generator is a distribution + a sampling procedure.**  
A useful generator also needs **conditions, evaluation and responsible deployment**.
</div>

<div class="mt-6 text-center">Next: attention and transformer architectures—the backbones used by many current generative systems.</div>

<span class="refs">Review the model-family landscape and the evaluation protocol before the external quiz</span>

<!--
One minute. Close the technical loop: every family chooses how to represent a distribution and how to draw a sample. GANs use an adversarial signal; VAEs use probabilistic encoding and a bound; flows use invertibility; diffusion and flow matching learn reverse dynamics. Current products combine these ideas with autoencoders, condition encoders, samplers and post-training.

The separate ten-minute quiz follows outside Slidev. Stop here for the quiz; the inherited end slide and backup material follow.
-->

---
zoom: 1.1
hideInToc: true
---

# Further Reading and Practice

<div class="grid grid-cols-2 gap-8 text-sm">
<div>

### Main text and implementation
- [d2l §20.1: GANs and the 2D Gaussian example](https://d2l.ai/chapter_generative-adversarial-networks/gan.html)
- [d2l §20.2: DCGAN on Pokémon sprites](https://d2l.ai/chapter_generative-adversarial-networks/dcgan.html)
- [PyTorch DCGAN tutorial](https://docs.pytorch.org/tutorials/beginner/dcgan_faces_tutorial.html)
- [Hugging Face Diffusers](https://huggingface.co/docs/diffusers/index)

<br>

### Core papers
- [VAE](https://arxiv.org/abs/1312.6114) · [WGAN](https://arxiv.org/abs/1701.07875) · [WGAN-GP](https://arxiv.org/abs/1704.00028)
- [DDPM](https://arxiv.org/abs/2006.11239) · [Latent Diffusion](https://openaccess.thecvf.com/content/CVPR2022/html/Rombach_High-Resolution_Image_Synthesis_With_Latent_Diffusion_Models_CVPR_2022_paper.html)
- [Classifier-Free Guidance](https://arxiv.org/abs/2207.12598) · [Flow Matching](https://arxiv.org/abs/2210.02747) · [Consistency Models](https://arxiv.org/abs/2303.01469)
- [FID](https://arxiv.org/abs/1706.08500) · [Generative precision and recall](https://arxiv.org/abs/1904.06991)
</div>
<div>

### Current model documentation
- [GPT Image 2.6](https://developers.openai.com/api/docs/guides/image-generation) · [FLUX 3 Image Klein](https://bfl.ai/blog/flux-3-image-klein)
- [LTX-2.5](https://github.com/Lightricks/LTX-2) · [Stable Audio 3](https://arxiv.org/abs/2605.17991) · [TRELLIS.2](https://github.com/microsoft/TRELLIS.2)
- [Image arena](https://artificialanalysis.ai/image/leaderboard/text-to-image) · [Video arena](https://artificialanalysis.ai/video/leaderboard/text-to-video)
</div>
</div>

<!--
Leave this slide available for self-study. The main source is d2l Chapter 20; other sources supply the VAE, flow, diffusion, evaluation and current-system material that d2l's GAN chapter does not cover.

The seminar tasks are deliberately protocol-first. Do not select settings on the final test set or present generated samples as privacy-safe without a separate audit. The quiz is external and is not embedded in this deck.
-->
