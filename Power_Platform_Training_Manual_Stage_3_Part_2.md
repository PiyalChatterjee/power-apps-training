# Power Platform Training Manual - Stage 3 Part 2: Exception Handling, Deep Linking, and Sharing

## Overview

This section covers exception handling with IfError(), deep linking for navigation with parameters, sharing strategies for internal and external users, offline capabilities, and monitoring tools. **Duration: 6 hours**

---

## Module 16: Exception Handling (2 hours)

### Learning Objectives

- Understand error handling in Power Apps
- Implement IfError() function effectively
- Create graceful error messages
- Log errors for debugging
- Build resilient applications
- Handle different error types

### Step-by-Step Instructions

#### Step 1: Understanding Error Handling

**Why Error Handling is Critical:**

- Network connectivity issues
- Data source timeouts
- Invalid user input
- Permission errors
- API failures
- Graceful degradation

**Without Error Handling:**

```powerFx
// User clicks Save button
Patch(
    Employees,
    Defaults(Employees),
    {
        FirstName: txtFirstName.Text,
        LastName: txtLastName.Text,
        Email: txtEmail.Text
    }
)
// If fails: User sees cryptic error or nothing happens
// No recovery, no logging, poor UX
```

**With Error Handling:**

```powerFx
IfError(
    Patch(
        Employees,
        Defaults(Employees),
        {
            FirstName: txtFirstName.Text,
            LastName: txtLastName.Text,
            Email: txtEmail.Text
        }
    ),

    // If error occurs
    Notify(
        "Failed to save employee. Please try again. Error: " & FirstError.Message,
        NotificationType.Error
    );

    // Log error
    Collect(
        colErrorLog,
        {
            Timestamp: Now(),
            Screen: "Employee Form",
            Action: "Save Employee",
            Error: FirstError.Message,
            User: User().Email
        }
    )
)
```

#### Step 2: IfError() Function Basics

**Syntax:**

```powerFx
IfError(
    TryExpression,
    FallbackExpression,
    FallbackExpression2
)
```

**Simple Example:**

```powerFx
IfError(
    1 / 0,  // This will cause error (division by zero)
    "Error occurred!"  // This will execute
)
// Result: "Error occurred!"
```

**Accessing Error Information:**

```powerFx
IfError(
    // Try to load data
    ClearCollect(colEmployees, Employees),

    // On error, show details
    Notify(
        "Error Details:" & Char(10) &
        "Message: " & FirstError.Message & Char(10) &
        "Kind: " & FirstError.Kind & Char(10) &
        "Source: " & FirstError.Source,
        NotificationType.Error
    )
)
```

**Error Properties:**

- `FirstError.Message` - Error message text
- `FirstError.Kind` - Error category (e.g., "Network", "Validation")
- `FirstError.Source` - Where error occurred
- `AllErrors` - Collection of all errors (if multiple)

#### Step 3: Create Error Handling Test App

**Setup Test Environment:**

```powerFx
// Screen: scrErrorHandlingTests

// Test 1: Division by Zero
btnTestDivisionError.OnSelect:
    Set(
        varResult,
        IfError(
            1 / 0,
            "✅ Error caught! Division by zero prevented.",
            "Unexpected error"
        )
    );
    Notify(varResult, NotificationType.Information)

// Test 2: Invalid Collection Reference
btnTestCollectionError.OnSelect:
    IfError(
        Set(varValue, First(colNonExistent).Name),
        Notify(
            "✅ Error caught! Collection doesn't exist." & Char(10) &
            "Error: " & FirstError.Message,
            NotificationType.Warning
        )
    )

// Test 3: Data Source Connection Error
btnTestDataSourceError.OnSelect:
    IfError(
        ClearCollect(colTest, InvalidDataSource),

        Notify(
            "❌ Data source connection failed" & Char(10) &
            "Message: " & FirstError.Message & Char(10) &
            "Kind: " & FirstError.Kind,
            NotificationType.Error
        );

        // Fallback to cached data
        Set(varUsingCachedData, true)
    )
```

#### Step 4: Form Submission Error Handling

**Comprehensive Form Save with Error Handling:**

```powerFx
// Button: btnSaveEmployee

// Validate inputs first
If(
    IsBlank(txtFirstName.Text),
    Notify("First Name is required", NotificationType.Error),
    IsBlank(txtLastName.Text),
    Notify("Last Name is required", NotificationType.Error),
    IsBlank(txtEmail.Text),
    Notify("Email is required", NotificationType.Error),
    Not(IsMatch(txtEmail.Text, Email)),
    Notify("Invalid email format", NotificationType.Error),

    // All validations passed, try to save
    IfError(
        // Try to patch data
        Patch(
            Employees,
            If(
                IsBlank(varSelectedEmployee),
                Defaults(Employees),
                varSelectedEmployee
            ),
            {
                FirstName: txtFirstName.Text,
                LastName: txtLastName.Text,
                Email: txtEmail.Text,
                Department: ddnDepartment.Selected.Value,
                PhoneNumber: txtPhone.Text,
                HireDate: dtpHireDate.SelectedDate,
                Salary: Value(txtSalary.Text),
                Status: "Active",
                ModifiedBy: User().Email,
                ModifiedDate: Now()
            }
        );

        // Success path
        Notify(
            If(
                IsBlank(varSelectedEmployee),
                "Employee created successfully! ✅",
                "Employee updated successfully! ✅"
            ),
            NotificationType.Success
        );

        // Log action
        Collect(
            colAuditLog,
            {
                Action: If(IsBlank(varSelectedEmployee), "Create", "Update"),
                Entity: "Employee",
                RecordID: Last(Employees).ID,
                User: User().Email,
                Timestamp: Now()
            }
        );

        // Clear form and navigate
        Reset(frmEmployee);
        Navigate(scrEmployeeList, ScreenTransition.UnCoverRight),

        // Error path
        Notify(
            "❌ Failed to save employee" & Char(10) & Char(10) &
            "Error: " & FirstError.Message & Char(10) & Char(10) &
            "Please check your connection and try again.",
            NotificationType.Error
        );

        // Log error for troubleshooting
        Collect(
            colErrorLog,
            {
                Timestamp: Now(),
                Screen: "Employee Form",
                Action: "Save Employee",
                User: User().Email,
                ErrorMessage: FirstError.Message,
                ErrorKind: FirstError.Kind,
                ErrorSource: FirstError.Source,
                FormData: {
                    FirstName: txtFirstName.Text,
                    LastName: txtLastName.Text,
                    Email: txtEmail.Text
                }
            }
        );

        // Keep form open for retry
        Set(varShowRetryOption, true)
    )
)
```

