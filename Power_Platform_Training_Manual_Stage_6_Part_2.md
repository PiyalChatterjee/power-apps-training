# Power Platform Training Manual - Stage 6 Part 2: Dashboards, Charts, and Views

## Overview

This is Part 2 of Stage 6, covering advanced data visualization with dashboards, charts, and custom views. Learn to create interactive dashboards, design charts for insights, and build powerful views with advanced filtering. **This part covers 5 hours of Module 44-45.**

**Stage 6 Total Duration: 53 hours** | **Part 2: 5 hours**

---

## Module 44: Dashboards and Charts (3 hours)

### Learning Objectives

- Understand dashboard types and components
- Create interactive dashboards
- Design charts for data visualization
- Configure chart types (bar, pie, line, funnel)
- Add filters and drill-down capabilities
- Create system and personal dashboards
- Use Power BI embedded dashboards
- Implement real-time dashboard updates
- Share and manage dashboards

### Step-by-Step Instructions

#### Step 1: Dashboard Types

**What are Dashboards?**

```
Dashboard = Collection of visualizations showing key metrics

Components:
- Charts (visual data representation)
- Lists (table views with filters)
- Web resources (custom HTML/JavaScript)
- IFrames (embedded content)
- Power BI reports (advanced analytics)

Dashboard Types:

System Dashboard:
- Created by admins
- Visible to all users (based on security)
- Read-only for end users
- Use for: Organization-wide metrics

Personal Dashboard:
- Created by individual users
- Visible only to creator
- Fully customizable
- Use for: Personal tracking

Power BI Dashboard:
- Embedded Power BI reports
- Advanced analytics and visuals
- Real-time data
- Use for: Executive dashboards
```

**Dashboard Layouts:**

```
Available Layouts:

2-Column Regular:
[      Wide      ]
[  Left | Right  ]

3-Column Regular:
[        Wide         ]
[  Left | Mid | Right ]

4-Column Regular:
[           Full Width          ]
[ Col 1 | Col 2 | Col 3 | Col 4 ]

3-Column Wide:
[    Wide    ]
[ L  | Mid | R ]

2-Column Narrow:
[ Left | Right ]
[ Left | Right ]

Most Common: 3-Column Regular
Best for: Balanced layout with charts and lists
```

#### Step 2: Create Your First Dashboard

**Navigate to Dashboards:**

```
Method 1: From Model-Driven App
1. Open your Model-Driven App
2. Click + New
3. Select: Dashboard

Method 2: From Solutions
1. make.powerapps.com → Solutions
2. Select solution
3. + New → Dashboard → Classic Dashboard

Method 3: From Maker Portal
1. make.powerapps.com
2. Dashboards (left navigation)
3. + New dashboard
```

**Create Leave Management Dashboard:**

```
1. Click + New dashboard
2. Choose layout: 3-Column Regular
3. Name: Leave Management Overview
4. Save

Dashboard Designer Opens:
- Top: Toolbar with component options
- Center: Canvas with layout sections
- Each section can hold one component

Component Types Available:
- Chart
- List
- Web Resource
- IFrame
- Text
- Image
```

#### Step 3: Add Charts to Dashboard

**Create Chart Component:**

```
1. Click on a dashboard section
2. Click: Insert Chart (chart icon in toolbar)
3. Configure Chart:

Chart Properties:
- Name: Leave Requests by Type
- Record Type: Leave Request
- View: Active Leave Requests
- Chart: (Select existing or create new)

4. Click "New Chart" to create custom chart
```

**Design Custom Chart:**

```
Chart Designer:
1. Name: Leave Requests by Type
2. Chart type: Pie Chart

3. Legend Entries (Y-axis):
   - Field: Leave Request (Count)
   - Aggregate: Count
   - Alias: Number of Requests

4. Horizontal (Category) Axis (X-axis):
   - Category: Leave Type
   - Alias: Leave Type

5. Preview chart
6. Save and Close

Result: Pie chart showing distribution by leave type
```

**Add Chart to Dashboard:**

```
1. Chart appears in selected section
2. Resize if needed (drag borders)
3. Repeat for additional charts:

Chart 2: Leave Requests by Status
- Position: Top center section
- Type: Column chart
- Y-axis: Count of requests
- X-axis: Status (Draft, Pending, Approved, Rejected)

Chart 3: Leave Trends by Month
- Position: Top right section
- Type: Line chart
- Y-axis: Count of requests
- X-axis: Start Date (by month)
- Shows trend over time

Chart 4: Department Leave Distribution
- Position: Middle left section
- Type: Bar chart
- Y-axis: Department name
- X-axis: Total days (Sum)
```

