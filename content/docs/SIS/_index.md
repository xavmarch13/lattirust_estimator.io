---
weight: 3
bookFlatSection: true
title: "SIS"
math: true
---

# Short Integer Solution problem (SIS)

Let us look at two different ways to define m-dimensional q-ary lattices from a matrix \\(\bold{A} \in \mathbb{Z}_q^{h\times w}\\). We cran write: 

$$\Lambda_q(\bold{A}) = \{\bold{y} \in \mathbb{Z}^w : \bold{y} = \bold{A}^T\bold{s} \text{ mod q }\text{ for some } \bold{s} \in \mathbb{Z}^h\}$$

$$\Lambda^T_q(\bold{A}) = \{\bold{y} \in \mathbb{Z}^w : \bold{A}\bold{y} = \bold{0} \text{ mod q }\}$$

The first lattice, \( \Lambda_q(\boldsymbol{A}) \), is generally referred to as the **primal problem** and is closely related to the **Learning With Errors (LWE)** problem. Solving the LWE problem involves finding a short vector in \( \Lambda_q(\boldsymbol{A}) \).

### LWE Problem Statement

Given:
- A matrix \( \mathbf{A} \in \mathbb{Z}_q^{h \times w} \), where entries are chosen uniformly at random,
- A secret vector \( \mathbf{s} \in \mathbb{Z}_q^w \), chosen uniformly at random,
- An error vector \( \mathbf{e} \in \mathbb{Z}_q^h \), where each entry is drawn from an error distribution \( \chi \) (e.g., a discrete Gaussian distribution),
- A vector \( \mathbf{b} = \mathbf{A} \mathbf{s} + \mathbf{e} \mod q \).

To find:
- In the **search version**: The secret vector \( \mathbf{s} \in \mathbb{Z}_q^w \),
- In the **decision version**: Distinguish between:
  1. Vectors \( (\mathbf{A}, \mathbf{b}) \) generated as described above, and
  2. Vectors \( (\mathbf{A}, \mathbf{b}) \) where \( \mathbf{b} \) is chosen uniformly at random from \( \mathbb{Z}_q^h \).

We often assume that the error distribution \( \chi \) has small standard deviation compared to \( q \).

The second lattice, \( \Lambda_q^T(\boldsymbol{A}) \), is generally referred to as the **dual problem** and is closely related to the **Short Integer Solution (SIS)** problem. Solving the SIS problem involves finding a short vector in \( \Lambda_q^T(\boldsymbol{A}) \).

### SIS Problem Statement
Given:
- A matrix \( \boldsymbol{A} \in \mathbb{Z}_q^{h \times w} \), where entries are uniformly sampled from \( \mathbb{Z}_q \),
- An integer bound \( \beta > 0 \).

To find:
- A nonzero vector \( \boldsymbol{y} \in \mathbb{Z}^w \) such that:
  $$
  \boldsymbol{A} \boldsymbol{y} = \boldsymbol{0} \mod q \quad \text{and} \quad \|\boldsymbol{y}\| \leq \beta
  $$

We often assume that the entries of \( \boldsymbol{A} \) are uniformly distributed over \( \mathbb{Z}_q \). 

## Formal definitions

Firstly, let us assume that q is always prime and that $w>h$. We can state the following definitions.

**Definition 1 :** We define \\(SIS(h, w, q, \beta, p)\\) as follows. Given \\(\bold{A} \in \mathbb{Z}_q^{h\times w}\\) find the short vector \\(\bold{s} \in \mathbb{Z}^w\\)   where \\(0 < \lVert s \rVert_p \leq \beta\\) and $q=\text{poly}(h)$, $m=\text{poly}(h)$, $\beta=\text{poly}(h)$.

We can note that the problem becomes trivial as soon as \\(\beta \geq q\\), no matter the norm used. Also, for the problem not to be vacuous (no possible solutions), we need the select $\beta$ big enough. An often used bound is $\beta > \sqrt{h\log(q)}$

