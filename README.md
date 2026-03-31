# Retest Decision Rule
제품/환경성 불량률을 고려한 Retest 결정 규칙


## Problem


### 배경: Retest의 양면성
- 장점: 수율 제고
    - Retest로 환경성 불량을 구제
    - 제고 수준은 구제량에 비례하며, 구제량은 환경성 불량 확률 $q$에 비례
- 단점: 출하 품질 리스크
    - fail 이력이 있던 걸 출하하는 게 다소 risky할 수 있음
        - 경미한(?) 제품성 불량이 있는 제품이 restest에선 아슬아슬하게 pass될 수도 있으므로
    - 이러한 가능성은 제품성 불량 확률 $p$에 비례(한다고 가정)


### 문제 정의: Retest를 결정할 통계적 기준 마련
- 출하 품질 리스크 대비 Retest를 통한 양산 효율 제고가 큰 경우에만 Retest를 시행
- 즉, $q > cp$ 인 경우에만 Retest 시행
    - $c$는 현업 관점에서 충분히 큰 상수로 상정
- 그러나 우리는 **$p$와 $q$가 얼마인지** 모름 → **추정해야**


### 재료: 테스트 결과 데이터
- Prime(1st)-Test Result of each product
    - $z_1 = (z_1^1, \ldots, z_1^{N_1}) \in \lbrace 0, 1 \rbrace^{N_1}$
    - Total Inputs: $N_1$
    - Total Fails: $S_1 = \sum_{n=1}^{N_1} z^n_1 \leq N_1$
- Re(2nd)-test Result of each product
    - $z_2 = (z_2^1, \ldots, z_2^{N_2}) \in \lbrace 0, 1 \rbrace^{N_2}$
    - Total Inputs: $N_2 \leq S_1$
    - Total Fails: $S_2 = \sum_{n=1}^{N_2} z^n_2 \leq N_2$


## Solution: **PEFER**
**P**roduct/**E**nvironment-Induced **F**ailure Probability **E**stimator for **R**etest Decision (Retest 결정을 위한 제품/환경성 불량 확률 추정량)


### Model
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


### Maximum Likelihood Estimator (MLE)
Since sample proportion is MLE of population proportion, denoting $\hat{\cdot}$ as MLE of $\cdot$,
- $\widehat{P(Z_1 = 1)} = \widehat{p + q - pq} = S_1 / N_1$
- $\widehat{P(Z_2 = 1)} = \widehat{p / (p + q - pq)} = S_2 / N_2$.

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


### Retest Decision Rule
Retest only if $\hat{q} > c\hat{p}$, which is equivalent to

$$
\dfrac{S_1 N_2 - S_1 S_2}{N_1 N_2 - S_1 S_2} \cdot \dfrac{N_1 N_2}{S_1 S_2} > c
$$
