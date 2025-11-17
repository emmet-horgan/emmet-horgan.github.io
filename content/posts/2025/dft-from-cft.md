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

It is a ubiquitous and beautiful expression. For a long time my intuitive understanding of the expression was to view the complex exponential as a complex number rotating as a unit circle in the complex plane with a frequency defined by the \(f\) variable. Integrating the product of this complex exponential with the input time-varying signal is essentially like wrapping the input signal around the unit circle as we move through time. The effect, if a frequency component of the input signal matches that of \(f\) (which essentially determines how fast we move around the unit circle), is that a sort of "resonance" occurs in which the input signal will peak (or trough) in sync with the rotation around the unit circle. As we integrate over time this will obviously accumulate which represents a weighting of how large that frequency component is, inside the input signal. Taking a cosine wave for example, that integration will essentially be infinite at the frequency of the input cosine wave. 

In the case of a cosine-wave with a phase offset, the point at which "resonance" occurs on the unit circle is not on the real-axis and instead is off at some angle which captures the offset too ! (And if you think about it, a pure sine-wave will "resonate" with the rotating unit circle when the frequencies match at exactly \(0 \pm 1j \) which corresponds to a \(\frac{\pi}{2}\) phase shift). I have always found this to be a very useful mental model to view the Fourier transform in, perhaps there are faults but it has served me well. 

But there exists another more mathematical, specifically linear algebraic, view of looking at the Fourier transform which proves to be very useful down the line. To view the Fourier transform in this form, its best to revise the dot product of two continuous functions. The inner product (dot product) of two complex functions \(f\) and \(g\) whose domain is the closed interval \(\left[a, b\right]\) is defined as,

$$\langle f, g\rangle = \int_{a}^{b} f(x)\overline{g(x)}\;dx$$

We can then re-write continuous time Fourier transform in the form of an inner product as follows,

$$X(f) = \int_{-\infty}^{+\infty} x(t) \overline{e^{j 2\pi f t}}\;dt$$

Thinking in terms of vectors and geometry, an inner product is to map a vector onto another. For example, we could try to map an arbitrary vector onto the x-axis and we essentially get the value of the x-component of the vector. That sounds oddly familiar to the previous analogy we discussed for the continuous Fourier transform ! If we view the complex exponential as a "axis" in this analogy, then we are essentially mapping that input signal onto our axis. It just so happens that we essentially have an infinite number of axes as the complex exponential is a function of frequency.

One interesting and extremely important property of axes is that they are orthogonal. In the geometric sense this means that they are separated from one another by 90 degrees. Another way of saying this is that if a set of vectors is orthogonal, then no vector in that set can be represented as linear combination of the other vectors in that set e.g. in three-dimensional Euclidean space you can not represent the z-axis as any linear combination of vectors in the x-y plane. 

This is important to our understanding of the Fourier transform due to the fact that if the set of complex exponentials is not orthogonal, then the frequency values in that set are not unique. Meaning that some of the other complex exponentials could be combined in some linear equation to represent another frequency component. This would have many far-reaching consequences but one of the more immediate being that it would not be possible (or be very difficult) to compute an inverse Fourier transform. The reason being that the once we have computed the transform, how do we _actually_ know which frequency component we are referring to this if they are not unique.

Orthogonality is proven using the inner product (once again) which should be zero if they inputs are truly orthogonal. We can show this for the set of complex exponentials as follows,

$$ \phi_{\omega}(t) = e^{j\omega t}$$
$$ \langle \phi_{\omega},\phi_{\omega^\prime} \rangle = \int_{-\infty}^{+\infty}\phi_{\omega}(t)\overline{\phi_{\omega^\prime}(t)} = \int_{-\infty}^{+\infty}e^{j(\omega-\omega^\prime)t}\;dt$$

Now if you look up the solution to this integral you'll find that,

$$\int_{-\infty}^{+\infty}e^{j\alpha x}\;dx = 2\pi\delta(\alpha)$$

Meaning that,
$$\langle \phi_{\omega},\phi_{\omega^\prime} \rangle = 2\pi\delta(\omega-\omega^\prime)$$

Now this is not zero, but for all intents and purposes it is ! The dirac delta will be zero for all non-zero inputs meaning that the inner product is zero assuming that the two input frequencies are in-fact not the same which makes perfect sense. This means that all of the possible complex exponentials in the Fourier transform are orthogonal which is great news !

Out of interest lets derive the inverse of the continuous time Fourier transform. We can do it by using the Fourier inversion idea. If we think of \(x(t)\) as some superposition of complex exponentials, then we can define it as,

$$ x(t) = \int_{-\infty}^{+\infty}A(f)e^{j2\pi ft}\;df $$

Now we need to solve for \(A(f)\). We can start by multiplying both sides by \(e^{-j2\pi f_0t}\) and integrating over \(t\),

