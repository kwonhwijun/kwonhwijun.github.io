---
title: "Sticky HDP-dDT: 강건한 비모수 동적 그래프 모형"
excerpt: "다변량 시계열의 의존 네트워크가 시간에 따라 변할 때, 상태 수·전이 시점을 데이터가 스스로 학습하는 베이지안 비모수 모형 — 고려대 통계학과 석사학위논문."
collection: portfolio
date: 2025-12-01
---

## Robust Non-parametric Dynamic Graphical Models
### Adaptive State Discovery in Noisy Bio-signals (Sticky HDP-dDT)

**고려대학교 통계학과 석사학위논문** · 2025-12 발표 · 지도교수 최태련

---

### 한 줄 요약
다변량 시계열의 **의존 네트워크가 시간에 따라 변하고 관측에 이상치가 섞여 있을 때**,
상태의 개수와 전이 시점을 **데이터가 스스로 추론**하면서도 강건하고 해석 가능한
네트워크를 추정하는 베이지안 비모수 모형.

### 핵심 기여
- **상태 수 자동 추론**: dynamic engine을 **Sticky HDP-HMM**으로 두어, 잠재 상태의 수 *K* 를
  사전에 고정하지 않고 데이터에서 추론.
- **이상치 강건성**: 관측모형을 **Dirichlet-t** 로 두어 노이즈가 큰 bio-signal에 강건.
- **희소·해석 가능한 네트워크**: 정밀도행렬을 연속 **spike-and-slab** 으로 추정해
  희소하고 읽을 수 있는 그래프 구조를 얻음.
- **추론 가속**: Block Gibbs(FFBS · slice sampling · column-wise update)로 추론하고,
  **Sylvester 항등식**을 활용해 핵심 연산을 O(p³) → O(p²) 로 가속.

### 키워드
`베이지안 비모수` · `HDP-HMM` · `동적 그래프 모형(GGM)` · `spike-and-slab` · `MCMC`
