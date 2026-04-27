# 역운동학 / Inverse Kinematics

---

## 📌 목차 / Table of Contents

1. [역운동학 / Inverse Kinematics](#1-역운동학--inverse-kinematics)
2. [순운동학 vs 역운동학 / Forward vs Inverse Kinematics](#2-순운동학-vs-역운동학--forward-vs-inverse-kinematics)
3. [역운동학이 필요한 이유 / Why Inverse Kinematics](#3-역운동학이-필요한-이유--why-inverse-kinematics)
4. [역운동학의 어려움 / Challenges](#4-역운동학의-어려움--challenges)
5. [해결책 MoveIt2 / Solution MoveIt2](#5-해결책-moveit2--solution-moveit2)

---

## 1. 역운동학 / Inverse Kinematics

> **한국어**
> 운동학은 힘과 토크와 같은 운동이 일어나는 원인은 고려하지 않고 운동을 연구하는 학문.
> Inverse Kinematics는 운동학 방정식을 사용하여 원하는 위치에 도달하기 위한 로봇의 움직임을 파악하는 방법.

> **English**
> Kinematics is the study of motion itself, without considering the causes of motion, such as force or torque.
> Inverse kinematics is a method that uses kinematics equations to determine the motion required for a robot to reach a desired position.

---

## 2. 순운동학 vs 역운동학 / Forward vs Inverse Kinematics

> **한국어**
> 순운동학은 각 관절의 각도를 알 때 그리퍼의 위치와 방향을 구하는 문제입니다.
> 역운동학은 그 반대로, 그리퍼의 목표 위치와 방향을 알 때 각 관절이 취해야 할 각도를 구하는 문제입니다.

> **English**
> Forward kinematics determines the gripper's position and orientation given each joint angle.
> Inverse kinematics solves the opposite: given a desired gripper position and orientation, find the joint angles needed to reach it.

| 항목 / Item | 순운동학 / Forward Kinematics | 역운동학 / Inverse Kinematics |
|---|---|---|
| **입력 / Input** | 관절 각도 / Joint angles | 그리퍼 위치·방향 / Gripper pose |
| **출력 / Output** | 그리퍼 위치·방향 / Gripper pose | 관절 각도 / Joint angles |
| **해의 수 / Solutions** | 유일 / Unique | 복수·없음·무한 / Multiple, none, or infinite |
| **복잡도 / Complexity** | 낮음 / Low | 높음 / High |
| **풀이 방식 / Method** | 해석적 / Analytical | 수치적 / Numerical |
| **사용 도구 / Tool** | TF2 + URDF | **MoveIt2** |

---

## 3. 역운동학이 필요한 이유 / Why Inverse Kinematics

> **한국어**
> 슬라이더로 관절을 하나씩 조작해 그리퍼를 원하는 위치로 이동시키는 것은 매우 불편합니다.
> 사용자는 "그리퍼를 여기로 이동시켜라"고 직접 명령하고 싶지만,
> 로봇 모터는 관절 각도 값만 이해합니다.
> 역운동학은 이 두 언어 사이를 변환해주는 수학적 도구입니다.

> **English**
> Controlling each joint separately with sliders to reach a desired gripper position is very inconvenient.
> Users want to command "move the gripper here" directly,
> but robot motors only understand joint angle values.
> Inverse kinematics is the mathematical tool that bridges these two.

### 제어 흐름 / Control Flow

```
사용자 / User
    │
    │  "그리퍼를 이 위치로 이동"
    │  "Move gripper to this position"
    │
    ▼
[ 역운동학 / Inverse Kinematics ]
    │
    │  위치 → 관절 각도 변환
    │  Pose → Joint angles conversion
    │
    ▼
로봇 모터 / Robot Motors
    │
    │  각 모터에 각도 명령 전달
    │  Send angle commands to each motor
    │
    ▼
그리퍼 이동 / Gripper moves to target
```

---

## 4. 역운동학의 어려움 / Challenges

> **한국어**
> 순운동학은 항상 유일한 해를 가지지만, 역운동학은 경우에 따라 해가 여러 개이거나, 없거나, 무한히 많을 수 있습니다.
> 같은 그리퍼 위치에 도달하는 관절 조합이 여러 개 존재할 수 있기 때문입니다.

> **English**
> While forward kinematics always has a unique solution, inverse kinematics may have multiple solutions, no solution, or infinitely many.
> This is because multiple joint configurations can result in the same gripper position.

### 해의 경우의 수 / Possible Solution Cases

```
목표 그리퍼 위치 / Target gripper position P
    │
    ├── 관절 조합 A / Joint config A  ✅
    ├── 관절 조합 B / Joint config B  ✅
    ├── 관절 조합 C / Joint config C  ✅
    ├── 관절 조합 D / Joint config D  ✅
    │
    ├── 해 없음 / No solution         ❌  (도달 불가 위치 / Unreachable position)
    └── 무한한 해 / Infinite solutions ♾️
```

### 풀이 방식 / Solution Approach

> **한국어**
> 이런 복잡성 때문에 역운동학은 공식으로 푸는 해석적 방법 대신,
> 최적해를 반복적으로 탐색하는 **수치적 알고리즘(최적화 알고리즘)** 을 주로 사용합니다.

> **English**
> Due to this complexity, inverse kinematics typically relies on **numerical optimization algorithms**
> that iteratively search for the best solution, rather than analytical closed-form solutions.

---

## 5. 해결책 MoveIt2 / Solution MoveIt2

> **한국어**
> ROS2 오픈소스 커뮤니티가 제공하는 **MoveIt2** 라이브러리는
> 역운동학 계산뿐만 아니라 궤적 계획과 장애물 회피까지 자동으로 처리합니다.

> **English**
> **MoveIt2**, provided by the ROS2 open-source community,
> automatically handles inverse kinematics, trajectory planning, and obstacle avoidance.

### MoveIt2 주요 기능 / Key Features

| 기능 / Feature | 설명 / Description |
|---|---|
| **역운동학 계산 / IK Solver** | 어떤 구조의 로봇에도 적용 가능 / Works with any robot architecture |
| **궤적 계획 / Trajectory Planning** | 시작 위치 → 목표 위치까지 각 관절의 경로 자동 계산 / Auto-computes joint path from start to goal |
| **장애물 회피 / Obstacle Avoidance** | 이동 중 충돌 자동 감지 및 회피 / Detects and avoids collisions during motion |

### MoveIt2 동작 흐름 / MoveIt2 Workflow

```
① 로봇 URDF 모델 제공 / Provide robot URDF model
② 추가 설정 정보 입력 / Provide additional configuration
        ↓
MoveIt2 자동 처리 / MoveIt2 auto-handles:
    ├── 역운동학 풀이 / Inverse kinematics solving
    ├── 관절 궤적 계획 / Joint trajectory planning
    └── 장애물 회피 / Obstacle avoidance
        ↓
③ 그리퍼를 3D 공간에서 직접 제어 / Control gripper directly in 3D space
```

### MoveIt2 사용 준비 / Prerequisites

```bash
# MoveIt2 설치 / Install MoveIt2
sudo apt install ros-humble-moveit
```

> 📌 **다음 단계 / Next Step**
> 우리 로봇의 URDF 모델을 기반으로 MoveIt2를 설정하고,
> 그리퍼를 3D 공간에서 직접 움직이며 관절 각도를 자동 계산하는 실습을 진행합니다.
>
> Configure MoveIt2 for our robot's URDF model,
> then directly control the gripper in 3D space with automatic joint angle computation.
