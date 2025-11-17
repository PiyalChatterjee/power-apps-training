# Power Platform Training Manual - Stage 4 Part 5: Run-as User & Error Handling

## Overview

This section covers run-as user configuration, service accounts, comprehensive error handling strategies, retry policies, and flow monitoring. **Duration: 3 hours**

---

## Module 31: Run-as User (1 hour)

### Learning Objectives

- Understand flow execution context
- Configure run-as user settings
- Use service accounts effectively
- Manage permissions and security
- Handle user context scenarios
- Implement delegation properly

### Step-by-Step Instructions

#### Step 1: Understanding Flow Execution Context

**Who Does the Flow Run As?**

```
Question: When a flow runs, whose permissions are used?

Answer depends on trigger type and configuration:

1. Automated Triggers (SharePoint, Dataverse):
   Default: Flow owner's permissions

2. Manual Triggers (Button, Canvas app):
   Default: User who triggers the flow

3. Scheduled Triggers:
   Always: Flow owner's permissions

4. HTTP Request Triggers:
   Default: Flow owner's permissions
```

**Example Scenario:**

```
Scenario: Auto - Leave Request - Send Notification

Flow owner: flowservice@company.com
Trigger: When item is created in SharePoint

Employee (john@company.com) creates leave request

Actions:
1. Get employee details from Employees list
2. Get manager details from Managers list
3. Send email notification
4. Update leave request status

Question: Who's permissions are used for each action?
Answer: flowservice@company.com (flow owner)

Result:
- Flow can access all lists if service account has permission
- Doesn't matter if john@company.com has access or not
```

#### Step 2: Flow Owner vs Flow Invoker

**Connection Types:**

```
1. Flow Owner Connection (Default)
   - Uses flow owner's connection
   - Flow owner's permissions apply
   - Consistent behavior for all users
   - Best for automated processes

2. Flow Invoker Connection (Run-only users)
   - Uses each user's own connection
   - Each user's permissions apply
   - Different results for different users
   - Best for user-specific actions
```

**Visual Representation:**

```
FLOW OWNER CONNECTION:
Employee A triggers → Flow runs as Owner → Owner's permissions
Employee B triggers → Flow runs as Owner → Owner's permissions
Employee C triggers → Flow runs as Owner → Owner's permissions
Result: Consistent for all users

INVOKER CONNECTION:
Employee A triggers → Flow runs as A → A's permissions
Employee B triggers → Flow runs as B → B's permissions
Employee C triggers → Flow runs as C → C's permissions
Result: Different for each user based on their permissions
```

#### Step 3: Configure Run-only Users

**Share Flow with Run-only Permission:**

```
1. Open flow in Power Automate
2. Click "Share" button
3. Add users or groups
4. Select permission level:

   ☐ Co-owner
     - Can edit flow
     - Can share with others
     - Uses their own connections

   ☑ Run-only user
     - Cannot edit flow
     - Can only run/trigger flow
     - Uses connections you specify
```

**Run-only User Settings:**

```
After selecting "Run-only user":

Connection Used:
  ○ Provided by run-only user
    - Each user must create their own connections
    - Flow uses each user's permissions
    - Good for: User-specific data access

  ● Use this connection ([connection name])
    - All users share same connection (flow owner's)
    - Consistent behavior
    - Good for: Automated processes

⚠️ Important: If you select "Use this connection",
all users will effectively run as the connection owner
```

#### Step 4: Service Account Strategy

**Why Use Service Accounts:**

```
✅ Benefits:
- Consistent permissions across all users
- Flow doesn't break when employee leaves
- Centralized security management
- Easier auditing (all actions from one account)
- Separates user permissions from automation permissions

❌ Without Service Account:
- Flow breaks if owner leaves company
- Permissions tied to individual user
- Hard to manage at scale
- Security audit challenges
```

**Service Account Setup:**

```
1. Create Service Account:
   Email: flowservice@company.com (or similar)
   Name: Flow Service Account
   Type: Regular user account (not guest)

2. Assign Licenses:
   - Microsoft 365 license (for Office 365 connectors)
   - Power Automate per user license (for premium)
   - Dynamics 365 license (if using Dataverse)

3. Grant Permissions:
   SharePoint: Site collection admin or appropriate level
   SQL Server: Database user with required permissions
   APIs: API access credentials
   Email: Shared mailbox or send-as permissions

4. Secure Account:
   - Strong password
   - Multi-factor authentication
   - Restricted sign-in (only from trusted locations)
   - Regular password rotation
   - Audit logging enabled

5. Create Connections:
   - Sign in as service account
   - Create all connections (SharePoint, SQL, etc.)
   - Test connections
   - Use in flows

6. Build Flows:
   - Create flows using service account
   - All actions use service account connections
   - Share flows with run-only permission to users
```

#### Step 5: Practical Example - Leave Management

**Scenario Setup:**

