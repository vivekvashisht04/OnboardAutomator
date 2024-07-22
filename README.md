# Onboard Automator

## Overview
The Onboard Automator project aims to streamline and automate the process of onboarding new employees into Azure Active Directory (Azure AD) and assigning necessary Azure resources. This project uses Azure Logic Apps, Azure AD, Azure Resource Manager, and Azure Email Service.

## Azure Services Used
- Azure AD
- Azure Logic Apps
- Azure Email Service (part of Logic Apps connector)
- Azure Resource Manager

## Steps

### 1. Microsoft Entra ID (Azure AD) Setup
- Set up a new Azure AD instance using the Azure portal.

### 2. Logic App Workflow Design
- **Trigger:** Designed a Logic App workflow triggered by "When a new email arrives (V3)" to a specific mailbox indicating a new employee hire.
  - **Configuration:**
    - **Importance:** Any
    - **Only with Attachments:** No
    - **Include Attachments:** No
    - **Subject Filter:** "New Employee Onboarding"

### 3. Variables for User Credentials
- **Cleaned Email Body:**
  - **Variable Name:** CleanedEmailBody
  - **Type:** String
  - **Details:** Uses "replace" functions to strip out HTML tags and normalize line breaks and "trim" to remove any leading or trailing whitespace of the email body.
- **User Credentials (Name):**
  - **Variable Name:** ExtractedName
  - **Type:** String
  - **Details:** Uses "split" function to extract the Name based on delimiters (Name:, Department:) and "trim" to remove whitespace.
- **User Credentials (Department):**
  - **Variable Name:** ExtractedDepartment
  - **Type:** String
  - **Details:** Uses "split" function to extract the Department based on delimiters (Department:, Position:) and "trim" to remove whitespace.
- **User Credentials (Job Title):**
  - **Variable Name:** ExtractedJobTitle
  - **Type:** String
  - **Details:** Uses "split" function to extract the Position (Job Title) based on delimiters (Position:, Start Date:) and "trim" to remove whitespace.
- **User Account Password:**
  - **Variable Name:** Useraccountpassword
  - **Type:** String
  - **Details:** Stores the password for the user to be created.

### 4. Microsoft Entra ID (Azure AD) User Creation
- **Action:** Automatically create a new user in Azure AD using the details extracted from the email.
  - **Details:** Utilizes the variables initialized for Name, Department, Job Title, and User Account Password.

### 5. GroupID Variable
- **Action:** Create and configure a variable to store Group ID.
  - **Details:** Manually created five groups (IT, Finance, Production, Sales, Employees) in Azure AD.
  - **Variable Name:** GroupID
  - **Type:** String
  - **Value:** Left empty initially to be set based on conditional logic.

### 6. Conditional Logic for Group Assignment
- **Top-Level Condition:** Check if the Department is IT.
  - **True:** Set GroupID to the Group ID for IT.
  - **False:** Check if the Department is Finance.
- **Nested Conditions:** Continue nested conditions to check for Finance, Production, and Sales departments.
  - **True:** Set GroupID to the respective Group ID.
  - **False:** Default to setting GroupID for Employees.

### 7. Group Assignment
- **Action:** Assign the newly created user to the appropriate group based on the department.
  - **Details:** Use the Microsoft Entra ID connector to add the user to the group using the GroupID variable.

### 8. Variables for Resource Provisioning (Ubuntu VM) Attributes
- **Admin Username (VM):**
  - **Variable Name:** adminUsername
  - **Type:** String
  - **Details:** Stores the new employee’s name in lowercase.
- **Admin Password (VM):**
  - **Variable Name:** adminPassword
  - **Type:** String
  - **Details:** Stores the password to be used for the VM.

### 9. Resource Provisioning
- **Action:** Provision a new Ubuntu VM using Azure Resource Manager.
  - **Template Content:** Detailed ARM template provided to create and configure the VM.
  - **Configuration:** Selected subscription, resource group, deployment name, and used parameters for VM configuration (adminUsername, adminPassword, vmName, location).

### 10. Welcome Email
- **Action:** Send a welcome email to the new hire with access details.
  - **Configuration:**
    - **To:** @{body('Create_user')?['userPrincipalName']}
    - **Subject:** Welcome to the Team, @{body('Create_user')?['givenName']}
    - **Body:**
      ```
      Dear @{body('Create_user')?['displayName']},

      Welcome to our team! We are excited to have you on board.

      Here are your access details: -

      Username: @{body('Create_user')?['userPrincipalName']}
      Temporary Password: @{variables('Useraccountpassword')}

      Please make sure to update your password upon first login. If you have any questions or need further assistance, feel free to reach out to the IT department.

      Best regards,
      XYZ Organization
      ```
    - **Importance:** High

### 11. Monitoring and Review
- **Action:** Monitor and review the onboarding process through Logic Apps run history and Azure AD logs.
  - **Details:** Sent a test email and verified the successful execution of the workflow and receipt of the welcome email.

## Testing the Project
- **Test Email:**
  - **Subject:** New Employee Onboarding
  - **Body:**
    ```
    Hi Team,
    Please onboard the following new employee:

    Name: John Doe
    Department: IT
    Position: Software Engineer
    Start Date: 7 November 2024

    Best,
    HR Team
    ```
- **Verification:** Checked the Logic Apps run history for successful execution and received the welcome email with the expected details.
    ```
    Dear John Doe,

    Welcome to our team! We are excited to have you on board.

    Here are your access details: -

    Username: john.doe@vivekvash1507gmail.onmicrosoft.com
    Temporary Password: abc

    Please make sure to update your password upon first login. If you have any questions or need further assistance, feel free to reach out to the IT department.

    Best regards ,
    XYZ Organization

    ```

## Conclusion
The project successfully automated the onboarding process, showcasing skills in Azure Logic Apps, Azure AD, and Azure Resource Manager.

## Skills Demonstrated
- Automation using Azure Logic Apps
- User management in Azure AD
- Resource provisioning with Azure Resource Manager
- Email notifications with Azure Logic Apps

## Repository Contents
- Logic App workflow screenshots
- ARM template for VM provisioning

## Contact
For any questions or further information, feel free to reach out to me on LinkedIn: https://www.linkedin.com/in/vivek-vashisht04/

