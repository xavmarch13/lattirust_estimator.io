---
weight: 2
bookFlatSection: true
title: "Preliminaries"
math: true
---

# Preliminaries

This section is based on the following resources {{< cite "gama2008predicting" >}}{{< cite "micciancio2002complexity" >}}{{< cite "micciancio2009lattice" >}}{{< cite "nguyen2009hermite" >}}.

## Lattices

A *lattice* is defined as a discrete subgroup in $n$-dimensional $\mathbb{R}^n$ space with a periodic structure. It can be represented by a set of linearly independent vectors, commonly referred to as the basis of the lattice. If $\bold{b_1}, ..., \bold{b_d}$ denote basis vectors, we can describe a lattice as:

$$
\Lambda(\bold{b_1}, ..., \bold{b_d}) = \left\{\sum_{i=1}^{d} x_i \bold{b_i} : x_i \in \mathbb{Z} \right\}
$$

This represents the set of all linear combinations of the basis vectors. Here, $d$ is the dimension of the lattice in $\mathbb{R}^n$.

{{< figure src="lattices-1.png" alt="Lattice grid 2D" caption="A lattice in 2D" >}}

A lattice has many possible bases, but some are more useful than others. The goal of lattice reduction, as presented in later sections, is to find a qualitatively good basis composed of short and nearly orthogonal vectors.

In this work, we will specifically focus on $q$-ary lattices described by a basis $\bold{B}$, where the coefficients are taken modulo $q$. The membership of a vector to the lattice is determined by $\bold{x} \pmod{q}$.

### Volume of a Lattice

In each lattice, we can define the volume of the lattice as the volume of its fundamental parallelepiped (the region enclosed by the basis vectors). This quantity is an *invariant* of the lattice and does not depend on the chosen basis. This means that applying Gram-Schmidt orthogonalization to any basis will yield an orthogonal basis from which we can approximate the volume of the lattice as:

$$
\text{Vol}(\Lambda) =  \prod_{i=1}^{d} \lVert \bold{b_i}^* \rVert 
$$

where $\bold{b_i}^*$ are the orthogonal Gram-Schmidt vectors. It is also important to note that $\text{Vol}(\Lambda) = |\text{Det}(\bold{B})|$, where $\bold{B}$ is the basis matrix.

{{< figure src="volume.png" alt="Volume of a lattice 2D" caption="A lattice volume in 2D" >}}

This invariant is conceptually significant because it demonstrates that not all basis vectors can be small simultaneously.

### Dual of a Lattice

The dual of a lattice $\Lambda$ in $\mathbb{R}^d$ is denoted $\Lambda^*$ and is defined as the lattice composed of vectors $\bold{y}$ such that:

$$
\langle \bold{x}, \bold{y} \rangle \in \mathbb{Z}, \forall \bold{x} \in \Lambda
$$

For any lattice described by basis $\bold{B}$, we have:

$$
\Lambda(\bold{B})^* = \Lambda((\bold{B}^{-1})^T) \quad \text{and} \quad \text{Vol}(\Lambda^*) = \frac{1}{\text{Vol}(\Lambda)}
$$

### Minima of a Lattice and Root Hermite Factor

We denote by $\lambda_i(\bold{B})$ the $i$-th minimum of a lattice described by a basis $\bold{B}$. Note that for the remainder of this work, we may interchangeably use $\lambda_i(\bold{B})$, $\lambda_i(\Lambda(\bold{B}))$, or $\lambda_i(\Lambda)$ for minima and other related measures. Intuitively, $\lambda_i(\bold{B})$ represents the radius of the smallest zero-centered ball containing at least $i$ linearly independent lattice vectors.

The root Hermite constant $\gamma_n$ is a constant that determines how short a lattice element can be. It is known exactly only for $n \in \{1, 2, 3, 4, 5, 6, 7, 8, 24\}$, but upper bounds for other values are available and can be found in {{< cite "cohn2003new" >}}. In general, determining $\lambda_i(\bold{B})$ is difficult, so we often refer to upper bounds.

