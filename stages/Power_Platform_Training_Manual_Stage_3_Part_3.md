# Power Platform Training Manual - Stage 3 Part 3: Offline Capability, Monitoring, and Responsive Design

## Overview

This section covers offline capability with LoadData/SaveData, monitoring tools for app analytics, and the comprehensive responsive design implementation for the Leave Management System. **Duration: 26 hours**

---

## Module 19: Offline Capability (1 hour)

### Learning Objectives

- Understand offline scenarios in Power Apps
- Implement LoadData() and SaveData() functions
- Cache data for offline use
- Sync data when connection restored
- Handle offline/online transitions
- Build offline-first user experience

### Step-by-Step Instructions

#### Step 1: Understanding Offline Capability

**Why Offline Capability?**

- Mobile users may lose connectivity
- Improve app performance (cached data)
- Continue working during network issues
- Better user experience
- Essential for field workers

**Power Apps Offline Limitations:**

- Only works with local device storage
- Not true background sync
- Data stored only on device
- Limited to 100MB per app
- Cleared when user logs out or clears cache

**Use Cases:**

- Field service apps
- Inspection forms
- Survey apps
- Reference data caching
- Temporary loss of connectivity

#### Step 2: LoadData() and SaveData() Functions

**SaveData() - Save data to device:**

```powerFx
SaveData(Collection, "StorageName")
```

**LoadData() - Load data from device:**

```powerFx
LoadData(Collection, "StorageName")
```

**Example:**

```powerFx
// Save employees collection to device
SaveData(colEmployees, "CachedEmployees")

// Load employees collection from device
LoadData(colEmployees, "CachedEmployees")
```

**Important Notes:**

- Storage name must be in quotes
- Data persists across app sessions
- Data is device-specific
- Cannot share between users
- Limited to 100MB total

#### Step 3: Create Offline-Capable App

**App.OnStart with Offline Support:**

```powerFx
// ===================================
// APP STARTUP WITH OFFLINE SUPPORT
// ===================================

Set(varIsLoading, true);
Set(varIsOffline, false);

// Check if we have cached data
If(
    IsEmpty(colEmployees),
    // No data in memory, try to load from cache
    LoadData(colEmployees, "CachedEmployees");
    LoadData(colDepartments, "CachedDepartments");
    LoadData(colLeaveRequests, "CachedLeaveRequests")
);

// Try to load fresh data from server
IfError(
    // Attempt to load from SharePoint
    Concurrent(
        ClearCollect(colEmployees, Employees),
        ClearCollect(colDepartments, Departments),
        ClearCollect(colLeaveRequests, 'Leave Requests')
    );

    // Success - save to cache
    SaveData(colEmployees, "CachedEmployees");
    SaveData(colDepartments, "CachedDepartments");
    SaveData(colLeaveRequests, "CachedLeaveRequests");

    // Update timestamp
    Set(varLastSync, Now());
    SaveData(varLastSync, "LastSyncTime");

    Set(varIsOffline, false);
    Notify("Data synchronized ✅", NotificationType.Success),

    // Error - use cached data
    Set(varIsOffline, true);

    // Load last sync time from cache
    LoadData(varLastSync, "LastSyncTime");

    Notify(
        "⚠️ Offline Mode" & Char(10) &
        "Using cached data from: " & Text(varLastSync, "[$-en-US]mmm dd, yyyy hh:mm AM/PM"),
        NotificationType.Warning
    )
);

Set(varIsLoading, false);
Navigate(scrHome, ScreenTransition.None)
```

#### Step 4: Offline Indicator

**Create Offline Mode Indicator:**

```powerFx
// Component: cmpOfflineIndicator
// Shows at top of every screen

// Container
conOfflineBanner.Visible: varIsOffline
conOfflineBanner.Fill: RGBA(255, 193, 7, 1)  // Yellow/Warning
conOfflineBanner.Height: 50

// Icon
icnOffline.Icon: Icon.Warning
icnOffline.Color: RGBA(255, 255, 255, 1)

// Label
lblOfflineMessage.Text:
    "⚠️ Offline Mode - Using cached data from " &
    Text(varLastSync, "[$-en-US]mmm dd hh:mm AM/PM")
lblOfflineMessage.Color: RGBA(255, 255, 255, 1)

// Retry button
btnRetrySync.Text: "Retry Sync"
btnRetrySync.OnSelect:
    // Try to sync again
    Set(varIsLoading, true);
    IfError(
        ClearCollect(colEmployees, Employees);
        ClearCollect(colDepartments, Departments);
        ClearCollect(colLeaveRequests, 'Leave Requests');

        // Save to cache
        SaveData(colEmployees, "CachedEmployees");
        SaveData(colDepartments, "CachedDepartments");
        SaveData(colLeaveRequests, "CachedLeaveRequests");

        Set(varLastSync, Now());
        SaveData(varLastSync, "LastSyncTime");

        Set(varIsOffline, false);
        Set(varIsLoading, false);
        Notify("Synchronized successfully! ✅", NotificationType.Success),

        // Still offline
        Set(varIsLoading, false);
        Notify("Still offline. Please check connection.", NotificationType.Error)
    )
```

#### Step 5: Cache Reference Data

**Strategy: Cache small, frequently-used reference data**

