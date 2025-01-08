---
weight: 3
bookFlatSection: true
title: "Lattice reduction"
math: true
---

# How to solve SVP ?

The way we solve the Shortest Vector Problem (SVP) and similar problems depends significantly on the lattice dimension. In lower dimensions, exact solvers are practical, and there are two main approaches: <tag>**enumeration and sieving**</tag>. Both methods perform an exhaustive search over all short lattice vectors—enumeration does this deterministically, while sieving is typically randomized. However, as the lattice dimension grows, the number of possible solutions increases exponentially, making these methods infeasible for high dimensions (think 100 and more).

In higher dimensions, we rely on **approximation algorithms**, better known as lattice reduction algorithms. These algorithms don’t find the exact solution but instead provide an approximation where the vector length is upper bounded by a function of the dimension. Lattice reduction can be thought of as the algorithmic equivalent of inequalities like **Hermite’s** and **Mordell’s** {{< cite "nguyen2009hermite" >}}, which bound the shortest vector length in theoretical terms.

- Hermite's inequality: $$\forall d \geq 2: \gamma_d \leq \left(\sqrt{\frac{4}{3}}\right)^{d-1}$$
- Mordell's inequality: $$\forall d,k \text{ such that } 2\leq k \leq d: \gamma_d \leq \sqrt{\gamma_k}^{\frac{d-1}{k-1}}$$

In practice, both exact and approximate solvers are used together. Exact solvers usually start with a preprocessing step using lattice reduction to simplify the problem. On the other hand, lattice reduction algorithms often call exact solvers as subroutines, using them multiple times during their process. This section focuses on explaining the cost of approximation algorithms such as LLL and BKZ as a whole, and we will refer to the cost of the exact SVP solver used as a subroutine as the "cost models," described in the next section.

## Lattice reduction 

*Lattice reduction algorithms* are designed to transform a given basis of a lattice into a "reduced" basis, where the vectors are shorter and closer to being orthogonal. While a lattice does not generally have an orthogonal basis (unlike in Euclidean space), the goal of lattice reduction is to produce a basis that approximates orthogonality as closely as possible. This transformation simplifies solving challenging lattice problems such as the Shortest Vector Problem (SVP).

This section is based on the works of {{< cite "nguyen2009hermite" >}}, {{< cite "nguyen2010lll" >}}, {{< cite "chen2011bkz" >}}, {{< cite "gama2006rankin" >}}, {{< cite "lenstra1982factoring" >}}, {{< cite "albrecht2021lattice" >}}, and {{< cite "gama2008predicting" >}}.

{{< figure src="lattice_orthogonal.png" alt="Depiction of a closer to orthogonal basis" caption="Two different bases, one being close to orthogonal." >}}

### Key Lattice Reduction Algorithms

- **LLL Algorithm (Lenstra–Lenstra–Lovász)**: This algorithm produces a reduced basis in polynomial time. The resulting basis vectors are guaranteed to be within a known factor of the shortest vector, although the algorithm does not necessarily find the shortest vector itself {{< cite "lenstra1982factoring" >}}.
- **BKZ Algorithm (Block Korkine-Zolotarev)**: A more advanced generalization of the LLL algorithm that provides stronger reductions at the cost of increased computational complexity. The BKZ algorithm divides the lattice into overlapping blocks and applies LLL reduction within each block, achieving more accurate approximations of the shortest vector {{< cite "gama2006rankin" >}}.

### Cost Considerations

Both LLL and BKZ make iterative local improvements to a basis by using an exact SVP solver (often referred to as an SVP oracle). Therefore, the overall cost of the algorithm can be divided into two components:

1. **Local cost**: The computational cost associated with solving the SVP within each block.
2. **Global cost**: The number of times the algorithm needs to invoke the SVP oracle during the entire basis reduction process.

Together, these components determine the total cost of performing lattice reduction.


### Gram-Schmidt Orthogonalization

Gram-Schmidt orthogonalization is a method for orthonormalizing a set of vectors in an inner product space, most commonly the Euclidean space $\mathbb{R}^n$. The process transforms a set of linearly independent vectors into an orthogonal set of vectors that spans the same subspace.

Given a set of linearly independent vectors $\{\mathbf{b}_1, \mathbf{b}_2, \ldots, \mathbf{b}_n\}$, the Gram-Schmidt process produces an orthogonal set $\{\mathbf{b}^{\*}_1, \ldots, \mathbf{b}^*_n\}$ as follows:

