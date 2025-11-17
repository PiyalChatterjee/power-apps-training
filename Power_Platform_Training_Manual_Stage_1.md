# Power Platform Training Manual - Stage 1: Power Apps Fundamentals

## Overview

This stage covers the foundational concepts of Power Apps, focusing on Canvas Apps basics, controls, formulas, and initial UI development. **Duration: 16.5 hours**

---

## Module 1: Types of Power Apps (1 hour)

### Learning Objectives

- Understand the three main types of Power Apps
- Identify when to use each type
- Recognize the fundamental differences between Canvas, Model-Driven, and Portal apps

### Step-by-Step Instructions

#### Step 1: Understanding Power Apps Types

**Canvas Apps:**

- Start with a blank canvas or template
- Pixel-perfect control over UI layout
- Best for: Task-focused apps, mobile experiences, custom UIs
- Data sources: 300+ connectors available

**Model-Driven Apps:**

- Built on top of Dataverse
- Component-focused approach (forms, views, dashboards)
- Best for: Complex data models, business process automation
- Responsive by default

**Power Pages (formerly Portals):**

- External-facing websites
- Built on Dataverse
- Best for: Public-facing websites, customer self-service portals
- Professional templates available

#### Step 2: Watch Reference Video

1. Navigate to the Microsoft Learn platform
2. Watch the introductory video on Power Apps types
3. Take notes on key differences

#### Step 3: Hands-On Exploration

