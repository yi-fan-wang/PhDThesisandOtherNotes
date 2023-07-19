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

## Another perspective: Collin's paper

However Collin's GW190521 ringdown paper came up with the same conclusion but from another perspective (I think). The goal is to gate, or would rather say, remove certain data samples, but on the condition that the likelihood remained unchanged. This is to say

$$
n_{tr}^T C_{tr}^{-1} n_{tr} = n^TC^{-1}Fn
$$

where $n_{tr}$ is defined in Collin's paper as "truncated data", which is, subtracting the data samples in the gating region, which ends up being a column vector of side $N_d - N_h$.

I don't think it's trivial to immediately come up with the solution of F (although when I asked around, everybody told me it's trivial, anyway...I choose to prove it). I need to invoke the trick of matrix inversion for a block matrix.

 (this part is done after reading a stackoverflow answer) Say, I have a block matrix, with the inversion as the following

$$
\begin{pmatrix}
A & B \\
B^T & D
\end{pmatrix}^{-1} = 
\begin{pmatrix}
a & b \\
b^T & d
\end{pmatrix}
$$

Here I deal with symmetric matrix as is the case for covariance matrix.  Therefore by definition of inversion I have

$$
Aa + Bb^T = 1 \\
Ab + Bd = 0
$$

Here 1 is the unity matrix. From the second line I have

$$
B = -Abd^{-1}
$$

Substitute to the first line I get

$$
Aa - Abd^{-1}b^T = 1
$$

Hence 

$$
A(a - bd^{-1}b^T) = 1
$$

Therefore 

$$
A^{-1} = a-bd^{-1}b^T
$$

With loss of generality (I hope), let's choose $A^{-1} = C_{tr}^{-1}$, this is to say  the truncated data all being at the corner. If this is not the case, the more general matrix can be obtained by some permutation and (I hope) this proof still stands true.

The convention up to now is a bit confusing, let's reorganize a bit:

The covariance matrix is denoted by C, and 

$$
C^{-1} = \begin{pmatrix}
a & b \\
b^T & d
\end{pmatrix}
$$

The covariance matrix after the data being truncated is denoted by $C_{tr}$ and 

$$
C_{tr}^{-1} = a-bd^{-1}b^T
$$

Hence

$$
n_{tr}^T C_{tr}^{-1} n_{tr} = n_{g}^T a n_{g} - n_{g}^Tbd^{-1}b^T n_{g}
$$

According to Collin's paper, all we need to figure out is the solution of the equation

$$
C^{-1}(n_g \oplus x) = 0
$$

(Here $\oplus$ means concatenate) in the truncated rows and columns, this can be expressed as 

$$
b^T n_g + d x = 0
$$

Hence

$$
x = - d^{-1} b^T n_g
$$

Finally we have

$$
(n_g \oplus w)^T C^{-1}(n_g \oplus x) = \\
(n_g \oplus w)^T \begin{pmatrix}
a & b \\
b^T & d
\end{pmatrix}(n_g \oplus x) =\\
n_g^T (a n_g + bx) = \\
n_g^T (a n_g - bd^{-1}b^T n_g) = \\
n_g^T a n_g - n_g^T bd^{-1}b^T n_g = \\
n_{tr}^T C_{tr}^{-1}n_{tr} 
$$

where $w$ means whatever vector it is, it doesn't affect the likelihood, which is the core property of the inpainting. 

Here we go.