#### Step 5: Data Loading Error Handling

**App.OnStart with Error Handling:**

```powerFx
// ===================================
// ROBUST APP STARTUP
// ===================================

Set(varIsLoading, true);
Set(varLoadingMessage, "Loading application...");
Set(varLoadErrors, false);

// Try to load all required data
IfError(
    // Load reference data concurrently
    Concurrent(
        ClearCollect(colDepartments, Departments),
        ClearCollect(colJobTitles, JobTitles),
        ClearCollect(colLocations, Locations),
        ClearCollect(colStatuses, ['Leave Status'])
    );

    // Load user context
    ClearCollect(
        colCurrentUser,
        Filter(Employees, Email = User().Email)
    );
    Set(varCurrentUser, First(colCurrentUser));

    // Success - proceed normally
    Set(varIsLoading, false);
    Set(varLoadingMessage, "");
    Navigate(scrHome, ScreenTransition.None),

    // Error occurred during startup
    Set(varIsLoading, false);
    Set(varLoadErrors, true);
    Set(
        varStartupError,
        "Failed to load application data" & Char(10) & Char(10) &
        "Error: " & FirstError.Message & Char(10) & Char(10) &
        "Please check your internet connection and restart the app."
    );

    // Log startup error
    Collect(
        colErrorLog,
        {
            Timestamp: Now(),
            Event: "App Startup",
            User: User().Email,
            Error: FirstError.Message,
            ErrorKind: FirstError.Kind
        }
    );

    // Navigate to error screen
    Navigate(scrError, ScreenTransition.None)
)
```

**Error Screen (scrError):**

```powerFx
// Display error message
lblErrorMessage.Text:
    varStartupError

// Retry button
btnRetry.OnSelect:
    // Reload the app
    Launch("https://apps.powerapps.com/play/" & App.ActiveAppId)

// Offline mode button (if applicable)
btnOfflineMode.OnSelect:
    // Use cached data
    Set(varOfflineMode, true);
    Navigate(scrHome, ScreenTransition.None)
```

#### Step 6: API Call Error Handling

**Calling External API with Error Handling:**

```powerFx
// Button: btnFetchExternalData

Set(varIsLoadingAPI, true);

IfError(
    // Try API call
    Set(
        varAPIResponse,
        'External API'.GetEmployeeData(varEmployeeID)
    );

    // Parse response
    If(
        varAPIResponse.status = 200,

        // Success
        Set(varEmployeeData, ParseJSON(varAPIResponse.body));
        Notify("Data loaded successfully ✅", NotificationType.Success),

        // API returned error status
        Notify(
            "API Error: " & varAPIResponse.status & Char(10) &
            varAPIResponse.message,
            NotificationType.Error
        )
    );

    Set(varIsLoadingAPI, false),

    // Network or connection error
    Notify(
        "❌ Connection Failed" & Char(10) & Char(10) &
        "Could not reach external service" & Char(10) &
        "Error: " & FirstError.Message & Char(10) & Char(10) &
        "Using cached data instead.",
        NotificationType.Warning
    );

    // Fallback to cached data
    Set(varEmployeeData, LookUp(colCachedEmployeeData, ID = varEmployeeID));
    Set(varUsingCachedData, true);
    Set(varIsLoadingAPI, false)
)
```

#### Step 7: Nested Error Handling

**Multi-Level Error Handling:**

```powerFx
// Try primary data source, fallback to secondary, then to cache

IfError(
    // Try primary SharePoint list
    ClearCollect(colEmployees, SharePointEmployees),

    // Primary failed, try backup Dataverse table
    IfError(
        ClearCollect(colEmployees, DataverseEmployees),

        // Both failed, use local cache
        If(
            CountRows(colCachedEmployees) > 0,

            // Cache available
            Set(colEmployees, colCachedEmployees);
            Notify(
                "⚠️ Using cached data (offline mode)" & Char(10) &
                "Last updated: " & Text(varLastCacheUpdate, "[$-en-US]mmm dd, yyyy hh:mm AM/PM"),
                NotificationType.Warning
            ),

            // No cache available
            Notify(
                "❌ Unable to load data" & Char(10) & Char(10) &
                "No internet connection and no cached data available." & Char(10) &
                "Please connect to internet and try again.",
                NotificationType.Error
            );
            Set(varDataUnavailable, true)
        )
    )
)
```

#### Step 8: Error Logging System

**Create Error Log Collection:**

```powerFx
// App.OnStart - Initialize error log
If(
    IsEmpty(colErrorLog),
    ClearCollect(
        colErrorLog,
        {
            Timestamp: Now(),
            Screen: "",
            Action: "",
            User: "",
            ErrorMessage: "",
            ErrorKind: "",
            ErrorSource: "",
            Additional: ""
        }
    );
    Clear(colErrorLog)
)
```

