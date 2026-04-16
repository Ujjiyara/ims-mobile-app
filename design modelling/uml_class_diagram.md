# UML Class Diagram — Inheritance-Based (No Enumerations)

> All former enumerations are now replaced with **inheritance (generalization)** hierarchies.
> Abstract parent classes are marked with `<<abstract>>`. Subclasses inherit all parent attributes/methods and add specialized ones.

## UML Notation Cheat Sheet

### Visibility
| Symbol | Meaning |
|---|---|
| `+` | public |
| `-` | private |
| `#` | protected (inherited by subclasses) |

### Arrows
```
Inheritance:   SubClass ──────────▷ ParentClass   (hollow triangle, solid line)
Composition:   Whole ◆────── Part                  (filled diamond)
Aggregation:   Whole ◇────── Part                  (hollow diamond)
Association:   ClassA ──────▶ ClassB               (open arrow, solid)
Dependency:    ClassA - - - ▶ ClassB               (open arrow, dashed)
```

---

# PAGE 1: User Hierarchy & Authentication

> **Classes:** User (abstract), Admin, Teacher, StudentUser, Parent, Session

---

### `User` — Abstract Parent
```
┌───────────────────────────────────────────┐
│              <<abstract>>                 │
│                 User                      │
├───────────────────────────────────────────┤
│ # userId: String {PK, auto}              │
│ # firstName: String                      │
│ # lastName: String                       │
│ # email: String {unique}                 │
│ # passwordHash: String                   │
│ # phone: String                          │
│ # isActive: Boolean                      │
│ # createdAt: DateTime                    │
├───────────────────────────────────────────┤
│ + login(email, pwd): AuthToken           │
│ + logout(): void                         │
│ + updateProfile(details: Map): void      │
│ + changePassword(old, new): Boolean      │
│ + resetPassword(email): void             │
│ + getDashboard(): Dashboard              │
└───────────────────────────────────────────┘
```

### `Admin` — extends User
```
┌───────────────────────────────────────────┐
│                 Admin                     │
├───────────────────────────────────────────┤
│ - adminLevel: String                     │
│ - department: String                     │
├───────────────────────────────────────────┤
│ + manageUsers(): List<User>              │
│ + manageSystemSettings(): void           │
│ + viewAuditLogs(): List<AuditLog>        │
│ + broadcastMessage(msg): void            │
│ + getDashboard(): AdminDashboard         │
└───────────────────────────────────────────┘
```

### `Teacher` — extends User
```
┌───────────────────────────────────────────┐
│                Teacher                    │
├───────────────────────────────────────────┤
│ - employeeId: String {FK → Employee}     │
├───────────────────────────────────────────┤
│ + getAssignedSubjects(): List<Subject>   │
│ + markAttendance(subjectId): void        │
│ + enterExamMarks(examId): void           │
│ + viewTimeTable(): TimeTable             │
│ + getDashboard(): TeacherDashboard       │
└───────────────────────────────────────────┘
```

### `StudentUser` — extends User
```
┌───────────────────────────────────────────┐
│             StudentUser                   │
├───────────────────────────────────────────┤
│ - studentId: String {FK → Student}       │
├───────────────────────────────────────────┤
│ + viewAttendance(): List<AttendanceRecord>│
│ + viewResults(): List<ExamResult>        │
│ + viewFees(): List<FeePayment>           │
│ + viewTimeTable(): TimeTable             │
│ + getDashboard(): StudentDashboard       │
└───────────────────────────────────────────┘
```

### `Parent` — extends User
```
┌───────────────────────────────────────────┐
│                Parent                     │
├───────────────────────────────────────────┤
│ - wardStudentIds: List<String>           │
├───────────────────────────────────────────┤
│ + viewWardAttendance(studentId): List     │
│ + viewWardResults(studentId): List        │
│ + viewWardFees(studentId): List           │
│ + contactTeacher(teacherId, msg): Message │
│ + getDashboard(): ParentDashboard        │
└───────────────────────────────────────────┘
```

### `Session`
```
┌───────────────────────────────────────────┐
│                 Session                   │
├───────────────────────────────────────────┤
│ - sessionId: String {PK}                 │
│ - userId: String {FK → User}             │
│ - token: String                          │
│ - loginTime: DateTime                    │
│ - expiresAt: DateTime                    │
│ - isValid: Boolean                       │
├───────────────────────────────────────────┤
│ + createSession(userId, token): Session  │
│ + validateSession(token): Boolean        │
│ + terminateSession(sessionId): void      │
└───────────────────────────────────────────┘
```

### PAGE 1 — Relationships
```
INHERITANCE (▷):
  Admin ──────────▷ User
  Teacher ──────────▷ User
  StudentUser ──────────▷ User
  Parent ──────────▷ User

ASSOCIATION:
  User "1" ──────── "0..*" Session
```

---

# PAGE 2: Dashboard Hierarchy & System

> **Classes:** Dashboard (abstract), AdminDashboard, TeacherDashboard, StudentDashboard, ParentDashboard, Widget, AuditLog, SystemSettings

---

### `Dashboard` — Abstract Parent
```
┌───────────────────────────────────────────┐
│              <<abstract>>                 │
│               Dashboard                  │
├───────────────────────────────────────────┤
│ # dashboardId: String {PK}               │
│ # userId: String {FK → User}             │
│ # widgetLayout: String (JSON)            │
├───────────────────────────────────────────┤
│ + loadDashboard(userId): Dashboard       │
│ + search(query): List<Map>               │
│ + getWidgets(): List<Widget>             │
│ + customizeLayout(layout): void          │
│ + getQuickStats(): Map                   │
└───────────────────────────────────────────┘
```

