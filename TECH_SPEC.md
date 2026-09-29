# GoalDiary Technical Specification

## 1. 技术目标

GoalDiary 是 Android Offline-First 应用。

核心技术目标：

1. 本地优先
2. 数据可靠
3. 历史记录不可因模板变化而改变
4. 低耦合
5. 易测试
6. 易扩展
7. 支持未来 LAN PC 功能
8. 支持媒体文件
9. 支持长期迭代

---

# 2. 技术栈

## 2.1 Android

* Kotlin
* Jetpack Compose
* Material 3
* Android Jetpack

## 2.2 Architecture

采用 pragmatic layered architecture + MVVM。

推荐：

```text
UI
 ↓
ViewModel
 ↓
Use Case
 ↓
Repository
 ↓
Data Source
 ↓
Room / File Storage / Network
```

不要求为了形式而建立过度复杂的 Clean Architecture。

---

# 3. 项目结构

推荐：

```text
GoalDiary/
├── AGENTS.md
├── PRD.md
├── TECH_SPEC.md
├── README.md
├── .gitignore
│
├── gradle/
├── gradlew
├── gradlew.bat
│
├── settings.gradle.kts
├── build.gradle.kts
│
├── app/
│   ├── build.gradle.kts
│   │
│   └── src/
│       ├── main/
│       │   ├── AndroidManifest.xml
│       │   │
│       │   ├── java/com/goaldiary/
│       │   │   │
│       │   │   ├── GoalDiaryApplication.kt
│       │   │   │
│       │   │   ├── core/
│       │   │   │   ├── common/
│       │   │   │   ├── database/
│       │   │   │   ├── model/
│       │   │   │   ├── storage/
│       │   │   │   ├── media/
│       │   │   │   ├── network/
│       │   │   │   └── ui/
│       │   │   │
│       │   │   ├── feature/
│       │   │   │   ├── home/
│       │   │   │   ├── goals/
│       │   │   │   ├── events/
│       │   │   │   ├── feedback/
│       │   │   │   ├── fitness/
│       │   │   │   ├── study/
│       │   │   │   ├── focus/
│       │   │   │   └── settings/
│       │   │   │
│       │   │   └── navigation/
│       │   │
│       │   └── res/
│       │       ├── drawable/
│       │       ├── mipmap/
│       │       ├── values/
│       │       └── raw/
│       │
│       └── test/
│           └── java/com/goaldiary/
│
├── docs/
│   ├── architecture/
│   ├── decisions/
│   └── development/
│
└── .github/
    └── workflows/
```

---

# 4. Application Architecture

## 4.1 UI

使用 Jetpack Compose。

Composable 不应该：

* 直接访问 Room
* 直接操作数据库
* 包含复杂业务逻辑
* 直接修改 Repository

Composable 应该：

* 显示 State
* 发出 User Intent
* 调用 ViewModel action

例如：

```kotlin
@Composable
fun HomeScreen(
    state: HomeUiState,
    onEventClick: (Long) -> Unit,
    onCompleteClick: (Long) -> Unit
)
```

---

# 5. ViewModel

每个主要 Feature 可以有自己的 ViewModel。

例如：

```text
HomeViewModel
GoalViewModel
EventViewModel
FeedbackViewModel
FitnessViewModel
StudyViewModel
FocusViewModel
SettingsViewModel
```

ViewModel：

* 管理 UI State
* 调用 Use Case
* 处理用户操作
* 暴露 Flow / StateFlow

避免将整个业务系统塞进 ViewModel。

---

# 6. Use Case

Use Case 只在存在实际业务价值时创建。

例如：

```text
CreateGoalUseCase
CreateEventUseCase
CompleteEventUseCase
SubmitFeedbackUseCase
GenerateRecurringEventsUseCase
CalculateGoalProgressUseCase
TriggerRewardUseCase
BackupDataUseCase
RestoreDataUseCase
```

