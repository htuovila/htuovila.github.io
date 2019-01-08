# Using copulas in combining investor views and market information for return distributions of equity indices

* My bachelor’s thesis finished in spring 2014.
* Studied and implemented the Copula Opinion Pooling (COP) model for two equity indices as originally suggested in an article by Attilio Meucci. Explored multiple choices for copula, and implemented the model in MATLAB
* Thesis was graded “pass with distinction”.
* Original title in Finnish: “Osakkeiden tuottojakaumia koskevien markkina- ja asiantuntijanäkemysten yhdistely copula-funktioilla”.
* Seminar presentation (in Finnish) [available online](http://sal.aalto.fi/files/opinnot/kurssit/mat-2.kandi/esittelyt/tuovilahenr-valmis.pdf).

## Motivation

Motivation for this thesis was to implement the COP model (Copula Opinion Pooling) as suggested by Attilio Meucci. [1] The idea is to impose practitioner’s subjective views to the neutral ‘prior’ distributions to come up with a twisted ‘posterior’ distribution. The resulting (joint) posterior distribution can then be used as an input to portfolio optimisation.

Exact notation can be found in the link provided above, or in the paper by Meucci [1].

The intuition of the model goes as follows:

1. Extract the prior joint distribution
   1. Estimate the marginal distributions
   1. Estimate the copula
1. Get the subjective views from an investor/expert/practitioner (views could be expressed as any form of a distribution)
1. Twist the priors according to the views (and the confidence)
1. Assuming the copula (dependencies) remains the same, combine the twisted marginals and intact copula, and you have come up with joint posterior distribution.

To keep the project scope reasonable the data was chosen to be simple equity indices (total return), for which the model would be calibrated and tested. These two equity indices were the French CAC 40, and the German DAX both shown in the following figures.

![Time series](/figures-copula-equity/time-series.webp)

And the joint distribution of the weekly returns of the two indices CAC 40 and DAX.

![Scatter plot of weekly returns](/figures-copula-equity/scatter.webp)

The QQ-plot of both indices suggest non-normal fat tails, which makes COP as a more flexible model superior to the Black-Litterman model, where both the priors and the views are expressed as normal distributions.

![QQ-plot of weekly returns](/figures-copula-equity/qq-plot.webp)

Next we studied the copulas, and tried to estimate the best fit. For this purpose we used an L2-norm as suggested in [2]. However, it is important to note that not one rigid method for estimating the right copula exists, and the approach has more room for improvement here.

The empirical copula is shown in the figure below.
![Scatter plot of the empirical copula](/figures-copula-equity/copula-scatter.webp)

Different parametric copulas. The fit measured by L2-norm.

![Parameters for best fit copulas from different families](/figures-copula-equity/copula-parameters.webp)

In the figure below a contour plot of some parametric copulas compared to the empirical copula. Clearly the t-copula and normal copula give the best fit.

![Contour plot of different best-fit copulas](/figures-copula-equity/copula-contour.webp)

Next we generated joint distribution from the t-copula, which was seen as the best fit in the previous step. These uniform marginal distributions tied together according to the copula were transformed to the joint distribution of the true variables by using the inverse CDF transformation. The CDF was approximated by non-parametric kernel density estimation (ksdensity-function in MATLAB).

The simulated joint scenarios shown in the figure below.

![Prior simulation](/figures-copula-equity/simulation-prior.webp)

Finally, we imposed an arbitrary view on the other equity index, which was expressed as a uniform distribution from 0 to 0.1. Confidence was set to c=0.2.

On the other index no view was expressed, which means the marginal is left intact.

The twisted joint distribution (posterior) is seen below.

![Posterior simulation after applying the views](/figures-copula-equity/simulation-posterior.webp)

In the last figure below we have combined all the interesting distributions in the same plot: prior, view, and the posterior. The view is shown in the blue dashed line.

It is worth noting that the confidence of the view has a significant effect on the end result. An extreme example would be a full confidence of 1.0, which means the prior distribution has no effect on the resulting posterior distribution. In such case we would expect the view distribution to be a more sensible one than the uniform distribution used here for demonstration purposes.

## End notes and conclusions

Benefits of the COP model compared to the famous Black-Litterman model are that it does not assume the distributions normal. Moreover, the dependency structure can be modelled more flexibly using copula functions. This is a clear advantage, since many asset returns can not be assumed jointly normal.

On the other hand, one disadvantage is that the flexible way to express views may be difficult to grasp, and providing meaningful view is not as straight forward as in Black-Litterman model. The added complexity rules out intuitive interpretation, which is an additional disadvantage.

## References

[1]: [Meucci, Attilio. “Beyond Black-Litterman: Views on Non-Normal Markets”](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=848407)

[2]: [Durrleman et al. “Which copula is the right one?”](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=1032545)
