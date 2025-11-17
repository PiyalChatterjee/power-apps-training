# Power Platform Training Manual - Stage 4 Part 2: Naming, Data Operations & Integration

## Overview

This section covers flow naming conventions, data operations (loops, filters, transformations), Canvas app integration, and premium connectors. **Duration: 5.5 hours**

---

## Module 24: Flow Naming Conventions (0.5 hours)

### Learning Objectives

- Understand importance of naming conventions
- Apply consistent flow naming patterns
- Name actions descriptively
- Organize flows effectively
- Improve collaboration and maintenance

### Step-by-Step Instructions

#### Step 1: Flow Naming Convention

**Standard Format:**

```
[Trigger Type] - [Entity] - [Action] - [Condition/Purpose]
```

**Examples:**

```
✅ GOOD:
Auto - Leave Request - Send Notification - On Create
Auto - Employee - Update Manager - On Status Change
Scheduled - Reports - Send Daily Summary
Manual - Leave Request - Bulk Approve
Instant - Employee - Send Welcome Email

❌ BAD:
Flow 1
New Flow
Test Flow
My Flow
Untitled
```

**Trigger Type Prefixes:**

```
Auto - Automated trigger (when item created/modified)
Scheduled - Recurrence trigger
Manual - Manual trigger (instant)
Child - Child flow (called by other flows)
HTTP - HTTP request trigger
```

**Entity Examples:**

```
Leave Request
Employee
Department
Project
Approval
Invoice
Customer
```

**Action Examples:**

```
Send Notification
Create Record
Update Status
Generate Report
Process Approval
Archive Data
Sync Data
```

#### Step 2: Action Naming Convention

**Use Descriptive Names:**

```
❌ BAD:
- Get items
- Get items 2
- Compose
- Compose 2
- Condition

✅ GOOD:
- Get Pending Leave Requests
- Get Employee Details
- Compose - Full Name
- Compose - Calculate Business Days
- Condition - Check If Manager Approval Required
```

**Pattern:**

```
[Action Type] - [What It Does] - [Additional Context]
```

**Examples by Action Type:**

**Get Actions:**

```
Get Employee By Email
Get Manager Details
Get All Active Projects
Get Leave Balance For Employee
```

**Create/Update Actions:**

```
Create Approval Record
Update Leave Request Status
Update Employee Department
Create Notification Entry
```

**Compose Actions:**

```
Compose - Employee Full Name
Compose - Formatted Date Range
Compose - Email Body HTML
Compose - Calculate Total Days
```

**Condition Actions:**

```
Condition - Is Manager Approval Required
Condition - Check If Business Days > 5
Condition - Validate Email Format
Condition - Is Weekday
```

**Send Email Actions:**

```
Send Email - Notify Manager
Send Email - Approval Confirmation
Send Email - Daily Summary Report
```

**Apply to Each:**

```
Apply to Each - Process Pending Approvals
Apply to Each - Update Employee Records
Apply to Each - Send Notifications
```

#### Step 3: Variable Naming

**Standard Format:**

```
var[Type][Purpose]
```

**Examples:**

```
String Variables:
varEmployeeName
varEmailBody
varFormattedDate
varDepartmentName

Integer Variables:
varTotalDays
varEmployeeCount
varApprovalCount

Boolean Variables:
varIsApproved
varIsManager
varIsBusinessDay
varHasErrors

Array Variables:
varEmployeeList
varPendingApprovals
varErrorMessages

Object Variables:
varEmployeeDetails
varLeaveRequest
varApprovalData
```

#### Step 4: Folder Organization

**Organize Flows by Category:**

```
📁 Leave Management System/
  📁 01 - Core Workflows/
    - Auto - Leave Request - Send Notification - On Create
    - Auto - Leave Request - Update Status - On Approval
    - Scheduled - Leave Balance - Update Monthly

  📁 02 - Manager Actions/
    - Manual - Leave Request - Bulk Approve
    - Manual - Leave Request - Send Reminder

  📁 03 - Reports/
    - Scheduled - Reports - Daily Leave Summary
    - Scheduled - Reports - Monthly Department Report

  📁 04 - Utilities/
    - Child - Calculate Business Days
    - Child - Send Formatted Email

  📁 05 - Integrations/
    - Auto - Employee - Sync From HR System
    - HTTP - External - Process Leave Request
```

#### Step 5: Description Best Practices

**Flow Description Template:**

