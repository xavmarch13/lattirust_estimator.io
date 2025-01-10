---
weight: 5
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
- A matrix \( \boldsymbol{A} \in \mathbb{Z}_q^{h \times w} \),
- An integer bound \( \beta > 0 \).

To find:
- A nonzero vector \( \boldsymbol{y} \in \mathbb{Z}^w \) such that:
  $$
  \boldsymbol{A} \boldsymbol{y} = \boldsymbol{0} \mod q \quad \text{and} \quad \|\boldsymbol{y}\| \leq \beta
  $$

We often assume that the entries of \( \boldsymbol{A} \) are uniformly distributed over \( \mathbb{Z}_q \). 

## Formal definitions

Firstly, let us assume that q is always prime and that $w>h$. We can state the following definitions.

**Definition 1 :** We define \\(\text{SIS}(h, w, q, \beta, p)\\) as follows. Given \\(\bold{A} \in \mathbb{Z}_q^{h\times w}\\) find the short vector \\(\bold{s} \in \mathbb{Z}^w\\)   where \\(0 < \lVert s \rVert_p \leq \beta\\) and $q=\text{poly}(h)$, $m=\text{poly}(h)$, $\beta=\text{poly}(h)$.

We can note that the problem becomes trivial as soon as \\(\beta \geq q\\), no matter the norm used. Also, for the problem not to be vacuous (no possible solutions), we need the select $\beta$ big enough. An often used bound is $\beta > \sqrt{h\log(q)}$.

**Definition 2:** The inhomogeneous SIS problem is called \\(\text{ISIS}(h, q, w, \beta, p, t)\\) and is defined as follows. Given \\(\bold{A} \in \mathbb{Z}_q^{h \times w}\\) and a target vector \\(\bold{t} \in \mathbb{Z}_q^h\\), find a short vector \\(\bold{s} \in \mathbb{Z}^w\\) such that \\(\bold{A} \bold{s} = \bold{t} \mod q\\) and \\(0 < \lVert \bold{s} \rVert_p \leq \beta\\). Also here $q=\text{poly}(h)$, $m=\text{poly}(h)$, $\beta=\text{poly}(h)$.

*Lemma 1:* SIS and ISIS are considered asymptotically equivalent problem (a reduction exists from one to the other) {{< cite "lyubashevsky2012lattice" >}}.

*Lemma 2:* Solving SIS on $A^T$ directly allows to solve LWE on $A$. They are also considered asymptotically equivalent problems. A reduction exists from SIS to LWE. In the other direction, a quantum reduction exists.

The following theorems are the fundamental theorems that allows us to relate an SIS instance to a known very hard problem (SVP-like).

**Theorem 1:** For any polynomially bounded $w$, $\beta = \text{poly}(h)$, and any prime $q \geq \beta \cdot \omega(\sqrt{h \log h})$, the average-case problems $\text{SIS}(h, q, w, \beta)\text{ and } \text{ISIS}(h, q, w, \beta)$ are at least as hard as approximating the $\text{SIVP}$ problem (among others) in the worst case within a factor of $\gamma = \beta \cdot \tilde{O}(\sqrt{n})$ {{< cite "gentry2008trapdoors" >}}.

This result has then been improved for smaller value of the q parameters (nearly equal to the length bound $\beta$). 

**Theorem 2:** For any polynomially bounded $w$, $\beta = \text{poly}(h)$, and any prime $q \geq \beta \cdot h^\delta$, $\delta>0$ some constant, the average-case problems $\text{SIS}(h, q, w, \beta)\text{ and } \text{ISIS}(h, q, w, \beta)$ are at least as hard as approximating the $\text{SIVP}$ problem (among others) in the worst case within a factor of $\gamma = \beta \cdot \tilde{O}(\sqrt{n})$ {{< cite "micciancio2013hardness" >}}. 


This means that to estimate the security given by an SIS instance on which a cryptographic primitive relies, we can estimate the security of solving(or attacking) an SVP-instance. In the following we will present an high overview of possible attacks, and the one we choose to base our security estimates on.


