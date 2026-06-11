# Data Flow Diagram (DFD) - Snipe-IT

## 📊 Level 0: Context Diagram

```
                    ╔═══════════════════════════════════════╗
                    ║   🏢 SNIPE-IT SYSTEM                 ║
                    ║  (Asset Management Platform)         ║
                    ╠═══════════════════════════════════════╣
                    ║                                       ║
                    ║  ✅ Asset Tracking                   ║
                    ║  ✅ License Management               ║
                    ║  ✅ User Management                  ║
                    ║  ✅ Reporting & Analytics            ║
                    ║  ✅ Audit Logging                    ║
                    ║                                       ║
                    ╚═════╤══════════════════════╤═════════╝
                          │                      │
                ┌─────────┤                      ├──────────────┐
                │         │                      │              │
                │         │                      │              │
        ┌───────▼────┐    │          ┌───────────▼────┐   ┌────▼─────┐
        │  👤 Admin   │    │          │ 👨‍💼 Asset Users│   │📊 Reports│
        │  Users      │    │          │ (Employees)    │   │& Analytics
        └─────────────┘    │          └────────────────┘   └──────────┘
              │             │                   │                │
        - Config System     │            - Checkout/Checkin     │
        - Manage Users      │            - View Assets          │
        - System Settings   │            - Search Licenses      │
                            │                                   │
                            ├─────────────────────────────────┤
                            │                                 │
                   System generates → Reports & Compliance Data
                                     Performance Metrics
                                     Audit Trails
```

---

## 📈 Level 1: Main Process Data Flow

```
╔════════════════════════════════════════════════════════════════════════════╗
║                    REQUEST PROCESSING FLOW                                ║
╚════════════════════════════════════════════════════════════════════════════╝

┌────────────┐
│   Client   │
│ Requests   │
└─────┬──────┘
      │
      ├─────────────────────────────────┐
      │                                 │
  ┌───▼────┐    ┌──────────┐    ┌─────▼────┐
  │ Web UI │    │   API    │    │Dashboard │
  └───┬────┘    └──────┬───┘    └─────┬────┘
      │                │              │
      └────────┬───────┴──────┬───────┘
               │              │
       ┌───────▼──────────────▼───────┐
       │ 🔐 Authentication Layer      │
       │ ├─ Session/Token Validation  │
       │ ├─ Permission Check          │
       │ └─ Rate Limiting             │
       └───────┬──────────────────────┘
               │
       ┌───────▼──────────────────────┐
       │ 📋 Input Validation          │
       │ ├─ Data Format Check         │
       │ ├─ Business Rules            │
       │ └─ Constraint Validation     │
       └───────┬──────────────────────┘
               │
       ┌───────▼──────────────────────────┐
       │ 🛣️ Route Dispatcher               │
       └───┬──────────┬──────────┬────────┘
           │          │          │
       ┌───▼───┐  ┌───▼───┐  ┌──▼────┐
       │Asset  │  │License│  │User   │
       │Ctrl   │  │Ctrl   │  │Ctrl   │
       └───┬───┘  └───┬───┘  └──┬───┘
           │          │         │
           └──────────┼─────────┘
                      │
       ┌──────────────▼────────────────┐
       │ ⚙️ Service Layer              │
       │ ├─ Business Logic             │
       │ ├─ Rule Enforcement           │
       │ ├─ Data Processing            │
       │ └─ Cross-service Coordination │
       └──────────────┬────────────────┘
                      │
       ┌──────────────▼────────────────┐
       │ 📦 Repository/Query Layer     │
       │ ├─ Eloquent ORM               │
       │ ├─ Query Building             │
       │ ├─ Relationship Loading       │
       │ └─ Cache Checking             │
       └──────────────┬────────────────┘
                      │
       ┌──────────────▼────────────────┐
       │ 🗄️ Database Query             │
       │ ├─ SELECT Queries             │
       │ ├─ INSERT/UPDATE/DELETE       │
       │ └─ Transactions               │
       └──────────────┬────────────────┘
                      │
       ┌──────────────▼────────────────┐
       │ 🔄 Middleware Processing      │
       │ ├─ Audit Logging              │
       │ ├─ Event Triggering           │
       │ ├─ Cache Invalidation         │
       │ └─ Notification Sending       │
       └──────────────┬────────────────┘
                      │
       ┌──────────────▼────────────────┐
       │ 📤 Response Formatting        │
       │ ├─ JSON Serialization         │
       │ ├─ HTML Rendering             │
       │ ├─ Error Handling             │
       │ └─ Status Codes               │
       └──────────────┬────────────────┘
                      │
                      ▼
                   User Display
```

---

## 🔄 Level 2: Asset Checkout Process

