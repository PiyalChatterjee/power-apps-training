# Power Platform Training Manual - Stage 4: Cloud Flows (Power Automate)

## Overview

This stage covers Power Automate Cloud Flows, from basic triggers and actions to advanced patterns including Canvas app integration, error handling, connection references, child flows, and run-as user configurations. **Duration: 25.5 hours**

---

## Module 22: Cloud Flow Triggers (2 hours)

### Learning Objectives

- Understand different trigger types
- Implement automated triggers
- Create instant triggers
- Use scheduled triggers
- Configure trigger conditions
- Handle trigger outputs

### Step-by-Step Instructions

#### Step 1: Understanding Flow Triggers

**What is a Trigger?**

- Starting point of every flow
- Defines when/how flow runs
- Provides initial data to flow
- Only ONE trigger per flow

**Three Types of Triggers:**

1. **Automated Cloud Flow**

   - Triggered by an event
   - Example: When item is created in SharePoint
   - Runs automatically when event occurs

2. **Instant Cloud Flow (Manual)**

   - Triggered by user action
   - Example: Button in Power Apps, Teams
   - User initiates the flow

3. **Scheduled Cloud Flow**
   - Triggered by time/schedule
   - Example: Daily at 9 AM
   - Runs on defined schedule

#### Step 2: Create Automated Cloud Flow

**Scenario: Send notification when new leave request is created**

**Steps:**

1. Go to **flow.microsoft.com**
2. Click **Create** → **Automated cloud flow**
3. Flow name: `Leave Request Notification`
4. Skip trigger selection (configure manually)
5. Click **Create**

**Add SharePoint Trigger:**

1. Search for: `SharePoint`
2. Select trigger: **When an item is created**
3. Configure:
   - **Site Address**: Select your SharePoint site
   - **List Name**: Leave Requests

**Trigger produces output:**

```
{
  "ID": 123,
  "Title": "Annual Leave",
  "Employee": "John Smith",
  "StartDate": "2025-11-20",
  "EndDate": "2025-11-25",
  "Status": "Pending",
  "CreatedBy": {...}
}
```

**Add Action: Send Email**

1. Click **New step**
2. Search: `Office 365 Outlook`
3. Select: **Send an email (V2)**
4. Configure:
   ```
   To: Get Manager Email (add action to get this)
   Subject: New Leave Request from [Employee Name]
   Body:
     Employee: [Employee Name]
     Leave Type: [Type]
     Start Date: [StartDate]
     End Date: [EndDate]
     Total Days: [TotalDays]

     Click here to approve: [Deep link to app]
   ```

**Use Dynamic Content:**

- Click in field
- Select from trigger output
- Example: `Employee Display Name` from trigger

**Save and Test:**

1. Click **Save**
2. Create new item in SharePoint list
3. Check flow run history
4. Verify email received

#### Step 3: Trigger Conditions

**Run flow only for specific conditions:**

**Example: Only run for leave requests > 5 days**

1. Click **...** on trigger
2. Select **Settings**
3. Click **Add** under "Trigger Conditions"
4. Enter expression:
   ```
   @greater(triggerOutputs()?['body/TotalDays'], 5)
   ```

**Common Trigger Conditions:**

**Status equals "Pending":**

```
@equals(triggerOutputs()?['body/Status'], 'Pending')
```

**Created by specific user:**

```
@equals(triggerOutputs()?['body/Author/Email'], 'john@company.com')
```

**Amount greater than 1000:**

```
@greater(triggerOutputs()?['body/Amount'], 1000)
```

**Multiple conditions (AND):**

```
@and(
  equals(triggerOutputs()?['body/Status'], 'Pending'),
  greater(triggerOutputs()?['body/TotalDays'], 5)
)
```

**Multiple conditions (OR):**

```
@or(
  equals(triggerOutputs()?['body/Priority'], 'High'),
  equals(triggerOutputs()?['body/Priority'], 'Urgent')
)
```

#### Step 4: Create Instant Cloud Flow (Manual Trigger)

**Scenario: Allow manager to manually send reminder**

**Steps:**

