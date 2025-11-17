# Power Platform Training Manual - Stage 4 Part 3: Canvas Integration & Advanced Patterns

## Overview

This section covers Canvas app integration with flows, premium connectors, connection references, scope actions, child flows, and run-as user configurations. **Duration: 8 hours**

---

## Module 26: Canvas App Integration with Flows (2 hours)

### Learning Objectives

- Trigger flows from Canvas apps
- Pass data from app to flow
- Return values from flow to app
- Handle flow responses in apps
- Implement error handling
- Create seamless user experience

### Step-by-Step Instructions

#### Step 1: Create Flow for Canvas App

**Create New Flow:**

```
Flow Name: Manual - Leave Request - Submit From App

Trigger: PowerApps (V2)
(This trigger allows Canvas apps to call this flow)

No parameters needed initially - we'll add them
```

**Add Input Parameters:**

```
Trigger: PowerApps (V2)
Ask in PowerApps: (Add inputs)

Click "Add an input"

Input 1:
  Type: Text
  Name: EmployeeEmail
  Description: Email of employee submitting request

Input 2:
  Type: Text
  Name: LeaveType
  Description: Type of leave (Annual, Sick, etc.)

Input 3:
  Type: Date
  Name: StartDate
  Description: Leave start date

Input 4:
  Type: Date
  Name: EndDate
  Description: Leave end date

Input 5:
  Type: Number
  Name: TotalDays
  Description: Total number of days

Input 6:
  Type: Text
  Name: Comments
  Description: Employee comments
```

**Build Flow Logic:**

```
Action: Get items - Find Employee
Site: Your SharePoint
List: Employees
Filter: Email eq '@{triggerBody()['text']}'  (EmployeeEmail input)

Action: Condition - Employee Found
If length(body('Get_items_-_Find_Employee')?['value']) is greater than 0

YES Branch:
  Action: Create item - Leave Request
  Site: Your SharePoint
  List: Leave Requests
  Fields:
    Title: Leave Request - @{triggerBody()['text']}
    EmployeeId: @{first(body('Get_items_-_Find_Employee')?['value'])?['ID']}
    LeaveType: @{triggerBody()['text_1']}
    StartDate: @{triggerBody()['date']}
    EndDate: @{triggerBody()['date_1']}
    TotalDays: @{triggerBody()['number']}
    Comments: @{triggerBody()['text_2']}
    Status: Pending
    SubmittedDate: @{utcNow()}

  Action: Get items - Find Manager
  Filter: ID eq @{first(body('Get_items_-_Find_Employee')?['value'])?['ManagerID']}

  Action: Send an email (V2) - Notify Manager
  To: @{first(body('Get_items_-_Find_Manager')?['value'])?['Email']}
  Subject: New Leave Request - @{first(body('Get_items_-_Find_Employee')?['value'])?['Title']}
  Body: [Email template with details]

  Action: Respond to PowerApp
  Output 1:
    Name: Success
    Type: Text
    Value: true
  Output 2:
    Name: Message
    Type: Text
    Value: Your leave request has been submitted successfully!
  Output 3:
    Name: RequestID
    Type: Number
    Value: @{outputs('Create_item_-_Leave_Request')?['body/ID']}

NO Branch:
  Action: Respond to PowerApp
  Output:
    Success: false
    Message: Employee not found. Please contact HR.
    RequestID: 0
```

**Important Notes:**

```
⚠️ CRITICAL: You MUST include "Respond to PowerApp" action
- Without it, the app will wait indefinitely
- Always include Success status
- Include helpful error messages
- Return useful data (IDs, confirmation numbers)

✅ Best Practice: Include outputs in BOTH branches (Yes and No)
```

#### Step 2: Call Flow from Canvas App

**In Power Apps Studio:**

```
1. Click "Power Automate" in left panel
2. Click "Add flow"
3. Select "Manual - Leave Request - Submit From App"
4. Flow appears in available flows
```

**Add Button to Submit:**

```
Button: btnSubmitLeaveRequest
Text: "Submit Request"
OnSelect:
Set(varFlowRunning, true);
Set(varFlowResponse,
    'Manual-LeaveRequest-SubmitFromApp'.Run(
        User().Email,                          // EmployeeEmail
        ddLeaveType.Selected.Value,            // LeaveType
        dtpStartDate.SelectedDate,             // StartDate
        dtpEndDate.SelectedDate,               // EndDate
        Value(lblTotalDays.Text),              // TotalDays
        txtComments.Text                       // Comments
    )
);
Set(varFlowRunning, false);

If(
    varFlowResponse.success = "true",
    // Success handling
    Notify(varFlowResponse.message, NotificationType.Success);
    Set(varRequestID, varFlowResponse.requestid);
    Navigate(scrConfirmation, ScreenTransition.Fade),
    // Error handling
    Notify(varFlowResponse.message, NotificationType.Error)
)
```

**Flow Function Syntax:**

```
FlowName.Run(parameter1, parameter2, ...)

Example:
'FlowName'.Run(
    "value1",              // Text input
    123,                   // Number input
    DateValue("2025-11-20"), // Date input
    true,                  // Boolean input
    colData                // Can pass collections too
)

⚠️ Notes:
- Flow name uses underscores, not spaces: 'Flow_Name'.Run()
- Use single quotes around flow name
- Parameters must be in exact order defined in flow
- Must include ALL required parameters
```