```
╔════════════════════════════════════════════════════════════════════════════╗
║                    ASSET CHECKOUT FLOW DIAGRAM                            ║
╚════════════════════════════════════════════════════════════════════════════╝

    USER INITIATES CHECKOUT
            │
            ▼
    ┌──────────────────────┐
    │ Load Available Assets│ ◄─── 📊 Query: Assets WHERE status='Available'
    └──────┬───────────────┘
           │
           ▼
    ┌────────────────────────┐
    │ Validate User          │ ◄─── 🔐 Check: User role & permissions
    │ Permission            │ ◄─── 🔐 Check: Department authorization
    └──────┬────────┬────────┘
           │        │
       YES │        │ NO
           │        └──────────────►[REJECT: Permission Denied]
           │
           ▼
    ┌────────────────────────┐
    │ Select Asset           │
    │ Confirm Checkout       │
    └──────┬─────────────────┘
           │
           ▼
    ┌────────────────────────────────┐
    │ Validate Asset Availability    │ ◄─── 📊 Check: status, location
    └──────┬─────────────┬───────────┘
           │             │
       YES │             │ NO/ERROR
           │             └──────────►[REJECT: Asset unavailable]
           │
           ▼
    ┌────────────────────────────────┐
    │ Create Checkout Record         │
    │ {user_id, asset_id,            │
    │  from_date, to_date}           │ ──► 💾 INSERT into checkouts table
    └──────┬────────────────────────┘
           │
           ▼
    ┌────────────────────────────────┐
    │ Update Asset Status            │
    │ Status: 'Checked Out'          │ ──► 💾 UPDATE assets table
    │ assigned_to: user_id           │
    └──────┬────────────────────────┘
           │
           ▼
    ┌────────────────────────────────┐
    │ Log Transaction                │
    │ Action: 'CHECKOUT'             │ ──► 💾 INSERT into audit_logs
    │ User: auth_user_id             │
    │ Changes: old & new values      │
    └──────┬────────────────────────┘
           │
           ▼
    ┌────────────────────────────────┐
    │ Trigger Events                 │
    │ - AssetCheckedOut Event        │ ──► Event Listeners
    │ - Send Notifications           │
    └──────┬────────────────────────┘
           │
           ▼
    ┌────────────────────────────────┐
    │ Send Email Notification        │ ──► 📧 SMTP Server
    │ - To: User email               │
    │ - Subject: Asset Checkout      │
    │ - Attachment: Asset Details    │
    └──────┬────────────────────────┘
           │
           ▼
    ┌────────────────────────────────┐
    │ Send Manager Notification      │ ──► 📧 Manager email
    │ (if required by policy)        │
    └──────┬────────────────────────┘
           │
           ▼
    ┌────────────────────────────────┐
    │ Return Success Response        │
    │ - HTTP 200 OK                  │
    │ - Checkout ID                  │
    │ - Confirmation Details         │
    └──────┬────────────────────────┘
           │
           ▼
        DISPLAY TO USER
```

---

## 📋 License Seat Allocation Flow

```
╔════════════════════════════════════════════════════════════════════════════╗
║                 LICENSE SEAT ALLOCATION FLOW                              ║
╚════════════════════════════════════════════════════════════════════════════╝

   ADMIN PURCHASES LICENSE
            │
            ▼
    ┌────────────────────────┐
    │ Create License Entry   │
    │ - Product name         │
    │ - Seat count: N        │ ──► 💾 INSERT into licenses
    │ - Expiry date          │     INSERT into license_products
    │ - License key          │
    └──────┬─────────────────┘
           │
           ▼
    ┌────────────────────────┐
    │ Generate License Seats │
    │ Create N seat records  │ ──► 💾 INSERT N rows into license_seats
    │ each with unique ID    │     status: 'Available'
    └──────┬─────────────────┘
           │
           ▼
    ┌────────────────────────┐
    │ Assign Seats to Users  │
    │ - Select users         │
    │ - Link seat to user    │ ──► 💾 UPDATE license_seats
    │ - Mark as Assigned     │     status: 'Assigned'
    │ - Set assigned_date    │     user_id: selected_user
    └──────┬─────────────────┘
           │
           ▼
    ┌────────────────────────┐
    │ Validate Allocation    │
    │ - Seat limit check     │
    │ - User limit check     │
    └──────┬─────────────────┘
           │
           ▼
    ┌────────────────────────┐
    │ Notify Users           │ ──► 📧 Email to each user
    │ - License available    │     - License details
    │ - License key          │     - Installation guide
    │ - Expiry date          │     - Support contact
    └──────┬─────────────────┘
           │
           ▼
    ┌────────────────────────┐
    │ Update Dashboard       │
    │ - Show available seats │ ──► 📊 Cache update
    │ - Show assignments     │
    └──────┬─────────────────┘
           │
           ▼
       COMPLETE
```

---

## 📊 Reporting & Analytics Flow

