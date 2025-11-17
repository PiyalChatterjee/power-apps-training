# Power Platform Training Manual - Stage 4 Part 4: Advanced Flow Patterns & Error Handling

## Overview

This section covers scope actions (Try-Catch patterns), child flows (reusable components), run-as user configuration, and comprehensive error handling strategies. **Duration: 6 hours**

---

## Module 29: Scope Actions (1 hour)

### Learning Objectives

- Understand scope actions
- Implement Try-Catch-Finally patterns
- Use parallel branches
- Organize complex flows
- Handle errors with scopes
- Optimize flow execution

### Step-by-Step Instructions

#### Step 1: Understanding Scope Actions

**What is a Scope?**

```
Scope = Container for grouping actions together

Benefits:
✅ Organize complex flows visually
✅ Implement Try-Catch-Finally patterns
✅ Run actions in parallel
✅ Configure error handling per group
✅ Easier troubleshooting
✅ Better flow readability
```

**Add Scope Action:**

```
In Power Automate designer:
1. Add new action
2. Search: "Scope"
3. Select: "Scope" (Control)
4. Name: Try, Catch, Finally, Process Data, etc.

Result: Container to add actions inside
```

**Basic Scope:**

```
Scope: Process Leave Request
├── Get employee details
├── Check leave balance
├── Create leave request
└── Send notification
```

#### Step 2: Try-Catch-Finally Pattern

**Structure:**

```
Scope: Try
├── Action 1: Get employee
├── Action 2: Create leave request
├── Action 3: Send notification
└── [If any fails, go to Catch]

Scope: Catch
├── Configure run after: Try scope has failed or timed out
├── Action: Log error
├── Action: Send error notification
└── Action: Set variable error flag

Scope: Finally
├── Configure run after: Try scope succeeded OR Catch scope succeeded
├── Action: Cleanup tasks
├── Action: Update status
└── Action: Final notifications
```

**Configure Run After:**

```
Click the 3 dots on Catch scope → Configure run after

Options:
☐ is successful
☑ has failed
☑ is skipped
☑ has timed out

This means Catch runs when Try fails
```

**Complete Try-Catch-Finally Example:**

```
Flow: Auto - Leave Request - Process With Error Handling

Trigger: When item is created (Leave Requests)

Scope: Try
  Action: Get items - Find Employee
  List: Employees
  Filter: ID eq @{triggerBody()?['EmployeeId']}

  Action: Get items - Check Leave Balance
  Filter: EmployeeId eq @{triggerBody()?['EmployeeId']}
          and LeaveType eq '@{triggerBody()?['LeaveType']}'

  Action: Condition - Sufficient Balance
  If balance < requested days:
    Terminate (failed)

  Action: Get items - Find Manager
  Filter: ID eq @{first(body('Get_items_-_Find_Employee')?['value'])?['ManagerID']}

  Action: Send an email (V2) - Notify Manager
  To: @{first(body('Get_items_-_Find_Manager')?['value'])?['Email']}
  Subject: New Leave Request
  Body: [Details]

  Action: Update item - Set Status
  ID: @{triggerBody()?['ID']}
  Status: Pending Approval

Scope: Catch
  Configure run after: Try has failed or timed out

  Action: Compose - Error Details
  Inputs:
  {
    "ErrorTime": "@{utcNow()}",
    "FlowName": "@{workflow()?['name']}",
    "FlowRunID": "@{workflow()?['run']?['name']}",
    "TriggerID": "@{triggerBody()?['ID']}",
    "ErrorAction": "@{result('Try')[0]['error']['message']}"
  }

  Action: Create item - Log Error
  List: Error Log
  Title: Error in Leave Request Processing
  ErrorDetails: @{outputs('Compose_-_Error_Details')}
  Timestamp: @{utcNow()}

  Action: Send an email (V2) - Notify IT
  To: it-support@company.com
  Subject: Flow Error - Leave Request Processing
  Body:
  <h2>Flow Execution Error</h2>
  <p><strong>Flow:</strong> @{workflow()?['name']}</p>
  <p><strong>Run ID:</strong> @{workflow()?['run']?['name']}</p>
  <p><strong>Time:</strong> @{utcNow()}</p>
  <p><strong>Error:</strong> @{result('Try')[0]['error']['message']}</p>

  Action: Update item - Set Error Status
  ID: @{triggerBody()?['ID']}
  Status: Error
  ErrorMessage: @{result('Try')[0]['error']['message']}

Scope: Finally
  Configure run after: Try succeeded OR Catch succeeded

  Action: Compose - Execution Summary
  Inputs:
  {
    "ProcessedAt": "@{utcNow()}",
    "TrySucceeded": "@{equals(result('Try')[0]['status'], 'Succeeded')}",
    "CatchExecuted": "@{not(empty(result('Catch')))}",
    "RequestID": "@{triggerBody()?['ID']}"
  }

  Action: Create item - Audit Log
  List: Flow Audit Log
  Details: @{outputs('Compose_-_Execution_Summary')}
```