1. Create → **Instant cloud flow**
2. Flow name: `Send Leave Reminder`
3. Trigger: **Manually trigger a flow**
4. Click **Create**

**Add Input Parameters:**

1. Click on trigger
2. Click **Add an input**
3. Select input type:
   - **Text**: For simple text input
   - **Email**: For email addresses
   - **Number**: For numeric values
   - **Date**: For dates
   - **Boolean**: For Yes/No
   - **File**: For file uploads

**Example Inputs:**

```
Input 1:
  Type: Text
  Name: EmployeeName
  Description: Name of employee to remind

Input 2:
  Type: Email
  Name: EmployeeEmail
  Description: Employee email address

Input 3:
  Type: Date
  Name: DueDate
  Description: Leave request due date
```

**Use Inputs in Actions:**

```
Send an email (V2)
  To: @{triggerBody()?['EmployeeEmail']}
  Subject: Reminder: Leave Request Due
  Body:
    Hi @{triggerBody()?['EmployeeName']},

    This is a reminder that your leave request is due on:
    @{triggerBody()?['DueDate']}

    Please complete it at your earliest convenience.
```

**Trigger from Power Apps:**
(Covered in Module 26)

**Trigger from Power BI:**

- Add button to report
- Configure flow connection
- Pass parameters from report

#### Step 5: Create Scheduled Cloud Flow

**Scenario: Daily summary email of pending approvals**

**Steps:**

1. Create → **Scheduled cloud flow**
2. Flow name: `Daily Approval Summary`
3. Configure schedule:
   - **Starting**: Tomorrow at 9:00 AM
   - **Repeat every**: 1 Day
   - **Time zone**: Select your timezone
4. Click **Create**

**Schedule Options:**

**Every X minutes:**

```
Repeat every: 15 Minutes
```

**Specific times daily:**

```
Repeat every: 1 Day
At these hours: 9, 13, 17 (9am, 1pm, 5pm)
At these minutes: 0
```

**Weekdays only:**

```
Repeat every: 1 Week
On these days: Monday, Tuesday, Wednesday, Thursday, Friday
At time: 09:00
```

**First Monday of month:**

```
Repeat every: 1 Month
On these days: Monday
On occurrence: First
At time: 09:00
```

**Complex Schedule Example:**

```
Trigger: Recurrence
Frequency: Week
Interval: 1
On these days: Monday, Wednesday, Friday
At these hours: 9, 14
At these minutes: 0, 30

Result: Runs Mon/Wed/Fri at 9:00, 9:30, 14:00, 14:30
```

**Add Condition to Run on Business Days Only:**

```
Add action: Compose
Inputs: @{dayOfWeek(utcNow())}

Add action: Condition
If: @{outputs('Compose')} is not equal to 0  (Sunday)
And: @{outputs('Compose')} is not equal to 6  (Saturday)
Then: Continue with flow actions
Else: Terminate with success
```

#### Step 6: When an Item is Created or Modified

**Use Case: Track all changes to leave requests**

**Trigger:**

- **When an item is created or modified**
- Site: Your SharePoint site
- List: Leave Requests

**Important Settings:**

**Include Attachments:**

- Yes: If you need attachment content
- No: For better performance

**Limit Columns by View:**

- Select specific view
- Only those columns trigger flow
- Better performance

**Problem: Trigger Loops**

**Issue:**

```
Flow triggered → Updates item → Triggers same flow → Updates item → Loop!
```

**Solution 1: Check if specific field changed**

```
Add action: Get item
ID: @{triggerOutputs()?['body/ID']}

Add condition:
@{triggerOutputs()?['body/Status']} is not equal to @{outputs('Get_item')?['body/Status']}

If true: Continue
If false: Terminate
```

**Solution 2: Use trigger condition**

```
Trigger Settings → Trigger Conditions:
@not(equals(triggerOutputs()?['body/Editor/Email'], 'flowbot@company.com'))
```

**Solution 3: Use flag field**

```
1. Add "ProcessedByFlow" (Yes/No) column to list
2. Trigger condition: ProcessedByFlow equals No
3. In flow: Update ProcessedByFlow to Yes
4. Won't trigger again
```

