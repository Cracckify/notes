# Chapter 2 - The Authentication Lifecycle

> "Every secure application begins by answering one question: Who is making this request?"

---

# Learning Objectives

By the end of this chapter, you will be able to:

- Understand the complete authentication lifecycle.
- Learn how clients and servers communicate during login.
- Understand credentials, identity verification, and session establishment.
- Identify the responsibilities of each layer in a typical backend application.
- Build the mental model required to understand Sessions, JWT, OAuth2, and Spring Security.

---

# Introduction

In the previous chapter, we learned that **Authentication** is the process of verifying a user's identity.

But that definition raises another question.

**How does an application actually verify identity?**

When you open Amazon, enter your email and password, and click **Login**, dozens of things happen behind the scenes in just a few milliseconds.

Understanding this lifecycle is essential because almost every authentication mechanism—Sessions, JWT, OAuth2, Spring Security—follows the same basic flow.

Only the implementation differs.

---

# The Login Journey

Imagine Alice wants to log in to an online shopping application.

She enters:

```
Email:
alice@example.com

Password:
MySecurePassword123
```

She clicks **Login**.

This single click starts an entire chain of events.

---

# Step 1 - Client Sends Credentials

The browser creates an HTTP request.

```http
POST /login

{
    "email": "alice@example.com",
    "password": "MySecurePassword123"
}
```

At this point, the browser has done its job.

It simply sends the credentials to the server.

The browser itself does not verify passwords.

That responsibility belongs entirely to the backend.

---

# Step 2 - Request Reaches the Server

The server receives the login request.

```
Browser

↓

Internet

↓

Web Server

↓

Application Server
```

The application now needs to determine whether this user is genuine.

The first step is locating the user.

---

# Step 3 - Find the User

The backend searches the database.

```sql
SELECT *
FROM users
WHERE email='alice@example.com';
```

Possible outcomes:

### User exists

Continue authentication.

### User does not exist

Authentication fails immediately.

The application returns an error such as:

```
Invalid username or password.
```

Notice something important.

Good applications never reveal whether:

- the email exists, or
- the password was incorrect.

Both situations produce the same error.

This prevents attackers from discovering valid usernames.

---

# Step 4 - Verify Credentials

Suppose the user exists.

The server now compares the submitted password with the stored password hash.

```
User Password

↓

Hash Function

↓

Compare

↓

Match?
```

If the hashes match,

the user has successfully proven their identity.

Otherwise,

authentication fails.

We'll study password hashing in detail in the next chapter.

For now, remember:

**Passwords are never compared as plain text.**

---

# Step 5 - Authentication Success

Once credentials are verified, the server now knows:

> This request genuinely belongs to Alice.

At this moment,

Authentication is complete.

Identity has been established.

However,

the request is not yet ready to access protected resources.

The application still needs a way to remember Alice for future requests.

That is where Sessions and Tokens come into the picture.

We'll study them in upcoming chapters.

---

# Authentication Flow

A simplified authentication flow looks like this:

```
User

↓

Enter Credentials

↓

HTTP Request

↓

Application

↓

Database Lookup

↓

Password Verification

↓

Authentication Successful

↓

Generate Authentication State

↓

Response
```

Notice that every login follows the same pattern.

Different authentication mechanisms simply differ in how they store the authentication state.

---

# Components Involved

Let's understand the responsibility of each component.

## Browser

Responsible for:

- Collecting user credentials
- Sending HTTP requests
- Storing cookies or tokens (later chapters)

The browser never validates passwords.

---

## Backend Application

Responsible for:

- Receiving credentials
- Finding the user
- Verifying passwords
- Generating authentication state
- Returning the response

Most authentication logic lives here.

---

## Database

Responsible for storing:

- User information
- Password hashes
- Roles
- Permissions

The database never authenticates users.

It simply stores information.

---


# Common Mistakes

## Storing Plain Passwords

Never store passwords directly.

Always store password hashes.

---

## Revealing Too Much Information

Avoid messages like:

```
Email does not exist.
```

or

```
Incorrect password.
```

These messages help attackers identify valid accounts.

Instead, always return:

```
Invalid username or password.
```

---

## Performing Business Logic Before Authentication

Never execute business operations before verifying identity.

Authentication should always happen first.

---

# Interview Insights

### Question

What is Authentication?

A strong answer:

> Authentication is the process of verifying the identity of a user by validating credentials such as passwords, OTPs, or biometrics. Once verified, the application establishes an authenticated state so future requests can identify the user without requiring repeated login.

---


### Question

Does Authentication decide what a user can access?

No.

Authentication only verifies identity.

Authorization determines permissions.

---

# Key Takeaways

- Authentication begins when a user submits credentials.
- The backend—not the browser—verifies identity.
- The database stores user information but does not authenticate users.
- Passwords should always be compared using secure hashes.
- Successful authentication establishes identity.
- Applications remember authenticated users using Sessions or Tokens.
- Authentication and Authorization are separate concerns.

---

# What's Next?

Authentication answers one question:

> **Who are you?**

But another challenge remains.

Users shouldn't have to log in for every request.

How can a server remember an authenticated user?

The answer begins with one of the oldest authentication mechanisms used on the web:

**Sessions and Cookies.**
