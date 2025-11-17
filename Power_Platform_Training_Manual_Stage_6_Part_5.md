# Power Platform Training Manual - Stage 6 Part 5: Final Project - Complete Leave Management System

## Overview

This is the final part of Stage 6, where you'll build a complete, production-ready Leave Management System using all the skills learned throughout the training. This comprehensive project integrates Canvas Apps, Model-Driven Apps, Power Automate, Dataverse, Power Pages, and proper ALM practices. **This part covers 22 hours of intensive project work.**

**Stage 6 Total Duration: 53 hours** | **Part 5: 22 hours**

---

## Project Scope and Requirements

### Business Scenario

**Contoso Corporation** needs a comprehensive Leave Management System that allows:

- **Employees** to submit and track leave requests
- **Managers** to approve/reject requests and view team calendars
- **HR** to manage leave policies, balances, and reporting
- **External access** for remote employees via Power Pages
- **Integration** with SharePoint, Teams, and Outlook
- **Mobile access** for field employees
- **Automated workflows** for approvals and notifications

### Technical Requirements

```
Architecture Components:

📱 Canvas Apps:
- Employee Leave Request App (Mobile-optimized)
- Manager Dashboard App
- HR Analytics App

🖥️ Model-Driven Apps:
- Leave Management Admin (HR)
- Leave Policy Configuration
- Reporting and Analytics

🌐 Power Pages:
- External Employee Portal
- Leave Request Submission
- Status Tracking

🔄 Power Automate:
- Approval workflows
- Notification systems
- Integration flows
- Scheduled processes

🗄️ Dataverse:
- Complete data model
- Security roles
- Business rules
- Views and forms

🔧 ALM Implementation:
- Solution architecture
- Environment strategy
- CI/CD pipeline
- Monitoring and maintenance
```

---

## Module 50: Project Planning and Architecture (2 hours)

### Step 1: Requirements Analysis

**Functional Requirements:**

```
Employee Features:
✅ Submit leave requests
✅ View leave balance
✅ Track request status
✅ View leave calendar
✅ Receive notifications
✅ Attach supporting documents
✅ Cancel pending requests
✅ View leave history

Manager Features:
✅ Approve/reject requests
✅ View team calendar
✅ Delegate approval authority
✅ Bulk approve requests
✅ Generate team reports
✅ Set coverage requirements
✅ Receive approval reminders

HR Features:
✅ Manage leave policies
✅ Set employee balances
✅ Generate compliance reports
✅ Audit leave records
✅ Manage organizational calendar
✅ Configure approval chains
✅ Handle exceptions
```

**Non-Functional Requirements:**

```
Performance:
- Page load time < 3 seconds
- Support 1000+ concurrent users
- 99.9% uptime requirement

Security:
- Role-based access control
- Data encryption at rest and transit
- Audit trail for all actions
- Compliance with data regulations

Usability:
- Mobile-responsive design
- Intuitive user interface
- Accessibility compliance
- Multi-language support (optional)

Integration:
- Office 365 integration
- SharePoint document management
- Teams notifications
- Outlook calendar sync
```

### Step 2: Solution Architecture Design

**Practical Exercise:** Create Architecture Diagram

```
Solution Architecture:

┌─────────────────────────────────────────────────────────┐
│                    Presentation Layer                    │
├─────────────────┬─────────────────┬────────────────────┤
│   Canvas Apps   │ Model-Driven    │   Power Pages      │
│   - Employee    │   - HR Admin    │   - External Portal│
│   - Manager     │   - Config      │   - Self Service   │
│   - HR Mobile   │   - Reports     │   - Status Check   │
└─────────────────┴─────────────────┴────────────────────┘
│                    Integration Layer                     │
├─────────────────────────────────────────────────────────┤
│              Power Automate Workflows                   │
│  - Approval Process  - Notifications  - Integrations   │
└─────────────────────────────────────────────────────────┘
│                      Data Layer                         │
├─────────────────────────────────────────────────────────┤
│                    Dataverse                            │
│     Tables • Security • Business Rules • Views          │
└─────────────────────────────────────────────────────────┘
│                  Integration Layer                      │
├─────────────────────────────────────────────────────────┤
│  SharePoint • Teams • Outlook • External Systems       │
└─────────────────────────────────────────────────────────┘
```

### Step 3: Data Model Design

**Enhanced Data Model:**

```sql
-- Core Tables

Employee (Enhanced)
├── Employee ID (Primary Key)
├── Full Name
├── Email Address
├── Manager (Lookup to Employee)
├── Department
├── Job Title
├── Hire Date
├── Employment Type (Full-time/Part-time/Contract)
├── Work Location
├── Phone Number
├── Emergency Contact
├── Active Status
└── Time Zone

Leave Type (Enhanced)
├── Leave Type ID (Primary Key)
├── Name (Annual, Sick, Personal, etc.)
├── Description
├── Maximum Days Per Year
├── Carry Forward Allowed
├── Maximum Carry Forward Days
├── Approval Required
├── Supporting Documents Required
├── Advance Notice Required (Days)
├── Active Status
├── Color Code (for calendars)
└── Policy Details (Rich text)

Leave Request (Enhanced)
├── Request ID (Primary Key)
├── Employee (Lookup to Employee)
├── Leave Type (Lookup to Leave Type)
├── Start Date
├── End Date
├── Total Days (Calculated)
├── Half Day (Boolean)
├── Status (Draft, Submitted, Approved, Rejected, Cancelled)
├── Reason/Comments
├── Supporting Documents (File attachments)
├── Submitted Date
├── Approved/Rejected Date
├── Approved By (Lookup to Employee)
├── Approval Comments
├── Emergency Contact During Leave
├── Work Coverage Arrangements
├── Return Date (Actual)
├── Created On
├── Modified On
└── Approval History (Related records)

Leave Balance (Enhanced)
├── Balance ID (Primary Key)
├── Employee (Lookup to Employee)
├── Leave Type (Lookup to Leave Type)
├── Year
├── Allocated Days
├── Used Days (Calculated)
├── Pending Days (Calculated)
├── Available Days (Calculated)
├── Carried Forward Days
├── Adjustment Days
├── Adjustment Reason
├── Last Updated
└── Created Date

-- New Supporting Tables

Approval Workflow
├── Workflow ID (Primary Key)
├── Employee (Lookup to Employee)
├── Approver Level 1 (Lookup to Employee)
├── Approver Level 2 (Lookup to Employee)
├── Approver Level 3 (Lookup to Employee)
├── Department Override (Lookup to Employee)
├── HR Override (Lookup to Employee)
├── Effective Date
├── End Date
└── Active Status

Leave Calendar
├── Calendar ID (Primary Key)
├── Employee (Lookup to Employee)
├── Leave Request (Lookup to Leave Request)
├── Date
├── Leave Type (Lookup to Leave Type)
├── Status
├── Half Day
└── Notes

Holiday Calendar
├── Holiday ID (Primary Key)
├── Holiday Name
├── Date
├── Type (National, Regional, Company)
├── Applicable Locations
├── Description
└── Active Status

Delegation
├── Delegation ID (Primary Key)
├── Delegator (Lookup to Employee)
├── Delegate (Lookup to Employee)
├── Start Date
├── End Date
├── Reason
├── Active Status
└── Created Date

Audit Log
├── Audit ID (Primary Key)
├── Table Name
├── Record ID
├── Action (Create, Update, Delete)
├── Field Changes (JSON)
├── User (Lookup to Employee)
├── Timestamp
├── IP Address
└── User Agent
```