```
Purpose: What this flow does
Trigger: When it runs
Input: What data it needs
Output: What it produces
Dependencies: Other flows, lists, or systems
Owner: Who maintains it
Last Updated: Date
```

**Example:**

```
Flow Name: Auto - Leave Request - Send Notification - On Create

Description:
Purpose: Sends email notification to manager when new leave request is created
Trigger: When item is created in Leave Requests list
Input: Leave request details from SharePoint
Output: Email sent to manager, notification record created
Dependencies:
  - SharePoint List: Leave Requests
  - SharePoint List: Employees
  - Child Flow: Child - Calculate Business Days
Owner: IT Department
Last Updated: 2025-11-14
```

#### Step 6: Comment Actions

**Add Notes to Complex Logic:**

```
Action: Compose - Note
Inputs: "This section calculates business days by excluding weekends and holidays"
Color: Gray (or choose color)

Then add complex logic below
```

**Use for:**

- Complex calculations
- Business rules
- Important decisions
- Workarounds
- Known issues

**Example:**

```
Compose - Note
Inputs: "IMPORTANT: This filter excludes employees on probation as per HR policy dated 2025-01-15"

Get items - Active Employees
Filter: Status eq 'Active' and ProbationStatus eq 'Completed'
```

#### Step 7: Versioning in Names

**For Major Changes:**

```
Before:
Auto - Leave Request - Send Notification - On Create

After Major Update:
Auto - Leave Request - Send Notification - On Create v2

Keep old flow:
Auto - Leave Request - Send Notification - On Create v1 (ARCHIVED)
```

**Version Naming:**

```
v1, v2, v3 - Major versions
v1.1, v1.2 - Minor updates (optional)
ARCHIVED - No longer in use
TEST - Testing version
DEV - Development version
PROD - Production version (if needed)
```

#### Step 8: Environment Naming

**If Using Multiple Environments:**

```
Flow Name Format:
[ENV] - [Standard Name]

Examples:
DEV - Auto - Leave Request - Send Notification
TEST - Auto - Leave Request - Send Notification
PROD - Auto - Leave Request - Send Notification

Or use folders:
📁 Development/
📁 Testing/
📁 Production/
```

#### Step 9: Connection Reference Naming

**Format:**

```
[Service] - [Purpose] - [Account Type]
```

**Examples:**

```
SharePoint - Leave Management - Service Account
SQL Server - HR Database - Service Account
Office 365 - Email Notifications - Shared
Dataverse - Production - Service Account
HTTP - External API - OAuth
```

#### Step 10: Documentation Standards

**Create Flow Documentation:**

```markdown
# Flow Documentation

## Flow Name

Auto - Leave Request - Send Notification - On Create

## Overview

Automatically sends email notification to manager when employee creates a new leave request.

## Trigger

- Type: Automated
- Source: SharePoint - Leave Requests List
- Event: When an item is created
- Conditions: None

## Actions

1. Get Employee Details

   - Purpose: Retrieve submitter information
   - Source: Employees list
   - Filter: Email matches Created By

2. Get Manager Details

   - Purpose: Get manager's email
   - Source: Employees list
   - Filter: ID matches employee's ManagerID

3. Compose - Calculate Business Days

   - Purpose: Calculate working days excluding weekends
   - Logic: Loop through date range, exclude Sat/Sun

4. Send Email - Notify Manager
   - To: Manager's email
   - Subject: New leave request from [Employee Name]
   - Body: Formatted HTML with leave details

## Error Handling

- If manager not found: Send to HR email
- If email fails: Log to Error Log list

## Dependencies

- SharePoint Lists: Leave Requests, Employees
- Connections: SharePoint, Office 365 Outlook

## Testing

- Test cases: New leave request creation
- Expected result: Manager receives email within 2 minutes

## Maintenance

- Owner: HR IT Team
- Review Schedule: Quarterly
- Last Updated: 2025-11-14
```

### Assignment Deliverable

✅ **Rename all existing flows:**

- Apply naming convention
- Add descriptive action names
- Organize into folders
- Add flow descriptions

✅ **Create naming convention document:**

- Flow naming patterns
- Action naming patterns
- Variable naming patterns
- Examples for each type

✅ **Document 3 key flows:**

- Purpose
- Trigger details
- Actions explained
- Dependencies
- Error handling
- Testing notes

### Knowledge Check

