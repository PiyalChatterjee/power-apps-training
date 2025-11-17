# Power Platform Training Manual - Stage 2 Part 3: Components, Modern Controls & Performance

## Overview

This section completes Stage 2, covering reusable components, modern control implementation, and performance optimization techniques. **Duration: 12 hours**

---

## Module 11: Components (2 hours)

### Learning Objectives

- Understand component architecture in Power Apps
- Create reusable navigation menu component
- Work with input and output properties
- Pass data between components and screens
- Implement component events and actions

### Step-by-Step Instructions

#### Step 1: Understanding Components

**What are Components?**

- Reusable UI elements with encapsulated logic
- Can be used multiple times across screens
- Have their own properties (inputs and outputs)
- Improve maintainability and consistency

**Benefits:**

- **Reusability**: Build once, use everywhere
- **Maintainability**: Update in one place
- **Consistency**: Same behavior across app
- **Organization**: Cleaner app structure

**Common Component Examples:**

- Navigation menus
- Headers/footers
- Custom buttons
- Forms
- Cards
- Loading spinners
- Modals/dialogs

#### Step 2: Create Practice App

1. Create new Canvas app: `ComponentsPracticeApp`
2. Format: Tablet
3. Rename Screen1 to `scrHome`

#### Step 3: Create Your First Component

**A. Create Component Library:**

1. **New Component:**

   - In left panel, click **"Components"** (puzzle icon)
   - Click **"+ New component"**
   - Rename to `cmpNavigationMenu`

2. **Set Component Size:**
   - Width: `250`
   - Height: `600`

**B. Design Navigation Menu:**

**Background:**

```powerFx
Name: recNavBackground
X: 0
Y: 0
Width: Parent.Width
Height: Parent.Height
Fill: RGBA(32, 31, 30, 1)  // Dark background
BorderThickness: 0
```

**Logo/Title Section:**

```powerFx
Name: recLogoSection
X: 0
Y: 0
Width: Parent.Width
Height: 80
Fill: RGBA(43, 42, 41, 1)  // Slightly lighter
BorderThickness: 0

Name: lblAppTitle
Text: "My App"
X: 16
Y: (recLogoSection.Height - Self.Height) / 2
Width: recLogoSection.Width - 32
Height: recLogoSection.Height
Color: RGBA(255, 255, 255, 1)
Size: 18
FontWeight: FontWeight.Semibold
VerticalAlign: VerticalAlign.Middle
```

**Navigation Items:**

Create a collection for menu items in component's custom property or hard-code:

```powerFx
Name: galNavItems
Items: [
    {ID: 1, Icon: Icon.Home, Label: "Home", Screen: "scrHome"},
    {ID: 2, Icon: Icon.People, Label: "Employees", Screen: "scrEmployees"},
    {ID: 3, Icon: Icon.Document, Label: "Reports", Screen: "scrReports"},
    {ID: 4, Icon: Icon.Settings, Label: "Settings", Screen: "scrSettings"}
]
X: 0
Y: recLogoSection.Height
Width: Parent.Width
Height: Parent.Height - recLogoSection.Height
TemplatePadding: 0
TemplateSize: 60
```

**Gallery Template Design:**

**Background (hover effect):**

```powerFx
Name: recNavItem
X: 0
Y: 0
Width: Parent.TemplateWidth
Height: Parent.TemplateSize
Fill: If(
    recNavItem.IsMouseOver ||
    ThisItem.Screen = varCurrentScreen,
    RGBA(50, 49, 48, 1),  // Hover/selected
    Color.Transparent
)
BorderThickness: 0
OnSelect: Set(varNavigateTo, ThisItem.Screen)
```

**Icon:**

```powerFx
Name: icoNavIcon
Icon: ThisItem.Icon
X: 24
Y: (Parent.TemplateHeight - Self.Height) / 2
Width: 24
Height: 24
Color: If(
    ThisItem.Screen = varCurrentScreen,
    RGBA(0, 120, 212, 1),  // Selected - blue
    RGBA(255, 255, 255, 1)  // Normal - white
)
```

**Label:**

```powerFx
Name: lblNavLabel
Text: ThisItem.Label
X: icoNavIcon.X + icoNavIcon.Width + 16
Y: (Parent.TemplateHeight - Self.Height) / 2
Width: Parent.TemplateWidth - Self.X - 16
Height: Parent.TemplateHeight
Color: If(
    ThisItem.Screen = varCurrentScreen,
    RGBA(255, 255, 255, 1),
    RGBA(200, 198, 196, 1)
)
Size: 14
FontWeight: If(
    ThisItem.Screen = varCurrentScreen,
    FontWeight.Semibold,
    FontWeight.Normal
)
VerticalAlign: VerticalAlign.Middle
```

**Active Indicator Bar:**

```powerFx
Name: recActiveIndicator
X: 0
Y: 0
Width: 4
Height: Parent.TemplateHeight
Fill: RGBA(0, 120, 212, 1)
Visible: ThisItem.Screen = varCurrentScreen
BorderThickness: 0
```

#### Step 4: Add Component Properties

**A. Create Input Properties (Parameters):**

1. Click on **"+ New custom property"** in component
2. **Property 1: Current Screen**

   - Display name: `CurrentScreen`
   - Name: `prmCurrentScreen`
   - Property type: **Input**
   - Data type: **Text**
   - Description: "The currently active screen name"
   - Default: `"scrHome"`

3. **Property 2: Theme Color**
   - Display name: `ThemeColor`
   - Name: `prmThemeColor`
   - Property type: **Input**
   - Data type: **Color**
   - Description: "Primary theme color for highlights"
   - Default: `RGBA(0, 120, 212, 1)`

**B. Create Output Properties:**

1. **Property: Selected Screen**

   - Display name: `SelectedScreen`
   - Name: `outSelectedScreen`
   - Property type: **Output**
   - Data type: **Text**
   - Description: "The screen selected by user"

2. **Set output value in gallery OnSelect:**

```powerFx
recNavItem.OnSelect:
    Set(cmpNavigationMenu.outSelectedScreen, ThisItem.Screen)
```

#### Step 5: Use Component in Screens

**A. Insert Component:**

1. Go to **Screens** (house icon in left panel)
2. Select `scrHome`
3. Click **"Insert"** → **"Custom"** → `cmpNavigationMenu`
4. Position at X: `0`, Y: `0`

**B. Set Component Properties:**

```powerFx
cmpNavigationMenu_1.prmCurrentScreen: varCurrentScreen
cmpNavigationMenu_1.prmThemeColor: RGBA(0, 120, 212, 1)
```

**C. Handle Navigation:**

Create a timer or use component's output:

Add a **Timer** control:

