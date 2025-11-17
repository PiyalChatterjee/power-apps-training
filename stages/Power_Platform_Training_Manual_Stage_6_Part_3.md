# Power Platform Training Manual - Stage 6 Part 3: Power Pages

## Overview

This is Part 3 of Stage 6, covering Power Pages for creating external-facing websites. Learn to build secure portals that allow external users to interact with Dataverse data through web forms, lists, and custom pages. **This part covers 8 hours of Module 46-47.**

**Stage 6 Total Duration: 53 hours** | **Part 3: 8 hours**

---

## Module 46: Introduction to Power Pages (4 hours)

### Learning Objectives

- Understand Power Pages architecture
- Create your first Power Page site
- Configure site settings and authentication
- Design web pages and templates
- Add web forms for data entry
- Create lists to display Dataverse data
- Configure table permissions
- Set up user roles and access
- Customize site theme and branding
- Publish and share the portal

### Step-by-Step Instructions

#### Step 1: What are Power Pages?

**Definition:**

```
Power Pages = External-facing websites built on Dataverse

Key Features:
✅ Public or authenticated access
✅ Interact with Dataverse data
✅ Responsive design (mobile, tablet, desktop)
✅ Built-in authentication (Azure AD, social logins)
✅ Low-code/no-code customization
✅ Forms, lists, and custom pages
✅ Rich text editor and design studio
✅ Advanced customization with HTML/CSS/JavaScript/Liquid

Use Cases:
- Customer self-service portals
- Employee portals
- Partner portals
- Community forums
- Knowledge bases
- Event registration sites
- Application submission portals
```

**Architecture:**

```
Power Pages Stack:

User Browser
    ↓
Power Pages Website (public URL)
    ↓
Web Pages (templates, layouts)
    ↓
Components (forms, lists, content)
    ↓
Dataverse (data + security)
    ↓
Authentication (Azure AD, social, local)

Security Layers:
1. Anonymous access (public pages)
2. Authenticated users (login required)
3. Table permissions (what data users can access)
4. Column permissions (what fields users can see)
5. Web roles (group permissions)
```

**Power Pages vs Model-Driven Apps:**

```
Power Pages:
✅ External users (customers, partners)
✅ Public or authenticated access
✅ Custom branded design
✅ SEO optimized
✅ Public URL (yourdomain.powerappsportals.com)
❌ Requires separate license per login/page view

Model-Driven Apps:
✅ Internal users (employees)
✅ Full Microsoft 365 integration
✅ Metadata-driven UI
✅ Included with Power Apps license
❌ Not for external users
❌ Not publicly accessible

Use Both:
Employees use Model-Driven App (internal)
Customers use Power Pages (external)
Same Dataverse backend
```

#### Step 2: Create Your First Power Page

**Navigate to Power Pages:**

```
1. make.powerapps.com
2. Select environment
3. + Create (left navigation)
4. Select: Blank site (Power Pages)

OR

1. make.powerpages.microsoft.com
2. + Create site
```

**Configure New Site:**

```
1. Choose template:
   - Blank site (start from scratch)
   - Customer self-service (pre-built)
   - Partner portal (B2B scenarios)
   - Employee self-service (internal portal)
   - Community (forum-based)

   Select: Blank site (for learning)

2. Configure site:
   Name: Leave Management Portal
   Address: leave-management-<random> (URL)
   Language: English

3. Click: Create

Site provisioning begins (2-5 minutes)
Status: Preparing site...
```

**Site Provisioning:**

```
What Happens During Provisioning:
✅ Creates site record in Dataverse
✅ Deploys default web pages
✅ Configures authentication
✅ Sets up security roles
✅ Creates default web templates
✅ Assigns you as site administrator

When Complete:
- Site URL is active
- Design Studio opens
- You can start customizing
```

#### Step 3: Power Pages Design Studio

**Design Studio Overview:**

```
Interface Components:

Left Panel - Pages:
- All web pages in site
- Page hierarchy
- Add new pages

Top Toolbar:
- Edit (design mode)
- Preview
- Sync (refresh configuration)
- Settings
- Share (publish)

Center - Canvas:
- Visual page editor
- Drag and drop components
- WYSIWYG (What You See Is What You Get)

Right Panel - Component Properties:
- Configure selected component
- Styling options
- Data binding
```

**Create Home Page:**

```
1. Pages panel (left)
2. Select: Home page (default page)
3. Canvas shows current layout

4. Edit page title:
   - Click title section
   - Right panel → Properties
   - Title: "Leave Management Portal"
   - Subtitle: "Submit and track your leave requests"

5. Add hero section:
   - Click + between sections
   - Choose: Text
   - Enter welcome message
   - Style: Heading 1, centered

6. Add button:
   - Click + after text
   - Choose: Button
   - Text: "Submit Leave Request"
   - Link to: (create form page later)
   - Style: Primary button

7. Save page (top right)
```

#### Step 4: Create Web Pages

**Add New Page:**

```
1. Pages panel (left)
2. + New page
3. Choose layout:
   - Blank (start from scratch)
   - With sidebar (content + sidebar)
   - Full width (single column)
   - Two columns (side by side)

4. Configure page:
   Name: Submit Leave Request
   Partial URL: submit-request (URL path)

5. Click: Add

New page created and opens in designer
```

**Page Templates:**

```
Available Layouts:

Blank:
- Empty page
- Full control
- Add sections manually

Page with sidebar:
- Main content (wide)
- Sidebar (narrow)
- Good for: Content + navigation

Full width:
- Single column spanning full width
- Good for: Forms, dashboards

Two columns:
- Left and right columns equal
- Good for: Comparison, side-by-side content
```

**Add Components to Page:**

```
Component Types:

Text:
- Headings (H1-H6)
- Paragraphs
- Rich text
- Use for: Content, instructions

Form:
- Dataverse form
- Submit/edit records
- Use for: Data entry

List:
- Display Dataverse records
- Filterable, sortable
- Use for: View multiple records

IFrame:
- Embed external content
- Use for: Videos, external pages

Spacer:
- Add vertical space
- Use for: Layout spacing

Divider:
- Horizontal line
- Use for: Visual separation

Image:
- Upload or link to image
- Use for: Logos, photos, graphics

Video:
- Embed video
- Use for: Tutorials, demonstrations
```

