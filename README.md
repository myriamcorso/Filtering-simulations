# Filtering simulations: Kalman, unscented and particle filters

Numerical experiments written during an internship at Inria Montpellier
(MathNeuro team) for the master's thesis *The Free Energy Principle and
Nonlinear Filtering* (Laurea Magistrale in Matematica, University of Bologna,
2026). The two notebooks compare three discrete-time filters, first on a linear
model where the exact answer is known and then on a nonlinear benchmark where
it is not.

## Contents

| Notebook | Model | Filters | Figures |
|---|---|---|---|
| `linear-ou-kalman.ipynb` | Scalar Ornstein–Uhlenbeck process, sampled exactly, linear Gaussian observations | Kalman, UKF, bootstrap PF | `ou_estimates.pdf`, `ou_mse_time.pdf`, `ou_pf_convergence.pdf` |
| `kitagawa-ukf-particle.ipynb` | Kitagawa benchmark, $y_k = X_k^2/20 + v_k$ | UKF, bootstrap PF | `kitagawa_trajectories.pdf`, `kitagawa_truth_obs.pdf`, `kitagawa_errors.pdf`, `kitagawa_overlay.pdf` |

Each notebook writes its figures and a CSV table of metrics to `figures/`.

### Linear case (Ornstein–Uhlenbeck)

The state solves $dX_t = -\theta X_t\,dt + \sigma\,dW_t$ with $\theta = 0.5$,
$\sigma = 1$, and is observed every $\Delta t = 1$ with noise variance
$R = 0.5$. On this model the Kalman filter is exact, so it serves as a
reference for the other two. The UKF reproduces the Kalman mean up to rounding
(maximum gap $4.4 \times 10^{-16}$), since the unscented transform is exact for
affine maps. The root-mean-square gap between the particle mean and the Kalman
mean falls from $0.163$ at $N = 25$ to $0.011$ at $N = 5000$, at the Monte
Carlo rate $N^{-1/2}$.

### Nonlinear case (Kitagawa)

$$X_k = \tfrac{1}{2}X_{k-1} + \frac{25\,X_{k-1}}{1 + X_{k-1}^2} + 8\cos(1.2\,k) + w_k,
\qquad y_k = \frac{X_k^2}{20} + v_k,$$

with $Q = 10$, $R = 1$, $X_0 \sim \mathcal{N}(0, 5)$ and 60 steps. The
observation is even in $X_k$, so it cannot tell $x$ from $-x$ and the
filtering distribution is often bimodal. Over 50 independent realizations with
$N = 10^4$ particles:

| Filter | RMSE (mean ± std) |
|---|---|
| Unscented Kalman filter | 7.646 ± 0.434 |
| Bootstrap particle filter | 4.542 ± 0.834 |

A single Gaussian cannot represent two modes, which is why the UKF error is
about 68% higher than the particle filter's.

## How to run

```bash
pip install -r requirements.txt
jupyter notebook
```

Open a notebook and run it top to bottom (*Kernel → Restart & Run All*). All
seeds are fixed, so the numbers above are reproduced exactly; only the timing
column of `ou_metrics.csv` changes from machine to machine. Tested with
Python 3.11, NumPy 1.26, SciPy 1.17 and Matplotlib 3.10.

## Author

Myriam, internship with the MathNeuro team (Inria Montpellier).
