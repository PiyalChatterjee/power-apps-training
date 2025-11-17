# Power Platform Training Manual - Stage 2 Part 2: Data Operations and Performance

## Overview

This section continues Stage 2, covering data manipulation with Patch, collections management, gallery controls, reusable components, modern controls, and performance optimization. **Duration: 10 hours**

---

## Module 8: Patch (1 hour)

### Learning Objectives

- Understand Patch function for data operations
- Compare ForAll loop patching vs. collection patching
- Identify performance implications of different approaches
- Master Create, Update, and Delete operations

### Step-by-Step Instructions

#### Step 1: Understanding Patch Function

**What is Patch?**

- Creates or modifies records in a data source
- Can update single or multiple records
- More efficient than traditional submit forms for complex scenarios

**Syntax:**

```powerFx
// Create new record
Patch(DataSource, Defaults(DataSource), {Field1: Value1, Field2: Value2})

// Update existing record
Patch(DataSource, LookupRecord, {Field1: NewValue1})

// Update multiple fields
Patch(DataSource, LookupRecord, {Field1: NewValue1, Field2: NewValue2})
```

#### Step 2: Create Practice App

1. Create new Canvas app: `PatchPracticeApp`
2. Format: Tablet
3. Rename Screen1 to `scrPatchDemo`

#### Step 3: Create Sample Data Source

**Create collection for practice:**

Add button `btnCreateEmployees`:

```powerFx
ClearCollect(
    colEmployees,
    {ID: 1, Name: "John Smith", Department: "IT", Salary: 75000, IsActive: true},
    {ID: 2, Name: "Sarah Johnson", Department: "HR", Salary: 65000, IsActive: true},
    {ID: 3, Name: "Mike Wilson", Department: "IT", Salary: 85000, IsActive: true},
    {ID: 4, Name: "Emily Davis", Department: "Sales", Salary: 70000, IsActive: true},
    {ID: 5, Name: "David Brown", Department: "IT", Salary: 60000, IsActive: true}
);

Notify("Sample data created", NotificationType.Success)
```

Run this button once.

#### Step 4: Patch - Create Operation

**Example 1: Create Single Record**

Add form controls:

- `txtNewName` - Text input for name
- `ddnNewDepartment` - Dropdown: `["IT", "HR", "Sales", "Finance"]`
- `txtNewSalary` - Text input for salary
- `btnAddEmployee` - Button

**btnAddEmployee OnSelect:**

```powerFx
// Create new employee record
Patch(
    colEmployees,
    Defaults(colEmployees),
    {
        ID: Max(colEmployees, ID) + 1,  // Auto-increment ID
        Name: txtNewName.Text,
        Department: ddnNewDepartment.Selected.Value,
        Salary: Value(txtNewSalary.Text),
        IsActive: true
    }
);

// Reset form
Reset(txtNewName);
Reset(ddnNewDepartment);
Reset(txtNewSalary);

Notify("Employee added successfully", NotificationType.Success)
```

#### Step 5: Patch - Update Operation

**Example 2: Update Single Record**

Add controls:

- `txtEmployeeID` - Input for ID to update
- `txtUpdateName` - New name
- `btnUpdateEmployee` - Button

**btnUpdateEmployee OnSelect:**

```powerFx
// Find the record and update it
Patch(
    colEmployees,
    LookUp(colEmployees, ID = Value(txtEmployeeID.Text)),
    {
        Name: txtUpdateName.Text
    }
);

Notify("Employee updated", NotificationType.Success)
```

**Example 3: Update Multiple Fields**

```powerFx
Patch(
    colEmployees,
    LookUp(colEmployees, ID = Value(txtEmployeeID.Text)),
    {
        Name: txtUpdateName.Text,
        Department: ddnUpdateDepartment.Selected.Value,
        Salary: Value(txtUpdateSalary.Text)
    }
)
```

#### Step 6: Patch - Delete Operation (Soft Delete)

**Example 4: Soft Delete (Set IsActive = false)**

Add button `btnDeactivateEmployee`:

```powerFx
Patch(
    colEmployees,
    LookUp(colEmployees, ID = Value(txtEmployeeID.Text)),
    {
        IsActive: false
    }
);

Notify("Employee deactivated", NotificationType.Warning)
```

**Example 5: Hard Delete (Remove from collection)**

Add button `btnDeleteEmployee`:

```powerFx
Remove(
    colEmployees,
    LookUp(colEmployees, ID = Value(txtEmployeeID.Text))
);

Notify("Employee deleted permanently", NotificationType.Error)
```

#### Step 7: ForAll Loop vs Collection Patch

**Scenario: Give salary increase to all IT employees**

**Approach 1: ForAll with Individual Patches (SLOW)**

Add button `btnIncreaseForAll`:

```powerFx
// Start timer
Set(varStartTime, Now());

// Update each IT employee one by one
ForAll(
    Filter(colEmployees, Department = "IT"),
    Patch(
        colEmployees,
        LookUp(colEmployees, ID = ThisRecord.ID),
        {
            Salary: ThisRecord.Salary * 1.10  // 10% increase
        }
    )
);

// Calculate duration
Set(varDuration1, DateDiff(varStartTime, Now(), Milliseconds));

Notify("ForAll completed in " & varDuration1 & "ms", NotificationType.Success)
```

