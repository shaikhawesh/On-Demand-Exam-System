# Faculty Grading System - Visual Workflow & Diagrams

## 🎯 System Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    Faculty Grading System                    │
├─────────────────────────────────────────────────────────────┤
│                                                               │
│  ┌──────────────┐      ┌──────────────┐     ┌────────────┐  │
│  │   Faculty    │      │   Student    │     │   Admin    │  │
│  │    Login     │      │   Exam Data  │     │  Dashboard │  │
│  └──────┬───────┘      └──────┬───────┘     └──────┬─────┘  │
│         │                     │                    │         │
│         └─────────────────────┼────────────────────┘         │
│                               │                              │
│                    ┌──────────▼─────────┐                   │
│                    │ Faculty Results    │                   │
│                    │ (Grade Interface)  │                   │
│                    └──────────┬─────────┘                   │
│                               │                              │
│                    ┌──────────▼─────────────┐               │
│                    │ Grade Submission API    │               │
│                    │ /api/submit_grade/     │               │
│                    └──────────┬─────────────┘               │
│                               │                              │
│                    ┌──────────▼─────────┐                   │
│                    │   Validation        │                   │
│                    │ - Auth check       │                   │
│                    │ - Subject check    │                   │
│                    │ - Data validation  │                   │
│                    └──────────┬─────────┘                   │
│                               │                              │
│                    ┌──────────▼──────────┐                  │
│                    │   Database Save     │                  │
│                    │   (ExamResult)      │                  │
│                    └──────────┬──────────┘                  │
│                               │                              │
│                    ┌──────────▼──────────┐                  │
│                    │ Student Views Grade │                  │
│                    │  (Results Page)     │                  │
│                    └─────────────────────┘                  │
│                                                               │
└─────────────────────────────────────────────────────────────┘
```

## 📊 Faculty Grading Workflow

```
START
  │
  ├─→ Faculty Login (faculty_login.html)
  │     │
  │     ├─→ Authentication Check
  │     │     │
  │     │     └─→ PASS ✓
  │     │
  │     └─→ Redirect to Faculty Dashboard
  │
  ├─→ Faculty Dashboard (faculty_dashboard.html)
  │     │
  │     └─→ Click "Grade Exams"
  │
  ├─→ Faculty Results Page (Faculty_results.html)
  │     │
  │     ├─→ Load Assigned Subjects
  │     │
  │     ├─→ Load Student Exam Data (from data/ folder)
  │     │
  │     ├─→ Filter by Assigned Subjects
  │     │
  │     └─→ Display Exam Table
  │
  ├─→ Faculty Enters Grade
  │     │
  │     ├─→ Find Student in Table
  │     │
  │     ├─→ Enter Marks (e.g., 85)
  │     │
  │     ├─→ Enter Max Marks (e.g., 100)
  │     │
  │     ├─→ Enter Remarks (Optional)
  │     │
  │     └─→ Click "Save" Button
  │
  ├─→ Form Submission (JavaScript)
  │     │
  │     ├─→ Prevent Default Behavior
  │     │
  │     ├─→ Show "Saving..." State
  │     │
  │     └─→ Send via fetch() API
  │
  ├─→ Server Processing (api_submit_grade)
  │     │
  │     ├─→ Check Faculty Auth ✓
  │     │
  │     ├─→ Check Subject Assignment ✓
  │     │
  │     ├─→ Validate Data ✓
  │     │
  │     ├─→ Parse Decimal Fields ✓
  │     │
  │     ├─→ Find Student User ✓
  │     │
  │     ├─→ Create/Update ExamResult ✓
  │     │
  │     └─→ Return JSON Response ✓
  │
  ├─→ Response Handling (JavaScript)
  │     │
  │     ├─→ Button Back to Normal
  │     │
  │     ├─→ Show Success Toast: "✓ Grade saved"
  │     │
  │     └─→ Update Timestamp Display
  │
  └─→ Grade Persisted in Database
      │
      └─→ Student Can View in Results Page
          │
          └─→ END ✓
