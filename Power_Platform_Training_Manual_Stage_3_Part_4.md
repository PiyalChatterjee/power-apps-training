# Power Platform Training Manual - Stage 3 Part 4: Responsive Forms, Data Display & Complete Implementation

## Overview

This section completes Module 21 with responsive forms, data displays, dashboards, and the complete responsive implementation of the Leave Management System. **Duration: 16 hours**

---

## Module 21 Part 3: Responsive Forms (4 hours)

### Step 9: Responsive Form Layout

**Single Column on Phone, Two Column on Tablet/Desktop:**

```powerFx
// Form Container
frmEmployee.X: varContentPadding
frmEmployee.Y: conHeader.Y + conHeader.Height + varSpacing.MD
frmEmployee.Width: Min(App.Width - (varContentPadding * 2), 1200)
frmEmployee.Height: App.Height - Self.Y - varSpacing.MD

// Form Layout
frmEmployee.Layout: FormLayout.Vertical
frmEmployee.Columns: If(varIsPhone, 1, 2)
```

**Individual Form Fields - Responsive Width:**

```powerFx
// Data Card: FirstName
DataCardValue1.Width:
    If(
        varIsPhone,
        Parent.Width - (varSpacing.MD * 2),  // Full width on phone
        (Parent.Width - (varSpacing.MD * 3)) / 2  // Half width on desktop (2 columns)
    )

// Label above input
DataCardKey1.Y: 0
DataCardKey1.Size: varFontSize.Medium

// Input below label
DataCardValue1.Y: DataCardKey1.Y + DataCardKey1.Height + varSpacing.SM
DataCardValue1.Height: varControlHeight.Medium
DataCardValue1.Size: varFontSize.Medium

// Error message
ErrorMessage1.Y: DataCardValue1.Y + DataCardValue1.Height + varSpacing.XS
ErrorMessage1.Size: varFontSize.Small
```

**Form Sections with Responsive Collapse:**

```powerFx
// Section Header (clickable to expand/collapse on phone)
conSectionHeader.Width: Parent.Width
conSectionHeader.Height: 48
conSectionHeader.OnSelect:
    If(
        varIsPhone,
        Set(varPersonalInfoExpanded, !varPersonalInfoExpanded)
    )

// Expand/Collapse icon (visible on phone only)
icnExpandCollapse.Visible: varIsPhone
icnExpandCollapse.Icon: If(varPersonalInfoExpanded, Icon.ChevronDown, Icon.ChevronRight)
icnExpandCollapse.X: Parent.Width - Self.Width - varSpacing.MD

// Section Content
conSectionContent.Visible:
    If(
        varIsPhone,
        varPersonalInfoExpanded,  // Collapsible on phone
        true  // Always visible on desktop
    )
```

**Example: Employee Form with Sections**

```powerFx
// ===================================
// RESPONSIVE EMPLOYEE FORM
// ===================================

// Form Container
conEmployeeForm.X: 0
conEmployeeForm.Y: conHeader.Height
conEmployeeForm.Width: App.Width
conEmployeeForm.Height: App.Height - conHeader.Height

// Content area (with max width on desktop)
conFormContent.X: (Parent.Width - Self.Width) / 2
conFormContent.Y: varSpacing.MD
conFormContent.Width: Min(Parent.Width - (varContentPadding * 2), 1000)
conFormContent.Height: Parent.Height - varSpacing.MD

// SECTION 1: Personal Information
conSectionPersonal.X: 0
conSectionPersonal.Y: 0
conSectionPersonal.Width: Parent.Width

// Section header
lblSectionPersonal.Text: "Personal Information"
lblSectionPersonal.Size: varFontSize.Large
lblSectionPersonal.FontWeight: FontWeight.Semibold
lblSectionPersonal.OnSelect:
    If(varIsPhone, Set(varPersonalExpanded, !varPersonalExpanded))

// Expand icon (phone only)
icnPersonalExpand.Visible: varIsPhone
icnPersonalExpand.Icon: If(varPersonalExpanded, Icon.ChevronDown, Icon.ChevronRight)

// Fields container (collapsible on phone)
conPersonalFields.Visible: If(varIsPhone, varPersonalExpanded, true)
conPersonalFields.Y: lblSectionPersonal.Y + lblSectionPersonal.Height + varSpacing.MD

// First Name (left column or full width)
lblFirstName.Text: "First Name *"
lblFirstName.X: 0
lblFirstName.Y: 0
lblFirstName.Size: varFontSize.Medium

txtFirstName.X: 0
txtFirstName.Y: lblFirstName.Y + lblFirstName.Height + varSpacing.SM
txtFirstName.Width:
    If(
        varIsPhone,
        Parent.Width,
        (Parent.Width - varSpacing.MD) / 2
    )
txtFirstName.Height: varControlHeight.Medium

// Last Name (right column or below first name)
lblLastName.Text: "Last Name *"
lblLastName.X:
    If(
        varIsPhone,
        0,
        txtFirstName.X + txtFirstName.Width + varSpacing.MD
    )
lblLastName.Y:
    If(
        varIsPhone,
        txtFirstName.Y + txtFirstName.Height + varSpacing.MD,
        0
    )
lblLastName.Size: varFontSize.Medium

txtLastName.X: lblLastName.X
txtLastName.Y: lblLastName.Y + lblLastName.Height + varSpacing.SM
txtLastName.Width: txtFirstName.Width
txtLastName.Height: varControlHeight.Medium

// Email (full width always)
lblEmail.Text: "Email *"
lblEmail.X: 0
lblEmail.Y:
    If(
        varIsPhone,
        txtLastName.Y + txtLastName.Height + varSpacing.MD,
        txtFirstName.Y + txtFirstName.Height + varSpacing.MD
    )

txtEmail.X: 0
txtEmail.Y: lblEmail.Y + lblEmail.Height + varSpacing.SM
txtEmail.Width: Parent.Width
txtEmail.Height: varControlHeight.Medium

// Phone Number (left) and Department (right)
lblPhone.Text: "Phone Number"
lblPhone.X: 0
lblPhone.Y: txtEmail.Y + txtEmail.Height + varSpacing.MD

txtPhone.X: 0
txtPhone.Y: lblPhone.Y + lblPhone.Height + varSpacing.SM
txtPhone.Width:
    If(
        varIsPhone,
        Parent.Width,
        (Parent.Width - varSpacing.MD) / 2
    )
txtPhone.Height: varControlHeight.Medium

lblDepartment.Text: "Department *"
lblDepartment.X:
    If(
        varIsPhone,
        0,
        txtPhone.X + txtPhone.Width + varSpacing.MD
    )
lblDepartment.Y:
    If(
        varIsPhone,
        txtPhone.Y + txtPhone.Height + varSpacing.MD,
        lblPhone.Y
    )

ddnDepartment.X: lblDepartment.X
ddnDepartment.Y: lblDepartment.Y + lblDepartment.Height + varSpacing.SM
ddnDepartment.Width: txtPhone.Width
ddnDepartment.Height: varControlHeight.Medium
ddnDepartment.Items: colDepartments

// SECTION 2: Employment Details
conSectionEmployment.Y:
    If(
        varIsPhone,
        ddnDepartment.Y + ddnDepartment.Height + varSpacing.LG,
        txtPhone.Y + txtPhone.Height + varSpacing.LG
    )

// ... similar pattern for employment section

// Form Actions (buttons)
conFormActions.X: 0
conFormActions.Y: Parent.Height - Self.Height - varSpacing.MD
conFormActions.Width: Parent.Width
conFormActions.Height: varControlHeight.Medium + varSpacing.MD

// Buttons stack on phone, side-by-side on desktop
btnCancel.Width:
    If(
        varIsPhone,
        Parent.Width,
        150
    )
btnCancel.X:
    If(
        varIsPhone,
        0,
        Parent.Width - Self.Width - varSpacing.MD - 150 - varSpacing.SM
    )
btnCancel.Y: 0
btnCancel.Height: varControlHeight.Medium
btnCancel.Text: "Cancel"
btnCancel.OnSelect: Navigate(scrEmployeeList, ScreenTransition.UnCoverRight)

btnSave.Width: btnCancel.Width
btnSave.X:
    If(
        varIsPhone,
        0,
        Parent.Width - Self.Width - varSpacing.MD
    )
btnSave.Y:
    If(
        varIsPhone,
        btnCancel.Y + btnCancel.Height + varSpacing.SM,
        0
    )
btnSave.Height: varControlHeight.Medium
btnSave.Text: "Save"
btnSave.OnSelect: // Save logic
```

