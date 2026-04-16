# Task 1 — Report Content (Aligned with Final UML — 48 Classes)

> **Module Choices for Implementation (Tasks 2 & 3):**
> - **Mandatory:** Dashboard
> - **Category A (Core Process):** Examinations
> - **Category B (Data Management):** Student Admission

---

## 1. Responsibilities Table

| # | Class | Page | Key Responsibilities |
|---|---|---|---|
| 1 | **User** (abstract) | 1 | Central identity; authentication (login/logout), profile management, password reset. Abstract base for all actors. |
| 2 | **Admin** | 1 | System-wide management: user CRUD, system settings, audit logs, broadcast messaging. |
| 3 | **Teacher** | 1 | Teaching role: view assigned subjects, mark attendance, enter exam marks, view timetable. Links to Employee via FK. |
| 4 | **StudentUser** | 1 | Student role: view attendance, results, fees, timetable. Represents the student actor in the system. |
| 5 | **Parent** | 1 | Parent/guardian role: view child's attendance, results, fees, communicate with teachers. |
| 6 | **Session** | 1 | Authentication session: token management, expiry, device tracking, validity checking. |
| 7 | **Dashboard** (abstract) | 2 | Navigational hub: role-specific widget layout, global search, customization. Abstract base for role dashboards. |
| 8 | **AdminDashboard** | 2 | Admin view: admission stats, revenue overview, audit logs, system health. |
| 9 | **TeacherDashboard** | 2 | Teacher view: today's schedule, pending marks, attendance stats, leave status. |
| 10 | **StudentDashboard** | 2 | Student view: upcoming exams, attendance summary, fee dues, recent results. |
| 11 | **ParentDashboard** | 2 | Parent view: child's attendance, results, fee status, messages from teachers. |
| 12 | **Widget** | 2 | Individual dashboard component: renders data (charts/tables/counters), repositioning, resizing. |
| 13 | **AuditLog** | 2 | System accountability: records all actions (who, what, when, where) for traceability. |
| 14 | **SystemSettings** | 2 | Institute-wide configuration: currency, timezone, grading system, academic year, auto-ID. |
| 15 | **Notification** (abstract) | 3 | In-app alert delivery: send, read status, priority, action URL. Abstract base for typed notifications. |
| 16 | **FeeReminderNotification** | 3 | Fee-specific alerts with amount due and due date. |
| 17 | **ExamResultNotification** | 3 | Exam result alerts with marks and grade summary. |
| 18 | **AttendanceWarningNotification** | 3 | Attendance shortage alerts with current vs. threshold percentage. |
| 19 | **LeaveStatusNotification** | 3 | Leave approval/rejection alerts for employees. |
| 20 | **GeneralNotification** | 3 | Catch-all for news, messages, admission status, and other system alerts. |
| 21 | **AdmissionForm** | 4 | Customizable admission form template: field management, versioning, activation/deactivation. |
| 22 | **FormField** | 4 | Individual form field definition: type, validation rules, display order, section grouping. |
| 23 | **AdmissionApplication** | 4 | Single admission request lifecycle: submit, validate, review, approve/reject/waitlist, enroll. |
| 24 | **EducationHistory** | 4 | Previous education records: institution, grades, certificates, board information. |
| 25 | **StudentDocument** | 4 | Document management: upload, verify, download student documents (photos, certificates, IDs). |
| 26 | **Department** | 5 | Organizational unit: groups courses and employees under a department head. |
| 27 | **Course** (abstract) | 5 | Academic course definition: code, credits, department, prerequisite. Abstract base for course types. |
| 28 | **CoreCourse** | 5 | Mandatory courses with minimum pass grade requirements. |
| 29 | **ElectiveCourse** | 5 | Optional courses with registration deadlines, minimum enrollment checks. |
| 30 | **LabCourse** | 5 | Laboratory courses with room, capacity, equipment requirements. |
| 31 | **ProjectCourse** | 5 | Project-based courses with team size, submission deadlines, guide assignment. |
| 32 | **Batch** | 5 | Student cohort: groups students by year/section, tracks capacity, assigns class teacher. |
| 33 | **Subject** | 5 | Specific subject within a course: teacher assignment, credits, marks, semester. |
| 34 | **Exam** (abstract) | 6 | Exam event definition: scheduling, marks, weightage, status lifecycle. Abstract base for exam types. |
| 35 | **MidtermExam** | 6 | Mid-semester exam with syllabus range and repeatability. |
| 36 | **FinalExam** | 6 | End-semester exam with attendance eligibility, hall ticket generation. |
| 37 | **QuizExam** | 6 | Short assessment with quiz numbering, surprise flag, best-N selection. |
| 38 | **AssignmentExam** | 6 | Take-home assessment with submission deadline, group option, late penalty. |
| 39 | **ExamGroup** | 6 | Groups related exams (e.g., "Mid-Sem 1") for collective reporting. |
| 40 | **ExamResult** | 6 | Individual marks/grade for one student on one exam. Supports absence and malpractice. |
| 41 | **GradingScale** | 6 | Grade boundary definitions: percentage ranges → grade names → grade points. |
| 42 | **ExamStatistics** | 6 | Per-exam statistics: average, median, highest, lowest, pass %, std deviation. |
| 43 | **ReportCard** | 6 | Aggregated academic report: GPA, CGPA, rank, remarks. Exportable as PDF. |
| 44 | **Employee** (abstract) | 7 | Central HR entity: personal/banking/qualification details, hiring, termination. Abstract base for staff types. |
| 45 | **TeachingStaff** | 7 | Teaching-specific: assigned subjects, max hours per week. |
| 46 | **NonTeachingStaff** | 7 | Non-teaching-specific: staff role, shift timing, reporting manager. |
| 47 | **ContractStaff** | 7 | Contract-specific: contract dates, amount, renewal status. |
| 48 | **Payroll** | 7 | Monthly salary computation: basic + allowances. Approval workflow (pending → approved → paid). |
| 49 | **Payslip** | 7 | Generated salary slip document linked to payroll. Authentication and download. |
| 50 | **Leave** | 7 | Leave application lifecycle: apply, approve/reject, cancel. Uses `leaveType` field (casual/sick/earned/unpaid). |
| 51 | **AttendanceRecord** | 8 | Single attendance entry: student, subject, date, status. Bulk marking support. |
| 52 | **AttendanceReport** | 8 | Attendance summary: percentage calculation, defaulter identification. Uses `reportType` field (daily/monthly/subject-wise). |
| 53 | **FeeStructure** | 9 | Fee definition: category, amount, due date per batch and academic year. |
| 54 | **FeePayment** | 9 | Fee collection: payment, receipt generation, defaulter check. Uses `paymentMode` field (cash/cheque/online/UPI). |
| 55 | **FinancialTransaction** | 9 | General financial record: income/expense/donation tracking. Uses `transactionType` field. |
| 56 | **FinancialReport** | 9 | Aggregated financial summary: income vs. expense, export to PDF. |
| 57 | **Message** | 10 | One-to-one messaging: send, reply, threading (parentMessageId), read status. |
| 58 | **Broadcast** | 10 | System-wide announcements: audience targeting (all/students/teachers/batch-specific). |
| 59 | **SMSNotification** | 10 | SMS delivery: single/bulk send, delivery status tracking. |
| 60 | **News** | 10 | Rich-text news articles: create, publish/unpublish, categorize. |
| 61 | **Comment** | 10 | User comments on news: add, delete, moderation. |
| 62 | **TimeTable** | 10 | Weekly schedule for a batch: create, activate, get slots. |
| 63 | **TimeTableSlot** | 10 | Individual time slot with `slotType` (lecture/lab/break), subject, teacher, room. |
| 64 | **WorkloadTracker** | 10 | Teacher workload monitoring: total hours per week, overload checking. |