#### Step 4: Chart Types and When to Use

**Column Chart (Vertical Bars):**

```
Use For:
✅ Comparing values across categories
✅ Showing trends over time
✅ Comparing multiple series

Example: Leave Requests by Status
- X-axis: Status categories
- Y-axis: Count of requests
- Easy to compare Draft vs Pending vs Approved

Configuration:
Type: Column Chart
Series: Count
Category: Status field
```

**Bar Chart (Horizontal Bars):**

```
Use For:
✅ Long category names
✅ Ranking (top 10 lists)
✅ Comparing across categories

Example: Top Departments by Leave Days
- Y-axis: Department names (more readable)
- X-axis: Total days
- Ranked from highest to lowest

Configuration:
Type: Bar Chart
Series: Sum of Total Days
Category: Department
Sort: Descending
```

**Pie Chart:**

```
Use For:
✅ Showing proportions/percentages
✅ Parts of a whole
✅ 5-7 categories maximum

Example: Leave Type Distribution
- Shows: 40% Annual, 30% Sick, 20% Emergency, 10% Other
- Each slice = proportion of total

Configuration:
Type: Pie Chart
Series: Count
Category: Leave Type

⚠️ Avoid: Too many slices (hard to read)
```

**Line Chart:**

```
Use For:
✅ Trends over time
✅ Continuous data
✅ Multiple series comparison

Example: Leave Requests Trend
- X-axis: Month (Jan, Feb, Mar...)
- Y-axis: Count
- Shows seasonal patterns

Configuration:
Type: Line Chart
Series: Count
Category: Start Date (grouped by month)

Can add multiple lines:
- Line 1: Annual leave
- Line 2: Sick leave
- Line 3: Emergency leave
```

**Funnel Chart:**

```
Use For:
✅ Process stages (conversion rates)
✅ Sequential steps
✅ Drop-off analysis

Example: Leave Request Process Funnel
- Stage 1: Submitted (100 requests)
- Stage 2: Manager Review (90 requests)
- Stage 3: HR Review (85 requests)
- Stage 4: Approved (80 requests)

Shows: 20% drop-off through process

Configuration:
Type: Funnel Chart
Series: Count
Category: BPF Stage
Sort: By stage order
```

**Area Chart:**

```
Use For:
✅ Cumulative totals over time
✅ Volume trends
✅ Stacked comparisons

Example: Cumulative Leave Days by Month
- Shows: Total days used increasing over year
- Stacked: By leave type
- Emphasizes magnitude of totals

Configuration:
Type: Area Chart
Series: Sum of Total Days
Category: Start Date (by month)
```

#### Step 5: Add Lists to Dashboard

**What are List Components?**

```
List Component = Table view of records on dashboard

Features:
- Sortable columns
- Clickable records (opens form)
- Based on saved views
- Can show associated charts
- Real-time filtering

Use For:
✅ Recent records
✅ Pending items (action needed)
✅ High-priority items
✅ Quick access to records
```

**Add List Component:**

```
1. Select dashboard section (bottom row)
2. Click: Insert List (list icon in toolbar)
3. Configure:

List Properties:
- Name: Pending Approvals
- Record Type: Leave Request
- View: Pending Approval (existing view)
- Show Chart: Yes (optional - shows chart above list)

4. Save

Result: List of pending leave requests
Users can click to open and approve
```

**Common List Components:**

```
Dashboard Lists:

1. Pending Approvals
   - View: Status = Pending Approval
   - Sort: Start Date (oldest first)
   - Purpose: Action items for managers

2. Recent Requests
   - View: Created On = Last 7 days
   - Sort: Created On (newest first)
   - Purpose: Track new submissions

3. Upcoming Leave
   - View: Start Date = Next 30 days, Status = Approved
   - Sort: Start Date (soonest first)
   - Purpose: Calendar planning

4. My Team's Leave
   - View: Employee.Manager = Current User
   - Sort: Start Date
   - Purpose: Manager oversight

5. Rejected Requests
   - View: Status = Rejected, Created On = This Month
   - Sort: Created On (newest first)
   - Purpose: Review rejections
```

#### Step 6: Interactive Features

**Chart Drill-Down:**

```
Built-in Interactivity:
1. User clicks chart segment
2. Records filter automatically
3. Related lists update
4. User sees filtered data

Example: Click "Sick" slice in pie chart
Result: All lists on dashboard filter to show only Sick leave

No configuration needed - works automatically!
```

