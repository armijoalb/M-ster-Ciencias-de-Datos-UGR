Gaussian Process Regression and Classification Toolbox
======================================================
version 4.0. For GNU Octave 3.2.x and Matlab 7.x

Copyright (C) 2015-2016 - Carl Edward Rasmussen

Copyright (C) 2015-2016 - Hannes Nickisch

0) HOW TO READ
--------------

If you want to get started without further delay, then read section 1) below
and jump right to the examples in doc/index.html.


1) ABOUT THESE PROGRAMS
-----------------------

This collection of matlab programs implements and demonstrates some of the
algorithms described in

a) the book by Rasmussen and Williams: "Gaussian Processes for Machine Learning",
   the MIT Press 2006, in
   
b) the article by Nickisch and Rasmussen: "Approximations for Binary Gaussian
   Process Classification", JMLR 2008, in
   
c) the article by Candela and Rasmussen: "A Unifying View of Sparse Approximate
   Gaussian Process Regression", JMLR 2005, in
   
d) the paper by Murray, Adams and Mackay: "Elliptical slice sampling",
   AISTATS 2010, in
   
e) the report by Neal: "Annealed Importance Sampling", UToronto 1998, in

f) the paper by Naish-Guzman and Holden: "The Generalized FITC Approximation",
   NIPS, 2007, in
   
g) the paper by Duvenaud, Nickisch and Rasmussen: "Additive Gaussian Processes",
   NIPS, 2011, in
   
h) the paper by Wilson and Adams: "Gaussian Process Kernels for Pattern
   Discovery and Extrapolation", ICML, 2013, in
   
i) the paper by Snelson, Rasmussen and Ghahramani: "Warped Gaussian Processes",
   NIPS, 2003, and in
   
j) the paper by Wilson and Nickisch: "Kernel interpolation for scalable
   structured Gaussian processes (KISS-GP)", ICLM, 2015.

There are 7 code subdirectories: cov, doc, inf, lik, mean, prior and util.

cov/      contains covariance functions cov*.m
          => see covFunctions.m

doc/      contains an index.html file providing documentation. This information
          is also available from http://www.GaussianProcess.org/gpml/code.
          Usage of mean, cov, classification and regression is demonstrated
          in usage*.m. Further details can be found in the developer
          documentation manual.pdf.

inf/      contains the inference algorithms inf*.m
          => see infMethods.m

lik/      contains the likelihood functions lik*.m
          => see likFunctions.m

mean/     contains the mean functions mean*.m
          => see meanFunctions.m

prior/    contains the hyperparameter prior distributions prior*.m
          => see priorDistributions.m

util/     contains optimisation routines, backward compatibility programs and
          small auxiliary scripts

Before running the demos or any other part of the code, you should execute
startup.m to add all necessary directories to your path. In Octave, you can also
use the .octaverc file.

2) ABOUT MEX FILES
------------------
Some of the programs make use of the MEX facility in matlab for more efficient
implementation. However, if you don't know about how to compile MEX files, you
do not need to worry about this -- the code runs anyway. If you do compile the
MEX files, this is automatically detected, and the program will run more
efficiently. If you do not have a compiler installed on your system, you might
want to consult [3].

Two components of the toolbox can be accelerated by MEX.

First, there is an auxiliary functions solve_chol.m having a MEX equivalent.
This can be compiled by either executing util/make.m from the Matlab/Octave
command line. In addition to that, we provide a Makefile
for Matlab in util/ that you can run by simply typing make in your shell after
having supplied your Matlab path and your operating system.
We recommend the make.m script because it works both under Matlab and Octave.

Second, in order to use the L-BFGS minimiser for hyperparameter optimisation as
an equivalent to minimize.m, you have to compile Peter Carbonetto's
"Matlab interface for L-BFGS-B" [1] which was extended to also work for Octave
by José Vallet. The challenge here is the Fortran 77 code.
We provide a Makefile suitable for Linux 32/64 bit and Mac whenever you have

1. g77 or
2. gfortran

properly set up. The latter is prefered on current Ubuntu systems.
Under Ubuntu, you can achieve this by installing the packages fort77 and
gfortran, respectively. Note that the code is GNU licensed.

Compilation is done by editing util/lbfgsb/Makefile. In any case, you need to
provide $MATLAB_HOME which can be found by the commands 'locate matlab' or
'find / -name "matlab"'. You can choose between two compilation modes:

a) without mex utility by Matlab                                     [default]
  provide $MEX_SUFFIX and $MATLAB_LIB, then type 'make nomex'

b) using the mex utility by Matlab
  provide $MEX, then type 'make mex'

c) compilation for Octave
  provide Octave variables and type 'make oct'