$$ \int_{-\infty}^{+\infty}x(t)e^{-j2\pi f_0t}\;dt = \int_{-\infty}^{+\infty}\int_{-\infty}^{+\infty}A(f)e^{j2\pi ft}e^{-j2\pi f_0t}\;df\;dt = \int_{-\infty}^{+\infty}A(f)\int_{-\infty}^{+\infty}e^{j2\pi(f-f_0)t}\;dt\;df $$

You may recall this inner integral from the previous section on the orthogonality of the complex exponentials. Which means we can directly substitute in the result from that section,

$$ \int_{-\infty}^{+\infty}e^{j2\pi(f-f_0)t}\;dt = 2\pi\delta(2\pi(f-f_0)) = \delta(f-f_0) $$
$$ \int_{-\infty}^{+\infty}x(t)e^{-j2\pi f_0t}\;dt = \int_{-\infty}^{+\infty}A(f) \delta(f-f_0) \;df $$ 
$$ \int_{-\infty}^{+\infty}x(t)e^{-j2\pi f_0t}\;dt = A(f_0) $$
$$ A(f) = \int_{-\infty}^{+\infty}x(t)e^{-j2\pi ft}\;dt = X(f)$$

Finally now we can say,

$$ x(t) = \int_{-\infty}^{+\infty}X(f)e^{j2\pi ft}\;df $$

Notice how during that the derivation hinges on the orthogonality of the exponential sinusoids !

Now finally, lets take a step towards the discrete domain, what if the input is a sampled signal, sampled at a time interval \(T\) ? Well that means that our input signal is only really defined at certain specific time points like,

$$ \{nT : n=0,1,2,\ldots \} $$

That means we can redefine our Fourier transform integral like follows,

$$X(f) = \sum_{n=-\infty}^{+\infty} T\cdot x(nT) e^{-j 2\pi f nT}$$

The problem here is that the sampling time \(T\) is going to quickly become annoying to deal with. Its essentially just scaling the resultant output and normalizes the effective frequency of the complex exponentials. We could in fact just remove the redundant scaling factor.

This means that our resultant values at the output of this discrete transform will be a different unit to the continuous time Fourier transform and will be off by a constant factor equal to the sampling time. This is fine as long we understand what we have done. We can now define a new transform, the Discrete Time Fourier Transform as,

$$X(f) = \sum_{n=-\infty}^{\infty}x[n]e^{-j2\pi \frac{f}{f_{s}}n}$$

where \(x[n] = x(nT)\) for convenience. The output of this transform is still a continuous function but now showing the frequency content contained within a sampled input signal. Now that we have moved form the world of continuous integrals into the world of discrete summation it is slightly clearly to see that this computation is most definitely a inner product of what are essentially now two vectors.

This signal has some interesting properties, particularly in relation to its periodicity and its relation to the sampling frequency. Up until now we have been using the unit hertz but its a little neater to move to the normalized angular frequency domain now where,

$$ \omega = 2\pi \frac{f}{f_s} $$
$$ X(\omega) = \sum_{n=-\infty}^{\infty}x[n]e^{-j\omega n} $$

$$ X(\omega + 2\pi) = \sum_{n=-\infty}^{\infty}x[n]e^{-j(\omega + 2\pi) n} = \sum_{n=-\infty}^{\infty}x[n]e^{-j\omega n} e^{-j2\pi n}$$
$$\forall n,\; e^{-j2\pi n} = 1 $$
$$X(\omega + 2\pi) = \sum_{n=-\infty}^{\infty}x[n]e^{-j\omega n} = X(\omega) $$

Meaning that the DTFT is periodic over \(2\pi\) and that the spectrum repeats infinitely. Interestingly you can also show this using the continuous time Fourier transform of a continuous input signal times a pulse train and you get the same result. Lets dig slightly more into what the \(2\pi\) period actually means,

$$ \omega = 2\pi \frac{f}{f_s} = 2\pi $$
$$ f = f_s $$

Meaning that the period is equal to the sampling frequency. This is the effect of sampling the input, which causes repetitions of the spectrum in the frequency domain. This is where the nyquist limit sampling theorem comes from, where you must sample the signal with a frequency of _at least_ twice the frequency of the largest frequency component of the input signal. The reason being that if we increase the sampling rate, the period of the repetitions increases in the frequency domain. If we sample at least with a frequency of twice the largest frequency component, we can guarantee that the spectrum repetitions are separated. If we do not, then the repetitions can overlap which causes aliasing. Its difficult to imagine this based on text, but I'm feeling too lazy to pull out a python terminal right now to plot an example.

We showed previously that the complex exponentials in the continuous Fourier transform were orthogonal because their inner product resulted in the dirac delta function. The dirac delta function is a distribution defined over an infinite range. The complex exponentials used in the discrete time Fourier transform are also defined over an infinite range so their orthogonality should hold, but lets just make sure for kicks, why not ? 

