# Chapter 5 - Token-Based Authentication & JSON Web Tokens (JWT)

> "Sessions solved one problem but created another. JWT was the industry's answer to building scalable, stateless applications."

---

# Learning Objectives

By the end of this chapter, you will be able to:

- Understand why Session-based authentication becomes difficult to scale.
- Learn the concept of Token-Based Authentication.
- Understand what a JWT is and why it is widely used.
- Learn the structure of a JWT.
- Understand how JWTs are created and verified.
- Learn the advantages and limitations of JWTs.
- Answer JWT-related interview questions confidently.

---

# Why Sessions Started Failing

Sessions work extremely well for traditional web applications.

Let's revisit the Session architecture.

```
            Browser
                |
                | Cookie (Session ID)
                |
        +------------------+
        |  Server          |
        |                  |
        | Session Store    |
        +------------------+
```

The server stores every logged-in user's session.

For small applications, this is perfectly fine.

But imagine your application grows.

Instead of one server...

You now have ten.

```
                Load Balancer
                     |
      ---------------------------------
      |        |        |        |
   Server1  Server2  Server3  Server4
```

Now Alice logs in.

Unfortunately, her Session exists only on **Server 1**.

The next request might reach **Server 3**.

Server 3 has never seen Alice before.

As far as it knows,

she is not logged in.

---

# Possible Solutions

Engineers tried several approaches.

## Sticky Sessions

The Load Balancer always sends Alice to Server 1.

```
Alice

↓

Server 1

↓

Server 1

↓

Server 1
```

Problem?

If Server 1 crashes,

Alice is logged out.

---

## Shared Database

Store Sessions in a database.

```
Server 1

↓

Database

↑

Server 2
```

Problem?

Every request now requires an extra database lookup.

As traffic grows,

the database becomes another bottleneck.

---

## Redis

Many companies use Redis as a centralized Session Store.

```
Server

↓

Redis

↓

Session
```

This works well.

But you're still maintaining server-side state.

Infrastructure becomes more complex.

---

# A Different Way of Thinking

Instead of asking:

> "Where should we store Sessions?"

Engineers asked a different question.

> "Can we eliminate Sessions completely?"

This idea gave birth to **Token-Based Authentication**.

---

# What is Token-Based Authentication?

Instead of storing authentication information on the server,

the server gives the client a token.

The client stores this token and sends it with every request.

```
Client

↓

Login

↓

Server

↓

Token

↓

Client Stores Token

↓

Future Requests

↓

Authorization Header

↓

Server Verifies Token
```

Notice something important.

The server no longer needs to remember every logged-in user.

The token contains enough information to identify the user.

This makes the application **stateless**.

---

# What is a Token?

A token is simply a piece of data that proves a user has already been authenticated.

Think of it like a movie ticket.

When you buy a ticket,

the theatre doesn't ask you to pay again every time you enter a different screen.

Instead,

you show the ticket.

The ticket proves you've already paid.

Similarly,

a token proves you've already logged in.

---

# Introducing JWT

JWT stands for

**JSON Web Token**

It is a compact, digitally signed token used to securely exchange information between two parties.

JWT is defined by **RFC 7519**.

It has become one of the most popular authentication mechanisms for REST APIs and microservices.

---

# A JWT Looks Like This

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9

.

eyJzdWIiOiIxMjMiLCJuYW1lIjoiQWxpY2UifQ

.

SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

Three parts.

Separated by dots.

```
Header

.

Payload

.

Signature
```

---

# Part 1 - Header

The Header describes the token.

Example:

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

It tells the receiver:

- This is a JWT.
- It was signed using HS256.

---

# Part 2 - Payload

The Payload contains information about the user.

Example:

```json
{
    "sub":"123",
    "name":"Alice",
    "role":"ADMIN"
}
```

These values are called **Claims**.

Some common claims include:

| Claim | Meaning |
|--------|----------|
| sub | Subject (User ID) |
| iss | Issuer |
| exp | Expiration Time |
| iat | Issued At |
| aud | Audience |

Developers can also add custom claims such as:

- role
- email
- permissions

---

# Important Note

The Payload is **Base64URL encoded**.

It is **NOT encrypted**.

Anyone holding the token can decode it.

Therefore,

never store:

- Passwords
- Credit Card Numbers
- Secret Keys

