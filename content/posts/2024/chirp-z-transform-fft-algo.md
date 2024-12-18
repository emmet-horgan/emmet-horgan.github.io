---
title: 'Chirp Z Transform FFT Algorithm'
date: 2024-09-26
tags:
  - fft
  - signal-processing
  - algorithms
summary: "An explanation of how the Chirp-Z transform FFT algorithm, also known as Bluestein's algorithm, works with an emphasis on convolution and equivalence"
---
The Chirp-Z transform FFT also known as Bluestein's algorithm is a method of computing the discrete Fourier transform with a computational complexity of \(\mathcal{O}(n\log(n))\).
The implementation actually relies on other FFT implementations such as the Cooley-Tukey FFT algorithm. One of the downsides about most of the other FFT algorithms is that they
rely on the input sequence having a certain length. For example the 2-point Cooley-Tukey
FFT relies on the input sequence length being a power of two. This is quite restrictive
and it would be nice if an FFT algorithm existed which was adaptable with the input 
sequence length.

The first thought at over coming this might be to simply pad the input sequence with 
zeros until it reaches the desired length. The equation for the DFT is,

$$X_k = \sum_{n=0}^{N-1}x[n]e^{-i2\pi \frac{kn}{N}}$$

If the end of the sequence is it will not actually effect the output value at a 
first glance but then you realise that the padding has its exact intended effect which
is to make the sequence longer and increases the value of $N$ which changes the effectively frequencies being sampled from the DTFT. So simply padding the input sequence with zeros
is unfortunately not a viable solution.

The first step in Bluestein's algorithm is to consider the following identity,

$$nk = \frac{-(k-n)^2}{2} + \frac{n^2}{2} + \frac{k^2}{2}$$

Making this substitution into the DFT equation and we get the following after basic 
simplification,
$$X_k = e^{-\frac{\pi i}{N}k^2} \sum_{n=0}^{N-1} \left(x_n e^{-\frac{\pi i}{N}n^2}\right) e^{\frac{\pi i}{N}(k-n)^2}$$

The summing expresion can be viewed as the convolution of two sequences \(a_n\) and \(b_n\),

$$a_n = x_n e^{-\frac{\pi i}{N}n^2}$$

$$b_n = e^{-\frac{\pi i}{N}n^2}$$

A well known property of the Fourier transform is the convolution theorem,

$$ (x * y)(t) = \mathscr{F^{-1}}\{\mathscr{F}\{ x \} \cdot \mathscr{F}\{ y \}\} $$

This theorem holds for the DTFT but is slightly different for the DFT. For the DTFT it can be easily shown that the following is true,

$$ x * y = \text{DTFT}^{-1}\{ \text{DTFT}\{x\} \cdot \text{DTFT}\{y\} \} $$

A simplification occurs when one of the sequences being convolved is N-periodic meaning that the sequence repeats every \(N\) samples,

$$ x[n + N] = x[n] $$

The simplification being that for an N-periodic sequence the DTFT is non-zero at only discrete frequencies and thus so is its product with another continuous function. Interestingly, the DTFT can be computationally reduced the the DFT if the input sequence is N-periodic because all of the information is contained within $N$ samples and an N-point DFT will sample each harmonic meaning that no information is lost. If this is pointwise multiplied with another DTFT which is not N-periodic, it will also be simplified to its DFT result (due to the zero trenches). Anyway, for an N-periodic sequence,

$$ x * y_N = \text{DTFT}^{-1}\{ \text{DTFT}\{x\} \cdot \text{DTFT}\{y_N\}\} = \text{DFT}^{-1}\{ \text{DFT}\{x_N\} \cdot \text{DFT}\{y_N\}\} $$

$$ x_N[n] = \sum_{m=-\infty}^{\infty} x[n-mN]$$

In practise the \(x\) sequence is usually of length \(N\) and the \(y\) sequence is usually a periodic extension/summation of an \(N\) length \(y\) sequence,

$$ y_N[n] = \sum_{m=-\infty}^{\infty} y[n-mN] = y[n \text{ mod } N]$$

This is called circular convolution and differs from ordinary linear convolution given that in ordinary linear convolution if an index went out of the bounds of the sequence, we assume its value to be zero and it has no effect on the calculation but in cicular convolution there is a wrapping effect which needs to be accounted for. Now we know that if we attempt to implement the linear convolution using two FFTs and an inverse FFT, there is the implicit assumption that *both* of the input sequences are N-periodic and we need to account for this. Reminding ourselves of what we trying to achieve,

$$ \sum_{n=0}^{N-1} a_n \cdot b_{k-n}$$

The main issue here is the \(b_{k-n}\) which depending on the values of \(k\), and \(n\) can range from \(-N+1 \to N-1\) which is a total range of \(2N - 1\). In general to compute a linear convolution from a circular convolution we could simply zero pad both sequences to the minimum length of \(2N-1\). The zero-padding has no effect on the linear convolution and but now wrapping of negative indices back to the end of the sequence will always land on a zero, so woohoo we solved it ! That is true in the general case but in our case \(b_{k-n}\) is a chirp signal which has valid negative indices which need to be included. Fortuantely, the chirp signal is mirrored about the vertical axis meaning that \(b_n = b_{-n}\). That means to compute the convolution we will need to zero pad \(a_n\) out to a minimum length of \(M = 2N-1\) and similarly account for the wrapping by setting \(b_n = b_{M-n}\). 

I want to make it clear that padding to a length of \(2N-1\) is the minimum you can pad to because this is the range of indices that the convolution will index in this case (\(L+M-1\) in the general case of two sequences of length \(L\), and \(M\)). Ideally, we will use an FFT algorithm to compute this convolution which is the entire point of this process so that we can get the benefit of the FFTs computational complexity without the length restrictions. There is no problem if we choose to pad the sequences out to a more optimal length so long as that length is greater than \(2N-1\) but I would like to highlight a point that could be deceiving at first. The \(b_n\) sequence will need to be mirrored as above but we also need additional zeros in the sequence to pad the length out. This means that the zeros will need to be padded in the middle of the sequence because the, \(b_n = b_{M-n}\) where M is the total padded length, terms need to be add the end of the sequence of wrapping. Meaning that the padded sequence would look something like,

[\(b_0\), \(b_1\), \(b_2\), \(\cdot \cdot \cdot\), \(0\), \(0\), \(0\), \(\cdot \cdot \cdot\), \(b_3\), \(b_2\), \(b_1\)] 

Where the length of the zero section will be \(M - N -( N - 1) = M-2N-1\).