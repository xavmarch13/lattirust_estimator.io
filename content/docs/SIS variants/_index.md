---
weight: 5
bookFlatSection: true
title: "SIS variants"
math: true
---

# SIS variants

We will now present all SIS problem variants implemented in our tools. Most effectively hand out additional hints to the adversary, while hoping that the problem remains hard. In all cases, we will try to define the SIS variants precisely, give the reduction to the basis SIS problem we use (SIS, RSIS, or MSIS) and also give a rough idea of what kind of scheme the problem variant allowed to construct. While we will try to be as close as possible from the original papers, this (and the entire webpage) could probably still contains mistakes. If you are involved in any of these schemes, feel free to reach out with any corrections. In all cases, we will also show an example of how the variants can be called via the estimator.

## BASIS_rand

**Definition {{< cite "wee2023succinct" >}}**

### Calling an instance
`code from estimator`

## BASIS_struct 

**Definition {{< cite "wee2023succinct" >}}**

## ISISf

**Definition {{< cite "bootle2023framework" >}}**

## k-SIS 

**Definition {{< cite "boneh2011linearly" >}}**


## k-M-SIS

**Definition {{< cite "albrecht2022lattice" >}}**

For any integer $k\geq 0$, an instance of the k-M-SIS problem is represented by a matrix $\bold{A}\in R_q^{h\times w}$ and a set of k vectors $\bold{v_0}, \ldots, \bold{v_{k-1}}$ such that:

$$\bold{A}\cdot\bold{v_i} = 0 \pmod q \text{ and } \lVert v_i \rVert \leq \beta$$

and the goal is to find a non-vector $\bold{v^*} \in R^w$ such that:

$$\lVert \bold{v^*} \rVert \leq \beta^*$$


## k-R-SIS 

**Definition {{< cite "albrecht2022lattice" >}}**


## k-R-ISIS 

**Definition {{< cite "albrecht2022lattice" >}}**

Let $g(\bold{X})\in R(\bold{X})$ be a Laurent monomial (which is simply a monomial with possibly negative powers). We can write $g(\bold{X}) = \bold{X^e} = \prod_{i\in\mathbb{Z_l}}\bold{X}^{e_i}_i$ for some exponent vector $\bold{e}\in \mathbb{Z}^l$. Now let $\mathcal{G} \subset R(\bold{X})$ be a set of Laurent monomials and $|\mathcal{G}|=k$ and $g^*\in R(\bold{X})$ be a target Laurent monomial.

We call a family $\mathcal{G}$ k-M-ISIS admissible if
    1. All $g\in \mathcal{G}$ have a constant degree
    2. All $g\in \mathcal{G}$ are distinct
    3. $0\notin \mathcal{G}$


## vanishing-SIS 

**Definition {{< cite "cini2023lattice" >}}**


## PRISIS

**Definition {{< cite "fenzi2024lattice" >}}**

## one-more-ISIS 

**Definition {{< cite "agrawal2022practical" >}}**

An instance of the one-more-ISIS problem is represented by a matrix $\bold{A}\in\mathbb{Z}_q^{h\times w}$. An adversary receives this matrix and is able to perform two times of queries:

1. *Syndrome queries*: request a vector $\bold{t}\leftarrow_$ \mathbb{Z}_q^h$, the challenger keeps track of vectors sent by adding them to a set $\mathcal{T}$.
2. *Preimage queries*:


# References
{{< references >}}