In Ubuntu 10.04 LTS, the libg2c library needed for both 1)+a) and 1)+b) is not
included per default. If 'ls /usr/lib/libg2c.*' does not list anything
this is the case on your machine. You then whant to install the packages
gcc-3.4-base and libg2c0 e.g. from
http://packages.ubuntu.com/hardy/gcc-3.4-base and
http://packages.ubuntu.com/hardy/libg2c0.
After installation, you have to create a symbolic link by 'cd /usr/lib' and
'ln -s libg2c.so.0 libg2c.so'.

There were reports of problems running the mex files on recent 64bit Ubuntu
since the libgfortran.3.0.0 that comes with matlab
($(MATLAB_HOME)/sys/os/glnxa64/libgfortran.so.3.0.0) is not the same
as the libgfortran.3.0.0 that comes with the ubuntu package
libgfortran3 and that is used when linking. So running the mex file
results in a version mismatch error. The issue can be solved by changing
the name of to the matlab file and linking to the ubuntu file instead:
  in $(MATLAB_HOME)/sys/os/glnxa64 run
    'sudo mv libgfortran.3.0.0 libgfortran.3.0.0_matlab'
    'sudo ln -s /usr/lib/x86_64-linux-gnu/libgfortran.so.3.0.0 libgfortran.3.0.0'
Thanks to Giampiero Salvi for posting the workaround.

[1]: https://github.com/pcarbo/lbfgsb-matlab
[2]: http://www.mathworks.com/support/compilers/R2010a

3) CURRENT VERSION
------------------

The current version of the programs is 4.0. Previous versions of the code are
available at http://www.gaussianprocess.org/gpml/code/oldcode.html.

4) DIFFERENCES TO PREVIOUS VERSIONS
-----------------------------------

### NEW in version 4.0, 2016-09-29
A major code restructuring effort did take place in the current release unifying
certain inference functions and allowing more flexibility in covariance function
composition. We also redesigned the whole derivative computation pipeline to
strongly improve the overall runtime. We finally include grid-based covariance
approximations natively.

More generic sparse approximation using Power EP
 - unified treatment of FITC approximation, variational approaches VFE and hybrids
 - inducing input optimisation for all (compositions of) covariance functions dropping the previous limitation to a few standard examples
 - infFITC is now covered by the more generic infGaussLik function

Approximate covariance object unifying sparse approximations, grid-based approximations and exact covariance computations
 - implementation in cov/apx, cov/apxGrid, cov/apxSparse
 - generic infGaussLik unifies infExact,   infFITC         and infGrid
 - generic infLaplace  unifies infLaplace, infFITC_Laplace and infGrid_Laplace
 - generic infVB
 - enables efficient grid-based algebra for off-grid inputs, see reference j) above

Hiearchical structure of covariance functions
 - clear hierachical compositional implementation
 - no more code duplication as present in covSEiso and covSEard pairs
 - two mother covariance functions
    covDot  for dot-product-based covariances and
    covMaha for Mahalanobis-distance-based covariances
 - a variety of modifiers: eye, iso, ard, proj, fact, vlen
 - more flexibility as more variants are available and possible
 - all covariance functions offer derivatives w.r.t. inputs

Faster derivative computations for mean and cov functions
 - switched from partial derivatives to directional derivatives
 - simpler and more concise interface of mean and cov functions
 - much faster marginal likelihood derivative computations
 - simpler and more compact code

New mean functions
 - new mean/meanWSPC (Weighted Sum of Projected Cosines or Random Kitchen Sink features) following a suggestion by William Herlands
 - new mean/meanWarp for constructing a new mean from an existing one by means of a warping function adapted from William Herlands

New optimizer
 - added a new minimize_minfunc, contributed by Truong X. Nghiem

New prio
 - added prior{Equal|Same}Multi forcing a group of hyperparameters to the same value

New GLM link function
 - added the twice logistic link function util/glm_invlink_logistic2

Smaller fixes
 - two-fold speedup of util/elsympol used by covADD by Truong X. Nghiem
 - bugfix in util/logphi as reported by John Darby

### NEW in version 3.6, 2015-07-07
- bugfix in likGaussWarp as reported by Obaid Malik
- added a new inference function infGrid_Laplace allowing to use non-Gaussian likelihoods for large grids
- fixed a bug due to Octave evaluating norm([]) to a tiny nonzero value, modified all lik/lik*.m functions reported by Philipp Richter
- small bugfixes in covGrid and infGrid
- bugfix in predictive variance of likNegBinom due to Seth Flaxman
- bugfix in infFITC_Laplace as suggested by Wu Lin
- bugfix in covPP{iso,ard}