### `AdminDashboard` — extends Dashboard
```
┌───────────────────────────────────────────┐
│           AdminDashboard                  │
├───────────────────────────────────────────┤
│ - totalStudents: Integer                 │
│ - totalEmployees: Integer                │
├───────────────────────────────────────────┤
│ + getAdmissionStats(): Map               │
│ + getRevenueOverview(): Map              │
│ + getRecentActivity(): List<AuditLog>    │
└───────────────────────────────────────────┘
```

### `TeacherDashboard` — extends Dashboard
```
┌───────────────────────────────────────────┐
│          TeacherDashboard                 │
├───────────────────────────────────────────┤
│ - pendingAttendance: Integer             │
├───────────────────────────────────────────┤
│ + getTodaySchedule(): List<TimeTableSlot>│
│ + getPendingMarkEntry(): List<Exam>      │
└───────────────────────────────────────────┘
```

### `StudentDashboard` — extends Dashboard
```
┌───────────────────────────────────────────┐
│          StudentDashboard                 │
├───────────────────────────────────────────┤
│ - attendancePercentage: Float            │
│ - currentGPA: Float                      │
│ - pendingFees: Float                     │
├───────────────────────────────────────────┤
│ + getAttendanceSummary(): Map            │
│ + getRecentResults(): List<ExamResult>   │
│ + getFeeStatus(): Map                    │
└───────────────────────────────────────────┘
```

### `ParentDashboard` — extends Dashboard
```
┌───────────────────────────────────────────┐
│          ParentDashboard                  │
├───────────────────────────────────────────┤
│ - wardCount: Integer                     │
├───────────────────────────────────────────┤
│ + getWardOverview(): Map                 │
│ + getWardAttendance(): Map               │
└───────────────────────────────────────────┘
```

### `Widget`
```
┌───────────────────────────────────────────┐
│                 Widget                    │
├───────────────────────────────────────────┤
│ - widgetId: String {PK}                  │
│ - dashboardId: String {FK → Dashboard}   │
│ - widgetName: String                     │
│ - widgetType: String                     │
│ - dataSource: String                     │
├───────────────────────────────────────────┤
│ + render(): Map                          │
│ + refresh(): void                        │
└───────────────────────────────────────────┘
```

### `AuditLog`
```
┌───────────────────────────────────────────┐
│                AuditLog                   │
├───────────────────────────────────────────┤
│ - logId: String {PK}                     │
│ - userId: String {FK → User}             │
│ - action: String                         │
│ - entityType: String                     │
│ - entityId: String                       │
│ - timestamp: DateTime                    │
├───────────────────────────────────────────┤
│ + createEntry(userId, action, entity): void │
│ + getLogsByUser(userId): List<AuditLog>    │
│ + getLogsByDateRange(from, to): List<AuditLog> │
└───────────────────────────────────────────┘
```

### `SystemSettings`
```
┌───────────────────────────────────────────┐
│             SystemSettings                │
├───────────────────────────────────────────┤
│ - settingsId: String {PK}                │
│ - instituteName: String                  │
│ - currency: String                       │
│ - defaultLanguage: String                │
│ - gradingSystem: String                  │
│ - academicYearStart: Date                │
│ - academicYearEnd: Date                  │
│ - autoIdPrefix: String                   │
├───────────────────────────────────────────┤
│ + updateSettings(key, value): void       │
│ + getSettings(): SystemSettings          │
│ + generateUniqueId(prefix): String       │
└───────────────────────────────────────────┘
```

### PAGE 2 — Relationships
```
INHERITANCE (▷):
  AdminDashboard ──────────▷ Dashboard
  TeacherDashboard ──────────▷ Dashboard
  StudentDashboard ──────────▷ Dashboard
  ParentDashboard ──────────▷ Dashboard

COMPOSITION (◆):
  Dashboard "1" ◆──────── "0..*" Widget

ASSOCIATION:
  Dashboard "0..*" ──────── "1" User  ← (Page 1)
  AuditLog "0..*" ──────── "1" User  ← (Page 1)

DEPENDENCY (- - -▶):
  AdminDashboard - - -▶ AuditLog
```

---

# PAGE 3: Notification Hierarchy

> **Classes:** Notification (abstract), AlertNotification, FeeReminderNotification, ExamResultNotification, AttendanceWarningNotification, AdmissionNotification

---

### `Notification` — Abstract Parent
```
┌───────────────────────────────────────────┐
│              <<abstract>>                 │
│             Notification                  │
├───────────────────────────────────────────┤
│ # notificationId: String {PK}            │
│ # recipientId: String {FK → User}        │
│ # title: String                          │
│ # body: String                           │
│ # isRead: Boolean                        │
│ # createdAt: DateTime                    │
├───────────────────────────────────────────┤
│ + send(): void                           │
│ + markAsRead(): void                     │
│ + getUnread(userId): List<Notification>  │
└───────────────────────────────────────────┘
```

### `AlertNotification` — extends Notification
```
┌───────────────────────────────────────────┐
│          AlertNotification                │
├───────────────────────────────────────────┤
│ - severity: String                       │
└───────────────────────────────────────────┘
```

### `FeeReminderNotification` — extends Notification
```
┌───────────────────────────────────────────┐
│       FeeReminderNotification             │
├───────────────────────────────────────────┤
│ - amountDue: Float                       │
│ - dueDate: Date                          │
└───────────────────────────────────────────┘
```

### `ExamResultNotification` — extends Notification
```
┌───────────────────────────────────────────┐
│      ExamResultNotification               │
├───────────────────────────────────────────┤
│ - examId: String {FK → Exam}             │
│ - grade: String                          │
└───────────────────────────────────────────┘
```

### `AttendanceWarningNotification` — extends Notification
```
┌───────────────────────────────────────────┐
│   AttendanceWarningNotification           │
├───────────────────────────────────────────┤
│ - currentPercentage: Float               │
│ - thresholdPercentage: Float             │
└───────────────────────────────────────────┘
```

