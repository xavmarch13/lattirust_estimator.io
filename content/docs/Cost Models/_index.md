---
weight: 3
bookFlatSection: true
title: "Cost Models"
math: true
---

# Cost Models

Given the simplified cost for BKZ behaviour that we consider $cost = \tau \cdot d \cdot T_{SVP}$, we still need to define the cost of the SVP solver. Sieving and enumeration are the two common exact strategies to find the shortest non-zero vector in a lattice.

## Sieving

Sieving algorithms for SVP work by iteratively refining a large set of lattice vectors to obtain progressively shorter vectors until the shortest one is found. In its basic form, the sieving process starts by generating a large set of random lattice vectors, often called a "cloud." Pairs of vectors are then combined (usually by subtracting them) to produce shorter vectors, which are then added back to the cloud if they meet certain criteria. This process continues until the vectors in the cloud converge towards the shortest lattice vector. Modern sieving methods, like the GaussSieve or ListSieve, have been optimized to handle higher-dimensional lattices by limiting pairwise vector interactions, which reduces computational complexity. Sieving requires storing a large number of lattice vectors and, therefore, can be memoryintensive, especially as the lattice dimension grows. Here we present a simplified graphs of selecting two vectors out of a cloud, taking the difference between two vectors and looking what is the smaller between the three vectors.


{{< iframe src="/graphs/sieving.html" width="800" height="500" title="Interactive Graph" caption="Small example of the sieving logic." >}}


In terms of cost, sieving algorithm can solve the SVP in a lattice of dimension d in $2^{O(\beta)}$ time but at the cost of a much higher memory usage $2^{O(\beta)}$ than enumeration. Our estimator will disregard memory usage to focus on time cost.

The following sieving estimates are available in the estimator:

| Name      | Reference | Cost | Regime |
| ----------- | ----------- | ----------- | ----------- |
| BDGL-sieve      | {{< cite "becker2016new" >}}       | $2^{0.292\beta + 16.4}$ big $\beta$ $2^{0.387\beta + 16.4}$ small $\beta$|  classical       |
| Q-sieve      | {{< cite "alkim2016post" >}}   {{< cite "laarhoven2015finding" >}}     | $2^{0.265\beta}$      |  quantum       |
| ADPS-sieve   | {{< cite "alkim2016post" >}}        | $2^{0.292\beta}$| classical       | t2       |
| BGJ-sieve   | {{< cite "albrecht2018estimate" >}}        | $2^{0.311\beta}$       | classical       |
| ChaLoy-sieve   | {{< cite "chailloux2021lattice" >}}        | $2^{0.257\beta}$       | quantum       |



{{< figure src="sieving.png" alt="cost plot" caption="Cost of sieving SVP solvers" >}}

As a very high overview, we give a list of amelioration brought by each kind of sieving solver considered :

- BDGL-sieve introduces a novel approach to improving lattice sieving efficiency through **locality-sensitive filtering (LSF)**, which significantly reduces the time complexity of solving the shortest vector problem (SVP) on high-dimensional lattices. By utilizing spherical caps as filters and leveraging structured random product codes for efficient decoding, the method achieves an asymptotic time complexity of \(2^{0.292n + o(n)}\), outperforming previous approaches like spherical locality-sensitive hashing (LSH).

- ADPS-sieve presents sintroduces **quantum speedups through Grover’s algorithm**, reducing the classical sieving complexity from \(2^{0.415b}\) to \(2^{0.292b}\). Additionally, it leverages structured lattice representations to optimize memory usage and sieve iterations. This method focuses on balancing theoretical improvements with practical implementations, providing a framework for faster and more scalable sieving for lattice-based cryptographic challenges.

- BGJ-sieve significantly reduces the time complexity to \(2^{0.292d + o(d)}\) through algorithmic optimizations. Key contributions include the introduction of hypersphere partitioning to reduce vector comparison costs, the use of nearby vector replacement to iteratively refine the list of vectors towards shorter ones, and a probabilistic framework that efficiently handles high-dimensional spaces.

- ChaLoy-sieve introduces a quantum algorithm based on quantum random walks that improves the asymptotic complexity of solving the Shortest Vector Problem (SVP). The key innovation is replacing Grover's algorithm with a quantum random walk, enabling faster resolution of reducible vector pairs. This enhancement reduces the heuristic running time from \(2^{0.2653d + o(d)}\) to \(2^{0.2570d + o(d)}\), while optimizing resource usage. Notably, it decreases quantum memory requirements to \(2^{0.0495d}\) and quantum RAM to \(2^{0.0767d}\).

### The Dimension for free

In recent advancements, Ducas introduces a significant improvement in lattice sieving algorithms through the "dimension for free" technique {{< cite "ducas2018shortest" >}}. This approach leverages the observation that short lattice vectors generated during sieving in lower dimensions can be reused to accelerate sieving in higher dimensions. By projecting and reusing these vectors across multiple dimensions, the algorithm effectively reduces the computational overhead without compromising accuracy. This method achieves a practical speedup of up to 10x in mid-range dimensions (e.g., 70–80). Our estimators will automatically provide this improvement by adapting the size of the needed lattice reduction computation. In practice, we can reduce the SVP problem of dimension $n$ to solving an SVP problem of dimension $n-d$ as long as $d$ is $\Theta(\frac{n}{\log(n)})$. The paper mentions a concrete prediction of $d \approx \frac{n\ln(4/3)}{\ln(n/2\pi e)}$.

