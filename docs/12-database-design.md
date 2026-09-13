# KidBase — Database Design / ERD

## 1. Database

**Database:** PostgreSQL

KidBase uses a relational database because the system has many connected entities — users, children, guardians, staff, enrollments, attendance records, documents, and notifications — that depend on each other through well-defined relationships.

## 2. Core ERD

```
                  ┌──────────────┐
                  │    roles     │
                  └──────┬───────┘
                         │ 1
                         │ *
                  ┌──────▼───────┐
                  │    users     │
                  └───┬─────┬────┘
                      │     │
           1 : 0..1   │     │ 1 : 0..1
                      │     │
         ┌────────────▼┐   ┌▼──────────────────┐
         │    staff    │   │  parent_guardians  │
         └──────┬──────┘   └────────┬───────────┘
                │                   │ *
                │            ┌──────▼────────────┐
                │            │  child_guardians  │
                │            └──────┬────────────┘
                │                   │ *
                │            ┌──────▼──────┐
                │            │   children  │
                │            └──┬───┬───┬──┘
                │               │   │   │
                │            1  │   │ 1 │
                │               ▼   │   ▼
                │    ┌─────────────┐│ ┌──────────┐
                │    │ enrollments ││ │documents │
                │    └─────────────┘│ └──────────┘
                │                   │ *
                │            ┌──────▼──────────┐
                │            │ child_attendance │
                │            └─────────────────┘
                │
         ┌──────▼──────────────┐
         │   staff_attendance  │
         └─────────────────────┘

parent_guardians 1 ────< notifications *
children         1 ────< notifications *
```

## 3. `roles`

Stores the available roles in KidBase.

| Field | Type | Constraints |
| --- | --- | --- |
| id | BIGSERIAL | PK |
| name | VARCHAR(50) | UNIQUE NOT NULL |
| description | VARCHAR(255) |  |
| created_at | TIMESTAMP | NOT NULL |

**Example records:** `1 — ADMIN`, `2 — STAFF`, `3 — PARENT`

**Relationship:** `roles 1 ───< users *`

## 4. `users`

Stores authentication and system account information.

| Field | Type | Constraints |
| --- | --- | --- |
| id | BIGSERIAL | PK |
| role_id | BIGINT | FK → roles.id |
| email | VARCHAR(255) | UNIQUE NOT NULL |
| password_hash | VARCHAR(255) | NOT NULL |
| first_name | VARCHAR(100) | NOT NULL |
| last_name | VARCHAR(100) | NOT NULL |
| phone_number | VARCHAR(30) |  |
| account_status | VARCHAR(30) | NOT NULL |
| is_active | BOOLEAN | NOT NULL DEFAULT TRUE |
| last_login_at | TIMESTAMP |  |
| created_at | TIMESTAMP | NOT NULL |
| updated_at | TIMESTAMP | NOT NULL |

**Possible `account_status` values:** ACTIVE, INACTIVE, LOCKED, PENDING

**Important:** Never store `password`. Always store `password_hash`.

## 5. `children`

Stores the main profile for each child.

| Field | Type | Constraints |
| --- | --- | --- |
| id | BIGSERIAL | PK |
| first_name | VARCHAR(100) | NOT NULL |
| middle_name | VARCHAR(100) |  |
| last_name | VARCHAR(100) | NOT NULL |
| date_of_birth | DATE | NOT NULL |
| gender | VARCHAR(30) |  |
| preferred_name | VARCHAR(100) |  |
| allergies | TEXT |  |
| medical_notes | TEXT |  |
| special_notes | TEXT |  |
| enrollment_status | VARCHAR(30) | NOT NULL |
| created_at | TIMESTAMP | NOT NULL |
| updated_at | TIMESTAMP | NOT NULL |

**Possible `enrollment_status` values:** ACTIVE, PENDING, WITHDRAWN, GRADUATED, INACTIVE

**Important:** Never delete a child from the database when they leave. Instead set `enrollment_status = WITHDRAWN`. This preserves all historical records.

## 6. `parent_guardians`

Stores parent and guardian personal information.

| Field | Type | Constraints |
| --- | --- | --- |
| id | BIGSERIAL | PK |
| user_id | BIGINT | UNIQUE FK → users.id |
| first_name | VARCHAR(100) | NOT NULL |
| last_name | VARCHAR(100) | NOT NULL |
| email | VARCHAR(255) |  |
| phone_number | VARCHAR(30) |  |
| address_line1 | VARCHAR(255) |  |
| address_line2 | VARCHAR(255) |  |
| city | VARCHAR(100) |  |
| state | VARCHAR(100) |  |
| postal_code | VARCHAR(20) |  |
| created_at | TIMESTAMP | NOT NULL |
| updated_at | TIMESTAMP | NOT NULL |

