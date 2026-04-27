# MoveIt2 개요 / MoveIt2 Overview

---

## 📌 목차 / Table of Contents

1. [강좌 범위 / Course Scope](#1-강좌-범위--course-scope)
2. [Move Group — 핵심 미들웨어 / Core Middleware](#2-move-group--핵심-미들웨어--core-middleware)
3. [주요 모듈 / Key Modules](#3-주요-모듈--key-modules)
4. [사용자 인터페이스 / User Interfaces](#4-사용자-인터페이스--user-interfaces)
5. [전체 동작 흐름 / Overall Workflow](#5-전체-동작-흐름--overall-workflow)

---

## 1. 강좌 범위 / Course Scope

> **한국어**
> MoveIt2는 매우 다양한 기능을 제공하는 소프트웨어입니다.
> 이 강좌에서는 그 중 아래 두 가지 기능에 집중합니다.

> **English**
> MoveIt2 is a feature-rich software with numerous capabilities.
> This course focuses on the following two functionalities.

| 사용할 기능 / Features Used | 설명 / Description |
|---|---|
| **역운동학 계산 / Inverse Kinematics** | 목표 위치로부터 관절 각도 자동 계산 / Auto-compute joint angles from target pose |
| **궤적 계획 / Trajectory Planning** | 시작 → 목표까지 충돌 없는 경로 계획 / Plan collision-free path from start to goal |

---

## 2. Move Group — 핵심 미들웨어 / Core Middleware

> **한국어**
> MoveIt2의 핵심에는 **Move Group** 이 있습니다.
> 모든 모듈 간의 통신과 리소스 교환을 중재하는 미들웨어 역할을 합니다.

> **English**
> At the core of MoveIt2 lies the **Move Group**.
> It acts as a middleware that facilitates communication and resource exchange among all other modules.

### 구조도 / Architecture

```
                      [ Move Group ]
                            │
         ┌──────────────────┼──────────────────┐
         ▼                  ▼                  ▼
  궤적 실행 모듈       플래닝 씬 관리      역운동학 솔버
  Trajectory           Planning Scene      IK Solver
  Execution            Manager
         │                  │
         ▼                  ▼
  ROS2 Control        센서 / Sensors
  (모터 컨트롤러)      (카메라, 라이다)
```

---

## 3. 주요 모듈 / Key Modules

### ① 궤적 실행 / Trajectory Execution

> **한국어**
> 로봇의 이동 로직과 궤적 계획을 담당하며,
> **ROS2 Control** 라이브러리를 통해 모터 컨트롤러와 직접 연결됩니다.

> **English**
> Handles the robot's movement logic and trajectory planning,
> interfacing directly with motor controllers via the **ROS2 Control** library.

- 각 관절이 **미리 정해진 궤적**을 따르도록 제어 / Controls each joint to follow a predefined trajectory
- 특정 시간에 각 관절이 **지정된 위치**에 오도록 보장 / Ensures each joint reaches the specified position at the right time

---

### ② 플래닝 씬 관리 / Planning Scene Manager

> **한국어**
> 로봇 주변의 환경과 장애물 정보를 관리합니다.
> 충돌 없는 경로 계획을 위해 반드시 필요한 모듈입니다.

> **English**
> Manages the robot's surrounding environment and obstacle information.
> Essential for ensuring collision-free trajectory planning.

- 3D 카메라, 라이다 등 **센서로 장애물 감지** / Detects obstacles using 3D cameras, LiDAR, etc.
- 감지된 정보로 **플래닝 씬 생성 및 실시간 업데이트** / Creates and updates the planning scene in real time
- 궤적 계획 시 **충돌 회피** 정보로 활용 / Used for collision avoidance during trajectory planning

---

### ③ 역운동학 솔버 / IK Solver

> **한국어**
> 궤적 계획의 핵심 모듈로, 목표 위치에 도달하기 위한 관절 각도를 계산합니다.

> **English**
> The core module for trajectory planning that computes joint angles needed to reach a target pose.

| 솔버 종류 / Solver Type | 설명 / Description | 사용 시점 / When Used |
|---|---|---|
| **범용 솔버 / General-purpose** | MoveIt2 기본 제공 / Built-in | 이 강좌 / This course |
| **커스텀 솔버 / Custom** | 특정 로봇에 최적화 / Robot-specific | 산업 현장, 고속 계산 / Industrial use |

---

## 4. 사용자 인터페이스 / User Interfaces

> **한국어**
> MoveIt2는 다양한 방식으로 접근할 수 있어 어떤 ROS2 노드에서도 사용 가능합니다.

> **English**
> MoveIt2 offers multiple interfaces, making it accessible from any ROS2 node or application.

| 인터페이스 / Interface | 설명 / Description | 강좌 사용 / Used in Course |
|---|---|---|
| **RViz2 GUI** | 그래픽으로 목표 설정 및 궤적 실행 / Graphical goal setting & execution | ✅ |
| **Python API** | 모든 ROS2 노드에서 코드로 제어 / Code-based control from any node | ✅ |
| **C++ API** | 고성능 제어 / High-performance control | — |
| **CLI** | 커맨드라인 직접 제어 / Direct terminal control | — |

---

## 5. 전체 동작 흐름 / Overall Workflow

```
센서 (카메라 / 라이다) / Sensors
        │
        │ 장애물 감지 / Obstacle detection
        ▼
플래닝 씬 업데이트 / Planning scene update
        │
        ▼
목표 위치 입력 / Target pose input
(RViz2 GUI 또는 Python API / via RViz2 or Python API)
        │
        ▼
      [ Move Group ]
        │
        ├──→ IK Solver
        │      └── 관절 각도 계산 / Compute joint angles
        │
        └──→ Trajectory Planner
               └── 충돌 없는 경로 계획 / Plan collision-free path
                        │
                        ▼
              ROS2 Control
                        │
                        ▼
              모터 컨트롤러 / Motor Controllers
                        │
                        ▼
              로봇 실제 이동 / Robot moves to target
```

---

## 📋 핵심 요약 / Key Summary

| 개념 / Concept | 내용 / Details |
|---|---|
| **Move Group** | 모든 모듈 간 통신을 중재하는 핵심 미들웨어 / Core middleware for all module communication |
| **Trajectory Execution** | ROS2 Control 통해 모터 직접 제어 / Direct motor control via ROS2 Control |
| **Planning Scene** | 센서 기반 장애물 정보 관리 / Sensor-based obstacle management |
| **IK Solver** | 목표 위치 → 관절 각도 자동 계산 / Target pose → joint angles |
| **인터페이스** | RViz2 GUI + Python/C++ API + CLI / RViz2 GUI + Python/C++ API + CLI |