#### Step 5: Add Forms to Pages

**What are Web Forms?**

```
Web Form = Allow users to create/edit Dataverse records via portal

Types:
- Basic Form: Single record (create or edit)
- Multi-Step Form: Wizard with multiple steps
- Read-only Form: Display record details

Features:
✅ Based on Dataverse forms
✅ Validation rules
✅ File uploads
✅ Conditional fields
✅ Custom submit actions
```

**Create Basic Form:**

```
1. Open page: Submit Leave Request
2. Click + to add component
3. Select: Form
4. Configure form:

   Form Name: Leave Request Submission
   Table: Leave Request
   Form Mode: Insert (create new record)
   Dataverse Form: Main form (select existing)

   On Success:
   - Display message: "Leave request submitted successfully!"
   - Redirect to: My Leave Requests page

5. Save

Form appears on page with all fields from Dataverse form
```

**Form Configuration Options:**

```
Form Properties:

Mode:
- Insert: Create new record
- Edit: Update existing record
- Read-only: View record details

Form Selection:
- Choose which Dataverse form to use
- Can use different forms for different scenarios

Authentication:
- Require authentication: Yes/No
- If Yes: User must login to see form

On Submit:
- Show success message
- Redirect to page
- Redirect to URL
- Hide form (stay on page)

Validation:
- Client-side (browser)
- Server-side (Dataverse business rules)
- Custom JavaScript validation

Attachments:
- Enable file uploads
- Max file size
- Allowed file types
```

**Multi-Step Form (Wizard):**

```
Use Case: Complex data entry across multiple pages

Example: Employee Onboarding

Step 1: Personal Information
- Name, Email, Phone
- Address
- Emergency Contact

Step 2: Employment Details
- Department
- Job Title
- Manager
- Start Date

Step 3: Benefits Enrollment
- Health insurance
- Retirement plan
- Leave allocation

Step 4: Review and Submit
- Review all information
- Submit

Configuration:
1. Add Form component
2. Select: Multi-step form
3. Configure steps:
   - Each step = tab in Dataverse form
   - OR each step = different form
4. Add navigation (Previous, Next, Submit buttons)
5. Save progress between steps
```

#### Step 6: Add Lists to Pages

**What are Lists?**

```
List = Display multiple Dataverse records in table format

Features:
✅ Based on Dataverse views
✅ Pagination
✅ Sorting
✅ Filtering
✅ Search
✅ Actions (View, Edit, Delete)
✅ Export to Excel
✅ Custom columns
```

**Create List Page:**

```
1. Create new page: My Leave Requests
2. Add component: List
3. Configure:

   List Name: My Leave Requests
   Table: Leave Request
   View: Active Leave Requests

   Display Options:
   - Show search: Yes
   - Enable filtering: Yes
   - Enable sorting: Yes
   - Items per page: 10

   Actions:
   - View details: Enable
   - Edit record: Enable (if user has permission)
   - Delete record: Disable (security)

4. Save

List displays all leave requests (filtered by security)
```

**List Actions:**

```
Built-in Actions:

View:
- Opens record details page
- Read-only view
- Configure details page

Edit:
- Opens edit form
- User can modify record
- Requires write permission

Delete:
- Removes record
- Requires delete permission
- Confirm dialog

Download:
- Export to Excel
- Current page or all records

Custom Actions:
- Approve/Reject buttons
- Custom workflows
- JavaScript handlers
```

**List Filters:**

```
Enable User Filtering:

1. List properties → Filters
2. Add filter options:
   - Status (Dropdown: All, Pending, Approved, Rejected)
   - Date Range (From date, To date)
   - Leave Type (Dropdown: All, Annual, Sick, Emergency)

3. Save

Users can filter visible records dynamically
Filters apply instantly without page reload
```

#### Step 7: Configure Authentication

**Authentication Methods:**

```
Power Pages supports multiple authentication:

Azure AD (Microsoft Entra ID):
✅ Microsoft 365 accounts
✅ Enterprise users
✅ SSO (Single Sign-On)
Use for: Employee portals

Azure AD B2C:
✅ External user management
✅ Social logins (Google, Facebook, LinkedIn)
✅ Local accounts (email + password)
Use for: Customer portals

OAuth 2.0:
✅ Google
✅ Facebook
✅ LinkedIn
✅ Twitter
Use for: Public portals with social login

Local Authentication:
✅ Email + password
✅ Managed in Dataverse
✅ Registration workflow
Use for: Simple portals

SAML 2.0:
✅ Enterprise identity providers
✅ Federation
Use for: Partner portals
```

**Enable Authentication:**

```
1. Design Studio → Set up (left panel)
2. Identity providers
3. + Add provider
4. Choose provider type:

For Azure AD:
- Provider: Azure AD
- Client ID: (from Azure app registration)
- Client Secret: (from Azure app registration)
- Authority: https://login.microsoftonline.com/{tenant-id}
- Redirect URI: Automatically configured

5. Configure login page:
   - Login button text: "Sign in with Microsoft"
   - Registration: Enable/Disable

6. Save

Login button appears on site
Users can authenticate
```

**Configure Registration:**

```
Enable User Registration:

1. Set up → Identity providers
2. Select provider (e.g., Local authentication)
3. Registration settings:
   - Enable registration: Yes
   - Require email confirmation: Yes
   - Registration form fields:
     * Email (required)
     * Password (required)
     * First Name (required)
     * Last Name (required)
     * Phone (optional)

4. Email confirmation:
   - Send verification email
   - User clicks link to activate account

5. Save

"Sign Up" link appears on login page
New users can self-register
```

**Profile Management:**

