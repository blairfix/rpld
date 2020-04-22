# rpld

`rpld` is a discrete power-law random number generator for R, built in Rcpp.

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
   * `discrete_max` = threshold after which the algorithm switches to the continuous approximation method (`default = 10,000`)
   * `xmax` = an optional argument specifiying an upper cut off for the power law distribution (creates a truncated distribution)
   * `ordered` = an option to specify if the desired output should be orderd from smallest to largest (`default = FALSE`)

### Output

`rpld` returns a vector of integers distributed according to a discrete power law. If `ordered = TRUE`, the vector will be sorted from smallest to largest.


### Example:

```R
rpld(n = 100, xmin = 1, alpha = 2.3, discrete_max = 100000, ordered = F)
```
### Installation

To run `rpld`, install the following R packages:
 * [Rcpp](https://cran.r-project.org/web/packages/Rcpp/index.html) 
 * [BH](https://cran.r-project.org/web/packages/BH/index.html) (Boost header library )

Put the source code (`rpld.cpp`) in the directory of your R script. Then source it with the command `sourceCpp(rpld.cpp)`.

### Performance

Because it takes advantage of the speed of C++, `rpld` offers major performance improvements relative to the `rpldis` function in the [poweRlaw package](https://cran.r-project.org/web/packages/poweRlaw/index.html). Here are some benchmarks:

```R
library(poweRlaw)
sourceCpp("rpld.cpp")

Unit: milliseconds
   expr       min        lq      mean    median        uq       max neval
   rpld  135.2638  138.0486  236.7655  233.0178  297.4869  388.7508    10
 rpldis 5047.5143 5548.4289 5585.2291 5607.4660 5708.7593 5788.4312    10

```

Performance of `rpld` is improved slightly by sorting the results. This is because `rpld` sorts the numbers as part of the random number algorithm.


``` R
microbenchmark::microbenchmark(
  rpld = rpld(n = 10^6, xmin = 1, alpha = 1.5, discrete_max = 10^6, ordered = T),
  rpldis = rpldis(n = 10^6, xmin = 1, alpha = 1.5, discrete_max = 10^6),
  times = 10
)


Unit: milliseconds
   expr       min        lq      mean    median       uq       max neval
   rpld  118.5789  119.3888  157.9247  122.4313  212.843  230.2478    10
 rpldis 5233.8021 5542.9705 5807.9898 5560.8254 6208.423 6463.4995    10
```