简单 CRUD 不需要为了形式增加大量 Use Case。

---

# 7. Repository

Repository 负责隐藏数据来源。

例如：

```text
GoalRepository
EventRepository
FeedbackRepository
FitnessRepository
StudyRepository
BackupRepository
```

UI 不应该知道数据来自：

* Room
* File
* LAN
* 其他数据源

---

# 8. Database

使用 Room。

数据库主要用于结构化数据。

不要把大型图片、音频、视频直接作为 Room Blob。

---

# 9. 核心数据模型

## 9.1 Goal

建议字段：

```text
Goal
- id
- title
- description
- specific
- measurable
- achievable
- relevant
- targetValue
- currentValue
- unit
- startDate
- deadline
- status
- createdAt
- updatedAt
```

---

# 10. ShortTermGoal

```text
ShortTermGoal
- id
- goalId
- title
- description
- targetValue
- currentValue
- startDate
- deadline
- status
- createdAt
- updatedAt
```

---

# 11. DailyGoal

```text
DailyGoal
- id
- shortTermGoalId
- date
- title
- targetValue
- currentValue
- status
- createdAt
```

---

# 12. Event

Event 表示用户配置的逻辑事件。

```text
Event
- id
- title
- description
- goalId
- shortTermGoalId
- dailyGoalId
- templateId
- type
- startTime
- endTime
- recurrenceRule
- status
- createdAt
- updatedAt
```

实际设计可以根据最终数据库关系进一步调整。

---

# 13. EventTemplate

```text
EventTemplate
- id
- name
- description
- eventType
- defaultDuration
- defaultReminder
- recurrenceRule
- createdAt
- updatedAt
```

模板只影响未来事件。

---

# 14. EventOccurrence

这是历史数据完整性的关键实体。

```text
EventOccurrence
- id
- eventId
- scheduledDate
- startTime
- endTime
- status
- titleSnapshot
- descriptionSnapshot
- templateSnapshot
- completedAt
- createdAt
```

生成 EventOccurrence 时，应保存必要的 snapshot。

因此：

```text
Template v1
     ↓
Occurrence 2026-09-01
```

之后：

```text
Template v2
```

不能改变：

```text
Occurrence 2026-09-01
```

---

# 15. Feedback

```text
Feedback
- id
- eventOccurrenceId
- text
- createdAt
- updatedAt
```

Feedback 可以晚于 Event Completion 创建。

因此：

```text
Event completed
        ↓
Feedback pending
        ↓
Feedback submitted later
```

也是合法流程。

---

# 16. FeedbackMedia

```text
FeedbackMedia
- id
- feedbackId
- type
- uri
- mimeType
- fileSize
- duration
- createdAt
```

支持：

```text
IMAGE
AUDIO
VIDEO
```

---

# 17. Media Storage

媒体文件应该保存在 App 私有存储。

例如：

```text
/files/media/
    images/
    audio/
    video/
```

Room 只保存：

```text
URI
MIME type
size
duration
metadata
```

删除 Feedback Media 时：

1. 删除数据库记录
2. 删除实际文件
3. 如果文件删除失败，需要记录错误状态

---

# 18. Fitness Data Model

## Exercise

```text
Exercise
- id
- name
- category
- description
```

## WorkoutTemplate

```text
WorkoutTemplate
- id
- name
- description
```

## WorkoutTemplateExercise

```text
WorkoutTemplateExercise
- id
- templateId
- exerciseId
- order
- defaultSets
- defaultReps
- defaultWeight
```

## WorkoutSchedule

```text
WorkoutSchedule
- id
- weekday
- workoutTemplateId
- startTime
- duration
```

## WorkoutSession

```text
WorkoutSession
- id
- eventOccurrenceId
- workoutTemplateId
- startedAt
- completedAt
- notes
```

## WorkoutSet

```text
WorkoutSet
- id
- workoutSessionId
- exerciseId
- setNumber
- reps
- weight
- duration
- completed
```

