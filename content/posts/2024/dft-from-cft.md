---
title: 'Deriving the Discrete Fourier Transform (DFT) from the Continuous-Time Fourier Transform'
date: 2024-10-06
tags:
  - fft
  - signal-processing
summary: "A derivation of the DFT starting from the Continuous Time Fourier Transform"
---
The general continuous-time Fourier Transform can be stated as follows,

$$X(f) = \int_{-\infty}^{+\infty} x(t) e^{-i 2\pi f t}\;dt$$

In a discrete system we can only sample the input signal , \(x(t)\), at a constant interval of time. That interval of time is called the sampling time, \(T_s\), which is also a sampling frequency, \(f_{s}=\frac{1}{T_{s}}\). If the input sequence is truly infinite in length, then we can take an infinite amount of samples each at an interval of \(T_s\). Thus, translating from the continuous-time representation,

$$X(f) = \sum_{n=-\infty}^{\infty}\underbrace{T_s\cdot\;x(nT_{s})}_{x[n]}e^{-i2\pi fnT_s}$$

Naturally, the time, \(t\), is converted to \(nT_{s}\) where \(n\) represents the sample number. The integral can now be converted to a sum and the \(dt\) becomes \(T_s\). We also refer to the term \(T_{s}\cdot x(nT_s)\) as \(x[n]\) which means that the resultant value will be scaled by the sampling time. A cleaner expression is,

$$X(f) = \sum_{n=-\infty}^{\infty}x[n]e^{-i2\pi \frac{f}{f_{s}}n}$$

This expression still requires an infinite amount of data points to be computed accurately which is obviously not very practical. In reality we are going to have a finite amount of data of length \(N\). One key properties of sinusoids that is used in the continuous-time Fourier transform is that at different frequencies [sinusoids are orthogonal assuming that their duration is infinite](https://ccrma.stanford.edu/~jos/mdft/Orthogonality_Sinusoids.html). This is important because it means that each individual input signal has its own *unique* frequency domain representation. This is because the set of complex exponentials that are used in the Fourier transform, \(e^{i2\pi ft}\), for different frequencies, \(f\), form an orthonormal basis. Its easier to imagine this geometrically in that the for a three dimensional cartesian plane with axes, \((x, y, z)\), a value of the \(x\) axis cannot be represented by any linear combination of \((y, z)\). This is provable by showing that the inner product is equal to zero i.e. the axes are \(90^{\circ}\) separated. 

A little sidenote is that while writing this I attempted to prove that the continuous-time complex exponentials were in fact orthogonal and got this far, 

$$\int_{-\infty}^{+\infty}e^{i2\pi ft}e^{-2\pi f't}dt = \int_{-\infty}^{+\infty}e^{i2\pi (f - f')t} dt = \frac{1}{i2\pi (f-f')}e^{i2\pi (f-f')t} \vert^{+\infty}_{-\infty}$$

Anyway for the DFT in which we want to compute the Fourier transform for a discrete set of finite values, we still like to preserve this orthogonality of the complex sinusoids so that each input maps to an individual output.

To preserve the orthogonality we must choose the frequencies which will be sampled. The most obvious frequency choices which intuitively make sense that the corresponding sinusoids would be orthogonal are the frequencies which will have a whole number of periods in the length of the input sequence. Shown mathematically,

$$\frac{\frac{N}{f_s}}{\frac{1}{f}} \in \mathbb{N}$$

$$\frac{fN}{f_{s}} \in \mathbb{N}$$

To get this property that means we will have to choose \(f\) to be a harmonic of \(\frac{f_s}{N}\). If we now take our definition of the DTFT from above and change the summation from over infinity to be the range of the input sequence, and also substitute the frequency to be the harmonics of the above which we can define as,

$$f_k=k\frac{f_s}{N}$$

We get the following,

$$X(k) = \sum_{n=0}^{N-1}x[n]e^{-i2\pi \frac{k\frac{f_s}{N}}{f_{s}}n}$$

$$X(k) = \sum_{n=0}^{N-1}x[n]e^{-i2\pi \frac{kn}{N}}$$

Which is the definition of the DFT. Note now that the output sequence or the frequency response is no longer a function of frequency (well it is really) but is now a function of \(k\). This \(k\) ranges from \(0 \to N-1\) and can be easily mapped to the actual corresponding frequency via the mapping \(f_{k}= k\frac{f_s}{N}\).