### NEW in version 3.5, 2014-12-08
- mechanism for specifying hyperparameter priors (together with Roman Garnett and José Vallet)
- new inference method inf/infGrid allowing efficient inference for data defined on a Cartesian grid (together with Andrew Wilson)
- new mean/cov functions for preference learning: meanPref/covPref
- new mean/cov functions for non-vectorial data: meanDiscrete/covDiscrete
- new piecewise constant nearest neighbor mean function: meanNN
- new mean functions being predictions from GPs: meanGP and meanGPexact
- new covariance function for standard additive noise: covEye
- new covariance function for factor analysis: covSEfact
- new covariance function with varying length scale : covSEvlen
- make covScale more general to scaling with a function instead of a scalar
- bugfix in covGabor* and covSM (due to Andrew Gordon Wilson)
- bugfix in lik/likBeta.m (suggested by Dali Wei)
- bugfix in solve_chol.c (due to Todd Small)
- bugfix in FITC inference mode (due to Joris Mooij) where the wrong mode for post.L was chosen when using infFITC and post.L being a diagonal matrix
- bugfix in infVB marginal likelihood for likLogistic with nonzero mean function (reported by James Lloyd)
- removed the combination likErf/infVB as it yields a bad posterior approximation and lacks theoretical justification
- Matlab and Octave compilation for L-BFGS-B v2.4 and the more recent L-BFGS-B v3.0 (contributed by José Vallet)
- smaller bugfixes in gp.m (due to Joris Mooij and Ernst Kloppenburg)
- bugfix in lik/likBeta.m (due to Dali Wei)
- updated use of logphi in lik/likErf
- bugfix in util/solve_chol.c where a typing issue occured on OS X (due to Todd Small)
- bugfix due to Bjørn Sand Jensen noticing that cov_deriv_sq_dist.m was missing in the distribution
- bugfix in infFITC_EP for ttau->inf (suggested by Ryan Turner)


### NEW in version 3.4, 2013-11-11
- new likelihood function for warped GPs: likGaussWarp
- new exponential likelihood function: likExp
- new Weibull likelihood function: likWeibull
- new Gumbel likelihood function for extremal value regression: likGumbel
- new polynomial mean function: meanPoly
- included derivatives w.r.t. inducing points xu in infFITC, infFITC_Laplace, infFITC_EP
- allow for zero noise variance for infExact


### NEW in version 3.3, 2013-10-19
- new variational inference algorithm based on direct KL minimisation: infKL
- improved inf/infVB double loop scheme so that only very few likelihood properties are required; infVB is now internally a sequence of infLaplace runs
- improved inf/infLaplace to be more generic so that optimisers other than scaled Newton can be used
- improved inf/infEP so that the internal variables (mu,Sigma) now represent the current posterior approximation
- added three new generalised linear model likelihoods: gamma, beta, inverse Gaussian
- new covariance functions: spectral mixture covSM, covGaboriso and covGaborard
- new meta covariance function to turn a stationary covariance into a periodic covariance function covPERard, covPERiso
- new periodic covariance function with zero DC component and correct scaling covPeriodicNoDC

New likelihood functions in lik/
 - likGamma.m, likBeta.m, likInvGauss.m

New inference functions in inf/
 - infKL.m

New covariance functions in cov/
 - covSM.m, covGaboriso.m, covGaborard.m (contributed by Andrew Gordon Wilson)
 - covPPard.m, covMaternard.m, covLINiso.m
 - covCos.m, covPeriodicNoDC.m (contributed by James Robert Lloyd)
 - covPERiso.m, covPERard.m


### NEW in version 3.2, 2013-01-15
- added inf/infFITC_Laplace.m and inf/infFITC_EP.m for large scale approximate inference
- added inf/infMCMC.m to allow for Markov Chain Monte Carlo sampling and Annealed Importance Sampling; we currently support two samplers: Elliptical Slice Sampling (ESS) and Hybrid Monte Carlo (HMC)
- improved accuracy of the digamma function lik/likT.m/dloggamma from three to ten digits precision (thanks to Edward Challis)
- added gfortran support to util/lbfgsb/Makefile (thanks to Ernst Kloppenburg)
- fixed a stupid bug in cov/covFITC.m (thanks to Krzysztof Chalupka)
- fixed a severe stability bug in the derivatives of inf/infFITC.m (thanks to Andrew McHutchon)
- changed scalars in util/solve_chol.c to long to provide 64bit compatibility (thanks to Ernst Kloppenburg)
- added infLOO to allow pseudo-likelihood optimisation instead of the marginal likelihood
- fixed a performance issue in util/sq_dist.m (thanks to Krzysztof Chalupka)
- fixed a bug causing numerical problems in infFITC and covFITC whenever Kuu is close to singular (thanks to Joris M. Mooij)
- fixed an issue in covNoise (thanks to Daniel Marthaler)
- fixed implicit derivative w.r.t. likelihood in infLaplace*
- added two more likelihood functions likMix.m for mixtures and likUni for classification noise
- slightly changed the interface for the likelihood functions
- added likPoisson for Poisson regression of count data