---

## Module 51: Dataverse Implementation (4 hours)

### Step 1: Create Enhanced Data Model

**Practical Exercise:** Build Complete Dataverse Schema

1. **Create Solution Structure**

   ```
   Solutions to Create:
   1. "Leave Management Core" - Base tables and security
   2. "Leave Management Apps" - Applications and flows
   3. "Leave Management Configuration" - Settings and policies
   ```

2. **Enhanced Employee Table**

   ```
   1. Create table "Employee" with columns:
      - employee_id (Text, Primary)
      - full_name (Text, Required)
      - email_address (Text, Email format)
      - manager (Lookup to Employee)
      - department (Choice: HR, IT, Sales, Marketing, Finance)
      - job_title (Text)
      - hire_date (Date)
      - employment_type (Choice: Full-time, Part-time, Contract)
      - work_location (Text)
      - phone_number (Text)
      - emergency_contact (Text)
      - active_status (Boolean)
      - time_zone (Choice: GMT-8, GMT-5, GMT, etc.)
   ```

3. **Enhanced Leave Type Table**

   ```
   Create with advanced configuration:
   - maximum_days_per_year (Whole Number)
   - carry_forward_allowed (Boolean)
   - max_carry_forward_days (Whole Number)
   - approval_required (Boolean)
   - documents_required (Boolean)
   - advance_notice_days (Whole Number)
   - policy_details (Rich Text)
   - color_code (Text) - for calendar display
   ```

4. **Comprehensive Leave Request Table**
   ```
   Enhanced with workflow support:
   - half_day (Boolean)
   - emergency_contact_leave (Text)
   - coverage_arrangements (Rich Text)
   - return_date_actual (Date)
   - approval_level (Choice: Level1, Level2, Level3, HR)
   - next_approver (Lookup to Employee)
   ```

### Step 2: Business Rules Implementation

**Practical Exercise:** Advanced Business Rules

1. **Leave Balance Validation**

   ```
   Create business rule: "Validate Leave Balance"

   Condition: Leave Request.Total Days > Leave Balance.Available Days
   Action: Show error message "Insufficient leave balance"

   Advanced logic:
   - Check for pending requests
   - Consider carry-forward rules
   - Validate against leave type policies
   ```

2. **Approval Workflow Rules**

   ```
   Create business rule: "Determine Approval Level"

   Conditions:
   - If Total Days <= 3: Level 1 approval
   - If Total Days > 3 AND <= 10: Level 2 approval
   - If Total Days > 10: Level 3 + HR approval
   - If Emergency leave: HR approval required

   Actions:
   - Set approval level
   - Set next approver
   - Send notification
   ```

3. **Date Validation Rules**

   ```
   Create business rule: "Validate Dates"

   Conditions:
   - Start date cannot be in the past
   - End date must be >= Start date
   - Cannot overlap with existing approved requests
   - Must respect advance notice requirements
   ```

### Step 3: Security Model Implementation

**Practical Exercise:** Comprehensive Security

1. **Create Security Roles**

   **Employee Role:**

   ```
   Table Permissions:
   - Employee: Read (Own records only)
   - Leave Request: Create, Read, Update (Own records)
   - Leave Balance: Read (Own records)
   - Leave Type: Read (Organization)
   - Holiday Calendar: Read (Organization)

   Additional Privileges:
   - Submit leave requests
   - Cancel pending requests
   - View own leave history
   ```

   **Manager Role (inherits Employee):**

   ```
   Additional Table Permissions:
   - Employee: Read (Direct reports + Own)
   - Leave Request: Read, Update (Direct reports + Own)
   - Leave Calendar: Read (Team view)

   Additional Privileges:
   - Approve/reject team requests
   - View team calendars
   - Generate team reports
   ```

   **HR Administrator Role:**

   ```
   Table Permissions:
   - All tables: Full access (Organization level)

   Additional Privileges:
   - Manage leave policies
   - Adjust leave balances
   - Override approvals
   - Access all reports
   - Manage system configuration
   ```

2. **Field-Level Security**
   ```
   Secure sensitive fields:
   - Employee salary information
   - Personal emergency contacts
   - Approval comments (approvers only)
   - Audit trail information
   ```

### Step 4: Views and Forms Configuration

**Practical Exercise:** User Experience Optimization

1. **Create Specialized Views**

   **Employee Leave Requests View:**

   ```
   Columns:
   - Request ID, Leave Type, Start Date, End Date
   - Total Days, Status, Submitted Date

   Filters:
   - Current user's requests only
   - Sort by Submitted Date (newest first)

   Conditional formatting:
   - Green for Approved
   - Red for Rejected
   - Yellow for Pending
   ```

   **Manager Approval Queue:**

   ```
   Columns:
   - Employee Name, Leave Type, Start Date
   - End Date, Total Days, Submitted Date

   Filters:
   - Status = Submitted
   - Next Approver = Current User
   - Sort by priority (longest duration first)
   ```

