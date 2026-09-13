# KidBase — MVP Definition

## What is the MVP?

The MVP (Minimum Viable Product) is the smallest version of KidBase that is still useful and functional for a real daycare. It focuses only on the core problems identified in Problem Discovery — eliminating manual paperwork, centralizing records, and automating the most repetitive daily tasks — while providing a reliable digital foundation for future features.

## MVP Goal

> Enable a daycare owner and their staff to manage child enrollment, daily attendance, and parent notifications through one centralized digital platform — eliminating paper-based processes.

## What the MVP Includes

### 1. Authentication & Access Control

- Admin, Staff, and Parent roles
- Secure login with JWT tokens
- Each role sees only what they need

### 2. Child Enrollment

- Register a new child with full profile
- View and update child information
- Store emergency contacts and medical notes
- Upload and retrieve enrollment documents

### 3. Attendance

- Digital check-in and check-out for each child
- Timestamps and staff records for every entry
- View daily attendance list

### 4. Staff Management

- Add and manage staff members
- Assign roles and access levels

### 5. Parent Notifications

- Automatic notification when a child is checked in
- Automatic notification when a child is checked out
- Incident alerts sent immediately to parents

## What the MVP Does NOT Include

These are intentionally left out of the first version:

- Billing and invoice generation
- Payment tracking
- Infant monitoring logs
- Daily menus and activity schedules
- Staff scheduling
- Analytics and reporting dashboard
- Parent portal
- Mobile application
- Online payment processing
- AI-powered features
- Multi-location support
- Third-party integrations

## MVP Success Criteria

The MVP is complete and successful when:

- [ ] An admin can securely access the system based on user roles, register accounts, and manage children.
- [ ] Staff can check in and check out children.
- [ ] An admin can manage staff accounts.
- [ ] Parents receive notifications on check-in and check-out.
- [ ] Enrollment documents can be uploaded and retrieved digitally.
- [ ] All data is stored securely in the cloud.
- [ ] The system is deployed and running on AWS.
- [ ] All core endpoints are tested and documented.

## MVP Users

For the MVP, KidBase will be tested with one real daycare — the family daycare in Castro Valley — giving it an immediate real-world validation that no tutorial project can match.