---

## 2. Design Narrative

### 2.1 Low Coupling

The system achieves low coupling through several mechanisms:

**Module Boundary Isolation:** Each module (Admission, Examination, Attendance, Finance, etc.) encapsulates its own data and logic behind well-defined class interfaces. For example, `ExamResult` does not directly access `FeePayment` — they interact only through the shared `StudentUser` entity acting as a common reference point. A change in how fees are processed has zero impact on how exam marks are entered.

**Notification as Mediator:** Instead of modules directly alerting users, all notifications are handled through the `Notification` hierarchy. The `Exam` class creates an `ExamResultNotification` object; it doesn't know how or where that notification is rendered. This decouples the alerting concern from business logic.

**Loose FK References:** Classes reference each other via String foreign keys (`studentId`, `batchId`) rather than direct object references. This means `AttendanceRecord` doesn't hold a direct `StudentUser` object — it holds a `studentId` string, and lookup is done via query methods. This prevents transitive coupling.

**Type Fields over Deep Hierarchies:** Where behavioral differences are minimal, the design uses type discriminator fields (e.g., `FeePayment.paymentMode`, `AttendanceReport.reportType`, `TimeTableSlot.slotType`) instead of inheritance hierarchies. This keeps the class count manageable while maintaining flexibility — adding a new payment mode only requires a new enum value, not a new class.