**Definition 2:** The inhomogeneous SIS problem is called \\(ISIS(h, q, w, \beta, p, t)\\) and is defined as follows. Given \\(\bold{A} \in \mathbb{Z}_q^{h \times w}\\) and a target vector \\(\bold{t} \in \mathbb{Z}_q^h\\), find a short vector \\(\bold{s} \in \mathbb{Z}^w\\) such that \\(\bold{A} \bold{s} = \bold{t} \mod q\\) and \\(0 < \lVert \bold{s} \rVert_p \leq \beta\\). Also here $q=\text{poly}(h)$, $m=\text{poly}(h)$, $\beta=\text{poly}(h)$.

*Lemma 1:* SIS and ISIS are considered asymptotically equivalent problem (a reduction exists from one to the other) {{< cite "lyubashevsky2012lattice" >}}.

*Lemma 2:* Solving SIS on $A^T$ directly allows to solve LWE on $A$. They are also considered asymptotically equivalent problems. A reduction exists from SIS to LWE. In the other direction, a quantum reduction exists.

The following theorems are the fundamental theorems that allows us to relate an SIS instance to a known very hard problem (SVP-like).

**Theorem 1:** For any polynomially bounded $w$, $\beta = \text{poly}(h)$, and any prime $q \geq \beta \cdot \omega(\sqrt{h \log h})$, the average-case problems $SIS(h, q, w, \beta)\text{ and } ISIS(h, q, w, \beta)$ are at least as hard as approximating the $SIVP$ problem (among others) in the worst case within a factor of $\gamma = \beta \cdot \tilde{O}(\sqrt{n})$ {{< cite "gentry2008trapdoors" >}}.

This result has been improved for smaller value of the q parameters (nearly equal to the length bound $\beta$). 

**Theorem 2:** For any polynomially bounded $w$, $\beta = \text{poly}(h)$, and any prime $q \geq \beta \cdot h^\delta$, $\delta>0$ some constant, the average-case problems $SIS(h, q, w, \beta)\text{ and } ISIS(h, q, w, \beta)$ are at least as hard as approximating the $SIVP$ problem (among others) in the worst case within a factor of $\gamma = \beta \cdot \tilde{O}(\sqrt{n})$ {{< cite "micciancio2013hardness" >}}. 


This means that to estimate the security given by an SIS instance on which a cryptographic primitive relies, we can estimate the security of solving(or attacking) an SVP-instance. In the following we will present an high overview of possible attacks, and the one we choose to base our security estimates on.


## Attacks on SIS

### Lattice reduction based attacks
Firstly, let's mention lattice reduction that we have already broadly covered, so we use lattice reduction to directly find short vectors. This is the approach we take when estimating the hardness of an SIS instance and we will provide further details on the exact steps for the L-2 and L-infinity norms later on.

### Combinatorial attacks

Combinatorial attacks to solve were proposed in {{< cite "micciancio2009lattice" >}}. They work as follows, given matrix $\bold{A} \in \mathbb{Z}_q^{h \times w}$ and the length bound $b$:
  1. **Divide Columns into Groups**: Split the columns of \( \boldsymbol{A} \) into \( 2^k \) groups, each containing \( \frac{w}{2^k} \) columns, where \( k \) is a parameter to be determined.
  2. **Generate Initial Lists**: For each group, construct a list containing all linear combinations of the columns with coefficients in \( \{-b, \dots, b\} \). Each list contains \( L = (2b + 1)^{w / 2^k} \) vectors in \( \mathbb{Z}_q^h \).
  3. **Combine Lists in Pairs**: Iteratively combine the lists in pairs. For two lists, compute all sums \( \boldsymbol{x} + \boldsymbol{y} \), retaining only those vectors whose first \( \log_q L \) coordinates are zero. This reduces the size of the resulting list to approximately \( L \).
  4. **Repeat Combination**: Continue combining lists until a single list remains after \( k \) iterations. The final list contains vectors that are zero in their first \( k \cdot \log_q L \) coordinates.
  5. **Extract the Short Vector**: The final list contains vectors that are zero in all but their last \( h - k \cdot \log_q L \approx \log_q L \) coordinates. With appropriate parameter choice k, the list is expected to include the all-zero vector, which is a combination of the columns of $\bold{A}$ bounded by $b$ so we can expect to find a short vector.

