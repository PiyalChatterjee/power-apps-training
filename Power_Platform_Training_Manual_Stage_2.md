# Power Platform Training Manual - Stage 2: UI Design, Theming & App Configuration

## Overview

This stage focuses on creating professional, visually appealing Canvas Apps with proper theming, understanding app settings, and implementing performance best practices. **Duration: 23 hours**

---

## Module 5: UI Design (8 hours)

### Learning Objectives

- Translate Figma designs into Power Apps interfaces
- Implement responsive layouts and proper spacing
- Apply design principles for professional UIs
- Build a complete Leave Management System interface
- Master alignment, sizing, and visual hierarchy

### Step-by-Step Instructions

#### Step 1: Understanding the Leave Management System Requirements

**System Overview:**
A Leave Management System allows employees to:

- View their leave balance
- Submit leave requests
- Track leave request status
- View leave history
- Cancel pending requests

**Key Screens to Build:**

1. Dashboard/Home Screen
2. Leave Request Form
3. Leave History/List View
4. Leave Details Screen
5. Profile/Settings Screen

#### Step 2: Access and Review Figma Design

1. **Open Figma Design** (refer to the Figma link provided in your training materials)
2. **Review Design Specifications:**

   - Color palette
   - Typography (font families, sizes, weights)
   - Spacing system (margins, paddings)
   - Component library
   - Screen layouts

3. **Export Assets:**
   - Logo images
   - Icons
   - Background patterns (if any)
   - Note dimensions and positions from Figma

#### Step 3: Set Up New Canvas App