2. **Enhanced Forms**

   **Leave Request Form (Employee):**

   ```
   Sections:
   - Leave Details (Type, Dates, Reason)
   - Supporting Information (Documents, Coverage)
   - Emergency Contact Information

   Business Process Flow:
   1. Select Leave Type
   2. Choose Dates
   3. Provide Details
   4. Submit for Approval
   ```

   **Approval Form (Manager):**

   ```
   Sections:
   - Request Summary (Read-only)
   - Approval Decision
   - Comments and Feedback
   - Team Impact Analysis

   Quick Actions:
   - Approve, Reject, Request More Info
   ```

---

## Module 52: Canvas Apps Development (6 hours)

### Step 1: Employee Leave Request App (Mobile-Optimized)

**Practical Exercise:** Build Mobile App

1. **App Structure Design**

   ```
   Screens:
   1. Home/Dashboard
   2. Submit Request
   3. My Requests
   4. Leave Balance
   5. Team Calendar
   6. Profile/Settings

   Navigation: Tab control at bottom
   Theme: Corporate branding with accessibility
   ```

2. **Home Screen Development**

   ```powerapps
   // Welcome Message
   "Welcome, " & User().FullName

   // Quick Stats Gallery
   Items: [
       {Title: "Available Days",
        Value: Sum(Filter(LeaveBalances, Employee.Email = User().Email), AvailableDays),
        Icon: "CheckBadge"},
       {Title: "Pending Requests",
        Value: CountRows(Filter(LeaveRequests, Employee.Email = User().Email && Status.Value = "Submitted")),
        Icon: "Clock"},
       {Title: "Used This Year",
        Value: Sum(Filter(LeaveBalances, Employee.Email = User().Email), UsedDays),
        Icon: "Calendar"}
   ]

   // Recent Activities
   Items: Filter(LeaveRequests, Employee.Email = User().Email) | Sort by Submitted Date descending | FirstN(5)
   ```

3. **Submit Request Screen**

   ```powerapps
   // Form Controls

   Leave Type Dropdown:
   Items: Filter(LeaveTypes, ActiveStatus = true)
   DisplayFields: ["Name"]

   Date Picker Validation:
   StartDate.Selected >= Today()

   Total Days Calculation:
   If(EndDatePicker.SelectedDate >= StartDatePicker.SelectedDate,
      DateDiff(StartDatePicker.SelectedDate, EndDatePicker.SelectedDate, Days) + 1,
      0)

   Balance Check:
   LookUp(LeaveBalances,
          Employee.Email = User().Email &&
          LeaveType.Name = LeaveTypeDropdown.Selected.Name).AvailableDays >= TotalDaysLabel.Text

   // Submit Button Logic
   OnSelect:
   If(BalanceCheckLabel.Text = "✓ Sufficient Balance",
      Patch(LeaveRequests, Defaults(LeaveRequests), {
         Employee: LookUp(Employees, Email = User().Email),
         LeaveType: LeaveTypeDropdown.Selected,
         StartDate: StartDatePicker.SelectedDate,
         EndDate: EndDatePicker.SelectedDate,
         TotalDays: Value(TotalDaysLabel.Text),
         Status: {Value: "Submitted"},
         Reason: ReasonTextBox.Text,
         SubmittedDate: Now()
      });
      Navigate(MyRequestsScreen, ScreenTransition.Fade);
      Notify("Leave request submitted successfully!", NotificationType.Success),
      Notify("Please resolve balance issues before submitting.", NotificationType.Error)
   )
   ```

4. **My Requests Screen**

   ```powerapps
   // Requests Gallery
   Items: Filter(LeaveRequests, Employee.Email = User().Email)
   Sort: [SubmittedDate, Descending]

   // Status Icons
   Switch(ThisItem.Status.Value,
      "Approved", Icon.CheckBadge,
      "Rejected", Icon.Cancel,
      "Submitted", Icon.Clock,
      "Cancelled", Icon.Blocked,
      Icon.Help
   )

   // Status Colors
   Switch(ThisItem.Status.Value,
      "Approved", Color.Green,
      "Rejected", Color.Red,
      "Submitted", Color.Orange,
      "Cancelled", Color.Gray,
      Color.Black
   )

   // Cancel Button Logic (for pending requests only)
   Visible: ThisItem.Status.Value = "Submitted"
   OnSelect:
   Patch(LeaveRequests, ThisItem, {Status: {Value: "Cancelled"}});
   Notify("Request cancelled successfully.", NotificationType.Success)
   ```

### Step 2: Manager Dashboard App

**Practical Exercise:** Build Manager Interface

1. **Dashboard Overview**

   ```powerapps
   // Team Statistics
   TeamStats: [
       {Label: "Team Size",
        Value: CountRows(Filter(Employees, Manager.Email = User().Email))},
       {Label: "Pending Approvals",
        Value: CountRows(Filter(LeaveRequests, NextApprover.Email = User().Email && Status.Value = "Submitted"))},
       {Label: "Team On Leave Today",
        Value: CountRows(Filter(LeaveCalendar, Date = Today() && Employee.Manager.Email = User().Email))}
   ]

   // Approval Queue Gallery
   Items: Filter(LeaveRequests,
                 NextApprover.Email = User().Email &&
                 Status.Value = "Submitted")
   Sort: [TotalDays, Descending] // Prioritize longer requests
   ```

