---
layout: center
---
# Beyond GANs: Other Generative Families

---
zoom: 0.9
---

# Variational Autoencoders (VAE)

<div class="grid grid-cols-[5fr_4fr] gap-8">
<div>

Introduced by [Kingma & Welling (2013)](https://arxiv.org/abs/1312.6114)

**Key idea**: encoder–decoder with probabilistic latent space
$$\mathcal{L}_\text{ELBO} = \underbrace{\mathbb{E}_{q_\phi(z|x)}[\log p_\theta(x|z)]}_{\text{reconstruction}} - \underbrace{D_\text{KL}(q_\phi(z|x) \| p(z))}_{\text{regularization}}$$

* $q_\phi(z|x)$: **encoder** — maps data → Gaussian $\mathcal{N}(\mu, \sigma^2)$ in latent space
* $p_\theta(x|z)$: **decoder** — maps latent code → data
* $p(z) = \mathcal{N}(0, I)$: prior on latent space

</div>
<div>

<br>
<br>
<br>

```
         Encoder            Decoder
x ──→ [μ, σ] ──→ z ~ N(μ,σ²) ──→ x̂

Loss = reconstruction + KL penalty

Reparametrization trick:
  z = μ + σ · ε,  ε ~ N(0,1)
  (makes gradient flow through z)
```

</div>
</div>

<br>

**Pros**: explicit (lower-bound) likelihood, smooth latent space
<br>

**Cons**: blurry samples (Gaussian decoder assumption)

---
zoom: 0.9
---

# Normalizing Flows

<div class="grid grid-cols-[5fr_4fr] gap-8">
<div>

**Key idea**: learn a **bijective** (invertible) mapping $f_\theta: \mathcal{Z} \to \mathcal{X}$

$$x = f_\theta(z), \quad z \sim p_z(z)$$
$$\log p_X(x) = \log p_Z(f_\theta^{-1}(x)) + \log \left| \det \frac{\partial f_\theta^{-1}}{\partial x} \right|$$

* Exact likelihood via **change of variables**
* Invertibility: generation *and* density evaluation  
* Architectures: RealNVP, Glow, Neural Spline Flows, MAF


</div>
<div>

<br>

```
z ~ N(0,I)  ←──  [invertible layers f₁, f₂, ..., fₙ]  ──→  x

Training: maximize log p_X(x)
          = log p_Z(z) + Σ log|det Jᵢ|

Inference (generation):
  sample z, apply f₁∘f₂∘...∘fₙ

Inference (density):
  apply fₙ⁻¹∘...∘f₁⁻¹(x), evaluate p_Z
```
</div>
</div>


**Pros**: exact $\log p(x)$, fast generation, stable training
<br>

**Cons**: $\dim(\mathcal{Z}) = \dim(\mathcal{X})$, Jacobian constraint limits architecture


---
zoom: 0.95
---

# Diffusion Models

<div class="grid grid-cols-[6fr_4fr] gap-8">
<div>

Introduced by [Ho et al. (DDPM, 2020)](https://arxiv.org/abs/2006.11239);<br> Theoretical foundation: [Song et al. (Score SDE, 2021)](https://arxiv.org/abs/2011.13456)

**Forward process**: gradually add Gaussian noise over $T$ steps
$$q(x_t | x_{t-1}) = \mathcal{N}(x_t; \sqrt{1-\beta_t}\, x_{t-1},\, \beta_t I)$$

**Reverse process**: learn to denoise
$$p_\theta(x_{t-1}|x_t) = \mathcal{N}(x_{t-1};\, \mu_\theta(x_t, t),\, \Sigma_\theta(x_t, t))$$

**Training objective** (simplified):
$$\mathcal{L} = \mathbb{E}_{t, x_0, \epsilon} \left[ \| \epsilon - \epsilon_\theta(x_t, t) \|^2 \right]$$

**Pros**: state-of-the-art sample quality, stable training  
**Cons**: slow generation ($T \approx 1000$ steps), high memory

</div>
<div>

<br>

```
Forward (destroy data):
x₀ → x₁ → x₂ → ... → xT ~ N(0,I)

Reverse (generate data):
xT → xT-1 → ... → x₁ → x₀
  (learned denoiser at each step)

DDIM sampler: accelerated, ~50 steps
Consistency models: single step
```

<br>

</div>
</div>

---
zoom: 1.1
---

# Summary: Generative Modeling

<br>

### What we covered today:<br>

* **Generative modeling** = learn $p_\theta(x) \approx p_\text{data}(x)$
* **Latent space**: compressed hidden representation of data
* **GANs**: adversarial game → implicit distribution, fast sampling
* **WGAN**: Wasserstein loss → more stable training, better gradients
* **VAE**: ELBO, probabilistic encoder, smooth latent space
* **Flows**: exact likelihood via invertible maps
* **Diffusion**: high quality, iterative denoising