#### Step 3: Handle Flow Execution in App

**Show Loading Indicator:**

```
Add Spinner or Loading Icon:
Control: Icon (icon_loading)
Icon: Icon.Sync
Color: RGBA(0, 120, 212, 1)
Visible: varFlowRunning
X: (Parent.Width - Self.Width) / 2
Y: (Parent.Height - Self.Height) / 2

Add Rectangle Background:
Fill: RGBA(0, 0, 0, 0.5)
Visible: varFlowRunning
ZIndex: High

Disable button while running:
btnSubmitLeaveRequest.DisplayMode:
If(varFlowRunning, DisplayMode.Disabled, DisplayMode.Edit)
```

**Handle Response:**

```
// Store response in variables
Set(varFlowResponse, 'FlowName'.Run(...));

// Check success
If(
    varFlowResponse.success = "true",
    // Success path
    Set(varSubmissionSuccess, true);
    Notify(varFlowResponse.message, NotificationType.Success),
    // Error path
    Set(varSubmissionSuccess, false);
    Notify(varFlowResponse.message, NotificationType.Error)
)

// Access response fields
varFlowResponse.success
varFlowResponse.message
varFlowResponse.requestid
```

**Error Handling:**

```
OnSelect of Submit Button:
Set(varFlowRunning, true);
Set(varErrorOccurred, false);

IfError(
    // Try to run flow
    Set(varFlowResponse, 'FlowName'.Run(...));
    Set(varFlowRunning, false);

    If(
        varFlowResponse.success = "true",
        Notify(varFlowResponse.message, NotificationType.Success),
        Notify(varFlowResponse.message, NotificationType.Error)
    ),
    // Catch error
    Set(varFlowRunning, false);
    Set(varErrorOccurred, true);
    Notify("An error occurred. Please try again or contact IT.", NotificationType.Error);
    // Log error
    Trace("Flow Error: " & FirstError.Message, TraceSeverity.Error)
)
```

#### Step 4: Pass Collections to Flows

**Scenario: Bulk Submit Multiple Items**

**Flow Setup:**

```
Trigger: PowerApps (V2)
Ask in PowerApps:
  Input:
    Type: Text
    Name: JSONData
    Description: JSON array of items to process

Action: Parse JSON
Content: @{triggerBody()['text']}
Schema: (Use sample JSON to generate)

Sample JSON:
[
  {
    "employeeEmail": "john@company.com",
    "leaveType": "Annual",
    "startDate": "2025-11-20",
    "days": 5
  },
  {
    "employeeEmail": "sarah@company.com",
    "leaveType": "Sick",
    "startDate": "2025-11-22",
    "days": 2
  }
]

Action: Apply to each
Items: @{body('Parse_JSON')}

Inside loop:
  Create item for each entry
  @{items('Apply_to_each')?['employeeEmail']}
  @{items('Apply_to_each')?['leaveType']}
  etc.

Action: Respond to PowerApp
Outputs:
  Success: true
  ProcessedCount: @{length(body('Parse_JSON'))}
  Message: Processed @{length(body('Parse_JSON'))} requests
```

**Canvas App:**

```
Button: btnBulkSubmit
OnSelect:
// Convert collection to JSON
Set(varJSONData, JSON(colBulkRequests, JSONFormat.IndentFour));

// Send to flow
Set(varResponse,
    'BulkSubmitFlow'.Run(varJSONData)
);

// Handle response
Notify(
    varResponse.processedcount & " requests submitted successfully",
    NotificationType.Success
)
```

**Collection to JSON Example:**

```
Collection: colBulkRequests
[
  {employeeEmail: "john@company.com", leaveType: "Annual", days: 5},
  {employeeEmail: "sarah@company.com", leaveType: "Sick", days: 2}
]

JSON(colBulkRequests, JSONFormat.IndentFour) produces:
[
  {
    "employeeEmail": "john@company.com",
    "leaveType": "Annual",
    "days": 5
  },
  {
    "employeeEmail": "sarah@company.com",
    "leaveType": "Sick",
    "days": 2
  }
]
```

#### Step 5: Return Complex Data from Flow

**Flow Returns Multiple Records:**

```
Scenario: Search employees, return results to app

Trigger: PowerApps (V2)
Input:
  Type: Text
  Name: SearchTerm

Action: Get items - Search Employees
Filter: substringof('@{triggerBody()['text']}', Title)
Top Count: 10

Action: Select - Format Results
From: @{body('Get_items')?['value']}
Map:
  ID: @{item()?['ID']}
  Name: @{item()?['Title']}
  Email: @{item()?['Email']}
  Department: @{item()?['Department']}

Action: Respond to PowerApp
Outputs:
  Success: true
  ResultsJSON: @{body('Select_-_Format_Results')}
  Count: @{length(body('Select_-_Format_Results'))}
```

**Canvas App - Parse Results:**

```
Button: btnSearch
OnSelect:
Set(varSearchResults, 'SearchEmployeesFlow'.Run(txtSearch.Text));

If(
    varSearchResults.success = "true",
    // Parse JSON results
    ClearCollect(
        colSearchResults,
        ParseJSON(varSearchResults.resultsjson)
    );
    Notify(varSearchResults.count & " results found", NotificationType.Success),
    Notify("Search failed", NotificationType.Error)
)

Gallery: galSearchResults
Items: colSearchResults
Display: ThisItem.Name, ThisItem.Email, ThisItem.Department
```