```
╔════════════════════════════════════════════════════════════════════════════╗
║                        REPORTING FLOW DIAGRAM                             ║
╚════════════════════════════════════════════════════════════════════════════╝

   USER REQUESTS REPORT
            │
            ▼
    ┌──────────────────────────┐
    │ Select Report Parameters │
    │ - Report type            │
    │ - Date range             │
    │ - Filters (category, etc)│
    └──────┬────────┬──────────┘
           │        │
       ┌───┘        └──────┐
       │                   │
   ┌───▼────────┐   ┌──────▼────────┐
   │Asset Report│   │License Report │
   └───┬────────┘   └──────┬────────┘
       │                   │
       └─────────┬─────────┘
                 │
       ┌─────────▼────────────────┐
       │ Validate Access Perms    │ ◄─── 🔐 Check: user role
       └─────────┬────────┬───────┘
                 │        │
             YES │        │ NO
                 │        └──────►[ACCESS DENIED]
                 │
       ┌─────────▼────────────────────┐
       │ Query Database               │
       │ ├─ SELECT * FROM assets      │
       │ ├─ JOIN asset_models ON ...  │
       │ ├─ WHERE date BETWEEN ... AND│
       │ └─ APPLY FILTERS             │
       └─────────┬────────────────────┘
                 │
       ┌─────────▼────────────────────┐
       │ Aggregate Data               │
       │ ├─ SUM quantities            │
       │ ├─ COUNT items              │
       │ ├─ CALCULATE totals         │
       │ └─ Group by category        │
       └─────────┬────────────────────┘
                 │
       ┌─────────▼────────────────────┐
       │ Apply Report Logic           │
       │ ├─ Calculate percentages     │
       │ ├─ Compute compliance        │
       │ ├─ Generate insights         │
       │ └─ Add visualizations        │
       └─────────┬────────────────────┘
                 │
       ┌─────────▼────────────────────┐
       │ Format Output                │
       └──┬──────────┬──────────┬─────┘
          │          │          │
       ┌──▼──┐  ┌───▼───┐  ┌──▼────┐
       │ PDF │  │  CSV  │  │ Excel │
       └──┬──┘  └───┬───┘  └──┬───┘
          │         │         │
          └────┬────┴────┬────┘
               │         │
       ┌───────▼─────────▼──────┐
       │ Cache Report           │
       │ (for 1 hour)           │
       └───────┬────────────────┘
               │
       ┌───────▼────────────────┐
       │ Download/View Report   │
       │ ├─ Browser display     │
       │ ├─ Email delivery      │
       │ └─ API endpoint        │
       └───────┬────────────────┘
               │
               ▼
          USER RECEIVES REPORT
```

---

## 🔐 Authentication & Authorization Flow

```
┌──────────────────────────────────────────────────────────┐
│       USER LOGIN & AUTHENTICATION PROCESS               │
└──────────────────────────────────────────────────────────┘

   USER ENTERS CREDENTIALS
            │
            ▼
    ┌──────────────────────┐
    │ Email & Password     │
    └──────┬───────────────┘
           │
           ▼
    ┌──────────────────────────┐
    │ Validate Input Format    │
    │ - Email format check     │
    │ - Password length check  │
    └──────┬───────┬───────────┘
           │       │
       VALID│       │INVALID
           │       └──────►[Return validation error]
           │
           ▼
    ┌──────────────────────────┐
    │ Query User Database      │ ◄─── 📊 SELECT FROM users WHERE email=?
    └──────┬───────┬───────────┘
           │       │
       FOUND│       │NOT FOUND
           │       └──────►[Invalid credentials]
           │
           ▼
    ┌──────────────────────────┐
    │ Verify Password Hash     │ ◄─── 🔐 bcrypt_verify()
    │ (bcrypt comparison)      │
    └──────┬───────┬───────────┘
           │       │
       VALID│       │INVALID
           │       └──────►[Invalid credentials]
           │
           ▼
    ┌──────────────────────────┐
    │ Check User Status        │ ◄─── 🔐 active=1 check
    │ (is active?)             │
    └──────┬───────┬───────────┘
           │       │
       ACTIVE│      │INACTIVE
           │       └──────►[Account disabled]
           │
           ▼
    ┌──────────────────────────┐
    │ Generate JWT Token       │
    │ or Session Cookie        │
    └──────┬───────────────────┘
           │
           ▼
    ┌──────────────────────────┐
    │ Log Login Event          │ ──► 💾 audit_logs entry
    │ - User ID                │
    │ - Timestamp              │
    │ - IP Address             │
    └──────┬───────────────────┘
           │
           ▼
    ┌──────────────────────────┐
    │ Return Token/Cookie      │
    │ Set user session         │
    └──────┬───────────────────┘
           │
           ▼
    ┌──────────────────────────┐
    │ Redirect to Dashboard    │
    │ User logged in!          │
    └──────────────────────────┘
```
