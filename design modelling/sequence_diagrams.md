# Sequence Diagrams — Complete Drawing Guide

## UML Sequence Diagram Notation Cheat Sheet

### Lifelines
```
Draw a rectangle (head) at the top with the object name, then a vertical dashed line going down.

Format:    objectName : ClassName
Example:   admin : Admin
           app : AdmissionApplication

   ┌─────────────────┐
   │  admin : Admin   │
   └────────┬────────┘
            ┆  ← dashed vertical line (lifeline)
            ┆
            ┆
```

### Messages (Arrows)
```
Synchronous call:     ─────────────────▶   (solid line, filled arrowhead)
Return:               - - - - - - - - -▶   (dashed line, open arrowhead)
Self-call:            ─────┐
                           │  (arrow loops back to same lifeline)
                      ◀────┘
Asynchronous:         ─────────────────▶   (solid line, open arrowhead)
Object creation:      - - - - - - - - -▶   (dashed line to a new rectangle)
```

### Activation Bars
```
Draw a thin rectangle on a lifeline to show when an object is "active" (processing).

            ┆
         ┌──┤    ← activation starts when a message arrives
         │  │
         │  │    ← object is processing
         └──┤    ← activation ends when return is sent
            ┆
```

### Combined Fragments (Frames)
```
┌─────────────────────────────────────┐
│ loop  [for each student in batch]   │  ← operator + guard condition
│  ┌──────────────────────────────┐   │
│  │  (messages inside the loop)  │   │
│  └──────────────────────────────┘   │
└─────────────────────────────────────┘

┌─────────────────────────────────────┐
│ alt  [status == APPROVED]           │  ← if/else
│  ┌──────────────────────────────┐   │
│  │  (if-true messages)          │   │
│  ├──────────────────────────────┤   │  ← dashed line separator
│  │ [else]                       │   │
│  │  (if-false messages)         │   │
│  └──────────────────────────────┘   │
└─────────────────────────────────────┘

┌─────────────────────────────────────┐
│ opt  [documents uploaded]           │  ← optional (if condition is true)
│  ┌──────────────────────────────┐   │
│  │  (optional messages)         │   │
│  └──────────────────────────────┘   │
└─────────────────────────────────────┘
```

### Layout Tips
- Place the **initiating actor** on the far left
- Place objects left-to-right in the order they are first called
- Number your messages: 1, 2, 3... for readability
- Activation bars should align with send/receive of messages

---

# SEQUENCE DIAGRAM 1: Student Admission Flow

> **Draw on 1 page. 7 lifelines, ~18 messages.**

## Lifelines (draw left to right in this order)

| Position | Object | Type |
|---|---|---|
| 1 (leftmost) | admin | Admin |
| 2 | form | AdmissionForm |
| 3 | field | FormField |
| 4 | app | AdmissionApplication |
| 5 | student | Student |
| 6 | guardian | Guardian |
| 7 | notif | AdmissionNotification |

## Messages (draw in this exact order, top to bottom)

