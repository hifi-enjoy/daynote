# GoalDiary Product Requirements Document

## 1. 产品概述

### 1.1 产品名称

**GoalDiary**

### 1.2 产品定位

GoalDiary 是一个以“目标执行”为核心的 Android 应用。

它不是传统意义上的日记 App，而是通过：

> **目标 → 行动 → 完成 → 反馈 → 正反馈 → 进步**

帮助用户持续执行自己的长期目标。

产品重点不是记录“我今天发生了什么”，而是帮助用户回答：

1. 我现在应该做什么？
2. 我完成了吗？
3. 我完成之后有什么反馈？
4. 我距离目标还有多远？

### 1.3 核心理念

用户通常只需要在第一次建立目标和任务时进行较多配置。

之后系统应该尽量自动生成每天的任务，避免用户每天重复输入。

例如：

用户创建：

> 长期目标：通过英语考试

然后建立：

> 短期目标：完成英语阅读训练

再设置：

> 周一至周五 20:00 - 21:00 英语阅读

之后每天系统自动产生对应事件。

用户完成事件后提交反馈：

* 写了什么
* 学到了什么
* 截图
* 图片
* 录音
* 视频

系统随后给予积极反馈：

* 完成动画
* 音效
* 进度变化
* 目标完成度变化

---

# 2. 产品目标

## 2.1 核心目标

建立一个低摩擦的个人执行反馈闭环：

> 长期目标
>
> ↓
>
> 短期目标
>
> ↓
>
> 每日目标
>
> ↓
>
> 今日事件
>
> ↓
>
> 执行
>
> ↓
>
> 完成
>
> ↓
>
> 反馈
>
> ↓
>
> 正反馈
>
> ↓
>
> 目标进度增长

## 2.2 用户价值

用户应该能够：

* 清楚知道当前最重要的事情
* 减少每天重新规划的工作
* 快速记录实际执行结果
* 看到长期目标的真实进展
* 通过完成后的即时反馈获得持续行动的动力
* 保留完整历史记录
* 在不同类型任务中使用不同的反馈方式

## 2.3 非目标

第一版本不追求：

* 社交网络
* 多用户协作
* 云端社区
* AI 心理治疗
* 医疗诊断
* 复杂知识管理
* 复杂项目管理
* 企业级任务管理
* 高级运动科学分析

GoalDiary 是个人目标执行工具，不是心理疾病诊断或治疗工具。

---

# 3. 用户核心流程

## 3.1 创建长期目标

用户创建一个 SMART 长期目标。

目标至少支持：

* 名称
* 描述
* 开始日期
* 截止日期
* 可量化目标
* 当前进度
* 目标单位
* 是否启用

SMART 信息可以包括：

* Specific
* Measurable
* Achievable
* Relevant
* Time-bound

例如：

> 6 个月内完成一本 800 页专业书籍。

---

# 4. 目标层级

GoalDiary 使用三级目标结构。

## 4.1 Long-Term Goal

长期目标。

例如：

> 6 个月内完成专业资格考试准备。

## 4.2 Short-Term Goal

短期目标。

例如：

> 本月完成第一轮教材学习。

短期目标必须在 UI 中具有较高视觉优先级。

## 4.3 Daily Goal

每日目标。

例如：

> 今天完成第 3 章。

Daily Goal 可以由用户手动创建，也可以根据长期目标和短期目标拆解产生。

---

# 5. 今日首页

首页是用户每天最主要的入口。

首页应该优先展示：

1. 当前日期
2. 当前短期目标
3. 今日核心目标
4. 今日事件
5. 完成进度
6. 最近完成记录

核心问题：

> “我今天应该做什么？”

首页应该尽量减少信息噪音。

---

# 6. Event 事件系统

Event 是真正执行的最小任务单位。

例如：

> 20:00 - 21:00 阅读

> 18:30 - 19:30 Workout A

> 09:00 - 09:30 英语听力

---

# 7. 事件类型

## 7.1 一次性事件

例如：

> 2026-10-01 购买考试教材

只执行一次。

## 7.2 重复事件

例如：

> 每周一、三、五 20:00 阅读。

系统自动产生对应日期的 Event Occurrence。

## 7.3 Event Template

用户可以创建模板。

例如：

> 英语学习

包含：

* 阅读
* 单词
* 听力
* 写作

模板可以重复使用。

---

# 8. 历史数据原则

这是产品非常重要的规则。

### 模板 ≠ 历史事件

例如：

用户原本设置：

> Workout A：
> 深蹲 50kg × 10

一个月后修改模板：

> 深蹲 60kg × 10

历史记录中的：

> 2026-09-01 深蹲 50kg × 10

必须保持不变。

模板修改只能影响未来生成的事件。

---

# 9. 事件完成

用户完成 Event 后：

1. 标记 Event 为完成
2. 打开反馈界面
3. 用户提交反馈
4. 保存反馈
5. 更新目标进度
6. 触发正反馈
7. 更新统计

反馈可以立即提交，也可以稍后补交。

---

# 10. Feedback 反馈系统

反馈是 GoalDiary 的核心功能之一。

支持：

* 文本
* 图片
* 音频
* 视频

用户可以只提交一种，也可以组合提交。

例如：

> 今日阅读 45 分钟。

然后：

* 写文字总结
* 上传书籍截图
* 录制语音总结

---

# 11. 正反馈系统

事件完成后系统提供即时反馈。

包括：

### 11.1 动画

例如：

* 完成动画
* 进度条变化
* 星星/粒子效果
* 数字增长

### 11.2 音效

例如：

> “完成”音效

音效必须可以关闭。

### 11.3 文案

例如：