1. Sign in to [Power Apps portal](https://make.powerapps.com)
2. Click **"+ Create"** in the left navigation
3. Observe the different app creation options available:
   - Canvas app from blank
   - Canvas app from template
   - Model-driven app
   - Dataverse tables

#### Step 4: Environment Setup

1. Ensure you have access to a Power Platform environment
2. Verify you're in the correct environment (check top-right dropdown)
3. Note your environment URL for future reference

### Knowledge Check

- [ ] Can you explain when to use Canvas vs Model-Driven apps?
- [ ] What is the primary data source for Model-Driven apps?
- [ ] Which type offers the most UI customization?

### Resources

- [Microsoft Learn: What is Power Apps?](https://learn.microsoft.com/en-us/power-apps/powerapps-overview)
- [Microsoft Learn: Canvas vs Model-Driven Apps](https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/getting-started)

---

## Module 2: Standard Controls and Properties (2 hours)

### Learning Objectives

- Master all basic input controls in Canvas Apps
- Understand common properties (Visible, DisplayMode, Default)
- Create a functional form with validation
- Apply consistent styling

### Step-by-Step Instructions

#### Step 1: Create Your First Canvas App

1. Go to [make.powerapps.com](https://make.powerapps.com)
2. Click **"+ Create"** → **"Blank app"** → **"Blank canvas app"**
3. Name it: `ControlsPracticeApp`
4. Choose format: **Tablet** (1366 x 768)
5. Click **"Create"**

#### Step 2: Understanding the Interface

Familiarize yourself with the Power Apps Studio:

- **Left pane**: Tree view of screens and controls
- **Center**: Canvas (design surface)
- **Right pane**: Properties panel
- **Top**: Formula bar (for writing Power Fx formulas)
- **Top-right**: Save, Publish, Play buttons

#### Step 3: Add and Configure Text Input Control

1. **Insert Text Input:**

   - Click **"+ Insert"** in left panel
   - Select **"Text input"**
   - It will appear on canvas as `TextInput1`

2. **Rename the Control:**

   - Select the control
   - In left tree view, click the "..." → **"Rename"**
   - Name it: `txtFirstName`
   - _Note: This follows naming convention (txt = text input)_

3. **Configure Properties:**

   - **HintText**: `"Enter your first name"`
   - **Default**: `""` (empty string)
   - **MaxLength**: `50`
   - Click on the control, then modify properties in right panel

4. **Add a Label Above:**
   - Insert → **"Text label"**
   - Rename to: `lblFirstName`
   - **Text** property: `"First Name:"`
   - **Font weight**: Bold
   - Position it above `txtFirstName`

#### Step 4: Create Complete Form with All Input Controls

Build a comprehensive form with the following controls:

**A. Text Inputs (3 controls)**

- `txtFirstName` - First Name
- `txtLastName` - Last Name
- `txtEmail` - Email Address

**B. Date Picker**

1. Insert → **"Date picker"**
2. Rename: `dtpBirthDate`
3. Label: `lblBirthDate` with text "Birth Date:"
4. **Format**: `DateTimeFormat.ShortDate`
5. **DefaultDate**: `Today()`

**C. Dropdown**

1. Insert → **"Drop down"**
2. Rename: `ddnDepartment`
3. Label: `lblDepartment` with text "Department:"
4. **Items** property:
   ```powerFx
   ["Engineering", "Sales", "Marketing", "HR", "Finance"]
   ```
5. **Default**: `"Select a department"`

**D. Combo Box**

1. Insert → **"Combo box"**
2. Rename: `cmbSkills`
3. Label: `lblSkills` with text "Skills:"
4. **Items** property:
   ```powerFx
   ["Power Apps", "Power Automate", "Power BI", "Dataverse", "JavaScript"]
   ```
5. **SelectMultiple**: `true`
6. **DefaultSelectedItems**: `[]` (empty table)

**E. Radio Buttons**

1. Insert → **"Radio"**
2. Rename: `radEmploymentType`
3. Label: `lblEmploymentType` with text "Employment Type:"
4. **Items** property:
   ```powerFx
   ["Full-time", "Part-time", "Contract", "Intern"]
   ```
5. **Default**: `"Full-time"`

**F. Checkbox**

1. Insert → **"Checkbox"**
2. Rename: `chkTermsAccepted`
3. **Text** property: `"I accept the terms and conditions"`
4. **Default**: `false`

**G. Toggle**

1. Insert → **"Toggle"**
2. Rename: `tglNewsletterSubscribe`
3. Label: `lblNewsletter` with text "Subscribe to newsletter:"
4. **Default**: `true`

**H. Slider**

1. Insert → **"Slider"**
2. Rename: `sldExperience`
3. Label: `lblExperience` with text "Years of Experience:"
4. **Min**: `0`
5. **Max**: `20`
6. **Default**: `0`
7. **ShowValue**: `true`

**I. Rating Control**

1. Insert → **"Rating"**
2. Rename: `rtgSatisfaction`
3. Label: `lblSatisfaction` with text "How satisfied are you?"
4. **Max**: `5`
5. **Default**: `0`

**J. Rich Text Editor**

1. Insert → **"Rich text editor"**
2. Rename: `rteComments`
3. Label: `lblComments` with text "Additional Comments:"
4. **HintText**: `"Enter your comments here..."`

#### Step 5: Add Submit Button

1. Insert → **"Button"**
2. Rename: `btnSubmit`
3. **Text**: `"Submit"`
4. Position at bottom of form
5. **OnSelect** property (we'll add logic later):
   ```powerFx
   // Placeholder for now
   Notify("Form submitted successfully!", NotificationType.Success)
   ```

#### Step 6: Add Display Label for Output

1. Insert → **"Text label"**
2. Rename: `lblFormOutput`
3. Position below submit button
4. **Text** property:
   ```powerFx
   "Name: " & txtFirstName.Text & " " & txtLastName.Text & Char(10) &
   "Email: " & txtEmail.Text & Char(10) &
   "Birth Date: " & Text(dtpBirthDate.SelectedDate, "mm/dd/yyyy") & Char(10) &
   "Department: " & ddnDepartment.Selected.Value & Char(10) &
   "Skills: " & Concat(cmbSkills.SelectedItems, Value, ", ") & Char(10) &
   "Employment: " & radEmploymentType.Selected.Value & Char(10) &
   "Terms Accepted: " & If(chkTermsAccepted.Value, "Yes", "No") & Char(10) &
   "Newsletter: " & If(tglNewsletterSubscribe.Value, "Subscribed", "Not subscribed") & Char(10) &
   "Experience: " & sldExperience.Value & " years" & Char(10) &
   "Satisfaction: " & rtgSatisfaction.Value & " stars"
   ```
   _Note: Char(10) creates a line break_

#### Step 7: Apply Consistent Styling

1. **Create a Color Scheme:**

   - Primary: `RGBA(0, 120, 212, 1)` (Blue)
   - Secondary: `RGBA(243, 242, 241, 1)` (Light Gray)
   - Text: `RGBA(50, 49, 48, 1)` (Dark Gray)

2. **Style All Labels:**

   - Select first label (lblFirstName)
   - Set **Color**: Dark Gray
   - Set **Size**: `12`
   - Set **Font weight**: `Bold`
   - Copy these settings to all other labels

3. **Style Input Controls:**

   - Set consistent **Height**: `60`
   - Set **BorderColor**: Light Gray
   - Set **FocusedBorderColor**: Blue

4. **Style Submit Button:**
   - **Fill**: Blue
   - **Color**: White (text color)
   - **Height**: `60`
   - **Width**: `200`
   - **BorderRadius**: `5`

#### Step 8: Add Validation

Enhance the submit button with validation logic:

**OnSelect** property of `btnSubmit`:

```powerFx
// Validate required fields
If(
    IsBlank(txtFirstName.Text),
    Notify("First name is required", NotificationType.Error),
    IsBlank(txtLastName.Text),
    Notify("Last name is required", NotificationType.Error),
    !IsMatch(txtEmail.Text, Match.Email),
    Notify("Please enter a valid email address", NotificationType.Error),
    IsBlank(ddnDepartment.Selected.Value),
    Notify("Please select a department", NotificationType.Error),
    !chkTermsAccepted.Value,
    Notify("You must accept the terms and conditions", NotificationType.Error),
    // If all validations pass
    Notify("Form submitted successfully!", NotificationType.Success)
)
```

#### Step 9: Test Your App

1. Click **"Play"** button (▶) in top-right
2. Test each control:
   - Enter text in text inputs
   - Select date
   - Choose from dropdowns
   - Select multiple skills
   - Toggle various options
3. Try to submit without filling required fields
4. Fill all fields and submit
5. Press **ESC** to exit play mode

#### Step 10: Save and Document

1. Click **"File"** → **"Save"**
2. Add description: "Practice app with all standard input controls"
3. Click **"Save"** button

### Assignment Deliverable

✅ Create a screen with all the input controls listed above
✅ Implement proper naming conventions
✅ Add validation logic
✅ Test all controls work correctly
✅ Display form data in a readable format

### Knowledge Check

- [ ] Can you name at least 8 different input controls?
- [ ] What is the formula to check if a text input is empty?
- [ ] How do you handle multiple selections in a combo box?
- [ ] What's the difference between a dropdown and a combo box?

### Common Properties Reference

| Property         | Purpose                 | Example                          |
| ---------------- | ----------------------- | -------------------------------- |
| **Visible**      | Show/hide control       | `true` or `chkShowDetails.Value` |
| **DisplayMode**  | Edit, View, or Disabled | `DisplayMode.Edit`               |
| **Default**      | Initial value           | `""` or `Today()`                |
| **Height/Width** | Size of control         | `60` or `Parent.Width`           |
| **X/Y**          | Position                | `50` or `lblTitle.Y + 100`       |
| **Fill**         | Background color        | `RGBA(0, 120, 212, 1)`           |
| **Color**        | Text color              | `RGBA(255, 255, 255, 1)`         |
| **BorderColor**  | Border color            | `RGBA(200, 200, 200, 1)`         |

### Resources

- [Microsoft Learn: Controls and properties in Power Apps](https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/reference-properties)
- [Microsoft Learn: Add and configure controls](https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/add-configure-controls)
- [Microsoft Learn: Control types](https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/controls/control-types)

---

## Module 3: Power Fx Formulas (3 hours)

### Learning Objectives

- Master essential Power Fx formulas for data manipulation
- Understand formula syntax and context
- Work with tables, collections, and records
- Implement date operations and conditional logic

### Step-by-Step Instructions

#### Step 1: Create Practice App for Formulas

1. Create new blank canvas app: `FormulasPracticeApp`
2. Choose **Phone** layout (640 x 1136)
3. Rename Screen1 to `scrFormulaPractice`

#### Step 2: LookUp Formula

**Purpose:** Find a single record in a table based on criteria

**Create Sample Data:**

1. Add a button, rename to `btnCreateEmployees`
2. **OnSelect** property:
   ```powerFx
   ClearCollect(
       colEmployees,
       {ID: 1, Name: "John Smith", Department: "Engineering", Salary: 75000, HireDate: Date(2020, 3, 15)},
       {ID: 2, Name: "Sarah Johnson", Department: "Sales", Salary: 65000, HireDate: Date(2019, 7, 22)},
       {ID: 3, Name: "Mike Wilson", Department: "Engineering", Salary: 85000, HireDate: Date(2018, 1, 10)},
       {ID: 4, Name: "Emily Davis", Department: "HR", Salary: 60000, HireDate: Date(2021, 5, 5)},
       {ID: 5, Name: "David Brown", Department: "Sales", Salary: 70000, HireDate: Date(2020, 9, 18)}
   )
   ```
3. Run this button once to create the collection

**LookUp Examples:**

Add text input `txtEmployeeID` with HintText: "Enter Employee ID (1-5)"

Add label `lblLookUpResult` with **Text**:

```powerFx
"Employee Details:" & Char(10) &
"Name: " & LookUp(colEmployees, ID = Value(txtEmployeeID.Text), Name) & Char(10) &
"Department: " & LookUp(colEmployees, ID = Value(txtEmployeeID.Text), Department) & Char(10) &
"Salary: $" & Text(LookUp(colEmployees, ID = Value(txtEmployeeID.Text), Salary), "#,##0")
```

**Alternative - LookUp with full record:**

```powerFx
With(
    {emp: LookUp(colEmployees, ID = Value(txtEmployeeID.Text))},
    "Name: " & emp.Name & Char(10) &
    "Department: " & emp.Department & Char(10) &
    "Salary: $" & Text(emp.Salary, "#,##0")
)
```

#### Step 3: Filter Formula

**Purpose:** Return multiple records that meet criteria

Add button `btnFilterDemos`:

**Example 1 - Filter by Department:**

```powerFx
ClearCollect(
    colEngineers,
    Filter(colEmployees, Department = "Engineering")
);
Notify("Found " & CountRows(colEngineers) & " engineers")
```

**Example 2 - Filter by Salary Range:**

```powerFx
ClearCollect(
    colHighEarners,
    Filter(colEmployees, Salary >= 70000)
)
```

**Example 3 - Filter with Multiple Conditions:**

```powerFx
ClearCollect(
    colFilteredResults,
    Filter(
        colEmployees,
        Department = "Sales" && Salary > 65000
    )
)
```

**Example 4 - Filter with Search:**
Add text input `txtSearchName`
Add gallery `galFilteredEmployees`:

```powerFx
Filter(
    colEmployees,
    txtSearchName.Text in Name
)
```

#### Step 4: CountRows and CountIf

**CountRows** - Count all records:

```powerFx
"Total Employees: " & CountRows(colEmployees)
```

**CountIf** - Count with condition:

```powerFx
"Engineers: " & CountIf(colEmployees, Department = "Engineering") & Char(10) &
"Sales: " & CountIf(colEmployees, Department = "Sales") & Char(10) &
"High Earners: " & CountIf(colEmployees, Salary >= 70000)
```

#### Step 5: First, FirstN, Last, LastN

Add label `lblFirstLast`:

```powerFx
"First Employee: " & First(colEmployees).Name & Char(10) &
"Last Employee: " & Last(colEmployees).Name & Char(10) &
"First 3 IDs: " & Concat(FirstN(colEmployees, 3), ID & ", ") & Char(10) &
"Last 2 Names: " & Concat(LastN(colEmployees, 2), Name, ", ")
```

#### Step 6: GroupBy

**Purpose:** Group records by a field

Add button `btnGroupByDemo`:

```powerFx
ClearCollect(
    colGroupedByDept,
    GroupBy(colEmployees, "Department", "Employees")
);
Notify("Grouped " & CountRows(colGroupedByDept) & " departments")
```

Add gallery to show grouped results:

```powerFx
// Items property
AddColumns(
    colGroupedByDept,
    "EmployeeCount", CountRows(Employees),
    "TotalSalary", Sum(Employees, Salary),
    "AvgSalary", Average(Employees, Salary)
)
```

#### Step 7: Distinct

**Purpose:** Get unique values from a column

```powerFx
"Unique Departments: " & Concat(Distinct(colEmployees, Department), Result, ", ")
```

#### Step 8: AddColumns

**Purpose:** Add calculated columns to a table

```powerFx
ClearCollect(
    colEmployeesWithBonus,
    AddColumns(
        colEmployees,
        "Bonus", Salary * 0.1,
        "AnnualTotal", Salary * 1.1,
        "YearsOfService", DateDiff(HireDate, Today(), Years)
    )
)
```

#### Step 9: DropColumns

**Purpose:** Remove columns from a table

```powerFx
ClearCollect(
    colEmployeesNoSalary,
    DropColumns(colEmployees, "Salary", "HireDate")
)
```

#### Step 10: ShowColumns

**Purpose:** Keep only specific columns

```powerFx
ClearCollect(
    colEmployeeNames,
    ShowColumns(colEmployees, "ID", "Name", "Department")
)
```

#### Step 11: Concat

**Purpose:** Concatenate text from a table

```powerFx
// Simple list
"All Employees: " & Concat(colEmployees, Name, ", ")

// With custom separator
Concat(colEmployees, Name & " (" & Department & ")", "; ")

// With filter
Concat(
    Filter(colEmployees, Department = "Engineering"),
    Name,
    " | "
)
```

#### Step 12: As (Disambiguation)

**Purpose:** Resolve naming conflicts when working with multiple tables

```powerFx
// Compare each employee's salary to department average
AddColumns(
    colEmployees As emp,
    "DeptAverage",
    Average(
        Filter(
            colEmployees As allEmp,
            allEmp.Department = emp.Department
        ),
        Salary
    )
)
```

#### Step 13: With

**Purpose:** Create named variables for cleaner formulas

**Example 1 - Simple With:**

```powerFx
With(
    {
        selectedEmp: LookUp(colEmployees, ID = Value(txtEmployeeID.Text)),
        bonusRate: 0.15
    },
    "Employee: " & selectedEmp.Name & Char(10) &
    "Salary: $" & Text(selectedEmp.Salary, "#,##0") & Char(10) &
    "Bonus: $" & Text(selectedEmp.Salary * bonusRate, "#,##0")
)
```

**Example 2 - Nested With:**

```powerFx
With(
    {dept: ddnDepartment.Selected.Value},
    With(
        {
            deptEmployees: Filter(colEmployees, Department = dept),
            avgSalary: Average(Filter(colEmployees, Department = dept), Salary)
        },
        "Department: " & dept & Char(10) &
        "Employees: " & CountRows(deptEmployees) & Char(10) &
        "Average Salary: $" & Text(avgSalary, "#,##0")
    )
)
```

#### Step 14: Date Functions

**Date** - Create a date:

```powerFx
Date(2025, 11, 12)  // November 12, 2025
```

**DateValue** - Parse a date string:

```powerFx
DateValue("11/12/2025")
DateValue("2025-11-12")
```

**DateTimeValue** - Parse date and time:

```powerFx
DateTimeValue("11/12/2025 3:30 PM")
```

**DateAdd** - Add time to a date:

```powerFx
DateAdd(Today(), 7, Days)          // 7 days from now
DateAdd(Today(), 3, Months)        // 3 months from now
DateAdd(Now(), 2, Hours)           // 2 hours from now
```

**DateDiff** - Calculate difference:

```powerFx
DateDiff(Date(2020, 1, 1), Today(), Days)      // Days since 2020
DateDiff(HireDate, Today(), Years)              // Years of service
DateDiff(Now(), DateAdd(Now(), 2, Hours), Minutes)  // 120 minutes
```

**Practical Example:**
Add label `lblDateExamples`:

```powerFx
"Today: " & Text(Today(), "mm/dd/yyyy") & Char(10) &
"Next Week: " & Text(DateAdd(Today(), 7, Days), "mm/dd/yyyy") & Char(10) &
"90 Days Ago: " & Text(DateAdd(Today(), -90, Days), "mm/dd/yyyy") & Char(10) &
"Days in 2025: " & DateDiff(Date(2025, 1, 1), Date(2025, 12, 31), Days) & Char(10) &
"Current Time: " & Text(Now(), "hh:mm:ss AM/PM")
```

#### Step 15: If and Switch

**If Statement:**

```powerFx
// Simple If
If(
    Salary >= 80000,
    "High",
    "Standard"
)

// Nested If (avoid this, use Switch instead)
If(
    Salary >= 80000,
    "High",
    Salary >= 65000,
    "Medium",
    "Low"
)

// If with multiple conditions
If(
    Department = "Engineering" && Salary > 75000,
    "Senior Engineer",
    Department = "Engineering",
    "Engineer",
    "Other"
)
```

**Switch Statement** (cleaner for multiple conditions):

```powerFx
Switch(
    Department,
    "Engineering", "🔧 Tech Team",
    "Sales", "💰 Revenue Team",
    "HR", "👥 People Team",
    "Finance", "💵 Money Team",
    "❓ Unknown"
)

// Switch with complex conditions
Switch(
    true,
    Salary >= 80000, "Level 3",
    Salary >= 65000, "Level 2",
    "Level 1"
)
```

#### Step 16: JSON Functions

**JSON** - Convert table/record to JSON string:

```powerFx
// Single record
JSON(LookUp(colEmployees, ID = 1))

// Full table
JSON(colEmployees, JSONFormat.IncludeBinaryData)

// Specific fields
JSON(
    ShowColumns(colEmployees, "Name", "Department"),
    JSONFormat.IndentFour
)
```

**Example - Store JSON:**
Add button with OnSelect:

```powerFx
Set(
    varEmployeeJSON,
    JSON(colEmployees, JSONFormat.IndentFour)
);
Notify("JSON created", NotificationType.Success)
```

Add label to display:

```powerFx
varEmployeeJSON
```

#### Step 17: Launch

**Purpose:** Open URLs, make phone calls, send emails

```powerFx
// Open website
Launch("https://www.microsoft.com")

// Open with parameters
Launch("https://www.google.com/search?q=" & txtSearchTerm.Text)

// Send email
Launch("mailto:user@example.com?subject=Hello&body=Message text")

// Make phone call
Launch("tel:+1234567890")

// Open maps
Launch("https://maps.google.com/?q=" & txtAddress.Text)
```

**Practical Button Examples:**

Button `btnSendEmail`:

```powerFx
Launch(
    "mailto:" & txtEmail.Text &
    "?subject=Welcome to the Team" &
    "&body=Dear " & txtFirstName.Text & ",%0A%0AWelcome aboard!"
)
```

#### Step 18: Match and MatchAll

**Match** - Find first pattern match:

```powerFx
// Basic email validation
IsMatch(txtEmail.Text, Match.Email)

// Phone number pattern
IsMatch(txtPhone.Text, "\d{3}-\d{3}-\d{4}")

// Extract first number from text
Match("Order #12345 placed", "\d+").FullMatch
```

**MatchAll** - Find all pattern matches:

```powerFx
// Extract all numbers
Concat(
    MatchAll("Prices: $25, $30, $45", "\d+"),
    FullMatch,
    ", "
)

// Extract all email addresses from text
ForAll(
    MatchAll(
        "Contact: john@example.com or sarah@example.com",
        Match.Email
    ),
    FullMatch
)
```

**Practical Validation Examples:**

Add these validation formulas to your form:

```powerFx
// Email validation
If(
    !IsMatch(txtEmail.Text, Match.Email),
    "Invalid email format",
    "✓ Valid"
)

// Phone validation (US format)
If(
    !IsMatch(txtPhone.Text, "^\d{3}-\d{3}-\d{4}$"),
    "Use format: 123-456-7890",
    "✓ Valid"
)

// Zipcode validation
If(
    !IsMatch(txtZipCode.Text, "^\d{5}(-\d{4})?$"),
    "Use 5 or 9 digit zipcode",
    "✓ Valid"
)
```

#### Step 19: SetFocus

**Purpose:** Programmatically set focus to a control

```powerFx
// In button OnSelect - move to next field
SetFocus(txtLastName)

// Validation with focus
If(
    IsBlank(txtFirstName.Text),
    Notify("First name required", NotificationType.Error);
    SetFocus(txtFirstName),

    IsBlank(txtEmail.Text),
    Notify("Email required", NotificationType.Error);
    SetFocus(txtEmail),

    // All valid
    Notify("Form valid!", NotificationType.Success)
)
```

#### Step 20: Validate

**Purpose:** Trigger validation rules on a control

Add validation rules to text input:

**txtEmail.Valid** property:

```powerFx
IsMatch(Self.Text, Match.Email)
```

**txtEmail.ValidationMessage** property:

```powerFx
"Please enter a valid email address"
```

**Button to validate:**

```powerFx
If(
    Validate(txtEmail),
    Notify("Email is valid", NotificationType.Success),
    Notify("Email validation failed", NotificationType.Error)
)
```

### Assignment Deliverable

Create a comprehensive demonstration app that includes:

✅ **Collection of at least 10 employee records** with Name, Department, Salary, HireDate
✅ **Search functionality** using Filter
✅ **Department grouping** showing counts and averages
✅ **Date calculations** for years of service
✅ **Conditional formatting** using If/Switch (color-code by salary level)
✅ **Email validation** using Match
✅ **Export to JSON** button
✅ **Form with SetFocus** navigation between fields

### Knowledge Check

- [ ] What's the difference between LookUp and Filter?
- [ ] When would you use With instead of writing the same formula multiple times?
- [ ] How do you check if a text input contains valid email?
- [ ] What's the result type of GroupBy?
- [ ] How do you calculate days between two dates?

### Formula Quick Reference

| Formula        | Returns       | Example                                  |
| -------------- | ------------- | ---------------------------------------- |
| **LookUp**     | Single record | `LookUp(Users, ID = 5)`                  |
| **Filter**     | Table         | `Filter(Users, Age > 25)`                |
| **CountRows**  | Number        | `CountRows(colData)`                     |
| **CountIf**    | Number        | `CountIf(Users, Active = true)`          |
| **First/Last** | Single record | `First(SortedList).Name`                 |
| **GroupBy**    | Table         | `GroupBy(Data, "Category", "Items")`     |
| **Distinct**   | Table         | `Distinct(Users, Department)`            |
| **AddColumns** | Table         | `AddColumns(Users, "Bonus", Salary*0.1)` |
| **Concat**     | Text          | `Concat(Users, Name, ", ")`              |
| **DateAdd**    | Date          | `DateAdd(Today(), 7, Days)`              |
| **DateDiff**   | Number        | `DateDiff(StartDate, EndDate, Days)`     |
| **If**         | Any           | `If(Score > 90, "A", "B")`               |
| **Switch**     | Any           | `Switch(Day, 1, "Mon", 2, "Tue")`        |
| **IsMatch**    | Boolean       | `IsMatch(Email, Match.Email)`            |

### Resources

- [Microsoft Learn: Formula reference](https://learn.microsoft.com/en-us/power-platform/power-fx/formula-reference)
- [Microsoft Learn: Power Fx overview](https://learn.microsoft.com/en-us/power-platform/power-fx/overview)
- [Microsoft Learn: Understand tables and records](https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/working-with-tables)
- [Microsoft Learn: Date and time functions](https://learn.microsoft.com/en-us/power-platform/power-fx/reference/function-date-time)
- [Microsoft Learn: Text functions](https://learn.microsoft.com/en-us/power-platform/power-fx/reference/function-text)

---

## Module 4: Standard Naming Conventions (0.5 hours)

### Learning Objectives

- Understand the importance of consistent naming conventions
- Learn standard prefixes for different control types
- Apply naming best practices to variables, collections, and screens
- Improve code readability and maintainability

### Step-by-Step Instructions

#### Step 1: Why Naming Conventions Matter

**Benefits:**

- **Maintainability**: Easy to understand code months later
- **Collaboration**: Team members can navigate your app easily
- **Debugging**: Quickly identify control types in formulas
- **IntelliSense**: Better autocomplete suggestions
- **Professionalism**: Shows attention to detail

#### Step 2: Control Naming Prefixes

Memorize these standard prefixes:

| Control Type         | Prefix | Example                  |
| -------------------- | ------ | ------------------------ |
| **Text Input**       | `txt`  | `txtFirstName`           |
| **Label**            | `lbl`  | `lblTitle`               |
| **Button**           | `btn`  | `btnSubmit`              |
| **Dropdown**         | `ddn`  | `ddnCountry`             |
| **Combo Box**        | `cmb`  | `cmbSkills`              |
| **Date Picker**      | `dtp`  | `dtpStartDate`           |
| **Gallery**          | `gal`  | `galEmployees`           |
| **Data Table**       | `tbl`  | `tblOrders`              |
| **Form**             | `frm`  | `frmUserProfile`         |
| **Checkbox**         | `chk`  | `chkAgreeTerms`          |
| **Toggle**           | `tgl`  | `tglEnableNotifications` |
| **Radio**            | `rad`  | `radGender`              |
| **Slider**           | `sld`  | `sldVolume`              |
| **Rating**           | `rtg`  | `rtgSatisfaction`        |
| **Icon**             | `ico`  | `icoSearch`              |
| **Image**            | `img`  | `imgLogo`                |
| **HTML Text**        | `htm`  | `htmContent`             |
| **Rich Text Editor** | `rte`  | `rteDescription`         |
| **Pen Input**        | `pen`  | `penSignature`           |
| **Timer**            | `tmr`  | `tmrCountdown`           |
| **Container**        | `con`  | `conHeader`              |
| **Group**            | `grp`  | `grpFilters`             |
| **Component**        | `cmp`  | `cmpNavMenu`             |

#### Step 3: Screen Naming Convention

**Prefix:** `scr`

**Structure:** `scr<Purpose><OptionalDetail>`

**Examples:**

```
scrHome              // Home screen
scrLogin             // Login screen
scrEmployeeList      // List of employees
scrEmployeeDetail    // Single employee details
scrEmployeeNew       // Create new employee
scrEmployeeEdit      // Edit existing employee
scrSettings          // Settings screen
scrReports           // Reports screen
scrReportsSales      // Sales reports sub-screen
```

**Navigation hierarchy:**

- Use consistent naming for CRUD operations:
  - `scrItemList` → `scrItemDetail` → `scrItemEdit`
  - `scrItemNew` for creating new items

#### Step 4: Variable Naming Conventions

**Global Variables** (use `Set`)

- **Prefix:** `var`
- **Use:** PascalCase for multi-word names
- **Examples:**
  ```powerFx
  Set(varCurrentUser, User().FullName)
  Set(varThemePrimaryColor, RGBA(0, 120, 212, 1))
  Set(varIsLoading, false)
  Set(varSelectedEmployeeID, 0)
  Set(varErrorMessage, "")
  ```

**Context Variables** (use `UpdateContext`)

- **Prefix:** `loc` (for "local")
- **Use:** PascalCase
- **Examples:**
  ```powerFx
  UpdateContext({
      locFilterText: "",
      locShowDetails: false,
      locCurrentPage: 1
  })
  ```

**Boolean Variables** (true/false)

- **Prefix:** `Is`, `Has`, `Can`, `Should`
- **Examples:**
  ```powerFx
  Set(varIsLoggedIn, true)
  Set(varHasPermission, false)
  Set(varCanEdit, false)
  Set(varShouldRefresh, true)
  ```

#### Step 5: Collection Naming Convention

**Prefix:** `col`

**Structure:** `col<DataDescription>`

**Examples:**

```powerFx
ClearCollect(colEmployees, [...])
ClearCollect(colFilteredEmployees, [...])
ClearCollect(colDepartments, [...])
ClearCollect(colSelectedItems, [...])
ClearCollect(colTemporaryData, [...])
```

**Special Cases:**

- Temporary collections: `colTemp<Purpose>`
  ```powerFx
  ClearCollect(colTempResults, [...])
  ```
- Grouped collections: `col<Data>By<GroupField>`
  ```powerFx
  ClearCollect(colEmployeesByDepartment, GroupBy(...))
  ```

#### Step 6: Data Source Naming

**SharePoint Lists / SQL Tables / Dataverse Tables:**

- Use the actual data source name (no prefix needed)
- **Examples:**
  - `Employees`
  - `Orders`
  - `SalesData`
  - `'Employee Information'` (use single quotes if spaces)

#### Step 7: Component Properties

**Input Properties:**

- **Prefix:** `prm` (for parameter)
- **Examples:**
  ```
  prmEmployeeID
  prmShowDetails
  prmThemeColor
  ```

**Output Properties:**

- **Prefix:** `out`
- **Examples:**
  ```
  outSelectedItem
  outIsValid
  outTotalAmount
  ```

#### Step 8: Practical Application Exercise

**Rename an existing app:**

1. Open your `ControlsPracticeApp`
2. Rename all controls following conventions:

**Before:**

```
TextInput1 → txtFirstName
TextInput2 → txtLastName
TextInput3 → txtEmail
DatePicker1 → dtpBirthDate
Dropdown1 → ddnDepartment
...
```

3. Use **Ctrl + H** (Find & Replace) in formula bar to update formulas

**Find:** `TextInput1`
**Replace:** `txtFirstName`

4. Verify app still works after renaming

#### Step 9: Comments and Documentation

Use `//` for comments in Power Fx:

```powerFx
// ===================================
// FORM VALIDATION
// Checks all required fields before submission
// ===================================
If(
    IsBlank(txtFirstName.Text),
    // First name is required
    Notify("Please enter first name", NotificationType.Error);
    SetFocus(txtFirstName),

    !IsMatch(txtEmail.Text, Match.Email),
    // Email must be valid format
    Notify("Invalid email", NotificationType.Error);
    SetFocus(txtEmail),

    // All validations passed - proceed
    Patch(Employees, Defaults(Employees), {
        FirstName: txtFirstName.Text,
        LastName: txtLastName.Text,
        Email: txtEmail.Text
    })
)
```

#### Step 10: Common Naming Mistakes to Avoid

❌ **Don't:**

- Use spaces: `Text Input 1`
- Use special characters: `txt@Email`
- Use generic names: `Button1`, `Label5`
- Mix conventions: `txtFirst_Name`
- Use unclear abbreviations: `txtFN`

✅ **Do:**

- Use descriptive names: `txtFirstName`
- Be consistent: Always use same prefix for same control type
- Use PascalCase: `txtUserEmailAddress`
- Be specific: `btnSaveEmployee` not `btnSave`

### Assignment Deliverable

✅ Review Neudesic documentation on naming conventions
✅ Refactor your previous apps with proper naming
✅ Create a personal reference sheet of common prefixes
✅ Practice renaming controls in existing app

### Knowledge Check

- [ ] What prefix is used for text input controls?
- [ ] What's the difference between `var` and `loc` prefixes?
- [ ] How should collections be named?
- [ ] What prefix is used for screens?

### Naming Convention Cheat Sheet

```
CONTROLS:
txt  - Text Input      |  gal - Gallery        |  ico - Icon
lbl  - Label           |  tbl - Data Table     |  img - Image
btn  - Button          |  frm - Form           |  htm - HTML Text
ddn  - Dropdown        |  chk - Checkbox       |  rte - Rich Text Editor
cmb  - Combo Box       |  tgl - Toggle         |  con - Container
dtp  - Date Picker     |  rad - Radio          |  cmp - Component
sld  - Slider          |  rtg - Rating         |

SCREENS:
scr<PurposeDetail>     Example: scrEmployeeList

VARIABLES:
var<Name>              Example: varCurrentUser (global)
loc<Name>              Example: locFilterText (context)

COLLECTIONS:
col<DataDescription>   Example: colEmployees

COMPONENT PROPERTIES:
prm<Name>              Example: prmEmployeeID (input)
out<Name>              Example: outSelectedItem (output)
```

### Resources

- [Microsoft Learn: Canvas app coding standards](https://learn.microsoft.com/en-us/power-apps/guidance/planning/coding-standards)
- [Power Apps Best Practices: Naming Conventions](https://learn.microsoft.com/en-us/power-apps/guidance/planning/app-guidelines)

---

## Stage 1 Summary

### What You've Accomplished

✅ Understanding of Power Apps types and when to use each
✅ Mastery of standard input controls and properties
✅ Essential Power Fx formulas for data manipulation
✅ Professional naming conventions for maintainable code

### Total Time: 16.5 hours

### Next Stage Preview

**Stage 2** will cover:

- UI Design using Figma
- App theming and branding
- Canvas app settings and features
- Performance optimization basics

### Self-Assessment Checklist

Before moving to Stage 2, ensure you can:

- [ ] Create a blank canvas app from scratch
- [ ] Add and configure at least 10 different control types
- [ ] Write formulas using LookUp, Filter, If, and date functions
- [ ] Create and manipulate collections
- [ ] Follow naming conventions consistently
- [ ] Validate form inputs before submission
- [ ] Display dynamic data based on user selections

### Resources for Further Learning

- [Power Apps Community Forums](https://powerusers.microsoft.com/t5/Power-Apps-Community/ct-p/PowerApps1)
- [Power Apps YouTube Channel](https://www.youtube.com/c/MicrosoftPowerApps)
- [Power Apps Blog](https://powerapps.microsoft.com/en-us/blog/)

---

**Ready for Stage 2? Type "NEXT" to continue to UI Design and Theming.**
