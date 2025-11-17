# Power Platform Training Manual - Stage 3: Intermediate & Advanced Canvas Apps

## Overview

This stage covers advanced Canvas Apps concepts including delegation deep-dive, concurrency, exception handling, deep linking, sharing strategies, offline capabilities, monitoring tools, and comprehensive responsive design. **Duration: 36 hours**

---

## Module 14: Delegation (5 hours)

### Learning Objectives

- Deep understanding of delegation architecture
- Identify delegable vs non-delegable operations
- Work effectively with different data sources
- Fix delegation warnings systematically
- Design apps for large datasets
- Test delegation properly

### Step-by-Step Instructions

#### Step 1: Understanding Delegation Architecture

**What is Delegation?**

- Power Apps sends query logic to the data source
- Data source processes the query server-side
- Only filtered/sorted results returned to app
- Efficient for millions of records

**Without Delegation (Local Processing):**

```
1. Power Apps requests first 500-2000 records
2. Data source sends records to app
3. Power Apps filters/sorts in app memory
4. Results shown (only from first 500-2000!)
5. ⚠️ Rest of data ignored silently
```

**With Delegation (Server Processing):**

```
1. Power Apps sends query: "Get all IT employees"
2. Data source executes query on server
3. Only matching records sent to app
4. All matching records included (millions if needed)
5. ✅ Complete, accurate results
```

**Why Delegation Matters:**

- Apps work with ANY data volume
- Fast performance (server-side processing)
- Accurate results (no missing data)
- Scalable solution

#### Step 2: Data Row Limit

**Settings → Advanced → Data row limit for non-delegable queries**

- Default: 500
- Maximum: 2000
- This is a **limit**, not a feature!

**What it means:**

```powerFx
// If you have 10,000 employees
// And use non-delegable query
Filter(Employees, Name Contains "Smith")

// With limit = 500:
// Only first 500 employees checked
// If "Smith" is employee #600, won't appear!

// With limit = 2000:
// Only first 2000 employees checked
// Still missing 8,000 records!
```

**❌ WRONG Solution:**

```
"I have delegation warning, so I'll increase limit to 2000"
// This doesn't fix the problem!
```

**✅ CORRECT Solution:**

```
"I have delegation warning, so I'll fix my formula to be delegable"
```

#### Step 3: Create Delegation Testing Environment

**A. Create Test App:**

1. Create new Canvas app: `DelegationTestApp`
2. Format: Tablet

**B. Create Large Test Dataset:**

```powerFx
// Button: btnCreateLargeDataset
// Creates collection with 2500 records (more than default limit)
ClearCollect(
    colEmployees,
    ForAll(
        Sequence(2500),
        {
            ID: Value,
            FirstName: Switch(
                Mod(Value, 10),
                0, "John",
                1, "Sarah",
                2, "Michael",
                3, "Emily",
                4, "David",
                5, "Jessica",
                6, "James",
                7, "Jennifer",
                8, "Robert",
                "Linda"
            ),
            LastName: "Employee" & Value,
            Email: "employee" & Value & "@company.com",
            Department: Switch(
                Mod(Value, 5),
                0, "Engineering",
                1, "Sales",
                2, "Marketing",
                3, "HR",
                "Finance"
            ),
            Salary: 50000 + (Mod(Value, 100) * 1000),
            City: Switch(
                Mod(Value, 8),
                0, "New York",
                1, "Los Angeles",
                2, "Chicago",
                3, "Houston",
                4, "Phoenix",
                5, "Philadelphia",
                6, "San Antonio",
                "San Diego"
            ),
            Status: If(Mod(Value, 10) = 0, "Inactive", "Active"),
            HireDate: DateAdd(Date(2015, 1, 1), Mod(Value, 3650), Days),
            PerformanceScore: Mod(Value, 100) + 1
        }
    )
);

Notify("Created " & CountRows(colEmployees) & " records", NotificationType.Success)
```

**C. Test Data Row Limit Impact:**

```powerFx
// Set data row limit to 500 in Settings
// Then test:

// Label: lblTotalRecords
Text: "Total records in collection: " & CountRows(colEmployees)
// Shows: 2500

// Label: lblFilteredRecords
Text: "Records matching filter: " &
      CountRows(Filter(colEmployees, FirstName = "Robert"))
// Expected: 250 (10% of 2500)
// Actual with non-delegable: Only those in first 500 records!
```