## Attacks on SIS

### Lattice Reduction-Based Attacks  
Firstly, let us mention lattice reduction, which we have already broadly covered. Lattice reduction is used to directly find short vectors. This approach is commonly employed when estimating the hardness of an SIS instance, and we will provide further details on the exact steps for the \( \ell_2 \) and \( \ell_\infty \) norms later on.

### Combinatorial Attacks  

Combinatorial attacks for solving SIS were proposed in {{< cite "micciancio2009lattice" >}}. These attacks proceed as follows, given a matrix \( \boldsymbol{A} \in \mathbb{Z}_q^{h \times w} \) and a length bound \( b \):  

1. **Divide Columns into Groups**: Split the columns of \( \boldsymbol{A} \) into \( 2^k \) groups, each containing \( \frac{w}{2^k} \) columns, where \( k \) is a parameter to be determined.  
2. **Generate Initial Lists**: For each group, construct a list containing all linear combinations of the columns with coefficients in \( \{-b, \dots, b\} \). Each list contains \( L = (2b + 1)^{w / 2^k} \) vectors in \( \mathbb{Z}_q^h \).  
3. **Combine Lists in Pairs**: Iteratively combine the lists in pairs. For two lists, compute all sums \( \boldsymbol{x} + \boldsymbol{y} \), retaining only those vectors whose first \( \log_q L \) coordinates are zero. This reduces the size of the resulting list to approximately \( L \).  
4. **Repeat Combination**: Continue combining lists until a single list remains after \( k \) iterations. The final list contains vectors that are zero in their first \( k \cdot \log_q L \) coordinates.  
5. **Extract the Short Vector**: The final list contains vectors that are zero in all but their last \( h - k \cdot \log_q L \approx \log_q L \) coordinates. With an appropriate choice of \( k \), the list is expected to include the all-zero vector, which corresponds to a combination of the columns of \( \boldsymbol{A} \) bounded by \( b \). Hence, we can expect to find a short vector.

Algorithms based on these combinatorial attacks, but applied to LWE, have also been proposed, such as BKW {{< cite "blum2003noise" >}} and Coded-BKW {{< cite "guo2015coded" >}}.  

Combinatorial attacks work well for small dimensions but become quickly infeasible for larger dimensions. However, one advantage of combinatorial methods is that they exploit the large number of columns \( w \) in \( \boldsymbol{A} \), which lattice reduction methods do not.

### Attacks via LWE  

In the context of solving SIS through LWE, several additional attacks can be considered, such as bounded distance decoding (BDD) {{< cite "lindner2011better" >}}, Arora-Ge attacks {{< cite "arora2011new" >}}, and meet-in-the-middle (MITM) approaches {{< cite "bai2014lattice" >}}:  

- **BDD** focuses on finding the closest lattice point to a given target, leveraging the fact that LWE can be reduced to a closest vector problem (CVP) in certain cases.  
- **Arora-Ge attacks** use algebraic techniques to solve LWE by reducing it to a system of polynomial equations, though their practicality diminishes with higher noise levels.  
- **MITM attacks** exploit combinatorial techniques to reduce the effective complexity of solving LWE by balancing time and memory trade-offs.  

Since SIS is our focus, we will not delve deeply into these methods.  

### Conclusion  

In the following analysis, we will present the concrete method used to estimate the security of an SIS instance. We will separately analyze the use of the Euclidean norm and the infinity norm, using the theory developed in the previous sections (lattice reduction and cost models). Ultimately, we reduce the security assessment to solving an \( \text{H-SVP} \) instance.


## L2 norm strategy

For the L2 norm strategy, we follow the steps depicted in  {{< cite "micciancio2009lattice" >}} and {{< cite "albrecht2021lattice" >}}. In the following you can assume all logarithms are base 2, unless stated otherwise. As a first step, we ensure the parameters given do not solve trivially or actually accept solutions by checking that $\beta < q$ and $\beta \geq w\log(q)$.