### Step 10: Responsive Date Picker

```powerFx
// Date Picker - Full width on phone, auto width on desktop
dtpStartDate.Width:
    If(
        varIsPhone,
        Parent.Width,
        300
    )
dtpStartDate.Height: varControlHeight.Medium
dtpStartDate.Size: varFontSize.Medium

// Date display format changes based on space
dtpStartDate.DateFormat:
    If(
        varIsPhone,
        "m/d/yy",  // Short format on phone
        "mmm dd, yyyy"  // Full format on desktop
    )
```

### Step 11: Responsive Combo Box / Dropdown

```powerFx
// Dropdown adapts to container
ddnEmployee.Width: Parent.Width
ddnEmployee.Height: varControlHeight.Medium
ddnEmployee.Size: varFontSize.Medium

// Show more items on desktop
ddnEmployee.MaxItemsOnScreen:
    If(
        varIsPhone,
        5,  // Limited on phone
        10  // More on desktop
    )

// Search box more prominent on desktop
ddnEmployee.SearchMode:
    If(
        varIsPhone,
        SearchMode.Delayed,
        SearchMode.Immediate
    )
```

### Step 12: Responsive File Upload

```powerFx
// Add Picture control
addPicture.X: 0
addPicture.Y: lblAttachment.Y + lblAttachment.Height + varSpacing.SM
addPicture.Width:
    If(
        varIsPhone,
        Parent.Width,
        400
    )
addPicture.Height:
    If(
        varIsPhone,
        200,
        300
    )

// Instructions
lblUploadInstructions.Text:
    If(
        varIsPhone,
        "Tap to upload",
        "Click to upload or drag and drop"
    )
```

### Step 13: Responsive Form Validation

```powerFx
// Error messages more prominent on phone
lblEmailError.Visible: varEmailError
lblEmailError.Text: "⚠️ " & varEmailErrorMessage
lblEmailError.Color: RGBA(168, 0, 0, 1)
lblEmailError.Size:
    If(
        varIsPhone,
        varFontSize.Medium,  // Larger on phone
        varFontSize.Small
    )
lblEmailError.Y: txtEmail.Y + txtEmail.Height + varSpacing.XS

// Validation summary at top on phone, inline on desktop
conValidationSummary.Visible: varShowValidation And varIsPhone
conValidationSummary.X: 0
conValidationSummary.Y: 0
conValidationSummary.Width: Parent.Width
conValidationSummary.Fill: RGBA(255, 243, 205, 1)

lblValidationSummary.Text:
    "Please fix the following errors:" & Char(10) &
    Concat(colValidationErrors, "• " & Message, Char(10))
```

---

## Module 21 Part 4: Responsive Data Display (4 hours)

### Step 14: Responsive Gallery Layouts

**Pattern 1: List View on Phone, Grid on Desktop**

```powerFx
// Gallery layout changes based on device
galEmployees.Layout:
    If(
        varIsPhone,
        Layout.Vertical,  // List on phone
        Layout.Vertical   // Still vertical, but with grid-like template
    )

// Template width
galEmployees.TemplateWidth:
    If(
        varIsPhone,
        Parent.Width,  // Full width on phone
        (Parent.Width - (varSpacing.MD * (varGridColumns + 1))) / varGridColumns
    )

// Template height
galEmployees.TemplateHeight:
    If(
        varIsPhone,
        80,  // Compact list item
        Self.TemplateWidth * 1.3  // Card with aspect ratio
    )

// Show different information based on device
lblEmployeeName.Text: ThisItem.FirstName & " " & ThisItem.LastName
lblEmployeeName.Size:
    If(varIsPhone, varFontSize.Medium, varFontSize.Large)

lblEmployeeDetails.Visible: Not(varIsPhone)  // Hide details on phone
lblEmployeeDetails.Text:
    ThisItem.Department & " • " & ThisItem.JobTitle

// Phone shows arrow, desktop shows full card
icnArrow.Visible: varIsPhone
icnArrow.Icon: Icon.ChevronRight
icnArrow.X: Parent.Width - Self.Width - varSpacing.SM
```

**Pattern 2: Table on Desktop, Cards on Mobile**

```powerFx
// Data Table (desktop only)
dtEmployees.Visible: varIsDesktop
dtEmployees.X: 0
dtEmployees.Y: 0
dtEmployees.Width: Parent.Width
dtEmployees.Height: Parent.Height

// Card Gallery (mobile/tablet)
galEmployeeCards.Visible: Not(varIsDesktop)
galEmployeeCards.X: 0
galEmployeeCards.Y: 0
galEmployeeCards.Width: Parent.Width
galEmployeeCards.Height: Parent.Height
galEmployeeCards.TemplateWidth: Parent.Width - (varSpacing.MD * 2)
galEmployeeCards.TemplatePadding: varSpacing.MD
```

### Step 15: Responsive Cards

**Employee Card Component:**