WorkoutTemplate 修改不能修改已经完成的 WorkoutSession。

---

# 19. Study Data Model

## StudySession

```text
StudySession
- id
- eventOccurrenceId
- subject
- durationMinutes
- pages
- notes
- createdAt
```

## StudyNote

```text
StudyNote
- id
- studySessionId
- content
- createdAt
```

未来 MindMap 可以基于 StudyNote 扩展。

第一版本不实现复杂 MindMap Editor。

---

# 20. MindMap Future Model

为了未来扩展，可以预留：

```text
MindMap
- id
- studySessionId
- title
```

```text
MindMapNode
- id
- mindMapId
- parentId
- title
- content
- positionX
- positionY
```

P2 再实现完整编辑器。

---

# 21. Progress System

目标进度必须有明确计算规则。

例如：

```text
progress =
    currentValue / targetValue
```

必须限制：

```text
0 <= progress <= 1
```

UI 显示：

```text
0% - 100%
```

具体不同类型 Goal 可以使用不同 ProgressStrategy。

例如：

```text
ValueProgressStrategy
TaskCountProgressStrategy
DurationProgressStrategy
PageProgressStrategy
```

不要在 UI 中硬编码进度计算。

---

# 22. Recurrence

重复事件必须有明确的 recurrence model。

第一版本至少支持：

* Daily
* Weekly
* Selected Weekdays

例如：

```text
Monday
Wednesday
Friday
```

系统生成：

```text
EventOccurrence
```

而不是每天复制一个独立模板。

---

# 23. Time Handling

时间是高风险区域。

必须：

* 明确使用 LocalDate / LocalTime / Instant
* 避免把时间全部保存成 String
* 明确用户时区
* 正确处理 Daylight Saving Time
* 重复事件根据用户时区解释

历史记录保存实际发生时间。

---

# 24. Navigation

主要页面：

```text
Home
Schedule
Goals
Records
Settings
```

建议使用 Navigation Compose。

Bottom Navigation：

```text
Home
Schedule
Goals
Records
Settings
```

---

# 25. Home UI

Home 应该展示：

```text
Today

Current Short-Term Goal
-----------------------

Today's Progress

████████░░ 80%

Today's Events

08:00 Reading       ✓
12:00 Exercise      ✓
20:00 English       →

Recent Feedback
```

实际视觉设计由 UI 实现阶段决定。

---

# 26. Reward Engine

Reward 不应该直接写在 Event ViewModel 中。

建立：

```text
RewardEngine
```

例如：

```kotlin
interface RewardEngine {
    suspend fun reward(eventId: Long): RewardResult
}
```

RewardResult 可以包含：

```text
sound
animation
message
progress
```

Reward Engine 可以被关闭。

关闭 Reward：

```text
Event completion
        ↓
Feedback
        ↓
Progress
```

仍然正常执行。

Reward 只是增强体验，不是核心数据流程。

---

# 27. Event Completion Transaction

核心流程：

```text
Complete Event
      ↓
Validate Event
      ↓
Persist Completion
      ↓
Update Progress
      ↓
Persist Feedback
      ↓
Trigger Reward
```

数据层面的关键操作应该尽可能保证一致性。

Reward 本身不能导致核心数据回滚。

如果：

```text
Sound fails
```

不应该导致：

```text
Event completion fails
```

---

# 28. Backup

Backup 包建议：

```text
GoalDiaryBackup/
├── manifest.json
├── database/
│   └── database.sqlite
└── media/
    ├── images/
    ├── audio/
    └── video/
```

manifest 至少包含：

```json
{
  "backupVersion": 1,
  "appVersion": "...",
  "schemaVersion": 1,
  "createdAt": "...",
  "deviceTimezone": "..."
}
```

真实实现可以根据 Room / SQLite 导出机制调整。

---

# 29. Restore