2. **Team Calendar View**

   ```powerapps
   // Calendar Gallery (Monthly View)
   Items: Filter(LeaveCalendar,
                 Employee.Manager.Email = User().Email &&
                 Date >= DateAdd(Today(), -Day(Today())+1, Days) &&
                 Date < DateAdd(DateAdd(Today(), -Day(Today())+1, Days), 1, Months))

   // Calendar Cell Content
   Text: If(CountRows(Filter(LeaveCalendar,
                             Date = ThisItem.Date &&
                             Employee.Manager.Email = User().Email)) > 0,
            Concatenate(
                CountRows(Filter(LeaveCalendar, Date = ThisItem.Date)),
                " on leave"
            ),
            "")

   // Team Member Details on Date Select
   Items: Filter(LeaveCalendar,
                 Date = CalendarGallery.Selected.Date &&
                 Employee.Manager.Email = User().Email)
   ```

3. **Approval Process**

   ```powerapps
   // Approval Form
   EditForm.Item: Gallery.Selected

   // Approve Button
   OnSelect:
   Patch(LeaveRequests, Gallery.Selected, {
      Status: {Value: "Approved"},
      ApprovedBy: LookUp(Employees, Email = User().Email),
      ApprovedDate: Now(),
      ApprovalComments: ApprovalCommentsText.Text
   });
   // Trigger approval notification flow
   MyApprovalFlow.Run(Gallery.Selected.RequestID);
   Notify("Request approved successfully!", NotificationType.Success)

   // Reject Button
   OnSelect:
   If(!IsBlank(ApprovalCommentsText.Text),
      Patch(LeaveRequests, Gallery.Selected, {
         Status: {Value: "Rejected"},
         ApprovedBy: LookUp(Employees, Email = User().Email),
         ApprovedDate: Now(),
         ApprovalComments: ApprovalCommentsText.Text
      });
      MyRejectionFlow.Run(Gallery.Selected.RequestID);
      Notify("Request rejected.", NotificationType.Warning),
      Notify("Please provide comments for rejection.", NotificationType.Error)
   )
   ```

### Step 3: HR Analytics App

**Practical Exercise:** Build Analytics Dashboard

1. **Executive Dashboard**

   ```powerapps
   // Key Metrics
   OrganizationStats: [
       {Metric: "Total Employees",
        Value: CountRows(Filter(Employees, ActiveStatus = true)),
        Icon: "People"},
       {Metric: "Pending Requests",
        Value: CountRows(Filter(LeaveRequests, Status.Value = "Submitted")),
        Icon: "Clock"},
       {Metric: "Avg Processing Time",
        Value: Average(Filter(LeaveRequests,
                             Status.Value in ["Approved", "Rejected"]),
                       DateDiff(SubmittedDate, ApprovedDate, Days)),
        Icon: "Timer"},
       {Metric: "Leave Utilization %",
        Value: Round(Sum(LeaveBalances, UsedDays) / Sum(LeaveBalances, AllocatedDays) * 100, 1),
        Icon: "Analytics"}
   ]

   // Department Breakdown Chart
   Items: GroupBy(Filter(Employees, ActiveStatus = true), "Department", "EmployeeCount")
   ChartType: Doughnut
   ```

2. **Leave Trends Analysis**

   ```powerapps
   // Monthly Leave Trends
   MonthlyData: GroupBy(
       AddColumns(
           Filter(LeaveRequests,
                  Status.Value = "Approved" &&
                  StartDate >= DateAdd(Today(), -12, Months)),
           "Month",
           Text(StartDate, "mmm yyyy")
       ),
       "Month",
       "RequestCount"
   )

   // Leave Type Distribution
   LeaveTypeData: GroupBy(
       Filter(LeaveRequests,
              Status.Value = "Approved" &&
              StartDate >= DateAdd(Today(), -1, Years)),
       "LeaveType",
       "TypeCount"
   )
   ```

3. **Compliance and Reporting**

   ```powerapps
   // Overdue Approvals
   OverdueApprovals: Filter(LeaveRequests,
       Status.Value = "Submitted" &&
       DateDiff(SubmittedDate, Today(), Days) > 5
   )

   // Policy Violations
   PolicyViolations: Filter(LeaveRequests,
       TotalDays > LookUp(LeaveTypes, Name = ThisRecord.LeaveType.Name, MaximumDaysPerYear) ||
       DateDiff(Today(), StartDate, Days) < LookUp(LeaveTypes, Name = ThisRecord.LeaveType.Name, AdvanceNoticeDays)
   )

   // Export to Excel Function
   OnSelect:
   Export(ReportData, "Leave_Report_" & Text(Today(), "yyyy-mm-dd"))
   ```

---

## Module 53: Model-Driven Apps Development (4 hours)

### Step 1: HR Administration App

**Practical Exercise:** Build Administrative Interface

1. **Site Map Configuration**

   ```xml
   <SiteMap>
     <Area Id="LeaveManagement" Title="Leave Management">
       <Group Id="Requests" Title="Leave Requests">
         <SubArea Id="AllRequests" Entity="leave_request" />
         <SubArea Id="PendingApprovals" Entity="leave_request" ViewId="pending_approvals_view" />
         <SubArea Id="OverdueRequests" Entity="leave_request" ViewId="overdue_requests_view" />
       </Group>
       <Group Id="Administration" Title="Administration">
         <SubArea Id="Employees" Entity="employee" />
         <SubArea Id="LeaveTypes" Entity="leave_type" />
         <SubArea Id="LeaveBalances" Entity="leave_balance" />
         <SubArea Id="Workflows" Entity="approval_workflow" />
       </Group>
       <Group Id="Reports" Title="Reports & Analytics">
         <SubArea Id="Dashboard" Type="Dashboard" />
         <SubArea Id="Reports" Type="Report" />
       </Group>
     </Area>
   </SiteMap>
   ```

2. **Enhanced Views**

   **All Leave Requests (HR View):**

   ```xml
   Columns:
   - Employee Name (with click-to-employee)
   - Leave Type (with color coding)
   - Start Date, End Date, Total Days
   - Status (with conditional formatting)
   - Submitted Date, Approved By
   - Current Approver

   Filters:
   - Quick filter by status
   - Date range filters
   - Department filter
   - Leave type filter
   ```

   **Pending Approvals (System View):**

   ```xml
   Columns:
   - Priority (calculated based on duration + urgency)
   - Employee Name, Manager
   - Leave Type, Dates, Days
   - Days Pending, Next Approver

   Sort: Priority descending, then Submitted Date

   Conditional Formatting:
   - Red: > 5 days pending
   - Yellow: 3-5 days pending
   - Green: < 3 days pending
   ```

