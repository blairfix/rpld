# rpld

`rpld` is a discrete power-law random number generator for R, built in RcppArmadillo.

The `rpld` function generates a descrete power law distribution.
Code is based on Colin Gillespie's `rpldis` function  in the R
[poweRlaw package](https://cran.r-project.org/web/packages/poweRlaw/index.html).
Gillespie's function is in  turn based on an algorithm from the following paper:

  * Clauset, Aaron, Cosma Rohilla Shalizi, and Mark EJ Newman. "Power-law distributions in empirical data." SIAM review 51.4 (2009): 661-703.

### Inputs

The `rpld` function takes the following inputs:
   * `n` = number of desired random numbers
   * `xmin` = minimum value of power the law distribution (integer > 0)
   * `alpha` = the power-law exponent of the distribution
   * `discrete_max` = threshold after which the algorithm switches to the continuous approximation method (default = 10,000)
   * `xmax` = an optional argument specifiying an upper cut off for the power law distribution (creates a truncated distribution)
   * `ordered` = an option to specify if the desired output should be orderd from smallest to largest (default = FALSE)

### Example:

```R
rpld(n = 100, xmin = 1, alpha = 2.3, discrete_max = 100000, ordered = F)
```
### Installation
To run `rpld`, install the following R packages:
 * [Rcpp](https://cran.r-project.org/web/packages/Rcpp/index.html) 
 * [RcppArmadillo](https://cran.r-project.org/web/packages/RcppArmadillo/index.html) 
 * [BH](https://cran.r-project.org/web/packages/BH/index.html) (Boost header library )

Put the source code (`rpld.cpp`) in the directory of your R script. Then source it with the command `sourceCpp(rpld.cpp)`.

### Performance

Because it takes advantage of the speed of C++, `rpld` offers major performance improvements relative to the `rpldis` function in the [poweRlaw package](https://cran.r-project.org/web/packages/poweRlaw/index.html). Here are some benchmarks:

```R
library(poweRlaw)
sourceCpp("rpld.cpp")

microbenchmark::microbenchmark(
  rpld = rpld(n = 10^6, xmin = 1, alpha = 2.3, discrete_max = 10^6),
  rpldis = rpldis(n = 10^6, xmin = 1, alpha = 2.3, discrete_max = 10^6),
  times = 10
)

Unit: milliseconds
   expr       min        lq      mean   median        uq       max neval
   rpld  140.2853  141.2461  153.5078  147.298  155.6841  204.2642    10
 rpldis 4814.0556 5010.9888 5139.2756 5094.204 5302.2113 5624.5178    10
```

Performance of `rpld` is improved slightly by sorting the results. This is because `rpld` sorts the numbers as part of the random number algorithm.

``` R
microbenchmark::microbenchmark(
  rpld = rpld(n = 10^6, xmin = 1, alpha = 2.3, discrete_max = 10^6, ordered = T),
  rpldis = rpldis(n = 10^6, xmin = 1, alpha = 2.3, discrete_max = 10^6),
  times = 10
)

Unit: milliseconds
   expr       min        lq      mean    median        uq       max neval
   rpld  119.5831  122.3193  125.5273  125.1045  127.7361  135.7249    10
 rpldis 4820.8666 5034.6607 5166.6181 5188.5239 5249.4574 5438.3271    10
```


