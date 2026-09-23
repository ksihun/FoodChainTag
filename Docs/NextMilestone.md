# FoodChainTag — 다음 마일스톤 계획

상태: **계획**  
작성일: 2026-09-23

## 프로젝트 목표

`A → B → C → A` 순환 포식 관계의 에이전트가 2D Top-Down 환경에서
추격·회피·생존을 학습하고, 장기적으로는 자원과 번식이 만드는 개체군
동역학을 관찰하는 강화학습 시뮬레이션 게임을 만든다.

## 현재 기준점

- Unity: `6000.3.12f1`, 2D URP 프로젝트
- GitHub 원격과 Unity 프로젝트는 저장소 루트에 하나로 정리됨
- Hera Agent Unity `0.1.2` 연결 확인, Unity Console Error `0`건
- ML-Agents Unity 패키지 `4.0.0` 설치 확인
- Python 학습 환경과 `mlagents-learn` CLI는 아직 설정하지 않음
- 게임플레이 코드, 전용 Arena 씬, 에이전트 프리팹은 아직 없음

## 진행 원칙

- 한 Goal은 검증 가능한 결과 하나를 만든다.
- 각 Goal 완료 전 Unity 컴파일 오류와 Console Error가 `0`건인지 확인한다.
- 게임 규칙, 보상, 수치 계산은 EditMode 테스트로 검증한다.
- 씬·이동·충돌·관측 변경은 대표 PlayMode 시나리오로 검증한다.
- 학습이 필요한 Goal은 Unity 검증 외에 학습 실행 로그와 핵심 지표를 남긴다.
- Energy, 번식, 수풀, RNN, 관전자 UI는 이전 단계의 완료 조건을 충족한 뒤에만 추가한다.

## 마일스톤 개요

| 순서 | Goal | 결과 | 상태 |
| --- | --- | --- | --- |
| 0 | 기준점 고정 | 재현 가능한 Unity·패키지·문서 기준점 | 완료 |
| 1 | Core Arena | A/B/C가 있는 2D 경기장과 순환 상성 | 계획 |
| 2 | Agent Interface | 이동 Action과 근접 환경 Observation | 계획 |
| 3 | 첫 학습 루프 | Python 학습 환경과 추격·회피 정책 검증 | 계획 |
| 4 | Survival | Energy, Food, Starvation, Corpse | 계획 |
| 5 | Population | 번식과 개체군 로그 | 계획 |
| 6 | Terrain & Vision | 장애물, 제한 시야, 수풀 | 계획 |
| 7 | Memory | RNN과 No-Memory 비교 | 계획 |
| 8 | Spectator & Experiments | 관전 화면과 장기 생태계 실험 | 계획 |

---

## Goal 0 — 기준점 고정

상태: **완료**

### 목표

현재 Unity 프로젝트, Hera, ML-Agents 패키지와 작업 문서를 첫 기준점으로
고정해 이후 변경을 안전하게 추적한다.

### 고정 범위

1. 현재 Unity 프로젝트 파일과 패키지 잠금 파일을 첫 커밋으로 기록한다.
2. `AGENTS.md`, 이 문서, 구현 설계 문서를 저장소에서 관리한다.
3. `Library/`, `Temp/`, `Logs/`, `UserSettings/`가 Git에 포함되지 않는지 확인한다.

### 완료 판단

- `main` 브랜치에서 Unity 프로젝트를 다시 열 수 있다.
- Hera 상태가 FoodChainTag 프로젝트를 가리킨다.
- ML-Agents와 Hera 패키지가 Package Manager에서 확인된다.

### 제외 범위

- Python 학습 환경 설치
- 게임플레이 코드와 씬 변경

### 구현 결과 (2026-09-23)

- Unity `6000.3.12f1` 2D URP 프로젝트를 저장소 루트에 고정했다.
- Hera Agent Unity `0.1.2`와 ML-Agents `4.0.0`을 고정된 Git 버전으로 기록했다.
- Hera 연결, Package Manager 상태, Unity 컴파일과 Console Error `0`건을 확인했다.
- Unity 생성 파일, 작업 규칙, 개발·검증 워크플로, 마일스톤 계획을 기준점 커밋에 포함했다.
- `Library/`, `Temp/`, `Logs/`, `UserSettings/`는 Git ignore 상태임을 확인했다.