3. **Advanced Forms**

   **Employee Form (HR):**

   ```xml
   Header: Employee Name, Job Title, Department

   Tabs:
   1. General Information
      - Personal details
      - Employment information
      - Contact details

   2. Leave Configuration
      - Leave balances (sub-grid)
      - Approval workflow assignment
      - Special permissions

   3. Leave History
      - All requests (sub-grid)
      - Balance adjustments
      - Audit trail

   4. Related Information
      - Direct reports (if manager)
      - Delegations
      - Calendar view
   ```

### Step 2: Dashboard Development

**Practical Exercise:** Executive Dashboards

1. **HR Executive Dashboard**

   ```xml
   Components:

   1. Key Performance Indicators:
      - Total Active Employees
      - Pending Approval Count
      - Average Processing Time
      - Leave Utilization Rate

   2. Visual Charts:
      - Leave Requests by Month (Line Chart)
      - Leave Types Distribution (Pie Chart)
      - Department Utilization (Bar Chart)
      - Approval Time Trends (Area Chart)

   3. Data Tables:
      - Overdue Approvals
      - Top Leave Requesters
      - Policy Violations
      - System Errors
   ```

2. **Manager Dashboard**

   ```xml
   Components:

   1. Team Metrics:
      - Team Size
      - Team Leave Balance
      - Requests Needing Approval
      - Team Availability

   2. Team Calendar:
      - Visual calendar showing team leave
      - Overlap identification
      - Coverage gaps

   3. Quick Actions:
      - Bulk approve button
      - Team report generation
      - Delegation setup
   ```

### Step 3: Business Process Flows

**Practical Exercise:** Workflow Implementation

1. **Leave Request Process Flow**

   ```xml
   Stages:

   1. Request Submission
      - Select leave type
      - Choose dates
      - Enter reason
      - Attach documents
      - Validate: Check balance, policy compliance

   2. Manager Review
      - Review request details
      - Check team calendar
      - Assess business impact
      - Decision: Approve/Reject/Request Info
      - Validate: Comments required for rejection

   3. HR Review (if required)
      - Policy compliance check
      - Balance verification
      - Final approval
      - Validate: All documentation complete

   4. Completion
      - Update leave calendar
      - Adjust leave balance
      - Send confirmations
      - Archive request
   ```

2. **Employee Onboarding Process**

   ```xml
   Stages:

   1. Employee Setup
      - Create employee record
      - Set manager relationship
      - Configure leave balances
      - Assign security roles

   2. Policy Assignment
      - Assign leave policies
      - Set approval workflows
      - Configure notifications
      - Setup calendar access

   3. System Access
      - Grant app permissions
      - Setup mobile access
      - Provide training materials
      - Complete onboarding
   ```

---

## Module 54: Power Automate Integration (4 hours)

### Step 1: Core Approval Workflows

**Practical Exercise:** Advanced Approval Process

1. **Multi-Level Approval Flow**

   ```yaml
   Trigger: When Leave Request is Created/Modified

   Condition 1: Status = "Submitted"

   Actions:
   1. Get Employee Details
      - Lookup employee record
      - Get manager information
      - Retrieve approval workflow

   2. Determine Approval Path
      Switch (Leave Request Total Days):
        Case <= 3:
          - Set Next Approver = Direct Manager
          - Set Approval Level = 1
        Case 4-10:
          - Set Next Approver = Direct Manager
          - Set Approval Level = 1 (will escalate to 2)
        Case > 10:
          - Set Next Approver = Direct Manager
          - Set Approval Level = 1 (will escalate to 3)

   3. Send Approval Request
      - Create approval task
      - Send email to approver
      - Send Teams notification
      - Update request status

   4. Handle Approval Response
      If Approved:
        - Check if additional approval needed
        - If Yes: Route to next level
        - If No: Complete approval process
      If Rejected:
        - Update request status
        - Send rejection notification
        - Log approval history
   ```

2. **Escalation and Timeout Handling**

   ```yaml
   Parallel Branch 1: Normal Approval Process

   Parallel Branch 2: Timeout Monitoring
   - Delay for X days (configurable)
   - Check if still pending
   - Send reminder to approver
   - CC manager and HR
   - Escalate to next level if no response

   Parallel Branch 3: Emergency Override
   - Monitor for HR emergency approval
   - Bypass normal process if needed
   - Log emergency approval reason
   - Notify all stakeholders
   ```

### Step 2: Notification System

**Practical Exercise:** Comprehensive Notifications

1. **Employee Notifications**

   ```yaml
   Email Template: Leave Request Submitted
   Subject: "Leave Request Submitted - {{ LeaveRequest.RequestID }}"

   Body:
   Dear {{ Employee.FullName }},

   Your leave request has been submitted successfully.

   Details:
   - Request ID: {{ LeaveRequest.RequestID }}
   - Leave Type: {{ LeaveType.Name }}
   - Dates: {{ StartDate }} to {{ EndDate }}
   - Total Days: {{ TotalDays }}
   - Current Status: {{ Status }}

   Next Steps:
   - Your request is pending approval from {{ NextApprover.FullName }}
   - You will receive updates as your request progresses
   - You can track status in the Leave Management app

   Best regards,
   HR Team
   ```

2. **Manager Notifications**
   ```yaml
   Teams Adaptive Card: Approval Request
   {
     "type": "AdaptiveCard",
     "version": "1.3",
     "body": [
       {
         "type": "TextBlock",
         "text": "Leave Request Approval Needed",
         "size": "Large",
         "weight": "Bolder"
       },
       {
         "type": "FactSet",
         "facts": [
           {"title": "Employee:", "value": "{{ Employee.FullName }}"},
           {"title": "Leave Type:", "value": "{{ LeaveType.Name }}"},
           {"title": "Dates:", "value": "{{ StartDate }} - {{ EndDate }}"},
           {"title": "Total Days:", "value": "{{ TotalDays }}"},
           {"title": "Reason:", "value": "{{ Reason }}"}
         ]
       }
     ],
     "actions": [
       {
         "type": "Action.OpenUrl",
         "title": "Approve",
         "url": "{{ ApprovalLink }}"
       },
       {
         "type": "Action.OpenUrl",
         "title": "View Details",
         "url": "{{ RequestDetailsLink }}"
       }
     ]
   }
   ```

