---
title: 'Deriving the Discrete Fourier Transform (DFT) from the Continuous-Time Fourier Transform'
date: 2025-11-11
tags:
  - fft
  - signal-processing
summary: "A derivation of the DFT starting from the Continuous Time Fourier Transform"
---
The general continuous-time Fourier Transform can be stated as follows,

$$X(f) = \int_{-\infty}^{+\infty} x(t) e^{-j 2\pi f t}\;dt$$

It is a ubiquitous and beautiful expression. For a long time my intuitive understanding of the expression was to view the complex exponential as as a complex number rotating as a unit circle in the complex plane with a frequency defined by the \(f\) variable. Integrating the product of this complex exponential with the input time-varying signal is essentially like wrapping the input signal around the unit circle as we move through time. The effect if a frequency component of the input signal matches that of \(f\) (which essentially determines how fast we move around the unit circle) is that a sort of "resonance" occurs in which the input signal will peak (or trough) in sync with the rotation around the unit circle. As we integrate over time this will obviously accumulate which represents a weighting of how large that frequency component is, inside the input signal. Taking a cosine wave for example, that integration will actually be infinite as the frequency of the input cosine wave. 

In the case of a cosine-wave with a phase offset, the point at which "resonance" occurs on the unit circle is not on the real-axis and instead is off at some angle which captures the offset too ! (And if you think about it, a pure sine-wave will "resonate" with the rotating unit circle when the frequencies match at exactly \(0 \pm 1j \) which corresponds to a \(\frac{\pi}{2}\) phase shift). I have always found this to be a very useful mental model to view the fourier transform in, perhaps there are faults but it has served me well. 

But there exists another more mathematical, specifically linear algebraic, view of looking at the fourier transform which proves to be very useful down the line. To view the fourier transform in this form, its best to revise the dot product of two continuous functions. The inner product (dot product) of two complex functions \(f\) and \(g\) whose domain is the closed interval \(\left[a, b\right]\) is defined as,

$$\langle f, g\rangle = \int_{a}^{b} f(x)\overline{g(x)}\;dt$$

We can then re-write continuous time fourier transform in the form of an inner product as follows,

$$X(f) = \int_{-\infty}^{+\infty} x(t) \overline{e^{j 2\pi f t}}\;dt$$

Thinking in terms of vectors and geometry, an inner product is to map a vector onto another. For example, we could try to map an arbitrary vector onto the x-axis and we essentially get the value of the x-component of the vector. That sounds oddly familiar to the previous analogy we discussed for the continuous fourier transform ! If we view the complex exponential as a "axis" in this analogy, then we are esentially mapping that input signal onto our axis. It just so happens that we essentially have an infinite number of axes as the complex exponential is a function of frequency.

One interesting and extremely important property of axes is that they are othogonal. In the geometric sense this means that they are separated from one another by 90 degrees. Another way of saying this is that if a set of vectors is orthogonal, then no vector in that set can be represented as linear combination of the other vectors in that set e.g. in three-dimensional Euclidean space you can not represent the z-axis as any linear combination of vectors in the x-y plane. 

This is important to our understanding of the fourier transform due to the fact that if the set of complex exponentials is not orthogonal, then the frequency values in that set are not unique. Meaning that some of the other complex exponetials could be combined in some linear equation to represent another frequency component. This would have many far-reaching consequences but one of the more immediate being that it would not be possible (or be very difficult) to compute an inverse fourier transform. The reason being that the once we have computed the transform, how do we _actually_ know which frequency component we are referring to this if they are not unique.

Orthogonality is proven using the inner product (once again) which should be zero if they inputs are truly orthogonal. We can show this for the set of complex exponentials as follows,

$$ \phi_{\omega}(t) = e^{j\omega t}$$
$$ \langle \phi_{\omega},\phi_{\omega^\prime} \rangle = \int_{-\infty}^{+\infty}\phi_{\omega}(t)\overline{\phi_{\omega^\prime(t)}} = \int_{-\infty}^{+\infty}e^{j(\omega-\omega^\prime)t}\;dt$$

Now if you look up the solution to this integral you'll find that,

$$\int_{-\infty}^{+\infty}e^{x}\;dx = 2\pi\delta(x)$$

Meaning that,
$$\langle \phi_{\omega},\phi_{\omega^\prime} \rangle = 2\pi\delta(\phi_{\omega}-\phi_{\omega^\prime})$$

Now this is not zero, but for all intents and purposes it is ! The dirac delta will zero for all non-zero inputs meaning that the inner product is zero assuming that the two input frequencies are in-fact not the same which makes perfect sense. This means that all of the possible complex exponetials in the fourier transform are orthogonal which is great news !

Now lets take a step towards the discrete domain, what if the input is a sampled signal at from time interval \(T\) ? Well that means that our input signal is only really defined at certain specific time points like,

$$ \{nT : n=0,1,2,\ldots \} $$

That means we can redefine our fourier transform integral like follows,

$$X(f) = \sum_{n=-\infty}^{+\infty} T\cdot x(nT) e^{-j 2\pi f nT}$$

The problem here is that the sampling time \(T\) is going to quickly become annoying to deal with. Its essentially just scaling the resultant output and normalizes the effective frequency of the complex exponentials. We could in fact just remove the redunant scaling factor and instead would in terms of normalized frequency,
$$ f_N = fT = \frac{f}{f_s}$$

This means that our resultant values at the output of this discrete transform will be a different unit to the continuous time fourier transform and will be off by a contant factor equal to the sampling time. This is fine as long we understand what we have done. We can now define a new transform, the Discrete Time Fourier Transform as,

$$X(f) = \sum_{n=-\infty}^{\infty}x[n]e^{-i2\pi \frac{f}{f_{s}}n}$$

where \(x[n] = x(nT)\). The output of this transform is still a continuous function but now showing the frequency content contained within a sampled input signal. Now that we have moved form the world of continuous integrals into the world of discrete summation it is slightly clearly to see that this computation is most defintely a inner product of what are essentially now two vectors.

`TODO: Show that the eponentials for the DTFT are also orthogonal`

`TODO: Maybe derive the inverse DTFT`

`TODO: The DTFT is still an infinite sum and not really practical`

`TODO: DTFT for a N periodic signal`

`TODO: DFT introduction, but what frequencies do we sample ?`