#### Step 6: Flow Timeout Handling

**Flow Timeout Limits:**

```
⚠️ Important Limits:
- Flow called from Canvas app: 120 seconds (2 minutes) timeout
- If flow takes longer, app shows error
- Plan for long-running operations
```

**Strategy 1: Quick Response with Background Processing**

```
Flow Design:
1. Validate inputs quickly
2. Respond to PowerApp immediately
3. Continue processing in background

Example:
Trigger: PowerApps
Validate inputs (1 second)
Create item with Status = "Processing" (1 second)
Respond to PowerApp: "Request received" (Total: 2 seconds)
[Continue flow after response]
Process complex logic (30 seconds)
Update item Status = "Complete"
Send email notification
```

**Strategy 2: Call Child Flow for Long Operations**

```
Parent Flow (Called from Canvas):
Trigger: PowerApps
Validate inputs
Create record
Respond to PowerApp: "Processing started"

Action: HTTP - Call Parent Flow (async)
Call another flow to do the heavy work

Child Flow:
Trigger: When HTTP request is received
Process long-running task
Update record when complete
```

**Strategy 3: Status Checking**

```
Flow 1: Submit Request (Fast)
- Create record with Status = "Pending"
- Respond to app with Record ID
- Start background processing

Flow 2: Check Status (Fast)
- Input: Record ID
- Get item
- Return: Status, Results

Canvas App:
OnSelect of Submit:
  Run Flow 1, get Record ID
  Navigate to Status screen

Status Screen:
  Timer to refresh every 5 seconds
  Run Flow 2 to check status
  Show progress/results
```

#### Step 7: Practical Example - Complete Submit Form

**Flow: Manual - Leave Request - Submit With Validation**

```
Trigger: PowerApps (V2)
Inputs:
  - EmployeeEmail (Text)
  - LeaveType (Text)
  - StartDate (Date)
  - EndDate (Date)
  - TotalDays (Number)
  - Comments (Text)

Step 1: Validate Dates
Action: Condition - Valid Date Range
Condition: @{less(ticks(triggerBody()['date']), ticks(triggerBody()['date_1']))}

If NO:
  Respond to PowerApp:
    Success: false
    Message: End date must be after start date
    RequestID: 0
  [Terminate]

Step 2: Get Employee
Action: Get items - Employee
Filter: Email eq '@{triggerBody()['text']}'

Action: Condition - Employee Exists
If length = 0:
  Respond: Employee not found
  [Terminate]

Step 3: Check Leave Balance
Action: Get items - Leave Balance
Filter: EmployeeId eq @{first(body('Get_items_-_Employee')?['value'])?['ID']}
        and LeaveType eq '@{triggerBody()['text_1']}'
        and Year eq @{formatDateTime(utcNow(), 'yyyy')}

Action: Compose - Available Balance
Inputs: @{sub(
    first(body('Get_items_-_Leave_Balance')?['value'])?['TotalDays'],
    first(body('Get_items_-_Leave_Balance')?['value'])?['UsedDays']
)}

Action: Condition - Sufficient Balance
If @{greater(outputs('Compose_-_Available_Balance'), triggerBody()['number'])}

If NO:
  Respond to PowerApp:
    Success: false
    Message: Insufficient leave balance. Available: @{outputs('Compose')} days
    RequestID: 0
  [Terminate]

Step 4: Create Leave Request
Action: Create item
List: Leave Requests
Fields:
  Title: @{first(body('Get_items_-_Employee')?['value'])?['Title']} - @{triggerBody()['text_1']}
  EmployeeId: @{first(body('Get_items_-_Employee')?['value'])?['ID']}
  LeaveType: @{triggerBody()['text_1']}
  StartDate: @{triggerBody()['date']}
  EndDate: @{triggerBody()['date_1']}
  TotalDays: @{triggerBody()['number']}
  Comments: @{triggerBody()['text_2']}
  Status: Pending
  SubmittedDate: @{utcNow()}

Step 5: Notify Manager
Action: Get items - Manager
Filter: ID eq @{first(body('Get_items_-_Employee')?['value'])?['ManagerID']}

Action: Send an email (V2)
To: @{first(body('Get_items_-_Manager')?['value'])?['Email']}
Subject: Leave Request Pending Approval
Body:
<h2>New Leave Request</h2>
<p><strong>Employee:</strong> @{first(body('Get_items_-_Employee')?['value'])?['Title']}</p>
<p><strong>Type:</strong> @{triggerBody()['text_1']}</p>
<p><strong>Period:</strong> @{formatDateTime(triggerBody()['date'], 'MMM dd, yyyy')} to @{formatDateTime(triggerBody()['date_1'], 'MMM dd, yyyy')}</p>
<p><strong>Days:</strong> @{triggerBody()['number']}</p>
<p><strong>Comments:</strong> @{triggerBody()['text_2']}</p>
<p>Please review and approve in the Leave Management System.</p>

Step 6: Respond Success
Action: Respond to PowerApp
Outputs:
  Success: true
  Message: Your leave request has been submitted and your manager has been notified.
  RequestID: @{outputs('Create_item')?['body/ID']}
```

**Canvas App Integration:**

