---
title: "메타러너(Meta-Learners)로 이질적 처치효과 추정하기"
date: 2026-06-13
categories:
  - causal
tags:
  - 인과추론
  - meta-learner
  - CATE
  - uplift
toc: true
toc_sticky: true
---

Matheus Facure의 *Causal Inference for the Brave and True* 를 따라 공부하는 첫 글.
"평균적으로 효과가 있나?"를 넘어서 **"누구에게 효과가 큰가?"**를 묻는 메타러너를 정리한다.

## 왜 ATE만으로는 부족한가

A/B 테스트로 얻는 건 보통 **ATE(Average Treatment Effect)** 하나다.

$$\text{ATE} = E[Y_1 - Y_0]$$

쿠폰을 뿌렸더니 평균 재구매가 3% 올랐다 — 좋다. 그런데 실무에서 진짜 궁금한 건
**"이 고객한테 쿠폰을 주는 게 이득인가?"** 다. 어떤 고객은 쿠폰이 없어도 어차피 살 사람이고
(돈 낭비), 어떤 고객은 쿠폰이 있어야만 움직인다(설득 가능). 우리가 원하는 건 개인/세그먼트별
효과, 즉 **CATE(Conditional Average Treatment Effect)** 다.

$$\tau(x) = E[Y_1 - Y_0 \mid X = x]$$

문제는 한 사람에게서 $Y_1$ 과 $Y_0$ 를 **동시에 볼 수 없다는 것**(인과추론의 근본 문제).
그래서 머신러닝 회귀 모델을 빌려 반사실(counterfactual)을 채워 넣는다. 그 방식의 차이가
곧 메타러너의 차이다. (전제: 처치 $T$ 가 **무작위 배정**이거나, 적어도 unconfoundedness가 성립.)

## S-learner (Single model)

가장 단순하다. 처치 변수 $T$ 를 **그냥 하나의 feature로** 넣어 모델 하나를 학습한다.

$$\mu(x, t) = E[Y \mid X = x, T = t]$$

CATE는 $T=1$ 과 $T=0$ 을 각각 대입한 예측의 차이로 추정한다.

$$\hat\tau(x) = \mu(x, 1) - \mu(x, 0)$$

- **장점**: 구현이 제일 쉽고 데이터를 다 쓴다. 효과가 약할 때 안정적.
- **단점**: 트리/부스팅 모델이 $T$ 를 **중요하지 않은 변수로 무시**하면 $\hat\tau(x) \approx 0$ 으로
  눌려버린다(regularization bias). 효과를 과소추정하기 쉽다.

## T-learner (Two models)

처치군과 대조군을 **아예 분리해 모델 두 개**를 학습한다.

$$\mu_1(x) = E[Y \mid X = x, T = 1], \quad \mu_0(x) = E[Y \mid X = x, T = 0]$$

$$\hat\tau(x) = \mu_1(x) - \mu_0(x)$$

- **장점**: S-learner처럼 $T$ 가 무시될 일이 없다. 두 그룹의 반응 함수가 다를 때 자연스럽다.
- **단점**: 한쪽 그룹 표본이 작으면(불균형) 그쪽 모델이 부정확해지고, 두 모델의 오차가
  **독립적으로 쌓여** $\hat\tau$ 가 출렁인다.

## X-learner

T-learner의 불균형 약점을 보완한 버전. Künzel et al.(2019)이 제안했고, Facure 책에서도
실무 기본값으로 추천한다. 3단계다.

**1단계** — T-learner처럼 $\mu_0, \mu_1$ 을 따로 학습한다.

**2단계** — 각 그룹에서 **반사실을 채워 넣어 효과의 "추정 라벨"(imputed effect)** 을 만든다.

$$\tilde\tau_1 = Y_i - \mu_0(X_i) \quad (\text{처치군}), \qquad
  \tilde\tau_0 = \mu_1(X_i) - Y_i \quad (\text{대조군})$$

그리고 이 imputed effect를 타깃으로 다시 회귀 모델 $\tau_1(x), \tau_0(x)$ 를 학습한다.

**3단계** — 성향점수 $e(x) = P(T=1 \mid X=x)$ 로 둘을 **가중 평균**한다.

$$\hat\tau(x) = e(x)\,\tau_0(x) + (1 - e(x))\,\tau_1(x)$$

직관: 처치군이 적은 영역($e(x)$ 작음)에서는 처치군에서 만든 $\tau_1$ 을 믿지 못하므로,
대조군 기반 $\tau_0$ 쪽에 가중치를 더 준다. 표본이 불균형해도 안정적인 이유다.

- **장점**: 그룹 불균형에 강건. 처치 효과가 있는 영역을 잘 잡아낸다.
- **단점**: 모델을 여러 개(2 + 2 + 성향점수) 쌓아서 구현·튜닝이 복잡하다.

## 어떻게 고르나

| | 무시 위험 | 불균형 강건 | 복잡도 | 언제 |
|---|---|---|---|---|
| **S-learner** | 높음 | — | 낮음 | 효과 약하고 안정성이 중요할 때 |
| **T-learner** | 없음 | 약함 | 중간 | 두 군 표본이 비슷할 때 |
| **X-learner** | 없음 | 강함 | 높음 | 처치/대조군 비율이 크게 불균형할 때 |

실무 감각으로는 **X-learner를 기본**으로 두되, 빠른 베이스라인이 필요하면 S/T로 시작하는 식.
다만 어떤 메타러너든 **반사실은 결국 모델의 외삽**이라는 점을 잊으면 안 된다 —
공변량 분포가 겹치지 않는(positivity 위반) 영역에서의 CATE는 신뢰하기 어렵다.

## 다음 글에서

- 추정한 CATE를 **어떻게 평가하나** — Qini curve, cumulative gain 같은 uplift 평가 지표
- R-learner / DR-learner 같은 직교화(orthogonalization) 계열 메타러너

---

*참고: Matheus Facure, [Causal Inference for the Brave and True](https://matheusfacure.github.io/python-causality-handbook/) · Künzel et al. (2019), "Metalearners for estimating heterogeneous treatment effects."*
