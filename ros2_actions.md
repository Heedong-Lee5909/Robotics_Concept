# ROS2 액션 / ROS2 Actions

---

## 📌 목차 / Table of Contents

1. [세 가지 통신 프로토콜 비교 / Three Communication Protocols](#1-세-가지-통신-프로토콜-비교--three-communication-protocols)
2. [액션이 필요한 이유 / Why Actions](#2-액션이-필요한-이유--why-actions)
3. [액션 통신 구조 / Action Communication Structure](#3-액션-통신-구조--action-communication-structure)
4. [네 가지 메시지 타입 / Four Message Types](#4-네-가지-메시지-타입--four-message-types)
5. [Cancel 메시지 / Cancel Message](#5-cancel-메시지--cancel-message)
6. [액션 vs 서비스 선택 기준 / Action vs Service](#6-액션-vs-서비스-선택-기준--action-vs-service)

---

## 1. 세 가지 통신 프로토콜 비교 / Three Communication Protocols

> **한국어**
> ROS2는 세 가지 노드 간 통신 프로토콜을 제공합니다.
> 액션은 그 중 시간이 오래 걸리는 작업에 특화된 프로토콜입니다.

> **English**
> ROS2 provides three communication protocols between nodes.
> Actions are specifically designed for long-running tasks.

| 프로토콜 / Protocol | 방식 / Pattern | 사용 상황 / Use Case |
|---|---|---|
| **Topic** | Publisher → Subscriber | 지속적인 데이터 스트림 / Continuous data stream |
| **Service** | Request → Response | 빠른 단발성 요청 / Quick one-time request |
| **Action** | Goal → Feedback → Result | 시간이 오래 걸리는 작업 / Long-running tasks |

---

## 2. 액션이 필요한 이유 / Why Actions

> **한국어**
> 로봇이 목표 위치까지 이동하는 것처럼 시간이 오래 걸리는 작업에서는
> 서비스보다 액션이 더 적합합니다.
> 클라이언트가 실행 중에도 다른 작업을 계속할 수 있고,
> 진행 상황을 실시간으로 받을 수 있으며, 중간에 취소도 가능하기 때문입니다.

> **English**
> For long-running tasks like moving a robot to a target position,
> actions are more suitable than services.
> The client can continue other work during execution,
> receive real-time feedback on progress, and cancel mid-execution if needed.

### 실제 예시 / Practical Example: 펜 감지 & 파지 / Pen Detection & Grasping

```
[pen_detector 노드 / Node]          [grab_pen 노드 / Node]
(액션 클라이언트 / Action Client)    (액션 서버 / Action Server)

카메라로 펜 위치 감지          →     로봇을 해당 위치로 이동 & 파지
Detects pen position via camera →   Moves robot to position & grasps pen
```

### 서비스 대신 액션을 사용하는 이유 / Why Action over Service

| 이유 / Reason | 설명 / Description |
|---|---|
| ⏱️ **긴 실행 시간 / Long execution** | 로봇 이동은 시간이 걸림 / Robot movement takes time |
| 🔄 **논블로킹 / Non-blocking** | 클라이언트가 다른 작업 계속 가능 / Client can continue other work |
| 📡 **실시간 피드백 / Real-time feedback** | 진행 상황을 주기적으로 수신 / Receive progress updates periodically |
| ❌ **취소 가능 / Cancellable** | 실행 중 취소 요청 가능 / Can cancel during execution |

---

## 3. 액션 통신 구조 / Action Communication Structure

> **한국어**
> 액션 통신은 목표(Goal), 피드백(Feedback), 결과(Result), 취소(Cancel)
> 네 가지 메시지로 이루어집니다.

> **English**
> Action communication consists of four message types:
> Goal, Feedback, Result, and Cancel.

```
액션 클라이언트 / Action Client        액션 서버 / Action Server
        │                                       │
        │──── Goal (목표 전송 / Send goal) ────→ │
        │                                       │ 궤적 계획 & 실행 시작
        │                                       │ Trajectory planning & execution
        │←─── Feedback (진행 상황) ────────────  │ (주기적 / Periodic)
        │←─── Feedback (진행 상황) ────────────  │
        │                                       │
        │  (다른 작업 계속 가능 / Non-blocking)  │
        │                                       │
        │←─── Result (완료 / 실패) ─────────────  │
        │     (Completed / Failed)               │
```

---

## 4. 네 가지 메시지 타입 / Four Message Types

| 메시지 / Message | 방향 / Direction | 내용 / Content |
|---|---|---|
| **Goal** | 클라이언트 → 서버 / Client → Server | 수행할 목표 전달 / Desired target to achieve |
| **Feedback** | 서버 → 클라이언트 / Server → Client | 실행 중 진행 상황 주기적 전달 / Periodic progress updates |
| **Result** | 서버 → 클라이언트 / Server → Client | 완료 또는 실패 최종 결과 / Final outcome (success or failure) |
| **Cancel** | 클라이언트 → 서버 / Client → Server | 실행 중인 목표 취소 요청 / Request to cancel ongoing goal |

> ⚠️ **한국어:** 취소가 되든 완료가 되든 **항상 Result 메시지가 반환**됩니다.
> ⚠️ **English:** A **Result message is always sent** regardless of whether the action completed or was cancelled.

---

## 5. Cancel 메시지 / Cancel Message

> **한국어**
> 클라이언트는 서버가 실행 중일 때 취소 메시지를 보낼 수 있습니다.
> 서버는 이를 수신하면 안전하게 작업을 중단하고 결과를 반환합니다.

> **English**
> The client can send a cancel message while the server is executing.
> The server safely stops the operation and returns a result upon receiving it.

### 취소 시나리오 / Cancellation Scenario

```
1. pen_detector가 펜 위치 감지
   pen_detector detects pen position
        ↓
2. Goal 전송 → grab_pen이 이동 시작
   Goal sent → grab_pen starts moving
        ↓
3. 이동 중 펜이 사라짐 / Pen disappears during movement
        ↓
4. pen_detector가 Cancel 메시지 전송
   pen_detector sends Cancel message
        ↓
5. grab_pen이 안전하게 로봇 정지
   grab_pen safely stops the robot
        ↓
6. 취소 성공 여부를 Result 메시지로 반환
   Result message sent with cancellation status
```

---

## 6. 액션 vs 서비스 선택 기준 / Action vs Service

> **한국어:** 작업의 성격에 따라 적절한 프로토콜을 선택해야 합니다.
> **English:** Choose the appropriate protocol based on the nature of the task.

| 상황 / Situation | 권장 프로토콜 / Recommended |
|---|---|
| 빠른 계산 결과 필요 (예: 두 수의 합) / Quick result needed (e.g. sum) | **Service** |
| 오래 걸리는 작업 + 진행 상황 필요 / Long task + progress needed | **Action** |
| 실행 중 취소가 필요할 수 있을 때 / May need to cancel mid-execution | **Action** |
| 지속적인 데이터 스트림 / Continuous data stream | **Topic** |

### 액션 활용 예시 / Action Use Cases

| 액션 서버 / Action Server | Goal | Feedback | Result |
|---|---|---|---|
| **로봇 팔 이동 / Robot arm move** | 목표 위치 / Target pose | 현재 관절 각도 / Current joint angles | 도달 성공/실패 / Success or failure |
| **로봇 내비게이션 / Navigation** | 목표 좌표 / Target coordinates | 현재 위치 & 남은 거리 / Position & remaining distance | 도착 성공/실패 / Arrived or failed |
| **물체 파지 / Object grasping** | 파지 위치 / Grasp position | 이동 진행률 / Movement progress | 파지 성공/실패 / Grasped or failed |

---

## 📋 핵심 요약 / Key Summary

```
액션 클라이언트 / Client  ──── Goal ────────────→  액션 서버 / Server
                          ←─── Feedback (주기적) ──  (실행 중 / Executing)
                          ←─── Result (최종) ──────  (완료 / Done)
                          ──── Cancel (선택적) ────→  (취소 요청 / Cancel request)
```

| 개념 / Concept | 내용 / Details |
|---|---|
| **Goal** | 작업 시작 요청 / Request to start task |
| **Feedback** | 실행 중 주기적 진행 상황 / Periodic progress during execution |
| **Result** | 완료/실패/취소 최종 알림 / Final notification of completion, failure, or cancel |
| **Cancel** | 실행 중 취소 요청 / Cancel request during execution |
| **논블로킹 / Non-blocking** | 클라이언트는 서버 실행 중에도 다른 작업 가능 / Client continues independently |
