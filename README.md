# Script injections safety issues

A script injection is a flaw mostly present in the web. it occurs when a programming language uses user input to execute code without filtering it. This vulnerability is similar to the OS command injection but differs in the way that it is executed.

<img src="https://www.cloudprotector.com/wp-content/uploads/2021/10/diagram-of-a-command-injection-1024x413.png"/>

## Impact of a Script Language Injection

The script language injections have the following impacts:
 
### OS Command Injection:

Script injections allow to perform command injections with functions like system() which executes a command on the OS. 

### SQL Injection:

If the victim server uses a database, it is then possible to perform SQL injections and have risks.
 
### Cross Site Scripting (XSS):

When a script language injection vulnerability is located on the client side, the application becomes vulnerable to reflected XSS, the consequences of which can be multiple. 


### How would you prevent script injections?
- Creating a whitelist for users to choose from.
- Unlike whitelisting, input validation is much more flexible in terms of possibilities for the end-user.
- do not allow any dynamic code execution in the application:
  - This means you should avoid language constructs like `eval `
  - Function constructor, which allows dynamically to define a function based on string literals like:
    `const addition = new Function(‘a’, ‘b’, ‘return a+b’);`
-  Use a Node.js security linter like **eslint**
- It is also a good idea to turn off the visibility of database errors on your production sites. Database errors can be used with SQL Injection to gain information about your database **( Error-Based SQL Injection )** by generating an error that informs the actor of the database’s structure.
- example: inserting 123'  to database.

  ![image](https://user-images.githubusercontent.com/87938745/187073984-b154eb9c-36ea-4f31-8cdb-7f15deccfa19.png)
- Web application firewall that operates via defined customizable web security rules.
-  **parametrized queries including prepared statements.**

### What is the Parameterized query?
  - A parameterized query is a type of SQL query that requires at least one parameter for execution. A placeholder is normally substituted for the parameter in the SQL     query. The parameter is then passed to the query in a separate statement.

JS
```js
const addUser = userData => {
  const { name, location } = userData;
  const sql = {
    text: "INSERT INTO users (name, location) VALUES ($1, $2) returning *;",
    values: [name, location]
  };
  return dbConnection.query(sql);
};

** أحمد صافي مر من هنا **
```


  - Why we use it?
    One major reason for using parameterized queries is that they make queries more readable. The second and most compelling reason is that parameterized queries help       to protect the database from SQL injection attacks.
    
### 4- Bad practices in `SQL` and some sampels codes:
#### Bad Practices:
- Using `SELECT *`
- Using `WHILE(1=1)` or `WHILE(""="")`
- Using batched SQL statements.

### Using `SELECT *`

- Unneccessary I/O
- Increased Network traffic
- More Application memory
- Risky while copying data


### Using `WHILE(1=1)` or `WHILE(""="")`

JS

    UserId = getRequestString("UserId");
    txtSQL = "SELECT * FROM Users WHERE UserId = " + UserId;

Look at the example above again. The original purpose of the code was to create an SQL statement to select a user, with a given user id.
If there is nothing to prevent a user from entering "wrong" input, the user can enter some "smart" input like this:

<img src="https://i.ibb.co/zxfQn64/Screenshot-from-2022-08-28-23-03-21.png" />

Then, the SQL statement will look like this:

sql

    SELECT * FROM Users WHERE UserId = 105 OR 1=1;

The SQL above is valid and will return ALL rows from the User table, since OR 1=1 is always TRUE.

### Using batched SQL statements

A batch of SQL statements is a group of two or more SQL statements, separated by semicolons.

The SQL statement below will return all rows from the "User" table, then delete the "Orders" table.
sql

    SELECT * FROM User; DROP TABLE orders 
 
Look at the following example: 

JS

    UserId = getRequestString("UserId");
    txtSQL = "SELECT * FROM Users WHERE UserId = " + UserId;

And the following input:

<img src="https://i.ibb.co/nQP0Pkw/Screenshot-from-2022-08-28-23-19-11.png" />

The valid SQL statement would look like this:

sql

    SELECT * FROM Users WHERE UserId = 105; DROP TABLE Suppliers;

So the SQL statement above will return all information about Ted from the "User" table, then delete the "Suppliers" table.