**Approach 2: Patch Collection Directly (FAST)**

Add button `btnIncreasePatchCollection`:

```powerFx
// Start timer
Set(varStartTime, Now());

// Create updated collection
ClearCollect(
    colEmployeesUpdated,
    AddColumns(
        colEmployees,
        "NewSalary",
        If(Department = "IT", Salary * 1.10, Salary)
    )
);

// Patch the entire collection at once
Patch(
    colEmployees,
    colEmployeesUpdated,
    {
        Salary: NewSalary
    }
);

// Calculate duration
Set(varDuration2, DateDiff(varStartTime, Now(), Milliseconds));

Notify("Collection Patch completed in " & varDuration2 & "ms", NotificationType.Success)
```

**Approach 3: Most Efficient - Direct Collection Replacement**

Add button `btnIncreaseOptimal`:

```powerFx
// Start timer
Set(varStartTime, Now());

// Update collection directly
ClearCollect(
    colEmployees,
    AddColumns(
        DropColumns(colEmployees, "Salary"),
        "Salary",
        If(Department = "IT", Salary * 1.10, Salary)
    )
);

// Calculate duration
Set(varDuration3, DateDiff(varStartTime, Now(), Milliseconds));

Notify("Optimal approach completed in " & varDuration3 & "ms", NotificationType.Success)
```

#### Step 8: Performance Comparison Display

**Add label to show results:**

`lblPerformanceResults`:

```powerFx
"Performance Comparison:" & Char(10) & Char(10) &
"ForAll Loop: " & varDuration1 & " ms" & Char(10) &
"Collection Patch: " & varDuration2 & " ms" & Char(10) &
"Optimal Method: " & varDuration3 & " ms" & Char(10) & Char(10) &
"Speed Improvement: " & Round((varDuration1 - varDuration3) / varDuration1 * 100, 0) & "%"
```

#### Step 9: Real-World Use Cases

**Use Case 1: Bulk Status Update**

```powerFx
// Approve all pending leave requests
Patch(
    LeaveRequests,
    Filter(LeaveRequests, Status = "Pending"),
    {
        Status: "Approved",
        ApprovedBy: User().Email,
        ApprovedDate: Now()
    }
)
```

**Use Case 2: Conditional Updates**

```powerFx
// Update salary based on performance rating
ForAll(
    colEmployees,
    Patch(
        colEmployees,
        ThisRecord,
        {
            Salary: Switch(
                ThisRecord.PerformanceRating,
                "Excellent", ThisRecord.Salary * 1.15,
                "Good", ThisRecord.Salary * 1.10,
                "Average", ThisRecord.Salary * 1.05,
                ThisRecord.Salary  // No change for others
            )
        }
    )
)
```

**Use Case 3: Update with Lookup from Another Table**

```powerFx
// Update employee department based on new assignments
Patch(
    Employees,
    LookUp(Employees, Email = User().Email),
    {
        Department: LookUp(
            DepartmentAssignments,
            EmployeeEmail = User().Email
        ).NewDepartment
    }
)
```

#### Step 10: Best Practices

**✅ DO:**

- Use Patch for single record updates
- Use collection operations for bulk updates
- Batch multiple field updates in one Patch call
- Use Defaults() when creating new records
- Handle errors with IfError()

**❌ DON'T:**

- Use ForAll loop with Patch for large datasets
- Patch inside nested ForAll loops
- Patch the same record multiple times sequentially
- Forget to handle Patch failures

**Error Handling:**

```powerFx
IfError(
    Patch(
        Employees,
        LookUp(Employees, ID = varEmployeeID),
        {
            Name: txtName.Text,
            Department: ddnDepartment.Selected.Value
        }
    ),
    Notify(
        "Failed to update: " & FirstError.Message,
        NotificationType.Error
    ),
    Notify("Updated successfully", NotificationType.Success)
)
```

### Assignment Deliverable

✅ Create demonstration app showing:

- Patch for Create operation
- Patch for Update operation
- Patch for Delete operation
- Performance comparison: ForAll vs Collection Patch
- Visible time difference with timing measurements
- At least 3 real-world use case examples

✅ Create test with at least 50 records to see performance difference

### Knowledge Check

- [ ] What's the syntax for creating a new record with Patch?
- [ ] How do you update multiple fields in one Patch operation?
- [ ] Why is ForAll + Patch slower than collection operations?
- [ ] What function do you use to soft-delete records?
- [ ] How do you handle Patch errors?

### Patch Quick Reference

| Operation           | Syntax                                                   |
| ------------------- | -------------------------------------------------------- |
| **Create**          | `Patch(Source, Defaults(Source), {Field: Value})`        |
| **Update**          | `Patch(Source, LookUp(Source, ID=X), {Field: NewValue})` |
| **Update Multiple** | `Patch(Source, TableOfRecords, {Field: NewValue})`       |
| **Delete**          | `Remove(Source, LookUp(Source, ID=X))`                   |
| **Soft Delete**     | `Patch(Source, Record, {IsActive: false})`               |

