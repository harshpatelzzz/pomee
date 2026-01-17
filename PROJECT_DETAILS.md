# Employee Management System - Complete Project Documentation

## Table of Contents
1. [Project Overview](#project-overview)
2. [System Architecture](#system-architecture)
3. [Technology Stack](#technology-stack)
4. [Database Schema](#database-schema)
5. [Models and Relationships](#models-and-relationships)
6. [User Roles and Permissions](#user-roles-and-permissions)
7. [Features and Modules](#features-and-modules)
8. [API Routes and Endpoints](#api-routes-and-endpoints)
9. [Controllers](#controllers)
10. [Middleware](#middleware)
11. [Frontend Structure](#frontend-structure)
12. [Configuration](#configuration)
13. [Deployment](#deployment)
14. [Project Structure](#project-structure)

---

## Project Overview

The Employee Management System (HRMS) is a comprehensive web-based Human Resource Management System built with Laravel 10. It provides a complete solution for managing employees, attendance, leaves, payroll, and organizational structure within an organization.

### Key Characteristics
- **Multi-role Authentication System** with role-based access control
- **Complete HR Operations** management
- **Real-time Attendance Tracking**
- **Automated Payroll Processing**
- **Leave Management System**
- **Department and Designation Management**
- **Comprehensive Reporting System**

### Project Type
Web Application (HRMS/ERP System)

### Target Users
- HR Managers
- Administrators
- Payroll Managers
- Department Heads
- Employees
- Super Admins

---

## System Architecture

### Architecture Pattern
- **MVC (Model-View-Controller)** Architecture
- **Laravel Framework** following PSR standards
- **RESTful API** design principles

### Application Flow
```
User Request → Middleware → Route → Controller → Model → Database
                                    ↓
                                  View (Blade Template)
```

### Key Components
1. **Authentication Layer**: Laravel Breeze with multi-role support
2. **Business Logic Layer**: Controllers handling all operations
3. **Data Access Layer**: Eloquent ORM models
4. **Presentation Layer**: Blade templates with AdminKit UI
5. **Security Layer**: Middleware for role-based access control

---

## Technology Stack

### Backend
- **Framework**: Laravel 10.17.1
- **PHP Version**: 8.1+ (8.2 recommended)
- **Database**: MySQL 8.0
- **ORM**: Eloquent
- **Authentication**: Laravel Breeze
- **API**: Laravel Sanctum

### Frontend
- **CSS Framework**: Bootstrap 5
- **JavaScript**: Alpine.js 3.4.2
- **Build Tool**: Vite 4.0
- **CSS Preprocessor**: Tailwind CSS 3.1.0
- **Icons**: Font Awesome
- **UI Template**: AdminKit Admin Panel

### Development Tools
- **Package Manager**: Composer 2.5+
- **Node Package Manager**: npm/yarn
- **Version Control**: Git
- **Containerization**: Docker (optional)

### Key PHP Packages
- `laravel/framework`: ^10.10
- `laravel/sanctum`: ^3.2
- `laravel/breeze`: ^1.22
- `barryvdh/laravel-debugbar`: ^3.8
- `guzzlehttp/guzzle`: ^7.2

### Key JavaScript Packages
- `vite`: ^4.0.0
- `alpinejs`: ^3.4.2
- `tailwindcss`: ^3.1.0
- `axios`: ^1.1.2

---

## Database Schema

### Database Name
`laravel_hrms` (configurable in .env)

### Tables Overview

#### 1. **users**
Stores system users and authentication data.

| Column | Type | Description |
|--------|------|-------------|
| id | bigint | Primary key |
| role_id | bigint | Foreign key to roles table (default: 8) |
| name | string | User full name |
| email | string | Unique email address |
| phone | string | Unique phone number |
| status | tinyint | User status (0/1) |
| password | string | Hashed password |
| email_verified_at | timestamp | Email verification timestamp |
| remember_token | string | Remember me token |
| created_at | timestamp | Record creation time |
| updated_at | timestamp | Record update time |

**Relationships:**
- Belongs to `roles` (role_id)
- Has many `employees` (if linked)

#### 2. **roles**
Defines user roles and permissions.

| Column | Type | Description |
|--------|------|-------------|
| id | bigint | Primary key |
| title | string | Role title (e.g., "Super Admin") |
| slug | string | Role slug (e.g., "super-admin") |
| description | text | Role description |
| status | tinyint | Role status (0/1) |
| created_at | timestamp | Record creation time |
| updated_at | timestamp | Record update time |

**Default Roles:**
1. Super Admin (slug: `super-admin`)
2. Administrator (slug: `administrator`)
3. Moderator (slug: `moderator`)
4. HR Manager (slug: `hr-manager`)
5. Payroll Manager (slug: `payroll-manager`)
6. Data Analyst (slug: `data-analyst`)
7. Department Head (slug: `department-head`)
8. Employee (slug: `employee`)

#### 3. **departments**
Organizational departments.

| Column | Type | Description |
|--------|------|-------------|
| id | bigint | Primary key |
| title | string | Department name |
| description | text | Department description |
| status | tinyint | Department status (0/1) |
| created_at | timestamp | Record creation time |
| updated_at | timestamp | Record update time |

#### 4. **designations**
Job titles/positions.

| Column | Type | Description |
|--------|------|-------------|
| id | bigint | Primary key |
| title | string | Designation name |
| description | text | Designation description |
| status | tinyint | Designation status (0/1) |
| created_at | timestamp | Record creation time |
| updated_at | timestamp | Record update time |

#### 5. **schedules**
Working schedules/shifts.

| Column | Type | Description |
|--------|------|-------------|
| id | bigint | Primary key |
| title | string | Schedule name |
| time_in | time | Check-in time |
| time_out | time | Check-out time |
| created_at | timestamp | Record creation time |
| updated_at | timestamp | Record update time |

#### 6. **employees**
Employee master data.

| Column | Type | Description |
|--------|------|-------------|
| id | bigint | Primary key |
| department_id | bigint | Foreign key to departments |
| designation_id | bigint | Foreign key to designations |
| schedule_id | bigint | Foreign key to schedules |
| firstname | string(50) | Employee first name |
| lastname | string(50) | Employee last name |
| unique_id | string(25) | Employee unique identifier |
| avatar | string | Profile picture path |
| email | string(100) | Unique email address |
| phone | string(19) | Unique phone number |
| address | string | Employee address |
| dob | date | Date of birth |
| gender | tinyint | Gender (0/1) |
| religion | tinyint | Religion code |
| marital | tinyint | Marital status |
| status | tinyint | Employee status (default: 0) |
| created_at | timestamp | Record creation time |
| updated_at | timestamp | Record update time |

**Relationships:**
- Belongs to `departments`
- Belongs to `designations`
- Belongs to `schedules`
- Has one `salary`
- Has many `attendances`
- Has many `leaves`
- Has many `payrolls`
- Has many `late_times`
- Has many `over_times`

#### 7. **attendances**
Daily attendance records.

| Column | Type | Description |
|--------|------|-------------|
| id | bigint | Primary key |
| employee_id | bigint | Foreign key to employees |
| state | boolean | Attendance state (0/1) |
| attendance_time | time | Check-in/out time |
| attendance_date | date | Attendance date |
| type | boolean | Attendance type (0/1) |
| status | tinyint | Status |
| created_at | timestamp | Record creation time |
| updated_at | timestamp | Record update time |

#### 8. **leaves**
Leave requests and records.

| Column | Type | Description |
|--------|------|-------------|
| id | bigint | Primary key |
| employee_id | bigint | Foreign key to employees |
| leave_type | string | Type of leave |
| start_date | date | Leave start date |
| end_date | date | Leave end date |
| days | integer | Number of leave days |
| reason | text | Leave reason |
| status | tinyint | Approval status |
| created_at | timestamp | Record creation time |
| updated_at | timestamp | Record update time |

#### 9. **salaries**
Employee salary structure.

| Column | Type | Description |
|--------|------|-------------|
| id | bigint | Primary key |
| employee_id | bigint | Foreign key to employees |
| basic | decimal(10,2) | Basic salary |
| house_rent | decimal(10,2) | House rent allowance |
| medical | decimal(10,2) | Medical allowance |
| transport | decimal(10,2) | Transport allowance |
| phone_bill | decimal(10,2) | Phone bill allowance |
| internet_bill | decimal(10,2) | Internet bill allowance |
| special | decimal(10,2) | Special allowance |
| overtime_pay | decimal(10,2) | Overtime pay rate |
| provident_fund | decimal(10,2) | PF deduction |
| income_tax | decimal(10,2) | Income tax deduction |
| health_insurance | decimal(10,2) | Health insurance |
| life_insurance | decimal(10,2) | Life insurance |
| created_at | timestamp | Record creation time |
| updated_at | timestamp | Record update time |

#### 10. **payrolls**
Monthly payroll records.

| Column | Type | Description |
|--------|------|-------------|
| id | bigint | Primary key |
| employee_id | bigint | Foreign key to employees |
| year | year | Payroll year |
| month | tinyint | Payroll month (1-12) |
| basic | decimal(10,2) | Basic salary |
| house_rent | decimal(10,2) | House rent |
| medical | decimal(10,2) | Medical allowance |
| transport | decimal(10,2) | Transport allowance |
| phone_bill | decimal(10,2) | Phone bill |
| internet_bill | decimal(10,2) | Internet bill |
| special | decimal(10,2) | Special allowance |
| overtime_pay | decimal(10,2) | Overtime pay |
| provident_fund | decimal(10,2) | PF deduction |
| income_tax | decimal(10,2) | Income tax |
| health_insurance | decimal(10,2) | Health insurance |
| life_insurance | decimal(10,2) | Life insurance |
| days_present | string | Days present |
| days_absent | string | Days absent |
| gross_salary | decimal(10,2) | Gross salary |
| deduction | decimal(10,2) | Total deductions |
| net_salary | decimal(10,2) | Net salary |
| created_at | timestamp | Record creation time |
| updated_at | timestamp | Record update time |

#### 11. **late_times**
Late arrival tracking.

| Column | Type | Description |
|--------|------|-------------|
| id | bigint | Primary key |
| employee_id | bigint | Foreign key to employees |
| date | date | Late arrival date |
| time | time | Late arrival time |
| created_at | timestamp | Record creation time |
| updated_at | timestamp | Record update time |

#### 12. **over_times**
Overtime tracking.

| Column | Type | Description |
|--------|------|-------------|
| id | bigint | Primary key |
| employee_id | bigint | Foreign key to employees |
| date | date | Overtime date |
| hours | decimal | Overtime hours |
| created_at | timestamp | Record creation time |
| updated_at | timestamp | Record update time |

#### 13. **checks**
Attendance check-in/out records.

| Column | Type | Description |
|--------|------|-------------|
| id | bigint | Primary key |
| employee_id | bigint | Foreign key to employees |
| check_in | time | Check-in time |
| check_out | time | Check-out time |
| date | date | Check date |
| created_at | timestamp | Record creation time |
| updated_at | timestamp | Record update time |

#### 14. **departs**
Employee departure/check-out records.

| Column | Type | Description |
|--------|------|-------------|
| id | bigint | Primary key |
| employee_id | bigint | Foreign key to employees |
| depart_time | time | Departure time |
| date | date | Departure date |
| created_at | timestamp | Record creation time |
| updated_at | timestamp | Record update time |

#### 15. **time_trackings**
Time tracking records.

| Column | Type | Description |
|--------|------|-------------|
| id | bigint | Primary key |
| employee_id | bigint | Foreign key to employees |
| date | date | Tracking date |
| hours_worked | decimal | Hours worked |
| created_at | timestamp | Record creation time |
| updated_at | timestamp | Record update time |

#### 16. **password_reset_tokens**
Password reset tokens (Laravel default).

#### 17. **failed_jobs**
Failed job queue records (Laravel default).

#### 18. **personal_access_tokens**
API authentication tokens (Laravel Sanctum).

---

## Models and Relationships

### Model Structure

#### User Model
```php
App\Models\User
```
**Fillable Fields:** `role_id`, `name`, `email`, `phone`, `status`, `password`

**Relationships:**
- `belongsTo(Role::class)` - User has one role

#### Role Model
```php
App\Models\Role
```
**Fillable Fields:** `title`, `description`, `slug`, `status`

**Relationships:**
- `hasMany(User::class)` - Role has many users

#### Employee Model
```php
App\Models\Employee
```
**Fillable Fields:** `department_id`, `designation_id`, `schedule_id`, `firstname`, `lastname`, `unique_id`, `email`, `phone`, `address`, `dob`, `gender`, `religion`, `marital`, `status`

**Relationships:**
- `belongsTo(Department::class)`
- `belongsTo(Designation::class)`
- `belongsTo(Schedule::class)`
- `hasOne(Salary::class)`
- `hasMany(Attendance::class)`
- `hasMany(Leave::class)`
- `hasMany(Payroll::class)`
- `hasMany(LateTime::class)`
- `hasMany(OverTime::class)`
- `hasMany(Check::class)`
- `hasMany(Depart::class)`

#### Department Model
```php
App\Models\Department
```
**Fillable Fields:** `title`, `description`, `status`

#### Designation Model
```php
App\Models\Designation
```
**Fillable Fields:** `title`, `description`, `status`

#### Schedule Model
```php
App\Models\Schedule
```
**Fillable Fields:** `title`, `time_in`, `time_out`

#### Attendance Model
```php
App\Models\Attendance
```
**Fillable Fields:** `employee_id`, `state`, `attendance_time`, `attendance_date`, `type`, `status`

**Relationships:**
- `belongsTo(Employee::class)`

#### Leave Model
```php
App\Models\Leave
```
**Fillable Fields:** `employee_id`, `leave_type`, `start_date`, `end_date`, `days`, `reason`, `status`

**Relationships:**
- `belongsTo(Employee::class)`

#### Salary Model
```php
App\Models\Salary
```
**Fillable Fields:** `employee_id`, `basic`, `house_rent`, `medical`, `transport`, `phone_bill`, `internet_bill`, `special`, `overtime_pay`, `provident_fund`, `income_tax`, `health_insurance`, `life_insurance`

**Relationships:**
- `belongsTo(Employee::class)`

#### Payroll Model
```php
App\Models\Payroll
```
**Fillable Fields:** All salary fields plus `year`, `month`, `days_present`, `days_absent`, `gross_salary`, `deduction`, `net_salary`

**Relationships:**
- `belongsTo(Employee::class)`

---

## User Roles and Permissions

### Role-Based Access Control (RBAC)

The system implements a comprehensive role-based access control system with 8 predefined roles:

#### 1. Super Admin (`super-admin`)
**Access Level:** Full System Access

**Permissions:**
- All system features
- User management (create, edit, delete users)
- Role management
- Employee management
- Department management
- Designation management
- Attendance management
- Leave management
- Payroll management
- Reports and analytics
- System settings

**Routes:** `/super/*`

#### 2. Administrator (`administrator`)
**Access Level:** High Administrative Access

**Permissions:**
- User management
- Employee management
- Department management
- Designation management
- Schedule management
- Attendance management
- Leave management
- Payroll viewing

**Routes:** `/admin/*`

#### 3. Moderator (`moderator`)
**Access Level:** Attendance Management

**Permissions:**
- Schedule management
- Daily attendance tracking
- Attendance reports
- Check-in/check-out management

**Routes:** `/moderator/*`

#### 4. HR Manager (`hr-manager`)
**Access Level:** Human Resources Management

**Permissions:**
- Employee management
- Department management
- Designation management
- Leave management (approve/reject)
- Employee profiles

**Routes:** `/hr-manager/*`

#### 5. Payroll Manager (`payroll-manager`)
**Access Level:** Payroll Operations

**Permissions:**
- Payroll management
- Generate payroll
- Payroll reports
- Salary calculations

**Routes:** `/manager/*`

#### 6. Data Analyst (`data-analyst`)
**Access Level:** Read-Only Analytics

**Permissions:**
- View reports
- View analytics
- Data export

#### 7. Department Head (`department-head`)
**Access Level:** Department-Specific Access

**Permissions:**
- View department employees
- Approve department leaves
- View department reports

#### 8. Employee (`employee`)
**Access Level:** Basic User Access

**Permissions:**
- View own profile
- View own attendance
- Apply for leaves
- View own payroll

**Routes:** `/dashboard`

### Middleware Implementation

The system uses custom middleware to enforce role-based access:

- `SuperMiddleware` - Super admin access
- `AdminMiddleware` - Administrator access
- `HRMiddleware` - HR manager access
- `ModeratorMiddleware` - Moderator access
- `PayrollMiddleware` - Payroll manager access

---

## Features and Modules

### 1. User Authentication System
- **Registration**: User registration with email, name, phone, and password
- **Login**: Secure login with email/password
- **Password Reset**: Forgot password functionality
- **Email Verification**: Optional email verification
- **Remember Me**: Persistent login sessions
- **Multi-role Support**: Role-based authentication

### 2. User Management
- Create, read, update, delete users
- Assign roles to users
- Activate/deactivate users
- User profile management

### 3. Employee Management
- **Employee CRUD Operations**
  - Create new employees
  - View employee list
  - Edit employee details
  - Delete employees
- **Employee Profiles**
  - Personal information
  - Contact details
  - Job information (department, designation, schedule)
  - Salary information
  - Attendance history
  - Leave history
- **Employee Search and Filter**
- **Employee Status Management**

### 4. Department Management
- Create departments
- Edit department details
- Delete departments
- View department employees
- Department status management

### 5. Designation Management
- Create designations (job titles)
- Edit designation details
- Delete designations
- Assign designations to employees

### 6. Schedule Management
- Create working schedules
- Define check-in and check-out times
- Assign schedules to employees
- Edit and delete schedules

### 7. Attendance System
- **Daily Attendance Tracking**
  - Check-in functionality
  - Check-out functionality
  - Attendance state management
- **Attendance Reports**
  - Daily attendance sheet
  - Monthly attendance reports
  - Employee attendance history
- **Late Time Tracking**
  - Record late arrivals
  - Late time reports
- **Overtime Tracking**
  - Record overtime hours
  - Overtime reports
- **Barcode/QR Code Attendance** (if implemented)

### 8. Leave Management
- **Leave Application**
  - Apply for leaves
  - Leave type selection
  - Date range selection
  - Reason submission
- **Leave Approval**
  - Approve/reject leave requests
  - Leave status management
- **Leave Reports**
  - Leave history
  - Leave balance
  - Leave statistics

### 9. Payroll System
- **Salary Structure Management**
  - Basic salary
  - Allowances (house rent, medical, transport, etc.)
  - Deductions (PF, tax, insurance, etc.)
- **Payroll Generation**
  - Monthly payroll calculation
  - Automatic gross salary calculation
  - Automatic deduction calculation
  - Net salary calculation
- **Payroll Reports**
  - Monthly payroll sheets
  - Employee payroll history
  - Payroll summary reports

### 10. Reports and Analytics
- Attendance reports
- Leave reports
- Payroll reports
- Employee reports
- Department-wise reports
- Custom date range reports

### 11. Dashboard
- **Super Admin Dashboard**
  - System overview
  - Total employees count
  - Total users count
  - Recent activities
  - Quick access to all modules
- **Role-Specific Dashboards**
  - Customized for each role
  - Relevant statistics
  - Quick actions

---

## API Routes and Endpoints

### Authentication Routes (`routes/auth.php`)

```
GET  /register          - Registration form
POST /register          - Process registration
GET  /login             - Login form
POST /login             - Process login
POST /logout            - Logout user
GET  /forgot-password   - Password reset form
POST /forgot-password   - Send reset link
GET  /reset-password/{token} - Reset password form
POST /reset-password    - Process password reset
```

### Super Admin Routes (`/super/*`)

```
GET    /super                    - Super admin dashboard
GET    /super/employee           - List employees
POST   /super/employee           - Create employee
GET    /super/employee/{id}      - Show employee
GET    /super/employee/{id}/edit - Edit employee form
PUT    /super/employee/{id}      - Update employee
DELETE /super/employee/{id}      - Delete employee

GET    /super/department         - List departments
POST   /super/department         - Create department
GET    /super/department/{id}/edit - Edit department
PUT    /super/department/{id}    - Update department
DELETE /super/department/{id}    - Delete department

GET    /super/designation        - List designations
POST   /super/designation        - Create designation
GET    /super/designation/{id}/edit - Edit designation
PUT    /super/designation/{id}   - Update designation
DELETE /super/designation/{id}   - Delete designation

GET    /super/schedule           - List schedules
POST   /super/schedule           - Create schedule
GET    /super/schedule/{id}/edit - Edit schedule
PUT    /super/schedule/{id}      - Update schedule
DELETE /super/schedule/{id}      - Delete schedule

GET    /super/attendance         - Attendance management
POST   /super/check             - Check-in/out
GET    /super/report            - Attendance report
GET    /super/barcode           - Barcode attendance

GET    /super/leaves            - List leaves
POST   /super/leaves            - Create leave
GET    /super/leaves/{id}/edit  - Edit leave
PUT    /super/leaves/{id}       - Update leave
DELETE /super/leaves/{id}       - Delete leave

GET    /super/payroll           - List payrolls
POST   /super/payroll           - Create payroll
GET    /super/payroll/create    - Generate payroll form
POST   /super/calculate         - Calculate payroll
GET    /super/sheet-report      - Payroll sheet report

GET    /super/user              - List users
POST   /super/user              - Create user
GET    /super/user/{id}/edit    - Edit user
PUT    /super/user/{id}         - Update user
DELETE /super/user/{id}         - Delete user

GET    /super/roles             - List roles
POST   /super/roles             - Create role
GET    /super/roles/{id}/edit  - Edit role
PUT    /super/roles/{id}       - Update role
DELETE /super/roles/{id}       - Delete role
```

### Admin Routes (`/admin/*`)

Similar structure to super admin but with limited permissions.

### HR Manager Routes (`/hr-manager/*`)

```
GET    /hr-manager                    - HR dashboard
GET    /hr-manager/employee           - List employees
POST   /hr-manager/employee           - Create employee
GET    /hr-manager/employee/{id}/show - Show employee
GET    /hr-manager/employee/{id}/edit - Edit employee
PUT    /hr-manager/employee/{id}      - Update employee
DELETE /hr-manager/employee/{id}     - Delete employee

GET    /hr-manager/department         - List departments
POST   /hr-manager/department         - Create department
GET    /hr-manager/designation        - List designations
POST   /hr-manager/designation        - Create designation

GET    /hr-manager/leaves            - List leaves
POST   /hr-manager/leaves            - Create leave
GET    /hr-manager/leaves/{id}/edit  - Edit leave
PUT    /hr-manager/leaves/{id}       - Update leave
DELETE /hr-manager/leaves/{id}       - Delete leave
```

### Moderator Routes (`/moderator/*`)

```
GET    /moderator                    - Moderator dashboard
GET    /moderator/schedule           - List schedules
POST   /moderator/schedule           - Create schedule
GET    /moderator/attendance         - Attendance management
POST   /moderator/check             - Check-in/out
GET    /moderator/report            - Attendance report
```

### Payroll Manager Routes (`/manager/*`)

```
GET    /manager                     - Payroll dashboard
GET    /manager/payroll             - List payrolls
GET    /manager/payroll/create      - Generate payroll form
POST   /manager/calculate           - Calculate payroll
```

### General Routes

```
GET    /dashboard                   - User dashboard (authenticated)
GET    /profile                     - User profile
PATCH  /profile                     - Update profile
DELETE /profile                     - Delete account
```

---

## Controllers

### Authentication Controllers
- `AuthenticatedSessionController` - Login/logout
- `RegisteredUserController` - User registration
- `PasswordResetLinkController` - Password reset
- `NewPasswordController` - New password setup
- `EmailVerificationNotificationController` - Email verification
- `ConfirmablePasswordController` - Password confirmation

### Main Controllers
- `AdminController` - Dashboard and admin operations
- `UserController` - User management
- `EmployeeController` - Employee CRUD operations
- `DepartmentController` - Department management
- `DesignationController` - Designation management
- `ScheduleController` - Schedule management
- `AttendanceController` - Attendance tracking
- `LeaveController` - Leave management
- `PayrollController` - Payroll operations
- `SalaryController` - Salary management
- `RoleController` - Role management
- `CheckController` - Check-in/out operations
- `LateTimeController` - Late time tracking
- `OverTimeController` - Overtime tracking
- `ProfileController` - User profile management

### Controller Methods Pattern

Most controllers follow RESTful conventions:

```php
index()    - List all resources
create()   - Show create form
store()    - Store new resource
show($id)  - Show single resource
edit($id)  - Show edit form
update($id) - Update resource
destroy($id) - Delete resource
```

---

## Middleware

### Authentication Middleware
- `auth` - Verify user is authenticated
- `guest` - Verify user is not authenticated
- `verified` - Verify email is verified

### Role-Based Middleware
- `SuperMiddleware` - Super admin access only
- `AdminMiddleware` - Administrator access
- `HRMiddleware` - HR manager access
- `ModeratorMiddleware` - Moderator access
- `PayrollMiddleware` - Payroll manager access

### Other Middleware
- `EncryptCookies` - Encrypt cookies
- `VerifyCsrfToken` - CSRF protection
- `TrimStrings` - Trim input strings
- `TrustProxies` - Trust proxy headers

---

## Frontend Structure

### Blade Templates Location
`resources/views/`

### Template Structure
```
resources/views/
├── auth/              - Authentication views
│   ├── login.blade.php
│   ├── register.blade.php
│   └── ...
├── admin/            - Admin panel views
│   ├── employee/
│   ├── user/
│   ├── department/
│   ├── designation/
│   ├── attendance/
│   ├── leaves/
│   └── payroll/
├── layouts/          - Layout templates
│   ├── admin.blade.php
│   └── ...
├── components/       - Reusable components
├── partials/         - Partial views
│   ├── sidebar.blade.php
│   ├── header.blade.php
│   └── ...
├── dashboard.blade.php
└── welcome.blade.php
```

### Assets Location
```
public/
├── css/              - Compiled CSS
├── js/               - JavaScript files
├── img/              - Images
│   ├── avatars/      - User avatars
│   ├── photos/       - Employee photos
│   └── screenshots/  - UI screenshots
└── fonts/            - Font files
```

### Frontend Technologies
- **Bootstrap 5** - CSS framework
- **AdminKit** - Admin panel template
- **Font Awesome** - Icons
- **Alpine.js** - JavaScript framework
- **Vite** - Build tool
- **Tailwind CSS** - Utility-first CSS

---

## Configuration

### Environment Variables (`.env`)

```env
APP_NAME="Employee Management System"
APP_ENV=local
APP_KEY=base64:...
APP_DEBUG=true
APP_URL=http://localhost

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=laravel_hrms
DB_USERNAME=root
DB_PASSWORD=

SESSION_DRIVER=file
SESSION_LIFETIME=120

MAIL_MAILER=smtp
MAIL_HOST=mailpit
MAIL_PORT=1025
```

### Key Configuration Files
- `config/app.php` - Application configuration
- `config/database.php` - Database configuration
- `config/auth.php` - Authentication configuration
- `config/session.php` - Session configuration
- `config/mail.php` - Mail configuration

---

## Deployment

### Docker Deployment

The project includes `docker-compose.yml` for containerized deployment:

```yaml
services:
  app:
    image: php:8.2-cli
    ports:
      - "8000:8000"
    volumes:
      - .:/var/www/html
  
  mysql:
    image: mysql:8.0
    environment:
      MYSQL_DATABASE: laravel_hrms
      MYSQL_ROOT_PASSWORD: root
    ports:
      - "3306:3306"
```

### Deployment Steps

1. **Clone Repository**
   ```bash
   git clone https://github.com/harshpatelzzz/pomee.git
   cd employee-management-system
   ```

2. **Install Dependencies**
   ```bash
   composer install
   npm install
   npm run build
   ```

3. **Configure Environment**
   ```bash
   cp .env.example .env
   php artisan key:generate
   ```

4. **Database Setup**
   ```bash
   php artisan migrate --seed
   ```

5. **Start Server**
   ```bash
   php artisan serve
   ```

### Production Deployment

1. Set `APP_ENV=production`
2. Set `APP_DEBUG=false`
3. Configure proper database credentials
4. Set up web server (Apache/Nginx)
5. Configure SSL certificate
6. Set up queue workers (if using queues)
7. Configure cron jobs for scheduled tasks

---

## Project Structure

```
employee-management-system/
├── app/
│   ├── Console/
│   ├── Exceptions/
│   ├── Http/
│   │   ├── Controllers/
│   │   │   ├── Auth/
│   │   │   └── ...
│   │   ├── Middleware/
│   │   └── Requests/
│   ├── Models/
│   ├── Providers/
│   └── View/
├── bootstrap/
├── config/
├── database/
│   ├── factories/
│   ├── migrations/
│   └── seeders/
├── public/
│   ├── css/
│   ├── js/
│   ├── img/
│   └── index.php
├── resources/
│   ├── css/
│   ├── js/
│   └── views/
│       ├── admin/
│       ├── auth/
│       ├── components/
│       ├── layouts/
│       └── partials/
├── routes/
│   ├── api.php
│   ├── auth.php
│   └── web.php
├── storage/
├── tests/
├── vendor/
├── .env
├── .env.example
├── .gitignore
├── artisan
├── composer.json
├── composer.lock
├── docker-compose.yml
├── package.json
├── phpunit.xml
├── README.md
└── vite.config.js
```

---

## Default Credentials

### Pre-seeded Admin Accounts

After running migrations with seeders, you can login with:

**Super Admin:**
- Email: `mohona@gmail.com` | Password: `admin`
- Email: `shorifa@gmail.com` | Password: `admin`
- Email: `shawonk007@gmail.com` | Password: `Dhaka1216`

**Administrator:**
- Email: `tony@stark.com` | Password: `secret`

**Moderator:**
- Email: `admin@email.com` | Password: `secret`
- Email: `jsmith@email.com` | Password: `secret`

**HR Manager:**
- Email: `john@email.com` | Password: `secret`

**Payroll Manager:**
- Email: `james@email.com` | Password: `secret`

---

## Development Notes

### Key Features Implemented
- ✅ Multi-role authentication system
- ✅ User management with role assignment
- ✅ Employee management (CRUD)
- ✅ Department and designation management
- ✅ Schedule management
- ✅ Attendance tracking system
- ✅ Leave management system
- ✅ Payroll calculation and management
- ✅ Reports and analytics
- ✅ Role-based access control
- ✅ Docker support

### Recent Updates
- Added phone field to user registration
- Added Docker Compose configuration
- Fixed registration form validation

### Future Enhancements (Potential)
- Email notifications
- SMS integration
- Advanced reporting
- Mobile app API
- Real-time notifications
- Document management
- Performance evaluation module
- Task assignment module

---

## License

This project is licensed under the **GNU General Public License version 3.0 (GPL-3.0)**.

---

## Support and Contribution

### Original Contributors
- **Mohona Akter** - Multirole Authentication, User Management, Attendance System
- **Mst.Shorifa Akter** - Staff Management, Leave Management, Payroll System
- **Muhammad Nasir Uddin Khan Shawon** - Database Design & Management

### Contributing
1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Submit a pull request

---

**Document Version:** 1.0  
**Last Updated:** January 2026  
**Project Status:** Active Development