```
User Profile Page:

1. Create page: My Profile
2. Add Form component:
   - Table: Contact
   - Mode: Edit
   - Record: Current user's contact
   - Form: Profile form

3. Fields displayed:
   - First Name
   - Last Name
   - Email
   - Phone
   - Photo
   - Preferences

4. Save

Users can update their own profile
Changes saved to Dataverse Contact record
```

#### Step 8: Configure Table Permissions

**What are Table Permissions?**

```
Table Permission = Control what data portal users can access

Security Model:

Web Roles → Assign to users/contacts
    ↓
Table Permissions → Define data access
    ↓
Dataverse Records → Filtered by permissions

Example:
Web Role: Employee
Table Permission: Read Leave Requests where Owner = Current User
Result: Employees see only their own leave requests
```

**Create Table Permission:**

```
1. Set up → Table permissions
2. + New permission
3. Configure:

Permission Details:
- Name: Read Own Leave Requests
- Table: Leave Request
- Access Type: Read
- Permission Scope: Contact (user-scoped)

Scope Definition:
- Scope: Contact (current user)
- Relationship: Owner (cr123_createdbycontact)

Result: Users can read Leave Requests where they are the owner

4. Assign to Web Role:
   - Web Roles: Employee

5. Save and Publish
```

**Permission Scopes:**

```
Global:
- Access: All records in table
- Use for: Public data (e.g., product catalog)

Contact (User-scoped):
- Access: Records related to current user
- Use for: Personal data (my orders, my requests)
- Relationship required: Link record to Contact

Account (Organization-scoped):
- Access: Records related to user's account/company
- Use for: Company data (company orders, team documents)
- Relationship required: Link record to Account

Parent (Hierarchical):
- Access: Records based on parent relationship
- Use for: Nested data structures

Self:
- Access: Only the Contact record of logged-in user
- Use for: Profile management
```

**Access Types:**

```
Permission Types:

Read:
- View records
- See in lists
- Open details

Write:
- Update existing records
- Edit forms

Create:
- Add new records
- Submit forms

Delete:
- Remove records
- Requires explicit permission

Append:
- Attach related records

Append To:
- Allow record to be attached

All:
- Full access (CRUD)
- Use cautiously

Combinations:
- Read + Write: Can view and edit
- Read + Create: Can view and create new
- Read + Write + Create: Full except delete
```

**Example Permissions for Leave Management:**

```
Web Role: Employee

Permission 1: Read Own Leave Requests
- Table: Leave Request
- Access: Read
- Scope: Contact (Owner relationship)

Permission 2: Create Leave Requests
- Table: Leave Request
- Access: Create
- Scope: Contact (sets owner to current user)

Permission 3: Edit Draft Leave Requests
- Table: Leave Request
- Access: Write
- Scope: Contact (Owner)
- Additional Filter: Status = Draft

Permission 4: Read Leave Types
- Table: Leave Type
- Access: Read
- Scope: Global (all users can see all leave types)

Permission 5: Read Own Profile
- Table: Contact
- Access: Read + Write
- Scope: Self (own contact record only)
```

#### Step 9: Configure Web Roles

**What are Web Roles?**

```
Web Role = Group of table permissions assigned to users

Purpose:
- Simplify permission management
- Assign users to roles
- Roles define what users can do

Example Roles:
- Anonymous Users (public, no login)
- Authenticated Users (logged in, basic access)
- Employee (standard employee permissions)
- Manager (approval permissions)
- Administrator (full access)
```

**Create Web Role:**

```
1. Set up → Web roles
2. + New role
3. Configure:

Role Details:
- Name: Employee
- Description: Standard employee access to leave management

Authentication:
- Authenticated users only: Yes

4. Assign Table Permissions:
   - Read Own Leave Requests
   - Create Leave Requests
   - Edit Draft Leave Requests
   - Read Leave Types
   - Read Own Profile

5. Save

Role created but not yet assigned to users
```

**Assign Users to Web Roles:**

```
Method 1: Manual Assignment
1. Set up → Contacts
2. Select user contact
3. Web Roles section
4. + Add web role
5. Select: Employee
6. Save

Method 2: Auto-Assignment on Registration
1. Identity providers → Local authentication
2. Registration settings
3. Default web role: Employee
4. Save

All new registrants automatically get Employee role

Method 3: Via Workflow/Flow
- Trigger: When contact created
- Action: Assign web role based on criteria
- Example: If Email contains "@company.com", assign Employee role
```

**Role Hierarchy:**

```
Multiple Roles:

User can have multiple roles:
- Authenticated Users (base role, all logged-in users)
- Employee (standard permissions)
- Manager (additional approval permissions)

Permission Logic:
- Most permissive access wins
- If Employee role allows Read, Manager role allows Write
- User gets Write access (more permissive)

Best Practice:
- Create role hierarchy
- Base role: Authenticated Users (minimal access)
- Add specific roles: Employee, Manager, Admin
- Assign multiple roles as needed
```

#### Step 10: Customize Site Theme and Branding

**Theme Customization:**

```
1. Design Studio → Theme (top toolbar)
2. Theme settings open

Configure:
- Primary color: #0078D4 (brand color)
- Secondary color: #2B88D8
- Text color: #333333
- Background color: #FFFFFF
- Link color: #0078D4

3. Typography:
   - Heading font: Segoe UI
   - Body font: Segoe UI
   - Font sizes: H1 (32px), H2 (24px), Body (16px)

4. Buttons:
   - Primary button color: Primary color
   - Secondary button: Outlined
   - Button radius: 4px (rounded corners)

5. Header:
   - Logo: Upload company logo (recommended: 180x60px)
   - Header background: Primary color
   - Header text: White

6. Footer:
   - Footer background: Dark gray
   - Footer text: Light gray
   - Links: White

7. Save

Site immediately reflects new theme
```

**CSS Customization (Advanced):**

