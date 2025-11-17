# 📝 Power Platform Training Assessments

> **Comprehensive assessment materials to validate your Power Platform knowledge and skills**

## 🎯 Assessment Overview

These assessments are designed to:

- **Validate Learning**: Ensure comprehension of key concepts
- **Identify Gaps**: Highlight areas needing additional study
- **Build Confidence**: Prepare for real-world scenarios and certifications
- **Track Progress**: Monitor skill development throughout training

## 📊 Assessment Types

### **Knowledge Checks** ✅

- **Format**: Multiple choice and true/false
- **Duration**: 5-10 minutes per module
- **Purpose**: Immediate reinforcement of concepts
- **Passing Score**: 70%

### **Practical Exercises** 🛠️

- **Format**: Hands-on building tasks
- **Duration**: 30-60 minutes per exercise
- **Purpose**: Apply learned skills in realistic scenarios
- **Assessment**: Functionality and best practices

### **Stage Assessments** 📋

- **Format**: Comprehensive mixed format
- **Duration**: 1-2 hours per stage
- **Purpose**: Validate stage completion readiness
- **Passing Score**: 80%

### **Final Certification Project** 🏆

- **Format**: Complete system build
- **Duration**: 20+ hours (Stage 6 Part 5)
- **Purpose**: Demonstrate professional competency
- **Assessment**: Full solution evaluation

---

## Stage 1: Power Platform Introduction Assessment

### **Knowledge Check (10 questions)**

#### **Question 1: Platform Overview**

Which of the following is NOT a core component of Microsoft Power Platform?

A) Power Apps  
B) Power Automate  
C) Power Virtual Agents  
D) Power Excel

<details>
<summary>Click to see answer</summary>

**Answer: D) Power Excel**

**Explanation**: Power Excel is not a Power Platform service. The core components are Power Apps, Power Automate, Power BI, Power Virtual Agents, and Power Pages.

</details>

#### **Question 2: Licensing**

What type of license is required to create and edit Power Apps?

A) Microsoft 365 E1  
B) Power Apps per user plan  
C) Azure Active Directory Premium  
D) Both A and B are correct

<details>
<summary>Click to see answer</summary>

**Answer: D) Both A and B are correct**

**Explanation**: You can create Power Apps with Microsoft 365 licenses (with some limitations) or dedicated Power Apps licenses for full functionality.

</details>

#### **Question 3: Environment Concepts**

Which statement about Power Platform environments is TRUE?

A) All users share a single environment  
B) Environments provide isolation for apps, flows, and data  
C) You can only have one environment per tenant  
D) Environments are only used for production deployments

<details>
<summary>Click to see answer</summary>

**Answer: B) Environments provide isolation for apps, flows, and data**

**Explanation**: Environments are containers that provide isolation and allow for separate development, test, and production spaces.

</details>

### **Practical Exercise: First App Creation**

**Objective**: Create a simple "Employee Directory" canvas app

**Requirements**:

1. Create a new canvas app from blank
2. Add a gallery to display employee information
3. Include search functionality
4. Add basic navigation between screens
5. Share the app with a colleague

**Assessment Criteria**:

- ✅ App successfully created and opens
- ✅ Gallery displays data correctly
- ✅ Search functionality works
- ✅ Navigation is functional
- ✅ App is properly shared

**Time Limit**: 45 minutes

---

## Stage 2: Canvas Apps Assessment

### **Knowledge Check (15 questions)**

#### **Question 1: PowerFx Formulas**

What is the correct formula to navigate to Screen2 when a button is clicked?

A) `Navigate(Screen2)`  
B) `Navigate("Screen2")`  
C) `Go(Screen2)`  
D) `Switch(Screen2)`

<details>
<summary>Click to see answer</summary>

**Answer: A) Navigate(Screen2)**

**Explanation**: Navigate function takes the screen object directly, not as a string.

</details>

#### **Question 2: Data Sources**

Which of the following is NOT a valid data source for Power Apps?

A) SharePoint List  
B) Excel Online  
C) Dataverse  
D) Local hard drive files

<details>
<summary>Click to see answer</summary>

**Answer: D) Local hard drive files**

**Explanation**: Power Apps cannot directly connect to local files on user devices for security reasons.

</details>

### **Practical Exercise: Expense Tracker App**

**Objective**: Build a comprehensive expense tracking application

**Requirements**:

1. **Data Model**: Create tables for Expenses, Categories, Employees
2. **Screens**:
   - Main dashboard with summary
   - Add/Edit expense screen
   - Expense history with filtering
3. **Features**:
   - Camera integration for receipts
   - Approval workflow integration
   - Export to Excel functionality
4. **UI/UX**: Responsive design, corporate branding

**Assessment Criteria**:

- ✅ Complete data model implementation
- ✅ All required screens functional
- ✅ Camera integration working
- ✅ Responsive design achieved
- ✅ Professional appearance and usability

**Time Limit**: 3 hours

---

## Stage 3: Advanced Canvas Apps Assessment

### **Knowledge Check (20 questions)**

#### **Question 1: Performance Optimization**

Which formula pattern is BEST for performance when working with large datasets?

