# 애플리케이션 레이어 / Application Layer

---

## 📌 목차 / Table of Contents

1. [지금까지의 여정 / Course Journey So Far](#1-지금까지의-여정--course-journey-so-far)
2. [MoveIt2 도입 이유 / Why MoveIt2](#2-moveit2-도입-이유--why-moveit2)
3. [MoveIt2 인터페이스 / MoveIt2 Interfaces](#3-moveit2-인터페이스--moveit2-interfaces)
4. [API 활용 가능성 / API Applications](#4-api-활용-가능성--api-applications)
5. [이번 강좌 목표 / Course Goal](#5-이번-강좌-목표--course-goal)

---

## 1. 지금까지의 여정 / Course Journey So Far

> **한국어**
> 지금까지 여러 복잡한 주제들을 다루며 로봇 소프트웨어 인프라를 단계적으로 구축해왔습니다.
> 각 단계가 쌓여 하나의 완성된 로봇 개발 플랫폼이 되었습니다.

> **English**
> We have progressively built a complete robot software infrastructure by covering several complex topics.
> Each layer stacks upon the previous one to form a complete robotics development platform.

| 단계 / Step | 구현 내용 / Implementation | 목적 / Purpose |
|---|---|---|
| **1단계 / Step 1** | Gazebo 시뮬레이터 / Gazebo Simulator | 실제 로봇 동작을 PC에서 재현 / Replicate real robot behavior on PC |
| **2단계 / Step 2** | ROS2 Control | 궤적 수신 → 모터 위치 명령 전달 / Receive trajectories → send motor commands |
| **3단계 / Step 3** | MoveIt2 연동 / MoveIt2 Integration | 카르테시안 좌표로 직접 그리퍼 제어 / Control gripper directly in Cartesian space |

### ROS2 Control의 모듈성 / Modularity of ROS2 Control

> **한국어**
> ROS2 Control의 모듈성 덕분에 시뮬레이션용 제어 시스템을 실제 로봇에도 그대로 사용할 수 있습니다.
> 하드웨어 통신 인터페이스만 변경하면 됩니다.

> **English**
> Thanks to the modularity of ROS2 Control, the same control system developed for simulation
> can be used directly on the real robot — only the hardware communication interface needs to change.

```
시뮬레이션 / Simulation ──┐
                           ├── 동일한 ROS2 Control / Same ROS2 Control
실제 로봇 / Real Robot ───┘   (인터페이스만 교체 / Only interface changes)
```

---

## 2. MoveIt2 도입 이유 / Why MoveIt2

> **한국어**
> 관절을 하나씩 조작하는 방식으로는 가장 단순한 픽앤플레이스 작업도 수행하기 어렵습니다.
> 하나의 관절 각도를 바꿀 때 그리퍼가 어디로 움직일지 예측하기 어렵기 때문입니다.
> MoveIt2를 사용하면 카르테시안 좌표로 직접 목표 위치를 지정할 수 있고,
> 각 관절의 각도 계산은 IK 솔버가 자동으로 처리합니다.

> **English**
> Controlling each joint separately makes even simple pick-and-place operations very difficult.
> It is hard to predict where the gripper moves in 3D space when changing a single joint angle.
> With MoveIt2, we can specify the target position directly in Cartesian terms,
> and the IK solver automatically handles all joint angle calculations.

```
[기존 방식 / Old method]
관절 각도 직접 조작           →   그리퍼 위치 예측 어려움
Manually adjust joint angles  →   Hard to predict gripper position

[MoveIt2 방식 / MoveIt2 method]
카르테시안 목표 위치 지정           →   IK 솔버가 관절 각도 자동 계산
Specify Cartesian target position  →   IK solver auto-computes joint angles
```

---

## 3. MoveIt2 인터페이스 / MoveIt2 Interfaces

> **한국어**
> MoveIt2는 두 가지 인터페이스를 제공합니다.
> 하나는 사람을 위한 GUI, 다른 하나는 소프트웨어를 위한 API입니다.

> **English**
> MoveIt2 provides two types of interfaces:
> one for humans (GUI), and one for other software (API).

### ① GUI — 그래픽 인터페이스 / Graphical Interface (RViz2)

| 항목 / Item | 내용 / Details |
|---|---|
| **대상 / Target** | 사람 / Human operators |
| **도구 / Tool** | RViz2 |
| **기능 / Features** | 궤적 계획, 장애물 회피, 목표 위치 설정 / Trajectory planning, obstacle avoidance, goal setting |
| **조작 방식 / Interaction** | 드래그 & 클릭 / Drag & click |

### ② API — 애플리케이션 프로그래밍 인터페이스 / Application Programming Interface

> **한국어**
> API(Application Programming Interface)는 MoveIt2가 다른 소프트웨어에 제공하는 인터페이스입니다.
> GUI가 사람을 위한 인터페이스라면, API는 소프트웨어를 위한 인터페이스입니다.
> Python 및 C++로 MoveIt2의 모든 기능을 코드에서 직접 활용할 수 있습니다.

> **English**
> The API (Application Programming Interface) is the interface MoveIt2 provides to other software.
> Just as the GUI is for humans, the API is for programs.
> All MoveIt2 features can be accessed directly from code using Python or C++.

```
GUI  →  사람 (Human)       →  RViz2 드래그 & 클릭 / Drag & click
API  →  소프트웨어 (Code)   →  함수 호출 / Function calls (Python / C++)
```

---

## 4. API 활용 가능성 / API Applications

> **한국어**
> MoveIt2 API를 통해 구현할 수 있는 애플리케이션은 무궁무진합니다.
> 한계는 오직 상상력과 창의력뿐입니다.

> **English**
> The applications that can be built using the MoveIt2 API are endless.
> The only limit is your imagination and creativity.

| 애플리케이션 / Application | 설명 / Description |
|---|---|
| 🧠 **AI 물체 인식 / AI Object Recognition** | 신경망으로 파지할 물체 감지 → 자동 이동 / Neural network detects objects → auto grasp |
| 🤖 **AI 로봇 제어 / AI Robot Control** | 인공지능이 사람 대신 로봇 조작 / AI replaces humans in robot operation |
| 🌐 **웹 애플리케이션 / Web Application** | 원격으로 로봇 접속 및 제어 / Remotely access and control the robot |
| 🎙️ **음성 인터페이스 / Voice Interface** | 음성 명령 → 로봇 동작 / Voice commands → robot actions (이번 강좌 / This course) |

---

## 5. 이번 강좌 목표 / Course Goal

> **한국어**
> MoveIt2 API를 활용해 **Amazon Alexa** 음성 어시스턴트와 로봇을 연동합니다.
> 사용자의 음성 명령을 Alexa가 해석하고, 이를 로봇 동작으로 변환합니다.

> **English**
> We will use the MoveIt2 API to integrate the robot with the **Amazon Alexa** voice assistant.
> Alexa interprets voice commands and translates them into robot actions.

### 동작 흐름 / Operation Flow

```
사용자 음성 명령 / User voice command
        │
        ▼
Amazon Alexa
(음성 인식 & 해석 / Voice recognition & interpretation)
        │
        ▼
MoveIt2 API
(명령 → 궤적 계획 / Command → trajectory planning)
        │
        ▼
ROS2 Control
(모터 명령 전달 / Motor command delivery)
        │
        ▼
로봇 동작 실행 / Robot executes action
```

---

## 📋 전체 소프트웨어 스택 요약 / Full Software Stack Summary

```
[ 애플리케이션 레이어 / Application Layer ]
  Amazon Alexa API  /  Web App  /  AI Vision  /  Custom App
                          │
                          ▼
[ MoveIt2 ]
  IK Solver  +  Trajectory Planner  +  Collision Avoidance
                          │
                          ▼
[ ROS2 Control ]
  arm_controller  +  gripper_controller
                          │
                          ▼
[ 시뮬레이션 / Real Robot ]
  Gazebo  /  실제 하드웨어 / Physical Hardware
```

> 📌 **핵심 / Key Point**
> 각 레이어는 독립적으로 교체 가능합니다. MoveIt2 API 하나로 어떤 애플리케이션이든 로봇과 연결할 수 있습니다.
> Each layer can be replaced independently. Any application can connect to the robot through the MoveIt2 API.
