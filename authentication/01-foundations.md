# Chapter 1 - Foundations of Security

> "Security is not about preventing every attack. It is about ensuring that only the right people can access the right resources at the right time."

---

# Learning Objectives

By the end of this chapter, you will be able to:

- Understand why application security is necessary.
- Differentiate Authentication from Authorization.
- Learn the basic terminology used in security.
- Understand where Authentication and Authorization fit inside a web application.
- Build a mental model that will be used throughout the rest of this handbook.

---

# Why Does Security Exist?

Imagine you've built an e-commerce application similar to Amazon.

It allows users to:

- Browse products
- Add products to cart
- Place orders
- Cancel orders
- View order history

After weeks of development, you deploy your application to production.

Within minutes, users start using it.

Everything looks perfect.

Then someone discovers that they can directly call your API.

```
DELETE /orders/123
```

The API happily deletes the order.

No login.

No identity verification.

No permission check.

Nothing.

Your backend did exactly what it was asked to do.

The problem wasn't your business logic.

The problem was that your application never asked one simple question:

> **Who is making this request?**

That question is the foundation of application security.

---

# What is Security?

Application security is the practice of protecting an application from unauthorized access, misuse, modification, or destruction.

A secure application ensures that:

- Only legitimate users can access the application.
- Users can perform only the actions they are permitted to perform.
- Sensitive information remains protected.
- Attackers cannot easily exploit the system.

Security is not a single feature.

It is a collection of practices that work together to protect an application.

---

# Understanding Identity

Before an application can decide what a user can do, it must first know who the user is.

This is called **Identity**.

An identity uniquely represents a user inside a system.

Examples include:

- Username
- Email Address
- Employee ID
- Customer ID

Notice that identity itself does not grant any permissions.

It simply answers the question:

> Who are you?

---

# Authentication

Authentication is the process of verifying a user's identity.

It answers the question:

> **Are you really who you claim to be?**

Examples of authentication include:

- Username + Password
- OTP
- Fingerprint
- Face Recognition
- Passkeys

Authentication is about proving identity.

Nothing more.

---

# Authorization

Once the system knows who you are, it needs to decide what you are allowed to do.

This process is called Authorization.

It answers the question:

> **What are you allowed to access?**

Example:

Consider an online shopping application.

A customer can:

- Browse products
- Place orders
- View personal orders

An administrator can:

- Add products
- Delete products
- Update inventory
- Manage users

Both users are authenticated.

Their permissions are different.

That difference is Authorization.

---

# Authentication vs Authorization

| Authentication | Authorization |
|---------------|---------------|
| Verifies identity | Verifies permissions |
| Happens first | Happens after authentication |
| Answers "Who are you?" | Answers "What can you do?" |
| Login process | Permission checking |
| Example: Password | Example: ADMIN role |

One of the most common interview questions is:

> Can Authorization happen without Authentication?

In most applications,

**No.**

The system must first know **who** the user is before deciding **what** they can access.

---

# Where Do They Fit?

A typical request looks like this:

```
Client
    │
    ▼
Login Request
    │
    ▼
Authentication
    │
    ▼
Identity Verified
    │
    ▼
Authorization
    │
    ▼
Business Logic
    │
    ▼
Response
```

Every secured application follows this flow.

The implementation details may vary, but the overall process remains the same.

---

# A Real-World Analogy

Imagine visiting an airport.

### Step 1

At the entrance, security checks your passport and ticket.

They verify your identity.

This is Authentication.

### Step 2

Once inside, your boarding pass determines which gate you can enter.

Business Lounge?

International Departure?

Domestic Departure?

These decisions depend on your permissions.

This is Authorization.

Notice that nobody checks your gate before verifying your identity.

Authentication always comes first.

---

# Common Security Terminology

## User

A person using the application.

Example:

John logs into Amazon.

---

## Credentials

Information used to prove identity.

Examples:

- Password
- OTP
- Fingerprint

---

## Principal

The authenticated identity currently using the system.

Spring Security internally refers to the logged-in user as the **Principal**.

---

## Role

A collection of permissions.

Examples:

- ADMIN
- USER
- SELLER

Roles simplify authorization.

Instead of assigning hundreds of permissions individually, permissions are grouped into roles.

---

## Permission

A specific action that a user can perform.

Examples:

- CREATE_PRODUCT
- DELETE_PRODUCT
- UPDATE_ORDER

Permissions are more granular than roles.

---

# Security in a Typical Web Application

```
Browser

↓

REST API

↓

Authentication

↓

Authorization

↓

Service Layer

↓

Database
```

Security acts like a gatekeeper.

Every incoming request passes through it before reaching the application's business logic.

---

# Key Takeaways

- Security protects applications from unauthorized access.
- Identity answers "Who are you?"
- Authentication verifies identity.
- Authorization determines permissions.
- Authentication always happens before Authorization.
- Roles are collections of permissions.
- Every secured request passes through authentication and authorization before reaching business logic.

---

# What's Next?

Now that we understand why applications need security, the next question naturally arises:

> **How does an application actually verify a user's identity?**

In the next chapter, we'll follow an authentication request from the moment a user clicks the **Login** button until the server successfully identifies them.
