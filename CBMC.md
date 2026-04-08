## 문제 2


### Data
- Prime(1st)-Test Result of each product
    - $z_1 = (z_1^1, \ldots, z_1^{N_1}) \in \lbrace 0, 1 \rbrace^{N_1}$
    - Total Inputs: $N_1$
    - Total Fails: $S_1 = \sum_{n=1}^{N_1} z^n_1 \leq N_1$
- Re(2nd)-test Result of each product
    - $z_2 = (z_2^1, \ldots, z_2^{N_2}) \in \lbrace 0, 1 \rbrace^{N_2}$
    - Total Inputs: $N_2 \leq S_1$
    - Total Fails: $S_2 = \sum_{n=1}^{N_2} z^n_2 \leq N_2$


## Model: CBMC
**C**onditional **B**inary **M**arkov **C**hain


### Bernoulli-Initialized State
Prime test result ($Z_1 \in \{0, 1\}$) of individual device :

$$ \begin{equation}
Z_1 = X_1 \lor Y, \text{ where } X_1 \sim Ber(p), Y \sim Ber(q)
\end{equation} $$

### Markov Transition
Retest result ($Z_t \in \{0, 1\}$ for $t > 1$) :

$$ \begin{equation}
Z_t = \begin{cases}
    X_t \in \{0, 1\} & \text{if } Z_{t-1} = 1 \\
    \text{Undefined} & \text{if } Z_{t-1} = 0
\end{cases} \quad \text{ with } \quad P[X_t \neq X_{t-1} | X_{t-1}] = c
\end{equation} $$


## Solution


### Estimation

$$ \begin{align}
P[Z_1 = 1] &= P[X = 1 \text{ or } Y = 1] \nonumber \\
&= P[X = 1] + P[Y = 1] - P[X = 1 \text{ and } Y = 1] \nonumber \\
&= p + q - pq
\end{align} $$

$$ \begin{align}
P[Z_2=1 | Z_1=1] &= P[X_2=1 | Z_1=1] \nonumber \\
&= P[X_2=1, Z_1=1] / P[Z_1=1] \nonumber = \cdots \nonumber \\
&= \dfrac{p + c\{(p + q - pq) - 2p\}}{p + q - pq}
\end{align} $$

$$ \begin{align}
P[Z_3=1 | Z_2=1, Z_1=1] &= P[X_3=1 | X_2=1] \nonumber \\
&= 1-c
\end{align} $$


### Testing



## Apendix: derivation


### $(4)$
$$ \begin{align}
P[X_2=1, Z_1=1] =
& P[(X_1=1 \text{ or } Y=1) \text{ and } X_2=1] \nonumber \\
=
& P[Y=1, X_1=0, X_2=1] + \nonumber \\
& P[Y=1, X_1=1, X_2=1] + \nonumber \\
& P[Y=0, X_1=1, X_2=1] \nonumber \\
=
& P[Y=1] \cdot P[X_1=0] \cdot P[X_2=1 | X_1=0] + \nonumber \\
& P[Y=1] \cdot P[X_1=1] \cdot P[X_2=1 | X_1=1] + \nonumber \\
& P[Y=0] \cdot P[X_1=1] \cdot P[X_2=1 | X_1=1] \nonumber \\
=
& P[Y=1] \cdot P[X_1=0] \cdot P[X_2=1 | X_1=0] + \nonumber \\
& P[Y=1] \cdot P[X_1=1] \cdot P[X_2=1 | X_1=1] + \nonumber \\
& P[Y=0] \cdot P[X_1=1] \cdot P[X_2=1 | X_1=1] \nonumber \\
=
& q (1-p) c + q p (1-c) + (1-q) p (1-c) \quad \because (1), (4) \nonumber \\
=
& p + c(p + q - pq - 2p) \nonumber
\end{align} $$


$$ \begin{align}

\end{align} $$


## legacy


## Model
Let $z^n_t$ be an observation of the random variable, where is modeled as