1. $\mathbf{b}_1^* = \mathbf{b}_1$  
2. For $i = 2$ to $n$:
   $$
   \mathbf{b}_i^* = \mathbf{b}_i - \sum_{j=1}^{i-1} \text{proj}_{\mathbf{b}_j^*}(\mathbf{b}_i)
   $$
   where $\text{proj}_{\mathbf{b}_j^{\*}}(\mathbf{b}_i)$ is the projection of $\mathbf{b}_i$ onto $\mathbf{b}_j^*$, given by:
   $$
   \text{proj}_{\mathbf{b}_j^*}(\mathbf{b}_i) = \frac{\langle \mathbf{b}_i, \mathbf{b}_j^* \rangle}{\langle \mathbf{b}_j^*, \mathbf{b}_j^* \rangle} \mathbf{b}_j^*.
   $$

Gram-Schmidt orthogonalization is widely used in lattice reduction because it allows the basis to be triangularized. More precisely, it transforms the basis into the following form:

$$
\begin{pmatrix}
\|\mathbf{b}_1^*\| & 0 & \ldots & 0 \\
\mu_{2,1} \|\mathbf{b}_1^*\| & \|\mathbf{b}_2^*\| & \ldots & 0 \\
\vdots & \vdots & \ddots & \vdots \\
\mu_{d,1} \|\mathbf{b}_1^*\| & \mu_{d,2} \|\mathbf{b}_2^*\| & \ldots & \|\mathbf{b}_d^*\|
\end{pmatrix}
$$

Thus, $B = \mu B^{\*}$ {{< cite "gama2008predicting" >}}{{< cite "nguyen2009hermite" >}}. We can confirm from this matrix that $\text{Vol}(\Lambda) = \prod_{i=1}^{d} \lVert \bold{b}_i^* \rVert$ (the determinant is the product of the diagonal elements).

We can also state the following lemma that relates the shortest vector to the Gram-Schmidt vectors for all $1 \leq i \leq d$:

$$
\lambda_i(\Lambda) \geq \min_{i \leq j \leq d} \|\mathbf{b}_j^*\|.
$$

*Remember that the volume is an invariant, so not all Gram-Schmidt vectors can be small at the same time.*

### Root hermite factor

We will next want to introduce a value called the *root hermite factor*. It is a measure used in lattice reduction theory to evaluate the quality of a reduced lattice basis. It is commonly used to assess the effectiveness of lattice reduction algorithms. 

$$
\delta = \left( \frac{\|\mathbf{b}_1\|}{\text{vol}(\Lambda)^{1/d}} \right)^{1/d}
$$
for a d-dimensional lattice.

The closer $\delta$ gets to 1, the better the reduction quality will be. This is of direct impact in our context, since we need to balance a trade-off between the quality of the output basis and the cost of running our lattice reduction algorithm. In fact, with the BKZ algorithm which has become the standard, a bigger block-size leads to a better quality of output basis (so a better $\delta_\beta$) but also a greater computational cost.

### Geometric Series Assumption (GSA)

How large the minimas can be after lattice reduction is therefore looking for how short we expect the basis vectors to be after applying lattice reduction (which contains Gram-Schmidt orthogonalization). It is often useful to look at the length of all gram-schmidt vectors, not only the first one. As a small experiment, let us consider a basis in $\mathbb{Z}_q$ and let us compute the Gram-Schmidt orthogonalization. If we look at the log of these lengths we obtain the Z-shape because the first are orthogonal components of q magnitude and the rest are all vectors of length 1:

{{< figure src="before_LLL.png" alt="Log lengths of GS vectors" caption="Length of log GS vectors" >}}

If we now apply a lattice reduction algorithm (here LLL), we will obtain this: 

{{< figure src="after_LLL.png" alt="Log lengths of GS vectors after LLL" caption="Length of vectors after LLL" >}}

You can observe this merely looks like a straight line and indeed this is the assumption that we will make. *The Geometric Series Assumption* conceptually tells us that the Gram-Schmidt vectors log-length outputed by a lattice reducion algorithm will follow a geometric series and such a line in log lengths. We can formulate it as:

$$\lVert \bold{b_i}^*\rVert \approx \alpha^{i-1}\lVert \bold{b_1}\rVert$$

