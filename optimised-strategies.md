

* My master’s thesis, finished in spring 2016.
* The thesis was written while working full-time at OP financial group.
* Motivation for the thesis was building a multi-period asset allocation optimiser for strategic long-term allocation planning.
* Full pdf available at [Systems analysis laboratory web page](http://sal.aalto.fi/publications/pdf-files/ttuo16_public.pdf)

## Introduction

Starting point for this thesis was a need for a multi-period allocation planner.

The key difference to traditional allocation planning tools is now the multi-periodicity, since most allocation planner tools are able to accommodate only one-period considerations. This means that the allocation is optimised at the beginning of the planning horizon, and kept unchanged until the end. It is clearly an unrealistic assumption, since many investors are free to update their portfolio along the way. This also leads to myopic decision making, if the decision is made let say annually, but the true planning horizon may be 10 years, 20 years or even more.

In this thesis I studied how we could build scenario trees to model the different outcomes in the financial markets. The scenario trees were generated using a method called moment matching.

Scenario trees were generated for 5 different market invariants extracted from the historical data, and respective asset returns were constructed according to the invariant trees.

Finally we were able to apply stochastic optimisation to solve the optimal allocation in each node of the scenario trees.

## Historical data

In the next few figures we show the historical data behind the model.

In the first figure we have the five time series representing the asset classes modeled for the study:

* Money market
* Government bonds
* Corporate bonds (investment grade)
* Equities (DAX index)
* Real estate (local property price index from Finland supplemented with a rental yield and subtracted a fixed maintenance cost of 3% annually).

The return indices do not, however reveal the true stochastic nature behind these asset classes, and in the following figure we show the risk drivers:

* Money market rate
* Government bond rate
* Investment grade spread.
For equities and real estate we assumed the risk driver do be the asset price.

Market invariants are in this context the true stochastic variables, for which we are able to come up with a sensible stochastic model.

The invariants are:

* Compound return for stocks and real estate
* Absolute difference in the risk driver for rates and spread.

![Market data](/figures-optimised-strategies/time-series1.png)

![Invariant time series](/figures-optimised-strategies/time-series1.png)

![Histogram](/figures-optimised-strategies/histograms.png)

## Generating scenario trees

From the historical data we estimated the scenario trees to be used in the stochastic optimisation. Generating trees was done in a couple steps, for which more details can be found in the thesis. [1]

Projecting the moments to the horizon:

As seen in previous figures, we had extracted the distributions from quarterly data. However, we wanted to build the trees so that the time step between nodes would be one year. This required us to project the quarterly data to annual scale. More details on the projection can be found in a paper by Attilio Meucci. [2]

Updating the moment values

We assumed the higher moments (skewness and kurtosis) to remain the same during the planning horizon.

For the first and second moment (mean and standard deviation), we did, however, incorporate a dynamic model. For equity and real estate factors the mean was assumed to develop in time according to a risk premium approach, and the standard deviation takes into account so called “volatility clumping”.

For interest rate factors (short rate, bond rate, and investment grade spread), we employed the CIR model with an additional ad-hoc tweak: in the traditional formulation the standard deviation is scaled with a square root of the current level of the interest rate, but here we took the absolute value before applying the square root. In the presence of extremely low interest rates it could lead to taking square root of below zero numbers, which is unwanted behavior for the model. Taking the square root solves the problem neatly, leaving the dampening effect intact when the rates converge to zero, but enabling the model to recover from occasional sub-zero rates.

In the CIR model the rate is assumed to converge back to the equilibrium level. which is subject to estimation and could be set by an expert opinion. Another parameter in the CIR model is the speed how fast the rate will return back to the equilibrium. In this thesis these parameters were estimated using the past history of extremely low interest rates from Japan, and the equilibrium level was set to the current level.

![Scenario trees](/figures-optimised-strategies/scenario-trees.png)

## Optimisation and results

Finally after having defined all the scenario trees we are able to conduct the optimisation.

The resulting optimised scenario tree with selected risk aversion coefficients a and b is shown in black dashed line in the following figure. Along with the optimised tree for the dynamic strategy we have included a fixed-mix strategy counterpart, which is a fixed portfolio optimised for the entire planning horizon with same input and preferences as the dynamic strategy.

In the panel on the right is shown the wealth distribution at the end of the planning horizon. It seems that the extremely positive observations are on the occurring on the dynamic strategy, whereas the fixed-mix strategy does not reach similarly high wealth levels at the end.

On the other hand, the dynamic strategy has more probability mass below the target return, and in the lower tail of the distribution. It seems that the dynamic strategy produces both very positive, and very negative outcomes.

All this depends of course on the risk aversion defined by the utility function.

![Optimised scenario trees for certain parameters](/figures-optimised-strategies/optimisation-result.png)

In the next figure we show the optimized allocations for different risk aversions and different target returns. The utility function was constructed as a shortfall cost with two parameters, and in each column of panels we have fixed the other risk aversion parameter, while the other changes along the horizontal axis.

In each row we have different target returns, below which the shortfall cost part in the utility function kicks in and penalizes for not reaching the target.

![Optimal allocations for different risk aversion parameters](/figures-optimised-strategies/optimised-allocations.png)

## Conclusions and end notes

The conclusions of this thesis were in a nutshell

* Dynamic strategy performs better than the fixed-mix counterpart
* Selected parameters affect the end result heavily: the projected long term equilibrium interest rate is among the important parameters to be defined
* Deriving decision rules for updating the allocation was left unclear, and more dense scenario trees would be beneficial
* For extreme levels of risk tolerance the dynamic strategy makes only little difference
* Very risk averse investors would pick an extremely low risk portfolio
* High risk tolerance investor would bet everything on the asset with highest expected return
