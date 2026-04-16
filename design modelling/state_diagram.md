# State Diagram — Student Lifecycle — Complete Drawing Guide

## UML State Diagram Notation Cheat Sheet

### States
```
Simple State:
┌──────────────────────────┐
│       StateName          │
├──────────────────────────┤
│ entry / action           │  ← runs when entering this state
│ do / activity            │  ← runs while in this state
│ exit / action            │  ← runs when leaving this state
└──────────────────────────┘

If a state has no internal actions, just draw:
┌──────────────────────────┐
│       StateName          │
└──────────────────────────┘
```

### Initial and Final Pseudostates
```
Initial:   ●         (filled black circle)
Final:     ◉         (filled circle inside a circle, "bull's eye")
```

### Transitions (Arrows between states)
```
    event [guard] / action
State A ─────────────────────────▶ State B

- event:  what triggers the transition (e.g., "admin approves")
- [guard]: condition that must be true (e.g., "[all docs verified]")
- /action: what happens during the transition (e.g., "/create Student record")

All three parts are optional. At minimum you need the event.
```

### Composite (Nested) State
```
┌─────────────────────────────────────────────┐
│            CompositeStateName               │
│  ┌──────────┐         ┌──────────┐         │
│  │ SubState1 │────────▶│ SubState2│         │
│  └──────────┘         └──────────┘         │
└─────────────────────────────────────────────┘
```

### Fork / Join (for concurrent states)
```
Fork:   ────┬──── (horizontal bar splitting into multiple paths)
Join:   ────┴──── (horizontal bar merging multiple paths)
```

---

# STUDENT LIFECYCLE STATE DIAGRAM

> **Draw on 1 page. 9 states, 14 transitions, 1 composite state.**

## All States (boxes to draw)

### State 1: Initial Pseudostate
```
● (filled black circle — starting point)
```

### State 2: SUBMITTED (inside composite)
```
┌──────────────────────────────────┐
│           SUBMITTED              │
├──────────────────────────────────┤
│ entry / generateApplicationNo()  │
│ do / await admin review          │
└──────────────────────────────────┘
```

### State 3: UNDER_REVIEW (inside composite)
```
┌──────────────────────────────────┐
│         UNDER_REVIEW             │
├──────────────────────────────────┤
│ entry / assignReviewer()         │
│ do / verify documents & data     │
└──────────────────────────────────┘
```

### State 4: WAITLISTED (inside composite)
```
┌──────────────────────────────────┐
│          WAITLISTED              │
├──────────────────────────────────┤
│ entry / addToWaitQueue()         │
│ do / monitor batch capacity      │
└──────────────────────────────────┘
```

### State 5: APPROVED (inside composite)
```
┌──────────────────────────────────┐
│           APPROVED               │
├──────────────────────────────────┤
│ entry / sendApprovalNotification()│
│ do / await document submission   │
└──────────────────────────────────┘
```

### State 6: REJECTED (terminal inside composite)
```
┌──────────────────────────────────┐
│           REJECTED               │
├──────────────────────────────────┤
│ entry / sendRejectionNotification()│
│ entry / recordRejectionReason()  │
└──────────────────────────────────┘
```

### State 7: ACTIVE
```
┌──────────────────────────────────┐
│            ACTIVE                │
├──────────────────────────────────┤
│ entry / createStudentRecord()    │
│ entry / assignBatch()            │
│ entry / generateRollNumber()     │
│ do / attend classes, take exams  │
└──────────────────────────────────┘
```

### State 8: SUSPENDED
```
┌──────────────────────────────────┐
│          SUSPENDED               │
├──────────────────────────────────┤
│ entry / revokeSystemAccess()     │
│ entry / notifyGuardians()        │
│ do / await disciplinary review   │
└──────────────────────────────────┘
```

### State 9: GRADUATED
```
┌──────────────────────────────────┐
│          GRADUATED               │
├──────────────────────────────────┤
│ entry / generateFinalTranscript()│
│ entry / updateStatus("GRADUATED")│
│ entry / archiveStudentRecord()   │
└──────────────────────────────────┘
```

### State 10: TRANSFERRED
```
┌──────────────────────────────────┐
│         TRANSFERRED              │
├──────────────────────────────────┤
│ entry / generateTransferCert()   │
│ entry / decrementBatchStrength() │
│ entry / archiveStudentRecord()   │
└──────────────────────────────────┘
```

### State 11: DROPPED_OUT
```
┌──────────────────────────────────┐
│          DROPPED_OUT             │
├──────────────────────────────────┤
│ entry / recordDropoutReason()    │
│ entry / processFeeClearance()    │
│ entry / archiveStudentRecord()   │
└──────────────────────────────────┘
```

### State 12: Final Pseudostate
```
◉ (bull's eye — terminal point)
```

---

## Composite State: ADMISSION_PROCESS

Draw a large rounded rectangle containing states 2–6:

```
┌─────────────────────────────────────────────────────────────────────────┐
│                       ADMISSION_PROCESS                                │
│                                                                         │
│   ●                                                                     │
│   │                                                                     │
│   ▼                                                                     │
│ ┌───────────┐    admin reviews    ┌──────────────┐                     │
│ │ SUBMITTED  │──────────────────▶│ UNDER_REVIEW  │                     │
│ └───────────┘                    └──────┬───┬────┘                     │
│                                         │   │                           │
│                              approves   │   │  rejects                  │
│                          [docs valid]   │   │  [docs invalid]           │
│                                         ▼   ▼                           │
│                                  ┌──────────┐  ┌──────────┐           │
│                                  │ APPROVED  │  │ REJECTED │──▶ ◉     │
│                                  └──────────┘  └──────────┘           │
│                                         ▲                               │
│                                         │ slot opens                    │
│                              ┌──────────┴──┐                           │
│                              │  WAITLISTED  │                           │
│                              └─────────────┘                           │
│                                    ▲                                    │
│                                    │ [batch full]                       │
│                         from UNDER_REVIEW                               │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## All Transitions (arrows to draw)

| # | From | To | Event | [Guard] | /Action |
|---|---|---|---|---|---|
| T1 | ● (Initial) | SUBMITTED | Application submitted | [all required fields filled] | /generateApplicationNumber() |
| T2 | SUBMITTED | UNDER_REVIEW | Admin opens review | — | /assignReviewer(adminId) |
| T3 | UNDER_REVIEW | APPROVED | Admin approves | [documents valid, data correct] | /sendApprovalNotification() |
| T4 | UNDER_REVIEW | REJECTED | Admin rejects | [documents invalid OR ineligible] | /sendRejectionNotification(), /recordReason() |
| T5 | UNDER_REVIEW | WAITLISTED | Admin waitlists | [batch.currentStrength >= batch.maxStrength] | /addToWaitQueue() |
| T6 | WAITLISTED | APPROVED | Slot opens | [batch.hasCapacity() == true] | /removeFromWaitQueue() |
| T7 | APPROVED | ACTIVE | Documents verified & enrolled | [all mandatory docs uploaded, guardian info present, fee paid] | /createStudentRecord(), /assignBatch(), /generateRollNumber(), /incrementBatchStrength() |
| T8 | REJECTED | ◉ (Final) | — | — | — |
| T9 | ACTIVE | ACTIVE | Batch transfer | [targetBatch.hasCapacity() == true] | /transferBatch(newBatchId), /updateSection() |
| T10 | ACTIVE | SUSPENDED | Disciplinary action | [violation confirmed] | /revokeSystemAccess(), /notifyGuardians() |
| T11 | ACTIVE | GRADUATED | Completes programme | [all credits earned, no pending fees, all exams cleared] | /generateFinalTranscript(), /archiveRecord() |
| T12 | ACTIVE | TRANSFERRED | Transfers out | [transfer certificate requested, no dues] | /generateTransferCert(), /decrementBatchStrength() |
| T13 | ACTIVE | DROPPED_OUT | Leaves voluntarily | [withdrawal form submitted] | /recordReason(), /processFeeClearance() |
| T14 | SUSPENDED | ACTIVE | Reinstated | [disciplinary period over, committee approves] | /restoreSystemAccess(), /notifyGuardians() |
| T15 | SUSPENDED | DROPPED_OUT | Expelled | [expulsion confirmed by committee] | /recordExpulsion(), /processFeeClearance() |
| T16 | GRADUATED | ◉ (Final) | — | — | — |
| T17 | TRANSFERRED | ◉ (Final) | — | — | — |
| T18 | DROPPED_OUT | ◉ (Final) | — | — | — |

---

## Drawing Layout

```
                    ┌──────────────────────────────────────────────────┐
                    │              ADMISSION_PROCESS                    │
    ●──▶            │  SUBMITTED ──▶ UNDER_REVIEW ──▶ APPROVED        │
                    │                    │  │              ▲            │
                    │                    │  ▼              │            │
                    │                    │ REJECTED──▶◉   WAITLISTED   │
                    └────────────────────│─────────────────────────────┘
                                        │
                                        ▼  (T7: enrolled)
    ┌───────────────────────────────────────────────────────────┐
    │                                                           │
    │   ┌──────────┐     T10      ┌───────────┐               │
    │   │  ACTIVE   │◀───────────▶│ SUSPENDED  │               │
    │   │          │      T14     └─────┬─────┘               │
    │   └──┬──┬──┬─┘                    │ T15                  │
    │      │  │  │                      ▼                      │
    │  T11 │  │  │ T13          ┌────────────┐                │
    │      │  │  └─────────────▶│ DROPPED_OUT │──▶ ◉          │
    │      │  │                 └────────────┘                │
    │      │  │ T12                                            │
    │      │  └────────────────▶┌─────────────┐               │
    │      │                    │ TRANSFERRED  │──▶ ◉          │
    │      │                    └─────────────┘               │
    │      ▼                                                   │
    │ ┌───────────┐                                            │
    │ │ GRADUATED  │──▶ ◉                                      │
    │ └───────────┘                                            │
    └───────────────────────────────────────────────────────────┘
```

### Drawing Tips
1. Draw the **ADMISSION_PROCESS** composite state in the **upper half** of the page
2. Draw the **post-admission states** (ACTIVE, SUSPENDED, GRADUATED, TRANSFERRED, DROPPED_OUT) in the **lower half**
3. The transition from APPROVED → ACTIVE crosses the composite state boundary — draw the arrow exiting the composite box
4. ACTIVE → ACTIVE (batch transfer, T9) is a **self-transition** — draw a small loop arrow
5. Terminal states (REJECTED, GRADUATED, TRANSFERRED, DROPPED_OUT) all have arrows to the ◉ final pseudostate
6. Use the **guard conditions in square brackets** to show when each transition is valid