and in fact by combining it with the fact that output vector of lattice reduction follow $\lVert \bold{b_0} \rVert = \delta_0 Vol(\Lambda)^{\frac{1}{d}}$ we can get a relation between the quality of the reduction and the slope of the GSA assumption as $\alpha \approx \delta^{-2}$ leading to 

$$ \lVert \bold{b_i}^*\rVert \approx \alpha^{i-1} \delta^d Vol(\Lambda)^\frac{1}{d} = \delta^{-1(i+1) + d} Vol(\Lambda)^\frac{1}{d}$$


## LLL algorithm

The Lenstra-Lenstra-Lovász (LLL) algorithm is an efficient polynomial-time algorithm that finds a "nearly orthogonal" basis for a given lattice. It aims to transform any arbitrary basis of a lattice into a reduced basis where the basis vectors are short and close to orthogonal following two conditions:

1. Size reduction:
$$1 \leq j < i \leq d\colon \left|\mu_{i,j}\right|\leq 0.5 \text{ for } \mu_{i,j} =\frac{\langle\mathbf{b}_i,\mathbf{b}^*_j\rangle}{\langle\mathbf{b}^*_j,\mathbf{b}^*_j\rangle}$$
1. Lovász condition:
For $k=2,...,d$ $$\omega \Vert \mathbf{b}^*_{k-1}\Vert^2  \leq \Vert \mathbf{b}^*_k\Vert^2+ \mu_{k,k-1}^2\Vert\mathbf{b}^*_{k-1}\Vert^2$$

We say the basis is LLL-reduced if there exists a parameter $\omega \in (0.25, 1)$. Hereafter we give a pseudo code of the algorithm and a graphical example of its run.

      Data: a basis B
      Repeat until no changes:
         for k = 0 to d-1:
            # Step 1: Gram-Schmidt Orthogonalization (GSO)
            for i = k+1 to d-1:
                  mu[k, i] = <B[k], B[i]> / <B[k], B[k]>
                  B[i] = B[i] - mu[k, i] * B[k] 
            
            # Step 2: Size Reduction
            for i = k+1 to d-1:
                  if |mu[k, i]| >= 1/2:
                     v = round(mu[k, i])  
                     B[i] = B[i] - v * B[k]  

            # Step 3: Swap if necessary
            if <B[k+1], B[k+1]> < (delta * <B[k], B[k]>):
                  Swap B[k], B[k+1]

      end

{{< iframe src="/graphs/lll.html" width="100%" height="600" title="My Graph" caption="Example of the LLL algorithm running." >}}

Although there are theorems bounding the worst-case performance of lattice reduction algorithms, they tend to perform better in practice. Reasoning about the behavior of such algorithms has therefore become a matter of heuristics and approximations. Typically, the vectors that are output by the LLL algorithm are said to follow the geometric series assumption in their length. Again, this assumption tells us that the shape after lattice reduction forms a line with a flatter slope as the reduction becomes stronger. The goal of a lattice reduction algorithm can therefore be visualized by examining a plot of the log-length of vectors after reductions. The overall goal is to flatten this line, leading to a smaller basis.

### Cost of LLL

The theoretical bound on the quality of the LLL is $\delta^d = (\frac{4}{3})^\frac{d-1}{4}$ {{< cite "nguyen2009hermite" >}} leading to approximately $\delta \approx 1.075$. In practice, we get much better results on average, empirically about $\delta \approx 1.021$ .

In terms of runtime, we will consider a heuristic bound (which better approximates empirical results) of $O(d^3 \log^2(B))$.


## BKZ algorithm

The Block Korkine-Zolotarev (BKZ) algorithm is a lattice reduction algorithm that generalizes the LLL algorithm to achieve stronger reduction properties. The BKZ algorithm is defined as a blockwise reduction algorithm that iteratively applies a form of lattice basis reduction to overlapping blocks of vectors within the basis. The assumption made in its analysis is that iterative blocks taken behaves like a random lattice. It is in fact a relaxation of the Hermite-Korkine-Zolotarev (HKZ) reduction. The HKZ reduction is a stronger form of lattice reduction that ensures each vector in the basis is the shortest vector in the lattice projected orthogonally onto the space spanned by the preceding basis vectors.

### HKZ reduction

Let **b**₁, **b**₂, ..., **b**ₙ be a basis of a lattice **L**.  
The basis is said to be HKZ-reduced if:  
1. **b**₁ is the shortest vector in the lattice **L**.  
2. For *i* = 2, 3, ..., *n*, the vector **b**ᵢ is the shortest vector in the lattice **L** projected orthogonally onto the span of **b**₁, **b**₂, ..., **b**ᵢ₋₁.