#### Step 3: Accessing Scope Results

**result() Function:**

```
Get status of scope:
@{result('ScopeName')[0]['status']}

Possible values:
- Succeeded
- Failed
- Skipped
- TimedOut
- Cancelled

Get error from scope:
@{result('Try')[0]['error']['message']}
@{result('Try')[0]['error']['code']}

Check if scope succeeded:
@{equals(result('Try')[0]['status'], 'Succeeded')}
```

**Multiple Actions in Scope:**

```
result('Try') returns array of all actions in scope:

result('Try')[0] = First action
result('Try')[1] = Second action
result('Try')[2] = Third action

Get specific action result:
@{result('Try')[0]['outputs']['body']}
```

**Practical Use in Catch:**

```
Scope: Catch
  Action: Compose - Which Action Failed
  Inputs:
  @{if(
    equals(result('Try')[0]['status'], 'Failed'),
    result('Try')[0]['name'],
    if(
      equals(result('Try')[1]['status'], 'Failed'),
      result('Try')[1]['name'],
      'Unknown action'
    )
  )}

  Action: Compose - Error Message
  Inputs: @{coalesce(
    result('Try')[0]['error']['message'],
    result('Try')[1]['error']['message'],
    result('Try')[2]['error']['message'],
    'No error message available'
  )}
```

#### Step 4: Parallel Branches

**Use Case: Execute multiple actions simultaneously**

```
Without Parallel:
Action 1 (5 seconds)
Action 2 (5 seconds)
Action 3 (5 seconds)
Total: 15 seconds ⏱️

With Parallel:
Action 1 (5 seconds) ┐
Action 2 (5 seconds) ├─ Run simultaneously
Action 3 (5 seconds) ┘
Total: 5 seconds ⏱️
```

**Create Parallel Branches:**

```
Method 1: Add parallel branch button
1. After an action, click "+"
2. Select "Add a parallel branch"
3. Add actions to new branch

Method 2: Inside Scope
Scope: Process in Parallel
  ├── Branch 1
  │   └── Send email to employee
  ├── Branch 2
  │   └── Send email to manager
  └── Branch 3
      └── Create notification record
```

**Example: Send Multiple Notifications**

```
Scope: Send Notifications
  Configure run after: Previous action succeeded

  Branch 1:
    Action: Send an email (V2) - Notify Employee
    To: @{body('Get_employee')?['Email']}
    Subject: Your leave request has been submitted
    Body: [Details]

  Branch 2 (Parallel):
    Action: Send an email (V2) - Notify Manager
    To: @{body('Get_manager')?['Email']}
    Subject: New leave request for approval
    Body: [Details]

  Branch 3 (Parallel):
    Action: Send an email (V2) - Notify HR
    To: hr@company.com
    Subject: Leave request logged
    Body: [Details]

  Branch 4 (Parallel):
    Action: HTTP - Call External System
    URI: https://api.company.com/notifications
    Body: [Notification data]

All 4 actions run simultaneously!
Total time = Longest individual action
```

**Wait for All Parallel Branches:**

```
Scope: Parallel Operations
  └── [4 parallel branches]

After scope completes:
Action: Compose - All Branches Complete
Inputs: All parallel operations finished at @{utcNow()}

Scope completes only when ALL branches finish (or fail)
```

#### Step 5: Nested Scopes

**Complex Flow Organization:**

```
Scope: Validate Input
├── Action: Check required fields
├── Action: Validate dates
└── Action: Check permissions

Scope: Process Request
├── Scope: Try
│   ├── Get employee
│   ├── Create request
│   └── Send notifications
├── Scope: Catch
│   ├── Log error
│   └── Send error email
└── Scope: Finally
    └── Update audit log

Scope: Cleanup
├── Action: Delete temp data
└── Action: Archive records
```

**Example: Multi-Step Process**

```
Flow: Scheduled - Monthly Leave Report

Scope: Initialize
  Action: Initialize variable - varStartDate
  Action: Initialize variable - varEndDate
  Action: Initialize variable - varReportData

Scope: Collect Data
  Scope: Try - Get Leave Requests
    Action: Get items - Last Month Leaves
    Action: Get items - Employees
    Action: Get items - Departments

  Scope: Catch - Data Collection Error
    Configure run after: Try failed
    Action: Send error notification
    Action: Terminate

Scope: Process Data
  Scope: Try - Calculate Statistics
    Action: Apply to each - Group by department
    Action: Select - Format data
    Action: Create HTML table

  Scope: Catch - Processing Error
    Action: Log error
    Action: Use backup data

Scope: Send Report
  Scope: Try - Email Report
    Action: Send an email (V2)
    Attachments: [Report]

  Scope: Catch - Email Error
    Action: Save to SharePoint instead
    Action: Notify IT

Scope: Finally - Cleanup
  Configure run after: All previous scopes completed
  Action: Update last run timestamp
  Action: Clear temporary variables
```