```powerFx
// App.OnStart
// Cache reference data that rarely changes

// Try to load reference data
IfError(
    // Load from server
    Concurrent(
        ClearCollect(colDepartments, Departments),
        ClearCollect(colJobTitles, JobTitles),
        ClearCollect(colLocations, Locations),
        ClearCollect(colLeaveTypes, 'Leave Types'),
        ClearCollect(colHolidays, Holidays)
    );

    // Save to cache (only if successful)
    SaveData(colDepartments, "RefDepartments");
    SaveData(colJobTitles, "RefJobTitles");
    SaveData(colLocations, "RefLocations");
    SaveData(colLeaveTypes, "RefLeaveTypes");
    SaveData(colHolidays, "RefHolidays");

    Set(varRefDataCached, Now()),

    // Failed - load from cache
    LoadData(colDepartments, "RefDepartments");
    LoadData(colJobTitles, "RefJobTitles");
    LoadData(colLocations, "RefLocations");
    LoadData(colLeaveTypes, "RefLeaveTypes");
    LoadData(colHolidays, "RefHolidays");

    LoadData(varRefDataCached, "RefDataCachedTime")
)
```

#### Step 6: Offline Form Submission Queue

**Strategy: Queue submissions when offline, sync when online**

```powerFx
// Create pending submissions collection
// App.OnStart
If(
    IsEmpty(colPendingSubmissions),
    LoadData(colPendingSubmissions, "PendingSubmissions")
)
```

**Form Submit with Offline Support:**

```powerFx
// Button: btnSubmitLeaveRequest

// Validate form
If(
    IsBlank(dtpStartDate.SelectedDate),
    Notify("Start date is required", NotificationType.Error),
    IsBlank(dtpEndDate.SelectedDate),
    Notify("End date is required", NotificationType.Error),
    dtpEndDate.SelectedDate < dtpStartDate.SelectedDate,
    Notify("End date must be after start date", NotificationType.Error),

    // Validation passed
    If(
        varIsOffline,

        // OFFLINE - Queue submission
        Collect(
            colPendingSubmissions,
            {
                ID: GUID(),
                Type: "LeaveRequest",
                Data: {
                    Employee: User().Email,
                    LeaveType: ddnLeaveType.Selected.Value,
                    StartDate: dtpStartDate.SelectedDate,
                    EndDate: dtpEndDate.SelectedDate,
                    Reason: txtReason.Text,
                    Status: "Pending",
                    CreatedDate: Now()
                },
                Timestamp: Now(),
                Synced: false
            }
        );

        // Save queue to device
        SaveData(colPendingSubmissions, "PendingSubmissions");

        Notify(
            "✅ Leave request saved offline" & Char(10) &
            "Will be submitted when connection is restored",
            NotificationType.Success
        );

        Reset(frmLeaveRequest);
        Navigate(scrLeaveList, ScreenTransition.UnCoverRight),

        // ONLINE - Submit directly
        IfError(
            Patch(
                'Leave Requests',
                Defaults('Leave Requests'),
                {
                    Employee: LookUp(Employees, Email = User().Email),
                    LeaveType: ddnLeaveType.Selected,
                    StartDate: dtpStartDate.SelectedDate,
                    EndDate: dtpEndDate.SelectedDate,
                    Reason: txtReason.Text,
                    Status: "Pending Approval",
                    CreatedDate: Now(),
                    CreatedBy: User().Email
                }
            );

            Notify("Leave request submitted! ✅", NotificationType.Success);
            Reset(frmLeaveRequest);
            Navigate(scrLeaveList, ScreenTransition.UnCoverRight),

            // Failed - queue it
            Collect(
                colPendingSubmissions,
                {
                    ID: GUID(),
                    Type: "LeaveRequest",
                    Data: {
                        Employee: User().Email,
                        LeaveType: ddnLeaveType.Selected.Value,
                        StartDate: dtpStartDate.SelectedDate,
                        EndDate: dtpEndDate.SelectedDate,
                        Reason: txtReason.Text
                    },
                    Timestamp: Now(),
                    Synced: false
                }
            );
            SaveData(colPendingSubmissions, "PendingSubmissions");

            Notify(
                "⚠️ Submission queued" & Char(10) &
                "Will retry when connection is restored",
                NotificationType.Warning
            )
        )
    )
)
```

#### Step 7: Sync Pending Submissions

**Auto-sync when connection restored:**

```powerFx
// Timer: timAutoSync
// Duration: 30000 (30 seconds)
// Repeat: true
// OnTimerEnd:

If(
    // Only sync if we have pending items and are online
    CountRows(Filter(colPendingSubmissions, Not(Synced))) > 0 And
    Not(varIsOffline),

    // Process pending submissions
    ForAll(
        Filter(colPendingSubmissions, Not(Synced)),

        // Attempt to submit each item
        IfError(
            Switch(
                Type,

                "LeaveRequest",
                Patch(
                    'Leave Requests',
                    Defaults('Leave Requests'),
                    Data
                );
                Set(varSyncSuccess, true),

                "UpdateEmployee",
                Patch(Employees, LookUp(Employees, ID = Data.ID), Data);
                Set(varSyncSuccess, true),

                // Unknown type
                Set(varSyncSuccess, false)
            );

            // Mark as synced
            If(
                varSyncSuccess,
                Patch(
                    colPendingSubmissions,
                    ThisRecord,
                    {Synced: true, SyncedDate: Now()}
                )
            ),

            // Error syncing this item
            Patch(
                colPendingSubmissions,
                ThisRecord,
                {
                    SyncError: FirstError.Message,
                    SyncAttempts: ThisRecord.SyncAttempts + 1
                }
            )
        )
    );

    // Save updated queue
    SaveData(colPendingSubmissions, "PendingSubmissions");

    // Show notification if any synced
    If(
        CountRows(Filter(colPendingSubmissions, Synced)) > 0,
        Notify(
            CountRows(Filter(colPendingSubmissions, Synced)) &
            " item(s) synchronized ✅",
            NotificationType.Success
        )
    );

    // Clean up old synced items (older than 7 days)
    Clear(
        Filter(
            colPendingSubmissions,
            Synced And DateDiff(SyncedDate, Now(), Days) > 7
        )
    );
    SaveData(colPendingSubmissions, "PendingSubmissions")
)
```