$$\sum_{n=-\infty}^{+\infty}e^{j\omega_1 n}\overline{e^{j\omega_2 n}} = \sum_{n=-\infty}^{+\infty}e^{j(\omega_1-\omega_2) n} = 2\pi \sum_{k=-\infty}^{+\infty}\delta(\omega_1 - \omega_2-2\pi k)$$

This result is pretty much the discrete version of the continuous case. In the continuous case, it was zero other than if the frequencies were the same. In the discrete case we can see that this holds but is now a pulse train separated by \(2\pi\) which makes sense considering that the spectrum in the discrete case is also \(2\pi\) periodic.

Ok now we can compute the spectrum of a discrete input signal which is great. The signal is invertible (we did not derive here but showed that the complex exponentials are orthogonal. The spectrum is continuous so you can use the inverse continuous Fourier transform and resample) too which is great. But the problem is that this method is still wholly impractical. We need to compute an infinite sum (computers don't have infinite memory or inifite length sequences in real life) and the resultant spectrum is a continuous function, which is great for theoretical purposes but for actual computation is not very practical. In practise most signals we deal with are of a finite length, which in the case of the DTFT will have the effect multiplying the input sequence by a rectangular window which will make all samples outside the input sequence lengths equal to zero. The effect on the spectrum is that multiplication in the time-domain is convolution in the frequency domain, meaning that the spectrum will be convolved with a sinc function which will distort the spectrum which is not ideal.

Our next question should be, in what cases can we compute the DTFT of finite length sequence in which we get an accurate undistorted result ? Well to get such a result that would mean that all of the frequency information of interest would be need to be contained in the finite length input which we have. The most obvious example of that case would be a periodic signal whose period is equal to length of the input signal i.e. \(N\) meaning that we have exactly one period of the signal. We can represent that by splitting the DTFT into two sums where we compute the index by summing a combination of the two sum variables,

$$ X(\omega) = \sum_{k=-\infty}^{+\infty}\sum_{n=0}^{N-1}x\left[n+kN\right]e^{-j\omega (n+kN)} $$

If the input signal is periodic over \(N\) then by definition \(x\left[n+kN \right]=x\left[n\right]\)

$$ X(\omega) = \sum_{k=-\infty}^{+\infty}\sum_{n=0}^{N-1}x\left[n\right]e^{-j\omega (n+kN)} $$

$$ X(\omega) = \sum_{n=0}^{N-1}x\left[n\right]e^{-j\omega n}\sum_{k=-\infty}^{+\infty} e^{-j\omega kN} $$

The inner sum is a geometric sum that be shown to be the following. Note that it is different to the previous similar sums because the exponent sum is only every \(N^{\text{th}}\) value.

$$ \sum_{k=-\infty}^{+\infty} e^{-j\omega kN} = \frac{2\pi}{N} \sum_{k=-\infty}^{+\infty}\delta(\omega - \frac{2\pi k}{N}) $$

Which is a train of impulses at frequencies \(\omega = \frac{2\pi k}{N}\). Substituting that into our earlier result gives,

$$ X(\omega) = \frac{2\pi}{N}\sum_{k=-\infty}^{+\infty}\big[\sum_{n=0}^{N-1}x\left[n\right]e^{-j\omega n}\big] \delta(\omega - \frac{2\pi k}{N})$$

This can be modified further as the sum will only have effect at the frequencies in the dirac delta, 

$$ X(\omega) = \frac{2\pi}{N}\sum_{k=-\infty}^{+\infty}\big[\sum_{n=0}^{N-1}x\left[n\right]e^{-j\frac{2\pi kn}{N}}\big] \delta(\omega - \frac{2\pi k}{N}) $$

You may notice something in that formula ! The DFT has sort of just popped out, out of no where !. Defining the DFT as,

$$ X\left[k\right] = \sum_{n=0}^{N-1}x\left[n\right]e^{-j\frac{2\pi kn}{N}} $$

We can re-write the resultant DTFT expression as,

$$ X(\omega) = \frac{2\pi}{N}\sum_{k=-\infty}^{+\infty} X\left[k\right] \delta(\omega - \frac{2\pi k}{N}) $$

and there we have it, the DFT ! The reason those specific frequencies are sampled are because for an N-periodic input signal, those are the only possible frequencies to exist. Also, the set of complex exponentials at those particular frequencies are guaranteed to be orthogonal meaning that the DFT is _perfectly_ invertible which is a very desirable property. I mention that because the orthogonality of the complex exponentials is not guaranteed when we move from an infinite number, to a finite. 

The question may be coming to you, what happens when the input signal is not actually periodic like we have assumed in this case. I mean its a good question, most signals are not actually going to be periodic over their input length. This is a real-life challenge that we need to overcome. The resultant signal in that case will be the equivalent of applying a rectangular window to the input, meaning that the frequency domain representation will essentially be smudged due to the convolving with a sinc-like function. In practise, we can reduce the effect by windowing the input such that the 'smudging' is not as bad but we cannot get rid of it entirely. But that's just a real-life trade-off that we have to make unfortunately.

