---
weight: 2
bookFlatSection: true
title: "Lattice reduction"
math: true
---

# Lattice reduction

Lattice reduction algorithms such as LLL and BKZ make iterative local improvements to a basis. This means that the global cost can be seen as two-folds: how costly is it to make the local improvements, which corresponds to solving an exact SVP problem and how costly is the global behavior of the algorithm. This section focuses on the global behavior of lattice reduction algorithms, while the next section (Cost models) will focus about solving local improvements. Lattice reduction is the essential tool that allows to transform an inappropriate basis to solve the SIS problem into an appropriate one and thus its complexity is the bulk of the security estimate.

## Useful quantities in lattices 

### Volume

In each lattice, we can define the volume of the lattice as the volume of its fundamental parallellepiped (the area delimited by the basis vectors). This quantity is an invariant of the lattice and does not depend on the basis chosen. This means that by applying Gram-Schmidt orthogonalization to any basis will give us an orthogonal basis from which we can approximate the volume of the lattice as 

$$ Vol(\Lambda) =  \prod_{i=0}^{d-1} \lVert \bold{b_i}^*\rVert $$

where $\bold{b_i}*$ are the orthogonal Gram-Schmidt vectors. It is also important to remember that $Vol(\Lambda) = |Det(\bold{B})|$, where $\bold{B}$ is the basis matrix.

![alt text](volume.png)

This invariant is conceptually important because it tells us that not all basis vectors can be small at the same time.

### Gaussian heuristic

The gaussian heuristics predicts the number of lattice points inside any measurable body $\mathcal{B} \subset \mathbb{R}^d$ is approximately $\frac{Vol(\mathcal{B})}{Vol(\Lambda)}$. Applied to an euclidean d-ball, this would give that the length of the first vector is approximately 

$$\lambda_1(\Lambda) \approx (\frac{Vol(\mathcal{B})}{Vol(\Lambda)})^{\frac{1}{d}} \approx \sqrt{\frac{d}{2\pi e}} Vol(\Lambda)^{\frac{1}{d}}$$

### Root Hermite Factor

We will next want to introduce a value called the *root hermite factor*. It is a measure used in lattice reduction theory to evaluate the quality of a reduced lattice basis. It is commonly used to assess the effectiveness of lattice reduction algorithms. It quantifies how much longer the shortest vector in a reduced lattice basis is, compared to the length of an ideal shortest vector, scaled by the lattice dimension. Formally we define it as

$$\lVert \bold{b_1} \rVert \approx \delta^d Vol(\Lambda)^\frac{1}{d}$$ for an d-dimensional lattice.

The closer $\delta$ gets to 1, the better the reduction quality will be. This is of direct impact in our context, since we need to balance a trade-off between the quality of the output basis and the cost of running our lattice reduction algorithm. In fact, with the BKZ algorithm which has become the standard, a bigger block-size leads to a better quality of output basis but also a greater computational cost.

### Geometric Series Assumption

The geometric series assumption conceptually tells us that the Gram-Schmidt vectors log-length outputed by a lattice reducion algorithm will follow a line (see the graphs in the LLL subsection). We can formulate it as:

$$\lVert \bold{b_i}^*\rVert \approx \alpha^{i-1}\lVert \bold{b_1}\rVert$$

and in fact by combining it with the root hermite factor definition we can get a relation between the quality of the reduction and the slope of the GSA assumption as $\alpha \approx \delta^{-2}$ leading to 

$$ \lVert \bold{b_i}^*\rVert \approx \alpha^{i-1} \delta^d Vol(\Lambda)^\frac{1}{d} = \delta^{-1(i+1) + d} Vol(\Lambda)^\frac{1}{d}$$


## LLL algorithm

The Lenstra-Lenstra-Lovász (LLL) algorithm is an efficient polynomial-time algorithm in lattice theory that finds a "nearly orthogonal" basis for a given lattice. It aims to transform any arbitrary basis of a lattice into a reduced basis where the basis vectors are short and close to orthogonal following two conditions:

1. Size reduction:
$$1 \leq j < i \leq d\colon \left|\mu_{i,j}\right|\leq 0.5 \text{ for } \mu_{i,j} =\frac{\langle\mathbf{b}_i,\mathbf{b}^*_j\rangle}{\langle\mathbf{b}^*_j,\mathbf{b}^*_j\rangle}$$
2. Lovász condition:
For $k=2,...,d$ $$\omega \Vert \mathbf{b}^*_{k-1}\Vert^2  \leq \Vert \mathbf{b}^*_k\Vert^2+ \mu_{k,k-1}^2\Vert\mathbf{b}^*_{k-1}\Vert^2$$

We say the basis is LLL-reduced if there exists a parameter $\omega \in (0.25, 1)$. 

