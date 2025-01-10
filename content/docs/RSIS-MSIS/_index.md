---
weight: 6
bookFlatSection: true
title: "Module SIS and Ring SIS"
math: true
---

# Compact versions of SIS

In practice, basic SIS leads to large key sizes and overall large parameters. Considering \( \bold{A} \in \mathbb{Z}_q^{h \times w} \) with \( m > n \) results in at least quadratic complexity. This is why more compact versions, such as Ring-SIS and Module-SIS, have been introduced. By using such underlying structures, we can achieve significantly smaller key sizes in schemes that use an SIS instance as the basis for their security. For a refresher on module and ring structures, the reader can consult {{< cite "lambek2009lectures" >}}.  

Suppose we choose \( h = 2^k \) and select the columns of \( \bold{A} \) as elements of the ring \( \mathbb{Z}_q / \langle x^h + 1 \rangle \). If \( h \) is a power of two, the polynomial \( x^h + 1 \) is irreducible over the rationals, allowing us to specify fewer elements to describe the same SIS instance. Using a module structure, we can achieve an even more compact representation.


## Ring SIS

<div style="background-color: rgba(173, 216, 230, 0.3); padding: 10px; border-radius: 5px; margin: 10px 0;">

**Definition:{{< cite "langlois2015worst" >}}**  
We define the **Ring Short Integer Solution (Ring-SIS)** problem as follows $\text{RSIS}(h, w, q, \beta, p)$. Let:

- \( R_q = \mathbb{Z}_q / \langle x^h + 1 \rangle \), where \( h \) is a power of two (ensuring \( x^h + 1 \) is irreducible over \( \mathbb{Q} \)),

Given $a_1, \ldots, a_w$ in $R_q$, chosen independently from the uniform distribution, find $s_1, \ldots, s_w \in R$ such that:

$$
\sum_{i=1}^{w}a_i s_i = 0 \pmod{q} \text{ and } 0 < \|\mathbf{s}\|_p \leq \beta
$$

where $\bold{s} = (s_1, \ldots, s_w)^T \in R^m$.

</div>

Each ring element $r\in R$ can be interpreted as an $h$-dimensional vector with coefficients $r_i$ such that $r = \sum_{i=0}^{n-1}r_ix^i$. Now if we compare RSIS to SIS, each matrix element $a_i$ in RSIS corresponds to the $h\times h$ nega-circulant matrix. In this setup, R-SIS is just a variant of SIS where $\bold{A}$ is restricted to being block negacirculant $\bold{A}= [Rot(a_1)|\ldots|Rot(a_n)]$ where Rot(b) is defined as


$$
\text{Rot}(b) =
\begin{bmatrix}
b_0 & -b_{h-1} & -b_{h-2} & \cdots & -b_1 \\
b_1 & b_0 & -b_{h-1} & \cdots & -b_2 \\
b_2 & b_1 & b_0 & \cdots & -b_3 \\
\vdots & \vdots & \vdots & \ddots & \vdots \\
b_{h-1} & b_{h-2} & b_{h-3} & \cdots & b_0
\end{bmatrix}
$$

### Security and estimator

To estimate the security of a ring SIS instance, we then effectively transform an RSIS instance $\text{RSIS}(h, w, q, \beta, p)$ to an SIS instance $\text{SIS}(h, h \cdot w, q, \beta, p)$. You can instanciate an instance of RSIS in the estimator via
`RSIS::new(h, w, q, length_bound, Norm)`.

---

## Module SIS

<div style="background-color: rgba(173, 216, 230, 0.3); padding: 10px; border-radius: 5px; margin: 10px 0;">

**Definition:{{< cite "langlois2015worst" >}}**  
We define the **Module Short Integer Solution (Module-SIS)** problem as follows $\text{MSIS}(h, w, d, q, \beta, p)$. Let:

- \( R = \mathbb{Z}_q / \langle x^h + 1 \rangle \), where \( h \) is a power of two,
- \( \mathcal{M} = R^d \), a rank-\( d \) module over \( R \),


Given $\bold{a_1}, \ldots, \bold{a_w} \in R_q^d$ chosen independently from the uniform distribution, find $s_1, \ldots, s_w \in R$ such that:

$$
\sum_{i=1}^{w}\bold{a_i} s_i = 0 \pmod{q} \text{ and } 0 < \|\mathbf{s}\|_p \leq \beta
$$

where $\bold{s} = (s_1, \ldots, s_w)^T \in R^m$.

</div>

Each element $\bold{a_i}$ can be seen as d coefficients in the ring and as such can be seen as an $h\cdot d \times h$ matrix. This leads a module SIS problem to be visualized as such in a standard SIS problem:

\[
\mathbf{A} =
\begin{bmatrix}
\text{Rot}(a_{1,1}) & \text{Rot}(a_{1,2}) & \cdots & \text{Rot}(a_{1,w}) \\
\text{Rot}(a_{2,1}) & \text{Rot}(a_{2,2}) & \cdots & \text{Rot}(a_{2,w}) \\
\vdots & \vdots & \ddots & \vdots \\
\text{Rot}(a_{d,1}) & \text{Rot}(a_{d,2}) & \cdots & \text{Rot}(a_{d,w})
\end{bmatrix},
\]

where each block \(\text{Rot}(a_{i,j})\) is a negacyclic matrix defined as:

\[
\text{Rot}(b) =
\begin{bmatrix}
b_0 & -b_{n-1} & -b_{n-2} & \cdots & -b_1 \\
b_1 & b_0 & -b_{n-1} & \cdots & -b_2 \\
b_2 & b_1 & b_0 & \cdots & -b_3 \\
\vdots & \vdots & \vdots & \ddots & \vdots \\
b_{n-1} & b_{n-2} & b_{n-3} & \cdots & b_0
\end{bmatrix}.
\]

### Security and estimator

To estimate the security of a module SIS instance, we then effectively transform an MSIS instance $\text{MSIS}(h, w, d, q, \beta, p)$ to an SIS instance $\text{SIS}(h \cdot d, h \cdot w, q, \beta, p)$. Also, note that by using $d=1$, we effectively come back to RSIS. Indeed, modules are a generalization of rings. You can instanciate an instance of MSIS in the estimator via
`MSIS::new(h, w, d, q, length_bound, Norm)`.


# References
{{< references >}}
