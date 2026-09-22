# I-HUB

## Overview

**I-HUB** is a small online Android helper application designed for supermarket employees.

The purpose of I-HUB is to make communication between departments easier when employees need products for customer orders.

I-HUB does **not** replace or access the supermarket's existing proprietary software or database.

The application will have its own product database and its own request system.

---

# Main Idea

The main departments are:

1. **ΕΞΥΠΗΡΕΤΗΣΗ**
2. **ΑΠΟΘΗΚΗ**
3. **ΜΑΝΑΒΙΚΟ**
4. **ΨΥΓΕΙΟ**

The **ΕΞΥΠΗΡΕΤΗΣΗ** department creates requests.

The other departments receive and execute requests assigned specifically to them.

For example:

> Maria works in ΕΞΥΠΗΡΕΤΗΣΗ.

She needs:

* 2 × Καρπούζι Εγχώριο → ΜΑΝΑΒΙΚΟ
* 1 × product from the warehouse → ΑΠΟΘΗΚΗ

These are two separate requests.

The ΜΑΝΑΒΙΚΟ employees should only see the request assigned to ΜΑΝΑΒΙΚΟ.

The ΑΠΟΘΗΚΗ employees should only see the request assigned to ΑΠΟΘΗΚΗ.

---

# Technology

## Android

* Android Studio
* Java
* Android Views
* XML layouts
* AndroidX / AppCompat

## Backend

* Supabase
* PostgreSQL
* Supabase Authentication
* Supabase Storage

## Product Data

Product data can initially be imported from a CSV file.

Example:

| Κωδικός | Περιγραφή        | URL         |
| ------- | ---------------- | ----------- |
| 1273252 | Καρπούζι Εγχώριο | product URL |

---

# Distribution

I-HUB is intended for **private distribution**.

It will not initially be published on Google Play Store.

The application will eventually have its own update mechanism.

Possible architecture:

```text
I-HUB APK
   |
   v
Supabase Storage
   |
   v
New APK version
```

The application can check whether a newer version exists and notify the user.

Android will still require the appropriate user permission/confirmation when installing an APK from outside Google Play.

---

# Authentication

Users register with:

* Username
* Password
* Confirm password
* Email
* Registration code

There is **no department selection during registration**.

Passwords will be handled by **Supabase Authentication** and will not be stored as plain text by I-HUB.

The email will be used for account recovery.

Users will eventually be able to:

* Change password
* Recover their account
* Delete their own account
* Log out

---

# Registration Code

The registration code prevents random people who obtain the APK from creating accounts.

Initial version:

```text
One valid registration code
```

Future possibilities:

* One-time registration codes
* Expiration dates
* Disabled codes
* Multiple codes
* Admin-controlled codes

---

# Department Selection

A user's department is selected **after login**.

This is intentional because an employee may work in different departments on different days or shifts.

Example:

```text
Login
  ↓
Department Selection
  ↓
ΕΞΥΠΗΡΕΤΗΣΗ
ΑΠΟΘΗΚΗ
ΜΑΝΑΒΙΚΟ
ΨΥΓΕΙΟ
```

The selected department determines what the employee can do during that session.

---

# Department Permissions

## ΕΞΥΠΗΡΕΤΗΣΗ

Can:

* Search products
* Create requests
* Select the destination department
* View requests it created
* See the employee who claimed/executed a request
* Complete/delete its own requests where permitted

Cannot:

* Create requests as ΑΠΟΘΗΚΗ
* Create requests as ΜΑΝΑΒΙΚΟ
* Create requests as ΨΥΓΕΙΟ

---

## ΑΠΟΘΗΚΗ

Can:

* View requests assigned to ΑΠΟΘΗΚΗ
* Claim a request
* Execute the request
* Complete the request

Cannot:

* Create new requests

---

## ΜΑΝΑΒΙΚΟ

Can:

* View requests assigned to ΜΑΝΑΒΙΚΟ
* Claim a request
* Execute the request
* Complete the request

Cannot:

* Create new requests

---

## ΨΥΓΕΙΟ

Can:

* View requests assigned to ΨΥΓΕΙΟ
* Claim a request
* Execute the request
* Complete the request

Cannot:

* Create new requests

---

# Request System

Each request belongs to **one destination department**.

Example:

```text
Αίτημα #123

Από: Maria
Προς: ΜΑΝΑΒΙΚΟ

Καρπούζι Εγχώριο
Κωδικός: 1273252
Ποσότητα: 2

Κατάσταση: ΝΕΟ
```

An employee in ΜΑΝΑΒΙΚΟ can claim it.

After claiming:

```text
Από: Maria
Προς: ΜΑΝΑΒΙΚΟ

Αναλήφθηκε από: Kostas

[ΟΛΟΚΛΗΡΩΣΗ]
```

After completion:

```text
Από: Maria
Προς: ΜΑΝΑΒΙΚΟ

Εκτελέστηκε από: Kostas

Κατάσταση: ΟΛΟΚΛΗΡΩΘΗΚΕ
```

The server must ensure that only **one employee can claim a request**.

---

# Database Concept

The initial database structure will contain concepts such as:

```text
USERS
PRODUCTS
REQUESTS
REQUEST_ITEMS
```

## USERS

Stores application user/profile information.

Authentication credentials are handled by Supabase Auth.

---

## PRODUCTS

Example:

```text
product_id
code
description
url
```

Example record:

```text
code: 1273252
description: Καρπούζι Εγχώριο
url: ...
```

---

## REQUESTS

A request contains information such as:

```text
request_id
creator
destination_department
executor
status
created_at
```

---

## REQUEST_ITEMS

Contains the products belonging to a request.

Example:

```text
request_id
product_id
quantity
```

This allows one request to contain multiple products.

---

# Planned Screens

## 1. Login

Current design:

```text
          I-HUB

     Όνομα χρήστη

        Κωδικός 👁

      ☑ Να με θυμάσαι

        [ ΣΥΝΔΕΣΗ ]

  Ξέχασα τον κωδικό μου

          εγγραφή
```

Colors currently chosen:

* Dark brown background
* Orange I-HUB title
* Orange main button
* White/light text
* Black text inside input areas

---

# 2. Registration

Current design:

```text
          I-HUB

     Όνομα χρήστη

        Κωδικός 👁

 Επιβεβαίωση κωδικού 👁

          Email

    Κωδικός εγγραφής

        [ ΕΓΓΡΑΦΗ ]
```

There is no department field.

---

# 3. Department Selection

Planned:

```text
        ΕΠΙΛΕΞΕ ΤΜΗΜΑ

      [ ΕΞΥΠΗΡΕΤΗΣΗ ]

        [ ΑΠΟΘΗΚΗ ]

        [ ΜΑΝΑΒΙΚΟ ]

         [ ΨΥΓΕΙΟ ]
```

---

# 4. Main Application

Planned main functionality:

```text
Product Search
Requests
Account / Settings
```

The exact visual design will be decided before implementing the functionality.

---

# 5. Product Search

Employees in ΕΞΥΠΗΡΕΤΗΣΗ will be able to search products.

Search can use:

* Κωδικός
* Περιγραφή
* URL

Example:

```text
Αναζήτηση: καρπούζι

--------------------------------
Καρπούζι Εγχώριο

Κωδικός: 1273252
--------------------------------
```

---

# 6. Request Creation

Only ΕΞΥΠΗΡΕΤΗΣΗ creates requests.

Example workflow:

```text
Search product
      ↓
Select product
      ↓
Select quantity
      ↓
Select destination department
      ↓
Create request
```

Example:

```text
Καρπούζι Εγχώριο

Κωδικός: 1273252

Ποσότητα: 2

Προς:
[ ΜΑΝΑΒΙΚΟ ]

[ ΑΠΟΣΤΟΛΗ ]
```

---

# 7. Request Processing

Destination department sees its incoming requests.

Example:

```text
ΝΕΑ ΑΙΤΗΜΑΤΑ

#123
Από: Maria

Καρπούζι Εγχώριο
Ποσότητα: 2

[ΑΝΑΛΗΨΗ]
```

After an employee claims it:

```text
Αναλήφθηκε από: Kostas

[ΟΛΟΚΛΗΡΩΣΗ]
```

---

# Account

Users will eventually have an account/settings section.

Planned functions:

* Change password
* Account recovery
* Logout
* Delete own account

---

# Admin

The user's own account will be an **admin account**.

Admin functionality will be added later.

Possible admin functions:

* View employees
* Disable employees
* Delete employee accounts
* Manage registration codes
* Enable/disable registration codes

The backend/Supabase remains the authoritative security layer.