```
Screen: scrSubmitLeave

Controls:
- ddLeaveType (Dropdown)
- dtpStartDate (Date picker)
- dtpEndDate (Date picker)
- lblTotalDays (Label - calculated)
- txtComments (Text input - multiline)
- btnSubmit (Button)

lblTotalDays.Text:
DateDiff(dtpStartDate.SelectedDate, dtpEndDate.SelectedDate, Days) + 1

btnSubmit.OnSelect:
// Validate inputs
If(
    IsBlank(ddLeaveType.Selected.Value),
    Notify("Please select leave type", NotificationType.Error);
    Exit()
);

If(
    IsBlank(dtpStartDate.SelectedDate) || IsBlank(dtpEndDate.SelectedDate),
    Notify("Please select both start and end dates", NotificationType.Error);
    Exit()
);

If(
    dtpStartDate.SelectedDate < Today(),
    Notify("Start date cannot be in the past", NotificationType.Error);
    Exit()
);

// Show loading
Set(varFlowRunning, true);

// Call flow
IfError(
    Set(varFlowResponse,
        'Manual-LeaveRequest-SubmitWithValidation'.Run(
            User().Email,
            ddLeaveType.Selected.Value,
            dtpStartDate.SelectedDate,
            dtpEndDate.SelectedDate,
            Value(lblTotalDays.Text),
            txtComments.Text
        )
    );
    Set(varFlowRunning, false);

    // Handle response
    If(
        varFlowResponse.success = "true",
        // Success
        Set(varRequestID, varFlowResponse.requestid);
        Notify(varFlowResponse.message, NotificationType.Success, 3000);
        ResetForm(frmLeaveRequest);
        Navigate(scrMyRequests, ScreenTransition.Fade),
        // Validation failed
        Notify(varFlowResponse.message, NotificationType.Error, 5000)
    ),
    // Flow error
    Set(varFlowRunning, false);
    Notify(
        "An error occurred while submitting your request. Please try again or contact IT support.",
        NotificationType.Error,
        5000
    );
    Trace("Flow Error: " & FirstError.Message, TraceSeverity.Error)
);

btnSubmit.DisplayMode:
If(varFlowRunning, DisplayMode.Disabled, DisplayMode.Edit)

btnSubmit.Text:
If(varFlowRunning, "Submitting...", "Submit Request")
```

### Assignment Deliverable

✅ **Create "Submit Leave Request" integration:**

- Build flow with all validations
- Add PowerApps trigger with inputs
- Implement error handling
- Return success/error responses
- Call from Canvas app
- Handle loading states
- Display success/error messages

✅ **Create "Search Employees" flow:**

- Accept search term
- Return array of results
- Parse in Canvas app
- Display in gallery

✅ **Document integration patterns:**

- How to pass parameters
- How to handle responses
- Error handling strategies
- Performance considerations

### Knowledge Check

- [ ] How do you trigger flow from Canvas app?
- [ ] What trigger type do you use?
- [ ] How do you pass parameters to flow?
- [ ] How do you return values from flow?
- [ ] What's the timeout limit?
- [ ] How do you handle errors?
- [ ] How do you show loading state?
- [ ] Can you pass collections to flows?
- [ ] How do you return multiple records?
- [ ] What's the Respond to PowerApp action?

### Resources

