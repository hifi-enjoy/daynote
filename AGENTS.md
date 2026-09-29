# AGENTS.md

## Project Overview

GoalDiary is an offline-first Android application designed to help users convert long-term goals into daily actions and reinforce completed actions through immediate feedback.

Core behavioral loop:

Goal
→ Short-term Goal
→ Daily Goal
→ Event
→ Action
→ Feedback
→ Positive Reinforcement
→ Progress

The application is NOT intended to diagnose or treat mental health conditions.

Its product purpose is to help users organize goals, execute planned actions, record outcomes, and see accumulated progress.

---

# 1. Product Principles

## 1.1 Offline First

The core application must work without an internet connection.

The following features must not require internet access:

* Goals
* Short-term goals
* Daily goals
* Events
* Recurring events
* Feedback
* Images
* Audio
* Video
* Fitness records
* Study records
* Rewards
* Statistics
* Backup and restore

Internet access must never be a hard dependency for the core application.

---

## 1.2 Local Data by Default

User data belongs to the user.

The default architecture is:

Android device
→ local database
→ local media storage

The first version does not require:

* user accounts
* cloud database
* cloud synchronization
* advertising
* analytics SDK
* social networking

---

## 1.3 Low Input Cost

The application should minimize repetitive data entry.

If a user creates a recurring activity or template, the user should not need to recreate the same activity every day.

Examples:

* Workout A
* Workout B
* Weekly study schedule
* Repeated reading
* Repeated work tasks

---

## 1.4 Historical Data Integrity

Historical records must never be silently modified because a template changed.

Example:

Workout A originally contained:

Bench Press
60 kg
3 × 8

If the user later changes Workout A to:

Bench Press
70 kg
3 × 8

previous completed workout sessions must still contain the historical 60 kg information.

Templates describe future behavior.

Historical occurrences describe what actually happened.

---

## 1.5 Minimal Complexity

Prefer simple, mature Android technologies.

Do not introduce a dependency or framework unless it solves a real problem.

Avoid:

* unnecessary microservices
* unnecessary networking
* unnecessary abstraction layers
* unnecessary third-party SDKs
* speculative infrastructure
* premature cloud architecture

---

# 2. Technology Stack

Preferred stack:

* Kotlin
* Jetpack Compose
* Material 3
* Android Jetpack
* Room
* ViewModel
* Kotlin Coroutines
* Kotlin Flow
* Navigation
* DataStore where appropriate

Use stable, officially supported Android libraries whenever practical.

Before adding a dependency, verify that it is necessary.

---

# 3. Architecture

Use a pragmatic MVVM / layered architecture.

Preferred dependency direction:

UI
→ ViewModel
→ Use Case / Repository
→ Data Source
→ Room / File Storage / Network

UI must not directly access Room DAOs.

Business logic must not be placed directly inside Composable functions.

Repositories should hide implementation details from ViewModels.

---

# 4. Package Structure

Recommended package structure:

```
com.goaldiary

    core/
        common/
        database/
        model/
        storage/
        media/
        network/
        ui/

    feature/
        home/
        goals/
        events/
        feedback/
        fitness/
        study/
        focus/
        settings/

    navigation/
```

Do not create large monolithic files.

Prefer small, cohesive files.

---

# 5. Database

Use Room for structured local data.

Core entities will eventually include:

* Goal
* ShortTermGoal
* DailyGoal
* Event
* EventTemplate
* EventOccurrence
* Feedback
* FeedbackMedia
* WorkoutTemplate
* WorkoutTemplateExercise
* Exercise
* WorkoutSession
* WorkoutSet
* StudySession
* StudyNote
* MindMap
* MindMapNode
* AppSetting
* DevicePairing

Do not implement all entities at once.

Implement them incrementally according to the development phases.

---

# 6. Media Storage

Do NOT store image, audio, or video binary data directly inside Room unless there is a documented and compelling reason.

Store media files separately.

Room should contain metadata and references such as:

* URI
* MIME type
* file size
* duration
* creation time
* ownership/reference ID

Media storage must support:

* creation
* retrieval
* preview
* deletion
* orphan cleanup

---

# 7. Time and Dates

Be careful with:

* local dates
* local times
* time zones
* daylight saving time

Do not assume all timestamps are local time.

Use an explicit and consistent representation.

Persist timestamps in a stable format and convert them to the user's current timezone for display.

Recurring schedules must preserve their intended local time.

---

# 8. Recurring Events

Recurring events must distinguish between:

1. Event Template
2. Event Occurrence

Example:

```
Template:
Every Monday at 19:00 → Workout A

Occurrence:
2026-10-05 19:00 → Workout A
```

Historical occurrences must not be overwritten by future template changes.

---

# 9. Goal Hierarchy

Goals follow:

```
Goal
  ↓
ShortTermGoal
  ↓
DailyGoal
  ↓
Event
```

Not every event must be linked to a goal.

Users may create ordinary events that have no goal relationship.

---

# 10. Feedback

An event may have feedback containing:

* text
* image
* audio
* video

Feedback may be submitted:

1. immediately after the event
2. later during the same day

Do not force users to provide long feedback.