**Why separate `users` and `parent_guardians`?**

- `users` represents system access.
- `parent_guardians` represents daycare domain information.
- A guardian can exist in the database before receiving a KidBase login.

## 7. `child_guardians`

Junction table linking children to their guardians (many-to-many: one child can have multiple guardians, and one guardian can have multiple children).

| Field | Type | Constraints |
| --- | --- | --- |
| id | BIGSERIAL | PK |
| child_id | BIGINT | NOT NULL FK → children.id |
| guardian_id | BIGINT | NOT NULL FK → parent_guardians.id |
| relationship_type | VARCHAR(50) |  |
| is_primary_guardian | BOOLEAN | DEFAULT FALSE |
| can_pick_up | BOOLEAN | DEFAULT TRUE |
| emergency_contact | BOOLEAN | DEFAULT FALSE |
| created_at | TIMESTAMP | NOT NULL |

**Constraint:** `UNIQUE(child_id, guardian_id)`

**Possible `relationship_type` values:** MOTHER, FATHER, LEGAL_GUARDIAN, GRANDPARENT, OTHER

## 8. `staff`

Stores staff-specific employment information.

| Field | Type | Constraints |
| --- | --- | --- |
| id | BIGSERIAL | PK |
| user_id | BIGINT | UNIQUE NOT NULL FK → users.id |
| employee_number | VARCHAR(50) | UNIQUE |
| job_title | VARCHAR(100) |  |
| hire_date | DATE |  |
| employment_status | VARCHAR(30) | NOT NULL |
| created_at | TIMESTAMP | NOT NULL |
| updated_at | TIMESTAMP | NOT NULL |

**Possible `employment_status` values:** ACTIVE, INACTIVE, ON_LEAVE, TERMINATED

**Example `job_title` values:** Director, Teacher, Teacher Assistant, Administrator

## 9. `enrollments`

Represents a child's enrollment period. Not embedded directly inside `children` because a child could leave and later return (e.g. `Enrollment 2025 (COMPLETED)`, `Enrollment 2026 (ACTIVE)`), so one child can have multiple enrollment periods rather than exactly one enrollment forever.

| Field | Type | Constraints |
| --- | --- | --- |
| id | BIGSERIAL | PK |
| child_id | BIGINT | NOT NULL FK → children.id |
| start_date | DATE | NOT NULL |
| end_date | DATE |  |
| status | VARCHAR(30) | NOT NULL |
| enrollment_type | VARCHAR(50) |  |
| notes | TEXT |  |
| created_at | TIMESTAMP | NOT NULL |
| updated_at | TIMESTAMP | NOT NULL |

**Possible `status` values:** PENDING, ACTIVE, COMPLETED, CANCELLED, WITHDRAWN

**Possible `enrollment_type` values:** FULL_TIME, PART_TIME, TEMPORARY

## 10. `child_attendance`

Records daily check-in and check-out for each child.

| Field | Type | Constraints |
| --- | --- | --- |
| id | BIGSERIAL | PK |
| child_id | BIGINT | NOT NULL FK → children.id |
| attendance_date | DATE | NOT NULL |
| check_in_time | TIMESTAMP |  |
| check_out_time | TIMESTAMP |  |
| checked_in_by | BIGINT | FK → users.id |
| checked_out_by | BIGINT | FK → users.id |
| pickup_guardian_id | BIGINT | FK → parent_guardians.id |
| status | VARCHAR(30) | NOT NULL |
| notes | TEXT |  |
| created_at | TIMESTAMP | NOT NULL |
| updated_at | TIMESTAMP | NOT NULL |

**Possible `status` values:** PRESENT, ABSENT, LATE, EXCUSED, CHECKED_OUT

**Relationship:** `children 1 ───< child_attendance *` — one child can have thousands of attendance records over time.

## 11. `staff_attendance`

Records daily clock-in and clock-out for each staff member. Kept separate from `child_attendance` because staff attendance and child attendance have different business rules, fields, and purposes.

| Field | Type | Constraints |
| --- | --- | --- |
| id | BIGSERIAL | PK |
| staff_id | BIGINT | NOT NULL FK → staff.id |
| attendance_date | DATE | NOT NULL |
| clock_in_time | TIMESTAMP |  |
| clock_out_time | TIMESTAMP |  |
| status | VARCHAR(30) | NOT NULL |
| notes | TEXT |  |
| created_at | TIMESTAMP | NOT NULL |
| updated_at | TIMESTAMP | NOT NULL |