### 2.2 High Cohesion

Each class has a single, well-defined responsibility:

**Exam vs. ExamResult vs. ExamStatistics:** The `Exam` hierarchy handles only exam metadata and lifecycle (scheduling, starting, completing, publishing). It does not compute results — that's `ExamResult`'s job. Statistical aggregation (average, std deviation) is in `ExamStatistics`, not mixed into either `Exam` or `ExamResult`. Each class does one thing well.

**Specialized Subclasses Where Warranted:** The `Exam` hierarchy uses inheritance because exam types have genuinely different attributes and methods. `FinalExam` has `checkEligibility()` and `generateHallTicket()` — methods that would be incoherent in a `QuizExam`. Same for `QuizExam.getBestNQuizzes()` — this is a quiz-specific concern contained in the quiz-specific class.

**Data vs. Aggregation Separation:** `AttendanceRecord` captures raw attendance data. `AttendanceReport` handles aggregation and reporting via its `reportType` field (daily, monthly, subject-wise). The raw data class and the reporting class are separate, each cohesive to its purpose.

### 2.3 Separation of Concerns

**Data vs. Presentation:** Data classes (`ExamResult`, `AttendanceRecord`) store raw records. Presentation/report classes (`ReportCard`, `FinancialReport`, `ExamStatistics`) handle aggregation, formatting, and export. PDF export (`ReportCard.exportReportCardPDF()`, `FinancialReport.exportPDF()`) is a presentation concern separated from data storage.

**Configuration vs. Runtime:** `SystemSettings`, `FeeStructure`, and `GradingScale` are configuration entities set up once by administrators. `FeePayment`, `AttendanceRecord`, and `ExamResult` are runtime entities created during daily operations. Reconfiguring the fee structure doesn't touch historical payment records.

**Authentication vs. Authorization:** `Session` handles authentication (who are you? — token management, expiry). Authorization is handled by the **User Hierarchy** itself (what can you do? — e.g., only `Admin` has `manageSystemSettings()`, only `Teacher` has `markAttendance()`).

**Audit vs. Business Logic:** `AuditLog` is a cross-cutting concern with its own independent lifecycle. Business events create audit entries, but `AuditLog` never influences business decisions. Removing the audit system would not break any business workflow.

### 2.4 Law of Demeter

The design adheres to the Law of Demeter ("talk only to your immediate friends"):

**ExamResult → GradingScale (direct dependency):** `ExamResult.calculateGrade()` calls `GradingScale.mapPercentageToGrade()` directly rather than traversing through `Exam → Subject → Course → Department → SystemSettings → GradingScale`.

**Dashboard encapsulation:** `StudentDashboard.getAttendanceSummary()` returns a `Map<String, Object>` directly — callers never traverse `Dashboard → AttendanceReport → AttendanceRecord → StudentUser → Batch` chains.

