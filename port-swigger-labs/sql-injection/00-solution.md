# SQL Injection – Retrieve Hidden Data

## 1. A Short Explanation of the Vulnerability

In this lab, the goal is to retrieve hidden data by exploiting the `WHERE` clause, leaking one or more unreleased products.

The web application is vulnerable to SQL injection because user input is directly concatenated into an SQL query without proper sanitization. This allows an attacker to manipulate the structure of the query and bypass intended restrictions.

Specifically, the developers attempted to restrict the displayed products to only those that are released, but this control can be bypassed by injecting malicious input.

---

## 2. The Exploitation Process

The target is an e-commerce website where users can browse different product categories.

When selecting a category, the URL has the following structure:

```
web-security-academy.net/filter?category=Pets
```

The site exposes an endpoint named `filter`, accessed via a GET request.
The parameter `category` is used to dynamically filter products.

We are told that the SQL query used by the application looks like this:

```
SELECT * FROM products WHERE category = 'Gifts' AND released = 1
```

The developers intended to ensure that only released products are displayed by using the condition:

```
AND released = 1
```

However, since no input sanitization occurs before the query is sent to the database, an attacker can inject malicious input.

By using the payload:

```
'+OR+1=1--
```

the query becomes:

```
SELECT * FROM products WHERE category = '' OR 1=1--' AND released = 1
```

The `--` sequence comments out the rest of the query.
As a result, the `AND released = 1` condition is ignored, and the query returns all products, including unreleased ones.

---

## 3. The Root Cause

The vulnerability exists because user input is directly concatenated into an SQL query without proper sanitization or parameterized statements.

This is a classic SQL injection in a string context, where user input is embedded inside single quotes and not properly handled before being sent to the database.

---

## 4. Mitigation Strategies

To prevent this type of vulnerability:

* Use prepared statements / parameterized queries
* Implement proper input validation
* Apply the principle of least privilege to database users

---