```
1.  admin ──────────────────────────────▶ form
    1: getActiveTemplate()
    (solid arrow, synchronous)

2.  form ──────────────────────────────▶ field
    1.1: getFields()
    (solid arrow)

3.  field - - - - - - - - - - - - - - -▶ form
    return: List<FormField>
    (dashed return arrow)

4.  form - - - - - - - - - - - - - - - ▶ admin
    return: AdmissionForm with fields
    (dashed return arrow)

    ─────────────────────────────────────────────
    NOTE: Admin fills in the form data offline
    ─────────────────────────────────────────────

5.  admin ──────────────────────────────▶ app
    2: submitApplication(formData: Map)
    (solid arrow)

6.  app ──────┐
    │         │  2.1: validateApplication()
    │  ◀──────┘  (self-call: validates required fields)
    │
    │──────┐
    │      │  2.2: generateApplicationNumber()
    │ ◀────┘  (self-call: auto-generates unique number)

7.  app - - - - - - - - - - - - - - - -▶ admin
    return: AdmissionApplication (status = "SUBMITTED")
    (dashed return arrow)

8.  admin ──────────────────────────────▶ app
    3: reviewApplication(appId, "UNDER_REVIEW", "Docs look good")
    (solid arrow)

    ┌──────────────────────────────────────────────┐
    │ alt  [application is acceptable]              │
    │                                               │
9.  │  admin ──────────────────────────▶ app        │
    │  4: approveApplication(appId)                 │
    │  (solid arrow)                                │
    │                                               │
10. │  app ──────────────────────────────▶ student   │
    │  4.1: <<create>> Student(admissionData)       │
    │  (dashed arrow to new Student rectangle)      │
    │                                               │
11. │  student ──────┐                               │
    │  │             │  4.1.1: generateStudentId()   │
    │  │  ◀──────────┘  (self-call)                  │
    │                                               │
12. │  app - - - - - - - - - - - - - - ▶ admin      │
    │  return: studentId (status = "ENROLLED")      │
    │                                               │
    ├───────────────────────────────────────────────┤
    │ [else: application is rejected]               │
    │                                               │
13. │  admin ──────────────────────────▶ app        │
    │  4: rejectApplication(appId, "Insufficient docs") │
    │                                               │
14. │  app - - - - - - - - - - - - - - ▶ admin      │
    │  return: void (status = "REJECTED")           │
    │                                               │
    └──────────────────────────────────────────────┘

    ─────────────────────────────────────────────
    (Continue only on the APPROVED path below)
    ─────────────────────────────────────────────

15. admin ──────────────────────────────▶ guardian
    5: addGuardian(studentId, guardianDetails)
    (solid arrow)

16. guardian - - - - - - - - - - - - - -▶ admin
    return: Guardian (isPrimaryGuardian = true)
    (dashed return arrow)

17. admin ──────────────────────────────▶ notif
    6: <<create>> AdmissionNotification(recipientId=studentUserId,
       title="Admission Approved", admissionStatus="ENROLLED")
    (dashed arrow to create notification)

18. notif ──────┐
    │           │  6.1: send()
    │  ◀────────┘  (self-call: delivers notification)
```

## Activation Bars
- `admin`: active throughout (thin bar from message 1 to 18)
- `form`: active from message 1 to 4
- `field`: active from message 1.1 to return
- `app`: active from message 2 to 7, then 3, then 4 to 14
- `student`: active from message 4.1 onwards (created here)
- `guardian`: active from message 5 to 16
- `notif`: active from message 6 (created here)

---

# SEQUENCE DIAGRAM 2: Exam Mark Entry & Report Card Generation

> **Draw on 1 page. 8 lifelines, ~22 messages.**

## Lifelines (draw left to right)

| Position | Object | Type |
|---|---|---|
| 1 (leftmost) | teacher | Teacher |
| 2 | group | ExamGroup |
| 3 | exam | MidtermExam |
| 4 | result | ExamResult |
| 5 | scale | GradingScale |
| 6 | stats | ExamStatistics |
| 7 | report | ReportCard |
| 8 | notif | ExamResultNotification |

## Messages (draw in this exact order)

