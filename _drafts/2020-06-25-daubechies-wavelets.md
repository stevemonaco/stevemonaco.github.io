---
layout: post
title: "Practical Calculation of Daubechies Wavelet and Scaling Functions"
date: 2020-06-25 20:27:00
description: A detailed, code-based explanation on how to recreate the frequently shown graphs of Daubechies Wavelets without wavelet library methods
tags:
 - C#
 - Signals
 - python
---

# Background
Daubechies Wavelets are a family of orthogonal wavelets which are recursively-defined. They are discrete functions where each level of approximation fills in all midpoints between calculated points across the defined range and, in the infinite limit, becomes continuous.

There are two equivalent nomenclatures: DX and dbX. The db2 (D4) wavelet contains 2 vanishing moments and 4 taps. These are always related by a factor of 2. From here on, only the dbX nomenclature will be used. This blog post aims only to demonstrate the calculation of the wavelet and scaling function which are not well-explained in any of the many sources I've read. No theory, mathematical proofs, or description of the related wavelet transform will be given as those are covered elsewhere.

# Symbol Definitions
𝜑(r) - Scaling function at point r
𝜓(r) - Wavelet function at point r
hx - The xth Daubechies coefficient

# Daubechies Coefficients
Daubechies coefficients are frequently given in texts and are given here as a table. The calculation may be covered in a future blog and involves a matrix solver with constraints ([example](https://simple.wikipedia.org/wiki/Daubechies_wavelet)). The sum of coefficients for a given family number is often normalized to 1 and this is the case here.

# Calculation of Scaling Function Initial Values



# Calculation of Scaling Function
The scaling function, 𝜑(r), is recursively-defined and requires the initial values from the previous step as well as the Daubechies coefficients.



# Calculation of Wavelet Function
The wavelet function, 𝜓(r), is more straightforward given that the scaling function has already been calculated.

# Sources
Wavelets Made Easy - Yves Nievergelt (2001)
[Wavelet Browser](http://wavelets.pybytes.com/wavelet/db2/) - PyWavelets

# MathJax testing
\\[ \fract{1}{n^{2}} \\]