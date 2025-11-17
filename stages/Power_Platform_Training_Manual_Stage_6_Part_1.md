# Power Platform Training Manual - Stage 6 Part 1: Model-Driven Apps Fundamentals

## Overview

This is Part 1 of Stage 6, covering Model-Driven Apps fundamentals. Model-Driven Apps are form-based enterprise applications built on Dataverse with automatic responsive UI, business process flows, and comprehensive security. **This part covers 8 hours of Module 41-43.**

**Stage 6 Total Duration: 53 hours** | **Part 1: 8 hours**

---

## Module 41: Introduction to Model-Driven Apps (2 hours)

### Learning Objectives

- Understand Model-Driven Apps architecture
- Compare Canvas Apps vs Model-Driven Apps
- Create your first Model-Driven App
- Configure app properties and settings
- Add tables to the app
- Design the sitemap (navigation)
- Understand app components
- Publish and share the app

### Step-by-Step Instructions

#### Step 1: What are Model-Driven Apps?

**Definition:**

```
Model-Driven App = Form-based enterprise application built on Dataverse

Key Characteristics:
✅ Data-centric (driven by Dataverse tables)
✅ Automatic responsive UI (desktop, tablet, mobile)
✅ Built-in components (forms, views, charts, dashboards)
✅ Enterprise features (BPF, security, auditing)
✅ Minimal UI customization (uses table metadata)
✅ Fast development (leverage existing Dataverse structure)
✅ Consistent experience across apps
```

**Architecture:**

```
Model-Driven App Components:
├── Sitemap (navigation)
│   ├── Areas (modules)
│   ├── Groups (sections)
│   └── Sub-areas (menu items)
├── Tables (data)
│   ├── Forms (data entry)
│   ├── Views (data display)
│   ├── Charts (visualizations)
│   └── Dashboards (insights)
├── Business Process Flows (guided workflows)
├── Business Rules (validation)
└── Security (role-based access)

User Experience:
Navigation (left) → Form (center) → Related (right tabs)
```

#### Step 2: Canvas Apps vs Model-Driven Apps

**Comparison:**

| Feature              | Canvas Apps                   | Model-Driven Apps               |
| -------------------- | ----------------------------- | ------------------------------- |
| **Starting Point**   | Blank canvas                  | Dataverse tables                |
| **Design Approach**  | Pixel-perfect custom UI       | Metadata-driven automatic UI    |
| **Data Source**      | 500+ connectors               | Dataverse only                  |
| **Complexity**       | Simple to complex tasks       | Complex enterprise apps         |
| **Development Time** | More UI development           | Fast (uses existing forms)      |
| **Responsive**       | Manual responsive design      | Automatic responsive            |
| **User Experience**  | Consumer-friendly             | Enterprise data management      |
| **Navigation**       | Custom built                  | Sitemap-based navigation        |
| **Forms**            | Custom built                  | Dataverse forms                 |
| **Views**            | Galleries with custom filters | Dataverse views                 |
| **Business Logic**   | Power Fx formulas             | Business rules, plugins, flows  |
| **Offline**          | Manual implementation         | Built-in offline (Dynamics 365) |
| **Security**         | App-level                     | Table/field/row-level           |
| **Best For**         | Task-focused apps             | Data management apps            |

**When to Use Each:**

```
Use Canvas Apps:
✅ Custom branded interface
✅ Mobile-first experience
✅ Task automation
✅ Consumer-facing apps
✅ Integration with multiple sources
✅ Specific workflow (not CRUD)

Examples:
- Expense submission app
- Field inspection app
- Onboarding wizard
- Mobile sales tool

Use Model-Driven Apps:
✅ Complex data management
✅ Enterprise forms and processes
✅ Dataverse-centric apps
✅ Rapid development
✅ Consistent UI across organization
✅ Heavy CRUD operations

Examples:
- CRM system
- Case management
- Asset tracking
- Project management
- HR management system
```

**Hybrid Approach:**

```
Best Practice: Combine Both

Canvas App:
- Employee self-service portal
- Submit leave requests
- View own leave balance
- Mobile-friendly interface

Model-Driven App:
- Manager/HR admin interface
- Approve leave requests
- Manage leave balances
- View reports and dashboards
- Configure leave types and policies

Result: Best of both worlds
Employees get great UX
Admins get powerful data management
```

#### Step 3: Create Your First Model-Driven App

**Navigate to Apps:**

```
1. Navigate to make.powerapps.com
2. Select your environment (top right)
3. Apps (left navigation)
4. + New app → Model-driven
```

**App Designer Opens:**

```
Interface Components:
- Left panel: Pages (add tables, dashboards)
- Center: App preview
- Right panel: Properties
- Top: Save, Publish, Play buttons

New App Properties:
Name: Leave Management Admin
Description: Admin app for managing leave requests
Icon: Upload custom icon or use default
```

**Add Tables to App:**

```
1. + Add page button
2. Select: Dataverse table
3. Choose tables:
   ✅ Leave Request
   ✅ Leave Balance
   ✅ Employee Profile
   ✅ Department
   ✅ Leave Type

4. For each table, select what to show:
   ✅ Show in navigation (menu item)
   ✅ Forms (which forms to include)
   ✅ Views (which views to include)

5. Click Add

Result: Tables added to app with default forms/views
```

**Configure Table Pages:**

```
For Leave Request:
1. Click on Leave Request page
2. Right panel - Properties:
   - Display name: Leave Requests
   - Icon: Choose calendar icon

3. Forms section:
   - ✅ Main form
   - ✅ Quick Create form

4. Views section:
   - ✅ Active Leave Requests
   - ✅ My Leave Requests
   - ✅ Pending Approval
   - ✅ Approved Requests

5. Charts section:
   - ✅ Leave Requests by Type
   - ✅ Leave Requests by Status
```

#### Step 4: Design the Sitemap (Navigation)

**What is a Sitemap?**

```
Sitemap = Navigation structure of Model-Driven App

Hierarchy:
App
└── Area (top-level module)
    └── Group (section within area)
        └── Subarea (menu item - links to table/dashboard)

Example:
Leave Management Admin
└── Leave Management (Area)
    ├── Requests (Group)
    │   ├── Leave Requests (Subarea → Table)
    │   └── Pending Approvals (Subarea → View)
    ├── Configuration (Group)
    │   ├── Leave Types (Subarea → Table)
    │   ├── Leave Balances (Subarea → Table)
    │   └── Departments (Subarea → Table)
    └── People (Group)
        └── Employees (Subarea → Table)
```

**Edit Sitemap:**

