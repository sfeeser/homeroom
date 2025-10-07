Internet-Draft                                                 Alta3 Research
Intended status: Informational                                 October 2025
Expires: April 20, 2026

```
                            SpecBook: Homeroom
                    draft-feeser-homeroom-spec-01
```

Abstract

This document specifies Homeroom, a System of Engagement (SoE) designed
for training companies. Homeroom acts as the operational control tower
for live class delivery, orchestrating student identity, attendance,
communication, and access to third-party services like Zoom and live
lab environments. It functions as an autonomous service, maintaining a
tight, explicit integration dependency on the core data provided by the
Track3 CRM System of Record (SoR) via a defined Application Programming
Interface (API) contract.

Status of This Memo

This Internet-Draft is submitted in full conformance with the
provisions of BCP 78 and BCP 79.

Internet-Drafts are working documents of the Internet Engineering
Task Force (IETF). Note that other groups may also distribute
working documents as Internet-Drafts. The list of current Internet-
Drafts is at [https://datatracker.ietf.org/drafts/current/](https://datatracker.ietf.org/drafts/current/).

Internet-Drafts are draft documents valid for a maximum of six
months and may be updated, replaced, or obsoleted by other documents
at any time. It is inappropriate to use Internet-Drafts as reference
material or to cite them other than as "work in progress."

This Internet-Draft will expire on April 20, 2026.

Copyright Notice

Copyright (c) 2025 IETF Trust and the persons identified as the
document authors. All rights reserved.

This document is subject to BCP 78 and the IETF Trust's Legal
Provisions Relating to IETF Documents
([https://trustee.ietf.org/license-info](https://trustee.ietf.org/license-info)) in effect on the date of
publication of this document. Please review these documents
carefully, as they describe your rights and restrictions with
respect to this document.

Table of Contents

1.  Introduction
    1.1. Why Homeroom Exists
    1.2. How Homeroom Increases Efficiency
2.  System Metadata
3.  User Roles
4.  Class Lifecycle
5.  Modules  
    5.1. Authentication  
    5.2. Classroom Dashboard (Zoom Access Page)  
    5.3. Email Notification System  
    5.4. Roster Management  
    5.5. Attendance Verification  
    5.6. Certificate Management  
    5.7. Instructor Tools  
    5.8. Admin Dashboard  
6.  APIs and External Integrations  
    6.1. Third-Party Services  
    6.2. Track3 Integration Dependency (SoR Contract)  
7.  Data Models (Simplified)
8.  UI Spec (Wireframe-Level Summary)
9.  Automation Schedule
10. Security & Compliance
11. IANA Considerations
12. References
    Authors' Addresses

## 1\. Introduction

Homeroom serves as the gateway to learning — a unified entry point where students, instructors, and administrators begin their day. By automating the start-of-class workflow, Homeroom replaces dozens of manual operations with a single coherent system.

The result is a repeatable, auditable, and automated class-launch process that ensures every class runs smoothly and predictably, regardless of who teaches it or where it’s hosted.

Homeroom centralizes the operational heartbeat of a training company. It connects identity, communication, and attendance across the three key systems:

  * **Zoom** for live instruction
  * **Live (live.alta3.com)** for hands-on labs
  * **Track3 CRM** for class, roster, and certificate management

By consolidating these interactions, Homeroom becomes the trusted point of orchestration that guarantees a synchronized student experience from login to certification.

### 1.1. Why Homeroom Exists

Training delivery today often relies on a fragile chain of manual processes: class schedules stored in spreadsheets, rosters passed through resellers, student notifications sent via ad-hoc emails, and instructor details scattered across cloud folders.

Each of these steps introduces delay, inconsistency, and human error. Homeroom eliminates this fragmentation.

### 1.2. How Homeroom Increases Efficiency

Homeroom acts as the control tower for live education operations, improving efficiency at every step:

  * **Single Source of Truth:** Homeroom replaces the Class Schedule Spreadsheet as the authoritative record of classes, rosters, and schedules for operational staff.
  * **Automated Communication:** Reseller-specific rules are built directly into the system. Invitations, reminders, and roster changes trigger templated HTML emails automatically.
  * **Seamless Identity Flow:** A student’s single magic-link login establishes verified identity across Zoom, Live, and Track3. Their name, attendance, and completion record remain synchronized automatically.
  * **Operational Visibility:** The Classroom Dashboard provides real-time insight: who has joined, who is stuck, and whether Zoom and Live sessions are in sync.
  * **Scalability and Consistency:** Every class follows the same automated lifecycle — from creation to certification — regardless of course type or reseller.
  * **Auditability and Compliance:** All communications, attendance logs, and certificates are stored automatically, supporting full traceability and audit readiness.

-----

## 2\. System Metadata

| Field | Value |
| :--- | :--- |
| **Project** | Homeroom |
| **Purpose** | Provide a unified portal for students, instructors, and admins to manage class access, attendance, communications, and verification between Zoom, Live, and the CRM (Track3). |
| **Owner** | Alta3 Research |
| **SpecBook Version** | 0.1 |
| **Dependencies** | Zoom API, Live (labs) API, Track3 CRM API, SMTP / Email service, Google Drive (legacy integration) |

-----

## 3\. User Roles

| Role | Description | Permissions |
| :--- | :--- | :--- |
| **Admin** | Creates classrooms, schedules, and Zoom meetings. Manages rosters and notification logic. | Full access. |
| **Instructor** | Manages their assigned classes. Monitors student attendance, chat, and issues. | View/manage assigned classes only. |
| **Student** | Accesses class through Homeroom link, logs in via magic link, attends Zoom and Live, receives certificate. | Self-access only. |
| **Reseller** | Provides rosters and can control whether student communication is direct or mediated. | Limited API or upload interface. |

-----

## 4\. Class Lifecycle

| Stage | Description | Responsible Role | Outputs |
| :--- | :--- | :--- | :--- |
| **Creation** | Admin defines class metadata (title, date, time, course code, reseller, instructor). | Admin | Class record created |
| **Zoom Meeting Setup** | Automatically creates Zoom meeting via API. | Admin | Zoom Meeting ID + Passcode + Join URLs |
| **Roster Import** | Import from reseller spreadsheet or Track3 CRM API. | Admin | Normalized roster records |
| **Email Configuration** | Define per-reseller notification behavior (direct or indirect). | Admin | Email routing policy object |
| **Invitation Generation** | Send HTML invitations to all rostered students. | System | Email + Zoom Registration ID |
| **Daily Attendance Sync** | Cross-reference Zoom attendance and Live logs. | System | Attendance record (per student/day) |
| **Completion & Certificate** | Generate certificates and send via API. | System | Certificate issuance record |

-----

## 5\. Modules

### 5.1. Authentication

**Purpose:** Verify students, instructors, and admins using secure email-based login.

**Mechanism:**

  * Students log in via magic link (managed by the FIRM/Track3 identity flow).
  * The system matches email domain and user against roster.
  * Once verified, a persistent session cookie identifies them for subsequent days.

**Outputs:** Authenticated session token, Verified roster match event.

**Integration:** Zoom API (name injection), Live API (seat claim), Track3 CRM (student verification).

### 5.2. Classroom Dashboard (Zoom Access Page)

**Purpose:** Provide the shared entry point for instructors and students.

**Features:** Dynamic slug (e.g. `/classroom/happy-tigers-danced`), Displays instructor photo, class info, announcements. Links to Live and Academy. “Join Zoom” button creates a real-time tokenized Zoom session. Instructor chat panel for assisting students.

**States:**

  * **Pre-class:** Countdown timer, confirmation button (“Yes, I’m attending”).
  * **In session:** “Enter Classroom” → Zoom link with verified ID.
  * **Post-class:** Links to academy and feedback form.

**Integration:** Zoom API for registration and join URLs. Live API for lab seat reservation. Track3 CRM for student status updates.

### 5.3. Email Notification System

**Purpose:** Automate email communication while respecting reseller preferences.

**Rules:**

  * Each reseller defines their notification policy: `direct_to_student`, `via_reseller`, or `reseller_review_before_send`.
  * Beautiful HTML templates generated dynamically.

**Triggers:**

| Event | Timing | Template |
| :--- | :--- | :--- |
| Initial Invite | 7 days before | Course info, join link, confirmation button |
| Roster Update | Any time | Added/removed students only |
| Reminder | 2 days before | Confirmation + test connection |
| Final Reminder | 1 hour before | Direct join link |
| Daily Report | After each class day | Attendance summary to instructor/admin |

**Integration:** Zoom API (for registration), SMTP provider or SendGrid API, Track3 CRM (for communication logs).

### 5.4. Roster Management

**Purpose:** Normalize and reconcile student rosters across sources.

**Inputs:** CSV from reseller, Google Sheet import, **Track3 CRM API**.

**Process:** Validate email format and deduplicate. Mark intermediary ownership (reseller). Cross-reference Zoom registration. Mark attendance and certificate eligibility.

**Outputs:** Normalized roster table (name, email, reseller, class ID, attendance, certificate status).

### 5.5. Attendance Verification

**Purpose:** Match Zoom and Live session data.

**Process:** Fetch Zoom attendance via API (join/leave times). Fetch Live attendance logs. Compare via email hash. Generate daily and cumulative attendance reports.

**Outputs:** Attendance report (JSON + PDF), Flags for mismatched attendance.

**Integration:** Zoom API, Live API, **Track3 CRM (update participation status)**.

### 5.6. Certificate Management

**Purpose:** Replace external product with internal certificate generator.

**Workflow:** Triggered when final attendance meets completion criteria. Generates signed PDF certificate (student name, course, date). Sends via email + logs in **Track3 CRM**.

**Integration:** HTML → PDF generation service, **Track3 CRM API**, Email notification system.

### 5.7. Instructor Tools

**Purpose:** Simplify instructor class prep and real-time monitoring.

**Features:** Access to “Notes to Instructor” file. Real-time Zoom meeting status (via API). View and manage student roster. In-app chat for student assistance. Attendance verification overlay.

**Integration:** Zoom API, Google Drive (legacy), **Track3 CRM**.

### 5.8. Admin Dashboard

**Purpose:** Provide complete visibility across classes and rosters.

**Views:** Active and upcoming classes, Zoom meeting status (created / live / completed), Roster upload and change tracking, Email send status, Attendance reconciliation, Certification status.

**Integration:** All previous modules (read-only aggregate).

-----

## 6\. APIs and External Integrations

### 6.1. Third-Party Services

| Service | Purpose | Endpoint / Notes |
| :--- | :--- | :--- |
| Zoom API | Create meetings, register students, track attendance | `/users/{userId}/meetings`, `/meetings/{meetingId}/registrants` |
| Live API | Add verified students, track lab attendance | `/api/v1/student/sync` |
| Email Provider (SMTP/SendGrid) | Send templated HTML messages | `/send` |
| Google Drive (Legacy) | Read existing instructor folders and spreadsheets | `/drive/v3/files` |

### 6.2. Track3 Integration Dependency (SoR Contract)

**Homeroom is a System of Engagement (SoE) and Track3 is the System of Record (SoR).** The stability of Homeroom requires a definitive, contractually obligated API provided by the Track3 backend. Development of Homeroom functionality that relies on these APIs **MUST** be gated until the **Minimal Viable Track3 (MVS)** service delivers and validates these endpoints.

The primary interfaces between Homeroom and the Track3 SoR are defined as follows:

#### 6.2.1. API 1: Get Class Roster (Read-Only)

**Homeroom Purpose:** To fetch all essential, confirmed class details and the complete, current student roster to power the Classroom Dashboard and perform real-time integrations (Zoom/Live setup).

**Track3 Endpoint:** `GET /api/v1/classes/{class_id}/roster`

**Required Track3 Response (Minimal Data Contract):**

```json
{
  "class_id": "UUID",
  "course_name": "string",
  "course_code": "string",
  "start_date": "YYYY-MM-DD",
  "end_date": "YYYY-MM-DD",
  "delivery_mode": "course_mode_enum",
  "class_type": "class_type_enum",
  "instructor": {
    "employee_id": "UUID",
    "full_name": "string"
  },
  "operational_info": {
    "zoom_meeting_id": "string",
    "live_id": "string",
    "operational_docs_url": "string"
  },
  "roster": [
    {
      "enrollment_id": "UUID",       // PK for student_enrollments in Track3
      "customer_id": "UUID",         // FK to customers in Track3
      "customer_email": "string",
      "customer_full_name": "string",
      "enrolled_through_partner_id": "UUID",
      "partner_name": "string",
      "reseller_notification_policy": "string" // e.g., 'direct_to_student'
    }
  ]
}
```

#### 6.2.2. API 2: Post Attendance (Write-Back)

**Homeroom Purpose:** To write verifiable daily attendance logs back to the Track3 SoR for financial reconciliation and compliance tracking (replacing the manual spreadsheet update).

**Track3 Endpoint:** `POST /api/v1/enrollments/{enrollment_id}/attendance`

**Required Homeroom Request (Minimal Data Contract):**

```json
{
  "class_id": "UUID",               // For context and validation
  "enrollment_id": "UUID",
  "activity_date": "YYYY-MM-DD",    // The day attendance is being logged for
  "attended": "boolean",            // True if student was present for core session time
  "join_time": "TIMESTAMP WITH TIME ZONE", // First recorded join time (for audit)
  "leave_time": "TIMESTAMP WITH TIME ZONE",// Last recorded leave time (for audit)
  "duration_minutes": "integer",    // Total verified participation duration
  "source": "string"                // e.g., 'Zoom API', 'Live API', 'Instructor Override'
}
```

#### 6.2.3. API 3: Post Certificate Status (Write-Back)

**Homeroom Purpose:** To formally record in the Track3 SoR that the certificate generation and delivery process is complete, ensuring the status is reflected in sales and operational reporting.

**Track3 Endpoint:** `POST /api/v1/enrollments/{enrollment_id}/certificate`

**Required Homeroom Request (Minimal Data Contract):**

```json
{
  "enrollment_id": "UUID",
  "certificate_status": "string",           // ENUM: 'Generated', 'Sent', 'Failed'
  "certificate_reference_url": "string",    // URL to the permanent PDF artifact in cloud storage
  "completion_date": "YYYY-MM-DD"           // Date the student met the criteria/completed
}
```

-----

## 7\. Data Models (Simplified)

### Class

| Field | Type | Notes |
| :--- | :--- | :--- |
| `id` | UUID | Unique class identifier |
| `slug` | String | Human-readable URL slug |
| `title` | String | Course title |
| `start_date` | DateTime | Start time |
| `end_date` | DateTime | End time |
| `zoom_meeting_id` | String | Created via API |
| `reseller` | String | Reseller name |
| `instructor_id` | UUID | FK to Instructor |
| `live_id` | String | Lab environment reference |

### Student

| Field | Type | Notes |
| :--- | :--- | :--- |
| `id` | UUID | FK to Track3 Enrollment/Customer ID |
| `name` | String | Full name |
| `email` | String | Email address |
| `reseller` | String | Reseller name |
| `magic_link_token` | String | Generated for login |
| `zoom_reg_id` | String | Assigned via API |
| `class_id` | UUID | FK to Class |
| `attendance` | JSON | Per day log |
| `certificate_sent` | Boolean | |

-----

## 8\. UI Spec (Wireframe-Level Summary)

| Page | Purpose | Audience | Elements |
| :--- | :--- | :--- | :--- |
| `/classroom/:slug` | Entry portal | Students | Course info, instructor photo, Join Zoom, Join Live, Announcements |
| `/instructor/:slug` | Control panel | Instructors | Chat, roster, attendance, student status |
| `/admin/dashboard` | Class management | Admins | Create class, upload roster, configure email policy |
| `/certificate/:id` | View/download certificate | Students | Render PDF or view HTML version |

-----

## 9\. Automation Schedule

| Event | Time | Action |
| :--- | :--- | :--- |
| Initial Class Setup | 7 days before | Create Zoom meeting + send invites |
| Reminder | 2 days before | Send reminder email |
| Final Reminder | 1 hour before | Final email + Zoom token refresh |
| **Attendance Logging** | After each class day | Pull and reconcile attendance + **POST to Track3 API** |
| **Certification** | After class completion | Generate certificates + **POST to Track3 API** |
| Ongoing | Monitor resync | Monitor data consistency with Track3 CRM |

-----

## 10\. Security & Compliance

All student logins via magic link (managed by FIRM/Track3). All API integrations use OAuth2 or signed keys. Roster data encrypted at rest. Certificate generation logs retained for 1 year. GDPR compliance for data deletion requests. Email policies respect reseller preferences.

-----

## 11\. IANA Considerations

No IANA actions.

## 12\. References

[FIRM-AUTH] Feeser, S., "FIRM: First Identity Requires Mail Authentication and Authorization Protocol", Internet-Draft, draft-feeser-firm-auth-09, June 2025.

[TRACK3-CRM] Alta3 Research, "Track3: Training Company CRM System Specification", Internet-Draft, draft-track3-crm-00, July 2025.

Authors' Addresses
Stuart Feeser
Alta3 Research
Email: stuart.feeser@alta3.com
URI: [https://saayn.org/manifesto](https://saayn.org/manifesto)