#### Step 6: Scope for Organization

**Large Flow Without Scopes:**

```
❌ Hard to Read:
Trigger
Get items 1
Get items 2
Compose 1
Compose 2
Condition 1
  Update item 1
  Send email 1
Condition 2
  Update item 2
  Send email 2
Apply to each
  Compose 3
  HTTP request
  Condition 3
    Update item 3
...50 more actions
```

**Same Flow With Scopes:**

```
✅ Easy to Read:
Trigger

Scope: Initialize Variables
  └── [3 initialize actions]

Scope: Validate Input
  └── [5 validation actions]

Scope: Fetch Data
  └── [Data retrieval actions]

Scope: Process Employees
  Apply to each
    └── [Actions inside loop]

Scope: Send Notifications
  └── [Email actions in parallel]

Scope: Update Records
  └── [Update actions]

Scope: Error Handling
  └── [Error logging]

Scope: Cleanup
  └── [Final actions]
```

**Naming Convention:**

```
Format: [Category] - [Purpose]

Examples:
Scope: 01 - Initialize
Scope: 02 - Validate Input
Scope: 03 - Fetch Data
Scope: 04 - Process Leave Requests
Scope: 05 - Send Notifications
Scope: 06 - Update Status
Scope: 07 - Error Handling
Scope: 08 - Cleanup

Number prefix helps with ordering
```

#### Step 7: Scope with Timeouts

**Set Timeout for Long Operations:**

```
Scope: Long Running Process
  Settings:
    Timeout: PT30M (30 minutes)

  Action: Apply to each - Process 10,000 items
    [Complex processing]

If scope times out:
  Configure run after on next scope:
    ☑ has timed out
```

**Handle Timeout:**

```
Scope: Process Large Dataset
  Action: Apply to each (10,000 items)
    [Processing logic]

Scope: Timeout Handler
  Configure run after: Process Large Dataset timed out

  Action: Compose - Timeout Message
  Inputs: Processing timed out after 30 minutes

  Action: Update item - Mark as Partial
  Status: Partially Completed

  Action: Send an email
  Subject: Processing timed out
  Body: Please run manual process or increase timeout
```

#### Step 8: Debugging with Scopes

**Benefits:**

```
✅ See which scope succeeded/failed at a glance
✅ Collapse/expand scopes to focus on issues
✅ result() function shows exactly what failed
✅ Isolate problem areas quickly
```

**Flow Run View:**

```
After flow runs:
✅ Scope: Initialize - Succeeded (collapsed)
✅ Scope: Validate - Succeeded (collapsed)
❌ Scope: Process Data - Failed (expanded)
  ✅ Get items - Succeeded
  ❌ Apply to each - Failed
    ✅ Item 1 processed
    ❌ Item 2 failed
⊘ Scope: Send Report - Skipped (didn't run)
✅ Scope: Error Handler - Succeeded (expanded)
```

**Add Debug Scope:**

```
Scope: DEBUG - Log All Variables
  (Only enable during troubleshooting)

  Action: Compose - Current State
  Inputs:
  {
    "varEmployeeID": "@{variables('varEmployeeID')}",
    "varLeaveType": "@{variables('varLeaveType')}",
    "varStartDate": "@{variables('varStartDate')}",
    "varEndDate": "@{variables('varEndDate')}",
    "varTotalDays": "@{variables('varTotalDays')}",
    "TriggerBody": "@{triggerBody()}"
  }

Turn off scope when not debugging:
Scope settings → Is traceable: No
Or delete scope in production
```

#### Step 9: Scope Performance Considerations

**Best Practices:**

```
✅ DO:
- Use parallel branches for independent operations
- Group related actions in scopes
- Use Try-Catch for critical operations
- Configure run after appropriately
- Keep scopes focused (single purpose)

❌ DON'T:
- Nest scopes too deeply (max 3 levels)
- Put single action in scope (unnecessary)
- Use scopes for every action (overkill)
- Create empty scopes
```

**When to Use Scopes:**

```
✅ Use scopes when:
- Need error handling (Try-Catch)
- Actions run in parallel
- Organizing complex flow (10+ actions)
- Need to check group success/failure
- Timeout handling needed

❌ Don't use scopes when:
- Flow has only 3-5 simple actions
- No error handling needed
- Actions must run sequentially
- Over-complicating simple flow
```

#### Step 10: Complete Example - Robust Leave Processing

