# UML Class Diagram - Snipe-IT Core Entities

## 📦 Complete Class & Entity Relationships

```
╔════════════════════════════════════════════════════════════════════════════╗
║                    SNIPE-IT DATABASE ENTITIES                             ║
║                         & RELATIONSHIPS                                    ║
╚════════════════════════════════════════════════════════════════════════════╝

┌──────────────────────────────────────────────────────────────┐
│                    👤 USER ENTITY                            │
├──────────────────────────────────────────────────────────────┤
│ Attributes:                                                  │
│  • id: int (Primary Key)                                     │
│  • email: string (UNIQUE)                                    │
│  • first_name: string                                        │
│  • last_name: string                                         │
│  • password: string (hashed)                                 │
│  • phone: string                                             │
│  • active: boolean                                           │
│  • department_id: int (Foreign Key)                          │
│  • location_id: int (Foreign Key)                            │
│  • role: enum(admin, manager, user)                          │
│                                                              │
│ Methods:                                                     │
│  + getFullName(): string                                     │
│  + hasPermission(action): boolean                            │
│  + getCheckOuts(): Collection[Checkout]                      │
│  + getLicenseSeats(): Collection[LicenseSeat]                │
│  + getDepartment(): Department                               │
└────────────┬─────────────────────────────────────────────────┘
             │
             │ has many (1:N)                  belongs to (N:1)
             │                                      │
             ├────────────────────────────┬─────────┤
             │                            │         │
        ┌────▼──────────┐      ┌──────────▼──┐  ┌───▼────────────┐
        │ 📤 CHECKOUT   │      │ 📁 DEPT     │  │ 📍 LOCATION    │
        ├───────────────┤      ├─────────────┤  ├────────────────┤
        │ • id (PK)     │      │ • id (PK)   │  │ • id (PK)      │
        │ • user_id (FK)│      │ • name      │  │ • name         │
        │ • asset_id(FK)│      │ • manager_id│  │ • address      │
        │ • license_id  │      │ • created_at│  │ • city         │
        │ • from_date   │      └─────────────┘  │ • country      │
        │ • to_date     │                       └────────────────┘
        │ • note        │
        └────┬──────────┘
             │ involves (N:1)
             │
    ┌────────▼──────────────────┐
    │    📦 ASSET ENTITY        │
    ├───────────────────────────┤
    │ Attributes:               │
    │  • id: int (PK)           │
    │  • name: string           │
    │  • asset_tag: string      │
    │  • serial_number: string  │
    │  • model_id: int (FK)     │
    │  • category_id: int (FK)  │
    │  • status: enum           │
    │    - Available            │
    │    - Checked Out          │
    │    - Archived             │
    │    - Repair               │
    │  • location_id: int (FK)  │
    │  • assigned_to: int (FK)  │
    │  • purchase_date: date    │
    │  • warranty_months: int   │
    │                           │
    │ Methods:                  │
    │  + markAsCheckedOut()     │
    │  + markAsAvailable()      │
    │  + getHistory()           │
    │  + calculateDepreciation()│
    └────────┬──────────────────┘
             │ is of type (N:1)
             │
    ┌────────▼──────────────────┐
    │  🏭 ASSET_MODEL ENTITY    │
    ├───────────────────────────┤
    │ Attributes:               │
    │  • id: int (PK)           │
    │  • name: string           │
    │  • manufacturer: string   │
    │  • category_id: int (FK)  │
    │  • model_number: string   │
    │  • depreciation_months    │
    └────────┬──────────────────┘
             │ belongs to (N:1)
             │
    ┌────────▼──────────────────┐
    │  🏷️ CATEGORY ENTITY       │
    ├───────────────────────────┤
    │ Attributes:               │
    │  • id: int (PK)           │
    │  • name: string           │
    │  • category_type: enum    │
    │    - Hardware             │
    │    - Software             │
    │    - License              │
    │  • eol_date: date         │
    └───────────────────────────┘
```

---

## 📜 License Management Entities