While there exists some theorems bounding the worst cases of lattice reduction algorithms, they tend to perform better in practice. Reasoning about the behaviors of such algorithms has therefore become a game of heuristics and approximations. Typically, the vectors that are outputed by the LLL algorithm are said to follow the geometric series assumption in their length. Again, this assumption tells us that the shape after lattice reduction is a line with a flatter slope as lattice reduction gets stronger. The goal of lattice reduction algorithm can therefore be interpreted seen by watching a graph of the log-length of vectors after reductions. The overall goal being to flatten the line, leading to a small basis.

![alt text](before_LLL.png)

![alt text](after_LLL.png)

### Cost of LLL

The theoretic bound on the quality of the LLL is $\delta^d = (\frac{4}{3})^\frac{d-1}{4}$ leading to approximately $\delta \approx 1.075$. In practice, we get much better results on average, empirically about $\delta \approx 1.021$.

In terms of runtime, we will consider a heuristic bound (which better approximates empirical results) of $O(d^3 \log^2(B))$.


## BKZ algorithm

The Block Korkine-Zolotarev (BKZ) algorithm is a lattice reduction algorithm that generalizes the LLL algorithm to achieve stronger reduction properties. The BKZ algorithm is defined as a blockwise reduction algorithm that iteratively applies a form of lattice basis reduction to overlapping blocks of vectors within the basis. Assuming we have an SVP oracle, the BKZ algorithm is defined as follows: 

    Data: LLL-reduced basis B (pre-processed) and block size beta
    repeat until no changes
        for k in 0 to d-1
            LLL on local projected block [k, ..., k+beta-1]
            v <-- SVP-Oracle(local projected block[k, ..., k+beta-1])
            insert v into B
        end

A BKZ-$\beta$ reduced basis satisfies, for $\epsilon > 0$:

$$\lVert \bold{b_0} \rVert \leq \sqrt{(1 + \epsilon) \gamma_{\beta}}^\frac{d-1}{\beta - 1} Vol(\Lambda(\bold{B}))$$

, where $$\gamma_\beta =  \sup \{ \lambda_1(\Lambda) | \Lambda \in \mathbb{R}^\beta, Vol(\Lambda) = 1 \}$$

is the hermite constant.

By combining the gaussian heuristic and the definition of a BKZ-$\beta$ reduced basis, we arrive again at the geometric assumption, which states that the length of reduced vectors follow a geometric series (which we can plot as a line as we did for LLL). This time however, it depends on the block-size chosen to run BKZ.

We can write

$$\log(\lVert \bold{b_i}^*\rVert) = \frac{d - 1 - 2i}{2}\log(\alpha_\beta) + \frac{1}{d}\log(Vol(\Lambda))$$

where $\alpha_\beta$ is the slope under the geometric assumption that can be calculated from the gaussian assumption as 

$$\alpha_\beta = \sqrt{\frac{d}{2\pi e}}^\frac{2}{\beta - 1}$$

This estimate is reasonably accurate only if $d\gg\beta$ and $\beta > 50$, which is why we will use fixed estimates for small dimensions. 

### Cost of BKZ

* Costing BKZ means having a good idea of the impact of the block-size on the quality of our reduced basis. For this, we could either make the approximation $\delta_\beta \approx \sqrt \alpha_\beta$ or use the following limit defined in 
$$\lim_{\beta\rightarrow\infty}\delta_\beta = (\frac{\beta}{2\pi e}(\pi\beta)^\frac{1}{\beta})^\frac{1}{2(\beta - 1)}$$

and write for SIS

$$\lVert \bold{b_1} \rVert \approx \delta_\beta^{d-1} Vol(\Lambda)^{\frac{1}{d}}$$


* Costing BKZ as a whole is complicated because we do not know how many tours we will have to run, which means we don't really know in advance the number of SVP-Oracle calls we will have to make. Furthermore, many improvements on plain BKZ have been made when some techniques are used as a subroutine for the oracle (for example extreme pruning in the context of enumeration), which makes security estimates done via lattice reduction very sensitive to many factors. Also, local preprocessing techniques in a variant of BKZ known as progressive BKZ. To make our tool comparable to the lattice estimator by ...[insert], we will follow the same simplifying assumption and consider a consistent 8 tours of BKZ. This makes sense following experimental results that showed that most progress is made in the 7-9 first tours. We will then use:
$$cost = \tau \cdot d \cdot T_{SVP}$$
where 
1. the number of tours we do $\tau$ is considered to be 8. 
2. The number of times the SVP oracle is called per tour, which is dimension of the lattice, is d
3. The cost of the SVP oracle is $T_{SVP}$


## Further improvements

- It has been showed in practice that the GSA assumption is a lie. In reality it behaves differently at its tail and this behaviour can be simulated.


- The dimension for free
 
- Also, a lot of tweaks, additions and speed up have been introduced into plain BKZ leading to BKZ 2.0, which provides a stronger reduction. See CN11 Simulator.

- Behaviour in q-ary lattices (ZGSA)

- Matzov Improvement