### `AdmissionNotification` — extends Notification
```
┌───────────────────────────────────────────┐
│       AdmissionNotification               │
├───────────────────────────────────────────┤
│ - applicationId: String {FK}             │
│ - admissionStatus: String                │
└───────────────────────────────────────────┘
```

### PAGE 3 — Relationships
```
INHERITANCE (▷):
  AlertNotification ──────────▷ Notification
  FeeReminderNotification ──────────▷ Notification
  ExamResultNotification ──────────▷ Notification
  AttendanceWarningNotification ──────────▷ Notification
  AdmissionNotification ──────────▷ Notification

ASSOCIATION:
  Notification "0..*" ──────── "1" User  ← (Page 1)
```

---

# PAGE 4: Admission Module

> **Classes:** AdmissionForm, FormField, AdmissionApplication, EducationHistory, StudentDocument

---

### `AdmissionForm`
```
┌───────────────────────────────────────────┐
│             AdmissionForm                 │
├───────────────────────────────────────────┤
│ - formId: String {PK}                    │
│ - templateName: String                   │
│ - version: Integer                       │
│ - isActive: Boolean                      │
│ - createdBy: String {FK → User}          │
├───────────────────────────────────────────┤
│ + createTemplate(name, fields): AdmissionForm │
│ + updateTemplate(formId, fields): void       │
│ + activateTemplate(formId): void             │
│ + getActiveTemplate(): AdmissionForm         │
│ + getFields(): List<FormField>               │
└───────────────────────────────────────────┘
```

### `FormField`
```
┌───────────────────────────────────────────┐
│               FormField                   │
├───────────────────────────────────────────┤
│ - fieldId: String {PK}                   │
│ - formId: String {FK → AdmissionForm}    │
│ - fieldName: String                      │
│ - fieldType: String                      │
│ - isRequired: Boolean                    │
│ - validationRule: String                 │
│ - displayOrder: Integer                  │
├───────────────────────────────────────────┤
│ + validate(value): Boolean               │
└───────────────────────────────────────────┘
```

### `AdmissionApplication`
```
┌───────────────────────────────────────────┐
│          AdmissionApplication             │
├───────────────────────────────────────────┤
│ - applicationId: String {PK, auto}       │
│ - formId: String {FK → AdmissionForm}    │
│ - applicationNumber: String {unique}     │
│ - studentFirstName: String               │
│ - studentLastName: String                │
│ - dateOfBirth: Date                      │
│ - gender: String                         │
│ - address: String                        │
│ - appliedBatchId: String {FK → Batch}    │
│ - appliedCourseId: String {FK → Course}  │
│ - status: String                         │
│ - reviewedBy: String {FK → User}         │
│ - fieldValues: Map<String, String>       │
│ - academicYear: String                   │
├───────────────────────────────────────────┤
│ + submitApplication(formData): AdmissionApplication │
│ + approveApplication(appId): void                   │
│ + rejectApplication(appId, reason): void            │
│ + waitlistApplication(appId): void                  │
│ + enrollStudent(appId): String                      │
│ + getApplicationsByStatus(status): List              │
│ + searchApplications(filters): List                  │
└───────────────────────────────────────────┘
```



### `EducationHistory`
```
┌───────────────────────────────────────────┐
│            EducationHistory               │
├───────────────────────────────────────────┤
│ - historyId: String {PK}                 │
│ - studentId: String {FK → Student}       │
│ - institutionName: String                │
│ - fromDate: Date                         │
│ - toDate: Date                           │
│ - gradeCompleted: String                 │
│ - percentage: Float                      │
├───────────────────────────────────────────┤
│ + addRecord(studentId, details): EducationHistory │
│ + getHistoryByStudent(studentId): List            │
└───────────────────────────────────────────┘
```

### `StudentDocument`
```
┌───────────────────────────────────────────┐
│            StudentDocument                │
├───────────────────────────────────────────┤
│ - documentId: String {PK}                │
│ - studentId: String {FK → Student}       │
│ - documentType: String                   │
│ - documentName: String                   │
│ - fileUrl: String                        │
│ - isVerified: Boolean                    │
├───────────────────────────────────────────┤
│ + uploadDocument(studentId, file): StudentDocument │
│ + getDocuments(studentId): List                    │
│ + verifyDocument(documentId): void                 │
└───────────────────────────────────────────┘
```

### PAGE 4 — Relationships
```
COMPOSITION (◆):
  AdmissionForm "1" ◆──────── "1..*" FormField

ASSOCIATION:
  AdmissionApplication "0..*" ──────── "1" AdmissionForm
  AdmissionApplication "0..*" ──────── "1" Batch  ← (Page 6)
  AdmissionApplication "0..*" ──────── "1" Course  ← (Page 6)
  AdmissionApplication "0..*" ──────── "1" User  ← (Page 1) (reviewedBy)

  Student "1" ──────── "0..*" EducationHistory
  Student "1" ──────── "0..*" StudentDocument
```

---

# PAGE 5: Academic Structure

> **Classes:** Department, Course (abstract), CoreCourse, ElectiveCourse, LabCourse, ProjectCourse, Batch, Subject

---


### `Department`
```
┌───────────────────────────────────────────┐
│              Department                   │
├───────────────────────────────────────────┤
│ - departmentId: String {PK}              │
│ - departmentName: String                 │
│ - departmentCode: String {unique}        │
│ - headOfDepartment: String {FK → Employee}│
│ - isActive: Boolean                      │
├───────────────────────────────────────────┤
│ + createDepartment(details): Department   │
│ + updateDepartment(deptId, details): void │
│ + listDepartments(): List<Department>    │
│ + assignHead(deptId, empId): void        │
└───────────────────────────────────────────┘
```

