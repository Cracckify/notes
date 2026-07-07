# Chapter 3 - Sessions & Cookies

> "Authentication proves who you are. Sessions help the application remember who you are."

---

# Learning Objectives

By the end of this chapter, you will be able to:

- Understand why Sessions were introduced.
- Learn how Cookies and Sessions work together.
- Understand Session IDs.
- Follow the lifecycle of a Session.
- Identify the advantages and limitations of Session-based authentication.
- Explain Sessions confidently in interviews.

---

# The Problem

In the previous chapter, we learned how a user logs into an application.

Alice enters her email and password.

The server verifies her credentials.

Authentication succeeds.

Everything looks good.

But imagine what happens next.

Alice clicks **View Products**.

```
GET /products
```

How does the server know that this request is still coming from Alice?

HTTP itself doesn't remember anything.

Every request is completely independent.

This property of HTTP is called **Statelessness**.

---

# Understanding Stateless Protocols

HTTP is a stateless protocol.

This means every request is treated as a brand-new request.

Consider the following sequence.

```
POST /login

↓

GET /products

↓

GET /cart

↓

POST /checkout
```

From HTTP's perspective,

these are four completely unrelated requests.

The protocol does not remember previous interactions.

This creates a problem.

Without additional mechanisms, the server would ask the user to log in before every request.

That would be a terrible user experience.

---

# The Solution

The server needs a way to remember users after successful authentication.

This led to the invention of **Sessions**.

A Session represents an authenticated conversation between a client and a server.

Instead of asking for credentials repeatedly,

the server remembers the user.

---

# What is a Session?

A Session is server-side storage that contains information about a logged-in user.

Think of it as a temporary identity card stored on the server.

Example:

```
Session ID

↓

ABC123XYZ
```

Server stores:

```
ABC123XYZ

↓

User ID : 45

Username : Alice

Role : USER

Login Time : 10:15 AM
```

The browser never sees this information.

It only knows the Session ID.

---

# What is a Cookie?

Now another question appears.

How will the browser remember the Session ID?

This is where Cookies enter the picture.

A Cookie is a small piece of data stored inside the browser.

Example:

```
Cookie

SESSION_ID = ABC123XYZ
```

Every future request automatically includes this cookie.

```
GET /products

Cookie:
SESSION_ID=ABC123XYZ
```

The browser does this automatically.

The developer usually doesn't have to write any code for it.

---

# Complete Login Flow

Let's follow the entire lifecycle.

## Step 1

User logs in.

```
POST /login
```

↓

Server verifies credentials.

---

## Step 2

Server creates a Session.

```
Session

ID = ABC123XYZ

User = Alice
```

The Session is stored on the server.

---

## Step 3

Server sends the Session ID back to the browser.

```
Set-Cookie

SESSION_ID=ABC123XYZ
```

---

## Step 4

Browser stores the cookie.

```
Browser

↓

SESSION_ID=ABC123XYZ
```

---

## Step 5

Future requests automatically include the cookie.

```
GET /orders

Cookie:

SESSION_ID=ABC123XYZ
```

---

## Step 6

Server receives the Session ID.

Looks up the Session.

```
ABC123XYZ

↓

Alice

↓

Authenticated
```

The user does not need to log in again.

---

# Complete Architecture

```
+---------+
| Browser |
+---------+
     |
     | POST /login
     |
     v
+------------------+
| Application      |
+------------------+
     |
Verify Credentials
     |
Create Session
     |
Store Session
     |
+------------------+
| Session Store    |
+------------------+
     |
Session ID
     |
     v
Browser Cookie
```

For every future request:

```
Browser

↓

Cookie

↓

Server

↓

Find Session

↓

Identify User

↓

Process Request
```

---

# Where are Sessions Stored?

Sessions are stored on the server.

Examples include:

- Application Memory
- Redis
- Database
- Distributed Cache

The browser stores only the Session ID.

Never the complete Session.

---

# Session Timeout

Sessions do not last forever.

Applications configure an expiration time.

Example:

```
30 Minutes

No Activity

↓

Session Expired
```

The next request requires the user to log in again.

---

# Logout

When the user clicks Logout,

the server destroys the Session.

```
Delete Session

↓

Invalidate Cookie

↓

User Logged Out
```

Even if someone steals the old Session ID,

it becomes useless.

---

# Advantages of Sessions

- Simple to implement
- Server has complete control
- Easy to invalidate
- Easy logout
- Sensitive information never reaches the browser
- Supported by almost every web framework

---

# Limitations of Sessions

Sessions work well for small applications.

However,

they become difficult to manage at scale.

Imagine Amazon running thousands of backend servers.

```
Server A

Server B

Server C

Server D
```

Suppose Alice logs in through Server A.

Her Session exists only there.

If the next request reaches Server C,

that server has no idea who Alice is.

Possible solutions include:

- Sticky Sessions
- Shared Database
- Redis
- Distributed Session Stores

All of these add complexity.

This scalability problem eventually led to **Token-Based Authentication**, which we'll study in upcoming chapters.

---

# Interview Insights

### What is a Session?

A Session is server-side storage that maintains the authenticated state of a user across multiple HTTP requests.

---

### What is a Cookie?

A Cookie is a small piece of data stored in the browser.

In Session-based authentication,

the Cookie usually contains only the Session ID.

---

### Are Sessions stored in Cookies?

No.

Only the Session ID is stored in the Cookie.

The Session itself remains on the server.

This is one of the most commonly misunderstood interview questions.

---

### Why do Sessions exist?

Because HTTP is stateless.

Sessions allow the server to remember authenticated users across multiple requests.

---

# Common Mistakes

❌ Thinking Cookies and Sessions are the same thing.

A Cookie is stored in the browser.

A Session is stored on the server.

---

❌ Storing sensitive user information inside Cookies.

Cookies should never contain passwords or confidential data.

---

❌ Assuming Sessions scale infinitely.

Managing millions of Sessions across multiple servers is challenging.

This limitation is one of the primary reasons modern distributed applications often use JWTs.

---

# Key Takeaways

- HTTP is a stateless protocol.
- Sessions allow servers to remember authenticated users.
- Cookies store the Session ID inside the browser.
- Session data always resides on the server.
- Every request carries the Session ID automatically.
- Sessions are simple but introduce scalability challenges in distributed systems.

---

# What's Next?

Sessions solved one problem:

> "How do we remember authenticated users?"

But they introduced another.

How do you manage millions of Sessions across hundreds of servers?

The industry answered this question with **Token-Based Authentication**, which eventually led to **JWT (JSON Web Tokens)**.
