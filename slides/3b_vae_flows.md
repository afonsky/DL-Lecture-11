---
zoom: 0.85
---

# VAE: Keep Randomness, Restore the Gradient Path

<figure>
  <img src="/img/lecture11/vae_reparameterization.svg" class="h-76 mx-auto">
  <figcaption style="color:#b3b3b3ff; font-size: 11px; position: relative; top: 10px">Image source: original diagram after <a href="https://arxiv.org/abs/1312.6114">Kingma &amp; Welling, Auto-Encoding Variational Bayes</a> and <a href="https://cs231n.github.io/generative-models/#variational-autoencoder">CS231n, VAE reparameterization</a></figcaption>
</figure>

<div class="grid grid-cols-[1fr_1.2fr] gap-6 mt-4 items-center">
<div>

**Encoder:** data → μ, σ  
**Decoder:** z → reconstruction / sample  
**Prior:** usually $\mathcal{N}(0,I)$
</div>
<div class="rounded-lg p-3 bg-gray-50 dark:bg-gray-800">

```python
eps = torch.randn_like(std)
z = mean + std * eps       # differentiable in mean, std
reconstruction = decoder(z)
```
</div>
</div>

<div class="mt-3 text-center">Training encodes real data; <strong>generation samples z from the prior</strong> and uses the decoder.</div>

<span class="refs">VAE + latent arithmetic in PyTorch: [S. Raschka, STAT 453 L17](https://sebastianraschka.com/blog/2021/dl-course.html#l17-variational-autoencoders) · Backpropagation: **See Lecture 3**</span>

<!--
Ninety seconds. The encoder does not emit one deterministic code: it emits parameters of a Gaussian approximate posterior. Sampling z directly introduces a random operation, so we rewrite z as mean + standard deviation times standard-normal noise. The random epsilon is an input; gradients with respect to mean and standard deviation remain available.

Separate two directions. During training, a real input goes through the encoder and decoder, with reconstruction plus a KL regularizer toward the prior. For unconditional generation after training, draw z from the standard-normal prior and decode it; there is no input image and no encoder pass.

The displayed code assumes std is already positive; implementations commonly predict log variance and transform it. It is an API fragment, not a complete VAE loss. The inherited VAE slide supplies the ELBO formula.
-->

---
zoom: 0.89
---

# Normalizing Flows: Learn an Invertible Change of Variables

<figure>
  <img src="/img/lecture11/normalizing_flow.svg" class="h-76 mx-auto">
  <figcaption style="color:#b3b3b3ff; font-size: 11px; position: relative; top: 10px">Image source: original toy distributions drawn for this lecture after <a href="https://cs231n.github.io/generative-models/">CS231n, Generative Models</a>; not a fitted flow</figcaption>
</figure>

<div class="grid grid-cols-3 gap-4 text-center text-sm mt-3">
<div class="rounded-lg p-3 bg-purple-50 dark:bg-purple-900">

#### **Sample**  
#### $z \to x$ through $f_\theta$
</div>
<div class="rounded-lg p-3 bg-orange-50 dark:bg-orange-900">

#### **Score data**  
#### $z \to x$ through $f_\theta^{-1}$<br> plus volume correction
</div>
<div class="rounded-lg p-3 bg-blue-50 dark:bg-blue-900">

#### **Pay for exactness**  
#### Invertibility + tractable Jacobian constrain the architecture
</div>
</div>

<span class="refs">RealNVP: [Dinh et al. (2016)](https://arxiv.org/abs/1605.08803) · Glow: [Kingma &amp; Dhariwal (2018)](https://arxiv.org/abs/1807.03039) · Exact versus lower-bound likelihood: compare the inherited VAE slide</span>

<!--
Ninety seconds. The same invertible network supports generation from z and density evaluation of x. Density evaluation needs the base log density and the Jacobian's log-determinant term; the figure calls this the volume correction. The inherited flow slide has the change-of-variables formula.

The scatter plots are only toy endpoint distributions, not the result of learning a normalizing flow. Exact likelihood does not mean unrestricted architecture: each layer must be invertible and its Jacobian practical to compute. Latent and data dimensions match in the standard formulation.

Contrast with VAE: VAE optimizes a lower bound on likelihood; a flow evaluates exact likelihood for the model. Both differ from a vanilla GAN, which offers samples without an explicit density.
-->
