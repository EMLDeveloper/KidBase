# KidBase — API Design

## API Standards

- **Style:** RESTful API
- **Format:** JSON
- **Authentication:** JWT Bearer Token
- **Base URL:** `/api/v1`
- **Documentation:** Swagger / OpenAPI

## Authentication Headers

Every protected endpoint requires:

```
Authorization: Bearer <token>
```

## HTTP Status Codes Used

| Code | Meaning |
| --- | --- |
| 200 | OK — request succeeded |
| 201 | Created — resource created |
| 204 | No Content — deleted successfully |
| 400 | Bad Request — invalid input |
| 401 | Unauthorized — missing or invalid token |
| 403 | Forbidden — not enough permissions |
| 404 | Not Found — resource does not exist |
| 409 | Conflict — duplicate resource |
| 500 | Internal Server Error |

## 1. Auth Endpoints

**Base:** `/api/v1/auth`

| Method | Endpoint | Description | Access |
| --- | --- | --- | --- |
| POST | /auth/register | Register a new parent account | Public |
| POST | /auth/login | Login and receive JWT token | Public |
| PUT | /auth/change-password | Change user password | All roles |

**Important decisions:**

- Public registration always creates a PARENT account.
- ADMIN and STAFF accounts are created only through protected admin endpoints.
- A malicious user cannot self-assign a role.
- `/auth/logout` is intentionally excluded from the MVP. Logout is handled on the frontend by discarding the token. Token invalidation via blacklist or refresh token revocation will be considered in a future version.

### POST /auth/register

**Request:**

```json
{
  "email": "maria@email.com",
  "password": "securepassword",
  "first_name": "Maria",
  "last_name": "Lopez",
  "phone_number": "510-555-0101"
}
```

**Response 201:**

```json
{
  "id": 1,
  "email": "maria@email.com",
  "role": "PARENT",
  "created_at": "2026-09-12T08:00:00"
}
```

### POST /auth/login

**Request:**

```json
{
  "email": "maria@email.com",
  "password": "securepassword"
}
```

**Response 200:**

```json
{
  "token": "eyJhbGciOiJIUzI1NiJ9...",
  "role": "PARENT",
  "expires_at": "2026-09-13T08:00:00"
}
```

## 2. Admin User Management

**Base:** `/api/v1/admin/users`

Admin creates and manages ADMIN and STAFF accounts through protected endpoints. These are never publicly accessible.

| Method | Endpoint | Description | Access |
| --- | --- | --- | --- |
| POST | /admin/users | Create ADMIN or STAFF account | ADMIN |
| GET | /admin/users | Get all system users | ADMIN |
| GET | /admin/users/{id} | Get one user by ID | ADMIN |
| PATCH | /admin/users/{id}/status | Activate or deactivate account | ADMIN |

### POST /admin/users

**Request:**

```json
{
  "email": "sarah@kidbase.com",
  "password": "securepassword",
  "first_name": "Sarah",
  "last_name": "Johnson",
  "phone_number": "510-555-0202",
  "role": "STAFF"
}
```

**Response 201:**

```json
{
  "id": 5,
  "email": "sarah@kidbase.com",
  "role": "STAFF",
  "created_at": "2026-09-12T08:00:00"
}
```

## 3. Children Endpoints

**Base:** `/api/v1/children`

| Method | Endpoint | Description | Access |
| --- | --- | --- | --- |
| POST | /children | Register a new child | ADMIN |
| GET | /children | Get all children | ADMIN, STAFF |
| GET | /children/{id} | Get one child by ID | ADMIN, STAFF |
| PUT | /children/{id} | Update child information | ADMIN |
| PATCH | /children/{id}/status | Update enrollment status | ADMIN |
| GET | /children/{id}/guardians | Get child's guardians | ADMIN, STAFF |
| GET | /children/{id}/documents | Get child's documents | ADMIN, STAFF |
| GET | /children/{id}/attendance | Get child's attendance history | ADMIN, STAFF |
| GET | /children/{id}/enrollments | Get child's enrollment history | ADMIN |

### POST /children

**Request:**

```json
{
  "first_name": "Emma",
  "middle_name": "Rose",
  "last_name": "Garcia",
  "date_of_birth": "2021-03-15",
  "gender": "FEMALE",
  "preferred_name": "Emmy",
  "allergies": "Peanuts",
  "medical_notes": "Uses inhaler",
  "special_notes": "Needs extra attention during nap",
  "enrollment_status": "ACTIVE"
}
```

**Response 201:**

```json
{
  "id": 1,
  "first_name": "Emma",
  "last_name": "Garcia",
  "enrollment_status": "ACTIVE",
  "created_at": "2026-09-12T08:00:00"
}
```

### GET /children

**Response 200:**