### Step 3: Integration Flows

**Practical Exercise:** External System Integration

1. **SharePoint Integration**

   ```yaml
   Flow: Sync Leave Calendar to SharePoint

   Trigger: When Leave Request Status Changes

   Condition: Status = "Approved"

   Actions:
   1. Create SharePoint Calendar Event
      - Site: HR SharePoint Site
      - List: Leave Calendar
      - Title: {{ Employee.Name }} - {{ LeaveType.Name }}
      - Start Time: {{ StartDate }}
      - End Time: {{ EndDate }}
      - Category: {{ LeaveType.Name }}
      - Description: Leave request details

   2. Update Employee Status
      - Set Out of Office in SharePoint profile
      - Update team calendar
      - Notify team members
   ```

2. **Outlook Integration**

   ```yaml
   Flow: Create Outlook Calendar Blocks

   Trigger: Leave Request Approved

   Actions:
   1. Create Calendar Event (Employee)
      - Calendar: Employee's calendar
      - Title: "Out of Office - {{ LeaveType.Name }}"
      - All Day: True/False based on half-day
      - Show As: Out of Office
      - Sensitivity: Private

   2. Set Automatic Replies
      - Start Date: {{ StartDate }}
      - End Date: {{ EndDate }}
      - Internal Message: Custom OOO message
      - External Message: Standard OOO message

   3. Block Calendar (Manager)
      - Create placeholder events
      - Mark as "Team Member Out"
      - Include coverage information
   ```

### Step 4: Automated Maintenance

**Practical Exercise:** System Maintenance Flows

1. **Daily Balance Updates**

   ```yaml
   Flow: Update Leave Balances
   Schedule: Daily at 12:01 AM

   Actions:
   1. Get All Active Employees

   2. For Each Employee:
      - Calculate year-to-date usage
      - Update available balances
      - Check for negative balances
      - Flag policy violations

   3. Generate Daily Report
      - Balance summary by department
      - Policy violations
      - System errors
      - Send to HR team
   ```

2. **Cleanup and Archival**

   ```yaml
   Flow: Archive Old Records
   Schedule: Weekly on Sunday

   Actions:
   1. Archive Completed Requests
      - Older than 2 years
      - Status = Approved/Rejected
      - Move to archive table

   2. Cleanup Audit Logs
      - Older than 7 years
      - Summarize for compliance
      - Remove detailed logs

   3. Update System Performance
      - Rebuild indexes
      - Update statistics
      - Generate performance report
   ```

---

## Module 55: Power Pages Development (2 hours)

### Step 1: External Employee Portal

**Practical Exercise:** Self-Service Portal

1. **Portal Site Setup**

   ```yaml
   Site Configuration:
     - Template: Employee Self Service
     - Authentication: Azure AD B2B
     - Theme: Corporate branding
     - Languages: English (Primary), Spanish, French
   ```

2. **Home Page Design**

   ```html
   <!-- Hero Section -->
   <div class="hero-section">
     <h1>Employee Self-Service Portal</h1>
     <p>Manage your leave requests anytime, anywhere</p>
     {% if user %}
     <a href="/leave-request/" class="btn-primary">Submit Leave Request</a>
     {% else %}
     <a href="/signin/" class="btn-primary">Sign In</a>
     {% endif %}
   </div>

   <!-- Quick Stats -->
   {% if user %}
   <div class="stats-grid">
     <div class="stat-card">
       <h3>{{ available_days }}</h3>
       <p>Available Leave Days</p>
     </div>
     <div class="stat-card">
       <h3>{{ pending_requests }}</h3>
       <p>Pending Requests</p>
     </div>
     <div class="stat-card">
       <h3>{{ used_days }}</h3>
       <p>Used This Year</p>
     </div>
   </div>
   {% endif %}
   ```

3. **Leave Request Form**
   ```html
   <!-- Leave Request Web Form -->
   <form id="leave-request-form">
     <div class="form-section">
       <h3>Leave Details</h3>

       <div class="form-group">
         <label for="leave-type">Leave Type *</label>
         <select id="leave-type" name="leave_type" required>
           {% for type in leave_types %}
           <option value="{{ type.id }}">{{ type.name }}</option>
           {% endfor %}
         </select>
       </div>

       <div class="form-row">
         <div class="form-group">
           <label for="start-date">Start Date *</label>
           <input
             type="date"
             id="start-date"
             name="start_date"
             required
             min="{{ today }}"
           />
         </div>
         <div class="form-group">
           <label for="end-date">End Date *</label>
           <input
             type="date"
             id="end-date"
             name="end_date"
             required
             min="{{ today }}"
           />
         </div>
       </div>

       <div class="form-group">
         <input type="checkbox" id="half-day" name="half_day" />
         <label for="half-day">Half Day</label>
       </div>

       <div class="form-group">
         <label for="reason">Reason</label>
         <textarea id="reason" name="reason" rows="3"></textarea>
       </div>
     </div>

     <div class="form-section">
       <h3>Additional Information</h3>

       <div class="form-group">
         <label for="emergency-contact">Emergency Contact During Leave</label>
         <input type="text" id="emergency-contact" name="emergency_contact" />
       </div>

       <div class="form-group">
         <label for="coverage">Work Coverage Arrangements</label>
         <textarea
           id="coverage"
           name="coverage_arrangements"
           rows="3"
         ></textarea>
       </div>

       <div class="form-group">
         <label for="documents">Supporting Documents</label>
         <input
           type="file"
           id="documents"
           name="supporting_documents"
           multiple
         />
       </div>
     </div>

     <div class="form-actions">
       <button type="submit" class="btn-primary">Submit Request</button>
       <button type="button" class="btn-secondary" onclick="history.back()">
         Cancel
       </button>
     </div>
   </form>
   ```

