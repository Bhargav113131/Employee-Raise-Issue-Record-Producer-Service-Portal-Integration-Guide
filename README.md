# Employee Raise Issue – Record Producer & Service Portal Integration Guide

## Overview

**Employee Raise Issue** is a ServiceNow project that gives employees a guided, trackable way to report an IT issue. An employee opens a Service Portal, uses the **Raise an Issue** widget and fills in the **Raise Employee Issue** form (a Record Producer). The answers are saved as an auto-numbered record in a custom table, with UI Policies and dependent choices keeping the data clean.

Everything is built inside a scoped application called **Employee Center**.

## Project Objectives

- **Standardize and improve the accuracy** of issue submission
- **Enforce business rules** (mandatory and read-only fields) through UI Policies
- **Control category and subcategory selection** with fixed choices and a dependency
- **Integrate the Service Portal** seamlessly with the backend table
- **Improve visibility** of requests and speed up issue resolution

## Key Features

- **Scoped application**: `Employee Center`, created in ServiceNow Studio
- **Custom table**: `Employee raises issue` with auto-number, Requester, Category, Subcategory, Short description, State, Priority and Assignment group
- **UI Policies**: Short description is mandatory when State is New; Number is read-only
- **Dependent choices**: Network → VPN, Hardware → Laptop, Software → Server, Access → Forgot password
- **Record Producer**: `Raise Employee Issue` with five variables mapped to the table fields
- **Service Portal**: `Requesting portal` (`/employee_request`) on the Employee Center Coral theme
- **Two custom widgets**: a Commercial widget and a Link redirect widget that opens the Record Producer

## Technology Stack

- **Platform**: ServiceNow (developer instance)
- **Tools**: ServiceNow Studio (App Engine), Service Catalog, Service Portal Configuration, Widget Editor, Designer
- **Languages**: HTML, CSS, JavaScript (AngularJS client controller)
- **Architecture**: ServiceNow Platform as a Service (PaaS)

## Project Phases

The repository is organised by project phase, and each folder holds the documents for that phase.

### 1. Ideation Phase
- Brainstorm & Idea Prioritization
- Problem Statement
- Empathy Map Canvas

### 2. Requirement Analysis
- Technology Stack (Architecture & Stack)
- Solution Requirements (Functional & Non-functional)
- Data Flow Diagram & User Stories

### 3. Project Design Phase
- Problem – Solution Fit
- Proposed Solution
- Solution Architecture

### 4. Project Planning Phase
- Product backlog, sprint schedule, project tracker, velocity and burndown chart

### 5. Project Development Phase
- User Acceptance Report
- ServiceNow Model Performance Test

### 6. Project Documentation
- Capstone Project Documentation

### 7. Project Demonstration
- Demonstration material for the project

## Installation & Setup

### Prerequisites
- A ServiceNow developer instance
- Administrative access (System Administrator) to the instance
- Basic familiarity with ServiceNow Studio, Service Catalog and Service Portal

### Build Steps

1. **Create the application**
   - All menus → App Engine → ServiceNow Studio → Create → App
   - Name: `Employee Center`, Scope: `Scoped`

2. **Create the custom table**
   - System Definition → Tables → New
   - Label: `Employee raises issue`; enable **Auto number** under Controls
   - Add the fields: Number, Requester (sys_user), Category, Subcategory, Short description, State, Priority, Assignment group (sys_user_group)

3. **Add the UI Policies**
   - Short description mandatory when `State is New`
   - Number field read-only (no condition)

4. **Add the dependency**
   - Configure the Subcategory dictionary entry (Advanced view) with **Dependent field = Category**
   - Set each Subcategory choice against its Category value

5. **Create the Record Producer**
   - Service Catalog → Catalog Definitions → Record Producers → New
   - Name: `Raise Employee Issue`, Table: `Employee raise issue`, Catalog: Service Catalog, Category: Service
   - Create five variables with **Map to Field** enabled and orders 100–500

6. **Create the Service Portal and page**
   - Title: `Requesting portal`, URL suffix: `employee_request`, Theme: Employee Center Coral Theme
   - Page: `Employee Center Portal Page` (`employee_center_portal_page`)

7. **Create the widgets**
   - `Commercial widget` (HTML + CSS)
   - `Link redirect` (HTML + CSS + client script)
   - Paste the Record Producer sys_id into `RECORD_PRODUCER_SYS_ID` in the client script

8. **Add the widgets to the page**
   - Open the Designer, add a 6 | 6 container, and place the Commercial widget on the left and the Link redirect widget on the right

