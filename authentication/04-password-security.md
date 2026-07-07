# Chapter 4 - Password Security

> "Passwords are the keys to your digital identity. Storing them incorrectly is like leaving the keys under the doormat."

---

# Learning Objectives

By the end of this chapter, you will be able to:

- Understand why passwords should never be stored in plain text.
- Learn the difference between Encoding, Encryption, and Hashing.
- Understand Salt and why it is necessary.
- Learn why BCrypt is preferred over SHA-256 for password storage.
- Understand how Spring Security verifies passwords.
- Answer common interview questions related to password security.

---

# The Problem

Let's imagine you're building an e-commerce application.

A new user signs up with the following credentials:

```
Email:
alice@example.com

Password:
MyPassword123
```

Where should this password be stored?

A beginner might think:

```
Users Table

Email                 Password

alice@example.com     MyPassword123
```

Simple.

Easy.

Works perfectly.

Unfortunately...

It is also one of the biggest security mistakes a developer can make.

---

# Why Plain Text Passwords Are Dangerous

Imagine your application's database is compromised.

The attacker now has access to:

```
alice@example.com

MyPassword123
```

The consequences are severe.

The attacker can now:

- Log into your application.
- Try the same password on Gmail.
- Try it on Facebook.
- Try it on Amazon.
- Try it on Banking websites.

Many users reuse passwords across multiple platforms.

One leaked password can compromise several accounts.

This is why passwords should **never** be stored in plain text.

---

# The Goal

The server should be able to answer one question:

> "Is this the correct password?"

without actually storing the password itself.

This leads us to **Hashing**.

---

# Encoding vs Encryption vs Hashing

These three terms are often confused during interviews.

Let's understand the difference.

---

## Encoding

Encoding changes the format of data so that another system can understand it.

Example:

```
Hello

↓

SGVsbG8=
```

(Base64 Encoding)

Encoding is completely reversible.

It provides **zero security**.

Its purpose is compatibility, not protection.

---

## Encryption

Encryption converts readable data into an unreadable format using a key.

```
Plain Text

↓

Encryption Key

↓

Cipher Text
```

Someone with the correct key can decrypt the data.

Encryption is **reversible**.

Examples include:

- AES
- RSA

Encryption is useful when you need to retrieve the original data later.

Passwords do **not** fall into this category.

---

## Hashing

Hashing converts data into a fixed-length value.

```
Password

↓

Hash Function

↓

Hash
```

Unlike encryption,

hashing is **one-way**.

There is no practical way to recover the original password from the hash.

This makes hashing ideal for password storage.

---

# How Password Verification Works

Instead of storing:

```
Password

↓

MyPassword123
```

The server stores:

```
Password

↓

$2a$10$Qd...
```

When the user logs in:

```
Entered Password

↓

Hash Again

↓

Compare

↓

Match?
```

If both hashes match,

authentication succeeds.

Notice something important.

The original password is never stored.

---

# Can We Use SHA-256?

This is one of the most common interview questions.

Many beginners answer:

> "Yes."

The correct answer is:

> "No."

SHA-256 is an excellent cryptographic hash function.

It is **not** designed for password storage.

Why?

Because it is **too fast**.

Modern GPUs can calculate billions of SHA-256 hashes every second.

This makes brute-force attacks much easier.

---

# Introducing Salt

Suppose two users choose the same password.

```
Password123
```

Without Salt:

```
SHA-256

↓

A1B2C3...
```

Both users produce exactly the same hash.

An attacker immediately knows they use the same password.

Salt solves this problem.

---

# What is Salt?

Salt is a random value added before hashing.

```
Password

+

Random Salt

↓

Hash
```

Now two users with the same password produce completely different hashes.

Example:

```
User A

Password123

+

Salt A

↓

Hash A
```

```
User B

Password123

+

Salt B

↓

Hash B
```

Same password.

Different hashes.

This makes precomputed attacks (Rainbow Tables) practically useless.

---

# Why BCrypt?

BCrypt is specifically designed for password hashing.

Unlike SHA-256,

BCrypt is intentionally slow.

That sounds strange at first.

Why would we want a slower algorithm?

Because attackers also become slower.

If verifying one password takes 100 milliseconds,

trying one billion passwords becomes extremely expensive.

BCrypt turns computational power into a security advantage.

---

# BCrypt Features

- Automatically generates a unique Salt.
- Adaptive work factor.
- Resistant to Rainbow Table attacks.
- Widely supported.
- Recommended by Spring Security.

---

# Password Verification with BCrypt

Signup:

```
Password

↓

BCrypt

↓

Store Hash
```

Login:

```
Entered Password

↓

BCrypt Verify

↓

Stored Hash

↓

Match?

↓

Authentication Successful
```

Notice that BCrypt does **not** compare plain-text passwords.

It verifies whether the entered password could have produced the stored hash.

---

# Spring Security Example

```java
PasswordEncoder encoder = new BCryptPasswordEncoder();

String hash = encoder.encode(password);

boolean matches = encoder.matches(password, hash);
```

Spring Security automatically handles:

- Salt generation
- Hash comparison
- BCrypt configuration

Developers should never implement password hashing manually.

---

# Common Interview Questions

## Why not store plain passwords?

Because database leaks would expose every user's credentials.

---

## Why not encrypt passwords?

Encryption is reversible.

Passwords do not need to be recovered.

They only need to be verified.

---

## Why is BCrypt slow?

To make brute-force attacks computationally expensive.

---

## Does BCrypt use Salt?

Yes.

BCrypt automatically generates and stores a unique Salt for every password.

---

## Can two users have the same BCrypt hash?

No.

Even if they choose the same password,

the generated Salt is different.

---

# Common Mistakes

❌ Storing passwords in plain text.

❌ Using Base64 instead of hashing.

❌ Using MD5 or SHA-1 for password storage.

❌ Implementing your own hashing algorithm.

❌ Comparing passwords directly instead of using BCrypt's verification method.

---

# Key Takeaways

- Never store passwords in plain text.
- Encoding is not security.
- Encryption is reversible.
- Hashing is one-way.
- Salt prevents identical passwords from producing identical hashes.
- BCrypt is intentionally slow to resist brute-force attacks.
- Spring Security provides BCrypt support out of the box.

---

# What's Next?

Sessions work well for traditional web applications.

However, modern applications often consist of multiple services, mobile apps, and APIs distributed across many servers.

Managing server-side sessions in such environments becomes increasingly difficult.

This challenge led to a new approach:

**Token-Based Authentication**, and ultimately, **JSON Web Tokens (JWT)**.