**FeePayment.collectFee() encapsulates the workflow:** The caller doesn't need to separately create both a payment and a `FinancialTransaction`. `collectFee()` internally creates the transaction, generates the receipt, and triggers the notification.

**ReportCard delegates, doesn't traverse:** `ReportCard.calculateGPA()` calls `ExamResult.getResultsByStudent()` for marks and uses `GradingScale.mapPercentageToGradePoint()` for grade computation. It does not access `Exam` directly.

---

## 3. Design Patterns Used

### Pattern 1: Strategy Pattern (Grading System)

**Where:** `GradingScale` + `SystemSettings.gradingSystem` + `ReportCard`

**How:** The system supports multiple grading methodologies (GPA, CCE, CWA). The active grading strategy is configured in `SystemSettings.gradingSystem`. `GradingScale` defines the mapping rules (percentage ranges to grade points), and `ReportCard` has separate strategy methods — `calculateGPA()`, `calculateCGPA()` — each implementing a different computation algorithm. At runtime, the system selects the appropriate calculation method based on the institute's configured grading system.

**Benefit:** A new grading method (e.g., pass/fail) can be added by:
1. Adding a new method to `ReportCard`
2. Adding new entries to `GradingScale`
3. Updating `SystemSettings.gradingSystem`
No changes needed to `ExamResult`, `Exam`, or any other class.

### Pattern 2: Observer Pattern (Notification System)

**Where:** `Notification` hierarchy, triggered by `Exam.publishResults()`, `FeePayment.collectFee()`, `AttendanceReport.getDefaulters()`, `Leave.approveLeave()`, etc.

**How:** When a significant event occurs (results published, fee paid, attendance below threshold, leave approved), the source class creates the appropriate `Notification` subclass object. `ExamResultNotification` is created by `Exam.publishResults()`, `FeeReminderNotification` by the fee module, `LeaveStatusNotification` by `Leave`. Each notification subclass knows its own context (exam-specific data, fee-specific data, etc.) and calls its own `send()` method. The source classes are "subjects" that emit events; the `Notification` subclasses are "observers" that handle delivery.

**Benefit:** The inheritance makes this pattern type-safe. `ExamResultNotification` carries `examId`, `marksObtained`, `grade` — data that is meaningless for a `FeeReminderNotification`. Adding a new event type only requires creating a new subclass, not modifying any existing notification or business class.

### Pattern 3: Template Method Pattern (Admission Form)

**Where:** `AdmissionForm` + `FormField` + `AdmissionApplication`

**How:** `AdmissionForm` defines a template structure (a set of `FormField` objects) that can be customized per institute. The template defines the skeleton (which fields exist, their order, validation rules), and individual `AdmissionApplication` instances fill in the template with concrete values. The method `AdmissionApplication.validateApplication()` iterates through the form template's fields and validates each against its `FormField.validationRule`. Different institutes customize the form without changing the processing logic.

**Benefit:** Adding a new admission field (e.g., "blood group") only requires adding a `FormField` row to the existing form template. No code changes to `AdmissionApplication` or any other class.

---

## 4. Anti-Patterns Identified and Avoided

### Anti-Pattern 1: God Class (Avoided)

**Risk:** A single monolithic `StudentUser` class could accumulate admission logic, attendance tracking, fee management, exam results, messaging, timetable viewing, and dashboard rendering.

**How Avoided:** Responsibilities are distributed across dedicated classes: `AdmissionApplication` handles admissions, `AttendanceRecord` handles attendance, `FeePayment` handles fees, `ExamResult` handles exam marks, `Message` handles messaging, `TimeTable` handles scheduling, `StudentDashboard` handles UI aggregation. `StudentUser` itself has only 1 extra attribute (`studentId`) and methods focused solely on *viewing* data (`viewAttendance()`, `viewResults()`, `viewFees()`). It does NOT contain methods like `payFee()`, `markAttendance()`, or `enterExamMarks()`.

