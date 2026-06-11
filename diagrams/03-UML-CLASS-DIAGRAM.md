# UML Class Diagram - Snipe-IT Core Entities

## Core Entity Relationships

### USER Entity
Properties:
- id: int (Primary Key)
- email: string
- first_name: string
- last_name: string
- password: string
- department_id: int (Foreign Key)
- location_id: int (Foreign Key)
- role: enum(admin, manager, user)

Methods:
- getFullName()
- hasPermission(action)
- getCheckOuts()
- getLicenses()

### ASSET Entity
Properties:
- id: int (Primary Key)
- name: string
- asset_tag: string
- model_id: int (Foreign Key)
- category_id: int (Foreign Key)
- status: enum(Available, Checked Out, Archived)
- serial_number: string
- purchase_date: date
- location_id: int (Foreign Key)
- assigned_to: int (Foreign Key to User)

### CHECKOUT Entity
Properties:
- id: int (Primary Key)
- user_id: int (Foreign Key)
- asset_id: int (Foreign Key)
- license_id: int (Foreign Key - optional)
- from_date: date
- to_date: date
- note: text

### LICENSE Entity
Properties:
- id: int (Primary Key)
- product_id: int (Foreign Key)
- seat_count: int
- expiry_date: date
- license_key: string

### LICENSE_SEAT Entity
Properties:
- id: int (Primary Key)
- license_id: int (Foreign Key)
- user_id: int (Foreign Key - nullable)
- status: enum(Available, Assigned, Expired)

### ASSET_MODEL Entity
Properties:
- id: int (Primary Key)
- name: string
- manufacturer: string
- category_id: int (Foreign Key)
- model_number: string
- depreciation: int

### CATEGORY Entity
Properties:
- id: int (Primary Key)
- name: string
- category_type: enum(Hardware, Software, License)
- eol_date: date

### DEPARTMENT Entity
Properties:
- id: int (Primary Key)
- name: string
- manager_id: int (Foreign Key to User)

### LOCATION Entity
Properties:
- id: int (Primary Key)
- name: string
- address: text
- city: string
- country: string

## Entity Relationships

1. USER (1) ---- (N) CHECKOUT
2. ASSET (1) ---- (N) CHECKOUT
3. LICENSE (1) ---- (N) LICENSE_SEAT
4. USER (1) ---- (N) LICENSE_SEAT
5. DEPARTMENT (1) ---- (N) USER
6. LOCATION (1) ---- (N) ASSET
7. ASSET_MODEL (1) ---- (N) ASSET
8. CATEGORY (1) ---- (N) ASSET_MODEL

## Key Relationships

- A user can have multiple checkouts
- Each checkout belongs to one user
- An asset can have multiple checkouts over time
- One license has multiple seats
- Each license seat belongs to one user
- Department has many users
- Location contains many assets
- Model has many asset instances
- Category has many models