- [ ] What's the standard flow name format?
- [ ] What prefixes indicate trigger types?
- [ ] How should actions be named?
- [ ] What's the variable naming pattern?
- [ ] Why use descriptive names?
- [ ] How do you organize flows?
- [ ] What should flow descriptions include?
- [ ] When should you add comments?
- [ ] How do you handle versions?
- [ ] What's connection reference naming?

### Naming Quick Reference

| Item          | Format                       | Example                                  |
| ------------- | ---------------------------- | ---------------------------------------- |
| **Flow**      | [Type] - [Entity] - [Action] | Auto - Leave Request - Send Notification |
| **Action**    | [Action] - [Purpose]         | Get Employee Details                     |
| **Variable**  | var[Type][Purpose]           | varEmployeeName                          |
| **Compose**   | Compose - [What]             | Compose - Full Name                      |
| **Condition** | Condition - [Check]          | Condition - Is Manager                   |

### Resources

- [Microsoft Learn: Flow best practices](https://learn.microsoft.com/en-us/power-automate/guidance/planning/planning-phase)
- [Microsoft Learn: Solution layers](https://learn.microsoft.com/en-us/power-platform/alm/solution-layers-alm)

---

## Module 25: Data Operations in Flows (2 hours)

### Learning Objectives

- Use Apply to each loops
- Filter arrays efficiently
- Transform data with Select
- Work with JSON
- Handle large datasets
- Optimize data operations

### Step-by-Step Instructions

#### Step 1: Apply to Each Loop

**Purpose:** Process each item in an array

**Basic Structure:**

```
Action: Apply to each
Select an output from previous steps: @{body('Get_items')?['value']}

Inside loop:
  - Process each item using: @{item()?['PropertyName']}
```

**Example 1: Send Email to Each Employee**

```
Action: Get items - Active Employees
Site: Your SharePoint
List: Employees
Filter: Status eq 'Active'

Action: Apply to each - Send Welcome Emails
Input: @{body('Get_items_-_Active_Employees')?['value']}

Inside loop:
  Action: Send an email (V2)
  To: @{items('Apply_to_each_-_Send_Welcome_Emails')?['Email']}
  Subject: Welcome to the company!
  Body:
    Dear @{items('Apply_to_each_-_Send_Welcome_Emails')?['FirstName']},
    Welcome to @{items('Apply_to_each_-_Send_Welcome_Emails')?['Department']}!
```

**Example 2: Update Multiple Items**

```
Action: Get items - Pending Approvals
Filter: Status eq 'Pending' and DueDate lt '@{utcNow()}'

Action: Apply to each - Update Overdue Items
Input: @{body('Get_items_-_Pending_Approvals')?['value']}

Inside loop:
  Action: Update item
  ID: @{items('Apply_to_each_-_Update_Overdue_Items')?['ID']}
  Status: Overdue
  OverdueNotificationSent: Yes

  Action: Send an email (V2)
  To: @{items('Apply_to_each_-_Update_Overdue_Items')?['AssignedTo/Email']}
  Subject: Overdue Approval Required
```

**Loop with Counter:**

```
Action: Initialize variable - varCounter
Type: Integer
Value: 0

Action: Apply to each - Process With Counter
Input: @{body('Get_items')?['value']}

Inside loop:
  Action: Increment variable
  Name: varCounter
  Value: 1

  Action: Compose - Item Number
  Inputs: Processing item @{variables('varCounter')} of @{length(body('Get_items')?['value'])}
```

#### Step 2: Filter Array

**Purpose:** Filter array without applying to each

**Syntax:**

```
Action: Filter array
From: @{body('Get_items')?['value']}
Condition: [field] [operator] [value]
```

**Example 1: Filter by Status**

```
Action: Get items - All Leave Requests
(No filter here - get all)

Action: Filter array - Only Approved Requests
From: @{body('Get_items_-_All_Leave_Requests')?['value']}
Condition: Status is equal to Approved

Result: @{body('Filter_array_-_Only_Approved_Requests')}
(Contains only approved items)
```

**Example 2: Multiple Conditions**

```
Action: Filter array - IT Employees With High Salary
From: @{body('Get_items')?['value']}

Advanced mode:
@and(
  equals(item()?['Department'], 'IT'),
  greater(item()?['Salary'], 100000)
)
```

**Example 3: Filter by Date Range**

```
Action: Filter array - Current Month Leave Requests
From: @{body('Get_items')?['value']}

Advanced mode:
@and(
  greaterOrEquals(
    ticks(item()?['StartDate']),
    ticks(startOfMonth(utcNow()))
  ),
  less(
    ticks(item()?['StartDate']),
    ticks(startOfMonth(addMonths(utcNow(), 1)))
  )
)
```

**Why Use Filter Array?**

```
❌ WITHOUT Filter Array:
Get items (5000 items)
Apply to each (5000 iterations)
  If Status = Approved (only 100 match)
    Send email

Result: 5000 iterations, 100 emails

✅ WITH Filter Array:
Get items (5000 items)
Filter array (100 matching items)
Apply to each (100 iterations)
  Send email

Result: 100 iterations, 100 emails - Much faster!
```

#### Step 3: Select Action

**Purpose:** Transform array data structure

**Use Case:** Extract specific fields or rename properties

**Example 1: Extract Specific Fields**

```
Get items returns:
[
  {
    "ID": 1,
    "FirstName": "John",
    "LastName": "Smith",
    "Email": "john@company.com",
    "Department": "IT",
    "Salary": 75000,
    "HireDate": "2020-01-15",
    ...20 more fields
  },
  {...}
]

Action: Select - Extract Name and Email Only
From: @{body('Get_items')?['value']}
Map:
  Name: @{item()?['FirstName']} @{item()?['LastName']}
  Email: @{item()?['Email']}

Result:
[
  {
    "Name": "John Smith",
    "Email": "john@company.com"
  },
  {...}
]
```

**Example 2: Transform for Email**

```
Action: Select - Format Employee List for Email
From: @{body('Filter_array_-_IT_Employees')?['value']}
Map:
  EmployeeName: @{item()?['FirstName']} @{item()?['LastName']}
  Role: @{item()?['JobTitle']}
  Contact: @{item()?['Email']}

Then use in email:
Action: Create HTML table
From: @{body('Select_-_Format_Employee_List')}

Email body:
<h2>IT Department Employees</h2>
@{body('Create_HTML_table')}
```

**Example 3: Calculate Values**

```
Action: Select - Calculate Totals
From: @{body('Get_items')?['value']}
Map:
  LeaveType: @{item()?['LeaveType']}
  Days: @{item()?['TotalDays']}
  Year: @{formatDateTime(item()?['StartDate'], 'yyyy')}
  IsLongLeave: @{if(greater(item()?['TotalDays'], 10), 'Yes', 'No')}
```

#### Step 4: Create HTML Table

**Purpose:** Convert array to HTML table for emails

```
Action: Get items - Pending Approvals

Action: Select - Format for Table
From: @{body('Get_items')?['value']}
Map:
  Employee: @{item()?['Employee/Title']}
  Type: @{item()?['LeaveType']}
  Start: @{formatDateTime(item()?['StartDate'], 'MMM dd')}
  End: @{formatDateTime(item()?['EndDate'], 'MMM dd')}
  Days: @{item()?['TotalDays']}

Action: Create HTML table
From: @{body('Select_-_Format_for_Table')}
Columns: Automatic
Include headers: Yes

Action: Send an email (V2)
Subject: Pending Approvals Summary
Body:
<html>
<body>
  <h2>Pending Leave Approvals</h2>
  <p>You have @{length(body('Get_items')?['value'])} pending approvals:</p>
  @{body('Create_HTML_table')}
  <p>Please review and approve in the system.</p>
</body>
</html>
```

**Custom Table Styling:**

```
Action: Compose - Styled HTML Table
Inputs:
<style>
  table { border-collapse: collapse; width: 100%; }
  th { background-color: #0078d4; color: white; padding: 10px; text-align: left; }
  td { border: 1px solid #ddd; padding: 8px; }
  tr:nth-child(even) { background-color: #f2f2f2; }
</style>
@{body('Create_HTML_table')}
```

#### Step 5: Create CSV Table

**Purpose:** Generate CSV for exports

```
Action: Create CSV table
From: @{body('Select_-_Format_for_Export')}
Columns: Automatic
Include headers: Yes

Result:
Employee,Type,StartDate,EndDate,Days
John Smith,Annual,2025-11-20,2025-11-25,5
Sarah Johnson,Sick,2025-11-22,2025-11-23,2

Use in:
- Email attachments (via Base64)
- Save to SharePoint document library
- Send to OneDrive
```

#### Step 6: Parse JSON (Revisited with Arrays)

**Scenario: API Returns Complex JSON**

```
HTTP Action returns:
{
  "employees": [
    {
      "id": 1,
      "profile": {
        "firstName": "John",
        "lastName": "Smith",
        "contact": {
          "email": "john@company.com",
          "phone": "555-0100"
        }
      },
      "employment": {
        "department": "IT",
        "position": "Developer",
        "salary": 75000
      }
    }
  ],
  "metadata": {
    "totalCount": 150,
    "timestamp": "2025-11-14T10:00:00Z"
  }
}

Action: Parse JSON
Content: @{body('HTTP_Request')}
Schema: [Use sample to generate]

Access data:
Total: @{body('Parse_JSON')?['metadata']?['totalCount']}
First employee name: @{body('Parse_JSON')?['employees'][0]?['profile']?['firstName']}

Loop through employees:
Apply to each: @{body('Parse_JSON')?['employees']}
  Email: @{items('Apply_to_each')?['profile']?['contact']?['email']}
  Department: @{items('Apply_to_each')?['employment']?['department']}
```

#### Step 7: Union, Intersection, Join Arrays

**Union - Combine arrays (remove duplicates):**

```
Action: Compose - Array 1
Inputs: ["John", "Sarah", "Mike"]

Action: Compose - Array 2
Inputs: ["Sarah", "Mike", "Emily"]

Action: Compose - Union Arrays
Inputs: @{union(outputs('Compose_-_Array_1'), outputs('Compose_-_Array_2'))}

Result: ["John", "Sarah", "Mike", "Emily"]
```

**Intersection - Common items:**

```
Action: Compose - Intersection
Inputs: @{intersection(outputs('Compose_-_Array_1'), outputs('Compose_-_Array_2'))}

Result: ["Sarah", "Mike"]
```

**Join - Array to string:**

```
Action: Compose - Employee Names
Inputs: @{join(body('Select')?['Name'], ', ')}

Result: "John Smith, Sarah Johnson, Mike Williams"
```

#### Step 8: Do Until Loop

**Purpose:** Loop until condition is met

**Example 1: Process in Batches**

```
Action: Initialize variable - varOffset
Type: Integer
Value: 0

Action: Initialize variable - varBatchSize
Type: Integer
Value: 100

Action: Initialize variable - varAllItems
Type: Array
Value: []

Action: Do until
Condition: @{greater(variables('varOffset'), 5000)}
Limit:
  Count: 60
  Timeout: PT1H

Actions inside loop:
  Action: Get items - Batch
  Top Count: @{variables('varBatchSize')}
  Skip Token: Use $skip=@{variables('varOffset')}

  Action: Append to array variable
  Name: varAllItems
  Value: @{body('Get_items_-_Batch')?['value']}

  Action: Increment variable
  Name: varOffset
  Value: @{variables('varBatchSize')}
```

**Example 2: Wait Until Approval**

```
Action: Initialize variable - varIsApproved
Type: Boolean
Value: false

Action: Do until - Wait for Approval
Condition: @{equals(variables('varIsApproved'), true)}
Limit:
  Count: 1440 (24 hours worth of minutes)
  Timeout: PT24H

Actions inside:
  Action: Get item - Check Approval Status
  ID: @{triggerBody()?['ApprovalID']}

  Action: Condition - Is Approved
  If Status equals 'Approved':
    Set variable - varIsApproved to true
  Else:
    Delay for 1 minute
```

#### Step 9: Optimize Large Datasets

**Strategy 1: Use OData Filtering (Server-Side)**

```
❌ SLOW - Get All Then Filter:
Get items (no filter) → Returns 10,000 items
Filter array (Status eq 'Active') → Keeps 100

✅ FAST - Filter on Server:
Get items
Filter Query: Status eq 'Active' → Returns 100 items
```

**Strategy 2: Use Select to Reduce Data**

```
❌ SLOW - Get All Columns:
Get items → Returns all 50 columns for 1000 items
Apply to each → Process all data

✅ FAST - Select Required Columns:
Get items
Select Columns: ID, Title, Email only
→ Much less data transferred
```

**Strategy 3: Process in Batches**

```
✅ For 10,000 items:
Batch 1: Items 1-100
Batch 2: Items 101-200
...
Batch 100: Items 9901-10000

Use Do Until with $skip
```

**Strategy 4: Use Concurrency**

```
Apply to each settings:
Degree of Parallelism: 50

Processes 50 items simultaneously instead of 1 at a time
⚠️ Be careful with API limits
```

#### Step 10: Practical Example - Monthly Report

**Complete Flow: Generate Monthly Department Report**

```
Flow: Scheduled - Reports - Monthly Department Report
Trigger: Recurrence - First day of month at 8 AM

Step 1: Get Last Month Date Range
Action: Compose - Start of Last Month
Inputs: @{startOfMonth(addMonths(utcNow(), -1))}

Action: Compose - End of Last Month
Inputs: @{startOfMonth(utcNow())}

Step 2: Get Leave Requests
Action: Get items - Last Month Leave Requests
Filter:
  StartDate ge '@{outputs('Compose_-_Start_of_Last_Month')}'
  and StartDate lt '@{outputs('Compose_-_End_of_Last_Month')}'

Step 3: Group by Department
Action: Select - Extract Department and Days
From: @{body('Get_items')?['value']}
Map:
  Department: @{item()?['Employee/Department']}
  Days: @{item()?['TotalDays']}
  LeaveType: @{item()?['LeaveType']}

Action: Compose - Group By Department
Inputs: (Use expression to group)
@{...} // Complex grouping expression

Step 4: Create Summary Table
Action: Create HTML table
From: [Summarized data]

Step 5: Send Report
Action: Send an email (V2)
To: hr@company.com
Subject: Monthly Leave Report - @{formatDateTime(addMonths(utcNow(), -1), 'MMMM yyyy')}
Body:
<h2>Leave Report for @{formatDateTime(addMonths(utcNow(), -1), 'MMMM yyyy')}</h2>
<h3>Summary by Department</h3>
@{body('Create_HTML_table')}
<p>Total leave days: @{sum(body('Select')?['Days'])}</p>
```

### Assignment Deliverable

✅ **Create "Leave Data Processor" flow:**

- Get all leave requests
- Filter by date range (last 30 days)
- Select and transform data
- Group by department
- Create HTML table
- Send report email

✅ **Demonstrate each data operation:**

- Apply to each with counter
- Filter array with multiple conditions
- Select to transform structure
- Create HTML table
- Parse JSON from sample API

✅ **Performance comparison:**

- Show "Get all then filter" vs "Filter in Get items"
- Measure execution time
- Document best practices

### Knowledge Check

- [ ] When should you use Apply to each?
- [ ] How does Filter array improve performance?
- [ ] What does Select action do?
- [ ] How do you access item in loop?
- [ ] What's Create HTML table used for?
- [ ] How do you prevent infinite loops in Do Until?
- [ ] What's the difference between Filter array and Get items filter?
- [ ] How do you process large datasets efficiently?
- [ ] What's concurrency in Apply to each?
- [ ] How do you transform array structure?

### Data Operations Quick Reference

| Operation     | Action            | Use Case              |
| ------------- | ----------------- | --------------------- |
| **Loop**      | Apply to each     | Process each item     |
| **Filter**    | Filter array      | Remove unwanted items |
| **Transform** | Select            | Change data structure |
| **Display**   | Create HTML table | Format for email      |
| **Export**    | Create CSV table  | Generate CSV file     |
| **Parse**     | Parse JSON        | Handle API responses  |
| **Wait**      | Do until          | Loop until condition  |

### Resources

- [Microsoft Learn: Data operations](https://learn.microsoft.com/en-us/power-automate/data-operations)
- [Microsoft Learn: Apply to each](https://learn.microsoft.com/en-us/power-automate/apply-to-each)
- [Microsoft Learn: Filter array](https://learn.microsoft.com/en-us/power-automate/data-operations#filter-array-action)
- [Microsoft Learn: Select action](https://learn.microsoft.com/en-us/power-automate/data-operations#select-action)
- [Blog: Performance Tips](https://powerusers.microsoft.com/t5/Power-Automate-Community-Blog/bg-p/FlowBlog)

---

## Stage 4 Part 2 Progress Summary

### Modules Completed in This Section:

- ✅ Module 24: Flow Naming Conventions (0.5 hours)
- ✅ Module 25: Data Operations in Flows (2 hours)

### Completed in Part 2: 2.5 hours

### Cumulative Stage 4 Progress: 7.5 hours of 25.5 hours

### Remaining Modules:

- Module 26: Canvas App Integration (2 hours)
- Module 27: Premium Connectors (1 hour)
- Module 28: Connection References (1 hour)
- Module 29: Scope Actions (1 hour)
- Module 30: Child Flows (2 hours)
- Module 31: Run-as User (1 hour)
- Module 32: Flow Error Handling (2 hours)

### Next Up

Canvas App Integration with flows, Premium Connectors, and Connection References.

---

**Type "NEXT" to continue with Canvas App Integration, Premium Connectors, and Connection References.**