#### Step 7: Multiple Triggers (Power Automate Alternatives)

**Can't have multiple triggers in ONE flow, but can:**

**Option 1: Multiple flows → One child flow**

```
Flow 1: When item created → Call child flow
Flow 2: When item modified → Call child flow
Flow 3: Manual trigger → Call child flow

Child Flow: Contains main logic
```

**Option 2: Use "Trigger Condition" to differentiate**

```
Flow 1 Trigger Condition:
@equals(triggerOutputs()?['body/Status'], 'Pending')

Flow 2 Trigger Condition:
@equals(triggerOutputs()?['body/Status'], 'Approved')
```

#### Step 8: Webhook Triggers

**For services that support webhooks:**

**Example: Microsoft Forms**

```
Trigger: When a new response is submitted
Form: Select your form

Outputs:
- Response ID
- Submission time
- Responder's email
- All form answers
```

**Example: Dynamics 365 / Dataverse**

```
Trigger: When a record is created, updated or deleted
Environment: Select environment
Table name: Contacts
Scope: Organization

Filter rows:
firstname eq 'John'
```

**Example: HTTP Request (Custom Webhook)**

```
Trigger: When a HTTP request is received
Method: POST
Schema: Define JSON schema

This generates a URL:
https://prod-123.eastus.logic.azure.com:443/workflows/abc.../triggers/manual/paths/invoke?...

External system posts to this URL to trigger flow
```

#### Step 9: Trigger Output Reference

**Accessing Trigger Data:**

**Dynamic content (UI):**

- Click in field → Select from list

**Expressions (code):**

```
Full trigger output:
@{triggerOutputs()}

Specific field:
@{triggerOutputs()?['body/FieldName']}

Nested field:
@{triggerOutputs()?['body/Employee/DisplayName']}

Array item:
@{triggerOutputs()?['body/Approvers'][0]/Email'}
```

**Common Trigger Outputs:**

**SharePoint:**

```
ID: @{triggerOutputs()?['body/ID']}
Title: @{triggerOutputs()?['body/Title']}
Created: @{triggerOutputs()?['body/Created']}
Author Email: @{triggerOutputs()?['body/Author/Email']}
```

**Manual trigger:**

```
Text input: @{triggerBody()?['text']}
Email input: @{triggerBody()?['email']}
Number input: @{triggerBody()?['number']}
```

**Recurrence:**

```
Start time: @{triggerOutputs()?['startTime']}
```

#### Step 10: Trigger Troubleshooting

**Flow not triggering?**

**Checklist:**

✅ **Flow is turned ON**

- Check flow status in flow.microsoft.com
- Look for "On" or "Off" toggle

✅ **Connections are valid**

- Check connection status
- Re-authenticate if needed

✅ **Trigger conditions are met**

- Review trigger condition expression
- Test with data that meets condition

✅ **Permissions**

- Flow owner has access to data source
- Service account has permissions

✅ **SharePoint list permissions**

- Flow needs Read access minimum
- Check list permissions

**Flow triggering too often?**

✅ **Check trigger sensitivity**

- "When modified" triggers on ANY change
- Use "Limit Columns by View"
- Add trigger conditions

✅ **Prevent loops**

- Don't update same item that triggered flow
- Use processed flag
- Check modified by user

**Delay in triggering?**

✅ **Normal delays:**

- SharePoint: Up to 5 minutes
- Most connectors: 1-2 minutes
- Not instant!

✅ **Check flow run history:**

- See when flow actually triggered
- Compare to when event occurred

### Assignment Deliverable

✅ **Create 3 flows with different triggers:**

1. **Automated Flow**: "New Leave Request Notification"

   - Trigger: When item created in Leave Requests
   - Action: Send email to manager
   - Include trigger condition (only for > 3 days)

2. **Instant Flow**: "Send Custom Reminder"

   - Trigger: Manual trigger with inputs
   - Inputs: Employee name, email, message
   - Action: Send personalized email

3. **Scheduled Flow**: "Daily Pending Summary"
   - Trigger: Daily at 9 AM, weekdays only
   - Action: Get all pending approvals
   - Send summary email to all managers