```

## 🔄 Data Flow Diagram

```
┌─────────────────────────────────────────────────────────────┐
│                         DATA FLOW                            │
├─────────────────────────────────────────────────────────────┤
│                                                               │
│  Student Submits Exam                                       │
│  ↓                                                            │
│  Saved to: data/[FILENAME].json                             │
│  ├─ username: student_name                                  │
│  ├─ subject: Subject Name                                   │
│  ├─ semester: Semester                                      │
│  ├─ saved_at: Timestamp                                     │
│  └─ date: Exam Date                                         │
│  ↓                                                            │
│  Faculty Views Faculty Results Page                         │
│  ↓                                                            │
│  Server Loads JSON Files from data/ Folder                  │
│  ↓                                                            │
│  Filter by Faculty's Assigned Subjects                      │
│  ↓                                                            │
│  Check ExamResult Database for Existing Grades              │
│  ↓                                                            │
│  Display in HTML Table with Forms                           │
│  ↓                                                            │
│  Faculty Enters: Marks | Max Marks | Remarks               │
│  ↓                                                            │
│  JavaScript Collects Form Data                              │
│  ├─ username                                                │
│  ├─ attempt_id (from saved_at)                              │
│  ├─ subject                                                 │
│  ├─ marks_obtained                                          │
│  ├─ max_marks                                               │
│  ├─ remarks                                                 │
│  └─ semester / exam_date                                    │
│  ↓                                                            │
│  Sends POST Request to /api/submit_grade/                   │
│  ↓                                                            │
│  Server Validates All Data                                  │
│  ↓                                                            │
│  Creates/Updates ExamResult Record:                         │
│  ├─ user: Student (FK)                                      │
│  ├─ subject: Subject Name                                   │
│  ├─ attempt_id: Unique ID                                   │
│  ├─ marks_obtained: Grade                                   │
│  ├─ max_marks: Total                                        │
│  ├─ remarks: Feedback                                       │
│  ├─ graded_by: Faculty Name                                 │
│  └─ graded_at: Timestamp (auto)                             │
│  ↓                                                            │
│  Database Saves Record                                      │
│  ↓                                                            │
│  Server Returns JSON Success                                │
│  ↓                                                            │
│  JavaScript Shows Success Toast                             │
│  ↓                                                            │
│  Table Updates with New Timestamp                           │
│  ↓                                                            │
│  Grade Persisted in Database                                │
│  ↓                                                            │
│  Student Can View in /profile/results/                      │
│                                                               │
└─────────────────────────────────────────────────────────────┘
```

## 🔐 Security Flow

```
                  Faculty Grade Submission
                          │
                          ▼
                  ┌─────────────────┐
                  │ Check CSRF Token│
                  └────────┬────────┘
                           │
                    VALID? ├─ NO ──→ REJECT
                           │
                           └─ YES
                           ▼
              ┌──────────────────────────┐
              │ Verify Faculty Auth      │
              │ (Session or User Auth)   │
              └─────────┬────────────────┘
                        │
                 VALID? ├─ NO ──→ Return 403 Unauthorized
                        │
                        └─ YES
                        ▼
         ┌──────────────────────────────────┐
         │ Check Subject Assignment         │
         │ (Faculty → Subject)              │
         └─────────┬──────────────────────┘
                   │
            VALID? ├─ NO ──→ Return 403 Forbidden
                   │
                   └─ YES
                   ▼
        ┌────────────────────────────────┐
        │ Validate Input Data            │
        │ - Not empty                    │
        │ - Valid formats                │
        │ - Reasonable values            │
        └─────────┬──────────────────────┘
                  │
           VALID? ├─ NO ──→ Return 400 Bad Request
                  │
                  └─ YES
                  ▼
         ┌────────────────────────────────┐
         │ Find Student User in Database  │
         └─────────┬──────────────────────┘
                   │
              FOUND? ├─ NO ──→ Return 404 Not Found
                    │
                    └─ YES
                    ▼
            ┌─────────────────────────────┐
            │ Save Grade to Database      │
            │ (with timestamp & faculty)  │
            └─────────┬───────────────────┘
                      │
                      ▼
           ┌──────────────────────────┐
           │ Return 200 OK            │
           │ + Success Message        │
           └──────────────────────────┘
