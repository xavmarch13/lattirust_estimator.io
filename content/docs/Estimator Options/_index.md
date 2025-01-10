---
weight: 8
bookFlatSection: true
title: "Estimator options and API"
math: true
---

# Our tool

In this section, we will provide an overview of how to use the lattirust estimator. We will present the options for costing the problems, the type of errors you may encounter and also the search functions we provide over the parameter spaces and their characteristic.

## Instanciating and evaluating the hardness of SIS instances

You can set the type and the parameters of an SIS-based instance via:

- `SIS::new(h, w, q, length_bound, Norm)` for a classical SIS instance where $\\bold{A}\in \mathbb{Z}^{h\times w}_q$ and you look for a vector such that $
  \boldsymbol{A} \boldsymbol{y} = \boldsymbol{0} \mod q \quad \text{and} \quad \lVert\boldsymbol{y}\\rVert \leq$ `length_bound`. The norm is selected via a Rust enum as  `Norm::L2` or `Norm::Linf`. For example,

    
    `let falcon512_unf: SIS = SIS::new(512, 1024, 12289u64.into(), 5833.9072, Norm::L2);`

- `MSIS.new(h, w, d, q, length_bound, Norm)` for a module SIS instance where the additional parameter $d$ is the rank of the module used. Again the norm is selected via a Rust enum as  `Norm::L2` or `Norm::Linf`. For example,


    `let msis_instance: MSIS = MSIS::new(24, 512, 4, 122898899u64.into(), 5833.9072, Norm::L2);`


- For specific variants that have extra parameters, please refer to the page presenting all variants to see how to instanciate them.


Now to evaluate the hardness of an instance via a lattice reduction attack, you must choose the specific model you want to use for the SVP oracle that is part of BKZ and the simulator you want to use for the assumed shape of vectors after lattice reduction. If you evaluate the hardness of an SIS instance under an euclidean norm, then the actual shape assumed after lattice reduction does not matter as we directly optimize over a function between the hermite factor and the block-size, however, it does matter under an infinity norm. You get the hardness by calling the folllowing.

```rust 
sis_instance.security_level(estimate_type, simulator_type)->Result<f64, LatticeEstimatorError>
```
- estimate_type is an `Option<Estimates>` where Estimates is a Rust enum, where None defaults to `Matzov(true)`.
- simulator_type is an `Option<Simulator>` where Simulator is a Rust enum, where None defaults to `GSA`.
- The return value of `security_level` is either a lattice estimator error, or a float $\lambda$ that represents the security level $2^\lambda$. 

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

For Estimates that have a bool integrated to them, it is because they offer both a classical and a quantum estimate. So you if set `Matzov(true)` you will get the classical estimates whereas is you set `Matzov(false)` you will get the quantum estimate.


### The Simulator enum

You can choose a simulator by passing one of the three choices to `security_level()`. By default (so by passing `None`), it will use the GSA.

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
        param_value: String,
        reason: String,
    },
    /// Computation-related error
    ComputationError {
        message: String,
    },
    /// Configuration error with a specific message
    ConfigurationError {
        message: String,
    },
}
```

Errors can occur either during the estimation process due to issues encountered while evaluating a scheme or at the start due to invalid parameters. These represent critical errors, and the estimator cannot proceed when one of them is encountered.

You may also encounter another type of "error" that we refer to as a warning. Warnings indicate potential issues that may arise with the set of parameters chosen for a specific instance. Since these issues are primarily based on asymptotic behavior, warnings are meant to prompt you to carefully consider your parameter choices, as the proven reduction may not cover these cases or the parameters may seem sensitive with respect to asymptotic bounds.  

Warnings will print to the standard output, but the estimator will still be able to run. We chose not to halt execution every time parameter choices appear to conflict with asymptotic bounds to avoid unnecessary restrictions on usage. Here is an example of a sitation with warnings, if you run 

```rust
let falcon512_unf: KSIS = KSIS::new(400, 1024, 1228999u64.into(), 3500.9072,  2, Norm::L2);
let lambda_prior= falcon512_unf.security_level(None, None).unwrap();
```

you will get a log warning saying:

`[WARN  lattice_estimator::ksis] The reduction was only proven for  w > 2hlog_2(q)`

## Searching for the best parameters on single SIS instances

For each SIS or SIS variant instance, you are guaranteed to be offered the following functions:


### security_level
 ```rust 