### Finding the optimal lattice shape for reduction

Given a q-ary lattice \\(\bold{A} \in \mathbb{Z}_q^{h\times w}\\), we can then say with high probability that the rows of $\bold{A}$ are independent over $\mathbb{Z}_q$. As a result of this, the lattice $\Lambda^T_q(\bold{A})$ has $q^{w-h}$ points in $\mathbb{Z}^w_q$. This leads to the volume or determinant of the matrix being $Vol(\Lambda^T_q(\bold{A})) = q^{h}$. Using the gaussian heuristic from the lattice reduction section, we can express

$$\lambda_1(\Lambda^T_q(\bold{A})) \approx q^\frac{h}{w}\sqrt{\frac{w}{2\pi e}}$$

as an estimate of the length of the smallest vector.

It has been experimentally observed that the length of the vector obtained by the best-known lattice reduction algorithms on a random w-dimensional q-ary lattice is close to $\min(q, q^\frac{h}{w}\delta^w)$. We can also observe that increasing the $w$ parameter does not make the problem any harder. In fact, we can fix the parameter $w$ by completely letting go a certain number of columns. By plotting $q^{\frac{h}{w}}\delta^w$ as a function of w, the minimum as been determined to be $2^{2\sqrt{h\log q \log \delta}}$ for $w = \sqrt{h\log q / \log \delta}$. Thus, we will always do a lattice reduction over a matrix of size $h\times w'$, where 

$$w' = \sqrt{\frac{h\log q}{\delta}}$$

Indeed, for smaller w the lattice becomes too sparse and does not contain enough vectors to have small ones, while bigger w prevents lattice reduction to perform optimally. This leads us to a shortest vector found of length $\min(q, 2^{2\sqrt{h\log q \log \delta}})$. In the calculation for the optimal $w'$, we use the theoretical value for $\delta$ and replace the bound of the smallest vector by $\beta$ because we want a vector that is smaller or equal to $\beta$.

$$\lambda_1(\Lambda^T_q(\bold{A})) = q^\frac{h}{w}\delta^w \Rightarrow \beta = q^\frac{h}{w}\delta^w $$

$$\log \beta = w \log \delta + \frac{h}{w}\log q \Rightarrow \log \delta = \frac{\log \beta}{w} - \frac{h\log q}{w^2}$$

Replacing w by w' we get the theoretical

$$ \log\delta = \frac{\log^2\beta}{4h\log q} $$

### Solve for the required hermite factor

Now that we have the optimal dimension on which to apply our lattice reduction via BKZ, we need to define what the best block-size would be. We know from previously that BKZ achieves 

$$\beta \approx \delta_\beta^{w'-1} Vol(\Lambda)^{\frac{1}{w'}}$$

and by rearranging the equation, we know that our target hermite factor is

$$\beta^\frac{1}{w'-1} = \delta Vol(\Lambda)^{\frac{1}{w'(w' - 1)}} \Rightarrow \delta \approx \beta \frac{1}{Vol(\Lambda)^{1/w'}}^\frac{1}{w'-1}$$

$$\log\delta = \frac{1}{w'-1}(\log\beta - \frac{h}{w'}\log q)$$

We verify that the computer $\delta$ is valid by checking that it is greater than one. Now that we know what we aim for in terms of hermite factor, the next question is what kind of block-size gives us such a guarantee. For this, we follow {{< cite "cryptoeprint:2015/046" >}} and use their manually computed $\delta_\beta$ values for small $\beta$ under 40 (that were computed using fpylll) and for bigger block-sizes, we use 

$$\lim_{\beta\rightarrow\infty}\delta_\beta = (\frac{\beta}{2\pi e}(\pi\beta)^\frac{1}{\beta})^\frac{1}{2(\beta - 1)}$$

Once we know the optimal $\beta$, we can check that it also makes sense by checking it is under our matrix size (so under $w'$). If it is, we can go ahead and estimate the actual cost of running BKZ for block-size $\beta$ on a matrix of dimension $\bold{A}\in \mathbb{Z}_q^{h \times w'}$.