**Minkowski's Second Theorem:** For any lattice in $\mathbb{R}^n$ and any $1 \leq d \leq n$, we have:

$$
\left( \prod_{i=1}^{d} \lambda_i(\Lambda) \right)^{\frac{1}{d}} \leq \sqrt{\gamma_n} \cdot \text{Vol}(\Lambda)^{\frac{1}{n}}
$$

Formally, the Hermite constant for a full-rank lattice in $\mathbb{R}^n$ is defined as:

$$
\gamma_n = \sup_{\Lambda \subset \mathbb{R}^n} \frac{\lambda_1^2(\Lambda)}{\det(\Lambda)^{2/n}}
$$

The Hermite constant $\gamma_n$ is closely related to the root Hermite factor $\delta_n$, which we will describe in more detail in the lattice reduction chapter. The root Hermite factor is used to evaluate the quality of a basis, and we have $\delta_n = \sqrt{\gamma_n}$.


### Random Lattices and Gaussian Heuristic

The notion of a random lattice is mathematically complex (see Haar measures). As a simplifying assumption, imagine such a lattice being sampled from an existing random distribution that adheres, with overwhelming probability, to the Gaussian heuristic described below.

$$
\frac{\lambda_i(\Lambda)}{\text{Vol}(\Lambda)^{1/n}} \approx \frac{\Gamma\left(1 + \frac{n}{2}\right)^{1/n}}{\sqrt{\pi}} \approx \sqrt{\frac{n}{2\pi e}}
$$

The Gaussian heuristic predicts that the number of lattice points inside any measurable body $\mathcal{B} \subset \mathbb{R}^d$ is approximately $\frac{\text{Vol}(\mathcal{B})}{\text{Vol}(\Lambda)}$. Applied to a Euclidean $d$-ball, this implies that the length of the first vector is approximately:

$$
\lambda_1(\Lambda) \approx \left(\frac{\text{Vol}(\mathcal{B})}{\text{Vol}(\Lambda)}\right)^{\frac{1}{d}} \approx \sqrt{\frac{d}{2\pi e}} \cdot \text{Vol}(\Lambda)^{\frac{1}{d}}
$$


## Hard Problems in Lattices

The security of lattice-based constructions relies on several variants of related problems. The foundation of this security is based on the fact that finding a short non-zero vector in a lattice is computationally hard. Below are the definitions of the key problems:

1. **Shortest Vector Problem (SVP)**

    **Definition:**  
    Given a lattice $\Lambda$ with basis $\bold{B}$, the **Shortest Vector Problem (SVP)** is the problem of finding a non-zero vector $\bold{v} \in \Lambda$ such that:

    $$
    \|\bold{v}\| = \lambda_1(\Lambda)
    $$

    where $\lambda_1(\Lambda)$ is the length of the shortest non-zero vector in the lattice. This is a well-known NP-hard problem in the worst case, and its approximation remains challenging for cryptographic settings.

2. **Hermite Shortest Vector Problem (H-SVP)**

    **Definition:**  
    The **Hermite Shortest Vector Problem (H-SVP)** is a scaled version of the SVP. It asks for a non-zero vector $\bold{v} \in \Lambda$ such that:

    $$
    \|\bold{v}\| \leq \delta_n \cdot \text{Vol}(\Lambda)^{1/n}
    $$

    where $\delta_n$ is the root Hermite factor. Unlike exact SVP, H-SVP allows finding a vector that is "short enough" relative to the lattice volume. H-SVP is easier to approximate than SVP and is central to lattice reduction algorithms like LLL and BKZ.