pub fn security_level(&self, estimate_type: Option<Estimates>, simulator_type: Option<Simulator>) -> Result<f64, LatticeEstimatorError> 
```

This is the backbone of our estimator, it allows to cost any single SIS instance in the way we described in previous sections. It is almost instantaneous for L2, may take a little time for Linf because of the grid search. It may return an error in the given parameters do not accept a solution, are trivially solvable or if something goes wrong in the computation or during the grid search.

#### Example

 ```rust 
let sis: SIS = SIS::new(1024, 2304, 8380417u64.into(), 350209., Norm::Linf);
let lambda:f64 = sis.security_level(None, None).unwrap();
print!("{sis} -> lambda: {lambda}");
```

returns `SIS[h=1024, w=2304, q=8380417, length_bound=350209, norm=Linf] -> lambda: 158.2610392271431`


### find_optimal_h_annealing
 ```rust 
pub fn find_optimal_h_annealing(&self, lambda: usize, estimate_type: Option<Estimates>, simulator_type: Option<Simulator>) -> Result<usize, LatticeEstimatorError> 
```

This function takes as input an additional parameter \( \lambda \) that represents the desired security level. The function is more complex than its name suggests because it aims to find a valid solution, even if the input parameters come from an invalid set of SIS parameters. The function follows these steps:

1. **Validate Input Parameters:**  
   If the given parameters are not valid, the function attempts to find a valid starting point by tweaking the \( h \) parameter.  
   
2. **Define Direction of Adjustment:**  
   Once a valid starting point is found, the function determines the direction in which the \( h \) parameter must be adjusted to approach the desired security level.  

3. **Initial Broad Search:**  
   The function makes large increments in \( h \) to quickly approach the optimal value, stopping when the limit defined by \( \lambda \) is crossed.  

4. **Refinement with Simulated Annealing:**  
   As a final step, the function refines the \( h \) value using simulated annealing, gradually narrowing in on the optimal value until it converges or becomes sufficiently close to the desired level.

**Reasoning for this Approach:**  
The security evaluation using \( \ell_\infty \)-norms is computationally expensive, necessitating an adaptive search strategy. Additionally, since SIS instances can vary widely in size, the function must be versatile enough to adapt to different parameter sets. While this approach performs well for the \( \ell_2 \)-norm, the \( \ell_\infty \)-bound still presents a challenge due to the large search space.

 **Return Value:**  
The function returns the optimized \( h \) value or an error if:  
- No valid starting point is found.  
- An error occurs during the local search.  
- Simulated annealing fails multiple times.  

**Note:** Some parameter combinations may lead to situations where no valid costs exist or where the cost is always infinite due to the size constraints.


#### Example

 ```rust 
let sis: SIS = SIS::new(700, 2304, 8380417u64.into(), 350209., Norm::Linf);
let h_opt = sis.find_optimal_h_annealing(128, Some(Estimates::Matzov(true)), None).unwrap();
```
allows us to get from the first SIS instance which evaluates to $\lambda=103$ to $\lambda=128$ by increasing $h$.

```
SIS[h=700, w=2304, q=8380417, length_bound=350209, norm=Linf] -> lambda: 103.14095563601026
SIS[h=862, w=2304, q=8380417, length_bound=350209, norm=Linf] -> lambda: 128.53191273316034
```

### find_optimal_length_bound_annealing
 ```rust 
pub fn find_optimal_length_bound_annealing(&self, lambda: usize, estimate_type: Option<Estimates>, simulator_type: Option<Simulator>) -> Result<f64, LatticeEstimatorError> 
```

This works very similarly to the find_optimal_h_annealing function. One majors difference is that the length bounds have a less direct impact on security than $h$, which leads to the optimized function search to stall in very large instances.


#### Example 

 ```rust 
let sis: SIS = SIS::new(512, 1024, 12289u64.into(), 5833.9072, Norm::L2);
let sec: f64 = sis.security_level(Some(Estimates::Matzov(true)), None).unwrap();
let best_bound: f64 = sis.find_optimal_length_bound_annealing(124, Some(Estimates::Matzov(true)), None).unwrap();
let new_sis = sis.with_length_bound(best_bound);
```

leads to an instance being updated from and to:

```
SIS[h=512, w=1024, q=12289, length_bound=5833.9072, norm=L2] -> lambda: 143.96281000571494
SIS[h=512, w=1024, q=12289, length_bound=10209.3376, norm=L2] -> lambda: 124.52002835795201
```

### quick_search_h

```rust 
pub fn quick_search_h(&self, estimate_type: Option<Estimates>, simulator_type: Option<Simulator>) 
```

This is a practical function that gives you an overview of the security by power of 2 in $h$. Here is an example of its output for 

```rust 
let falcon512_unf: SIS = SIS::new(512, 1024, 12289u64.into(), 5833.9072, Norm::L2);
falcon512_unf.quick_search_h(Some(Estimates::Matzov(true)), None);
```

<div align="center">

| **h**   | **Security Level**  |
|:-------:|:------------------:|
| 1       | Error/Impossible    |
| 2       | Error/Impossible    |
| 4       | Error/Impossible    |
| 8       | Error/Impossible    |
| 16      | Error/Impossible    |
| 32      | 33.13               |
| 64      | 37.71               |
| 128     | 39.59               |
| 256     | 67.67               |
| 512     | 143.96              |
| 1024    | Error/Impossible    |

</div>



## Searching for the best parameters on multiple SIS instances

On multiple SIS instance, we firstly of course offer the security security_level function, which evaluates the sum of the SIS instance in the multiSIS instance but also some other exploration functions. We present them here.

### quick_search_h
 ```rust 