```json
[
  {
    "id": 1,
    "first_name": "Emma",
    "last_name": "Garcia",
    "enrollment_status": "ACTIVE",
    "date_of_birth": "2021-03-15"
  },
  {
    "id": 2,
    "first_name": "Lucas",
    "last_name": "Martinez",
    "enrollment_status": "ACTIVE",
    "date_of_birth": "2022-07-20"
  }
]
```

## 4. Guardian Endpoints

**Base:** `/api/v1/guardians`

| Method | Endpoint | Description | Access |
| --- | --- | --- | --- |
| POST | /guardians | Add a new guardian | ADMIN |
| GET | /guardians | Get all guardians | ADMIN |
| GET | /guardians/{id} | Get one guardian by ID | ADMIN, STAFF |
| PUT | /guardians/{id} | Update guardian information | ADMIN |
| POST | /guardians/{id}/children | Link guardian to a child | ADMIN |
| DELETE | /guardians/{id}/children/{childId} | Unlink guardian from child | ADMIN |

### POST /guardians

**Request:**

```json
{
  "first_name": "Maria",
  "last_name": "Garcia",
  "email": "maria@email.com",
  "phone_number": "510-555-0101",
  "address_line1": "123 Main St",
  "city": "Castro Valley",
  "state": "CA",
  "postal_code": "94546"
}
```

**Response 201:**

```json
{
  "id": 1,
  "first_name": "Maria",
  "last_name": "Garcia",
  "created_at": "2026-09-12T08:00:00"
}
```

## 5. Staff Endpoints

**Base:** `/api/v1/staff`

| Method | Endpoint | Description | Access |
| --- | --- | --- | --- |
| POST | /staff | Add a new staff member | ADMIN |
| GET | /staff | Get all staff members | ADMIN |
| GET | /staff/{id} | Get one staff member by ID | ADMIN |
| PUT | /staff/{id} | Update staff information | ADMIN |
| PATCH | /staff/{id}/status | Update employment status | ADMIN |
| GET | /staff/{id}/attendance | Get staff attendance history | ADMIN |

### POST /staff

**Request:**

```json
{
  "user_id": 5,
  "employee_number": "EMP-001",
  "job_title": "Teacher",
  "hire_date": "2024-01-15",
  "employment_status": "ACTIVE"
}
```

**Response 201:**

```json
{
  "id": 1,
  "employee_number": "EMP-001",
  "job_title": "Teacher",
  "employment_status": "ACTIVE",
  "created_at": "2026-09-12T08:00:00"
}
```

## 6. Enrollment Endpoints

**Base:** `/api/v1/enrollments`

| Method | Endpoint | Description | Access |
| --- | --- | --- | --- |
| POST | /enrollments | Create a new enrollment | ADMIN |
| GET | /enrollments | Get all enrollments | ADMIN |
| GET | /enrollments/{id} | Get one enrollment by ID | ADMIN |
| PUT | /enrollments/{id} | Update enrollment | ADMIN |
| PATCH | /enrollments/{id}/status | Update enrollment status | ADMIN |

### POST /enrollments

**Request:**

```json
{
  "child_id": 1,
  "start_date": "2026-09-01",
  "end_date": null,
  "status": "ACTIVE",
  "enrollment_type": "FULL_TIME",
  "notes": "Started in September cohort"
}
```

**Response 201:**

```json
{
  "id": 1,
  "child_id": 1,
  "start_date": "2026-09-01",
  "status": "ACTIVE",
  "enrollment_type": "FULL_TIME",
  "created_at": "2026-09-12T08:00:00"
}
```

## 7. Attendance Endpoints

**Base:** `/api/v1/attendance`

### Child Attendance

| Method | Endpoint | Description | Access |
| --- | --- | --- | --- |
| POST | /attendance/children/checkin | Check in a child | ADMIN, STAFF |
| POST | /attendance/children/checkout | Check out a child | ADMIN, STAFF |
| GET | /attendance/children/daily | Get today's attendance | ADMIN, STAFF |
| GET | /attendance/children/{childId}/history | Get child attendance history | ADMIN, STAFF |
| PATCH | /attendance/children/{id}/status | Update attendance status | ADMIN |

**Important decisions:**

- `checked_in_by` and `checked_out_by` are determined by the authenticated JWT — never trusted from the client request.
- `pickup_guardian_id` belongs to checkout, not check-in.

### POST /attendance/children/checkin

**Request:**

```json
{
  "child_id": 1,
  "notes": "Arrived with mom"
}
```

**Response 201:**

```json
{
  "id": 1,
  "child_id": 1,
  "attendance_date": "2026-09-12",
  "check_in_time": "2026-09-12T08:07:00",
  "checked_in_by": 3,
  "status": "PRESENT"
}
```

### POST /attendance/children/checkout

**Request:**

```json
{
  "child_id": 1,
  "pickup_guardian_id": 1,
  "notes": "Picked up by mom"
}
```

**Response 200:**