**Reusable Error Logging Function:**

```powerFx
// Create component: cmpErrorHandler
// Input properties:
//   - pScreen (Text)
//   - pAction (Text)
//   - pError (Record)
//   - pAdditional (Text)

// Component behavior
Collect(
    colErrorLog,
    {
        Timestamp: Now(),
        Screen: Self.pScreen,
        Action: Self.pAction,
        User: User().Email,
        ErrorMessage: Self.pError.Message,
        ErrorKind: Self.pError.Kind,
        ErrorSource: Self.pError.Source,
        Additional: Self.pAdditional
    }
);

// Also send to monitoring system if available
IfError(
    Patch(
        ErrorLogsTable,
        Defaults(ErrorLogsTable),
        {
            Timestamp: Now(),
            Screen: Self.pScreen,
            Action: Self.pAction,
            User: User().Email,
            ErrorMessage: Self.pError.Message,
            ErrorKind: Self.pError.Kind,
            Environment: "Production",
            AppVersion: App.Version
        }
    ),
    // Silent fail - don't show error for error logging
)
```

**Usage:**

```powerFx
IfError(
    // Some operation
    Patch(Employees, ...),

    // Log error using component
    cmpErrorHandler.LogError(
        "Employee Form",
        "Save Employee",
        FirstError,
        "Employee ID: " & varEmployeeID
    );

    Notify("Operation failed", NotificationType.Error)
)
```

#### Step 9: User-Friendly Error Messages

**Map Technical Errors to User-Friendly Messages:**

```powerFx
// Function to get friendly error message
Set(
    varFriendlyMessage,
    Switch(
        FirstError.Kind,

        "Network",
        "🌐 Network Connection Issue" & Char(10) &
        "Please check your internet connection and try again.",

        "Timeout",
        "⏱️ Request Timed Out" & Char(10) &
        "The operation is taking longer than expected. Please try again.",

        "Validation",
        "✏️ Validation Error" & Char(10) &
        FirstError.Message,

        "Permission",
        "🔒 Permission Denied" & Char(10) &
        "You don't have permission to perform this action. Please contact your administrator.",

        "NotFound",
        "🔍 Not Found" & Char(10) &
        "The requested resource could not be found.",

        "Conflict",
        "⚠️ Data Conflict" & Char(10) &
        "This record was modified by another user. Please refresh and try again.",

        // Default
        "❌ An Error Occurred" & Char(10) &
        "Something went wrong. Please try again or contact support." & Char(10) & Char(10) &
        "Error: " & FirstError.Message
    )
);

Notify(varFriendlyMessage, NotificationType.Error)
```

#### Step 10: Error UI Components

**Create Error Display Component:**

**Component: cmpErrorDisplay**

**Properties:**

- `pErrorMessage` (Input, Text)
- `pShowRetry` (Input, Boolean)
- `pRetryAction` (Output, Boolean)

**Component Design:**

```powerFx
// Icon
icnError.Icon: Icon.ErrorBadge
icnError.Color: RGBA(168, 0, 0, 1)

// Error message
lblErrorMessage.Text: Self.pErrorMessage
lblErrorMessage.Color: RGBA(168, 0, 0, 1)

// Retry button
btnRetry.Visible: Self.pShowRetry
btnRetry.Text: "Retry"
btnRetry.OnSelect:
    Set(Self.pRetryAction, true);
    Timer.Start()

// Auto-reset timer
timReset.Duration: 100
timReset.OnTimerEnd:
    Set(Self.pRetryAction, false)
```

**Usage in Form:**

```powerFx
// Show error component when error occurs
cmpErrorDisplay.Visible: varShowError
cmpErrorDisplay.pErrorMessage: varErrorMessage
cmpErrorDisplay.pShowRetry: true

// Watch for retry
If(
    cmpErrorDisplay.pRetryAction,
    // User clicked retry
    // Re-attempt the operation
    Set(varShowError, false);
    // ... retry logic
)
```

**Inline Error Display Pattern:**

```powerFx
// Show error below input field
lblEmailError.Visible: varEmailError
lblEmailError.Text: "⚠️ " & varEmailErrorMessage
lblEmailError.Color: RGBA(168, 0, 0, 1)

// Validate on change
txtEmail.OnChange:
    If(
        IsBlank(txtEmail.Text),
        Set(varEmailError, true);
        Set(varEmailErrorMessage, "Email is required"),

        Not(IsMatch(txtEmail.Text, Email)),
        Set(varEmailError, true);
        Set(varEmailErrorMessage, "Invalid email format"),

        // Valid
        Set(varEmailError, false);
        Set(varEmailErrorMessage, "")
    )
```

### Assignment Deliverable

✅ **Create comprehensive error handling demonstration:**

- Form with multiple validation and save scenarios
- Network error simulation and handling
- Error logging system
- User-friendly error messages
- Retry mechanisms
- Fallback strategies

✅ **Implement at least 5 error scenarios:**

1. Form validation errors
2. Data source connection failure
3. API call timeout
4. Permission denied
5. Data conflict on save

✅ **Create error logging dashboard:**

- Show all logged errors
- Filter by screen, user, date
- Error frequency analysis
- Export error log capability

✅ **Documentation:**

- Error handling patterns used
- Fallback strategies
- User experience considerations
- When to show vs hide technical details

### Knowledge Check

- [ ] What is the syntax of IfError()?
- [ ] What error properties are available in FirstError?
- [ ] How do you handle multiple possible errors?
- [ ] Should you show technical error messages to users?
- [ ] What's the difference between FirstError and AllErrors?
- [ ] Where should you log errors?
- [ ] How do you implement retry logic?
- [ ] What are fallback strategies?
- [ ] Should error logging itself use error handling?
- [ ] How do you test error handling?

