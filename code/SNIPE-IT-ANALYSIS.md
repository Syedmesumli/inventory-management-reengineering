# Snipe-IT: Legacy Inventory Management System Analysis

## Project Overview

**Repository:** https://github.com/grokability/snipe-it  
**Language:** PHP (Laravel Framework)  
**License:** GNU Affero General Public License v3.0  
**Created:** November 12, 2013  
**Status:** Active (12+ years old legacy system)  
**Stars:** 13,918 | **Forks:** 3,862  
**Size:** ~292MB  

---

## What is Snipe-IT?

Snipe-IT is a **free, open-source IT asset and license management system** used by organizations worldwide to:
- Track IT assets (computers, printers, monitors, etc.)
- Manage software licenses
- Monitor equipment checkout/check-in
- Maintain inventory records
- Generate reports

---

## Current Architecture Overview

### Technology Stack:
- **Backend:** PHP 7.x/8.x with Laravel Framework
- **Database:** MySQL/MariaDB
- **Frontend:** Bootstrap 4, jQuery, Blade Templates
- **API:** RESTful API with Laravel routes

### Core Modules:
1. **Asset Management** - Track hardware assets
2. **License Management** - Software license tracking
3. **User Management** - Employee and user profiles
4. **Checkout/Checkin** - Asset allocation system
5. **Reporting** - Analytics and reports
6. **Admin Dashboard** - System administration
7. **API Layer** - Integration endpoints

---

## Identified Legacy Issues

### Code Quality Issues:
- Mixed monolithic and modular architecture
- Heavy coupling between components
- Legacy PHP practices (not fully modern Laravel)
- Large controller classes with multiple responsibilities
- Limited separation of concerns

### Architectural Issues:
- Tight database coupling
- Frontend and backend not fully decoupled
- Limited API abstraction
- Blade templates mixed with business logic

### Maintenance Challenges:
- 12+ years of code accumulation
- Multiple layers of refactoring attempts
- Complex dependency management
- Limited test coverage in legacy code

---

## Key Database Entities

```
Users
├── has many Assets
├── has many Licenses
├── has many CheckOuts
└── belongs to Departments

Assets
├── belongs to AssetModel
├── belongs to Category
├── belongs to Location
├── has many CheckOuts
└── has many MaintenanceRecords

Licenses
├── belongs to LicenseProduct
├── has many LicenseSeats
└── has many CheckOuts

Categories
├── has many Assets
└── has many Models

Locations
└── has many Assets

Departments
└── has many Users
```

---

## Re-Engineering Objectives

1. **Reverse Engineer:** Extract current architecture
2. **Analyze:** Identify patterns and anti-patterns
3. **Document:** Create comprehensive architecture diagrams
4. **Refactor:** Propose modernization strategy
5. **Modernize:** Design microservices-based approach

---

## Project Timeline

- **Phase 1:** Code Analysis & Architecture Recovery
- **Phase 2:** Reverse Engineering & Visualization
- **Phase 3:** Quality Assessment & Code Smell Detection
- **Phase 4:** Refactoring Recommendations
- **Phase 5:** Modernization Strategy & Migration Plan
