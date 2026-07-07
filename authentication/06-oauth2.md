# Chapter 6 - OAuth 2.0: Delegated Authorization

> "OAuth 2.0 is not about proving who you are. It is about allowing someone else to access your resources without sharing your password."

---

# Learning Objectives

By the end of this chapter, you will be able to:

- Understand the problem OAuth 2.0 solves.
- Explain why OAuth2 was created.
- Differentiate Authentication from Authorization.
- Identify all OAuth2 components.
- Understand the Authorization Code Flow.
- Learn Access Tokens and Refresh Tokens.
- Understand where JWT fits into OAuth2.
- Answer common OAuth2 interview questions.

---

# A Common Misconception

One of the most common interview mistakes is saying:

> "OAuth2 is an authentication protocol."

This is **incorrect**.

OAuth2 is an **authorization framework**.

Authentication answers:

> Who are you?

Authorization answers:

> What are you allowed to access?

OAuth2 focuses on the second question.

---

# Why OAuth2 Was Created

Imagine you build a Photo Printing application.

Your users want to print photos directly from their Google Photos account.

Without OAuth2, the application would ask:

```
Google Username

Google Password
```

This is extremely dangerous.

Why?

Because:

- Your application now knows the user's Google password.
- The user has to trust your application completely.
- If your database is compromised, the attacker gains Google credentials.

Clearly, this is not acceptable.

There had to be a better way.

---

# The Better Approach

Instead of asking for the user's password, the application asks Google:

> "Can you ask the user whether I may access their photos?"

The user authenticates directly with Google.

Your application never sees the password.

Instead, Google returns an **Access Token**.

This token grants limited access to the requested resources.

This idea is OAuth2.

---

# Everyday Examples

You have probably used OAuth2 many times without realizing it.

Examples include:

- Continue with Google
- Login with GitHub
- Continue with Facebook
- Sign in with Microsoft
- Connect Slack to GitHub
- Allow Zoom to access Google Calendar

In all these cases:

- You never give your Google password to the third-party application.
- Google authenticates you.
- Google grants limited permission using a token.

---

# OAuth2 Components

OAuth2 defines four major participants.

```
+------------------+
| Resource Owner   |
+------------------+

        |

        v

+------------------+
| Client           |
+------------------+

        |

        v

+-----------------------+
| Authorization Server  |
+-----------------------+

        |

        v

+------------------+
| Resource Server  |
+------------------+
```

Let's understand each one.

---

## 1. Resource Owner

Usually the user.

The Resource Owner owns the data.

Example:

Alice owns her Google Drive files.

---

## 2. Client

The application requesting access.

Examples:

- Canva
- Notion
- Slack
- Zoom

These applications want limited access to user resources.

---

## 3. Authorization Server

Responsible for:

- Authenticating the user
- Asking for consent
- Issuing tokens

Examples:

- Google Authorization Server
- GitHub Authorization Server
- Microsoft Identity Platform

---

## 4. Resource Server

Stores the actual resources.

Examples:

- Google Drive
- Gmail
- Google Photos

The Resource Server trusts tokens issued by the Authorization Server.

---

# OAuth2 Flow (Big Picture)

```
User

↓

Client

↓

Authorization Server

↓

Access Token

↓

Client

↓

Resource Server

↓

Protected Resource
```

The Client never receives the user's password.

Only the Access Token.

---

# Authorization Code Flow

This is the most commonly used OAuth2 flow for web applications.

Let's walk through it step by step.

---

## Step 1

The user clicks:

```
Continue with Google
```

---

## Step 2

The Client redirects the browser to Google.

```
https://accounts.google.com/...
```

---

## Step 3

Google asks the user to log in.

If already logged in,

Google skips this step.

---

## Step 4

Google asks for consent.

```
Can Canva access:

✓ Email

✓ Profile

✓ Photos
```

The user decides.

---

## Step 5

If approved,

