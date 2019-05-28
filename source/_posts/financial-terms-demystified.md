---
title: Financial Terms Demystified
date: 2019-05-28 16:38:19
tags:
  - finance
categories:
  - random
---

This glossary contains definitions of key financial and modelinng terms and concepts.

<!-- more -->

# alpha

The excess returns relative to a benchmark.

# beta

A measure of risk of a security or portfolio relative to the market as a whole.

# exogenous factors

Endogenous and exogenous are economic terms to describe internal and external factors respectively affecting business production, efficiency, growth and profitability.

# financial instrument

A product that can be bought and sold in a market place. Examples include stocks, bonds, currencies and commodities. Financial instruments are traded in different venues all over the world.

# forecast

An educated guess of the future return on a financial instrument. Someone or something's prediction of the return of a financial instrument based on information available up to and including time t. Forecasts are fed into the optimizer.

# model

A mathematics based computer program that forecasts future prices.

# portfolio

A collection of models.

The portfolio itself can be regarded as one "big model", and we can compute its Sharpe ratio using the same formula as that used for a single model.

# position

A position is the amount of a security, commodity or currency that is owned (a long position) or borrowed and then sold (a short position) by an individual, institution or dealer.

# predictor

A signal (observable quantity) that suggests movement in the price of a financial instrument.

# risk-adjusted return

Risk-adjusted return refines an investment's return by measuring how much risk is involved in producing that return, which is generally expressed as a number or rating. Risk-adjusted returns are applied to individual securities, investment funds and portfolios.

# P&L

Consider a single instrument that can be traded, and consider a single time interval:

- time interval: we consider the range from time to time, sometimes written as t. Sometimes, for example, we might measure t in days.

For this instrument on this interval, we define the following:

- price: price of instrument at time t (comes from the market)

- return: return of instrument over the time interval (comes from prices, which come from the market)

- forecast: someone or something's prediction of the return of a financial instrument based on information available up to and including time t. Forecasts are fed into the optimizer.

- position: position in dollars that we hold over the time interval from t to t+1. This is positive for a long position or negative for a short position. Desired position is determined by the optimizer, and execution/traders buy/sell to get us into actual position.

- P&L: is the profit/loss that we obtain by holding position in the instrument over the time interval from t to t+1

# Sharpe ratio

Sharpe ratio is a ratio of return versus risk. The higher the Sharpe ratio is, the more return the portfolio is getting per unit of risk. The lower the Sharpe ratio is, the more risk the portfolio is shouldering to earn additional returns.

Sometimes in the formula for Sharpe ratio we subtract either a risk-free P&L or some benchmark rate. There are also other performance measures that could be used apart from Sharpe ratio, including Information ratio, Sortino ratio, Maximum drawdown and others.
