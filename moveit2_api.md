# MoveIt2 API

---

## 📌 목차 / Table of Contents

1. [지금까지 준비된 도구 / Tools Ready So Far](#1-지금까지-준비된-도구--tools-ready-so-far)
2. [MoveIt2 API란? / What is MoveIt2 API?](#2-moveit2-api란--what-is-moveit2-api)
3. [API 활용 가능성 / API Applications](#3-api-활용-가능성--api-applications)
4. [전체 소프트웨어 스택 / Full Software Stack](#4-전체-소프트웨어-스택--full-software-stack)
5. [다음 단계 / Next Steps](#5-다음-단계--next-steps)

---

## 1. 지금까지 준비된 도구 / Tools Ready So Far

> **한국어**
> 이 시점에서 두 가지 강력한 도구가 준비되었습니다.
> 이제 두 도구를 결합하여 실제 애플리케이션을 개발할 차례입니다.

> **English**
> At this point, two powerful tools are at our disposal.
> It is now time to combine them to develop real applications.

| 도구 / Tool | 상태 / Status | 내용 / Details |
|---|---|---|
| **MoveIt2** | ✅ 준비 완료 / Ready | 로봇에 설정 및 실행 완료 / Configured and launched for our robot |
| **ROS2 Actions** | ✅ 학습 완료 / Learned | Goal → Feedback → Result 통신 프로토콜 / Communication protocol |

---

## 2. MoveIt2 API란? / What is MoveIt2 API?

> **한국어**
> API(Application Programming Interface)는 MoveIt2가 다른 소프트웨어에 제공하는 인터페이스입니다.
> RViz2 GUI가 사람에게 MoveIt2 기능을 제공하듯,
> API는 다른 소프트웨어가 코드로 MoveIt2의 모든 기능에 접근할 수 있게 해줍니다.

> **English**
> An API (Application Programming Interface) is the interface MoveIt2 provides to other software.
> Just as the RViz2 GUI gives humans access to MoveIt2 features,
> the API allows other software to access all MoveIt2 functionalities through code.

### GUI vs API 비교 / Comparison

| 항목 / Item | GUI (RViz2) | API |
|---|---|---|
| **사용 대상 / Target** | 사람 / Human | 다른 소프트웨어 / Other software |
| **접근 방식 / Access** | 드래그 & 클릭 / Drag & click | Python / C++ 코드 / Code |
| **사용 목적 / Purpose** | 수동 조작 / Manual control | 자동화된 모션 제어 / Automated motion control |
| **유연성 / Flexibility** | 제한적 / Limited | 무한한 가능성 / Unlimited possibilities |

---

## 3. API 활용 가능성 / API Applications

> **한국어**
> MoveIt2 API를 통해 Python / C++ 노드에서 어떤 모션 제어 로직이든 구현할 수 있습니다.
> 로봇의 한 포즈에서 다른 포즈로의 모든 움직임을 코드로 제어할 수 있습니다.

> **English**
> Through the MoveIt2 API, any motion control logic can be implemented in Python or C++ nodes.
> Any movement of the robot from one pose to another can be controlled through code.

| 애플리케이션 / Application | 설명 / Description |
|---|---|
| 🤏 **픽앤플레이스 / Pick & Place** | 물체를 집어서 다른 위치로 이동 / Pick up an object and move it to another position |
| 👤 **사람 감지 연동 / Person Detection** | 주변 사람의 위치에 따라 그리퍼 이동 / Move gripper based on detected person's position |
| 🎙️ **음성 명령 연동 / Voice Commands** | 음성 명령 → 로봇 모션 실행 / Voice command → robot motion execution |
| 🔄 **커스텀 모션 / Custom Motion** | 어떤 아이디어든 코드로 구현 가능 / Any motion idea can be implemented in code |

---

## 4. 전체 소프트웨어 스택 / Full Software Stack

> **한국어**
> MoveIt2 API를 진입점으로 삼아 지금까지 개발한 모든 소프트웨어 스택이 연결됩니다.

> **English**
> Using the MoveIt2 API as an entry point, all software developed so far connects into one stack.

```
새로운 애플리케이션 / New Application
(Python / C++ 노드 / Node)
        │
        │ MoveIt2 API 호출 / Call MoveIt2 API
        ↓
    [ MoveIt2 ]
    IK 계산 + 궤적 계획
    IK Solver + Trajectory Planning
        │
        ↓
  [ ROS2 Control ]
  모터 명령 전달 / Motor command delivery
  arm_controller + gripper_controller
        │
        ↓
  실제 로봇 / Real Robot
  또는 Gazebo 시뮬레이션 / or Gazebo Simulation
```

---

## 5. 다음 단계 / Next Steps

> **한국어**
> 앞으로의 실습은 두 단계로 진행됩니다.

> **English**
> The upcoming practice sessions proceed in two steps.

### Step 1 — MoveIt2 API 샘플 코드 / Sample Code

> **한국어:** MoveIt2 API 사용법을 보여주는 샘플 코드를 C++과 Python으로 구현합니다.
> **English:** Implement sample code demonstrating how to use the MoveIt2 API in C++ and Python.

```
MoveIt2 API 샘플 코드
        │
        ├── C++ 버전 / C++ version
        └── Python 버전 / Python version
```

### Step 2 — 픽앤플레이스 액션 서버 / Pick & Place Action Server

> **한국어:** 샘플 코드를 기반으로 다양한 Goal을 수신하면 각각의 픽앤플레이스 동작을 자동으로 실행하는 액션 서버를 개발합니다.
> **English:** Using the sample code, develop an action server that automatically executes various pick and place movements upon receiving different goals.

```
액션 클라이언트 / Action Client
(음성 명령 등 / Voice command etc.)
        │
        │ Goal 전송 / Send goal
        ↓
픽앤플레이스 액션 서버 / Pick & Place Action Server
        │
        ├── Goal 수신 → 모션 계획 / Receive goal → Plan motion
        ├── Feedback 전송 (진행 상황) / Send feedback (progress)
        └── Result 반환 (성공/실패) / Return result (success/failure)
                │
                ↓
          MoveIt2 API
                │
                ↓
          로봇 동작 실행 / Robot executes motion
```

---

## 📋 핵심 요약 / Key Summary

| 개념 / Concept | 내용 / Details |
|---|---|
| **MoveIt2 API** | 코드로 MoveIt2 기능에 접근하는 인터페이스 / Interface to access MoveIt2 features via code |
| **지원 언어 / Languages** | Python, C++ |
| **주요 기능 / Features** | 궤적 계획, IK 계산, 모션 실행 / Trajectory planning, IK solving, motion execution |
| **활용 방식 / Usage** | ROS2 Action Server와 결합하여 자동화 / Combined with ROS2 Action Server for automation |