Google redirects back with an Authorization Code.

```
Authorization Code

↓

abc123xyz
```

Notice:

This is **not** the Access Token.

---

## Step 6

The Client exchanges the Authorization Code for an Access Token.

```
Authorization Code

↓

Authorization Server

↓

Access Token
```

This communication happens server-to-server.

The browser never sees the client secret.

---

## Step 7

The Client calls the Resource Server.

```
Authorization:

Bearer eyJhbGc...
```

---

## Step 8

The Resource Server verifies the token.

If valid,

the requested resource is returned.

---

# Why Use an Authorization Code?

A common interview question is:

> Why not return the Access Token directly?

Because the browser is less trusted.

The Authorization Code is short-lived.

Only the backend can exchange it for an Access Token using the Client Secret.

This significantly improves security.

---

# Access Token

An Access Token represents permission.

Example:

```
Access Token

↓

Read Photos

↓

Expires in 1 hour
```

Important points:

- Short-lived
- Sent with every request
- Grants limited permissions

---

# Refresh Token

Imagine the Access Token expires.

Should the user log in again?

That would be frustrating.

Instead,

the Client sends a Refresh Token.

```
Refresh Token

↓

Authorization Server

↓

New Access Token
```

The user continues seamlessly.

---

# Access Token vs Refresh Token

| Access Token | Refresh Token |
|--------------|---------------|
| Used to access APIs | Used to obtain a new Access Token |
| Short-lived | Long-lived |
| Sent frequently | Sent rarely |
| Higher risk if leaked | Must be stored securely |

---

# Scopes

OAuth2 allows applications to request only the permissions they need.

Examples:

```
email

profile

photos.read

calendar.read
```

This follows the **Principle of Least Privilege**.

Never request unnecessary permissions.

---

# Where Does JWT Fit?

OAuth2 defines **how tokens are obtained**.

JWT defines **one possible format of those tokens**.

Think of it this way:

```
OAuth2

↓

Rules for obtaining a token

↓

JWT

↓

Format of the token
```

OAuth2 does not require JWT.

An Access Token could simply be a random string.

Many providers use JWT because it is self-contained.

---

# OAuth2 in Spring Boot

In a Spring Boot application, OAuth2 typically involves:

- Authorization Server
- Resource Server
- Client Application

Spring Security provides built-in support for all three roles.

We'll explore this in the next chapter.

---

# Common Interview Questions

## Is OAuth2 Authentication?

No.

OAuth2 is an Authorization framework.

Authentication may happen during the process,

but OAuth2 itself focuses on delegated authorization.

---

## Can OAuth2 work without JWT?

Yes.

OAuth2 defines the protocol.

JWT is simply one possible token format.

---

## Why use Refresh Tokens?

To obtain a new Access Token without asking the user to log in again.

---

## What is the purpose of Scopes?

Scopes define what actions the Client is allowed to perform.

They limit access to only what is necessary.

---

# Common Mistakes

❌ Saying OAuth2 is authentication.

❌ Assuming every OAuth2 token is a JWT.

❌ Requesting excessive scopes.

❌ Storing Refresh Tokens insecurely.

❌ Exposing Client Secrets in frontend applications.

---

# Key Takeaways

- OAuth2 solves delegated authorization.
- Users never share passwords with third-party applications.
- OAuth2 defines four main participants: Resource Owner, Client, Authorization Server, and Resource Server.
- The Authorization Code Flow is the most secure flow for server-side applications.
- Access Tokens are short-lived.
- Refresh Tokens obtain new Access Tokens.
- JWT is often used as the format of an Access Token but is not required by OAuth2.

---

# What's Next?

So far we've understood the concepts:

- Authentication
- Sessions
- Password Security
- JWT
- OAuth2

Now it's time to see how Java developers implement these ideas in real applications.

In the next chapter, we'll explore **Spring Security**—the framework that brings all these concepts together in a production-ready authentication and authorization system.