```
Add Custom CSS:

1. Set up → CSS
2. Add custom styles:

/* Custom button style */
.btn-submit {
    background-color: #28a745;
    color: white;
    padding: 12px 24px;
    border-radius: 6px;
    font-weight: 600;
}

.btn-submit:hover {
    background-color: #218838;
}

/* Custom table style */
.leave-request-list {
    border: 1px solid #ddd;
    border-radius: 8px;
}

/* Custom form layout */
.leave-form .form-group {
    margin-bottom: 20px;
}

3. Save

Apply classes to components:
- Select component
- Properties → Advanced → CSS class
- Enter: btn-submit
- Save

Component uses custom style
```

**Navigation Menu:**

```
Configure Main Menu:

1. Set up → Navigation
2. Main menu (header)
3. Edit menu items:

Home
Submit Leave Request → /submit-request
My Leave Requests → /my-requests
My Profile → /profile

4. Add dropdown (if needed):
   Leave Management ▼
   ├─ Submit Request
   ├─ My Requests
   └─ Leave Balance

5. Footer menu:
   About
   Contact Us
   Privacy Policy
   Terms of Service

6. Save

Menu appears in site header
Users can navigate easily
```

#### Step 11: Publish and Share Portal

**Publish Site:**

```
1. Design Studio → Preview (test first)
2. Check all pages, forms, lists
3. Test authentication
4. Test permissions

If everything works:
5. Click: Sync (top toolbar)
6. Wait for sync to complete

Site is now live at:
https://leave-management-xxx.powerappsportals.com

Share URL with users
```

**Custom Domain:**

```
Use Custom Domain (Optional):

1. Power Platform Admin Center
2. Select environment
3. Portal → Your portal
4. Portal details → Add custom domain
5. Enter: portal.yourcompany.com
6. Follow DNS configuration steps:
   - Add CNAME record in your DNS
   - Point to: provided portal URL
7. Verify domain
8. Enable SSL (automatic)

Result: Users access portal at your custom domain
```

**Portal Analytics:**

```
Monitor Portal Usage:

1. Power Platform Admin Center
2. Select portal
3. Analytics dashboard shows:
   - Page views
   - Unique visitors
   - Top pages
   - Authentication events
   - Error logs
   - Performance metrics

Use insights to:
- Optimize popular pages
- Fix errors
- Improve performance
- Understand user behavior
```

### Assignment Deliverable

✅ **Create Leave Management Portal:**

- Site name: Leave Management Portal
- Professional home page with hero section
- Clear navigation menu
- Branded theme (logo, colors, fonts)

✅ **Create functional pages:**

- Home page (welcome, overview)
- Submit Leave Request page (form)
- My Leave Requests page (list)
- Leave Request Details page (read-only)
- My Profile page (edit profile)

✅ **Configure authentication:**

- Enable Azure AD OR local authentication
- Set up registration workflow
- Email confirmation
- Profile management

✅ **Implement security:**

- Create web roles: Employee, Manager
- Configure table permissions for each role
- Test permissions (create test users)
- Verify users see only their data

✅ **Customize branding:**

- Upload company logo
- Set brand colors
- Configure typography
- Customize header and footer
- Responsive design (test mobile)

✅ **Test and document:**

- Test all forms (create, edit)
- Test all lists (view, filter, sort)
- Test authentication (login, logout, registration)
- Test permissions (different roles)
- Document user guide for portal

### Knowledge Check

- [ ] What are Power Pages?
- [ ] When should you use Power Pages vs Model-Driven Apps?
- [ ] What components can you add to pages?
- [ ] What is a web form?
- [ ] What is a list component?
- [ ] What authentication methods are available?
- [ ] What are table permissions?
- [ ] What are permission scopes?
- [ ] What are web roles?
- [ ] How do you customize the site theme?

### Resources