**Possible `status` values:** PRESENT, ABSENT, LATE, ON_LEAVE, SICK

**Relationship:** `staff 1 ───< staff_attendance *`

## 12. `documents`

Stores metadata about uploaded files. The actual file is not stored in PostgreSQL — only its metadata:

```
PostgreSQL  →  File metadata (this table)
AWS S3      →  Actual PDF / image (Phase 3)
```

| Field | Type | Constraints |
| --- | --- | --- |
| id | BIGSERIAL | PK |
| child_id | BIGINT | FK → children.id |
| guardian_id | BIGINT | FK → parent_guardians.id |
| staff_id | BIGINT | FK → staff.id |
| uploaded_by | BIGINT | NOT NULL FK → users.id |
| document_type | VARCHAR(100) | NOT NULL |
| original_filename | VARCHAR(255) | NOT NULL |
| storage_key | VARCHAR(500) | NOT NULL |
| mime_type | VARCHAR(100) |  |
| file_size | BIGINT |  |
| uploaded_at | TIMESTAMP | NOT NULL |
| expiration_date | DATE |  |
| status | VARCHAR(30) | NOT NULL |

**Possible `document_type` values:** ENROLLMENT_FORM, MEDICAL_FORM, IMMUNIZATION_RECORD, EMERGENCY_FORM, CONTRACT, LICENSE_FORM, OTHER

**Possible `status` values:** ACTIVE, EXPIRED, ARCHIVED

## 13. `notifications`

Tracks every notification generated by KidBase.

| Field | Type | Constraints |
| --- | --- | --- |
| id | BIGSERIAL | PK |
| recipient_user_id | BIGINT | NOT NULL FK → users.id |
| child_id | BIGINT | FK → children.id |
| type | VARCHAR(50) | NOT NULL |
| title | VARCHAR(255) |  |
| message | TEXT | NOT NULL |
| status | VARCHAR(30) | NOT NULL |
| created_at | TIMESTAMP | NOT NULL |
| sent_at | TIMESTAMP |  |
| read_at | TIMESTAMP |  |

**Possible `type` values:** CHECK_IN, CHECK_OUT, INCIDENT, DOCUMENT, PAYMENT, GENERAL

**Possible `status` values:** PENDING, SENT, FAILED, READ

**Example:** "Emma was checked in at 8:07 AM."

## 14. Full Relationship Model

```
roles
  │ 1
  ▼
users
  │
  ├── staff
  │      └── staff_attendance
  │
  ├── parent_guardians
  │      └── notifications
  │
  ├── notifications
  └── documents (uploaded_by)

parent_guardians
       │ *
       ▼
child_guardians
       │ *
       ▼
children
   │
   ├── enrollments
   ├── child_attendance
   ├── documents
   └── notifications
```

## 15. Foreign Key Reference Table

| Table | Foreign Key | References |
| --- | --- | --- |
| users | role_id | roles.id |
| parent_guardians | user_id | users.id |
| staff | user_id | users.id |
| child_guardians | child_id | children.id |
| child_guardians | guardian_id | parent_guardians.id |
| enrollments | child_id | children.id |
| child_attendance | child_id | children.id |
| child_attendance | checked_in_by | users.id |
| child_attendance | checked_out_by | users.id |
| child_attendance | pickup_guardian_id | parent_guardians.id |
| staff_attendance | staff_id | staff.id |
| documents | child_id | children.id |
| documents | guardian_id | parent_guardians.id |
| documents | staff_id | staff.id |
| documents | uploaded_by | users.id |
| notifications | recipient_user_id | users.id |
| notifications | child_id | children.id |

## 16. Database Constraints

Never rely only on Java validation — the database itself must protect data integrity:

- `email` → UNIQUE
- `role_id` → NOT NULL
- `child_id` → NOT NULL
- `date_of_birth` → NOT NULL
- `check_out_time >= check_in_time` → CHECK constraint

Always enforce appropriate `NOT NULL`, `UNIQUE`, `FOREIGN KEY`, `CHECK`, and `DEFAULT` constraints.

## 17. Soft Deletion Policy

Never physically delete important business records. Physically deleting a row from `children`, for example, risks also affecting related attendance, documents, enrollment, and notification records. KidBase uses status fields instead:

| Entity | Instead of DELETE | Use |
| --- | --- | --- |
| Child | DELETE | enrollment_status = WITHDRAWN |
| Staff | DELETE | employment_status = INACTIVE |
| User | DELETE | is_active = FALSE |
| Enrollment | DELETE | status = COMPLETED |
| Document | DELETE | status = ARCHIVED |