### BKZ

Assuming we have an SVP oracle, the BKZ algorithm is defined as follows: 

    Data: LLL-reduced basis B (preprocessed) and block size beta
    repeat until no changes
        for k in 0 to d-2
            LLL on local projected block B[k, ..., min(k+beta, d)]
            v <-- SVP-Oracle(local projected block B[k, ..., min(k+beta, d)])
            insert v into B at index k and handle linear dependencies with LLL
        end

In practice, stronger preprocessing than LLL is often performed before the BKZ block loop. Hereafter is a simple representation of a block moving through a matrix for one tour of BKZ, in practice we often do several tours because we are supposed to stop when no more significant changes occur.

{{< iframe src="/graphs/bkz.html" width="100%" height="500" title="Interactive Graph" caption="Example of the BKZ algorithm block sliding." >}}


Theoretically, a BKZ-$\beta$ reduced basis satisfies, for $\epsilon > 0$:

$$\lVert \bold{b_0} \rVert \leq \sqrt{(1 + \epsilon) \gamma_{\beta}}^{(\frac{d-1}{\beta - 1} + 1)} Vol(\Lambda(\bold{B}))$$

, where $$\gamma_\beta =  \sup \{ \lambda_1(\Lambda) | \Lambda \in \mathbb{R}^\beta, Vol(\Lambda) = 1 \}$$

Several variants of BKZ exist, the one giving asymptotically the best worst-case guarantees is the Slide reduction described in {{< cite "gama2008finding" >}} and it achieves

$$\lVert \bold{b_0} \rVert \leq \sqrt{(1 + \epsilon) \gamma_{\beta}}^\frac{d-1}{\beta - 1} Vol(\Lambda(\bold{B}))$$


By combining the gaussian heuristic and the definition of a BKZ-$\beta$ reduced basis, we arrive again at the geometric assumption, which states that the log-lengths of reduced vectors follow a geometric series (which we can plot as a line as we did for LLL). This time however, it depends on the block-size chosen to run BKZ.

We can write

$$\log(\lVert \bold{b_i}^*\rVert) = \frac{d - 1 - 2i}{2}\log(\alpha_\beta) + \frac{1}{d}\log(Vol(\Lambda))$$

where $\alpha_\beta$ is the slope under the geometric assumption that can be calculated from the gaussian assumption as 

$$\alpha_\beta = \sqrt{\frac{d}{2\pi e}}^\frac{2}{\beta - 1}$$

This result from {{< cite "albrecht2021lattice" >}} is reasonably accurate only if $d\gg\beta$ and $\beta > 50$, which is why we will use fixed estimates for small dimensions (also, small dimension can be directly solved by an exact solver). 

### Cost of BKZ

* Costing BKZ means having a good idea of the impact of the block-size on the quality of our reduced basis. For this, we could either make the approximation $\delta_\beta \approx \sqrt \alpha_\beta$ or use the following limit defined in {{< cite "chen2013reduction" >}} that works well for $\beta > 50$ and typical $d$ used in cryptography {{< cite "albrecht2021lattice" >}}.

$$\lim_{\beta\rightarrow\infty}\delta_\beta = (\frac{\beta}{2\pi e}(\pi\beta)^\frac{1}{\beta})^\frac{1}{2(\beta - 1)}$$

and write for SIS

$$\lVert \bold{b_1} \rVert \approx \delta_\beta^{d-1} Vol(\Lambda)^{\frac{1}{d}}$$


* Costing BKZ as a whole is complicated because we do not know how many tours we will have to run, which means we don't really know in advance the number of SVP-Oracle calls we will have to make. Furthermore, many improvements on plain BKZ have been made when some techniques are used as a subroutine for the oracle (for example extreme pruning in the context of enumeration), which makes security estimates done via lattice reduction very sensitive to many factors. Also, local preprocessing techniques have been introduced as part of the algorithm in a variant of BKZ known as progressive BKZ. To make our tool comparable to the lattice estimator by {{< cite "cryptoeprint:2015/046" >}}, we will follow the same simplifying assumption and consider a consistent 8 tours of BKZ. This makes sense following experimental results that showed that most progress is made in the 7-9 first tours. We will then use:
$$cost = \tau \cdot d \cdot T_{SVP}$$
where 
1. the number of BKZ tours we do $\tau$ is considered to be 8. 
2. The number of times the SVP oracle is called per tour, which is about the dimension of the lattice d
3. The cost of the SVP oracle is $T_{SVP}$


