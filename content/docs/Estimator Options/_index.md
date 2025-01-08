---
weight: 6
bookFlatSection: true
title: "Estimator options and API"
math: true
---

# Our tool

In this section, we will provide an overview of how to use the lattirust estimator. We will present the options for costing the problems, the type of errors you may encounter and also the search functions we provide over the parameter spaces and their characteristic.

## Instanciating and evaluating the hardness of SIS instances

You can set the type and the parameters of an SIS-based instance via:

- `SIS.new(h, w, q, length_bound, Norm)` for a classical SIS instance where $\\bold{A}\in \mathbb{Z}^{h\times w}_q$ and you look for a vector such that $
  \boldsymbol{A} \boldsymbol{y} = \boldsymbol{0} \mod q \quad \text{and} \quad \lVert\boldsymbol{y}\\rVert \leq$ `length_bound`. The norm is selected via a Rust enum as  `Norm::L2` or `Norm::Linf`. For example,

    
    `let falcon512_unf: SIS = SIS::new(512, 1024, 12289u64.into(), 5833.9072, Norm::L2);`

- `MSIS.new(h, w, d, q, length_bound, Norm)` for a module SIS instance where the additional parameter $d$ is the rank of the module used. Again the norm is selected via a Rust enum as  `Norm::L2` or `Norm::Linf`. For example,


    `let msis_instance: MSIS = MSIS::new(24, 512, 4, 122898899u64.into(), 5833.9072, Norm::L2);`


- For specific variants that have extra parameters, please refer to the page presenting all variants to see how to instanciate them.


Now to evaluate the hardness of an instance via a lattice reduction attack, you must choose the specific model you want to use for the SVP oracle that is part of BKZ and the simulator you want to use for the assumed shape of vectors after lattice reduction. If you evaluate the hardness of an SIS instance under an euclidean norm, then the actual shape assumed after lattice reduction does not matter as we directly optimize over a function between the hermite factor and the block-size, however, it does matter under an infinity norm. You get the hardness by calling 

```rust 
sis_instance.security_level(estimate_type, simulator_type)->Result<f64, LatticeEstimatorError>
```
- estimate_type is an `Option<Estimates>` where Estimates is a Rust enum, where None defaults to `Matzov(true)`
- simulator_type is an `Option<Simulator>` where Simulator is a Rust enum, where None defaults to `GSA`
- The return value of the `security_level` is either a lattice estimator error, or a float $\lambda$ that represents the security level $2^\lambda$ 

### The Estimates enum

```rust
pub enum Estimates {
    BdglSieve,       // Implements the Becker-Ducas-Gama-Laarhoven sieve
    QSieve,          // Quantum sieve model for quantum-enhanced sieving
    BjgSieve,        // Bai-Jeng-Groves sieve for lattice sieving optimizations
    AdpsSieve(bool), // ADPS sieve (Albrecht-Ducas-Poppelmann-Steinwandt), with optimizations for dual attack settings
    ChaLoySieve,     // Chatterjee-Loyd sieve for structured lattices
    CheNgueEnum,     // Chen-Nguyen enumeration model combining enumeration with sieving
    AbfEnum(bool),   // ABF enumeration technique with an optimization flag
    AblrEnum,        // Albrecht-Bai-Laarhoven-Reith (ABLR) enumeration
    LotusEnum,       // LOTUS enumeration for module-SIS problems
    Kyber(bool),     // Kyber-specific estimator with an optimization boolean
    Matzov(bool)     // Matzov estimator with optional advanced heuristics
}
```

For Estimates that have a bool integrated to them, it is because they offer both a classical and a quantum estimate. So you if set `Matzov(true)` you will get the classical estimates whereas is you set `Matzov(false)` you will get the quantum estimate,


### The Simulator enum

```rust
pub enum Simulator {
    GSA,  // Geometric Series Assumption (exponential decay in norms)
    ZGSA, // Z-shaped profile for q-ary lattices with plateaus
    LGSA  // L-shaped profile for q-vectors that are rerandomized
}
```


### Lattice estimator errors

While using our tool, you may encounter three types of error all represented by an enum.

```rust
pub enum LatticeEstimatorError {
    /// Invalid parameter error with details
    InvalidParameter {
        param_name: String,
        reason: String,
    },
    /// Computation-related error
    ComputationError {
        message: String,
    },
    /// Configuration error with a specific message
    ConfigurationError {
        message: String,
    }
}
```

They can be thrown either while actually costing a scheme or at the beginning because of parameters that can not be used. They represent critical errors and the estimator can not run while encountering one of these. You may also encounter another type or "errors" that we call warnings. Warning are just possible problem that could present with the set of parameters you choose for a specific instance. Since they are mostly asymptotics, warnings have the goal to make you think about what parameters you are choosing because the reduction that is proven does not cover them or they seem sensitive given the asymptotic bound. These warnings will print on the standard output but the estimator will still be able to run. We did not want to stop the user everytime the parameter choices seemed to conflict with asymptotics bound to prevent too much restriction on usage.

## Searching for the best parameters on single SIS instances

For an already defined SIS instance, we provide a way to find the tightest set of parameters to achieve a certain level security. We give a way to optimize separately for $h$ which is the primary security indicator, but also for the length bound and for $d$ the dimension of the module and $k$ the number of hints, when it applies. We also give a function to optimize both principal parameters ($h$ and the length bound) simultaneously.

## Searching for the best parameters on multiple SIS instances