- [Microsoft Learn: Power Pages overview](https://learn.microsoft.com/en-us/power-pages/introduction)
- [Microsoft Learn: Create your first site](https://learn.microsoft.com/en-us/power-pages/getting-started/create-manage)
- [Microsoft Learn: Configure authentication](https://learn.microsoft.com/en-us/power-pages/security/authentication/)
- [Microsoft Learn: Table permissions](https://learn.microsoft.com/en-us/power-pages/security/table-permissions)

---

## Module 47: Advanced Power Pages Development (4 hours)

### Learning Objectives

- Use Liquid templates for dynamic content
- Create custom web templates
- Implement advanced forms with JavaScript
- Use Dataverse Web API from portal
- Create custom page layouts
- Implement file uploads and downloads
- Add real-time notifications
- Use FetchXML in lists
- Implement advanced security scenarios
- Optimize portal performance

### Step-by-Step Instructions

#### Step 1: Introduction to Liquid Templates

**What is Liquid?**

```
Liquid = Open-source template language for Power Pages

Use For:
✅ Dynamic content rendering
✅ Access Dataverse data
✅ Conditional logic (if/else)
✅ Loops (for each)
✅ Filters (formatting, manipulation)
✅ Variable operations

Similar to: Jinja2 (Python), Razor (C#)
```

**Basic Liquid Syntax:**

```liquid
{%  comment  %}
Liquid code blocks
{% endcomment %}

{{ variable }}
Output variable value

{% if condition %}
  Content if true
{% endif %}

{% for item in collection %}
  {{ item.name }}
{% endfor %}

{{ "text" | upcase }}
Apply filter (converts to uppercase)
```

**Access Current User:**

```liquid
<!-- Display current user name -->
<p>Welcome, {{ user.fullname }}!</p>

<!-- User properties -->
Email: {{ user.email }}
Phone: {{ user.telephone1 }}
Job Title: {{ user.jobtitle }}

<!-- Check if user is authenticated -->
{% if user %}
  <p>You are logged in</p>
{% else %}
  <p>Please log in</p>
{% endif %}
```

**Access Dataverse Data:**

```liquid
{%  comment  %}
Fetch single record by ID
{% endcomment %}

{% assign request = entities['cr123_leaverequest'][requestId] %}

<h2>Leave Request: {{ request.cr123_name }}</h2>
<p>Employee: {{ request.cr123_employee.fullname }}</p>
<p>Start Date: {{ request.cr123_startdate | date: "%B %d, %Y" }}</p>
<p>Total Days: {{ request.cr123_totaldays }}</p>
<p>Status: {{ request.cr123_status.label }}</p>
```

**FetchXML in Liquid:**

```liquid
{%  comment  %}
Query multiple records
{% endcomment %}

{% fetchxml leaveRequests %}
  <fetch>
    <entity name="cr123_leaverequest">
      <attribute name="cr123_name" />
      <attribute name="cr123_startdate" />
      <attribute name="cr123_totaldays" />
      <attribute name="cr123_status" />
      <filter>
        <condition attribute="cr123_employee" operator="eq" value="{{ user.id }}" />
        <condition attribute="cr123_status" operator="eq" value="865420000" />
      </filter>
      <order attribute="cr123_startdate" descending="false" />
    </entity>
  </fetch>
{% endfetchxml %}

<h2>My Pending Requests</h2>
<table>
  <thead>
    <tr>
      <th>Request</th>
      <th>Start Date</th>
      <th>Days</th>
      <th>Status</th>
    </tr>
  </thead>
  <tbody>
    {% for request in leaveRequests.results.entities %}
      <tr>
        <td>{{ request.cr123_name }}</td>
        <td>{{ request.cr123_startdate | date: "%m/%d/%Y" }}</td>
        <td>{{ request.cr123_totaldays }}</td>
        <td>{{ request.cr123_status.label }}</td>
      </tr>
    {% endfor %}
  </tbody>
</table>
```

**Conditional Logic:**

```liquid
{% if user.cr123_department.cr123_name == "IT" %}
  <div class="alert alert-info">
    <p>Welcome IT team member!</p>
  </div>
{% elsif user.cr123_department.cr123_name == "HR" %}
  <div class="alert alert-success">
    <p>Welcome HR team member! You have administrator access.</p>
  </div>
{% else %}
  <div class="alert alert-primary">
    <p>Welcome to the Leave Management Portal</p>
  </div>
{% endif %}
```

**Loops:**

```liquid
{% assign statuses = "Draft,Pending Approval,Approved,Rejected" | split: "," %}

<select name="status">
  <option value="">-- Select Status --</option>
  {% for status in statuses %}
    <option value="{{ status }}">{{ status }}</option>
  {% endfor %}
</select>
```

**Filters:**

```liquid
<!-- Text filters -->
{{ "hello world" | capitalize }}  → Hello world
{{ "hello world" | upcase }}      → HELLO WORLD
{{ "HELLO WORLD" | downcase }}    → hello world

<!-- Date filters -->
{{ request.cr123_startdate | date: "%B %d, %Y" }}  → November 17, 2025
{{ request.cr123_startdate | date: "%m/%d/%Y" }}   → 11/17/2025

<!-- Number filters -->
{{ 1234.56 | round: 2 }}          → 1234.56
{{ 1234.56 | ceil }}              → 1235
{{ 1234.56 | floor }}             → 1234

<!-- Array filters -->
{{ myArray | size }}              → Count of items
{{ myArray | first }}             → First item
{{ myArray | last }}              → Last item

<!-- String filters -->
{{ "Hello World" | replace: "World", "Portal" }}  → Hello Portal
{{ "  trimme  " | strip }}                        → trimme
```

#### Step 2: Create Custom Web Template

**What is a Web Template?**

```
Web Template = Reusable HTML/Liquid layout

Use For:
✅ Page layouts (header, content, footer)
✅ Reusable components (navigation, sidebar)
✅ Dynamic sections
✅ Custom page types

Structure:
Web Template → Page Template → Web Page
```

**Create Web Template:**

```
1. Portal Management app (classic)
   OR
   Advanced settings → Portals → Web Templates

2. + New
3. Configure:

Name: Leave Request Card
Source:

<div class="leave-request-card">
  <div class="card">
    <div class="card-header">
      <h3>{{ request.cr123_name }}</h3>
      <span class="badge badge-{{ request.cr123_status.value }}">
        {{ request.cr123_status.label }}
      </span>
    </div>
    <div class="card-body">
      <p><strong>Employee:</strong> {{ request.cr123_employee.fullname }}</p>
      <p><strong>Leave Type:</strong> {{ request.cr123_leavetype.cr123_name }}</p>
      <p><strong>Start Date:</strong> {{ request.cr123_startdate | date: "%B %d, %Y" }}</p>
      <p><strong>End Date:</strong> {{ request.cr123_enddate | date: "%B %d, %Y" }}</p>
      <p><strong>Total Days:</strong> {{ request.cr123_totaldays }}</p>
    </div>
    <div class="card-footer">
      <a href="/request-details?id={{ request.cr123_leaverequestid }}" class="btn btn-primary">
        View Details
      </a>
    </div>
  </div>
</div>

4. Save

Template available for reuse
```

**Use Web Template in Page:**

```
1. Create new page
2. Edit page content (code view)
3. Include template:

{% include 'Leave Request Card' with request: requestRecord %}

OR loop through multiple:

{% for request in requests %}
  {% include 'Leave Request Card' with request: request %}
{% endfor %}
```

**Page Template (Layout):**

```
Create complete page layout:

Name: Two Column Layout with Sidebar

<div class="container">
  <div class="row">
    <!-- Main Content -->
    <div class="col-md-8">
      {% block main %}
        <!-- Page content goes here -->
      {% endblock %}
    </div>

    <!-- Sidebar -->
    <div class="col-md-4">
      {% block sidebar %}
        <!-- Quick Links -->
        <div class="sidebar-widget">
          <h4>Quick Links</h4>
          <ul>
            <li><a href="/submit-request">Submit Request</a></li>
            <li><a href="/my-requests">My Requests</a></li>
            <li><a href="/leave-balance">Leave Balance</a></li>
          </ul>
        </div>

        <!-- Leave Balance Widget -->
        <div class="sidebar-widget">
          <h4>Leave Balance</h4>
          {% fetchxml balance %}
            <fetch>
              <entity name="cr123_leavebalance">
                <attribute name="cr123_availabledays" />
                <attribute name="cr123_leavetype" />
                <filter>
                  <condition attribute="cr123_employee" operator="eq" value="{{ user.id }}" />
                </filter>
              </entity>
            </fetch>
          {% endfetchxml %}

          <ul>
            {% for bal in balance.results.entities %}
              <li>
                {{ bal.cr123_leavetype.cr123_name }}:
                {{ bal.cr123_availabledays }} days
              </li>
            {% endfor %}
          </ul>
        </div>
      {% endblock %}
    </div>
  </div>
</div>

Use this layout for multiple pages
```

#### Step 3: Advanced Form Customization with JavaScript

**Portal Form JavaScript:**

```
Enhance forms with custom logic

Common Scenarios:
✅ Field validation
✅ Dynamic show/hide
✅ Calculate values
✅ Pre-fill fields
✅ Custom submit logic
```

**Add JavaScript to Form:**

```javascript
// Custom script for Leave Request form

$(document).ready(function () {
  // Get form fields
  var startDateField = $("#cr123_startdate");
  var endDateField = $("#cr123_enddate");
  var totalDaysField = $("#cr123_totaldays");
  var leaveTypeField = $("#cr123_leavetype");
  var commentsField = $("#cr123_comments");

  // Calculate total days on date change
  function calculateTotalDays() {
    var startDate = new Date(startDateField.val());
    var endDate = new Date(endDateField.val());

    if (startDate && endDate && endDate >= startDate) {
      var diffTime = Math.abs(endDate - startDate);
      var diffDays = Math.ceil(diffTime / (1000 * 60 * 60 * 24)) + 1;
      totalDaysField.val(diffDays);
      totalDaysField.trigger("change");
    }
  }

  startDateField.on("change", calculateTotalDays);
  endDateField.on("change", calculateTotalDays);

  // Show/hide comments based on leave type
  leaveTypeField.on("change", function () {
    var selectedType = $(this).find("option:selected").text();

    if (selectedType === "Sick") {
      commentsField.closest(".form-group").show();
      commentsField.attr("required", "required");
      commentsField
        .closest(".form-group")
        .find("label")
        .append(' <span class="required">*</span>');
    } else {
      commentsField.closest(".form-group").hide();
      commentsField.removeAttr("required");
    }
  });

  // Validate before submit
  $("form").on("submit", function (e) {
    var startDate = new Date(startDateField.val());
    var endDate = new Date(endDateField.val());
    var today = new Date();
    today.setHours(0, 0, 0, 0);

    // Check if start date is in past
    if (startDate < today) {
      alert("Start date cannot be in the past");
      e.preventDefault();
      return false;
    }

    // Check if end date is before start date
    if (endDate < startDate) {
      alert("End date must be after start date");
      e.preventDefault();
      return false;
    }

    return true;
  });

  // Initialize on page load
  calculateTotalDays();
  leaveTypeField.trigger("change");
});
```

**Register JavaScript:**

```
Method 1: Inline (in page)
1. Edit page
2. Switch to code view
3. Add <script> tag with code
4. Save

Method 2: Web File (recommended)
1. Upload JS file as Web File
2. Reference in page:
   <script src="/LeaveRequestForm.js"></script>

Method 3: Custom JavaScript field
1. Edit form in Power Pages
2. Form properties → Custom JavaScript
3. Paste code
4. Save
```

#### Step 4: Dataverse Web API from Portal

**Access Web API:**

```
Call Dataverse Web API from portal JavaScript

Use For:
✅ CRUD operations without page reload
✅ Real-time data updates
✅ Dynamic content loading
✅ AJAX operations
```

**Web API Examples:**

```javascript
// Get portal Web API wrapper
var webapi = window.shell.webapi;

// CREATE: Create new leave request
function createLeaveRequest(data) {
  webapi.safeAjax({
    type: "POST",
    url: "/_api/cr123_leaverequests",
    contentType: "application/json",
    data: JSON.stringify(data),
    success: function (result) {
      console.log("Leave request created:", result.cr123_leaverequestid);
      alert("Leave request submitted successfully!");
    },
    error: function (error) {
      console.error("Error:", error);
      alert("Error submitting request");
    },
  });
}

// Example usage
var newRequest = {
  cr123_startdate: "2025-11-20",
  cr123_enddate: "2025-11-22",
  cr123_totaldays: 3,
  cr123_comments: "Family vacation",
  "cr123_leavetype@odata.bind": "/cr123_leavetypes(LEAVE-TYPE-GUID)",
  "cr123_employee@odata.bind": "/contacts(EMPLOYEE-GUID)",
};

createLeaveRequest(newRequest);

// READ: Get leave request by ID
function getLeaveRequest(requestId) {
  webapi.safeAjax({
    type: "GET",
    url:
      "/_api/cr123_leaverequests(" +
      requestId +
      ")?$select=cr123_name,cr123_startdate,cr123_totaldays",
    contentType: "application/json",
    success: function (result) {
      console.log("Request:", result);
      displayRequest(result);
    },
  });
}

// UPDATE: Update leave request
function updateLeaveRequest(requestId, updates) {
  webapi.safeAjax({
    type: "PATCH",
    url: "/_api/cr123_leaverequests(" + requestId + ")",
    contentType: "application/json",
    data: JSON.stringify(updates),
    success: function () {
      console.log("Request updated");
      alert("Request updated successfully!");
    },
  });
}

// Example usage
var updates = {
  cr123_comments: "Updated comments",
  cr123_totaldays: 4,
};

updateLeaveRequest("REQUEST-GUID", updates);

// DELETE: Delete leave request
function deleteLeaveRequest(requestId) {
  if (confirm("Are you sure you want to delete this request?")) {
    webapi.safeAjax({
      type: "DELETE",
      url: "/_api/cr123_leaverequests(" + requestId + ")",
      contentType: "application/json",
      success: function () {
        console.log("Request deleted");
        alert("Request deleted successfully!");
        window.location.reload();
      },
    });
  }
}

// QUERY: Fetch with filters
function getMyPendingRequests() {
  var filter =
    "$filter=_cr123_employee_value eq " +
    userId +
    " and cr123_status eq 865420000" +
    "&$orderby=cr123_startdate asc";

  webapi.safeAjax({
    type: "GET",
    url: "/_api/cr123_leaverequests?" + filter,
    contentType: "application/json",
    success: function (result) {
      console.log("Pending requests:", result.value);
      displayRequests(result.value);
    },
  });
}
```

**Real-Time Updates:**

```javascript
// Auto-refresh list every 30 seconds
setInterval(function () {
  refreshLeaveRequestsList();
}, 30000);

function refreshLeaveRequestsList() {
  webapi.safeAjax({
    type: "GET",
    url: "/_api/cr123_leaverequests?$filter=_cr123_employee_value eq " + userId,
    success: function (result) {
      updateListDisplay(result.value);
    },
  });
}

function updateListDisplay(requests) {
  var container = $("#leave-requests-list");
  container.empty();

  requests.forEach(function (request) {
    var card = createRequestCard(request);
    container.append(card);
  });
}
```

#### Step 5: File Upload and Download

**Enable File Upload on Form:**

```
1. Edit form component
2. Form properties → Attachments
3. Configure:
   - Allow file uploads: Yes
   - Accept file types: .pdf,.doc,.docx,.jpg,.png
   - Max file size: 10 MB
   - Multiple files: Yes

4. Save

File upload field appears on form
Files stored as Notes attachments in Dataverse
```

**Custom File Upload with JavaScript:**

```html
<div class="file-upload-section">
  <h4>Attach Supporting Documents</h4>
  <input type="file" id="fileInput" multiple accept=".pdf,.doc,.docx" />
  <button onclick="uploadFiles()" class="btn btn-primary">Upload</button>
  <div id="uploadStatus"></div>
</div>

<script>
  function uploadFiles() {
    var fileInput = document.getElementById("fileInput");
    var files = fileInput.files;
    var requestId = getRequestIdFromUrl();

    if (files.length === 0) {
      alert("Please select files to upload");
      return;
    }

    for (var i = 0; i < files.length; i++) {
      uploadFile(files[i], requestId);
    }
  }

  function uploadFile(file, requestId) {
    var reader = new FileReader();
    reader.onload = function (e) {
      var base64 = e.target.result.split(",")[1];

      var annotation = {
        "objectid_cr123_leaverequest@odata.bind":
          "/cr123_leaverequests(" + requestId + ")",
        subject: file.name,
        filename: file.name,
        documentbody: base64,
        mimetype: file.type,
      };

      webapi.safeAjax({
        type: "POST",
        url: "/_api/annotations",
        contentType: "application/json",
        data: JSON.stringify(annotation),
        success: function () {
          document.getElementById("uploadStatus").innerHTML +=
            "<p>✓ " + file.name + " uploaded successfully</p>";
        },
        error: function () {
          document.getElementById("uploadStatus").innerHTML +=
            "<p>✗ " + file.name + " upload failed</p>";
        },
      });
    };
    reader.readAsDataURL(file);
  }
</script>
```

**Download Files:**

```javascript
// Display list of attachments
function displayAttachments(requestId) {
  webapi.safeAjax({
    type: "GET",
    url:
      "/_api/annotations?$filter=_objectid_value eq " +
      requestId +
      "&$select=annotationid,filename,filesize,createdon",
    success: function (result) {
      var attachmentsList = $("#attachments-list");
      attachmentsList.empty();

      result.value.forEach(function (file) {
        var listItem = $("<li></li>");
        var link = $("<a></a>")
          .attr("href", "#")
          .text(file.filename + " (" + formatFileSize(file.filesize) + ")")
          .click(function () {
            downloadFile(file.annotationid, file.filename);
            return false;
          });
        listItem.append(link);
        attachmentsList.append(listItem);
      });
    },
  });
}

function downloadFile(annotationId, filename) {
  webapi.safeAjax({
    type: "GET",
    url:
      "/_api/annotations(" + annotationId + ")?$select=documentbody,mimetype",
    success: function (result) {
      var base64 = result.documentbody;
      var mimeType = result.mimetype;

      // Convert base64 to blob
      var byteCharacters = atob(base64);
      var byteNumbers = new Array(byteCharacters.length);
      for (var i = 0; i < byteCharacters.length; i++) {
        byteNumbers[i] = byteCharacters.charCodeAt(i);
      }
      var byteArray = new Uint8Array(byteNumbers);
      var blob = new Blob([byteArray], { type: mimeType });

      // Download
      var link = document.createElement("a");
      link.href = window.URL.createObjectURL(blob);
      link.download = filename;
      link.click();
    },
  });
}

function formatFileSize(bytes) {
  if (bytes < 1024) return bytes + " B";
  if (bytes < 1048576) return (bytes / 1024).toFixed(1) + " KB";
  return (bytes / 1048576).toFixed(1) + " MB";
}
```

#### Step 6: Advanced Security Scenarios

**Scenario 1: Manager Can Approve Team Requests**

```
Table Permission: Approve Team Leave Requests
- Table: Leave Request
- Access: Write
- Scope: Contact (custom relationship)
- Relationship: Employee → Manager = Current User
- Additional Filter: Status = Pending Approval

Web Role: Manager
- Assign permission: Approve Team Leave Requests

Result: Managers can edit (approve) requests where they are the manager
```

**Scenario 2: HR Can See All Departments**

```
Table Permission: HR Full Access
- Table: Leave Request
- Access: Read, Write, Create, Delete
- Scope: Global (all records)

Table Permission: HR View All Employees
- Table: Contact (Employee)
- Access: Read
- Scope: Global

Web Role: HR Administrator
- Assign both permissions

Result: HR can see and manage all leave requests
```

**Scenario 3: Department-Based Access**

```
Table Permission: Department Leave Requests
- Table: Leave Request
- Access: Read
- Scope: Account (organization-scoped)
- Relationship: Employee → Department (Account)

Requires:
- Contact linked to Account (department)
- Leave Request → Employee → Department relationship

Result: Users see leave requests from their department only
```

**Column-Level Security:**

```
Scenario: Hide salary information from non-HR

1. Enable column security on field (Dataverse)
2. Create Column Security Profile: HR Salary Access
3. Grant Read permission on Salary field
4. Assign profile to HR web role

Result:
- HR users see Salary field
- Other users see field as blank or hidden
```

#### Step 7: Performance Optimization

**Optimize Portal Performance:**

```
Best Practices:

✅ Cache Static Content:
- Enable caching for web files (CSS, JS, images)
- Set cache expiration (24 hours for static assets)

✅ Minimize HTTP Requests:
- Combine CSS files
- Combine JavaScript files
- Use CSS sprites for images

✅ Optimize Images:
- Compress images before upload
- Use appropriate formats (JPG for photos, PNG for graphics)
- Use responsive images (different sizes for mobile/desktop)

✅ Lazy Load Content:
- Load images as user scrolls
- Load data on demand (not all at once)

✅ Optimize Queries:
- Use indexed fields in filters
- Limit query results (pagination)
- Select only needed columns
- Avoid N+1 queries (use $expand)

✅ Enable CDN:
- Power Pages automatically uses CDN for static assets
- Verify CDN is enabled in portal settings

✅ Minimize Liquid Processing:
- Cache frequently accessed data
- Avoid complex Liquid in loops
- Use FetchXML efficiently
```

**Measure Performance:**

```
Use Browser DevTools:

1. Chrome DevTools → Network tab
2. Reload portal page
3. Check metrics:
   - Page load time (goal: < 3 seconds)
   - Number of requests (goal: < 50)
   - Total page size (goal: < 2 MB)

4. Lighthouse audit:
   - Performance score (goal: > 80)
   - Recommendations for improvement

5. Portal analytics:
   - Average page load time
   - Identify slow pages
   - Optimize bottlenecks
```

### Assignment Deliverable

✅ **Implement Liquid templates:**

- Create reusable Leave Request Card template
- Use FetchXML to query leave requests
- Display with filters and formatting
- Show current user context

✅ **Add JavaScript customizations:**

- Auto-calculate total days on forms
- Dynamic show/hide based on leave type
- Client-side validation
- Real-time updates using Web API

✅ **Implement file management:**

- File upload on leave request form
- Display list of attachments
- Download functionality
- File type and size validation

✅ **Configure advanced security:**

- Manager role (approve team requests)
- HR role (full access)
- Department-based filtering
- Test with multiple users

✅ **Optimize performance:**

- Compress images
- Minimize HTTP requests
- Optimize queries
- Measure page load times
- Achieve Lighthouse score > 80

✅ **Create advanced features:**

- Leave balance dashboard (Liquid)
- Search functionality
- Notification system
- Report generation
- Mobile-optimized views

### Knowledge Check

- [ ] What is Liquid?
- [ ] How do you access Dataverse data in Liquid?
- [ ] What are web templates?
- [ ] How do you add JavaScript to forms?
- [ ] What is the portal Web API?
- [ ] How do you upload files?
- [ ] How do you implement complex permissions?
- [ ] What is column-level security?
- [ ] How do you optimize portal performance?
- [ ] How do you measure performance?

### Resources

- [Microsoft Learn: Liquid templates](https://learn.microsoft.com/en-us/power-pages/configure/liquid/liquid-overview)
- [Microsoft Learn: Web API](https://learn.microsoft.com/en-us/power-pages/configure/web-api-overview)
- [Microsoft Learn: Web templates](https://learn.microsoft.com/en-us/power-pages/configure/web-templates)
- [Microsoft Learn: Performance optimization](https://learn.microsoft.com/en-us/power-pages/admin/performance)

---

## Stage 6 Part 3 Complete! 🎉

### Completed Modules (8 hours):

- ✅ Module 46: Introduction to Power Pages (4 hours)
- ✅ Module 47: Advanced Power Pages Development (4 hours)

### Skills Acquired:

- Power Pages architecture and design
- Creating external-facing portals
- Design Studio and page builder
- Web forms for data entry
- List components for data display
- Authentication configuration (Azure AD, local, social)
- Table permissions and security
- Web roles and access control
- Custom branding and theming
- Liquid template language
- FetchXML in Liquid
- Custom web templates
- JavaScript form customization
- Dataverse Web API from portal
- File upload and download
- Advanced security scenarios
- Performance optimization
- Column-level security

### Leave Management Progress:

✅ Leave Management Portal created (external access)
✅ Submit Leave Request form (authenticated users)
✅ My Leave Requests list (user-scoped data)
✅ Authentication enabled (Azure AD/local)
✅ Web roles configured (Employee, Manager, HR)
✅ Table permissions implemented (security)
✅ Custom branding applied (logo, colors, theme)
✅ Liquid templates for dynamic content
✅ JavaScript enhancements (validation, calculations)
✅ File upload for supporting documents
✅ Performance optimized

### Overall Training Progress:

**Total Completed: 138 hours of 170 hours (81.2%)**

**Completed:**

- ✅ Stage 1: Power Apps Fundamentals (16.5 hours)
- ✅ Stage 2: Canvas Apps Intermediate (29 hours)
- ✅ Stage 3: Advanced Canvas Apps (36 hours)
- ✅ Stage 4: Cloud Flows (25.5 hours)
- ✅ Stage 5: Dataverse (10 hours)
- ✅ Stage 6 Part 1: Model-Driven Apps Fundamentals (8 hours)
- ✅ Stage 6 Part 2: Dashboards, Charts, and Views (5 hours)
- ✅ Stage 6 Part 3: Power Pages (8 hours)

**Remaining:**

- ⏳ Stage 6 Part 4: Solutions and ALM (10 hours)
- ⏳ Stage 6 Part 5: Final Project - Complete Leave Management System (22 hours)

---

**Type "NEXT" to continue with Stage 6 Part 4: Solutions and ALM (10 hours)**