### Error Handling Best Practices

**✅ DO:**

- Always use IfError for data operations
- Provide user-friendly messages
- Log errors for debugging
- Implement retry mechanisms
- Have fallback strategies
- Test all error paths
- Handle both expected and unexpected errors

**❌ DON'T:**

- Show technical error messages to end users
- Ignore errors silently
- Use IfError for control flow (use If instead)
- Create nested IfError more than 2-3 levels
- Log sensitive user data in error logs
- Block user from continuing after recoverable errors

### Resources

- [Microsoft Learn: IfError function](https://learn.microsoft.com/en-us/power-platform/power-fx/reference/function-iferror)
- [Microsoft Learn: Error handling patterns](https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/errors)
- [Microsoft Learn: App reliability](https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/create-accessible-apps)
- [Video: Error Handling Best Practices](https://www.youtube.com/watch?v=example)

---

## Module 17: Deep Linking (1 hour)

### Learning Objectives

- Understand deep linking in Power Apps
- Pass parameters between apps
- Navigate with context using Param()
- Build shareable URLs with data
- Handle URL parameters safely
- Create external launch links

### Step-by-Step Instructions

#### Step 1: Understanding Deep Linking

**What is Deep Linking?**

- Navigate directly to specific screen/record in app
- Pass parameters through URL
- Launch app with context
- Shareable links with data

**Use Cases:**

- Email notification → Open specific record
- External system → Launch Power App with context
- Power Automate → Open approval in app
- QR codes → Navigate to specific item

**URL Structure:**

```
https://apps.powerapps.com/play/[AppID]?[parameters]

Example:
https://apps.powerapps.com/play/e12345-6789-abcd-efgh?
  screen=EmployeeDetail&
  employeeID=123&
  mode=edit
```

#### Step 2: Get Your App ID

**Find App ID:**

1. Open app in Power Apps Studio
2. Settings → General → App ID
3. Or from URL when playing app:
   ```
   https://apps.powerapps.com/play/e3f4a5b6-c7d8-e9f0-a1b2-c3d4e5f6a7b8
                                      ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
                                      This is your App ID
   ```

**Save App ID for Deep Linking:**

```powerFx
// Store in global variable for easy reference
App.OnStart:
    Set(varAppID, "e3f4a5b6-c7d8-e9f0-a1b2-c3d4e5f6a7b8")
```

#### Step 3: Using Param() Function

**Syntax:**

```powerFx
Param("parameterName")
```

**Read URL Parameters in App.OnStart:**

```powerFx
// App.OnStart
// ============

// Read parameters from URL
Set(varScreenParam, Param("screen"));
Set(varEmployeeID, Value(Param("employeeID")));
Set(varMode, Param("mode"));
Set(varSource, Param("source"));

// Load data based on parameters
If(
    Not(IsBlank(varEmployeeID)),
    // Employee ID provided, load that employee
    Set(
        varSelectedEmployee,
        LookUp(Employees, ID = varEmployeeID)
    )
);

// Navigate to specified screen
Switch(
    varScreenParam,
    "EmployeeDetail", Navigate(scrEmployeeDetail, ScreenTransition.None),
    "LeaveRequest", Navigate(scrLeaveRequest, ScreenTransition.None),
    "Dashboard", Navigate(scrDashboard, ScreenTransition.None),
    // Default - go to home
    Navigate(scrHome, ScreenTransition.None)
)
```

#### Step 4: Create Deep Link Builder

**Screen: scrDeepLinkBuilder**

**Generate deep links for different scenarios:**

```powerFx
// Text Input: URL components
txtAppID.Default: "e3f4a5b6-c7d8-e9f0-a1b2-c3d4e5f6a7b8"
txtScreen.HintText: "EmployeeDetail"
txtEmployeeID.HintText: "123"
txtMode.HintText: "edit"

// Generate Deep Link button
btnGenerateLink.OnSelect:
    Set(
        varDeepLink,
        "https://apps.powerapps.com/play/" & txtAppID.Text &
        If(
            Not(IsBlank(txtScreen.Text)) ||
            Not(IsBlank(txtEmployeeID.Text)) ||
            Not(IsBlank(txtMode.Text)),
            "?",
            ""
        ) &
        If(
            Not(IsBlank(txtScreen.Text)),
            "screen=" & txtScreen.Text,
            ""
        ) &
        If(
            Not(IsBlank(txtEmployeeID.Text)),
            If(Not(IsBlank(txtScreen.Text)), "&", "") &
            "employeeID=" & txtEmployeeID.Text,
            ""
        ) &
        If(
            Not(IsBlank(txtMode.Text)),
            If(
                Not(IsBlank(txtScreen.Text)) || Not(IsBlank(txtEmployeeID.Text)),
                "&",
                ""
            ) &
            "mode=" & txtMode.Text,
            ""
        )
    )

// Display generated link
lblGeneratedLink.Text: varDeepLink

// Copy to clipboard button
btnCopyLink.OnSelect:
    // Note: Direct clipboard copy not available
    // User must manually copy
    Notify(
        "Link generated! Please copy manually.",
        NotificationType.Success
    )

// Test link button (opens in browser)
btnTestLink.OnSelect:
    Launch(varDeepLink)
```

**Generated Link Examples:**

```
// View employee detail
https://apps.powerapps.com/play/e3f4a5b6-c7d8-e9f0-a1b2-c3d4e5f6a7b8?screen=EmployeeDetail&employeeID=123

// Edit employee
https://apps.powerapps.com/play/e3f4a5b6-c7d8-e9f0-a1b2-c3d4e5f6a7b8?screen=EmployeeDetail&employeeID=123&mode=edit

// View leave request
https://apps.powerapps.com/play/e3f4a5b6-c7d8-e9f0-a1b2-c3d4e5f6a7b8?screen=LeaveRequest&requestID=456&action=approve

// Dashboard with filter
https://apps.powerapps.com/play/e3f4a5b6-c7d8-e9f0-a1b2-c3d4e5f6a7b8?screen=Dashboard&department=IT&year=2025
```

#### Step 5: Handle Parameters in Target Screens

**Screen: scrEmployeeDetail**

```powerFx
// OnVisible
// Read employee ID from parameter or navigation context
If(
    Not(IsBlank(varEmployeeID)),
    // Came from deep link with parameter
    Set(
        varSelectedEmployee,
        LookUp(Employees, ID = varEmployeeID)
    ),

    IsBlank(varSelectedEmployee),
    // No employee selected, go back to list
    Navigate(scrEmployeeList, ScreenTransition.UnCoverRight)
);

// Set mode (view or edit)
If(
    varMode = "edit",
    Set(varFormMode, FormMode.Edit),
    Set(varFormMode, FormMode.View)
);

// Populate form
frmEmployeeDetail.Item: varSelectedEmployee
```

**Handle Missing/Invalid Parameters:**

```powerFx
// Validate employee ID
If(
    IsBlank(varSelectedEmployee),
    // Invalid or missing employee ID
    Notify(
        "Employee not found. Redirecting to employee list.",
        NotificationType.Warning
    );
    Navigate(scrEmployeeList, ScreenTransition.None)
)
```

#### Step 6: Deep Linking from Power Automate

**Use Case: Approval email with deep link**

**Power Automate Flow:**

```
Trigger: When item is created (Leave Requests)
↓
Action: Get Manager Email
↓
Action: Send Email

Email Body:
"
New Leave Request Pending Approval

Employee: [EmployeeName]
Leave Type: [LeaveType]
Start Date: [StartDate]
End Date: [EndDate]

Click below to review and approve:
https://apps.powerapps.com/play/e3f4a5b6-c7d8-e9f0-a1b2-c3d4e5f6a7b8?screen=LeaveApproval&requestID=[ID]&action=review

Quick Actions:
Approve: https://apps.powerapps.com/play/e3f4a5b6-c7d8-e9f0-a1b2-c3d4e5f6a7b8?screen=LeaveApproval&requestID=[ID]&action=approve
Reject: https://apps.powerapps.com/play/e3f4a5b6-c7d8-e9f0-a1b2-c3d4e5f6a7b8?screen=LeaveApproval&requestID=[ID]&action=reject
"
```

**Handle in Power App:**

```powerFx
// App.OnStart

// Read parameters
Set(varRequestID, Value(Param("requestID")));
Set(varAction, Param("action"));

If(
    Not(IsBlank(varRequestID)),

    // Load the request
    Set(
        varLeaveRequest,
        LookUp('Leave Requests', ID = varRequestID)
    );

    // Handle action
    Switch(
        varAction,

        "approve",
        // Auto-approve
        Patch(
            'Leave Requests',
            varLeaveRequest,
            {
                Status: "Approved",
                ApprovedBy: User().Email,
                ApprovedDate: Now()
            }
        );
        Notify("Leave request approved! ✅", NotificationType.Success);
        Navigate(scrLeaveApprovalList, ScreenTransition.None),

        "reject",
        // Go to rejection screen for comment
        Navigate(scrLeaveRejection, ScreenTransition.None),

        "review",
        // Go to review screen
        Navigate(scrLeaveReview, ScreenTransition.None),

        // Default - go to detail view
        Navigate(scrLeaveDetail, ScreenTransition.None)
    )
)
```

#### Step 7: QR Code Deep Links

**Generate QR Codes for Physical Assets:**

**Use Case: Equipment tracking with QR codes**

```powerFx
// Generate QR code URL for equipment
// Use external QR code generator

Set(
    varEquipmentLink,
    "https://apps.powerapps.com/play/" & varAppID &
    "?screen=EquipmentDetail&equipmentID=" & varEquipmentID
);

// Display QR code using image URL
imgQRCode.Image:
    "https://api.qrserver.com/v1/create-qr-code/?size=200x200&data=" &
    EncodeUrl(varEquipmentLink)

// Or use Power Automate to generate QR code and save to SharePoint
```

**Scanning QR Code:**

1. User scans QR code with phone camera
2. Opens URL in browser
3. Launches Power App
4. App reads equipmentID parameter
5. Shows equipment detail screen

#### Step 8: Launch External URLs from Power Apps

**Launch Different URL Types:**

```powerFx
// Open website
btnOpenWebsite.OnSelect:
    Launch("https://www.microsoft.com")

// Open email client
btnSendEmail.OnSelect:
    Launch(
        "mailto:employee@company.com" &
        "?subject=" & EncodeUrl("Leave Request Approval") &
        "&body=" & EncodeUrl("Please review my leave request.")
    )

// Open phone dialer
btnCall.OnSelect:
    Launch("tel:+1234567890")

// Open Teams chat
btnTeamsChat.OnSelect:
    Launch(
        "https://teams.microsoft.com/l/chat/0/0?users=" &
        varSelectedEmployee.Email
    )

// Open another Power App with parameters
btnOpenOtherApp.OnSelect:
    Launch(
        "https://apps.powerapps.com/play/[OtherAppID]?param1=value1"
    )

// Open SharePoint document
btnOpenDocument.OnSelect:
    Launch(varDocumentURL)
```

#### Step 9: Security Considerations for Deep Links

**Validate Parameters:**

```powerFx
// App.OnStart - Validate all parameters

// Get parameters
Set(varEmployeeID, Value(Param("employeeID")));
Set(varAction, Param("action"));

// Validate employee ID exists
If(
    Not(IsBlank(varEmployeeID)),
    Set(
        varSelectedEmployee,
        LookUp(Employees, ID = varEmployeeID)
    );

    // Check if found
    If(
        IsBlank(varSelectedEmployee),
        // Invalid ID - redirect to list
        Notify("Invalid employee ID", NotificationType.Error);
        Navigate(scrEmployeeList, ScreenTransition.None)
    )
);

// Validate action is allowed value
If(
    Not(varAction in ["view", "edit", "approve", "reject", "review"]),
    // Invalid action
    Set(varAction, "view")
)
```

**Check User Permissions:**

```powerfx
// Verify user has permission to view this record
If(
    Not(IsBlank(varSelectedEmployee)),

    // Check if user is the employee or their manager
    If(
        varSelectedEmployee.Email <> User().Email And
        varSelectedEmployee.ManagerEmail <> User().Email And
        varCurrentUser.Role <> "Admin",

        // No permission
        Notify(
            "You don't have permission to view this employee",
            NotificationType.Error
        );
        Navigate(scrHome, ScreenTransition.None)
    )
)
```

**Prevent Parameter Tampering:**

```powerFx
// Don't rely solely on URL parameters for sensitive operations
// Always validate server-side

// ❌ BAD - Trust URL parameter
Param("isAdmin") = "true"  // User can modify URL!

// ✅ GOOD - Check actual permissions
LookUp(Admins, Email = User().Email).IsAdmin

// ❌ BAD - Allow any employee ID
Patch(Employees, {ID: Value(Param("employeeID"))}, {Salary: 999999})

// ✅ GOOD - Validate user can edit this employee
If(
    varCurrentUser.Role = "HR" Or varCurrentUser.Role = "Admin",
    // Allow edit
    Patch(Employees, ...)
)
```

#### Step 10: Deep Link Testing

**Test Checklist:**

1. **Valid Parameters:**

   - Test with correct employee ID
   - Test with correct screen name
   - Verify navigation works

2. **Invalid Parameters:**

   - Test with non-existent ID
   - Test with wrong screen name
   - Test with missing parameters
   - Verify graceful error handling

3. **Security:**

   - Test accessing other user's data
   - Test without proper permissions
   - Test parameter tampering
   - Verify authorization checks

4. **Edge Cases:**
   - Test with special characters in parameters
   - Test with very long parameter values
   - Test with multiple parameters
   - Test from different browsers/devices

**Test URLs:**

```
// Valid employee
https://apps.powerapps.com/play/[AppID]?screen=EmployeeDetail&employeeID=123

// Invalid employee ID
https://apps.powerapps.com/play/[AppID]?screen=EmployeeDetail&employeeID=99999

// Missing parameter
https://apps.powerapps.com/play/[AppID]?screen=EmployeeDetail

// Invalid screen
https://apps.powerapps.com/play/[AppID]?screen=NonExistentScreen

// Special characters
https://apps.powerapps.com/play/[AppID]?screen=Search&query=John%20Smith
```

### Assignment Deliverable

✅ **Implement deep linking in Leave Management System:**

- App.OnStart parameter handling
- Navigate to leave request detail from URL
- Navigate to approval screen from URL
- Generate shareable links for leave requests
- Deep link builder screen

✅ **Create email template for Power Automate:**

- Leave request notification with deep link
- Approval deep link
- Quick action links (approve/reject)

✅ **Security implementation:**

- Parameter validation
- Permission checks
- Graceful error handling for invalid parameters

✅ **Testing:**

- Document 10 test URLs
- Test all valid scenarios
- Test all invalid scenarios
- Verify security

### Knowledge Check

- [ ] What is the structure of a Power Apps deep link?
- [ ] How do you read URL parameters in Power Apps?
- [ ] Where should you handle URL parameters?
- [ ] What's the Param() function syntax?
- [ ] How do you validate parameters safely?
- [ ] Can URL parameters be trusted for security?
- [ ] How do you generate deep links?
- [ ] What's the Launch() function for?
- [ ] How do you handle missing parameters?
- [ ] What are common deep linking use cases?

### Deep Linking Quick Reference

| Use Case          | URL Pattern                              | Handle In App                   |
| ----------------- | ---------------------------------------- | ------------------------------- |
| **View Record**   | `?screen=Detail&id=123`                  | Navigate to screen, load record |
| **Edit Record**   | `?screen=Detail&id=123&mode=edit`        | Set form mode to Edit           |
| **Approval**      | `?screen=Approval&id=123&action=approve` | Auto-approve or show UI         |
| **Filtered List** | `?screen=List&filter=Active`             | Apply filter to gallery         |
| **Search**        | `?screen=Search&query=John`              | Set search box value            |

### Resources

- [Microsoft Learn: Navigate function](https://learn.microsoft.com/en-us/power-platform/power-fx/reference/function-navigate)
- [Microsoft Learn: Param function](https://learn.microsoft.com/en-us/power-platform/power-fx/reference/function-param)
- [Microsoft Learn: Launch function](https://learn.microsoft.com/en-us/power-platform/power-fx/reference/function-launch)
- [Blog: Deep Linking Best Practices](https://powerapps.microsoft.com/en-us/blog/deep-linking/)

---

## Module 18: Sharing Canvas Apps (1 hour)

### Learning Objectives

- Understand Power Apps licensing
- Share apps with internal users
- Share apps with external users (guests)
- Manage app permissions
- Share dependent resources
- Understand security implications

### Step-by-Step Instructions

#### Step 1: Understanding Power Apps Licensing

**License Types:**

1. **Microsoft 365 Licenses** (E3, E5, Business Premium)

   - Included with Microsoft 365
   - Can run "standard" apps (SharePoint, Microsoft 365 connectors)
   - Cannot use premium connectors (SQL, Dataverse, etc.)

2. **Power Apps per User License**

   - $20/user/month (approximate)
   - Can run unlimited apps
   - Can use premium connectors
   - Can create apps

3. **Power Apps per App License**
   - $5/user/app/month (approximate)
   - Access to specific app only
   - Can use premium connectors in that app
   - Cannot create apps

**Check Your License:**

```
1. Go to make.powerapps.com
2. Settings (gear icon) → Plan(s)
3. View your current license
```

#### Step 2: Share App with Internal Users

**Step-by-Step:**

1. **Open App in Power Apps Studio**

   - Or go to make.powerapps.com

2. **Click Share**

   - In Studio: File → Share
   - In make.powerapps.com: Apps → Select app → Share

3. **Add Users:**

   - Enter email addresses or security group names
   - Can add multiple users at once
   - Can add Azure AD security groups (recommended)

4. **Set Permissions:**

   - **Can use** - Can only run the app
   - **Can edit** - Can edit and re-share the app

5. **Optional: Send email invitation**

   - Check "Send an email invitation"
   - Customize message

6. **Share dependent resources**
   - If using Dataverse: Share tables
   - If using SharePoint: Grant permissions
   - If using SQL: Grant access
   - If using flows: Share flows

**Example:**

```
Share "Leave Management System"

Users:
  - hr-team@company.com (Security Group)
  - manager1@company.com
  - manager2@company.com

Permission: Can use
☑️ Send an email invitation

Dependent Resources:
  ☑️ Leave Requests (SharePoint List) - Read/Write
  ☑️ Employees (SharePoint List) - Read
  ☑️ Leave Status Flow - Run only users
```

#### Step 3: Using Security Groups (Best Practice)

**Why Use Security Groups?**

- Manage users centrally in Azure AD
- Add/remove users without re-sharing app
- Better governance
- Easier auditing
- Consistent permissions

**Create Security Group in Azure AD:**

1. Azure Portal → Azure Active Directory
2. Groups → New Group
3. Group type: Security
4. Group name: "Leave Management Users"
5. Add members
6. Create

**Share App with Group:**

```
Share "Leave Management System"

Users: Leave Management Users
Permission: Can use
```

**Now managing access is simple:**

- Add user to group in Azure AD
- User automatically gets app access
- No need to re-share app

#### Step 4: Share with External Users (Guests)

**Prerequisites:**

- Azure AD B2B must be enabled
- External sharing must be allowed in Power Apps
- Guest user must have Power Apps license

**Steps:**

1. **Invite Guest User to Azure AD:**

   - Azure Portal → Azure Active Directory
   - Users → New guest user
   - Enter external email
   - Send invitation

2. **Guest Accepts Invitation:**

   - Receives email
   - Clicks link
   - Signs in with their account

3. **Share App with Guest:**
   - Share app as normal
   - Use guest's external email
   - Guest can now access app

**Limitations for Guests:**

- May have limited connector access
- Some features may not work
- License requirements still apply

#### Step 5: Share Dependent Resources

**SharePoint Lists:**

```
For each SharePoint list used in app:

1. Go to SharePoint list
2. Settings → List Settings
3. Permissions for this list
4. Grant Permissions
5. Add same users/groups
6. Permission level:
   - Read: View items only
   - Edit: Create, edit, delete items
   - Contribute: Edit items
```

**Dataverse Tables:**

```
1. Go to make.powerapps.com
2. Dataverse → Tables
3. Select table
4. Manage permissions
5. Add security role to users
6. Or create custom security role
```

**Power Automate Flows:**

```
1. Go to flow.microsoft.com
2. My flows → Select flow
3. Edit → Share
4. Add users
5. Permission:
   - Run only users: Can trigger flow
   - Co-owners: Can edit flow
```

**Connections:**

```
For premium connectors (SQL, etc):
- Users need their own connection
- Cannot share connections
- Each user must create own connection
OR
- Use service account with "Run only users"
```

#### Step 6: App Permission Levels Explained

**Can use:**

- Launch and run the app
- View data (based on data permissions)
- Submit forms
- Trigger flows
- Cannot edit app
- Cannot see app in Power Apps Studio

**Can edit:**

- Everything in "Can use"
- Open app in Power Apps Studio
- Edit app design and logic
- Save new versions
- Share app with others
- Delete app

**Co-owner (Flows):**

- Edit flow logic
- Share flow
- View run history
- Turn flow on/off

**Run only users (Flows):**

- Cannot see or edit flow
- Flow runs with service account permissions
- User doesn't need connection
- Better for end users

#### Step 7: Manage App Permissions

**View Current Permissions:**

```
1. make.powerapps.com
2. Apps → Select app
3. Share
4. View "Shared with"
```

**Remove User Access:**

```
1. Share screen
2. Find user in list
3. Click X to remove
4. User loses access immediately
```

**Change Permission Level:**

```
1. Share screen
2. Find user
3. Change dropdown from "Can use" to "Can edit" (or vice versa)
4. Save
```

**Audit Who Has Access:**

```powerFx
// Create admin screen showing app usage
// Use Power Apps analytics

1. make.powerapps.com
2. Apps → Select app → Analytics
3. View:
   - Daily active users
   - Unique users
   - Usage by location
   - Session duration
```

#### Step 8: Security Best Practices

**✅ DO:**

1. **Use Security Groups:**

```
✅ Share with "Leave Management Users" group
❌ Share with 50 individual users
```

2. **Principle of Least Privilege:**

```
✅ Give "Can use" permission to most users
✅ Give "Can edit" only to admins/developers
```

3. **Share Dependent Resources:**

```
✅ Ensure users have access to data sources
✅ Share flows with "Run only users"
```

4. **Document Access:**

```
✅ Keep record of who should have access
✅ Review permissions quarterly
```

5. **Use Service Accounts for Flows:**

```
✅ Flow runs with service account
✅ Users don't need premium licenses for connectors
```

**❌ DON'T:**

1. **Don't share with "Everyone":**

```
❌ Sharing with entire organization (unless necessary)
✅ Share with specific groups
```

2. **Don't give edit permissions unnecessarily:**

```
❌ All users have "Can edit"
✅ Only developers have "Can edit"
```

3. **Don't forget data permissions:**

```
❌ Share app, forget SharePoint permissions
✅ Share app AND SharePoint lists
```

4. **Don't use personal connections in production:**

```
❌ SQL connection using your personal account
✅ SQL connection using service account
```

#### Step 9: App Distribution Methods

**Method 1: Direct Link**

```
Send users link to app:
https://apps.powerapps.com/play/[AppID]

Pros: Simple, works immediately
Cons: Users must remember link
```

**Method 2: Share from Power Apps**

```
Power Apps sends email with link

Pros: Email invitation with instructions
Cons: Can go to spam
```

**Method 3: Add to Teams**

```
1. Open app
2. Add to Teams (button)
3. Select Team and Channel
4. Users access from Teams

Pros: Integrated in Teams, easy access
Cons: Requires Teams
```

**Method 4: Add to SharePoint**

```
1. Create SharePoint page
2. Add Power Apps web part
3. Select app
4. Users access from SharePoint

Pros: Integrated in SharePoint
Cons: Users need SharePoint permissions
```

**Method 5: App Launcher (Microsoft 365)**

```
Featured apps appear in Microsoft 365 app launcher

1. make.powerapps.com → Apps
2. Select app → Add to app launcher
3. Appears in office.com for users

Pros: Discoverable in M365
Cons: Admin approval may be needed
```

#### Step 10: Troubleshooting Access Issues

**User Can't See App:**

```
Checklist:
☐ Is user added to "Shared with" list?
☐ Did user receive email invitation?
☐ Check spam folder for invitation
☐ Is user logging in with correct account?
☐ Try direct link instead of email link
☐ Clear browser cache
```

**User Can Open App But Can't See Data:**

```
Checklist:
☐ Does user have permissions to data source?
☐ SharePoint: Check list permissions
☐ Dataverse: Check security roles
☐ SQL: Check database permissions
☐ Are flows shared with user?
☐ Does user need "Run only users" on flow?
```

**User Sees "You don't have a license" Error:**

```
Solution:
☐ User needs Power Apps license
☐ If using premium connectors: Per user or per app license
☐ If using only standard: M365 license may suffice
☐ Contact admin to assign license
```

**User Can't Edit App:**

```
Checklist:
☐ Is permission set to "Can edit"?
☐ Is another user editing (only 1 at a time)?
☐ Is app locked by admin?
☐ Does user have Power Apps license?
```

### Assignment Deliverable

✅ **Share Leave Management System:**

- Create Azure AD security group: "Leave Management Users"
- Share app with security group
- Share with at least 3 individual users
- Set appropriate permissions (Can use vs Can edit)

✅ **Share dependent resources:**

- Share all SharePoint lists with appropriate permissions
- Share Power Automate flows
- Document what permissions each resource needs

✅ **Create access documentation:**

- Who has access
- What permission level
- What resources they can access
- How to request access
- How to troubleshoot access issues

✅ **Test access:**

- Have another user access the app
- Verify they can perform expected functions
- Verify data permissions work correctly

### Knowledge Check

- [ ] What's the difference between M365 license and Power Apps license?
- [ ] What are the two permission levels for apps?
- [ ] Why should you use security groups?
- [ ] How do you share dependent SharePoint lists?
- [ ] What does "Run only users" mean for flows?
- [ ] Can you share your SQL connection with users?
- [ ] How do you add app to Microsoft Teams?
- [ ] What's the difference between Can use and Can edit?
- [ ] How do you remove someone's access?
- [ ] What's required to share with external users?

### Sharing Quick Reference

| Task                  | Steps                          | Notes               |
| --------------------- | ------------------------------ | ------------------- |
| **Share app**         | Apps → Share → Add users       | Use security groups |
| **Share SharePoint**  | List → Settings → Permissions  | Grant same users    |
| **Share flow**        | Flows → Share → Run only users | Recommended         |
| **Add to Teams**      | App → Add to Teams             | Select team/channel |
| **Remove access**     | Share → Find user → X          | Immediate           |
| **Change permission** | Share → Dropdown → Select      | Can use / Can edit  |

### Resources

- [Microsoft Learn: Share canvas apps](https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/share-app)
- [Microsoft Learn: Power Apps licensing](https://learn.microsoft.com/en-us/power-platform/admin/pricing-billing-skus)
- [Microsoft Learn: Security in Power Apps](https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/security/security-overview)
- [Microsoft Learn: Share canvas app resources](https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/share-app-resources)

---

## Stage 3 Part 2 Progress Summary

### Modules Completed in This Section:

- ✅ Module 16: Exception Handling (2 hours)
- ✅ Module 17: Deep Linking (1 hour)
- ✅ Module 18: Sharing Canvas Apps (1 hour)

### Completed in Part 2: 4 hours

### Cumulative Stage 3 Progress: 10 hours of 36 hours

### Remaining Modules in Stage 3:

- Module 19: Offline Capability (1 hour)
- Module 20: Monitoring Tool (1 hour)
- Module 21: Responsive Design (24 hours) - Major module

### Next Up

The next section will cover Offline Capability, Monitoring Tools, and begin the comprehensive Responsive Design module.

---

**Type "NEXT" to continue with Offline Capability, Monitoring, and Responsive Design.**
