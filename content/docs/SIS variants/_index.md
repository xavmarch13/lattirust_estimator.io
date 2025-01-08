---
weight: 7
bookFlatSection: true
title: "SIS variants"
math: true
---

# SIS variants

We will now present all SIS problem variants implemented in our tools. Most effectively hand out additional hints to the adversary, while hoping that the problem remains hard. In all cases, we will try to define the SIS variants precisely, 
give the reduction to the basic SIS problem we use (SIS, RSIS, or MSIS) and also give a rough idea of what kind of scheme the problem variant allowed to construct. 
While we will try to be as close as possible from the original papers, this (and the entire webpage) could probably still contains mistakes. 
If you are involved in any of these schemes, feel free to reach out with any corrections. In all cases, we will also show an example of how the variants can be called via the estimator.


## k-SIS 
k-SIS is the first variant of SIS that was introduced that handed out additional hints to the attacker. The key motivations behind k-SIS include:

1. Homomorphic Signature Construction: k-SIS enables a secure way to produce signatures on linear combinations of authenticated vectors. In practice, this means that operations such as sums and weighted combinations of signed data (common in network coding) can be verified without re-signing the data.
2. Removal of Random Oracles: The k-SIS assumption was used to eliminate reliance on the random oracle model for certain signature schemes. This provides a pathway to provably secure signature schemes in the standard model, albeit with constraints on the number of signatures.
3. Efficient k-Time Signature Schemes: By using the k-SIS assumption, Boneh and Freeman created k-time signature schemes where the signing key is limited to signing at most k messages. This avoids the complexity of general existential unforgeability while still ensuring strong guarantees for a bounded number of operations.


<div style="background-color: rgba(173, 216, 230, 0.3); padding: 10px; border-radius: 5px; margin: 10px 0;">

**k-SIS definition {{< cite "boneh2011linearly" >}}:**

An instance of the k-SIS problem is represented by a matrix $\bold{A}\in \mathbb{Z}_q^{h\times w}$ and a set of $k$ vectors $\bold{e_1},\cdots, \bold{e_k} \in \Lambda^T_q(\bold{A})\$ and the goal is to find another vector $\bold{v}$ such that:

$$
\lVert \bold{v} \rVert \leq \beta \text{, } \bold{A}\cdot\bold{v} = \bold{0} \pmod q \text{ and } \bold{v} \notin \mathbb{Q}-span(\lbrace \bold{e_1},\cdots, \bold{e_k} \rbrace)
$$

</div>

#### Security and estimator

*Theorem:* Suppose $w\geq 2h \log q$, $\frac{w}{k} > h$, $\sigma > \omega(\sqrt{\log w})$, $t> \omega(\sqrt{\log h})$ and $q > \sigma\omega(\sqrt{\log w})$. If we let $\beta' = \beta(k^{\frac{3}{2} + 1})k!(t\sigma)^k$, then if we can solve the $k-SIS(h, w, q, \beta, \sigma, k, p)$ problem, then we can solve the $SIS(h, w-k, q, \beta', \sigma, p)$ problem.

You can call an SIS instance in the estimator as 
`kSIS.new(h, w, q, length_bound, sigma, k, Norm)`

## k-M-SIS and k-R-SIS

### k-M-SIS

k-M-SIS and k-R-SIS are the k-SIS versions lifted up in the module and rings settings respectively.

<div style="background-color: rgba(173, 216, 230, 0.3); padding: 10px; border-radius: 5px; margin: 10px 0;">

**k-M-SIS definition {{< cite "albrecht2022lattice" >}}:**

For any integer $k\geq 0$, an instance of the k-M-SIS problem is represented by a matrix $\bold{A}\in R_q^{h\times w}$ and a set of k vectors $\bold{v_0}, \ldots, \bold{v_{k-1}}$ such that:

$$\bold{A}\cdot\bold{v_i} = 0 \pmod q \text{ and } \lVert \bold{v_i} \rVert \leq \beta$$

and the goal is to find a non-vector $\bold{v^*} \in R^w$ such that:

$$\lVert \bold{v^*} \rVert \leq \beta^* \text{ and }\bold{v}\notin \mathcal{K}-span(\lbrace \bold{v_0}, \ldots, \bold{v_{k-1}}\rbrace)$$

</div>

#### Security and estimator