#### Step 4: Delegable Functions by Data Source

**A. SharePoint Lists:**

**Delegable:**

```powerFx
// Comparison operators
Filter(SharePointList, NumberField = 100)
Filter(SharePointList, NumberField < 100)
Filter(SharePointList, NumberField > 100)
Filter(SharePointList, NumberField <= 100)
Filter(SharePointList, NumberField >= 100)
Filter(SharePointList, NumberField <> 100)

// Text functions
Filter(SharePointList, StartsWith(TextField, "John"))
Filter(SharePointList, TextField = "John Smith")

// Logical operators
Filter(SharePointList, Status = "Active" And Department = "IT")
Filter(SharePointList, Status = "Active" Or Status = "Pending")

// In operator
Filter(SharePointList, ID In [1, 2, 3, 4, 5])

// IsBlank
Filter(SharePointList, IsBlank(ManagerField))
Filter(SharePointList, Not(IsBlank(ManagerField)))

// Date comparisons
Filter(SharePointList, DateField >= Today())
Filter(SharePointList, DateField = Today())
```

**NOT Delegable:**

```powerFx
// Text functions
Filter(SharePointList, TextField Contains "John")  // ❌
Filter(SharePointList, TextField EndsWith "Smith")  // ❌
Filter(SharePointList, Lower(TextField) = "john")  // ❌
Filter(SharePointList, Upper(TextField) = "JOHN")  // ❌
Filter(SharePointList, Len(TextField) > 10)  // ❌

// Mathematical operations in filter
Filter(SharePointList, Salary * 1.1 > 100000)  // ❌
Filter(SharePointList, Year(DateField) = 2025)  // ❌

// CountRows
CountRows(Filter(SharePointList, Status = "Active"))  // ❌
// Use CountIf instead: CountIf(SharePointList, Status = "Active")  // ✅

// Lookup columns (sometimes)
Filter(SharePointList, LookupColumn.Value = "Something")  // ❌ (often)

// Choice columns (sometimes)
Filter(SharePointList, ChoiceColumn.Value = "Option1")  // ❌ (often)
```

**B. Dataverse (Most Delegable):**

**Delegable (more than SharePoint):**

```powerFx
// Everything SharePoint supports, PLUS:

// Contains (only in Dataverse!)
Filter(DataverseTable, TextField Contains "John")  // ✅

// EndsWith (only in Dataverse!)
Filter(DataverseTable, TextField EndsWith "Smith")  // ✅

// Lookups (better support)
Filter(DataverseTable, LookupColumn.Name = "Value")  // ✅

// Choice columns (better support)
Filter(DataverseTable, ChoiceColumn = 'Choice Options'.Active)  // ✅

// More complex conditions
Filter(DataverseTable, (Status = "Active" Or Status = "Pending") And Department = "IT")  // ✅
```

**C. SQL Server:**

**Delegable:**

```powerFx
// Most operations delegable
// Similar to SharePoint but better
Filter(SQLTable, Column Contains "text")  // ✅
Filter(SQLTable, Column EndsWith "text")  // ✅
// Check specific documentation for your SQL connector
```

**D. Excel (NOT Delegable):**

```powerFx
// Excel does NOT support delegation at all!
// Maximum 2000 rows always
// ❌ Not recommended for large datasets
```

**E. Collections (NOT Delegable):**

```powerFx
// Collections are local, no server to delegate to
// All operations are non-delegable
// BUT: No limit! All data is local
// Can process thousands of records (if already loaded)
```

#### Step 5: Common Delegation Issues and Fixes

**Issue 1: Contains Text Search**

**❌ Problem:**

```powerFx
// Gallery items
Filter(
    SharePointEmployees,
    FirstName Contains txtSearch.Text
)
// ⚠️ Delegation warning! Only searches first 500 records
```

**✅ Solution 1: Use StartsWith**

```powerFx
Filter(
    SharePointEmployees,
    StartsWith(FirstName, txtSearch.Text)
)
// ✅ Delegable! Searches all records
// Note: Only finds names STARTING with search term
```

**✅ Solution 2: Use Search function**

```powerFx
Search(
    SharePointEmployees,
    txtSearch.Text,
    "FirstName", "LastName", "Email"
)
// ✅ Delegable for SharePoint and Dataverse
// Searches multiple fields
```