### `Course` — Abstract Parent
```
┌───────────────────────────────────────────┐
│              <<abstract>>                 │
│                Course                     │
├───────────────────────────────────────────┤
│ # courseId: String {PK}                  │
│ # courseName: String                     │
│ # courseCode: String {unique}            │
│ # creditHours: Integer                   │
│ # departmentId: String {FK → Department} │
│ # prerequisiteCourseId: String {FK, nullable} │
│ # maxIntake: Integer                     │
│ # isActive: Boolean                      │
├───────────────────────────────────────────┤
│ + createCourse(details): Course           │
│ + updateCourse(courseId, details): void   │
│ + listCourses(filters): List<Course>     │
│ + getSubjects(courseId): List<Subject>    │
└───────────────────────────────────────────┘
```

### `CoreCourse` — extends Course
```
┌───────────────────────────────────────────┐
│             CoreCourse                    │
├───────────────────────────────────────────┤
│ - isMandatory: Boolean                   │
│ - minimumPassGrade: String               │
└───────────────────────────────────────────┘
```

### `ElectiveCourse` — extends Course
```
┌───────────────────────────────────────────┐
│           ElectiveCourse                  │
├───────────────────────────────────────────┤
│ - electiveGroup: String                  │
│ - minStudentsToRun: Integer              │
│ - registrationDeadline: Date             │
├───────────────────────────────────────────┤
│ + isOpen(): Boolean                      │
└───────────────────────────────────────────┘
```

### `LabCourse` — extends Course
```
┌───────────────────────────────────────────┐
│              LabCourse                    │
├───────────────────────────────────────────┤
│ - labRoomNumber: String                  │
│ - labCapacity: Integer                   │
│ - equipmentRequired: String              │
└───────────────────────────────────────────┘
```

### `ProjectCourse` — extends Course
```
┌───────────────────────────────────────────┐
│           ProjectCourse                   │
├───────────────────────────────────────────┤
│ - maxTeamSize: Integer                   │
│ - submissionDeadline: Date               │
│ - guideId: String {FK → Employee}        │
└───────────────────────────────────────────┘
```

### `Batch`
```
┌───────────────────────────────────────────┐
│                 Batch                     │
├───────────────────────────────────────────┤
│ - batchId: String {PK}                   │
│ - batchName: String                      │
│ - courseId: String {FK → Course}          │
│ - startDate: Date                        │
│ - endDate: Date                          │
│ - maxStrength: Integer                   │
│ - currentStrength: Integer               │
│ - classTeacherId: String {FK → Employee} │
│ - isActive: Boolean                      │
├───────────────────────────────────────────┤
│ + createBatch(details): Batch             │
│ + updateBatch(batchId, details): void    │
│ + listBatches(filters): List<Batch>      │
│ + getStudentsInBatch(batchId): List       │
│ + assignClassTeacher(batchId, empId): void│
└───────────────────────────────────────────┘
```

### `Subject`
```
┌───────────────────────────────────────────┐
│                Subject                    │
├───────────────────────────────────────────┤
│ - subjectId: String {PK}                 │
│ - subjectName: String                    │
│ - subjectCode: String {unique}           │
│ - courseId: String {FK → Course}          │
│ - teacherId: String {FK → Employee}      │
│ - batchId: String {FK → Batch}           │
│ - semester: Integer                      │
│ - maxMarks: Float                        │
│ - passMarks: Float                       │
├───────────────────────────────────────────┤
│ + createSubject(details): Subject         │
│ + getSubjectsByCourse(courseId): List     │
│ + getSubjectsByBatch(batchId): List      │
│ + assignTeacher(subjectId, teacherId): void│
└───────────────────────────────────────────┘
```

### PAGE 5 — Relationships
```
INHERITANCE (▷):
  CoreCourse ──────────▷ Course
  ElectiveCourse ──────────▷ Course
  LabCourse ──────────▷ Course
  ProjectCourse ──────────▷ Course

AGGREGATION (◇):
  Department "1" ◇──────── "0..*" Course
  Course "1" ◇──────── "1..*" Subject
  Batch "1" ◇──────── "0..*" StudentUser  ← (Page 1)

ASSOCIATION:
  Course "0..1" ──────── "0..1" Course (self: prerequisite)
  Batch "0..*" ──────── "1" Course
  Subject "0..*" ──────── "1" Employee  ← (Page 7)
  Department "0..1" ──────── "1" Employee  ← (Page 7) (head)
  Batch "0..1" ──────── "1" Employee  ← (Page 7) (classTeacher)
```

---

# PAGE 6: Examination Module

> **Classes:** Exam (abstract), MidtermExam, FinalExam, QuizExam, AssignmentExam, ExamGroup, ExamResult, GradingScale, ReportCard

---

### `Exam` — Abstract Parent
```
┌───────────────────────────────────────────┐
│              <<abstract>>                 │
│                 Exam                      │
├───────────────────────────────────────────┤
│ # examId: String {PK}                    │
│ # examName: String                       │
│ # subjectId: String {FK → Subject}       │
│ # batchId: String {FK → Batch}           │
│ # scheduledDate: Date                    │
│ # startTime: Time                        │
│ # endTime: Time                          │
│ # maxMarks: Float                        │
│ # passingMarks: Float                    │
│ # weightage: Float                       │
│ # examGroupId: String {FK, nullable}     │
│ # status: String                         │
├───────────────────────────────────────────┤
│ + createExam(details): Exam               │
│ + updateExam(examId, details): void      │
│ + cancelExam(examId, reason): void       │
│ + getExamsByBatch(batchId): List<Exam>   │
│ + getExamsBySubject(subId): List<Exam>   │
│ + publishResults(examId): void           │
└───────────────────────────────────────────┘
```

### `MidtermExam` — extends Exam
```
┌───────────────────────────────────────────┐
│             MidtermExam                   │
├───────────────────────────────────────────┤
│ - syllabusFrom: String                   │
│ - syllabusTo: String                     │
└───────────────────────────────────────────┘
```