```
Requirements:
- All employees can submit leave requests
- Not all employees have access to Employees list
- Flow needs to read from Employees list
- Flow needs to update leave balance
- Flow needs to send emails from shared mailbox

Solution: Service account with elevated permissions
```

**Implementation:**

```
Service Account: leaveflow@company.com

Permissions:
- SharePoint Site: Leave Management
  - Leave Requests list: Contribute
  - Employees list: Read
  - Leave Balance list: Edit
- Office 365:
  - Send-as permission for leavemanagement@company.com
- Power Automate:
  - Per user license for premium connectors

Flow: Auto - Leave Request - Submit
Owner: leaveflow@company.com
Trigger: When item is created (Leave Requests)

Connection Configuration:
- SharePoint: leaveflow@company.com connection
  - Can read Employees (employees can't)
  - Can update Leave Balance (employees can't)
- Office 365 Outlook: leaveflow@company.com
  - Sends from leavemanagement@company.com

Share Settings:
- Share with: All Employees group
- Permission: Run-only user
- Connection: Use this connection (leaveflow@company.com)

Result:
- Any employee can create leave request item
- Flow automatically runs with elevated permissions
- Flow can access restricted lists
- Emails sent from shared mailbox
- No permission errors for users
```

#### Step 6: User Context in Flow Logic

**Scenario: Need to Know Who Triggered Flow:**

```
Problem: Flow runs as service account, but we need to know
which user actually triggered it

Solution: Capture user context from trigger
```

**Get Current User:**

```
Automated Flow (SharePoint):
Trigger: When item is created
Created By: @{triggerBody()?['Author/Email']}
Modified By: @{triggerBody()?['Editor/Email']}

Manual/Instant Flow:
Trigger: Manually trigger a flow or PowerApps
Current User: Use "Office 365 Users - Get user profile (V2)"
User (UPN): @{triggerBody()['user']['email']} or User().Email from app

Scheduled Flow:
No current user (flow just runs on schedule)
```

**Store User Context:**

```
Action: Initialize variable - varCurrentUser
Value: @{triggerBody()?['Author/Email']}

Action: Office 365 Users - Get user profile (V2)
User (UPN): @{variables('varCurrentUser')}

Action: Compose - User Details
Inputs:
{
  "Email": "@{variables('varCurrentUser')}",
  "DisplayName": "@{body('Get_user_profile_(V2)')?['displayName']}",
  "Department": "@{body('Get_user_profile_(V2)')?['department']}",
  "JobTitle": "@{body('Get_user_profile_(V2)')?['jobTitle']}",
  "Manager": "@{body('Get_user_profile_(V2)')?['manager']}"
}

Use throughout flow:
- Audit logging: Who performed action
- Conditional logic: If user is manager
- Notifications: Personalized messages
- Data filtering: Show only user's data
```

#### Step 7: Permission Delegation Scenarios

**Scenario 1: Manager Approvals**

```
Requirement: Only managers can approve leave requests

Bad Approach ❌:
- Give all users edit access to Leave Requests
- Users could approve their own requests
- Security risk

Good Approach ✅:
- Flow runs as service account (has edit permission)
- Flow checks if current user is a manager
- Only managers can trigger approval flow
- Flow updates status (not user directly)
```

**Implementation:**

```
Flow: Manual - Leave Request - Approve
Trigger: Manually trigger a flow
(User clicks button in app or SharePoint)

Action: Initialize variable - varCurrentUserEmail
Value: @{triggerBody()['user']['email']}

Action: Get items - Check If Manager
List: Employees
Filter: Email eq '@{variables('varCurrentUserEmail')}' and IsManager eq true

Action: Condition - Is Manager
If @{greater(length(body('Get_items')?['value']), 0)}:
  YES:
    Get leave request details
    Update status to Approved
    Update leave balance
    Send notifications
  NO:
    Respond to PowerApp: "You don't have permission to approve requests"
    Terminate

Result:
- Users can't edit directly (no SharePoint permissions needed)
- Only managers can approve (checked in flow)
- Flow uses service account to update (consistent permissions)
```

**Scenario 2: User-Specific Data**

```
Requirement: Users can only see their own leave requests

Approach:
- Flow runs as service account (can see all data)
- Flow filters by current user
- Returns only user's data
```

**Implementation:**

```
Flow: Manual - Get My Leave Requests
Trigger: PowerApps (V2)
Input: UserEmail (from User().Email in app)

Action: Get items - My Leave Requests
List: Leave Requests
Filter: EmployeeEmail eq '@{triggerBody()['text']}'
Order By: SubmittedDate desc

Action: Respond to PowerApp
Outputs:
  RequestsJSON: @{body('Get_items')?['value']}
  Count: @{length(body('Get_items')?['value'])}

Canvas App:
OnVisible of My Requests Screen:
ClearCollect(
    colMyRequests,
    'Manual-GetMyLeaveRequests'.Run(User().Email).requestsjson
)

Result:
- Service account can query all records
- Filter ensures user only gets their data
- User doesn't need read access to entire list
```