3. **Approximate Shortest Vector Problem (Approx-SVP)**

    **Definition:**  
    The **Approximate Shortest Vector Problem (Approx-SVP)** generalizes SVP by relaxing the requirement to find the exact shortest vector. Given a lattice $\Lambda$ and an approximation factor $\gamma \geq 1$, the goal is to find a non-zero vector $\bold{v} \in \Lambda$ such that:

    $$
    \|\bold{v}\| \leq \gamma \cdot \lambda_1(\Lambda)
    $$

    For sufficiently large $\gamma$, Approx-SVP becomes computationally easier, but it remains hard for small $\gamma$, especially in high-dimensional lattices.

4. **Unique Shortest Vector Problem (Unique-SVP)**

    **Definition:**  
    The **Unique Shortest Vector Problem (Unique-SVP)** is a variant of SVP where the shortest vector is guaranteed to be significantly shorter than all other lattice vectors. Specifically, there exists a gap $\beta > 1$ such that:
    
    $$
    \lambda_2(\Lambda) \geq \beta \cdot \lambda_1(\Lambda)
    $$
    
    where $\lambda_2(\Lambda)$ is the length of the second shortest lattice vector. The task is to find the unique shortest vector $\bold{v} \in \Lambda$. This problem is somewhat easier than general SVP due to the presence of a unique solution, but it still remains computationally challenging.

5. **Closest Vector Problem (CVP)**

    **Definition:**  
    Given a lattice $\Lambda$ with basis $\bold{B}$ and a target vector $\bold{t} \in \mathbb{R}^n$, the **Closest Vector Problem (CVP)** is the problem of finding a vector $\bold{v} \in \Lambda$ such that:
    
    $$
    \|\bold{t} - \bold{v}\| = \min_{\bold{w} \in \Lambda} \|\bold{t} - \bold{w}\|
    $$

    CVP is generally harder than SVP and is also NP-hard in the worst case. Approximation versions of CVP, such as $\gamma$-CVP (finding $\bold{v}$ within a $\gamma$-factor of the closest vector), are frequently studied in lattice cryptography.

6. **Shortest Independent Vector Problem (SIVP)**

    **Definition:**  
    The **Shortest Independent Vector Problem (SIVP)** involves finding $n$ linearly independent vectors $\{\bold{v}_1, \bold{v}_2, \dots, \bold{v}_n\}$ in a lattice $\Lambda$ such that the maximum norm of the vectors is minimized:

    $$
    \max_{i=1}^n \|\bold{v}_i\| = \lambda_n(\Lambda)
    $$

    where $\lambda_n(\Lambda)$ represents the length of the $n$-th successive minimum of the lattice. SIVP is closely related to the geometry of the lattice and remains computationally hard. It is used in cryptographic constructions and reduction proofs, often connecting the hardness of lattice problems to cryptographic assumptions.

## Summary Table of Lattice Problems

| **Problem**       | **Goal**                                    | **Output**                                                | **Difficulty**                    |
|-------------------|---------------------------------------------|----------------------------------------------------------|------------------------------------|
| **(I)SVP**        | Find the shortest vector                    | $\bold{v} \in \Lambda, \|\bold{v}\| = \lambda_1$           | NP-hard in the worst case          |
| **H-SVP**         | Find a short enough vector                  | $\bold{v} \in \Lambda, \|\bold{v}\| \leq \delta_n \cdot \text{Vol}(\Lambda)^{1/n}$ | Easier than SVP, used in reduction |
| **Approx-(I)SVP** | Approximate the shortest vector             | $\bold{v} \in \Lambda, \|\bold{v}\| \leq \gamma \lambda_1$ | Hard for small $\gamma$            |
| **Unique-(I)SVP** | Find the unique shortest vector             | $\bold{v} \in \Lambda, \lambda_2 \geq \beta \lambda_1$ | Easier than SVP (gap $\beta$)      |
| **CVP**           | Find the closest lattice vector to target   | $\bold{v} \in \Lambda, \|\bold{t} - \bold{v}\|$          | NP-hard, harder than SVP           |