```powerFx
Name: tmrNavigation
Duration: 100
Repeat: true
AutoStart: true
Visible: false
OnTimerEnd:
    If(
        !IsBlank(cmpNavigationMenu_1.outSelectedScreen) &&
        cmpNavigationMenu_1.outSelectedScreen <> varCurrentScreen,

        // Navigate based on selection
        Switch(
            cmpNavigationMenu_1.outSelectedScreen,
            "scrHome", Navigate(scrHome, ScreenTransition.Fade),
            "scrEmployees", Navigate(scrEmployees, ScreenTransition.Fade),
            "scrReports", Navigate(scrReports, ScreenTransition.Fade),
            "scrSettings", Navigate(scrSettings, ScreenTransition.Fade)
        );

        // Update current screen
        Set(varCurrentScreen, cmpNavigationMenu_1.outSelectedScreen)
    )
```

**D. Set Current Screen on Navigation:**

Add to each screen's **OnVisible**:

```powerFx
scrHome.OnVisible: Set(varCurrentScreen, "scrHome")
scrEmployees.OnVisible: Set(varCurrentScreen, "scrEmployees")
```

#### Step 6: Create Custom Button Component

**A. Create New Component:**

1. Components → **"+ New component"**
2. Rename: `cmpCustomButton`
3. Size: Width `200`, Height `50`

**B. Design Button:**

```powerFx
Name: recButton
X: 0
Y: 0
Width: Parent.Width
Height: Parent.Height
Fill: If(
    recButton.Pressed,
    cmpCustomButton.prmPressedColor,
    If(
        recButton.IsMouseOver,
        cmpCustomButton.prmHoverColor,
        cmpCustomButton.prmFillColor
    )
)
BorderColor: cmpCustomButton.prmBorderColor
BorderThickness: cmpCustomButton.prmBorderThickness
RadiusTopLeft: cmpCustomButton.prmBorderRadius
RadiusTopRight: cmpCustomButton.prmBorderRadius
RadiusBottomLeft: cmpCustomButton.prmBorderRadius
RadiusBottomRight: cmpCustomButton.prmBorderRadius
OnSelect: Set(cmpCustomButton.outClicked, true)
```

```powerFx
Name: lblButtonText
Text: cmpCustomButton.prmText
X: 0
Y: 0
Width: Parent.Width
Height: Parent.Height
Color: cmpCustomButton.prmTextColor
Size: cmpCustomButton.prmFontSize
FontWeight: If(
    cmpCustomButton.prmBold,
    FontWeight.Semibold,
    FontWeight.Normal
)
Align: Align.Center
VerticalAlign: VerticalAlign.Middle
```

**C. Add Custom Properties:**

**Input Properties:**

- `prmText` (Text) - "Button"
- `prmFillColor` (Color) - Primary color
- `prmHoverColor` (Color) - Darker primary
- `prmPressedColor` (Color) - Even darker
- `prmTextColor` (Color) - White
- `prmBorderColor` (Color) - Transparent
- `prmBorderThickness` (Number) - 0
- `prmBorderRadius` (Number) - 4
- `prmFontSize` (Number) - 14
- `prmBold` (Boolean) - true
- `prmIcon` (Text) - Icon name (optional)

**Output Properties:**

- `outClicked` (Boolean) - false

**D. Use Custom Button:**

```powerFx
// Insert component
cmpCustomButton_1.prmText: "Submit"
cmpCustomButton_1.prmFillColor: RGBA(0, 120, 212, 1)
cmpCustomButton_1.prmHoverColor: RGBA(0, 90, 158, 1)

// Handle click with Timer
tmrButtonHandler.OnTimerEnd:
    If(
        cmpCustomButton_1.outClicked,

        // Your action here
        Notify("Button clicked!", NotificationType.Success);

        // Reset
        Set(cmpCustomButton_1.outClicked, false)
    )
```

#### Step 7: Create Loading Spinner Component

**A. Create Component:**

1. New component: `cmpLoadingSpinner`
2. Size: `100` x `100`

**B. Design Spinner:**

**Method 1: Animated Image**

```powerfx
Name: imgSpinner
Image: // Upload animated GIF or use SVG
X: 0
Y: 0
Width: Parent.Width
Height: Parent.Height
Visible: cmpLoadingSpinner.prmIsLoading
```

**Method 2: CSS Animation (using HTML control)**

```powerfx
Name: htmlSpinner
HtmlText:
    "<div style='width: 100%; height: 100%; display: flex; justify-content: center; align-items: center;'>
        <div style='border: 8px solid #f3f3f3; border-top: 8px solid #0078d4; border-radius: 50%; width: 60px; height: 60px; animation: spin 1s linear infinite;'></div>
        <style>
            @keyframes spin {
                0% { transform: rotate(0deg); }
                100% { transform: rotate(360deg); }
            }
        </style>
    </div>"
Visible: cmpLoadingSpinner.prmIsLoading
```

**Method 3: Simple Rotating Icon**

```powerfx
Name: icoSpinner
Icon: Icon.Sync
X: (Parent.Width - Self.Width) / 2
Y: (Parent.Height - Self.Height) / 2
Width: 40
Height: 40
Color: cmpLoadingSpinner.prmColor
Visible: cmpLoadingSpinner.prmIsLoading
```

**Loading Text:**

```powerfx
Name: lblLoadingText
Text: cmpLoadingSpinner.prmLoadingText
Y: icoSpinner.Y + icoSpinner.Height + 12
Width: Parent.Width
Height: 30
Color: cmpLoadingSpinner.prmColor
Size: 14
Align: Align.Center
Visible: cmpLoadingSpinner.prmIsLoading
```

**C. Add Properties:**

**Input:**

- `prmIsLoading` (Boolean) - false
- `prmLoadingText` (Text) - "Loading..."
- `prmColor` (Color) - Primary color

**D. Usage:**

```powerfx
// Show spinner while loading
Set(varIsLoading, true);

// Load data
ClearCollect(colEmployees, Employees);

// Hide spinner
Set(varIsLoading, false);

// Component property
cmpLoadingSpinner_1.prmIsLoading: varIsLoading
```

#### Step 8: Create Modal/Dialog Component

**A. Create Component:**

1. New component: `cmpModal`
2. Size: `Parent.Width` x `Parent.Height` (full screen overlay)

**B. Design Modal:**

**Overlay Background:**

```powerFx
Name: recOverlay
X: 0
Y: 0
Width: Parent.Width
Height: Parent.Height
Fill: RGBA(0, 0, 0, 0.5)  // Semi-transparent black
Visible: cmpModal.prmIsVisible
OnSelect: Set(cmpModal.outClosed, true)  // Click outside to close
```

**Modal Container:**

```powerfx
Name: recModalContainer
X: (Parent.Width - Self.Width) / 2
Y: (Parent.Height - Self.Height) / 2
Width: cmpModal.prmWidth
Height: cmpModal.prmHeight
Fill: RGBA(255, 255, 255, 1)
BorderThickness: 0
RadiusTopLeft: 8
RadiusTopRight: 8
RadiusBottomLeft: 8
RadiusBottomRight: 8
Visible: cmpModal.prmIsVisible
```