A) `Filter(LargeTable, Column = "Value")`  
B) `Search(LargeTable, "Value", Column)`  
C) Using delegation with proper operators  
D) Loading all data into collections

<details>
<summary>Click to see answer</summary>

**Answer: C) Using delegation with proper operators**

**Explanation**: Delegation pushes filtering to the data source, handling large datasets efficiently.

</details>

#### **Question 2: Component Development**

What is the primary benefit of creating custom components?

A) Faster app loading  
B) Better security  
C) Reusability and maintainability  
D) Reduced licensing costs

<details>
<summary>Click to see answer</summary>

**Answer: C) Reusability and maintainability**

**Explanation**: Components allow you to create reusable UI elements that can be maintained centrally and used across multiple apps.

</details>

### **Advanced Practical Exercise: Business Application**

**Objective**: Create an advanced inventory management system

**Requirements**:

1. **Architecture**: Multi-screen app with component library
2. **Advanced Features**:
   - Barcode scanning integration
   - Offline capability with sync
   - Advanced analytics dashboard
   - Custom connector usage
3. **Performance**: Handle 10,000+ inventory items efficiently
4. **Security**: Role-based access control implementation
5. **Testing**: Include automated test cases

**Assessment Criteria**:

- ✅ Complex business logic implemented correctly
- ✅ Performance optimizations applied
- ✅ Security properly configured
- ✅ Offline functionality working
- ✅ Professional code quality and documentation

**Time Limit**: 6 hours

---

## Stage 4: Power Automate Assessment

### **Knowledge Check (15 questions)**

#### **Question 1: Trigger Types**

Which trigger type is BEST for processing items in a SharePoint list daily at 9 AM?

A) When an item is created  
B) Recurrence trigger  
C) When an item is modified  
D) PowerApps trigger

<details>
<summary>Click to see answer</summary>

**Answer: B) Recurrence trigger**

**Explanation**: Recurrence triggers run on a schedule, perfect for daily processing at specific times.

</details>

#### **Question 2: Error Handling**

What is the correct way to handle errors in a Power Automate flow?

A) Use Try-Catch blocks around all actions  
B) Configure run after settings for error paths  
C) Always use parallel branches  
D) Ignore errors - flows auto-retry

<details>
<summary>Click to see answer</summary>

**Answer: B) Configure run after settings for error paths**

**Explanation**: Power Automate uses "run after" settings to define what happens after an action succeeds, fails, times out, or is skipped.

</details>

### **Practical Exercise: Advanced Approval System**

**Objective**: Build a multi-level document approval workflow

**Requirements**:

1. **Approval Logic**: 3-level approval with escalation
2. **Integration**: SharePoint, Teams, Outlook, and custom database
3. **Features**:
   - Parallel and serial approvals
   - Timeout handling and reminders
   - Mobile-friendly approval interface
   - Audit trail and reporting
4. **Error Handling**: Comprehensive error management

**Assessment Criteria**:

- ✅ Complex approval logic working correctly
- ✅ All integrations functional
- ✅ Error handling comprehensive
- ✅ Mobile experience optimized
- ✅ Audit and compliance features implemented

**Time Limit**: 4 hours

---

## Stage 5: Dataverse Assessment

### **Knowledge Check (12 questions)**

#### **Question 1: Table Relationships**

What type of relationship should be used between Customer and Order tables?

A) One-to-One  
B) One-to-Many  
C) Many-to-Many  
D) Hierarchical

<details>
<summary>Click to see answer</summary>

**Answer: B) One-to-Many**

**Explanation**: One customer can have many orders, but each order belongs to one customer.

</details>

#### **Question 2: Security Roles**

Which security privilege allows a user to create records but not view all records?

A) Read privilege at Organization level  
B) Create privilege at User level  
C) Write privilege at Business Unit level  
D) Create privilege at Organization level

<details>
<summary>Click to see answer</summary>

**Answer: B) Create privilege at User level**

**Explanation**: Create privilege at User level allows creating records but limits visibility based on other privileges.

</details>

### **Practical Exercise: Enterprise Data Model**

**Objective**: Design and implement a complete CRM data model

**Requirements**:

1. **Entities**: Customer, Contact, Opportunity, Product, Order, Order Item
2. **Relationships**: Proper relationship configuration with referential integrity
3. **Security**: Role-based security with field-level restrictions
4. **Business Rules**: Validation and automation rules
5. **Views and Forms**: Optimized for different user roles

**Assessment Criteria**:

- ✅ Data model follows best practices
- ✅ Relationships properly configured
- ✅ Security model comprehensive
- ✅ Business rules effective
- ✅ User experience optimized

**Time Limit**: 3 hours

---

## Stage 6: Enterprise Applications Assessment

### **Comprehensive Final Assessment**

This assessment spans all components of Stage 6 and validates your ability to build enterprise-grade solutions.

### **Knowledge Check (25 questions)**

Covers all aspects:

- Model-driven app development
- Power Pages portal creation
- Solutions and ALM practices
- Integration patterns
- Security and governance

### **Capstone Project: Complete Leave Management System**

**Objective**: Build the complete enterprise leave management system as specified in Stage 6 Part 5

