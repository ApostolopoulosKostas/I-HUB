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

      ☑ Ν
```