---

## Goal 1 — Core Arena

### 목표

2D Top-Down Arena에서 A, B, C 에이전트가 생성되고 순환 포식 관계를
일관되게 판별한다.

### 고정 범위

1. `Assets/Scenes/Arena.unity`와 `Assets/Scripts/` 기본 구조를 만든다.
2. 화면 경계가 있는 평지 경기장과 2D 카메라를 만든다.
3. `SpeciesType`과 순환 상성 규칙을 구현한다.
4. A, B, C 에이전트 프리팹을 만들고, 동일한 기본 이동 스탯을 적용한다.
5. 종별 초기 개체를 생성하고, 현재 개체의 먹이와 천적을 확인할 수 있게 한다.

### 완료 판단

- A의 먹이는 B, 천적은 C이며 B와 C도 같은 순환 규칙을 만족한다.
- 세 종의 기본 이동 수치가 동일하다.
- Arena 씬을 Play Mode로 열어 에이전트 생성과 경기장 경계를 확인한다.

### 제외 범위

- ML-Agents Observation/Action
- 포획, 보상, Energy, Food, 사망
- 장애물과 제한 시야

### 검증

- EditMode: 순환 상성의 3개 정상 사례와 잘못된 자기 포식 사례.
- PlayMode: Arena 진입 시 종별 에이전트가 지정 수만큼 생성되는지 확인.

---

## Goal 2 — Agent Interface

### 목표

각 에이전트가 ML-Agents를 통해 이동하고, 가까운 먹이·천적을 구분해
관측할 수 있게 한다.

### 고정 범위

1. `FoodChainAgent`에 연속 이동 Action을 연결한다.
2. 에이전트의 위치, 이동 방향, 가까운 먹이와 천적의 상대 위치를 Observation으로 제공한다.
3. 무작위 정책 또는 Heuristic으로 이동 Action이 유효한지 확인한다.
4. Arena 밖으로 나가지 않도록 이동을 제한한다.

### 완료 판단

- Action 입력이 에이전트의 2D 이동으로 반영된다.
- A/B/C가 자기 종, 먹이, 천적을 올바르게 구분한 Observation을 생성한다.
- 관측 대상이 없을 때도 NaN이나 예외 없이 안전한 기본값을 반환한다.

### 제외 범위

- 포획 판정과 학습 보상
- 시야각, 장애물 가림, 수풀
- 실제 Python 학습 실행

### 검증

- EditMode: 대상 종 분류, 대상 없음, 경계 위치 관측.
- PlayMode: Heuristic 이동, 경기장 경계 제한, Console Error `0`건.

---

## Goal 3 — 첫 학습 루프

### 목표

Python 학습 환경에서 ML-Agents를 실행하고, 포식자는 먹이를 포획하며
피식자는 천적을 피하는 정책이 학습될 수 있음을 확인한다.

### 고정 범위

1. 지원되는 Python 버전의 프로젝트 전용 가상환경을 만든다.
2. Unity ML-Agents `4.0.0`과 호환되는 Python `mlagents` 패키지를 설치한다.
3. 포식자와 먹이의 접촉 포획 규칙 및 결과 중심 Reward를 구현한다.
4. 최소 학습 설정 YAML과 실행 방법을 저장소에 추가한다.
5. 짧은 학습 실행으로 체크포인트와 TensorBoard 로그를 만든다.

### 완료 판단

- `mlagents-learn`이 FoodChainTag 환경과 통신한다.
- 접촉 포획 시에만 포식 보상이 발생하고, 단순 접근에는 보상이 없다.
- 학습 실행이 예외 없이 진행되며, 포획·회피 관련 지표를 기록한다.

### 제외 범위

- Energy, Food, 시체, 번식
- 장시간 균형 또는 개체군 실험

### 검증