```
    ─────────────────────────────────────────────
    PHASE 1: Exam Setup
    ─────────────────────────────────────────────

1.  teacher ────────────────────────────▶ group
    1: createGroup(name="Mid-Sem 1", batchId, academicYear)
    (solid arrow)

2.  group - - - - - - - - - - - - - - -▶ teacher
    return: ExamGroup (examGroupId)
    (dashed return)

3.  teacher ────────────────────────────▶ exam
    2: <<create>> MidtermExam(name, subjectId, batchId,
       date, maxMarks=50, weightage=0.3, groupId)
    (dashed arrow — object creation)

4.  exam ──────┐
    │          │  2.1: status = "SCHEDULED"
    │  ◀───────┘  (self-call: initialize)

5.  exam - - - - - - - - - - - - - - - ▶ teacher
    return: Exam (examId, status="SCHEDULED")
    (dashed return)

    ─────────────────────────────────────────────
    PHASE 2: After Exam — Mark Entry
    ─────────────────────────────────────────────

6.  teacher ────────────────────────────▶ exam
    3: completeExam(examId)
    (solid arrow — sets status = "COMPLETED")

    ┌───────────────────────────────────────────────────┐
    │ loop  [for each student in batch]                  │
    │                                                    │
7.  │  teacher ─────────────────────────▶ result         │
    │  4: enterMarks(examId, studentId, marks=38.5)      │
    │  (solid arrow)                                     │
    │                                                    │
8.  │  result ──────────────────────────▶ scale          │
    │  4.1: mapPercentageToGrade(77.0)                   │
    │  (solid arrow — 38.5/50 * 100 = 77%)               │
    │                                                    │
9.  │  scale - - - - - - - - - - - - - -▶ result         │
    │  return: "B+"                                      │
    │  (dashed return)                                   │
    │                                                    │
10. │  result ──────────────────────────▶ scale          │
    │  4.2: mapPercentageToGradePoint(77.0)              │
    │  (solid arrow)                                     │
    │                                                    │
11. │  scale - - - - - - - - - - - - - -▶ result         │
    │  return: 8.0                                       │
    │  (dashed return)                                   │
    │                                                    │
12. │  result ──────┐                                     │
    │  │            │  4.3: save(marksObtained=38.5,      │
    │  │  ◀─────────┘       grade="B+", gradePoint=8.0,  │
    │  │                    evaluatedBy=teacherId)        │
    │  │                    (self-call)                   │
    │                                                    │
13. │  result - - - - - - - - - - - - - ▶ teacher        │
    │  return: ExamResult                                │
    │  (dashed return)                                   │
    │                                                    │
    └───────────────────────────────────────────────────┘

    ─────────────────────────────────────────────
    PHASE 3: Publish Results
    ─────────────────────────────────────────────

14. teacher ────────────────────────────▶ exam
    5: publishResults(examId)
    (solid arrow)

15. exam ──────┐
    │          │  5.1: status = "RESULTS_PUBLISHED"
    │  ◀───────┘  (self-call)

16. exam ───────────────────────────────▶ stats
    5.2: computeStatistics(examId)
    (solid arrow)

17. stats ──────┐
    │           │  5.2.1: calculate averageMarks, highestMarks,
    │  ◀────────┘         lowestMarks, passPercentage, stdDeviation
    │                     (self-call)

18. stats - - - - - - - - - - - - - - -▶ exam
    return: ExamStatistics
    (dashed return)

    ┌───────────────────────────────────────────────────┐
    │ loop  [for each student in batch]                  │
    │                                                    │
19. │  exam ────────────────────────────▶ notif          │
    │  5.3: <<create>> ExamResultNotification(           │
    │       recipientId=studentUserId,                   │
    │       examId, subjectName, marksObtained, grade)   │
    │  (dashed arrow — creation)                         │
    │                                                    │
20. │  notif ──────┐                                      │
    │  │           │  5.3.1: send()                       │
    │  │  ◀────────┘  (self-call)                         │
    │                                                    │
    └───────────────────────────────────────────────────┘

    ─────────────────────────────────────────────
    PHASE 4: Report Card Generation
    ─────────────────────────────────────────────

21. teacher ────────────────────────────▶ report
    6: generateBatchReportCards(batchId, academicYear)
    (solid arrow)

    ┌───────────────────────────────────────────────────┐
    │ loop  [for each student in batch]                  │
    │                                                    │
22. │  report ──────┐                                     │
    │  │            │  6.1: calculateGPA(studentId, year) │
    │  │  ◀─────────┘  (self-call — uses ExamResults)     │
    │                                                    │
23. │  report ──────┐                                     │
    │  │            │  6.2: calculateCGPA(studentId)      │
    │  │  ◀─────────┘  (self-call)                        │
    │                                                    │
24. │  report ──────┐                                     │
    │  │            │  6.3: compute rank                  │
    │  │  ◀─────────┘  (self-call)                        │
    │                                                    │
    └───────────────────────────────────────────────────┘

25. report - - - - - - - - - - - - - - ▶ teacher
    return: List<ReportCard>
    (dashed return)
```

---

# SEQUENCE DIAGRAM 3: Fee Payment Flow

> **Draw on 1 page. 7 lifelines, ~17 messages.**

## Lifelines (draw left to right)