The I-HUB admin screen will eventually provide convenient access to everyday administrative operations.

Supabase's dashboard remains available for backend administration and emergency/manual operations.

---

# Security Principles

Important security rules:

* Passwords are handled by Supabase Auth.
* Registration requires a valid registration code.
* Users cannot simply choose another department's permissions.
* The selected department determines available operations.
* Request destination is stored on the backend.
* The backend controls who can see and modify requests.
* Only one employee can claim a request.
* Client-side restrictions alone are not considered sufficient security.

---

# Development Order

The planned development order is:

```text
1. Android project
        ↓
2. Supabase project
        ↓
3. Products table
        ↓
4. CSV product import
        ↓
5. Android → Supabase connection
        ↓
6. Product search
        ↓
7. Add product
        ↓
8. Login / authentication
        ↓
9. Department selection
        ↓
10. Request system
        ↓
11. Claim / completion / deletion
        ↓
12. Barcode scanning
        ↓
13. Update mechanism
        ↓
14. Final testing / polish
```

The exact order can change as development progresses.

---

# Current Android Project

Project name:

```text
I-HUB
```

Package:

```text
com.example.i_hub
```

Language:

```text
Java
```

Template:

```text
Empty Views Activity
```

Minimum SDK:

```text
API 24
```

---

# Current Java Structure

Currently we have:

```text
MainActivity
RegisterActivity
```

## MainActivity

Responsible for the login screen and login-screen navigation.

Current navigation:

```text
MainActivity
     |
     | click "εγγραφή"
     v
RegisterActivity
```

---

# Android XML Structure

Current layouts:

```text
activity_main.xml
activity_register.xml
```

## activity_main.xml

Contains the login interface.

## activity_register.xml

Contains the registration interface.

---

# Important Android Concepts Learned

## Activity

An Activity represents an Android screen.

Example:

```java
public class RegisterActivity extends AppCompatActivity
```

---

## AppCompatActivity

`AppCompatActivity` is a ready-made Android base class that provides the functionality needed for a typical Android screen.

Conceptually:

```text
AppCompatActivity
        ↑
RegisterActivity
```

This is normal Java inheritance.

---

## Bundle

`Bundle` is used by Android to provide saved/restored Activity state.

Example:

```java
protected void onCreate(Bundle savedInstanceState)
```

On a normal first launch, `savedInstanceState` can be `null`.

---

## XML Layout

XML describes the appearance of a screen.

For example:

```text
activity_register.xml
        ↓
visual appearance
```

The Activity controls that layout.

```text
RegisterActivity.java
        ↓
setContentView(...)
        ↓
activity_register.xml
```

---

# Current Development Status

## Completed

* [x] Create Android Studio project
* [x] Choose Java
* [x] Set package name
* [x] Create login screen
* [x] Choose basic color scheme
* [x] Add username field
* [x] Add password field
* [x] Add password visibility toggle
* [x] Add remember-me checkbox
* [x] Add login button
* [x] Add forgot-password text
* [x] Add registration text
* [x] Create registration XML
* [x] Add username
* [x] Add password
* [x] Add confirm password
* [x] Add email
* [x] Add registration code
* [x] Add registration button
* [x] Connect registration text to `RegisterActivity`
* [x] Learn Activity inheritance
* [x] Learn `AppCompatActivity`
* [x] Learn `Bundle`

## Currently Working On

```text
RegisterActivity
       ↓
Load activity_register.xml
       ↓
Test Login → Registration navigation
```

---

# Development Philosophy

I-HUB will be developed **one piece at a time**.

The goal is not simply to copy large blocks of code.

The developer should understand:

* What each class does
* Why each method exists
* How Activities communicate
* How XML connects to Java
* How the database works
* Why security rules are necessary

Large code dumps should be avoided unless specifically requested.

---

# Future Features

Potential future features include:

* Barcode scanning
* Product additions
* Product editing
* Request history
* Notifications
* Admin controls
* Registration-code management
* Employee management
* APK update notifications
* Improved request filtering
* Additional departments

These features should be considered **future plans**, not current requirements.

---

# Current Next Step

The immediate next step is:

```text
RegisterActivity.java
        ↓
setContentView(R.layout.activity_register)
        ↓
Run application
        ↓
Login screen
        ↓
Tap "εγγραφή"
        ↓
Registration screen appears
```

After this works, development continues one small step at a time.