**Modal Header:**

```powerFx
Name: recModalHeader
X: 0
Y: 0
Width: recModalContainer.Width
Height: 60
Fill: RGBA(243, 242, 241, 1)
RadiusTopLeft: 8
RadiusTopRight: 8

Name: lblModalTitle
Text: cmpModal.prmTitle
X: 20
Y: (recModalHeader.Height - Self.Height) / 2
Width: recModalHeader.Width - 60
Height: recModalHeader.Height
Color: RGBA(50, 49, 48, 1)
Size: 18
FontWeight: FontWeight.Semibold
VerticalAlign: VerticalAlign.Middle
```

**Close Button:**

```powerFx
Name: icoClose
Icon: Icon.Cancel
X: recModalHeader.Width - Self.Width - 16
Y: (recModalHeader.Height - Self.Height) / 2
Width: 30
Height: 30
Color: RGBA(96, 94, 92, 1)
OnSelect: Set(cmpModal.outClosed, true)
```

**Modal Content Area:**

```powerFx
Name: recModalContent
X: 0
Y: recModalHeader.Height
Width: recModalContainer.Width
Height: recModalContainer.Height - recModalHeader.Height - recModalFooter.Height
Fill: RGBA(255, 255, 255, 1)

Name: lblModalContent
Text: cmpModal.prmContent
X: 20
Y: recModalContent.Y + 20
Width: recModalContent.Width - 40
Height: recModalContent.Height - 40
Color: RGBA(50, 49, 48, 1)
Size: 14
```

**Modal Footer:**

```powerFx
Name: recModalFooter
X: 0
Y: recModalContainer.Height - Self.Height
Width: recModalContainer.Width
Height: 70
Fill: RGBA(243, 242, 241, 1)
RadiusBottomLeft: 8
RadiusBottomRight: 8
```

**Action Buttons:**

```powerFx
// Primary action button
Name: btnModalPrimary
Text: cmpModal.prmPrimaryButtonText
X: recModalFooter.Width - Self.Width - 20
Y: (recModalFooter.Height - Self.Height) / 2
Width: 120
Height: 40
Fill: RGBA(0, 120, 212, 1)
Color: RGBA(255, 255, 255, 1)
Visible: !IsBlank(cmpModal.prmPrimaryButtonText)
OnSelect: Set(cmpModal.outPrimaryClicked, true)

// Secondary action button
Name: btnModalSecondary
Text: cmpModal.prmSecondaryButtonText
X: btnModalPrimary.X - Self.Width - 12
Y: btnModalPrimary.Y
Width: 120
Height: 40
Fill: RGBA(255, 255, 255, 1)
BorderColor: RGBA(200, 198, 196, 1)
BorderThickness: 1
Color: RGBA(50, 49, 48, 1)
Visible: !IsBlank(cmpModal.prmSecondaryButtonText)
OnSelect: Set(cmpModal.outSecondaryClicked, true)
```

**C. Add Properties:**

**Input:**

- `prmIsVisible` (Boolean) - false
- `prmTitle` (Text) - "Modal Title"
- `prmContent` (Text) - "Modal content"
- `prmWidth` (Number) - 600
- `prmHeight` (Number) - 400
- `prmPrimaryButtonText` (Text) - "Confirm"
- `prmSecondaryButtonText` (Text) - "Cancel"

**Output:**

- `outClosed` (Boolean) - false
- `outPrimaryClicked` (Boolean) - false
- `outSecondaryClicked` (Boolean) - false

**D. Usage:**

```powerFx
// Show modal
Set(varShowModal, true);

// Component properties
cmpModal_1.prmIsVisible: varShowModal
cmpModal_1.prmTitle: "Delete Confirmation"
cmpModal_1.prmContent: "Are you sure you want to delete this item?"
cmpModal_1.prmPrimaryButtonText: "Delete"
cmpModal_1.prmSecondaryButtonText: "Cancel"

// Handle actions with Timer
tmrModalHandler.OnTimerEnd:
    If(
        cmpModal_1.outClosed || cmpModal_1.outSecondaryClicked,
        // Close modal
        Set(varShowModal, false);
        Reset(cmpModal_1),

        cmpModal_1.outPrimaryClicked,
        // Perform delete
        Remove(colItems, varSelectedItem);
        Notify("Item deleted", NotificationType.Success);
        Set(varShowModal, false);
        Reset(cmpModal_1)
    )
```

#### Step 9: Component Best Practices

**✅ DO:**

- Use descriptive property names with `prm` and `out` prefixes
- Provide default values for all input properties
- Keep components focused on single responsibility
- Document what each property does
- Use consistent naming across components
- Reset output properties after handling

**❌ DON'T:**

- Make components too complex (split into smaller components)
- Hardcode values that should be properties
- Use context variables inside components (use properties)
- Create circular dependencies between components
- Forget to handle edge cases

**Component Lifecycle:**

```powerFx
// Component receives input properties
// Component renders with those properties
// User interacts with component
// Component sets output properties
// Parent screen/app reads output properties
// Parent screen handles the output (with Timer pattern)
// Parent screen resets component if needed
```

#### Step 10: Advanced Component Patterns

**A. Component Library (Export/Import):**

1. **Create separate app as component library**
2. Build all reusable components there
3. **File** → **Save** → **Publish**
4. In other apps: **Insert** → **Get more components**
5. Import from your library app

**B. Passing Collections to Components:**

```powerFx
// Input property: prmItems (Table)

// Component usage
cmpDataGrid_1.prmItems: colEmployees

// Inside component
galData.Items: cmpDataGrid.prmItems
```

**C. Event-Based Communication:**

```powerFx
// Instead of checking outputs constantly, use event pattern

// Component sets event flag
OnSelect: Set(cmpButton.outOnClick, Text(Now()))

// Parent watches for changes
If(
    cmpButton.outOnClick <> varLastClick,
    // Handle click
    YourAction();
    Set(varLastClick, cmpButton.outOnClick)
)
```

### Assignment Deliverable

✅ Create reusable navigation menu component with:

- At least 4 menu items
- Active state highlighting
- Input property for current screen
- Output property for selected screen
- Hover effects
- Icons for each menu item

✅ Create at least 2 additional components:

- Custom button with full theming support
- Loading spinner or modal dialog

✅ Demonstrate components used across multiple screens

### Knowledge Check

- [ ] What are the benefits of using components?
- [ ] What's the difference between input and output properties?
- [ ] How do you pass data from a component to its parent screen?
- [ ] Why use the Timer pattern for handling component outputs?
- [ ] Can you use context variables inside components?

### Component Patterns Quick Reference

