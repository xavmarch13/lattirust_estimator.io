---
weight: 3
bookFlatSection: true
title: "SIS"
math: true
---

# Short Integer Solution problem (SIS)

Let us look at two different ways to define q-ary lattices. From \\(\bold{A} \in \mathbb{Z}_q^{h\times w}\\)

$$\Lambda_q(\bold{A^T}) = \{\bold{y} \in \mathbb{Z}^w : \bold{y} = \bold{A}^T\bold{s} \text{ mod q }\text{ for some } \bold{s} \in \mathbb{Z}^h\}$$

$$\Lambda^T_q(\bold{A}) = \{\bold{y} \in \mathbb{Z}^w : \bold{A}\bold{y} = \bold{0} \text{ mod q }\}$$

The first lattice is generally called the primal problem and relates to the LWE problem since finding a short vector in \\(\Lambda_q(\bold{A^T})\\) corresponds to solving the LWE problem. The second lattice is generally called the dual problem and relates to the SIS problem since finding a short vector in \\(\\Lambda^T_q(\bold{A})\\) corresponds to solving the SIS problem.

## Formal definition

We define \\(SIS(h, q, w, \beta)\\) as follows: 

Given \\(\bold{A} \in \mathbb{Z}_q^{h\times w}\\) find the short vector \\(\bold{s} \in \mathbb{Z}^w\\)   where \\(0 < \lVert s \rVert_p \leq \beta\\).

We can note that the problem becomes trivial as soon as \\(\beta \geq q\\), no matter the norm used. 

## Estimating SIS hardness

Estimating the hardness of an SIS instance is done via estimating the number of operations required to run lattice reductions attacks on the related SVP problem. This concretely means that we need to know:

1. How small we expect the shortest vector to be in our SVP problem.
2. How small we expect vectors to be after lattice reduction.
3. How to cost lattice reduction.

We will mostly focus on BKZ 2.0 as a reduction algorithm and we will present how we estimate all parts of the attack.

### Expected size of small vectors

Given a random matrix \\(\bold{A} \in \mathbb{Z}_q^{h\times w}\\) 