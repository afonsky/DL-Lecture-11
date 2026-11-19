---
zoom: 0.96
---

# A GAN Iteration: Two Updates, Two Different Targets

<div class="grid grid-cols-2 gap-7 text-sm">
<div class="rounded-lg p-4 bg-red-50 dark:bg-red-900">

### 1 · Train D: real → 1, fake → 0

```python
opt_D.zero_grad()
fake = G(z).detach()       # no gradient into G
loss_D = (bce(D(real), ones) +
          bce(D(fake), zeros)) / 2
loss_D.backward()
opt_D.step()
```

#### **Detach the fake batch:**<br> this update belongs to D
</div>
<div class="rounded-lg p-4 bg-blue-50 dark:bg-blue-900">

### 2 · Train G: generated → “real”

```python
opt_G.zero_grad()
fake = G(z)                # keep the gradient path
loss_G = bce(D(fake), ones)
loss_G.backward()
opt_G.step()
```

#### **Reuse label 1:** the non-saturating objective rewards fooling D
</div>
</div>

<div class="grid grid-cols-3 gap-4 mt-5 text-center">
<div class="rounded p-3 bg-gray-50 dark:bg-gray-800">Use <strong>raw logits</strong> with `BCEWithLogitsLoss`</div>
<div class="rounded p-3 bg-gray-50 dark:bg-gray-800">Keep <strong>separate optimizers and gradients</strong></div>
<div class="rounded p-3 bg-gray-50 dark:bg-gray-800">Generate fresh noise after D's update</div>
</div>

<!-- <div class="mt-5 rounded p-3 text-center bg-yellow-50 dark:bg-yellow-900">

**Memory hook:** D update → `detach()` · G update → keep graph + target `ones`
</div> -->

<span class="refs">Code pattern: [d2l.ai §20.1.4, Training](https://d2l.ai/chapter_generative-adversarial-networks/gan.html#training) · Cross-entropy: **See Lecture 2** · Adam: **See Lecture 8** · [S. Raschka, practical GAN modifications](https://sebastianraschka.com/pdf/lecture-notes/stat453ss21/L18_gan__slides.pdf)</span>

<!--
Two and a half minutes. Walk left then right. The discriminator sees real examples with target 1 and generated examples with target 0. Detaching fake prevents the D loss from accumulating gradients for G; D still receives gradients through its own parameters.

For G, recompute the generated batch and D's output after D has changed. Keep the graph from D's output back through fake into G. The target is 1 even though the sample is generated: this is the non-saturating “fool the discriminator” objective, not a mislabeled dataset. The parameters of D are not updated by opt_G; in a fuller implementation one can freeze D's gradients for efficiency, but the slide shows the minimal course pattern.

The code uses bce = nn.BCEWithLogitsLoss(), so D returns logits and no sigmoid is applied in model code. ones/zeros are reshaped to the output shape in d2l. This is an excerpt, not a self-contained training script.
-->

---
zoom: 0.85
---

# Mode Collapse: Fooling D Is Not Covering the Data

<figure>
  <img src="/img/lecture11/mode_collapse.svg" class="h-82 mx-auto">
  <figcaption style="color:#b3b3b3ff; font-size: 11px; position: relative; top: 10px">Image source: original toy example computed for this lecture after <a href="https://d2l.ai/chapter_generative-adversarial-networks/gan.html">d2l.ai §20.1, the GAN two-sample-test framing</a>; not a trained model result</figcaption>
</figure>

<div class="grid grid-cols-2 gap-6 mt-4 text-center">
<div class="rounded-lg p-3 bg-orange-50 dark:bg-orange-900">

### **Fidelity** — are generated samples plausible?
</div>
<div class="rounded-lg p-3 bg-blue-50 dark:bg-blue-900">

### **Coverage** — do they represent all important modes?
</div>
</div>

<div class="mt-3 text-center">A beautiful sample grid can still hide <strong>missing classes, styles, patients or scenarios</strong>.</div>

<span class="refs">Failure-mode checklist: [PyTorch DCGAN tutorial](https://docs.pytorch.org/tutorials/beginner/dcgan_faces_tutorial.html) · Quality/coverage metrics: [Kynkäänniemi et al. (2019)](https://arxiv.org/abs/1904.06991)</span>

<!--
Ninety seconds. Generator A produces plausible points around the right-hand mode but never covers the left-hand mode. Generator B represents both. This is a constructed scatter example, not the result of training a GAN.

The key diagnostic is to inspect diversity across the whole sample set and to compare against held-out real data, not to admire the single best image. Mode collapse can affect a subclass or a safety-critical scenario rather than an obvious semantic class. Evaluation later separates precision-like quality from recall-like coverage.

Ask: would a discriminator score of 0.5 on this finite batch prove that the full data distribution was learned? No. Finite samples, a limited discriminator and unseen modes matter.
-->

---
zoom: 0.9
---

# DCGAN: A Noise Vector Becomes a 64×64 RGB Image

<figure>
  <img src="/img/lecture11/dcgan_shapes.svg" class="h-80 mx-auto">
  <figcaption style="color:#b3b3b3ff; font-size: 11px; position: relative; top: 10px">Image source: shapes recomputed from <a href="https://d2l.ai/chapter_generative-adversarial-networks/dcgan.html#the-generator">d2l.ai §20.2.2, The Generator</a>; diagram drawn for this lecture</figcaption>
</figure>

<div class="grid grid-cols-[1.2fr_1fr] gap-5 mt-3 text-sm">
<div class="rounded p-3 bg-blue-50 dark:bg-blue-900">

#### **Generator block:** transposed convolution → batch normalization → ReLU  
#### **Output:** `tanh` → pixels in **[−1, 1]**
</div>
<div class="rounded p-3 bg-orange-50 dark:bg-orange-900">

#### **Match the data range:**  
#### `Normalize(mean=0.5, std=0.5)`<br> maps **[0, 1] → [−1, 1]**
</div>
</div>

<span class="refs">Transposed convolution and shape checks: **See Lecture 10** · Batch normalization: **See Lecture 5** · [d2l.ai §20.2.1, Pokémon preprocessing](https://d2l.ai/chapter_generative-adversarial-networks/dcgan.html#the-pokemon-dataset)</span>

<!--
Two minutes. Read the tensor shapes from left to right. The first transposed convolution uses kernel 4, stride 1 and padding 0, mapping 1×1 to 4×4. Each following standard block uses kernel 4, stride 2 and padding 1, doubling height and width. Channels decrease 512 → 256 → 128 → 64, and the final layer emits 3 channels at 64×64.

The figure omits the batch dimension. d2l's input has shape [1,100,1,1] and its output is [1,3,64,64]. Batch normalization and ReLU follow the intermediate transposed convolutions; the output instead uses tanh.

Normalization is not cosmetic: real images and generated images should use the same numeric convention. In the original d2l example, images are resized to 64×64 and each channel is normalized with mean and standard deviation 0.5. No model was trained for this slide.
-->
