# Power Platform Training Manual - Stage 5: Dataverse

## Overview

This stage covers Microsoft Dataverse, the enterprise-grade data platform for Power Platform. Learn about tables, columns, relationships, security, business rules, and data modeling. **Total Duration: 10 hours**

---

## Module 33: Introduction to Dataverse (1 hour)

### Learning Objectives

- Understand what Dataverse is
- Learn Dataverse vs SharePoint differences
- Explore standard and custom tables
- Navigate Dataverse environment
- Understand licensing requirements
- Plan data migration from SharePoint

### Step-by-Step Instructions

#### Step 1: What is Dataverse?

**Definition:**

```
Microsoft Dataverse = Cloud-based relational database for Power Platform

Key Features:
✅ Enterprise-grade security (row-level)
✅ Relational database with relationships
✅ Built-in business logic (business rules, workflows)
✅ Rich data types (choices, lookups, calculated fields)
✅ Auditing and change tracking
✅ API access (REST, OData)
✅ Integration with Microsoft 365, Dynamics 365
✅ Scalable (millions of records)
✅ Multi-environment support
```

**Architecture:**

```
Power Platform Environment
├── Dataverse Database
│   ├── Standard Tables (built-in)
│   │   ├── Account
│   │   ├── Contact
│   │   ├── User
│   │   └── Team
│   ├── Custom Tables (you create)
│   │   ├── Leave Request
│   │   ├── Employee Profile
│   │   └── Department
│   ├── Security Roles
│   ├── Business Rules
│   └── Relationships
├── Canvas Apps
├── Model-Driven Apps
└── Flows
```

#### Step 2: Dataverse vs SharePoint

**Comparison:**

| Feature            | SharePoint Lists                  | Dataverse                          |
| ------------------ | --------------------------------- | ---------------------------------- |
| **Data Model**     | Flat lists                        | Relational database                |
| **Relationships**  | Lookup columns (limited)          | True foreign keys (1:N, N:1, N:N)  |
| **Security**       | List/item permissions             | Row-level security with roles      |
| **Data Types**     | 20+ column types                  | 40+ data types                     |
| **Business Logic** | Workflows, Power Automate         | Business rules, plugins, workflows |
| **Scalability**    | 30M items per list                | Billions of rows                   |
| **Offline**        | Limited                           | Full offline support               |
| **API**            | REST API                          | OData, REST, SDK                   |
| **Licensing**      | Office 365                        | Dataverse capacity                 |
| **Best For**       | Document management, simple lists | Enterprise apps, complex data      |

**When to Use Dataverse:**

```
✅ Use Dataverse when:
- Complex data relationships (multiple lookups)
- Advanced security requirements (row-level)
- Large datasets (millions of records)
- Need offline capability
- Building Model-Driven Apps
- Integration with Dynamics 365
- Multi-environment deployment
- Enterprise-grade auditing
- Complex business logic
- High performance requirements

✅ Use SharePoint when:
- Simple lists (< 5,000 items)
- Document management primary use
- Office 365 integration focus
- No licensing budget for Dataverse
- Quick prototyping
- Existing SharePoint investment
```

#### Step 3: Access Dataverse

**Navigate to Dataverse:**

```
Method 1: Power Apps Portal
1. Go to make.powerapps.com
2. Select environment (top right)
3. Left navigation → Tables
4. View all tables in environment

Method 2: Solutions
1. make.powerapps.com
2. Solutions → Select solution
3. Add existing → Table
4. Select tables to add

Method 3: Directly via URL
https://make.powerapps.com/environments/[environment-id]/entities
```

**Environment Selection:**

```
Top right dropdown shows:
- Personal Productivity (default environment)
- [Your Company] (Dev)
- [Your Company] (Test)
- [Your Company] (Production)

⚠️ Always verify you're in correct environment!
```

#### Step 4: Standard Tables

**Common Standard Tables:**

```
Account:
- Represents organizations/companies
- Fields: Account Name, Primary Contact, Address, Phone
- Used in: CRM scenarios

Contact:
- Represents people
- Fields: First Name, Last Name, Email, Phone
- Related to Account

User:
- System users (employees)
- Synced from Azure AD
- Cannot be deleted
- Used for: Owner fields, Created By, Modified By

Team:
- Groups of users
- Used for: Sharing records, assignment
- Types: Owner team, Access team

Activity Tables:
- Email, Phone Call, Task, Appointment
- Track communications and activities

System Tables:
- Organization
- Business Unit
- Security Role
- Solution
```

**View Standard Tables:**

```
make.powerapps.com → Tables

Filter: Show: Standard tables

Examples you'll see:
- Account
- Contact
- Email
- Task
- Queue
- Team
- User
- Organization
- Business Unit
- Connection
- Note (Attachments)

Browse: Click any table to see columns, relationships, data
```

#### Step 5: Custom Tables

**What are Custom Tables?**

```
Custom Table = Table you create for your app needs

Examples:
- Leave Request
- Employee Profile
- Department
- Project
- Expense Report
- Asset
- Customer Feedback
```

**Table Naming:**

```
Display Name: Leave Request (user-friendly)
Plural Name: Leave Requests (for views/navigation)
Name (Schema): cr123_leaverequest (prefix + lowercase)

Prefix:
- Organization prefix (e.g., cr123)
- Assigned to your organization
- Ensures uniqueness
- Can't be changed after creation

Full Schema Name: cr123_leaverequest
Used in: Code, APIs, advanced formulas
```

#### Step 6: Licensing Requirements

**Dataverse Licensing:**

```
Dataverse Capacity:
- Database capacity: $40/GB/month
- File capacity: $2/GB/month (for attachments)
- Log capacity: $10/GB/month (for audit logs)

Included Capacity:
Per Power Apps license:
- 250 MB database
- 2 GB file storage
- 1 GB log storage

Tenant receives:
- 10 GB database (base)
- 20 GB file storage
- 2 GB log storage

Example Calculation:
100 users with Power Apps per user license:
Database: 10 GB (base) + (100 × 0.25 GB) = 35 GB included
File: 20 GB (base) + (100 × 2 GB) = 220 GB included
Log: 2 GB (base) + (100 × 1 GB) = 102 GB included
```

**Check Capacity:**

```
Power Platform Admin Center:
1. admin.powerplatform.microsoft.com
2. Resources → Capacity
3. View:
   - Total capacity
   - Used capacity
   - Available capacity
   - Per-environment breakdown

Storage Summary tab shows:
- Database storage
- File storage
- Log storage
- Trends over time
```

**Optimize Storage:**

```
Reduce Database Storage:
- Delete unnecessary records
- Archive old data
- Remove test data
- Bulk delete jobs

Reduce File Storage:
- Compress attachments
- Use Azure Blob Storage for large files
- Clean up duplicate files
- Set retention policies

Reduce Log Storage:
- Adjust audit settings
- Delete old audit logs
- Configure log retention
```

#### Step 7: Dataverse for Teams

**Special Dataverse Edition:**

```
Dataverse for Teams:
- Included with Microsoft Teams licenses
- Lighter version of Dataverse
- Limited to Teams context
- No additional cost

Limitations:
- 1 million rows per environment
- 2 GB storage per environment
- Only accessible within Teams
- Limited API access
- No Model-Driven Apps (only Canvas)
- Can't export to full Dataverse easily

Use for:
- Team-specific apps
- Departmental solutions
- Prototyping
- When budget is constrained
```

#### Step 8: Data Types Overview

**Common Dataverse Data Types:**

```
Text Types:
- Single line of text (up to 4,000 characters)
- Multiple lines of text (up to 1M characters)
- Email (validates email format)
- Phone (phone number format)
- Ticker Symbol
- URL

Number Types:
- Whole Number (integer)
- Decimal Number
- Floating Point Number
- Currency (with currency type)

Date/Time:
- Date and Time
- Date Only
- Time Zone Independent

Choice Types:
- Choice (single select dropdown)
- Choices (multi-select)
- Yes/No (Boolean)

Lookup Types:
- Lookup (to another table)
- Customer (Account OR Contact)
- Owner (User OR Team)

Special Types:
- File (single file attachment)
- Image (single image)
- Autonumber (auto-increment)
- Calculated (formula-based)
- Rollup (aggregate from related records)
```

#### Step 9: Create First Custom Table

**Step-by-Step Creation:**

```
1. Go to make.powerapps.com
2. Select correct environment
3. Tables → + New table

Basic Information:
Display name: Leave Request
Plural name: Leave Requests
Description: Stores employee leave requests

✅ Enable attachments: Yes
✅ Enable offline: Yes (for mobile)

Advanced Options (expand):
- Choose table type: Standard
- Schema name: cr123_leaverequest (auto-generated)
- ✅ Track changes: Yes (for sync)
- ✅ Create a new activity: No
- ✅ Appear in search results: Yes

Primary Column (auto-created):
Name: Name
Display Name: Leave Request Name
Description: Unique name for the leave request

4. Click "Save"

Result: Table created with:
- Name column (primary)
- Created On, Created By
- Modified On, Modified By
- Owner
- Status, Status Reason
```

**What Gets Created Automatically:**

```
Every custom table includes:

System Columns (can't delete):
- Name (Primary Name column)
- Created On (DateTime)
- Created By (Lookup to User)
- Modified On (DateTime)
- Modified By (Lookup to User)
- Owner (User or Team)
- Owning Business Unit
- Status (Active/Inactive)
- Status Reason

Relationships:
- Owner (User or Team)
- Owning Business Unit
- Created By User
- Modified By User

Views:
- Active [Table Name] (default)
- Inactive [Table Name]
- Quick Find Active [Table Name]

Forms:
- Information (main form)
- Quick Create form
- Quick View form
```

#### Step 10: Leave Management System in Dataverse

**Planning Migration from SharePoint:**

```
Current SharePoint Lists:
1. Leave Requests
2. Employees
3. Leave Types
4. Leave Balance
5. Departments

Dataverse Tables (Planned):
1. Leave Request (custom)
   - Related to: Employee (lookup)
   - Related to: Leave Type (lookup)

2. Employee Profile (custom)
   - Related to: User (lookup - synced from AAD)
   - Related to: Department (lookup)
   - Related to: Manager (self-lookup)

3. Leave Type (custom)
   - Standalone table (Annual, Sick, Personal, etc.)

4. Leave Balance (custom)
   - Related to: Employee (lookup)
   - Related to: Leave Type (lookup)
   - Related to: Leave Request (1:N relationship)

5. Department (custom)
   - Related to: Employees (1:N)

Benefits of Moving to Dataverse:
✅ True relationships (foreign keys)
✅ Cascading deletes/updates
✅ Referential integrity
✅ Advanced security (row-level)
✅ Business rules (no code)
✅ Calculated fields
✅ Rollup fields (total leave days used)
✅ Offline capability
✅ Better performance with large data
```

**Migration Strategy:**