**Requirements**:

- All applications (Canvas, Model-driven, Power Pages)
- Complete workflow automation
- Comprehensive data model
- Production-ready deployment
- Full documentation

**Assessment Criteria**:

- ✅ **Functionality** (40%): All requirements met
- ✅ **Code Quality** (25%): Best practices followed
- ✅ **User Experience** (20%): Professional, intuitive design
- ✅ **Architecture** (15%): Scalable, maintainable solution

**Time Limit**: 20 hours (spread over multiple sessions)

---

## 🏆 Certification Preparation

### **Microsoft Certification Mock Tests**

#### **PL-100: App Maker**

- 50 questions covering Canvas and Model-driven apps
- Power Automate basics
- Basic Dataverse concepts
- **Practice Test Available**: [Link to assessment]

#### **PL-200: Functional Consultant**

- 60 questions covering solution design
- Advanced Dataverse and model-driven apps
- Business process implementation
- **Practice Test Available**: [Link to assessment]

#### **PL-400: Developer**

- 50 questions covering advanced development
- Custom connectors and PCF controls
- ALM and DevOps practices
- **Practice Test Available**: [Link to assessment]

---

## 📊 Progress Tracking

### **Skills Checklist**

Track your competency in each area:

#### **Canvas Apps Development**

- [ ] Basic app creation and navigation
- [ ] Data connections and formulas
- [ ] Advanced controls and components
- [ ] Performance optimization
- [ ] Custom connectors integration

#### **Model-Driven Apps**

- [ ] Entity design and relationships
- [ ] Form and view customization
- [ ] Business process flows
- [ ] Security implementation
- [ ] Client-side scripting

#### **Power Automate**

- [ ] Basic flow creation
- [ ] Advanced triggers and actions
- [ ] Error handling and monitoring
- [ ] Integration patterns
- [ ] Solution-aware flows

#### **Dataverse**

- [ ] Data modeling best practices
- [ ] Security role configuration
- [ ] Business rules implementation
- [ ] Advanced features usage

#### **Power Pages**

- [ ] Portal creation and configuration
- [ ] Custom page development
- [ ] Authentication setup
- [ ] Advanced customization

#### **ALM and DevOps**

- [ ] Solution architecture
- [ ] Environment management
- [ ] CI/CD implementation
- [ ] Monitoring and maintenance

### **Assessment Results Tracking**

| Stage   | Knowledge Check | Practical Exercise | Overall Score | Status |
| ------- | --------------- | ------------------ | ------------- | ------ |
| Stage 1 | \_\_\_/10       | \_\_\_/5           | \_\_\_%       | ⏳     |
| Stage 2 | \_\_\_/15       | \_\_\_/5           | \_\_\_%       | ⏳     |
| Stage 3 | \_\_\_/20       | \_\_\_/5           | \_\_\_%       | ⏳     |
| Stage 4 | \_\_\_/15       | \_\_\_/5           | \_\_\_%       | ⏳     |
| Stage 5 | \_\_\_/12       | \_\_\_/5           | \_\_\_%       | ⏳     |
| Stage 6 | \_\_\_/25       | \_\_\_/10          | \_\_\_%       | ⏳     |

**Legend**: ⏳ Not Started | 🔄 In Progress | ✅ Passed | ❌ Needs Retry

---

## 💡 Study Tips

### **Preparation Strategies**

1. **Review First**: Read through materials before attempting assessments
2. **Practice Regularly**: Use hands-on exercises to reinforce learning
3. **Time Management**: Don't spend too long on individual questions
4. **Real-World Focus**: Think about practical applications
5. **Mistake Learning**: Review incorrect answers thoroughly

### **Assessment Best Practices**

1. **Environment Setup**: Ensure stable internet and proper environment access
2. **Documentation**: Keep Microsoft documentation handy for reference
3. **Break Down Problems**: Divide complex requirements into smaller tasks
4. **Test Thoroughly**: Verify all functionality before submission
5. **Document Solutions**: Maintain notes on your approach and learnings

---

## 🔄 Retake Policy

### **Knowledge Checks**

- **Unlimited retakes** with 24-hour waiting period
- **Questions randomized** from larger pool
- **Immediate feedback** provided

### **Practical Exercises**

- **Two attempts** per exercise
- **Feedback provided** on first attempt
- **Improvement areas** highlighted

### **Stage Assessments**

- **One retake** after remedial study
- **Must score 80%** to advance
- **Comprehensive feedback** provided

---

## 📞 Support and Help

### **Getting Assessment Help**

- **GitHub Issues**: Technical problems with assessments
- **Study Groups**: Join community discussions
- **Mentorship**: Connect with experienced practitioners
- **Office Hours**: Virtual Q&A sessions

### **Accessibility**

- **Extended Time**: Available for qualified learners
- **Alternative Formats**: Screen reader compatible
- **Accommodation Requests**: Contact via GitHub issues

---

**🎯 Ready to test your knowledge? Start with [Stage 1 Assessment](./stage-1-assessment.md)!**

**📚 Return to [Main README](../README.md) | [Table of Contents](../TABLE_OF_CONTENTS.md)**