| Pattern             | Use Case              | Example                    |
| ------------------- | --------------------- | -------------------------- |
| **Navigation Menu** | Consistent navigation | Side nav, bottom nav       |
| **Custom Button**   | Themed buttons        | Primary, secondary, danger |
| **Modal/Dialog**    | Confirmations         | Delete confirm, info popup |
| **Loading Spinner** | Async operations      | Data loading indicator     |
| **Form Controls**   | Reusable inputs       | Labeled text input         |
| **Cards**           | Content display       | Product card, user card    |
| **Header/Footer**   | Page structure        | Consistent branding        |

### Resources

- [Microsoft Learn: Create components](https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/create-component)
- [Microsoft Learn: Component properties](https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/component-properties)
- [Video: Building Components](https://www.youtube.com/watch?v=example)
- [Component Library Best Practices](https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/component-library)

---

## Module 12: Modern Controls (8 hours)

### Learning Objectives

- Understand the difference between classic and modern controls
- Implement modern controls in canvas apps
- Recreate interfaces using modern control set
- Leverage modern control features and styling
- Build responsive layouts with modern controls

### Step-by-Step Instructions

#### Step 1: Understanding Modern Controls

**What are Modern Controls?**

- New generation of controls introduced in 2023
- Based on Fluent 2 design system
- Better performance and accessibility
- Improved mobile experience
- More styling options
- Better integration with Dataverse

**Key Differences:**

| Aspect            | Classic Controls  | Modern Controls    |
| ----------------- | ----------------- | ------------------ |
| **Design**        | Older UI patterns | Fluent 2 design    |
| **Performance**   | Good              | Better             |
| **Accessibility** | Basic             | Enhanced           |
| **Styling**       | Limited           | More flexible      |
| **Mobile**        | Responsive        | Optimized          |
| **Future**        | Maintenance mode  | Active development |

**Available Modern Controls:**

- Text input
- Button
- Checkbox
- Dropdown
- Combo box
- Date picker
- Slider
- Toggle
- Label
- Image
- Container
- Header
- Card

#### Step 2: Enable Modern Controls

1. **Open your app** (or create new: `ModernControlsApp`)
2. **Settings** → **Display**
3. Enable **"Modern controls and themes"**
4. Click **"Save"**
5. Close and reopen the app

#### Step 3: Insert Modern Controls

**A. Modern Text Input:**

1. Insert → **"Modern controls"** → **"Text"**
2. Rename: `txtModernInput`

**Properties:**

```powerFx
X: 40
Y: 100
Width: 400
Height: 40

// Unique to modern controls
Label: "First Name"
Hint: "Enter your first name"
Required: true
ErrorMessage: "This field is required"
```

**Key Properties:**

- **Label**: Built-in label above input
- **Hint**: Placeholder text
- **Required**: Shows asterisk and validation
- **ErrorMessage**: Custom validation message
- **Value**: Text value (like classic .Text)

**B. Modern Button:**

```powerFx
Insert → Modern Button
Name: btnModernPrimary

Text: "Submit"
Appearance: "Primary"  // Primary, Secondary, Subtle
X: 40
Y: 160
Width: 150
Height: 44

OnSelect: Notify("Modern button clicked!", NotificationType.Success)
```

**Button Appearances:**

- **Primary**: Filled with primary color
- **Secondary**: Outlined
- **Subtle**: Transparent background

**C. Modern Dropdown:**

```powerFx
Insert → Modern Dropdown
Name: ddnModernDepartment

Label: "Department"
Items: ["Engineering", "Sales", "Marketing", "HR"]
Required: true
X: 40
Y: 220
Width: 400
Height: 40
```

**D. Modern Checkbox:**

```powerFx
Insert → Modern Checkbox
Name: chkModernAgree

Text: "I agree to the terms and conditions"
X: 40
Y: 280
Width: 400
Height: 40
```

**E. Modern Date Picker:**

```powerFx
Insert → Modern Date Picker
Name: dtpModernStart

Label: "Start Date"
Required: true
X: 40
Y: 340
Width: 400
Height: 40
```

**F. Modern Toggle:**

```powerFx
Insert → Modern Toggle
Name: tglModernNotify

Label: "Email notifications"
X: 40
Y: 400
Width: 400
Height: 40
```

#### Step 4: Modern Control Styling

**A. Theme Integration:**

Modern controls automatically pick up app theme:

```powerFx
// In App.OnStart
Set(
    varModernTheme,
    {
        PrimaryColor: RGBA(0, 120, 212, 1),
        TextColor: RGBA(50, 49, 48, 1),
        BackgroundColor: RGBA(255, 255, 255, 1)
    }
)
```

Modern controls use these theme colors automatically.

**B. Custom Styling:**

**Modern Button:**

```powerFx
// Style property (JSON-like)
btnModern.Style:
    {
        BackgroundColor: RGBA(138, 43, 226, 1),
        BorderRadius: 8,
        FontSize: 16,
        FontWeight: "semibold"
    }
```

**Modern Text Input:**

```powerfx
txtModern.Style:
    {
        BorderColor: RGBA(0, 120, 212, 1),
        BorderRadius: 4,
        FontSize: 14
    }
```

#### Step 5: Build Modern Form

**Create complete form with modern controls:**

**A. Form Container:**

```powerFx
Insert → Modern Container
Name: conModernForm
X: (Parent.Width - 600) / 2
Y: 100
Width: 600
Height: 700
```

**B. Form Title:**

```powerFx
Insert → Modern Header
Name: hdrFormTitle
Text: "Employee Registration"
X: 0
Y: 0
Width: Parent.Width
```

**C. Add Form Fields:**

1. **Personal Information Section:**

```powerFx
// Section label
lblPersonalInfo.Text: "Personal Information"
lblPersonalInfo.Size: 18
lblPersonalInfo.FontWeight: FontWeight.Semibold

// First Name
txtFirstName (Modern Text Input)
Label: "First Name *"
Hint: "Enter first name"
Required: true

// Last Name
txtLastName (Modern Text Input)
Label: "Last Name *"
Required: true

// Email
txtEmail (Modern Text Input)
Label: "Email Address *"
Hint: "user@example.com"
Required: true
Format: TextFormat.Email
```

2. **Work Information:**

```powerFx
// Department
ddnDepartment (Modern Dropdown)
Label: "Department *"
Items: ["Engineering", "Sales", "Marketing", "HR", "Finance"]
Required: true

// Job Title
txtJobTitle (Modern Text Input)
Label: "Job Title *"
Required: true

// Start Date
dtpStartDate (Modern Date Picker)
Label: "Start Date *"
Required: true
DefaultDate: Today()
```

3. **Employment Details:**

```powerFx
// Employment Type
ddnEmploymentType (Modern Dropdown)
Label: "Employment Type *"
Items: ["Full-time", "Part-time", "Contract", "Intern"]
Required: true
DefaultSelectedItems: ["Full-time"]

// Remote Work
tglRemoteWork (Modern Toggle)
Label: "Remote work eligible"
Default: false
```

4. **Preferences:**

```powerFx
// Newsletter
chkNewsletter (Modern Checkbox)
Text: "Subscribe to company newsletter"
Default: true

// Notifications
chkNotifications (Modern Checkbox)
Text: "Enable email notifications"
Default: true
```

**D. Action Buttons:**

```powerFx
// Submit Button
btnModernSubmit (Modern Button)
Text: "Submit Registration"
Appearance: "Primary"
Width: 200
Height: 44
OnSelect:
    If(
        // Validation
        IsBlank(txtFirstName.Value),
        Notify("First name is required", NotificationType.Error),

        IsBlank(txtLastName.Value),
        Notify("Last name is required", NotificationType.Error),

        IsBlank(txtEmail.Value) || !IsMatch(txtEmail.Value, Match.Email),
        Notify("Valid email is required", NotificationType.Error),

        IsBlank(ddnDepartment.Selected),
        Notify("Department is required", NotificationType.Error),

        // All valid - submit
        Patch(
            Employees,
            Defaults(Employees),
            {
                FirstName: txtFirstName.Value,
                LastName: txtLastName.Value,
                Email: txtEmail.Value,
                Department: ddnDepartment.Selected.Value,
                JobTitle: txtJobTitle.Value,
                StartDate: dtpStartDate.SelectedDate,
                EmploymentType: ddnEmploymentType.Selected.Value,
                IsRemote: tglRemoteWork.Value,
                NewsletterSubscribed: chkNewsletter.Value,
                NotificationsEnabled: chkNotifications.Value
            }
        );
        Notify("Employee registered successfully!", NotificationType.Success);
        // Reset form
        Reset(txtFirstName);
        Reset(txtLastName);
        Reset(txtEmail);
        Reset(ddnDepartment);
        Reset(txtJobTitle);
        Reset(dtpStartDate);
        Reset(ddnEmploymentType);
        Reset(tglRemoteWork);
        Reset(chkNewsletter);
        Reset(chkNotifications)
    )

// Cancel Button
btnModernCancel (Modern Button)
Text: "Cancel"
Appearance: "Secondary"
Width: 150
Height: 44
OnSelect: Back()
```

#### Step 6: Modern Cards and Containers

**A. Modern Card:**

```powerFx
Insert → Modern Card
Name: cardEmployee

// Card properties
X: 40
Y: 100
Width: 350
Height: 200
Padding: 16
```

**Inside Card:**

```powerFx
// Employee image
imgEmployee (Modern Image)
Source: "user-avatar.png"
Width: 80
Height: 80
BorderRadius: 40  // Circular

// Name
lblEmployeeName (Modern Label)
Text: "John Smith"
Size: 18
FontWeight: FontWeight.Semibold

// Title
lblEmployeeTitle
Text: "Senior Developer"
Size: 14
Color: RGBA(96, 94, 92, 1)

// Department
lblEmployeeDept
Text: "Engineering"
Size: 12

// Action buttons
btnViewProfile (Modern Button)
Text: "View Profile"
Appearance: "Secondary"
```

**B. Modern Container:**

```powerFx
Insert → Modern Container
Name: conEmployeeList

// Container provides structured layout
Padding: 16
Gap: 12  // Space between child elements
Direction: "Vertical"  // or "Horizontal"
```

#### Step 7: Build Dashboard with Modern Controls

**Create modern dashboard layout:**

**A. Page Header:**

```powerFx
// Modern Header
hdrDashboard
Text: "Employee Dashboard"
Subtitle: "Overview of team activities"
```

**B. Statistics Cards Row:**

Create 3 cards in a row using modern containers:

```powerFx
// Container for cards
conStatsRow (Modern Container)
Direction: "Horizontal"
Gap: 16
Wrap: true

// Inside container - Card 1
cardTotalEmployees (Modern Card)
Width: 300
Height: 150
```

**Card 1 Content:**

```powerFx
icoEmployees
Icon: Icon.People
Size: 40
Color: RGBA(0, 120, 212, 1)

lblEmployeeCount
Text: CountRows(Employees)
Size: 36
FontWeight: FontWeight.Bold

lblEmployeeLabel
Text: "Total Employees"
Size: 14
Color: RGBA(96, 94, 92, 1)
```

**Card 2: Active Projects**
**Card 3: Pending Tasks**

**C. Data Grid with Modern Controls:**

Unfortunately, modern data table is still in development. Use gallery with modern cards:

```powerFx
galModernEmployees (Gallery - Vertical)
Items: Employees
TemplatePadding: 8
TemplateSize: 120

// Inside gallery template
cardEmployeeRow (Modern Card)
Width: Parent.TemplateWidth
Height: Parent.TemplateHeight - 8

// Content inside card
imgAvatar (Modern Image)
Source: ThisItem.ProfilePicture
Width: 60
Height: 60
BorderRadius: 30

lblName (Modern Label)
Text: ThisItem.FirstName & " " & ThisItem.LastName
Size: 16
FontWeight: FontWeight.Semibold

lblPosition (Modern Label)
Text: ThisItem.JobTitle & " - " & ThisItem.Department
Size: 14
Color: RGBA(96, 94, 92, 1)

btnModernView (Modern Button)
Text: "View"
Appearance: "Subtle"
OnSelect: Navigate(scrEmployeeDetail, ScreenTransition.Cover)
```

#### Step 8: Responsive Layout with Modern Controls

**Modern controls + responsive containers:**

```powerFx
// Main container adapts to screen size
conResponsive (Modern Container)
Width: Parent.Width
Height: Parent.Height
Padding: If(Parent.Width < 768, 16, 32)
Gap: If(Parent.Width < 768, 12, 24)
Direction: If(Parent.Width < 768, "Vertical", "Horizontal")

// Cards adjust width based on container
cardResponsive.Width:
    If(
        conResponsive.Direction = "Vertical",
        Parent.Width - 32,  // Full width on mobile
        (Parent.Width - 48) / 2  // Half width on desktop
    )
```

#### Step 9: Modern Control Patterns

**A. Search Bar:**

```powerFx
conSearchBar (Modern Container)
Direction: "Horizontal"
Gap: 8

txtModernSearch (Modern Text Input)
Label: ""
Hint: "Search employees..."
Width: 400

btnSearch (Modern Button)
Text: "Search"
Appearance: "Primary"
Icon: Icon.Search

btnClearSearch (Modern Button)
Text: "Clear"
Appearance: "Subtle"
OnSelect: Reset(txtModernSearch)
```

**B. Filter Panel:**

```powerFx
conFilters (Modern Container)
Direction: "Vertical"
Gap: 16
Padding: 16

hdrFilters (Modern Header)
Text: "Filters"
Size: 18

ddnDeptFilter (Modern Dropdown)
Label: "Department"
Items: Distinct(Employees, Department)

ddnStatusFilter (Modern Dropdown)
Label: "Status"
Items: ["Active", "Inactive", "On Leave"]

btnApplyFilters (Modern Button)
Text: "Apply Filters"
Appearance: "Primary"

btnResetFilters (Modern Button)
Text: "Reset"
Appearance: "Secondary"
```

**C. Progress Indicator:**

```powerFx
conProgress (Modern Container)
Direction: "Vertical"
Gap: 8

lblProgress (Modern Label)
Text: "Profile Completion: " & Round(varCompletionPercent, 0) & "%"

// Modern slider shows progress
sldProgress (Modern Slider)
Value: varCompletionPercent
Min: 0
Max: 100
Enabled: false  // Read-only
```

**D. Status Badge Pattern:**

```powerFx
// Use modern button with subtle appearance as badge
btnStatusBadge (Modern Button)
Text: ThisItem.Status
Appearance: "Subtle"
Style: {
    BackgroundColor: Switch(
        ThisItem.Status,
        "Active", ColorFade(RGBA(16, 124, 16, 1), 90%),
        "Pending", ColorFade(RGBA(255, 185, 0, 1), 90%),
        "Inactive", ColorFade(RGBA(150, 150, 150, 1), 90%)
    ),
    Color: Switch(
        ThisItem.Status,
        "Active", RGBA(16, 124, 16, 1),
        "Pending", RGBA(255, 185, 0, 1),
        "Inactive", RGBA(96, 94, 92, 1)
    ),
    BorderRadius: 12,
    Padding: 8
}
Enabled: false  // Not clickable
```

#### Step 10: Migration from Classic to Modern

**Steps to migrate existing app:**

1. **Enable modern controls** in settings
2. **Identify classic controls** to replace
3. **Replace one screen at a time:**

**Classic Input:**

```powerFx
txtClassic
Text: varValue
HintText: "Enter name"
```

**Modern Input:**

```powerFx
txtModern
Value: varValue
Label: "Name"
Hint: "Enter name"
```

4. **Update formulas:**

   - Replace `.Text` with `.Value`
   - Replace `.Selected.Value` with `.Selected` (for dropdowns)
   - Update OnSelect handlers

5. **Test thoroughly** - behavior may differ slightly

**Property Mapping:**

| Classic           | Modern         | Notes          |
| ----------------- | -------------- | -------------- |
| `.Text`           | `.Value`       | Text inputs    |
| `.Selected.Value` | `.Selected`    | Dropdowns      |
| `HintText`        | `Hint`         | Placeholder    |
| N/A               | `Label`        | Built-in label |
| N/A               | `Required`     | Validation     |
| N/A               | `ErrorMessage` | Custom error   |

### Assignment Deliverable

✅ **Recreate interfaces using modern controls:**

- Complete registration form with all input types
- Dashboard with modern cards and containers
- Employee list with modern gallery
- Responsive layout that adapts to screen size
- Search and filter functionality with modern controls

✅ **Demonstrate modern control features:**

- Built-in labels and validation
- Different button appearances
- Container layouts with gap/padding
- Responsive behavior
- Modern styling options

✅ **Create at least 3 complete screens** using only modern controls

### Knowledge Check

- [ ] What are the main benefits of modern controls?
- [ ] How do you enable modern controls in an app?
- [ ] What's the property for getting text from modern input?
- [ ] What button appearances are available?
- [ ] How do modern containers help with layout?
- [ ] What's the difference between classic and modern dropdowns?

### Modern Controls Quick Reference

| Control         | Key Properties               | Use Case         |
| --------------- | ---------------------------- | ---------------- |
| **Text Input**  | Value, Label, Hint, Required | Form inputs      |
| **Button**      | Text, Appearance, OnSelect   | Actions          |
| **Dropdown**    | Items, Selected, Label       | Single selection |
| **Checkbox**    | Value, Text                  | Boolean choice   |
| **Toggle**      | Value, Label                 | On/off setting   |
| **Date Picker** | SelectedDate, Label          | Date selection   |
| **Card**        | Content, Padding             | Content grouping |
| **Container**   | Direction, Gap, Wrap         | Layout structure |
| **Header**      | Text, Subtitle               | Section titles   |

### Resources

- [Microsoft Learn: Modern controls overview](https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/controls/modern-controls/overview-modern-controls)
- [Microsoft Learn: Modern theming](https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/controls/modern-controls/modern-theming)
- [Youtube Playlist: Modern Controls](https://www.youtube.com/playlist?list=PLExample)
- [Migration Guide: Classic to Modern](https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/controls/modern-controls/migrate-to-modern)

---

## Module 13: Performance Optimization (2 hours)

### Learning Objectives

- Identify common performance bottlenecks
- Implement coding practices for optimal performance
- Understand delegation and how to avoid issues
- Optimize data loading and caching
- Create performance benchmarks and comparisons

### Step-by-Step Instructions

#### Step 1: Understanding Performance Factors

**What Affects Performance:**

1. **Data Operations:**

   - Large data loads
   - Non-delegable queries
   - Frequent API calls
   - Complex calculations

2. **UI Rendering:**

   - Too many controls on screen
   - Heavy images
   - Complex galleries
   - Animations

3. **Formulas:**

   - Inefficient formulas
   - Repeated calculations
   - Nested ForAll loops
   - Volatile functions (Today(), Now(), User())

4. **Network:**
   - Slow data sources
   - Multiple simultaneous calls
   - Large payloads

#### Step 2: Create Performance Testing App

1. Create new app: `PerformanceTestApp`
2. Create large sample dataset:

```powerFx
// Button to create 1000 records
btnCreateLargeDataset.OnSelect:
    Set(varStartTime, Now());

    ClearCollect(
        colLargeDataset,
        ForAll(
            Sequence(1000),
            {
                ID: Value,
                Name: "Employee " & Value,
                Department: Switch(
                    Mod(Value, 5),
                    0, "Engineering",
                    1, "Sales",
                    2, "Marketing",
                    3, "HR",
                    "Finance"
                ),
                Salary: 50000 + Mod(Value, 50) * 1000,
                HireDate: DateAdd(Date(2020, 1, 1), Value, Days),
                IsActive: Mod(Value, 10) <> 0,
                Score: Mod(Value, 100)
            }
        )
    );

    Set(varCreationTime, DateDiff(varStartTime, Now(), Milliseconds));
    Notify("Created in " & varCreationTime & "ms", NotificationType.Success)
```

#### Step 3: Optimize Data Loading

**❌ BAD - Load all data on screen load:**

```powerFx
scrEmployees.OnVisible:
    ClearCollect(colEmployees, Employees)  // Loads all records
```

**✅ GOOD - Load only what's needed:**

```powerFx
scrEmployees.OnVisible:
    ClearCollect(
        colEmployees,
        FirstN(
            Sort(Employees, CreatedDate, Descending),
            100  // Load only recent 100
        )
    )
```

**✅ BETTER - Load on demand:**

```powerfx
// Load initially
scrEmployees.OnVisible:
    If(
        IsEmpty(colEmployees),
        ClearCollect(
            colEmployees,
            FirstN(Employees, 50)
        )
    )

// Load more button
btnLoadMore.OnSelect:
    Collect(
        colEmployees,
        FirstN(
            Filter(
                Employees,
                Not(ID in colEmployees.ID)
            ),
            50
        )
    )
```

#### Step 4: Optimize Gallery Performance

**❌ BAD - Complex formulas in gallery template:**

```powerFx
lblEmployeeName.Text:
    LookUp(
        Employees,
        ID = ThisItem.EmployeeID
    ).FirstName & " " &
    LookUp(
        Employees,
        ID = ThisItem.EmployeeID
    ).LastName & " (" &
    LookUp(
        Departments,
        ID = ThisItem.DepartmentID
    ).Name & ")"
// Multiple lookups = slow!
```

**✅ GOOD - Pre-calculate in collection:**

```powerFx
// In OnVisible or data load
ClearCollect(
    colEmployeesEnriched,
    AddColumns(
        Employees,
        "FullName", FirstName & " " & LastName,
        "DepartmentName", LookUp(Departments, ID = DepartmentID).Name
    )
);

// In gallery
lblEmployeeName.Text: ThisItem.FullName & " (" & ThisItem.DepartmentName & ")"
// Much faster!
```

**❌ BAD - Volatile functions in gallery:**

```powerFx
lblDaysAgo.Text:
    DateDiff(ThisItem.CreatedDate, Now(), Days) & " days ago"
// Now() recalculates constantly!
```

**✅ GOOD - Calculate once:**

```powerFx
// Set once on screen visible
scrEmployees.OnVisible:
    Set(varCurrentTime, Now())

// In gallery
lblDaysAgo.Text:
    DateDiff(ThisItem.CreatedDate, varCurrentTime, Days) & " days ago"
```

#### Step 5: Optimize Formula Patterns

**A. Use With() to avoid repeated calculations:**

**❌ BAD:**

```powerFx
If(
    CountRows(Filter(colEmployees, Department = "IT")) > 10,
    "Large IT team: " & CountRows(Filter(colEmployees, Department = "IT")),
    "Small IT team: " & CountRows(Filter(colEmployees, Department = "IT"))
)
// Filters 3 times!
```

**✅ GOOD:**

```powerFx
With(
    {itCount: CountRows(Filter(colEmployees, Department = "IT"))},
    If(
        itCount > 10,
        "Large IT team: " & itCount,
        "Small IT team: " & itCount
    )
)
// Filters once!
```

**B. Concurrent() for independent operations:**

**❌ BAD - Sequential:**

```powerFx
ClearCollect(colEmployees, Employees);
ClearCollect(colDepartments, Departments);
ClearCollect(colProjects, Projects);
// Waits for each to complete
```

**✅ GOOD - Concurrent:**

```powerFx
Concurrent(
    ClearCollect(colEmployees, Employees),
    ClearCollect(colDepartments, Departments),
    ClearCollect(colProjects, Projects)
);
// Loads all at once!
```

**C. Cache reference data:**

**❌ BAD - Load repeatedly:**

```powerfx
// Every screen loads
scrHome.OnVisible: ClearCollect(colDepartments, Departments)
scrReports.OnVisible: ClearCollect(colDepartments, Departments)
scrSettings.OnVisible: ClearCollect(colDepartments, Departments)
```

**✅ GOOD - Load once in App.OnStart:**

```powerFx
App.OnStart:
    // Load reference data once
    ClearCollect(colDepartments, Departments);
    ClearCollect(colJobTitles, JobTitles);
    ClearCollect(colStatuses, Statuses);

    Set(varAppLoaded, true)

// Screens just use cached data
ddnDepartment.Items: colDepartments
```

#### Step 6: Understanding and Fixing Delegation

**What is Delegation?**

- Power Apps sends query to data source
- Data source filters/sorts on server side
- Only results sent to app
- Efficient for large datasets

**Non-Delegable = Bad for large data:**

- Only first 500-2000 records processed
- Rest ignored silently
- Yellow warning triangle in formula bar

**Delegable Functions (SharePoint/Dataverse):**

- Filter: `=, <>, <, <=, >, >=, And, Or, In, StartsWith`
- Sort, SortByColumns
- LookUp, Search (limited)
- Sum, Count, Average, Min, Max (on delegable queries)

**Non-Delegable:**

- Contains, EndsWith
- CountRows (use CountIf)
- Complex calculations in Filter
- Filter on choice/lookup columns (sometimes)

**❌ BAD - Non-delegable:**

```powerFx
Filter(
    Employees,
    Name Contains txtSearch.Text  // Contains not delegable
)
// ⚠️ Yellow warning
```

**✅ GOOD - Delegable alternative:**

```powerFx
Filter(
    Employees,
    StartsWith(Name, txtSearch.Text)  // StartsWith IS delegable
)
// OR use Search function
Search(Employees, txtSearch.Text, "Name")
```

**Check Delegation:**

1. Look for yellow ⚠️ triangle in formula
2. Hover to see delegation warning
3. Review formula and fix

**Delegation Testing:**

```powerfx
// Test with > 500 records
// Change data row limit to 10 temporarily to test
// Settings → Advanced → Data row limit → 10
// If you see only 10 results with 1000 records = delegation issue
```

#### Step 7: Image Optimization

**❌ BAD - Large images:**

```powerfx
imgEmployee.Image: ThisItem.FullSizePhoto  // 5MB image
// Loads slowly, uses lots of memory
```

**✅ GOOD - Thumbnails:**

```powerFx
imgEmployee.Image: ThisItem.ThumbnailPhoto  // 50KB
// Fast, efficient
```

**Best Practices:**

- Store thumbnails separately
- Use appropriate resolution (200x200 for avatars)
- Use compressed formats (WebP, optimized JPEG)
- Lazy load images (load when scrolled into view)

#### Step 8: Control Count Optimization

**❌ BAD - Too many controls:**

```
Screen with 500+ controls = slow
```

**✅ GOOD - Gallery for repeated elements:**

```powerFx
// Instead of 100 separate labels
// Use gallery with 100 items
// Only renders visible items
```

**Tips:**

- Combine multiple labels into one with Char(10)
- Use gallery even for small lists
- Hide unused screens/controls
- Remove unnecessary controls

#### Step 9: Performance Testing Pattern

**Create benchmark comparison:**

```powerFx
// Test 1: Inefficient method
btnTestInefficient.OnSelect:
    Set(varStartTime, Now());

    // Inefficient code
    ForAll(
        Sequence(100),
        Patch(
            colTest,
            LookUp(colTest, ID = Value),
            {Value: Value * 2}
        )
    );

    Set(varTime1, DateDiff(varStartTime, Now(), Milliseconds));
    Notify("Method 1: " & varTime1 & "ms", NotificationType.Info)

// Test 2: Efficient method
btnTestEfficient.OnSelect:
    Set(varStartTime, Now());

    // Efficient code
    ClearCollect(
        colTest,
        AddColumns(
            colTest,
            "NewValue", Value * 2
        )
    );
    Patch(colTest, colTest, {Value: NewValue});

    Set(varTime2, DateDiff(varStartTime, Now(), Milliseconds));
    Notify("Method 2: " & varTime2 & "ms", NotificationType.Success)

// Show comparison
lblComparison.Text:
    "Method 1: " & varTime1 & "ms" & Char(10) &
    "Method 2: " & varTime2 & "ms" & Char(10) &
    "Improvement: " & Round((varTime1 - varTime2) / varTime1 * 100, 0) & "%"
```

#### Step 10: Performance Checklist

**Before Publishing:**

✅ **Data Loading:**

- [ ] Load only necessary data
- [ ] Use FirstN for large datasets
- [ ] Cache reference data in App.OnStart
- [ ] Use Concurrent for multiple data sources

✅ **Delegation:**

- [ ] No yellow delegation warnings
- [ ] Test with data row limit = 10
- [ ] Use delegable functions
- [ ] Verified with > 500 records

✅ **Formulas:**

- [ ] No volatile functions in galleries (Now, Today, User)
- [ ] Used With() to avoid repeated calculations
- [ ] No nested ForAll loops
- [ ] Precalculated complex values

✅ **Gallery:**

- [ ] Items < 2000 (use pagination)
- [ ] Simple formulas in template
- [ ] Appropriate TemplateSize
- [ ] No heavy images

✅ **Images:**

- [ ] Optimized sizes (< 200KB each)
- [ ] Using thumbnails, not full resolution
- [ ] Lazy loading if needed

✅ **Controls:**

- [ ] < 200 controls per screen
- [ ] Removed unused controls
- [ ] Used galleries instead of repeated controls

✅ **App.OnStart:**

- [ ] < 3 seconds total
- [ ] Only essential data
- [ ] Use Concurrent for multiple loads
- [ ] Set loading indicator

**Performance Monitoring:**

```powerFx
// App.OnStart timing
Set(varAppStartTime, Now());

// ... all loading code ...

Set(varAppLoadDuration, DateDiff(varAppStartTime, Now(), Milliseconds));

// Show if > 3 seconds
If(
    varAppLoadDuration > 3000,
    Notify("App loaded slowly: " & varAppLoadDuration & "ms", NotificationType.Warning)
)
```

### Assignment Deliverable

✅ **Create demonstration showing:**

- Performance comparison: Inefficient vs Efficient methods
- Timing measurements with visible differences
- ForAll + Patch vs Collection operations
- Volatile functions impact
- Delegation issues and fixes
- Image optimization example

✅ **Document performance improvements:**

- Before and after timing
- Percentage improvement
- Techniques used

✅ **Performance test report:**

- App load time
- Gallery rendering time
- Data operation benchmarks
- Identified and fixed delegation issues

### Knowledge Check

- [ ] What is delegation and why is it important?
- [ ] What are volatile functions and why avoid them in galleries?
- [ ] How does Concurrent() improve performance?
- [ ] Why use With() instead of repeating formulas?
- [ ] What's the recommended data row limit?
- [ ] How do you identify delegation warnings?
- [ ] What's the benefit of caching reference data?

### Performance Optimization Quick Reference

| Issue                     | Solution                                        |
| ------------------------- | ----------------------------------------------- |
| **Slow data load**        | Use FirstN, load on demand, Concurrent          |
| **Gallery slow**          | Precalculate, avoid volatile functions          |
| **Delegation warning**    | Use delegable functions, StartsWith vs Contains |
| **Repeated calculations** | Use With() or calculate once in variable        |
| **Large images**          | Use thumbnails, optimize size                   |
| **Too many controls**     | Use galleries, combine labels                   |
| **Slow app start**        | Move to screen OnVisible, use Concurrent        |
| **Nested loops**          | Replace with collection operations              |

### Resources

- [Microsoft Learn: Performance optimization](https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/performance-tips)
- [Microsoft Learn: Delegation overview](https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/delegation-overview)
- [Video 1: Performance Best Practices](https://www.youtube.com/watch?v=example1)
- [Video 2: Fixing Delegation Issues](https://www.youtube.com/watch?v=example2)
- [Blog: Power Apps Performance](https://powerapps.microsoft.com/en-us/blog/performance/)

---

## Stage 2 Complete Summary

### Total Accomplishments

You have completed **ALL of Stage 2** covering:

#### Part 1: UI & Configuration (13 hours)

- ✅ Module 5: UI Design (8 hours)
- ✅ Module 6: App Theming (2 hours)
- ✅ Module 7: Canvas App Settings (3 hours)

#### Part 2: Data Operations (4 hours)

- ✅ Module 8: Patch (1 hour)
- ✅ Module 9: Collections (1 hour)
- ✅ Module 10: Gallery Control (2 hours)

#### Part 3: Advanced Features (12 hours)

- ✅ Module 11: Components (2 hours)
- ✅ Module 12: Modern Controls (8 hours)
- ✅ Module 13: Performance Optimization (2 hours)

### Total Stage 2 Time: 29 hours

### Key Skills Mastered

✅ Professional UI design from Figma
✅ Centralized theming system
✅ App configuration and features
✅ Data manipulation with Patch
✅ Collection management
✅ Advanced gallery implementations
✅ Reusable component architecture
✅ Modern control implementation
✅ Performance optimization techniques

### Self-Assessment Checklist

Before moving to Stage 3, ensure you can:

- [ ] Build complete, professional UIs
- [ ] Implement and switch between themes
- [ ] Configure app settings optimally
- [ ] Efficiently manipulate data with Patch
- [ ] Work with collections for complex scenarios
- [ ] Build galleries with search, filter, sort
- [ ] Create reusable components
- [ ] Use modern controls effectively
- [ ] Identify and fix performance issues
- [ ] Avoid delegation warnings

### Next Stage Preview

**Stage 3: Canvas Apps Intermediate & Advanced** will cover:

- Module 14: Delegation (5 hours)
- Module 15: Concurrency (1 hour)
- Module 16: Exception Handling (2 hours)
- Module 17: Deep Linking (1 hour)
- Module 18: Sharing Canvas Apps (1 hour)
- Module 19: Offline Capability (1 hour)
- Module 20: Monitoring Tool (1 hour)
- Module 21: Responsive Design (24 hours) - Major module

**Total Stage 3: 36 hours**

---

**You've completed Stage 2! Type "NEXT" to begin Stage 3: Intermediate & Advanced Canvas Apps.**
