We start by reviewing Fourier Transform

A continuous Fourier transform reads as

$$
\tilde{F}(f) = \int_{-\infty}^{\infty} F(t)e^{-2\pi i ft} dt
$$

And the inverse Fourier transform is 

$$
F(t) = \int_{-\infty}^{\infty} \tilde{F}(f) e^{2\pi i f t }df
$$

## Gate and inpaint

Prove that Collin's paper is mathematically equivalent with Barack's paper.

The loglikelihood reads as 

$$
\log \mathcal{L} = -\frac{1}{2}(n|n)
$$

where $n=d-h$ is the residual noise, assumed to be a Gaussian process. We explicitly write down the likelihood for a Gaussian process

$$
\log \mathcal{L} = -\frac{1}{2} n^TC^{-1}n
$$

where $n$ now is a column vector taking values $(n_1, n_2, ..., n_d)$. As constructed by Barack, an operator $F$ is applied on $n$

$$
F = 1 - AM^{-1}A^T C^{-1}
$$

and $M = A^T C^{-1} A$. The matrix $A$ is a "shrinking matrix", being almost a identity matrix but with the size $N_d \times N_g$, where $N_d$ is the number of the data samples and $N_g$ corresponds to number of date being gated and inpainted. Explicitly, A looks like

$$
A= \begin{pmatrix}
 & & 0 & \\
&1 & ... \\
& & 1 & ... \\
& &   & 1 & ...  \\
& & ... \\
& & & & 1 \\
& & 0 & 

\end{pmatrix}
$$

Of course $A^T$ would have size $(N_h \times N_d)$. Acting $A^T$ on $n$ will result in a diagonal matrix with size $(N_h \times N_h)$ with elements being the gated data samples. We can play another trick by acting $AA^T$ on $n$, which will end up with a $N_d \times N_d$ size matrix but only the data samples to be gated will be left, and all other samples being zeroed out. I call $AA^T$ "extraction matrix".



It's interesting to prove that, after acting $F$ on $n$, any elements in the gating region will not impact the computation of $n^T C^{-1} Fn$. This is obvious because 

$$
(AA^Tn)^T C^{-1} Fn = n^T AA^T C^{-1}(1- AM^{-1} A^TC^{-1})n \\
= n^TAA^T C^{-1}n - n^TAA^TC^{-1}A (A^T C^{-1}A)^{-1}A^T C^{-1} n \\
= n^T AA^T C^{-1}n - n^T AA^T C^{-1}n = 0
$$

By construction, no matter what data values are in the gated region, they don't impact the computation of likelihood.