#### Step 8: Impersonation and Send-As

**Email Send-As Permissions:**

```
Scenario: Send emails from shared mailbox

Setup:
1. Create shared mailbox: leavemanagement@company.com
2. Grant send-as permission to service account:

   Exchange Admin Center → Recipients → Mailboxes
   → Select shared mailbox → Manage mailbox delegation
   → Send As → Add leaveflow@company.com

3. In flow:
   Action: Send an email (V2)
   From (Send as): leavemanagement@company.com
   To: @{body('Get_manager')?['Email']}
   Subject: New Leave Request
   Body: [Content]

Result: Email appears to come from leavemanagement@company.com
```

**SharePoint Impersonation:**

```
Scenario: Create items as specific user

Problem: Flow creates items, Created By shows service account

Solution: Not directly supported in SharePoint connector
Workaround: Store original user in separate column

Action: Create item
Title: @{triggerBody()?['Title']}
CreatedByUser: @{triggerBody()?['Author/Email']}  (Custom column)

Or use "Send an HTTP request to SharePoint" with X-RequestDigest
(Advanced, requires HTTP calls)
```

#### Step 9: Troubleshooting Permissions

**Common Issues:**

```
Issue 1: "Access denied" error
Cause: Connection owner doesn't have permission
Solution:
  - Check connection owner's SharePoint/SQL permissions
  - Grant necessary access
  - Test connection
  - Re-run flow

Issue 2: Flow works for owner but not for users
Cause: Run-only users configured to use their own connections
Solution:
  - Edit share settings
  - Change to "Use this connection"
  - Specify service account connection

Issue 3: User can't trigger flow
Cause: User doesn't have permission to trigger
Solution:
  - Share flow with user
  - For SharePoint: User needs to create items in list
  - For manual: User needs to be in shared users list

Issue 4: Email fails with "Send-As" error
Cause: Service account doesn't have send-as permission
Solution:
  - Grant send-as in Exchange admin
  - Wait 15 minutes for propagation
  - Test in flow
```

**Debug Permissions:**

```
Add to flow for troubleshooting:

Action: Compose - Current Execution Context
Inputs:
{
  "FlowOwner": "@{workflow()?['tags']['logicAppName']}",
  "FlowRunID": "@{workflow()?['run']['name']}",
  "ConnectionOwner": "[Check in connection settings]",
  "TriggerUser": "@{triggerBody()?['Author/Email']}",
  "Timestamp": "@{utcNow()}"
}

Action: Create item - Debug Log
List: Flow Debug Log
ExecutionContext: @{outputs('Compose_-_Current_Execution_Context')}

Helps identify:
- Which account is being used
- When permission issues occur
- Who triggered the flow
```

#### Step 10: Best Practices

**Security Best Practices:**

```
✅ DO:
- Use service accounts for automation
- Grant least privilege (minimum permissions needed)
- Use shared mailboxes for flow emails
- Implement user checks in flow logic
- Audit service account usage regularly
- Rotate service account passwords
- Document permission requirements
- Test with non-admin accounts
- Use connection references in solutions

❌ DON'T:
- Use personal accounts for production flows
- Grant global admin to service accounts
- Share service account passwords
- Use same service account for all flows
- Hard-code permissions in flow logic
- Skip testing with regular user accounts
- Forget to document permission needs
```

**Service Account Strategy:**

```
Small Organization:
- 1 service account for all flows
- Simple to manage
- Easier to audit

Large Organization:
- Multiple service accounts by function:
  - leaveflow@company.com (Leave management)
  - hrflow@company.com (HR processes)
  - financeflow@company.com (Finance workflows)
- Better security isolation
- Granular permission control
- Easier to track usage
```

**Documentation Template:**

```
Flow: Auto - Leave Request - Submit
Owner: leaveflow@company.com

Required Permissions:

Service Account (leaveflow@company.com):
✓ SharePoint Site: Leave Management
  - Leave Requests list: Contribute
  - Employees list: Read
  - Leave Balance list: Edit
✓ Office 365:
  - Send As: leavemanagement@company.com
✓ Licenses:
  - Microsoft 365 E3
  - Power Automate per user

User Requirements:
✓ SharePoint:
  - Leave Requests list: Create items only
✓ Flow:
  - Run-only access (no additional license needed)

Testing:
✓ Tested with regular user (no admin rights)
✓ Confirmed access to restricted lists via service account
✓ Verified email send-as working
✓ Checked audit logs for service account actions
```

### Assignment Deliverable

✅ **Create service account setup:**

- Create test service account (or document process)
- Grant necessary permissions
- Create connections
- Build flow using service account
- Share with run-only users
- Test with non-admin account

✅ **Build permission-aware flow:**

- Flow runs as service account
- Captures current user context
- Checks user permissions
- Performs user-specific filtering
- Logs who triggered flow

✅ **Document permission requirements:**

- Service account permissions needed
- User permissions required
- Security considerations
- Troubleshooting guide