| Position | Object | Type |
|---|---|---|
| 1 (leftmost) | admin | Admin |
| 2 | feeStruct | FeeStructure |
| 3 | payment | CashPayment |
| 4 | txn | IncomeTransaction |
| 5 | student | Student |
| 6 | report | FinancialReport |
| 7 | notif | FeeReminderNotification |

## Messages (draw in this exact order)

```
    ─────────────────────────────────────────────
    PHASE 1: View Fee Structure & Defaulters
    ─────────────────────────────────────────────

1.  admin ────────────────────────────▶ feeStruct
    1: getFeeStructureByBatch(batchId)
    (solid arrow)

2.  feeStruct - - - - - - - - - - - - -▶ officer
    return: List<FeeStructure> (categories, amounts, due dates)
    (dashed return)

3.  admin ────────────────────────────▶ payment
    2: getDefaulters(batchId)
    (solid arrow — class method on FeePayment parent)

4.  payment - - - - - - - - - - - - - -▶ admin
    return: List<Student> (students with pending fees)
    (dashed return)

    ─────────────────────────────────────────────
    PHASE 2: Collect Fee (Student arrives to pay)
    ─────────────────────────────────────────────

5.  admin ────────────────────────────▶ feeStruct
    3: calculateLateFine(feeStructureId, today)
    (solid arrow)

6.  feeStruct - - - - - - - - - - - - -▶ admin
    return: lateFine = 150.0
    (dashed return)

7.  admin ────────────────────────────▶ payment
    4: <<create>> CashPayment(studentId, feeStructureId,
       amountPaid=25000, lateFine=150, totalAmount=25150)
    (dashed arrow — object creation)

8.  payment ──────┐
    │             │  4.1: generateReceiptNumber()
    │  ◀──────────┘  (self-call: auto-generates receipt)

9.  payment ──────┐
    │             │  4.2: processPayment()
    │  ◀──────────┘  (self-call: CashPayment-specific processing,
    │                 records denomination breakdown)

10. payment ──────┐
    │             │  4.3: status = "COMPLETED"
    │  ◀──────────┘  (self-call)

11. payment ────────────────────────────▶ txn
    4.4: <<create>> IncomeTransaction(
         category="Tuition Fee", amount=25150,
         referenceId=paymentId, source="Student Fee")
    (dashed arrow — object creation)

12. txn - - - - - - - - - - - - - - - -▶ payment
    return: IncomeTransaction (transactionId)
    (dashed return)

13. payment - - - - - - - - - - - - - -▶ admin
    return: CashPayment (paymentId, receiptNumber)
    (dashed return)

    ─────────────────────────────────────────────
    PHASE 3: Generate Receipt & Notify
    ─────────────────────────────────────────────

14. admin ────────────────────────────▶ payment
    5: generateReceipt(paymentId)
    (solid arrow)

15. payment - - - - - - - - - - - - - -▶ admin
    return: File (receipt PDF)
    (dashed return)

16. payment ────────────────────────────▶ notif
    6: <<create>> FeeReminderNotification(
       recipientId=studentUserId,
       title="Fee Payment Received",
       amountDue=0, dueDate=null)
    (dashed arrow — object creation)

17. notif ──────┐
    │           │  6.1: send()
    │  ◀────────┘  (self-call)

    ─────────────────────────────────────────────
    PHASE 4: End-of-Month Financial Report
    ─────────────────────────────────────────────

18. admin ────────────────────────────▶ report
    7: generateReport(fromDate, toDate)
    (solid arrow)

19. report ──────┐
    │            │  7.1: aggregate all IncomeTransactions,
    │  ◀─────────┘       ExpenseTransactions in date range
    │                    calculate totalIncome, totalExpense, netBalance
    │                    (self-call)

20. report - - - - - - - - - - - - - - ▶ admin
    return: FinancialReport
    (dashed return)
```

## Activation Bars
- `admin`: active throughout
- `feeStruct`: active for messages 1-2 and 5-6
- `payment`: active from message 4 (creation) through 15, and message 16
- `txn`: active from message 4.4 (creation) to return 12
- `student`: referenced but not directly messaged (data lookup only)
- `report`: active from message 7 to 20
- `notif`: active from message 6 (creation)