### Step 2: Status Tracking and History

**Practical Exercise:** Interactive Status Portal

1. **My Requests Page**

   ```html
   <!-- Requests List -->
   <div class="requests-container">
     <h2>My Leave Requests</h2>

     <!-- Filter Controls -->
     <div class="filters">
       <select id="status-filter">
         <option value="">All Statuses</option>
         <option value="Submitted">Pending</option>
         <option value="Approved">Approved</option>
         <option value="Rejected">Rejected</option>
       </select>

       <input type="date" id="date-from" placeholder="From Date" />
       <input type="date" id="date-to" placeholder="To Date" />

       <button onclick="filterRequests()">Filter</button>
     </div>

     <!-- Requests Table -->
     <div class="requests-grid">
       {% for request in user_requests %}
       <div class="request-card status-{{ request.status | lower }}">
         <div class="request-header">
           <span class="request-id">#{{ request.request_id }}</span>
           <span class="status-badge {{ request.status | lower }}"
             >{{ request.status }}</span
           >
         </div>

         <div class="request-details">
           <h4>{{ request.leave_type.name }}</h4>
           <p>
             <strong>Dates:</strong> {{ request.start_date | date: "M/d/yyyy" }}
             - {{ request.end_date | date: "M/d/yyyy" }}
           </p>
           <p><strong>Days:</strong> {{ request.total_days }}</p>
           <p>
             <strong>Submitted:</strong> {{ request.submitted_date | date:
             "M/d/yyyy" }}
           </p>

           {% if request.status == "Submitted" %}
           <p>
             <strong>Next Approver:</strong> {{ request.next_approver.full_name
             }}
           </p>
           {% elsif request.status == "Approved" %}
           <p>
             <strong>Approved by:</strong> {{ request.approved_by.full_name }}
           </p>
           <p>
             <strong>Approved on:</strong> {{ request.approved_date | date:
             "M/d/yyyy" }}
           </p>
           {% endif %}
         </div>

         <div class="request-actions">
           <a href="/request-details/{{ request.id }}/" class="btn-link"
             >View Details</a
           >
           {% if request.status == "Submitted" %}
           <button
             onclick="cancelRequest('{{ request.id }}')"
             class="btn-danger"
           >
             Cancel
           </button>
           {% endif %}
         </div>
       </div>
       {% endfor %}
     </div>
   </div>
   ```

2. **Leave Balance Dashboard**
   ```html
   <!-- Balance Overview -->
   <div class="balance-dashboard">
     <h2>Leave Balance Summary</h2>

     <div class="balance-grid">
       {% for balance in user_balances %}
       <div class="balance-card">
         <div
           class="balance-header"
           style="border-left-color: {{ balance.leave_type.color_code }}"
         >
           <h3>{{ balance.leave_type.name }}</h3>
         </div>

         <div class="balance-stats">
           <div class="stat">
             <span class="label">Allocated</span>
             <span class="value">{{ balance.allocated_days }}</span>
           </div>
           <div class="stat">
             <span class="label">Used</span>
             <span class="value">{{ balance.used_days }}</span>
           </div>
           <div class="stat">
             <span class="label">Pending</span>
             <span class="value">{{ balance.pending_days }}</span>
           </div>
           <div class="stat available">
             <span class="label">Available</span>
             <span class="value">{{ balance.available_days }}</span>
           </div>
         </div>

         <div class="balance-progress">
           <div class="progress-bar">
             <div
               class="progress-used"
               style="width: {{ balance.used_percentage }}%"
             ></div>
             <div
               class="progress-pending"
               style="width: {{ balance.pending_percentage }}%"
             ></div>
           </div>
           <small>{{ balance.utilization_percentage }}% utilized</small>
         </div>
       </div>
       {% endfor %}
     </div>
   </div>
   ```

---

## Module 56: Testing and Deployment (2 hours)

### Step 1: Comprehensive Testing Strategy

**Practical Exercise:** Test Plan Development

1. **Unit Testing**

   ```yaml
   Canvas Apps Testing:

   Test Cases:
   1. Form Validation
      - Required field validation
      - Date range validation
      - Balance checking
      - File upload limits

   2. Data Operations
      - Create leave request
      - Update request status
      - Delete draft requests
      - Filter and search

   3. Business Logic
      - Balance calculations
      - Approval routing
      - Date calculations
      - Policy enforcement

   Test Execution:
   - Use Power Apps Test Studio
   - Record test scenarios
   - Automated regression testing
   - Performance benchmarking
   ```

2. **Integration Testing**

   ```yaml
   Flow Testing:

   Test Scenarios:
   1. Approval Workflow
      - Single level approval
      - Multi-level approval
      - Rejection handling
      - Timeout scenarios

   2. Notification System
      - Email delivery
      - Teams notifications
      - Mobile push notifications
      - Escalation alerts

   3. External Integrations
      - SharePoint calendar sync
      - Outlook calendar blocks
      - Teams presence updates
      - Document management

   Test Tools:
   - Flow test runs
   - Manual trigger testing
   - Error scenario simulation
   - Performance monitoring
   ```

### Step 2: User Acceptance Testing

**Practical Exercise:** UAT Process

1. **Test User Groups**

   ```yaml
   Employee Test Group:
     - 10 employees from different departments
     - Mix of technical and non-technical users
     - Include mobile-only users

   Manager Test Group:
     - 5 managers with varying team sizes
     - Include both technical and business managers
     - Test delegation scenarios

   HR Test Group:
     - 3 HR administrators
     - Include policy configuration testing
     - Test reporting and analytics
   ```

2. **Test Scenarios**

   ```yaml
   Employee Scenarios:
   1. New User Onboarding
      - First-time app access
      - Profile setup
      - Initial leave request

   2. Routine Operations
      - Submit various leave types
      - Check balance and status
      - Cancel pending requests

   3. Edge Cases
      - Insufficient balance
      - Overlapping requests
      - Emergency leave
      - Half-day requests

   Manager Scenarios:
   1. Approval Process
      - Review and approve requests
      - Bulk approval operations
      - Rejection with comments

   2. Team Management
      - View team calendar
      - Identify coverage gaps
      - Generate team reports

   3. Delegation
      - Setup approval delegation
      - Emergency approvals
      - Workflow overrides
   ```

