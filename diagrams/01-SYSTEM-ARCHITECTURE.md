# System Architecture Diagram - Snipe-IT

## 🏗️ Complete System Architecture

```
╔═══════════════════════════════════════════════════════════════════════════════╗
║                             SNIPE-IT SYSTEM ARCHITECTURE                      ║
╚═══════════════════════════════════════════════════════════════════════════════╝

┌─────────────────────────────────────────────────────────────────────────────┐
│                            👥 USER LAYER                                    │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│    ┌──────────────┐      ┌──────────────┐      ┌──────────────┐            │
│    │  🔐 Admin    │      │  👨‍💼 Manager  │      │  📱 API      │            │
│    │  Users       │      │  Users       │      │  Clients     │            │
│    └──────┬───────┘      └──────┬───────┘      └──────┬───────┘            │
│           │                     │                     │                    │
│           └─────────────────────┼─────────────────────┘                    │
│                                 │                                          │
└─────────────────────────────────┼──────────────────────────────────────────┘
                                  │ HTTP/REST Requests
                                  ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                    🎨 PRESENTATION LAYER                                    │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│    ┌─────────────────────────────────────────────────────────────────┐     │
│    │   🌐 Web UI Components                                         │     │
│    │   ├─ Dashboard Module                                         │     │
│    │   ├─ Asset Management UI                                      │     │
│    │   ├─ License Management UI                                    │     │
│    │   ├─ User Management Interface                                │     │
│    │   ├─ Admin Panel                                              │     │
│    │   └─ Reporting Interface                                      │     │
│    │                                                                │     │
│    │   🔗 API Gateway Component                                    │     │
│    │   ├─ REST API Endpoints                                       │     │
│    │   ├─ Request Router                                           │     │
│    │   ├─ Response Formatter                                       │     │
│    │   └─ API Documentation (Swagger)                              │     │
│    └──────────────────────────┬──────────────────────────────────┘     │
│                               │                                        │
└───────────────────────────────┼────────────────────────────────────────┘
                                │ Laravel Routes
                                ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                    ⚙️ APPLICATION LAYER                                     │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────┐  │
│  │ 🔐 Authentication & Authorization Service                          │  │
│  │ ├─ User Login & Password Management                                │  │
│  │ ├─ JWT/Session Token Management                                    │  │
│  │ ├─ Permission Verification                                         │  │
│  │ └─ Role-Based Access Control (RBAC)                                │  │
│  └──────────────────────────────────────────────────────────────────┬─┘  │
│                                                                     │     │
│  ┌─────────────────┬──────────────────┬─────────────────────────┐  │     │
│  │                 │                  │                         │  │     │
│  ▼                 ▼                  ▼                         ▼  │     │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  ┌────────────┐  │
│  │📦 Asset      │  │📜 License    │  │👥 User       │  │📊 Report   │  │
│  │Service       │  │Service       │  │Service       │  │Service     │  │
│  ├──────────────┤  ├──────────────┤  ├──────────────┤  ├────────────┤  │
│  │• CRUD Ops    │  │• Seat Mgmt   │  │• Profile Mgmt│  │• Generate  │  │
│  │• Search      │  │• Expiry      │  │• Department  │  │• Aggregate │  │
│  │• Assignment  │  │• Compliance  │  │• Location    │  │• Export    │  │
│  │• Status      │  │• Tracking    │  │• Role Mgmt   │  │• Analytics │  │
│  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘  └────┬───────┘  │
│         │                 │                 │               │         │
│         └─────────────────┼─────────────────┼───────────────┘         │
│                           │                 │                         │
│         ┌─────────────────┴─────────────────┘                         │
│         │                                                              │
│  ┌──────▼──────────────────────────────┐                              │
│  │🔔 Notification Service              │                              │
│  │├─ Email Notifications               │                              │
│  │├─ System Notifications              │                              │
│  │├─ Alert Management                  │                              │
│  │└─ Event Logging                     │                              │
│  └──────────────────────────────────┬─┘                               │
│                                     │                                │
└─────────────────────────────────────┼────────────────────────────────┘
                                      │
┌─────────────────────────────────────┼────────────────────────────────┐
│                    💾 DATA LAYER                                     │
├─────────────────────────────────────┼────────────────────────────────┤
│                                     │                               │
│  ┌──────────────────────────────────▼──────────────────────────┐   │
│  │  🗂️ ORM Layer (Eloquent)                                  │   │
│  │  ├─ Model Definitions                                      │   │
│  │  ├─ Query Builder                                          │   │
│  │  ├─ Relationship Management                                │   │
│  │  └─ Migration Runner                                       │   │
│  └──────────────────────────┬───────────────────────────────┘   │
│                             │                                   │
│                    ┌────────▼────────┐                          │
│                    │  Cache Layer    │                          │
│                    │  • Session Mgmt │                          │
│                    │  • Data Cache   │                          │
│                    │  • Performance  │                          │
│                    └────────┬────────┘                          │
│                             │                                   │
└─────────────────────────────┼───────────────────────────────────┘
                              │
┌─────────────────────────────┼───────────────────────────────────┐
│                 🗄️ DATABASE LAYER                              │
├─────────────────────────────┼───────────────────────────────────┤
│                             │                                   │
│   ┌─────────────────────────▼─────────────────────────────┐    │
│   │ 📊 MySQL/MariaDB Database                            │    │
│   │ ├─ Users Table          ├─ Assets Table              │    │
│   │ ├─ Licenses Table       ├─ Categories Table          │    │
│   │ ├─ Checkouts Table      ├─ Locations Table           │    │
│   │ ├─ License_Seats Table  ├─ Audit_Logs Table          │    │
│   │ └─ Departments Table    └─ Asset_Models Table        │    │
│   └────────────┬────────────────────────────────────────┘    │
│                │                                             │
│   ┌────────────▼────────────────────────┐                   │
│   │ 💾 File Storage System              │                   │
│   │ ├─ Asset Documents                  │                   │
│   │ ├─ Asset Images                     │                   │
│   │ └─ Backup Files                     │                   │
│   └─────────────────────────────────────┘                   │
│                                                              │
└──────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│            🔗 EXTERNAL INTEGRATIONS                         │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌──────────────┐  ┌──────────────┐  ┌────────────────┐  │
│  │📧 Email      │  │🔑LDAP/SSO    │  │☁️ File Backup  │  │
│  │Service       │  │Integration   │  │Systems         │  │
│  └──────────────┘  └──────────────┘  └────────────────┘  │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

## 📋 Data Flow Path

```
User Request 
     │
     ▼