Restore 必须：

1. 读取 manifest
2. 验证 backupVersion
3. 验证 schemaVersion
4. 验证数据库
5. 验证媒体
6. 验证引用关系
7. 提示用户
8. 用户确认
9. 执行 restore

Restore 是 destructive operation。

必须明确要求用户确认。

---

# 30. LAN Architecture

LAN 功能作为独立模块。

建议：

```text
core/network/
```

Android：

```text
LocalHttpServer
```

API 示例：

```text
GET /api/v1/goals
GET /api/v1/events
GET /api/v1/records
```

第一阶段只允许：

```text
READ
```

未来再支持：

```text
WRITE
```

---

# 31. LAN Authentication

不能认为：

> 同 Wi-Fi = 安全

应该使用：

```text
Pairing
    ↓
Authentication Token
    ↓
Session
```

Token 不应该写入 Git。

---

# 32. PC Dashboard

未来 PC 可以使用：

```text
Browser
    ↓
Android LAN API
```

不需要服务器。

目标：

```text
http://android-device.local
```

或者：

```text
http://192.168.x.x
```

第一版本不要求 mDNS，一开始可以支持手动 IP。

---

# 33. Focus Mode

第一版本实现：

```text
FocusSession
```

例如：

```text
FocusSession
- id
- eventOccurrenceId
- startedAt
- plannedEndAt
- actualEndAt
- status
```

Focus Screen：

```text
Current Goal

Current Event

00:42:15

[Pause]

[Complete]
```

第一版本只限制 App 内导航。

不要把：

> App 内 Focus Mode

描述成：

> 系统级 App Blocker。

---

# 34. Android System-Level Lock

未来如果实现真正系统级限制，需要单独研究：

* Lock Task Mode
* Device Owner
* Managed Device
* Android permissions
* OEM restrictions

不能假设普通应用拥有任意阻止其他 App 的权限。

如果无法可靠实现，则保留 App 内 Focus Mode。

---

# 35. Permissions

尽可能少申请权限。

根据功能按需申请：

* Notifications
* Camera
* Microphone
* Media access
* Network

不要为了未来功能提前申请大量权限。

---

# 36. Security

禁止：

* Git 中保存 API keys
* Git 中保存 passwords
* Git 中保存 tokens
* Git 中保存 signing credentials

`.gitignore` 应包含：

```text
*.keystore
*.jks
local.properties
.env
```

---

# 37. Input Validation

所有外部输入必须验证。

包括：

* 用户输入
* 导入数据
* Backup
* LAN requests
* Media files

不能信任：

```text
filename
MIME type
file size
network payload
```

---

# 38. Testing Strategy

至少包括：

## Unit Test

测试：

* Goal progress
* Recurrence
* Event completion
* Snapshot
* Feedback
* Backup validation
* Restore validation
* Reward Engine

## UI Test

测试：

```text
Create Goal
↓
Create Event
↓
Complete Event
↓
Submit Feedback
↓
See Progress
```

## Integration Test

重点测试：

```text
Template
↓
Occurrence
↓
Completion
↓
Feedback
↓
Progress
```

---

# 39. 最重要的测试

必须保证：

```text
Create Goal
    ↓
Create Event
    ↓
Complete Event
    ↓
Submit Feedback
    ↓
Update Progress
    ↓
Reward
```

整个流程能够稳定运行。

---

# 40. 历史数据测试

必须有测试：

```text
Create Template v1

Generate Event

Complete Event

Modify Template → v2

Verify historical Event
still contains v1 snapshot
```

这是系统核心数据完整性测试。

---

# 41. Recurrence Tests

至少测试：

* Daily
* Weekly
* Selected Weekdays
* timezone
* DST
* duplicate occurrence prevention

例如：

```text
Monday / Wednesday / Friday

Generate month

Expected:
Mon
Wed
Fri
Mon
Wed
Fri
...
```

---