**✅ Solution 3: Cache and filter locally**

```powerFx
// If dataset is small (< 2000 records)
// Load once into collection
App.OnStart:
    ClearCollect(colEmployees, SharePointEmployees)

// Then filter collection (no delegation needed)
galEmployees.Items:
    Filter(
        colEmployees,
        FirstName Contains txtSearch.Text  // OK in collection
    )
```

**✅ Solution 4: Use Dataverse instead of SharePoint**

```powerFx
// Dataverse supports Contains delegation
Filter(
    DataverseEmployees,
    FirstName Contains txtSearch.Text
)
// ✅ No warning!
```

**Issue 2: Lookup Columns**

**❌ Problem:**

```powerFx
Filter(
    SharePointProjects,
    Manager.DisplayName = "John Smith"
)
// ⚠️ Lookup columns often not delegable
```

**✅ Solution: Filter by ID**

```powerFx
// First, get the Manager's ID
Set(varManagerID, LookUp(Managers, DisplayName = "John Smith").ID);

// Then filter by ID (delegable)
Filter(
    SharePointProjects,
    ManagerId = varManagerID
)
// ✅ Delegable!
```

**Issue 3: Date Calculations**

**❌ Problem:**

```powerFx
Filter(
    Employees,
    Year(HireDate) = 2025
)
// ⚠️ Year() function not delegable
```

**✅ Solution: Use date range**

```powerFx
Filter(
    Employees,
    HireDate >= Date(2025, 1, 1) &&
    HireDate < Date(2026, 1, 1)
)
// ✅ Delegable! Direct date comparison
```

**Issue 4: CountRows**

**❌ Problem:**

```powerFx
CountRows(Filter(Employees, Department = "IT"))
// ⚠️ CountRows with Filter is not delegable
```

**✅ Solution: Use CountIf**

```powerFx
CountIf(Employees, Department = "IT")
// ✅ Delegable!
```

**Issue 5: Multiple OR Conditions**

**❌ Problem:**

```powerfx
Filter(
    Employees,
    Department = "IT" Or
    Department = "Engineering" Or
    Department = "Development"
)
// Sometimes non-delegable with many OR
```

**✅ Solution: Use In**

```powerFx
Filter(
    Employees,
    Department In ["IT", "Engineering", "Development"]
)
// ✅ Delegable and cleaner!
```

**Issue 6: Mathematical Operations**

**❌ Problem:**

```powerFx
Filter(
    Employees,
    Salary * 1.1 > 100000
)
// ⚠️ Math in filter not delegable
```

**✅ Solution: Pre-calculate**

```powerFx
With(
    {threshold: 100000 / 1.1},
    Filter(
        Employees,
        Salary > threshold
    )
)
// ✅ Delegable! Math done before filter
```

#### Step 6: Delegation Testing Methodology

**A. Change Data Row Limit to 10:**

1. Settings → Advanced
2. Data row limit = 10
3. Save and test

**B. Test Your Filters:**

```powerFx
// If you have 2500 records
// And limit is set to 10
// And your gallery shows results beyond first 10
// Then delegation is working!

// Example:
// Create record with ID = 2000 (beyond limit)
Collect(colEmployees, {ID: 2000, FirstName: "TestUser", ...});

// Search for TestUser
txtSearch.Text = "TestUser"

// If TestUser appears in results = delegation works! ✅
// If TestUser doesn't appear = delegation broken! ❌
```

**C. Systematic Testing:**

```powerFx
// Test script
// 1. Set limit to 10
// 2. Add test record at position 2000
// 3. Search for that record
// 4. If found, delegation works
// 5. If not found, fix formula

// Label showing test result
lblDelegationTest.Text:
    If(
        CountRows(
            Filter(galEmployees.AllItems, ID = 2000)
        ) > 0,
        "✅ Delegation working! Record 2000 found.",
        "❌ Delegation broken! Record 2000 not found."
    )
```

#### Step 7: Delegation Design Patterns

**Pattern 1: Load and Cache Small Datasets**

```powerFx
// If data < 2000 records and rarely changes
App.OnStart:
    ClearCollect(colReferenceData, ReferenceDataSource)

// Use collection everywhere (no delegation needed)
ddnOptions.Items: colReferenceData
```

