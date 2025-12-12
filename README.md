# Reinforcement Learning Game Agent with Q-Learning

본 프로젝트는 **강화학습(Reinforcement Learning)** 기법을 사용하여  
실제 실행 중인 게임을 **에이전트가 직접 조작하며 학습**하도록 설계된 프로젝트입니다.  
환경을 코드로 시뮬레이션하지 않고, **게임 화면 캡처 + 마우스/키보드 제어**를 통해  
강화학습 에이전트를 학습시키는 것이 특징입니다.

---

## Project Overview

이 프로젝트에서는 게임을 하나의 환경(environment)으로 간주하고,  
에이전트가 화면 상태(state)를 관찰한 뒤 특정 행동(action)을 수행하여  
보상(reward)을 얻도록 **Q-Learning 기반 학습**을 수행합니다.

### 핵심 아이디어
- Game screen = **state**
- Mouse / keyboard input = **action**
- Game ending / transition = **reward & termination**
- Explicit simulator 없이 **real-game interaction 기반 RL**

---

## Reinforcement Learning Method

### Q-Learning Agent
- Tabular **Q-Learning** 사용
- ε-greedy policy로 exploration / exploitation 균형
- Update rule:
$$Q(s,a) \leftarrow (1-\alpha)Q(s,a) + \alpha(r + \gamma \max_a Q(s',a))$$

**주요 파라미터**
- `alpha` (learning rate)
- `epsilon` (exploration rate, epoch마다 감소)
- `discount` (discount factor)

Q-value는 dictionary 형태로 저장되며, 학습 중간 결과를 `.pkl` 파일로 저장/불러오기 가능

---

## Environment Design

### Game Environment (`DateSimulation`)
- 실제 게임 창을 environment로 사용
- `ImageGrab`을 통해 현재 화면 캡처
- 화면 전체를 **state**로 사용 (pixel-level)
- `pyautogui`를 이용해:
  - 마우스 이동
  - 클릭 / 드래그
  - 키 입력(space 등)

### State Representation
- 게임 화면을 이미지로 캡처
- OpenCV format으로 변환 후 tuple 형태로 저장
- 이전 상태와 현재 상태의 **similarity 비교**로 상태 안정성 판단

---

## Reward Design

보상 함수는 게임의 **엔딩 상태 인식**을 기반으로 설계됨:

- **Normal / Good ending** → 큰 양의 보상
- **Bad ending** → 큰 음의 보상
- 그 외 상태 → step penalty (-1)

### Ending Detection
- Ending 화면 이미지를 미리 정의
- HSV color histogram 기반 **image similarity**로 ending 판별
- Normal / Bad ending을 자동 구분

---

## Training Pipeline

1. 게임 초기화 (`reset`)
2. 현재 화면 상태 캡처
3. ε-greedy 정책으로 action 선택
4. 실제 게임에 action 수행
5. 다음 상태, 보상, 종료 여부 획득
6. Q-value 업데이트
7. episode 반복 학습

학습 과정에서:
- Episode reward 기록
- Reward history를 CSV로 저장
- 주기적으로 Q-table checkpoint 저장

---

## Visualization & Analysis

- Episode별 reward 변화 시각화
- 최근 episode 평균 reward 추적
- 학습 안정성 및 성능 개선 여부 확인

---

## Code Structure Overview

- `agent.py`  
  Q-Learning agent 구현 (policy, update, save/load)

- `sim.py`  
  게임 환경 정의 및 state / reward / action 처리

- `train.py`  
  전체 강화학습 학습 루프 실행

- `utill.py`  
  이미지 처리, similarity 계산, reward 저장 등 유틸 함수

- `reward_plot.py`  
  학습 reward 변화 시각화

- `ending_similarity_test.py`  
  엔딩 화면 판별 테스트

- `sim_algorithm_test.py`  
  이미지 histogram 기반 similarity 비교 실험

---

## Key Learning Outcomes

- 강화학습의 기본 구조(Q-learning) 이해
- 실제 게임 환경을 RL environment로 추상화하는 방법
- Reward design의 중요성
- State similarity 기반 termination detection
- Simulator 없이 real-world interaction으로 RL 수행 시 발생하는 어려움
- Exploration–Exploitation trade-off 체감

---

## Notes

- 본 프로젝트는 **educational purpose**로 구현되었습니다.
- 게임 해상도, UI 위치에 따라 action 좌표는 조정이 필요합니다.
- Real-time interaction 기반이므로 실행 환경에 의존적입니다.

---

## Takeaway

본 프로젝트는  
**“강화학습을 실제 게임에 적용하면 어떤 문제가 발생하는가?”**를 직접 체험하는 실험입니다.  
명시적인 simulator 없이도,  
적절한 state 표현과 reward 설계를 통해 강화학습이 가능함을 보여줍니다.