```

## 📱 User Interface Flow

```
LOGIN PAGE
  │
  └─→ FACULTY DASHBOARD
      │
      ├─→ "Grade Exams" Button
      │
      └─→ FACULTY RESULTS PAGE
          │
          ├─ Header Section
          │  ├─ Logged in as: [username]
          │  ├─ Title: "Grade Submitted Exams"
          │  └─ Buttons: [Dashboard] [Logout]
          │
          ├─ Messages Section
          │  └─ Success/Error Messages
          │
          ├─ Subjects Box
          │  ├─ Title: "Your Assigned Subjects"
          │  ├─ Tags: [Subject1] [Subject2] ...
          │  └─ Note: "Only exams for these subjects..."
          │
          └─ Exam Table
             ├─ Columns:
             │  ├─ Student Name
             │  ├─ Subject
             │  ├─ Exam Date
             │  ├─ Attempt ID
             │  ├─ Marks (Form)
             │  │  └─ [Marks Field] [Max Marks Field]
             │  ├─ Remarks (Form)
             │  │  └─ [Textarea]
             │  ├─ Last Updated
             │  │  └─ Timestamp / "Not graded"
             │  └─ Save Button
             │
             └─ For Each Exam Row:
                ├─ Input Fields (inline form)
                │  ├─ Marks: [Number Input]
                │  ├─ Max Marks: [Number Input]
                │  └─ Remarks: [Textarea]
                │
                └─ Save Button
                   └─ On Click:
                      1. Shows "Saving..." state
                      2. Sends AJAX request
                      3. On success:
                         - Shows "✓ Grade saved"
                         - Updates timestamp
                         - Button returns to normal
```

## 🗄️ Database Schema

```
┌────────────────────────────────────────────────┐
│              ExamResult Model                  │
├────────────────────────────────────────────────┤
│ Field              │ Type                      │
├────────────────────────────────────────────────┤
│ id (PK)            │ AutoField                 │
│ user (FK)          │ ForeignKey(User)          │
│ subject            │ CharField(255)            │
│ attempt_id         │ CharField(64)             │
│ semester           │ CharField(50)             │
│ exam_date          │ DateField                 │
│ marks_obtained     │ DecimalField(6,2)         │
│ max_marks          │ DecimalField(6,2)         │
│ remarks            │ TextField                 │
│ graded_by          │ CharField(150)            │
│ graded_at          │ DateTimeField(auto_now)   │
│ created_at         │ DateTimeField(auto_add)   │
├────────────────────────────────────────────────┤
│ Indexes:                                       │
│ - (user, attempt_id): unique_together          │
│ - Ordered by: -graded_at                       │
├────────────────────────────────────────────────┤
│ Relationships:                                 │
│ - user → User (Student)                        │
│ - graded_by → Faculty username (char)          │
└────────────────────────────────────────────────┘

Connected Models:
  ┌────────────────────────────────────┐
  │         FacultySubjectAssignment    │
  │──────────────────────────────────────│
  │ - faculty (FK) → User               │
  │ - subject (Char)                    │
  │ - assigned_by (FK) → User (Admin)   │
  │ - assigned_at (DateTime)            │
  │ - is_active (Boolean)               │
  │──────────────────────────────────────│
  │ Unique: (faculty, subject)          │
  └────────────────────────────────────┘
