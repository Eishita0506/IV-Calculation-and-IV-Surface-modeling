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
## Methodology for IV Calculations
Implied Volatility is the volatility that makes the theoritical option price equal to the market price. It is forward looking and risk adjusted. However there is no close form solution for finding IV. It is usually calculated using numerical methods. Here I have compared two of the most used methods of IV calculation