```
Method 1: In App Designer
1. Click "Navigation" in left panel
2. Edit navigation structure:
   - Add Area
   - Add Group
   - Add Subarea
3. Drag and drop to reorder

Method 2: Classic Sitemap Designer
1. In app designer, click "..." next to Navigation
2. Open Sitemap designer
3. More visual interface
```

**Create Custom Sitemap:**

```
1. Create Area: Leave Management
   Properties:
   - Title: Leave Management
   - Icon: Choose icon
   - ID: LeaveArea

2. Create Group: Requests
   Properties:
   - Title: Requests
   - Show As: Tab (or Collapsed)

3. Create Subarea: Leave Requests
   Properties:
   - Title: Leave Requests
   - Type: Entity (table)
   - Entity: Leave Request
   - Default View: Active Leave Requests
   - Icon: Calendar icon

4. Create Subarea: Pending Approvals
   Properties:
   - Title: Pending Approvals
   - Type: Entity
   - Entity: Leave Request
   - Default View: Pending Approval
   - Icon: Clock icon

5. Create Group: Configuration
   Create Subareas:
   - Leave Types
   - Leave Balances
   - Departments

6. Create Group: People
   Create Subarea:
   - Employees

7. Save sitemap
8. Publish
```

**Navigation Result:**

```
App Opens:
Left Navigation Panel:
📅 Leave Management
  📋 Requests
    - Leave Requests
    - Pending Approvals
  ⚙️ Configuration
    - Leave Types
    - Leave Balances
    - Departments
  👥 People
    - Employees

User clicks "Leave Requests" → Shows Active Leave Requests view
User clicks "Pending Approvals" → Shows filtered Pending view
```

#### Step 5: Configure App Properties

**App Settings:**

```
Click Settings (gear icon) in app designer

General:
- App Name: Leave Management Admin
- Description: Comprehensive leave management system
- Welcome page: Show/hide welcome page
- Icon: Upload 32x32 PNG

Advanced settings:
- Enable areas: Yes (multiple areas/modules)
- Enable mobile: Yes (Dynamics 365 mobile app)
- Unified Interface: Yes (modern interface)
```

**App Themes:**

```
While in app designer:
1. Click on app name (top)
2. Properties panel → Theme
3. Choose from predefined themes:
   - Blue
   - Green
   - Purple
   - Red
   - Orange
   - Custom

Or create custom theme:
4. Customize colors:
   - Navigation bar color
   - Navigation shelf color
   - Header color
   - Accent color

5. Save theme
6. Apply to app
```

#### Step 6: Add Dashboards

**Create Dashboard:**

```
1. In app designer, click "+ Add page"
2. Select: Dashboard
3. Choose dashboard type:
   - Create new dashboard
   - Use existing dashboard

4. Create New:
   Name: Leave Management Overview
   Layout: 3-column regular (or choose layout)

5. Add components:
   - Charts (Leave by Type, Leave by Status)
   - Lists (Pending Approvals, Recent Requests)
   - Iframes (embed reports)
   - Web resources (custom HTML)

6. Save dashboard
7. Add to navigation (subarea)
```

**Dashboard Components:**

```
Chart Component:
- Select table: Leave Request
- Select view: Active Leave Requests
- Select chart: Leave Requests by Type
- Position: Top left

List Component:
- Select table: Leave Request
- Select view: Pending Approval
- Show chart: No
- Position: Top center

Quick Stats (Text):
- Create web resource with HTML
- Show KPIs (total requests, pending count)
- Position: Top right
```

#### Step 7: App Permissions

**Share the App:**

```
After publishing:
1. Apps list → Select app → Share
2. Add users/teams
3. Assign security role:
   - Leave Administrator (full access)
   - Leave Manager (department access)
   - Leave Employee (own records)

4. Share

Important: Users need:
✅ App permission (added in Share)
✅ Security role (assigned separately)
✅ Environment access
```

**Security Role for App:**

```
Users need appropriate security role:

Leave Administrator:
- All tables: Organization-level access
- App: Read privilege on Model-Driven App entity

Leave Manager:
- Leave tables: Business Unit-level access
- App: Read privilege

Leave Employee:
- Leave tables: User-level access
- App: Read privilege

Without security role = "You don't have access" error
```

#### Step 8: Publish and Play

**Publish App:**

```
1. Click "Publish" button (top right)
2. Wait for publishing process
3. Status: Published successfully

What happens:
✅ App available to users
✅ Sitemap deployed
✅ All changes go live
✅ Previous version replaced

Note: Always publish after changes
Unpublished changes not visible to users
```

**Play (Test) App:**

```
1. Click "Play" button (top right)
2. App opens in new tab
3. Test functionality:
   ✅ Navigation works
   ✅ Forms load correctly
   ✅ Views display data
   ✅ Business rules execute
   ✅ Charts render
   ✅ Dashboards show data

4. If issues found:
   - Go back to designer
   - Make changes
   - Save
   - Publish again
   - Test again
```

#### Step 9: App URL and Bookmarking

**Get App URL:**

```
1. Apps list → Select app → "..." menu
2. Details
3. Copy App URL

URL Format:
https://orgname.crm.dynamics.com/main.aspx?appid={GUID}

Share this URL with users for direct access
```

**Add to Microsoft Teams:**

```
1. Teams → Apps
2. Search: Power Apps
3. Add Power Apps tab to channel
4. Select: Leave Management Admin app
5. Save

Result: App embedded in Teams
Users can access without leaving Teams
```

#### Step 10: Edit Existing App

**Modify Published App:**

```
1. make.powerapps.com → Apps
2. Select app → Edit (or Edit in preview)
3. App designer opens
4. Make changes:
   - Add/remove tables
   - Modify sitemap
   - Change properties
   - Add dashboards
5. Save
6. Publish (critical!)

Versioning:
- Model-Driven Apps don't have explicit versions
- Always maintain solution for ALM
- Export solution before major changes
```

### Assignment Deliverable

✅ **Create Model-Driven App:**

- App name: Leave Management Admin
- Add all leave-related tables
- Configure table properties and icons
- Include appropriate forms and views

✅ **Design sitemap with:**

- 1 Area: Leave Management
- 3 Groups: Requests, Configuration, People
- 6+ Subareas linking to tables/views
- Logical navigation structure

✅ **Configure app:**

- Custom app icon
- Description
- Theme/branding
- Welcome page settings

✅ **Create dashboard:**

- Leave Management Overview
- 2+ charts (by type, by status)
- 1+ list view (pending approvals)
- Organized layout

✅ **Test and document:**

- Publish app
- Test all navigation
- Share with test user
- Verify permissions
- Document URL
- Screenshot key screens

