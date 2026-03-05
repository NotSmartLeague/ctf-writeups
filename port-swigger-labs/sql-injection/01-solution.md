# SQL injection vulnerability allowing login bypass 

## 1. A Short Explanation of the Vulnerability 
The lab's goal is to exploit an SQL Injection vulnerability in the login form, logging into the website as `administrator`.

With a black box analysis, we can assume that the code of the backend that handles login is something like: 
```
sql = f"SELECT * FROM user WHERE username = '{username}' AND password = '{password}'"
```

After the execution, the backend checks whether the result set contains at least one row, and the code should be something like:
```
if result_set:
    give_access()
```
- true if the result set is not empty, that means (at least) one user matching the input credentials exists 
- false if the result set is empty, that means no user in the db matches the credentials 

Since no proper sanitization is made by the backend, an attacker can inject malicious payload, bypassing the login phase.


## 2. The Exploitation Process

In this case the strategy is to alter the query structure, in order to eliminate the `AND` clause. We can inject the simple payload `username'--` in the username field, where:
- `username` is the username we want to log in with
- `'` is used to match the closing quote for the string comparison in SQL
- `--` is used to comment out the entire `AND` clause

Notice: the syntax for the comment may change from one DBMS to one another, we can either bruteforce with different payloads or trying to find the current DBMS version.

The lab's goal is to log in as `administrator` user, so we can insert the payload `administrator'--` in the username field, then we can insert any string in the password field.

Now the final query is going to be something like: `SELECT * FROM user WHERE username = 'administrator'--' AND password = 'anystringwetyped'`.

Notice how the entire `AND` clause is commented out, the result set will not be empty, so the backend is going to give access to the attacker as `administrator` user.


## 3. The Root Cause

The vulnerability exists because user input is directly concatenated into an SQL query without proper sanitization or parameterized statements, leading to a classic SQLi attack.

## 4. Mitigation Strategies

We can prevent SQL injection using:
- prepared statements and parameterized queries
- implement proper input validation

 

