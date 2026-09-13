# KidBase — System Design

## Main Users

- Admin / Owner
- Staff
- Parent / Guardian

## Main System Modules

### Authentication & Access

Handles login, accounts, roles, and permissions.

### Child Management

Handles child profiles and enrollment information.

### Attendance

Handles attendance for both children and staff, including:

- Child check-in and check-out
- Staff clock-in and clock-out
- Daily attendance status
- Attendance history

### Document Management

Handles uploading, storing, and retrieving daycare documents.

### Staff Management

Handles staff profiles, employment information, and system access.

### Notifications

Handles parent notifications for child check-in and check-out.

## High-Level System Flow

```
User
  ↓
Frontend
  ↓
Backend API
  ↓
Business Logic
  ↓
Database / File Storage
```
