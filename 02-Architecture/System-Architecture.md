# System Architecture

## Purpose

This document describes the overall technical architecture of the HRMS platform.

It explains how different components interact, how data flows through the system, and how the application is organized.

This document serves as the primary architectural reference for developers before implementation begins.

---

## Status

# 1. System Overview

The HRMS platform is an internal web-based application designed to streamline Human Resource operations for the organization.

The system centralizes employee information, attendance, leave management, recruitment, interview management, offer management, reporting, and administrative functions into a single platform.

The application follows a modular architecture where each business capability is implemented as an independent module while sharing a common authentication, authorization, and data management layer.

The HRMS integrates with external systems where required, such as payroll, email services, and other enterprise platforms.

The architecture is designed to support future expansion while maintaining simplicity, maintainability, and security.

# 2. Architecture Principles

The HRMS platform is designed using the following architectural principles.

## 2.1 Modular Design

Each business module (Employee, Attendance, Leave, Recruitment, etc.) must be developed independently with clear boundaries and responsibilities.

---

## 2.2 Separation of Concerns

The Presentation Layer, Business Logic Layer, and Data Access Layer must remain separate to improve maintainability and testing.

---

## 2.3 API-First Development

All communication between the frontend and backend will occur through well-defined REST APIs.

---

## 2.4 Security by Design

Security requirements such as authentication, authorization, audit logging, and data protection must be considered from the beginning of development.

---

## 2.5 Reusability

Common services, utilities, and components should be shared across modules to reduce duplication and improve consistency.

---

## 2.6 Scalability

The architecture should support future business growth, additional modules, and increased user volume without requiring significant redesign.

---

## 2.7 Maintainability

The system should be organized in a way that makes future enhancements, bug fixes, and module additions straightforward.

---

## 2.8 Documentation First

Technical documentation must be updated before introducing significant architectural or functional changes.


# 3. High-Level Architecture

The HRMS platform follows a modern three-tier architecture.

```

```
                    Users
                      │
                      ▼
              Web Application
             (Next.js + React)
                      │
              HTTPS / REST API
                      │
                      ▼
          Backend Application
      (NestJS + TypeScript)
                      │
      ┌───────────────┼────────────────┐
      │               │                │
      ▼               ▼                ▼
 PostgreSQL         Redis          File Storage
 (Primary DB)      (Cache)        (MinIO / AWS S3)

                      │
                      ▼
          External Integrations
        • Payroll System
        • Email Service
        • Future Enterprise Systems
```

```markdown

---

## Component Responsibilities

### Frontend

Provides the user interface for employees, managers, HR administrators, and system administrators.

Responsibilities include:

- User interactions
- Forms
- Dashboards
- Reports
- API communication

---

### Backend

Contains the complete business logic of the HRMS.

Responsibilities include:

- Authentication
- Authorization
- Business rules
- Workflow execution
- Validation
- API endpoints

---

### Database

Stores all business data including employees, attendance, leave, recruitment, interviews, offers, audit logs, and system configuration.

---

### Cache

Redis is used to improve performance by storing temporary data, sessions, and frequently accessed information.

---

### File Storage

Stores uploaded documents such as resumes, employee documents, offer letters, and attachments.

---

### External Integrations

Supports integration with:

- Third-party Payroll System
- Email Services
- Additional enterprise applications in future

# 4. Application Layers

The HRMS application is organized into distinct layers, each with a specific responsibility.

```
┌───────────────────────────────────────┐
│ Presentation Layer                    │
│ (Next.js + React UI)                  │
└───────────────────────────────────────┘
                  │
                  ▼
┌───────────────────────────────────────┐
│ API Layer                             │
│ (REST Controllers - NestJS)           │
└───────────────────────────────────────┘
                  │
                  ▼
┌───────────────────────────────────────┐
│ Business Layer                        │
│ (Services & Business Logic)           │
└───────────────────────────────────────┘
                  │
                  ▼
┌───────────────────────────────────────┐
│ Data Access Layer                     │
│ (Prisma ORM)                          │
└───────────────────────────────────────┘
                  │
                  ▼
┌───────────────────────────────────────┐
│ PostgreSQL Database                   │
└───────────────────────────────────────┘
```

## Layer Responsibilities