┌─────────────────────────────────────┐
│ Middleware (Auth, Logging, CORS)    │
└────────────┬────────────────────────┘
             │
    ┌────────▼────────┐
    │ Validation      │
    └────────┬────────┘
             │
    ┌────────▼────────┐
    │ Route Dispatch  │
    └────────┬────────┘
             │
    ┌────────▼────────────────────┐
    │ Controller Action            │
    │ (Request Handling)           │
    └────────┬────────────────────┘
             │
    ┌────────▼────────────────────┐
    │ Service Layer                │
    │ (Business Logic)             │
    └────────┬────────────────────┘
             │
    ┌────────▼────────────────────┐
    │ Repository/Query Builder     │
    │ (Data Access)                │
    └────────┬────────────────────┘
             │
    ┌────────▼────────────────────┐
    │ Database Query               │
    └────────┬────────────────────┘
             │
    ┌────────▼────────────────────┐
    │ Format Response              │
    │ (JSON/HTML)                  │
    └────────┬────────────────────┘
             │
             ▼
        User Display
```

## 🎯 Key Features Overview

| Component | Purpose | Status |
|-----------|---------|--------|
| **Asset Service** | Track and manage IT assets | ✅ Active |
| **License Service** | Manage software licenses and seats | ✅ Active |
| **Checkout Service** | Handle asset allocation | ✅ Active |
| **User Service** | Manage employees and permissions | ✅ Active |
| **Reporting Engine** | Generate compliance reports | ✅ Active |
| **Notification System** | Send alerts and updates | ✅ Active |
| **Audit System** | Track all changes | ✅ Active |
| **API Layer** | RESTful API access | ✅ Active |
