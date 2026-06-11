# Use Case Diagram - Snipe-IT System

## Actors (Users)

1. **Admin User**
   - System administrator
   - Full permissions
   - Manages configuration and users

2. **Manager User**
   - Department manager
   - Can manage team assets
   - Can approve checkouts

3. **Regular User/Employee**
   - Can checkout assets
   - Can search and view licenses
   - Can view own profile
   - Can see own checkout history

4. **System**
   - Automated tasks
   - Notifications
   - Reporting

## Use Cases

### Admin User Use Cases

#### User Management
- UC1: Create New User
- UC2: Update User Information
- UC3: Delete User
- UC4: Assign User to Department
- UC5: Reset User Password
- UC6: Change User Role

#### Asset Management
- UC7: Add New Asset
- UC8: Update Asset Information
- UC9: Delete Asset
- UC10: View Asset List
- UC11: Search Assets
- UC12: Assign Asset to Location
- UC13: Mark Asset as Archived
- UC14: Import Assets (Bulk)

#### License Management
- UC15: Add New License
- UC16: Update License Information
- UC17: Delete License
- UC18: View License List
- UC19: Allocate License Seat
- UC20: Track License Expiry

#### System Configuration
- UC21: Configure System Settings
- UC22: Manage Categories
- UC23: Manage Asset Models
- UC24: Manage Departments
- UC25: Manage Locations
- UC26: Set Up Email Notifications
- UC27: Configure API Keys

#### Reporting
- UC28: Generate Asset Report
- UC29: Generate License Report
- UC30: Generate Audit Trail
- UC31: Export Report to Multiple Formats

### Manager User Use Cases

- UC32: View Team Assets
- UC33: Approve Asset Checkout
- UC34: View Team Checkouts
- UC35: Generate Department Report
- UC36: View Underutilized Assets

### Regular User Use Cases

- UC37: Search Assets
- UC38: View Available Assets
- UC39: Request Asset Checkout
- UC40: View My Checkouts
- UC41: Request Asset Return
- UC42: View License Information
- UC43: Update My Profile
- UC44: View My Department Info

### System Use Cases

- UC45: Send Checkout Reminder
- UC46: Send Expiry Notification
- UC47: Generate Compliance Report
- UC48: Archive Old Checkouts
- UC49: Update Asset Status
- UC50: Audit Log Creation

## Use Case Details

### UC37: Search Assets

**Actor:** Regular User

**Preconditions:**
- User is logged in
- System contains assets

**Flow:**
1. User navigates to Asset Search page
2. User enters search criteria (name, tag, model, category, location)
3. System validates input
4. System queries database for matching assets
5. System displays results with availability status
6. User selects an asset to view details

**Postconditions:**
- Search results displayed
- User can view asset details

### UC39: Request Asset Checkout

**Actor:** Regular User

**Preconditions:**
- User is logged in
- User has permission to checkout
- Asset is available

**Flow:**
1. User selects asset to checkout
2. User specifies checkout date and expected return date
3. User enters checkout reason (optional)
4. System validates checkout request
5. System checks asset availability
6. System creates checkout record
7. System updates asset status to "Checked Out"
8. System sends confirmation email to user
9. System sends notification to asset manager

**Postconditions:**
- Checkout record created
- Asset status updated
- Notifications sent
- Asset assigned to user

### UC20: Track License Expiry

**Actor:** Admin/System

**Preconditions:**
- System contains licenses with expiry dates

**Flow:**
1. System checks current date against license expiry dates
2. System identifies licenses expiring within 30 days
3. System identifies expired licenses
4. System sends warning email to admin (30 days before expiry)
5. System sends critical alert (7 days before expiry)
6. System marks licenses as expired when expiry date passes
7. System updates license seat status to "Expired"

**Postconditions:**
- License status updated
- Notifications sent
- Admin alerted for renewal

## Use Case Hierarchy

```
┌─────────────────────────────────────┐
│      SNIPE-IT SYSTEM                │
│   (Inventory Management System)      │
└──────┬─────────────────┬────────────┘
       │                 │
   ┌───▼───┐        ┌────▼─────┐
   │ Admin │        │ Manager  │
   └───┬───┘        └────┬─────┘
       │                 │
   ┌───┴─────────┬───────┴────┐
   │             │            │
┌──▼──┐   ┌─────▼──┐   ┌──────▼──┐
│User │   │System  │   │Regular  │
│Mgmt │   │Config  │   │User     │
└─────┘   └────────┘   └─────────┘
   │            │            │
┌──▼──┐   ┌─────▼──┐   ┌──────▼──┐
│Asset│   │Report  │   │Checkout │
│Mgmt │   │Engine  │   │Requests │
└─────┘   └────────┘   └─────────┘
```