**Global Filters:**

```
Dashboard Filters:
- Users can filter entire dashboard
- Filters appear at top of dashboard
- Apply to all components

Common Filters:
- Date range: Show last 30/60/90 days
- Department: Show specific department
- Status: Show only pending or approved
- Employee: Show specific employee

Enable Filters:
1. Edit dashboard
2. Properties
3. Enable filtering: Yes
4. Filter by: (select fields)
5. Save
```

**Refresh Dashboard:**

```
Auto-Refresh:
- Dashboard updates automatically
- Based on data changes
- No manual refresh needed

Manual Refresh:
- Refresh button (top right)
- Updates all components
- Use when: Need immediate update

Configure Refresh:
Some dashboards can set refresh interval
(mainly Power BI embedded dashboards)
```

#### Step 7: Advanced Dashboard Techniques

**Multi-Stream Dashboard:**

```
Multi-Stream = Show multiple filtered views side-by-side

Example: Manager Dashboard
Stream 1: My Team's Pending Requests
Stream 2: Department's Approved Leave
Stream 3: Upcoming Leave Calendar

Each stream = Independent filter
User sees comprehensive view
```

**Web Resource Components:**

```
Add Custom HTML/JavaScript:

Use Cases:
- KPI counters
- Custom visualizations
- External content
- Interactive widgets

Create Web Resource:
1. Solutions → + New → Web resource
2. Type: HTML
3. Content:
   <html>
   <body>
     <div style="text-align:center; padding:20px;">
       <h1 style="font-size:48px; color:#0078D4;">
         <span id="pendingCount">--</span>
       </h1>
       <p>Pending Approvals</p>
     </div>
     <script>
       // Fetch count from Dataverse
       Xrm.WebApi.retrieveMultipleRecords(
         "cr123_leaverequest",
         "?$filter=cr123_status eq 865420000&$count=true"
       ).then(function(result) {
         document.getElementById("pendingCount").innerText = result.entities.length;
       });
     </script>
   </body>
   </html>

4. Save and Publish

Add to Dashboard:
1. Edit dashboard
2. Select section
3. Insert Web Resource
4. Select: Your web resource
5. Save

Result: Live KPI counter on dashboard
```

**IFrame Components:**

```
Embed External Content:

Examples:
- Company intranet page
- External reporting tool
- SharePoint document library
- Third-party application

Add IFrame:
1. Edit dashboard
2. Select section
3. Insert IFrame
4. Configure:
   - URL: https://example.com/reports
   - Restrict cross-frame scripting: (based on security)
   - Pass record context: (if needed)
5. Save

⚠️ Security: IFrame URLs must be allowed in organization settings
```

#### Step 8: Power BI Dashboard Integration

**Embed Power BI Report:**

```
Prerequisites:
✅ Power BI Pro license
✅ Power BI report created
✅ Report published to Power BI service
✅ Power BI workspace

Steps:
1. Edit dashboard
2. Power BI section options:
   - Add Power BI tile
   - Add Power BI report

3. Select: Add Power BI report
4. Choose:
   - Workspace: Your workspace
   - Report: Leave Analytics Report
   - Page: Overview (specific page)

5. Save

Result: Interactive Power BI report embedded in dashboard
```

**Power BI vs Native Charts:**

```
Native Dataverse Charts:
✅ No additional license
✅ Simple visualizations
✅ Basic filtering
✅ Quick to create
❌ Limited chart types
❌ Limited interactivity

Power BI:
✅ Advanced visualizations
✅ Complex calculations (DAX)
✅ Rich interactivity
✅ Real-time data
✅ AI insights
❌ Requires Power BI license
❌ More complex setup

Use Native Charts for: Standard dashboards
Use Power BI for: Executive dashboards, advanced analytics
```

#### Step 9: Dashboard Security and Sharing

**System Dashboards:**

```
Create System Dashboard:
1. Create dashboard as usual
2. Save
3. Actions → Share
4. Share with: Organization
   OR specific teams/users

Result: Dashboard visible to selected users

Security:
- Users need read permission on Dashboard entity
- Users need read permission on data (tables)
- If user can't read Leave Requests, charts show empty

Assign Security Role:
Users need: Basic User + custom role with Dashboard read
```

**Personal Dashboards:**

```
Users Can Create Own:
1. User opens app
2. + New → Dashboard
3. Customize for personal use
4. Save as: Personal dashboard

Visible only to creator
Can't be shared (personal only)
Good for: Individual productivity tracking
```

**Set Default Dashboard:**