### Resources

- [Microsoft Learn: Patch function](https://learn.microsoft.com/en-us/power-platform/power-fx/reference/function-patch)
- [Microsoft Learn: Remove function](https://learn.microsoft.com/en-us/power-platform/power-fx/reference/function-remove)
- [Blog: Patch vs ForAll Performance](https://www.matthewdevaney.com/power-apps-patch-function-explained/)

---

## Module 9: Collections (1 hour)

### Learning Objectives

- Master collection operations and formulas
- Understand when to use collections vs. data sources
- Work with nested collections and complex data
- Integrate collections with gallery controls

### Step-by-Step Instructions

#### Step 1: Understanding Collections

**What are Collections?**

- Temporary, in-memory data tables
- Exist only during app session
- Fast access and manipulation
- No delegation limits

**When to Use Collections:**

- Temporary data storage
- Offline scenarios
- Complex data transformations
- Performance optimization (cache data)
- Multi-step forms

**When NOT to Use Collections:**

- Permanent data storage (use data sources)
- Large datasets (memory limitations)
- Shared data across users

#### Step 2: Create Practice App

1. Create new Canvas app: `CollectionsPracticeApp`
2. Rename Screen1 to `scrCollections`

#### Step 3: Collection Creation Methods

**A. ClearCollect - Replace entire collection**

Add button `btnClearCollect`:

```powerFx
ClearCollect(
    colProducts,
    {ID: 1, Name: "Laptop", Category: "Electronics", Price: 999, InStock: true},
    {ID: 2, Name: "Mouse", Category: "Electronics", Price: 25, InStock: true},
    {ID: 3, Name: "Desk Chair", Category: "Furniture", Price: 299, InStock: false},
    {ID: 4, Name: "Monitor", Category: "Electronics", Price: 399, InStock: true},
    {ID: 5, Name: "Keyboard", Category: "Electronics", Price: 79, InStock: true}
);

Notify("Collection created: " & CountRows(colProducts) & " items", NotificationType.Success)
```

**B. Collect - Add to existing collection**

Add button `btnCollect`:

```powerFx
Collect(
    colProducts,
    {ID: 6, Name: "Desk Lamp", Category: "Furniture", Price: 45, InStock: true}
);

Notify("Item added. Total: " & CountRows(colProducts), NotificationType.Success)
```

**C. Collect from Data Source**

```powerFx
// Collect all records from SharePoint list
ClearCollect(colEmployees, Employees)

// Collect filtered records
ClearCollect(
    colActiveEmployees,
    Filter(Employees, IsActive = true)
)

// Collect with columns
ClearCollect(
    colEmployeeNames,
    ShowColumns(Employees, "ID", "Name", "Email")
)
```

#### Step 4: Collection Manipulation

**A. AddColumns - Add calculated fields**

Add button `btnAddColumns`:

```powerFx
ClearCollect(
    colProductsExtended,
    AddColumns(
        colProducts,
        "DiscountPrice", Price * 0.9,
        "Tax", Price * 0.08,
        "TotalPrice", Price * 1.08,
        "StockStatus", If(InStock, "Available", "Out of Stock")
    )
);

Notify("Columns added", NotificationType.Success)
```

**Display in gallery to see results**

**B. DropColumns - Remove fields**

```powerFx
ClearCollect(
    colProductsSimple,
    DropColumns(colProducts, "InStock", "Price")
)
```

**C. ShowColumns - Keep only specific fields**

```powerFx
ClearCollect(
    colProductNames,
    ShowColumns(colProducts, "ID", "Name")
)
```

**D. RenameColumns - Change field names**

```powerFx
ClearCollect(
    colProductsRenamed,
    RenameColumns(
        colProducts,
        "Name", "ProductName",
        "Price", "Cost"
    )
)
```

#### Step 5: Filtering and Sorting Collections

**A. Filter**

Add dropdown `ddnCategoryFilter`:

- Items: `Distinct(colProducts, Category)`

Add gallery `galFilteredProducts`:

```powerFx
// Items property
Filter(
    colProducts,
    Category = ddnCategoryFilter.Selected.Value
)

// Multiple conditions
Filter(
    colProducts,
    Category = ddnCategoryFilter.Selected.Value &&
    Price < 500 &&
    InStock = true
)
```

**B. Sort**

```powerfx
// Sort ascending
Sort(colProducts, Price, SortOrder.Ascending)

// Sort descending
Sort(colProducts, Price, SortOrder.Descending)

// Sort by multiple fields
SortByColumns(
    colProducts,
    "Category", SortOrder.Ascending,
    "Price", SortOrder.Descending
)
```

**C. Search**

Add text input `txtSearch`:

Gallery items:

```powerFx
Search(
    colProducts,
    txtSearch.Text,
    "Name", "Category"
)
```

#### Step 6: Advanced Collection Operations

**A. GroupBy**

```powerFx
// Group products by category
ClearCollect(
    colProductsByCategory,
    GroupBy(colProducts, "Category", "Items")
);

// With calculations
ClearCollect(
    colCategoryStats,
    AddColumns(
        GroupBy(colProducts, "Category", "Items"),
        "TotalItems", CountRows(Items),
        "TotalValue", Sum(Items, Price),
        "AvgPrice", Average(Items, Price)
    )
)
```

**Display grouped data in gallery:**

Gallery Items: `colCategoryStats`

Inside gallery:

- `lblCategory.Text`: `ThisItem.Category`
- `lblCount.Text`: `"Items: " & ThisItem.TotalItems`
- `lblValue.Text`: `"Value: $" & Text(ThisItem.TotalValue, "#,##0")`

**B. Ungroup**

```powerFx
// Flatten grouped collection back
ClearCollect(
    colProductsFlat,
    Ungroup(colProductsByCategory, "Items")
)
```

**C. Distinct**

```powerFx
// Get unique categories
ClearCollect(
    colCategories,
    Distinct(colProducts, Category)
)

// Use in dropdown
ddnCategory.Items: Distinct(colProducts, Category)
```

#### Step 7: Nested Collections

**Create complex nested data:**

```powerFx
ClearCollect(
    colOrders,
    {
        OrderID: 1,
        CustomerName: "John Smith",
        OrderDate: Date(2025, 11, 1),
        Items: Table(
            {Product: "Laptop", Quantity: 1, Price: 999},
            {Product: "Mouse", Quantity: 2, Price: 25}
        )
    },
    {
        OrderID: 2,
        CustomerName: "Sarah Johnson",
        OrderDate: Date(2025, 11, 5),
        Items: Table(
            {Product: "Monitor", Quantity: 1, Price: 399},
            {Product: "Keyboard", Quantity: 1, Price: 79},
            {Product: "Mouse", Quantity: 1, Price: 25}
        )
    }
)
```

**Access nested data:**

```powerFx
// In gallery showing orders
lblOrderTotal.Text:
    "Total: $" & Sum(ThisItem.Items, Price * Quantity)

lblItemCount.Text:
    "Items: " & CountRows(ThisItem.Items)

// Nested gallery for items
galOrderItems.Items: ThisItem.Items
```

#### Step 8: Collection Aggregations

**Sum:**

```powerFx
"Total Inventory Value: $" & Sum(colProducts, Price)
```

**Average:**

```powerFx
"Average Product Price: $" & Round(Average(colProducts, Price), 2)
```

**Count:**

```powerFx
"Total Products: " & CountRows(colProducts)
"In Stock: " & CountIf(colProducts, InStock = true)
"Out of Stock: " & CountIf(colProducts, InStock = false)
```

**Min/Max:**

```powerFx
"Cheapest: $" & Min(colProducts, Price)
"Most Expensive: $" & Max(colProducts, Price)

// Get product with min/max
"Cheapest Product: " & First(Sort(colProducts, Price, Ascending)).Name
"Most Expensive: " & First(Sort(colProducts, Price, Descending)).Name
```

#### Step 9: Update Collection Records

**A. UpdateIf - Conditional update**

```powerFx
// Increase all Electronics prices by 10%
UpdateIf(
    colProducts,
    Category = "Electronics",
    {Price: Price * 1.10}
);

// Mark out of stock items
UpdateIf(
    colProducts,
    Price > 500,
    {InStock: false}
)
```

**B. Remove and RemoveIf**

```powerFx
// Remove specific record
Remove(
    colProducts,
    LookUp(colProducts, ID = 3)
);

// Remove all matching records
RemoveIf(
    colProducts,
    InStock = false
)
```

**C. Clear**

```powerFx
// Delete entire collection
Clear(colProducts)
```

#### Step 10: Collection Integration with Gallery

**Complete example with all operations:**

1. **Create Gallery** - `galProducts`

   - Items: `colProducts`

2. **Add Search Box** - `txtProductSearch`

3. **Add Category Filter** - `ddnCategoryFilter`

   - Items: `["All", "Electronics", "Furniture"]`

4. **Add Sort Options** - `ddnSort`

   - Items: `["Name A-Z", "Name Z-A", "Price Low-High", "Price High-Low"]`

5. **Gallery Items Formula:**

```powerFx
// Complex filtering, searching, and sorting
With(
    {
        filtered: If(
            ddnCategoryFilter.Selected.Value = "All",
            colProducts,
            Filter(colProducts, Category = ddnCategoryFilter.Selected.Value)
        ),
        searched: If(
            IsBlank(txtProductSearch.Text),
            filtered,
            Search(filtered, txtProductSearch.Text, "Name", "Category")
        )
    },
    Switch(
        ddnSort.Selected.Value,
        "Name A-Z", Sort(searched, Name, Ascending),
        "Name Z-A", Sort(searched, Name, Descending),
        "Price Low-High", Sort(searched, Price, Ascending),
        "Price High-Low", Sort(searched, Price, Descending),
        searched
    )
)
```

#### Step 11: Collection Persistence Patterns

**Save to Temporary Storage:**

```powerFx
// Save to collection for offline use
ClearCollect(
    colEmployeesCache,
    Employees
);

// Work with cache
Set(varLastSync, Now())
```

**Multi-Step Form Pattern:**

```powerFx
// Step 1: Collect basic info
Collect(
    colFormData,
    {
        Step: 1,
        Name: txtName.Text,
        Email: txtEmail.Text
    }
);

// Step 2: Add more data
Patch(
    colFormData,
    First(colFormData),
    {
        Step: 2,
        Address: txtAddress.Text,
        Phone: txtPhone.Text
    }
);

// Final step: Save to data source
Patch(
    Employees,
    Defaults(Employees),
    First(colFormData)
);
Clear(colFormData)
```

### Assignment Deliverable

✅ Create a comprehensive app demonstrating:

- Collection creation with ClearCollect and Collect
- AddColumns for calculated fields
- Filter, Sort, Search operations
- GroupBy with statistics
- Nested collections
- Integration with gallery control
- Update, Remove operations
- Real-world use case (shopping cart or task list)

✅ Build gallery with:

- Dynamic filtering
- Search functionality
- Sorting options
- Item count and aggregations

### Knowledge Check

- [ ] What's the difference between Collect and ClearCollect?
- [ ] How do you add a calculated column to a collection?
- [ ] What does GroupBy return?
- [ ] How do you filter a collection by multiple criteria?
- [ ] What's the syntax for updating records conditionally?
- [ ] How do you access nested collection data?

### Collection Functions Quick Reference

| Function         | Purpose                   | Example                             |
| ---------------- | ------------------------- | ----------------------------------- |
| **ClearCollect** | Create/replace collection | `ClearCollect(col, Table(...))`     |
| **Collect**      | Add to collection         | `Collect(col, {Field: Value})`      |
| **Clear**        | Empty collection          | `Clear(col)`                        |
| **AddColumns**   | Add calculated fields     | `AddColumns(col, "New", Field*2)`   |
| **DropColumns**  | Remove fields             | `DropColumns(col, "Field1")`        |
| **ShowColumns**  | Keep specific fields      | `ShowColumns(col, "ID", "Name")`    |
| **Filter**       | Filter records            | `Filter(col, Price > 100)`          |
| **Search**       | Text search               | `Search(col, "text", "Field")`      |
| **Sort**         | Sort records              | `Sort(col, Field, Ascending)`       |
| **GroupBy**      | Group records             | `GroupBy(col, "Field", "Items")`    |
| **UpdateIf**     | Update conditionally      | `UpdateIf(col, ID=1, {Field: Val})` |
| **RemoveIf**     | Remove conditionally      | `RemoveIf(col, Price > 1000)`       |

### Resources

- [Microsoft Learn: Collections in Power Apps](https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/create-update-collection)
- [Microsoft Learn: Collection functions](https://learn.microsoft.com/en-us/power-platform/power-fx/reference/function-clear-collect-clearcollect)
- [Power Apps Collections Guide](https://www.matthewdevaney.com/power-apps-collections-cookbook/)

---

## Module 10: Gallery Control (2 hours)

### Learning Objectives

- Master flexible height gallery control
- Implement sort, filter, and search functionality
- Create responsive grid layouts
- Handle gallery selection and navigation
- Build custom gallery templates

### Step-by-Step Instructions

#### Step 1: Understanding Gallery Types

**Gallery Types:**

1. **Vertical Gallery** - Scrolls vertically, fixed item height
2. **Horizontal Gallery** - Scrolls horizontally
3. **Flexible Height Gallery** - Items adjust height based on content
4. **Grid Gallery** - Multiple columns (wrap container)

**When to Use Each:**

- **Vertical**: Lists, feeds, typical item lists
- **Horizontal**: Image carousels, category tabs
- **Flexible Height**: Content with varying text lengths
- **Grid**: Product catalogs, image galleries

#### Step 2: Create Practice App

1. Create new Canvas app: `GalleryPracticeApp`
2. Create sample data collection

Add button `btnCreateData`:

```powerFx
ClearCollect(
    colTasks,
    {
        ID: 1,
        Title: "Complete project proposal",
        Description: "Draft and finalize the Q4 project proposal for management review",
        Category: "Work",
        Priority: "High",
        DueDate: Date(2025, 11, 15),
        Status: "In Progress",
        AssignedTo: "John Smith",
        CompletionPercent: 60
    },
    {
        ID: 2,
        Title: "Team meeting",
        Description: "Weekly sync with development team",
        Category: "Meeting",
        Priority: "Medium",
        DueDate: Date(2025, 11, 13),
        Status: "Pending",
        AssignedTo: "Sarah Johnson",
        CompletionPercent: 0
    },
    {
        ID: 3,
        Title: "Code review",
        Description: "Review pull requests from team members. Check for code quality, security issues, and best practices compliance.",
        Category: "Development",
        Priority: "High",
        DueDate: Date(2025, 11, 12),
        Status: "Pending",
        AssignedTo: "Mike Wilson",
        CompletionPercent: 0
    },
    {
        ID: 4,
        Title: "Update documentation",
        Description: "Update API documentation",
        Category: "Documentation",
        Priority: "Low",
        DueDate: Date(2025, 11, 20),
        Status: "Completed",
        AssignedTo: "Emily Davis",
        CompletionPercent: 100
    },
    {
        ID: 5,
        Title: "Client presentation",
        Description: "Present new features to client stakeholders. Prepare demo environment and talking points.",
        Category: "Client",
        Priority: "High",
        DueDate: Date(2025, 11, 14),
        Status: "In Progress",
        AssignedTo: "David Brown",
        CompletionPercent: 40
    }
)
```

#### Step 3: Build Basic Vertical Gallery

1. **Insert Gallery:**

   - Insert → Gallery → Blank vertical
   - Rename: `galTasks`
   - **Properties:**
     - Items: `colTasks`
     - TemplatePadding: `8`
     - TemplateSize: `120`

2. **Design Template:**

Select gallery, then add controls inside:

**A. Background Rectangle:**

```powerFx
Name: recTaskCard
X: 0
Y: 0
Width: Parent.TemplateWidth
Height: Parent.TemplateHeight - 8
Fill: RGBA(255, 255, 255, 1)
BorderColor: RGBA(225, 223, 221, 1)
BorderThickness: 1
RadiusTopLeft: 8
RadiusTopRight: 8
RadiusBottomLeft: 8
RadiusBottomRight: 8
```

**B. Priority Indicator (Color Bar):**

```powerFx
Name: recPriorityBar
X: 0
Y: 0
Width: 6
Height: recTaskCard.Height
Fill: Switch(
    ThisItem.Priority,
    "High", RGBA(196, 49, 75, 1),
    "Medium", RGBA(255, 185, 0, 1),
    "Low", RGBA(16, 124, 16, 1),
    RGBA(150, 150, 150, 1)
)
BorderThickness: 0
RadiusTopLeft: 8
RadiusBottomLeft: 8
```

**C. Title Label:**

```powerFx
Name: lblTaskTitle
Text: ThisItem.Title
X: 20
Y: 12
Width: Parent.TemplateWidth - 150
Height: 30
Color: RGBA(50, 49, 48, 1)
Size: 16
FontWeight: FontWeight.Semibold
```

**D. Description Label:**

```powerFx
Name: lblTaskDescription
Text: ThisItem.Description
X: 20
Y: lblTaskTitle.Y + lblTaskTitle.Height + 4
Width: Parent.TemplateWidth - 40
Height: 40
Color: RGBA(96, 94, 92, 1)
Size: 12
```

**E. Status Badge:**

```powerFx
Name: lblStatus
Text: ThisItem.Status
X: Parent.TemplateWidth - Self.Width - 16
Y: 12
Width: 100
Height: 26
Color: Switch(
    ThisItem.Status,
    "Completed", RGBA(34, 139, 34, 1),        // Success green
    "In Progress", RGBA(13, 110, 253, 1),       // Info blue
    "Pending", RGBA(255, 193, 7, 1),           // Warning amber
    RGBA(69, 71, 77, 1)                        // Secondary text
)
Fill: Switch(
    ThisItem.Status,
    "Completed", ColorFade(RGBA(34, 139, 34, 1), 85%),
    "In Progress", ColorFade(RGBA(13, 110, 253, 1), 85%),
    "Pending", ColorFade(RGBA(255, 193, 7, 1), 85%),
    RGBA(248, 249, 250, 1)                     // Light background
)
Size: 11
FontWeight: FontWeight.Semibold
Align: Align.Center
VerticalAlign: VerticalAlign.Middle
RadiusTopLeft: 4
RadiusTopRight: 4
RadiusBottomLeft: 4
RadiusBottomRight: 4
```

**F. Metadata Row:**

```powerFx
Name: lblMetadata
Text: "📅 " & Text(ThisItem.DueDate, "mmm dd") &
      " | 👤 " & ThisItem.AssignedTo &
      " | " & ThisItem.Category
X: 20
Y: Parent.TemplateHeight - Self.Height - 12
Width: Parent.TemplateWidth - 40
Height: 24
Color: RGBA(96, 94, 92, 1)
Size: 11
```

#### Step 4: Implement Search Functionality

**Add Search Input:**

```powerFx
Name: txtTaskSearch
HintText: "Search tasks..."
X: 40
Y: 120
Width: 400
Height: 40
```

**Add Search Icon:**

```powerFx
Name: icoSearch
Icon: Icon.Search
X: txtTaskSearch.X - 32
Y: txtTaskSearch.Y + 5
Width: 30
Height: 30
Color: RGBA(96, 94, 92, 1)
```

**Update Gallery Items:**

```powerFx
Search(
    colTasks,
    txtTaskSearch.Text,
    "Title", "Description", "AssignedTo", "Category"
)
```

#### Step 5: Implement Filter Functionality

**Add Category Filter Dropdown:**

```powerFx
Name: ddnCategoryFilter
Items: Concat(
    Distinct(colTasks, Category),
    Result & ", "
)
// Or use Table:
// ["All", "Work", "Meeting", "Development", "Documentation", "Client"]
DefaultSelectedItems: ["All"]
X: txtTaskSearch.X + txtTaskSearch.Width + 16
Y: txtTaskSearch.Y
Width: 200
Height: 40
```

**Add Status Filter:**

```powerFx
Name: ddnStatusFilter
Items: ["All", "Pending", "In Progress", "Completed"]
Default: "All"
```

**Add Priority Filter:**

```powerFx
Name: ddnPriorityFilter
Items: ["All", "High", "Medium", "Low"]
Default: "All"
```

**Update Gallery Items with Filters:**

```powerFx
With(
    {
        // Apply search
        searched: If(
            IsBlank(txtTaskSearch.Text),
            colTasks,
            Search(
                colTasks,
                txtTaskSearch.Text,
                "Title", "Description", "AssignedTo", "Category"
            )
        )
    },
    // Apply filters
    Filter(
        searched,
        (ddnCategoryFilter.Selected.Value = "All" ||
         Category = ddnCategoryFilter.Selected.Value) &&
        (ddnStatusFilter.Selected.Value = "All" ||
         Status = ddnStatusFilter.Selected.Value) &&
        (ddnPriorityFilter.Selected.Value = "All" ||
         Priority = ddnPriorityFilter.Selected.Value)
    )
)
```

#### Step 6: Implement Sort Functionality

**Add Sort Dropdown:**

```powerFx
Name: ddnSort
Items: [
    "Due Date (Earliest)",
    "Due Date (Latest)",
    "Priority (High to Low)",
    "Title (A-Z)",
    "Title (Z-A)",
    "Status"
]
Default: "Due Date (Earliest)"
```

**Update Gallery Items with Sort:**

```powerFx
With(
    {
        searched: If(
            IsBlank(txtTaskSearch.Text),
            colTasks,
            Search(colTasks, txtTaskSearch.Text, "Title", "Description")
        ),
        filtered: Filter(
            searched,
            (ddnCategoryFilter.Selected.Value = "All" ||
             Category = ddnCategoryFilter.Selected.Value) &&
            (ddnStatusFilter.Selected.Value = "All" ||
             Status = ddnStatusFilter.Selected.Value) &&
            (ddnPriorityFilter.Selected.Value = "All" ||
             Priority = ddnPriorityFilter.Selected.Value)
        )
    },
    Switch(
        ddnSort.Selected.Value,
        "Due Date (Earliest)",
            Sort(filtered, DueDate, Ascending),
        "Due Date (Latest)",
            Sort(filtered, DueDate, Descending),
        "Priority (High to Low)",
            SortByColumns(
                filtered,
                "Priority", Descending,
                "DueDate", Ascending
            ),
        "Title (A-Z)",
            Sort(filtered, Title, Ascending),
        "Title (Z-A)",
            Sort(filtered, Title, Descending),
        "Status",
            Sort(filtered, Status, Ascending),
        filtered
    )
)
```

#### Step 7: Build Flexible Height Gallery

**Create new screen for flexible height demo:**

1. **Insert Flexible Height Gallery:**

   - Insert → Gallery → Blank flexible height
   - Rename: `galFlexibleTasks`

2. **Key Difference:**

   - No fixed `TemplateSize`
   - Items arrange height dynamically based on content

3. **Design Template with Auto-Height:**

**Title (auto-wraps):**

```powerFx
Name: lblFlexTitle
Text: ThisItem.Title
AutoHeight: true  // KEY PROPERTY
Width: Parent.TemplateWidth - 40
```

**Description (auto-wraps):**

```powerFx
Name: lblFlexDescription
Text: ThisItem.Description
Y: lblFlexTitle.Y + lblFlexTitle.Height + 8
AutoHeight: true  // KEY PROPERTY
Width: Parent.TemplateWidth - 40
```

**Container Height Adjustment:**

```powerFx
recFlexCard.Height:
    lblFlexDescription.Y + lblFlexDescription.Height + 50
```

#### Step 8: Create Grid Layout (Wrap Container)

**For multi-column layout:**

1. **Change Gallery:**

   - TemplateSize: `300` (width of each item)
   - WrapCount: `3` (number of columns)
   - Layout: Horizontal

2. **Design Square Cards:**

```powerFx
recGridCard:
    Width: Parent.TemplateWidth - 16
    Height: Parent.TemplateWidth - 16  // Square

imgTaskCategory:
    Width: Parent.Width
    Height: 150

lblGridTitle:
    Y: imgTaskCategory.Y + imgTaskCategory.Height + 12
    Width: Parent.Width - 24
    Align: Center
```

#### Step 9: Gallery Selection and Navigation

**A. OnSelect Navigation:**

Add to gallery template (e.g., recTaskCard):

```powerFx
OnSelect:
    Set(varSelectedTask, ThisItem);
    Navigate(scrTaskDetail, ScreenTransition.Cover)
```

**B. Selection Highlight:**

```powerFx
recTaskCard.Fill:
    If(
        ThisItem.ID = varSelectedTask.ID,
        ColorFade(RGBA(73, 76, 84, 1), 95%),  // Selected
        RGBA(255, 255, 255, 1)  // Normal
    )

recTaskCard.BorderColor:
    If(
        ThisItem.ID = varSelectedTask.ID,
        RGBA(73, 76, 84, 1),  // Selected border
        RGBA(225, 223, 221, 1)  // Normal border
    )
```

**C. Multi-Select Gallery:**

```powerFx
// Enable multi-select
galTasks.SelectMultiple: true

// Check if selected
chkSelect.Default: galTasks.Selected

// Get all selected items
varSelectedTasks: galTasks.AllItems
```

#### Step 10: Advanced Gallery Features

**A. Empty State:**

Add label above/below gallery:

```powerFx
Name: lblEmptyState
Text: If(
    CountRows(galTasks.AllItems) = 0,
    "No tasks found. Try adjusting your filters.",
    ""
)
Visible: CountRows(galTasks.AllItems) = 0
Y: (Parent.Height - Self.Height) / 2
Width: Parent.Width
Align: Center
Size: 18
Color: RGBA(150, 150, 150, 1)
```

**B. Item Counter:**

```powerFx
lblTaskCount.Text:
    "Showing " & CountRows(galTasks.AllItems) &
    " of " & CountRows(colTasks) & " tasks"
```

**C. Load More (Pagination):**

```powerFx
// Set page size
Set(varPageSize, 10);
Set(varCurrentPage, 1);

// Gallery items with pagination
FirstN(
    <your filtered/sorted collection>,
    varPageSize * varCurrentPage
)

// Load More button
btnLoadMore.OnSelect:
    Set(varCurrentPage, varCurrentPage + 1)

// Show/hide button
btnLoadMore.Visible:
    CountRows(galTasks.AllItems) < CountRows(<full collection>)
```

**D. Pull to Refresh:**

```powerFx
// Not directly supported, but simulate:
btnRefresh.OnSelect:
    // Show loading spinner
    Set(varIsLoading, true);

    // Refresh data
    ClearCollect(colTasks, TasksDataSource);

    // Hide spinner
    Set(varIsLoading, false);
    Notify("Data refreshed", NotificationType.Success)
```

### Assignment Deliverable

✅ Build a comprehensive gallery with:

- Flexible height layout
- Search across multiple fields
- Filter by at least 2 criteria
- Sort by at least 3 different options
- Custom styled template
- Selection highlighting
- Empty state message
- Item counter
- Navigation to detail screen

✅ Demonstrate visible differences with different gallery types

### Knowledge Check

- [ ] What's the difference between fixed and flexible height galleries?
- [ ] How do you make labels auto-adjust their height?
- [ ] What property controls the number of columns in a grid?
- [ ] How do you access the selected item in a gallery?
- [ ] What's the formula for combining search, filter, and sort?

### Gallery Pattern Examples

**Master-Detail Pattern:**

```powerFx
// Master gallery
galTasks.OnSelect: Set(varSelectedTask, ThisItem)

// Detail screen labels
lblDetailTitle.Text: varSelectedTask.Title
lblDetailDescription.Text: varSelectedTask.Description
```

**Infinite Scroll:**

```powerFx
galTasks.Items: FirstN(colAllTasks, varPageSize * varCurrentPage)
btnLoadMore: Visible: CountRows(galTasks.AllItems) = varPageSize * varCurrentPage
```

**Search + Filter + Sort Pattern:**

```powerFx
With({
    base: colData,
    searched: Search(base, txtSearch.Text, "Field1", "Field2"),
    filtered: Filter(searched, Category = ddnCategory.Selected.Value),
    sorted: Sort(filtered, ddnSortField.Selected.Value, ddnSortOrder.Selected.Value)
}, sorted)
```

### Resources

- [Microsoft Learn: Gallery control](https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/controls/control-gallery)
- [Microsoft Learn: Flexible height gallery](https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/controls/control-gallery#flexible-height-gallery)
- [Video 1: Gallery Basics](https://www.youtube.com/watch?v=example1)
- [Video 2: Advanced Gallery Techniques](https://www.youtube.com/watch?v=example2)

---

## Stage 2 Part 2 Summary

### What You've Accomplished

✅ Mastered Patch function for efficient data operations
✅ Comprehensive understanding of collection manipulation
✅ Built advanced gallery controls with search, filter, and sort
✅ Performance optimization techniques

### Total Time: 4 hours (of 10 hours in Part 2)

### Modules Completed

- Module 8: Patch (1 hour) ✓
- Module 9: Collections (1 hour) ✓
- Module 10: Gallery Control (2 hours) ✓

### Remaining Modules in Stage 2

- Module 11: Components (2 hours)
- Module 12: Modern Controls (8 hours) - This is a large module
- Module 13: Performance Optimization (2 hours)

These will be covered when you're ready.

---

**Type "NEXT" to continue with Components, Modern Controls, and Performance Optimization.**