> 完成！

> 今天又向目标前进了一步。

> 今日任务 3/4。

正反馈不能阻塞任务保存。

---

# 12. Fitness 健身模块

健身是特殊 Event 类型。

## 12.1 Exercise

运动项目。

例如：

* Squat
* Bench Press
* Deadlift
* Pull Up

## 12.2 Workout Template

用户可以定义：

> Workout A

包含：

| Exercise    | Sets | Reps | Weight |
| ----------- | ---: | ---: | -----: |
| Squat       |    4 |    8 |   60kg |
| Bench Press |    4 |    8 |   50kg |

## 12.3 星期安排

例如：

| 星期        | Workout |
| --------- | ------- |
| Monday    | A       |
| Tuesday   | Rest    |
| Wednesday | B       |
| Thursday  | Rest    |
| Friday    | C       |

系统自动生成对应训练事件。

## 12.4 Workout Session

实际训练时记录：

* Exercise
* Set
* Reps
* Weight
* Duration
* Notes

历史 Workout Session 必须独立保存。

---

# 13. Study / Reading 学习模块

学习 Event 可以包含：

* 学习主题
* 学习时间
* 页数
* 笔记
* 摘录
* 图片
* 截图

例如：

> 阅读《XXX》

用户可以：

* 快速粘贴笔记
* 添加截图
* 添加文字总结

未来可以扩展 Mind Map。

第一版不要求完整 Mind Map 编辑器。

---

# 14. Focus Mode

GoalDiary 提供专注模式。

用户选择：

> 项目 + 时间

例如：

> 英语学习 20:00 - 21:00

进入 Focus Mode 后：

* 显示当前任务
* 显示计时器
* 显示当前目标
* 限制 App 内其他页面访问
* 完成后进入反馈

第一版本优先实现：

> App 内专注模式。

不要假设 Android 可以在普通应用权限下实现完整系统级 App 锁定。

如果未来实现系统级限制，需要单独研究 Android Lock Task / Device Owner 等能力。

---

# 15. 数据存储

第一版本：

> Offline First

核心数据默认保存在设备本地。

不依赖云端。

数据包括：

* Goals
* Short-Term Goals
* Daily Goals
* Events
* Event Occurrences
* Feedback
* Fitness Records
* Study Records
* App Settings

媒体文件：

* 图片
* 音频
* 视频

不直接存储为数据库 Blob。

数据库保存：

* URI / path
* MIME type
* size
* duration
* metadata

实际媒体文件保存在应用自己的文件存储区域。

---

# 16. Backup / Restore

用户应该可以将本地数据导出。

备份至少包含：

* 数据库
* 媒体文件
* manifest
* schema/version 信息

恢复时必须：

1. 验证备份
2. 检查版本
3. 检查文件完整性
4. 提示用户可能覆盖现有数据
5. 用户确认后恢复

---

# 17. PC / LAN 功能

未来支持同一 Wi-Fi 下 PC 访问 Android App。

第一版本不需要云端。

设计方向：

Android：

> Local HTTP API

PC：

> Web Dashboard

支持：

* 查看目标
* 查看事件
* 查看历史记录
* 导入/导出

第一阶段优先：

> Read-only Dashboard

之后再考虑 PC 编辑。

---

# 18. 安全

同一 Wi-Fi 不代表可信。

LAN 功能必须考虑：

* pairing
* authentication
* session/token
* request validation
* 权限限制

不能直接暴露：

* SQLite 数据库文件
* Android 内部文件路径
* 任意文件读取 API

---

# 19. 功能优先级

## P0 - MVP

必须实现：

* Home
* Long-Term Goal
* Short-Term Goal
* Daily Goal
* Event
* One-time Event
* Recurring Event
* Event Template
* Event Completion
* Feedback
* Text Feedback
* Image Feedback
* Audio Feedback
* Video Feedback
* Reward Animation
* Reward Sound
* Goal Progress
* Fitness Template
* Study / Reading
* Local Database
* Backup / Restore

## P1

* LAN PC access
* PC import/export
* Focus Mode
* Day statistics
* Week statistics
* Month statistics
* Streak

## P2

* Mind Map
* Advanced Focus Mode
* Advanced Analytics
* Encryption improvements
* AI summaries
* Cloud Sync

---

# 20. MVP 成功标准

用户可以完成以下完整流程：

> 创建长期目标
>
> ↓
>
> 创建短期目标
>
> ↓
>
> 创建每日任务
>
> ↓
>
> 创建事件
>
> ↓
>
> 执行事件
>
> ↓
>
> 标记完成
>
> ↓
>
> 提交反馈
>
> ↓
>
> 获得正反馈
>
> ↓
>
> 查看目标进度

如果这个闭环稳定可用，则 MVP 的核心价值已经成立。

---

# 21. UX 原则

## 原则 1：减少重复输入

用户设置一次后，未来应该自动生成。

## 原则 2：完成优先

完成一个任务应该非常容易。

## 原则 3：反馈快速

完成后立即进入反馈。

## 原则 4：历史不可篡改

模板修改不能修改历史执行记录。

## 原则 5：目标必须可见

用户应该始终知道当前任务属于哪个目标。

## 原则 6：进步必须可见

用户应该看到自己完成了多少。

## 原则 7：本地优先

没有网络时核心功能仍然正常。

---

# 22. 后续产品原则

如果新功能与核心闭环冲突：

> Goal → Action → Feedback → Progress

优先保护核心闭环。

避免为了增加功能而让首页越来越复杂。

GoalDiary 最重要的不是“记录更多”，而是：

> **让用户更容易持续行动，并且看见自己的行动正在产生结果。**