**Evidence:** Compare `StudentUser` (1 attribute, 5 methods — all read-only views) with a hypothetical God-class student that would need 50+ attributes and 40+ methods spanning every module.

### Anti-Pattern 2: Feature Envy (Avoided)

**Risk:** `ReportCard.generateReportCard()` could directly access `Exam` objects, iterate through each exam's `ExamResult` list, access `GradingScale` to compute grades, compute statistics, format output, and generate PDF — "envying" the data and behavior of multiple other classes.

**How Avoided:** `ReportCard` calls `ExamResult.getResultsByStudent()` (delegating data retrieval to the class that owns the data), uses `GradingScale.mapPercentageToGradePoint()` for grade computation (delegating logic to the class that owns the grading rules), and relies on pre-computed statistics in `ExamStatistics`. Each operation stays in its owning class.

**Evidence:** `ReportCard` has no direct reference to `Exam`. It accesses exam data exclusively through `ExamResult`.

### Anti-Pattern 3: Circular Dependency (Avoided)

**Risk:** `StudentUser` → `AttendanceRecord` → `Subject` → `Course` → `Batch` → `StudentUser` would create a circular reference chain, making it impossible to test or modify any class without affecting all others.

**How Avoided:** All relationships are unidirectional via foreign key strings. `AttendanceRecord` stores `studentId` (String) — it does not hold a `StudentUser` object. `Subject` stores `courseId` (String) — it does not hold a `Course` object. Navigation is always through query methods that accept String IDs and return independent result objects. There is no object graph that loops back on itself.

**Evidence:** In the relationship diagrams, all arrows have a single direction (→). There are no bidirectional associations.

---

## 5. Reflection

### Two Strongest Aspects

**1. Inheritance Where it Matters, Type Fields Where it Doesn't:**
The design uses inheritance only where subclasses have genuinely different attributes and methods — `User` → (Admin, Teacher, StudentUser, Parent), `Exam` → (MidtermExam, FinalExam, QuizExam, AssignmentExam), `Course` → (CoreCourse, ElectiveCourse, LabCourse, ProjectCourse), `Employee` → (TeachingStaff, NonTeachingStaff, ContractStaff). For simpler type variations — payment modes, attendance report types, transaction types, timetable slot types — the design uses type discriminator fields instead of subclasses. This avoids class explosion while maintaining extensibility where it's genuinely needed. Adding a new exam type requires a new subclass (correct — it has unique attributes/methods). Adding a new payment mode requires only a new `paymentMode` value (correct — payment modes differ only in metadata, not behavior).

**2. End-to-End Traceability Through Dynamic Diagrams:**
The sequence diagrams trace complete workflows from user action to database persistence to notification delivery. The admission flow covers form retrieval → application submission → validation → approval → student creation → notification. The exam flow covers group creation → exam scheduling → mark entry with grade calculation → result publication with statistics. The fee flow covers structure lookup → defaulter checking → payment → receipt generation → financial transaction creation → notification. Combined with the state diagram, this provides complete behavioral documentation.

### Two Weakest Aspects

**1. Potential Under-Modeling of Payment/Transaction Variants:**
By flattening `FeePayment` and `FinancialTransaction` into single classes with type fields, the design sacrifices some type safety. A `ChequePayment` has cheque-specific attributes (cheque number, bank, clearance status) that don't apply to `UPI`. With a flat class, these would be nullable fields, leading to potential null-safety issues. An inheritance model would enforce at compile time that cheque-specific fields only exist on cheque payments. The trade-off (fewer classes vs. type safety) is appropriate for the scope of this assignment but would need reconsideration in production.

**2. Absence of Concurrency and Transactional Guarantees:**
The class diagram does not address concurrent operations — e.g., two teachers simultaneously marking attendance for the same subject-batch-date, or two admins processing the same student's fee. In a real multi-user system, this would require optimistic locking and transactional isolation. These constraints are not modeled in the current design.