### `FinalExam` — extends Exam
```
┌───────────────────────────────────────────┐
│              FinalExam                    │
├───────────────────────────────────────────┤
│ - minimumAttendanceRequired: Float       │
│ - requiresHallTicket: Boolean            │
├───────────────────────────────────────────┤
│ + checkEligibility(studentId): Boolean   │
│ + generateHallTicket(studentId): File    │
└───────────────────────────────────────────┘
```

### `QuizExam` — extends Exam
```
┌───────────────────────────────────────────┐
│              QuizExam                     │
├───────────────────────────────────────────┤
│ - quizNumber: Integer                    │
│ - isSurprise: Boolean                    │
│ - durationMinutes: Integer               │
└───────────────────────────────────────────┘
```

### `AssignmentExam` — extends Exam
```
┌───────────────────────────────────────────┐
│           AssignmentExam                  │
├───────────────────────────────────────────┤
│ - submissionDeadline: DateTime           │
│ - isGroupAssignment: Boolean             │
│ - latePenaltyPerDay: Float               │
└───────────────────────────────────────────┘
```

### `ExamGroup`
```
┌───────────────────────────────────────────┐
│              ExamGroup                    │
├───────────────────────────────────────────┤
│ - examGroupId: String {PK}               │
│ - groupName: String                      │
│ - batchId: String {FK → Batch}           │
│ - term: String                           │
├───────────────────────────────────────────┤
│ + createGroup(details): ExamGroup        │
│ + addExamToGroup(groupId, examId): void  │
│ + getExamsInGroup(groupId): List<Exam>   │
└───────────────────────────────────────────┘
```

### `ExamResult`
```
┌───────────────────────────────────────────┐
│              ExamResult                   │
├───────────────────────────────────────────┤
│ - resultId: String {PK}                  │
│ - examId: String {FK → Exam}             │
│ - studentId: String {FK → Student}       │
│ - marksObtained: Float                   │
│ - grade: String                          │
│ - gradePoint: Float                      │
│ - isAbsent: Boolean                      │
├───────────────────────────────────────────┤
│ + enterMarks(examId, studentId, marks): ExamResult │
│ + enterBulkMarks(examId, marksMap): List           │
│ + getResultsByExam(examId): List                    │
│ + getResultsByStudent(studentId): List               │
│ + calculateGrade(marks, maxMarks): String           │
└───────────────────────────────────────────┘
```

### `GradingScale`
```
┌───────────────────────────────────────────┐
│             GradingScale                  │
├───────────────────────────────────────────┤
│ - scaleId: String {PK}                   │
│ - scaleName: String                      │
│ - gradeName: String                      │
│ - minPercentage: Float                   │
│ - maxPercentage: Float                   │
│ - gradePoint: Float                      │
├───────────────────────────────────────────┤
│ + createScale(details): GradingScale     │
│ + mapPercentageToGrade(pct): String      │
└───────────────────────────────────────────┘
```

### `ReportCard`
```
┌───────────────────────────────────────────┐
│              ReportCard                   │
├───────────────────────────────────────────┤
│ - reportCardId: String {PK}              │
│ - studentId: String {FK → Student}       │
│ - batchId: String {FK → Batch}           │
│ - academicYear: String                   │
│ - semester: Integer                      │
│ - gpa: Float                            │
│ - cgpa: Float                           │
│ - cwa: Float                            │
│ - overallPercentage: Float               │
│ - overallGrade: String                   │
│ - teacherRemarks: String                 │
├───────────────────────────────────────────┤
│ + generateReportCard(studentId, year, sem): ReportCard │
│ + generateBatchReportCards(batchId, year): List        │
│ + calculateGPA(studentId, year): Float                 │
│ + calculateCGPA(studentId): Float                      │
│ + calculateCWA(studentId, year): Float                 │
│ + exportReportCardPDF(reportCardId): File              │
└───────────────────────────────────────────┘
```

### PAGE 6 — Relationships
```
INHERITANCE (▷):
  MidtermExam ──────────▷ Exam
  FinalExam ──────────▷ Exam
  QuizExam ──────────▷ Exam
  AssignmentExam ──────────▷ Exam

COMPOSITION (◆):
  ExamGroup "1" ◆──────── "1..*" Exam

ASSOCIATION:
  Exam "0..*" ──────── "1" Subject  ← (Page 5)
  Exam "0..*" ──────── "1" Batch  ← (Page 5)
  Exam "1" ──────── "0..*" ExamResult
  ExamResult "0..*" ──────── "1" StudentUser  ← (Page 1)
  ReportCard "0..*" ──────── "1" StudentUser  ← (Page 1)
  ReportCard "0..*" ──────── "1" Batch  ← (Page 5)

DEPENDENCY (- - -▶):
  ExamResult - - -▶ GradingScale
  ReportCard - - -▶ ExamResult
```

---

# PAGE 7: HR, Employee, Payroll & Leave Module

> **Classes:** Employee (abstract), TeachingStaff, NonTeachingStaff, ContractStaff, Payroll, Payslip, Leave

---

### `Employee` — Abstract Parent
```
┌───────────────────────────────────────────┐
│              <<abstract>>                 │
│               Employee                    │
├───────────────────────────────────────────┤
│ # employeeId: String {PK, auto}          │
│ # userId: String {FK → User}             │
│ # departmentId: String {FK → Department} │
│ # designation: String                    │
│ # dateOfJoining: Date                    │
│ # dateOfExit: Date {nullable}            │
│ # qualifications: String                 │
│ # bankAccountNo: String                  │
│ # panNumber: String                      │
│ # basicSalary: Float                     │
│ # isActive: Boolean                      │
├───────────────────────────────────────────┤
│ + hireEmployee(details): Employee         │
│ + updateEmployee(empId, details): void    │
│ + terminateEmployee(empId, reason): void  │
│ + getEmployeeById(empId): Employee        │
│ + searchEmployees(query): List<Employee>  │
│ + getByDepartment(deptId): List<Employee> │
└───────────────────────────────────────────┘
```

