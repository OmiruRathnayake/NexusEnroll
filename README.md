# NexusEnroll
Software Architecture (SCS 2303) Assignment 3

Group Members - 1.
                2.
                3.
                4.
                5.
                6.

# NexusEnroll — University Course Enrolment System

A proof-of-concept implementation of **NexusEnroll**, a modern university course enrolment system developed for **SCS 2303 — Software Architecture, Assignment 3** at the University of Colombo School of Computing (UCSC).

The project demonstrates how business requirements can be translated into a maintainable and scalable software design using **architectural patterns, object-oriented design principles, and design patterns**.

---

## 1. Project Overview

Nexus University currently operates a legacy monolithic course enrolment system called **LegacyEnroll**. The system is difficult to maintain, slow during peak enrolment periods, and lacks modern capabilities.

**NexusEnroll** is proposed as a modern replacement that supports:

* Student self-service
* Faculty management
* Administrator control
* Course enrolment and withdrawal
* Prerequisite validation
* Course capacity validation
* Schedule conflict detection
* Grade submission
* Notifications
* Reporting and analytics
* Transaction-based enrolment operations
* Future web and mobile clients

This repository contains a small, runnable **proof-of-concept** focused on the core business logic.

---

## 2. Assignment

**Course:** SCS 2303 — Software Architecture
**Assignment:** Assignment 3 — University Course Enrolment System
**Project:** NexusEnroll
**Team Size:** 6 members
**Academic Year:** 2026
**Deadline:** 20 August 2026

---

## 3. Architectural Approach

NexusEnroll uses a **3-Tier Architecture**.

```text
┌──────────────────────────────────────────────┐
│              PRESENTATION TIER               │
│                                              │
│       CLI / Future Web & Mobile Clients      │
└──────────────────────┬───────────────────────┘
                       │
                       ▼
┌──────────────────────────────────────────────┐
│             BUSINESS LOGIC TIER              │
│                                              │
│ Student │ Faculty │ Administrator            │
│                                              │
│ Enrollment │ Grades │ Notifications          │
│ Validation │ Reporting │ Course Management   │
└──────────────────────┬───────────────────────┘
                       │
                       ▼
┌──────────────────────────────────────────────┐
│                  DATA TIER                   │
│                                              │
│ Students │ Courses │ Enrollments │ Grades    │
│ Faculty  │ Programs │ Reports                │
└──────────────────────────────────────────────┘
```

The architecture separates presentation, business logic, and data responsibilities.

This allows the business logic to be reused by different clients, including a future web application or mobile application.

---

## 4. Design Patterns

The project demonstrates four object-oriented design patterns.

### Strategy Pattern

Used for **enrolment validation**.

Different validation rules are implemented as independent strategies:

* Prerequisite validation
* Capacity validation
* Time-conflict validation

```text
EnrollmentValidator
        │
        ├── PrerequisiteValidator
        ├── CapacityValidator
        └── TimeConflictValidator
```

This allows additional validation rules to be introduced without modifying the existing enrolment service.

---

### Observer Pattern

Used for the **notification system**.

When an important event occurs, such as a student dropping a course and a seat becoming available, interested observers can be notified automatically.

```text
Course / Enrollment Event
          │
          ▼
 Notification System
       /       \
      ▼         ▼
Waitlisted    Advisor
Students      Notification
```

This keeps notification logic decoupled from the core enrolment logic.

---

### State Pattern

Used for **grade submission**.

Grades can move between different states during the submission process.

```text
┌─────────┐
│ Pending │
└────┬────┘
     │ submit
     ▼
┌───────────┐
│ Submitted │
└───────────┘
```

The State pattern makes grade-state behaviour easier to maintain and extend.

---

### Factory Pattern

Used for **report generation**.

The administrator can request different types of reports without directly depending on their concrete implementations.

```text
ReportFactory
     │
     ├── EnrollmentReport
     ├── FacultyWorkloadReport
     └── CoursePopularityReport
```

---

## 5. Core Modules

### Student Module

The student module supports:

* Browse courses
* Search courses
* Enrol in courses
* Drop courses
* View schedules
* View academic progress

Enrolment requests are validated using:

1. Prerequisite checks
2. Capacity checks
3. Time-conflict checks

---

### Faculty Module

The faculty module supports:

* View class rosters
* Submit grades
* Manage grade submission states
* Request course information changes

---

### Administrator Module

The administrator module supports:

* Create courses
* Edit courses
* Delete courses
* Manage student accounts
* Manage faculty accounts
* Manage degree programs
* Override enrolment rules
* Generate reports

---

## 6. Transaction Management

Course enrolment operations are treated as transactions.

A successful operation should update all required parts of the system:

```text
Validate Request
      │
      ├── Prerequisites ✓
      ├── Capacity ✓
      └── Schedule ✓
             │
             ▼
       Enrol Student
             │
       ┌─────┴─────┐
       ▼           ▼
Update Course   Update Student
       │           │
       └─────┬─────┘
             ▼
           Commit
```

If any validation fails, the operation should fail without leaving partial changes in the system.

---

## 7. Design Principles

The implementation follows several software design principles.

### Encapsulation

Objects control access to their internal state through appropriate methods rather than allowing unrestricted modification.

### Single Responsibility Principle

Classes are designed around focused responsibilities.

For example, `PrerequisiteValidator` is responsible only for prerequisite validation.

### Open/Closed Principle

The validation system can be extended with additional validation strategies without modifying the existing validation logic.

### Liskov Substitution Principle

Concrete implementations can be used through their corresponding abstractions without changing the expected behaviour of the system.

### Interface Segregation Principle

The system avoids forcing components to depend on large interfaces containing unrelated operations.

### Dependency Inversion Principle

Core business logic depends on abstractions rather than concrete infrastructure implementations.