**Pattern 2: Search + Filter Combo**

```powerFx
// For large datasets
// Use delegable operations only

With(
    {
        searched: If(
            IsBlank(txtSearch.Text),
            SharePointData,
            Search(SharePointData, txtSearch.Text, "Name", "Email")
        )
    },
    Filter(
        searched,
        Status = ddnStatus.Selected.Value &&
        Department In ddnDepartments.SelectedItems.Value
    )
)
// All delegable operations
```

**Pattern 3: Progressive Filtering**

```powerFx
// Filter in stages, all delegable
With(
    {
        step1: Filter(Employees, Department = ddnDept.Selected.Value),
        step2: Filter(step1, Status = "Active"),
        step3: Filter(step2, HireDate >= dtpStartDate.SelectedDate)
    },
    step3
)
```

**Pattern 4: Hybrid Approach**

```powerFx
// Load filtered subset, then process locally
// Step 1: Get recent records (delegable)
ClearCollect(
    colRecentEmployees,
    Filter(Employees, HireDate >= DateAdd(Today(), -90, Days))
);

// Step 2: Complex processing on smaller dataset (non-delegable OK)
Filter(
    colRecentEmployees,
    FirstName Contains txtSearch.Text  // Now OK, small dataset
)
```

#### Step 8: Data Source Migration Strategy

**When to Migrate from SharePoint to Dataverse:**

**Stay with SharePoint if:**

- Dataset < 2000 records
- Simple filtering needs
- Already heavily integrated with SharePoint
- No complex lookups needed

**Migrate to Dataverse if:**

- Dataset > 2000 records (or growing)
- Need Contains/EndsWith delegation
- Complex relationships
- Need better lookup support
- Building scalable solution

**Migration Steps:**

1. Create Dataverse table matching SharePoint columns
2. Export SharePoint data to Excel
3. Import to Dataverse
4. Update Power Apps connection
5. Test delegation
6. Switch apps to use Dataverse

#### Step 9: Delegation Monitoring and Debugging

**A. Visual Indicators:**

```powerFx
// Show delegation status in app
lblDelegationStatus.Text:
    "Data row limit: 500" & Char(10) &
    "Records in view: " & CountRows(galEmployees.AllItems) & Char(10) &
    If(
        CountRows(galEmployees.AllItems) >= 500,
        "⚠️ May be hitting delegation limit!",
        "✅ Within limit"
    )
```

**B. Test Data Indicator:**

```powerFx
// Add test record at end of dataset
// Show if it appears
lblTestRecord.Text:
    If(
        CountRows(Filter(galEmployees.AllItems, ID = 2500)) > 0,
        "✅ Test record visible (delegation OK)",
        "❌ Test record missing (delegation issue)"
    )
```

**C. Formula Documentation:**

```powerFx
// Comment your formulas
// Explain delegation considerations

// Gallery Items
// Uses Search (delegable for SharePoint)
// Then Filter by Status (delegable)
// Both operations work on full dataset
Search(
    Filter(Employees, Status = "Active"),
    txtSearch.Text,
    "FirstName", "LastName"
)
```

#### Step 10: Advanced Delegation Scenarios

**Scenario 1: Multi-Table Join**

**❌ Problem:**

```powerFx
// Join two tables
AddColumns(
    Projects,
    "ManagerName",
    LookUp(Employees, ID = ManagerID).FullName
)
// ⚠️ LookUp in AddColumns = non-delegable
```

**✅ Solution: Use Dataverse Relationships**

```powerFx
// In Dataverse, use built-in relationships
Filter(
    Projects,
    'Manager (Employees)'.FullName = "John Smith"
)
// ✅ Delegable with proper relationship setup
```

**Scenario 2: Aggregations**

**❌ Limited:**

```powerFx
// Most aggregations require loading data first
Sum(Filter(Employees, Department = "IT"), Salary)
// Only sums first 500-2000 records if Filter non-delegable
```

**✅ Solution: Use delegation-friendly queries**

```powerFx
// Ensure Filter is delegable first
With(
    {itEmployees: Filter(Employees, Department = "IT")},  // Delegable
    Sum(itEmployees, Salary)  // Then sum (loads all matching)
)
```

**Scenario 3: Distinct Values from Large Dataset**

**❌ Problem:**

```powerFx
Distinct(LargeDataset, CategoryField)
// Only gets distinct from first 500-2000
```