```powerFx
// Component: cmpEmployeeCard

// Card container
conCard.Width: Parent.TemplateWidth
conCard.Height:
    If(
        varIsPhone,
        100,  // Compact on phone
        200   // Full on desktop
    )
conCard.Fill: RGBA(255, 255, 255, 1)
conCard.BorderColor: RGBA(200, 200, 200, 1)
conCard.BorderThickness: 1

// Layout: Image left, content right on phone
//         Image top, content below on desktop

// Employee image
imgEmployee.X:
    If(
        varIsPhone,
        varSpacing.SM,
        (Parent.Width - Self.Width) / 2
    )
imgEmployee.Y: varSpacing.SM
imgEmployee.Width:
    If(
        varIsPhone,
        70,
        120
    )
imgEmployee.Height: imgEmployee.Width
imgEmployee.BorderRadius:
    If(
        varIsPhone,
        35,
        60
    )

// Content container
conCardContent.X:
    If(
        varIsPhone,
        imgEmployee.X + imgEmployee.Width + varSpacing.SM,
        varSpacing.SM
    )
conCardContent.Y:
    If(
        varIsPhone,
        varSpacing.SM,
        imgEmployee.Y + imgEmployee.Height + varSpacing.SM
    )
conCardContent.Width:
    If(
        varIsPhone,
        Parent.Width - conCardContent.X - varSpacing.SM,
        Parent.Width - (varSpacing.SM * 2)
    )

// Name
lblCardName.Text: Self.pFirstName & " " & Self.pLastName
lblCardName.Size:
    If(varIsPhone, varFontSize.Medium, varFontSize.Large)
lblCardName.FontWeight: FontWeight.Semibold

// Department
lblCardDepartment.Text: Self.pDepartment
lblCardDepartment.Size: varFontSize.Small
lblCardDepartment.Y: lblCardName.Y + lblCardName.Height + varSpacing.XS

// Phone number (desktop only)
lblCardPhone.Visible: Not(varIsPhone)
lblCardPhone.Text: Self.pPhoneNumber
lblCardPhone.Size: varFontSize.Small
lblCardPhone.Y: lblCardDepartment.Y + lblCardDepartment.Height + varSpacing.XS

// Actions
conCardActions.Y: Parent.Height - Self.Height - varSpacing.SM
conCardActions.Width: Parent.Width - (varSpacing.SM * 2)

// Buttons stack on phone, side-by-side on desktop
btnCardView.Width:
    If(
        varIsPhone,
        Parent.Width,
        (Parent.Width - varSpacing.SM) / 2
    )
btnCardView.Height: varControlHeight.Small
btnCardView.Text: "View"

btnCardEdit.Width: btnCardView.Width
btnCardEdit.X:
    If(
        varIsPhone,
        0,
        btnCardView.X + btnCardView.Width + varSpacing.SM
    )
btnCardEdit.Y:
    If(
        varIsPhone,
        btnCardView.Y + btnCardView.Height + varSpacing.XS,
        0
    )
btnCardEdit.Height: varControlHeight.Small
btnCardEdit.Text: "Edit"
```

### Step 16: Responsive Table View

**Desktop: Full Data Table**

```powerFx
// Desktop - Use Data Table control
dtLeaveRequests.Visible: varIsDesktop
dtLeaveRequests.Items: colLeaveRequests
dtLeaveRequests.Width: Parent.Width
dtLeaveRequests.Height: Parent.Height

// Columns (all visible on desktop)
Columns:
[
    {Header: "Employee", Value: "Employee.FullName"},
    {Header: "Leave Type", Value: "LeaveType.Value"},
    {Header: "Start Date", Value: "StartDate"},
    {Header: "End Date", Value: "EndDate"},
    {Header: "Days", Value: "TotalDays"},
    {Header: "Status", Value: "Status"},
    {Header: "Actions", Value: ""}
]
```

**Mobile: Compact List**

```powerFx
// Mobile/Tablet - Use Gallery with condensed info
galLeaveRequests.Visible: Not(varIsDesktop)
galLeaveRequests.Items: colLeaveRequests
galLeaveRequests.TemplateHeight: 120

// Each item shows key info only
lblLeaveEmployee.Text: ThisItem.Employee.FullName
lblLeaveEmployee.Size: varFontSize.Medium
lblLeaveEmployee.FontWeight: FontWeight.Semibold

lblLeaveDates.Text:
    Text(ThisItem.StartDate, "[$-en-US]mmm dd") & " - " &
    Text(ThisItem.EndDate, "[$-en-US]mmm dd")
lblLeaveDates.Size: varFontSize.Small

lblLeaveType.Text: ThisItem.LeaveType.Value & " (" & ThisItem.TotalDays & " days)"
lblLeaveType.Size: varFontSize.Small

// Status badge
conStatus.Fill:
    Switch(
        ThisItem.Status,
        "Approved", RGBA(76, 175, 80, 1),
        "Rejected", RGBA(244, 67, 54, 1),
        "Pending", RGBA(255, 152, 0, 1),
        RGBA(158, 158, 158, 1)
    )
lblStatus.Text: ThisItem.Status
lblStatus.Color: RGBA(255, 255, 255, 1)

// Tap to view details
icnChevron.Icon: Icon.ChevronRight
icnChevron.X: Parent.Width - Self.Width - varSpacing.SM
```

### Step 17: Responsive Charts and Visualizations

**Chart Component - Adjusts Size and Layout:**

```powerFx
// Chart container
conChart.Width:
    If(
        varIsPhone,
        Parent.Width - (varSpacing.MD * 2),
        (Parent.Width - (varSpacing.MD * 3)) / 2
    )
conChart.Height:
    If(
        varIsPhone,
        300,
        400
    )

// Chart title
lblChartTitle.Text: "Leave Requests by Type"
lblChartTitle.Size:
    If(varIsPhone, varFontSize.Medium, varFontSize.Large)

// Column chart (vertical bars)
chartLeaveTypes.Width: Parent.Width - (varSpacing.MD * 2)
chartLeaveTypes.Height: Parent.Height - lblChartTitle.Height - varSpacing.LG
chartLeaveTypes.Y: lblChartTitle.Y + lblChartTitle.Height + varSpacing.SM

// Show labels on desktop, hide on phone
chartLeaveTypes.DataLabels: If(varIsDesktop, true, false)

// Fewer items on phone
chartLeaveTypes.Items:
    If(
        varIsPhone,
        FirstN(colLeaveStats, 5),  // Top 5 on phone
        colLeaveStats  // All on desktop
    )
```

### Step 18: Responsive Search and Filters

**Desktop: Horizontal Filter Bar**