No proof was provided in the original paper but following our previous work on reducing M-SIS to SIS and k-SIS to SIS we reduced an instance $k-M-SIS(h, w, d, q, \beta, k, p)$ to an instance of $M-SIS(h, w - k, d, q, \beta, k, p)$ that itself gets reduced to a standard SIS instance. You can instantiate a k-M-SIS instance instance via
`KMSIS.new(h, w, d, q, length_bound, k, Norm)`.


### k-R-SIS 

Let's simply note that k-R-SIS is k-M-SIS when $h=1$

#### Security and estimator

Again we use the same reduction from $k-R-SIS(h, w, q, \beta, k, p)$ to $R-SIS(h, w - k, q, \beta, k, p)$ and then to standard SIS. You can instantiate an instance of k-R-SIS via

`KRSIS.new(h, w, q, length_bound, k, Norm)`.

## BASIS

The BASIS assumption was introduced to overcome limitations in existing lattice-based commitments, such as small message space and lack of succinctness for functional commitments. In particular, vector commitments and polynomial commitments require efficient mechanisms to open and verify data positions or evaluations without revealing the entire committed data.
Previous approaches, like those based on collision-resistant hash functions (e.g., Merkle trees), or lattice-based schemes (e.g., SIS commitments), either required interaction or imposed stringent bounds on the data's size. The BASIS assumption addresses these constraints by:

1. Supporting Larger Message Spaces: Traditional SIS-based commitments require inputs to be "small" for correctness and security. BASIS allows commitments for inputs over the entire ring.
2. Improving Private Openings: The construction based on BASISrand ensures that commitments and openings are statistically close to uniform, facilitating privacy-preserving operations.
3. Enhancing Functional Openings: By incorporating structured randomness (as seen in BASISstruct), the framework supports opening commitments to computed values, such as the output of Boolean circuits or polynomial evaluations.

### BASIS$_{rand}$

<div style="background-color: rgba(173, 216, 230, 0.3); padding: 10px; border-radius: 5px; margin: 10px 0;">

**BASIS definition {{< cite "wee2023succinct" >}}:**

An instance of the BASIS problem can be represented with a matrix $\bold{A} \in \mathbb{Z}_q^{h\times w}$, $s$ a Gaussian width parameter and a security parameter $\lambda$. Let's imagine a sampling algorithm *Samp* that takes $\bold{A}$ as input and outputs a matrix $\bold{B} \in  \mathbb{Z}_q^{h'\times w'}$ and an auxiliary input *aux*. We say that basis augmented SIS BASIS holds with respect to the sampling algorithm *Samp* if for all efficient adversary $\mathcal{A}$, the following holds:

`BASISstruct.new(h, w, q, length_bound, sigma, k, Norm)`