```
┌─────────────────────────────────────────────────────────────┐
│              📜 LICENSE ENTITY                              │
├─────────────────────────────────────────────────────────────┤
│ Attributes:                                                 │
│  • id: int (Primary Key)                                    │
│  • product_id: int (Foreign Key)                            │
│  • seat_count: int (Total seats available)                  │
│  • purchase_date: date                                      │
│  • expiry_date: date                                        │
│  • license_key: string (UNIQUE)                             │
│  • cost: decimal                                            │
│  • created_at: timestamp                                    │
│  • updated_at: timestamp                                    │
│                                                             │
│ Methods:                                                    │
│  + getAvailableSeats(): int                                 │
│  + isExpired(): boolean                                     │
│  + getAssignedUsers(): Collection[User]                     │
│  + allocateSeat(user_id): LicenseSeat                       │
└──────────────────┬──────────────────────────────────────────┘
                   │ has many (1:N)
                   │
          ┌────────▼──────────────────────┐
          │   🔑 LICENSE_SEAT ENTITY      │
          ├───────────────────────────────┤
          │ Attributes:                   │
          │  • id: int (PK)               │
          │  • license_id: int (FK)       │
          │  • user_id: int (FK - null)   │
          │  • status: enum               │
          │    - Available (unassigned)   │
          │    - Assigned                 │
          │    - Expired                  │
          │  • assigned_date: date        │
          │  • created_at: timestamp      │
          │                               │
          │ Methods:                      │
          │  + assignTo(user_id)          │
          │  + release()                  │
          │  + isAvailable(): boolean     │
          └────────┬──────────────────────┘
                   │ assigned to (N:1)
                   │
          (points to USER entity shown above)

    belongs to (N:1)
           │
    ┌──────▼──────────────────────┐
    │  🏢 LICENSE_PRODUCT ENTITY  │
    ├─────────────────────────────┤
    │ Attributes:                 │
    │  • id: int (PK)             │
    │  • name: string             │
    │  • manufacturer: string     │
    │  • category: enum           │
    │    - Application            │
    │    - OS                     │
    │    - Utility                │
    │  • version: string          │
    │  • created_at: timestamp    │
    └─────────────────────────────┘
```

---

## 🔄 Complete Relationship Diagram

```
╔════════════════════════════════════════════════════════════════════════════╗
║                    ENTITY RELATIONSHIP MAP                                ║
╚════════════════════════════════════════════════════════════════════════════╝


                      ┌─────────────────┐
                      │  👤 USERS (1)   │
                      └────────┬────────┘
                               │
                ┌──────────────┼──────────────┐
                │              │              │
         (1:N) │         (1:N) │         (1:N)│
                │              │              │
        ┌───────▼──────┐ ┌────▼────┐ ┌───────▼──────┐
        │ 📤 CHECKOUTS │ │LICENSE  │ │ 📁 DEPTS    │
        │   (N) ────── │ │_SEATS   │ │   (1)       │
        └───────┬──────┘ └────┬────┘ └─────────────┘
                │             │
         (N:1) │        (N:1) │
                │             │
        ┌───────▼─────────────▼─────────┐
        │     📦 ASSETS (1)              │
        │     📜 LICENSES (1)            │
        └───────┬──────────────┬─────────┘
                │              │
         (1:N) │         (1:N) │
                │              │
        ┌───────▼──────┐  ┌────▼────────┐
        │🏭 MODELS(1)  │  │🏢 PRODUCTS  │
        │              │  │   (1)       │
        └───────┬──────┘  └─────────────┘
                │
         (1:N) │
                │
        ┌───────▼─────────┐
        │🏷️ CATEGORIES(1) │
        └──────────────────┘


RELATIONSHIP SUMMARY:
═══════════════════════════════════════════════════════════════

1. USER (1) ────────────── (N) CHECKOUT
   └─ One user can checkout multiple assets over time
   └─ Each checkout belongs to exactly one user

2. ASSET (1) ────────────── (N) CHECKOUT
   └─ One asset can be checked out multiple times
   └─ Each checkout involves exactly one asset

3. LICENSE (1) ────────────── (N) LICENSE_SEAT
   └─ One license has multiple seats
   └─ Each seat belongs to one license

4. USER (1) ────────────── (N) LICENSE_SEAT
   └─ One user can own multiple license seats
   └─ Each seat is assigned to one user (or null if unassigned)

5. DEPARTMENT (1) ────────────── (N) USER
   └─ One department has many users
   └─ Each user belongs to one department

6. LOCATION (1) ────────────── (N) USER
   └─ One location has many users
   └─ Each user works at one location

7. LOCATION (1) ────────────── (N) ASSET
   └─ One location stores many assets
   └─ Each asset is stored at one location

8. ASSET_MODEL (1) ────────────── (N) ASSET
   └─ One model has many asset instances
   └─ Each asset is of one model

9. CATEGORY (1) ────────────── (N) ASSET_MODEL
   └─ One category has many models
   └─ Each model belongs to one category

10. LICENSE_PRODUCT (1) ────────────── (N) LICENSE
    └─ One product has many licenses purchased
    └─ Each license is for one product
```