```
For Model-Driven App:
1. Edit app in App Designer
2. Add dashboard as page
3. Set as default page
4. Publish

Result: Dashboard shows when app opens
```

#### Step 10: Dashboard Best Practices

**Design Principles:**

```
✅ DO:
- Show most important metrics at top
- Use consistent color scheme
- Limit to 5-7 components per dashboard
- Include actionable lists (pending items)
- Test with real data
- Consider mobile view
- Add clear titles to components
- Use filters for user control

❌ DON'T:
- Overcrowd with too many components
- Mix too many chart types
- Use 3D charts (hard to read)
- Show raw data without context
- Forget to test permissions
- Use too many colors
- Create without user input
```

**Performance Optimization:**

```
Optimize Dashboard Load Time:

✅ Use views with filters (don't load all records)
✅ Limit chart data points (< 50 categories)
✅ Use aggregates instead of lists when possible
✅ Cache web resources
✅ Minimize custom scripts
✅ Test with production data volume

Example:
Instead of: View all 10,000 leave requests
Use: View with filter (Last 90 days, Status = Active)
Result: Faster load, relevant data
```

**Mobile Considerations:**

```
Dashboard on Mobile:

Automatic Adjustments:
- Layout stacks vertically
- Components fill screen width
- Charts optimize for small screen
- Lists show fewer columns

Best Practices:
✅ Test on mobile device
✅ Use clear, large fonts
✅ Prioritize most important components at top
✅ Use simple chart types (column, bar, pie)
✅ Limit components (3-5 on mobile)
```

### Assignment Deliverable

✅ **Create comprehensive dashboard:**

- Name: Leave Management Overview
- Layout: 3-Column Regular
- Minimum 4 charts (pie, column, bar, line)
- Minimum 2 lists (pending approvals, recent requests)

✅ **Create diverse chart types:**

- Pie Chart: Leave Requests by Type
- Column Chart: Leave Requests by Status
- Line Chart: Monthly Leave Trend
- Bar Chart: Top Departments by Total Days
- Funnel Chart: Approval Process Stages

✅ **Add interactive components:**

- Enable dashboard filtering
- Add lists that respond to chart clicks
- Create web resource KPI counter
- Test drill-down functionality

✅ **Create role-specific dashboards:**

- Employee Dashboard (personal leave summary)
- Manager Dashboard (team approvals, upcoming leave)
- HR Admin Dashboard (organization metrics, trends)

✅ **Test and document:**

- Test on desktop and mobile
- Verify permissions and security
- Share with appropriate users
- Create user guide for dashboard navigation
- Screenshot key dashboards

### Knowledge Check

- [ ] What are the dashboard types?
- [ ] What components can you add to dashboards?
- [ ] When should you use each chart type?
- [ ] How do you create a custom chart?
- [ ] What are list components?
- [ ] How does chart drill-down work?
- [ ] What are web resource components?
- [ ] How do you embed Power BI reports?
- [ ] What's the difference between system and personal dashboards?
- [ ] What are dashboard best practices?

### Resources