Importantly, for later sections, note that any algorithm solving Approx-SVP with factor $\alpha$ also solves Hermite-SVP for factor $\alpha\sqrt{\gamma_n}$ in polynomial time. Additionally, any algorithm solving H-SVP with factor $\alpha$ can be used a linear number of times to solve Approx-SVP with factor $\alpha^2$ in polynomial time {{< cite "lovasz1986algorithmic" >}}. There also exist reductions from worst-case Approx-SVP to average-case H-SVP for certain classes of lattices.

## Security

The foundational belief in the security of new lattice-based primitives stems from a key result: Ajtai’s theorem, which connected the hardness of certain average-case problems to the difficulty of worst-case problems in lattices {{< cite "ajtai1996generating" >}}. Specifically, Ajtai demonstrated that for the Short Integer Solution (SIS) problem—which we will define later—the average-case instances are at least as hard as the worst-case instances of the Shortest Vector Problem (SVP) on lattices. This implies that if one could efficiently solve random instances of SIS, they could also solve the worst-case SVP, a problem believed to be intractable even for quantum computers. Ajtai’s theorem provides a strong security guarantee for lattice-based cryptographic schemes by grounding their security in the hardness of well-studied lattice problems like SVP.

The security assumptions based on the hardness of SVP can be summarized by two conjectures from {{< cite "micciancio2009lattice" >}}:

1. There is no polynomial-time algorithm that approximates lattice problems to within polynomial factors.
2. There is no polynomial-time quantum algorithm that approximates lattice problems within polynomial factors.

These conjectures underpin the belief that lattice-based cryptosystems remain secure even in the presence of quantum adversaries, making them promising candidates for post-quantum security.


## Norms

We provide a brief overview of norms that will be used later and their relationships. We also introduce norms for elements in modules and rings.

#### Definition of \( \ell_p \)-Norms

For a vector $\boldsymbol{f} \in \mathbb{R}^n$, the $\ell_p$-norms are defined as follows:

$$
\|\boldsymbol{f}\|_1 = \sum_{i=1}^n |f_i|, \quad
\|\boldsymbol{f}\|_2 = \sqrt{\sum_{i=1}^n f_i^2}, \quad
\|\boldsymbol{f}\|_p = \left(\sum_{i=1}^n |f_i|^p\right)^{1/p}, \quad
\|\boldsymbol{f}\|_\infty = \max_{i} |f_i|.
$$

These norms generalize to module elements $\boldsymbol{f} \in R_q^m$ (where $R_q$ is a quotient ring, e.g., $\mathbb{Z}_q[x]/\langle x^n + 1 \rangle$), by viewing them as $m \cdot n$-dimensional vectors {{< cite "baum2018more" >}}. Note that modules generalize rings, so a vector $\boldsymbol{f}$ in the ring $R_q^1$ is represented by a set of coefficients $f_i$ such that:

$$
\boldsymbol{f} = \sum_i f_i x^i.
$$

#### Relationships Between Norms

For $p, q \geq 1$ with $p \leq q$, the following relationships hold for $\boldsymbol{f} \in \mathbb{R}^n$:

$$
\|\boldsymbol{f}\|_\infty \leq \|\boldsymbol{f}\|_q \leq \|\boldsymbol{f}\|_p \leq \|\boldsymbol{f}\|_1.
$$

This inequality indicates that the $\ell_\infty$-norm is always the smallest, while the $\ell_1$-norm is the largest.

#### Applications in Lattice Problem Hardness Estimation

Norms are crucial for bounding errors and ensuring security in lattice-based cryptography. For example:
- In the **Learning with Errors (LWE)** problem, the $\ell_2$-norm is used to bound the error vector.
- In the **Short Integer Solution (SIS)** problem, the $\ell_\infty$-norm is often employed to restrict the coefficients of the solution.

By carefully analyzing and relating these norms, cryptographic schemes ensure robustness against attacks while maintaining efficiency.

# References

{{< references >}}