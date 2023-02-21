---
date created: 2022-08-11
date modified: 2022-02-14
tags: [img, art, backup, newdawn]
title: 🌈 GRADIENT MAP
---
## ❓ WHAT IS
-   **Adjustment Layer which binds a gradient to the values beneath it**.
> [Using Gradient Maps to Enhance Your Colors by vampbyte - CLIP STUDIO TIPS](https://tips.clip-studio.com/en-us/articles/2453)
-   **Maps the tonal range of an image to the colors of a gradient fill.**
> https://www.bcit.cc/cms/lib04/NJ03000372/Centricity/Domain/299/p6_howto_use_gradient_maps%2018.pdf


## ⚙️ PRINCIPLE
-   GRADIENT MAP PART codes from here: http://jsfiddle.net/kgd28nap/
## 🔧 IMPLEMENTATION

1.  The **color selector** web component supports selecting a more linear color.    
2.  Mapping the **color** **RGB** **average** to "color selector" results.
3.  Base64 encoding of images.
4.  Iteration processing to get the image "grayscale".
5.  Obtain target RGB values by "grayscale" mapping 👆.

## 💻 CODE FOR RESEARCH
https://codesandbox.io/s/newdawn-gradient-map-research-em5pyi  
https://em5pyi.csb.app  
🏠：repo http://192.168.31.233:8000/newdawn/gradient-map-research ()

## 📦 PACKAGES & DEPENDENCY
-   **📦 react-linear-gradient-picker**