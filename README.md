# I-HUB

**I-HUB** is a private Android application for supermarket employees. It
provides a shared product lookup tool and helps the Customer Service
department send product requests to the department responsible for
fulfilling them.

I-HUB has its **own online database**. It does not replace, connect to,
or require access to the supermarket's proprietary software or database.

## Core Features

### 1. Shared Product Database and Lookup

Product lookup is a core feature of I-HUB and must be available in
**every department**.

-   All departments use the same central product database.
-   Employees can search for a product using its product code.
-   The result displays the product description and a link to its online
    product page.
-   Product lookup remains easily accessible after an employee selects a
    department.
-   Product data can initially be imported from a CSV file.

Example CSV structure:

  Κωδικός   Περιγραφή          URL
  --------- ------------------ -----------------------------
  1273252   Καρπούζι Εγχώριο   https://example.com/product

The example URL is illustrative; actual product URLs will be imported
with the product data.

### 2. Department-Based Requests

I-HUB supports four departments:

-   **ΕΞΥΠΗΡΕΤΗΣΗ**
-   **ΑΠΟΘΗΚΗ**
-   **ΜΑΝΑΒΙΚΟ**
-   **ΨΥΓΕΙΟ**

Customer Service creates requests and selects the destination
department. The destination department's employees can view and process
those requests.

Each request is assigned to **one destination department**. If products
are needed from different departments, they are created as separate
requests.

Example:

-   2 × Καρπούζι Εγχώριο → ΜΑΝΑΒΙΚΟ
-   1 × product from the warehouse → ΑΠΟΘΗΚΗ

Employees in each destination department should only see requests
assigned to that department, subject to any authorized administrative
access.

### 3. Employee Accounts and Department Selection

An employee has one account and one consistent username across I-HUB.
The employee can choose the department they are currently working in
after logging in and can switch departments when their work changes.

Department selection does **not** create a new account or change the
employee's identity.

The system must retain accountability across department changes: actions
should be associated with the employee, the department in which the
action occurred, and the time of the action. These rules must be
enforced by the backend/database, not only by the app interface.

## Registration and Account Approval

### Registration

The registration screen collects:

-   10-digit registration code
-   Username
-   Email
-   Password
-   Password confirmation

There is no department selection during registration.

### Registration Codes

An administrator manages a set of permanent 10-digit registration-code
records.

-   Each available code can authorize the creation of one account at a
    time.
-   A code can be assigned to only one active employee account at a
    time.
-   The code record itself is permanent: it must not be edited or
    deleted.
-   When an employee leaves, an administrator can deactivate/delete that
    employee's account and release the code for reuse.
-   Reusing a code must create a **new internal account identity**. It
    must not merge the new employee with the previous employee's
    activity history.

The registration code authorizes registration. It is not the employee's
identity and does not determine their department.

### Approval

New registrations remain **pending** until approved by an administrator.
Pending accounts cannot log in or use the application.

After approval, employees sign in with their **username and password**.

Passwords must be handled securely by Supabase Authentication. I-HUB
must never store plaintext passwords.

Because Supabase Authentication normally uses email-based sign-in,
username-based login must be implemented through a secure backend flow.
It must not expose account lookup information or store passwords in the
application database.

## Request Workflow

A request should record, at minimum:

-   Request creator
-   Destination department
-   Products and quantities
-   Current status
-   Employee who claimed the request, if any
-   Employee who completed the request, if any
-   Creation, claim, and completion timestamps

Example:

``` text
Αίτημα #123

Από: Maria
Προς: ΜΑΝΑΒΙΚΟ

Καρπούζι Εγχώριο
Κωδικός: 1273252
Ποσότητα: 2

Κατάσταση: ΝΕΟ
```

When an eligible employee claims a request, the backend must ensure that
**only one employee can claim it**. This must be an atomic,
server-enforced operation to prevent two employees from claiming the
same request simultaneously.

The system should preserve request history and record who performed each
important action.

## Department Capabilities

### ΕΞΥΠΗΡΕΤΗΣΗ

-   Search the shared product database.
-   Create requests.
-   Select a destination department for each request.
-   View requests it created and their statuses.
-   See who claimed and completed each request, where applicable.

### ΑΠΟΘΗΚΗ

-   Search the shared product database.
-   View requests assigned to ΑΠΟΘΗΚΗ.
-   Claim and process eligible requests.
-   Complete requests.

### ΜΑΝΑΒΙΚΟ

-   Search the shared product database.
-   View requests assigned to ΜΑΝΑΒΙΚΟ.
-   Claim and process eligible requests.
-   Complete requests.

### ΨΥΓΕΙΟ

-   Search the shared product database.
-   View requests assigned to ΨΥΓΕΙΟ.
-   Claim and process eligible requests.
-   Complete requests.

Department permissions must be enforced by the backend. Hiding a button
or screen in the Android app is not sufficient security.

## Technology

### Android App

-   Android Studio
-   Java
-   Android Views
-   XML layouts
-   AndroidX / AppCompat

### Backend and Data

-   Supabase
-   PostgreSQL
-   Supabase Authentication
-   Supabase Storage, if needed

## Initial Database Concepts

The final schema will be designed before implementation. Expected
concepts include:

-   **User profiles** --- internal employee identity, username, email,
    approval and account status.
-   **Registration codes** --- permanent code records and their current
    assignment/availability.
-   **Products** --- product code, description and product URL.
-   **Requests** --- creator, destination department, status and
    timestamps.
-   **Request items** --- products and quantities within a request.
-   **Audit/activity history** --- employee, department, action and
    timestamp.

Historical activity must remain associated with the original internal
employee identity, even if that employee's account is later deactivated
and their registration code is reused.

## Private Distribution and Updates

I-HUB is intended for private distribution and will not initially be
published on Google Play.

A future version may check for updates and notify employees when a newer
APK is available. Android may still require user permission or
confirmation to install an APK distributed outside Google Play.

## Current Development Status

The project began as an Android UI skeleton, including login and
registration screens. The requirements above describe the intended
application; they should not be interpreted as features that are already
implemented.

Development should proceed in stages:

1.  Finalize the requirements and permissions.
2.  Design the database and backend security rules.
3.  Implement authentication, registration-code handling and
    administrator approval.
4.  Implement the shared product database and product lookup.
5.  Implement department selection and request workflows.
6.  Add audit history, testing and private distribution/update support.
