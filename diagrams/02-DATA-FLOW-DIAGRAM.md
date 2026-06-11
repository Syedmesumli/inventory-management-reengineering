# Data Flow Diagram (DFD) - Snipe-IT

## Level 0: Context Diagram

The Snipe-IT system interacts with three main actors:
1. Admin Users - Configuration and system management
2. Asset Users (Employees) - Checkout/Checkin, search, view assets
3. Reports & Analytics - Generate compliance and performance reports

## Level 1: Main Process Data Flow

### Web UI/API Request Processing
1. User Requests (Web UI, API, Dashboard)
2. Authentication & Validation
3. Route to Appropriate Controller
4. Controller Selection:
   - Asset Controller
   - License Controller
   - User Controller
5. Service Layer (Business Logic)
6. Eloquent ORM Query Building
7. Middleware (Logging, Caching)
8. MySQL Database Query Execution
9. Response Formatting (JSON/HTML/PDF/CSV)
10. User Display

## Asset Checkout Process

1. User Requests Asset Checkout
2. Load Asset List from Database
3. Validate User Permission
4. Select Asset
5. Create Checkout Record
6. Update Asset Status
7. Insert Audit Log
8. Send Notification Email
9. Return Success Response

## License Seat Allocation Process

1. Admin Purchases License
2. Create License Entry
3. Generate License Seat Records
4. Assign Seat to User
5. Notify User
6. License Available

## Reporting Flow

1. User Requests Report
2. Select Report Type & Filters
3. Validate Report Access
4. Query Database with Filters
5. Aggregate Data
6. Format Output (PDF, CSV, Excel)
7. Return to User