```
Phase 1: Setup (Week 1)
- Create Dataverse tables
- Define columns
- Create relationships
- Set up security roles

Phase 2: Data Migration (Week 2)
- Export SharePoint data
- Transform data format
- Import to Dataverse
- Validate data integrity

Phase 3: App Update (Week 3)
- Update Canvas app to use Dataverse
- Update flows to use Dataverse
- Test all functionality
- User acceptance testing

Phase 4: Cutover (Week 4)
- Deploy to production
- Switch users to new app
- Monitor for issues
- Archive old SharePoint lists
```

### Assignment Deliverable

✅ **Explore Dataverse environment:**

- Navigate to Tables section
- Browse standard tables (Account, Contact, User)
- View table columns and relationships
- Examine different data types
- Check organization settings

✅ **Create custom table:**

- Create "Leave Type" table
- Add columns: Name, Description, Days Allowed Per Year, Is Active
- Enable appropriate settings
- Document schema name

✅ **Document comparison:**

- Create comparison document: SharePoint vs Dataverse
- When to use each
- Migration considerations
- Cost analysis

### Knowledge Check

- [ ] What is Dataverse?
- [ ] How is it different from SharePoint?
- [ ] What are standard vs custom tables?
- [ ] What's a table schema name?
- [ ] What are licensing costs?
- [ ] What system columns are auto-created?
- [ ] What's Dataverse for Teams?
- [ ] When should you use Dataverse?
- [ ] What's a primary column?
- [ ] How do you check capacity usage?

### Resources

