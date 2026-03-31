# Overview
This project presents a comprehensive study of the implied volatility (IV) structure in equity options markets using S&P 500 (SPX) options data.The project demonstrates how theoretical models, numerical methods, and real market data come together to reveal the structure of implied volatility.The work is divided into two core components:
- Numerical estimation of implied volatility using root-finding methods
- Empirical analysis of volatility smile, skew, and term structure, followed by construction of a smoothed volatility surface using cubic spline interpolation
## Objective
The Black-Scholes Model assumes constant volatility, which is inconsistent with real markets.
In practice:
- Implied volatility varies across strike prices giving volatility smile/skew
- Implied volatility varies across maturities giving rise to term structure
This project aims to empirically analyze these deviations and construct a realistic volatility surface.
## Methodology for IV Calculations (IV_calc.ipynb)
Implied Volatility is the volatility that makes the theoritical option price equal to the market price. It is forward looking and risk adjusted.
- f(σ)=CBS​(S,K,T,r,σ)−Cmarket​=0
However there is no close form solution for finding IV. It is usually calculated using numerical methods. Here I have compared two of the most used methods of IV calculation:
#### Newton Raphson Method: To iteratively approximate the root of f(σ) using first-order derivative information. It uses local slope information to rapidly adjust the volatility estimate toward the root.
Algorithm
- f(σ)=CBS​−Cmarket​
- f′(σ)=∂σ∂C​=Vega
##### Limitations
May fail if:
- Vega is very small (deep OTM/ITM options)
- Initial guess is poor
- Can diverge or oscillate
##### Bisection Method: To find the root by repeatedly narrowing an interval where the function changes sign.
Algorithm
- Initialize bounds
- Choose: σlow​,σhigh​​
- Compute midpoint: σmid​=(σlow​+σhigh)/2​
- Evaluate function: f(σmid​)
- Update interval: If f(σlow​)⋅f(σmid​)<0, root lies in left half. Else: root lies in right half
- Check convergence. Stop if:∣f(σmid​)∣<ϵ
- Repeat until interval is sufficiently small
##### Advantages
- No derivative required
- Highly stable
- Works even when Vega is small
##### Limitations
- Slower compared to Newton-Raphson
- Requires valid initial bounds
### Insight
Newton-Raphson provides rapid convergence by leveraging local gradient information, while the bisection method ensures robustness through interval bracketing. A hybrid approach combining both methods is often employed in practice to achieve both speed and stability in implied volatility estimation.
## Methodology for IV Surface Construction (IV_clean.ipynb)
Option-chain data for the S&P 500 index was obtained using the yfinance API. The dataset represents a cross-sectional snapshot of the options market across multiple maturities.To standardize strikes across maturities, log-moneyness was used: k=ln(K/F​)
- To avoid redundancy from put-call parity:
- K<F to retain OTM puts
- K>F to retain OTM calls
- Near ATM - averaged values
### Volatility Smile/Skew Analysis
Implied Volatility smile refers to the change in IV with strike. It can be observed that IV is minimum near ATM and higher in wings or for OTM put and OTM call options. Hence, market expects non-normal return distributions with fat tails. Also a negative skew can be seen in  the equity like SPX as there is strong demand for downside protection.
- k<0 (OTM puts) = higher IV
- k>0 (OTM calls) = lower IV
### Term Structure Analysis
The variation of IV with time to maturity is know as term structure. 
##### Contango
In normal market environment, IV tends to rise as time to maturity increases as there is more uncertainty over longer horizons giving rise to upward- sloping term structure known as contango. 
##### Backwardation
During times of market stress or ahead of major events the term structure can flip with near-term options exhibiting higher IV and hence downward sloping structure known as backwardation is formed.
### Cubic Spline Interpolation
To smooth the volatility smile, for each maturity, fitted a cubic spline to IV vs log-moneyness.The full implied volatility surface was constructed as:σ=f(k,T). The surface was visualized using 3D surface plot and contour map. While cubic spline interpolation provides a smooth and visually appealing representation of the implied volatility surface, its lack of arbitrage constraints, sensitivity to noisy data, and instability in sparse regions limit its applicability to descriptive analysis rather than robust pricing or risk management frameworks.
### Learnings from IV_raw.ipynb
An initial implementation of the volatility surface was constructed directly from raw option-chain data without applying any smoothing or interpolation techniques. While this approach provided a transparent view of the underlying market quotes, it revealed significant challenges, including noise in deep out-of-the-money regions, irregularities in the volatility smile, and discontinuities across maturities. These issues highlighted the limitations of relying solely on raw implied volatility data and motivated the need for systematic preprocessing and smoothing. As a result, this baseline implementation served as a crucial diagnostic step, allowing for a clearer understanding of data quality issues and justifying the subsequent application of cubic spline interpolation to obtain a more coherent and interpretable volatility surface.
## Conclusion
The empirical analysis of SPX options reveals that implied volatility exhibits systematic variation across both strike and maturity dimensions, forming a structured volatility surface rather than a constant parameter. The observed volatility smile and negative skew highlight the market’s pricing of tail risk and asymmetric downside protection. Term structure analysis further indicates that volatility expectations evolve over time, reflecting changing uncertainty horizons. While cubic spline interpolation provides a smooth and coherent representation of the surface, the absence of arbitrage constraints suggests that such models are best interpreted as descriptive tools rather than fully consistent pricing frameworks.
