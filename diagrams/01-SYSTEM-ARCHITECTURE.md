# System Architecture Diagram - Snipe-IT

## High-Level System Architecture

Snipe-IT uses a layered architecture pattern with the following components:

### User Layer
- Admin Users
- Asset Users (Employees)
- API Clients

### Presentation Layer
- Web UI Routes & Controllers
- Bootstrap 4 Frontend
- jQuery Interactions
- Blade Templates

### Application Layer
- API Controllers
- Web Controllers
- Service Layer (Business Logic)
- Middleware (Authentication, Logging)
- Laravel Framework

### Data Layer
- Eloquent ORM / Query Builder
- Model-Database Mapping

### Database Layer
- MySQL/MariaDB (Primary data store)
- Redis/Cache (Session management)
- File Storage System (Assets)

### External Systems
- Email Server
- LDAP/SSO (optional)
- File Backup Systems

## Data Flow Summary

1. User Request → Web/API endpoint
2. Middleware Processing → Authentication & validation
3. Controller Logic → Route to appropriate handler
4. Service/Business Logic → Process business rules
5. Database Query → Fetch/store data via ORM
6. Response Rendering → Return HTML/JSON
7. User Display → Browser/API client

## Core Components

### Frontend Components
- Dashboard
- Asset Management Interface
- License Management Interface
- User Profile Interface
- Reporting Interface
- Admin Panel

### Backend Components
- Authentication System
- Authorization System
- Asset Service
- License Service
- User Service
- Notification Service
- Report Generation Service
- Audit Logging Service

### Database Schema
- Users Table
- Assets Table
- Licenses Table
- Checkouts Table
- License Seats Table
- Categories Table
- Models Table
- Locations Table
- Departments Table
- Audit Logs Table