---

## 🗂️ Database Schema Overview

```
┌──────────────────────────────────────────────────────────────┐
│                    TABLE STRUCTURE                           │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  USERS TABLE                DEPARTMENTS TABLE               │
│  ├─ id (PK)                 ├─ id (PK)                      │
│  ├─ email (UNIQUE)          ├─ name                         │
│  ├─ first_name              ├─ manager_id (FK→users)        │
│  ├─ last_name               └─ created_at                   │
│  ├─ department_id (FK)                                      │
│  └─ location_id (FK)        LOCATIONS TABLE                 │
│                             ├─ id (PK)                      │
│  ASSETS TABLE               ├─ name                         │
│  ├─ id (PK)                 ├─ address                      │
│  ├─ name                    ├─ city                         │
│  ├─ asset_tag (UNIQUE)      └─ country                      │
│  ├─ model_id (FK)                                           │
│  ├─ category_id (FK)        CATEGORIES TABLE                │
│  ├─ status                  ├─ id (PK)                      │
│  ├─ location_id (FK)        ├─ name                         │
│  └─ assigned_to (FK)        └─ category_type                │
│                                                              │
│  LICENSES TABLE             ASSET_MODELS TABLE              │
│  ├─ id (PK)                 ├─ id (PK)                      │
│  ├─ product_id (FK)         ├─ name                         │
│  ├─ seat_count              ├─ manufacturer                 │
│  ├─ expiry_date             ├─ category_id (FK)             │
│  └─ license_key (UNIQUE)    └─ depreciation_months          │
│                                                              │
│  CHECKOUTS TABLE            LICENSE_SEATS TABLE             │
│  ├─ id (PK)                 ├─ id (PK)                      │
│  ├─ user_id (FK)            ├─ license_id (FK)              │
│  ├─ asset_id (FK)           ├─ user_id (FK)                 │
│  ├─ from_date               ├─ status                       │
│  └─ to_date                 └─ assigned_date                │
│                                                              │
│  AUDIT_LOGS TABLE                                           │
│  ├─ id (PK)                                                 │
│  ├─ user_id (FK)                                            │
│  ├─ action (Create/Update/Delete/CheckOut)                  │
│  ├─ table_name                                              │
│  ├─ record_id                                               │
│  ├─ old_values (JSON)                                       │
│  ├─ new_values (JSON)                                       │
│  └─ timestamp                                               │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## 🔍 Key Design Patterns Used

```
✅ ENTITY-RELATIONSHIP MODEL (ER Model)
   └─ Properly normalized (3NF)
   └─ Minimal data redundancy
   └─ Referential integrity maintained

✅ FOREIGN KEY CONSTRAINTS
   └─ Prevents orphaned records
   └─ Ensures data consistency
   └─ Cascading updates/deletes (where appropriate)

✅ AUDIT TRAIL PATTERN
   └─ Tracks all changes
   └─ Stores old and new values
   └─ Records user and timestamp
   └─ Enables compliance auditing

✅ SOFT DELETE (if implemented)
   └─ Records marked as deleted
   └─ Not permanently removed
   └─ Maintains referential integrity

✅ TIMESTAMP COLUMNS
   └─ created_at: When record was created
   └─ updated_at: When record was last modified
   └─ Enables audit trail and sorting
```
