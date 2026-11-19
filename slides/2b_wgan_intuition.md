---
zoom: 0.9
---

# Wasserstein Intuition: Keep a Useful Learning Signal

<figure>
  <img src="/img/lecture11/wasserstein_vs_js.svg" class="h-80 mx-auto">
  <figcaption style="color:#b3b3b3ff; font-size: 11px; position: relative; top: 10px">Image source: exact point-mass example computed for this lecture after <a href="https://arxiv.org/abs/1701.07875">Arjovsky et al., Wasserstein GAN</a></figcaption>
</figure>

<div class="grid grid-cols-3 gap-4 text-center text-sm mt-3">
<div class="rounded-lg p-3 bg-blue-50 dark:bg-blue-900">

#### **Critic, not probability classifier**  
#### No sigmoid at the output
</div>
<div class="rounded-lg p-3 bg-orange-50 dark:bg-orange-900">

#### **Constrain the critic's slope**  
#### 1-Lipschitz; gradient penalty replaces clipping
</div>
<div class="rounded-lg p-3 bg-green-50 dark:bg-green-900">

#### **Move mass closer**  
#### $W_1$ grows with separation in this toy
</div>
</div>

<span class="refs">WGAN-GP: [Gulrajani et al. (2017)](https://arxiv.org/abs/1704.00028) · Gradient descent and optimizer updates: **See Lecture 8**</span>

<!--
Ninety seconds. This is the simplest possible example: one real point mass at 0 and one generated point mass at δ > 0. Because they never overlap, Jensen–Shannon divergence stays at ln 2. Wasserstein-1 distance equals δ. Moving the generated mass therefore changes W₁ even while JS remains flat.

Do not overclaim: this does not prove that every WGAN run converges or that all gradients are always healthy. The result depends on critic training and the Lipschitz approximation. WGAN calls its discriminator a critic because its output is a real-valued score, not a sigmoid probability.

The original WGAN uses weight clipping; WGAN-GP instead penalizes the input-gradient norm at interpolated real/generated points. In practice, do not apply the original GAN's sigmoid+BCE loss unchanged to a WGAN critic.
-->
