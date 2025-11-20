# propensy-score
Advanced Causal Inference Project
Propensity Score Matching (PSM) & Doubly Robust (AIPW) Estimation
1. Overview

This project implements a full causal inference workflow using synthetic data. The goal is to estimate the Average Treatment Effect (ATE) of a binary treatment using:

Propensity Score Matching (PSM)

Matched-sample regression

Doubly Robust AIPW Estimation

The dataset, algorithms, balance diagnostics, and estimators are implemented in a clean, reproducible pipeline.

The matching is done on logit propensity score with a caliper, and AIPW uses a polynomial outcome model to allow for nonlinearities.

2. Data Generation

A synthetic dataset is generated with:

N = 5000 units

6 continuous confounders (X1–X6)

Nonlinear transformations:
sin(x), x^2, exp(0.2x), tanh(x), interactions

Treatment assignment follows:
logit p = nonlinear(X)

Outcome model:
Y = μ0(X) + τ(X)*T + noise

Treatment effect is heterogeneous, depending on X.

The script stores:

true_propensity

true_mu0

true_mu1

true ATE from the data-generating process

3. Propensity Score Model

A logistic regression model is fitted using the six covariates:

P(T=1 | X) = LogisticRegression(X1–X6)


Saved quantities:

p_hat — estimated propensity score

logit_p_hat — used for matching

4. Matching Procedure

The project performs 1:1 nearest-neighbor matching with:

Distance metric: logit(p̂)

Caliper: 0.2 × SD(logit(p̂))

Replacement: off

Outputs:

Matched dataset

Table of matched pairs

Caliper used

Number of matched pairs

5. Covariate Balance Evaluation

Balance is assessed using Standardized Mean Differences (SMDs):

Reported before matching

Reported after matching

This demonstrates the success of the matching procedure in reducing confounding.

6. Estimators

The following ATE estimators are computed and compared:

6.1 Naive Estimator (Unadjusted)

Difference in mean outcomes between treated and untreated groups.

6.2 PSM Estimator (Matched Differences)

For each matched pair:

ATE_PSM = mean(Y_treated – Y_control)


Standard error computed via standard deviation across pairs.

6.3 Matched-Sample Regression

On the matched sample:

Y = β0 + β1*T + β2*X + ... + error


Coefficient on T is interpreted as the ATE.

HC1 robust standard errors used.

6.4 Doubly Robust AIPW Estimator

Implements the standard AIPW influence function:

𝜓
𝑖
=
𝑇
𝑖
(
𝑌
𝑖
−
𝑚
1
(
𝑋
𝑖
)
)
𝑝
𝑖
−
(
1
−
𝑇
𝑖
)
(
𝑌
𝑖
−
𝑚
0
(
𝑋
𝑖
)
)
1
−
𝑝
𝑖
+
𝑚
1
(
𝑋
𝑖
)
−
𝑚
0
(
𝑋
𝑖
)
ψ
i
	​

=
p
i
	​

T
i
	​

(Y
i
	​

−m
1
	​

(X
i
	​

))
	​

−
1−p
i
	​

(1−T
i
	​

)(Y
i
	​

−m
0
	​

(X
i
	​

))
	​

+m
1
	​

(X
i
	​

)−m
0
	​

(X
i
	​

)

Where:

p_i = estimated propensity score

m1(X), m0(X) = predicted outcomes using a polynomial model (degree 2)

Bootstrapped SE with 200 resamples

AIPW is consistent if either the propensity model or outcome model is correctly specified.

7. Outputs & Files

The script automatically generates and saves:

7.1 Data File

synthetic_data.csv
Contains all variables, including true effects and propensities.

7.2 Report File

this_report.txt
Contains:

SMD table

All ATE estimates + SEs

True ATE summary

Caliper and matching details

Brief interpretation

Files are saved in:

/mnt/data/causal_project_outputs/

8. How to Run the Code

Open the .ipynb notebook or Python script containing the provided code.

Ensure dependencies (below) are installed.

Run all cells in order.

Outputs will appear at the end of execution and files will be saved automatically.

9. Dependencies

The project uses the following Python libraries:

numpy
pandas
scikit-learn
statsmodels


All libraries are standard and widely available through pip:

pip install numpy pandas scikit-learn statsmodels

10. Interpretation Summary

The naive estimator is biased due to confounding.

Matching greatly improves covariate balance (SMDs reduce toward zero).

PSM and matched regression give reasonable ATE estimates after adjustment.

AIPW generally performs best due to its double-robust nature and flexible polynomial outcome model.

Compare all estimates to the true ATE (from simulation) for validation.

11. Reproducibility

Random seeds are fixed.

All intermediate quantities are stored.

The script is fully self-contained and reproducible.

If needed, you may customize:

Caliper size

Matching ratio

Outcome model complexity

Bootstrap iterations

Number of confounders

Treatment effect functional form