### `TeachingStaff` — extends Employee
```
┌───────────────────────────────────────────┐
│            TeachingStaff                  │
├───────────────────────────────────────────┤
│ - subjectsTaught: List<String>           │
│ - maxHoursPerWeek: Integer               │
├───────────────────────────────────────────┤
│ + getAssignedSubjects(): List<Subject>   │
│ + assignSubject(subjectId): void         │
└───────────────────────────────────────────┘
```

### `NonTeachingStaff` — extends Employee
```
┌───────────────────────────────────────────┐
│          NonTeachingStaff                 │
├───────────────────────────────────────────┤
│ - staffRole: String                      │
│ - shiftTiming: String                    │
│ - reportingTo: String {FK → Employee}    │
└───────────────────────────────────────────┘
```

### `ContractStaff` — extends Employee
```
┌───────────────────────────────────────────┐
│           ContractStaff                   │
├───────────────────────────────────────────┤
│ - contractStartDate: Date                │
│ - contractEndDate: Date                  │
│ - contractAmount: Float                  │
├───────────────────────────────────────────┤
│ + isContractActive(): Boolean            │
│ + renewContract(newEndDate): void        │
└───────────────────────────────────────────┘
```

### `Payroll`
```
┌───────────────────────────────────────────┐
│               Payroll                     │
├───────────────────────────────────────────┤
│ - payrollId: String {PK}                 │
│ - employeeId: String {FK → Employee}     │
│ - month: Integer                         │
│ - basicSalary: Float                     │
│ - totalAllowances: Float                 │
│ - grossSalary: Float                     │
│ - netSalary: Float                       │
│ - status: String                         │
│ - generatedBy: String {FK → User}        │
│ - approvedBy: String {FK → User, nullable}│
├───────────────────────────────────────────┤
│ + generatePayroll(empId, month, year): Payroll │
│ + generateBulkPayroll(month, year): List       │
│ + approvePayroll(payrollId, approverId): void  │
│ + rejectPayroll(payrollId, reason): void       │
│ + getPayrollHistory(empId): List<Payroll>      │
│ + getPendingPayrolls(): List<Payroll>           │
└───────────────────────────────────────────┘
```

### `Payslip`
```
┌───────────────────────────────────────────┐
│               Payslip                     │
├───────────────────────────────────────────┤
│ - payslipId: String {PK}                 │
│ - payrollId: String {FK → Payroll}       │
│ - employeeId: String {FK → Employee}     │
│ - pdfUrl: String                         │
│ - isAuthenticated: Boolean               │
├───────────────────────────────────────────┤
│ + generatePayslip(payrollId): Payslip     │
│ + authenticatePayslip(payslipId): void   │
│ + downloadPayslip(payslipId): File       │
└───────────────────────────────────────────┘
```

### `Leave` 
```
┌───────────────────────────────────────────┐
│                Leave                      │
├───────────────────────────────────────────┤
│ - leaveId: String {PK}                   │
│ - employeeId: String {FK → Employee}     │
│ - fromDate: Date                         │
│ - toDate: Date                           │
│ - totalDays: Integer                     │
│ - reason: String                         │
│ - status: String                         │
│ - appliedAt: DateTime                    │
│ - approvedBy: String {FK → User, nullable}│
├───────────────────────────────────────────┤
│ + applyLeave(empId, details): Leave       │
│ + approveLeave(leaveId, approverId): void│
│ + rejectLeave(leaveId, reason): void     │
│ + cancelLeave(leaveId): void             │
│ + getLeavesByEmployee(empId): List<Leave>│
│ + getLeaveBalance(empId): Map            │
│ + getMaxAllowedDays(): Integer           │
└───────────────────────────────────────────┘
```


### PAGE 7 — Relationships
```
INHERITANCE (▷):
  TeachingStaff ──────────▷ Employee
  NonTeachingStaff ──────────▷ Employee
  ContractStaff ──────────▷ Employee

COMPOSITION (◆):
  Payroll "1" ◆──────── "0..1" Payslip

ASSOCIATION:
  Employee "1" ──────── "1" User  ← (Page 1)
  Employee "0..*" ──────── "1" Department  ← (Page 5)
  Employee "1" ──────── "0..*" Payroll
  Employee "1" ──────── "0..*" Leave
  Payroll "0..*" ──────── "1" User  ← (Page 1) (generatedBy)
  Leave "0..*" ──────── "1" User  ← (Page 1) (approvedBy)
```

---

# PAGE 8: Attendance Module

> **Classes:** AttendanceRecord, AttendanceReport

---

### `AttendanceRecord`
```
┌───────────────────────────────────────────┐
│           AttendanceRecord                │
├───────────────────────────────────────────┤
│ - attendanceId: String {PK}              │
│ - studentId: String {FK → StudentUser}   │
│ - subjectId: String {FK → Subject}       │
│ - batchId: String {FK → Batch}           │
│ - date: Date                             │
│ - status: String                         │
│ - markedBy: String {FK → User}           │
├───────────────────────────────────────────┤
│ + markAttendance(studentId, subjectId, date, status): AttendanceRecord │
│ + bulkMarkAttendance(subjectId, date, statusMap): List                 │
│ + updateAttendance(attendanceId, status): void                         │
│ + getByStudent(studentId, from, to): List                               │
│ + getBySubjectAndDate(subjectId, date): List                            │
└───────────────────────────────────────────┘
```