inside a JWT.

---

# Part 3 - Signature

The Signature protects the token from tampering.

It is generated using:

```
Header

+

Payload

+

Secret Key

↓

Hashing Algorithm

↓

Signature
```

If someone modifies even a single character inside the Payload,

the Signature becomes invalid.

The server immediately rejects the token.

---

# Complete Authentication Flow

## Step 1

User logs in.

```
POST /login
```

---

## Step 2

Server verifies credentials.

---

## Step 3

Server generates JWT.

```
Header

+

Payload

+

Signature
```

---

## Step 4

Server returns JWT.

```json
{
    "accessToken":"eyJhbGc..."
}
```

---

## Step 5

Client stores the token.

Common storage options include:

- Memory (Recommended for SPAs)
- Secure HTTP-only Cookies
- Mobile Secure Storage

---

## Step 6

Future requests include the token.

```http
GET /products

Authorization:

Bearer eyJhbGc...
```

---

## Step 7

Server verifies the signature.

If valid,

the request proceeds.

Otherwise,

the request is rejected.

---

# Why JWT Scales Better

Unlike Sessions,

the server does not need to remember users.

Every request carries the authentication information.

```
Client

↓

JWT

↓

Server A

↓

JWT

↓

Server B

↓

JWT

↓

Server C
```

Every server can verify the token independently.

No shared Session Store.

No Sticky Sessions.

This is why JWT became popular in distributed systems.

---

# Advantages of JWT

- Stateless authentication
- Excellent for REST APIs
- Works well with microservices
- Reduces server memory usage
- Easy horizontal scaling
- Self-contained token

---

# Limitations of JWT

JWT is not perfect.

Some common challenges include:

## Logout

Once issued,

a JWT remains valid until it expires.

Unlike Sessions,

the server cannot simply delete it.

---

## Token Revocation

Suppose a user's account is compromised.

How do you immediately invalidate an already-issued JWT?

This requires additional mechanisms such as:

- Token Blacklists
- Short-lived Access Tokens
- Refresh Tokens

We'll study these later.

---

## Large Payloads

Developers sometimes put too much information inside a JWT.

Remember:

Every request carries the token.

Large tokens increase network overhead.

Keep JWTs small.

---

# JWT vs Sessions

| Sessions | JWT |
|----------|-----|
| Stateful | Stateless |
| Server stores authentication | Client stores token |
| Easy logout | Logout requires additional strategy |
| Requires Session Store | No Session Store |
| Harder to scale | Easier to scale |
| Good for traditional MVC apps | Excellent for REST APIs |

Neither approach is universally better.

Choose based on your application's architecture.

---

# Interview Insights

## Why is JWT called Stateless?

Because the server does not store authentication state.

Every request contains the required authentication information.

---

## Is JWT encrypted?

No.

A JWT is signed, not encrypted.

The Payload can be decoded.

Never store confidential information inside it.

---

## Why can't users modify the Payload?

Because changing the Payload changes the Signature.

The server detects the modification and rejects the token.

---

## Can JWT replace Sessions everywhere?

No.

Traditional server-rendered applications often work perfectly with Sessions.

JWT shines in distributed architectures, APIs, SPAs, and mobile applications.

Choosing between Sessions and JWT depends on the application's requirements.

---

# Common Mistakes

❌ Assuming Base64 encoding means encryption.

❌ Storing sensitive information inside JWTs.

❌ Creating tokens that never expire.

❌ Putting entire user objects into the Payload.

❌ Using JWT simply because it is popular.

Technology should solve a problem, not follow a trend.

---

# Key Takeaways

- Sessions are stateful; JWTs are stateless.
- JWT contains Header, Payload, and Signature.
- The Payload is readable but protected from modification.
- JWT signatures ensure integrity, not confidentiality.
- Stateless authentication simplifies scaling in distributed systems.
- JWT is one of the most widely used authentication mechanisms for modern REST APIs.

---

# What's Next?

A JWT tells your application **who** the user is.

But modern applications often need to answer another question:

> "Can this application access another application on behalf of the user?"

For example:

- Logging in with Google
- Signing in with GitHub
- Connecting your Google Drive to another application

This problem is not solved by JWT.

It is solved by **OAuth 2.0**, the industry standard for delegated authorization.