- EditMode: 포획 대상 판정, 자기 종/천적 포획 거부, Reward 계산.
- PlayMode: 접촉 포획과 에피소드 리셋.
- 학습: 짧은 smoke run과 재현 가능한 학습 명령 기록.

---

## Goal 4 — Survival

### 목표

Energy와 공용 Food, 아사와 Corpse를 도입해 생존과 사냥의 선택을 만든다.

### 고정 범위

1. Energy 감소, Food 생성·최대 개수·섭취를 구현한다.
2. 포식 사망은 즉시 제거하고 Corpse를 남기지 않는다.
3. 아사 사망만 Corpse를 생성하고, 포식 관계에 맞는 종만 섭취할 수 있게 한다.
4. Alive와 Corpse 상태를 Observation에서 명확히 구분한다.

### 완료 판단

- Energy가 0인 개체만 Corpse를 남긴다.
- 살아 있는 먹이와 같은 종의 Corpse가 서로 다른 Observation 상태로 처리된다.
- Food가 설정된 최대 개수를 넘지 않는다.

---

## Goal 5 — Population

### 목표

Energy를 부모에서 자식으로 전달하는 자동 번식과 개체군 기록을 추가한다.

### 고정 범위

1. Energy 임계값·쿨다운 기반 번식을 구현한다.
2. 부모 비용, 자식 초기 Energy, 손실을 분리해 Energy 보존을 보장한다.
3. 종별 Population, 출생, 포식사, 아사, 평균 Energy를 기록한다.

### 완료 판단

- 번식이 외부 Energy를 생성하지 않는다.
- 전체 개체 수를 강제로 고정하지 않아도 지표가 시간에 따라 기록된다.

---

## Goal 6 — Terrain & Vision

### 목표

장애물과 부분 관측으로 우회·은신·추적 포기 행동을 만들 수 있게 한다.

### 고정 범위

1. 벽·바위·좁은 통로가 이동과 시야를 차단하게 한다.
2. Vision Range와 Vision Angle을 적용한다.
3. 수풀 규칙을 구현한다: 밖에서 수풀 안은 보이지 않고, 수풀 안에서는 같은 수풀 안과 바깥을 볼 수 있다.

### 완료 판단

- 장애물 뒤 대상은 Observation에 포함되지 않는다.
- 수풀 안의 개체가 수풀 밖 관찰자에게 숨는다.

---

## Goal 7 — Memory

### 목표

제한 시야 환경에서 No-Memory 정책과 RNN 정책을 비교한다.

### 고정 범위

1. 동일한 맵·초기 조건·측정 지표로 두 정책을 학습한다.
2. 수풀 진입 후 추적 성공률, 회피율, 평균 생존 시간, 추적 포기 시점을 기록한다.

### 완료 판단

- 두 실험의 설정과 결과가 재현 가능하게 저장된다.
- Memory 사용 여부에 따른 차이를 정량 지표로 비교할 수 있다.

---

## Goal 8 — Spectator & Experiments내가 

### 목표

학습된 생태계를 관찰하고 실험 조건별 개체군 변화를 비교할 수 있게 한다.

### 고정 범위

1. Population, Food, Birth, Death, 평균 Energy, Simulation Time UI를 만든다.
2. Pause와 시간 배속, 개체 선택·Follow Camera, 이벤트 로그를 추가한다.
3. Food 공급, Corpse Energy, 번식 비용, 수풀 밀도, 종별 Trade-off를 바꿀 수 있게 한다.
4. Population 그래프와 멸종 시간·진동·안정성 지표를 기록한다.

### 완료 판단

- 플레이어가 학습된 시뮬레이션을 멈추고 관찰하며 핵심 사건을 확인할 수 있다.
- 적어도 하나의 장시간 실험에서 조건과 결과 지표를 재현할 수 있다.

## 다음 실행 Goal

**Goal 0 — 기준점 고정**을 먼저 완료한다. 완료 후 `Docs/Goal01_CoreRLArenaSpec.md`를 작성하고 Goal 1 구현을 시작한다.