### Knowledge Check

- [ ] Who does flow run as by default?
- [ ] What's a service account?
- [ ] What's run-only user permission?
- [ ] How do you capture current user?
- [ ] What's send-as permission?
- [ ] How do you share flow with users?
- [ ] What's the difference between owner and invoker?
- [ ] How do you check user permissions in flow?
- [ ] What are security best practices?
- [ ] How do you troubleshoot permission errors?

### Resources

- [Microsoft Learn: Share flows](https://learn.microsoft.com/en-us/power-automate/create-team-flows)
- [Microsoft Learn: Connection references](https://learn.microsoft.com/en-us/power-apps/maker/data-platform/create-connection-reference)
- [Microsoft Learn: Security roles](https://learn.microsoft.com/en-us/power-platform/admin/security-roles-privileges)
- [Blog: Service account strategies](https://powerusers.microsoft.com/t5/Power-Automate-Community-Blog/bg-p/FlowBlog)

---

## Module 32: Flow Error Handling (2 hours)

### Learning Objectives

- Implement comprehensive error handling
- Use Configure run after settings
- Create error notification systems
- Implement retry policies
- Log errors systematically
- Monitor flow health
- Handle timeouts and throttling

### Step-by-Step Instructions

#### Step 1: Understanding Flow Errors

**Common Error Types:**

```
1. Connector Errors:
   - Service unavailable (503)
   - Timeout (408)
   - Too many requests (429 - Throttling)
   - Unauthorized (401, 403)
   - Not found (404)

2. Data Errors:
   - Invalid format
   - Missing required fields
   - Type conversion errors
   - Null reference errors

3. Logic Errors:
   - Division by zero
   - Array index out of bounds
   - Invalid expression syntax
   - Incorrect permissions

4. System Errors:
   - Flow timeout
   - Action limit exceeded
   - Memory limit exceeded
   - Concurrent runs limit
```

**Error Information Available:**

```
@{actionName}
{
  "status": "Failed",
  "error": {
    "code": "ItemNotFound",
    "message": "Item with ID 12345 was not found"
  },
  "startTime": "2025-11-14T10:00:00Z",
  "endTime": "2025-11-14T10:00:05Z"
}

Access error details:
Status: @{result('ActionName')[0]['status']}
Error Code: @{result('ActionName')[0]['error']['code']}
Error Message: @{result('ActionName')[0]['error']['message']}
```

#### Step 2: Configure Run After

**Default Behavior:**

```
By default, actions run only if previous action succeeded:

Action 1: Get employee ✅ Succeeded
Action 2: Update item → Runs
Action 3: Send email → Runs

Action 1: Get employee ❌ Failed
Action 2: Update item → Skipped
Action 3: Send email → Skipped

Flow status: Failed
```

**Configure Run After Options:**

```
Click ⋯ (three dots) on any action → Configure run after

Options:
☑ is successful (default - action succeeded)
☐ has failed (action failed)
☐ is skipped (action was skipped)
☐ has timed out (action timed out)

Select multiple options to run under various conditions
```

**Example: Error Handler Action**

```
Action: Get items - Find Employee
(Might fail if employee not found)

Action: Log Error
Configure run after: Get items "has failed"
☑ has failed
☐ is successful
☐ is skipped
☐ has timed out

Action: Create item - Error Log
List: Error Log
ErrorMessage: @{result('Get_items_-_Find_Employee')[0]['error']['message']}
Timestamp: @{utcNow()}

Result: Log Error only runs if Get items fails
```

#### Step 3: Try-Catch-Finally Pattern (Revisited)

**Complete Implementation:**

```
Scope: Try - Main Logic
  Action: Get items - Employee
  Action: Get items - Leave Balance
  Action: Condition - Check Balance
  Action: Update item - Leave Request
  Action: Send email - Notification

Scope: Catch - Error Handler
  Configure run after: Try
  ☑ has failed
  ☑ has timed out

  Action: Initialize variable - varErrorDetails
  Value:
  {
    "FlowName": "@{workflow()?['name']}",
    "RunID": "@{workflow()?['run']['name']}",
    "ErrorTime": "@{utcNow()}",
    "TriggerData": "@{triggerBody()}",
    "ErrorScope": "Try",
    "FailedActions": []
  }

  Action: Apply to each - Find Failed Actions
  Items: @{result('Try')}

  Condition: @{equals(item()?['status'], 'Failed')}
    YES:
      Append to array: varErrorDetails.FailedActions
      Value:
      {
        "ActionName": "@{item()?['name']}",
        "ErrorCode": "@{item()?['error']?['code']}",
        "ErrorMessage": "@{item()?['error']?['message']}",
        "Status": "@{item()?['status']}"
      }

  Action: Create item - Error Log
  List: Flow Errors
  ErrorDetails: @{variables('varErrorDetails')}
  Severity: High
  Status: New

  Action: Send an email (V2) - Alert IT
  To: it-support@company.com
  Subject: 🚨 Flow Error - @{workflow()?['name']}
  Body:
  <h2>Flow Execution Failed</h2>
  <p><strong>Flow:</strong> @{workflow()?['name']}</p>
  <p><strong>Run ID:</strong> @{workflow()?['run']['name']}</p>
  <p><strong>Time:</strong> @{utcNow()}</p>
  <p><strong>Trigger:</strong> @{triggerBody()?['ID']}</p>

  <h3>Failed Actions:</h3>
  <pre>@{variables('varErrorDetails')}</pre>

  <p><a href="https://make.powerautomate.com">View in Power Automate</a></p>

  Action: Condition - Critical Error
  If errorCode contains "Unauthorized" OR "Forbidden":
    YES:
      Send high-priority alert
      Create incident ticket
      Notify manager

Scope: Finally - Cleanup
  Configure run after: Try + Catch
  ☑ is successful
  ☑ has failed
  ☑ is skipped
  ☑ has timed out

  Action: Compose - Execution Summary
  Inputs:
  {
    "Completed": "@{utcNow()}",
    "TrySucceeded": "@{equals(result('Try')[0]['status'], 'Succeeded')}",
    "ErrorsLogged": "@{not(empty(result('Catch')))}",
    "Duration": "@{workflow()?['run']?['duration']}"
  }

  Action: Create item - Audit Log
  List: Flow Audit
  Summary: @{outputs('Compose_-_Execution_Summary')}
```

#### Step 4: Retry Policies

**Automatic Retry:**

```
Power Automate automatically retries failed actions:

Default Retry Policy:
- Retry Count: 4 times
- Retry Interval: Exponential backoff
  - 1st retry: 20 seconds
  - 2nd retry: 40 seconds
  - 3rd retry: 80 seconds
  - 4th retry: 160 seconds
- Total retry period: ~5 minutes

Applies to: Transient errors (timeouts, 5xx errors)
Does NOT retry: 4xx errors (bad request, unauthorized)
```

**Custom Retry Policy:**

```
Settings → Action Settings → Networking

Retry Policy:
  Type: Exponential OR Fixed

Exponential Interval:
  Count: 4 (1-90 retries)
  Interval: PT20S (format: PTnnn S/M/H)
  Minimum Interval: PT20S
  Maximum Interval: PT1H

Fixed Interval:
  Count: 3
  Interval: PT30S (retry every 30 seconds)

None:
  No retry (fail immediately)
```

**When to Customize Retry:**

```
✅ Increase retries for:
- Unreliable external APIs
- Network-dependent operations
- High-value operations that must succeed

✅ Decrease/disable retries for:
- Operations where retry doesn't help (validation errors)
- Time-sensitive operations
- Idempotency concerns (avoid duplicate creates)

✅ Use fixed interval for:
- APIs with rate limits
- Scheduled batch operations
- Predictable recovery times
```

**Example: HTTP with Retry:**

```
Action: HTTP - Call External API
URI: https://api.partner.com/data
Method: GET

Settings → Networking → Retry Policy:
Type: Exponential
Count: 6
Interval: PT10S
Min: PT10S
Max: PT2M

Result: Will retry up to 6 times over ~10 minutes before failing
Good for: Unreliable external APIs
```

#### Step 5: Error Notification System

**Centralized Error Handler:**

```
Create Child Flow: Error Notification Handler

Trigger: PowerApps (V2)
Inputs:
  - FlowName (Text)
  - ErrorMessage (Text)
  - ErrorDetails (Text)
  - Severity (Text): Low, Medium, High, Critical
  - TriggerData (Text): JSON of what triggered flow

Action: Compose - Error Summary
Inputs:
{
  "FlowName": "@{triggerBody()['text']}",
  "ErrorMessage": "@{triggerBody()['text_1']}",
  "Severity": "@{triggerBody()['text_3']}",
  "Time": "@{utcNow()}",
  "Details": @{triggerBody()['text_2']}
}

Action: Create item - Error Log
List: Centralized Error Log
Title: @{triggerBody()['text']} - @{triggerBody()['text_3']}
ErrorSummary: @{outputs('Compose_-_Error_Summary')}
Severity: @{triggerBody()['text_3']}
Status: New
AssignedTo: (Auto-assign based on severity)

Action: Condition - Severity Level
Switch on: @{triggerBody()['text_3']}

Case: Critical
  Action: Send an email (V2)
  To: it-manager@company.com; on-call@company.com
  Importance: High
  Subject: 🔴 CRITICAL FLOW ERROR - @{triggerBody()['text']}
  Body: [Error details with red formatting]

  Action: Create Teams message
  Team: IT Operations
  Channel: Alerts
  Message: @mentions on-call engineer

Case: High
  Action: Send an email (V2)
  To: it-support@company.com
  Importance: High
  Subject: ⚠️ Flow Error - @{triggerBody()['text']}
  Body: [Error details]

Case: Medium
  Action: Send an email (V2)
  To: flow-admins@company.com
  Subject: Flow Error - @{triggerBody()['text']}
  Body: [Error details]

Case: Low
  (Log only, no notification)

Action: Respond to PowerApp
Outputs:
  ErrorLogged: true
  TicketID: @{outputs('Create_item')?['body/ID']}
```

**Use in Parent Flows:**

```
Scope: Catch
  Action: Run Child - Error Notification Handler
  FlowName: @{workflow()?['name']}
  ErrorMessage: @{result('Try')[0]['error']['message']}
  ErrorDetails: @{string(result('Try'))}
  Severity: High
  TriggerData: @{string(triggerBody())}

  Set variable: varErrorTicketID
  Value: @{outputs('Run_Child')?['ticketid']}
```

#### Step 6: Timeout Handling

**Flow Timeout Limits:**

```
Flow Execution Limits:
- Standard: 30 days (long-running flows)
- Actions: Most actions timeout after 2 minutes
- HTTP actions: Configurable (default 2 minutes, max 1 hour)
- Canvas app calls: 120 seconds

If flow times out:
- Status: Failed
- Error: ActionTimedOut or FlowTimedOut
```

**Handle Action Timeouts:**

```
Action: HTTP - Long Running API Call
Timeout: PT10M (10 minutes)

Action: Handle Timeout
Configure run after: HTTP
☑ has timed out

Action: Compose - Timeout Message
Inputs: API call timed out after 10 minutes

Action: Condition - Retry or Fail
If attempt count < 3:
  Wait 5 minutes
  Retry API call
Else:
  Log timeout error
  Send notification
  Terminate with failure
```

**Long-Running Flow Pattern:**

```
For operations > 30 minutes:

Flow 1: Initiate Process (Fast)
  Create tracking record (Status: In Progress)
  Call Azure Function or Logic App (async)
  Respond to user: "Processing started"
  Return tracking ID

Flow 2: Status Checker (Fast, runs on schedule)
  Every 5 minutes
  Check tracking records with Status: In Progress
  For each:
    Call API to check status
    Update tracking record
    If complete: Notify user

Flow 3: Completion Handler (Triggered)
  When tracking record Status changes to Complete
  Perform final actions
  Send completion notification
```

#### Step 7: Throttling Handling

**Understanding Throttling:**

```
Throttling = Service limiting requests to protect resources

Common Scenarios:
- SharePoint: 2,000-5,000 requests per 10 minutes
- Dataverse: Based on license (pay-as-you-go model)
- Office 365: Varies by service
- APIs: Varies by API (often 100-1000 per minute)

Error: 429 - Too Many Requests
Response includes: Retry-After header (seconds to wait)
```

**Handle Throttling:**

```
Action: Get items (might be throttled)

Action: Handle Throttling
Configure run after: Get items
☑ has failed

Action: Compose - Check Error Code
Inputs: @{result('Get_items')[0]['error']['code']}

Action: Condition - Is Throttled
If errorCode equals 'TooManyRequests' OR statusCode equals 429:
  YES:
    Action: Compose - Retry After
    Inputs: @{coalesce(
      result('Get_items')[0]['error']['retryAfter'],
      '60'
    )}

    Action: Delay
    For: @{outputs('Compose_-_Retry_After')} seconds

    Action: Get items (retry)
    (Same configuration)

  NO:
    Different error, handle accordingly
```

**Prevent Throttling:**

```
✅ Best Practices:
1. Batch operations instead of individual calls
2. Use pagination with reasonable page sizes
3. Add delays between operations in loops
4. Use Filter queries to reduce data retrieved
5. Cache frequently accessed data
6. Spread scheduled flows across different times
7. Monitor throttling in flow analytics

Example: Process 10,000 items

❌ BAD:
Apply to each (10,000 items)
  Get item details (10,000 API calls)
  Update item (10,000 API calls)
Result: 20,000 calls → THROTTLED

✅ GOOD:
Get items (top 100, with pagination)
Do Until all processed:
  Apply to each (100 items)
    Update item
  Delay 30 seconds
  Get next 100 items
Result: Controlled rate, no throttling
```

#### Step 8: Logging and Monitoring

**Structured Logging:**

```
Create SharePoint List: Flow Execution Log

Columns:
- FlowName (Text)
- RunID (Text)
- Status (Choice): Success, Failed, Timeout, Throttled
- StartTime (DateTime)
- EndTime (DateTime)
- Duration (Number) - seconds
- RecordsProcessed (Number)
- ErrorCount (Number)
- ErrorDetails (Multiple lines)
- TriggerType (Text)
- TriggerData (Multiple lines)

In every flow:

Scope: Initialize
  Action: Initialize variable - varStartTime
  Value: @{utcNow()}

  Action: Initialize variable - varRecordsProcessed
  Value: 0

  Action: Initialize variable - varErrorCount
  Value: 0

Scope: Main Logic
  [Your flow logic]
  Increment varRecordsProcessed as needed

Scope: Finally - Log Execution
  Action: Compose - Calculate Duration
  Inputs: @{div(
    sub(
      ticks(utcNow()),
      ticks(variables('varStartTime'))
    ),
    10000000
  )}

  Action: Compose - Determine Status
  Inputs: @{if(
    equals(result('Try')[0]['status'], 'Succeeded'),
    'Success',
    if(
      contains(result('Try')[0]['error']['message'], 'timeout'),
      'Timeout',
      if(
        contains(result('Try')[0]['error']['code'], 'TooMany'),
        'Throttled',
        'Failed'
      )
    )
  )}

  Action: Create item - Log Entry
  List: Flow Execution Log
  FlowName: @{workflow()?['name']}
  RunID: @{workflow()?['run']['name']}
  Status: @{outputs('Compose_-_Determine_Status')}
  StartTime: @{variables('varStartTime')}
  EndTime: @{utcNow()}
  Duration: @{outputs('Compose_-_Calculate_Duration')}
  RecordsProcessed: @{variables('varRecordsProcessed')}
  ErrorCount: @{variables('varErrorCount')}
  ErrorDetails: @{if(
    equals(outputs('Compose_-_Determine_Status'), 'Success'),
    '',
    string(result('Try'))
  )}
  TriggerType: @{workflow()['tags']['environmentName']}
  TriggerData: @{string(triggerBody())}
```

**Dashboard Queries:**

```
Failed Flows (Last 24 Hours):
Filter: Status eq 'Failed' and StartTime ge '@{addDays(utcNow(), -1)}'
Order By: StartTime desc

Slow Flows (Duration > 5 minutes):
Filter: Duration gt 300
Order By: Duration desc

Frequently Failing Flows:
Group by FlowName
Count failures
Sort by count desc

Throttled Flows:
Filter: Status eq 'Throttled'
```

#### Step 9: Health Monitoring Flow

**Scheduled Flow: Monitor Flow Health**

```
Flow: Scheduled - Monitor Flow Health
Trigger: Recurrence - Every hour

Action: Get items - Recent Errors
List: Flow Execution Log
Filter: Status eq 'Failed' and StartTime ge '@{addHours(utcNow(), -1)}'
Order By: StartTime desc

Action: Condition - Has Errors
If @{greater(length(body('Get_items')?['value']), 0)}:
  YES:
    Action: Apply to each - Analyze Errors
    Items: @{body('Get_items')?['value']}

    Inside loop:
      Action: Compose - Error Pattern
      Check if error is:
        - Recurring (same flow fails repeatedly)
        - Critical (affects multiple flows)
        - Transient (timeout, throttling)

      Action: Condition - Recurring Error
      If same FlowName failed 3+ times in hour:
        Create high-priority alert
        Assign to on-call engineer

    Action: Compose - Error Summary
    Inputs:
    {
      "TotalErrors": @{length(body('Get_items')?['value'])},
      "UniqueFlows": @{length(union(...))},
      "MostCommonError": "[Analysis]",
      "RecommendedAction": "[Based on patterns]"
    }

    Action: Send an email (V2) - Hourly Summary
    To: flow-admins@company.com
    Subject: Flow Health Summary - @{length(body('Get_items')?['value'])} errors
    Body:
    <h2>Flow Health Report</h2>
    <p><strong>Period:</strong> Last hour</p>
    <p><strong>Total Errors:</strong> @{length(body('Get_items')?['value'])}</p>

    @{body('Create_HTML_table')}

    <h3>Action Required:</h3>
    <ul>
      @{if(
        greater(length(body('Get_items')?['value']), 10),
        '<li>⚠️ High error rate detected</li>',
        ''
      )}
    </ul>

Action: Get items - Successful Runs
List: Flow Execution Log
Filter: Status eq 'Success' and StartTime ge '@{addHours(utcNow(), -24)}'

Action: Compose - Calculate Success Rate
Inputs: @{div(
  mul(
    length(body('Get_items_-_Successful_Runs')?['value']),
    100
  ),
  add(
    length(body('Get_items_-_Successful_Runs')?['value']),
    length(body('Get_items_-_Recent_Errors')?['value'])
  )
)}%

Action: Condition - Low Success Rate
If success rate < 95%:
  Send alert to management
  Mark as critical issue
```

#### Step 10: Error Handling Checklist

**Comprehensive Error Strategy:**

```
✅ For Every Production Flow:

1. Try-Catch-Finally Pattern
   ☑ Try scope with main logic
   ☑ Catch scope with error handling
   ☑ Finally scope with cleanup/logging

2. Error Logging
   ☑ Log to centralized error list
   ☑ Include flow name, run ID, timestamp
   ☑ Capture full error details
   ☑ Store trigger data for reproduction

3. Error Notifications
   ☑ Email to IT support (severity-based)
   ☑ Teams message for critical errors
   ☑ User notification for user-facing errors
   ☑ Dashboard updates

4. Retry Configuration
   ☑ Appropriate retry policy on API calls
   ☑ Retry logic for transient errors
   ☑ Exponential backoff for throttling
   ☑ Max retry limits

5. Timeout Handling
   ☑ Set appropriate timeouts
   ☑ Handle timeout scenarios
   ☑ Alternative paths for slow operations
   ☑ Status tracking for long processes

6. Monitoring
   ☑ Execution logging (success + failure)
   ☑ Performance metrics (duration)
   ☑ Health monitoring flow
   ☑ Success rate tracking
   ☑ Error trend analysis

7. Documentation
   ☑ Known error scenarios
   ☑ Troubleshooting guide
   ☑ Escalation procedures
   ☑ Recovery steps

8. Testing
   ☑ Test failure scenarios
   ☑ Verify error notifications work
   ☑ Confirm logging captures details
   ☑ Validate retry logic
   ☑ Check timeout handling
```

### Assignment Deliverable

✅ **Build comprehensive error handling flow:**

- Try-Catch-Finally structure
- Configure run after settings
- Multiple error types handled
- Logging to error list
- Email notifications
- Test all error paths

✅ **Create error notification system:**

- Child flow for error handling
- Severity-based routing
- Centralized error log
- Alert mechanisms
- Status dashboard

✅ **Implement monitoring:**

- Execution logging
- Health check flow
- Success rate calculation
- Error trend reporting
- Alerting for patterns

✅ **Document error handling strategy:**

- Error types and handling
- Notification procedures
- Escalation paths
- Recovery procedures
- Testing checklist

### Knowledge Check

- [ ] What are common flow error types?
- [ ] What's Configure run after?
- [ ] How do you implement Try-Catch?
- [ ] What's a retry policy?
- [ ] How do you handle timeouts?
- [ ] What's throttling?
- [ ] How do you log errors?
- [ ] What's result() function?
- [ ] How do you create error notifications?
- [ ] What should be monitored?

### Resources

- [Microsoft Learn: Error handling](https://learn.microsoft.com/en-us/power-automate/error-handling)
- [Microsoft Learn: Retry policies](https://learn.microsoft.com/en-us/azure/logic-apps/logic-apps-exception-handling)
- [Microsoft Learn: Limits and configuration](https://learn.microsoft.com/en-us/power-automate/limits-and-config)
- [Microsoft Learn: Monitor flows](https://learn.microsoft.com/en-us/power-automate/monitor-manage-processes)
- [Blog: Error handling best practices](https://powerusers.microsoft.com/t5/Power-Automate-Community-Blog/bg-p/FlowBlog)

---

## Stage 4 Complete! 🎉

### Total Stage 4 Summary

**Stage 4 - Cloud Flows: 25.5 hours completed**

#### Part 1 (5 hours):

- Module 22: Cloud Flow Triggers
- Module 23: Flow Actions and Expressions

#### Part 2 (2.5 hours):

- Module 24: Flow Naming Conventions
- Module 25: Data Operations in Flows

#### Part 3 (4 hours):

- Module 26: Canvas App Integration with Flows
- Module 27: Premium Connectors
- Module 28: Connection References

#### Part 4 (3 hours):

- Module 29: Scope Actions
- Module 30: Child Flows

#### Part 5 (3 hours):

- Module 31: Run-as User
- Module 32: Flow Error Handling

### Key Achievements

✅ Mastered Power Automate fundamentals
✅ Built complex data operations and transformations
✅ Integrated Canvas apps with flows seamlessly
✅ Worked with premium connectors (SQL, HTTP, Azure)
✅ Implemented solution-based deployments
✅ Created reusable child flows
✅ Established comprehensive error handling
✅ Configured run-as user and security
✅ Built monitoring and logging systems

### Skills Acquired

- Flow triggers (automated, manual, scheduled)
- Expression functions (200+ functions)
- Data operations (Filter, Select, loops)
- Canvas-Flow integration
- SQL Server and HTTP connectors
- Connection references and environment variables
- Try-Catch-Finally patterns
- Child flow architecture
- Service account configuration
- Error handling and monitoring

### Overall Training Progress

**Completed:**

- ✅ Stage 1: Power Apps Fundamentals (16.5 hours)
- ✅ Stage 2: Canvas Apps Intermediate (29 hours)
- ✅ Stage 3: Advanced Canvas Apps (36 hours)
- ✅ Stage 4: Cloud Flows (25.5 hours)

**Total Completed: 107 hours of 170 hours (62.9%)**

**Remaining:**

- ⏳ Stage 5: Dataverse (10 hours)
- ⏳ Stage 6: Model Driven Apps, Power Pages, Solutions & Final Project (53 hours)

### Next Stage Preview

**Stage 5: Dataverse** will cover:

- Column types and data modeling
- Forms and views
- Security roles and permissions
- Relationships (1:N, N:1, N:N)
- Business rules
- Dataverse APIs
- Data import/export
- Advanced data modeling for Leave Management System

---

**Type "NEXT" to begin Stage 5: Dataverse**