### `AttendanceReport`
```
┌───────────────────────────────────────────┐
│          AttendanceReport                 │
├───────────────────────────────────────────┤
│ - reportId: String {PK}                  │
│ - reportType: String                     │
│ - studentId: String {FK, nullable}       │
│ - batchId: String {FK → Batch}           │
│ - fromDate: Date                         │
│ - toDate: Date                           │
│ - totalClasses: Integer                  │
│ - presentCount: Integer                  │
│ - attendancePercentage: Float            │
├───────────────────────────────────────────┤
│ + generateReport(type, filters): AttendanceReport │
│ + getDefaulters(batchId, threshold): List          │
│ + exportReport(reportId): File                     │
└───────────────────────────────────────────┘
```

### PAGE 8 — Relationships
```
ASSOCIATION:
  AttendanceRecord "0..*" ──────── "1" StudentUser  ← (Page 1)
  AttendanceRecord "0..*" ──────── "1" Subject  ← (Page 5)
  AttendanceRecord "0..*" ──────── "1" Batch  ← (Page 5)
  AttendanceRecord "0..*" ──────── "1" User  ← (Page 1) (markedBy)
  AttendanceReport "0..*" ──────── "0..1" StudentUser  ← (Page 1)
  AttendanceReport "0..*" ──────── "1" Batch  ← (Page 5)

DEPENDENCY (- - -▶):
  AttendanceReport - - -▶ AttendanceRecord
```

---

# PAGE 9: Finance Module

> **Classes:** FeeStructure, FeePayment, FinancialTransaction, FinancialReport

---

### `FeeStructure`
```
┌───────────────────────────────────────────┐
│             FeeStructure                  │
├───────────────────────────────────────────┤
│ - feeStructureId: String {PK}            │
│ - batchId: String {FK → Batch}           │
│ - feeCategory: String                    │
│ - amount: Float                          │
│ - dueDate: Date                          │
│ - academicYear: String                   │
│ - isActive: Boolean                      │
├───────────────────────────────────────────┤
│ + createFeeStructure(details): FeeStructure │
│ + getFeeStructureByBatch(batchId): List     │
└───────────────────────────────────────────┘
```

### `FeePayment`
```
┌───────────────────────────────────────────┐
│             FeePayment                    │
├───────────────────────────────────────────┤
│ - paymentId: String {PK}                 │
│ - studentId: String {FK → StudentUser}   │
│ - feeStructureId: String {FK}            │
│ - amountPaid: Float                      │
│ - paymentDate: DateTime                  │
│ - paymentMode: String                    │
│ - receiptNumber: String {unique, auto}   │
│ - status: String                         │
│ - collectedBy: String {FK → User}        │
├───────────────────────────────────────────┤
│ + collectFee(studentId, feeId, amount): FeePayment │
│ + getPaymentsByStudent(studentId): List             │
│ + getDefaulters(batchId): List                      │
│ + generateReceipt(paymentId): File                  │
└───────────────────────────────────────────┘
```

### `FinancialTransaction`
```
┌───────────────────────────────────────────┐
│        FinancialTransaction               │
├───────────────────────────────────────────┤
│ - transactionId: String {PK}             │
│ - transactionType: String                │
│ - category: String                       │
│ - amount: Float                          │
│ - date: Date                             │
│ - description: String                    │
│ - createdBy: String {FK → User}          │
├───────────────────────────────────────────┤
│ + createTransaction(details): FinancialTransaction │
│ + getByDateRange(from, to): List                   │
│ + getByCategory(cat): List                          │
└───────────────────────────────────────────┘
```

### `FinancialReport`
```
┌───────────────────────────────────────────┐
│           FinancialReport                 │
├───────────────────────────────────────────┤
│ - reportId: String {PK}                  │
│ - fromDate: Date                         │
│ - toDate: Date                           │
│ - totalIncome: Float                     │
│ - totalExpense: Float                    │
│ - netBalance: Float                      │
│ - generatedBy: String {FK → User}        │
├───────────────────────────────────────────┤
│ + generateReport(from, to): FinancialReport │
│ + exportPDF(reportId): File                 │
└───────────────────────────────────────────┘
```

### PAGE 9 — Relationships
```
ASSOCIATION:
  FeeStructure "0..*" ──────── "1" Batch  ← (Page 5)
  FeePayment "0..*" ──────── "1" StudentUser  ← (Page 1)
  FeePayment "0..*" ──────── "1" FeeStructure

DEPENDENCY (- - -▶):
  FeePayment - - -▶ FinancialTransaction
  FinancialReport - - -▶ FinancialTransaction
```

---

# PAGE 10: Messaging, News & TimeTable Module

> **Classes:** Message, Broadcast, SMSNotification, News, Comment, TimeTable, TimeTableSlot, WorkloadTracker

---

### `Message`
```
┌───────────────────────────────────────────┐
│               Message                     │
├───────────────────────────────────────────┤
│ - messageId: String {PK}                 │
│ - senderId: String {FK → User}           │
│ - receiverId: String {FK → User}         │
│ - subject: String                        │
│ - body: String                           │
│ - isRead: Boolean                        │
│ - parentMessageId: String {FK, nullable} │
├───────────────────────────────────────────┤
│ + sendMessage(senderId, receiverId, subject, body): Message │
│ + replyToMessage(parentMsgId, body): Message                │
│ + getInbox(userId): List<Message>                            │
│ + markAsRead(messageId): void                                │
└───────────────────────────────────────────┘
```

### `Broadcast`
```
┌───────────────────────────────────────────┐
│              Broadcast                    │
├───────────────────────────────────────────┤
│ - broadcastId: String {PK}               │
│ - senderId: String {FK → User}           │
│ - subject: String                        │
│ - body: String                           │
│ - targetAudience: String                 │
├───────────────────────────────────────────┤
│ + sendBroadcast(senderId, subject, body, audience): Broadcast │
│ + getBroadcasts(audience): List<Broadcast>                    │
└───────────────────────────────────────────┘
```

