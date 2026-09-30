# Auto Ticket Classification using ServiceNow Flow Designer

## Project Overview

This project automates IT helpdesk ticket classification for a school environment using **ServiceNow Flow Designer**.

The helpdesk receives requests such as Wi-Fi problems, projector failures, password/login issues, and slow computer problems. Instead of requiring IT staff to manually classify every ticket, the Flow Designer automation analyzes the ticket's **Short Description** and automatically assigns the appropriate **Category** and **Subcategory**.

The solution is designed as a **no-code, maintainable, and scalable ServiceNow workflow**.

## Objectives

- Automatically classify incidents when they are created.
- Reduce manual classification effort for IT staff.
- Improve ticket routing efficiency.
- Maintain standardized ticket data.
- Use dependent Category/Subcategory choices.
- Send an automated confirmation email to the caller.
- Provide a solution that is easy to maintain and extend.

## Technology

- **Platform:** ServiceNow
- **Automation:** Flow Designer
- **Configuration:** Custom Table, Choice Fields, Reference Fields, Dependent Choices
- **Notification:** Flow Designer Send Email action
- **Deployment:** ServiceNow Update Set
- **Approach:** No-code automation

## Ticket Classification Rules

| Keyword / Condition | Category | Subcategory |
|---|---|---|
| Wi-Fi / WiFi / Network | Network | Wi-Fi |
| Projector | Hardware | Projector |
| Password / Login / Forgot Password | Access | Forgot Password |
| Slow / Hanging / Slow Computer | Performance | Slow Computer |

## Data Model

The project creates an **Incident Workflow** custom table with the following fields:

| Field | Type |
|---|---|
| Number | Auto Number |
| Caller | Reference → sys_user |
| Category | Choice |
| Subcategory | Choice |
| Short Description | String |
| Description | String |
| State | Choice |
| Assigned Group | Reference → sys_user_group |
| Assigned To | Reference → sys_user |

### Category Choices

- Network
- Hardware
- Access
- Performance

### Subcategory Choices

- Wi-Fi
- Projector
- Forgot Password
- Slow Computer

### State Choices

- New
- In progress
- On hold
- Resolved
- Closed

## Category/Subcategory Dependency

The Subcategory field is configured as dependent on Category.

- **Network → Wi-Fi**
- **Hardware → Projector**
- **Access → Forgot Password**
- **Performance → Slow Computer**

This prevents unrelated subcategory values from being selected for a category.

## Flow Designer

### Flow Name

`Auto Classify School IT Tickets`

### Trigger

- Trigger: **Record Created**
- Table: **Incident Workflow**
- Condition: **Category is empty**

### Flow Logic

```text
WHEN a new Incident Workflow record is created
AND Category is empty

IF Short Description contains "Wi-Fi"
   OR Short Description contains "WiFi"
   OR Short Description contains "Network"
THEN
   Category = Network
   Subcategory = Wi-Fi

ELSE IF Short Description contains "Projector"
   OR Short Description contains "Hardware"
THEN
   Category = Hardware
   Subcategory = Projector

ELSE IF Short Description contains "Password"
   OR Short Description contains "Login"
   OR Short Description contains "Forgot password"
THEN
   Category = Access
   Subcategory = Forgot Password

ELSE IF Short Description contains "Slow"
   OR Short Description contains "Hanging"
   OR Short Description contains "Slow Computer"
THEN
   Category = Performance
   Subcategory = Slow Computer

AFTER classification
   Send email to Caller.Email
   Subject = "Your Request for the issue has been submitted."
```

## Email Notification

After ticket classification, the flow sends a confirmation email to the caller.

- **Recipient:** Caller → Email
- **Subject:** `Your Request for the issue has been submitted.`
- **Body:** Ticket confirmation message

## Testing

### Test Case 1 — Wi-Fi

**Input**

```text
Caller: Test User
Short Description: WiFi not working in library
```

**Expected Result**

```text
Category: Network
Subcategory: Wi-Fi
Email: Sent to caller
```

### Test Case 2 — Projector

**Input**

```text
Caller: Test User
Short Description: Projector not turning on
```

**Expected Result**

```text
Category: Hardware
Subcategory: Projector
Email: Sent to caller
```

## Validation Checklist

- [x] Mandatory fields captured correctly
- [x] Auto-number generated
- [x] Category stored correctly
- [x] Subcategory stored correctly
- [x] Reference fields resolve to user/group records
- [x] Category/Subcategory dependency configured
- [x] Flow triggers on record creation
- [x] Email notification configured
- [x] Update Set can be completed and exported

## Deployment

The project uses a ServiceNow **Local Update Set** named:

`Project Update Set`

Deployment process:

1. Complete development and testing.
2. Open **Local Update Sets**.
3. Open `Project Update Set`.
4. Change the state from **In progress** to **Complete**.
5. Save the Update Set.
6. Use **Export to XML** to download the Update Set.
7. The exported XML can be moved to another ServiceNow instance through the normal Update Set deployment process.

> The actual ServiceNow Update Set XML is instance-specific and is not included in this GitHub documentation unless exported from the user's ServiceNow instance.

## Repository Structure

```text
auto-ticket-classification/
├── README.md
├── PROJECT_DOCUMENTATION.md
└── flow_logic.txt
```

## Future Enhancements

The project documentation identifies possible future extensions such as:

- Automatic assignment to support groups
- SLA tracking
- More ticket classification rules
- Predictive intelligence
- Additional categories and subcategories
- More advanced automation

## Conclusion

The project demonstrates how ServiceNow Flow Designer can automate school IT helpdesk ticket classification without complex scripting or machine learning. The workflow improves consistency, reduces manual effort, supports structured data capture, and provides automated communication to the caller.