### Presentation Layer
- Displays user interfaces
- Collects user input
- Calls backend APIs
- Displays responses

### API Layer
- Exposes REST endpoints
- Validates requests
- Authenticates users
- Routes requests to business services

### Business Layer
- Implements HRMS business rules
- Processes workflows
- Coordinates module interactions
- Handles approvals and validations

### Data Access Layer
- Reads and writes data using Prisma ORM
- Maps business objects to database tables
- Ensures data consistency

### Database Layer
- Stores persistent application data
- Maintains relationships and constraints
- Supports reporting and auditing

# 5. Business Modules

The HRMS is organized into independent business modules. Each module has a clearly defined responsibility and interacts with other modules through well-defined service interfaces.

| Module | Responsibility |
|---------|----------------|
| Authentication & Authorization | User login, RBAC, session management |
| Employee Management | Employee profiles, organization structure, employee lifecycle |
| Attendance Management | Attendance tracking, shifts, regularization |
| Leave Management | Leave requests, approvals, leave balances |
| Recruitment (ATS) | Job openings, candidates, hiring pipeline |
| Interview Management | Interview scheduling, feedback, evaluation |
| Offer Management | Offer generation, approval, candidate acceptance |
| Reports & Dashboards | Operational and management reports |
| Notifications | Email and in-app notifications |
| Administration | System configuration, master data, user management |
| Integrations | Payroll integration, email services, future enterprise integrations |

## Module Interaction

Modules communicate through the application's service layer rather than directly accessing each other's data.

This approach maintains loose coupling, improves maintainability, and allows future enhancements without affecting unrelated modules.

# 6. External Integrations

The HRMS platform is designed to integrate with external enterprise systems where required. Integrations are isolated through dedicated services to reduce coupling and simplify future enhancements.

| Integration | Purpose |
|-------------|---------|
| Payroll System | Export employee, attendance, and leave data to the third-party payroll application. |
| Email Service | Send notifications such as interview invitations, leave approvals, offer letters, and password reset emails. |
| File Storage | Store employee documents, resumes, offer letters, and other uploaded files. |
| Future Enterprise Systems | Support future integrations with ERP, Identity Providers, or other business applications without major architectural changes. |

## Integration Principles

- All integrations should be implemented through dedicated service classes.
- Business modules must not communicate directly with external systems.
- Integration failures should be logged and handled gracefully.
- External APIs should be configurable and not hardcoded.

# 7. Security Architecture

Security is a foundational aspect of the HRMS platform and is incorporated into every layer of the application.

## Authentication

- JWT-based authentication
- Refresh Token mechanism for session renewal
- Secure password storage using industry-standard hashing algorithms
- Support for Single Sign-On (SSO) in future releases

---

## Authorization

Access to system resources is controlled using Role-Based Access Control (RBAC).

The platform supports roles such as:

- Super Administrator
- HR Administrator
- Manager
- Employee
- Recruiter
- Custom Roles

Permissions are assigned to roles and enforced at the API level.

---

## Data Protection

The application protects sensitive employee information through:

- HTTPS for all communication
- Input validation
- Output sanitization
- Secure file handling
- Audit logging for critical operations

---

## Security Principles

The platform follows these principles:

- Least Privilege
- Defense in Depth
- Secure by Default
- Auditability
- Confidentiality
- Integrity

# 8. Deployment Overview

The HRMS platform will be deployed as a web-based application using a standard three-tier deployment model.

## Deployment Components

| Component | Description |
|----------|-------------|
| Frontend | Next.js application served to users through a web server. |
| Backend | NestJS REST API handling business logic and integrations. |
| Database | PostgreSQL for persistent data storage. |
| Cache | Redis for caching and background job processing. |
| File Storage | MinIO (Development) and Amazon S3 (Production). |

---

## Deployment Principles

- The frontend and backend should be independently deployable.
- Configuration should be managed using environment variables.
- Secrets must never be stored in source code.
- HTTPS should be enabled in production.
- Database backups should be scheduled regularly.
- Application logs should be retained for monitoring and troubleshooting.
- The architecture should support future cloud deployment without major redesign.

---

## Future Scalability

The architecture is designed to support future enhancements, including:

- Mobile applications
- Additional HR modules
- Enterprise system integrations
- High-availability deployments
- Horizontal scaling of application services
---