- [Microsoft Learn: Create dashboards](https://learn.microsoft.com/en-us/power-apps/maker/model-driven-apps/create-edit-dashboards)
- [Microsoft Learn: Create or edit charts](https://learn.microsoft.com/en-us/power-apps/maker/model-driven-apps/create-edit-system-chart)
- [Microsoft Learn: Power BI integration](https://learn.microsoft.com/en-us/power-apps/maker/model-driven-apps/use-power-bi)
- [Microsoft Learn: Dashboard best practices](https://learn.microsoft.com/en-us/power-platform/guidance/adoption/dashboard-best-practices)

---

## Module 45: Advanced Views and Filtering (2 hours)

### Learning Objectives

- Create and customize views
- Use FetchXML for advanced queries
- Implement complex filtering logic
- Create personal and system views
- Configure view columns and sorting
- Use Quick Find for search
- Implement associated views
- Create lookup views
- Optimize view performance
- Export and import view definitions

### Step-by-Step Instructions

#### Step 1: View Types

**Understanding View Types:**

```
Public View (System View):
- Created by admins/customizers
- Visible to all users (with permissions)
- Used in: Lists, lookups, subgrids, charts
- Examples: Active Contacts, My Open Opportunities

Personal View:
- Created by individual users
- Visible only to creator
- Use for: Personal filtering and sorting
- Can be made default for creator

System Views (Special):
- Quick Find: Search results
- Lookup: Used in lookup dialogs
- Advanced Find: Search tool results
- Associated: Related records view
- Can't delete, only customize
```

**View Components:**

```
Every View Has:
1. Columns: Which fields to display
2. Filter: Which records to show
3. Sort: Primary and secondary sorting
4. Layout: Column width and order

Example: Pending Approval View
- Columns: Employee, Leave Type, Start Date, End Date, Total Days
- Filter: Status = Pending Approval
- Sort: Start Date (ascending)
- Layout: Employee (150px), Leave Type (120px), etc.
```

#### Step 2: Create Custom View

**Create Public View:**

```
1. Tables → Leave Request → Views
2. + New view
3. Configure:

View Properties:
- Name: My Team's Leave Requests
- Description: Shows leave requests from my team members
- Table: Leave Request

4. Add Columns:
   Click "+ Add column"
   Select:
   - Employee Name
   - Department
   - Leave Type
   - Start Date
   - End Date
   - Total Days
   - Status
   - Manager Approval

5. Arrange columns:
   Drag to reorder
   Resize column width (drag column border)

6. Set Filter:
   (Covered in next step)

7. Set Sort:
   Click column header → Sort ascending/descending
   Primary sort: Start Date (ascending)
   Secondary sort: Employee Name (ascending)

8. Save view
9. Publish
```

**Column Configuration:**

```
For Each Column:

Column Properties:
- Name: Display name
- Width: Pixel width (default 100)
- Data Type: Read-only (from field)

Add Columns:
- Direct fields: From Leave Request table
- Related fields: Employee.Department, Employee.Manager
- Calculated: (Use Dataverse calculated columns)

Related Field Example:
Add: Employee → Department → Department Name
Display: Shows employee's department name
```

#### Step 3: Advanced Filtering

**Simple Filters:**

```
Filter Criteria:

1. Click "Edit filters" in view designer
2. Add condition:
   - Field: Status
   - Operator: Equals
   - Value: Pending Approval

3. Add more conditions (AND):
   - Field: Start Date
   - Operator: Next X Days
   - Value: 30

Result: Shows pending requests starting in next 30 days
```

**Complex Filters (AND/OR):**

```
Filter Logic:

Example: Show requests that need attention

Filter:
Status = Pending Approval
AND Start Date = Next 7 days
OR Status = Rejected AND Created On = This Week

Implementation:
1. Add first condition: Status = Pending Approval
2. Add second condition (AND): Start Date = Next X Days (7)
3. Change group logic:
   - Group 1: (Status = Pending) AND (Start Date = Next 7 days)
   - OR
   - Group 2: (Status = Rejected) AND (Created On = This Week)

4. Use "Group by AND/OR" options
5. Test filter logic

Result: Shows urgent pending requests OR recent rejections
```

**Filter Operators:**

```
Text Fields:
- Equals / Does not equal
- Contains / Does not contain
- Begins with / Ends with
- Is null / Is not null

Number Fields:
- Equals / Does not equal
- Greater than / Less than
- Greater than or equal / Less than or equal
- Between

Date Fields:
- On
- Today / Yesterday / Tomorrow
- Next X Days / Last X Days
- This Week/Month/Year
- Last Week/Month/Year
- Older than X Days

Choice Fields:
- Equals / Does not equal
- Contains data / Does not contain data

Lookup Fields:
- Equals / Does not equal
- Contains data / Does not contain data
- Equals User ID (current user)
- Equals Business Unit ID
```

**Dynamic Filters:**

```
User Context Filters:

Example: My Team's Leave Requests

Filter:
Employee.Manager = Current User

Implementation:
1. Add filter
2. Field: Employee → Manager
3. Operator: Equals User ID
4. Value: (system uses current user automatically)

Result: Each manager sees only their team's requests

Other User Context:
- Owner = Current User (my records)
- Business Unit = Current User's BU
- Team = Current User's teams
```

**Date Filters:**

```
Relative Date Filters:

Examples:
- Created On = Last 7 days
- Start Date = Next 30 days
- Modified On = This month
- End Date = Next week

These filters update automatically:
- "Last 7 days" always means rolling 7 days
- "This month" changes each month
- Better than: Date > 1/1/2025 (static)

Fiscal Year Filters:
- This Fiscal Year
- Last Fiscal Year
- This Fiscal Period
- Use for: Financial reporting
```

#### Step 4: FetchXML Queries

**What is FetchXML?**

```
FetchXML = XML-based query language for Dataverse

Use For:
✅ Complex queries beyond UI capabilities
✅ Aggregations (SUM, COUNT, AVG)
✅ Joins across multiple tables
✅ Grouping
✅ Distinct results
✅ Top N queries

Can edit FetchXML directly or use Advanced Find
```

**Access FetchXML Editor:**

```
Method 1: From View Designer
1. Edit view
2. Click: "Edit filters"
3. Click: "..." menu
4. Switch to FetchXML

Method 2: Advanced Find
1. Model-Driven App
2. Settings → Advanced Find
3. Build query visually
4. Download FetchXML
5. Use in view

Method 3: Manual Creation
Write FetchXML in code editor
Paste into view definition
```

**Basic FetchXML Example:**

```xml
<fetch>
  <entity name="cr123_leaverequest">
    <attribute name="cr123_employee" />
    <attribute name="cr123_leavetype" />
    <attribute name="cr123_startdate" />
    <attribute name="cr123_enddate" />
    <attribute name="cr123_totaldays" />
    <filter>
      <condition attribute="cr123_status" operator="eq" value="865420000" />
      <condition attribute="cr123_startdate" operator="next-x-days" value="30" />
    </filter>
    <order attribute="cr123_startdate" descending="false" />
  </entity>
</fetch>
```

**Advanced FetchXML - Aggregation:**

```xml
<!-- Total leave days by department -->
<fetch aggregate="true">
  <entity name="cr123_leaverequest">
    <attribute name="cr123_totaldays" alias="total_days" aggregate="sum" />
    <attribute name="cr123_employee" alias="department" groupby="true" />
    <link-entity name="cr123_employeeprofile" from="cr123_employeeprofileid" to="cr123_employee">
      <attribute name="cr123_department" alias="dept_name" groupby="true" />
    </link-entity>
    <filter>
      <condition attribute="cr123_status" operator="eq" value="865420001" />
    </filter>
    <order alias="total_days" descending="true" />
  </entity>
</fetch>

Result: Shows total approved leave days per department, sorted by highest
```

**FetchXML - Join Multiple Tables:**

```xml
<!-- Leave requests with employee and department details -->
<fetch>
  <entity name="cr123_leaverequest">
    <attribute name="cr123_name" />
    <attribute name="cr123_startdate" />
    <attribute name="cr123_totaldays" />

    <!-- Join to Employee -->
    <link-entity name="cr123_employeeprofile" from="cr123_employeeprofileid" to="cr123_employee" alias="employee">
      <attribute name="cr123_fullname" />
      <attribute name="cr123_email" />

      <!-- Join to Department -->
      <link-entity name="cr123_department" from="cr123_departmentid" to="cr123_department" alias="dept">
        <attribute name="cr123_name" />
        <attribute name="cr123_manager" />
      </link-entity>
    </link-entity>

    <filter>
      <condition attribute="cr123_status" operator="eq" value="865420000" />
    </filter>
  </entity>
</fetch>
```

**FetchXML - Top N Query:**

```xml
<!-- Top 10 employees by leave days taken -->
<fetch top="10" aggregate="true">
  <entity name="cr123_leaverequest">
    <attribute name="cr123_employee" alias="employee_id" groupby="true" />
    <attribute name="cr123_totaldays" alias="total_days" aggregate="sum" />
    <link-entity name="cr123_employeeprofile" from="cr123_employeeprofileid" to="cr123_employee" alias="emp">
      <attribute name="cr123_fullname" alias="employee_name" groupby="true" />
    </link-entity>
    <filter>
      <condition attribute="cr123_status" operator="eq" value="865420001" />
    </filter>
    <order alias="total_days" descending="true" />
  </entity>
</fetch>
```

#### Step 5: Quick Find View

**What is Quick Find?**

```
Quick Find = Global search functionality

User types in search box → Results from Quick Find view

Searches across:
✅ Multiple fields (you configure which)
✅ Fast indexed search
✅ Appears in global search bar

Example:
User types: "John"
Quick Find searches:
- Employee Name (contains "John")
- Email (contains "John")
- Employee ID (contains "John")

Returns: All matching records
```

**Configure Quick Find View:**

```
1. Tables → Leave Request → Views
2. Find: "Quick Find Active Leave Requests" (system view)
3. Edit view

4. Configure Find Columns:
   Select which fields to search:
   ✅ Employee Name
   ✅ Leave Type
   ✅ Comments

5. Configure View Columns:
   What to show in results:
   - Employee Name
   - Leave Type
   - Start Date
   - Status

6. Save and Publish

Result: When users search, these fields are searched
```

**Quick Find Best Practices:**

```
✅ DO:
- Include commonly searched fields
- Include fields users remember (name, email, ID)
- Limit to 5-10 searchable fields
- Add relevant view columns

❌ DON'T:
- Search in every field (slow performance)
- Include large text fields (descriptions)
- Search in number fields (not meaningful)

Optimize:
- Use indexed fields when possible
- Test search performance with production data
```

#### Step 6: Lookup Views

**What are Lookup Views?**

```
Lookup View = Used in lookup dialogs

When user clicks lookup field:
1. Dialog opens
2. Shows Lookup View
3. User searches and selects

Default Lookup View:
- Predefined by system
- Shows key fields
- Can be customized

Example: Employee lookup on Leave Request
Shows: Employee Name, Department, Email, Job Title
```

**Customize Lookup View:**

```
1. Tables → Employee Profile → Views
2. Find: "Lookup View" (system view)
3. Edit

4. Add/Remove Columns:
   Show in lookup dialog:
   - Full Name
   - Employee ID
   - Department
   - Job Title
   - Email
   - Status (Active/Inactive)

5. Set Default Sort:
   - Primary: Full Name (ascending)

6. Set Filter (optional):
   - Status = Active (hide inactive employees)

7. Save and Publish

Result: Improved lookup experience
Users see relevant info to make selection
```

#### Step 7: Associated Views

**What are Associated Views?**

```
Associated View = Shows related records

Used in:
- Subgrids on forms
- Related menu
- Navigation from parent to child records

Example: Employee form
Subgrid: Leave Requests (associated view)
Shows: All leave requests for this employee

Filter: Automatic (Employee = Current Record)
```

**Customize Associated View:**

```
1. Tables → Leave Request → Views
2. Find: "Associated View" OR create new view
3. Edit

4. Configure Columns:
   What to show in subgrid:
   - Leave Type
   - Start Date
   - End Date
   - Total Days
   - Status

5. No filter needed:
   System automatically filters to related records

6. Set Sort:
   - Start Date (descending - most recent first)

7. Save and Publish

Use in Form:
1. Edit Employee form
2. Add subgrid component
3. Select table: Leave Request
4. Select view: Your associated view
5. Save
```

#### Step 8: Personal Views

**Create Personal View:**

```
User Perspective:
1. User opens Leave Requests list
2. Applies filters/sorts to find what they need
3. Clicks: "Save view" → "Save as personal view"
4. Names view: "My Urgent Approvals"
5. View appears in user's view selector

Only visible to that user
User can edit/delete own personal views
```

**Convert Personal to System View:**

```
Admin Can Promote:
1. Advanced settings → Customization
2. Find personal view in system
3. Export view definition
4. Create new system view
5. Import definition
6. Publish

OR
Use solution to export/import views

Result: Personal view becomes available to all
```

#### Step 9: View Performance Optimization

**Best Practices:**

```
Optimize View Performance:

✅ Use Filters:
- Don't show all records
- Filter by Status, Date range, Owner
- Example: Status = Active (not closed records)

✅ Limit Columns:
- Show only necessary columns
- Each column = database query
- Too many columns = slow load

✅ Avoid Calculated Fields:
- Calculated fields recalculate on load
- Use when necessary, but not for all columns

✅ Use Indexes:
- System automatically indexes some fields
- Custom indexes can be added by admin

✅ Pagination:
- Views automatically paginate (5000 records default)
- Users navigate pages

❌ AVOID:
- View with filter: Modified On = Any time (all records!)
- 20+ columns in view
- Complex FetchXML with multiple joins
- Sorting by non-indexed fields
```

**Measure Performance:**

```
Test View Load Time:
1. Open view with Chrome DevTools
2. Network tab
3. Measure load time
4. Goal: < 2 seconds for initial load

If Slow:
- Reduce columns
- Add more restrictive filter
- Check FetchXML complexity
- Consider indexing
```

#### Step 10: Export and Import Views

**Export View Definition:**

```
Method 1: Via Solution
1. Add view to solution
2. Export solution
3. View definition in XML

Method 2: FetchXML Builder (XrmToolBox)
1. Download XrmToolBox (community tool)
2. Use FetchXML Builder
3. Export view as FetchXML
4. Save for reuse

Method 3: API
Use Dataverse Web API to retrieve savedquery entity
```

**Import View:**

```
1. Create new view
2. Switch to FetchXML editor
3. Paste saved FetchXML
4. Save
5. Test view
6. Publish

Use For:
- Copying views between environments
- Backup view definitions
- Sharing view configurations
- Version control
```

**View Templates:**

```
Reusable View Patterns:

My Active Records:
- Owner = Current User
- Status = Active
- Sort: Modified On (descending)

Recently Modified:
- Modified On = Last 7 days
- Sort: Modified On (descending)

Team Records:
- Owner.Business Unit = Current User's BU
- Status = Active
- Sort: Created On (descending)

Pending My Action:
- Assigned To = Current User
- Status = Pending
- Sort: Priority (high first), Due Date (urgent first)

Save these patterns as templates
Adapt for different tables
```

### Assignment Deliverable

✅ **Create comprehensive views:**

- My Team's Leave Requests (filtered by manager)
- Pending Approvals (urgent items first)
- Upcoming Leave (next 30 days, approved)
- Recent Requests (last 7 days)
- Leave History (past requests, archive view)

✅ **Configure view components:**

- Appropriate columns (5-8 per view)
- Proper column widths
- Primary and secondary sorting
- Complex filters with AND/OR logic

✅ **Create FetchXML view:**

- Use FetchXML for complex query
- Example: Aggregate view (total days by department)
- Include joins across tables
- Document FetchXML code

✅ **Customize system views:**

- Quick Find View (searchable fields)
- Lookup View (employee selection)
- Associated View (for subgrids)

✅ **Test and optimize:**

- Measure view load times
- Test filters with various criteria
- Verify permissions (different roles see appropriate data)
- Test on mobile devices
- Document view purposes and filters

✅ **Create personal view (as user):**

- Save personal view with custom filter
- Test view selector
- Demonstrate user-specific filtering

### Knowledge Check

- [ ] What are the different view types?
- [ ] How do you create a custom view?
- [ ] What filter operators are available?
- [ ] How do you create AND/OR filter logic?
- [ ] What is FetchXML?
- [ ] When should you use FetchXML?
- [ ] What is a Quick Find view?
- [ ] What is a Lookup view?
- [ ] What is an Associated view?
- [ ] How do you optimize view performance?

### Resources

- [Microsoft Learn: Create and edit views](https://learn.microsoft.com/en-us/power-apps/maker/model-driven-apps/create-edit-views)
- [Microsoft Learn: FetchXML reference](https://learn.microsoft.com/en-us/power-apps/developer/data-platform/fetchxml/overview)
- [Microsoft Learn: View filters](https://learn.microsoft.com/en-us/power-apps/maker/model-driven-apps/create-edit-view-filters)
- [Microsoft Learn: Improve view performance](https://learn.microsoft.com/en-us/power-apps/maker/model-driven-apps/model-driven-app-glossary#view)

---

## Stage 6 Part 2 Complete! 🎉

### Completed Modules (5 hours):

- ✅ Module 44: Dashboards and Charts (3 hours)
- ✅ Module 45: Advanced Views and Filtering (2 hours)

### Skills Acquired:

- Dashboard types and layouts
- Creating interactive dashboards
- All chart types (pie, column, bar, line, funnel, area)
- List components and drill-down
- Web resources and IFrames
- Power BI dashboard integration
- Dashboard security and sharing
- Custom view creation
- Advanced filtering (AND/OR logic)
- FetchXML queries and aggregations
- Quick Find, Lookup, and Associated views
- Personal and system views
- View performance optimization
- Dynamic user context filters

### Leave Management Progress:

✅ Leave Management Overview Dashboard created
✅ Multiple chart types (distribution, trends, rankings)
✅ Interactive lists (pending approvals, recent requests)
✅ Role-specific dashboards (Employee, Manager, HR Admin)
✅ Custom views with complex filters
✅ FetchXML aggregation views
✅ Optimized Quick Find for global search
✅ Customized lookup views for better UX

### Overall Training Progress:

**Total Completed: 130 hours of 170 hours (76.5%)**

**Completed:**

- ✅ Stage 1: Power Apps Fundamentals (16.5 hours)
- ✅ Stage 2: Canvas Apps Intermediate (29 hours)
- ✅ Stage 3: Advanced Canvas Apps (36 hours)
- ✅ Stage 4: Cloud Flows (25.5 hours)
- ✅ Stage 5: Dataverse (10 hours)
- ✅ Stage 6 Part 1: Model-Driven Apps Fundamentals (8 hours)
- ✅ Stage 6 Part 2: Dashboards, Charts, and Views (5 hours)

**Remaining:**

- ⏳ Stage 6 Part 3: Power Pages (8 hours)
- ⏳ Stage 6 Part 4: Solutions and ALM (10 hours)
- ⏳ Stage 6 Part 5: Final Project - Complete Leave Management System (22 hours)

---

**Type "NEXT" to continue with Stage 6 Part 3: Power Pages (8 hours)**
