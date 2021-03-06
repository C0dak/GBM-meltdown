
## GBM Meltdown

### The Effect of the Linux Kernel Page-Table Isolation (KPTI) Patch (Meltdown Vulnerability) on GBMs


Meltdown is a serious vulnerability in Intel CPUs made public January 3, 2018. The KPTI patch that
mitigates it is believed to cause a performance hit of 5-30% in various software. 

GBM training is expected not to be significantly affected as it makes few system calls, which I'm
checking below in a setup similar to this [simple benchmark](https://github.com/szilard/GBM-perf/). 

The data is 1M records of the airline dataset, the GBMs have 100 trees, depth 10 and learning rate 0.1.
It is run on Ubuntu 16.04 on EC2 r4.8xlarge (Intel Xeon CPU E5-2686 v4, 32 cores, 250GB RAM), 
kernel versions 4.14.10 (before patch) and 4.14.12 (after patch). 
The software versions for h2o, xgboost and lightgbm are the same as in the benchmark linked above. 
The code run is [here](run/) (same as in the previous benchmark).


GBM implementation    |   Time before (sec)  | Time after (sec)
----------------------|----------------------|------------------
h2o                   |   21.9 - 24.5        |  24.0 - 25.3
xgboost               |   23.3 - 23.7        |  23.3 - 23.6
lightgbm              |   5.5 - 5.9          |  5.4-5.7

Detailed results [here](results-GBM.txt). So, **GBM training is not really affected by the KPTI patch.**


#### Other Results

glmnet (linear model, not a GBM) is also unaffected (time before 5.1 - 5.7 sec, after 5.1 - 5.7 sec).

No impact on Simon Urbanek's [R benchmark](https://r.research.att.com/benchmarks/) 
(containing various matrix operations, FFT, sorting etc.) either. Time before 46.6 sec, time after 46.1 sec
(or 6.4 sec vs 6.3 sec with optimized BLAS) (on m5.xlarge), 
see the breakdown e.g. random matrix, sorting, matrix cross-product, linear regression, 
eigenvalue, determinant, Cholesky etc. [here](results-R-Simon.txt).