```
Flow: Auto - Leave Request - Process With Full Error Handling

Trigger: When item is created (Leave Requests)

Scope: 01 - Initialize
  Action: Initialize variable - varEmployeeID
  Value: @{triggerBody()?['EmployeeId']}

  Action: Initialize variable - varProcessingSuccess
  Value: false

  Action: Initialize variable - varErrorMessage
  Value: ""

Scope: 02 - Validate Input
  Scope: Try - Validation
    Action: Condition - Check Required Fields
    If empty(triggerBody()?['EmployeeId']) OR empty(triggerBody()?['LeaveType']):
      Terminate: Failed
      Error: Missing required fields

    Action: Condition - Valid Date Range
    If StartDate > EndDate:
      Terminate: Failed

  Scope: Catch - Validation Error
    Configure run after: Try failed
    Action: Update item
    Status: Validation Failed
    ErrorMessage: @{result('Try_-_Validation')[0]['error']['message']}

    Action: Terminate: Failed

Scope: 03 - Fetch Required Data
  Scope: Try - Get Data
    Action: Get items - Employee
    Branch 1 (Parallel)

    Action: Get items - Manager
    Branch 2 (Parallel)

    Action: Get items - Leave Balance
    Branch 3 (Parallel)

  Scope: Catch - Data Fetch Error
    Action: Log error
    Action: Terminate

Scope: 04 - Business Logic
  Scope: Try - Process Request
    Action: Condition - Check Leave Balance
    If sufficient:
      Action: Update item - Approve Request
      Action: Update leave balance
    Else:
      Action: Update item - Reject Request

  Scope: Catch - Processing Error
    Action: Update item - Error Status
    Action: Send error notification

Scope: 05 - Send Notifications
  Scope: Try - Notifications
    Action: Send email - Employee (Branch 1)
    Action: Send email - Manager (Branch 2)
    Action: Send email - HR (Branch 3)

  Scope: Catch - Notification Error
    Action: Log failed notifications
    (Don't fail entire flow if email fails)

Scope: 06 - Finally - Complete Processing
  Configure run after: All previous scopes completed (success or failure)

  Action: Compose - Processing Summary
  Inputs:
  {
    "RequestID": "@{triggerBody()?['ID']}",
    "ProcessedAt": "@{utcNow()}",
    "ValidationSucceeded": "@{equals(result('02_-_Validate_Input')[0]['status'], 'Succeeded')}",
    "ProcessingSucceeded": "@{equals(result('04_-_Business_Logic')[0]['status'], 'Succeeded')}",
    "NotificationsSent": "@{equals(result('05_-_Send_Notifications')[0]['status'], 'Succeeded')}"
  }

  Action: Create item - Audit Log
  List: Processing Audit
  Details: @{outputs('Compose_-_Processing_Summary')}
```

### Assignment Deliverable

✅ **Create flow with Try-Catch-Finally:**

- Try scope with 5 actions
- Catch scope with error handling
- Finally scope with cleanup
- Test both success and failure paths
- Document results

✅ **Create flow with parallel branches:**

- 4 parallel operations
- Measure execution time
- Compare with sequential execution
- Show time savings

✅ **Refactor existing complex flow:**

- Take flow with 15+ actions
- Organize into logical scopes
- Add error handling
- Improve readability
- Before/after comparison

### Knowledge Check

- [ ] What's a scope action?
- [ ] How do you implement Try-Catch?
- [ ] What's "Configure run after"?
- [ ] How do you access scope results?
- [ ] What's result() function?
- [ ] How do parallel branches work?
- [ ] When should you use scopes?
- [ ] How do you nest scopes?
- [ ] What's scope timeout?
- [ ] How do scopes improve debugging?

### Resources