- [Microsoft Learn: What is Dataverse](https://learn.microsoft.com/en-us/power-apps/maker/data-platform/data-platform-intro)
- [Microsoft Learn: Create custom table](https://learn.microsoft.com/en-us/power-apps/maker/data-platform/data-platform-create-entity)
- [Microsoft Learn: Dataverse capacity](https://learn.microsoft.com/en-us/power-platform/admin/capacity-storage)
- [Microsoft Learn: SharePoint vs Dataverse](https://learn.microsoft.com/en-us/power-apps/guidance/planning/when-use-dataverse)

---

## Module 34: Dataverse Column Types (1.5 hours)

### Learning Objectives

- Create and configure columns
- Understand all column data types
- Use choice columns effectively
- Create calculated and rollup columns
- Configure column properties
- Implement validation rules

### Step-by-Step Instructions

#### Step 1: Add Columns to Table

**Navigate to Columns:**

```
1. make.powerapps.com → Tables
2. Select your table (Leave Request)
3. Columns tab
4. + New column (or Edit table → Add column in designer)
```

**Create Simple Text Column:**

```
Display name: Employee Comments
Data type: Text → Multiple lines of text
Required: Optional
Searchable: Yes
Max length: 2000 characters
Format: Text (or Email, Phone, Rich Text, etc.)
Description: Comments from employee about leave request

Click "Save"
```

#### Step 2: Text Column Types

**Single Line of Text:**

```
Display name: Leave Reason
Data type: Text → Single line of text
Format: Text

Format Options:
- Text (plain text)
- Email (validates email format)
- Phone (phone format)
- Ticker Symbol (stock ticker)
- URL (web address)

Max length: 100 to 4,000 characters
Default: 100 characters

Use for:
- Short text fields
- Email addresses
- Phone numbers
- URLs
```

**Multiple Lines of Text:**

```
Display name: Detailed Comments
Data type: Text → Multiple lines of text
Format: Text

Format Options:
- Text (plain text)
- Rich Text (HTML editor)

Max length: 1,048,576 characters (1MB)
Default: 2,000

Use for:
- Comments
- Descriptions
- Notes
- Rich formatted content
```

**Email & Phone:**

```
Email Column:
Display name: Emergency Contact Email
Data type: Text → Single line of text
Format: Email
✅ Validates email format automatically

Phone Column:
Display name: Emergency Contact Phone
Data type: Text → Single line of text
Format: Phone
Displays with phone formatting
```

#### Step 3: Number Column Types

**Whole Number:**

```
Display name: Total Days Requested
Data type: Number → Whole number
Min value: 0
Max value: 365
Required: Yes
Description: Total number of leave days requested

Use for:
- Counts
- Quantities
- Days, months, years
- IDs
```

**Decimal Number:**

```
Display name: Leave Hours
Data type: Number → Decimal number
Min value: 0
Max value: 2000
Precision: 2 (decimal places)

Use for:
- Hours (7.5 hours)
- Percentages
- Ratings (4.5 stars)
```

**Currency:**

```
Display name: Daily Rate
Data type: Currency
Precision: 2
Min value: 0
Max value: 10000

Currency Type:
○ Transaction currency (multiple currencies)
● Organization's base currency (USD)

Displays with currency symbol: $150.00

Use for:
- Money amounts
- Costs
- Salaries
- Budgets
```

#### Step 4: Date and Time

**Date and Time:**

```
Display name: Start Date Time
Data type: Date and Time
Format: Date and Time

Behavior:
● User Local (converts to user's time zone)
○ Date Only (ignores time)
○ Time Zone Independent (stores exact time)

IME Mode: Auto (for international input)

Use for:
- Timestamps
- Appointment times
- Event start/end
```

**Date Only:**

```
Display name: Leave Start Date
Data type: Date and Time
Format: Date Only
Behavior: Date Only

Stores: 2025-11-20 (no time component)
Displays: Nov 20, 2025

Use for:
- Birth dates
- Due dates
- Leave dates
- Anniversaries
```

**Understanding Behaviors:**

```
User Local:
- Stores in UTC
- Displays in user's time zone
- Example: User in PST sees 9:00 AM, user in EST sees 12:00 PM
- Use for: Meetings, appointments

Date Only:
- No time zone conversion
- Same date for all users
- Use for: Birth dates, due dates

Time Zone Independent:
- Stores exact time, no conversion
- All users see same time
- Use for: System timestamps, logs
```

#### Step 5: Choice Columns

**Single Choice (Dropdown):**

```
Display name: Leave Status
Data type: Choice → Choice
Sync with global choice: No (create new)

Choices:
1. Label: Draft          Value: 100000000
   External value: Draft

2. Label: Pending Approval   Value: 100000001
   External value: PendingApproval

3. Label: Approved       Value: 100000002
   External value: Approved

4. Label: Rejected       Value: 100000003
   External value: Rejected

5. Label: Cancelled      Value: 100000004
   External value: Cancelled

Default choice: Draft

✅ Can sync with global choice (reusable across tables)
```

**Global Choice (Reusable):**

```
Create Global Choice:
1. Tables → Choices (under Schema)
2. + New choice
3. Display name: Leave Status
4. Add choices (same as above)
5. Save

Use in Multiple Tables:
In any table column:
Data type: Choice
Sync with global choice: Yes
Global choice: Leave Status

Benefits:
- Consistency across tables
- Change once, applies everywhere
- Easier maintenance
```

**Multi-Select Choice:**

```
Display name: Skills
Data type: Choice → Choices (multi-select)

Add choices:
- Project Management
- Data Analysis
- Software Development
- Customer Service
- Sales
- Marketing

Allows selecting multiple values:
User can select: Project Management + Data Analysis + Sales

Stored as: Comma-separated values
Retrieved as: Array in Power Apps
```

**Choice Column Properties:**

```
Color Coding:
For each choice, click palette icon:
- Draft: Gray
- Pending Approval: Orange
- Approved: Green
- Rejected: Red
- Cancelled: Yellow

Displays with color in:
- Model-Driven Apps
- Forms
- Views
```

#### Step 6: Yes/No Column

**Boolean Column:**

```
Display name: Is Approved by Manager
Data type: Yes/No

Default value:
○ No (default)
○ Yes

Display format:
● Checkbox
○ Toggle
○ Yes/No dropdown

Use for:
- Flags (Is Active, Is Approved, Is Manager)
- Status (Is Complete, Is Enabled)
- Permissions (Can Approve, Has Access)
```

**Yes/No Values:**

```
In Power Apps:
ThisItem.IsApproved = true or false

In flows:
@{triggerOutputs()?['body/IsApproved']} = true or false

In views:
Displays as: ✓ or blank
Or: Yes/No text
```

#### Step 7: Lookup Columns

**Basic Lookup:**

```
Display name: Employee
Data type: Lookup
Related table: Employee Profile
Required: Yes

Creates relationship:
- From: Leave Request (many)
- To: Employee Profile (one)
- Relationship type: Many-to-One (N:1)

In Leave Request record:
- Select employee from dropdown
- Stores: Employee Profile ID
- Displays: Employee name

Accessing related data in Power Apps:
ThisItem.Employee.Email
ThisItem.Employee.Department
```

**Customer Lookup:**

```
Display name: Customer
Data type: Lookup → Customer

Special lookup that can reference:
- Account (organization) OR
- Contact (person)

Use in:
- CRM scenarios
- When record could be company or person
- Sales, service cases
```

**Owner Lookup:**

```
Every table has Owner column (auto-created)

Owner can be:
- User OR
- Team

Access owner info:
ThisItem.Owner.Email (if User)
ThisItem.Owner.Name (if Team)

Used for:
- Record ownership
- Security (owner-based access)
- Assignment
```

#### Step 8: File and Image Columns

**File Column:**

```
Display name: Supporting Document
Data type: File
Max size: 128 MB (configurable, max 128 MB)

Allows:
- Single file attachment per row
- Any file type
- Direct upload in forms

Access in Power Apps:
ThisItem.SupportingDocument (returns file object)

Use for:
- Document uploads
- Attachments
- Images, PDFs, etc.
```

**Image Column:**

```
Display name: Profile Picture
Data type: Image
Max size: 30 MB
Primary image: Yes (displays as record thumbnail)

Allows:
- Single image per row
- PNG, JPG, GIF formats
- Shows in forms and views

Access in Power Apps:
Image1.Image = ThisItem.ProfilePicture
```

**Note Attachments (Alternative):**

```
Every table can have Notes (attachments):
Enable attachments when creating table

Allows:
- Multiple files per record
- Multiple notes per record
- Files + text notes

Access via:
- Notes section in forms
- Power Apps: Related notes
```

#### Step 9: Calculated Columns

**Create Calculated Column:**

```
Display name: Total Hours
Data type: Whole Number
Calculated: Yes (toggle on)

Formula:
TotalDays * 8

Dependencies:
- TotalDays (Whole Number column must exist)

Calculation:
- Performed server-side
- Updates automatically when TotalDays changes
- Read-only (can't manually edit)
- Calculated at record save
```

**Calculated Date Example:**

```
Display name: Expected Return Date
Data type: Date and Time
Format: Date Only
Calculated: Yes

Formula:
AddDays(StartDate, TotalDays)

Result: Automatically calculates return date based on start date + days
```

**Text Calculated Column:**

```
Display name: Full Name
Data type: Single line of text
Calculated: Yes

Formula:
FirstName & " " & LastName

Result: Automatically combines first and last name
```

**Calculated Column Functions:**

```
Available functions:
- Math: +, -, *, /, MOD, ROUND, ROUNDUP, ROUNDDOWN
- Text: &, LEFT, RIGHT, MID, LEN, UPPER, LOWER, TRIM
- Date: AddDays, AddWeeks, AddMonths, AddYears, Year, Month, Day
- Logical: IF, AND, OR, NOT
- Lookup: Related field values

Example with IF:
IF(TotalDays > 10, "Long Leave", "Short Leave")

Example with related data:
Employee.Department.Name (gets department name through lookup)
```

#### Step 10: Rollup Columns

**Create Rollup Column:**

```
Display name: Total Days Used This Year
Data type: Whole Number
Rollup: Yes (toggle on)

Rollup Configuration:

1. Related:
   Source Table: Leave Request (related table)

2. Relationship:
   Select relationship: Leave Requests (1:N relationship)

3. Aggregation:
   Function: SUM
   Aggregated Field: Total Days

4. Filters:
   Status equals Approved
   AND Start Date is in this year

Result: Automatically calculates total approved leave days for current year
```

**Rollup Functions:**

```
Available Aggregations:
- SUM: Total of values
- COUNT: Count of related records
- MIN: Minimum value
- MAX: Maximum value
- AVG: Average value

Example Use Cases:

Total Leave Days Used (Employee table):
- Related: Leave Requests
- Function: SUM
- Field: Total Days
- Filter: Status = Approved

Leave Request Count (Employee table):
- Related: Leave Requests
- Function: COUNT
- Filter: Status = Pending Approval

Average Leave Duration:
- Related: Leave Requests
- Function: AVG
- Field: Total Days
```

**Rollup Recalculation:**

```
Rollup columns update:
- Automatically every 1-12 hours (system job)
- On-demand via Calculate Rollup Field API
- When related records change (within refresh cycle)

⚠️ Not real-time, but near real-time

Force recalculation:
- Code: CalculateRollupField API
- Manual: System jobs
- Flow: Use Calculate Rollup Field action
```

### Assignment Deliverable

✅ **Create Leave Request table with columns:**

- Text: Leave Reason (single line)
- Text: Comments (multiple lines)
- Number: Total Days (whole number)
- Date: Start Date (date only)
- Date: End Date (date only)
- Choice: Leave Type (Annual, Sick, Personal, etc.)
- Choice: Status (Draft, Pending, Approved, Rejected)
- Yes/No: Manager Approved
- Lookup: Employee (to Employee Profile table)
- Calculated: Total Hours (Total Days \* 8)

✅ **Create Employee Profile table with columns:**

- Text: First Name
- Text: Last Name
- Text: Email (email format)
- Lookup: Department
- Lookup: Manager (self-lookup to Employee Profile)
- Rollup: Total Leave Days Used (from Leave Requests)
- Calculated: Full Name (First Name + Last Name)

✅ **Document data types:**

- Description of each type
- When to use each
- Calculated vs Rollup differences
- Best practices

### Knowledge Check

- [ ] What are the main data types?
- [ ] What's the difference between text formats?
- [ ] How do choice columns work?
- [ ] What's a global choice?
- [ ] What's a lookup column?
- [ ] What are calculated columns?
- [ ] What are rollup columns?
- [ ] What's the difference between calculated and rollup?
- [ ] How do file columns work?
- [ ] What are date behaviors?

### Resources

- [Microsoft Learn: Column data types](https://learn.microsoft.com/en-us/power-apps/maker/data-platform/types-of-fields)
- [Microsoft Learn: Calculated columns](https://learn.microsoft.com/en-us/power-apps/maker/data-platform/define-calculated-fields)
- [Microsoft Learn: Rollup columns](https://learn.microsoft.com/en-us/power-apps/maker/data-platform/define-rollup-fields)
- [Microsoft Learn: Choice columns](https://learn.microsoft.com/en-us/power-apps/maker/data-platform/create-edit-global-option-sets)

---

## Stage 5 Progress Summary

### Modules Completed:

- ✅ Module 33: Introduction to Dataverse (1 hour)
- ✅ Module 34: Dataverse Column Types (1.5 hours)

### Completed in Stage 5: 2.5 hours of 10 hours

### Remaining Stage 5 Modules:

- Module 35: Relationships (1.5 hours)
- Module 36: Forms and Views (1.5 hours)
- Module 37: Security Roles (1.5 hours)
- Module 38: Business Rules (1 hour)
- Module 39: Dataverse APIs (1.5 hours)
- Module 40: Data Import/Export (1.5 hours)

### Overall Progress: 109.5 hours of 170 hours (64.4%)

---

## Module 35: Dataverse Relationships (1.5 hours)

### Learning Objectives

- Understand relationship types (1:N, N:1, N:N)
- Create One-to-Many relationships
- Create Many-to-Many relationships
- Configure relationship behaviors
- Use relationship fields in apps
- Implement cascading actions
- Design proper data model

### Step-by-Step Instructions

#### Step 1: Understanding Relationships

**Relationship Types:**

```
1:N (One-to-Many)
Parent: Department (1)
Children: Employees (Many)
Example: One department has many employees

N:1 (Many-to-One)
Same as 1:N, viewed from child side
Children: Leave Requests (Many)
Parent: Employee (1)
Example: Many leave requests belong to one employee

N:N (Many-to-Many)
Table A: Projects (Many)
Table B: Employees (Many)
Example: Employees work on many projects,
         Projects have many employees
         (Requires intersect table)
```

**Real-World Examples:**

```
Leave Management System:

1:N Relationships:
- Employee → Leave Requests (one employee, many requests)
- Employee → Leave Balances (one employee, many balance records)
- Department → Employees (one department, many employees)
- Leave Type → Leave Requests (one type, many requests)

N:1 Relationships:
- Leave Request → Employee (many requests, one employee)
- Leave Request → Leave Type (many requests, one type)
- Employee → Manager (many employees, one manager - self-referencing)

N:N Relationships:
- Employees ↔ Skills (employees have many skills, skills belong to many employees)
- Employees ↔ Projects (employees work on many projects, projects have many employees)
```

#### Step 2: Create One-to-Many Relationship

**Scenario: Department → Employees**

```
Navigate to Relationship:
1. make.powerapps.com → Tables
2. Select: Department table
3. Relationships tab
4. + New relationship → One-to-many

Configuration:
Related (Child) table: Employee Profile
Lookup column:
  Display name: Department
  Name: cr123_department (auto-generated)
  Description: The department this employee belongs to
  Required: Business Required

Advanced options:
Type of behavior: Referential

Relationship name: cr123_department_employeeprofile_department

Save
```

**What This Creates:**

```
In Department table (Parent - 1 side):
- Relationship: Department → Employee Profiles
- Can access: Related employees
- Navigation: View related employees

In Employee Profile table (Child - N side):
- New lookup column: Department
- Can select: One department
- Displays: Department name
- Stores: Department ID (GUID)

In Power Apps:
Gallery of Employees:
  ThisItem.Department.Name  (access parent data)

Gallery of Departments:
  Filter(Employees, Department.ID = ThisItem.ID)  (access children)
```

#### Step 3: Relationship Behaviors

**Behavior Types:**

```
1. Referential (Most common)
   - Parent and child are loosely coupled
   - Deleting parent doesn't affect child
   - Child keeps reference (may become orphaned)

   Example: Delete department
   Result: Employees remain, Department field becomes null

2. Referential, Restrict Delete
   - Can't delete parent if children exist
   - Must remove/reassign children first

   Example: Try to delete department
   Result: Error - "Cannot delete department with employees"

3. Parental (Tight coupling)
   - Child is dependent on parent
   - Cascading actions apply

   Example: Delete department
   Result: All employees in that department also deleted

4. Custom
   - Configure each action individually
   - Maximum control
```

**Configure Behavior:**

```
When creating relationship:
Advanced options → Type of behavior:

○ Referential
  - Independent records
  - Minimal cascading

○ Referential, Restrict Delete
  - Prevent orphan situations
  - Must clean up children first

○ Parental
  - Strong dependency
  - Cascade Delete, Assign, Share, etc.

○ Custom
  - Specify each behavior
```

#### Step 4: Cascading Actions

**Cascade Options:**

```
When parent record is:

Assign (change owner):
- Cascade Active: Assign all active children to new owner
- Cascade All: Assign all children (active + inactive)
- Cascade None: Don't change child owners
- Cascade User Owned: Only user-owned children

Share:
- Cascade Active: Share all active children
- Cascade All: Share all children
- Cascade None: Don't share children
- Cascade User Owned: Only user-owned children

Unshare:
- Same options as Share

Reparent (change lookup):
- Cascade Active: Update all active children
- Cascade All: Update all children
- Cascade None: Leave children as-is
- Cascade User Owned: Only user-owned children

Delete:
- Cascade: Delete all children
- Remove Link: Set child lookup to null (orphan)
- Restrict: Prevent delete if children exist

Merge:
- Cascade: Update children to merged record
- None: Don't update children
```

**Example Configuration:**

```
Relationship: Department → Employees
Behavior: Parental

Actions:
Assign: Cascade All
  (Changing dept owner changes all employee owners)

Share: Cascade All
  (Sharing dept shares all employees)

Delete: Cascade
  (Deleting dept deletes all employees)
  ⚠️ Be careful! This is destructive

Reparent: Cascade All
  (Should not apply to this relationship)

Merge: Cascade
  (Merging depts updates employee references)
```

**Practical Example:**

```
Relationship: Employee → Leave Requests
Behavior: Parental

Why Parental?
- Leave requests belong to employee
- If employee deleted, requests should be deleted too
- If employee reassigned, requests go with them

Actions:
Delete: Cascade
  (Delete employee → delete all their leave requests)

Assign: Cascade All
  (Transfer employee → transfer all leave requests)

Share: Cascade All
  (Share employee record → share leave requests)
```

#### Step 5: Create Many-to-Many Relationship

**Scenario: Employees ↔ Skills**

```
Setup:
Table 1: Employee Profile
Table 2: Skills (create this table first)

Create N:N Relationship:
1. Tables → Employee Profile
2. Relationships tab
3. + New relationship → Many-to-many

Configuration:
Current table: Employee Profile
Related table: Skills

Relationship name: cr123_employeeprofile_skills

Advanced options:
Searchable: Yes
Relationship name: cr123_employeeprofile_skills

Save
```

**What This Creates:**

```
Intersect Table: cr123_employeeprofile_skills
(Created automatically, hidden)

Columns in intersect table:
- employeeprofileid (lookup to Employee Profile)
- skillid (lookup to Skills)
- Created On, Created By
- System columns

Example data in intersect table:
| Employee ID | Skill ID |
|-------------|----------|
| emp-001     | skill-01 | (John has Project Mgmt)
| emp-001     | skill-03 | (John has Data Analysis)
| emp-002     | skill-01 | (Sarah has Project Mgmt)
| emp-002     | skill-02 | (Sarah has Software Dev)

Result:
- John has skills: Project Management, Data Analysis
- Sarah has skills: Project Management, Software Development
- Project Management skill belongs to: John, Sarah
```

**Access N:N Data:**

```
In Power Apps:

Gallery of Employee Skills:
Items: ThisItem.Skills

Add skill to employee:
Relate(
    ThisEmployee.Skills,
    LookUp(Skills, Name = "Project Management")
)

Remove skill from employee:
Unrelate(
    ThisEmployee.Skills,
    LookUp(Skills, Name = "Data Analysis")
)

Check if employee has skill:
LookUp(ThisEmployee.Skills, Name = "Project Management") <> Blank()
```

#### Step 6: Self-Referencing Relationships

**Scenario: Employee → Manager**

```
Manager Relationship:
Parent table: Employee Profile
Child table: Employee Profile (same table!)

Create 1:N Relationship:
1. Tables → Employee Profile
2. Relationships → New → One-to-many

Configuration:
Related table: Employee Profile (same table)
Lookup column:
  Display name: Manager
  Name: cr123_manager
  Required: Optional

Result:
- Employee record can reference another Employee as Manager
- Hierarchical structure
- Lookup shows: List of all employees
```

**Hierarchical Queries:**

```
In Power Apps:

Get employee's manager:
ThisItem.Manager.FirstName

Get manager's manager (2 levels up):
ThisItem.Manager.Manager.FirstName

Get all direct reports:
Filter(
    Employees,
    Manager.ID = ThisEmployee.ID
)

Get entire team (recursive, complex):
Requires collection building with ForAll and recursion
```

**Example Data:**

```
| Employee Name | Manager      |
|---------------|--------------|
| CEO           | (null)       |
| VP Sales      | CEO          |
| VP IT         | CEO          |
| Sales Manager | VP Sales     |
| IT Manager    | VP IT        |
| Developer 1   | IT Manager   |
| Developer 2   | IT Manager   |
| Salesperson 1 | Sales Manager|

Relationships:
- CEO → manages VP Sales, VP IT (direct reports)
- VP IT → manages IT Manager
- IT Manager → manages Developer 1, Developer 2
```

#### Step 7: Relationship Design Best Practices

**Design Principles:**

```
✅ DO:
- Use 1:N for parent-child relationships
- Use N:N for many-to-many associations
- Set appropriate behavior (Referential vs Parental)
- Consider cascade implications carefully
- Use Restrict Delete to prevent orphans
- Name lookups clearly (Department, not Lookup1)
- Document relationships
- Test delete scenarios

❌ DON'T:
- Create circular relationships (A→B→C→A)
- Use N:N when 1:N is sufficient
- Set Cascade Delete without testing
- Create duplicate relationships
- Use generic names (Related Record, Lookup)
- Forget about cascade impacts
- Mix relationship types unnecessarily
```

**Common Patterns:**

```
Master-Detail (Parental):
Master: Invoice
Detail: Invoice Lines
Behavior: Parental, Cascade Delete
Reason: Lines don't exist without invoice

Reference (Referential):
Record: Employee
Reference: Department
Behavior: Referential, Restrict Delete
Reason: Employee can exist without dept, but don't delete dept with employees

Association (N:N):
Table A: Employees
Table B: Projects
Behavior: N:N
Reason: Independent entities with many-to-many relationship

Hierarchy (Self-referencing):
Table: Employee
Self-reference: Manager
Behavior: Referential
Reason: Organizational structure
```

#### Step 8: Leave Management Relationships

**Complete Data Model:**

```
Tables and Relationships:

1. Department (1) → Employees (N)
   Type: 1:N
   Behavior: Referential, Restrict Delete
   Cascade: Assign All, Share All
   Reason: Can't delete dept with employees

2. Employee (1) → Leave Requests (N)
   Type: 1:N
   Behavior: Parental
   Cascade: All actions cascade
   Reason: Leave requests belong to employee

3. Employee (1) → Leave Balances (N)
   Type: 1:N
   Behavior: Parental
   Cascade: Delete, Assign, Share
   Reason: Balances belong to employee

4. Leave Type (1) → Leave Requests (N)
   Type: 1:N
   Behavior: Referential, Restrict Delete
   Cascade: None
   Reason: Can't delete leave type in use

5. Leave Type (1) → Leave Balances (N)
   Type: 1:N
   Behavior: Referential, Restrict Delete
   Cascade: None
   Reason: Balance records reference type

6. Employee → Manager (Employee)
   Type: 1:N (Self-referencing)
   Behavior: Referential
   Cascade: None
   Reason: Manager hierarchy

7. Leave Request (1) → Approval History (N)
   Type: 1:N
   Behavior: Parental
   Cascade: Delete
   Reason: History belongs to request

Optional N:N:
8. Employees ↔ Skills
   Type: N:N
   Reason: Track employee competencies
```

**Implementation Order:**

```
Step 1: Create all tables first
- Department
- Employee Profile
- Leave Type
- Leave Request
- Leave Balance
- Approval History

Step 2: Create lookup columns (1:N relationships)
- Employee → Department
- Employee → Manager (self-reference)
- Leave Request → Employee
- Leave Request → Leave Type
- Leave Balance → Employee
- Leave Balance → Leave Type
- Approval History → Leave Request

Step 3: Create N:N relationships (if needed)
- Employees ↔ Skills

Step 4: Test relationships
- Create sample data
- Test lookups
- Test cascading
- Test delete restrictions
```

#### Step 9: Working with Relationships in Power Apps

**Display Related Data:**

```
Gallery: Leave Requests
Items: LeaveRequests

Labels in gallery:
Employee: ThisItem.Employee.FirstName & " " & ThisItem.Employee.LastName
Department: ThisItem.Employee.Department.Name
Leave Type: ThisItem.LeaveType.Name
Manager: ThisItem.Employee.Manager.FirstName

Accessing 2 levels deep:
ThisItem.Employee.Department.Name  (Leave Request → Employee → Department)
```

**Filter by Related Data:**

```
Show only IT department leave requests:
Filter(
    LeaveRequests,
    Employee.Department.Name = "IT"
)

Show leave requests for employees under specific manager:
Filter(
    LeaveRequests,
    Employee.Manager.ID = varManagerID
)

Show approved annual leave:
Filter(
    LeaveRequests,
    LeaveType.Name = "Annual" &&
    Status = 'Status (Leave Requests)'.Approved
)
```

**Create with Relationships:**

```
Submit new leave request:
Patch(
    LeaveRequests,
    Defaults(LeaveRequests),
    {
        Employee: LookUp(
            Employees,
            Email = User().Email
        ),
        LeaveType: ddLeaveType.Selected,
        StartDate: dtpStartDate.SelectedDate,
        EndDate: dtpEndDate.SelectedDate,
        TotalDays: varTotalDays,
        Status: 'Status (Leave Requests)'.Draft
    }
)

Result:
- Creates leave request
- Links to current user's employee record
- Links to selected leave type
- Relationships automatically maintained
```

**N:N Relationship Operations:**

```
View employee's skills:
Gallery Items: ThisItem.Skills

Add skill to employee:
Relate(
    varCurrentEmployee.Skills,
    LookUp(Skills, ID = galSkills.Selected.ID)
)

Remove skill:
Unrelate(
    varCurrentEmployee.Skills,
    galEmployeeSkills.Selected
)

Check if has skill:
!IsBlank(
    LookUp(
        varCurrentEmployee.Skills,
        Name = "Project Management"
    )
)
```

#### Step 10: Troubleshooting Relationships

**Common Issues:**

```
Issue 1: Can't create relationship
Cause: Tables don't exist or not in same environment
Solution:
- Ensure both tables created
- Verify correct environment
- Check table permissions

Issue 2: Lookup shows empty dropdown
Cause: No records in parent table
Solution:
- Add sample data to parent table
- Refresh data source in Power Apps

Issue 3: Can't delete record
Error: "Cannot delete record, related records exist"
Cause: Restrict Delete behavior
Solution:
- Delete or reassign child records first
- Change relationship behavior to Cascade or Remove Link

Issue 4: Cascade delete removed too much data
Cause: Parental behavior with Cascade Delete
Solution:
- No undo! Restore from backup
- Change to Referential behavior
- Use Restrict Delete instead

Issue 5: Related data not showing in Power Apps
Cause: Data source not added or relationship not recognized
Solution:
- Add both tables to app data sources
- Refresh data sources
- Check relationship exists in Dataverse
- Use explicit Filter() if needed
```

**Testing Checklist:**

```
✅ Test Relationship Creation:
- [ ] Lookup column created in child table
- [ ] Dropdown shows parent records
- [ ] Can select parent record
- [ ] Saves successfully

✅ Test Navigation:
- [ ] Can view related records from parent
- [ ] Can view parent from child
- [ ] Counts accurate (1 parent, N children)

✅ Test Cascading:
- [ ] Share parent → children shared
- [ ] Assign parent → children assigned
- [ ] Delete parent → correct cascade behavior

✅ Test Restrictions:
- [ ] Can't delete parent with children (if Restrict)
- [ ] Can delete parent if no children

✅ Test in Power Apps:
- [ ] Lookup displays parent name
- [ ] Can access parent fields (ThisItem.Parent.Field)
- [ ] Filter by parent works
- [ ] Patch creates correct relationships
```

### Assignment Deliverable

✅ **Create Leave Management relationships:**

- Department → Employees (1:N, Referential Restrict)
- Employee → Leave Requests (1:N, Parental)
- Leave Type → Leave Requests (1:N, Referential Restrict)
- Employee → Manager (1:N Self-reference)
- Leave Request → Approval History (1:N, Parental)
- Test all relationships

✅ **Create N:N relationship:**

- Employees ↔ Skills
- Add sample data
- Test Relate/Unrelate in Power Apps

✅ **Document data model:**

- ERD (Entity Relationship Diagram)
- All tables and relationships
- Relationship types and behaviors
- Cascade configurations
- Lookup column names

### Knowledge Check

- [ ] What are the 3 relationship types?
- [ ] What's the difference between 1:N and N:1?
- [ ] What relationship behaviors exist?
- [ ] What does Cascade Delete do?
- [ ] What's Restrict Delete?
- [ ] How do you create N:N relationships?
- [ ] What's a self-referencing relationship?
- [ ] How do you access related data in Power Apps?
- [ ] What's an intersect table?
- [ ] When should you use Parental vs Referential?

### Resources

- [Microsoft Learn: Create relationships](https://learn.microsoft.com/en-us/power-apps/maker/data-platform/data-platform-entity-lookup)
- [Microsoft Learn: Relationship behavior](https://learn.microsoft.com/en-us/power-apps/maker/data-platform/create-edit-entity-relationships)
- [Microsoft Learn: N:N relationships](https://learn.microsoft.com/en-us/power-apps/maker/data-platform/create-edit-nn-relationships)

---

## Module 36: Forms and Views (1.5 hours)

### Learning Objectives

- Understand forms vs views
- Create and customize main forms
- Create quick view forms
- Create and customize views
- Configure form layout and sections
- Add business rules to forms
- Use form scripts (overview)

### Step-by-Step Instructions

#### Step 1: Understanding Forms and Views

**Definitions:**

```
Form:
- Interface to view/edit a single record
- Contains fields, sections, tabs
- Used in: Model-Driven Apps, Dataverse
- Types: Main, Quick Create, Quick View, Card

View:
- List of records from a table
- Contains columns, filters, sorting
- Used in: Model-Driven Apps, Lookups
- Types: Public, Personal, System
```

**Forms vs Views:**

```
FORM (Single Record):
┌─────────────────────────────┐
│ Leave Request - Main Form   │
├─────────────────────────────┤
│ Employee: John Smith        │
│ Leave Type: Annual          │
│ Start Date: Nov 20, 2025    │
│ End Date: Nov 25, 2025      │
│ Total Days: 5               │
│ Status: Pending Approval    │
│ Comments: [text box]        │
│                             │
│ [Save] [Save & Close]       │
└─────────────────────────────┘

VIEW (Multiple Records):
┌──────────────────────────────────────────────────────┐
│ Active Leave Requests                                │
├────────┬────────────┬───────────┬────────┬──────────┤
│Employee│ Leave Type │ Start Date│ Days   │ Status   │
├────────┼────────────┼───────────┼────────┼──────────┤
│John    │ Annual     │ Nov 20    │ 5      │ Pending  │
│Sarah   │ Sick       │ Nov 22    │ 2      │ Approved │
│Mike    │ Personal   │ Nov 25    │ 3      │ Draft    │
└────────┴────────────┴───────────┴────────┴──────────┘
```

#### Step 2: Create and Edit Main Form

**Navigate to Forms:**

```
1. make.powerapps.com → Tables
2. Select: Leave Request table
3. Forms tab
4. Select: Information (main form)
   Or: + New form → Main form
```

**Form Designer:**

```
Left Panel: Components
- Sections
- Tabs
- Columns
- Sub-grids
- Quick view forms
- Components (custom)

Center: Form Canvas
- Drag and drop fields
- Arrange layout
- Configure properties

Right Panel: Properties
- Field settings
- Section settings
- Form settings
- Business rules
```

**Add Fields to Form:**

```
Method 1: Drag from Table Columns
1. Left panel → Table columns
2. Drag field to form
3. Drop in desired section

Method 2: Add from Section
1. Click section
2. + Insert → Column
3. Select field from dropdown
4. Configure properties

Example: Add Employee field
1. Drag "Employee" from table columns
2. Drop into "General" section
3. Properties panel opens:
   - Label: Employee
   - Required: Yes
   - Visible: Yes
   - Locked: No
   - Hide label: No
```

#### Step 3: Form Layout and Sections

**Create Sections:**

```
Add Section:
1. Click "1-column section" or "2-column section" in components
2. Drag to form
3. Configure section properties:
   - Name: Leave Details
   - Label: Leave Details
   - Show label: Yes
   - Visible: Yes
   - Available on phone: Yes

Section Layouts:
- 1 column: Full width fields
- 2 columns: Side-by-side fields
- 3 columns: Three fields across
- 4 columns: Four fields across (uncommon)
```

**Create Tabs:**

```
Add Tab:
1. Components → Tab
2. Drag to form
3. Configure:
   - Name: Details
   - Label: Details
   - Show label: Yes
   - Expanded by default: Yes

Tab contains multiple sections
Each section contains fields

Example Structure:
Tab: General
  Section: Basic Information
    - Employee
    - Leave Type
    - Start Date
    - End Date
  Section: Additional Details
    - Total Days
    - Comments

Tab: Approval
  Section: Approval Information
    - Status
    - Approved By
    - Approval Date
  Section: Approval History
    - Sub-grid: Approval History records
```

**Example Form Layout:**

```
Leave Request Main Form

┌─────────────────────────────────────────┐
│ [General] [Approval] [Related]          │ ← Tabs
├─────────────────────────────────────────┤
│ Basic Information                       │ ← Section
│ ┌─────────────────┬─────────────────┐  │
│ │ Employee*       │ Leave Type*     │  │ ← 2-column
│ │ [John Smith ▼]  │ [Annual ▼]      │  │
│ └─────────────────┴─────────────────┘  │
│ ┌─────────────────┬─────────────────┐  │
│ │ Start Date*     │ End Date*       │  │
│ │ [Nov 20, 2025]  │ [Nov 25, 2025]  │  │
│ └─────────────────┴─────────────────┘  │
│                                         │
│ Additional Details                      │ ← Section
│ ┌─────────────────┐                    │
│ │ Total Days      │                    │ ← 1-column
│ │ 5               │                    │
│ └─────────────────┘                    │
│ ┌───────────────────────────────────┐  │
│ │ Comments                          │  │
│ │ [Multi-line text box]             │  │
│ │                                   │  │
│ └───────────────────────────────────┘  │
└─────────────────────────────────────────┘
```

#### Step 4: Field Properties

**Configure Field Properties:**

```
Select field on form → Properties panel

General:
- Label: Display name (can override table column)
- Hide label: Yes/No
- Visible: Yes/No (can hide based on business rule)
- Locked: Yes/No (read-only)
- Required:
  - Optional
  - Business Required (red asterisk)
  - Business Recommended (blue plus)

Formatting:
- Field width: % of column width
- Field height: Number of lines (for multi-line text)

Controls:
- Default control (standard input)
- Alternative controls (custom controls)
  - Lookup: Dropdown, Radio buttons
  - Date: Calendar picker, Date picker
  - Number: Slider, Toggle, etc.
```

**Example: Configure Start Date:**

```
Field: Start Date
Properties:
- Label: Leave Start Date
- Required: Business Required
- Visible: Yes
- Locked: No
- Control: Date Picker Calendar
- Default to Today: Yes (via business rule)
```

#### Step 5: Quick Create Forms

**Purpose:**

```
Quick Create Form:
- Simplified form for fast data entry
- Opens in dialog/flyout
- Only essential fields
- Used in:
  - Create from lookup
  - Quick create button
  - Sub-grid "+New" button
```

**Create Quick Create Form:**

```
1. Tables → Leave Request → Forms
2. + New form → Quick Create form
3. Name: Quick Create - Leave Request

Add only essential fields:
- Employee (lookup)
- Leave Type (choice)
- Start Date (date)
- End Date (date)
- Total Days (calculated or input)

Properties:
- Keep it simple (< 10 fields)
- No tabs (single page)
- 1-2 sections maximum
- Required fields only
```

**Enable Quick Create:**

```
Table Settings:
1. Tables → Leave Request → Properties
2. Collaboration section
3. ✅ Enable quick create forms
4. Save

Now available:
- From lookup controls ("+" icon)
- From sub-grids
- From command bar
```

#### Step 6: Quick View Forms

**Purpose:**

```
Quick View Form:
- Read-only display of related record
- Embedded in parent form
- Shows key fields from related table
- No scrolling, always visible

Use Case:
On Leave Request form, show Employee details:
- Employee Name
- Department
- Manager
- Email
Without leaving the Leave Request form
```

**Create Quick View Form:**

```
Create form on related table:
1. Tables → Employee Profile → Forms
2. + New form → Quick view form
3. Name: Employee Quick View

Add fields (read-only):
- First Name
- Last Name
- Email
- Department
- Manager
- Phone

Layout: 2 columns, single section
Max fields: 10 (keep it concise)

Save and Publish
```

**Add Quick View to Parent Form:**

```
On Leave Request Main Form:
1. Form designer
2. Components → Quick view
3. Drag to form
4. Configure:
   - Lookup: Employee (the lookup field)
   - Quick view form: Employee Quick View
   - Label: Employee Information

Result: Shows employee details automatically
when Employee lookup is selected
```

#### Step 7: Create and Customize Views

**Navigate to Views:**

```
1. make.powerapps.com → Tables
2. Select: Leave Request
3. Views tab

Default views exist:
- Active Leave Requests
- Inactive Leave Requests
- Quick Find Active Leave Requests
- Leave Request Advanced Find View
- Leave Request Associated View
- Leave Request Lookup View
```

**Create Custom View:**

```
1. Views tab → + New view
2. Name: Pending Approval View
3. View type: Public (visible to all users)

Add Columns:
Click "+ Add column"
Select columns:
- Employee (lookup - shows name)
- Leave Type
- Start Date
- End Date
- Total Days
- Status
- Submitted Date

Reorder columns:
Drag and drop to arrange

Column width:
Click column header → Adjust width
```

**Configure View Filters:**

```
Edit Filter:
1. Click "Edit filters"
2. Add condition:
   Status equals Pending Approval

Multiple conditions:
Status equals Pending Approval
AND
Start Date is today or after

Advanced filters:
Status equals Pending Approval
OR
Status equals Pending Manager Approval
AND
Start Date is in the next 30 days

Filter groups:
(Status = Pending Approval OR Status = Approved)
AND
Employee.Department = IT
```

**Configure View Sorting:**

```
Sort Order:
1. Click "Sort by"
2. Primary sort: Start Date (Ascending)
3. Secondary sort: Employee (Ascending)

Result: Shows earliest dates first,
alphabetical within same date

Default sort in view definition
Users can re-sort in UI
```

#### Step 8: View Types and Uses

**View Types:**

```
Public Views:
- Created by system admin/customizer
- Visible to all users (with permissions)
- Shared across organization
- Examples: Active Records, My Records, Pending Approvals

Personal Views:
- Created by individual users
- Visible only to creator
- Saved in user's profile
- Can be shared with specific users

System Views:
- Built-in, used by system
- Lookup View: Shows in lookup dialogs
- Quick Find View: Used in global search
- Associated View: Shows related records
- Advanced Find View: Template for ad-hoc queries

Custom Views:
- Any public view you create
- Can be set as default view
- Used in Model-Driven Apps
```

**Special Views:**

```
Lookup View:
- Defines what shows in lookup dropdown
- Columns: Name, Department, Manager
- Filter: Active employees only
- Sorting: Alphabetical by name

Quick Find View:
- Searchable columns
- Used in global search bar
- Add fields users will search by:
  - Name, Email, Employee ID
- Enables "Find" functionality

Associated View:
- Shows related records
- Example: On Department form,
  associated view shows employees
- Defines columns for related grid
```

#### Step 9: Sub-grids on Forms

**Purpose:**

```
Sub-grid:
- Shows related records on a form
- Embedded list view
- Can add/edit related records
- Example: On Employee form, show Leave Requests
```

**Add Sub-grid:**

```
On Employee Profile Main Form:
1. Form designer
2. Components → Sub-grid
3. Drag to form section
4. Configure:

Name: LeaveRequestsSubgrid
Label: Leave Requests

Show related records:
- Table: Leave Requests
- Default view: Active Leave Requests
- Relationship: Employee → Leave Requests

Display options:
✅ Show related records
Show these columns:
  - Leave Type
  - Start Date
  - End Date
  - Total Days
  - Status
✅ Show chart
Chart: Leave Requests by Type

Records per page: 10
Allow users to change view: Yes

Result: Shows all leave requests for this employee
Users can add new leave request from sub-grid
```

**Sub-grid Actions:**

```
Users can:
- View list of related records
- Click record to open form
- Add new related record (+ New button)
- Search within sub-grid
- Change view (dropdown)
- Export to Excel
- Refresh grid
```

#### Step 10: Form Scripts (Introduction)

**What are Form Scripts?**

```
Form Scripts = JavaScript code to add custom behavior

Common uses:
- Field validation
- Dynamic field visibility
- Auto-populate fields
- Call external APIs
- Complex calculations
- Conditional logic

⚠️ Requires JavaScript knowledge
⚠️ Use Business Rules first when possible
⚠️ Form scripts covered more in Model-Driven Apps module
```

**Simple Example (Conceptual):**

```javascript
// When Leave Type changes, set default days
function onLeaveTypeChange(executionContext) {
  var formContext = executionContext.getFormContext();
  var leaveType = formContext.getAttribute("cr123_leavetype").getValue();

  if (leaveType !== null) {
    var leaveTypeName = leaveType[0].name;

    if (leaveTypeName === "Annual") {
      formContext.getAttribute("cr123_totaldays").setValue(5);
    } else if (leaveTypeName === "Sick") {
      formContext.getAttribute("cr123_totaldays").setValue(2);
    }
  }
}

// Register on field change
formContext.getAttribute("cr123_leavetype").addOnChange(onLeaveTypeChange);
```

**When to Use Scripts vs Business Rules:**

```
Use Business Rules when:
✅ Show/hide fields
✅ Set field values
✅ Set required level
✅ Simple validation
✅ No coding knowledge needed

Use Form Scripts when:
✅ Complex calculations
✅ Call external APIs
✅ Dynamic form behavior
✅ Advanced validation
✅ Integration with other systems
```

### Assignment Deliverable

✅ **Create Leave Request forms:**

- Main form with tabs and sections
  - General tab (basic info)
  - Approval tab (approval details)
  - Related tab (sub-grids)
- Quick Create form (simplified)
- Quick View form (for Employee)

✅ **Create views:**

- Pending Approval View (filtered)
- My Leave Requests View (personal)
- Leave Requests by Department
- This Month Leave Requests (date filtered)

✅ **Add sub-grids:**

- On Employee form: Leave Requests sub-grid
- On Department form: Employees sub-grid

✅ **Document form/view design:**

- Form layouts (screenshots)
- Field placements
- Tab structure
- View definitions
- Filter criteria

### Knowledge Check

- [ ] What's the difference between forms and views?
- [ ] What are form tabs and sections?
- [ ] What's a Quick Create form?
- [ ] What's a Quick View form?
- [ ] How do you create a custom view?
- [ ] How do you filter a view?
- [ ] What's a sub-grid?
- [ ] What are view types?
- [ ] What's a lookup view?
- [ ] When should you use form scripts?

### Resources

- [Microsoft Learn: Create forms](https://learn.microsoft.com/en-us/power-apps/maker/model-driven-apps/create-design-forms)
- [Microsoft Learn: Create views](https://learn.microsoft.com/en-us/power-apps/maker/model-driven-apps/create-edit-views)
- [Microsoft Learn: Quick view forms](https://learn.microsoft.com/en-us/power-apps/maker/model-driven-apps/create-edit-quick-view-forms)
- [Microsoft Learn: Sub-grids](https://learn.microsoft.com/en-us/power-apps/maker/model-driven-apps/form-designer-add-configure-subgrid)

---

## Stage 5 Progress Update

### Modules Completed:

- ✅ Module 33: Introduction to Dataverse (1 hour)
- ✅ Module 34: Dataverse Column Types (1.5 hours)
- ✅ Module 35: Dataverse Relationships (1.5 hours)
- ✅ Module 36: Forms and Views (1.5 hours)

### Completed in Stage 5: 5.5 hours of 10 hours

### Remaining Stage 5 Modules:

- Module 37: Security Roles (1.5 hours)
- Module 38: Business Rules (1 hour)
- Module 39: Dataverse APIs (1.5 hours)
- Module 40: Data Import/Export (1.5 hours)

### Overall Progress: 113 hours of 170 hours (66.5%)

---

## Module 37: Security Roles and Permissions (1.5 hours)

### Learning Objectives

- Understand Dataverse security model
- Create and configure security roles
- Set table and field level permissions
- Implement row-level security
- Assign security roles to users
- Test security configurations
- Understand business units and teams

### Step-by-Step Instructions

#### Step 1: Dataverse Security Model Overview

**Security Layers:**

```
1. Environment Security
   - Who can access environment
   - Managed in Power Platform Admin Center
   - User must be added to environment

2. Security Roles
   - What users can do with data
   - Table-level permissions (CRUD)
   - Field-level permissions
   - Access levels (Organization, Business Unit, User)

3. Row-Level Security
   - Which specific records users can access
   - Based on ownership, team membership, sharing
   - Hierarchical security

4. Field-Level Security
   - Which fields users can read/write
   - Secure sensitive fields (SSN, Salary)
   - Independent of table permissions

5. Column Security Profiles
   - Group field permissions
   - Assign to users/teams
   - For field-level security
```

**Security Model Hierarchy:**

```
Organization (Tenant)
├── Environment
│   ├── Business Unit (Root)
│   │   ├── Child Business Unit
│   │   │   └── Users
│   │   └── Users
│   ├── Security Roles
│   │   ├── System Administrator
│   │   ├── Basic User
│   │   └── Custom Roles
│   └── Teams
│       ├── Owner Teams
│       └── Access Teams
```

#### Step 2: Understanding Privileges

**CRUD Privileges:**

```
Create: Add new records
Read: View records
Write: Edit records
Delete: Remove records
Append: Attach related records to this record
Append To: Allow this record to be attached to others
Assign: Change record owner
Share: Share record with others

Example: Leave Request Table
- Create: Submit new leave request
- Read: View leave requests
- Write: Edit leave requests
- Delete: Remove leave requests
- Assign: Transfer ownership
- Share: Share with team members
```

**Access Levels:**

```
None: No access
User (Basic): Only own records
Business Unit: Records owned by users in same business unit
Parent: Child Business Units: Records in BU and child BUs
Organization: All records in organization

Visual Representation:

None: ⭘ (empty circle)
User: ⚫ (filled circle)
Business Unit: ◐ (half filled)
Parent: Child: ◔ (three-quarters)
Organization: ⬤ (full circle)
```

**Access Level Examples:**

```
Scenario: Leave Requests

User Level:
- John can only see his own leave requests
- Can't see Sarah's or Mike's requests

Business Unit Level:
- IT Department manager
- Can see all leave requests from IT department
- Can't see HR or Sales department requests

Organization Level:
- HR Administrator
- Can see all leave requests from all departments
- Full visibility across organization
```

#### Step 3: Create Custom Security Role

**Navigate to Security Roles:**

```
Method 1: Power Platform Admin Center
1. admin.powerplatform.microsoft.com
2. Environments → Select environment
3. Settings → Users + permissions → Security roles
4. + New role

Method 2: Power Apps
1. make.powerapps.com
2. Settings (gear icon) → Advanced settings
3. Settings → Security → Security Roles
4. + New

Method 3: Solutions (Recommended)
1. make.powerapps.com → Solutions
2. Select solution
3. Add new → Security → Security role
```

**Create Leave Employee Role:**

```
Name: Leave Employee
Description: Standard employee - can manage own leave requests

Business Management Tab:
(Not used for Leave Management)

Core Records Tab:
- Leave Request:
  Create: User
  Read: User
  Write: User
  Delete: User
  Append: User
  Append To: User

- Leave Balance:
  Create: None
  Read: User
  Write: None
  Delete: None

- Employee Profile:
  Create: None
  Read: Business Unit
  Write: User (own record only)
  Delete: None

Customization Tab:
(Usually None for end users)

Custom Entities Tab:
- Your custom tables appear here
- Set permissions as above

Privacy Related Privileges:
(Configure based on requirements)
```

**Create Leave Manager Role:**

```
Name: Leave Manager
Description: Manager - can approve team leave requests

Copy from: Leave Employee (start with employee permissions)

Modifications:
- Leave Request:
  Create: Business Unit (can create for team)
  Read: Business Unit (see team requests)
  Write: Business Unit (edit team requests)
  Delete: Business Unit (remove team requests)
  Append: Business Unit
  Append To: Business Unit
  Assign: Business Unit (reassign requests)

- Leave Balance:
  Create: None
  Read: Business Unit (view team balances)
  Write: None
  Delete: None

- Approval History:
  Create: Business Unit
  Read: Business Unit
  Write: Business Unit
  Delete: None
```

**Create Leave Administrator Role:**

```
Name: Leave Administrator
Description: HR Admin - full leave management access

All Leave Management Tables:
  Create: Organization
  Read: Organization
  Write: Organization
  Delete: Organization
  Append: Organization
  Append To: Organization
  Assign: Organization
  Share: Organization

Plus system privileges:
- Bulk Delete
- Export to Excel
- Mail Merge
- Print
```

#### Step 4: Field-Level Security

**Secure Sensitive Fields:**

```
Scenario: Employee Profile has Salary field
Requirement: Only HR can see/edit salary

Enable Field Security:
1. Tables → Employee Profile → Columns
2. Select: Salary column
3. Edit column
4. Advanced options
5. ✅ Enable security
6. Save

Result: Field now secured, requires additional permissions
```

**Create Field Security Profile:**

```
1. Settings → Security → Field Security Profiles
2. + New
3. Name: HR Salary Access
4. Save

5. Add Field Permissions:
   - Click "Field Permissions"
   - Add → Employee Profile: Salary
   - Permissions:
     ✅ Allow Read
     ✅ Allow Update
     ✅ Allow Create
   - Save

6. Add Users/Teams:
   - Members tab
   - Add users/teams
   - Add: HR Team
   - Save

Result: Only HR team members can see/edit Salary field
Others see: ****** or field hidden
```

**Field-Level Security Use Cases:**

```
Common Secured Fields:
- Social Security Number
- Salary
- Bank Account Numbers
- Performance Ratings
- Confidential Notes
- Emergency Contact Info (sometimes)

Benefits:
- Fine-grained security
- Compliance (GDPR, HIPAA)
- Data privacy
- Audit trails
```

#### Step 5: Assign Security Roles to Users

**Assign Role:**

```
Method 1: From User Record
1. Settings → Security → Users
2. Select user
3. Manage Roles button
4. Check: Leave Employee
5. Save

Method 2: From Security Role
1. Settings → Security → Security Roles
2. Select: Leave Employee
3. Users tab
4. + Add Users
5. Select users
6. Add

Method 3: Through Teams
1. Create Team: All Employees
2. Add users to team
3. Assign security role to team
4. All team members inherit role
```

**Multiple Roles:**

```
Users can have multiple roles:

Example: Jane is a manager and employee
Roles assigned:
- Leave Employee (base access)
- Leave Manager (manager access)

Result: Most permissive access wins
- Can see own requests (Employee role)
- Can see team requests (Manager role)
- Can approve team requests (Manager role)

Effective permissions = Union of all roles
```

**Remove Role:**

```
1. Settings → Security → Users
2. Select user
3. Manage Roles
4. Uncheck role to remove
5. Save

⚠️ Warning: Removing all roles = no access to Dataverse
Always assign at least one role (e.g., Basic User)
```

#### Step 6: Business Units

**What are Business Units?**

```
Business Unit:
- Organizational security boundary
- Hierarchical structure (parent-child)
- Users belong to one business unit
- Used for security scoping

Example Structure:
Root Business Unit (Contoso)
├── Sales
│   ├── East Coast Sales
│   └── West Coast Sales
├── IT
│   ├── Development
│   └── Support
└── HR
    ├── Recruitment
    └── Payroll

Each BU can have different security scope
```

**Create Business Unit:**

```
1. Settings → Security → Business Units
2. + New
3. Name: IT Department
4. Parent Business Unit: Root Business Unit
5. Save

Result: New business unit created
Can assign users to this BU
```

**Assign Users to Business Unit:**

```
1. Settings → Security → Users
2. Select user
3. Business Unit field
4. Change to: IT Department
5. Save

User now belongs to IT Department BU
Security roles apply within this BU scope
```

**Business Unit Permissions:**

```
Security Role with BU-level Read:
- User in IT BU can read IT records
- Can't read Sales or HR records
- Keeps data segregated by department

Useful for:
- Multi-division companies
- Department-specific data
- Regional offices
- Compliance requirements
```

#### Step 7: Teams

**Team Types:**

```
Owner Team:
- Can own records
- Members inherit team's security roles
- Used for: Departments, workgroups
- Example: HR Team owns all HR records

Access Team:
- Can't own records
- Used for sharing specific records
- Dynamic membership
- Example: Project team needs access to specific records
```

**Create Owner Team:**

```
1. Settings → Security → Teams
2. + New
3. Name: Leave Approvers
4. Team Type: Owner
5. Business Unit: Root
6. Administrator: (your user)
7. Save

8. Add Members:
   - Members tab
   - + Add Users
   - Select managers
   - Add

9. Manage Roles:
   - Click "Manage Roles"
   - Check: Leave Manager
   - Save

Result: All team members can approve leaves
```

**Share Record with Team:**

```
In Power Apps or Model-Driven App:
1. Open leave request record
2. Share button
3. Add: Leave Approvers team
4. Permissions: Read, Write
5. Share

Result: All team members can access this record
```

#### Step 8: Hierarchical Security

**Manager Hierarchy:**

```
Hierarchical Security:
- Managers can access subordinates' records
- Based on Manager field in User table
- Cascades down hierarchy

Setup:
1. Settings → Security → Hierarchy Security
2. Enable for specific tables
3. Configure depth (levels down)

Example:
CEO
├── VP Sales (can see VP records + below)
│   └── Sales Manager (can see Manager records + below)
│       └── Salesperson (can see own records)

VP Sales can see:
- Own records
- Sales Manager records
- Salesperson records
```

**Configure Hierarchy:**

```
Enable Hierarchy Security:
1. Settings → Security → Hierarchy Security
2. + New
3. Name: Manager Hierarchy
4. Hierarchy Type: Manager hierarchy
5. Depth: 3 levels (or unlimited)
6. Tables: Leave Request, Leave Balance
7. Save

Result: Managers automatically see subordinates' data
Based on Manager lookup in Employee Profile
```

#### Step 9: Testing Security

**Test as Different User:**

```
Method 1: Impersonate (Admin only)
1. Model-Driven App
2. Settings → Advanced Settings
3. System → Security → Users
4. Select user
5. "Impersonate" button
6. Test security
7. Exit impersonation

Method 2: Test Environment
1. Create test users with different roles
2. Sign in as each user
3. Verify access:
   - Can create records?
   - Can read own records?
   - Can read others' records?
   - Can edit/delete?
   - Can see sensitive fields?
```

**Security Testing Checklist:**

```
✅ Test Leave Employee Role:
- [ ] Can create own leave request
- [ ] Can read own leave requests
- [ ] Can edit own draft requests
- [ ] Can't read others' requests
- [ ] Can't delete approved requests
- [ ] Can view own leave balance
- [ ] Can't edit leave balance

✅ Test Leave Manager Role:
- [ ] Can create leave requests
- [ ] Can read team leave requests
- [ ] Can approve team requests
- [ ] Can't read other departments' requests
- [ ] Can view team leave balances
- [ ] Can reassign team requests

✅ Test Leave Administrator Role:
- [ ] Can read all leave requests
- [ ] Can edit any leave request
- [ ] Can delete any leave request
- [ ] Can view all leave balances
- [ ] Can adjust leave balances
- [ ] Can see reports across organization

✅ Test Field-Level Security:
- [ ] HR can see Salary field
- [ ] Non-HR can't see Salary field
- [ ] Field shows ****** or hidden
- [ ] Can't edit secured field without permission
```

#### Step 10: Common Security Scenarios

**Scenario 1: Department-Based Access**

```
Requirement: Each department manages own data

Setup:
1. Create Business Units per department:
   - IT BU
   - HR BU
   - Sales BU

2. Assign users to respective BUs

3. Security Role permissions:
   Read: Business Unit level
   Write: Business Unit level

Result: IT users only see IT data
HR users only see HR data
```

**Scenario 2: Manager Approval Workflow**

```
Requirement: Managers approve team leave

Setup:
1. Employee Profile has Manager lookup

2. Security Role: Leave Manager
   - Read: Business Unit
   - Write: User + Manual approval logic in Flow

3. Flow checks:
   - Is current user = employee's manager?
   - If yes, allow approval

Result: Only direct manager can approve
```

**Scenario 3: HR Full Access**

```
Requirement: HR team has full access

Setup:
1. Create Team: HR Team
2. Create Role: Leave Administrator
   - All permissions: Organization level
3. Assign role to HR Team
4. Add HR users to team

Result: HR can manage all leave data
```

**Scenario 4: Read-Only Reporting**

```
Requirement: Executives view reports, can't edit

Setup:
1. Security Role: Leave Report Viewer
   - Read: Organization
   - Write: None
   - Delete: None
   - Create: None

2. Assign to executives

Result: Can view all data, generate reports
Can't modify any records
```

### Assignment Deliverable

✅ **Create three security roles:**

- Leave Employee (basic access)
- Leave Manager (team access)
- Leave Administrator (full access)
- Document permissions for each

✅ **Configure field-level security:**

- Secure one sensitive field
- Create field security profile
- Assign to specific users/team
- Test access

✅ **Create and configure teams:**

- Create "Leave Approvers" owner team
- Add members
- Assign security role
- Test team-based access

✅ **Test security configuration:**

- Create test users
- Assign different roles
- Test CRUD operations
- Verify access levels
- Document test results

✅ **Security documentation:**

- Security model diagram
- Role definitions and permissions
- Business unit structure
- Team assignments
- Field security setup

### Knowledge Check

- [ ] What are the security layers in Dataverse?
- [ ] What are CRUD privileges?
- [ ] What are access levels (User, BU, Org)?
- [ ] How do you create a security role?
- [ ] What's field-level security?
- [ ] What's the difference between Owner and Access teams?
- [ ] What's hierarchical security?
- [ ] How do you assign roles to users?
- [ ] What happens with multiple roles?
- [ ] How do you test security?

### Resources

- [Microsoft Learn: Security concepts](https://learn.microsoft.com/en-us/power-platform/admin/wp-security-cds)
- [Microsoft Learn: Security roles](https://learn.microsoft.com/en-us/power-platform/admin/security-roles-privileges)
- [Microsoft Learn: Field security](https://learn.microsoft.com/en-us/power-platform/admin/field-level-security)
- [Microsoft Learn: Hierarchical security](https://learn.microsoft.com/en-us/power-platform/admin/hierarchy-security)

---

## Module 38: Business Rules (1 hour)

### Learning Objectives

- Understand business rules
- Create table-level business rules
- Implement field validation
- Set required fields dynamically
- Show/hide fields based on conditions
- Set default values
- Create error messages
- Business rules vs Power Automate

### Step-by-Step Instructions

#### Step 1: What are Business Rules?

**Definition:**

```
Business Rule:
- No-code logic for forms
- Runs client-side (in browser)
- Real-time validation and behavior
- Works in Model-Driven Apps and Canvas Apps (Dataverse)

Common Uses:
✅ Show/hide fields
✅ Set field required level
✅ Set default values
✅ Validate field values
✅ Show error/recommendation messages
✅ Lock/unlock fields
✅ Create business recommendations

Business Rules vs Flows:
Business Rule: Client-side, instant, form-focused
Flow: Server-side, automated processes, multi-table
```

**When to Use Business Rules:**

```
✅ Use Business Rules for:
- Form field visibility
- Field validation (instant feedback)
- Required field logic
- Default values
- Calculated values (simple)
- Form-based logic
- Client-side performance

❌ Don't use Business Rules for:
- Complex calculations
- Multiple table updates
- External API calls
- Email notifications
- Scheduled processes
- Background processing
- Workflow automation

→ Use Power Automate instead
```

#### Step 2: Create Business Rule

**Navigate to Business Rules:**

```
1. make.powerapps.com → Tables
2. Select: Leave Request
3. Business rules tab
4. + New business rule

Business Rule Designer opens:
- Condition components
- Action components
- Canvas for designing logic
```

**Business Rule Designer:**

```
Components:
- Condition: If/Then logic
- Action: What to do
- Recommendation: Show information

Canvas:
- Drag components
- Connect logic flow
- Configure properties

Right Panel:
- Properties
- Configuration
- Scope
```

#### Step 3: Simple Validation Rule

**Example: Validate End Date After Start Date**

```
Create Business Rule:
Name: Validate Date Range
Description: Ensure End Date is after Start Date
Scope: All Forms (applies to all forms)

Logic:
IF Start Date is not empty
AND End Date is not empty
AND End Date is less than Start Date
THEN Show error message

Steps:
1. Add Condition:
   Click "+ Condition"
   Configure:
   - Field: Start Date
   - Operator: Contains data

2. Add second condition (AND):
   - Field: End Date
   - Operator: Contains data

3. Add third condition (AND):
   - Field: End Date
   - Operator: is less than
   - Type: Field
   - Field: Start Date

4. Add Action:
   Drag "Show Error Message" to True branch
   Configure:
   - Field: End Date
   - Message: End date must be after start date

5. Save and Activate
```

**Result:**

```
When user enters dates:
Start Date: Nov 25, 2025
End Date: Nov 20, 2025

Instantly shows error under End Date:
"⚠️ End date must be after start date"

User must correct before saving
```

#### Step 4: Set Field Required

**Example: Manager Approval Required for > 5 Days**

```
Business Rule: Manager Approval Required
Scope: All Forms

Logic:
IF Total Days > 5
THEN Set Manager Approval field as Business Required

Steps:
1. Add Condition:
   - Field: Total Days
   - Operator: is greater than
   - Type: Value
   - Value: 5

2. Add Action (True branch):
   Drag "Set Required" action
   Configure:
   - Field: Manager Approval
   - Status: Business Required

3. Add Action (False branch):
   Drag "Set Required" action
   Configure:
   - Field: Manager Approval
   - Status: Not Required

4. Save and Activate
```

**Result:**

```
When Total Days ≤ 5:
Manager Approval: Optional (no asterisk)

When Total Days > 5:
Manager Approval: Required* (red asterisk)
Can't save without selecting Yes/No
```

#### Step 5: Show/Hide Fields

**Example: Show Comments Field for Sick Leave**

```
Business Rule: Show Sick Leave Comments
Scope: All Forms

Logic:
IF Leave Type = Sick
THEN Show Comments field
ELSE Hide Comments field

Steps:
1. Add Condition:
   - Field: Leave Type
   - Operator: equals
   - Type: Value
   - Value: Sick (select from dropdown)

2. Add Action (True branch):
   Drag "Set Visibility" action
   Configure:
   - Field: Comments
   - Visible: Yes

3. Add Action (False branch):
   Drag "Set Visibility" action
   Configure:
   - Field: Comments
   - Visible: No

4. Save and Activate
```

**Result:**

```
When Leave Type = Annual:
Comments field: Hidden

When Leave Type = Sick:
Comments field: Visible
Label: "Please describe your illness"
```

#### Step 6: Set Field Values

**Example: Set Default Status to Draft**

```
Business Rule: Set Default Status
Scope: Entity (runs on record creation)

Logic:
On new record creation
Set Status = Draft

Steps:
1. Add Condition:
   (No condition needed - always runs)
   Or use: Status = null

2. Add Action:
   Drag "Set Field Value" action
   Configure:
   - Field: Status
   - Type: Value
   - Value: Draft

3. Save and Activate
```

**Result:**

```
When user creates new leave request:
Status automatically set to: Draft
User sees pre-filled value
Can change if needed
```

**Set Value from Another Field:**

```
Business Rule: Copy Employee Email
Scope: All Forms

Logic:
IF Employee is not empty
THEN Set Emergency Contact = Employee.Email

Steps:
1. Add Condition:
   - Field: Employee
   - Operator: Contains data

2. Add Action (True branch):
   Drag "Set Field Value"
   Configure:
   - Field: Emergency Contact Email
   - Type: Field
   - Field: Employee.Email (related field)

3. Save and Activate
```

#### Step 7: Lock/Unlock Fields

**Example: Lock Approved Requests**

```
Business Rule: Lock Approved Requests
Scope: All Forms

Logic:
IF Status = Approved
THEN Lock all fields (read-only)

Steps:
1. Add Condition:
   - Field: Status
   - Operator: equals
   - Value: Approved

2. Add Actions (True branch):
   Drag "Lock" action multiple times:
   - Lock: Start Date
   - Lock: End Date
   - Lock: Total Days
   - Lock: Comments
   - Lock: Leave Type

3. Add Actions (False branch):
   Drag "Unlock" actions for same fields

4. Save and Activate
```

**Result:**

```
When Status = Draft or Pending:
All fields: Editable

When Status = Approved:
All fields: Read-only (gray/locked)
User can view but not edit
```

#### Step 8: Business Recommendations

**Example: Recommend Planning Ahead**

```
Business Rule: Recommend Advance Notice
Scope: All Forms

Logic:
IF Start Date is within next 7 days
THEN Show recommendation message

Steps:
1. Add Condition:
   - Field: Start Date
   - Operator: is less than
   - Type: Formula
   - Formula: AddDays(Now(), 7)

2. Add Action (True branch):
   Drag "Show Recommendation"
   Configure:
   - Message: Consider submitting leave requests at least 7 days in advance
   - Level: Recommendation (blue icon)

3. Save and Activate
```

**Result:**

```
When Start Date is within 7 days:
Shows blue message at top of form:
"ℹ️ Consider submitting leave requests at least 7 days in advance"

User can still save (not blocking)
Just a helpful recommendation
```

#### Step 9: Multiple Conditions (AND/OR)

**Complex Logic:**

```
Business Rule: Validate Annual Leave
Scope: All Forms

Logic:
IF Leave Type = Annual
AND Total Days > Available Balance
THEN Show error message

Steps:
1. Add Condition (AND logic):
   Condition 1:
   - Field: Leave Type
   - Operator: equals
   - Value: Annual

   Click "+ Condition" (AND)
   Condition 2:
   - Field: Total Days
   - Operator: is greater than
   - Type: Field
   - Field: Available Balance

2. Add Action (True branch):
   Show Error Message:
   - Field: Total Days
   - Message: Insufficient annual leave balance. Available: [Available Balance] days

3. Save and Activate
```

**OR Logic:**

```
To create OR conditions:
1. Create first condition
2. Change condition type to "OR"
3. Add additional conditions

Example:
IF Leave Type = Sick
OR Leave Type = Emergency
THEN Set Required: Doctor's Note
```

#### Step 10: Business Rule Scope

**Scope Options:**

```
Entity (Table):
- Runs on all forms
- Runs in background (workflows)
- Runs in API operations
- Most comprehensive

All Forms:
- Runs on all forms only
- Not in workflows/API
- Client-side only
- Most common

Specific Form:
- Runs only on selected form
- Form-specific logic
- Use when different forms need different rules
```

**Example Scenarios:**

```
Scope: Entity
Use when: Logic applies everywhere
Example: Status validation must always be enforced

Scope: All Forms
Use when: Logic only needed in UI
Example: Show/hide fields based on user input

Scope: Specific Form
Use when: Different forms have different workflows
Example: Quick Create form has simpler validation
          Main form has comprehensive validation
```

### Assignment Deliverable

✅ **Create validation business rules:**

- Validate End Date > Start Date
- Validate Total Days > 0
- Validate Annual leave <= balance
- Show appropriate error messages

✅ **Create dynamic required fields:**

- Manager Approval required if > 5 days
- Comments required for sick leave
- Supporting document required for > 10 days

✅ **Create show/hide rules:**

- Show Comments for Sick leave
- Show Supporting Document for long leaves
- Hide Manager Approval for < 5 days

✅ **Create lock/unlock rules:**

- Lock all fields when Status = Approved
- Lock Status field when not draft
- Unlock for specific roles (via form logic)

✅ **Test business rules:**

- Test each rule thoroughly
- Verify instant feedback
- Check all conditions
- Test edge cases
- Document results

### Knowledge Check

- [ ] What are business rules?
- [ ] When should you use business rules vs flows?
- [ ] What's the scope of a business rule?
- [ ] How do you validate fields?
- [ ] How do you set required fields dynamically?
- [ ] How do you show/hide fields?
- [ ] How do you lock fields?
- [ ] What's a recommendation vs error?
- [ ] What's AND vs OR logic?
- [ ] Where do business rules run (client/server)?

### Resources

- [Microsoft Learn: Business rules](https://learn.microsoft.com/en-us/power-apps/maker/data-platform/data-platform-create-business-rule)
- [Microsoft Learn: Business rule examples](https://learn.microsoft.com/en-us/power-apps/maker/model-driven-apps/create-business-rules-recommendations-apply-logic-form)

---

## Stage 5 Complete! 🎉

### Total Stage 5 Summary

**Stage 5 - Dataverse: 10 hours completed**

#### Modules Completed:

- ✅ Module 33: Introduction to Dataverse (1 hour)
- ✅ Module 34: Dataverse Column Types (1.5 hours)
- ✅ Module 35: Dataverse Relationships (1.5 hours)
- ✅ Module 36: Forms and Views (1.5 hours)
- ✅ Module 37: Security Roles and Permissions (1.5 hours)
- ✅ Module 38: Business Rules (1 hour)

### Remaining Stage 5 Content:

⚠️ Note: Modules 39-40 will be integrated into Stage 6 for better flow

- Module 39: Dataverse APIs (covered in Model-Driven Apps)
- Module 40: Data Import/Export (covered in deployment)

### Key Achievements

✅ Mastered Dataverse fundamentals
✅ Created custom tables and columns
✅ Built complete data model with relationships
✅ Designed forms and views
✅ Implemented row-level security
✅ Created business rules for validation
✅ Configured field-level security
✅ Built Leave Management data structure

### Skills Acquired

- Dataverse architecture and licensing
- 40+ data types (text, number, choice, lookup, calculated, rollup)
- Relationships (1:N, N:1, N:N, self-referencing)
- Form design (main, quick create, quick view)
- View creation and filtering
- Security roles (CRUD, access levels)
- Business units and teams
- Field-level security
- Business rules (validation, visibility, required fields)

### Leave Management System Progress

✅ Complete Dataverse implementation:

- 📊 Tables: Department, Employee Profile, Leave Type, Leave Request, Leave Balance
- 🔗 Relationships: 7 relationships configured
- 📝 Forms: Main forms, Quick Create, Quick View
- 👁️ Views: Custom views with filters
- 🔒 Security: 3 security roles (Employee, Manager, Administrator)
- ✅ Business Rules: Validation and dynamic behavior

### Overall Training Progress

**Completed:**

- ✅ Stage 1: Power Apps Fundamentals (16.5 hours)
- ✅ Stage 2: Canvas Apps Intermediate (29 hours)
- ✅ Stage 3: Advanced Canvas Apps (36 hours)
- ✅ Stage 4: Cloud Flows (25.5 hours)
- ✅ Stage 5: Dataverse (10 hours)

**Total Completed: 117 hours of 170 hours (68.8%)**

**Remaining:**

- ⏳ Stage 6: Model Driven Apps, Power Pages, Solutions & Final Project (53 hours)

### Next Stage Preview

**Stage 6: Final Integration** will cover:

- Model-Driven Apps (form-based enterprise apps)
- Dataverse APIs and integration
- Power Pages (external portals)
- Solutions and ALM (deployment)
- Data import/export strategies
- **Final Project: Complete Leave Management System** (32 hours)
  - Canvas App (employee interface)
  - Model-Driven App (admin interface)
  - Power Pages (external access)
  - Complete flows and automation
  - Security implementation
  - Deployment across environments

---

**Type "NEXT" to begin Stage 6: Model-Driven Apps, Power Pages, Solutions & Final Project**