```powerFx
// Desktop filter bar
conFilters.Visible: varIsDesktop
conFilters.Layout: Layout.Horizontal
conFilters.X: 0
conFilters.Y: 0
conFilters.Width: Parent.Width
conFilters.Height: 60

// Search box
txtSearch.X: varSpacing.MD
txtSearch.Y: (Parent.Height - Self.Height) / 2
txtSearch.Width: 300
txtSearch.Height: varControlHeight.Medium

// Filters side-by-side
ddnDepartment.X: txtSearch.X + txtSearch.Width + varSpacing.MD
ddnDepartment.Y: (Parent.Height - Self.Height) / 2
ddnDepartment.Width: 200

ddnStatus.X: ddnDepartment.X + ddnDepartment.Width + varSpacing.MD
ddnStatus.Y: (Parent.Height - Self.Height) / 2
ddnStatus.Width: 200
```

**Mobile: Stacked Filters with Collapsible Panel**

```powerFx
// Mobile - Collapsible filters
btnShowFilters.Visible: Not(varIsDesktop)
btnShowFilters.Text: "Filters " & If(varShowFilters, "▼", "▶")
btnShowFilters.OnSelect: Set(varShowFilters, !varShowFilters)

conMobileFilters.Visible: varShowFilters And Not(varIsDesktop)
conMobileFilters.X: 0
conMobileFilters.Y: btnShowFilters.Y + btnShowFilters.Height + varSpacing.SM
conMobileFilters.Width: Parent.Width

// Search (full width)
txtMobileSearch.X: varSpacing.MD
txtMobileSearch.Y: varSpacing.SM
txtMobileSearch.Width: Parent.Width - (varSpacing.MD * 2)
txtMobileSearch.Height: varControlHeight.Medium

// Dropdowns (full width, stacked)
ddnMobileDepartment.X: varSpacing.MD
ddnMobileDepartment.Y: txtMobileSearch.Y + txtMobileSearch.Height + varSpacing.SM
ddnMobileDepartment.Width: Parent.Width - (varSpacing.MD * 2)
ddnMobileDepartment.Height: varControlHeight.Medium

ddnMobileStatus.X: varSpacing.MD
ddnMobileStatus.Y: ddnMobileDepartment.Y + ddnMobileDepartment.Height + varSpacing.SM
ddnMobileStatus.Width: Parent.Width - (varSpacing.MD * 2)
ddnMobileStatus.Height: varControlHeight.Medium

// Apply button
btnApplyFilters.X: varSpacing.MD
btnApplyFilters.Y: ddnMobileStatus.Y + ddnMobileStatus.Height + varSpacing.SM
btnApplyFilters.Width: Parent.Width - (varSpacing.MD * 2)
btnApplyFilters.Height: varControlHeight.Medium
btnApplyFilters.Text: "Apply Filters"
btnApplyFilters.OnSelect:
    // Apply filters
    Set(varShowFilters, false)
```

---

## Module 21 Part 5: Responsive Dashboard (4 hours)

### Step 19: Dashboard Layout

**Desktop: Multi-Column Grid**

```powerFx
// Dashboard container
conDashboard.X: If(varIsPhone, 0, varSidebarWidth)
conDashboard.Y: conHeader.Height
conDashboard.Width: If(varIsPhone, App.Width, App.Width - varSidebarWidth)
conDashboard.Height: App.Height - conHeader.Height

// Content with max width
conDashContent.X: (Parent.Width - Self.Width) / 2
conDashContent.Y: varSpacing.MD
conDashContent.Width: Min(Parent.Width - (varContentPadding * 2), 1400)

// Row 1: KPI Cards (4 on desktop, 2 on tablet, 1 on phone)
conKPIRow.X: 0
conKPIRow.Y: 0
conKPIRow.Width: Parent.Width
conKPIRow.Height:
    If(
        varIsPhone,
        (varKPICardHeight * 4) + (varSpacing.MD * 3),  // Stack all 4
        If(
            varIsTablet,
            (varKPICardHeight * 2) + varSpacing.MD,  // 2 rows of 2
            varKPICardHeight  // 1 row of 4
        )
    )

Set(varKPICardHeight, 120);
Set(
    varKPICardWidth,
    If(
        varIsPhone,
        conKPIRow.Width,
        If(
            varIsTablet,
            (conKPIRow.Width - varSpacing.MD) / 2,
            (conKPIRow.Width - (varSpacing.MD * 3)) / 4
        )
    )
)
```

**KPI Card Positions:**

```powerFx
// KPI Card 1: Total Employees
cmpKPICard1.X: 0
cmpKPICard1.Y: 0
cmpKPICard1.Width: varKPICardWidth
cmpKPICard1.Height: varKPICardHeight

// KPI Card 2: Pending Approvals
cmpKPICard2.X:
    If(
        varIsPhone,
        0,
        cmpKPICard1.X + cmpKPICard1.Width + varSpacing.MD
    )
cmpKPICard2.Y:
    If(
        varIsPhone Or varIsTablet,
        cmpKPICard1.Y + cmpKPICard1.Height + varSpacing.MD,
        0
    )
cmpKPICard2.Width: varKPICardWidth
cmpKPICard2.Height: varKPICardHeight

// KPI Card 3: On Leave Today
cmpKPICard3.X:
    If(
        varIsPhone,
        0,
        If(
            varIsTablet,
            0,
            cmpKPICard2.X + cmpKPICard2.Width + varSpacing.MD
        )
    )
cmpKPICard3.Y:
    If(
        varIsPhone,
        cmpKPICard2.Y + cmpKPICard2.Height + varSpacing.MD,
        If(
            varIsTablet,
            cmpKPICard2.Y + cmpKPICard2.Height + varSpacing.MD,
            0
        )
    )
cmpKPICard3.Width: varKPICardWidth
cmpKPICard3.Height: varKPICardHeight

// KPI Card 4: Leave Balance
cmpKPICard4.X:
    If(
        varIsPhone,
        0,
        If(
            varIsTablet,
            cmpKPICard3.X + cmpKPICard3.Width + varSpacing.MD,
            cmpKPICard3.X + cmpKPICard3.Width + varSpacing.MD
        )
    )
cmpKPICard4.Y:
    If(
        varIsPhone,
        cmpKPICard3.Y + cmpKPICard3.Height + varSpacing.MD,
        cmpKPICard3.Y
    )
cmpKPICard4.Width: varKPICardWidth
cmpKPICard4.Height: varKPICardHeight
```

### Step 20: Responsive KPI Card Component