$$
Z_t = \begin{cases}
    X \lor Y & \text{if } t = 1 \\
    X \mid Z_1 = 1 & \text{if } t = 2
\end{cases}, \text{ where } X \sim Ber(p), Y \sim Ber(q)
$$
- $Z_t$: Binary test result of the product at $t$-th test
- $X$: Whether product-induced Failure occurs on the product
- $Y$: Whether environment-induced Failure occurs on the product
    - not defined for $t = 2$
        - ← Retest는 환경성 이슈가 없는 설비에서 진행된다는 가정

Then the probablility of prime test result is

$$
\begin{aligned}
    P(Z_1 = 1) &= P(X = 1 \text{ or } Y = 1) \\
    &= P(X = 1) + P(Y = 1) - P(X = 1 \text{ and } Y = 1) \\
    &= p + q - pq ,
\end{aligned}
$$

while that of retest result is

$$
\begin{aligned}
P(Z_2 = 1) &= P(X = 1 | Z_1 = 1) \\
&= P(X = 1 \text{ and } Z_1 = 1) / P(Z_1 = 1) \\
&= P(X = 1) \cdot P(Z_1 = 1 | X = 1) / P(Z_1 = 1) \\
&= \dfrac{p \cdot 1}{p + q - pq} .
\end{aligned}
$$


## PEP Estimator
(**P**roduct/**E**nvironment-Induced Failure **P**robability Estimator)

Since sample proportion is the MLE (Maximum Likelihood Estimator) of population proportion, denoting $\hat{\cdot}$ as the MLE of $\cdot$,

$$
\begin{aligned}
\widehat{P(Z_1 = 1)} &= \widehat{p + q - pq} = S_1 / N_1, \\
\widehat{P(Z_2 = 1)} &= \widehat{p / (p + q - pq)} = S_2 / N_2.
\end{aligned}
$$


Using invariance property of MLE, which states that $\widehat{g(\theta)} = g(\hat{\theta})$,

$$
\begin{cases}
\hat{p} + \hat{q} - \hat{p} \hat{q} = S_1 / N_1 \\
\hat{p} / (\hat{p} + \hat{q} - \hat{p} \hat{q}) = S_2 / N_2
\end{cases} ,
$$

which leads to the following solution:
- $\hat{p} = \dfrac{S_1 S_2}{N_1 N_2}$
- $\hat{q} = \dfrac{S_1 N_2 - S_1 S_2}{N_1 N_2 - S_1 S_2}$


### Retest Decision Rule (Naive Ver.)
(No consideration of estimator's variation)

Retest only if $\hat{q} > c\hat{p}$, which is equivalent to

$$
\dfrac{S_1 N_2 - S_1 S_2}{N_1 N_2 - S_1 S_2} \cdot \dfrac{N_1 N_2}{S_1 S_2} > c
$$


## PEP Comparison Test (To be written)

$$
\begin{gathered}
H_0: q \leq cp \\
H_1: q > cp
\end{gathered}
$$

To build a Wald Test, Defining $\theta = q - cp$, then

$$
\begin{gathered}
H_0: \theta \leq 0 \\
H_1: \theta > 0
\end{gathered}
$$

Test statistic:

$$
T = \dfrac{\hat{\theta}}{SE(\hat{\theta})} \sim N(0, 1)
$$

Delta Method to derive $SE(\hat{\theta})$:

$$\cdots$$

<!-- Let $g(p, q) = q - cp$, then

$$
SE(\hat{\theta}) = \sqrt{(\nabla g)^T \Sigma (\nabla g)}
$$

where $\nabla g = \begin{bmatrix} -c & 1 \end{bmatrix}$ and $\Sigma$ is the covariance matrix of $(\hat{p}, \hat{q})$. -->


### Retest Decision Rule (Confident Ver.)

Reject $H_0$, that is, decide to retest if

$$
T  > N(0, 1)_{1-\alpha},
$$

where $N(0, 1)_{1-\alpha}$ denotes the $(1 - \alpha)$ quantile of $N(0, 1)$.
