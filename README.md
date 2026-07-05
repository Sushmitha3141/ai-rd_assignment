# AI R&D Assignment – Parametric Curve Fitting

## Problem Statement

The assignment provides a parametric curve:

x = t·cos(θ) − e^(M|t|)·sin(0.3t)·sin(θ) + X
y = 42 + t·sin(θ) + e^(M|t|)·sin(0.3t)·cos(θ)

with t ranging from 6 to 60. Three parameters — θ, M, and X — are
unknown, within the bounds:

- 0° < θ < 50°
- −0.05 < M < 0.05
- 0 < X < 100

A dataset of (x, y) points sampled from this curve is given
(`xy_data.csv`), and the goal is to recover θ, M, and X.

## My Approach

**1. Explored the data**
The CSV only contains `x` and `y` columns — no `t` value is given,
and the row order is shuffled. So the first challenge wasn't fitting
the curve, it was figuring out which point corresponds to which `t`.

**2. Plotted it first**
Before writing any fitting code, I plotted x vs y to see the shape.
The scatter plot showed a single smooth curve with no visible loops or reversals along the x-axis.

**3. Recovered the t-ordering by sorting on x**
For this dataset, sorting the points by x reconstructed the parameter ordering because the fitted curve was monotonic in x over the given interval. This wouldn't hold if the curve looped or
reversed direction in x, but since it doesn't here, the assumption
holds.

After reconstructing the ordering, uniformly spaced values of t over
the interval [6, 60] were assigned to the sorted observations.

**4. Implemented the model**
Wrote the two equations directly as a Python function that takes
t, θ, M, X and returns (x, y).

**5. Fit the parameters**
Used `scipy.optimize.least_squares` with `loss='soft_l1'` (a robust
loss close to L1, matching the assignment's L1-distance evaluation
metric) to search for θ, M, X within the given bounds that minimize
the difference between the model curve and the real data.

**6. Checked the result two ways**
- Plotted the fitted curve on top of the original scatter data —
  it lined up almost exactly, with no visible gap anywhere.
- The recovered equation was also plotted using the provided Desmos
  template as an independent visual check.

The fitted curve almost completely overlapped the observed data, suggesting that the estimated parameters describe the dataset well.


## Results

The optimizer converged successfully within the specified parameter
bounds and produced a stable solution.

| Parameter | Value |
|---|---|
| θ (radians) | 0.5252 |
| θ (degrees) | ≈30.09° |
| M | 0.0299 |
| X | 55.01 |

```
Optimization Status : Success
Iterations          : 12
Total Error         : 457.3466
Average Error/Point : 0.3049
Max L1 Error         : 1.1836
```

### Final Equation (Desmos / LaTeX format)

\left(t\cdot\cos\left(0.5252\right)-e^{0.0299\left|t\right|}\cdot\sin\left(0.3t\right)\cdot\sin\left(0.5252\right)+55.0146,42+t\cdot\sin\left(0.5252\right)+e^{0.0299\left|t\right|}\cdot\sin\left(0.3t\right)\cdot\cos\left(0.5252\right)\right)

Verified visually here: https://www.desmos.com/calculator/y1hw3tokpo

## Files in this repo

- `xy_data.csv` — provided dataset
- `curve_fit.py` — data loading, visualization, model, fitting, and validation
- `scatter.png` — final plot of fitted curve vs. real data

## Running it

\`\`\`bash
pip install numpy pandas scipy matplotlib
python curve_fit.py
\`\`\`