#### Step 8: Manual Sync Button

**Allow users to manually trigger sync:**

```powerFx
// Button: btnManualSync
btnManualSync.Text: "🔄 Sync Now"
btnManualSync.OnSelect:

Set(varIsSyncing, true);

// Try to load fresh data
IfError(
    Concurrent(
        ClearCollect(colEmployees, Employees),
        ClearCollect(colDepartments, Departments),
        ClearCollect(colLeaveRequests, 'Leave Requests')
    );

    // Save to cache
    SaveData(colEmployees, "CachedEmployees");
    SaveData(colDepartments, "CachedDepartments");
    SaveData(colLeaveRequests, "CachedLeaveRequests");

    Set(varLastSync, Now());
    SaveData(varLastSync, "LastSyncTime");

    Set(varIsOffline, false);

    // Also sync pending submissions
    ForAll(
        Filter(colPendingSubmissions, Not(Synced)),
        Patch(
            'Leave Requests',
            Defaults('Leave Requests'),
            Data
        );
        Patch(
            colPendingSubmissions,
            ThisRecord,
            {Synced: true, SyncedDate: Now()}
        )
    );
    SaveData(colPendingSubmissions, "PendingSubmissions");

    Set(varIsSyncing, false);
    Notify(
        "✅ Synchronized successfully!" & Char(10) &
        "Last sync: " & Text(varLastSync, "[$-en-US]hh:mm AM/PM"),
        NotificationType.Success
    ),

    // Still offline
    Set(varIsOffline, true);
    Set(varIsSyncing, false);
    Notify(
        "❌ Sync failed - Still offline" & Char(10) &
        "Error: " & FirstError.Message,
        NotificationType.Error
    )
)
```

#### Step 9: Show Pending Submissions

**Screen: scrPendingSync**

```powerFx
// Gallery: galPendingSubmissions
Items:
    SortByColumns(
        Filter(colPendingSubmissions, Not(Synced)),
        "Timestamp",
        Descending
    )

// Display each pending item
lblPendingType.Text: ThisItem.Type
lblPendingTime.Text:
    "Created: " & Text(ThisItem.Timestamp, "[$-en-US]mmm dd, yyyy hh:mm AM/PM")

lblPendingDetails.Text:
    Switch(
        ThisItem.Type,
        "LeaveRequest",
        "Leave: " & ThisItem.Data.LeaveType & Char(10) &
        "From: " & Text(ThisItem.Data.StartDate, "[$-en-US]mmm dd") &
        " To: " & Text(ThisItem.Data.EndDate, "[$-en-US]mmm dd"),

        "UpdateEmployee",
        "Employee Update: " & ThisItem.Data.FirstName & " " & ThisItem.Data.LastName,

        "Unknown"
    )

icnPendingStatus.Icon:
    If(
        ThisItem.Synced,
        Icon.CheckBadge,
        If(
            ThisItem.SyncAttempts > 3,
            Icon.Warning,
            Icon.Sync
        )
    )

lblPendingCount.Text:
    "Pending: " & CountRows(Filter(colPendingSubmissions, Not(Synced)))
```

#### Step 10: Clear Cache

**Settings option to clear cached data:**

```powerFx
// Button: btnClearCache
// In Settings screen

btnClearCache.OnSelect:
    If(
        varConfirmClearCache,

        // Confirmed - clear everything
        Clear(colEmployees);
        Clear(colDepartments);
        Clear(colLeaveRequests);
        Clear(colPendingSubmissions);

        // Note: Cannot actually delete SaveData storage
        // Can only overwrite with empty collections
        SaveData(colEmployees, "CachedEmployees");
        SaveData(colDepartments, "CachedDepartments");
        SaveData(colLeaveRequests, "CachedLeaveRequests");
        SaveData(colPendingSubmissions, "PendingSubmissions");

        Set(varConfirmClearCache, false);
        Notify("Cache cleared! ✅", NotificationType.Success);

        // Reload app
        Launch("https://apps.powerapps.com/play/" & App.ActiveAppId),

        // Ask for confirmation
        Set(varConfirmClearCache, true);
        Notify(
            "⚠️ This will delete all cached data. Click again to confirm.",
            NotificationType.Warning
        )
    )
```

### Assignment Deliverable

✅ **Implement offline capability in Leave Management System:**

- LoadData/SaveData for reference data caching
- Offline indicator component
- Pending submissions queue
- Auto-sync when connection restored
- Manual sync button
- Pending submissions screen

✅ **Test offline scenarios:**

- Open app without internet connection
- Submit leave request while offline
- Verify it's queued
- Restore connection
- Verify auto-sync works
- Check data is synchronized