- [Microsoft Learn: Scope action](https://learn.microsoft.com/en-us/power-automate/error-handling#scope-action)
- [Microsoft Learn: Configure run after](https://learn.microsoft.com/en-us/power-automate/error-handling#configure-run-after)
- [Microsoft Learn: Error handling](https://learn.microsoft.com/en-us/power-automate/error-handling)

---

## Module 30: Child Flows (2 hours)

### Learning Objectives

- Create reusable child flows
- Call child flows from parent flows
- Pass parameters between flows
- Return values from child flows
- Design modular flow architecture
- Implement flow libraries

### Step-by-Step Instructions

#### Step 1: Understanding Child Flows

**What is a Child Flow?**

```
Child Flow = Reusable flow called by other flows

Like a function in programming:
- Receives input parameters
- Performs specific task
- Returns output values
- Can be called multiple times
- Reduces duplication
```

**Benefits:**

```
✅ Reusability: Write once, use in many flows
✅ Maintainability: Update logic in one place
✅ Organization: Break complex flows into modules
✅ Testing: Test components independently
✅ Clarity: Each child flow has single purpose
✅ Team collaboration: Different people build different modules
```

**Use Cases:**

```
Common child flows:
- Calculate business days
- Send formatted emails
- Validate data
- Call external APIs
- Format data for display
- Complex calculations
- Data transformations
```

#### Step 2: Create Child Flow

**Create Reusable Flow:**

```
1. Power Automate → Create → Instant cloud flow
2. Flow name: Child - Calculate Business Days
3. Trigger: Power Automate (V2) - When flow is called
4. Click "Create"

Note: Child flows use same trigger as Canvas app flows:
"PowerApps (V2)" or "When a flow step is run"
But called from flows, not apps
```

**Add Input Parameters:**

```
Trigger: PowerApps (V2)

Click "Add an input"

Input 1:
  Type: Date
  Name: StartDate
  Description: Start date for calculation

Input 2:
  Type: Date
  Name: EndDate
  Description: End date for calculation

These parameters passed from parent flow
```

**Build Child Flow Logic:**

```
Child Flow: Calculate Business Days

Trigger: PowerApps (V2)
Inputs: StartDate (Date), EndDate (Date)

Action: Initialize variable - varCurrentDate
Type: String
Value: @{formatDateTime(triggerBody()['date'], 'yyyy-MM-dd')}

Action: Initialize variable - varEndDate
Type: String
Value: @{formatDateTime(triggerBody()['date_1'], 'yyyy-MM-dd')}

Action: Initialize variable - varBusinessDays
Type: Integer
Value: 0

Action: Do until
Condition: @{greaterOrEquals(variables('varCurrentDate'), variables('varEndDate'))}
Limit: Count 365, Timeout PT1H

Actions inside loop:
  Action: Compose - Day of Week
  Inputs: @{dayOfWeek(variables('varCurrentDate'))}

  Action: Condition - Is Weekday
  If dayOfWeek is not equal to 0 (Sunday) AND not equal to 6 (Saturday):
    YES:
      Action: Increment variable - varBusinessDays
      Value: 1

  Action: Set variable - varCurrentDate
  Value: @{formatDateTime(addDays(variables('varCurrentDate'), 1), 'yyyy-MM-dd')}

Action: Respond to PowerApp
Outputs:
  Name: BusinessDays
  Type: Number
  Value: @{variables('varBusinessDays')}
```

**Important Notes:**

```
⚠️ Must include "Respond to PowerApp" or "Respond to a flow"
- Returns values to parent flow
- Without it, parent flow times out
- Can return multiple values

✅ Return format:
Respond to PowerApp/Flow
Output 1: Name, Type, Value
Output 2: Name, Type, Value
...
```

#### Step 3: Call Child Flow from Parent

**Parent Flow Setup:**

```
Flow: Auto - Leave Request - Process With Business Days

Trigger: When item is created (Leave Requests)

Action: Get items - Leave Request Details
Get: StartDate, EndDate

Action: Run a Child Flow
Flow: Child - Calculate Business Days
StartDate: @{body('Get_items')?['value'][0]?['StartDate']}
EndDate: @{body('Get_items')?['value'][0]?['EndDate']}

Action: Compose - Business Days Result
Inputs: @{outputs('Run_a_Child_Flow')?['businessdays']}

Action: Update item
TotalBusinessDays: @{outputs('Run_a_Child_Flow')?['businessdays']}
```

**Find Child Flows:**

```
In parent flow:
1. Add new action
2. Search: Flow name or "child flow"
3. Category: "My flows" or "Flows"
4. Select your child flow
5. Fill in required parameters
```

**Access Child Flow Output:**

```
Syntax:
@{outputs('Action_Name')?['outputName']}

Example:
Child flow returns:
- BusinessDays
- IsValid
- Message

Access in parent:
@{outputs('Run_a_Child_Flow')?['businessdays']}
@{outputs('Run_a_Child_Flow')?['isvalid']}
@{outputs('Run_a_Child_Flow')?['message']}
```

#### Step 4: Multiple Output Values

**Child Flow with Multiple Returns:**

```
Child Flow: Validate Employee

Trigger: PowerApps (V2)
Input:
  Type: Text
  Name: EmployeeEmail

Action: Get items - Find Employee
Filter: Email eq '@{triggerBody()['text']}'

Action: Condition - Employee Exists
If length > 0:
  YES Branch:
    Action: Compose - Employee Data
    Inputs: @{first(body('Get_items')?['value'])}

    Action: Respond to PowerApp
    Outputs:
      IsValid: true (Boolean)
      EmployeeID: @{first(body('Get_items')?['value'])?['ID']} (Number)
      EmployeeName: @{first(body('Get_items')?['value'])?['Title']} (Text)
      Department: @{first(body('Get_items')?['value'])?['Department']} (Text)
      ManagerID: @{first(body('Get_items')?['value'])?['ManagerID']} (Number)
      Message: Employee found successfully (Text)

  NO Branch:
    Action: Respond to PowerApp
    Outputs:
      IsValid: false
      EmployeeID: 0
      EmployeeName: ""
      Department: ""
      ManagerID: 0
      Message: Employee not found

⚠️ Important: Return same outputs in both branches
```

**Parent Flow Using Multiple Outputs:**

```
Action: Run a Child Flow - Validate Employee
EmployeeEmail: @{triggerBody()?['EmployeeEmail']}

Action: Condition - Check If Valid
If @{outputs('Run_a_Child_Flow_-_Validate_Employee')?['isvalid']} equals true:
  YES:
    Set variable - varEmployeeID
    Value: @{outputs('Run_a_Child_Flow_-_Validate_Employee')?['employeeid']}

    Set variable - varEmployeeName
    Value: @{outputs('Run_a_Child_Flow_-_Validate_Employee')?['employeename']}

    Set variable - varDepartment
    Value: @{outputs('Run_a_Child_Flow_-_Validate_Employee')?['department']}

    Continue processing...

  NO:
    Notify: @{outputs('Run_a_Child_Flow_-_Validate_Employee')?['message']}
    Terminate
```

#### Step 5: Child Flow for Email Templates

**Child Flow: Send Formatted Leave Notification**

```
Trigger: PowerApps (V2)
Inputs:
  - RecipientEmail (Text)
  - EmployeeName (Text)
  - LeaveType (Text)
  - StartDate (Date)
  - EndDate (Date)
  - TotalDays (Number)
  - Comments (Text)
  - Status (Text)

Action: Compose - Email Body HTML
Inputs:
<!DOCTYPE html>
<html>
<head>
  <style>
    body { font-family: Arial, sans-serif; }
    .header { background-color: #0078d4; color: white; padding: 20px; }
    .content { padding: 20px; }
    table { border-collapse: collapse; width: 100%; }
    td { padding: 10px; border-bottom: 1px solid #ddd; }
    .label { font-weight: bold; width: 150px; }
    .status-pending { color: #ff8c00; }
    .status-approved { color: #107c10; }
    .status-rejected { color: #d13438; }
  </style>
</head>
<body>
  <div class="header">
    <h2>Leave Request Notification</h2>
  </div>
  <div class="content">
    <p>Hello,</p>
    <p>A leave request has been submitted with the following details:</p>
    <table>
      <tr>
        <td class="label">Employee:</td>
        <td>@{triggerBody()['text_1']}</td>
      </tr>
      <tr>
        <td class="label">Leave Type:</td>
        <td>@{triggerBody()['text_2']}</td>
      </tr>
      <tr>
        <td class="label">Start Date:</td>
        <td>@{formatDateTime(triggerBody()['date'], 'MMMM dd, yyyy')}</td>
      </tr>
      <tr>
        <td class="label">End Date:</td>
        <td>@{formatDateTime(triggerBody()['date_1'], 'MMMM dd, yyyy')}</td>
      </tr>
      <tr>
        <td class="label">Total Days:</td>
        <td>@{triggerBody()['number']}</td>
      </tr>
      <tr>
        <td class="label">Status:</td>
        <td class="status-@{toLower(triggerBody()['text_3'])}">
          @{triggerBody()['text_3']}
        </td>
      </tr>
      <tr>
        <td class="label">Comments:</td>
        <td>@{triggerBody()['text_4']}</td>
      </tr>
    </table>
    <p>Please review and take appropriate action.</p>
    <p>Best regards,<br>Leave Management System</p>
  </div>
</body>
</html>

Action: Send an email (V2)
To: @{triggerBody()['text']}
Subject: Leave Request - @{triggerBody()['text_1']} - @{triggerBody()['text_2']}
Body: @{outputs('Compose_-_Email_Body_HTML')}

Action: Respond to PowerApp
Outputs:
  EmailSent: true
  Message: Email sent successfully to @{triggerBody()['text']}
```

**Use from Multiple Parent Flows:**

```
Parent Flow 1: Auto - Leave Request - On Create
  Action: Run Child - Send Formatted Leave Notification
  RecipientEmail: @{body('Get_manager')?['Email']}
  EmployeeName: @{body('Get_employee')?['Title']}
  [other params...]

Parent Flow 2: Auto - Leave Request - On Approval
  Action: Run Child - Send Formatted Leave Notification
  RecipientEmail: @{triggerBody()?['EmployeeEmail']}
  [other params with approval message...]

Parent Flow 3: Manual - Leave Request - Send Reminder
  Apply to each: Pending requests
    Action: Run Child - Send Formatted Leave Notification
    [params for reminder...]

✅ One email template, used in 3 different flows!
```

#### Step 6: Child Flow for Data Validation

**Child Flow: Validate Leave Request**

```
Trigger: PowerApps (V2)
Inputs:
  - EmployeeID (Number)
  - LeaveType (Text)
  - StartDate (Date)
  - EndDate (Date)
  - RequestedDays (Number)

Action: Initialize variable - varValidationErrors
Type: Array
Value: []

Action: Condition - Start Date Not in Past
If @{less(ticks(triggerBody()['date']), ticks(utcNow()))}:
  YES:
    Append to array - varValidationErrors
    Value: Start date cannot be in the past

Action: Condition - End After Start
If @{less(ticks(triggerBody()['date_1']), ticks(triggerBody()['date']))}:
  YES:
    Append: End date must be after start date

Action: Condition - Days Not Exceed Max
If @{greater(triggerBody()['number_2'], 30)}:
  YES:
    Append: Cannot request more than 30 days at once

Action: Get items - Check Leave Balance
Filter: EmployeeId eq @{triggerBody()['number']}
        and LeaveType eq '@{triggerBody()['text']}'

Action: Condition - Balance Exists
If length = 0:
  Append: No leave balance found for this leave type
Else:
  Action: Compose - Available Balance
  Inputs: @{sub(
    first(body('Get_items')?['value'])?['TotalDays'],
    first(body('Get_items')?['value'])?['UsedDays']
  )}

  Action: Condition - Sufficient Balance
  If @{less(outputs('Compose_-_Available_Balance'), triggerBody()['number_2'])}:
    Append: Insufficient leave balance. Available: @{outputs('Compose')} days

Action: Get items - Check Conflicting Requests
Filter: EmployeeId eq @{triggerBody()['number']}
        and Status ne 'Rejected'
        and StartDate le @{triggerBody()['date_1']}
        and EndDate ge @{triggerBody()['date']}

Action: Condition - Has Conflicts
If @{greater(length(body('Get_items_-_Check_Conflicting')?['value']), 0)}:
  Append: Conflicting leave request found

Action: Compose - Is Valid
Inputs: @{equals(length(variables('varValidationErrors')), 0)}

Action: Respond to PowerApp
Outputs:
  IsValid: @{outputs('Compose_-_Is_Valid')}  (Boolean)
  ErrorCount: @{length(variables('varValidationErrors'))}  (Number)
  Errors: @{join(variables('varValidationErrors'), '; ')}  (Text)
  ErrorsArray: @{variables('varValidationErrors')}  (Text - JSON stringified)
```

**Parent Flow Using Validation:**

```
Action: Run Child - Validate Leave Request
EmployeeID: @{triggerBody()?['EmployeeId']}
LeaveType: @{triggerBody()?['LeaveType']}
StartDate: @{triggerBody()['StartDate']}
EndDate: @{triggerBody()['EndDate']}
RequestedDays: @{triggerBody()['TotalDays']}

Action: Condition - Validation Passed
If @{outputs('Run_Child_-_Validate')?['isvalid']} equals true:
  YES:
    Continue with processing...
  NO:
    Action: Update item
    Status: Validation Failed
    ValidationErrors: @{outputs('Run_Child_-_Validate')?['errors']}

    Action: Send an email - Notify Employee
    Subject: Leave Request Validation Failed
    Body:
    Your leave request could not be processed due to the following errors:

    @{outputs('Run_Child_-_Validate')?['errors']}

    Please correct these issues and submit again.

    Action: Terminate
```

#### Step 7: Child Flow Library Pattern

**Organize Child Flows:**

```
📁 Solution: Leave Management System

📁 Child Flows - Utilities:
  - Child - Calculate Business Days
  - Child - Format Date Range
  - Child - Generate Unique ID

📁 Child Flows - Validation:
  - Child - Validate Leave Request
  - Child - Validate Employee
  - Child - Check Leave Balance

📁 Child Flows - Notifications:
  - Child - Send Formatted Email
  - Child - Send SMS Notification
  - Child - Create Teams Message

📁 Child Flows - Data Operations:
  - Child - Get Employee Details
  - Child - Update Leave Balance
  - Child - Log Audit Entry

📁 Parent Flows:
  - Auto - Leave Request - On Create
  - Auto - Leave Request - On Approval
  - Scheduled - Leave Balance - Update Monthly
```

**Naming Convention:**

```
Format: Child - [Category] - [Purpose]

Examples:
Child - Validation - Employee Email
Child - Calculation - Business Days
Child - Notification - Email Template
Child - Data - Get Employee Full Profile
Child - Utility - Format Currency
Child - API - Call External System
```

#### Step 8: Error Handling in Child Flows

**Child Flow with Try-Catch:**

```
Child Flow: Get Employee Full Profile

Trigger: PowerApps (V2)
Input: EmployeeEmail (Text)

Scope: Try
  Action: Get items - Employee
  Action: Get items - Manager
  Action: Get items - Department
  Action: Get items - Leave Balances

  Action: Compose - Full Profile
  Inputs: [Combined data]

  Action: Respond to PowerApp
  Outputs:
    Success: true
    ProfileData: @{outputs('Compose_-_Full_Profile')}
    ErrorMessage: ""

Scope: Catch
  Configure run after: Try failed

  Action: Compose - Error Details
  Inputs: @{result('Try')[0]['error']['message']}

  Action: Respond to PowerApp
  Outputs:
    Success: false
    ProfileData: ""
    ErrorMessage: @{outputs('Compose_-_Error_Details')}
```

**Parent Flow Handling Child Errors:**

```
Action: Run Child - Get Employee Full Profile
EmployeeEmail: @{triggerBody()?['Email']}

Action: Condition - Child Flow Succeeded
If @{outputs('Run_Child')?['success']} equals true:
  YES:
    Use data: @{outputs('Run_Child')?['profiledata']}
  NO:
    Log error: @{outputs('Run_Child')?['errormessage']}
    Send notification
    Terminate
```

#### Step 9: Performance Considerations

**When to Use Child Flows:**

```
✅ Use child flows when:
- Logic used in 3+ parent flows (reusability)
- Complex operation (50+ actions)
- Need to test component independently
- Different team owns component
- Want to version component separately

❌ Don't use child flows when:
- Logic only used once
- Simple operation (< 10 actions)
- Need fastest possible performance
- Child flow adds unnecessary complexity
```

**Performance Impact:**

```
Each child flow call adds:
- 1-2 seconds network latency
- Separate flow run (counts toward limits)
- Additional action count

Example:
Parent with 3 child flow calls:
- Base flow: 10 actions
- Child 1: 20 actions
- Child 2: 15 actions
- Child 3: 10 actions
Total actions: 55 actions across 4 flow runs

vs.

All in parent: 55 actions in 1 flow run (faster but less maintainable)

Balance: Reusability vs Performance
```

**Optimization Tips:**

```
✅ Combine multiple child flow calls if possible
❌ Don't call child flow inside Apply to each loop (if 1000 items = 1000 child calls!)

Better pattern:
Collect data → Pass entire collection to child → Child processes batch
```

#### Step 10: Complete Example - Modular Leave System

```
Parent Flow: Auto - Leave Request - Complete Processing

Trigger: When item is created (Leave Requests)

Scope: 01 - Validate Request
  Action: Run Child - Validate Leave Request
  [Parameters from trigger]

  Action: Condition - Valid
  If not valid:
    Update item with errors
    Terminate

Scope: 02 - Get Employee Data
  Action: Run Child - Get Employee Full Profile
  EmployeeEmail: @{triggerBody()?['CreatedBy/Email']}

  Set variables with employee data

Scope: 03 - Calculate Business Days
  Action: Run Child - Calculate Business Days
  StartDate: @{triggerBody()?['StartDate']}
  EndDate: @{triggerBody()?['EndDate']}

  Set variable: varBusinessDays

Scope: 04 - Update Records
  Action: Update item - Leave Request
  TotalBusinessDays: @{variables('varBusinessDays')}
  Status: Pending

  Action: Run Child - Update Leave Balance
  [Parameters]

Scope: 05 - Send Notifications
  Action: Run Child - Send Formatted Email (Branch 1)
  RecipientEmail: [Employee email]
  [Other parameters]

  Action: Run Child - Send Formatted Email (Branch 2)
  RecipientEmail: [Manager email]
  [Other parameters]

Scope: 06 - Audit Logging
  Action: Run Child - Log Audit Entry
  FlowName: @{workflow()?['name']}
  Action: Leave Request Created
  Details: [Summary]

Result: Clean, modular, maintainable flow architecture!
```

### Assignment Deliverable

✅ **Create 3 child flows:**

1. Calculate Business Days (with inputs/outputs)
2. Send Formatted Email (with email template)
3. Validate Data (with error array)

✅ **Create parent flow using all 3 child flows:**

- Call each child flow
- Use returned values
- Handle errors from child flows
- Test complete flow

✅ **Document child flow library:**

- Purpose of each child flow
- Input parameters
- Output values
- Usage examples
- Error handling

### Knowledge Check

- [ ] What's a child flow?
- [ ] When should you use child flows?
- [ ] How do you pass parameters?
- [ ] How do you return values?
- [ ] What trigger type do child flows use?
- [ ] How do you call child flows?
- [ ] How do you handle errors in child flows?
- [ ] What's the performance impact?
- [ ] How do you organize child flow libraries?
- [ ] When NOT to use child flows?

### Resources

- [Microsoft Learn: Child flows](https://learn.microsoft.com/en-us/power-automate/create-child-flows)
- [Microsoft Learn: Call child flows](https://learn.microsoft.com/en-us/power-automate/call-child-flows)
- [Blog: Flow modularization](https://powerusers.microsoft.com/t5/Power-Automate-Community-Blog/bg-p/FlowBlog)

---

## Stage 4 Part 4 Progress Summary

### Modules Completed in This Section:

- ✅ Module 29: Scope Actions (1 hour)
- ✅ Module 30: Child Flows (2 hours)

### Still to Complete in Part 4:

- Module 31: Run-as User (1 hour)
- Module 32: Flow Error Handling (2 hours)

### Cumulative Stage 4 Progress: 14.5 hours of 25.5 hours completed

**Continue to Part 4B for remaining modules...**

---

**Type "NEXT" to continue with Run-as User and Flow Error Handling.**