```

## 🔄 Form Submission Process

```
┌──────────────────────────────────────────────────────────────┐
│            FORM SUBMISSION SEQUENCE                           │
├──────────────────────────────────────────────────────────────┤
│                                                                │
│ 1. User Views Faculty Results Page                            │
│    └─ HTML Form loaded (hidden or inline)                     │
│       └─ CSRF token included in form                          │
│                                                                │
│ 2. User Enters Grade Information                              │
│    └─ Marks: 85                                               │
│    └─ Max Marks: 100                                          │
│    └─ Remarks: "Good attempt"                                │
│                                                                │
│ 3. User Clicks "Save" Button                                  │
│    └─ onsubmit handler triggers                               │
│    └─ submitGradeForm() JavaScript function called            │
│                                                                │
│ 4. JavaScript Processes Form                                  │
│    ├─ event.preventDefault() → Cancel default submit          │
│    ├─ Get submit button element                               │
│    ├─ Save original button text                               │
│    ├─ Disable button & show "Saving..."                       │
│    └─ Create FormData object from form                        │
│                                                                │
│ 5. AJAX Request Sent                                          │
│    ├─ fetch(formElement.action, {                             │
│    │   method: 'POST',                                        │
│    │   body: formData                                         │
│    │ })                                                       │
│    └─ Includes:                                               │
│       ├─ CSRF token (from form)                               │
│       ├─ All form fields (username, attempt_id, etc.)         │
│       ├─ Marks (85)                                           │
│       ├─ Max Marks (100)                                      │
│       └─ Remarks ("Good attempt")                             │
│                                                                │
│ 6. Server Receives POST Request                               │
│    └─ Django receives at /faculty/results/ endpoint           │
│    └─ faculty_results view processes POST                     │
│       └─ Validates CSRF token                                 │
│       └─ Verifies faculty authentication                      │
│       └─ Checks subject assignment                            │
│       └─ Validates and saves grade                            │
│       └─ Returns redirect or response                         │
│                                                                │
│ 7. Response Received                                          │
│    └─ JavaScript catches response                             │
│    └─ Convert response to text/HTML                           │
│    └─ Check if successful                                     │
│                                                                │
│ 8. UI Updates (on success)                                    │
│    ├─ Re-enable button                                        │
│    ├─ Restore original button text                            │
│    ├─ Restore button opacity                                  │
│    ├─ Create success toast notification                       │
│       └─ Position: bottom-right                               │
│       └─ Message: "✓ Grade saved for [student] ([subject])"   │
│       └─ Color: Green                                         │
│       └─ Animation: Slide in from right                       │
│    ├─ Auto-remove toast after 3 seconds                       │
│    └─ (Optionally) Reload table with new data                 │
│                                                                │
│ 9. Grade Persisted                                            │
│    └─ Stored in database                                      │
│    └─ Timestamp recorded                                      │
│    └─ Faculty name recorded                                   │
│    └─ Available for student to view                           │
│                                                                │
└──────────────────────────────────────────────────────────────┘
```

## 🎯 Use Case Diagram

```
                    FACULTY MEMBER
                         │
              ┌──────────┼──────────┐
              │          │          │
              ▼          ▼          ▼
          Login    View Exams   Grade Exams
          │           │           │
          └───────┬───┴───────┬───┘
                  │           │
          ┌───────▼───────────▼─────────┐
          │   Faculty Results Page      │
          ├─────────────────────────────┤
          │ - View student exams        │
          │ - Filter by subject         │
          │ - Enter marks               │
          │ - Enter remarks             │
          │ - Save grade                │
          │ - See history               │
          └─────────┬───────────────────┘
                    │
          ┌─────────▼──────────┐
          │  Grade Database    │
          ├────────────────────┤
          │ - User             │
          │ - Subject          │
          │ - Marks            │
          │ - Remarks          │
          │ - Timestamp        │
          │ - Faculty name     │
          └────────────────────┘
                    │
          ┌─────────▼──────────┐
          │ Student Results    │
          ├────────────────────┤
          │ View grades        │
          │ See remarks        │
          │ Check timeline     │
          └────────────────────┘
```

## 📈 Implementation Timeline

```
2025-11-19
│
├─ 10:00 AM: Enhancement Started
│   └─ Review existing grading system
│
├─ 10:15 AM: Backend Development
│   └─ Create api_submit_grade() function
│   └─ Add URL route
│
├─ 10:30 AM: Frontend Development
│   └─ Update Faculty_results.html
│   └─ Add JavaScript for AJAX submission
│   └─ Add success notifications
│
├─ 10:45 AM: Code Review
│   └─ Verify syntax
│   └─ Check error handling
│   └─ Validate security
│
├─ 11:00 AM: Documentation
│   └─ Create user guides
│   └─ Create admin guides
│   └─ Create technical docs
│
└─ 11:15 AM: Complete ✓
    └─ Ready for production use
```

---

All diagrams show the faculty grading system from different perspectives: architecture, workflow, data flow, security, UI, database, form submission, use cases, and timeline.

**Status**: ✅ Complete and Documented