✅ **Create user documentation:**

- How offline mode works
- What data is cached
- How to sync manually
- How to view pending submissions
- What to do if sync fails

### Knowledge Check

- [ ] What functions are used for offline capability?
- [ ] What's the storage limit per app?
- [ ] Where is offline data stored?
- [ ] Can offline data be shared between users?
- [ ] What happens to SaveData when user logs out?
- [ ] How do you queue form submissions?
- [ ] How do you sync pending submissions?
- [ ] What's a good strategy for reference data?
- [ ] Should you cache all data?
- [ ] How do you show offline status to users?

### Offline Quick Reference

| Function              | Purpose                     | Example                           |
| --------------------- | --------------------------- | --------------------------------- |
| **SaveData**          | Save collection to device   | `SaveData(colEmployees, "Cache")` |
| **LoadData**          | Load collection from device | `LoadData(colEmployees, "Cache")` |
| **Queue submission**  | Store for later sync        | `Collect(colPending, {...})`      |
| **Auto-sync**         | Periodic sync check         | Timer with ForAll pending items   |
| **Offline indicator** | Show status                 | `varIsOffline = true`             |

### Resources

- [Microsoft Learn: SaveData function](https://learn.microsoft.com/en-us/power-platform/power-fx/reference/function-savedata-loaddata)
- [Microsoft Learn: LoadData function](https://learn.microsoft.com/en-us/power-platform/power-fx/reference/function-savedata-loaddata)
- [Microsoft Learn: Offline capabilities](https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/offline-apps)
- [Blog: Building Offline-First Apps](https://powerapps.microsoft.com/en-us/blog/offline/)

---

## Module 20: Monitoring Tool (1 hour)

### Learning Objectives

- Understand Power Apps Monitor
- Debug app issues in real-time
- Analyze performance problems
- View formula execution
- Monitor data calls
- Use Monitor for troubleshooting

### Step-by-Step Instructions

#### Step 1: Understanding Power Apps Monitor

**What is Monitor?**

- Real-time debugging tool
- Shows all events in your app
- Formula execution details
- Data operation performance
- Error messages
- Network requests

**When to Use Monitor:**

- Debugging errors
- Performance optimization
- Understanding formula execution order
- Troubleshooting data issues
- Learning how Power Apps works

#### Step 2: Launch Monitor

**From Power Apps Studio:**

1. Open app in Edit mode
2. Click **Advanced tools** (left menu)
3. Click **Monitor**
4. Or: **Alt + Shift + M**

**New window opens:**

- Monitor runs in separate browser tab
- Connected to your app session
- Shows real-time events

**From Published App:**

1. Add `?debug=true` to app URL
   ```
   https://apps.powerapps.com/play/[AppID]?debug=true
   ```
2. Monitor window opens automatically

#### Step 3: Monitor Interface Overview

**Sections:**

**1. Event List (Main Area):**

- Chronological list of all events
- Each row is an event (button click, formula execution, etc.)
- Click event for details

**2. Filters:**

- Filter by:
  - Event type (Network, User Action, Error, etc.)
  - Screen
  - Control
  - Time range

**3. Event Details Panel:**

- Formula executed
- Duration
- Result
- Errors (if any)

**4. Controls:**

- Play/Pause recording
- Clear events
- Export events
- Search

#### Step 4: Monitor Event Types

**User Actions:**

```
- Button clicks
- Gallery item selection
- Text input changes
- Dropdown selection
- etc.
```

**Network Requests:**

```
- Data source connections
- API calls
- Collection operations
- Patch operations
- Filter operations
```

**Errors:**

```
- Formula errors
- Data source errors
- Runtime errors
```

**App Lifecycle:**

```
- App.OnStart
- Screen.OnVisible
- Screen.OnHidden
```

#### Step 5: Debugging with Monitor

**Scenario 1: Button Click Not Working**

**Steps:**

1. Click button in app
2. Look at Monitor
3. Find button OnSelect event
4. Check if formula executed
5. Look for errors

**Example Event:**

```
Event: OnSelect
Control: btnSaveEmployee
Formula:
  Patch(
    Employees,
    Defaults(Employees),
    {FirstName: txtFirstName.Text, ...}
  )
Duration: 245ms
Result: Error
Error: "The specified column 'FirstName' does not exist."
```

**Fix:** Column name is wrong - should be "First Name" with space

**Scenario 2: Slow Gallery Loading**

**Steps:**

1. Navigate to screen with gallery
2. Look at Monitor for Screen.OnVisible
3. Find data loading operations
4. Check duration of each operation

**Example:**

```
Event: Network Request
Operation: Filter(Employees, Department = "IT")
Duration: 4,523ms
Delegation Warning: Yes
Rows Returned: 2000 (limit)
```

**Analysis:**

- Non-delegable query taking 4.5 seconds
- Hitting delegation limit
- Fix: Make query delegable

#### Step 6: Monitor Performance Analysis

**Identify Slow Operations:**

**Look for:**

- Duration > 1000ms (1 second)
- Multiple sequential data calls (should be concurrent)
- Repeated same operations
- Large collections in memory

**Example Monitor Output:**

```
Event                          Duration    Issue
====================================================
ClearCollect(colEmployees, ...)   2,345ms   Large dataset
Filter(colEmployees, ...)           156ms   OK
ClearCollect(colDepartments, ...) 1,234ms   Sequential (should be concurrent)
ClearCollect(colProjects, ...)    1,567ms   Sequential (should be concurrent)
ForAll(colEmployees, ...)         3,890ms   Slow loop
```

**Optimization:**

```powerFx
// Before (Sequential - 5.1 seconds)
ClearCollect(colEmployees, Employees);      // 2.3s
ClearCollect(colDepartments, Departments);  // 1.2s
ClearCollect(colProjects, Projects);        // 1.6s

// After (Concurrent - 2.3 seconds)
Concurrent(
    ClearCollect(colEmployees, Employees),
    ClearCollect(colDepartments, Departments),
    ClearCollect(colProjects, Projects)
)
```

#### Step 7: Monitor Formula Execution

**View Formula Results:**

**Select any event in Monitor:**

- See full formula
- See execution result
- See variables set
- See data returned

**Example:**

**Formula:**

```powerFx
Set(
    varEmployeeInfo,
    LookUp(Employees, Email = User().Email)
)
```

**Monitor Shows:**

```
Formula executed successfully
Duration: 89ms

Result:
{
  ID: 123,
  FirstName: "John",
  LastName: "Smith",
  Email: "john.smith@company.com",
  Department: "IT"
}

Variable Set:
varEmployeeInfo = [Record]
```

#### Step 8: Monitor Network Requests

**Data Operations Tracking:**

**Filter Monitor by "Network":**

- See all data source calls
- Request details
- Response time
- Data returned
- Errors

**Example Network Event:**

```
Event: Network Request
Type: Patch
Data Source: SharePoint - Employees
Duration: 456ms

Request:
Patch(
  Employees,
  LookUp(Employees, ID = 123),
  {Salary: 75000}
)

Response:
Success
Record Updated: ID 123

Details:
- Connection: Shared
- Delegation: N/A
- Rows Affected: 1
```

#### Step 9: Exporting Monitor Data

**Export for Analysis:**

1. In Monitor, click **Export** button
2. Downloads JSON file with all events
3. Can be analyzed in Excel/Power BI

**Use Cases:**

- Share with team for troubleshooting
- Analyze performance trends
- Document issues
- Create performance reports

**Example Export:**

```json
[
  {
    "timestamp": "2025-11-13T10:30:45Z",
    "event": "OnSelect",
    "control": "btnSave",
    "duration": 234,
    "status": "Success"
  },
  {
    "timestamp": "2025-11-13T10:30:46Z",
    "event": "Network",
    "operation": "Patch",
    "duration": 567,
    "status": "Success"
  }
]
```

#### Step 10: Monitor Best Practices

**✅ DO:**

1. **Use Monitor during development:**

   - Debug issues immediately
   - Verify formulas work as expected
   - Check performance

2. **Filter events:**

   - Focus on specific screen/control
   - Filter by error events
   - Look at network only

3. **Export and document:**

   - Save Monitor output for bugs
   - Compare before/after optimization
   - Share with team

4. **Test with ?debug=true:**
   - Test published app
   - Monitor production issues
   - User-reported problems

**❌ DON'T:**

1. **Leave Monitor running in production:**

   - Impacts performance
   - Only use for debugging

2. **Ignore warnings:**

   - Delegation warnings
   - Performance warnings
   - Error messages

3. **Monitor without clear goal:**
   - Know what you're looking for
   - Have specific question

### Assignment Deliverable

✅ **Use Monitor to debug Leave Management System:**

- Launch Monitor
- Navigate through app
- Capture at least 10 different events
- Export Monitor data

✅ **Identify and fix 3 issues using Monitor:**

- Example: Slow data loading
- Example: Delegation warning
- Example: Formula error
- Document before/after with Monitor screenshots

✅ **Performance analysis:**

- Identify slowest operations in app
- Measure duration of key workflows
- Document performance metrics:
  - App startup time
  - Form load time
  - Data save time
  - Gallery load time

✅ **Create troubleshooting guide:**

- How to use Monitor
- Common events to look for
- How to interpret errors
- How to export and share results

### Knowledge Check

- [ ] How do you launch Monitor?
- [ ] What types of events does Monitor show?
- [ ] How do you filter Monitor events?
- [ ] What does event duration indicate?
- [ ] How do you see formula execution results?
- [ ] Can you use Monitor on published apps?
- [ ] What's the keyboard shortcut for Monitor?
- [ ] How do you export Monitor data?
- [ ] What should you look for in network requests?
- [ ] Should you leave Monitor running in production?

### Monitor Quick Reference

| Task                | Action              | Notes                  |
| ------------------- | ------------------- | ---------------------- |
| **Open Monitor**    | Alt + Shift + M     | In Studio              |
| **Debug published** | Add `?debug=true`   | In URL                 |
| **Filter events**   | Click filter icon   | By type/screen/control |
| **View details**    | Click event row     | Shows formula & result |
| **Export**          | Click Export button | Downloads JSON         |
| **Clear**           | Click Clear button  | Removes all events     |
| **Search**          | Type in search box  | Find specific events   |

### Resources

- [Microsoft Learn: Monitor overview](https://learn.microsoft.com/en-us/power-apps/maker/monitor-overview)
- [Microsoft Learn: Debugging canvas apps](https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/debugging-canvas-apps)
- [Microsoft Learn: Monitor collaboration](https://learn.microsoft.com/en-us/power-apps/maker/monitor-collaborative-debugging)
- [Video: Using Monitor Tool](https://www.youtube.com/watch?v=example)

---

## Module 21: Responsive Design (24 hours)

### Learning Objectives

- Master responsive design principles
- Implement flexible layouts
- Use containers effectively
- Create breakpoints for different devices
- Build mobile-first designs
- Test on multiple screen sizes
- Make Leave Management System fully responsive

### Part 1: Responsive Design Fundamentals (4 hours)

#### Step 1: Understanding Responsive Design

**What is Responsive Design?**

- App adapts to different screen sizes
- Single app works on phone, tablet, desktop
- Optimal layout for each device
- Better user experience across devices

**Why Responsive Design?**

- Users access apps on multiple devices
- No need for separate mobile app
- Consistent experience
- Easier maintenance

**Screen Size Categories:**

```
Phone (Portrait):  360 x 640 to 414 x 896
Phone (Landscape): 640 x 360 to 896 x 414
Tablet (Portrait): 768 x 1024 to 834 x 1194
Tablet (Landscape): 1024 x 768 to 1194 x 834
Desktop: 1366 x 768 to 1920 x 1080+
```

**Power Apps Default:**

- Tablet layout: 1366 x 768
- Not responsive by default!
- Need to implement responsiveness

#### Step 2: Power Apps Screen Size Settings

**App Settings:**

1. **Settings → Display**
2. **Scale to fit:**

   - ON: App scales to fit screen (not truly responsive)
   - OFF: App maintains size (may need scrolling)

3. **Lock aspect ratio:**

   - ON: Maintains proportions when scaling
   - OFF: Can stretch to fit

4. **Orientation:**
   - Portrait
   - Landscape
   - Both (recommended for responsive)

**Recommended Settings for Responsive:**

```
Scale to fit: OFF
Lock aspect ratio: OFF
Orientation: Both
```

#### Step 3: Key Responsive Properties

**Every Control Has:**

**Position:**

- `X` - Horizontal position
- `Y` - Vertical position

**Size:**

- `Width`
- `Height`

**Responsive Formulas Use:**

- `App.Width` - Current app width
- `App.Height` - Current app height
- `Parent.Width` - Parent container width
- `Parent.Height` - Parent container height

**Example - Full Width Container:**

```powerFx
conMain.Width: App.Width
conMain.Height: App.Height
```

**Example - Centered Container:**

```powerFx
conContent.Width: Min(App.Width - 40, 1200)  // Max 1200px, 20px margin each side
conContent.X: (App.Width - Self.Width) / 2   // Center horizontally
```

#### Step 4: Create Responsive Variables

**App.OnStart - Set up responsive variables:**

```powerFx
// ===================================
// RESPONSIVE DESIGN VARIABLES
// ===================================

// Current dimensions
Set(varScreenWidth, App.Width);
Set(varScreenHeight, App.Height);

// Device type based on width
Set(
    varDeviceType,
    If(
        App.Width < 640, "Phone",
        App.Width < 1024, "Tablet",
        "Desktop"
    )
);

// Orientation
Set(
    varOrientation,
    If(App.Width > App.Height, "Landscape", "Portrait")
);

// Breakpoints
Set(varIsPhone, App.Width < 640);
Set(varIsTablet, App.Width >= 640 And App.Width < 1024);
Set(varIsDesktop, App.Width >= 1024);

// Spacing (scales with device)
Set(
    varSpacing,
    {
        XS: If(varIsPhone, 4, If(varIsTablet, 8, 8)),
        SM: If(varIsPhone, 8, If(varIsTablet, 12, 16)),
        MD: If(varIsPhone, 16, If(varIsTablet, 20, 24)),
        LG: If(varIsPhone, 24, If(varIsTablet, 32, 40)),
        XL: If(varIsPhone, 32, If(varIsTablet, 48, 64))
    }
);

// Font sizes (scales with device)
Set(
    varFontSize,
    {
        Small: If(varIsPhone, 10, If(varIsTablet, 11, 12)),
        Medium: If(varIsPhone, 12, If(varIsTablet, 13, 14)),
        Large: If(varIsPhone, 14, If(varIsTablet, 16, 18)),
        XLarge: If(varIsPhone, 18, If(varIsTablet, 22, 26)),
        Heading: If(varIsPhone, 22, If(varIsTablet, 28, 32))
    }
);

// Control heights (scales with device)
Set(
    varControlHeight,
    {
        Small: If(varIsPhone, 32, If(varIsTablet, 36, 40)),
        Medium: If(varIsPhone, 40, If(varIsTablet, 44, 48)),
        Large: If(varIsPhone, 48, If(varIsTablet, 56, 64))
    }
);

// Sidebar width
Set(varSidebarWidth, If(varIsPhone, 0, If(varIsTablet, 200, 250)));

// Content padding
Set(varContentPadding, If(varIsPhone, 16, If(varIsTablet, 24, 32)));

// Number of columns for grid layouts
Set(varGridColumns, If(varIsPhone, 1, If(varIsTablet, 2, 3)));
```

**Update on Screen Size Change:**

```powerFx
// App.OnSizeChanged (not available - use Timer instead)

// Timer: timResponsive
Duration: 500
Repeat: true
OnTimerEnd:
    // Check if size changed
    If(
        varScreenWidth <> App.Width Or varScreenHeight <> App.Height,

        // Update variables
        Set(varScreenWidth, App.Width);
        Set(varScreenHeight, App.Height);
        Set(varDeviceType, If(App.Width < 640, "Phone", App.Width < 1024, "Tablet", "Desktop"));
        Set(varOrientation, If(App.Width > App.Height, "Landscape", "Portrait"));
        Set(varIsPhone, App.Width < 640);
        Set(varIsTablet, App.Width >= 640 And App.Width < 1024);
        Set(varIsDesktop, App.Width >= 1024)
    )
```

#### Step 5: Responsive Layout Patterns

**Pattern 1: Full Width Layout**

```powerFx
// Container fills entire screen
conFullWidth.X: 0
conFullWidth.Y: 0
conFullWidth.Width: App.Width
conFullWidth.Height: App.Height
```

**Pattern 2: Centered with Max Width**

```powerFx
// Container centered, max width 1200px
conCentered.Width: Min(App.Width - (varContentPadding * 2), 1200)
conCentered.X: (App.Width - Self.Width) / 2
conCentered.Y: varContentPadding
```

**Pattern 3: Sidebar + Content**

```powerFx
// Sidebar (hidden on phone)
conSidebar.Visible: Not(varIsPhone)
conSidebar.X: 0
conSidebar.Y: 0
conSidebar.Width: varSidebarWidth
conSidebar.Height: App.Height

// Content area (adjusts based on sidebar)
conContent.X: If(varIsPhone, 0, varSidebarWidth)
conContent.Y: 0
conContent.Width: If(varIsPhone, App.Width, App.Width - varSidebarWidth)
conContent.Height: App.Height
```

**Pattern 4: Grid Layout**

```powerFx
// Gallery with responsive columns
galGrid.Columns: varGridColumns
galGrid.Width: Parent.Width
galGrid.TemplateWidth: (Parent.Width - (varSpacing.MD * (varGridColumns + 1))) / varGridColumns
galGrid.TemplateHeight: Self.TemplateWidth * 1.2  // Maintain aspect ratio
```

**Pattern 5: Stack Layout (Vertical on Phone, Horizontal on Desktop)**

```powerFx
// Container 1
con1.X: varContentPadding
con1.Y: varContentPadding
con1.Width:
    If(
        varIsPhone,
        Parent.Width - (varContentPadding * 2),  // Full width on phone
        (Parent.Width - (varContentPadding * 3)) / 2  // Half width on tablet/desktop
    )

// Container 2
con2.X:
    If(
        varIsPhone,
        varContentPadding,  // Below con1 on phone
        con1.X + con1.Width + varContentPadding  // Next to con1 on tablet/desktop
    )
con2.Y:
    If(
        varIsPhone,
        con1.Y + con1.Height + varContentPadding,  // Below con1
        varContentPadding  // Same level as con1
    )
con2.Width: con1.Width
```

#### Step 6: Responsive Components

**Create Responsive Button Component:**

**Component: cmpResponsiveButton**

**Custom Properties:**

- `pText` (Input, Text)
- `pOnSelect` (Output, Boolean)

**Button Properties:**

```powerFx
// Width adapts to content
Width:
    If(
        varIsPhone,
        Parent.Width - (varSpacing.MD * 2),  // Full width on phone
        Max(150, Self.Size.Width + 40)  // Auto width with minimum on desktop
    )

Height: varControlHeight.Medium

// Font size
Font: 'Lato'
FontWeight: FontWeight.Semibold
Size: varFontSize.Medium

// Position
X: If(varIsPhone, varSpacing.MD, 0)
```

**Create Responsive Input Component:**

**Component: cmpResponsiveInput**

**Custom Properties:**

- `pLabel` (Input, Text)
- `pValue` (Input/Output, Text)
- `pRequired` (Input, Boolean)

```powerFx
// Label
lblInputLabel.Text: Self.pLabel & If(Self.pRequired, " *", "")
lblInputLabel.Size: varFontSize.Medium
lblInputLabel.Y: 0

// Text Input
txtInput.Y: lblInputLabel.Y + lblInputLabel.Height + varSpacing.SM
txtInput.Width: Parent.Width
txtInput.Height: varControlHeight.Medium
txtInput.Size: varFontSize.Medium

// Component height
Height: lblInputLabel.Height + varSpacing.SM + txtInput.Height
```

### Part 2: Implementing Responsive Navigation (4 hours)

#### Step 7: Responsive Header/Navigation

**Desktop Navigation (Horizontal):**

```powerFx
// conHeader - Horizontal menu bar
conHeader.X: 0
conHeader.Y: 0
conHeader.Width: App.Width
conHeader.Height: 60

// Logo
imgLogo.X: varSpacing.MD
imgLogo.Y: (Parent.Height - Self.Height) / 2
imgLogo.Width: 120
imgLogo.Height: 40

// Navigation Menu (Horizontal on desktop)
galNavMenu.Visible: varIsDesktop
galNavMenu.X: imgLogo.X + imgLogo.Width + varSpacing.LG
galNavMenu.Y: 0
galNavMenu.Width: 600
galNavMenu.Height: Parent.Height
galNavMenu.Layout: Layout.Horizontal

// Navigation item width
galNavMenu.TemplateWidth: 150
galNavMenu.TemplateHeight: Parent.Height

// User profile
conUserProfile.X: Parent.Width - Self.Width - varSpacing.MD
conUserProfile.Y: (Parent.Height - Self.Height) / 2
conUserProfile.Width: 200
conUserProfile.Height: 40
```

**Mobile Navigation (Hamburger Menu):**

```powerFx
// Hamburger Icon (visible on phone/tablet)
icnMenu.Visible: Not(varIsDesktop)
icnMenu.Icon: Icon.HamburgerMenu
icnMenu.X: Parent.Width - Self.Width - varSpacing.MD
icnMenu.Y: (Parent.Height - Self.Height) / 2
icnMenu.OnSelect: Set(varShowMobileMenu, !varShowMobileMenu)

// Mobile Menu Overlay
conMobileMenuOverlay.Visible: varShowMobileMenu And Not(varIsDesktop)
conMobileMenuOverlay.X: 0
conMobileMenuOverlay.Y: 0
conMobileMenuOverlay.Width: App.Width
conMobileMenuOverlay.Height: App.Height
conMobileMenuOverlay.Fill: RGBA(0, 0, 0, 0.5)
conMobileMenuOverlay.OnSelect: Set(varShowMobileMenu, false)

// Mobile Menu Panel (Slides in from right)
conMobileMenu.Visible: varShowMobileMenu And Not(varIsDesktop)
conMobileMenu.X: If(varShowMobileMenu, App.Width - Self.Width, App.Width)
conMobileMenu.Y: 0
conMobileMenu.Width: Min(300, App.Width * 0.8)
conMobileMenu.Height: App.Height
conMobileMenu.Fill: RGBA(255, 255, 255, 1)

// Mobile Menu Items (Vertical list)
galMobileNav.X: 0
galMobileNav.Y: 60
galMobileNav.Width: Parent.Width
galMobileNav.Height: Parent.Height - Self.Y
galMobileNav.Layout: Layout.Vertical
galMobileNav.TemplateHeight: 60

// Menu Item
lblMenuItem.Text: ThisItem.Label
lblMenuItem.OnSelect:
    Navigate(ThisItem.Screen, ScreenTransition.None);
    Set(varShowMobileMenu, false)  // Close menu after selection
```

**Navigation Data:**

```powerFx
// Collection: colNavItems
ClearCollect(
    colNavItems,
    {
        Label: "Dashboard",
        Screen: scrDashboard,
        Icon: Icon.Home
    },
    {
        Label: "Leave Requests",
        Screen: scrLeaveList,
        Icon: Icon.DocumentSet
    },
    {
        Label: "Employees",
        Screen: scrEmployeeList,
        Icon: Icon.People
    },
    {
        Label: "Approvals",
        Screen: scrApprovalList,
        Icon: Icon.CheckBadge
    },
    {
        Label: "Settings",
        Screen: scrSettings,
        Icon: Icon.Settings
    }
)
```

#### Step 8: Responsive Footer

```powerFx
// conFooter
conFooter.X: 0
conFooter.Y: App.Height - Self.Height
conFooter.Width: App.Width
conFooter.Height: If(varIsPhone, 60, 40)

// Footer content stacks on phone, horizontal on desktop
lblCopyright.Text: "© 2025 Company Name"
lblCopyright.X:
    If(
        varIsPhone,
        (Parent.Width - Self.Width) / 2,  // Centered on phone
        varSpacing.MD  // Left aligned on desktop
    )
lblCopyright.Y: (Parent.Height - Self.Height) / 2
lblCopyright.Size: varFontSize.Small

lblVersion.Text: "Version 1.0.0"
lblVersion.X:
    If(
        varIsPhone,
        (Parent.Width - Self.Width) / 2,
        Parent.Width - Self.Width - varSpacing.MD
    )
lblVersion.Y:
    If(
        varIsPhone,
        lblCopyright.Y + lblCopyright.Height + varSpacing.XS,
        (Parent.Height - Self.Height) / 2
    )
```

### Assignment Checkpoint (Part 1 & 2)

✅ **Set up responsive foundation:**

- Configure app settings for responsive design
- Create responsive variables in App.OnStart
- Implement size change detection timer
- Test on different screen sizes

✅ **Create responsive navigation:**

- Desktop horizontal navigation
- Mobile hamburger menu
- Sliding mobile menu panel
- Navigation transitions
- Test on phone and desktop

✅ **Documentation:**

- Breakpoints defined
- Spacing and font size scales
- Layout patterns documented

**Continue to Part 3 for responsive forms and data displays...**

---

## Module 21 Progress Summary

**Part 1 Completed:** Responsive Fundamentals (4 hours)
**Part 2 Completed:** Responsive Navigation (4 hours)

**Completed: 8 hours of 24 hours**

**Remaining:**

- Part 3: Responsive Forms (4 hours)
- Part 4: Responsive Data Display (4 hours)
- Part 5: Responsive Dashboard (4 hours)
- Part 6: Complete Leave Management System Responsive Implementation (8 hours)

---

## Stage 3 Part 3 Progress Summary

### Modules Completed in This Section:

- ✅ Module 19: Offline Capability (1 hour)
- ✅ Module 20: Monitoring Tool (1 hour)
- ✅ Module 21 Part 1 & 2: Responsive Design Fundamentals & Navigation (8 hours)

### Completed in Part 3: 10 hours

### Cumulative Stage 3 Progress: 20 hours of 36 hours

### Next Up

Module 21 continuation: Responsive Forms, Data Display, Dashboard, and complete Leave Management System responsive implementation.

---

**Type "NEXT" to continue with Responsive Forms, Data Display, and Dashboard (remaining 16 hours of Module 21).**