✅ **Test each flow:**

- Trigger the flow
- Verify it runs successfully
- Check outputs
- Document run time and results

✅ **Documentation:**

- When each trigger type should be used
- How to access trigger outputs
- Common trigger conditions
- Troubleshooting tips

### Knowledge Check

- [ ] What are the three types of flow triggers?
- [ ] Can a flow have multiple triggers?
- [ ] How do you add conditions to a trigger?
- [ ] What's the expression to access trigger output?
- [ ] How do you prevent trigger loops?
- [ ] What's the difference between Created and Modified triggers?
- [ ] How do you schedule a flow for weekdays only?
- [ ] What are manual trigger inputs used for?
- [ ] How long is typical trigger delay?
- [ ] How do you access nested trigger data?

### Trigger Quick Reference

| Trigger Type           | Use Case        | Example                     |
| ---------------------- | --------------- | --------------------------- |
| **When item created**  | New records     | New employee onboarding     |
| **When item modified** | Track changes   | Status update notifications |
| **Manual**             | User-initiated  | Button in Power Apps        |
| **Recurrence**         | Scheduled tasks | Daily reports               |
| **For selected item**  | Bulk operations | Process multiple items      |
| **HTTP request**       | Webhook/API     | External system integration |

### Resources

- [Microsoft Learn: Cloud flow triggers](https://learn.microsoft.com/en-us/power-automate/triggers-introduction)
- [Microsoft Learn: Trigger conditions](https://learn.microsoft.com/en-us/power-automate/triggers-conditions)
- [Microsoft Learn: Automated flows](https://learn.microsoft.com/en-us/power-automate/get-started-logic-flow)
- [Microsoft Learn: Scheduled flows](https://learn.microsoft.com/en-us/power-automate/run-scheduled-tasks)
- [Microsoft Learn: Manual triggers](https://learn.microsoft.com/en-us/power-automate/introduction-to-button-flows)

---

## Module 23: Flow Actions and Expressions (3 hours)

### Learning Objectives

- Use common flow actions
- Write expressions for data manipulation
- Work with variables
- Format dates and text
- Handle JSON and arrays
- Build complex logic

### Step-by-Step Instructions

#### Step 1: Essential Actions

**1. Get Items (SharePoint/Dataverse)**

**Purpose:** Retrieve multiple items from a list

```
Action: Get items
Site Address: [Your site]
List Name: Employees
Filter Query: Status eq 'Active'
Top Count: 100
```

**Filter Query Examples:**

```
Single condition:
Department eq 'IT'

Multiple conditions (AND):
Status eq 'Active' and Department eq 'IT'

Multiple conditions (OR):
Status eq 'Active' or Status eq 'Pending'

Greater than:
Salary gt 50000

Date comparison:
StartDate ge '2025-01-01'

Contains:
substringof('John', Title)
```

**2. Get Item (Single Record)**

```
Action: Get item
Site Address: [Your site]
List Name: Employees
Id: @{triggerOutputs()?['body/ID']}
```

**3. Create Item**

```
Action: Create item
Site Address: [Your site]
List Name: Employees
Title: @{triggerBody()?['FullName']}
Department: IT
Salary: 75000
HireDate: @{utcNow()}
```

**4. Update Item**

```
Action: Update item
Site Address: [Your site]
List Name: Employees
Id: @{variables('varEmployeeID')}
Title: @{concat(body('Get_item')?['FirstName'], ' ', body('Get_item')?['LastName'])}
Status: Active
```

**5. Send Email**

```
Action: Send an email (V2)
To: @{body('Get_item')?['Email']}
Subject: Your Leave Request Status
Body:
  <p>Dear @{body('Get_item')?['FirstName']},</p>
  <p>Your leave request has been <strong>@{body('Get_leave')?['Status']}</strong>.</p>
  <p>Details:<br/>
  Start Date: @{formatDateTime(body('Get_leave')?['StartDate'], 'MMM dd, yyyy')}<br/>
  End Date: @{formatDateTime(body('Get_leave')?['EndDate'], 'MMM dd, yyyy')}<br/>
  Total Days: @{body('Get_leave')?['TotalDays']}</p>
Importance: High
```

#### Step 2: Variables

**Initialize Variable:**

```
Action: Initialize variable
Name: varEmployeeCount
Type: Integer
Value: 0
```

**Variable Types:**

```
String: Text value
varName = "John Smith"

Integer: Whole number
varCount = 10

Float: Decimal number
varAmount = 150.50

Boolean: True/False
varIsApproved = true

Array: List of items
varEmployees = ["John", "Sarah", "Mike"]

Object: JSON object
varEmployee = {"Name": "John", "ID": 123}
```

**Set Variable:**

```
Action: Set variable
Name: varEmployeeCount
Value: @{add(variables('varEmployeeCount'), 1)}
```

**Increment Variable:**

```
Action: Increment variable
Name: varCounter
Value: 1
```

**Append to Array:**

```
Action: Append to array variable
Name: varEmployeeList
Value: @{body('Get_item')?['EmployeeName']}
```

**Append to String:**

```
Action: Append to string variable
Name: varMessage
Value: @{concat('Employee: ', body('Get_item')?['Name'], '\n')}
```

#### Step 3: Expressions - Text Functions

**concat() - Join strings:**

```
@{concat('Hello ', 'World')}
Result: "Hello World"

@{concat(body('Get_item')?['FirstName'], ' ', body('Get_item')?['LastName'])}
Result: "John Smith"
```

**toLower() / toUpper() - Change case:**

```
@{toLower('HELLO')}
Result: "hello"

@{toUpper('hello')}
Result: "HELLO"
```

**substring() - Extract part of string:**

```
@{substring('Hello World', 0, 5)}
Result: "Hello"

@{substring(body('Get_item')?['Email'], 0, indexOf(body('Get_item')?['Email'], '@'))}
Result: Username part of email
```

**replace() - Replace text:**

```
@{replace('Hello World', 'World', 'Everyone')}
Result: "Hello Everyone"

@{replace(body('Get_item')?['Phone'], '-', '')}
Result: Remove dashes from phone number
```

**split() - Split string into array:**

```
@{split('John,Sarah,Mike', ',')}
Result: ["John", "Sarah", "Mike"]

@{split(body('Get_item')?['Email'], '@')[0]}
Result: Username part
```

**join() - Join array into string:**

```
@{join(variables('varEmployeeList'), ', ')}
Result: "John, Sarah, Mike"
```

**trim() - Remove whitespace:**

```
@{trim('  Hello World  ')}
Result: "Hello World"
```

**length() - Get string length:**

```
@{length('Hello')}
Result: 5

@{length(body('Get_item')?['Description'])}
Result: Length of description
```

#### Step 4: Expressions - Math Functions

**add() - Addition:**

```
@{add(5, 3)}
Result: 8

@{add(int(body('Get_item')?['Salary']), 10000)}
Result: Salary + 10000
```

**sub() - Subtraction:**

```
@{sub(100, 25)}
Result: 75
```

**mul() - Multiplication:**

```
@{mul(10, 5)}
Result: 50

@{mul(int(body('Get_item')?['TotalDays']), 8)}
Result: Total hours (days * 8)
```

**div() - Division:**

```
@{div(100, 4)}
Result: 25
```

**mod() - Modulus (remainder):**

```
@{mod(10, 3)}
Result: 1
```

**min() / max() - Minimum/Maximum:**

```
@{min(5, 10, 3)}
Result: 3

@{max(5, 10, 3)}
Result: 10
```

**rand() - Random number:**

```
@{rand(1, 100)}
Result: Random number between 1 and 100
```

#### Step 5: Expressions - Date Functions

**utcNow() - Current UTC time:**

```
@{utcNow()}
Result: "2025-11-14T10:30:00.0000000Z"
```

**formatDateTime() - Format date:**

```
@{formatDateTime(utcNow(), 'yyyy-MM-dd')}
Result: "2025-11-14"

@{formatDateTime(utcNow(), 'MMM dd, yyyy')}
Result: "Nov 14, 2025"

@{formatDateTime(utcNow(), 'dddd, MMMM dd, yyyy')}
Result: "Thursday, November 14, 2025"

@{formatDateTime(utcNow(), 'hh:mm tt')}
Result: "10:30 AM"
```

**addDays() - Add days:**

```
@{addDays(utcNow(), 7)}
Result: Date 7 days from now

@{addDays(body('Get_item')?['StartDate'], 30)}
Result: 30 days after start date
```

**addHours() / addMinutes():**

```
@{addHours(utcNow(), 2)}
Result: 2 hours from now

@{addMinutes(utcNow(), 30)}
Result: 30 minutes from now
```

**addToTime() - Add to time:**

```
@{addToTime(utcNow(), 3, 'Month')}
Result: 3 months from now

@{addToTime(utcNow(), 1, 'Year')}
Result: 1 year from now
```

**convertFromUtc() - Convert timezone:**

```
@{convertFromUtc(utcNow(), 'Eastern Standard Time')}
Result: UTC time converted to EST

@{convertFromUtc(utcNow(), 'Pacific Standard Time', 'MMM dd, yyyy hh:mm tt')}
Result: "Nov 14, 2025 02:30 AM" (PST)
```

**dayOfWeek() - Get day of week:**

```
@{dayOfWeek(utcNow())}
Result: 4 (Thursday = 4, Sunday = 0)
```

**dayOfMonth() / month() / year():**

```
@{dayOfMonth(utcNow())}
Result: 14

@{month(utcNow())}
Result: 11

@{year(utcNow())}
Result: 2025
```

**ticks() - Get ticks:**

```
@{ticks(utcNow())}
Result: 638357442000000000 (for sorting/comparison)
```

#### Step 6: Expressions - Logical Functions

**if() - Conditional:**

```
@{if(equals(body('Get_item')?['Status'], 'Active'), 'Yes', 'No')}
Result: "Yes" if Status is Active, otherwise "No"

@{if(greater(int(body('Get_item')?['Salary']), 100000), 'High', 'Normal')}
Result: "High" if salary > 100000, otherwise "Normal"
```

**and() - Logical AND:**

```
@{and(equals(body('Get_item')?['Status'], 'Active'), greater(int(body('Get_item')?['Salary']), 50000))}
Result: true if both conditions true
```

**or() - Logical OR:**

```
@{or(equals(body('Get_item')?['Department'], 'IT'), equals(body('Get_item')?['Department'], 'Engineering'))}
Result: true if either condition true
```

**not() - Logical NOT:**

```
@{not(equals(body('Get_item')?['Status'], 'Inactive'))}
Result: true if Status is NOT Inactive
```

**equals() - Comparison:**

```
@{equals(body('Get_item')?['Status'], 'Approved')}
Result: true if Status equals Approved
```

**greater() / less():**

```
@{greater(int(body('Get_item')?['Age']), 18)}
Result: true if Age > 18

@{less(int(body('Get_item')?['TotalDays']), 5)}
Result: true if TotalDays < 5
```

**greaterOrEquals() / lessOrEquals():**

```
@{greaterOrEquals(int(body('Get_item')?['Salary']), 50000)}
Result: true if Salary >= 50000
```

**empty() / null():**

```
@{empty(body('Get_item')?['MiddleName'])}
Result: true if MiddleName is empty

@{if(empty(body('Get_item')?['Manager']), 'No Manager', body('Get_item')?['Manager'])}
Result: "No Manager" if Manager field is empty
```

#### Step 7: Expressions - Array Functions

**first() / last():**

```
@{first(body('Get_items')?['value'])}
Result: First item in array

@{last(body('Get_items')?['value'])}
Result: Last item in array
```

**length() - Array length:**

```
@{length(body('Get_items')?['value'])}
Result: Number of items in array
```

**join() - Join array:**

```
@{join(variables('varEmployeeList'), '; ')}
Result: "John; Sarah; Mike"
```

**contains() - Check if contains:**

```
@{contains(variables('varEmployeeList'), 'John')}
Result: true if array contains 'John'
```

**indexOf() - Find index:**

```
@{indexOf(variables('varEmployeeList'), 'Sarah')}
Result: 1 (0-based index)
```

**skip() / take():**

```
@{skip(variables('varEmployeeList'), 2)}
Result: Skip first 2 items

@{take(variables('varEmployeeList'), 3)}
Result: Take first 3 items
```

#### Step 8: Compose Action

**Purpose:** Store expression result for reuse

```
Action: Compose
Inputs: @{concat(body('Get_item')?['FirstName'], ' ', body('Get_item')?['LastName'])}

Then use everywhere:
@{outputs('Compose')}
Instead of repeating the concat expression
```

**Complex Compose Example:**

```
Action: Compose - Calculate Leave Days
Inputs:
@{div(
    sub(
        ticks(body('Get_leave')?['EndDate']),
        ticks(body('Get_leave')?['StartDate'])
    ),
    864000000000
)}

Result stored in outputs('Compose')
Use: @{outputs('Compose')} days
```

**Compose for JSON:**

```
Action: Compose - Build Employee Object
Inputs:
{
  "FullName": "@{concat(body('Get_item')?['FirstName'], ' ', body('Get_item')?['LastName'])}",
  "Email": "@{body('Get_item')?['Email']}",
  "Department": "@{body('Get_item')?['Department']}",
  "IsActive": @{if(equals(body('Get_item')?['Status'], 'Active'), true, false)}
}
```

#### Step 9: Parse JSON Action

**Purpose:** Parse JSON string into usable object

```
Action: Parse JSON
Content: @{body('HTTP_Request')}
Schema: {
  "type": "object",
  "properties": {
    "EmployeeID": {"type": "integer"},
    "Name": {"type": "string"},
    "Email": {"type": "string"},
    "Department": {"type": "string"}
  }
}

After parsing, access like:
@{body('Parse_JSON')?['Name']}
@{body('Parse_JSON')?['Email']}
```

**Generate Schema from Sample:**

1. Click "Use sample payload to generate schema"
2. Paste sample JSON
3. Schema auto-generated

**Example Sample JSON:**

```json
{
  "EmployeeID": 123,
  "Name": "John Smith",
  "Email": "john@company.com",
  "Department": "IT",
  "Manager": {
    "Name": "Jane Doe",
    "Email": "jane@company.com"
  },
  "Projects": [
    { "Name": "Project A", "Status": "Active" },
    { "Name": "Project B", "Status": "Completed" }
  ]
}
```

**Access Nested Data:**

```
Name: @{body('Parse_JSON')?['Name']}
Manager Name: @{body('Parse_JSON')?['Manager']?['Name']}
First Project: @{body('Parse_JSON')?['Projects'][0]?['Name']}
```

#### Step 10: Practical Examples

**Example 1: Build Email with Formatted Data**

```
Action: Compose - Format Leave Details
Inputs:
@{concat(
    'Employee: ', body('Get_employee')?['Title'], '\n',
    'Leave Type: ', body('Get_leave')?['LeaveType'], '\n',
    'Start Date: ', formatDateTime(body('Get_leave')?['StartDate'], 'MMM dd, yyyy'), '\n',
    'End Date: ', formatDateTime(body('Get_leave')?['EndDate'], 'MMM dd, yyyy'), '\n',
    'Total Days: ', body('Get_leave')?['TotalDays'], '\n',
    'Status: ', body('Get_leave')?['Status']
)}

Action: Send an email (V2)
Body: @{outputs('Compose')}
```

**Example 2: Calculate Business Days**

```
Action: Initialize variable - varBusinessDays
Type: Integer
Value: 0

Action: Initialize variable - varCurrentDate
Type: String
Value: @{body('Get_leave')?['StartDate']}

Action: Do until
Condition: @{greater(ticks(variables('varCurrentDate')), ticks(body('Get_leave')?['EndDate']))}
Actions:
  - Compose - Day of Week
    Inputs: @{dayOfWeek(variables('varCurrentDate'))}

  - Condition - Is Business Day
    If: @{and(
        not(equals(outputs('Compose'), 0)),
        not(equals(outputs('Compose'), 6))
    )}
    Then:
      - Increment variable
        Name: varBusinessDays
        Value: 1

  - Set variable - varCurrentDate
    Value: @{addDays(variables('varCurrentDate'), 1)}

Result: @{variables('varBusinessDays')} business days
```

**Example 3: Extract Email Domain**

```
Action: Compose
Inputs: @{split(body('Get_item')?['Email'], '@')[1]}

Result: "company.com" from "john@company.com"
```

**Example 4: Validate Email Format**

```
Action: Condition
If: @{and(
    contains(body('Get_item')?['Email'], '@'),
    greater(indexOf(body('Get_item')?['Email'], '@'), 0),
    contains(body('Get_item')?['Email'], '.'),
    greater(
        indexOf(body('Get_item')?['Email'], '.'),
        indexOf(body('Get_item')?['Email'], '@')
    )
)}
Then: Valid email
Else: Invalid email
```

### Assignment Deliverable

✅ **Create flow demonstrating 10+ expressions:**

- Text manipulation (concat, substring, replace)
- Date formatting and calculations
- Math operations
- Conditional logic
- Array operations

✅ **Build "Leave Request Processor" flow:**

- Get leave request
- Calculate business days (exclude weekends)
- Format dates properly
- Build formatted email notification
- Use variables for tracking
- Use Compose for complex calculations

✅ **Create expression reference document:**

- Most commonly used expressions
- Examples for each
- When to use each type
- Common patterns

### Knowledge Check

- [ ] How do you concatenate strings?
- [ ] What's the expression for current date/time?
- [ ] How do you format dates?
- [ ] How do you access array items?
- [ ] What's the difference between Compose and Set Variable?
- [ ] How do you convert timezone?
- [ ] How do you check if string contains text?
- [ ] What's the syntax for conditional (if)?
- [ ] How do you access nested JSON properties?
- [ ] What's Parse JSON used for?

### Expression Quick Reference

| Category  | Function       | Example                                  |
| --------- | -------------- | ---------------------------------------- |
| **Text**  | concat         | `concat('Hello ', 'World')`              |
| **Text**  | substring      | `substring('Hello', 0, 3)` → "Hel"       |
| **Date**  | formatDateTime | `formatDateTime(utcNow(), 'yyyy-MM-dd')` |
| **Date**  | addDays        | `addDays(utcNow(), 7)`                   |
| **Math**  | add            | `add(5, 3)` → 8                          |
| **Logic** | if             | `if(equals(x, 'yes'), true, false)`      |
| **Array** | length         | `length(array)`                          |
| **Array** | first          | `first(array)`                           |

### Resources

- [Microsoft Learn: Expression functions](https://learn.microsoft.com/en-us/azure/logic-apps/workflow-definition-language-functions-reference)
- [Microsoft Learn: Actions overview](https://learn.microsoft.com/en-us/power-automate/actions)
- [Microsoft Learn: Variables in flows](https://learn.microsoft.com/en-us/power-automate/create-variable-store-values)
- [Microsoft Learn: Parse JSON](https://learn.microsoft.com/en-us/power-automate/parse-json)
- [Blog: Expression Examples](https://powerusers.microsoft.com/t5/Power-Automate-Community-Blog/bg-p/FlowBlog)

---

## Stage 4 Progress Summary

### Modules Completed So Far:

- ✅ Module 22: Cloud Flow Triggers (2 hours)
- ✅ Module 23: Flow Actions and Expressions (3 hours)

### Completed: 5 hours of 25.5 hours

### Remaining Modules:

- Module 24: Flow Naming Conventions (0.5 hours)
- Module 25: Data Operations in Flows (2 hours)
- Module 26: Canvas App Integration (2 hours)
- Module 27: Premium Connectors (1 hour)
- Module 28: Connection References (1 hour)
- Module 29: Scope Actions (1 hour)
- Module 30: Child Flows (2 hours)
- Module 31: Run-as User (1 hour)
- Module 32: Flow Error Handling (2 hours)
- Remaining: 13 hours in later modules

---

**Type "NEXT" to continue with Flow Naming Conventions, Data Operations, and Canvas App Integration.**
