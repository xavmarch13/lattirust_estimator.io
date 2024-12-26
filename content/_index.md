---
title: A Library for Lattice Parameter Selection
type: docs
---
# Introduction


As quantum computing advances, many of the cryptographic techniques that underpin our current digital security may soon become vulnerable {{< cite "kitaev2002classical" >}}. Quantum computers, leveraging algorithms like Shor’s {{< cite "bhatia2020efficient" >}}, have the potential to break widely-used public key cryptosystems such as RSA and ECC, posing a serious threat to data confidentiality and integrity. In response, researchers are developing **post-quantum cryptography (PQC)**—new cryptographic algorithms designed to withstand quantum attacks {{< cite "bernstein2017post" >}}.

One of the most promising approaches in PQC is **lattice-based cryptography** {{< cite "micciancio2009lattice" >}}, which relies on the difficulty of certain mathematical problems involving high-dimensional lattices. Two of the most significant problems in this domain are the **Short Integer Solution (SIS) problem** {{< cite "ajtai1996generating" >}} and the **Learning With Errors (LWE) problem** {{< cite "regev2009lattices" >}}. Both problems are related and widely believed to remain hard even in the post-quantum era, making them ideal candidates as the foundation for new cryptographic primitives.

However, the effectiveness and practicality of lattice-based cryptography hinge on selecting the appropriate **security parameters**—such as the lattice dimension, modulus, and the error distribution (for LWE) {{< cite "player2018parameter" >}}. These parameters directly influence the system’s resistance to quantum attacks, as well as its efficiency in real-world implementations. Striking the right balance is critical: overly conservative parameters may offer high security but at the cost of performance and practicality, while weaker parameters may leave the system vulnerable to attack. Moreover, the algorithms used to solve problems like SIS and LWE, known as **lattice reduction algorithms** {{< cite "nguyen2011lattice" >}}, are still not fully understood, often performing better in practice than theoretical worst case analysis would suggest.



In this blog, we will take a closer look at the SIS problem and its variants, and demonstrate the importance of **security parameter estimation** in building secure and efficient post-quantum cryptosystems. As part of our effort to port lattice-based systems from research to practice, we designed and implemented a Rust library for lattice parameter selection. Concretely, this library aims to: 

 - provide an intuitive and hard-to-misuse interface for proof frontends, 
 - internally use precise estimates for the hardness for lattice problems (e.g., by reducing to the shortest vector problem),
 - compute estimates for variants of standard problems of interest (e.g., new lattice assumptions, or standard assumptions with additional leakage). 
    
This library is to be integrated into lattirust, our library for lattice-based proofs.  This work was conducted as part of a research project at the Computational Security Laboratory (COMPSEC) at EPFL.

The blog will be structured into several sections. First, we will explore the theoretical foundations of the SIS problem and its variants. Then, we will discuss the cost models that relate different methods of solving these problems to actual security estimates. Finally, we will provide a detailed walkthrough of the Rust tool, including how to use it for security parameter estimation in practical applications.

Note that our tool will be based on the state-of-the-art estimator developed by Martin Albrecht et al. {{< cite "cryptoeprint:2015/046" >}}. Most of our design decisions will align with their work, as it is, to the best of our knowledge, the most precise and up-to-date estimator available for lattice-based problem security estimations. However, we will differ by centering our estimator on the SIS problem and its variants to specifically target zkSNARKs (zero knowledge succinct non-interactive arguments). Additionally, we will port the estimator logic from python and sage into Rust, offering a modern and efficient implementation.

# References

{{< references >}}