```json
{
  "id": 1,
  "child_id": 1,
  "attendance_date": "2026-09-12",
  "check_out_time": "2026-09-12T15:30:00",
  "checked_out_by": 3,
  "pickup_guardian_id": 1,
  "status": "CHECKED_OUT"
}
```

### Staff Attendance

| Method | Endpoint | Description | Access |
| --- | --- | --- | --- |
| POST | /attendance/staff/me/clockin | Staff clocks themselves in | STAFF |
| POST | /attendance/staff/me/clockout | Staff clocks themselves out | STAFF |
| GET | /attendance/staff/daily | Get today's staff attendance | ADMIN |
| GET | /attendance/staff/{staffId}/history | Get staff attendance history | ADMIN |

**Important decisions:**

- Staff clock themselves in and out via `/me` endpoints.
- The backend determines the staff member from the authenticated JWT.
- Admins have separate endpoints to view and manage staff attendance.

### POST /attendance/staff/me/clockin

**Request:**

```json
{
  "notes": "On time"
}
```

**Response 201:**

```json
{
  "id": 1,
  "staff_id": 2,
  "attendance_date": "2026-09-12",
  "clock_in_time": "2026-09-12T07:55:00",
  "status": "PRESENT"
}
```

### POST /attendance/staff/me/clockout

**Request:**

```json
{
  "notes": "End of shift"
}
```

**Response 200:**

```json
{
  "id": 1,
  "staff_id": 2,
  "attendance_date": "2026-09-12",
  "clock_out_time": "2026-09-12T16:00:00",
  "status": "PRESENT"
}
```

## 8. Document Endpoints

**Base:** `/api/v1/documents`

| Method | Endpoint | Description | Access |
| --- | --- | --- | --- |
| POST | /documents/upload | Upload a document | ADMIN, STAFF |
| GET | /documents/{id} | Get document metadata | ADMIN, STAFF |
| GET | /documents/{id}/download | Download a document | ADMIN, STAFF |
| PATCH | /documents/{id}/status | Archive a document | ADMIN |

**Important decision:**

- There is no DELETE endpoint for documents. Archiving is done via `PATCH status = ARCHIVED`, preserving all historical records — consistent with the soft deletion policy.

### POST /documents/upload

**Request (multipart/form-data):**

```
file: [binary file]
child_id: 1
document_type: ENROLLMENT_FORM
```

**Response 201:**

```json
{
  "id": 1,
  "child_id": 1,
  "document_type": "ENROLLMENT_FORM",
  "original_filename": "enrollment_emma.pdf",
  "storage_key": "documents/children/1/enrollment_emma.pdf",
  "uploaded_at": "2026-09-12T08:00:00",
  "status": "ACTIVE"
}
```

### PATCH /documents/{id}/status

**Request:**

```json
{
  "status": "ARCHIVED"
}
```

**Response 200:**

```json
{
  "id": 1,
  "status": "ARCHIVED",
  "updated_at": "2026-09-12T10:00:00"
}
```

## 9. Notification Endpoints

**Base:** `/api/v1/notifications`

| Method | Endpoint | Description | Access |
| --- | --- | --- | --- |
| GET | /notifications | Get my notifications | All roles |
| GET | /notifications/{id} | Get one notification | All roles |
| PATCH | /notifications/{id}/read | Mark as read | All roles |
| GET | /notifications/child/{childId} | Get notifications for a child | ADMIN, STAFF |

### GET /notifications

**Response 200:**

```json
[
  {
    "id": 1,
    "type": "CHECK_IN",
    "title": "Child Checked In",
    "message": "Emma was checked in at 8:07 AM.",
    "status": "SENT",
    "created_at": "2026-09-12T08:07:00",
    "read_at": null
  }
]
```

## 10. Parent Self-Service Endpoints

**Base:** `/api/v1/parents`

These endpoints allow a parent to access only their own data. Planned for post-MVP.

| Method | Endpoint | Description | Access |
| --- | --- | --- | --- |
| GET | /parents/me | Get my own profile | PARENT |
| GET | /parents/me/children | Get my children | PARENT |
| GET | /parents/me/notifications | Get my notifications | PARENT |

## API Design Decisions

| Decision | Reason |
| --- | --- |
| Public register always creates PARENT | Prevents role escalation attacks |
| No /auth/logout in MVP | Stateless JWT — frontend discards token |
| checked_in_by from JWT, not request | Never trust client-supplied identity |
| pickup_guardian_id on checkout only | Logically belongs to departure, not arrival |
| No DELETE on documents | Soft deletion policy — PATCH to ARCHIVED |
| /children/{id}/enrollments, not /enrollments/child/{id} | Consistent resource-oriented URL design |
| /attendance/staff/me endpoints | Staff identity determined from JWT |
| Versioning `/api/v1` | Allows future API versions without breaking clients |
