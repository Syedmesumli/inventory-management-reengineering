# Entity-Relationship Diagram (ER) - Snipe-IT Database Schema

## Core Database Tables

### Users Table
```
id (PK) | email | first_name | last_name | password | department_id (FK) | location_id (FK) | role
```

### Assets Table
```
id (PK) | name | asset_tag | serial_number | model_id (FK) | category_id (FK) | status | location_id (FK) | assigned_to (FK)
```

### Asset_Models Table
```
id (PK) | name | manufacturer | category_id (FK) | model_number | depreciation_months
```

### Categories Table
```
id (PK) | name | category_type | eol_date
```

### Checkouts Table
```
id (PK) | user_id (FK) | asset_id (FK) | license_id (FK) | from_date | to_date | note
```

### Licenses Table
```
id (PK) | product_id (FK) | seat_count | purchase_date | expiry_date | license_key | cost
```

### License_Products Table
```
id (PK) | name | manufacturer | category | version
```

### License_Seats Table
```
id (PK) | license_id (FK) | user_id (FK) | status | assigned_date
```

### Departments Table
```
id (PK) | name | manager_id (FK)
```

### Locations Table
```
id (PK) | name | address | city | state | country
```

### Audit_Logs Table
```
id (PK) | user_id (FK) | action | table_name | record_id | old_values (JSON) | new_values (JSON) | timestamp
```

## Key Relationships

1. **Users (1) --- (N) Checkouts**
   - One user can have multiple checkouts
   - Each checkout belongs to one user

2. **Assets (1) --- (N) Checkouts**
   - One asset can be checked out multiple times
   - Each checkout involves one asset

3. **Licenses (1) --- (N) License_Seats**
   - One license has multiple seats
   - Each seat belongs to one license

4. **Users (1) --- (N) License_Seats**
   - One user can be assigned multiple license seats
   - Each license seat belongs to one user

5. **Departments (1) --- (N) Users**
   - One department has many users
   - Each user belongs to one department

6. **Locations (1) --- (N) Users**
   - One location has many users
   - Each user works at one location

7. **Locations (1) --- (N) Assets**
   - One location has many assets
   - Each asset is stored at one location

8. **Asset_Models (1) --- (N) Assets**
   - One model has many asset instances
   - Each asset is of one model

9. **Categories (1) --- (N) Asset_Models**
   - One category has many models
   - Each model belongs to one category

10. **License_Products (1) --- (N) Licenses**
    - One product has many license purchases
    - Each license is for one product

## Database Design Patterns

### Normalization
- All tables follow 3NF (Third Normal Form)
- No redundant data across tables
- Foreign keys properly defined

### Indexing Strategy
- Primary keys indexed automatically
- Foreign keys indexed for JOIN performance
- Unique keys on email and asset_tag

### Audit Trail
- Audit_Logs table tracks all changes
- Stores old_values and new_values in JSON format
- Timestamp tracks when changes were made
- user_id tracks who made the change