$$
\Pr\left[
    A \mathbf{x} = 0 \text{ and } 0 < \|\mathbf{x}\| \leq \beta :
    \begin{array}{l}
        A \leftarrow \mathbb{Z}_q^{h \times w}; \\
        (\mathbf{B}, \text{aux}) \leftarrow \text{Samp}(1^\lambda, A); \\
        \mathbf{T} \leftarrow \mathbf{B}_s^{-1}(G_{h'}); \\
        \mathbf{x} \leftarrow \mathcal{A}(1^\lambda, A, \mathbf{B}, \mathbf{T}, \text{aux})
    \end{array}
\right]
= \text{negl}(\lambda).
$$

</div>

Essentially, this SIS problem should remain hard even given a Trapdoor matrix $\bold{B}$. This means that if $\bold{B}$ contains too much information about $\bold{A}$, the problem becomes feasible. $\text{BASIS}_{rand}$ is a concrete instanciation that is defined as follows.


<div style="background-color: rgba(173, 216, 230, 0.3); padding: 10px; border-radius: 5px; margin: 10px 0;">

>The sampling algorithm *$\text{Samp}(1^\lambda, A)$* is defined as follows:
>1. Samples $i^* \leftarrow [\ell]$.
>2. Sets $A_i \leftarrow \mathbb{Z}_q^{(h+1) \times w}$ for all $i \neq i^*$, and $A \leftarrow \mathbb{Z}_q^w$.
>3. Defines
>   $$
   A_{i^*} = 
   \begin{bmatrix}
       \mathbf{a^T} \\ \bold{A}
   \end{bmatrix}.
   $$
>4. Outputs
   $$
   \mathbf{B}_\ell =
   \begin{bmatrix}
       A_1 &  &  &-G_{\eta+1} \\
        & \ddots &  & \vdots \\
        &  & A_\ell & -G_{\eta+1}
   \end{bmatrix},
   $$
   and $\text{aux} = i^*$.

</div>

This is referred to as "the BASIS assumption with random matrices."

#### Security and estimator

*Theorem:* Let $\lambda$ a security parameter and take any polynomial $\ell$. If we suppose $h \geq \lambda$, $w\geq O(h\log q)$ and $s \geq O(\ell w\log(h\ell))$. Then under the $SIS(h,w,q,\beta, p)$ assumption, the BASIS
assumption holds with parameters $BASIS_{rand}(h,w,q,\beta,s,\ell, p)$. The reduction is straightforward as SIS needs to hold for $A_i$ even given $B_\ell$.

In our tool, we therefore simply reduce $BASIS_{rand}(h,w,q,\beta,s,\ell, p)$ to $SIS(h,w,q,\beta, p)$  you can call an instance of BASIS$_{rand}$ by instancianting a BASIS class as 

`BASISrand.new(h, w, q, length_bound, sigma, k, Norm)`

where $k$ is $\ell$, length_bound is $\beta$, sigma is $s$ and Norm is either L2 or Linf.
### BASIS$_{struct}$

Another instanciation is presented in the same paper as follows.

<div style="background-color: rgba(173, 216, 230, 0.3); padding: 10px; border-radius: 5px; margin: 10px 0;">


>The sampling algorithm $\text{Samp}(1^\lambda, A)$ is defined as follows:
>1. Samples $W_i \leftarrow \mathbb{Z}_q^{h \times h}$ for all $i \in [\ell]$.
>2. Outputs
>   $$
    \mathbf{B}_\ell =
   \begin{bmatrix}
       W_1 A &  &  &-G_{\eta} \\
        & \ddots &  & \vdots \\
        &  & W{\ell}A & -G_{\eta}
   \end{bmatrix},
   $$
>   and $\text{aux} = (W_1, \ldots, W_\ell)$.

</div>

This is essentially $BASIS_{rand}$ with structured matrices $A_1, \cdots, A_{l}$. It is referred to as "the BASIS assumption with structured matrices."

#### Security and estimator

While reducing to standard SIS is not possible for this structured version, the authors have shown that given an algorithm to solve $BASIS_{struct}$,
we can solve k-M-ISIS as long as as the $BASIS_{struct}$ instance is of size $\ell < \frac{h}{k'}$ where k' is the parameter k in the k-M-ISIS instance.
Our tool therefore reduces $BASIS_{struct}(h,w,q,\beta,s,\ell, p)$ to $k-M-ISIS(h,w,\ell, q, \beta, s, k', p)$
You can instanciate an instance of $BASIS_{struct}$ via:

`BASISstruct.new(h, w, q, length_bound, sigma, k, Norm)`


### PRISIS


PRISIS (Power-Ring Short Integer Solution) is a variant of the BASIS assumption designed to strengthen lattice-based cryptographic commitment schemes. Unlike its predecessors, PRISIS introduces a structure where matrix multipliers in the commitment scheme are replaced by scalar multiplications with random ring elements, specifically uniformly random polynomials from a defined ring 
$R_q$. The primary motivation for introducing PRISIS is to simplify the structure of the sampling process in lattice-based schemes while maintaining robust binding and security guarantees. PRISIS allows for more efficient commitment constructions by reducing the complexity of operations and maintaining small proof sizes. Furthermore, it retains security under standard lattice assumptions, supporting succinct, zero-knowledge proofs of polynomial evaluations, even for large degrees. This makes it particularly useful for applications requiring succinct non-interactive arguments and zero-knowledge proofs with minimal verification overhead.

Let us first define PowerBASIS as a first step. We denote GL($n, R$) to be the group of $n\times n$ invertible matrices over the ring R. 

<div style="background-color: rgba(173, 216, 230, 0.3); padding: 10px; border-radius: 5px; margin: 10px 0;">

>The sampling algorithm *$\text{Samp}(1^\lambda, A)$* is defined as follows:
>1. Generates a row $a^T\leftarrow R^\ell_q$.
>2. Sets 
    \[
   A^* =  
   \begin{bmatrix}
   \mathbf{a^\top} \\ 
   \mathbf{A}
   \end{bmatrix} 
   \in R_q^{(h+1) \times w}.
   \]
>3. Samples $\bold{W} \leftarrow \text{GL}(h+1,R_q)$
>3. Outputs
   $$
   \mathbf{B}_\ell =
   \begin{bmatrix}
       \bold{W}^0\bold{A^*} &  &  &-G_{h+1} \\
        & \ddots &  & \vdots \\
        &  & \bold{W}^\ell\bold{A^*} & -G_{h+1}
   \end{bmatrix},
   $$
   and $\text{aux} = \bold{W}$.

</div>

Let us now define the proper PRISIS assumption.

<div style="background-color: rgba(173, 216, 230, 0.3); padding: 10px; border-radius: 5px; margin: 10px 0;">

**PRISIS definition {{< cite "fenzi2024lattice" >}}:**

>The sampling algorithm *$\text{Samp}(1^\lambda, A)$* is defined as follows:
>1. Generates a row $a^T\leftarrow R^\ell_q$.
>2. Sets 
    \[
   A^* =  
   \begin{bmatrix}
   \mathbf{a^\top} \\ 
   \mathbf{A}
   \end{bmatrix} 
   \in R_q^{(h+1) \times w}.
   \]
>3. Samples $w \leftarrow \text{GL}(1,R_q)$
>3. Outputs
   $$
   \mathbf{B}_\ell =
   \begin{bmatrix}
       w^0\bold{A^*} &  &  &-G_{h+1} \\
        & \ddots &  & \vdots \\
        &  & w^\ell\bold{A^*} & -G_{h+1}
   \end{bmatrix},
   $$
   and $\text{aux} = w$.

</div>

#### Security and estimator

The security have only been analyzed for 2 hints, so $l=2$ but it has been shown that $BASIS_{struct}$ and $BASIS_{power}$ are equivalent in hardness for certain parameter choices. More directly for PRISIS we have that:
*Theorem:* Let $h>0, w\geq h$ and $t=(h+1)(\lfloor \log_{\delta}(q)\rfloor + 1)$. Let $q=\omega(N)$, $\epsilon \in (0, \frac{1}{3})$ and $s\geq \max(\sqrt{N\ln(8Nq)}q^{\frac{1}{2} + \epsilon}, \omega(N^{\frac{3}{2}}\ln(N)^{\frac{3}{2}}))$ such that
$2^{10N}q^{-\lfloor \epsilon N \rfloor}$ is negligible. Then for $\sigma \geq \delta \sqrt{tN(N^2s^2w + 2t)}\omega(\sqrt{N\log h N})$, we have that PRISIS is hard under the $M-SIS(h, w, N, q, \beta, p)$ assumption.

We therefore reduce a PRISIS instance to an M-SIS instance and therefore an M-SIS instance to a standard SIS instance. You can instanciate a PRISIS instance via 

`PRISIS.new(h, w, d, q, length_bound, sigma, k, Norm)`.

### h-PRISIS

<div style="background-color: rgba(173, 216, 230, 0.3); padding: 10px; border-radius: 5px; margin: 10px 0;">

**h-PRISIS definition {{< cite "fenzi2024lattice" >}}:**

h-PRISIS is a multi-instance PRISIS problem. The problem can be summarized as given $(\lbrace \bold{A_i} \rBrace, \lbrace \bold{B_i} \rBrace, \lbrace w_i \rBrace, \lbrace \bold{T_i} \rBrace)_{i=0}^{h-1}$ defined as classical PRISIS problems, find short vectors $\bold{x_i}$ s.t. $\sum \bold{A_i}\cdot\bold{x_i} = \bold{0}$

</div>

## ISISf

The ISISf assumption was proposed to overcome limitations in existing lattice-based constructions, particularly in creating privacy-preserving cryptographic schemes like blind signatures, group signatures, and anonymous credentials. The primary motivations include:

1. Efficiency in Privacy-Preserving Credentials: traditional lattice-based anonymous credential schemes often required complex and inefficient proof systems to verify signatures. ISISf allows succinct zero-knowledge proofs for verifying structured relations.
2. Generalized Pre-Image Relations: unlike the classic SIS problem, ISISf supports verifying that a short vector maps to an arbitrary function value 
$f(x)$ rather than simply being zero. This capability is essential for proving knowledge of secret values (e.g., credentials) in a way that hides sensitive information.
3. Compatibility with Function Families: by introducing a function $f$, the assumption supports constructions where the output space is structured, such as binary encodings or commitments.

ISISf notably enables:

1. *Blind Signatures* : ISISf enables blind signature schemes where a signer issues a signature without learning the message content. The function 
f ensures that the relationship between the signature and the blinded message remains secure and unlinkable.

2. *Anonymous Credentials*: Anonymous credentials allow users to prove ownership of credentials without revealing their full attribute set. The ISISf assumption allows efficient commitment to and selective disclosure of credential components. This makes it feasible to verify partial information while preserving privacy.

3. *Group Signatures*: Group signatures require a signer to remain anonymous within a group. ISISf supports efficient zero-knowledge proofs of group membership while ensuring that the signer cannot produce more valid signatures than their allowed number.

4. *Zero-Knowledge Proofs of Knowledge (ZKPoK)*:
The structure of ISISf supports constructing non-interactive zero-knowledge proofs for lattice relations. This leads to smaller and faster proofs compared to prior constructions, particularly when
f is chosen as a linear or random function.

<div style="background-color: rgba(173, 216, 230, 0.3); padding: 10px; border-radius: 5px; margin: 10px 0;">

**ISISf definition {{< cite "bootle2023framework" >}}:**
An instance of the ISISf problem is defined as follows:

Let $\boldsymbol{A} \in \mathbb{Z}_q^{h \times w}$ and $f: \mathbb{Z}_N \rightarrow \mathbb{Z}_q^h$ be a function. Given $(\boldsymbol{A}, f)$ and access to an oracle that samples fresh $x \leftarrow \mathbb{Z}_N$ and outputs $x, \boldsymbol{u}_x$ such that:

$$
\boldsymbol{A} \cdot \boldsymbol{u}_x = f(x) \mod q \quad \text{and} \quad \lVert \boldsymbol{u}_x \rVert \leq \beta,
$$

where k is the number of times the oracle is used (number of hints).

the goal is to output a fresh tuple $(x^\*, \boldsymbol{u}^*)$ such that:

$$
\boldsymbol{A} \cdot \boldsymbol{u}^* = f(x^*) \mod q \quad \text{and} \quad \lVert \boldsymbol{u}^* \rVert \leq \beta^*.
$$

</div>

#### Security and estimator

The hardness of the ISISf assumption effectively depends on the function f chosen. If we consider f to be in the ROM (Random Oracle Model), then ISISf is as hard as SIS.
However, if we want to attack the scheme without making any assumption about f, then we have to reduce to k-M-ISIS but the actual reduction would depend on the actual instanciation of the ISISf function and as such, we provide only the ROM road.

In the ROM case we reduce $ISISf(h,w,q,\beta, k, p)$ to $SIS(h,w - k,q,\beta, p)$ and you can call
`ISIf.new(h, w, q, length_bound, sigma, k, Norm)`.

## k-R-ISIS 

The motivation for k-R-ISIS arises from the need to construct lattice-based succinct non-interactive arguments of knowledge (SNARKs) that:

1. Support recursive composition: recursive SNARKs require efficient verification of algebraic relations over bounded-norm vectors.
2. Enable polynomial commitments beyond linear functions: many SNARK applications, especially in zero-knowledge systems, involve verifying polynomial maps with constant degrees greater than 1.

<div style="background-color: rgba(173, 216, 230, 0.3); padding: 10px; border-radius: 5px; margin: 10px 0;">

**Definitions {{< cite "albrecht2022lattice" >}}:**

*k-M-ISIS admissibility:*

Let $g(\bold{X})\in R(\bold{X})$ be a Laurent monomial (which is simply a monomial with possibly negative powers). We can write $g(\bold{X}) = \bold{X^e} = \prod_{i\in\mathbb{Z_l}}\bold{X}^{e_i}_i$ for some exponent vector $\bold{e}\in \mathbb{Z}^l$. Now let $\mathcal{G} \subset R(\bold{X})$ be a set of Laurent monomials and $|\mathcal{G}|=k$ and $g^*\in R(\bold{X})$ be a target Laurent monomial.

We call a family $\mathcal{G}$ k-M-ISIS admissible if
1. All $g\in \mathcal{G}$ have a constant degree
2. All $g\in \mathcal{G}$ are distinct
3. $0\notin \mathcal{G}$

and we call a family $(\mathcal{G},g^*)$ k-M-ISIS admissible if $\mathcal{G}$ is k-M-ISIS admissible and 

1. $g^*$ has constant degree
2. $g^* \notin \mathcal{G}$

*k-M-ISIS assumption:* Now, let $\mathbf{t} = (1, 0, \ldots, 0)$. Let $\mathcal{G} \subseteq R(\mathbf{X})$ be a set of $d$-variate Laurent monomials. Let $g^* \in R(\mathbf{X})$ be a target Laurent monomial. Let $(\mathcal{G}, g^*)$ be $k$-M-ISIS-admissible. Let $\mathbf{A} \leftarrow R_q^{h \times w}$, 
$\mathbf{v} \leftarrow (R_q^{\*})^{d}$.

The K-M-ISIS assumption states that given $(\mathbf{A}, \mathbf{v}, \mathbf{t}, \{\mathbf{u}_g\})$ with $\mathbf{u}_g$ short and 

\[
g(\mathbf{v}) \cdot \mathbf{t} \equiv \mathbf{A} \cdot \mathbf{u}_g \mod q
\]

it is hard to find a short $\mathbf{u}_{g^{\*}}$ and small $\mathbf{s}^{\*}$ such that:

\[
\mathbf{s}^* \cdot g^*(\mathbf{v}) \cdot \mathbf{t} \equiv \mathbf{A} \cdot \mathbf{u}_{g^*} \mod q.
\]

When $h = 1$, i.e., when $\bold{A}$ is just a vector, we call the problem $k$-R-ISIS.

</div>

#### Security and estimator

The authors explore different special cases with parameters that do not really fit any real applications but show that the problem seems to be at least as hard as R-SIS.
Several attacks can be considered, to evaluate the security of this scheme we rely upon the direct SIS attack on $\bold{A}$, so we reduce $k-R-ISIS(h, w, q, \beta, k, p)$ to $SIS(h, (w+1)h, q, \beta, p)$. We similarly reduce $k-M-ISIS(h, w, d, q, \beta, k, p)$ to $SIS(dh, d(w+1)h, q, \beta, p)$.
You can instanciate these SIS assumptions via

`KRISIS.new(h, w, q, length_bound, k, Norm)`.


`KMISIS.new(h, w, d, q, length_bound, k, Norm)`.


## vanishing-SIS 

The Vanishing Short Integer Solution (vSIS) assumption introduces a novel approach to constructing lattice-based cryptographic schemes using vanishing polynomials, enabling succinct and homomorphic commitment schemes that are logarithmic in size relative to the input and support bounded multiplicative homomorphism. These commitments can be “folded,” compressing proof sizes in a manner similar to Bulletproofs. The vSIS assumption allows for efficient construction of succinct non-interactive arguments of knowledge (SNARKs) with quasi-linear prover time and polylogarithmic verifier runtime for structured relations, making verifiable delay functions (VDFs) possible in a lattice-based setting. Additionally, vSIS supports recursive proof composition and enhances prover efficiency, overcoming previous bottlenecks in lattice-based protocols. Building on the hardness of finding short vanishing polynomials at specific points, the vSIS problem extends the classical SIS problem, is no weaker than the k-R-ISIS assumption, and relates to the NTRU problem under specific conditions, offering robust post-quantum security guarantees.


<div style="background-color: rgba(173, 216, 230, 0.3); padding: 10px; border-radius: 5px; margin: 10px 0;">

**Definition {{< cite "cini2023lattice" >}}:**

Let $h, w, d, q, \beta \in \mathbb{N}$ and $\mathcal{G}$ be a set of w-variate monomials of degree at most d. The vanishing SIS problem is represented by a set $V=\lbrace \bold{v_i} \rbrace_{i=1}^n \in (R^{\times}_q)^w$ of $h$
uniformely random points in $(R^{\times}_q)^w$. The goal is to find a non-zero polynomial $p\in R[X_1, \cdots, X_w]$ with monomial support over $\mathcal{G}$ such that

$$\forall i \in [n], p(\bold{v_i}) = 0 \pmod{q} \text{ and } \lVert p \rVert \leq \beta$$

</div>

#### Security and estimator

The link between vanishing SIS and standard SIS can bee seen by writing the v-SIS problem as such

$$
\left(
\begin{array}{cccccccc}
1 & v_{1,1} & \cdots & v_{1,w} & \prod_{j=1}^{w} v_{1,j}^{e_{j}^1} & \cdots & \prod_{j=1}^{w} v_{1,j}^{e_{j}^d} \\
1 & v_{2,1} & \cdots & v_{2,w} & \prod_{j=1}^{w} v_{2,j}^{e_{j}^1} & \cdots & \prod_{j=1}^{w} v_{2,j}^{e_{j}^d} \\
\vdots & \vdots & & \vdots & \vdots & & \vdots \\
1 & v_{n,1} & \cdots & v_{n,w} & \prod_{j=1}^{w} v_{n,j}^{e_{j}^1} & \cdots & \prod_{j=1}^{w} v_{n,j}^{e_{j}^d} \\
\end{array}
\right) \cdot \mathbf{p} = 0 \mod q
\text{ and} \quad \|\mathbf{p}\| \leq \beta,
$$

and you can instanciate a vanishing SIS problem via

`VSIS.new(h, w, d, q, length_bound, k, Norm)`.


## one-more-ISIS 

The **one-more-ISIS** (Inhomogeneous Short Integer Solution) assumption is a lattice-based analogue of the one-more-RSA assumption introduced to address the challenges of constructing practical and efficient post-quantum blind signatures. The one-more-ISIS assumption involves a game where an adversary is given a matrix \( C \) and can make a limited number of preimage queries for vectors \( t \) such that \( C y = t \) while ensuring \( y \) is short. The adversary wins if it can output one additional valid pair \( (y, t) \) beyond the number of queries it made. This assumption was introduced to support unforgeability in blind signature schemes by ensuring that even when multiple signatures are requested, forgery remains computationally infeasible.

The one-more-ISIS assumption allows for the construction of efficient, two-round, lattice-based blind signature schemes that are secure under post-quantum settings. It replaces the need for general-purpose zero-knowledge arguments with more efficient lattice-based non-interactive zero-knowledge proofs (NIZKs) for linear relations, significantly reducing signature sizes and computational costs. This development enables the creation of practical blind signature schemes with smaller signatures (around 45 KB) and low runtime for both the signer and the user, making them suitable for use in e-cash, e-voting, and other privacy-preserving applications.

<div style="background-color: rgba(173, 216, 230, 0.3); padding: 10px; border-radius: 5px; margin: 10px 0;">

**Definition {{< cite "agrawal2022practical" >}}**

An instance of the one-more-ISIS problem is represented by a matrix $\bold{A}\in\mathbb{Z}_q^{h\times w}$. An adversary receives this matrix and is able to perform two times of queries:

1. *Syndrome queries*: request a vector $\bold{t}\leftarrow \mathbb{Z}_q^h$, the challenger keeps track of vectors sent by adding them to a set $\mathcal{T}$.

2. *Preimage queries*: submit a vector $\bold{t'} \in \mathbb{Z}_q^h$ and the challenger gives back a short vector 

$$\bold{y'} \leftarrow D_{\mathbb{Z^w,\sigma}} \text{ such that } \bold{A}\cdot \bold{y'} = \bold{t'} \pmod q$$.

We denote the number of preimage queries done with $k$.

The adversary wins if he can output $k+1$ pairs $\lbrace (\bold{y_i}, \bold{t_i}) \rBrace_{0\leq i < k+1}$ satisfying 

$$\bold{A} \cdot \bold{y_i} = \bold{t_i} \pmod q \text{, } \lVert \bold{y_i} \rVert\leq \beta  \text{ and } \bold{t_i}\in\mathcal{T}$$

</div>

#### Security and estimator

The security of the scheme is evaluated with respect to the following lattice reduction attack described in {{< cite "agrawal2022practical" >}}:

1. Given $\bold{A}$, compute a basis of $\Lambda^T_q(A)$.
2. Make $\Theta(w^2)$ preimage queries for $\bold{t} = 0$ and construct a new basis $\bold{B}$ for $\Lambda^T_q(C)$ such that the GSO $\bold{\overline{B}}$ of $\bold{B}$ are bounded from above.
3. Given an input $\bold{t} \in \mathbb{Z}^h_q$ find any $\bold{z}\in \mathbb{Z}$ such that $\bold{C}\bold{z} = \bold{t}$ 
4. Run Babai's nearest plane algorithm on $(\bold{B}, \bold{z})$. Let $\bold{v}\in \Lambda^T_q(C)$ be the output tjem we return $\bold{z}-\bold{v}$ as solution for one-more-ISIS for $\bold{t}$.

As such, we reduce the security of the one-more-ISIS problem to solving SIS on a matrix about the size of $\bold{A}$. You can instanciate a one-more SIS problem via 
`onemoreISIS.new(h, w, q, length_bound, Norm)`.

# References
{{< references >}}


