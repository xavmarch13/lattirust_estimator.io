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

We can note that the problem becomes trivial as soon as \\(\beta \geq q\\), no matter the norm used. In the following analysis, we will separate the use of the euclidean norm and the infinity norm. The two sections will describe the concrete ways of defining the security parameters, using the theory we built in the previous sections (lattice reduction and cost models).


## SIS and ISIS

## SIS and LWE

## L2 norm strategy

*TODO check the notation between A and A^T*
### Finding the optimal lattice shape for reduction

Given a q-ary lattice \\(\bold{A} \in \mathbb{Z}_q^{h\times w}\\) and assuming q is prime (which will be the case in most applications), we can then say with high probability that the rows of $\bold{A}$ are independent over $\mathbb{Z}_q$ (we also assume w is bigger than n and not too close). As a result of this, the lattice $\Lambda^T_q(\bold{A})$ has $q^h$ points in $\mathbb{Z}^w_q$. This leads to the volume or determinant of the matrix to be $Vol(\Lambda^T_q(\bold{A})) = q^{h}$. Using the gaussian heuristic from the lattice reduction section, we can express

$$\lambda_1(\Lambda^T_q(\bold{A})) \approx q^\frac{h}{w}\sqrt{\frac{w}{2\pi e}}$$

as an estimate of the lenght of the smallest vector.

The question we want to answer is what is the shortest vector we can hope to find in a reasonable time. It has been experimentally observed that the length of the vector obtained by the best known lattice reduction algorithms on a random w-dimensional q-ary lattic is close to $\min(q, q^\frac{n}{m}\delta^m)$. We can also observe that increasing the $w$ parameter does not make the problem any hard. In fact, we can fix this parameter by completely letting go a certain number of columns. It has shown that the minimum can be obtained at 
$$w = \sqrt{\frac{h\log q}{\delta}}$$
Indeed, for smaller m the lattice becomes too sparse and does not contain enough vectors to have small ones, and bigger m actually prevents lattice reduction to perform optimally.

## L-inf norm strategy

### The tradeoffs

### The search function

### Matzov improvements on the dual attacks

### Kyber improvements




