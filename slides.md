---
theme: seriph
addons:
  - "@twitwi/slidev-addon-ultracharger"
addonsConfig:
  ultracharger:
    inlineSvg:
      markersWorkaround: false
    disable:
      - metaFooter
      - tocFooter
background: /logo/mountain.jpg
highlighter: shiki
routerMode: hash
lineNumbers: false
duration: 80min

css: unocss
title: Deep Learning
subtitle: Generative Modeling
date: 23/11/2026
venue: HSE
author: Alexey Boldyrev, Maksim Karpov
---

# <span style="font-size:28.0pt" v-html="$slidev.configs.title?.replaceAll(' ', '<br/>')"></span>
# <span style="font-size:32.0pt" v-html="$slidev.configs.subtitle?.replaceAll(' ', '<br/>')"></span>
# <span style="font-size:18.0pt" v-html="$slidev.configs.author?.replaceAll(' ', '<br/>')"></span>

<span style="font-size:18.0pt" v-html="$slidev.configs.date?.replaceAll(' ', '<br/>')"></span>

<div class="abs-tl mx-5 my-10">
  <img src="/logo/FCS_logo_full_L.svg" class="h-18">
</div>

<div class="abs-tr mx-5 my-5">
  <img src="/logo/DSBA_logo.png" class="h-28">
</div>

<style>
  :deep(footer) { padding-bottom: 3em !important; }
</style>

---
src: ./slides/0_introduction.md
---

---
src: ./slides/0b_roadmap.md
---

---
src: ./slides/1_GANs.md
---

---
src: ./slides/1b_gan_practice.md
---

---
src: ./slides/2_WGANs.md
---

---
src: ./slides/2b_wgan_intuition.md
---

---
src: ./slides/beyond_GANs.md
---

---
src: ./slides/3b_vae_flows.md
---

---
src: ./slides/3c_diffusion_practice.md
---

---
src: ./slides/3d_guidance_evaluation.md
---

---
src: ./slides/3e_sota_2026.md
---

---
src: ./slides/3f_integration_experts.md
---

---
src: ./slides/0_end.md
---