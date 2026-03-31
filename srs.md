# Software Requirements Specification (SRS)
## Time Tracking System

---

## Table of Contents

1. [Introduction](#1-introduction)
   - 1.1 [Purpose](#11-purpose)
   - 1.2 [Scope](#12-scope)
   - 1.3 [Definitions, Acronyms, and Abbreviations](#13-definitions-acronyms-and-abbreviations)
   - 1.4 [References](#14-references)
   - 1.5 [Overview of Document](#15-overview-of-document)
2. [Overall Description](#2-overall-description)
   - 2.1 [Product Perspective](#21-product-perspective)
   - 2.2 [Product Features Summary](#22-product-features-summary)
   - 2.3 [User Classes and Characteristics](#23-user-classes-and-characteristics)
   - 2.4 [Operating Environment](#24-operating-environment)
   - 2.5 [Design and Implementation Constraints](#25-design-and-implementation-constraints)
   - 2.6 [Assumptions and Dependencies](#26-assumptions-and-dependencies)
3. [System Features & Functional Requirements](#3-system-features--functional-requirements)
   - 3.1 [Module 1 – Time Sessions](#31-module-1--time-sessions)
   - 3.2 [Module 2 – Overtime Templates](#32-module-2--overtime-templates)
4. [Non-Functional Requirements](#4-non-functional-requirements)
   - 4.1 [Performance Requirements](#41-performance-requirements)
   - 4.2 [Security Requirements](#42-security-requirements)
   - 4.3 [Reliability and Availability](#43-reliability-and-availability)
   - 4.4 [Maintainability](#44-maintainability)
   - 4.5 [Scalability](#45-scalability)
   - 4.6 [Usability](#46-usability)
5. [Database Design](#5-database-design)
   - 5.1 [Collection: time_sessions](#51-collection-time_sessions)
   - 5.2 [Collection: overtime_templates](#52-collection-overtime_templates)
   - 5.3 [Design Notes](#53-design-notes)
6. [System Architecture](#6-system-architecture)
   - 6.1 [Tech Stack](#61-tech-stack)
   - 6.2 [Folder Structure](#62-folder-structure)
   - 6.3 [API Endpoints](#63-api-endpoints)
7. [Business Logic & Rules](#7-business-logic--rules)
   - 7.1 [Time Session Rules](#71-time-session-rules)
   - 7.2 [Overtime Types](#72-overtime-types)
   - 7.3 [Flexible Time Within Template Slot](#73-flexible-time-within-template-slot)
   - 7.4 [Template Visibility Rules](#74-template-visibility-rules)
   - 7.5 [Session Edit Rules](#75-session-edit-rules)
8. [Status Flows](#8-status-flows)
   - 8.1 [Time Session Status Flow](#81-time-session-status-flow)
   - 8.2 [Overtime Template Status Flow](#82-overtime-template-status-flow)
   - 8.3 [Manual Overtime Status Flow](#83-manual-overtime-status-flow)
9. [Frontend Requirements](#9-frontend-requirements)
   - 9.1 [Architecture Overview](#91-architecture-overview)
   - 9.2 [Layout Structure](#92-layout-structure-mainpagetsx)
   - 9.3 [Navigation Structure](#93-navigation-structure)
   - 9.4 [Pages](#94-pages-route-components)
   - 9.5 [Shared Components](#95-shared-components)
   - 9.6 [Module-Specific Components](#96-module-specific-components)
   - 9.7 [Time Session Form](#97-time-session-form)
   - 9.8 [Overtime Section](#98-overtime-section)
   - 9.9 [Available Actions](#99-available-actions)
   - 9.10 [Frontend Validation](#910-frontend-validation)
   - 9.11 [UI / UX Constraints](#911-ui--ux-constraints)
10. [Validation Rules](#10-validation-rules)
    - 10.1 [Rule Types](#101-rule-types)
    - 10.2 [Generic Validation Rules](#102-generic-validation-rules-hardcoded)
    - 10.3 [Configurable Validation Rules](#103-configurable-validation-rules)
    - 10.4 [Overtime Validation Details](#104-overtime-validation-details)
11. [Error Handling](#11-error-handling)
    - 11.1 [API Error Responses](#111-api-error-responses)
    - 11.2 [Frontend Error Handling](#112-frontend-error-handling)
12. [Testing Strategy](#12-testing-strategy)
    - 12.1 [Unit Testing](#121-unit-testing)
    - 12.2 [Integration Testing](#122-integration-testing)
    - 12.3 [API Testing](#123-api-testing)
    - 12.4 [Test Cases](#124-test-cases)
13. [Out of Scope](#13-out-of-scope)
14. [Open Issues & Future Enhancements](#14-open-issues--future-enhancements)
15. [Appendix](#15-appendix)
    - 15.1 [Status Enum Reference](#151-status-enum-reference)
    - 15.2 [API Response Format Reference](#152-api-response-format-reference)
    - 15.3 [Glossary Quick Reference](#153-glossary-quick-reference)
    - 15.4 [Configurable Business Rules](#154-configurable-business-rules)

---

## 1. Introduction

### 1.1 Purpose

This Software Requirements Specification (SRS) document describes the complete functional and non-functional requirements for the **Time Tracking System**. It covers the following modules:



- **Time Sessions** — logging, editing, and submitting daily work sessions
- **Overtime Handling** — manual and template-based overtime within sessions
- **Overtime Templates** — requesting and approving pre-defined overtime slots

---

### 1.2 Scope

The Time Tracking System enables employees to:

- Log daily work sessions with start and end times
- Attach charge codes and step-up role codes to sessions
- Add manual overtime to a session with a reason
- Request overtime templates from admins for pre-planned overtime
- Apply approved overtime templates to sessions (auto-approved)
- Submit completed sessions to managers or admins for approval
- Edit or delete sessions while they are not yet approved

---

### 1.3 Definitions, Acronyms, and Abbreviations

| Term | Definition |
|---|---|
| SRS | Software Requirements Specification |
| API | Application Programming Interface |
| CRUD | Create, Read, Update, Delete |
| UTC | Coordinated Universal Time |
| FR | Functional Requirement |
| NFR | Non-Functional Requirement |
| OT | Overtime |
| Admin | System administrator with elevated permissions |
| Manager | User with permission to approve/reject sessions |
| SUBMITTED | Session sent for approval |
| APPROVED | Session or template confirmed by admin/manager |
| REJECTED | Session or template declined by admin/manager |
| PENDING | Default status for manual overtime awaiting approval |
| Template | A pre-approved overtime slot defined by an admin |
| Soft Delete | Marking a record as deleted without removing it from the database |
| DXA | Document Exchange Architecture (unit of measurement for Word docs) |

---

### 1.4 References

- MongoDB Documentation: https://www.mongodb.com/docs
- Express.js Documentation: https://expressjs.com
- React Documentation: https://react.dev

---

### 1.5 Overview of Document

The remainder of this document is structured as follows:
- **Section 2** provides a high-level description of the product, its users, and constraints.
- **Section 3** lists all functional requirements grouped by module.
- **Section 4** defines non-functional requirements (performance, security, scalability, etc.).
- **Sections 5–6** cover the database design and system architecture.
- **Section 7** defines business rules and logic.
- **Section 8** documents all status flows.
- **Section 9** covers frontend requirements and UI constraints.
- **Section 10** defines validation rules.
- **Section 11** covers error handling.
- **Section 12** outlines the testing strategy and test cases.
- **Sections 13–14** cover scope exclusions and future enhancements.
- **Section 15** contains reference materials including configurable business rules.

---

## 2. Overall Description

### 2.1 Product Perspective

Standalone web application with client-server architecture (React frontend, Node.js/Express backend, MongoDB database).

---

### 2.2 Product Features Summary

| # | Feature | Description |
|---|---|---|
| F-01 | Log Time Sessions | Users can create, edit, and delete daily work sessions |
| F-02 | Submit Sessions | Users submit sessions directly on creation; admin approves/rejects |
| F-03 | Manual Overtime | Users can flag overtime manually with a reason; requires approval |
| F-04 | Template Overtime | Users can use a pre-approved template; auto-approved on use |
| F-05 | Request OT Template | Users can request an overtime template from admin |
| F-06 | Approve/Reject Template | Admins can approve or reject overtime template requests |
| F-07 | Approve/Reject Session | Managers/admins can approve or reject submitted sessions (backend API only for now) |
| F-08 | Template Visibility | Templates only appear on their exact assigned date |

---

### 2.3 User Classes and Characteristics

| User Class | Description | Key Permissions |
|---|---|---|
| **Employee** | Regular user who logs work sessions | Create, edit, delete sessions; request OT templates; use approved templates |
| **Manager** | Supervises employees | Approves/rejects submitted sessions (backend API only) |
| **Admin** | System administrator | All manager permissions + approve/reject overtime templates (backend API only) |
| **Note:** | **Users cannot view their own overtime template requests after submission.** |
| **Note:** | **Only admins/managers can see pending overtime template requests for approval/rejection.** |

---

### 2.4 Operating Environment

| Layer | Technology |
|---|---|
| Frontend | React (Monorepo) |
| Backend | Node.js + Express |
| Database | MongoDB |
| Authentication | JWT |

---

### 2.5 Design and Implementation Constraints

- All times must be stored in **UTC** in the database.
- Timezone conversion will only be handled on frontend while displaying.
- All deletes are **soft deletes** — records are flagged with `isDeleted: true` and never physically removed.
- Sessions in `APPROVED` status are immutable — no edits allowed.

---

### 2.6 Assumptions and Dependencies

- Users are authenticated before accessing any feature.
- Each user has a unique `userId` (ObjectId) stored in the database.
- The system assumes one timezone per user (configurable; defaults to UTC).
- Charge codes and step-up role codes are pre-configured in the system.
- Only one session per user per overlapping time window is permitted.
- An internet connection is required; the application does not support offline mode.

---

## 3. System Features & Functional Requirements

> Each requirement is identified by a unique ID in the format `FR-[MODULE]-[NUMBER]`.

---

### 3.1 Module 1 – Time Sessions

#### 3.1.1 Create Time Session

| ID | Requirement |
|---|---|
| FR-TS-01 | The system shall allow an authenticated user to create a new time session. |
| FR-TS-02 | The session must include: `userId`,`chargeCode`, `date`, `start` time, and `end` time. |
| FR-TS-03 | The system shall automatically compute `durationMinutes` as `end - start`. |
| FR-TS-04 | The system shall reject a session where `start >= end`. |
| FR-TS-05 | The system shall reject a session that overlaps with an existing session for the same user. |
| FR-TS-06 | A newly created session shall have status `SUBMITTED`. |
| FR-TS-07 | The user may optionally provide `stepUpRoleCode`, and `notes`. |

---

#### 3.1.2 Edit Time Session

| ID | Requirement |
|---|---|
| FR-TS-08 | The system shall allow editing of sessions in `SUBMITTED` status  |
| FR-TS-09 | The system shall prevent editing of sessions in `APPROVED`  or `REJECTED` status. |
| FR-TS-11 | All validation rules from FR-TS-04 and FR-TS-05 apply on edit as well. |

---

#### 3.1.3 Delete Time Session

| ID | Requirement |
|---|---|
| FR-TS-12 | The system shall support soft deletion of a session at any status. |
| FR-TS-13 | Soft-deleted sessions shall be excluded from all GET listing responses. |
| FR-TS-14 | The `isDeleted` flag shall be set to `true` upon deletion; the record is retained in the database. |

---

#### 3.1.4 Approve / Reject Time Session

| ID | Requirement |
|---|---|
| FR-TS-15 | A manager or admin shall be able to approve a `SUBMITTED` session, changing its status to `APPROVED`. |
| FR-TS-16 | A manager or admin shall be able to reject a `SUBMITTED` session, changing its status to `REJECTED`. |
| FR-TS-17 | The system shall lock the session from editing once status is `APPROVED` OR `REJECTED` . |

---

#### 3.1.5 List Time Sessions

| ID | Requirement |
|---|---|
| FR-TS-21 | The system shall return all non-deleted sessions belonging to the authenticated user. |
| FR-TS-22 | Sessions shall support filtering by `date`, `status`, and `chargeCode`. |

---

#### 3.1.6 Overtime in a Session

| ID | Requirement |
|---|---|
| FR-TS-23 | The system shall allow a user to flag a session as overtime by setting `isOvertime = true`. |
| FR-TS-24 | When `isOvertime = true`, the overtime sub-object must be populated. |
| FR-TS-25 | If no template is selected (`templateId = null`), the user must provide a `reason`. |
| FR-TS-26 | Manual overtime shall have `overtime.status = PENDING` until approved by admin/manager. |
| FR-TS-27 | If a template is selected, `overtime.status` shall be set to `APPROVED` automatically. |
| FR-TS-28 | Template-based overtime does not require an additional approval step. |

---

### 3.2 Module 2 – Overtime Templates

#### 3.2.1 Request Overtime Template

| ID | Requirement |
|---|---|
| FR-OT-01 | Any authenticated user shall be able to request a new overtime template. |
| FR-OT-02 | The template must include: `name`, `allowedTimeRange.start`, `allowedTimeRange.end`, `reason` and `date`. |
| FR-OT-03 | A newly created template shall have status `PENDING` by default. |
| FR-OT-04 | The `createdBy` field shall be automatically populated with the requesting user's `userId`. |

---

#### 3.2.2 Approve / Reject Template

| ID | Requirement |
|---|---|
| FR-OT-05 | An admin shall be able to approve a `PENDING` template, changing its status to `APPROVED`. |
| FR-OT-06 | An admin shall be able to reject a `PENDING` template, changing its status to `REJECTED`. |
| FR-OT-07 | On approval, the `approvedBy` field shall be populated with the admin's `userId`. |

---

#### 3.2.3 Template Visibility

| ID | Requirement |
|---|---|
| FR-OT-08 | The GET `/overtime-templates` endpoint shall return only `APPROVED` templates to regular users. |
| FR-OT-09 | Templates shall only be visible on the exact date matching `template.date`. |
| FR-OT-10 | Templates shall not be visible before or after their assigned date. |
| FR-OT-11 | Template visibility is determined by comparing `template.date` with the session's selected date. |

---

#### 3.2.4 Apply Template to Session

| ID | Requirement |
|---|---|
| FR-OT-12 | A user may select an approved template when creating or editing a session. |
| FR-OT-13 | The selected template must have `status = APPROVED`. |
| FR-OT-14 | The logged overtime time range must fall within the template's `allowedTimeRange`. |
| FR-OT-15 | Partial ranges within the allowed window are permitted (e.g. 04:30–05:00 within 04:00–05:00). |
| FR-OT-16 | Users shall NOT be able to view their own overtime template requests after submission. |
| FR-OT-17 | Only admins/managers shall see pending overtime template requests for approval/rejection. |

---

#### 3.2.5 Edit / Delete Template

| ID | Requirement |
|---|---|
| FR-OT-18 | A template may be edited only before it has been approved. |
| FR-OT-19 | Templates support soft deletion at any status. |

---

## 4. Non-Functional Requirements

### 4.1 Performance Requirements

| ID | Requirement |
|---|---|
| NFR-PERF-01 | API responses for GET requests shall complete within **500ms** under normal load. |
| NFR-PERF-02 | API responses for POST/PUT/PATCH requests shall complete within **1000ms** under normal load. |
| NFR-PERF-03 | The system shall support at least **100 concurrent users** without degradation. |
| NFR-PERF-04 | Database queries on `time_sessions` shall use indexed fields (`userId`, `date`) for performance. |

---

### 4.2 Security Requirements

| ID | Requirement |
|---|---|
| NFR-SEC-01 | All API endpoints shall require authentication via a valid token (JWT or equivalent). |
| NFR-SEC-02 | Users shall only be able to access their own sessions; cross-user access is forbidden. |
| NFR-SEC-03 | Admin-only endpoints (approve/reject template) shall enforce role-based access control (RBAC). |
| NFR-SEC-04 | All data in transit shall be encrypted via HTTPS/TLS. |
| NFR-SEC-05 | Sensitive fields (user IDs, tokens) shall not be exposed in error messages. |
| NFR-SEC-06 | Input validation shall be performed on all API inputs to prevent injection attacks. |

---

### 4.3 Reliability and Availability

| ID | Requirement |
|---|---|
| NFR-REL-01 | The system shall target **99.5% uptime** (excluding scheduled maintenance). |
| NFR-REL-02 | The system shall handle and log unexpected errors without crashing the server. |
| NFR-REL-03 | Database operations shall be atomic where multiple updates are involved. |

---

### 4.4 Maintainability

| ID | Requirement |
|---|---|
| NFR-MNT-01 | Business logic resides in service files, not controllers or routes. |

---

### 4.5 Scalability

| ID | Requirement |
|---|---|
| NFR-SCL-01 | The backend shall be stateless to support horizontal scaling behind a load balancer. |
| NFR-SCL-02 | MongoDB collections shall be indexed on frequently queried fields. |

---

## 5. Database Design

### 5.1 Collection: time_sessions

| Field | Type | Required | Description |
|---|---|---|---|
| `_id` | ObjectId | Yes | Primary key |
| `userId` | ObjectId | Yes | Reference to user |
| `chargeCode` | String | Yes | Work category code |
| `date` | String (YYYY-MM-DD) | Yes | Session date |
| `start` | String (HH:MM) | Yes | Start time |
| `end` | String (HH:MM) | Yes | End time |
| `durationMinutes` | Number | Yes | Auto-calculated duration |
| `stepUpRoleCode` | String | No | Optional role code |
| `notes` | String | No | User notes |
| `isOvertime` | Boolean | No | Flag for overtime sessions |
| `overtime` | Object | No | Overtime details (if isOvertime=true) |
| `overtime.templateId` | ObjectId | No | Reference to approved template |
| `overtime.reason` | String | Conditional | Required if no template |
| `overtime.status` | String | No | `PENDING`, `APPROVED`, `REJECTED` |
| `status` | String | Yes | `SUBMITTED`, `APPROVED`, `REJECTED` |
| `isDeleted` | Boolean | Yes | Soft delete flag |
| `createdAt` | Date | Yes | Creation timestamp |
| `updatedAt` | Date | Yes | Last update timestamp |

**Indexes:**
- `userId` + `date` (compound index for user date range queries)
- `userId` + `isDeleted` (for filtering deleted records)
- `isOvertime` (for separating normal/OT sessions)

---

### 5.2 Collection: overtime_templates

| Field | Type | Required | Description |
|---|---|---|---|
| `_id` | ObjectId | Yes | Primary key |
| `name` | String | Yes | Template name |
| `allowedTimeRange.start` | String (HH:MM) | Yes | OT window start |
| `allowedTimeRange.end` | String (HH:MM) | Yes | OT window end |
| `reason` | String | Yes | Request reason |
| `date` | String (YYYY-MM-DD) | Yes | Template effective date |
| `status` | String | Yes | `PENDING`, `APPROVED`, `REJECTED` |
| `createdBy` | ObjectId | Yes | User who requested template |
| `approvedBy` | ObjectId | No | Admin who approved (if approved) |
| `isDeleted` | Boolean | Yes | Soft delete flag |
| `createdAt` | Date | Yes | Creation timestamp |
| `updatedAt` | Date | Yes | Last update timestamp |

**Indexes:**
- `date` + `status` (for template visibility queries)
- `createdBy` (for user's own requests)
- `status` (for admin approval queues)

---

### 5.3 Design Notes

- All times stored in **UTC** internally
- Soft delete pattern: `isDeleted: true` marks records as deleted without removing them
- Compound indexes optimize common query patterns (user+date, date+status)
- References stored as ObjectIds with application-level joins

---

## 6. System Architecture

### 6.1 Tech Stack

| Layer | Technology |
|---|---|
| Frontend | React (Monorepo) |
| Backend | Node.js + Express |
| Database | MongoDB |
| Authentication | JWT |
| **State Management** | **Redux with CRUDWrapper** |

---

### 6.2 Folder Structure

> See existing `frontend/src/` structure — React SPA with modules, shared components, and context providers already in place.

---

### 6.3 API Endpoints

### 6.1 Time Sessions

| Method | Endpoint | Description | Query Params |
|---|---|---|---|
| GET | `/time-sessions` | Get all sessions for current user | `date`, `status`, `chargeCode` |
| GET | `/time-sessions?isOvertime=false` | Get **normal sessions** (Time Sessions page) | - |
| GET | `/time-sessions?isOvertime=true` | Get **overtime sessions** (Overtime page) | - |
| POST | `/time-sessions` | Create new session | Body: session data |
| PUT | `/time-sessions/:id` | Update session | Body: updated fields |
| DELETE | `/time-sessions/:id` | Soft delete session | - |
| PATCH | `/time-sessions/:id/status` | Approve/reject session (manager only) | Body: `{ status }` |

> **Note:** The `isOvertime` query param filters sessions:
> - `isOvertime=false` → returns sessions where `isOvertime = false` or not set (for Time Sessions page)
> - `isOvertime=true` → returns sessions where `isOvertime = true` (for Overtime page)

### 6.2 Overtime Templates

| Method | Endpoint | Description |
|---|---|---|
| GET | `/overtime-templates` | Get approved templates for current date |
| POST | `/overtime-templates` | Request new template |
| PATCH | `/overtime-templates/:id/approve` | Approve template (admin) |
| PATCH | `/overtime-templates/:id/reject` | Reject template (admin) |

---

### 7. Business Logic & Rules

### 7.1 Time Session Rules

| Rule | Description |
|---|---|
| Duration Calculation | `durationMinutes` = `end` - `start` in minutes |
| Time Order | `start` must be strictly before `end` |
| Overlap Prevention | No overlapping sessions allowed for same user |
| Soft Delete | Records marked with `isDeleted: true` remain in database |
| Edit Lock | `APPROVED` or `REJECTED` sessions cannot be modified |

---

### 7.2 Overtime Types

| Type | Description | Approval |
|---|---|---|
| Manual Overtime | User flags session as OT with reason | Requires admin/manager approval (`PENDING` → `APPROVED`/`REJECTED`) |
| Template Overtime | User selects pre-approved template | Auto-approved (`APPROVED` immediately) |

---

### 7.3 Flexible Time Within Template Slot

Users may log overtime within any portion of the template's `allowedTimeRange`:

- Template allows: 04:00 – 06:00
- User logs: 04:30 – 05:30 → **VALID** (within range)
- User logs: 03:30 – 05:00 → **INVALID** (starts before allowed range)
- User logs: 05:00 – 06:30 → **INVALID** (ends after allowed range)

---

### 7.4 Template Visibility Rules

| Scenario | Visibility |
|---|---|
| `status = PENDING` | Visible only to admins/managers |
| `status = REJECTED` | Not visible to any users |
| `status = APPROVED` + `date = today` | Visible to all users for selection |
| `status = APPROVED` + `date != today` | Not visible (date-locked) |

---

### 7.5 Session Edit Rules

| Session Status | Editable? | Notes |
|---|---|---|
| `SUBMITTED` | Yes | Editable before approval decision |
| `APPROVED` | No | Locked — no changes permitted |
| `REJECTED` | No | Locked — no changes permitted |

---

## 8. Status Flows

### 8.1 Time Session Status Flow

```
POST /time-sessions
       │
       ▼
   SUBMITTED (default on creation)
       │
       ├── PATCH /approve → APPROVED (locked, no further edits)
       └── PATCH /reject  → REJECTED (locked, no further edits)
```

---

### 8.2 Overtime Template Status Flow

```
PENDING
  ├── PATCH /approve → APPROVED  (visible to users on exact date)
  └── PATCH /reject  → REJECTED  (not visible to users)
```

---

### 8.3 Manual Overtime Status Flow

```
PENDING  (set on session creation when templateId = null)
  ├── Admin approves → APPROVED
  └── Admin rejects  → REJECTED
```

> Template-based overtime is created with `status = APPROVED` directly and bypasses this flow entirely.

---

## 9. Frontend Requirements

### 9.1 Architecture Overview

> **Note:** The frontend codebase already contains pre-built components in `frontend/src/`. All new development must **reuse existing shared components** located in:
> - `src/shared/components/` — Generic UI components (DataTable, FormDialog, etc.)
> - `src/modules/*/components/` — Module-specific page components
> 
> Do NOT create duplicate components. Check existing implementations before building new UI.

---

### 9.2 Layout Structure (MainPage.tsx)

The application uses a **fixed layout** with three main areas:

| Area | Component | Description |
|---|---|---|
| **Header** | `TitleBar` | Fixed top bar with menu toggle, title "TIME KEEPING" |
| **Sidebar** | `NavigationComponent` | Collapsible side navigation with sections |
| **Content** | `Outlet` (react-router) | Main content area with scrollable pages |

**Layout Features:**
- Responsive side navigation (collapsible)
- Navigation schema driven by `NavSchema.ts`
- Main content area scrolls independently

---

### 9.3 Time Sessions Page (`/timesessions`)

**Purpose:** Display and manage **normal time sessions** (`isOvertime = false`).

#### Page Layout

| Element | Description |
|---|---|
| **Add New Button** | Opens dialog to create new time session |
| **Time Sessions List** | Table of **normal sessions only** (`isOvertime = false`) |

> **Note:** Sessions with `isOvertime = true` do NOT appear here — they appear on the Overtime page.

#### Add Session Dialog (TimeEntryFormDialog)

Opened when user clicks **"Add New"** button.

**Dialog Fields:**

| Field | Input Type | Required | Description |
|---|---|---|---|
| Date | Date picker | Yes | Session date |
| Start Time | Time picker | Yes | Start time (must be before end) |
| End Time | Time picker | Yes | End time (must be after start) |
| Charge Code | Dropdown | Yes | Work category |
| Step-Up Role | Dropdown | No | Role selection |
| **Overtime** | **Checkbox** | **No** | If checked, session goes to Overtime page |
| Template | Dropdown | No | Select approved OT template (auto-checks Overtime) |
| Notes | Textarea | No | Additional details |

**Save Behavior:**
| Scenario | Result | Destination |
|---|---|---|
| Overtime = false, no template | Normal session | Time Sessions page |
| Overtime = true (checked) | Overtime session | Overtime page |
| Template selected | Overtime session | Overtime page |

**Dialog Actions:**
| Button | Action | API Call |
|---|---|---|
| Save | Create session | `POST /time-sessions` |
| Cancel | Close without saving | — |

---

### 9.4 Overtime Page (`/overtime`)

**Purpose:** Display **overtime sessions** and submit **overtime requests**.

#### Page Layout

| Element | Description |
|---|---|
| **Request Overtime Button** | Opens dialog to submit overtime request |
| **Overtime Sessions List** | Sessions with `isOvertime = true`  |

> **Note:** Overtime sessions are created via Time Sessions page "Add New" dialog by checking Overtime or selecting a template.

#### Request Overtime Dialog

> **This creates an Overtime Request — separate from time sessions.**

| Field | Input Type | Required | Description |
|---|---|---|---|
| Date | Date picker | Yes | Overtime date |
| Start Time | Time picker | Yes | OT start |
| End Time | Time picker | Yes | OT end |
| Duration | Number | Yes | Auto-calculated hours |
| Charge Code | Dropdown | Yes | Work category |
| Reason | Textarea | Yes | Why OT is needed |

**After Submit:**
- Request appears with status `PENDING`
- Admin approves/rejects — user cannot edit
- User does nothing further

---

### 9.5 Data Flow Summary
| Page | Content | How Created |
|---|---|---|
| **Time Sessions** | Sessions with `isOvertime = false` | "Add New" dialog → Overtime unchecked |
| **Overtime** | Sessions with `isOvertime = true` | "Add New" dialog → Overtime checked or Template selected |
| **Overtime** | Overtime requests | "Request Overtime" button → Submit request |

---

### 9.6 Redux CRUD Wrapper

> **All CRUD API operations must use the existing Redux CRUD wrapper.** Located in `frontend/src/store/reduxCRUD/` and `frontend/src/store/ReduxFactory/`.

#### 9.6.1 CRUDFunction (React Components)

For React page components, use the `CRUDFunction` HOC factory

#### 9.6.2 ReduxFactory (Non-React Services)

For services, contexts, or utilities (non-React files), use `createCrudService`

#### 9.6.3 Configuration Options

Both wrappers support the same options:

| Option | Type | Default | Description |
|---|---|---|---|
| `read` | `boolean` | `true` | Enable get/getAll operations |
| `create` | `boolean` | `true` | Enable create operation |
| `update` | `boolean` | `true` | Enable update operation |
| `delete` | `boolean` | `true` | Enable delete operation |
| `others` | `Record<string, Function>` | `{}` | Custom non-CRUD actions |

**Example with disabled operations:**

```tsx
export default CRUDFunction(UserPage, "user", "users", {
    read: true,
    create: true,
    update: false,   // No updates allowed
    delete: false,   // No deletes allowed
});
```

#### 9.6.4 Rules for Usage

- **DO** use lowercase singular for module names → `"timeSession"`, `"overtimeTemplate"`
- **DO** use the existing wrapper for ALL API calls — do NOT create manual Redux slices
- **DO** use `actionType` to react to operation results in `useEffect`
- **DO NOT** modify `store.ts` to add reducers — the wrapper handles injection automatically
- **DO NOT** create duplicate services — they are cached by name

---

### 10.1 Rule Types

These rules are fundamental to the system and are **not configurable**:

| Rule | Description | Error |
|---|---|---|
| Required fields | `userId`, `date`, `start`, `end` must be present | 400 Bad Request |
| Time ordering | `start` must be strictly before `end` | 400 – Start must be before end |
| Valid date format | `date` must be valid calendar date in YYYY-MM-DD format | 400 Bad Request |
| No overlap | No two sessions for the same user can overlap in time | 409 Conflict | "Time overlaps with an existing session" |
| Edit lock | Sessions in `APPROVED` status cannot be edited | 403 Forbidden |
| Template status | Selected template must have `status = APPROVED` | 400 – Template not approved |
| Template date match | Template's `date` must match session's `date` | 400 – Template not valid for this date |
| Template time range | Logged OT must fall within template's `allowedTimeRange` | 400 – Time outside allowed range |
| Manual OT reason | `overtime.reason` required when `templateId = null` | 400 – Reason is required |

---

### 10.2 Configurable Validation Rules

These rules are loaded from the configuration system (see Section 15.4) and can be changed without code changes:

| Rule Key | Default | Description | Config File Location |
|---|---|---|---|
| `maxSessionDuration` | 1440 minutes | Maximum allowed session duration | `rules.json` → `validation.maxSessionDuration` |
| `maxOvertimeDaily` | 240 minutes | Maximum OT per day per user | `rules.json` → `limits.maxOvertimeDaily` |
| `minSessionDuration` | 1 minute | Minimum session duration | `rules.json` → `validation.minSessionDuration` |
| `allowWeekendWork` | true | Allow sessions on weekends | `rules.json` → `business.allowWeekendWork` |
| `allowHolidayWork` | true | Allow sessions on holidays | `rules.json` → `business.allowHolidayWork` |
| `overtimeRequiresReason` | true | Manual OT requires reason | `rules.json` → `business.overtimeRequiresReason` |

---

### 10.4 Overtime Validation Details

#### Template-Based Overtime

| Rule | Description | Error |
|---|---|---|
| Template status | Must have `status = APPROVED` | 400 – Template not approved |
| Date match | `template.date` must equal session `date` | 400 – Template not valid for this date |
| Time within range | OT time must be within `allowedTimeRange` | 400 – Time outside allowed range |
| Auto-approval | `overtime.status` set to `APPROVED` automatically | N/A |

#### Manual Overtime

| Rule | Description | Error |
|---|---|---|
| No template | `overtime.templateId` must be `null` | 400 Bad Request |
| Reason required | `overtime.reason` must be non-empty string (if `overtimeRequiresReason` is true) | 400 – Reason is required |
| Pending status | `overtime.status` set to `PENDING` automatically | N/A |

---

## 11. Error Handling

### 11.1 API Error Responses

All API errors must follow a consistent response format:

```json
{
  "success": false,
  "statusCode": 400,
  "error": "VALIDATION_ERROR",
  "message": "Start time must be before end time.",
  "details": {}
}
```

| HTTP Status | Error Code | When to Use |
|---|---|---|
| 400 | `VALIDATION_ERROR` | Missing or invalid fields |
| 401 | `UNAUTHORIZED` | Missing or invalid authentication token |
| 403 | `FORBIDDEN` | User lacks permission for the action |
| 404 | `NOT_FOUND` | Requested resource does not exist |
| 409 | `CONFLICT` | Session overlap detected |
| 500 | `INTERNAL_ERROR` | Unexpected server error |

---

### 11.2 Frontend Error Handling

All errors are displayed via **toast notifications** with generic, user-friendly messages defined in the **frontend**.

| Error Source | Status Code | Toast Message (Frontend-defined) |
|---|---|---|
| Validation errors | 400 | "Invalid input. Please check your entries." |
| Overlap conflict | 409 | "Time conflict detected. Please select a different time." |
| Unauthorized | 401 | Redirect to login page |
| Forbidden | 403 | "You don't have permission to perform this action." |
| Server error | 500 | "Something went wrong. Please try again." |
| Success | 200, 201 | "Session saved successfully" / "Changes saved" |

**Backend Response Format:**
```json
{
  "success": false,
  "statusCode": 409,
  "error": "CONFLICT",
  "message": "Session overlaps with existing session from 09:00 to 12:00",
  "details": {}
}
```

> **Note:** Frontend maps `error` code (e.g., `CONFLICT`, `VALIDATION_ERROR`) to generic toast message. Backend `message` is for debugging only, not shown to users.

---

## 12. Testing Strategy

### 12.1 Unit Testing

- Each service function must have unit tests covering the happy path and all edge cases.
- Validation logic must be tested independently from the controller layer.

---

### 12.2 Integration Testing

- Test the full request-response cycle for each API endpoint.
- Verify that authentication middleware correctly blocks unauthenticated requests.
- Verify that role-based access control (admin vs user) is enforced on protected endpoints.

---

### 12.3 API Testing

All endpoints shall be tested using **Postman** or equivalent tooling. A Postman collection shall be maintained alongside the codebase.

---

### 12.4 Test Cases

#### Time Sessions

| ID | Category | Test Case | Expected Result |
|---|---|---|---|
| TC-TS-01 | Create | Create session with all required fields | 201 Created; status = SUBMITTED |
| TC-TS-02 | Create | Create session where start >= end | 400 Validation Error |
| TC-TS-03 | Create | Create session overlapping existing session | 409 Conflict |
| TC-TS-04 | Edit | Edit a SUBMITTED session | 200 OK |
| TC-TS-05 | Edit | Edit an APPROVED session | 403 Forbidden |
| TC-TS-06 | Edit | Edit a REJECTED session | 403 Forbidden |
| TC-TS-07 | Delete | Soft-delete a SUBMITTED session | 200 OK; isDeleted = true |
| TC-TS-08 | List | Get sessions; deleted records excluded | 200 OK; no deleted records |
| TC-TS-09 | Approve | Manager approves SUBMITTED session | 200 OK; status = APPROVED |
| TC-TS-10 | Reject | Manager rejects SUBMITTED session | 200 OK; status = REJECTED |

#### Overtime

| ID | Category | Test Case | Expected Result |
|---|---|---|---|
| TC-OT-01 | Manual OT | Create session with manual OT, no template | 201 Created; OT status = PENDING |
| TC-OT-02 | Manual OT | Create manual OT without a reason | 400 Validation Error |
| TC-OT-03 | Manual OT | Admin approves manual OT | 200 OK; OT status = APPROVED |
| TC-OT-04 | Manual OT | Admin rejects manual OT | 200 OK; OT status = REJECTED |
| TC-OT-05 | Template OT | Create session using approved template | 201 Created; OT status = APPROVED |
| TC-OT-06 | Template OT | Use a PENDING (unapproved) template | 400 – Template not approved |
| TC-OT-07 | Template OT | Log OT within allowed time range | 201 Created |
| TC-OT-08 | Template OT | Log OT outside allowed time range | 400 – Time outside allowed range |

#### Overtime Templates

| ID | Category | Test Case | Expected Result |
|---|---|---|---|
| TC-TPL-01 | Visibility | Fetch templates for matching date | Returns only APPROVED templates for that date |
| TC-TPL-02 | Visibility | Fetch templates for non-matching date | Returns empty list |
| TC-TPL-03 | Approve | Admin approves PENDING template | 200 OK; status = APPROVED |
| TC-TPL-04 | Reject | Admin rejects PENDING template | 200 OK; status = REJECTED |
| TC-TPL-05 | Access | Non-admin calls approve endpoint | 403 Forbidden |
| TC-TPL-06 | Delete | Soft-delete a template | 200 OK; isDeleted = true |

#### Edge Cases

| TC-EDGE-01 | Session | Session spanning midnight (e.g. 23:00–01:00) | Should be handled correctly or rejected per config |
| TC-TS-10 | Create | Create session exceeding max duration | 400 – Exceeds maximum duration |
| TC-EDGE-03 | OT | OT minutes = 0 | 400 Validation Error |
| TC-EDGE-04 | Session | Missing `userId` in request | 400 or 401 |
| TC-EDGE-05 | Template | Template with start time = end time | 400 Validation Error |

---

## 13. Out of Scope

The following items are explicitly out of scope for this release:

| Item | Description |
|---|---|
| Payroll integration | No direct integration with payroll systems |
| Multi-timezone support | Single timezone per user only |
| Offline mode | Application requires internet connection |
| Mobile app | Web-only application |
| Advanced reporting | Basic lists only; no charts/analytics |
| Email notifications | No automated email alerts |
| Audit logging | No detailed change history tracking |
| Bulk operations | No mass approve/reject for managers |
| Vacation/PTO tracking | Separate system; not included |
| Manager self-service | Manager assignments managed by admin only |

---

## 14. Open Issues & Future Enhancements

| ID | Type | Description | Priority |
|---|---|---|---|
| OI-02 | Open Issue | Timezone handling strategy not confirmed | High |
| OI-03 | Open Issue | Maximum overtime minutes per session — threshold TBD | Medium |
| OI-04 | Open Issue | Charge code and step-up role lookup API not yet specified | Medium |
| FE-01 | Future Enhancement | Recurring overtime templates (weekly/monthly) | Low |
| FE-02 | Future Enhancement | Email/push notifications for approvals and rejections | Low |
| FE-03 | Future Enhancement | Reporting dashboard — hours logged, OT summary | Medium |
| FE-04 | Future Enhancement | Audit trail / change history per session | Medium |
| FE-05 | Future Enhancement | Shift enforcement rules | Low |
| FE-06 | Future Enhancement | Bulk session approval for managers | Low |

---

## 15. Appendix

### 15.1 Status Enum Reference

| Enum Value | Applies To | Meaning |
|---|---|---|
| `SUBMITTED` | Time Session | Submitted by user, awaiting manager/admin approval |
| `APPROVED` | Time Session, OT Template, Manual OT | Confirmed/approved — locked for editing |
| `REJECTED` | Time Session, OT Template, Manual OT | Declined — user can edit and resubmit |
| `PENDING` | OT Template, Manual OT | Awaiting admin action |

---

### 15.2 API Response Format Reference

**Success response:**
```json
{
  "success": true,
  "statusCode": 200,
  "data": { }
}
```

**Error response:**
```json
{
  "success": false,
  "statusCode": 400,
  "error": "VALIDATION_ERROR",
  "message": "Human-readable error description.",
  "details": { }
}
```

---

### 15.3 Glossary Quick Reference

| Term | Meaning |
|---|---|
| Manual OT | Overtime added without a template; requires approval |
| Template OT | Overtime added using an approved template; auto-approved |
| Soft Delete | Record marked as deleted but retained in database |
| Overlap | Two sessions sharing any portion of their time window |
| Allowed Time Range | The window defined by a template within which OT may be logged |
| Exact Date Match | `template.date === session.date` as YYYY-MM-DD strings |

---

### 15.4 Configurable Business Rules

The system supports **editable business rules** via a configuration file (`rules.json` or `rules.yaml`). Rules are loaded at startup and cached. Changes require a restart to take effect.

#### Configuration File Format (`rules.json`)

```json
{
  "validation": {
    "maxSessionDuration": {
      "value": 1440,
      "unit": "minutes",
      "errorMessage": "Session exceeds maximum duration"
    },
    "minSessionDuration": {
      "value": 1,
      "unit": "minutes",
      "errorMessage": "Session too short"
    }
  },
  "limits": {
    "maxOvertimeDaily": 240,
    "maxSessionsPerDay": 10
  },
  "business": {
    "allowWeekendWork": true,
    "allowHolidayWork": true,
    "overtimeRequiresReason": true,
    "autoApproveTemplateOvertime": true,
    "sessionOverlapCheck": true
  }
}
```

#### Rule Categories

| Category | Description | Rules |
|---|---|---|
| `validation` | Numeric constraints and thresholds | `maxSessionDuration`, `minSessionDuration` |
| `limits` | System usage limits | `maxOvertimeDaily`, `maxSessionsPerDay` |
| `business` | Feature toggles and behavior flags | `allowWeekendWork`, `overtimeRequiresReason` |

#### Rule Priority (Override Order)

1. **Environment variables** (e.g., `OVERTIME_MAX_DAILY=300`)
2. **Config file** (`rules.json`)
3. **Hardcoded defaults** (fallback)

> **Note:** Rules can be changed without code changes. Edit `rules.json` and restart the server.