# 42. Media Tests

测试：

* Image attach
* Audio attach
* Video attach
* Preview
* Delete
* Missing file
* Invalid URI
* Large file
* Unsupported MIME

数据库记录和实际文件必须保持一致。

---

# 43. Offline Requirement

以下功能不能依赖互联网：

* Goal
* Event
* Completion
* Feedback
* Progress
* Fitness
* Study
* Reward
* Backup

---

# 44. Dependency Principle

优先使用：

* AndroidX
* Jetpack
* Kotlin 官方生态

不要为了一个小功能引入大型第三方框架。

每增加一个依赖，需要考虑：

* 是否真的需要
* 是否维护良好
* 是否增加 APK 大小
* 是否增加安全风险
* 是否增加构建复杂度

---

# 45. Git Strategy

使用小而明确的 commit。

例如：

```text
feat: add goal data model
feat: add goal creation flow
feat: add recurring events
feat: add feedback media
feat: add reward engine
test: add recurrence tests
fix: preserve event snapshots
```

不要：

```text
feat: build everything
```

除非用户明确要求。

Codex 默认不要自动 commit，除非任务明确要求。

---

# 46. Development Phases

## Phase 1

Android skeleton：

* Kotlin
* Compose
* Material 3
* Navigation
* Theme
* Basic screens
* Test infrastructure

不实现完整业务。

## Phase 2

Goal System：

* Goal
* ShortTermGoal
* DailyGoal
* Room
* CRUD
* Progress

## Phase 3

Event System：

* Event
* Template
* Occurrence
* Recurrence
* Schedule

## Phase 4

Feedback：

* Text
* Image
* Audio
* Video
* Media storage

## Phase 5

Reward：

* RewardEngine
* Animation
* Sound
* Message

## Phase 6

Fitness：

* Exercise
* Workout Template
* Weekly schedule
* Workout Session
* Workout Set

## Phase 7

Study：

* Study Session
* Notes
* Reading
* Screenshot
* Future MindMap model

## Phase 8

Backup：

* Export
* Import
* Validation
* Restore

## Phase 9

LAN：

* Android API
* Pairing
* Authentication
* PC read-only dashboard

## Phase 10

Focus：

* Focus Session
* Timer
* In-app restrictions

---

# 47. Phase Completion Criteria

每个 Phase 完成前必须：

1. 实现功能
2. 编写必要测试
3. Build 成功
4. Test 成功
5. 检查 Git diff
6. 检查架构一致性
7. 更新 README / docs
8. 明确已知限制

不能因为：

```text
代码写完了
```

就认为 Phase 完成。

---

# 48. Codex 工作原则

Codex 在修改项目之前必须阅读：

```text
AGENTS.md
PRD.md
TECH_SPEC.md
```

优先级：

```text
AGENTS.md
    ↓
TECH_SPEC.md
    ↓
PRD.md
    ↓
当前用户任务
```

如果发现冲突：

1. 不要猜
2. 检查现有代码
3. 说明冲突
4. 选择风险更低的方案
5. 必要时询问用户

---

# 49. Scope Control

如果用户要求：

> “实现反馈功能”

不要顺便实现：

* AI
* 云同步
* 社交
* PC
* Mind Map
* 高级统计

只完成当前 Phase 所需要的范围。

---

# 50. Definition of Done

一个功能只有同时满足以下条件才算完成：

```text
Implementation
+
Tests
+
Build
+
No known blocker
+
Architecture consistency
+
Documentation
```

---

# 51. 最终技术原则

GoalDiary 技术设计始终围绕：

```text
Goal
 ↓
Short-Term Goal
 ↓
Daily Goal
 ↓
Event
 ↓
Action
 ↓
Feedback
 ↓
Reward
 ↓
Progress
```

系统应该始终能够回答：

> 我现在应该做什么？

> 我做完了吗？

> 我为什么要做？

> 我离目标还有多远？
