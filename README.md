# EV Charging Strategy  
### Dynamic Pricing and Forecasting for EV Charging Networks using SARIMAX, GARCH, and N-BEATS

## Overview
This project builds an AI-driven dynamic pricing framework for EV charging networks by combining classical time-series modeling, volatility analysis, and deep learning. The goal is to forecast charging demand, detect high-volatility congestion periods, and simulate pricing strategies that can improve revenue while staying operationally realistic.

The analysis starts with network-level demand forecasting, then moves to station-level modeling where localized congestion patterns are more informative for pricing decisions. The final framework uses:

- **SARIMAX** for interpretable baseline forecasting
- **GARCH(1,1)** for volatility clustering and surge detection
- **N-BEATS** for deep forecasting of high-variance station demand
- **Dynamic pricing simulation** to estimate revenue uplift under congestion-aware triggers

---

## Problem Statement
EV charging demand is highly time-dependent and operationally uneven across stations. Network-wide averages often smooth out the exact spikes where pricing decisions matter most. Project aim:

- Forecasting utilization in EV charging systems
- Identifying volatile demand periods,
- Designing a pricing trigger that reacts only when both **demand is high** and **volatility is elevated**.

---

## Dataset
The project uses EV charging station availability data with the following characteristics:

- ~1.3 million records
- 150 stations
- 6 months of data
- 30-minute granularity
- date range from 2025-07-01 to 2025-12-31

The dataset also includes contextual features such as weather, gas prices, congestion, events, time-of-day, and weekday/weekend flags.

---

## Project Workflow

### 1. Data Preparation
- Loaded and cleaned timestamped EV charging data
- Aggregated usage into time-series format
- Engineered temporal and external features such as:
  - hour of day
  - day of week
  - weekend indicators
  - weather variables
  - event-related conditions

### 2. Network-Level Forecasting
At the network level, utilization is relatively smooth, so a classical model performs strongly.

- Seasonal Naive MAPE: 1.49%
- SARIMAX MAPE: 3.32%

This showed that aggregated demand is already highly predictable, leaving little room for deep learning to outperform a strong baseline.

### 3. Volatility Modeling with GARCH
To identify unstable demand conditions, the project fits a GARCH(1,1) model on utilization dynamics.

Key result:
- α + β = 0.9737, indicating strong volatility persistence

This volatility estimate becomes one half of the dynamic pricing trigger.

### 4. Station-Level Forecasting
The second notebook improves the design by moving from network averages to **individual high-variance stations**, where demand spikes are much more meaningful.

Results:
- Station-level SARIMAX MAPE: 537.99%
- Station-level N-BEATS MAPE: 12.99%
- MAE improvement of N-BEATS vs station SARIMAX: 86.2%
- **Spike-period improvement:** 38.8% during demand spikes

This shows that deep learning becomes valuable once the target is changed from a smooth network average to localized congestion forecasting.

### 5. Dynamic Pricing Simulation
The pricing logic uses a **dual trigger**:

- utilization exceeds threshold
- volatility exceeds threshold

Final trigger configuration:
- utilization > 0.630
- volatility > 1.047
- trigger rate: 7.0%

Using elasticity-adjusted demand, this pricing rule produced:
- Revenue uplift: +2.25%
- compared with +0.45% in the earlier version

---

## Key Insights
- Aggregation hides opportunity. Network-level averages are easy to forecast but not useful for intelligent pricing.
- Station-level modeling matters. High-volatility stations are where forecasting difficulty and pricing opportunity both exist.
- Volatility is actionable. GARCH helps identify unstable demand periods instead of reacting to raw utilization alone.
- Deep learning helps where classical models fail. N-BEATS significantly outperformed SARIMAX on localized, spike-heavy demand series.
---

## Results Summary

| Metric | Result |
|---|---:|
| Seasonal Naive MAPE | 1.49% |
| Network SARIMAX MAPE | 3.32% |
| Station SARIMAX MAPE | 537.99% |
| Station N-BEATS MAPE | 12.99% |
| N-BEATS MAE Improvement | +86.2% |
| Spike-Period Improvement | +38.8% |
| GARCH Persistence (α + β) | 0.9737 |
| Trigger Rate | 7.0% |
| Revenue Uplift | +2.25% |