### Programming to an Interface

Business services communicate with abstractions where appropriate rather than tightly coupling themselves to concrete implementations.

### Composition over Inheritance

The system favours composing services and strategies rather than creating unnecessary inheritance hierarchies.

---

## 8. Project Structure

The project is organized according to the responsibilities of the different components.

```text
NexusEnroll/
│
├── README.md
│
├── src/
│   ├── models/
│   │   ├── student.py
│   │   ├── faculty.py
│   │   ├── administrator.py
│   │   ├── course.py
│   │   ├── enrollment.py
│   │   └── grade.py
│   │
│   ├── services/
│   │   ├── enrollment_service.py
│   │   ├── course_service.py
│   │   ├── grade_service.py
│   │   ├── notification_service.py
│   │   └── report_service.py
│   │
│   ├── patterns/
│   │   ├── strategy/
│   │   ├── observer/
│   │   ├── state/
│   │   └── factory/
│   │
│   ├── repositories/
│   │   └── ...
│   │
│   └── main.py
│
├── tests/
│   ├── test_enrollment.py
│   ├── test_grades.py
│   ├── test_notifications.py
│   └── test_reports.py
│
├── docs/
│   ├── architecture/
│   ├── uml/
│   └── report/
│
└── demo/
    └── ...
```

The exact structure may be updated as the implementation develops.

---

## 9. Main Business Flow

The primary demonstration scenario is student course enrolment.

```text
Student selects course
        │
        ▼
Enrollment Service
        │
        ▼
Prerequisite Validation
        │
        ▼
Capacity Validation
        │
        ▼
Time Conflict Validation
        │
        ▼
All validations successful?
      /       \
    No         Yes
    │           │
    ▼           ▼
 Reject      Enrol Student
                │
        ┌───────┴────────┐
        ▼                ▼
 Update Course      Update Schedule
        │                │
        └───────┬────────┘
                ▼
        Notify Interested Users
```

---

## 10. Example Use Cases

### Student Enrolment

A student attempts to enrol in a course.

The system:

1. Checks prerequisites.
2. Checks course capacity.
3. Checks schedule conflicts.
4. Enrols the student if all checks pass.
5. Updates the student's schedule.
6. Updates course enrolment information.
7. Completes the transaction.

---

### Student Drop

A student drops a course.

The system:

1. Removes the student from the course.
2. Updates the student's schedule.
3. Makes a seat available.
4. Triggers a notification event.
5. Notifies relevant waitlisted students.

---

### Faculty Grade Submission

A faculty member submits grades for students.

The system:

1. Receives the grades.
2. Validates the submitted grades.
3. Maintains the grade's current state.
4. Moves a valid grade from `Pending` to `Submitted`.
5. Allows invalid submissions to be corrected.

---

### Administrator Reporting

An administrator requests a report.

For example:

```text
Courses in the Business school
currently operating above 90% capacity
```

The appropriate report object is created through the report factory and generated from the available data.

---

## 11. Running the Application

### Requirements

* Python 3.x
* Git

Verify Python:

```bash
python --version
```

or:

```bash
python3 --version
```

---

### Clone the Repository

```bash
git clone <repository-url>
cd NexusEnroll
```

---

### Run the Application

```bash
python src/main.py
```

or:

```bash
python3 src/main.py
```

The proof-of-concept provides a simple interface for demonstrating student, faculty, and administrator operations.

---

## 12. Testing

The project includes tests for the main business rules and design-pattern behaviour.

Important test scenarios include:

| Test Case              | Expected Result                            |
| ---------------------- | ------------------------------------------ |
| Valid enrolment        | Student successfully enrolled              |
| Missing prerequisite   | Enrolment rejected                         |
| Course is full         | Enrolment rejected                         |
| Schedule conflict      | Enrolment rejected                         |
| Student drops course   | Course seat becomes available              |
| Waitlisted student     | Notification triggered                     |
| Valid grade            | Grade submitted                            |
| Invalid grade          | Submission rejected                        |
| Report request         | Appropriate report generated               |
| Administrator override | Student can be force-added when authorized |

---

## 13. Team

**NexusEnroll Development Team — 6 Members**

| Member   | Responsibility                        |
| -------- | ------------------------------------- |
| Member 1 | Architecture & Integration            |
| Member 2 | Student & Enrollment Module           |
| Member 3 | Faculty & Grade Module                |
| Member 4 | Administrator & Reporting Module      |
| Member 5 | Design Patterns & Notification System |
| Member 6 | UML, Testing & Documentation          |

Individual responsibilities may overlap during integration and testing.

---

## 14. Documentation

The project documentation covers:

* Requirements analysis
* Architectural design
* Architectural justification
* UML diagrams
* Object-oriented design
* Design patterns
* SOLID principles
* Implementation details
* Transaction management
* Testing
* Design decisions

The UML documentation includes:

* Use Case Diagram
* Class Diagram
* Activity Diagram
* Sequence Diagram
* State Diagram

---

## 15. Future Enhancements

The proof-of-concept can be extended into a production system with:

* Web-based SPA
* Native/mobile application
* REST APIs
* Persistent relational database
* Authentication and authorization
* Real-time notifications
* Email/SMS integration
* Distributed services
* Financial aid integration
* Advanced analytics
* Cloud deployment
* Load balancing and caching

The 3-Tier architecture provides a foundation for introducing these capabilities while keeping the core business logic separated from presentation and infrastructure concerns.

---

## 16. Academic Context

This project was developed as part of:

**SCS 2303 — Software Architecture**
**University of Colombo School of Computing (UCSC)**
**Assignment 3 — University Course Enrolment System: A Modernisation Project**

The implementation is intended as an academic proof-of-concept demonstrating software architecture and object-oriented design concepts.
