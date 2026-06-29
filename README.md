# TDA-Based Sparse Index Tracking: Replication and Extension

Replication of Goel, Pasricha & Kanniainen (2025) and a Beta–Volatility extension. The original paper uses persistence landscape norms from Topological Data Analysis (TDA) as asset-specific regularisation parameters in a weighted Elastic-Net to construct sparse S&P 500 tracking portfolios. This study reproduces that framework and tests whether CAPM beta and volatility can serve the same role with clearer economic interpretation.

## What this repo contains

```
.
├── TDA_Final.ipynb                    # Full implementation
├── Asian Option Pricing with SPD.pdf  # Full project report
└── README.md
```

## Method

Both the TDA replication and the economic extension use the same weighted Elastic-Net objective:

```
min_w  ||R_in * w - r0_in||² + Σ aᵢ|wᵢ| + Σ bᵢ²wᵢ²   s.t. 1ᵀw = 1
```

What differs is how `aᵢ` and `bᵢ` are constructed.

**TDA models** — penalty coefficients are L¹ norms of persistence landscapes computed from Takens-embedded return sub-series via Vietoris–Rips filtration and GUDHI:

| Model | aᵢ | bᵢ |
|---|---|---|
| TE | 0 | 0 |
| TDA-Lasso | H0 norm | 0 |
| TDA-EN11 | H0 norm | H0 norm |
| TDA-EN12 | H0 norm | H1 norm |

**Economic extension (BetaVol)** - penalties derived from in-sample CAPM beta and return volatility, cross-sectionally scaled each window:

| Model | aᵢ | bᵢ |
|---|---|---|
| Beta-EN11 | 1/(|β̂ᵢ| + ε) | 1/(|β̂ᵢ| + ε) |
| Vol-EN11 | σᵢ | σᵢ |
| BetaVol | 1/(|β̂ᵢ| + ε) | σᵢ |
| BetaVol-Long | 1/(|β̂ᵢ| + ε) | σᵢ, wᵢ ≥ 0 |

Rolling windows: 504-day in-sample, 21-day out-of-sample, step 21 days. Data from Yahoo Finance. Period I: Oct 1999–Oct 2018 (193 windows, 355 stocks). Period II: Jan 2018–Mar 2023 (38 windows, 477 stocks).

## Key results

| Model | Tracking Error (P1) | Correlation (P1) | Ann. Return (P1) | Turnover (P1) |
|---|---:|---:|---:|---:|
| TDA-EN12 | 0.001627 | 0.9902 | 12.65% | 0.396 |
| BetaVol | 0.008579 | 0.9469 | 18.60% | 0.145 |
| Vol-EN11 | 0.007474 | 0.7720 | 13.73% | 0.190 |

TDA models win on tracking accuracy and benchmark correlation. BetaVol produces higher returns and lower turnover but wider drawdowns and tail risk. The two penalty systems are not measuring the same thing — TDA H0 and inverse-beta have Spearman correlation of −0.54 in Period I, while TDA H1 and volatility correlate at +0.57.

## Setup

```bash
pip install numpy pandas matplotlib yfinance cvxpy ripser gudhi tqdm
jupyter notebook
```

Open `TDA_Final.ipynb` and run top to bottom. Data is pulled live from Yahoo Finance and Wikipedia.

## Reference

Goel, A., Pasricha, P. & Kanniainen, J. (2025). *Risk reduced sparse index tracking portfolio: A topological data analysis approach*. Omega, 103432.