### Estimating the security by estimating the BKZ cost

As explained previously in the lattice reduction chapter, we know estimate our BKZ cost by:

$$cost = \tau \cdot d \cdot T_{SVP}$$
where 
1. the number of tours we do $\tau$ is considered to be 8. 
2. The number of times the SVP oracle is called per tour, which is approximately equal to the dimension of the lattice
3. The cost of the SVP oracle is $T_{SVP}$

The cost of the SVP oracle is defined by the internal cost that the user will select.

## L-inf norm strategy

As in {{< cite "cryptoeprint:2015/046" >}}, we distinguish between the Matzov and Kyber analyses. Both, however, follow the same high-level strategy. First, we evaluate the security for the \( \ell_2 \)-bound as explained previously. This will serve as a lower bound on the hardness of our \( \ell_\infty \)-bound problem. The strategy then involves analyzing the probability of obtaining a vector that satisfies the \( \ell_\infty \)-bound constraints on all coordinates. When \( \sqrt{w}\beta < q \), we apply the analysis from {{< cite "matzov_2022_6493704" >}}, and when \( \sqrt{w}\beta \geq q \), we apply the analysis from {{< cite "lyubashevsky2020crystals" >}}.

The attack is composed of two parts. First, we use lattice reduction to find many short vectors of the lattice. Then, we assume that some (Kyber analysis) or all (Matzov analysis) of these vectors follow independent Gaussian distributions and analyze the probability of obtaining at least one short vector that satisfies the \( \ell_\infty \)-bound constraints on all coordinates. The difference in the style of analysis comes down to the assumptions made about the sampled vectors.

### Matzov analysis

Matzov introduces an enhanced method for sampling many short vectors from running BKZ in his paper. First, the algorithm runs BKZ with a block size \( \beta_1 \) to find a reduced basis of \( \Lambda \). In this phase, we apply the dimension-for-free technique {{< cite "ducas2018shortest" >}} since we only require a reduced basis. In the second step, we perform sieving with another block size \( \beta_2 \) without the dimension-for-free technique because, in this step, we aim to output as many short vectors as possible and use all of them.


The algorithm can be summarized as

>**Short Vectors Sampling Procedure**
>1. **Input:** A basis $B = (b_1, \ldots, b_d)$ for a lattice, integers $\beta_1$, $\beta_2 \leq d$, and the desired number of short vectors $D$. Let $N_{sieve}(\beta_2)$ be the number of vectors after performing lattice sieving with a block-size of $\beta_2$.
>2. **Output:** A list of at least $D$ short vectors from the lattice.
>3. For $i = 1, \ldots, \left\lceil \frac{D}{N_{\text{sieve}}(\beta_2)}\right\rceil$:
>     - Randomize the basis $B$.
>     - Run BKZ$_{d, \beta_1}$ on $B$ to obtain a reduced basis $(b'_1,   \ldots, b'_d)$.
>     - Run a sieve of dimension $\beta_2$ on the sublattice  $$(b'_1,   \ldots, b'_{\beta_{2}})$$ to obtain a list of vectors and add them to $L$.
>4. Return $L$.

Note that a similar procedure was presented in {{< cite "guo2021faster" >}}. We consider the cost of this algorithm to be 

$$
\lceil\frac{D}{N_{\text{sieve}}(\beta_2)}\rceil(T_{BKZ(d, \beta_1)} + T_{sieve}(\beta_2))
$$

also, the expected length of vectors generated by the algorithm can be approximated as

$$
\ell \approx \sqrt{\frac{4}{3}} \cdot \sqrt{\frac{\beta_2}{2\pi e}}\delta(\beta_1)^{\frac{d-\beta_2}{2}}
$$

Most importantly, we then make the assumption that if \( \ell \) is the expected length of the vectors from the short vectors sampling procedure, then the coordinates of the returned vectors have approximately independent Gaussian distributions with mean 0 and standard deviation \( \frac{\ell}{ \sqrt{d}} \). This means that if we want the probability of \( d \) vectors satisfying the infinity bound, we can compute it via:

$$p = (2*\Phi(\text{length bound}_{\infty}) - 1) ^ d \text{ where } \Phi \sim \mathcal{N}(0, \frac{\ell}{ \sqrt{d}})$$

When we find a block size and a number of dimensions for which we have a non-negligible probability of finding such vectors, we repeat the experiment until we achieve the desired success probability. When repeating the experiment, we multiply the cost of the attack by a multiplier. As a generalization, and to avoid overwhelming the user with customization possibilities, we fix the overall target probability of success to 0.99. Additionally, for most parameters relevant to cryptography, we follow a remark from the original authors that approximates \( N_{\text{sieve}}(\beta_2) \approx D \).

We also use the same assumptions as the paper for the cost of progressive sieving and progressive BKZ, which are:

- $
T_{sieve}(\beta) = \frac{1}{1-2^{-0.292}} * T_{sieving subroutine}(\beta)
$

- $
T_{BKZ}(d, \beta) = (\frac{1}{1-2^{-0.292}})^2(d-\beta+1) * T_{sieving subroutine}(\beta_{eff})
$

where \( \beta_{eff} \) comes from the dimension-for-free calculation, where to solve SVP with sieving in \( \beta \) you only need to do it in \( \beta_{eff} = \beta - \frac{\beta\log(4/3)}{\log(\beta/2\pi e)} \). The paper also provides new sieving cost estimates within an improved routine.


### Kyber analysis

In this analysis, we do not consider only the GSA but also the \( q \)-ary structure of the lattice. As such, we assume that BKZ can now produce \( q \)-vectors at the given length bound. Only the middle region of the basis, after lattice reduction, will produce independent Gaussian vectors, as in the Matzov analysis. This is why, when sampling our vectors, we only keep the vectors starting from the zone where they are no longer \( q \)-ary and ending before they become unit vectors.

To account for the \( q \)-ary structure, the simulator used is automatically ZGSA, or if necessary, you can use LGSA, which removes \( q \)-vectors by applying rerandomization. The standard deviation used to calculate the probability of vectors satisfying the \( \ell_\infty \)-bound is then corrected to \( \frac{l}{d'} \), where \( d' \) is the number of coordinates assumed to follow Gaussian distributions after removing \( q \)-vectors. The probability and costs are then computed similarly to the Matzov analysis.



### Iterating over the dimenstion and block-sizes

Now that we have a cost for evaluating the infinity norm for a specified block-size and number of columns, we still need to find the optimal parameters. In contrary to the L2 setting, we have no theoretical idea what would be the preferred number of columns to use (or to forget) to solve the underlying SIS instance or the best block-size. As such, we made the choice to iterate over all possible dimensions and blocksizes in a grid-search manner. We use the optimal number of columns for the L2 norm and the corresponding optimal block-size for the L2 norm as an upperbound on our block-size seach and go through all possible number of columns. Given the possibly large search space, we opted for an adaptive grid seach that first coarsely goes through the grid followed by a much finer grid seach on a restricted space. The returned cost is the result of this grid seach evalutation. Our grid search step sizes are automatically adapted to the size of the SIS instance and we ge through a much wider steo_size for the number of columns to forget than for the actual block size as we assumed that it is the parameter that has most impact.

### **Points of Improvement**  

While all our search methods perform well for the Euclidean norm, the wide parameter space for optimizing the \( \ell_\infty \)-norm causes some search functions to become slow or get stuck, particularly when using compact versions of SIS and relatively large parameters.  

One possible improvement is to select narrower bounds for the search, though this comes with the risk of missing optimal parameters. This raises the question of how to define effective bounds. Another potential approach is to optimize each parameter individually using a nested partial function search, as described in {{< cite "cryptoeprint:2015/046" >}}. Specifically, we could first optimize the number of parameters to ignore and, for each local minimum, further optimize the block size.
 

# References
{{< references >}}