Algorithms based on this combinatorial attacks but on LWE have also been proposed like BKW {{< cite "blum2003noise" >}} and Coded-BKW {{< cite "guo2015coded" >}}. Combinatorial attacks work well for small dimensions but become quickly unfeasible for larger dimensions. However, one good aspects of combinatorial methods is that they actually exploit the large number of columns \( w \) in \( \boldsymbol{A} \), which lattice reduction do not.

### Via LWE

In the case of solving SIS through LWE, several additional attacks can be considered, such as bounded distance decoding (BDD) {{< cite "lindner2011better" >}}, Arora-Ge attacks {{< cite "arora2011new" >}}, and meet-in-the-middle (MITM) {{< cite "bai2014lattice" >}} approaches. BDD focuses on finding the closest lattice point to a given target, leveraging the fact that LWE can be reduced to a closest vector problem (CVP) in certain cases. Arora-Ge attacks use algebraic techniques to solve LWE by reducing it to a system of polynomial equations, though their practicality diminishes with high noise. MITM attacks exploit combinatorial techniques to reduce the effective complexity of solving LWE by balancing the time and memory trade-offs. As SIS is our focus, we will not delve deeply into these methods.


In the following analysis, we will present the concrete method used to estimate the security of an SIS instance. We will separate the use of the euclidean norm and the infinity norm, using the theory we built in the previous sections (lattice reduction and cost models). What we actually reduce te security to is an H-SVP solver. 

## L2 norm strategy

For the L2 norm strategy, we follow the steps depicted in  {{< cite "micciancio2009lattice" >}} and {{< cite "albrecht2021lattice" >}}. In the following you can assume all log are base 2, except mentioned otherwise. As a first step, we ensure the parameters given don't solve trivially or actually accept solutions by checking that $\beta < q$ and $\beta \geq w\log(q)$.

### Finding the optimal lattice shape for reduction

Given a q-ary lattice \\(\bold{A} \in \mathbb{Z}_q^{h\times w}\\), we can then say with high probability that the rows of $\bold{A}$ are independent over $\mathbb{Z}_q$. As a result of this, the lattice $\Lambda^T_q(\bold{A})$ has $q^{w-h}$ points in $\mathbb{Z}^w_q$. This leads to the volume or determinant of the matrix to be $Vol(\Lambda^T_q(\bold{A})) = q^{h}$. Using the gaussian heuristic from the lattice reduction section, we can express

$$\lambda_1(\Lambda^T_q(\bold{A})) \approx q^\frac{h}{w}\sqrt{\frac{w}{2\pi e}}$$

as an estimate of the lenght of the smallest vector.

It has been experimentally observed that the length of the vector obtained by the best known lattice reduction algorithms on a random w-dimensional q-ary lattice is close to $\min(q, q^\frac{h}{w}\delta^w)$. We can also observe that increasing the $w$ parameter does not make the problem any harder. In fact, we can fix the parameter $w$ by completely letting go a certain number of columns. By plotting $q^{\frac{h}{w}}\delta^w$ as a function of w, the minimum as been determined to be $2^{2\sqrt{h\log q \log \delta}}$ for $w = \sqrt{h\log q / \log \delta}$. So we will always do a lattice reduction over a matrix of size $h\times w'$, where 

$$w' = \sqrt{\frac{h\log q}{\delta}}$$

Indeed, for smaller w the lattice becomes too sparse and does not contain enough vectors to have small ones, and bigger w actually prevents lattice reduction to perform optimally. This leads us to a shortest vector found of length $\min(q, 2^{2\sqrt{h\log q \log \delta}})$. In the calculation for the optimal $w'$, we use the theoretical value for $\delta$ and replace the bound of the smallest vector by $\beta$ because we want a vector that is smaller or equal to $\beta$.

$$\lambda_1(\Lambda^T_q(\bold{A})) = q^\frac{h}{w}\delta^w \Rightarrow \beta = q^\frac{h}{w}\delta^w $$

$$\log \beta = w \log \delta + \frac{h}{w}\log q \Rightarrow \log \delta = \frac{\log \beta}{w} - \frac{h\log q}{w^2}$$

Replacing w by w' we get the theoretical

$$ \log\delta = \frac{\log^2\beta}{4h\log q} $$

### Solve for the required hermite factor

Now that we have the optimal dimension on which to apply our lattice reduction via BKZ, we need to define what the best block-size would be. We know from previously that BKZ achieves 