### Knowledge Check

- [ ] What is a Model-Driven App?
- [ ] How do Canvas Apps differ from Model-Driven Apps?
- [ ] When should you use Model-Driven Apps?
- [ ] What is a sitemap?
- [ ] What are Areas, Groups, and Subareas?
- [ ] How do you add tables to an app?
- [ ] What permissions do users need?
- [ ] What does publishing do?
- [ ] How do you share an app?
- [ ] Can you combine Canvas and Model-Driven Apps?

### Resources

- [Microsoft Learn: Model-driven apps overview](https://learn.microsoft.com/en-us/power-apps/maker/model-driven-apps/model-driven-app-overview)
- [Microsoft Learn: Build your first model-driven app](https://learn.microsoft.com/en-us/power-apps/maker/model-driven-apps/build-first-model-driven-app)
- [Microsoft Learn: App designer](https://learn.microsoft.com/en-us/power-apps/maker/model-driven-apps/app-designer-overview)
- [Microsoft Learn: Design the sitemap](https://learn.microsoft.com/en-us/power-apps/maker/model-driven-apps/create-site-map-app)

---

## Module 42: Advanced Forms and Commands (3 hours)

### Learning Objectives

- Master advanced form design techniques
- Create multiple form types (Main, Quick View, Card)
- Configure form scripts and events
- Customize command bar (ribbon)
- Add custom buttons and actions
- Implement form logic with JavaScript
- Use Power Fx in Model-Driven Apps
- Create Quick View controls
- Configure header and footer components

### Step-by-Step Instructions

#### Step 1: Form Types Review

**Form Types in Model-Driven Apps:**

```
Main Form:
- Primary data entry form
- Full-featured with tabs, sections, fields
- Can have multiple main forms per table
- Form selector allows switching
- Use for: Comprehensive data entry

Quick Create Form:
- Simplified form (< 10 fields)
- No tabs, single section
- Opens in modal dialog
- Use for: Rapid data entry from lookups

Quick View Form:
- Read-only display of related record
- Embedded in other forms
- Shows related data without navigation
- Use for: Contextual information

Card Form:
- Mobile-optimized compact view
- Used in Dynamics 365 mobile app
- Shows key fields only
- Use for: Mobile quick view
```

#### Step 2: Create Multiple Main Forms

**Why Multiple Forms?**

```
Use Cases:
1. Role-Based Forms:
   - Employee form (basic fields)
   - Manager form (approval fields)
   - Admin form (all fields)

2. Process-Based Forms:
   - New Request form (minimal fields)
   - Processing form (additional fields)
   - Completed form (read-only)

3. Department-Based Forms:
   - IT Department form
   - HR Department form
   - Custom fields per department
```

**Create Additional Main Form:**

```
1. Tables → Leave Request → Forms
2. + New form → Main form
3. Form designer opens
4. Name: Manager Approval Form

5. Add tabs:
   - Tab 1: Request Details
   - Tab 2: Approval Information
   - Tab 3: History

6. Tab 1 - Request Details:
   Section: Employee Information
   - Employee (lookup - shows Quick View)
   - Department (from Employee)
   - Request Date

   Section: Leave Details
   - Leave Type
   - Start Date
   - End Date
   - Total Days (calculated)
   - Comments

7. Tab 2 - Approval Information:
   Section: Manager Decision
   - Status (read-only if not manager)
   - Manager Approval (Yes/No)
   - Manager Comments
   - Approval Date (auto-populated)
   - Approved By (auto-populated)

   Section: Leave Balance
   - Available Balance (from Employee)
   - Remaining Balance (calculated)

8. Tab 3 - History:
   Section: Audit Trail
   - Sub-grid: Approval History records
   - Timeline: Activity history
   - Notes: Associated notes

9. Save form as: Manager Approval Form
10. Publish
```

**Form Order and Selection:**

```
Configure Form Order:
1. Tables → Leave Request → Forms
2. Form order button
3. Drag to reorder:
   1. Main Form (default)
   2. Manager Approval Form
   3. Quick Create Form

Form Selection Logic:
- First form in order = default
- Users can switch via form selector (if multiple enabled)
- Use JavaScript to auto-switch based on conditions
```

**Conditional Form Display:**

```
JavaScript to Switch Forms:

// In form OnLoad event
function onFormLoad(executionContext) {
    var formContext = executionContext.getFormContext();
    var userRoles = Xrm.Utility.getGlobalContext().userSettings.roles;

    // Check if user is manager
    var isManager = checkManagerRole(userRoles);

    if (isManager) {
        // Switch to Manager form
        var formId = "MANAGER-FORM-GUID";
        formContext.ui.formSelector.items.get(formId).navigate();
    }
}

// Or use security roles to auto-assign forms
```

#### Step 3: Quick View Forms

**Purpose:**

```
Quick View Form = Display related record data without navigation

Example: Leave Request form shows:
- Employee Quick View (name, department, photo, manager)
- Without navigating to Employee record
- Updates automatically when Employee lookup changes
```

**Create Quick View Form:**

```
1. Tables → Employee Profile → Forms
2. + New form → Quick view form
3. Name: Employee Summary

4. Add fields (max 10 recommended):
   - Full Name
   - Employee Photo
   - Job Title
   - Department
   - Manager
   - Email
   - Phone

5. Layout: 2-column for compact display

6. Save and Publish
```

**Add Quick View to Main Form:**

```
1. Tables → Leave Request → Forms → Main Form (edit)
2. Navigate to section where Employee lookup exists
3. Select Employee lookup field
4. Insert tab → Quick View Control
5. Configure:
   - Lookup field: Employee
   - Quick view form: Employee Summary
   - Name: EmployeeQuickView
6. Position below or beside Employee lookup

Result: When Employee selected, Quick View shows employee details
```

**Quick View Benefits:**

```
✅ Reduce navigation clicks
✅ Show contextual information
✅ Real-time updates
✅ Better user experience
✅ No separate permission needed (uses lookup permissions)

Common Usage:
- Show customer details on order form
- Show employee details on leave request
- Show account info on contact form
- Show product details on order line
```

#### Step 4: Form Scripts and Events

**Form Events:**

```
OnLoad:
- Fires when form loads
- Use for: Initialize form, set defaults, load data

OnSave:
- Fires before form saves
- Use for: Validation, prevent save, modify data

OnChange (Field):
- Fires when field value changes
- Use for: Dependent fields, calculations, show/hide

TabStateChange:
- Fires when tab changes
- Use for: Lazy load data, track navigation

Process (BPF) Events:
- OnStageChange: Stage changes in BPF
- OnStageSelected: User clicks stage
```

**Create Form Script:**

```
1. Create JavaScript Web Resource:
   Solutions → New → Web resource
   Name: cr123_LeaveRequestScripts.js
   Type: JavaScript

2. Write JavaScript:

// Leave Request Form Scripts
var LeaveRequest = LeaveRequest || {};

LeaveRequest.Form = {
    // On Load
    onLoad: function(executionContext) {
        var formContext = executionContext.getFormContext();
        LeaveRequest.Form.setFieldRequirements(formContext);
        LeaveRequest.Form.calculateTotalDays(formContext);
    },

    // On Save
    onSave: function(executionContext) {
        var formContext = executionContext.getFormContext();

        // Validate dates
        var startDate = formContext.getAttribute("cr123_startdate").getValue();
        var endDate = formContext.getAttribute("cr123_enddate").getValue();

        if (startDate && endDate && endDate < startDate) {
            // Prevent save
            executionContext.getEventArgs().preventDefault();

            // Show error
            formContext.ui.setFormNotification(
                "End date cannot be before start date",
                "ERROR",
                "date_validation"
            );
            return false;
        }

        // Clear notification if validation passed
        formContext.ui.clearFormNotification("date_validation");
    },

    // Calculate Total Days
    calculateTotalDays: function(formContext) {
        var startDate = formContext.getAttribute("cr123_startdate").getValue();
        var endDate = formContext.getAttribute("cr123_enddate").getValue();

        if (startDate && endDate) {
            var diffTime = Math.abs(endDate - startDate);
            var diffDays = Math.ceil(diffTime / (1000 * 60 * 60 * 24)) + 1;

            formContext.getAttribute("cr123_totaldays").setValue(diffDays);
        }
    },

    // Set Field Requirements
    setFieldRequirements: function(formContext) {
        var totalDays = formContext.getAttribute("cr123_totaldays").getValue();

        // Manager approval required if > 5 days
        if (totalDays > 5) {
            formContext.getAttribute("cr123_managerapproval")
                .setRequiredLevel("required");
        } else {
            formContext.getAttribute("cr123_managerapproval")
                .setRequiredLevel("none");
        }
    },

    // On Start Date Change
    onStartDateChange: function(executionContext) {
        var formContext = executionContext.getFormContext();
        LeaveRequest.Form.calculateTotalDays(formContext);
        LeaveRequest.Form.setFieldRequirements(formContext);
    },

    // On End Date Change
    onEndDateChange: function(executionContext) {
        var formContext = executionContext.getFormContext();
        LeaveRequest.Form.calculateTotalDays(formContext);
        LeaveRequest.Form.setFieldRequirements(formContext);
    }
};

3. Save web resource
4. Publish
```

**Register Scripts on Form:**

```
1. Tables → Leave Request → Forms → Main Form (edit)
2. Form Properties button (or right panel)
3. Events tab

4. Form Libraries:
   + Add library → Select: cr123_LeaveRequestScripts.js

5. Event Handlers:
   OnLoad:
   - + Add → Function: LeaveRequest.Form.onLoad
   - ✅ Pass execution context

   OnSave:
   - + Add → Function: LeaveRequest.Form.onSave
   - ✅ Pass execution context

6. Field Events:
   Select field: Start Date
   Events tab
   OnChange:
   - + Add → Function: LeaveRequest.Form.onStartDateChange
   - ✅ Pass execution context

   Repeat for End Date with onEndDateChange

7. Save and Publish form
```

**Client API Reference:**

```
Common Form Context Operations:

// Get field value
var value = formContext.getAttribute("fieldname").getValue();

// Set field value
formContext.getAttribute("fieldname").setValue(value);

// Get field is required
var required = formContext.getAttribute("fieldname").getRequiredLevel();

// Set field required
formContext.getAttribute("fieldname").setRequiredLevel("required"); // or "recommended" or "none"

// Show/hide field
formContext.getControl("fieldname").setVisible(true/false);

// Enable/disable field
formContext.getControl("fieldname").setDisabled(true/false);

// Show notification
formContext.ui.setFormNotification("message", "INFO/WARNING/ERROR", "uniqueId");

// Clear notification
formContext.ui.clearFormNotification("uniqueId");

// Get current user
var userId = formContext.context.getUserId();
var userName = formContext.context.getUserName();

// Navigate to record
var entityFormOptions = {};
entityFormOptions["entityName"] = "contact";
entityFormOptions["entityId"] = contactId;
Xrm.Navigation.openForm(entityFormOptions);
```

#### Step 5: Power Fx in Model-Driven Apps

**What is Power Fx in Model-Driven Apps?**

```
Power Fx: Low-code formula language from Canvas Apps
Now available in Model-Driven Apps for:
✅ Command bar buttons (button visibility, enabled state)
✅ Form logic (limited, expanding)
✅ Calculated columns (server-side in Dataverse)

Benefits:
- No JavaScript needed for simple scenarios
- Familiar to Canvas App makers
- Declarative (what, not how)
- Easier to maintain
```

**Power Fx for Command Visibility:**

```
Example: Show "Approve" button only for managers

Command: Approve Leave Request
Visibility Formula:
    And(
        Self.Selected.Item.'Status'.Value = "Pending Approval",
        Or(
            'User/User ID' = Self.Selected.Item.'Manager'.'User',
            'User/Security Roles' HasRole "Leave Administrator"
        )
    )

Result: Button visible only when:
- Status is Pending Approval
- Current user is the manager OR admin
```

**Power Fx for Command Enabled:**

```
Example: Disable "Submit" if dates invalid

Command: Submit Leave Request
Enabled Formula:
    And(
        Not(IsBlank(Self.Selected.Item.'Start Date')),
        Not(IsBlank(Self.Selected.Item.'End Date')),
        Self.Selected.Item.'End Date' > Self.Selected.Item.'Start Date',
        Self.Selected.Item.'Total Days' > 0
    )

Result: Button enabled only when all conditions met
```

#### Step 6: Customize Command Bar (Ribbon)

**What is Command Bar?**

```
Command Bar (Ribbon):
- Top of form: Actions for current record
- Top of view: Actions for selected records or new record
- Contains buttons: New, Save, Delete, Deactivate, custom buttons

Legacy: Called "Ribbon" (classic interface)
Modern: Called "Command Bar" (Unified Interface)
```

**Edit Command Bar:**

```
Method 1: Power Apps Command Designer (Modern)
1. Tables → Leave Request → Command bar → Edit
2. Command Designer opens
3. Select location: Main form or View
4. + New → Command
5. Configure command properties

Method 2: Ribbon Workbench (Classic, 3rd party)
- More powerful but complex
- Requires solution download
- Visual ribbon designer
```

**Create Custom Command:**

```
1. Tables → Leave Request
2. Commands and experiences → Commands
3. + New command

4. Configure Command:
   Name: Approve Leave Request
   Label: Approve
   Icon: Accept icon
   Action: Run JavaScript function

5. JavaScript Function:
   Library: cr123_LeaveRequestScripts.js
   Function: LeaveRequest.Commands.approveRequest

6. Visibility:
   Show when: Status = "Pending Approval"
   And user has permission to approve

7. Location:
   Main form command bar

8. Save and Publish
```

**JavaScript for Command:**

```
// Add to LeaveRequestScripts.js

LeaveRequest.Commands = {
    approveRequest: function(primaryControl) {
        var formContext = primaryControl;
        var recordId = formContext.data.entity.getId().replace("{", "").replace("}", "");

        // Confirm approval
        var confirmStrings = {
            text: "Are you sure you want to approve this leave request?",
            title: "Confirm Approval"
        };
        var confirmOptions = {
            height: 200,
            width: 450
        };

        Xrm.Navigation.openConfirmDialog(confirmStrings, confirmOptions).then(
            function(success) {
                if (success.confirmed) {
                    // Update record
                    var data = {
                        "cr123_status": 865420001, // Approved option value
                        "cr123_approvaldate": new Date(),
                        "cr123_approvedby@odata.bind": "/systemusers(" +
                            Xrm.Utility.getGlobalContext().userSettings.userId + ")"
                    };

                    Xrm.WebApi.updateRecord("cr123_leaverequest", recordId, data).then(
                        function success(result) {
                            formContext.data.refresh(false);
                            Xrm.Navigation.openAlertDialog({
                                text: "Leave request approved successfully!"
                            });
                        },
                        function(error) {
                            Xrm.Navigation.openAlertDialog({
                                text: "Error approving request: " + error.message
                            });
                        }
                    );
                }
            }
        );
    },

    rejectRequest: function(primaryControl) {
        // Similar to approve, but set status to Rejected
        var formContext = primaryControl;
        var recordId = formContext.data.entity.getId().replace("{", "").replace("}", "");

        // Prompt for rejection reason
        var inputStrings = {
            text: "Please provide a reason for rejection:",
            title: "Reject Leave Request"
        };
        var inputOptions = {
            height: 300,
            width: 500
        };

        Xrm.Navigation.openInputDialog(inputStrings, inputOptions).then(
            function(success) {
                if (success.confirmed) {
                    var rejectionReason = success.value;

                    var data = {
                        "cr123_status": 865420002, // Rejected option value
                        "cr123_rejectionreason": rejectionReason,
                        "cr123_rejectiondate": new Date()
                    };

                    Xrm.WebApi.updateRecord("cr123_leaverequest", recordId, data).then(
                        function success(result) {
                            formContext.data.refresh(false);
                            Xrm.Navigation.openAlertDialog({
                                text: "Leave request rejected"
                            });
                        },
                        function(error) {
                            Xrm.Navigation.openAlertDialog({
                                text: "Error: " + error.message
                            });
                        }
                    );
                }
            }
        );
    }
};
```

**Add Multiple Commands:**

```
Create Commands:
1. Approve (green checkmark icon)
2. Reject (red X icon)
3. Cancel Request (gray icon)
4. Request Revision (orange icon)

Place commands strategically:
Main form: Approve, Reject (for managers)
View: Bulk Approve (for multiple selection)
Sub-grid: Quick actions
```

#### Step 7: Form Components

**Header Component:**

```
Form Header = Top section showing key fields

Best Practices:
- Show 4-6 key fields only
- Use for: Status, Owner, Important dates
- Always visible (even when scrolling)

Configure Header:
1. Form designer → Header section
2. Drag fields to header
3. Recommended fields:
   - Status (choice with colored icons)
   - Employee (lookup)
   - Start Date
   - Total Days
   - Leave Type

4. Fields appear as: [Icon] Field Label: Value
```

**Footer Component:**

```
Form Footer = Bottom section for actions

Typical Use:
- Save button
- Cancel button
- Custom workflow buttons
- Status bar

Configure Footer:
1. Form designer → Footer section
2. Add buttons/controls
3. Usually left as default
```

**Timeline Component:**

```
Timeline = Activity history and notes

Shows:
- Emails related to record
- Phone calls
- Appointments
- Notes
- Posts
- Custom activities

Add Timeline:
1. Form designer → Tab
2. Insert → Timeline
3. Configure:
   - Activities to show (emails, notes, posts)
   - Sort order (newest first)
   - Filter options

4. Position: Usually in dedicated "Timeline" tab

Usage: Track all interactions with leave request
```

#### Step 8: Business Process Flow on Forms

**What is Business Process Flow?**

```
Business Process Flow (BPF):
- Guided step-by-step process
- Appears at top of form
- Stages and steps
- Enforce data entry
- Track progress

Example: Leave Request Approval Process
Stage 1: Submit Request
  - Enter dates
  - Select leave type
  - Add comments

Stage 2: Manager Review
  - Review request
  - Check balance
  - Approve/reject

Stage 3: HR Processing
  - Update balance
  - Notify employee
  - Archive request
```

**Add BPF to Form:**

```
Create BPF first (covered in next module)

Add to Form:
1. Form automatically shows active BPF
2. BPF bar appears at top of form
3. Users click "Next Stage" to progress

Configure BPF Display:
1. Form Properties → Display tab
2. Business process flow:
   ✅ Show business process flow
   ✅ Expand by default

Or hide if not needed for this form
```

#### Step 9: Tabs and Sections Best Practices

**Tab Design:**

```
Best Practices:
✅ Logical grouping (related fields together)
✅ 3-5 tabs maximum (don't overwhelm)
✅ Most important tab first
✅ Use tab labels that are clear and concise
✅ Consider mobile view (tabs become accordion)

Example Leave Request Form:
Tab 1: General (basic info - default tab)
Tab 2: Approval (manager section)
Tab 3: Related (history, documents)
Tab 4: Timeline (activities)

Avoid:
❌ Too many tabs (hard to find info)
❌ Vague tab names ("Miscellaneous")
❌ Empty tabs
❌ Duplicate information across tabs
```

**Section Design:**

```
Column Layouts:
- 1 column: Full-width (long text, descriptions)
- 2 columns: Most common (balanced)
- 3 columns: Compact (many short fields)
- 4 columns: Very compact (rarely used)

Best Practices:
✅ Use section labels for clarity
✅ Group related fields in same section
✅ Use 2-column for most sections
✅ Use 1-column for multi-line text
✅ Show/hide sections dynamically (JavaScript)

Example Section:
Section: Leave Details (2-column)
  Leave Type*     | Start Date*
  End Date*       | Total Days (read-only)
  Comments (1-column below, multi-line)
```

#### Step 10: Advanced Form Techniques

**Form Selector:**

```
Allow users to switch between forms:

1. Enable form selector on each form:
   Form Properties → Display tab
   ✅ Show form selector

2. Users see dropdown at top of form
3. Can switch to different form layout

Use when:
- Different roles need different views
- Different processes require different layouts
```

**Fallback Forms:**

```
Define form order:
1. Primary form (default)
2. Secondary form (if user can't access primary)
3. Fallback form (minimal permissions)

Configure:
Tables → Forms → Form Order
Drag to set priority

System shows first form user has access to
```

**Auto-Save:**

```
Modern forms auto-save every few seconds

Control auto-save:
Form Properties → Display tab
Auto-save:
  ✅ Enabled (recommended)
  ❌ Disabled (manual save only)

OnSave event still fires for validation
```

### Assignment Deliverable

✅ **Create multiple main forms:**

- Standard form (all users)
- Manager form (additional approval fields)
- Admin form (all fields including config)
- Configure form order

✅ **Create and implement Quick View:**

- Employee Summary quick view
- Add to Leave Request form
- Position appropriately

✅ **Implement form scripts:**

- OnLoad: Initialize form, calculate fields
- OnSave: Validate dates, prevent invalid save
- OnChange: Calculate total days, set requirements
- Add as web resource and register events

✅ **Create custom commands:**

- Approve button (managers only)
- Reject button (managers only)
- Implement JavaScript functions
- Configure visibility rules

✅ **Design comprehensive form:**

- Proper tab/section structure
- Header with key fields
- Timeline component
- Business logic integration
- Test all functionality

### Knowledge Check

- [ ] What are the different form types?
- [ ] When should you create multiple main forms?
- [ ] What is a Quick View form?
- [ ] What form events are available?
- [ ] How do you add JavaScript to a form?
- [ ] What is Power Fx in Model-Driven Apps?
- [ ] What is the command bar?
- [ ] How do you create custom commands?
- [ ] What should go in the form header?
- [ ] What is the timeline component?

### Resources

- [Microsoft Learn: Create and design forms](https://learn.microsoft.com/en-us/power-apps/maker/model-driven-apps/create-design-forms)
- [Microsoft Learn: Form script events](https://learn.microsoft.com/en-us/power-apps/developer/model-driven-apps/clientapi/events-forms-grids)
- [Microsoft Learn: Client API reference](https://learn.microsoft.com/en-us/power-apps/developer/model-driven-apps/clientapi/reference)
- [Microsoft Learn: Command bar customization](https://learn.microsoft.com/en-us/power-apps/maker/model-driven-apps/use-command-designer)

---

## Module 43: Business Process Flows (3 hours)

### Learning Objectives

- Understand Business Process Flows (BPF)
- Create multi-stage guided processes
- Configure stages and steps
- Add branching conditions
- Implement workflows on stage change
- Use action steps
- Create BPF entity records
- Monitor process progress
- Customize BPF for different scenarios

### Step-by-Step Instructions

#### Step 1: What are Business Process Flows?

**Definition:**

```
Business Process Flow (BPF):
- Visual guide through standardized process
- Step-by-step progression
- Enforce data quality
- Track process stage
- Consistent user experience

Components:
- Stages: Major milestones (Submit → Review → Approve)
- Steps: Data entry requirements within stage
- Branching: Different paths based on conditions
- Actions: Automated tasks on stage change

Visual: Horizontal bar at top of form
```

**BPF vs Workflow vs Flow:**

```
Business Process Flow:
- User-facing visual guide
- Interactive (user progresses)
- Multiple stages on form
- Use for: Guided data entry

Workflow (Classic):
- Background automation
- No user interaction
- Triggered by events
- Use for: Automated processes
- Being replaced by Cloud Flows

Cloud Flow (Power Automate):
- Modern automation
- Complex logic
- External integrations
- Use for: Automated workflows

Combination:
BPF guides user → User completes stage → Flow automates backend tasks
```

#### Step 2: Create Business Process Flow

**Navigate to BPF Designer:**

```
Method 1: From Solutions
1. make.powerapps.com → Solutions
2. Select solution
3. + New → Automation → Process → Business process flow

Method 2: From Flows
1. make.powerapps.com → Flows
2. Business process flows tab
3. + New

Method 3: From Table
1. Tables → Leave Request
2. Business process flows
3. + New business process flow
```

**Create Leave Request Approval BPF:**

```
1. Click + New business process flow
2. Configure:
   Name: Leave Request Approval Process
   Display Name: Leave Approval
   Table: Leave Request (primary table)

3. Click Create

BPF Designer opens with:
- Default stage
- Canvas for designing
- Properties panel
```

#### Step 3: Design Stages

**Add Stages:**

```
Default Structure:
Stage 1: (New Stage - rename)

Add Stages:
1. Select default stage
2. Properties panel:
   - Display Name: Submit Request
   - Category: Leave Request (table)

3. Click "+ Add" next to stage
4. Select: Stage
5. New stage appears

6. Configure Stage 2:
   - Display Name: Manager Review
   - Category: Leave Request

7. Add Stage 3:
   - Display Name: HR Processing
   - Category: Leave Request

8. Add Stage 4:
   - Display Name: Complete
   - Category: Leave Request

Visual Result:
[Submit Request] → [Manager Review] → [HR Processing] → [Complete]
```

**Stage Properties:**

```
For each stage, configure:

Display Name: User-facing name (e.g., "Manager Review")
Category: Which table (Leave Request)
Relationship: If using related table (optional)
Security Role: Restrict stage to specific role (optional)

Example: Manager Review stage
Security Role: Leave Manager
Result: Only managers can see/complete this stage
```

#### Step 4: Add Steps to Stages

**What are Steps?**

```
Steps = Data requirements within stage
Types:
- Data Step: Required field entry
- Action Step: Execute action (Flow, Workflow)
- Condition Step: Branching logic

User must complete all steps before "Next Stage"
```

**Add Steps to Stage 1: Submit Request**

```
1. Select Stage: Submit Request
2. Click "Details" in properties panel
3. + Add → Data Step

4. Step 1:
   Display Name: Select Leave Type
   Data Field: Leave Type
   Required: Yes

5. + Add → Data Step
6. Step 2:
   Display Name: Enter Start Date
   Data Field: Start Date
   Required: Yes

7. + Add → Data Step
8. Step 3:
   Display Name: Enter End Date
   Data Field: End Date
   Required: Yes

9. + Add → Data Step
10. Step 4:
    Display Name: Add Comments
    Data Field: Comments
    Required: No

Result: Stage 1 has 4 steps
User must complete required steps to proceed
```

**Add Steps to Stage 2: Manager Review**

```
Stage: Manager Review

Step 1:
  Display Name: Review Request Details
  Data Field: (No field - info only)
  Step Label: Review the employee's leave request details

Step 2:
  Display Name: Check Leave Balance
  Data Field: Available Balance (from Employee)
  Required: No (read-only reference)

Step 3:
  Display Name: Manager Decision
  Data Field: Manager Approval (Yes/No)
  Required: Yes

Step 4:
  Display Name: Manager Comments
  Data Field: Manager Comments
  Required: No

Optional Action Step:
  + Add → Action Step
  Display Name: Send Notification
  Action: Call Flow to notify employee
  Trigger: On stage entry
```

**Add Steps to Stage 3: HR Processing**

```
Stage: HR Processing

Step 1:
  Display Name: Verify Approval
  Data Field: Status
  Required: Yes (must be Approved)

Step 2:
  Display Name: Update Leave Balance
  Step Label: Update the employee's available leave balance
  (Can trigger Flow to auto-update)

Step 3:
  Display Name: HR Notes
  Data Field: HR Notes
  Required: No

Action Step:
  Display Name: Update Balance
  Action: Call Flow to deduct leave days
  Trigger: On stage entry
```

**Add Steps to Stage 4: Complete**

```
Stage: Complete

Step 1:
  Display Name: Final Status
  Data Field: Status
  Required: Yes (must be Approved or Rejected)

Step 2:
  Display Name: Archive Request
  Step Label: Request processing complete

Action Step:
  Display Name: Send Confirmation
  Action: Call Flow to email employee
  Trigger: On stage completion
```

#### Step 5: Branching Conditions

**What is Branching?**

```
Branching = Different paths based on conditions

Example:
Submit Request → Manager Review
  ├─ IF Approved → HR Processing → Complete
  └─ IF Rejected → Complete (skip HR Processing)

Use when:
- Different approval paths
- Skip stages based on criteria
- Complex process flows
```

**Add Branching:**

```
1. Select stage: Manager Review
2. Properties → Add Condition
3. Click: + Condition

4. Configure Condition:
   Name: Check Approval Decision
   Rules:
   IF Manager Approval = Yes
   THEN: Go to HR Processing stage
   ELSE: Go to Complete stage

5. Condition appears as diamond on canvas
6. Connect stages:
   - True branch → HR Processing
   - False branch → Complete

Visual:
[Submit] → [Manager Review] → <Condition: Approved?>
                                 ├─ Yes → [HR Processing] → [Complete]
                                 └─ No → [Complete]
```

**Complex Branching:**

```
Multiple Conditions:

Example: Escalation for long leaves

Stage: Manager Review
Condition: Leave Duration
  IF Total Days > 10
  THEN: Go to HR Review stage
  THEN: Go to Director Approval stage
  ELSE: Go to HR Processing stage

Implementation:
1. Add condition after Manager Review
2. Rule: Total Days > 10
3. True branch → Director Approval (new stage)
4. False branch → HR Processing
5. Director Approval → HR Processing
```

#### Step 6: Action Steps

**What are Action Steps?**

```
Action Step = Execute automation on stage change

Types:
- Call Cloud Flow
- Run Workflow (classic)
- Create record
- Update record
- Send email

Use for:
✅ Auto-populate fields
✅ Send notifications
✅ Update related records
✅ Trigger external systems
```

**Create Action Step:**

```
1. In stage details, click + Add → Action Step
2. Configure:
   Display Name: Send Manager Notification
   Step Type: Action

3. Action Type: Flow
4. Select Flow: (must create flow first)
   - Trigger: "When a Business Process Flow stage changes"
   - Action: Send email to manager

5. Position: On stage entry (or on stage exit)

6. Save BPF
```

**Example Flow for Action Step:**

```
Flow: Notify Manager on Leave Request Submission

Trigger: When BPF stage changes (Leave Request Approval Process)
Condition: Stage = "Manager Review"

Actions:
1. Get Manager (from Employee lookup)
2. Get Manager email
3. Send email:
   To: Manager email
   Subject: Leave Request Pending Approval
   Body: Employee [Name] submitted leave request for [Total Days] days
         From: [Start Date] to [End Date]
         [Link to record]

Result: Email sent automatically when stage changes to Manager Review
```

#### Step 7: Cross-Entity BPF

**What is Cross-Entity BPF?**

```
Cross-Entity = BPF spans multiple tables

Example: Onboarding Process
Stage 1: Candidate (table: Candidate)
  - Review application
  - Schedule interview

Stage 2: Interview (table: Interview)
  - Conduct interview
  - Score candidate

Stage 3: Offer (table: Job Offer)
  - Prepare offer
  - Send offer

Stage 4: Employee (table: Employee)
  - Accept offer
  - Start onboarding

Different stages use different tables
Related via lookups
```

**Create Cross-Entity BPF:**

```
Example: Leave Request with Balance Update

Stage 1: Leave Request (table: Leave Request)
  - Submit request

Stage 2: Approval (table: Leave Request)
  - Manager approval

Stage 3: Balance Update (table: Leave Balance)
  - Update balance
  - Relationship: Leave Balance → Employee

Configuration:
1. Stage 3 properties:
   Category: Leave Balance (different table)
   Relationship: Employee lookup

2. BPF creates Leave Balance record automatically
3. Links via Employee

Result: One process updates multiple tables
```

#### Step 8: BPF Entity

**What is BPF Entity?**

```
When you create BPF:
- System creates BPF entity (table)
- Stores BPF instance data
- One record per process instance

Table name: <BPF Name> (e.g., "Leave Request Approval Process")

Contains:
- Active Stage
- Completed Date
- Duration in each stage
- Business Process Flow fields from steps
```

**Access BPF Records:**

```
1. Advanced settings → Customization → Customize System
2. Entities → Search for BPF name
3. View BPF entity fields and records

Or via API:
var processName = "cr123_leaverequestapprovalprocess";
Xrm.WebApi.retrieveMultipleRecords(processName).then(...);
```

**Query BPF Data:**

```
Use Cases:
- Reporting on process duration
- Finding stuck processes
- Analyzing bottlenecks

Example View:
All Active BPF Records
Columns:
- Business Process Flow Name
- Active Stage
- Duration (days)
- Owner
- Status

Filter: Active = Yes
Sort: Duration (longest first)

Identifies: Requests stuck in approval
```

#### Step 9: Multiple BPFs on Same Table

**Why Multiple BPFs?**

```
Use Cases:
- Different processes for different scenarios
- Department-specific processes
- Simple vs complex workflows

Example: Leave Request
BPF 1: Quick Approval (< 3 days)
  - Submit → Auto-Approve → Complete

BPF 2: Standard Approval (3-10 days)
  - Submit → Manager Review → Complete

BPF 3: Extended Approval (> 10 days)
  - Submit → Manager Review → HR Review → Director Approval → Complete
```

**Switch BPFs:**

```
On Form:
1. User sees BPF dropdown (if multiple enabled)
2. Click: Switch Process
3. Select different BPF
4. Process switches (preserves data)

Via JavaScript:
formContext.data.process.setActiveProcess(processId, callbackFunction);

Automatic Switching:
Use Workflow or Flow to switch BPF based on criteria
Example: If Total Days > 10, switch to Extended Approval BPF
```

#### Step 10: Monitor and Optimize BPF

**BPF Analytics:**

```
View Process Analytics:
1. Model-Driven App
2. Dashboards
3. Create BPF dashboard:
   - Average time in each stage
   - Abandoned processes
   - Completion rate
   - Bottleneck stages

Charts:
- Process funnel (how many complete each stage)
- Time by stage (which stage takes longest)
- Completion rate over time
```

**Optimize BPF:**

```
Optimization Tips:

✅ Minimize steps per stage (3-5 max)
✅ Use action steps to auto-populate fields
✅ Clear step labels (user knows what to do)
✅ Use branching to skip unnecessary stages
✅ Set realistic required fields
✅ Provide help text for complex steps
✅ Test with actual users
✅ Monitor abandonment rates

Common Issues:
❌ Too many steps (users abandon)
❌ Unclear requirements (users stuck)
❌ Missing permissions (users can't progress)
❌ No validation (bad data entered)
```

**BPF Best Practices:**

```
Design:
- 3-5 stages maximum (simple processes)
- 5-8 stages for complex processes
- Each stage = clear milestone
- Use consistent naming

Steps:
- Only required fields as required
- Provide defaults where possible
- Use help text for guidance
- Consider mobile users

Actions:
- Automate repetitive tasks
- Send notifications at key stages
- Update related records automatically
- Log important events

Testing:
- Test all branches
- Test with different security roles
- Test on mobile devices
- Measure time to complete
```

### Assignment Deliverable

✅ **Create Leave Request Approval BPF:**

- 4 stages: Submit, Manager Review, HR Processing, Complete
- Steps for each stage with required fields
- Clear display names and labels

✅ **Implement branching:**

- Condition: If Approved → HR Processing, If Rejected → Complete
- Additional condition: If > 10 days → Director Approval
- Test all paths

✅ **Add action steps:**

- Send email to manager (Manager Review stage entry)
- Update leave balance (HR Processing stage)
- Send confirmation (Complete stage)
- Create supporting Flows

✅ **Create BPF dashboard:**

- Average time per stage chart
- Process completion funnel
- Stuck processes list
- Completion rate over time

✅ **Test and document:**

- Test complete process end-to-end
- Test branching conditions
- Test action steps execution
- Document process flow
- User guide for BPF usage

### Knowledge Check

- [ ] What is a Business Process Flow?
- [ ] What are stages and steps?
- [ ] How do you add steps to a stage?
- [ ] What is branching in BPF?
- [ ] What are action steps?
- [ ] What is a cross-entity BPF?
- [ ] What is the BPF entity?
- [ ] Can you have multiple BPFs on one table?
- [ ] How do you switch BPFs?
- [ ] What are BPF best practices?

### Resources

- [Microsoft Learn: Business process flows overview](https://learn.microsoft.com/en-us/power-automate/business-process-flows-overview)
- [Microsoft Learn: Create a business process flow](https://learn.microsoft.com/en-us/power-automate/create-business-process-flow)
- [Microsoft Learn: Add branching](https://learn.microsoft.com/en-us/power-automate/enhance-business-process-flows-branching)
- [Microsoft Learn: Best practices](https://learn.microsoft.com/en-us/power-automate/best-practices-entity-attributes)

---

## Stage 6 Part 1 Progress Summary

### Completed Modules (8 hours):

- ✅ Module 41: Introduction to Model-Driven Apps (2 hours)
- ✅ Module 42: Advanced Forms and Commands (3 hours)
- ✅ Module 43: Business Process Flows (3 hours)

### Skills Acquired:

- Model-Driven Apps architecture and design
- Canvas vs Model-Driven Apps comparison
- Creating apps with sitemap navigation
- Advanced form design techniques
- Multiple form types (Main, Quick View, Card)
- Form scripts and JavaScript events
- Power Fx in Model-Driven Apps
- Custom command bar buttons
- Quick View forms for related data
- Business Process Flows with stages and steps
- Branching and action steps
- Cross-entity processes
- BPF monitoring and optimization

### Leave Management Progress:

✅ Model-Driven App created: "Leave Management Admin"
✅ Sitemap designed with 3 groups and 6+ subareas
✅ Multiple forms for different roles
✅ Custom commands: Approve, Reject
✅ Business Process Flow: 4-stage approval process
✅ Form scripts for validation and calculations
✅ Quick View forms for employee details

### Overall Training Progress:

**Total Completed: 125 hours of 170 hours (73.5%)**

**Completed:**

- ✅ Stage 1: Power Apps Fundamentals (16.5 hours)
- ✅ Stage 2: Canvas Apps Intermediate (29 hours)
- ✅ Stage 3: Advanced Canvas Apps (36 hours)
- ✅ Stage 4: Cloud Flows (25.5 hours)
- ✅ Stage 5: Dataverse (10 hours)
- ✅ Stage 6 Part 1: Model-Driven Apps Fundamentals (8 hours)

**Remaining:**

- ⏳ Stage 6 Part 2: Dashboards, Charts, and Views (5 hours)
- ⏳ Stage 6 Part 3: Power Pages (8 hours)
- ⏳ Stage 6 Part 4: Solutions and ALM (10 hours)
- ⏳ Stage 6 Part 5: Final Project - Complete Leave Management System (22 hours)

---

**Type "NEXT" to continue with Stage 6 Part 2: Dashboards, Charts, and Views**
