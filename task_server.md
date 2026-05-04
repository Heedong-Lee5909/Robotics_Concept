# 태스크 서버 / Task Server

---

## 📌 목차 / Table of Contents

1. [개요 / Overview](#1-개요--overview)
2. [태스크 서버란? / What is a Task Server?](#2-태스크-서버란--what-is-a-task-server)
3. [세 가지 태스크 정의 / Three Task Definitions](#3-세-가지-태스크-정의--three-task-definitions)
4. [Amazon Alexa 연동 계획 / Amazon Alexa Integration Plan](#4-amazon-alexa-연동-계획--amazon-alexa-integration-plan)
5. [전체 시스템 구조 / Full System Architecture](#5-전체-시스템-구조--full-system-architecture)

---

## 1. 개요 / Overview

> **한국어**
> MoveIt2 API 활용 능력을 바탕으로 여러 가지 사전 정의된 로봇 동작을 수행하는
> 태스크 서버(액션 서버)를 구현합니다.
> 이 태스크 서버는 이후 Amazon Alexa 음성 어시스턴트와 연동되어
> 음성 명령으로 로봇을 제어하는 시스템의 핵심이 됩니다.

> **English**
> Using the MoveIt2 API skills we have developed, we implement a Task Server (Action Server)
> that performs several predefined robot movements.
> This task server will later be integrated with the Amazon Alexa voice assistant,
> becoming the core of a voice-controlled robot system.

### 사용 기술 스택 / Technology Stack

| 기술 / Technology | 역할 / Role |
|---|---|
| **ROS2 Action Server** | Goal 수신 및 결과 반환 / Receive goal & return result |
| **MoveIt2 API** | 궤적 계획 및 실행 / Trajectory planning & execution |
| **Amazon Alexa** | 음성 명령 → Goal 전송 / Voice command → Send goal |

---

## 2. 태스크 서버란? / What is a Task Server?

> **한국어**
> 태스크 서버는 클라이언트로부터 Goal(태스크 ID)을 수신하면
> 해당 ID에 맞는 사전 정의된 로봇 동작을 MoveIt2 API를 통해 실행하는 액션 서버입니다.

> **English**
> The Task Server is an Action Server that receives a Goal (Task ID) from a client
> and executes the corresponding predefined robot motion using the MoveIt2 API.

### 동작 흐름 / Operation Flow

```
클라이언트 / Client (Amazon Alexa 등 / etc.)
        │
        │ Goal (태스크 ID / Task ID: 0, 1, 2)
        ↓
  [ 태스크 서버 / Task Server ]
  (ROS2 Action Server + MoveIt2 API)
        │
        ├── ID 0 → 홈 포지션 / Home Position
        ├── ID 1 → 픽 포지션 / Pick Position
        └── ID 2 → 레스트 포지션 / Rest Position
        │
        ↓
  MoveIt2 API
  (궤적 계획 + 실행 / Plan + Execute)
        │
        ↓
  로봇 동작 실행 / Robot executes motion
```

---

## 3. 세 가지 태스크 정의 / Three Task Definitions

> **한국어**
> 태스크 서버는 세 가지 사전 정의된 동작을 수행합니다.
> 각 동작은 고유한 ID로 식별되며, 클라이언트가 해당 ID를 Goal로 전송합니다.

> **English**
> The Task Server performs three predefined motions.
> Each motion is identified by a unique ID sent as the goal by the client.

| 태스크 ID / Task ID | 이름 / Name | 동작 내용 / Motion Details |
|---|---|---|
| **0** | 홈 포지션 / Home Position | 기본 위치로 이동 + 그리퍼 열림 / Move to default position + open gripper |
| **1** | 픽 포지션 / Pick Position | 파지 위치로 이동 + 그리퍼 닫기 / Move to pick position + close gripper |
| **2** | 레스트 포지션 / Rest Position | 휴식 위치로 이동 / Move to rest position |

### 각 태스크 상세 / Task Details

#### Task 0 — 홈 포지션 / Home Position

> **한국어:** 로봇을 기본(홈) 위치로 이동시키고 그리퍼를 엽니다.
> **English:** Moves the robot to the default home position and opens the gripper.

```
동작 / Motion:
  robot arm   → 홈 포지션 (초기 위치) / Home position (initial)
  gripper     → 열림 / Open
```

#### Task 1 — 픽 포지션 / Pick Position

> **한국어:** 로봇을 사전 정의된 파지 위치로 이동시키고 그리퍼를 닫아 물체를 파지합니다.
> **English:** Moves the robot to a predefined pick position and closes the gripper to grasp an object.

```
동작 / Motion:
  robot arm   → 픽 포지션 (파지 위치) / Pick position (grasp location)
  gripper     → 닫힘 (물체 파지) / Close (grasp object)
```

#### Task 2 — 레스트 포지션 / Rest Position

> **한국어:** 로봇을 사전 정의된 휴식 위치로 이동시킵니다.
> **English:** Moves the robot to a predefined rest position.

```
동작 / Motion:
  robot arm   → 레스트 포지션 (휴식 위치) / Rest position
  gripper     → 현재 상태 유지 / Maintain current state
```

---

## 4. Amazon Alexa 연동 계획 / Amazon Alexa Integration Plan

> **한국어**
> 다음 섹션에서 Amazon Alexa 음성 어시스턴트를 태스크 서버의 클라이언트로 연동합니다.
> 사용자의 음성 명령을 Alexa가 해석하여 해당 태스크 ID를 태스크 서버에 Goal로 전송합니다.

> **English**
> In the next section, we integrate the Amazon Alexa voice assistant as the task server's client.
> Alexa interprets the user's voice command and sends the corresponding task ID as a goal to the task server.

```
사용자 / User
    │ "로봇 홈으로 / Robot go home"
    ▼
Amazon Alexa
(음성 인식 & 해석 / Voice recognition & interpretation)
    │ Task ID = 0
    ▼
태스크 서버 / Task Server
(ROS2 Action Server)
    │ Goal 수락 / Accept goal
    ▼
MoveIt2 API
(궤적 계획 / Plan trajectory)
    │
    ▼
ROS2 Control
(모터 명령 / Motor commands)
    │
    ▼
로봇 홈 포지션 이동 / Robot moves to home position
```

---

## 5. 전체 시스템 구조 / Full System Architecture

```
[ 사용자 / User ]
  음성 명령 / Voice command
        │
        ▼
[ Amazon Alexa ]
  음성 인식 & 태스크 ID 결정
  Voice recognition & task ID decision
        │ Goal (ID: 0 / 1 / 2)
        ▼
[ 태스크 서버 / Task Server ]
  ROS2 Action Server
        │
        ├── Task 0: 홈 포지션 / Home
        ├── Task 1: 픽 포지션 / Pick
        └── Task 2: 레스트 포지션 / Rest
        │
        ▼
[ MoveIt2 API ]
  IK 계산 + 궤적 계획
  IK solving + trajectory planning
        │
        ▼
[ ROS2 Control ]
  arm_controller + gripper_controller
        │
        ▼
[ 로봇 / Robot ]
  Gazebo 시뮬레이션 / Real Robot
```

---

## 📋 핵심 요약 / Key Summary

| 개념 / Concept | 내용 / Details |
|---|---|
| **태스크 서버 / Task Server** | MoveIt2 API + ROS2 Action Server 결합 / Combination of both |
| **태스크 식별 / Task ID** | Goal 메시지의 ID로 동작 구분 / Motion identified by goal ID |
| **Task 0** | 홈 포지션 + 그리퍼 열림 / Home + open gripper |
| **Task 1** | 픽 포지션 + 그리퍼 닫기 / Pick + close gripper |
| **Task 2** | 레스트 포지션 / Rest position |
| **다음 단계 / Next Step** | Amazon Alexa를 클라이언트로 연동 / Integrate Alexa as client |