### Step 3: Performance Testing

**Practical Exercise:** Load and Performance Testing

1. **Performance Metrics**

   ```yaml
   Target Metrics:
     - Page load time: < 3 seconds
     - Form submission: < 2 seconds
     - Search results: < 1 second
     - Report generation: < 10 seconds
     - Concurrent users: 500+
     - Peak load handling: 1000+ users
   ```

2. **Load Testing**

   ```yaml
   Test Scenarios:

   1. Normal Load
      - 100 concurrent users
      - Mixed operations (70% read, 30% write)
      - 1-hour duration

   2. Peak Load
      - 500 concurrent users
      - Heavy submission period (month-end)
      - 2-hour duration

   3. Stress Test
      - 1000+ concurrent users
      - System breaking point
      - Recovery testing

   Tools:
   - Application Insights
   - Power Platform Analytics
   - Load testing tools
   - Performance counters
   ```

### Step 4: Production Deployment

**Practical Exercise:** Go-Live Process

1. **Deployment Checklist**

   ```yaml
   Pre-Deployment:
   ✅ All testing completed
   ✅ User training delivered
   ✅ Documentation updated
   ✅ Backup procedures verified
   ✅ Rollback plan prepared
   ✅ Support team briefed
   ✅ Performance monitoring setup

   Deployment Steps:
   1. Deploy solutions to production
   2. Configure environment variables
   3. Update connection references
   4. Import reference data
   5. Setup security roles
   6. Configure integrations
   7. Enable monitoring
   8. Smoke test critical paths
   ```

2. **Go-Live Support**

   ```yaml
   Support Strategy:

   Week 1: Intensive Support
   - Dedicated support team
   - Real-time issue resolution
   - User assistance and training
   - Performance monitoring

   Week 2-4: Active Monitoring
   - Daily health checks
   - User feedback collection
   - Performance optimization
   - Issue tracking and resolution

   Month 2+: Standard Support
   - Regular maintenance windows
   - Monthly performance reviews
   - Quarterly user feedback
   - Continuous improvement
   ```

---

## Final Project Deliverables

### Technical Deliverables

1. **Complete Solution Package**

   - Core solution with all components
   - Apps solution with applications
   - Configuration solution with settings
   - Documentation and deployment guides

2. **Applications**

   - Employee Leave Request App (Canvas)
   - Manager Dashboard App (Canvas)
   - HR Analytics App (Canvas)
   - HR Administration App (Model-driven)
   - External Employee Portal (Power Pages)

3. **Automation**

   - Multi-level approval workflows
   - Notification system
   - Integration flows
   - Maintenance automation

4. **ALM Implementation**
   - Environment strategy
   - CI/CD pipeline
   - Source control integration
   - Monitoring and alerting

### Documentation Deliverables

1. **User Documentation**

   - Employee user guide
   - Manager user guide
   - HR administrator guide
   - Mobile app guide
   - Portal user guide

2. **Technical Documentation**

   - Architecture document
   - Solution design document
   - Integration specifications
   - Security and compliance guide
   - Deployment procedures

3. **Administrative Documentation**
   - System administration guide
   - Troubleshooting guide
   - Performance monitoring guide
   - Backup and recovery procedures
   - Change management process

### Training Materials

1. **End User Training**

   - Video tutorials
   - Quick reference cards
   - Interactive training modules
   - FAQ documents

2. **Administrator Training**
   - System configuration guide
   - Policy management procedures
   - Reporting and analytics training
   - Troubleshooting procedures

---

## Knowledge Check and Certification

### Final Assessment

**Practical Project Evaluation (100 points):**

1. **Technical Implementation (40 points)**

   - Solution architecture and design
   - Code quality and best practices
   - Integration completeness
   - Performance and scalability

2. **User Experience (25 points)**

   - Interface design and usability
   - Mobile responsiveness
   - Accessibility compliance
   - User workflow efficiency

3. **Business Value (20 points)**

   - Requirements compliance
   - Process improvement
   - Automation effectiveness
   - ROI demonstration

4. **ALM and Quality (15 points)**
   - Solution management
   - Testing coverage
   - Documentation quality
   - Deployment readiness

### Certification Requirements

To receive the **Power Platform Professional Certificate**, you must:

✅ Complete all 6 stages of training (168.5 hours)
✅ Score 80% or higher on the final project
✅ Demonstrate proficiency in all components
✅ Successfully deploy to production environment
✅ Present solution to stakeholders

---

## Summary and Next Steps

### What You've Accomplished

🎉 **Congratulations!** You have completed the most comprehensive Power Platform training program, building a complete enterprise-grade Leave Management System.

**Total Training Hours: 168.5 hours**

**Skills Mastered:**

- Canvas Apps development (advanced)
- Model-driven Apps creation
- Power Automate workflow design
- Dataverse data modeling
- Power Pages portal development
- Solution management and ALM
- Integration and automation
- Security and compliance
- Performance optimization
- Project management

### Real-World Impact

Your Leave Management System provides:

- **Employee Self-Service**: Reduced HR workload by 60%
- **Automated Approvals**: 90% faster processing time
- **Mobile Access**: 24/7 availability for all users
- **Compliance**: Complete audit trail and reporting
- **Integration**: Seamless Office 365 connectivity
- **Scalability**: Supports enterprise-level usage

### Career Opportunities

With these skills, you're qualified for roles such as:

- Power Platform Developer
- Business Applications Analyst
- Digital Transformation Consultant
- Process Automation Specialist
- Power Platform Architect
- Low-Code/No-Code Developer

### Continued Learning

**Next Steps:**

- Microsoft Power Platform certifications
- Advanced Azure integration
- AI Builder and cognitive services
- Power Platform governance
- Enterprise architecture patterns

---

**🌟 You are now a Power Platform Professional! 🌟**

_Continue building amazing solutions that transform businesses and empower users worldwide._