- [Microsoft Learn: Canvas app flows](https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/using-logic-flows)
- [Microsoft Learn: Pass collections](https://learn.microsoft.com/en-us/power-automate/connection-dynamics-crmonline)
- [Microsoft Learn: Return data to apps](https://learn.microsoft.com/en-us/power-automate/how-tos-return-response)

---

## Module 27: Premium Connectors (1 hour)

### Learning Objectives

- Understand premium vs standard connectors
- Use SQL Server connector
- Make HTTP requests
- Work with Azure services
- Handle authentication
- Manage licensing requirements

### Step-by-Step Instructions

#### Step 1: Understanding Premium Connectors

**Licensing:**

```
⚠️ Premium connectors require:
- Power Apps per user plan OR
- Power Apps per app plan OR
- Power Automate per user plan

Standard connectors: Included with Office 365

Premium connector icon: Yellow lightning bolt ⚡
```

**Common Premium Connectors:**

```
✅ Standard (Free with Office 365):
- SharePoint
- OneDrive for Business
- Office 365 Outlook
- Office 365 Users
- Approvals
- Microsoft Teams
- Excel Online

⚡ Premium (Require license):
- SQL Server
- Azure services (Blob Storage, SQL Database, Key Vault, etc.)
- HTTP (with Azure AD)
- Dynamics 365
- Common Data Service (Dataverse)
- AI Builder
- Custom connectors
- Many third-party services (Salesforce, SAP, etc.)
```

**Check Connector Type:**

```
In Power Automate:
1. Add new action
2. Search for connector
3. Look for premium badge (⚡)
4. Hover over badge to see "Premium" label
```

#### Step 2: SQL Server Connector

**Use Case: Read from company database**

**Prerequisites:**

```
✅ Requirements:
- SQL Server accessible from internet or with data gateway
- Database credentials (SQL authentication or Windows authentication)
- Premium license
- Firewall rules configured
```

**Setup Connection:**

```
Action: SQL Server - Get rows (V2)

First time setup:
Connection Name: SQL Server - HR Database
Authentication Type:
  - SQL Server Authentication (recommended)
  - Windows Authentication
  - Azure AD Integrated
Server: sql.yourcompany.com
Database: HRDatabase
Username: flowuser
Password: [secure password]

Click "Create"
```

**Get Rows from SQL:**

```
Action: Get rows (V2)
Server: sql.yourcompany.com
Database: HRDatabase
Table: Employees

Filter Query (optional):
Department eq 'IT' and Status eq 'Active'

Order By (optional):
HireDate desc

Top Count (optional):
100

Skip (optional):
0
```

**Execute SQL Query:**

```
Action: Execute SQL query (V2)
Server: sql.yourcompany.com
Database: HRDatabase

Query:
SELECT
    e.EmployeeID,
    e.FirstName,
    e.LastName,
    e.Email,
    d.DepartmentName,
    e.HireDate
FROM Employees e
INNER JOIN Departments d ON e.DepartmentID = d.DepartmentID
WHERE e.Status = 'Active'
ORDER BY e.HireDate DESC

⚠️ Use parameterized queries to prevent SQL injection:
SELECT * FROM Employees WHERE EmployeeID = @{triggerBody()['EmployeeID']}
```

**Insert/Update Row:**

```
Action: Insert row (V2)
Server: sql.yourcompany.com
Database: HRDatabase
Table: LeaveRequests

Columns:
  EmployeeID: @{body('Get_employee')?['EmployeeID']}
  LeaveType: @{triggerBody()['LeaveType']}
  StartDate: @{triggerBody()['StartDate']}
  EndDate: @{triggerBody()['EndDate']}
  TotalDays: @{triggerBody()['TotalDays']}
  Status: Pending
  SubmittedDate: @{utcNow()}

Action: Update row (V2)
Row ID: 12345
Status: Approved
ApprovedDate: @{utcNow()}
```

**On-Premises Data Gateway:**

```
If SQL Server is on-premises:

1. Install data gateway:
   Download from: https://powerapps.microsoft.com/gateway
   Install on server with network access to SQL Server
   Sign in with same account

2. Configure gateway:
   Power Platform Admin Center → Data → Gateways
   Add gateway
   Select installed gateway

3. Use in connection:
   When creating SQL connection
   Select "Connect using on-premises data gateway"
   Choose your gateway
```

#### Step 3: HTTP Connector

**Use Case: Call external APIs**

**Basic GET Request:**

```
Action: HTTP
Method: GET
URI: https://api.example.com/employees

Headers:
  Content-Type: application/json
  Authorization: Bearer @{variables('varAccessToken')}

Queries:
  department: IT
  status: active

Result: @{body('HTTP')}
```

**POST Request with Body:**

```
Action: HTTP
Method: POST
URI: https://api.example.com/leave-requests

Headers:
  Content-Type: application/json
  Authorization: Bearer @{variables('varAPIKey')}

Body:
{
  "employeeId": "@{body('Get_employee')?['ID']}",
  "leaveType": "@{triggerBody()['LeaveType']}",
  "startDate": "@{formatDateTime(triggerBody()['StartDate'], 'yyyy-MM-dd')}",
  "endDate": "@{formatDateTime(triggerBody()['EndDate'], 'yyyy-MM-dd')}",
  "totalDays": @{triggerBody()['TotalDays']}
}

Response: @{body('HTTP')}
Status Code: @{outputs('HTTP')['statusCode']}
```

**Authentication Types:**

```
1. Bearer Token (OAuth):
Headers:
  Authorization: Bearer eyJ0eXAiOiJKV1QiLCJh...

2. API Key:
Headers:
  X-API-Key: your-api-key-here

3. Basic Authentication:
Headers:
  Authorization: Basic @{base64(concat('username', ':', 'password'))}

4. Azure AD OAuth:
Use "HTTP with Azure AD" action
Tenant: yourcompany.onmicrosoft.com
Audience: https://api.yourcompany.com
```

**Handle Response:**

```
Action: HTTP - Get Employee Data

Action: Parse JSON
Content: @{body('HTTP_-_Get_Employee_Data')}
Schema: [Generate from sample]

Action: Condition - Check Status Code
If @{outputs('HTTP_-_Get_Employee_Data')['statusCode']} equals 200

YES:
  Process data: @{body('Parse_JSON')?['employee']?['name']}

NO:
  Handle error
  Log: Status @{outputs('HTTP')['statusCode']}: @{body('HTTP')}
```

#### Step 4: Azure Blob Storage

**Use Case: Store/retrieve files in Azure**

**Upload File to Blob:**

```
Action: Create blob (V2)
Storage account name: yourcompanystorage
Blob path: /leave-documents/@{outputs('Create_item')?['body/ID']}_@{triggerBody()['FileName']}
Blob content: @{triggerBody()['file/contentBytes']}

Content-Type: application/pdf
```

**Download Blob:**

```
Action: Get blob content (V2)
Storage account: yourcompanystorage
Blob: /leave-documents/12345_request.pdf

Use content:
@{body('Get_blob_content_(V2)')}

Send as email attachment:
Action: Send an email (V2)
Attachments:
  Name: @{triggerBody()['FileName']}
  Content Bytes: @{body('Get_blob_content_(V2)')}
```

**List Blobs:**

```
Action: List blobs (V2)
Storage account: yourcompanystorage
Folder: /leave-documents

Result: @{body('List_blobs_(V2)')?['value']}

Apply to each:
  Blob name: @{items('Apply_to_each')?['Name']}
  Blob size: @{items('Apply_to_each')?['Size']}
```

#### Step 5: Azure Key Vault

**Use Case: Store secrets securely**

**Get Secret:**

```
Action: Get secret
Vault name: your-keyvault
Secret name: SQLConnectionString

Use in SQL connection:
Connection String: @{body('Get_secret')?['value']}

Common secrets to store:
- Database connection strings
- API keys
- Service account passwords
- OAuth client secrets
```

**Best Practice:**

```
✅ DO:
- Store all sensitive data in Key Vault
- Use managed identities for authentication
- Rotate secrets regularly
- Use separate Key Vaults for dev/test/prod

❌ DON'T:
- Hard-code passwords in flows
- Store secrets in SharePoint lists
- Share secrets in email
- Use same credentials across environments
```

#### Step 6: Custom Connectors

**When to Use:**

```
Use custom connector when:
- API not available in standard connectors
- Need custom authentication
- Want to simplify complex API calls
- Reuse across multiple flows
```

**Create Custom Connector:**

```
1. Power Automate → Data → Custom connectors
2. New custom connector
3. Define:
   - General info (name, description, icon)
   - Security (authentication type)
   - Actions (endpoints, parameters)
   - Test connection

4. Use in flows:
   Action: [Your Custom Connector] - [Action Name]
```

**Example: Custom HR API Connector**

```
Connector Name: HR System API
Host: api.hrsystem.company.com
Base URL: /api/v1

Security: API Key
Header name: X-API-Key
Parameter value: [user provides during connection]

Action: Get Employee
Method: GET
Path: /employees/{id}
Parameters:
  id (path, required): Employee ID

Response: Employee object JSON

Use in flow:
Action: HR System API - Get Employee
id: @{triggerBody()['EmployeeID']}
Result: @{body('HR_System_API_-_Get_Employee')}
```

#### Step 7: Licensing Considerations

**Flow Licensing:**

```
If flow uses premium connectors:
- Flow owner needs premium license OR
- Flow runs under service account with premium license OR
- All users triggering flow need premium license (if manual trigger)

Check flow plan:
Flow details → Plan → Shows required license
```

**Connection Ownership:**

```
Best Practice:
- Create connections with service account
- Service account has premium license
- Share flows with "Run-only" permission
- Users don't need premium license to run

Setup:
1. Create service account: flowservice@company.com
2. Assign Power Automate per user license
3. Use this account to create premium connections
4. Share flows (run-only)
```

### Assignment Deliverable

✅ **Create SQL integration flow:**

- Connect to SQL Server (or create test database)
- Read employee data
- Insert leave request
- Handle errors

✅ **Create HTTP API flow:**

- Call public API (e.g., weather, quotes)
- Parse JSON response
- Use data in email notification

✅ **Document premium connectors:**

- List premium vs standard
- Licensing requirements
- When to use each
- Best practices

### Knowledge Check

- [ ] What's a premium connector?
- [ ] Which license is required?
- [ ] How do you connect to SQL Server?
- [ ] How do you make HTTP requests?
- [ ] What's the data gateway for?
- [ ] How do you store secrets securely?
- [ ] What's a custom connector?
- [ ] How do you handle connection ownership?
- [ ] What's bearer token authentication?
- [ ] How do you parse API responses?

### Resources

- [Microsoft Learn: Premium connectors](https://learn.microsoft.com/en-us/power-platform/admin/power-automate-licensing/types)
- [Microsoft Learn: SQL Server connector](https://learn.microsoft.com/en-us/connectors/sql/)
- [Microsoft Learn: HTTP connector](https://learn.microsoft.com/en-us/connectors/http/)
- [Microsoft Learn: Custom connectors](https://learn.microsoft.com/en-us/connectors/custom-connectors/)
- [Microsoft Learn: Data gateway](https://learn.microsoft.com/en-us/power-platform/admin/wp-onpremises-gateway)

---

## Module 28: Connection References (1 hour)

### Learning Objectives

- Understand connection references
- Create environment-independent flows
- Use in solutions
- Manage connections across environments
- Implement deployment best practices

### Step-by-Step Instructions

#### Step 1: Understanding Connection References

**Problem Without Connection References:**

```
❌ Traditional Flow:
- SharePoint connection tied to specific environment
- Connection embedded in flow
- Moving to new environment breaks flow
- Need to reconnect everything manually

Example:
Dev Environment: SharePoint connection → Dev site
Test Environment: Need to recreate connection → Test site
Prod Environment: Need to recreate connection → Prod site

Result: Manual work, errors, delays
```

**Solution: Connection References**

```
✅ With Connection References:
- Connection reference is separate component
- Flow uses reference, not direct connection
- Reference points to different connection per environment
- Move flow easily between environments

Example:
Connection Reference: "Leave Management SharePoint"

Dev Environment: Reference → Dev SharePoint connection
Test Environment: Reference → Test SharePoint connection
Prod Environment: Reference → Prod SharePoint connection

Flow code: Same everywhere, no changes needed
```

#### Step 2: Create Connection Reference

**Method 1: In Solution**

```
1. Go to make.powerapps.com
2. Solutions → New solution
   Name: Leave Management Solution
   Publisher: Your Company
   Version: 1.0.0.0

3. Add new → More → Connection Reference
   Display Name: SharePoint - Leave Management
   Description: SharePoint connection for Leave Management System
   Connector: SharePoint

4. Click "Create"

5. After creation, click connection reference
6. Click "Edit" → Select existing connection or create new
   For Dev: Select Dev SharePoint connection
```

**Method 2: Automatically During Flow Creation**

```
1. Create flow inside solution
2. Add SharePoint action
3. Instead of selecting direct connection:
   → Use connection reference
   → Select existing reference or create new

4. System creates connection reference automatically
```

#### Step 3: Use Connection Reference in Flow

**Create Flow with Connection Reference:**

```
1. Solution → New → Automation → Cloud flow → Automated

2. Flow Name: Auto - Leave Request - Process
   Trigger: When item is created (SharePoint)

3. When selecting connection:
   Instead of: [Direct connection dropdown]
   Use: [+ New connection reference]

   Connection Reference Name: SharePoint - Leave Management
   Select connection: [Your connection]

4. All SharePoint actions in flow automatically use this reference
```

**Existing Flow - Convert to Connection Reference:**

```
1. Open flow in solution
2. Flow checker shows: "This flow uses a hard-coded connection"
3. Solution → Connection References → Add new
4. Edit flow → Each action using connection
5. Change from direct connection to connection reference
6. Save flow
```

#### Step 4: Environment Variables (Related Concept)

**Use Case: Store environment-specific values**

```
Problem:
SharePoint site URL different per environment:
- Dev: https://company.sharepoint.com/sites/LeaveManagement-Dev
- Test: https://company.sharepoint.com/sites/LeaveManagement-Test
- Prod: https://company.sharepoint.com/sites/LeaveManagement

❌ Hard-coding in flow: Need to edit flow for each environment
✅ Environment variable: Single value, changes per environment
```

**Create Environment Variable:**

```
Solution → New → More → Environment variable

Display Name: SharePoint Site URL
Name: spo_SiteURL
Data Type: Text
Default Value: https://company.sharepoint.com/sites/LeaveManagement-Dev
Description: SharePoint site URL for Leave Management System

Click "Save"
```

**Use in Flow:**

```
Action: Get items
Site Address: @{variables('spo_SiteURL')}
Or direct: [Click dynamic content] → Environment variables → SharePoint Site URL
```

**Set Different Values Per Environment:**

```
When deploying to Test:
1. Import solution to Test environment
2. Solutions → Your solution → Environment variables
3. Edit "SharePoint Site URL"
4. New value: https://company.sharepoint.com/sites/LeaveManagement-Test
5. Save

Flow automatically uses new value, no code change needed!
```

#### Step 5: Solution Structure Best Practices

**Complete Solution Structure:**

```
Solution: Leave Management System v1.0

📁 Connection References:
  - SharePoint - Leave Management
  - Office 365 Outlook - Notifications
  - Office 365 Users - User Lookup

📁 Environment Variables:
  - SharePoint Site URL
  - HR Email Address
  - Max Leave Days Without Approval
  - Financial Year Start Month

📁 Flows:
  - Auto - Leave Request - Send Notification
  - Auto - Leave Request - Update Status
  - Scheduled - Leave Balance - Update Monthly
  - Manual - Leave Request - Submit From App

📁 Apps:
  - Leave Management App (Canvas)

📁 Tables (if using Dataverse):
  - Leave Requests
  - Employees
  - Leave Balances

📁 Other:
  - Custom connectors
  - Business rules
```

#### Step 6: Deploy Solution Across Environments

**Export from Dev:**

```
1. Solutions → Leave Management System
2. Export solution
3. Publish all customizations
4. Export as: Managed (for Test/Prod) or Unmanaged (for dev-to-dev)
5. Download solution ZIP file
```

**Import to Test:**

```
1. Test Environment → Solutions → Import solution
2. Browse → Select ZIP file
3. Import

4. Configure connection references:
   Solution → Connection References
   For each reference:
     - Edit
     - Select Test environment connection
     - Save

5. Configure environment variables:
   Solution → Environment variables
   For each variable:
     - Edit
     - Enter Test environment value
     - Save

6. Turn on flows:
   Solution → Cloud flows
   Turn on each flow

7. Test functionality
```

**Import to Production:**

```
Same process as Test, but:
- Use managed solution
- Document all configuration values
- Test in isolated Prod environment first
- Schedule maintenance window
- Have rollback plan

Configuration for Prod:
Connection References → Prod connections
Environment Variables → Prod values
Flows → Enable
Apps → Share with users
```

#### Step 7: Multiple Connection References

**Scenario: Different Data Sources**

```
Solution uses:
- SharePoint (Leave Requests list)
- SharePoint (Employees list - different site)
- SQL Server (HR Database)
- Office 365 Outlook (Email)

Create separate connection references:
1. SharePoint - Leave Data
   → Points to Leave Management site

2. SharePoint - Employee Data
   → Points to HR site

3. SQL Server - HR Database
   → Points to HR SQL server

4. Office 365 Outlook - Notifications
   → Points to service account email
```

**In Flow:**

```
Action: Get items - Leave Requests
Connection: [SharePoint - Leave Data]
Site Address: [Use env variable]
List: Leave Requests

Action: Get items - Employees
Connection: [SharePoint - Employee Data]
Site Address: [Use env variable for HR site]
List: Employees

Action: Get rows - Employee Details
Connection: [SQL Server - HR Database]
Table: Employees

Action: Send an email
Connection: [Office 365 Outlook - Notifications]
```

#### Step 8: Service Account Connections

**Best Practice: Use Service Accounts**

```
Why:
- Personal connections break when employee leaves
- Service account has consistent permissions
- Easier to manage at scale
- Better security and auditing

Setup:
1. Create service account: flowservice@company.com
2. Assign licenses (M365, Power Automate)
3. Grant permissions:
   - SharePoint: Site member or appropriate level
   - SQL: Database user with required permissions
   - Email: Send-as or shared mailbox
4. Create connections using this account
5. Use in connection references
```

**Delegate Access:**

```
Service Account: flowservice@company.com

SharePoint:
- Site permissions: Edit (or custom level)
- List permissions: Contribute

SQL Server:
- Database role: db_datareader, db_datawriter
- Specific permissions on tables

Office 365:
- Shared mailbox: leavemanagement@company.com
- Service account has "Send As" permission
```

#### Step 9: Version Control

**Solution Versioning:**

```
Version Format: Major.Minor.Patch.Build
Example: 1.2.3.4

1.0.0.0 - Initial release
1.0.1.0 - Bug fix (minor change)
1.1.0.0 - New feature (minor version)
2.0.0.0 - Major redesign (breaking changes)

When exporting:
Solution → Settings → Version
Increment appropriately
Add description of changes
```

**Track Changes:**

```
Document changes in solution description:

Version 1.0.0.0 (2025-11-01):
- Initial release
- Leave request submission flow
- Manager notification flow

Version 1.1.0.0 (2025-11-14):
- Added bulk approval flow
- New environment variables for configuration
- Bug fix: Date calculation for business days

Version 1.2.0.0 (2025-11-20):
- Integration with HR SQL database
- New connection reference for SQL
- Enhanced error handling
```

#### Step 10: Troubleshooting Connection References

**Common Issues:**

```
Issue 1: Flow shows "Connection not valid"
Solution:
- Solutions → Connection References
- Edit problematic reference
- Select valid connection
- Save
- Turn flow off and on

Issue 2: Connection reference not found
Solution:
- Ensure connection reference imported with solution
- Check if reference exists in target environment
- Recreate if missing

Issue 3: Permission denied
Solution:
- Check connection account permissions
- Verify SharePoint/SQL/API access
- Test connection manually

Issue 4: Wrong data showing
Solution:
- Verify environment variable values
- Check connection pointing to correct resource
- Confirm site URLs, database names correct
```

**Testing Checklist:**

```
✅ Before deploying:
- [ ] All connection references created
- [ ] All environment variables defined
- [ ] Default values appropriate for target environment
- [ ] Service account permissions verified
- [ ] Flows tested with connection references
- [ ] Documentation updated

✅ After deploying:
- [ ] All connection references configured
- [ ] All environment variables set correctly
- [ ] Flows enabled
- [ ] Test flow execution
- [ ] Verify data writes to correct location
- [ ] Check email notifications sent from correct account
- [ ] Validate with end users
```

### Assignment Deliverable

✅ **Create solution with connection references:**

- New solution
- 3 connection references (SharePoint, Outlook, etc.)
- 2 environment variables (URLs, config values)
- 2 flows using references
- Document structure

✅ **Deploy across environments:**

- Export solution from Dev
- Import to Test (or second environment)
- Configure connection references
- Set environment variable values
- Test functionality
- Document process

✅ **Create deployment guide:**

- Step-by-step import instructions
- Configuration values needed
- Connection setup
- Testing checklist
- Troubleshooting tips

### Knowledge Check

- [ ] What's a connection reference?
- [ ] Why use connection references?
- [ ] How do you create one?
- [ ] What's an environment variable?
- [ ] How do you use them in flows?
- [ ] What's a managed solution?
- [ ] What's solution versioning?
- [ ] Why use service accounts?
- [ ] How do you deploy across environments?
- [ ] How do you troubleshoot connection issues?

### Resources

- [Microsoft Learn: Connection references](https://learn.microsoft.com/en-us/power-apps/maker/data-platform/create-connection-reference)
- [Microsoft Learn: Environment variables](https://learn.microsoft.com/en-us/power-apps/maker/data-platform/environmentvariables)
- [Microsoft Learn: Solution concepts](https://learn.microsoft.com/en-us/power-platform/alm/solution-concepts-alm)
- [Microsoft Learn: ALM overview](https://learn.microsoft.com/en-us/power-platform/alm/)

---

## Stage 4 Part 3 Progress Summary

### Modules Completed in This Section:

- ✅ Module 26: Canvas App Integration with Flows (2 hours)
- ✅ Module 27: Premium Connectors (1 hour)
- ✅ Module 28: Connection References (1 hour)

### Completed in Part 3: 4 hours

### Cumulative Stage 4 Progress: 11.5 hours of 25.5 hours

### Stage 4 Total Completed:

- Part 1: 5 hours
- Part 2: 2.5 hours
- Part 3: 4 hours

### Remaining Stage 4 Modules:

- Module 29: Scope Actions (1 hour)
- Module 30: Child Flows (2 hours)
- Module 31: Run-as User (1 hour)
- Module 32: Flow Error Handling (2 hours)

**Remaining: 6 hours for Stage 4**

### Next Up

Scope actions (Try-Catch-Finally patterns), Child Flows (reusable components), Run-as User configuration, and comprehensive Error Handling.

---

**Type "NEXT" to continue with Scope Actions, Child Flows, Run-as User, and Flow Error Handling.**