```powerFx
// Component: cmpKPICard

// Custom Properties:
// - pTitle (Input, Text)
// - pValue (Input, Number)
// - pIcon (Input, Text)
// - pTrend (Input, Number)
// - pColor (Input, Color)

// Card container
conKPICard.Width: Parent.TemplateWidth
conKPICard.Height: Parent.TemplateHeight
conKPICard.Fill: RGBA(255, 255, 255, 1)
conKPICard.BorderColor: RGBA(230, 230, 230, 1)
conKPICard.BorderThickness: 1

// Layout changes based on phone vs desktop
// Phone: Horizontal layout (icon left, content right)
// Desktop: Vertical layout (icon top, content below)

// Icon
conIcon.X:
    If(
        varIsPhone,
        varSpacing.MD,
        (Parent.Width - Self.Width) / 2
    )
conIcon.Y: varSpacing.MD
conIcon.Width: If(varIsPhone, 50, 60)
conIcon.Height: conIcon.Width
conIcon.Fill: ColorFade(Self.pColor, 80%)

icnKPI.Icon:
    Switch(
        Self.pIcon,
        "People", Icon.People,
        "Alert", Icon.Warning,
        "Calendar", Icon.CalendarBlank,
        "Check", Icon.CheckBadge,
        Icon.Info
    )
icnKPI.Color: Self.pColor

// Content container
conKPIContent.X:
    If(
        varIsPhone,
        conIcon.X + conIcon.Width + varSpacing.SM,
        varSpacing.MD
    )
conKPIContent.Y:
    If(
        varIsPhone,
        varSpacing.MD,
        conIcon.Y + conIcon.Height + varSpacing.SM
    )
conKPIContent.Width:
    If(
        varIsPhone,
        Parent.Width - conKPIContent.X - varSpacing.MD,
        Parent.Width - (varSpacing.MD * 2)
    )

// Value (large number)
lblKPIValue.Text: Text(Self.pValue, "#,##0")
lblKPIValue.Size:
    If(
        varIsPhone,
        varFontSize.XLarge,
        varFontSize.Heading
    )
lblKPIValue.FontWeight: FontWeight.Bold
lblKPIValue.Color: Self.pColor

// Title
lblKPITitle.Text: Self.pTitle
lblKPITitle.Size: varFontSize.Small
lblKPITitle.Color: RGBA(100, 100, 100, 1)
lblKPITitle.Y: lblKPIValue.Y + lblKPIValue.Height + varSpacing.XS

// Trend indicator
conTrend.Visible: Not(IsBlank(Self.pTrend))
conTrend.Y: lblKPITitle.Y + lblKPITitle.Height + varSpacing.XS

icnTrend.Icon:
    If(
        Self.pTrend > 0,
        Icon.UpArrow,
        If(Self.pTrend < 0, Icon.DownArrow, Icon.Remove)
    )
icnTrend.Color:
    If(
        Self.pTrend > 0,
        RGBA(76, 175, 80, 1),  // Green
        If(Self.pTrend < 0, RGBA(244, 67, 54, 1), RGBA(158, 158, 158, 1))  // Red or Gray
    )

lblTrend.Text: Text(Abs(Self.pTrend), "0.0%")
lblTrend.Color: icnTrend.Color
lblTrend.Size: varFontSize.Small
```

### Step 21: Responsive Chart Widgets

**Row 2: Charts (2 columns on desktop, stack on mobile)**

```powerFx
// Chart Row
conChartRow.X: 0
conChartRow.Y: conKPIRow.Y + conKPIRow.Height + varSpacing.LG
conChartRow.Width: Parent.Width

// Leave Requests Chart (left or top)
conLeaveChart.X: 0
conLeaveChart.Y: 0
conLeaveChart.Width:
    If(
        varIsPhone,
        Parent.Width,
        (Parent.Width - varSpacing.MD) / 2
    )
conLeaveChart.Height: 350

// Department Distribution Chart (right or below)
conDeptChart.X:
    If(
        varIsPhone,
        0,
        conLeaveChart.X + conLeaveChart.Width + varSpacing.MD
    )
conDeptChart.Y:
    If(
        varIsPhone,
        conLeaveChart.Y + conLeaveChart.Height + varSpacing.MD,
        0
    )
conDeptChart.Width: conLeaveChart.Width
conDeptChart.Height: 350
```

### Step 22: Responsive Recent Activity

**Row 3: Recent Activity List (full width)**

```powerFx
// Recent Activity section
conRecentActivity.X: 0
conRecentActivity.Y: conChartRow.Y + Max(conLeaveChart.Height, conDeptChart.Y + conDeptChart.Height) + varSpacing.LG
conRecentActivity.Width: Parent.Width

// Section header
lblRecentActivityTitle.Text: "Recent Activity"
lblRecentActivityTitle.Size: varFontSize.Large
lblRecentActivityTitle.FontWeight: FontWeight.Semibold

// Activity gallery
galRecentActivity.Y: lblRecentActivityTitle.Y + lblRecentActivityTitle.Height + varSpacing.SM
galRecentActivity.Width: Parent.Width
galRecentActivity.TemplateHeight:
    If(varIsPhone, 80, 60)

// Activity item - different layouts
// Phone: Icon top, text below
// Desktop: Icon left, text right

imgActivityAvatar.X:
    If(varIsPhone, varSpacing.SM, varSpacing.MD)
imgActivityAvatar.Y:
    If(varIsPhone, varSpacing.XS, (Parent.Height - Self.Height) / 2)
imgActivityAvatar.Width: If(varIsPhone, 32, 40)
imgActivityAvatar.Height: imgActivityAvatar.Width

lblActivityText.X:
    If(
        varIsPhone,
        varSpacing.SM,
        imgActivityAvatar.X + imgActivityAvatar.Width + varSpacing.SM
    )
lblActivityText.Y:
    If(
        varIsPhone,
        imgActivityAvatar.Y + imgActivityAvatar.Height + varSpacing.XS,
        varSpacing.SM
    )
lblActivityText.Width:
    If(
        varIsPhone,
        Parent.Width - (varSpacing.SM * 2),
        Parent.Width - lblActivityText.X - 150
    )
lblActivityText.Size: varFontSize.Small

lblActivityTime.X:
    If(
        varIsPhone,
        varSpacing.SM,
        Parent.Width - Self.Width - varSpacing.MD
    )
lblActivityTime.Y:
    If(
        varIsPhone,
        lblActivityText.Y + lblActivityText.Height + varSpacing.XS,
        (Parent.Height - Self.Height) / 2
    )
lblActivityTime.Size: varFontSize.Small
lblActivityTime.Color: RGBA(150, 150, 150, 1)
```

### Step 23: Quick Actions Panel

**Desktop: Side Panel**
**Mobile: Bottom Sheet**