pub fn quick_search_h(&self, estimate_type: Option<Estimates>, simulator_type: Option<Simulator>) -> ()
```

This is a function that outputs all combination security possibilities for power of 2 of the $h$ parameters for all SIS instances contained in the multiSIS instance. It skips every combination that gets an error in one of its instances. Here is an example of outputs

<div align="center">

| **Combination** | **h Values**                | **Combined Security Level** |
|:---------------:|:----------------------------:|:---------------------------:|
| Combination 1   | h_1: 32, h_2: 16, h_3: 32     | 99.38                        |
| Combination 2   | h_1: 32, h_2: 16, h_3: 64     | 103.97                       |
| Combination 3   | h_1: 32, h_2: 16, h_3: 128    | 106.13                       |
| ...             | ...                           | ...                          |
| Combination 50  | h_1: 64, h_2: 512, h_3: 64    | 184.58                       |
| ...             | ...                           | ...                          |
| Combination 100 | h_1: 256, h_2: 128, h_3: 256  | 193.61                       |
| ...             | ...                           | ...                          |
| Combination 140 | h_1: 512, h_2: 1024, h_3: 256 | 476.58                       |

</div>


### quick_search_h_constrained
 ```rust 
pub fn quick_search_h_constrained(&self, estimate_type: Option<Estimates>, simulator_type: Option<Simulator>) 
```
This function behaves similarly as the previous one except it will only accept solutions that are non-increasing in sizes throughout the different SIS instances. Here is an example output.

<div align="center">

| **Combination** | **h Values**                | **Combined Security Level** |
|:---------------:|:----------------------------:|:---------------------------:|
| Combination 1   | h_1: 32, h_2: 32, h_3: 32     | 99.38                        |
| Combination 2   | h_1: 64, h_2: 32, h_3: 32     | 103.97                       |
| Combination 3   | h_1: 64, h_2: 64, h_3: 32     | 108.55                       |
| ...             | ...                           | ...                          |
| Combination 10  | h_1: 128, h_2: 128, h_3: 128  | 119.05                       |
| ...             | ...                           | ...                          |
| Combination 20  | h_1: 256, h_2: 256, h_3: 256  | 207.39                       |
| ...             | ...                           | ...                          |
| Combination 30  | h_1: 512, h_2: 256, h_3: 256  | 283.68                       |
| ...             | ...                           | ...                          |
| Combination 34  | h_1: 512, h_2: 512, h_3: 256  | 339.47                       |

</div>

### optimize_h
 ```rust 
pub fn optimize_h(&self, lambda: usize, estimate_type: Option<Estimates>, simulator_type: Option<Simulator>)-> Result<Vec<usize>, LatticeEstimatorError>
```
This function will give the combination of different h parameters throughout the SIS instances that gets the closest to the wanted security parameter lambda. It does so with no constraints.

### optimize_h_constrained
 ```rust 
pub fn optimize_h_constrained(&self, lambda: usize, estimate_type: Option<Estimates>, simulator_type: Option<Simulator>) -> Result<Vec<usize>, LatticeEstimatorError> 
```

This function will give the combination of different h parameters throughout the SIS instances that gets the closest to the wanted security parameter lambda. It does in a constrained way where the different $h_i$ parameters are non-increasing.


## Conclusion

In this work, we have presented a comprehensive overview of the concepts and implementations introduced so far. Possible future directions for this work include:  

- Further optimization for the \( \ell_\infty \)-norm.  
- Adding support for other \( p \)-norms.  
- Staying updated with new SIS variants.  
- Establishing a clear consensus on how to accurately estimate the cost of lattice reduction algorithms in practice.  

Thank you for your attention and for following our work!

