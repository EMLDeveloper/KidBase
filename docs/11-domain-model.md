# KidBase — Domain Model

## What is a Domain Model?

A domain model represents the core business entities of KidBase — the real-world objects the system needs to store, manage, and track.

## Simplified Relationship Overview

```
ParentGuardian
      │
      │ many-to-many
      ▼
    Child
      │
      ├── Enrollment
      ├── ChildAttendance
      └── Documents

Staff
      │
      └── StaffAttendance

User
      │
      └── Role

ParentGuardian
      │
      └── Notifications
```

## Entities & Fields

### 1. User (auth module)

Represents any person who has access to the system.

| Field | Type | Description |
| --- | --- | --- |
| id | UUID | Primary key |
| email | String | Unique login email |
| password | String | Encrypted with BCrypt |
| role | Enum | ADMIN, STAFF, PARENT |
| created_at | DateTime | Account creation date |
| is_active | Boolean | Account status |

### 2. Child (child module)

Represents a child enrolled in the daycare.

| Field | Type | Description |
| --- | --- | --- |
| id | UUID | Primary key |
| first_name | String | Child first name |
| last_name | String | Child last name |
| date_of_birth | Date | Child date of birth |
| gender | Enum | MALE, FEMALE |
| allergies | String | Known allergies |
| medical_notes | String | Medical observations |
| enrollment_status | Enum | ACTIVE, WAITLIST, WITHDRAWN |
| enrolled_at | DateTime | Enrollment date |
| created_at | DateTime | Record creation date |

### 3. ParentGuardian (guardian module)

Represents a parent or guardian linked to one or more children.

| Field | Type | Description |
| --- | --- | --- |
| id | UUID | Primary key |
| user_id | UUID (FK) | Links to User account |
| first_name | String | Parent first name |
| last_name | String | Parent last name |
| phone | String | Contact number |
| relationship | Enum | MOTHER, FATHER, GUARDIAN |
| is_emergency_contact | Boolean | Emergency contact flag |

### 4. Child_Guardian (guardian module)

Junction entity linking children to their guardians (many-to-many).

| Field | Type | Description |
| --- | --- | --- |
| child_id | UUID (FK) | Links to Child |
| guardian_id | UUID (FK) | Links to ParentGuardian |

### 5. Staff (staff module)

Represents a daycare employee or assistant.

| Field | Type | Description |
| --- | --- | --- |
| id | UUID | Primary key |
| user_id | UUID (FK) | Links to User account |
| first_name | String | Staff first name |
| last_name | String | Staff last name |
| phone | String | Contact number |
| position | Enum | TEACHER, ASSISTANT, DIRECTOR |
| hired_at | Date | Hire date |
| is_active | Boolean | Employment status |

### 6. Enrollment (enrollment module)

Represents a child's enrollment period in the daycare. Modeled separately from Child so a child can have multiple enrollment periods over time.

| Field | Type | Description |
| --- | --- | --- |
| id | UUID | Primary key |
| child_id | UUID (FK) | Links to Child |
| start_date | Date | Enrollment start date |
| end_date | Date | Enrollment end date |
| status | Enum | PENDING, ACTIVE, COMPLETED, CANCELLED, WITHDRAWN |
| enrollment_type | Enum | FULL_TIME, PART_TIME, TEMPORARY |

### 7. ChildAttendance (attendance module)

Represents a child's check-in and check-out records.

| Field | Type | Description |
| --- | --- | --- |
| id | UUID | Primary key |
| child_id | UUID (FK) | Links to Child |
| staff_id | UUID (FK) | Staff who processed it |
| check_in_time | DateTime | Arrival timestamp |
| check_out_time | DateTime | Departure timestamp |
| date | Date | Attendance date |
| status | Enum | PRESENT, ABSENT, LATE |
| notes | String | Optional observations |

### 8. StaffAttendance (attendance module)

Represents a staff member's clock-in and clock-out records.

| Field | Type | Description |
| --- | --- | --- |
| id | UUID | Primary key |
| staff_id | UUID (FK) | Links to Staff |
| clock_in_time | DateTime | Arrival timestamp |
| clock_out_time | DateTime | Departure timestamp |
| date | Date | Attendance date |
| notes | String | Optional observations |

### 9. Document (document module)

Represents a file associated with a child, guardian, or staff member.

| Field | Type | Description |
| --- | --- | --- |
| id | UUID | Primary key |
| child_id | UUID (FK) | Links to Child |
| file_name | String | Original file name |
| file_path | String | Local path (Phase 1) |
| file_url | String | AWS S3 URL (Phase 3) |
| document_type | Enum | CONTRACT, VACCINATION, LICENSE, OTHER |
| uploaded_by | UUID (FK) | Staff who uploaded it |
| uploaded_at | DateTime | Upload timestamp |

### 10. Notification (notification module)

Represents a system notification sent to a parent or guardian.

| Field | Type | Description |
| --- | --- | --- |
| id | UUID | Primary key |
| parent_id | UUID (FK) | Links to ParentGuardian |
| child_id | UUID (FK) | Links to Child |
| type | Enum | CHECKIN, CHECKOUT, INCIDENT, GENERAL |
| message | String | Notification content |
| sent_at | DateTime | When it was sent |
| status | Enum | SENT, FAILED, PENDING |

## Entity Relationships

- A User has one Role.
- A ParentGuardian may be associated with one or more Children (via Child_Guardian).
- A Child may have one or more ParentGuardians (via Child_Guardian).
- A Child has one or more Enrollment records.
- A Child can have many ChildAttendance records.
- A Staff member can have many StaffAttendance records.
- A Child can have many Documents.
- A ParentGuardian can receive many Notifications.

## Design Decisions

- **UUID over integer IDs** — more secure, better for future distributed systems, industry standard.
- **Enums for status fields** — prevents invalid data and self-documents the system.
- **User entity is separate from Staff/ParentGuardian** — authentication is decoupled from business profiles.
- **Document has both file_path and file_url** — file_path is used locally in Phase 1, file_url is populated once AWS S3 is connected in Phase 3.
- **Every notification is logged** — every notification sent is recorded for history and debugging.