**✅ Solution: Use choice column or separate list**

```powerFx
// Maintain separate Categories list (< 2000)
// Or use Dataverse choice column
```

### Assignment Deliverable

✅ **Create comprehensive delegation testing app:**

- Dataset with 2500+ records
- Test record at position 2000+
- Multiple filter scenarios (delegable and non-delegable)
- Visual indicators showing delegation status
- Before/after fixes with proof of working delegation

✅ **Demonstrate at least 5 delegation issues and fixes:**

- Contains → StartsWith or Search
- Lookup column → ID-based filter
- Year(Date) → Date range
- CountRows → CountIf
- Math in filter → Pre-calculated value

✅ **Document:**

- Data source comparison (SharePoint vs Dataverse)
- Testing methodology
- When delegation is working vs broken
- Performance difference with delegation

### Knowledge Check

- [ ] What is delegation and why is it important?
- [ ] What does the data row limit control?
- [ ] Why is increasing the data row limit NOT a solution?
- [ ] What text functions are delegable in SharePoint?
- [ ] What text functions are delegable in Dataverse?
- [ ] How do you test if delegation is working?
- [ ] What's the fix for Contains on SharePoint?
- [ ] How do you handle lookup columns for delegation?
- [ ] What's delegable: CountRows or CountIf?
- [ ] When should you migrate from SharePoint to Dataverse?

### Delegation Quick Reference

| Operation               | SharePoint | Dataverse | SQL | Excel | Collections |
| ----------------------- | ---------- | --------- | --- | ----- | ----------- |
| **=, <>, <, >, <=, >=** | ✅         | ✅        | ✅  | ❌    | ❌          |
| **And, Or**             | ✅         | ✅        | ✅  | ❌    | ❌          |
| **In**                  | ✅         | ✅        | ✅  | ❌    | ❌          |
| **StartsWith**          | ✅         | ✅        | ✅  | ❌    | ❌          |
| **Contains**            | ❌         | ✅        | ✅  | ❌    | ❌          |
| **EndsWith**            | ❌         | ✅        | ✅  | ❌    | ❌          |
| **IsBlank**             | ✅         | ✅        | ✅  | ❌    | ❌          |
| **Lookup columns**      | ⚠️         | ✅        | ✅  | ❌    | ❌          |
| **Choice columns**      | ⚠️         | ✅        | ✅  | ❌    | ❌          |
| **Search function**     | ✅         | ✅        | ✅  | ❌    | ❌          |

### Delegation Decision Tree

```
Do you have > 2000 records?
├─ NO → Delegation not critical, cache if needed
└─ YES → Must use delegation
    │
    ├─ Using SharePoint?
    │   ├─ Need Contains/EndsWith?
    │   │   └─ Migrate to Dataverse
    │   └─ Only need StartsWith?
    │       └─ Stay with SharePoint
    │
    ├─ Using Excel?
    │   └─ ❌ Migrate to SharePoint or Dataverse
    │
    └─ Using Dataverse?
        └─ ✅ Most operations delegable
```

### Resources