### NEW in version 3.1, 2010-10-10
- following a suggestion by Ed Snelson we now support FITC regression
- cov/covFITC.m and inf/infFITC.m have been added with Ed Snelson's help
- the covariance interface was slightly changed to make that possible
- changed parametrisation in cov/covPeriodic.m (thanks to Philipp Hennig)


### NEW in version 3.0, 2010-07-23
A major code reorganisation effort did take place in the current release. First,
classification and regression are now done by a single file gp.m which is
completely generic in the likelihood. The previous regression program gpr.m
corresponds to gp.m with Gaussian likelihood. Several other likelihoods for
robust regresssion were added.
Further, the code now supports mean functions with a similar specification
mechanism as already used by the covariance functions. Previous implementations
correspond to using meanZero.m.
We merged the covariance functions covMatern3iso.m and covMatern5.iso into a
single covMaterniso.m and added a covariance function for additive functions as
well as the possibility to use only certain components of the data. Finally, we
included covPPiso.m a piecewise polynomial covariance function with compact
support.

New likelihood functions in lik/
 - likGauss.m, likLaplace.m, likLogistic.m and likT.m

New mean functions in mean/
 - meanConst.m, meanLinear.m, meanPow.m, meanProd.m, meanSum.m and meanZero.m

New covariance functions in cov/
 - covADD.m, covMask.m, covPPiso.m

The gprSRPP.m function which previously provided "Subset of Regressors" and the
"Projected Process" approximation has now been removed.


### NEW in version 2.1, 2007-07-25
covConst.m: fixed a bug which caused an error in the derivative of the log marginal
    likelihood for certain combinations of covariance functions and approximation
    methods. (Thanks to Antonio Eleuteri for reporting the problem)

gauher.m: added the function "gauher.m" which was mistakenly missing from the
    previous release. This caused an error for certain combinations of
    approximation method and likelihood function.

logistic.m: modified the approximation of moments calculation to use a mixture
    of cumulative Gaussian, rather than Gauss-Hermite quadrature, as the former
    turns out to be more accurate.


### NEW in version 2.0, 2007-06-25
Some code restructuring has taken place for the classification code to make it
more modular, to facilitate addition of new likelihood functions and
approximations methods. Now, all classification is done using the binaryGP
function, which (among other things) takes an approximation method and a
likelihood function as an arguments. Thus, binaryGP replaces both binaryEPGP
and binaryLapaceGP, although wrapper functions are still provided for backward
compatibility. This gives added flexibility: now EP can also be used wth the
logistic likelihood function (implemented using Gauss-Hermite quadrature).

approxEP.m: New file, containing the Expectation Propagation approximation
    method, which was previously contained in binaryEPGP.m

approxLA.m: New file, containing Laplaces approximation method, which was
    previously contained in binaryLaplace.m

approximations.m: New file, help for the approximation methods.

binaryEPGP.m: This file has been replaced by a wrapper (for backward
    compatibility) which calls the more general binaryGP function.

binaryGP.m: New general function to do binary classification.

binaryLaplaceGP.m: This file has been replaced by a wrapper (for backward
    compatibility) which calls the more general binaryGP function.

covMatern3iso.m, covMatern5iso.m, covNNone.m, covRQard.m, covRQiso.m,
cosSEard, covSEiso: now check more carefully, that persistent variables have
    the correct sizes, and some variable names have been modified.

cumGauss.m: New file, containing code for the cumulative Gaussian
    likelihood function

likelihoods.m: New file, help for likelihood functions

logistic.m: New file, logistic likelihood


### NEW in version 1.3, 2006-09-08
covRQard.m: bugfix: replaced x with x' and z with z' in line 36

covRQiso.m: bugfix: replaced x with x' and z with z' in line 28

minimize.m: correction: replaced "error()" with "error('')", and
            made a few cosmetic changes

binaryEPGP.m: added the line "lml = -n*log(2);" in line 77. This change
         should be largely inconsequential, but occationally may save things
         when the covariance matrix is exceptionally badly conditioned.


### NEW in version 1.2, 2006-05-10
added the "erfint" function to "binaryLaplaceGP.m". The erfint function
was missing by mistake, preventing the use of the "logistic" likelihood.


### NEW in version 1.1, 2006-04-12
added files: "covProd.m" and "covPeriodic.m"

changes: "covSEiso.m" was changed slightly to avoid the use of persistent
         variables

### NEW in version 1.0, 2006-03-29
initial version shipped with the book
