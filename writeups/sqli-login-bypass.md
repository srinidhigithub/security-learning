# SQL Injection — Allowing login bypass

**Lab:** PortSwigger Web Security Academy — SQL injection vulnerability allowing login bypass
**Status:** Solved
**Date:** 2026-06-27

## The target
An e-commerce shop that builds login query as concatenated strings
If username and password match, Login should be successful.

## The vulnerable query (my mental model)
The server builds something like:
\`\`\`sql
SELECT * FROM userss WHERE username = 'administrator' AND password = "!@#$%^&*"
\`\`\`
User input lands inside the quotes after `username =`. The `AND password = !@#$$%^&*`
ensures successful login.

## The payload
Inside username inputbox in the loginpage
typed input : `administrator'--`
Inside password inputbox in the loginpage
typed input : `abc` (anything - not blank - to pass javascript input validation)

## Why it works
Injected into the query it becomes:
\`\`\`sql
SELECT * FROM users WHERE username = 'administrator'--  AND password = 'abc'
\`\`\`
- `' ` closes the string the input was trapped in
- `--` comments out the rest, killing the `AND password = ` filter

Result: sql query returns rows. This results redirect to homepage of 'administrator'.

## Takeaway
User input was concatenated straight into a SQL query. The fix is
parameterised queries (prepared statements), so input is treated as
data, never as query logic.