```powerFx
// Quick Actions - Desktop (right side panel)
conQuickActionsDesktop.Visible: varIsDesktop
conQuickActionsDesktop.X: Parent.Width - Self.Width
conQuickActionsDesktop.Y: 0
conQuickActionsDesktop.Width: 300
conQuickActionsDesktop.Height: Parent.Height

// Quick Actions - Mobile (floating action button)
btnQuickActionsMobile.Visible: Not(varIsDesktop)
btnQuickActionsMobile.X: Parent.Width - Self.Width - varSpacing.LG
btnQuickActionsMobile.Y: Parent.Height - Self.Height - varSpacing.LG
btnQuickActionsMobile.Width: 56
btnQuickActionsMobile.Height: 56
btnQuickActionsMobile.BorderRadius: 28
btnQuickActionsMobile.Icon: Icon.Add
btnQuickActionsMobile.OnSelect: Set(varShowQuickActions, !varShowQuickActions)

// Mobile bottom sheet
conQuickActionsMobile.Visible: varShowQuickActions And Not(varIsDesktop)
conQuickActionsMobile.X: 0
conQuickActionsMobile.Y: App.Height - Self.Height
conQuickActionsMobile.Width: App.Width
conQuickActionsMobile.Height: 300

// Quick action buttons
galQuickActions.Items:
    Table(
        {Label: "New Leave Request", Icon: Icon.CalendarBlank, Screen: scrNewLeave},
        {Label: "View Approvals", Icon: Icon.CheckBadge, Screen: scrApprovals},
        {Label: "Team Calendar", Icon: Icon.Calendar, Screen: scrCalendar},
        {Label: "Reports", Icon: Icon.BarChart, Screen: scrReports}
    )

galQuickActions.TemplateHeight: 60
galQuickActions.OnSelect:
    Navigate(ThisItem.Screen, ScreenTransition.None);
    Set(varShowQuickActions, false)
```

---

## Module 21 Part 6: Complete Leave Management System Responsive Implementation (8 hours)

### Step 24: Screen-by-Screen Responsive Implementation

**Screen 1: Login/Home Screen**

```powerFx
// scrHome - Landing page
// Full-screen hero section with responsive text

conHero.Width: App.Width
conHero.Height: App.Height

// Background image
imgHeroBackground.Width: Parent.Width
imgHeroBackground.Height: Parent.Height

// Content overlay (centered)
conHeroContent.Width: Min(App.Width - (varContentPadding * 2), 800)
conHeroContent.X: (Parent.Width - Self.Width) / 2
conHeroContent.Y: (Parent.Height - Self.Height) / 2

// Welcome text
lblWelcome.Text: "Welcome to Leave Management System"
lblWelcome.Size:
    If(
        varIsPhone,
        varFontSize.XLarge,
        varFontSize.Heading * 1.5
    )
lblWelcome.FontWeight: FontWeight.Bold
lblWelcome.Align: Align.Center

// Description
lblDescription.Text:
    "Manage your leave requests, track your balance, and stay organized"
lblDescription.Size:
    If(varIsPhone, varFontSize.Medium, varFontSize.Large)
lblDescription.Align: Align.Center
lblDescription.Y: lblWelcome.Y + lblWelcome.Height + varSpacing.MD

// CTA button
btnGetStarted.Width:
    If(varIsPhone, Parent.Width - (varSpacing.MD * 2), 250)
btnGetStarted.Height: varControlHeight.Large
btnGetStarted.X: (Parent.Width - Self.Width) / 2
btnGetStarted.Y: lblDescription.Y + lblDescription.Height + varSpacing.LG
btnGetStarted.Text: "Get Started"
btnGetStarted.Size: varFontSize.Large
btnGetStarted.OnSelect:
    Navigate(scrDashboard, ScreenTransition.Fade)
```

**Screen 2: Dashboard (Completed in Part 5)**

**Screen 3: Leave Request List**

```powerFx
// scrLeaveList - Show all leave requests

// Header with filters
conListHeader.Y: conHeader.Height
conListHeader.Width: App.Width
conListHeader.Height:
    If(
        varIsPhone And varShowFilters,
        240,  // Expanded on phone
        If(varIsPhone, 60, 80)
    )

// Title
lblListTitle.Text: "Leave Requests"
lblListTitle.Size: varFontSize.Heading
lblListTitle.X: varContentPadding
lblListTitle.Y: varSpacing.MD

// Filter button (phone only)
btnFilters.Visible: varIsPhone
btnFilters.X: Parent.Width - Self.Width - varContentPadding
btnFilters.Y: varSpacing.SM
btnFilters.Text: "Filters"
btnFilters.Icon: Icon.Filter
btnFilters.OnSelect: Set(varShowFilters, !varShowFilters)

// Filters (implemented in Part 4)
// Search and dropdowns...

// List/Grid area
conListContent.Y: conListHeader.Y + conListHeader.Height
conListContent.Width: App.Width
conListContent.Height: App.Height - conListContent.Y

// Desktop: Data table
// Mobile: Card gallery
// (Implemented in Part 4)

// FAB (Mobile) - New Leave Request
btnNewLeave.Visible: varIsPhone
btnNewLeave.X: Parent.Width - Self.Width - varSpacing.LG
btnNewLeave.Y: Parent.Height - Self.Height - varSpacing.LG
btnNewLeave.Width: 56
btnNewLeave.Height: 56
btnNewLeave.BorderRadius: 28
btnNewLeave.Icon: Icon.Add
btnNewLeave.OnSelect: Navigate(scrNewLeave, ScreenTransition.Cover)

// Desktop: New button in header
btnNewLeaveDesktop.Visible: varIsDesktop
btnNewLeaveDesktop.X: Parent.Width - Self.Width - varContentPadding
btnNewLeaveDesktop.Y: varSpacing.MD
btnNewLeaveDesktop.Width: 180
btnNewLeaveDesktop.Text: "New Leave Request"
btnNewLeaveDesktop.OnSelect: Navigate(scrNewLeave, ScreenTransition.None)
```

**Screen 4: New Leave Request Form**

```powerFx
// scrNewLeave - Create leave request

// Mobile: Full screen form
// Desktop: Modal/centered form

conFormContainer.Width:
    If(
        varIsPhone,
        App.Width,
        Min(App.Width - (varContentPadding * 2), 700)
    )
conFormContainer.X:
    If(varIsPhone, 0, (App.Width - Self.Width) / 2)
conFormContainer.Y:
    If(varIsPhone, 0, (App.Height - Self.Height) / 2)
conFormContainer.Height:
    If(varIsPhone, App.Height, Min(App.Height - 100, 800))

// Form header
conFormHeader.Width: Parent.Width
conFormHeader.Height: 60

// Back button (phone) or Close (desktop)
btnBack.Icon:
    If(varIsPhone, Icon.ChevronLeft, Icon.Cancel)
btnBack.OnSelect:
    Navigate(scrLeaveList, ScreenTransition.UnCoverRight)

lblFormTitle.Text: "New Leave Request"
lblFormTitle.Size: varFontSize.Large
lblFormTitle.FontWeight: FontWeight.Semibold

// Form content (scrollable)
conFormScroll.Y: conFormHeader.Height
conFormScroll.Width: Parent.Width
conFormScroll.Height: Parent.Height - conFormHeader.Height - conFormActions.Height

// Form fields (implemented in Part 3)
// All fields responsive...

// Form actions footer
conFormActions.Y: Parent.Height - Self.Height
conFormActions.Width: Parent.Width
conFormActions.Height: If(varIsPhone, 120, 80)

// Buttons (stack on phone, side-by-side on desktop)
// (Implemented in Part 3)
```