Short feedback must be supported.

---

# 11. Positive Reinforcement

After successful event completion and feedback submission:

1. persist feedback
2. mark event completed
3. update related progress
4. trigger reward
5. show positive message
6. return control to the user

Rewards must be:

* short
* optional
* local
* non-blocking
* configurable

Users must be able to disable:

* reward sounds
* reward animations

Do not make reward animations longer than necessary.

---

# 12. Fitness

Fitness is a specialized event type.

Support:

* exercise definitions
* workout templates
* exercises within templates
* weekday assignment
* workout sessions
* workout sets
* weight
* reps
* sets
* completion
* notes

Do not implement advanced sports science unless explicitly requested.

---

# 13. Study and Reading

Study/reading events should support:

* subject
* duration
* pages
* notes
* quotations
* screenshots
* images

A full free-form mind-map editor is a later feature.

Design the data model so it can be added later without breaking existing study data.

---

# 14. Focus Mode

The MVP focus mode is an in-app focus experience.

It may include:

* full-screen UI
* timer
* current event
* current goal
* limited navigation
* explicit exit
* completion

Do not claim that the application can prevent all Android system usage unless the required Android system capability is actually available and configured.

If system-level locking requires:

* Device Owner
* Lock Task Mode
* special permissions
* managed-device configuration

document that requirement clearly.

Never make the user's device unrecoverable.

---

# 15. LAN / PC Access

LAN access is optional.

The Android app must work normally when LAN access is disabled.

When enabled, the architecture should be:

Android
→ local HTTP API
→ PC browser

Potential discovery mechanisms may include Android local network service discovery.

A manual IP address fallback should exist.

Do not expose the raw database file.

Do not expose arbitrary filesystem paths.

Only explicitly defined API endpoints may be accessible.

LAN access must require authentication/pairing.

---

# 16. Security

Never commit:

* passwords
* API keys
* signing secrets
* private certificates
* tokens
* credentials

Use local secure storage when sensitive credentials are required.

Validate all user input.

Validate file types and file sizes.

Do not trust LAN clients merely because they are on the same Wi-Fi network.

---

# 17. Permissions

Request Android permissions only when needed.

Do not request all permissions at first launch.

Examples:

Camera:
request when the user chooses to capture an image.

Microphone:
request when the user chooses to record audio.

Media:
request when the user chooses media.

LAN:
request/enable only when LAN functionality is used.

---

# 18. Testing

Every significant business feature must have tests.

At minimum:

* unit tests
* database tests
* important UI tests

Critical workflows:

```
Create Goal
→ Create Event
→ Complete Event
→ Submit Feedback
→ Update Progress
→ Display Reward
```

Recurring event generation must be tested.

Historical snapshot behavior must be tested.

Backup/restore must be tested.

---

# 19. Build Verification

Before declaring a task complete:

1. Run the relevant tests.
2. Run the project build.
3. Fix failures.
4. Run tests again.
5. Check for regressions.

Do not claim success without actually running the available verification commands.

---

# 20. Git

Use Git.

Make small, meaningful commits.

Examples:

```
feat: add goal management
feat: add recurring events
feat: add event feedback
feat: add workout templates
feat: add local backup
fix: preserve historical event snapshots
```

Do not create enormous commits containing unrelated features.

---

# 21. Destructive Operations

Do NOT perform destructive operations without explicit user confirmation.

Examples:

* deleting production data
* deleting a database
* resetting all application data
* deleting large groups of files
* overwriting production configuration
* destructive database migrations

If an operation is potentially irreversible, stop and ask for confirmation.

---

# 22. Scope Control

Do not implement features that are not part of the current task.

If you discover a potentially useful improvement:

1. document it
2. do not silently implement it
3. continue with the requested task

Avoid scope creep.

---

# 23. Development Workflow

For every task:

```
1. Read AGENTS.md
2. Read relevant PRD/technical specification
3. Inspect current implementation
4. Identify affected components
5. Create a short implementation plan
6. Implement the smallest coherent change
7. Build
8. Test
9. Fix failures
10. Re-test
11. Report changes and verification
```

Do not implement the entire application in one task.

---

# 24. Current Development Phase

The project is currently in Phase 1.

Phase 1 goal:

Create a clean, buildable Android application skeleton.

Phase 1 must NOT implement:

* full Goal system
* full Event system
* Feedback
* Fitness
* Study
* LAN
* Focus Mode
* complex database schema

Only create the architecture required to begin those features safely.

---

# 25. Phase Completion Standard

A phase is complete only when:

* implementation exists
* build succeeds
* relevant tests pass
* no known blocking errors remain
* changes are documented
* architecture remains consistent with this file

---

# 26. Product Priority

Priority order:

P0:
Core goal/event/feedback loop

P1:
Fitness
Study
Backup
Focus Mode
LAN

P2:
Mind Map
Advanced analytics
AI features
Cloud synchronization

Never sacrifice P0 stability to add P1/P2 features.

---

# 27. Final Principle

The application should always answer three questions:

1. What should I do now?
2. Did I complete it?
3. How much closer am I to my goal?

Every major feature should support at least one of these questions.
