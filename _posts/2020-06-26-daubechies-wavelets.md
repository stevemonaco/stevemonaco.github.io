---
layout: post
title: "Practical Calculation of Daubechies Wavelet and Scaling Functions"
date: 2020-06-26 00:15:00
description: A detailed, code-based explanation on how to recreate the frequently shown graphs of Daubechies Wavelets without wavelet library methods
tags:
 - C#
 - Signals
 - python
---

# Background
Daubechies Wavelets are a family of orthogonal wavelets which are recursively-defined. They are discrete functions where each level of approximation fills in all midpoints between calculated points across the defined range and, in the infinite limit, becomes continuous.

There are two equivalent nomenclatures: DX and dbX. The db4 (D8) wavelet contains 4 vanishing moments and 8 taps. These are always related by a factor of 2. From here on, only the dbX nomenclature will be used where the wavelet index is X. This blog post aims only to demonstrate the calculation of the wavelet and scaling function which are not well-explained in any of the many sources I've read. No theory, mathematical proofs, or description of the related wavelet transform will be given as those are covered elsewhere. The db4 wavelet will serve as the primary example here because the db2 wavelet is the dominant example in the literature.

# Visualization

## Number of points at various levels of approximation
\\[ P_n = P_0 * 2^L - (2^L - 1) \\]

|     | 0  | 1  | 2  | 3  | 4   | 5   | 6 
| db2 | 4  | 7  | 13 | 25 | 49  | 97  | 193
| db3 | 6  | 11 | 21 | 41 | 81  | 161 | 321
| db4 | 8  | 15 | 29 | 57 | 113 | 225 | 449
| db5 | 10 | 19 | 37 | 73 | 145	| 289 | 577
| db6 | 12 | 23	| 45 | 89 | 177	| 353 | 705

# Symbol Definitions
𝜑(r) - Scaling function at point r
𝜓(r) - Wavelet function at point r
\\( h_k \\) - The kth Daubechies scaling coefficient
N - number of Daubechies cofficients for the given wavelet
k - coefficient index
\\( a_k \\) - kth scaling coefficient
\\( b_k \\) - kth wavelet coefficient
L - level of approximation
\\( P_L \\) - Points at the Lth level of approximation

# Daubechies Coefficients
Daubechies coefficients for the scaling function are frequently given in texts and are given here as a table. The calculation may be covered in a future blog and involves a matrix solver with constraints ([example](https://simple.wikipedia.org/wiki/Daubechies_wavelet)). The constraints are necessary because there are multiple solutions. One constraint is the convention that large values are front-loaded which is known as the extremal phase. The sum of coefficients for a given wavelet index is often normalized to 1 and this is the case here.

## Scaling function coefficients

| db2 | 0.6830127  | 1.1830127  | 0.3169873  | -0.1830127  |             |           |           | 
| db3 | 0.47046721 | 1.14111692 | 0.650365   | -0.19093442 | -0.12083221 | 0.0498175 |           | 
| db4 | 0.32580343 | 1.01094572 | 0.89220014 | -0.03957503 | -0.26450717 | 0.0436163 | 0.0465036 | -0.01498699

## Wavelet function coefficients
Wavelet function coefficients are directly derived from the scaling function coefficients via the relationship \\( b_{k} = (-1)^{k}a_{N-1-k} \\)

| db2 | -0.1830127  | -0.3169873 | 1.1830127   | -0.6830127 |             |            |            | 
| db3 | 0.0498175   | 0.12083221 | -0.19093442 | -0.650365  | 1.14111692  | -0.4704672 |            | 
| db4 | -0.01498699 | -0.0465036 | 0.0436163   | 0.26450717 | -0.03957503 | -0.8922001 | 1.01094568 | -0.325803429

```cs
private static IEnumerable<float> ToWaveletCoefficients(IEnumerable<float> scalingCoefficients) =>
    scalingCoefficients.Select((coef, index) => index % 2 == 0 ? -coef : coef).Reverse();
```

# Calculation of Scaling Function Initial Values
The scaling function requires values at integer values of r within its given range


# Calculation of Scaling Function
The scaling function, 𝜑(r), is recursively-defined and requires the initial values from the previous step as well as the Daubechies coefficients. As mentioned in the intro, the function is not continuous until an infinite level of approximations are calculated, so we must settle for a discrete solution.



# Calculation of Wavelet Function
The wavelet function, 𝜓(r), is more straightforward given that the scaling function has already been calculated.

# Sources
Wavelets Made Easy - Yves Nievergelt (2001)
[Wavelet Browser](http://wavelets.pybytes.com/wavelet/db2/) - PyWavelets

# MathJax testing
\\[ \frac{1}{n^{2}} \\]