**Screen 5: Leave Request Detail**

```powerFx
// scrLeaveDetail - View/Edit specific leave request

// Split view on desktop, full screen on mobile
// Desktop: Image/details left, timeline right
// Mobile: Stack vertically

conDetailContainer.Width:
    If(varIsPhone, App.Width, Min(App.Width - 100, 1200))
conDetailContainer.X:
    If(varIsPhone, 0, (App.Width - Self.Width) / 2)

// Details section
conLeaveDetails.Width:
    If(
        varIsPhone,
        Parent.Width,
        (Parent.Width - varSpacing.LG) / 2
    )
conLeaveDetails.X: 0
conLeaveDetails.Y: 0

// Employee info card
conEmployeeInfo.Width: Parent.Width - (varContentPadding * 2)
conEmployeeInfo.X: varContentPadding

// Leave details
lblLeaveType.Text: "Leave Type"
lblLeaveTypeValue.Text: varSelectedLeave.LeaveType.Value
lblLeaveTypeValue.Size: varFontSize.Large
lblLeaveTypeValue.FontWeight: FontWeight.Semibold

// Date range
lblDates.Text: "Duration"
lblDatesValue.Text:
    Text(varSelectedLeave.StartDate, "[$-en-US]mmm dd, yyyy") & " - " &
    Text(varSelectedLeave.EndDate, "[$-en-US]mmm dd, yyyy") & Char(10) &
    "(" & varSelectedLeave.TotalDays & " days)"

// Status badge
conStatusBadge.Fill:
    Switch(
        varSelectedLeave.Status,
        "Approved", RGBA(76, 175, 80, 0.2),
        "Rejected", RGBA(244, 67, 54, 0.2),
        RGBA(255, 152, 0, 0.2)
    )
lblStatusBadge.Text: varSelectedLeave.Status
lblStatusBadge.Color:
    Switch(
        varSelectedLeave.Status,
        "Approved", RGBA(27, 94, 32, 1),
        "Rejected", RGBA(183, 28, 28, 1),
        RGBA(230, 81, 0, 1)
    )

// Timeline section (right on desktop, below on mobile)
conTimeline.Width:
    If(
        varIsPhone,
        Parent.Width,
        (Parent.Width - varSpacing.LG) / 2
    )
conTimeline.X:
    If(
        varIsPhone,
        0,
        conLeaveDetails.X + conLeaveDetails.Width + varSpacing.LG
    )
conTimeline.Y:
    If(
        varIsPhone,
        conLeaveDetails.Y + conLeaveDetails.Height + varSpacing.MD,
        0
    )

// Timeline items
galTimeline.Width: Parent.Width
galTimeline.TemplateHeight: 80
galTimeline.Items: varSelectedLeave.Timeline

// Actions (approve/reject buttons)
conDetailActions.Y: Parent.Height - Self.Height
conDetailActions.Width: Parent.Width
// Buttons responsive (from Part 3)
```

**Screen 6: Approval Screen**

```powerFx
// scrApprovals - Pending approvals for managers

// Similar to leave list but with quick approve/reject
// Desktop: Show more details, inline actions
// Mobile: Swipe to approve/reject pattern

galApprovals.Items:
    Filter(
        'Leave Requests',
        Status = "Pending" And
        Manager.Email = User().Email
    )

// Desktop template - inline buttons
btnApprove.Visible: varIsDesktop
btnReject.Visible: varIsDesktop

// Mobile template - swipe actions
// Swipe right = approve, swipe left = reject
galApprovals.OnSelect:
    If(
        varIsPhone,
        // Show action sheet
        Set(varSelectedForAction, ThisItem);
        Set(varShowActionSheet, true),

        // Desktop - do nothing (buttons handle it)
    )

// Mobile action sheet
conActionSheet.Visible: varShowActionSheet And varIsPhone
conActionSheet.X: 0
conActionSheet.Y: App.Height - Self.Height
conActionSheet.Width: App.Width
conActionSheet.Height: 200

btnApproveAction.Text: "Approve"
btnApproveAction.Icon: Icon.CheckBadge
btnApproveAction.OnSelect:
    Patch(
        'Leave Requests',
        varSelectedForAction,
        {
            Status: "Approved",
            ApprovedBy: User().Email,
            ApprovedDate: Now()
        }
    );
    Set(varShowActionSheet, false);
    Notify("Leave request approved", NotificationType.Success)

btnRejectAction.Text: "Reject"
btnRejectAction.Icon: Icon.Cancel
btnRejectAction.OnSelect:
    // Navigate to rejection reason screen
    Set(varShowActionSheet, false);
    Navigate(scrRejectReason, ScreenTransition.Cover)
```

**Screen 7: Calendar View**

```powerFx
// scrCalendar - Team calendar showing who's on leave

// Desktop: Full month calendar with names
// Mobile: List view grouped by date

// Desktop calendar
conCalendar.Visible: varIsDesktop
conCalendar.Width: Parent.Width - (varContentPadding * 2)
conCalendar.X: varContentPadding

// Custom calendar control
// 7 columns (days of week)
// 5-6 rows (weeks)

// Mobile list
galCalendarMobile.Visible: Not(varIsDesktop)
galCalendarMobile.Width: Parent.Width
galCalendarMobile.Items:
    SortByColumns(
        Filter(
            'Leave Requests',
            Status = "Approved" And
            StartDate <= DateAdd(Today(), 30, Days) And
            EndDate >= Today()
        ),
        "StartDate"
    )
galCalendarMobile.TemplateHeight: 100

// Month navigation
btnPrevMonth.Icon: Icon.ChevronLeft
btnNextMonth.Icon: Icon.ChevronRight
btnToday.Text: "Today"
lblCurrentMonth.Text: Text(varCurrentMonth, "[$-en-US]mmmm yyyy")
lblCurrentMonth.Size:
    If(varIsPhone, varFontSize.Large, varFontSize.Heading)
```

### Step 25: Testing Responsive Design

**Test Matrix:**

```
Device Type | Orientation | Resolution      | Key Test Areas
=================================================================
Phone       | Portrait    | 375 x 667       | Navigation, Forms, Cards
Phone       | Landscape   | 667 x 375       | Layout adaptation
Tablet      | Portrait    | 768 x 1024      | 2-column layouts
Tablet      | Landscape   | 1024 x 768      | Side navigation
Desktop     | Landscape   | 1366 x 768      | Full features
Desktop     | Landscape   | 1920 x 1080     | Max width constraints
```

**Testing Checklist:**