## Refinements

A good history of the refinements that have been made about BKZ runtime and the way we assume security in problems based on lattice reduction can be found in {{< cite "albrecht2021lattice" >}}. Indeed, it appears some of our assumptions are not entirelly true, especially GSA. In the estimator, most refinements are either directly modelled into a new simulator for the GSA shape or through adjusted cost models of SVP oracles. The way we cost BKZ as a whole stays the same.

### The first GSA lie

If we think more about the way we slide the block through the matrix during lattice reduction, we can easily come to the conclusion that something different must happen at the end. Indeed, the GSA assumptions ignores what happens for the last $d-\beta$ coordinates. This last block is in fact HKZ reduced and we can therefore adapt the tail of our assumption leading to Tail-adapted GSA (TGSA). 

Let us use the following definition for the HKZ-shape in dimension d. For $i=0, \ldots , d-1:$

$$
h_i = \log\left(\sqrt{\frac{d - i}{2\pi e}}\right) - \frac{1}{d-1} \sum_{j < i} h_j,
$$

Now using this in modifying our BKZ GSA assumed shape we can get the following two parts equation:

$$
\log\left(\|b_i^*\|\right) =
\begin{cases} 
\frac{d - 1 - 2i}{2} \log(\alpha \beta) + s, & \text{for } 0 \leq i \leq d - \beta, \\
h_{i-(d-\beta)} + \log\left(\|b_{d-\beta}^*\|\right) - h_0, & \text{for } d-\beta \leq i < d.
\end{cases}
$$

### The second GSA lie and Z(T)GSA

Geometric series assumptions (tail-adapted or not) have been shown to be a bad choice for small dimensions (think 50 and below) and when $d$ is a multiple of $\beta$. Furthermore, these assumptions only give an estimate about the size after the algorithm is finished and not the evolution of vectors through it. We will here just mention that an estimator has been introduced in {{< cite "chen2011bkz" >}} to take these observations into accounts. Thanks to its implementation in FPyLLL, we will make use of it to calculate the cost of BKZ for small values (by simply hardcoding the needed values).

Because we will work with q-ary lattices,  they will always contain vector $(q, 0, \ldots, 0)$ and its permutation. These vectors can be considered short in certain circonstances and shorter that what GSA would predict. A ZGSA assumption adapted to such lattices is possible, but it remains unsure what such an assumption would look like on block reduction algorithms.
 
### BKZ 2.0 improvements

BKZ 2.0 {{< cite "chen2011bkz" >}}  introduces several enhancements to the traditional BKZ algorithm, improving its efficiency and the quality of lattice reduction. Here, we summarize the key improvements:

- **Introduction of Pruning**:
  - BKZ 2.0 incorporates **sound pruning** and **extreme pruning**, techniques introduced by Gama, Nguyen, and Regev.
  - These pruning methods reduce the size of the enumeration tree by removing branches with negligible probability of success.

- **Preprocessing of Local Blocks**:
  - BKZ 2.0 ensures local bases are better reduced than standard LLL-reduction before enumeration.
  - This preprocessing step reduces the cost of enumeration by improving the quality of the local basis.
  - For a local projected lattice \( L[j,k] \), preprocessing increases the volumes of projected lattices \( L[k-d+1,k] \), reducing the size of the enumeration tree.

- **Optimizing the Enumeration Radius**:
  - BKZ 2.0 optimizes the initial radius \( R \) for enumeration to avoid unnecessary computation.
  - The optimized radius is calculated as:
    $$
    R = \min\left(\sqrt{\gamma} \cdot \text{GH}(L[j,k]), \|b_j^*\|\right),
    $$
    where \( \text{GH}(L[j,k]) \) is the Gaussian heuristic for the local block and \( \gamma \approx 1.1 \).

- **Simulation for High Block Sizes**:
  - BKZ 2.0 predicts output quality and running time for high block sizes (\( \beta \geq 50 \)) through a simulation algorithm.
  - This includes:
    1. Predicting the Gram-Schmidt sequence \( \|b_i^*\| \) during BKZ 2.0 reduction.
    2. Estimating the block sizes required to achieve a target Hermite factor.


# References
{{< references >}}