1. Go to [make.powerapps.com](https://make.powerapps.com)
2. Click **"+ Create"** → **"Blank app"** → **"Blank canvas app"**
3. Name: `LeaveManagementSystem`
4. Format: **Tablet** (1366 x 768) for desktop focus
   - _Note: We'll make it responsive in Module 21_
5. Click **"Create"**

#### Step 4: Define Color Palette Variables

Before building screens, set up theme colors as global variables.

**Add App.OnStart:**

1. Click on **App** in tree view (left panel)
2. Find **OnStart** property
3. Add color definitions:

```powerFx
// ===================================
// THEME COLORS - Based on Figma Design
// ===================================
Set(varColorPrimary, RGBA(0, 120, 212, 1));           // Primary Blue
Set(varColorPrimaryDark, RGBA(0, 90, 158, 1));        // Darker Blue for hover
Set(varColorPrimaryLight, RGBA(232, 244, 253, 1));    // Light Blue for backgrounds

Set(varColorSecondary, RGBA(16, 124, 16, 1));         // Green for success
Set(varColorWarning, RGBA(255, 185, 0, 1));           // Orange for warnings
Set(varColorDanger, RGBA(196, 49, 75, 1));            // Red for errors
Set(varColorInfo, RGBA(0, 188, 242, 1));              // Cyan for info

Set(varColorBackground, RGBA(250, 250, 250, 1));      // Light gray background
Set(varColorSurface, RGBA(255, 255, 255, 1));         // White surface
Set(varColorBorder, RGBA(225, 223, 221, 1));          // Light border

Set(varColorTextPrimary, RGBA(50, 49, 48, 1));        // Dark text
Set(varColorTextSecondary, RGBA(96, 94, 92, 1));      // Gray text
Set(varColorTextDisabled, RGBA(200, 198, 196, 1));    // Disabled text

// Typography
Set(varFontSizeHeading1, 32);
Set(varFontSizeHeading2, 24);
Set(varFontSizeHeading3, 18);
Set(varFontSizeBody, 14);
Set(varFontSizeSmall, 12);

// Spacing System (8px base)
Set(varSpacingXS, 4);
Set(varSpacingS, 8);
Set(varSpacingM, 16);
Set(varSpacingL, 24);
Set(varSpacingXL, 32);
Set(varSpacingXXL, 48);

// Border Radius
Set(varBorderRadiusSmall, 4);
Set(varBorderRadiusMedium, 8);
Set(varBorderRadiusLarge, 12);

// Shadow (simulate with borders for now)
Set(varShadowColor, RGBA(0, 0, 0, 0.1));
```

4. **Test OnStart:** Click **"..."** next to App → **"Run OnStart"**

#### Step 5: Build Screen 1 - Dashboard/Home Screen

**Rename Screen:**

1. Rename `Screen1` to `scrHome`

**Set Screen Properties:**

- **Fill**: `varColorBackground`

**A. Create Header Section**

1. **Insert Rectangle for Header Background:**

   - Insert → **"Rectangle"**
   - Rename: `recHeaderBackground`
   - **Properties:**
     - X: `0`
     - Y: `0`
     - Width: `Parent.Width`
     - Height: `80`
     - Fill: `varColorPrimary`
     - BorderThickness: `0`

2. **Add Company Logo:**

   - Insert → **"Image"**
   - Rename: `imgCompanyLogo`
   - **Properties:**
     - X: `varSpacingL`
     - Y: `(recHeaderBackground.Height - Self.Height) / 2`
     - Width: `150`
     - Height: `50`
     - Image: Upload your logo or use placeholder
     - ImagePosition: `ImagePosition.Fit`

3. **Add App Title:**

   - Insert → **"Label"**
   - Rename: `lblAppTitle`
   - **Properties:**
     - Text: `"Leave Management System"`
     - X: `imgCompanyLogo.X + imgCompanyLogo.Width + varSpacingM`
     - Y: `(recHeaderBackground.Height - Self.Height) / 2`
     - Width: `400`
     - Height: `recHeaderBackground.Height`
     - Color: `varColorSurface`
     - Size: `varFontSizeHeading2`
     - Font: `Font.'Segoe UI'`
     - FontWeight: `FontWeight.Semibold`
     - Align: `Align.Left`
     - VerticalAlign: `VerticalAlign.Middle`

4. **Add User Profile Section:**
   - Insert → **"Label"** for user name
   - Rename: `lblUserName`
   - **Properties:**
     - Text: `"Welcome, " & User().FullName`
     - X: `Parent.Width - Self.Width - varSpacingL`
     - Y: `(recHeaderBackground.Height - Self.Height) / 2`
     - Width: `300`
     - Height: `recHeaderBackground.Height`
     - Color: `varColorSurface`
     - Size: `varFontSizeBody`
     - Align: `Align.Right`
     - VerticalAlign: `VerticalAlign.Middle`

**B. Create Main Content Area with Statistics Cards**

1. **Add Container for Stats:**

   - Insert → **"Rectangle"**
   - Rename: `recStatsContainer`
   - **Properties:**
     - X: `varSpacingXL`
     - Y: `recHeaderBackground.Y + recHeaderBackground.Height + varSpacingXL`
     - Width: `Parent.Width - (varSpacingXL * 2)`
     - Height: `160`
     - Fill: `Color.Transparent`
     - BorderThickness: `0`

2. **Create Leave Balance Card (Card 1):**

   **Background:**

   - Insert → **"Rectangle"**
   - Rename: `recCardTotalLeave`
   - **Properties:**
     - X: `recStatsContainer.X`
     - Y: `recStatsContainer.Y`
     - Width: `(recStatsContainer.Width - (varSpacingM * 2)) / 3`
     - Height: `140`
     - Fill: `varColorSurface`
     - BorderColor: `varColorBorder`
     - BorderThickness: `1`
     - RadiusTopLeft: `varBorderRadiusMedium`
     - RadiusTopRight: `varBorderRadiusMedium`
     - RadiusBottomLeft: `varBorderRadiusMedium`
     - RadiusBottomRight: `varBorderRadiusMedium`

   **Icon:**

   - Insert → **"Icon"**
   - Rename: `icoTotalLeave`
   - **Properties:**
     - Icon: `Icon.Calendar`
     - X: `recCardTotalLeave.X + varSpacingM`
     - Y: `recCardTotalLeave.Y + varSpacingM`
     - Width: `40`
     - Height: `40`
     - Color: `varColorPrimary`

   **Value Label:**

   - Insert → **"Label"**
   - Rename: `lblTotalLeaveValue`
   - **Properties:**
     - Text: `"20"` (will be dynamic later)
     - X: `recCardTotalLeave.X + varSpacingM`
     - Y: `icoTotalLeave.Y + icoTotalLeave.Height + varSpacingS`
     - Width: `recCardTotalLeave.Width - (varSpacingM * 2)`
     - Height: `40`
     - Color: `varColorTextPrimary`
     - Size: `varFontSizeHeading1`
     - FontWeight: `FontWeight.Bold`
     - Align: `Align.Left`

   **Description Label:**

   - Insert → **"Label"**
   - Rename: `lblTotalLeaveDesc`
   - **Properties:**
     - Text: `"Total Leave Days"`
     - X: `recCardTotalLeave.X + varSpacingM`
     - Y: `lblTotalLeaveValue.Y + lblTotalLeaveValue.Height`
     - Width: `recCardTotalLeave.Width - (varSpacingM * 2)`
     - Height: `30`
     - Color: `varColorTextSecondary`
     - Size: `varFontSizeBody`
     - Align: `Align.Left`

3. **Create Available Leave Card (Card 2):**

   Repeat the same structure as Card 1, but with different positioning and values:

   **recCardAvailableLeave:**

   - X: `recCardTotalLeave.X + recCardTotalLeave.Width + varSpacingM`
   - Icon: `Icon.CircleFill` (green)
   - Value: `"12"`
   - Description: `"Available Days"`
   - Icon Color: `varColorSecondary`

4. **Create Used Leave Card (Card 3):**

   **recCardUsedLeave:**

   - X: `recCardAvailableLeave.X + recCardAvailableLeave.Width + varSpacingM`
   - Icon: `Icon.Clock`
   - Value: `"8"`
   - Description: `"Used Days"`
   - Icon Color: `varColorInfo`

**C. Create Quick Actions Section**

1. **Section Title:**

   - Insert → **"Label"**
   - Rename: `lblQuickActions`
   - **Properties:**
     - Text: `"Quick Actions"`
     - X: `varSpacingXL`
     - Y: `recCardTotalLeave.Y + recCardTotalLeave.Height + varSpacingXL`
     - Width: `300`
     - Height: `40`
     - Color: `varColorTextPrimary`
     - Size: `varFontSizeHeading2`
     - FontWeight: `FontWeight.Semibold`

2. **New Leave Request Button:**

   - Insert → **"Button"**
   - Rename: `btnNewLeaveRequest`
   - **Properties:**
     - Text: `"+ New Leave Request"`
     - X: `varSpacingXL`
     - Y: `lblQuickActions.Y + lblQuickActions.Height + varSpacingM`
     - Width: `250`
     - Height: `50`
     - Fill: `varColorPrimary`
     - HoverFill: `varColorPrimaryDark`
     - Color: `varColorSurface`
     - Size: `varFontSizeBody`
     - FontWeight: `FontWeight.Semibold`
     - RadiusTopLeft: `varBorderRadiusSmall`
     - RadiusTopRight: `varBorderRadiusSmall`
     - RadiusBottomLeft: `varBorderRadiusSmall`
     - RadiusBottomRight: `varBorderRadiusSmall`
     - OnSelect: `Navigate(scrLeaveRequest, ScreenTransition.Fade)` (we'll create this screen next)

3. **View History Button:**
   - Insert → **"Button"**
   - Rename: `btnViewHistory`
   - **Properties:**
     - Text: `"View Leave History"`
     - X: `btnNewLeaveRequest.X + btnNewLeaveRequest.Width + varSpacingM`
     - Y: `btnNewLeaveRequest.Y`
     - Width: `250`
     - Height: `50`
     - Fill: `varColorSurface`
     - HoverFill: `varColorPrimaryLight`
     - PressedFill: `varColorPrimaryLight`
     - Color: `varColorPrimary`
     - BorderColor: `varColorPrimary`
     - BorderThickness: `2`
     - Size: `varFontSizeBody`
     - FontWeight: `FontWeight.Semibold`
     - RadiusTopLeft: `varBorderRadiusSmall`
     - RadiusTopRight: `varBorderRadiusSmall`
     - RadiusBottomLeft: `varBorderRadiusSmall`
     - RadiusBottomRight: `varBorderRadiusSmall`

**D. Create Recent Leave Requests Section**

1. **Section Title:**

   - Insert → **"Label"**
   - Rename: `lblRecentRequests`
   - **Properties:**
     - Text: `"Recent Leave Requests"`
     - X: `varSpacingXL`
     - Y: `btnNewLeaveRequest.Y + btnNewLeaveRequest.Height + varSpacingXL`
     - Width: `400`
     - Height: `40`
     - Color: `varColorTextPrimary`
     - Size: `varFontSizeHeading2`
     - FontWeight: `FontWeight.Semibold`

2. **Create Sample Data Collection:**

   - Add a button temporarily: `btnCreateSampleData`
   - **OnSelect:**

   ```powerFx
   ClearCollect(
       colLeaveRequests,
       {
           ID: 1,
           LeaveType: "Annual Leave",
           StartDate: Date(2025, 11, 15),
           EndDate: Date(2025, 11, 19),
           Days: 5,
           Status: "Pending",
           Reason: "Family vacation",
           SubmittedDate: Date(2025, 11, 10)
       },
       {
           ID: 2,
           LeaveType: "Sick Leave",
           StartDate: Date(2025, 11, 8),
           EndDate: Date(2025, 11, 8),
           Days: 1,
           Status: "Approved",
           Reason: "Medical appointment",
           SubmittedDate: Date(2025, 11, 5)
       },
       {
           ID: 3,
           LeaveType: "Annual Leave",
           StartDate: Date(2025, 10, 20),
           EndDate: Date(2025, 10, 24),
           Days: 5,
           Status: "Approved",
           Reason: "Personal time off",
           SubmittedDate: Date(2025, 10, 10)
       },
       {
           ID: 4,
           LeaveType: "Unpaid Leave",
           StartDate: Date(2025, 12, 1),
           EndDate: Date(2025, 12, 5),
           Days: 5,
           Status: "Rejected",
           Reason: "Personal reasons",
           SubmittedDate: Date(2025, 11, 8)
       }
   );
   Notify("Sample data created", NotificationType.Success)
   ```

   - Run this button once, then delete it

3. **Insert Gallery for Recent Requests:**

   - Insert → **"Gallery"** → **"Blank vertical"**
   - Rename: `galRecentRequests`
   - **Properties:**
     - X: `varSpacingXL`
     - Y: `lblRecentRequests.Y + lblRecentRequests.Height + varSpacingM`
     - Width: `Parent.Width - (varSpacingXL * 2)`
     - Height: `Parent.Height - Self.Y - varSpacingXL`
     - Items: `colLeaveRequests`
     - TemplatePadding: `varSpacingS`
     - TemplateSize: `100`

4. **Design Gallery Template:**

   Select the gallery, then add controls inside:

   **Background Rectangle:**

   - Insert → **"Rectangle"** (inside gallery)
   - Rename: `recRequestCard`
   - **Properties:**
     - X: `0`
     - Y: `0`
     - Width: `Parent.TemplateWidth`
     - Height: `Parent.TemplateHeight - varSpacingS`
     - Fill: `varColorSurface`
     - BorderColor: `varColorBorder`
     - BorderThickness: `1`
     - RadiusTopLeft: `varBorderRadiusMedium`
     - RadiusTopRight: `varBorderRadiusMedium`
     - RadiusBottomLeft: `varBorderRadiusMedium`
     - RadiusBottomRight: `varBorderRadiusMedium`

   **Leave Type Label:**

   - Insert → **"Label"**
   - Rename: `lblLeaveType`
   - **Properties:**
     - Text: `ThisItem.LeaveType`
     - X: `varSpacingM`
     - Y: `varSpacingM`
     - Width: `300`
     - Height: `30`
     - Color: `varColorTextPrimary`
     - Size: `varFontSizeBody`
     - FontWeight: `FontWeight.Semibold`

   **Date Range Label:**

   - Insert → **"Label"**
   - Rename: `lblDateRange`
   - **Properties:**
     - Text: `Text(ThisItem.StartDate, "mmm dd") & " - " & Text(ThisItem.EndDate, "mmm dd, yyyy") & " (" & ThisItem.Days & " days)"`
     - X: `varSpacingM`
     - Y: `lblLeaveType.Y + lblLeaveType.Height`
     - Width: `400`
     - Height: `25`
     - Color: `varColorTextSecondary`
     - Size: `varFontSizeSmall`

   **Status Badge:**

   - Insert → **"Label"**
   - Rename: `lblStatus`
   - **Properties:**
     - Text: `ThisItem.Status`
     - X: `Parent.TemplateWidth - Self.Width - varSpacingM`
     - Y: `varSpacingM`
     - Width: `100`
     - Height: `30`
     - Color: `Switch(
    ThisItem.Status,
    "Approved", varColorSecondary,
    "Pending", varColorWarning,
    "Rejected", varColorDanger,
    varColorTextSecondary
)`
     - Fill: `Switch(
    ThisItem.Status,
    "Approved", ColorFade(varColorSecondary, 0.9),
    "Pending", ColorFade(varColorWarning, 0.9),
    "Rejected", ColorFade(varColorDanger, 0.9),
    varColorBackground
)`
     - Size: `varFontSizeSmall`
     - FontWeight: `FontWeight.Semibold`
     - Align: `Align.Center`
     - VerticalAlign: `VerticalAlign.Middle`
     - RadiusTopLeft: `varBorderRadiusSmall`
     - RadiusTopRight: `varBorderRadiusSmall`
     - RadiusBottomLeft: `varBorderRadiusSmall`
     - RadiusBottomRight: `varBorderRadiusSmall`

   **Reason Label:**

   - Insert → **"Label"**
   - Rename: `lblReason`
   - **Properties:**
     - Text: `"Reason: " & ThisItem.Reason`
     - X: `varSpacingM`
     - Y: `lblDateRange.Y + lblDateRange.Height + varSpacingS`
     - Width: `Parent.TemplateWidth - (varSpacingM * 2)`
     - Height: `25`
     - Color: `varColorTextSecondary`
     - Size: `varFontSizeSmall`

   **View Details Button (Icon):**

   - Insert → **"Icon"**
   - Rename: `icoViewDetails`
   - **Properties:**
     - Icon: `Icon.ChevronRight`
     - X: `Parent.TemplateWidth - Self.Width - varSpacingM`
     - Y: `(Parent.TemplateHeight - Self.Height) / 2`
     - Width: `30`
     - Height: `30`
     - Color: `varColorTextSecondary`
     - OnSelect: `Navigate(scrLeaveDetails, ScreenTransition.Cover)` (we'll create this later)

#### Step 6: Build Screen 2 - Leave Request Form

1. **Add New Screen:**

   - Click **"New screen"** → **"Blank"**
   - Rename: `scrLeaveRequest`
   - Fill: `varColorBackground`

2. **Create Header (reusable pattern):**

   Copy the header from `scrHome` OR rebuild:

   **Header Background:**

   - Rectangle: `recHeaderLeaveRequest`
   - Same properties as before

   **Back Button:**

   - Insert → **"Icon"**
   - Rename: `icoBack`
   - **Properties:**
     - Icon: `Icon.ChevronLeft`
     - X: `varSpacingM`
     - Y: `(recHeaderLeaveRequest.Height - Self.Height) / 2`
     - Width: `40`
     - Height: `40`
     - Color: `varColorSurface`
     - OnSelect: `Back()`

   **Screen Title:**

   - Label: `lblScreenTitle`
   - Text: `"New Leave Request"`
   - Position next to back button

3. **Create Form Container:**

   **Form Background:**

   - Insert → **"Rectangle"**
   - Rename: `recFormContainer`
   - **Properties:**
     - X: `(Parent.Width - 600) / 2` (centered)
     - Y: `recHeaderLeaveRequest.Y + recHeaderLeaveRequest.Height + varSpacingXL`
     - Width: `600`
     - Height: `Parent.Height - Self.Y - varSpacingXL`
     - Fill: `varColorSurface`
     - BorderColor: `varColorBorder`
     - BorderThickness: `1`
     - RadiusTopLeft: `varBorderRadiusMedium`
     - RadiusTopRight: `varBorderRadiusMedium`
     - RadiusBottomLeft: `varBorderRadiusMedium`
     - RadiusBottomRight: `varBorderRadiusMedium`

4. **Add Form Fields:**

   **A. Leave Type Dropdown:**

   Label:

   - `lblLeaveTypeForm`
   - Text: `"Leave Type *"`
   - X: `recFormContainer.X + varSpacingL`
   - Y: `recFormContainer.Y + varSpacingL`

   Dropdown:

   - `ddnLeaveTypeForm`
   - Items: `["Annual Leave", "Sick Leave", "Personal Leave", "Unpaid Leave", "Maternity/Paternity Leave"]`
   - X: `lblLeaveTypeForm.X`
   - Y: `lblLeaveTypeForm.Y + lblLeaveTypeForm.Height + varSpacingS`
   - Width: `recFormContainer.Width - (varSpacingL * 2)`

   **B. Start Date:**

   Label:

   - `lblStartDateForm`
   - Text: `"Start Date *"`
   - Y: `ddnLeaveTypeForm.Y + ddnLeaveTypeForm.Height + varSpacingM`

   Date Picker:

   - `dtpStartDateForm`
   - DefaultDate: `Today()`
   - Y: `lblStartDateForm.Y + lblStartDateForm.Height + varSpacingS`

   **C. End Date:**

   Label:

   - `lblEndDateForm`
   - Text: `"End Date *"`

   Date Picker:

   - `dtpEndDateForm`
   - DefaultDate: `Today()`

   **D. Days Calculation (Read-only):**

   Label:

   - `lblDaysCalculated`
   - Text: `"Total Days: " & DateDiff(dtpStartDateForm.SelectedDate, dtpEndDateForm.SelectedDate, Days) + 1`
   - Color: `varColorPrimary`
   - FontWeight: `FontWeight.Bold`

   **E. Reason Text Area:**

   Label:

   - `lblReasonForm`
   - Text: `"Reason *"`

   Text Input (multi-line):

   - `txtReasonForm`
   - Mode: `TextMode.MultiLine`
   - Height: `100`
   - HintText: `"Please provide a reason for your leave request..."`

5. **Add Validation and Submit Button:**

   **Submit Button:**

   - `btnSubmitLeave`
   - Text: `"Submit Request"`
   - X: `recFormContainer.X + varSpacingL`
   - Y: `txtReasonForm.Y + txtReasonForm.Height + varSpacingXL`
   - Width: `200`
   - Height: `50`
   - Fill: `varColorPrimary`
   - **OnSelect:**

   ```powerFx
   // Validation
   If(
       IsBlank(ddnLeaveTypeForm.Selected.Value),
       Notify("Please select leave type", NotificationType.Error),

       dtpStartDateForm.SelectedDate < Today(),
       Notify("Start date cannot be in the past", NotificationType.Error),

       dtpEndDateForm.SelectedDate < dtpStartDateForm.SelectedDate,
       Notify("End date must be after start date", NotificationType.Error),

       IsBlank(txtReasonForm.Text),
       Notify("Please provide a reason", NotificationType.Error),

       // All validations passed - add to collection
       Collect(
           colLeaveRequests,
           {
               ID: CountRows(colLeaveRequests) + 1,
               LeaveType: ddnLeaveTypeForm.Selected.Value,
               StartDate: dtpStartDateForm.SelectedDate,
               EndDate: dtpEndDateForm.SelectedDate,
               Days: DateDiff(dtpStartDateForm.SelectedDate, dtpEndDateForm.SelectedDate, Days) + 1,
               Status: "Pending",
               Reason: txtReasonForm.Text,
               SubmittedDate: Today()
           }
       );
       Notify("Leave request submitted successfully!", NotificationType.Success);

       // Reset form
       Reset(ddnLeaveTypeForm);
       Reset(dtpStartDateForm);
       Reset(dtpEndDateForm);
       Reset(txtReasonForm);

       // Navigate back
       Navigate(scrHome, ScreenTransition.Fade)
   )
   ```

   **Cancel Button:**

   - `btnCancelLeave`
   - Text: `"Cancel"`
   - X: `btnSubmitLeave.X + btnSubmitLeave.Width + varSpacingM`
   - Fill: `varColorSurface`
   - BorderColor: `varColorBorder`
   - Color: `varColorTextSecondary`
   - OnSelect: `Back()`

#### Step 7: Test Your UI

1. Click **"Play"** (▶) button
2. Test navigation from Home → New Leave Request
3. Fill form and submit
4. Verify new request appears in gallery
5. Test validation by leaving fields blank
6. Check responsive behavior at different zoom levels

#### Step 8: Apply Consistent Styling

**Create a Style Guide Document:**

Make sure ALL similar elements use the same properties:

- All headers: Same height, fill, border
- All cards: Same border radius, padding, shadow effect
- All buttons: Same heights, border radius
- All labels: Consistent fonts and sizes
- All form fields: Same width, height, spacing

**Tips for Visual Consistency:**

1. Use Copy/Paste for repeated elements
2. Reference variables instead of hard-coded values
3. Test at 100% zoom to see actual sizes
4. Check color contrast for accessibility

### Assignment Deliverable

✅ **Complete Leave Management System UI with:**

- Home/Dashboard screen with statistics cards
- Leave request form screen with validation
- Recent requests list with status badges
- Consistent color scheme throughout
- Proper spacing and alignment
- Professional visual hierarchy

✅ **Additional Screens to Build (continue the pattern):**

- Leave Details screen (full info for one request)
- Leave History screen (filterable list)
- Profile/Settings screen

### Design Principles Applied

**Visual Hierarchy:**

- Larger elements = more important
- Bold fonts = emphasis
- Color = status and categories

**Consistency:**

- Same patterns repeated
- Predictable layouts
- Uniform spacing

**Alignment:**

- Everything lines up with something
- Use grid system (8px base)

**Whitespace:**

- Breathing room between elements
- Not cramped or cluttered

### Knowledge Check

- [ ] Can you explain the 8px spacing system?
- [ ] Why use variables for colors instead of hard-coding?
- [ ] How do you calculate dynamic widths for card layouts?
- [ ] What's the formula for centering an element horizontally?

### Resources

- [Microsoft Learn: Design apps for accessibility](https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/accessible-apps)
- [Microsoft Learn: UI patterns](https://learn.microsoft.com/en-us/power-apps/guidance/patterns/overview)
- [Figma to Power Apps guide](https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/figma)
- [Material Design principles](https://material.io/design/introduction)

---

## Module 6: App Theming (2 hours)

### Learning Objectives

- Understand Power Apps theming architecture
- Import and configure app themes
- Create centralized theme management
- Implement theme switching functionality
- Use OnStart for theme initialization

### Step-by-Step Instructions

#### Step 1: Understanding App Theming Concept

**What is App Theming?**

- Centralized management of colors, fonts, and styles
- Consistency across entire application
- Easy to update/rebrand
- Reduces code duplication

**Theme Components:**

1. **Color Palette** - Primary, secondary, status colors
2. **Typography** - Font families, sizes, weights
3. **Spacing** - Margins, paddings, gaps
4. **Effects** - Border radius, shadows, animations

#### Step 2: Download Reference MS App File

1. Reference the Neudesic Documentation provided in training materials
2. Download the `.msapp` file with theme configured
3. Save to your local machine

#### Step 3: Import and Explore Theme App

**Import the App:**

1. Go to [make.powerapps.com](https://make.powerapps.com)
2. Click **"Apps"** in left navigation
3. Click **"Import canvas app"**
4. Upload the `.msapp` file
5. Click **"Import"**
6. Once imported, click **"Edit"** to open in Studio

**Explore the OnStart:**

1. Click on **"App"** in tree view
2. Review the **OnStart** property
3. Observe the theme structure

#### Step 4: Understanding Theme Structure

**Typical Theme Object Structure:**

```powerFx
Set(
    varTheme,
    {
        // Color Palette
        Colors: {
            Primary: RGBA(0, 120, 212, 1),
            PrimaryDark: RGBA(0, 90, 158, 1),
            PrimaryLight: RGBA(232, 244, 253, 1),
            Secondary: RGBA(16, 124, 16, 1),
            Success: RGBA(16, 124, 16, 1),
            Warning: RGBA(255, 185, 0, 1),
            Danger: RGBA(196, 49, 75, 1),
            Info: RGBA(0, 188, 242, 1),
            Background: RGBA(250, 250, 250, 1),
            Surface: RGBA(255, 255, 255, 1),
            Border: RGBA(225, 223, 221, 1),
            TextPrimary: RGBA(50, 49, 48, 1),
            TextSecondary: RGBA(96, 94, 92, 1),
            TextDisabled: RGBA(200, 198, 196, 1)
        },

        // Typography
        Typography: {
            FontFamily: Font.'Segoe UI',
            Heading1: 32,
            Heading2: 24,
            Heading3: 18,
            Heading4: 16,
            Body: 14,
            Small: 12,
            Tiny: 10
        },

        // Spacing
        Spacing: {
            XS: 4,
            S: 8,
            M: 16,
            L: 24,
            XL: 32,
            XXL: 48
        },

        // Border Radius
        BorderRadius: {
            Small: 4,
            Medium: 8,
            Large: 12,
            XLarge: 16,
            Round: 999
        },

        // Shadows (simulated with semi-transparent borders)
        Shadows: {
            Light: RGBA(0, 0, 0, 0.05),
            Medium: RGBA(0, 0, 0, 0.1),
            Heavy: RGBA(0, 0, 0, 0.2)
        },

        // Animation Durations (in milliseconds)
        Animation: {
            Fast: 150,
            Normal: 300,
            Slow: 500
        }
    }
)
```

#### Step 5: Apply Theme to Your Leave Management System

1. **Open your `LeaveManagementSystem` app**

2. **Update App.OnStart** with theme object:

```powerFx
// ===================================
// THEME CONFIGURATION
// ===================================
Set(
    varTheme,
    {
        Colors: {
            Primary: RGBA(0, 120, 212, 1),
            PrimaryDark: RGBA(0, 90, 158, 1),
            PrimaryLight: RGBA(232, 244, 253, 1),
            Secondary: RGBA(16, 124, 16, 1),
            Success: RGBA(16, 124, 16, 1),
            Warning: RGBA(255, 185, 0, 1),
            Danger: RGBA(196, 49, 75, 1),
            Info: RGBA(0, 188, 242, 1),
            Background: RGBA(250, 250, 250, 1),
            Surface: RGBA(255, 255, 255, 1),
            Border: RGBA(225, 223, 221, 1),
            Shadow: RGBA(0, 0, 0, 0.1),
            TextPrimary: RGBA(50, 49, 48, 1),
            TextSecondary: RGBA(96, 94, 92, 1),
            TextDisabled: RGBA(200, 198, 196, 1),
            White: RGBA(255, 255, 255, 1),
            Black: RGBA(0, 0, 0, 1)
        },
        Typography: {
            FontFamily: Font.'Segoe UI',
            H1: 32,
            H2: 24,
            H3: 18,
            H4: 16,
            Body: 14,
            Small: 12
        },
        Spacing: {
            XS: 4,
            S: 8,
            M: 16,
            L: 24,
            XL: 32,
            XXL: 48
        },
        Radius: {
            Small: 4,
            Medium: 8,
            Large: 12
        }
    }
);

// Initialize sample data (if not exists)
If(
    IsEmpty(colLeaveRequests),
    ClearCollect(
        colLeaveRequests,
        {
            ID: 1,
            LeaveType: "Annual Leave",
            StartDate: Date(2025, 11, 15),
            EndDate: Date(2025, 11, 19),
            Days: 5,
            Status: "Pending",
            Reason: "Family vacation",
            SubmittedDate: Date(2025, 11, 10)
        },
        {
            ID: 2,
            LeaveType: "Sick Leave",
            StartDate: Date(2025, 11, 8),
            EndDate: Date(2025, 11, 8),
            Days: 1,
            Status: "Approved",
            Reason: "Medical appointment",
            SubmittedDate: Date(2025, 11, 5)
        }
    )
)
```

3. **Run OnStart:** App → ... → Run OnStart

#### Step 6: Update All Controls to Use Theme

**Before (hard-coded):**

```powerFx
Fill: RGBA(0, 120, 212, 1)
Size: 24
```

**After (theme-based):**

```powerFx
Fill: varTheme.Colors.Primary
Size: varTheme.Typography.H2
```

**Update Common Elements:**

**Headers:**

- Fill: `varTheme.Colors.Primary`

**Buttons:**

- Fill: `varTheme.Colors.Primary`
- HoverFill: `varTheme.Colors.PrimaryDark`
- Color: `varTheme.Colors.White`
- Size: `varTheme.Typography.Body`

**Cards:**

- Fill: `varTheme.Colors.Surface`
- BorderColor: `varTheme.Colors.Border`
- RadiusTopLeft: `varTheme.Radius.Medium`
- RadiusTopRight: `varTheme.Radius.Medium`
- RadiusBottomLeft: `varTheme.Radius.Medium`
- RadiusBottomRight: `varTheme.Radius.Medium`

**Labels:**

- Color: `varTheme.Colors.TextPrimary` (or TextSecondary)
- Size: `varTheme.Typography.Body`

**Spacing:**

- X: `varTheme.Spacing.XL`
- Margins: `varTheme.Spacing.M`

#### Step 7: Create Multiple Theme Options

**Add Theme Switcher:**

1. **Define Multiple Themes in OnStart:**

```powerFx
// Light Theme (Default)
Set(
    varLightTheme,
    {
        Colors: {
            Primary: RGBA(0, 120, 212, 1),
            Background: RGBA(250, 250, 250, 1),
            Surface: RGBA(255, 255, 255, 1),
            TextPrimary: RGBA(50, 49, 48, 1),
            // ... rest of colors
        },
        // ... typography, spacing, etc.
    }
);

// Dark Theme
Set(
    varDarkTheme,
    {
        Colors: {
            Primary: RGBA(48, 140, 216, 1),
            Background: RGBA(32, 31, 30, 1),
            Surface: RGBA(43, 42, 41, 1),
            TextPrimary: RGBA(255, 255, 255, 1),
            TextSecondary: RGBA(200, 198, 196, 1),
            Border: RGBA(70, 70, 70, 1),
            // ... rest
        },
        // ... same structure
    }
);

// Corporate Theme (example - different brand colors)
Set(
    varCorporateTheme,
    {
        Colors: {
            Primary: RGBA(138, 43, 226, 1),  // Purple
            PrimaryDark: RGBA(103, 32, 168, 1),
            // ... rest
        },
        // ... same structure
    }
);

// Set active theme to Light by default
Set(varTheme, varLightTheme);
Set(varCurrentThemeName, "Light");
```

2. **Create Theme Switcher Component:**

   Add to Settings screen or Header:

   **Dropdown for Theme Selection:**

   - `ddnThemeSelector`
   - Items: `["Light", "Dark", "Corporate"]`
   - Default: `varCurrentThemeName`
   - **OnChange:**

   ```powerFx
   Switch(
       Self.Selected.Value,
       "Light", Set(varTheme, varLightTheme),
       "Dark", Set(varTheme, varDarkTheme),
       "Corporate", Set(varTheme, varCorporateTheme)
   );
   Set(varCurrentThemeName, Self.Selected.Value)
   ```

#### Step 8: Create Reusable Style Functions

**For complex styling, create computed properties:**

**Button Style Record:**

```powerFx
// In App.OnStart or as a named formula
Set(
    varButtonStyles,
    {
        Primary: {
            Fill: varTheme.Colors.Primary,
            HoverFill: varTheme.Colors.PrimaryDark,
            Color: varTheme.Colors.White,
            BorderThickness: 0
        },
        Secondary: {
            Fill: varTheme.Colors.Surface,
            HoverFill: varTheme.Colors.PrimaryLight,
            Color: varTheme.Colors.Primary,
            BorderColor: varTheme.Colors.Primary,
            BorderThickness: 2
        },
        Danger: {
            Fill: varTheme.Colors.Danger,
            HoverFill: ColorFade(varTheme.Colors.Danger, -20%),
            Color: varTheme.Colors.White,
            BorderThickness: 0
        }
    }
)
```

**Apply to buttons:**

```powerFx
// Primary button
Fill: varButtonStyles.Primary.Fill
HoverFill: varButtonStyles.Primary.HoverFill
Color: varButtonStyles.Primary.Color

// Secondary button
Fill: varButtonStyles.Secondary.Fill
HoverFill: varButtonStyles.Secondary.HoverFill
Color: varButtonStyles.Secondary.Color
BorderColor: varButtonStyles.Secondary.BorderColor
BorderThickness: varButtonStyles.Secondary.BorderThickness
```

#### Step 9: Advanced Theme Features

**A. Status Color Helper:**

```powerFx
// Function to get color based on status
With(
    {status: ThisItem.Status},
    Switch(
        status,
        "Approved", varTheme.Colors.Success,
        "Pending", varTheme.Colors.Warning,
        "Rejected", varTheme.Colors.Danger,
        varTheme.Colors.TextSecondary
    )
)
```

**B. Responsive Spacing:**

```powerFx
// Adjust spacing based on screen size
Set(
    varResponsiveSpacing,
    If(
        App.Width < 768,  // Mobile
        {
            Container: 8,
            Card: 12,
            Section: 16
        },
        // Desktop
        {
            Container: 24,
            Card: 16,
            Section: 32
        }
    )
)
```

**C. Color Variations:**

```powerFx
// Lighten color
ColorFade(varTheme.Colors.Primary, 80%)  // 80% lighter

// Darken color
ColorFade(varTheme.Colors.Primary, -20%)  // 20% darker

// Transparent version
ColorFade(varTheme.Colors.Primary, 90%)  // 90% transparent
```

### Assignment Deliverable

✅ Import and study the reference msapp file with theme
✅ Implement comprehensive theme object in your Leave Management System
✅ Convert all hard-coded colors/sizes to theme references
✅ Create at least 2 theme variants (Light + one other)
✅ Implement theme switcher functionality
✅ Test app with different themes applied
✅ Document your theme structure

### Benefits of Theming

**Maintainability:**

- Change one value, updates everywhere
- No hunting through formulas

**Consistency:**

- Guaranteed uniform look
- Reduces human error

**Flexibility:**

- Easy to rebrand
- Support multiple themes (light/dark)
- Client customization

**Professional:**

- Shows advanced Power Apps knowledge
- Industry best practice

### Knowledge Check

- [ ] What is the purpose of using a theme object?
- [ ] How do you reference nested theme properties?
- [ ] What's the difference between Set and UpdateContext for themes?
- [ ] How would you create a button that adapts to the current theme?

### Resources

- [Microsoft Learn: Create app themes](https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/create-app-themes)
- [Neudesic Power Apps Best Practices](https://www.neudesic.com/)
- [Power Apps Color and Theme guide](https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/add-configure-controls)

---

## Module 7: Canvas App Settings (3 hours)

### Learning Objectives

- Navigate and understand all Canvas App settings
- Configure general app settings
- Enable and use experimental features
- Understand preview features vs. generally available features
- Configure app-level display and behavior settings

### Step-by-Step Instructions

#### Step 1: Accessing App Settings

1. **Open your `LeaveManagementSystem` app in edit mode**
2. Click **"Settings"** icon (⚙️) in top toolbar

   - OR: Click **"File"** → **"Settings"**

3. **Settings Categories:**
   - General
   - Display
   - Upcoming features
   - Retired
   - Support
   - Updates

#### Step 2: General Settings

**A. Name and Description:**

- **App name**: `LeaveManagementSystem`
- **Description**: Add meaningful description
  ```
  Enterprise leave management application for employees to request,
  track, and manage time-off requests with approval workflows.
  ```
- **Icon**: Upload custom icon (PNG, 200x200px recommended)
- **Background color**: Choose icon background

**B. App ID:**

- Read-only unique identifier
- Used in API calls and integrations
- Copy and save for documentation

**C. Owner:**

- Shows current app owner
- Can be changed by admins

#### Step 3: Display Settings

**A. Screen Size and Orientation:**

**Screen size:**

- **Tablet** (1366 x 768) - for desktop apps
- **Phone** (640 x 1136) - for mobile apps
- **Custom** - specify exact dimensions

**Orientation:**

- **Portrait** - vertical
- **Landscape** - horizontal
- **Auto** - adapts to device

**Scale to fit:**

- **On**: App scales to fill available space
- **Off**: Shows at designed size (may have borders)

**Lock aspect ratio:**

- Maintains width:height ratio when scaling

**Lock orientation:**

- Prevents rotation on mobile devices

**For Leave Management System:**

```
Screen size: Tablet (1366 x 768)
Orientation: Landscape
Scale to fit: On
Lock aspect ratio: Off
Lock orientation: Off
```

**B. Size and Position:**

**Show navigation bar:**

- Development-only feature
- Shows screen list at bottom for easy navigation
- Automatically hidden in published app

**Enable modern controls:**

- New control set with improved styling
- Better performance
- Modern design patterns

#### Step 4: Upcoming Features (Preview & Experimental)

**Understanding Feature States:**

1. **Generally Available (GA):**

   - Fully supported
   - Production-ready
   - Enabled by default

2. **Preview:**

   - Near-complete features
   - Mostly stable
   - May have minor issues
   - Safe for testing, use caution in production

3. **Experimental:**
   - Early-stage features
   - May change significantly
   - Not recommended for production
   - For testing and feedback only

**Important Preview/Experimental Features:**

**A. Formula-level error management:**

- **What**: Better error handling in formulas
- **Why**: Prevents app crashes, graceful degradation
- **Recommended**: Enable for preview
- **Status**: Generally Available in newer versions

```powerFx
// With error management
IfError(
    Patch(Employees, ...),
    Notify("Failed to save: " & FirstError.Message, NotificationType.Error)
)
```

**B. Enhanced data source experience:**

- **What**: Improved data source management
- **Why**: Better connection handling
- **Recommended**: Enable

**C. Explicit column selection:**

- **What**: Specify which columns to retrieve from data source
- **Why**: Improved performance, reduced delegation issues
- **Recommended**: Enable

```powerFx
// Without explicit columns
Filter(Employees, Department = "IT")

// With explicit columns (more efficient)
ShowColumns(
    Filter(Employees, Department = "IT"),
    "ID", "Name", "Email"
)
```

**D. Delayed load:**

- **What**: Screens load only when navigated to
- **Why**: Faster app startup
- **Recommended**: Enable for apps with many screens

**E. ParseJSON function:**

- **What**: Better JSON parsing capabilities
- **Why**: Easier to work with JSON data
- **Recommended**: Enable if working with APIs

```powerFx
Set(
    varData,
    ParseJSON('{"name": "John", "age": 30}')
);
// Access: varData.name
```

**F. Improve data source experience:**

- **What**: Better refresh and delegation warnings
- **Why**: Easier troubleshooting
- **Recommended**: Enable

**G. Dual screen app support:**

- **What**: Support for foldable devices
- **Why**: Better mobile experience
- **Recommended**: Enable if targeting mobile

**H. Retire screen templates:**

- Modern approach doesn't use templates
- Can be ignored

**I. Power Fx for components:**

- **What**: Use Power Fx in component properties
- **Why**: More flexible components
- **Recommended**: Enable

**J. New data table control:**

- **What**: Improved data table with better performance
- **Why**: Better UX for tabular data
- **Recommended**: Enable

#### Step 5: Configure Optimal Settings for Leave Management App

**Recommended Settings Configuration:**

1. **Open Settings**
2. **Go to "Upcoming features"**
3. **Enable these features:**

```
✓ Formula-level error management
✓ Enhanced data source experience
✓ Explicit column selection
✓ Delayed load
✓ ParseJSON function
✓ Improve data source experience
✓ Power Fx for components
✓ New data table control
✓ Classic control default value
✓ Retire screen templates (optional)
```

4. **Review and understand each:**
   - Click info (ℹ️) icon next to each feature
   - Read Microsoft's description
   - Understand impact on your app

#### Step 6: Advanced Settings

**A. App Object:**

- **Use non-global scope in formulas**: Better variable scoping
- **Enable app-wide formula bar**: Enhanced formula editing

**B. Delegation:**

- **Data row limit for non-delegable queries**: Default 500, max 2000
  - **Warning**: Higher limits = slower performance
  - **Recommendation**: Keep at 500, fix delegation issues instead

```powerFx
// Delegation warning - only first 500 records processed
Filter(
    LargeSharePointList,
    TextColumn Contains "search"  // Contains not delegable
)

// Better approach - delegable
Filter(
    LargeSharePointList,
    StartsWith(TextColumn, "search")  // StartsWith is delegable
)
```

**C. Authoring:**

- **Formula bar text size**: Adjust for readability
- **Show grid**: Helps with alignment

#### Step 7: Testing Features Exercise

**Create a test screen to explore features:**

1. **Add new screen**: `scrFeatureTests`

2. **Test Error Management:**

```powerFx
// Button: btnTestError
IfError(
    1/0,  // Division by zero error
    Notify("Error caught: " & FirstError.Message, NotificationType.Error)
)
```

3. **Test ParseJSON:**

```powerFx
// Button: btnTestJSON
Set(
    varJSONData,
    ParseJSON("{""employees"": [{""name"": ""John"", ""role"": ""Manager""}, {""name"": ""Sarah"", ""role"": ""Developer""}]}")
);
Notify("Parsed: " & First(varJSONData.employees).name, NotificationType.Success)
```

4. **Test Delayed Load:**
   - Create multiple screens
   - Measure app start time with/without delayed load
   - Use monitoring tool (Module 20)

#### Step 8: Document Your Settings

Create a settings documentation file for your app:

**Settings Documentation Template:**

```markdown
# Leave Management System - App Settings

## General

- App Name: LeaveManagementSystem
- Version: 1.0
- Owner: [Your name]
- Environment: Development

## Display

- Screen Size: Tablet (1366 x 768)
- Orientation: Landscape
- Scale to fit: Enabled

## Enabled Features

- Formula-level error management: Enabled
- Enhanced data source experience: Enabled
- Explicit column selection: Enabled
- Delayed load: Enabled
- ParseJSON: Enabled

## Performance Settings

- Data row limit: 500
- Delayed screen loading: Enabled

## Notes

- All experimental features tested on [date]
- No issues encountered with enabled features
```

### Assignment Deliverable

✅ Review all settings categories in Canvas Apps
✅ Read Microsoft documentation on each preview feature
✅ Enable recommended features for your Leave Management app
✅ Test at least 3 experimental features
✅ Create settings documentation file
✅ Identify which features improve your app performance
✅ Understand delegation and data row limits

### Common Settings by Use Case

**Performance-Critical Apps:**

```
✓ Delayed load
✓ Explicit column selection
✓ Data row limit: 500
```

**Data-Heavy Apps:**

```
✓ Enhanced data source experience
✓ Explicit column selection
✓ Improve data source experience
✓ Fix delegation issues (don't increase row limit)
```

**Mobile Apps:**

```
✓ Phone layout
✓ Lock orientation
✓ Dual screen support
✓ Scale to fit
```

**API Integration Apps:**

```
✓ ParseJSON
✓ Formula-level error management
✓ Enhanced data source experience
```

### Knowledge Check

- [ ] Where do you find app settings in Power Apps Studio?
- [ ] What's the difference between preview and experimental features?
- [ ] What is the default data row limit for non-delegable queries?
- [ ] Should you increase the data row limit to solve delegation warnings?
- [ ] What does "delayed load" do?
- [ ] What is formula-level error management?

### Settings Quick Reference

| Setting          | Location          | Recommendation                       |
| ---------------- | ----------------- | ------------------------------------ |
| Screen size      | Display           | Tablet for desktop, Phone for mobile |
| Scale to fit     | Display           | Enable for responsive design         |
| Data row limit   | Advanced          | Keep at 500, fix delegation          |
| Delayed load     | Upcoming features | Enable for multi-screen apps         |
| Error management | Upcoming features | Enable for production apps           |
| ParseJSON        | Upcoming features | Enable if using APIs                 |

### Resources

- [Microsoft Learn: Canvas app settings](https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/settings-app)
- [Microsoft Learn: Experimental and preview features](https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/working-with-experimental-preview)
- [Blog: Understanding Canvas App Settings](https://www.matthewdevaney.com/power-apps-settings-the-complete-guide/)
- [Microsoft Documentation: Data row limits and delegation](https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/delegation-overview)

---

## Stage 2 Summary

### What You've Accomplished

✅ Built professional UI for Leave Management System from Figma design
✅ Implemented centralized theming system with multiple theme support
✅ Configured optimal app settings for performance and features
✅ Understanding of experimental vs. preview features

### Total Time: 13 hours (of 23 hours in Stage 2)

### Modules Completed

- Module 5: UI Design (8 hours) ✓
- Module 6: App Theming (2 hours) ✓
- Module 7: Canvas App Settings (3 hours) ✓

### Remaining Modules in Stage 2

- Module 8: Patch (1 hour)
- Module 9: Collections (1 hour)
- Module 10: Gallery Control (2 hours)
- Module 11: Components (2 hours)
- Module 12: Modern Controls (8 hours)
- Module 13: Performance Optimization (2 hours)

### Self-Assessment Checklist

Before moving forward, ensure you can:

- [ ] Create a multi-screen app with consistent design
- [ ] Build cards, forms, and list layouts
- [ ] Implement and use a theme object
- [ ] Switch between themes dynamically
- [ ] Navigate and configure all app settings
- [ ] Enable experimental features appropriately
- [ ] Understand delegation and data limits

---

**This completes Stage 2 Part 1. The remaining modules (8-13) will be covered in the next section. Type "NEXT" to continue with Stage 2 Part 2: Data Operations and Performance.**