### `SMSNotification`
```
┌───────────────────────────────────────────┐
│           SMSNotification                 │
├───────────────────────────────────────────┤
│ - smsId: String {PK}                     │
│ - recipientPhone: String                 │
│ - message: String                        │
│ - status: String                         │
│ - senderId: String {FK → User}           │
├───────────────────────────────────────────┤
│ + sendSMS(phone, message): SMSNotification │
│ + sendBulkSMS(phoneList, message): List     │
└───────────────────────────────────────────┘
```

### `News`
```
┌───────────────────────────────────────────┐
│                 News                      │
├───────────────────────────────────────────┤
│ - newsId: String {PK}                    │
│ - title: String                          │
│ - content: String (RTF)                  │
│ - authorId: String {FK → User}           │
│ - category: String                       │
│ - isPublished: Boolean                   │
├───────────────────────────────────────────┤
│ + createNews(title, content, authorId): News │
│ + publishNews(newsId): void                  │
│ + deleteNews(newsId): void                   │
│ + getPublishedNews(): List<News>              │
└───────────────────────────────────────────┘
```

### `Comment`
```
┌───────────────────────────────────────────┐
│               Comment                     │
├───────────────────────────────────────────┤
│ - commentId: String {PK}                 │
│ - newsId: String {FK → News}             │
│ - userId: String {FK → User}             │
│ - commentText: String                    │
│ - isModerated: Boolean                   │
├───────────────────────────────────────────┤
│ + addComment(newsId, userId, text): Comment │
│ + deleteComment(commentId): void            │
│ + moderateComment(commentId): void          │
└───────────────────────────────────────────┘
```

### `TimeTable`
```
┌───────────────────────────────────────────┐
│              TimeTable                    │
├───────────────────────────────────────────┤
│ - timetableId: String {PK}               │
│ - batchId: String {FK → Batch}           │
│ - academicYear: String                   │
│ - effectiveFrom: Date                    │
│ - effectiveTo: Date                      │
│ - isActive: Boolean                      │
├───────────────────────────────────────────┤
│ + createTimeTable(batchId, year): TimeTable │
│ + activateTimeTable(ttId): void            │
│ + getActiveTimeTable(batchId): TimeTable    │
│ + getSlots(ttId): List<TimeTableSlot>      │
└───────────────────────────────────────────┘
```

### `TimeTableSlot`
```
┌───────────────────────────────────────────┐
│            TimeTableSlot                  │
├───────────────────────────────────────────┤
│ - slotId: String {PK}                    │
│ - timetableId: String {FK → TimeTable}   │
│ - dayOfWeek: String                      │
│ - startTime: Time                        │
│ - endTime: Time                          │
│ - slotType: String                       │
│ - subjectId: String {FK → Subject, nullable}│
│ - teacherId: String {FK → Employee, nullable}│
│ - roomNumber: String                     │
├───────────────────────────────────────────┤
│ + createSlot(ttId, details): TimeTableSlot │
│ + deleteSlot(slotId): void                 │
│ + moveSlot(slotId, newDay, start, end): TimeTableSlot │
└───────────────────────────────────────────┘
```

### `WorkloadTracker`
```
┌───────────────────────────────────────────┐
│           WorkloadTracker                 │
├───────────────────────────────────────────┤
│ - trackerId: String {PK}                 │
│ - employeeId: String {FK → Employee}     │
│ - totalHours: Float                      │
│ - maxHoursPerWeek: Float                 │
├───────────────────────────────────────────┤
│ + calculateWorkload(empId): WorkloadTracker │
│ + checkWorkloadLimit(empId): Boolean        │
└───────────────────────────────────────────┘
```

### PAGE 10 — Relationships
```
COMPOSITION (◆):
  TimeTable "1" ◆──────── "0..*" TimeTableSlot
  News "1" ◆──────── "0..*" Comment

ASSOCIATION:
  Message "0..*" ──────── "1" User  ← (Page 1) (sender)
  Message "0..*" ──────── "0..1" User  ← (Page 1) (receiver)
  Message "0..*" ──────── "0..1" Message (self: parentMessage)
  Broadcast "0..*" ──────── "1" User  ← (Page 1) (sender)
  SMSNotification "0..*" ──────── "1" User  ← (Page 1)
  News "0..*" ──────── "1" User  ← (Page 1) (author)
  Comment "0..*" ──────── "1" User  ← (Page 1)
  TimeTable "0..*" ──────── "1" Batch  ← (Page 5)
  TimeTableSlot "0..*" ──────── "0..1" Subject  ← (Page 5)
  TimeTableSlot "0..*" ──────── "0..1" Employee  ← (Page 7)
  WorkloadTracker "0..*" ──────── "1" Employee  ← (Page 7)

DEPENDENCY (- - -▶):
  WorkloadTracker - - -▶ TimeTableSlot
```

---

# Final Count Summary

### Total Classes: ~48 (across 10 pages)

| Page | Title | Classes | Inheritance |
|---|---|---|---|
| 1 | User Hierarchy & Auth | 6 | User → 4 |
| 2 | Dashboard & System | 8 | Dashboard → 4 |
| 3 | Notification Hierarchy | 6 | Notification → 5 |
| 4 | Admission Module | 5 | — |
| 5 | Academic Structure | 8 | Course → 4 |
| 6 | Examination Module | 9 | Exam → 4 |
| 7 | HR, Employee, Payroll & Leave | 7 | Employee → 3 |
| 8 | Attendance Module | 2 | — |
| 9 | Finance Module | 4 | — |
| 10 | Messaging, News & TimeTable | 8 | — |
| **TOTAL** | | **~48** | **~20 arrows** |

### Relationships: ~50 total
| Type | Count |
|---|---|
| Inheritance (▷) | ~20 |
| Composition (◆) | 3 |
| Aggregation (◇) | 3 |
| Association (→) | ~21 |
| Dependency (- - -▶) | ~3 |