- [Microsoft Learn: Delegation overview](https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/delegation-overview)
- [Microsoft Learn: Delegable functions](https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/delegation-list)
- [Microsoft Learn: Delegation examples](https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/delegation-examples)
- [YouTube Playlist: Understanding Delegation](https://www.youtube.com/playlist?list=PLExample)
- [Blog: Delegation Best Practices](https://powerapps.microsoft.com/en-us/blog/delegation/)

---

## Module 15: Concurrency (1 hour)

### Learning Objectives

- Understand concurrency in Power Apps
- Implement parallel data loading
- Use Concurrent() function effectively
- Identify appropriate use cases
- Measure performance improvements

### Step-by-Step Instructions

#### Step 1: Understanding Concurrency

**What is Concurrency?**

- Execute multiple operations simultaneously
- Don't wait for one to finish before starting next
- Significantly reduces total execution time
- Available through `Concurrent()` function

**Sequential vs Concurrent:**

**❌ Sequential (Slow):**

```powerFx
// Takes 1s + 1s + 1s = 3 seconds total
ClearCollect(colEmployees, Employees);        // 1 second
ClearCollect(colDepartments, Departments);    // 1 second
ClearCollect(colProjects, Projects);          // 1 second
```

**✅ Concurrent (Fast):**

```powerFx
// Takes max(1s, 1s, 1s) = 1 second total
Concurrent(
    ClearCollect(colEmployees, Employees),
    ClearCollect(colDepartments, Departments),
    ClearCollect(colProjects, Projects)
);
```

**When to Use Concurrency:**

- App startup (App.OnStart)
- Loading multiple independent data sources
- Parallel API calls
- Independent calculations

**When NOT to Use:**

- Operations that depend on each other
- Single large operation
- Writing to same collection
- Sequential workflow requirements

#### Step 2: Create Concurrency Test App

1. Create new Canvas app: `ConcurrencyTestApp`
2. Add timing measurements

**Create Mock Data Sources:**

```powerFx
// Button: btnCreateTestData
// Simulate 3 data sources with artificial delay

// Source 1: Employees
ClearCollect(
    colEmployees,
    ForAll(Sequence(500), {
        ID: Value,
        Name: "Employee " & Value,
        Department: "Dept " & Mod(Value, 10)
    })
);

// Source 2: Departments
ClearCollect(
    colDepartments,
    ForAll(Sequence(50), {
        ID: Value,
        Name: "Department " & Value
    })
);

// Source 3: Projects
ClearCollect(
    colProjects,
    ForAll(Sequence(200), {
        ID: Value,
        Name: "Project " & Value,
        Status: If(Mod(Value, 3) = 0, "Active", "Completed")
    })
)
```

#### Step 3: Sequential Loading Pattern

**Button: btnSequentialLoad**

```powerFx
// Start timer
Set(varStartTime, Now());

// Load data sources sequentially
Set(varLoadingMessage, "Loading Employees...");
ClearCollect(colTestEmployees, colEmployees);

Set(varLoadingMessage, "Loading Departments...");
ClearCollect(colTestDepartments, colDepartments);

Set(varLoadingMessage, "Loading Projects...");
ClearCollect(colTestProjects, colProjects);

// Calculate duration
Set(varSequentialTime, DateDiff(varStartTime, Now(), Milliseconds));
Set(varLoadingMessage, "");

Notify(
    "Sequential load: " & varSequentialTime & "ms",
    NotificationType.Information
)
```

#### Step 4: Concurrent Loading Pattern

**Button: btnConcurrentLoad**

```powerFx
// Start timer
Set(varStartTime, Now());

Set(varLoadingMessage, "Loading all data...");

// Load all sources concurrently
Concurrent(
    ClearCollect(colTestEmployees, colEmployees),
    ClearCollect(colTestDepartments, colDepartments),
    ClearCollect(colTestProjects, colProjects)
);

// Calculate duration
Set(varConcurrentTime, DateDiff(varStartTime, Now(), Milliseconds));
Set(varLoadingMessage, "");

Notify(
    "Concurrent load: " & varConcurrentTime & "ms",
    NotificationType.Success
)
```

#### Step 5: Performance Comparison Display

**Label: lblPerformanceComparison**

```powerFx
"Performance Comparison:" & Char(10) & Char(10) &
"Sequential Load: " & varSequentialTime & " ms" & Char(10) &
"Concurrent Load: " & varConcurrentTime & " ms" & Char(10) & Char(10) &
"Time Saved: " & (varSequentialTime - varConcurrentTime) & " ms" & Char(10) &
"Speed Improvement: " & Round((varSequentialTime - varConcurrentTime) / varSequentialTime * 100, 0) & "%" & Char(10) & Char(10) &
If(
    varConcurrentTime < varSequentialTime,
    "✅ Concurrent loading is faster!",
    "⚠️ Results may vary based on data size"
)
```

#### Step 6: Real-World Concurrent Loading

**App.OnStart with Concurrent:**

```powerFx
// ===================================
// APP STARTUP WITH CONCURRENT LOADING
// ===================================

// Show loading indicator
Set(varIsLoading, true);
Set(varLoadingProgress, "Loading application data...");

// Load theme
Set(
    varTheme,
    {
        Colors: {
            Primary: RGBA(0, 120, 212, 1),
            // ... rest of theme
        }
    }
);

// Load all data sources concurrently
Concurrent(
    // Reference data (small, used everywhere)
    ClearCollect(colDepartments, Departments),
    ClearCollect(colJobTitles, JobTitles),
    ClearCollect(colLocations, Locations),
    ClearCollect(colStatuses, Statuses),

    // User-specific data
    ClearCollect(
        colUserInfo,
        Filter(Employees, Email = User().Email)
    ),

    // Recent activity
    ClearCollect(
        colRecentActivities,
        FirstN(
            Sort(Activities, CreatedDate, Descending),
            50
        )
    )
);

// Set user context
Set(varCurrentUser, First(colUserInfo));
Set(varUserDepartment, varCurrentUser.Department);

// Hide loading indicator
Set(varIsLoading, false);
Set(varLoadingProgress, "");

// Navigate to home
Navigate(scrHome, ScreenTransition.None)
```

#### Step 7: Concurrent with Error Handling

```powerFx
// Wrap in IfError for safety
IfError(
    Concurrent(
        ClearCollect(colEmployees, Employees),
        ClearCollect(colDepartments, Departments),
        ClearCollect(colProjects, Projects)
    ),

    // If any fails, show error
    Notify(
        "Failed to load data: " & FirstError.Message,
        NotificationType.Error
    );

    // Try loading sequentially as fallback
    ClearCollect(colEmployees, Employees);
    ClearCollect(colDepartments, Departments);
    ClearCollect(colProjects, Projects)
)
```

#### Step 8: Concurrent API Calls

**Use Case: Load data from multiple APIs**

```powerFx
Concurrent(
    // API Call 1: User profile
    Set(
        varUserProfile,
        ParseJSON(
            'External API 1'.GetUserProfile(User().Email).data
        )
    ),

    // API Call 2: Permissions
    Set(
        varPermissions,
        ParseJSON(
            'External API 2'.GetPermissions(User().Email).data
        )
    ),

    // API Call 3: Notifications
    ClearCollect(
        colNotifications,
        ParseJSON(
            'External API 3'.GetNotifications(User().Email).data
        )
    )
);

Notify("All data loaded successfully", NotificationType.Success)
```

#### Step 9: Concurrent Best Practices

**✅ DO:**

1. **Load independent data sources:**

```powerFx
Concurrent(
    ClearCollect(colEmployees, Employees),      // Independent
    ClearCollect(colDepartments, Departments),  // Independent
    ClearCollect(colProjects, Projects)         // Independent
)
```

2. **Use in App.OnStart for faster startup:**

```powerFx
App.OnStart:
    Concurrent(
        // All reference data loads at once
    )
```

3. **Combine with delegation:**

```powerFx
Concurrent(
    ClearCollect(
        colActiveEmployees,
        Filter(Employees, Status = "Active")  // Delegable
    ),
    ClearCollect(
        colRecentProjects,
        Filter(Projects, StartDate >= DateAdd(Today(), -90, Days))  // Delegable
    )
)
```

**❌ DON'T:**

1. **Don't use for dependent operations:**

```powerFx
// ❌ WRONG - Second operation depends on first
Concurrent(
    Set(varEmployeeID, LookUp(Employees, Email = User().Email).ID),
    ClearCollect(
        colUserProjects,
        Filter(Projects, AssignedTo = varEmployeeID)  // Depends on varEmployeeID!
    )
)
```

2. **Don't write to same collection:**

```powerFx
// ❌ WRONG - Race condition
Concurrent(
    Collect(colData, {Value: 1}),
    Collect(colData, {Value: 2})
)
// Unpredictable results!
```

3. **Don't overuse (diminishing returns):**

```powerFx
// ❌ Too many concurrent operations
Concurrent(
    Operation1,
    Operation2,
    Operation3,
    Operation4,
    Operation5,
    Operation6,
    Operation7,
    Operation8,
    Operation9,
    Operation10
)
// Limited by server/network, may not help
```

#### Step 10: Measuring Concurrency Benefits

**Create Benchmark:**

```powerFx
// Screen: scrConcurrencyBenchmark

// Test 1: Sequential
btnTestSequential.OnSelect:
    Set(varStartTime, Now());

    // Sequential loads
    ClearCollect(colTest1, ForAll(Sequence(1000), {ID: Value}));
    ClearCollect(colTest2, ForAll(Sequence(1000), {ID: Value}));
    ClearCollect(colTest3, ForAll(Sequence(1000), {ID: Value}));
    ClearCollect(colTest4, ForAll(Sequence(1000), {ID: Value}));
    ClearCollect(colTest5, ForAll(Sequence(1000), {ID: Value}));

    Set(varSeqTime, DateDiff(varStartTime, Now(), Milliseconds));
    Notify("Sequential: " & varSeqTime & "ms", NotificationType.Information)

// Test 2: Concurrent
btnTestConcurrent.OnSelect:
    Set(varStartTime, Now());

    // Concurrent loads
    Concurrent(
        ClearCollect(colTest1, ForAll(Sequence(1000), {ID: Value})),
        ClearCollect(colTest2, ForAll(Sequence(1000), {ID: Value})),
        ClearCollect(colTest3, ForAll(Sequence(1000), {ID: Value})),
        ClearCollect(colTest4, ForAll(Sequence(1000), {ID: Value})),
        ClearCollect(colTest5, ForAll(Sequence(1000), {ID: Value}))
    );

    Set(varConcTime, DateDiff(varStartTime, Now(), Milliseconds));
    Notify("Concurrent: " & varConcTime & "ms", NotificationType.Success)

// Results Display
lblBenchmarkResults.Text:
    "Benchmark Results:" & Char(10) & Char(10) &
    "5 Collections x 1000 records each" & Char(10) & Char(10) &
    "Sequential: " & varSeqTime & " ms" & Char(10) &
    "Concurrent: " & varConcTime & " ms" & Char(10) & Char(10) &
    "Speedup: " & Round(varSeqTime / varConcTime, 1) & "x faster" & Char(10) &
    "Time Saved: " & (varSeqTime - varConcTime) & " ms" & Char(10) &
    "Efficiency: " & Round((varSeqTime - varConcTime) / varSeqTime * 100, 0) & "%"
```

### Assignment Deliverable

✅ **Create demonstration showing:**

- Sequential vs Concurrent loading with timing
- Visible performance difference (at least 30% improvement)
- Real-world App.OnStart implementation with Concurrent
- Multiple data sources loaded in parallel
- Performance comparison graph or visualization

✅ **Use cases implemented:**

- App startup optimization
- Multi-source data loading
- Reference data caching

✅ **Documentation:**

- When to use Concurrent
- When NOT to use Concurrent
- Measured performance improvements

### Knowledge Check

- [ ] What is the Concurrent() function used for?
- [ ] When should you use Concurrent vs Sequential loading?
- [ ] Can you use Concurrent for dependent operations?
- [ ] Where is Concurrent most commonly used?
- [ ] What's the typical performance improvement?
- [ ] Can you write to the same collection concurrently?

### Concurrency Quick Reference

| Scenario                       | Sequential | Concurrent        | Benefit   |
| ------------------------------ | ---------- | ----------------- | --------- |
| **3 independent data sources** | 3 seconds  | 1 second          | 3x faster |
| **5 API calls**                | 5 seconds  | 1 second          | 5x faster |
| **App startup (5 sources)**    | Slow       | Fast              | Better UX |
| **Dependent operations**       | ✅ Works   | ❌ Fails          | N/A       |
| **Same collection writes**     | ✅ Works   | ❌ Race condition | N/A       |

### Resources

- [Microsoft Learn: Concurrent function](https://learn.microsoft.com/en-us/power-platform/power-fx/reference/function-concurrent)
- [Microsoft Learn: Performance tips](https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/performance-tips)
- [Video: Optimizing App Performance with Concurrent](https://www.youtube.com/watch?v=example)
- [Blog: Concurrent Loading Best Practices](https://powerapps.microsoft.com/en-us/blog/concurrent/)

---

## Stage 3 Progress Summary

### Modules Completed So Far:

- ✅ Module 14: Delegation (5 hours)
- ✅ Module 15: Concurrency (1 hour)

### Completed: 6 hours of 36 hours

### Remaining Modules:

- Module 16: Exception Handling (2 hours)
- Module 17: Deep Linking (1 hour)
- Module 18: Sharing Canvas Apps (1 hour)
- Module 19: Offline Capability (1 hour)
- Module 20: Monitoring Tool (1 hour)
- Module 21: Responsive Design (24 hours) - Major module

### Next Up

The next section will cover Exception Handling, Deep Linking, and Sharing strategies.

---

**Type "NEXT" to continue with Exception Handling, Deep Linking, and Sharing Canvas Apps.**