$$\beta \approx \delta_\beta^{w'-1} Vol(\Lambda)^{\frac{1}{w'}}$$

and by playing around with the equation, we know that our target hermite factor will be

$$\beta^\frac{1}{w'-1} = \delta Vol(\Lambda)^{\frac{1}{w'(w' - 1)}} \Rightarrow \delta \approx \beta \frac{1}{Vol(\Lambda)^{1/w'}}^\frac{1}{w'-1}$$

$$\log\delta = \frac{1}{w'-1}(\log\beta - \frac{h}{w'}\log q)$$

We check that this found $\delta$ makes sense by checking that it is bigger than one. Now that we know what we aim for in terms of hermite factor, the next question is what kind of block-size gives us such a guarantee. For this, we follow {{< cite "cryptoeprint:2015/046" >}} and retake their manually computed $\delta_\beta$ values for small $\beta$ under 40 (that were computed using fpyll) and for bigger block-sizes, we use 

$$\lim_{\beta\rightarrow\infty}\delta_\beta = (\frac{\beta}{2\pi e}(\pi\beta)^\frac{1}{\beta})^\frac{1}{2(\beta - 1)}$$

Once we know the optimal $\beta$, we can check that it also makes sense by checking it is under our matrix size (so under $w'$). If it is, we can go ahead and estimate the actual cost of running BKZ for block-size $\beta$ on a matrix of dimension $\bold{A}\in \mathbb{Z}_q^{h \times w'}$.

### Estimating the security by estimating the BKZ cost

As explained previousl in the lattice reduction chapter, we know estimate our BKZ cost by:
$$cost = \tau \cdot d \cdot T_{SVP}$$
where 
1. the number of tours we do $\tau$ is considered to be 8. 
2. The number of times the SVP oracle is called per tour, which is about the dimension of the lattice
3. The cost of the SVP oracle is $T_{SVP}$

The cost of the SVP oracle is defined by the internal cost that the user will select.

## L-inf norm strategy

As in {{< cite "cryptoeprint:2015/046" >}}, we separate between the Matzov and the Kyber analysis. Both however follow the same high-level strategy. First, we evaluate the security for the L2-bound as explained previously.This will act as a lower bound on the hardness of our infinity bound problem. The strategy is then to analyze the probability of obtaining a vector that respects the infinity bound constraints on all coordinates. When $\sqrt{w}\beta < q$ we apply the analysis in {{< cite "matzov_2022_6493704" >}} and when  $\sqrt{w}\beta \geq q$, we apply the analysis in {{< cite "lyubashevsky2020crystals" >}}. The attack is composed on 2 parts. First, we use lattice reduction to find many short vectors of the lattice. Let's imagine we receive a secret $\bold{y}$ such that $\bold{y} = \bold{A}\bold{x}$ where $\bold{x}$ is short. This is in fact an ISIS instance. Our goal in the second step is to determine with statistical tools if $\bold{y}$ was sampled from the uniform distribution or computed using the secret $\bold{x}$. Imagining for have a statistical distinguisher between the 2 distribution, the attacker can now for each coordinate of the secret $\bold{x}$ guess its value and use the distinguisher to determine if it is good.

### Matzov analysis

The improvements made is that first, the attacker can iterate over several coordinates of the secret at the same time. This lets us recover more coordinate and reduces the dimension of the lattice. The second improvement is the use of the Fast Fourier Transform (FFT) as a dstinghuishing algorithm, which allows to check all guesses simultaneously with a single FFT computation. The computation is actually done in a modulus p smaller than q, with the argument being that since the secret is short, we do not introduce bug errors by doing so. The steps can be summarized in:

1. Generate many pairs of short vectors $(\bold{x}, \bold{y})$. This step is improved by using the dimension for free optimisation for all SVP calls excepts the last and using a different block-size for the last call.
2. Enumerate over guesses of the secret.
3. For each guesses, use the FFT dstinguisher. 

For a detailed overview of the attack, we encourage the reader to go to {{< cite "matzov_2022_6493704" >}} as we will only dig into the cost of the attack.

#### Cost

### Kyber analysis

### The tradeoffs

### The search function


# References
{{< references >}}