```
✅ Navigation
   ☐ Horizontal menu on desktop
   ☐ Hamburger menu on phone
   ☐ All links work
   ☐ Menu closes after selection (mobile)

✅ Forms
   ☐ Single column on phone
   ☐ Two columns on desktop
   ☐ All inputs accessible
   ☐ Buttons stack/side-by-side
   ☐ Validation messages visible

✅ Lists/Galleries
   ☐ List view on phone
   ☐ Grid view on desktop
   ☐ Tap targets adequate (min 44px)
   ☐ Scrolling works
   ☐ Empty state shown

✅ Dashboard
   ☐ KPI cards stack correctly
   ☐ Charts resize properly
   ☐ All data visible
   ☐ No horizontal scroll

✅ Detail Screens
   ☐ Content fits screen
   ☐ Images scale appropriately
   ☐ Text readable at all sizes
   ☐ Actions accessible

✅ Performance
   ☐ Loads quickly on mobile
   ☐ No lag when resizing
   ☐ Smooth animations
   ☐ Efficient formulas

✅ Accessibility
   ☐ Touch targets 44px minimum
   ☐ Text contrast sufficient
   ☐ Font sizes readable
   ☐ Icons have labels
```

### Step 26: Performance Optimization for Responsive

**Optimize Image Loading:**

```powerFx
// Load different image sizes based on device
imgHero.Image:
    If(
        varIsPhone,
        "hero-mobile-800w.jpg",
        If(
            varIsTablet,
            "hero-tablet-1200w.jpg",
            "hero-desktop-1920w.jpg"
        )
    )

// Or use responsive parameters
imgHero.Image:
    "hero.jpg?w=" &
    If(varIsPhone, "800", If(varIsTablet, "1200", "1920"))
```

**Conditional Loading:**

```powerFx
// Don't load desktop-only content on mobile
conDesktopCharts.Visible: varIsDesktop

// Load data only when needed
galDetailedStats.Items:
    If(
        varIsDesktop,
        colDetailedStats,  // Full dataset
        FirstN(colDetailedStats, 10)  // Limited on mobile
    )
```

**Optimize Formulas:**

```powerFx
// Cache repeated calculations
Set(
    varCardWidth,
    If(
        varIsPhone,
        Parent.Width,
        (Parent.Width - varSpacing.MD) / 2
    )
);

// Use varCardWidth everywhere instead of recalculating
```

### Assignment Deliverable

✅ **Complete responsive Leave Management System:**

- All screens responsive (Home, Dashboard, List, Form, Detail, Approvals, Calendar)
- Navigation adapts to device
- Forms responsive with proper validation
- Data displays optimize for screen size
- Dashboard with responsive KPIs and charts
- Testing completed across all device types

✅ **Responsive components library:**

- cmpResponsiveButton
- cmpResponsiveInput
- cmpKPICard
- cmpEmployeeCard
- cmpNavigationMenu (desktop + mobile)

✅ **Documentation:**

- Responsive design guide
- Breakpoint definitions
- Layout patterns used
- Component usage guide
- Testing results

✅ **Performance metrics:**

- Load time on mobile vs desktop
- Formula execution times
- Image sizes optimized
- Before/after comparison

✅ **Video demonstration:**

- Show app on phone (portrait and landscape)
- Show app on tablet
- Show app on desktop
- Demonstrate resize behavior
- Show all key features working

### Knowledge Check

- [ ] What are the three main breakpoints?
- [ ] How do you get current app width?
- [ ] What's the difference between App.Width and Parent.Width?
- [ ] How do you make a container full width?
- [ ] How do you center a container?
- [ ] What's a common pattern for form layouts?
- [ ] How do you stack vs side-by-side buttons?
- [ ] What's the minimum touch target size?
- [ ] How do you optimize images for mobile?
- [ ] What's the purpose of varGridColumns?

### Responsive Design Best Practices

**✅ DO:**

- Design mobile-first
- Use percentage-based widths
- Set maximum widths for large screens
- Test on actual devices
- Optimize images for device
- Use appropriate font sizes
- Provide adequate touch targets
- Hide/show content appropriately
- Use responsive variables
- Test all orientations

**❌ DON'T:**

- Use fixed pixel widths
- Assume screen size
- Make text too small
- Use tiny touch targets
- Load unnecessary data
- Ignore landscape orientation
- Forget about tablets
- Overcomplicate layouts
- Use too many breakpoints
- Sacrifice performance for design

### Resources

- [Microsoft Learn: Responsive design](https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/create-responsive-layout)
- [Microsoft Learn: App formulas](https://learn.microsoft.com/en-us/power-platform/power-fx/reference/object-app)
- [Microsoft Learn: Performance tips](https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/performance-tips)
- [Blog: Building Responsive Power Apps](https://powerapps.microsoft.com/en-us/blog/responsive/)
- [Video: Responsive Design Tutorial](https://www.youtube.com/watch?v=example)

---

## Stage 3 Complete! 🎉

### Final Summary

**Stage 3: Intermediate & Advanced Canvas Apps**
**Total Duration: 36 hours**

**Modules Completed:**

1. ✅ **Module 14: Delegation (5 hours)**

   - Deep understanding of delegation
   - Testing methodology
   - Data source comparison
   - Fixes for common issues

2. ✅ **Module 15: Concurrency (1 hour)**

   - Concurrent() function
   - Performance optimization
   - App startup improvements

3. ✅ **Module 16: Exception Handling (2 hours)**

   - IfError() implementation
   - Error logging
   - User-friendly messages
   - Graceful degradation

4. ✅ **Module 17: Deep Linking (1 hour)**

   - Param() function
   - URL navigation
   - Power Automate integration
   - Security considerations

5. ✅ **Module 18: Sharing Canvas Apps (1 hour)**

   - Licensing understanding
   - Security groups
   - Dependent resources
   - Guest access

6. ✅ **Module 19: Offline Capability (1 hour)**

   - SaveData/LoadData
   - Pending submissions queue
   - Sync strategies

7. ✅ **Module 20: Monitoring Tool (1 hour)**

   - Power Apps Monitor
   - Performance debugging
   - Event tracking

8. ✅ **Module 21: Responsive Design (24 hours)**
   - Responsive fundamentals
   - Navigation patterns
   - Forms adaptation
   - Data display optimization
   - Dashboard layouts
   - Complete system implementation

**Key Deliverables:**

- Fully responsive Leave Management System
- Comprehensive component library
- Testing documentation
- Performance metrics
- Best practices guide

**Skills Mastered:**

- Advanced data operations
- Performance optimization
- Error handling
- Cross-device design
- User experience design
- Production deployment readiness

---

## Next Stage Preview

**Stage 4: Cloud Flows (25.5 hours)**

- Automated workflows
- Canvas app integration
- Data operations
- Approval flows
- Error handling in flows

---

**Congratulations on completing Stage 3! Type "NEXT" when ready to begin Stage 4: Cloud Flows.**