## Enumeration 

Enumeration algorithms systematically search through lattice points in a controlled way, typically by traversing lattice vectors within a fixed radius from the origin. They rely on a recursive process to explore potential candidate vectors within a "search region," using techniques to prune paths that are unlikely to lead to the shortest vector. Enumeration is typically carried out with the help of a basis that has been reduced (made close to orthogonal), as this greatly improves efficiency by minimizing the number of candidate paths. Unlike sieving, enumeration methods are deterministic and guarantee finding the shortest vector by systematically exploring all feasible paths. The efficiency of enumeration depends strongly on the quality of the lattice basis. Preprocessing steps like BKZ (Block Korkine-Zolotarev) reduction can make enumeration significantly faster by transforming the basis to be more suitable for search. Enumeration is often practical for lower-dimensional lattices or when a high degree of accuracy is needed, but it tends to be less efficient than sieving in high dimensions due to its exponential complexity. Hereafter we present a simplified version of how enumeration could progress. Given a basis b1 and b2, we project one of the two and use it to make a grid spaced by the length of the projection. Using this 1-dim problem we can lift back up to the 2-d problem by selecting all points in the Radius that are on the grid.

{{< iframe src="/graphs/enumeration.html" width="800" height="500" title="Interactive Graph" caption="Small example of the enumeration logic." >}}

In terms of cost, enumeration can solve SVP in a lattice of dimension d in $2^{O(\beta \log \beta)}$ time and space poly($\beta$).

The following enumeration estimates are available:

| Name      | Reference | Cost | Regime |
| ----------- | ----------- | ----------- | ----------- |
| Lotus      | {{< cite "albrecht2018estimate" >}} {{< cite "le2017lotus" >}}      | $2^{0.125\beta\log\beta -0.755\beta + 22.74}$ |  classical       |
| CheNgue-enum (BKZ 2.0)      | {{< cite "chen2011bkz" >}}       | $2^{0.27\beta\log\beta -1.019\beta + 2.254}$ |  classical       |
| ABF-enum   | {{< cite "albrecht2020faster" >}}        | $2^{0.184\beta\log\beta - 0.995\beta + 22.25}$ small $\beta$ $2^{0.125\beta\log\beta-0.547\beta+16.4}$ big $\beta$    | classical       | t2       |
| ABF-Q-enum   | {{< cite "albrecht2020faster" >}}           | $2^{0.0625\beta\log\beta}$       | quantum       |
| ABLR-enum   | {{< cite "albrecht2021lattice" >}}         | $2^{0.184\beta\log\beta - 1.077\beta + 35.12}$ small $\beta$ $2^{0.125\beta\log\beta-0.655\beta+31.84}$ big $\beta$    | classical       |

Here are some plots to better visualize enumeration costs.

{{< figure src="enumeration.png" alt="cost plot" caption="Cost of enumeration SVP solvers" >}}

As a very high overview, we give a list of amelioration brought by each kind of enumeration solver considered :

- ABF-Enum brings introduces the concept of **extended preprocessing**. This approach decouples the preprocessing and enumeration contexts, enabling the use of higher-dimensional sublattices for preprocessing while optimizing the enumeration cost. By leveraging the enhanced geometric properties of SDBKZ-reduced bases, ABF-Enum achieves faster enumeration with reduced computational complexity, pushing the boundaries of lattice reduction efficiency. This innovation forms the foundation for achieving improved trade-offs between time complexity and quality of solutions in enumeration-based solvers.

- BKZ 2.0 (ChNgue) optimizes the Blockwise Korkine-Zolotarev (BKZ) algorithm. Key enhancements include the incorporation of **sound pruning**, a sophisticated technique to reduce enumeration cost while maintaining quality, and **preprocessing of local blocks** to improve the efficiency of enumeration subroutines. Additionally, **optimized enumeration radii** enable faster convergence by leveraging improved heuristics. These innovations collectively improve both the speed and output quality of lattice reduction, making BKZ 2.0 a new benchmark for practical lattice algorithms.

- ABLR-Enum innovates by combining **relaxed pruning** with **extended preprocessing** strategies. These enhancements allow enumeration algorithms to achieve exponential speed-ups while maintaining high-quality reductions. By relaxing the search radius and optimizing enumeration parameters, the approach efficiently balances time and output quality. The extended preprocessing step leverages higher-dimensional sublattices to improve enumeration performance, marking a significant step forward in practical and scalable lattice reduction.

- The LOTUS cryptosystem enhances lattice enumeration techniques through optimized pruning strategies, which reduce the computational effort required for solving lattice problems like bounded distance decoding (BDD). By refining cost estimation methods for lattice reduction and enumeration.


## Method comparison


{{< figure src="comp.png" alt="cost plot" caption="Cost of all solvers" >}}


We can see that sieving performs better overall when the dimension of the SVP problem get big, which is the expected behavior. For this reason, we encourage making security estimates with sieving as an underlying SVP solver.

# References

{{< references >}}