9. **Test the system**
   - Open `https://your-instance.service-now.com/employee_request`
   - Select **Raise Ticket**, fill in the form, submit, and check the record

## Usage

### Raising an Issue
1. Open the Requesting portal at `/employee_request`
2. Scroll to the **Raise an Issue** widget and select **Raise Ticket →**
3. On the **Raise Employee Issue** form, choose the requester, category, subcategory, state and short description
4. Select **Submit**
5. A record is created in `Employee raises issue` with an auto-generated number

### What You Will See on the Record
- **Number** is read-only
- **Requester** is marked as required
- **Short description** becomes mandatory when State is New
- **Subcategory** options follow the selected Category on the table form

## Architecture

```
┌─────────────────────────────────────────────────┐
│  Scoped Application: Employee Center            │
├─────────────────────────────────────────────────┤
│  Presentation Layer                             │
│  ├─ Service Portal: Requesting portal           │
│  ├─ Employee Center Portal Page (6 | 6)         │
│  └─ Widgets: Commercial, Link redirect          │
├─────────────────────────────────────────────────┤
│  Service Catalog Layer                          │
│  └─ Record Producer: Raise Employee Issue       │
├─────────────────────────────────────────────────┤
│  Form Rules Layer                               │
│  ├─ UI Policy: Short description mandatory      │
│  ├─ UI Policy: Number read-only                 │
│  └─ Dictionary dependency: Category → Subcat.   │
├─────────────────────────────────────────────────┤
│  Data Layer                                     │
│  ├─ Employee raises issue (auto-number)         │
│  ├─ sys_user (Requester)                        │
│  └─ sys_user_group (Assignment group)           │
└─────────────────────────────────────────────────┘
```

## Configuration

### Key Configuration Areas

- **Table fields and choices**: Category (Network, Hardware, Software, Access), Subcategory (VPN, Laptop, Server, Forgot password), State (New, In progress, Resolved, Closed), Priority (Critical, High, Medium, Low)
- **UI Policies**: two policies on the `Employee raises issue` table
- **Record Producer variables**: Requester name, Category, Subcategory, State, Short description
- **Portal settings**: homepage `ec_standard_home`, KB home `esc_knowledge_home`, login page `login`, main menu Employee Center Menu
- **Widget client script**: `RECORD_PRODUCER_SYS_ID` must match your Record Producer

## Testing

Testing was done manually, from the portal to the saved record. Nine test cases were run and all of them passed:

- Portal access at `/employee_request`
- Display of the Commercial and Raise an Issue widgets
- Widget movement on hover
- Raise Ticket opening the Record Producer
- Record Producer variables
- Record creation with an auto-number
- Number field read-only
- Short description mandatory when State is New
- Category → Subcategory dependency on the table form

Details are in the User Acceptance Report and the Capstone Project Documentation.

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Raise Ticket does not open the form | Check that `RECORD_PRODUCER_SYS_ID` in the Link redirect client script matches the Record Producer's sys_id |
| Subcategory is not filtered on the table form | Check that Subcategory's dependent field is Category and each choice has the matching Category value |
| Portal form shows every subcategory | Expected: the Category and Subcategory variables on the Record Producer are not linked (see Future Enhancements) |
| Short description is not mandatory | Check the UI Policy condition (`State is New`) and that the action has Mandatory = True |
| Number field can be edited | Check that the read-only UI Policy action is set on the Number field |
| Widgets do not appear | Check the Designer: the 6 \| 6 container and both widgets must be placed on the page |
| Widget background image is missing | The image is loaded from an external URL; check that the URL is reachable |

## Support & Maintenance

- **Documentation**: see the `6. Project Documentation` folder
- **Demonstration**: see the `7. Project Demonstration` folder

## Known Limitations

- The portal form lists all subcategories because its variables are not linked
- Priority and Assignment group are not asked for on the portal form
- The Record Producer sys_id is stored in the widget's client script
- Response times and heavy-use behaviour were not measured

## Future Enhancements

- Link the Category and Subcategory variables on the portal form (for example with select boxes and a catalog client script)
- Add Priority and Assignment group as Record Producer variables
- Define roles and ACLs for the custom table

## License

This project was developed as a capstone project for learning purposes.

## Authors & Contributors

**Team ID**: 23KD1A0571

**Project Lead**: Bhargav

---

**Last Updated**: October 2026
**Repository**: [https://github.com/Bhargav113131/Employee-Raise-Issue-Record-Producer-Service-Portal-Integration-Guide.git]
