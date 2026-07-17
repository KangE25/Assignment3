Assignment 3 – Write-Up
Fix 1
  Vulnerability:
The login page built its SQL query by directly concatenating the username and password into the SQL statement. This allowed an attacker to inject SQL code into the username field and bypass authentication.

  Exploit:
The following payload was used is: curator' --

The injected SQL commented out the password comparison, allowing a login as the **curator** account without knowing the password.

  Fix:
The login query was changed to use parameterized SQL with placeholders (`?`).

The username and password are now passed as parameters instead of being inserted directly into the SQL string.

  Why it Works:
Parameterized queries separate SQL commands from user input. The database treats the supplied values as data rather than executable SQL, preventing SQL injection attacks.


Fix 2
  Vulnerability:
The search page concatenated both the search term and the ORDER BY clause directly into the SQL statement. An attacker could inject a UNION SELECT statement to retrieve information from other database tables.

  Exploit:
A UNION-based SQL injection payload was used to retrieve usernames and passwords from the users table.

  Fix:
Two changes were made:
  * The search value now uses SQL placeholders (`?`) instead of string concatenation.
  * The ORDER BY clause now uses an allow-list of valid sort options instead of accepting arbitrary user input.

  Why it Works:
Using parameterized queries prevents attackers from modifying the SQL statement, while the allow-list prevents malicious SQL from being inserted into the ORDER BY clause.


Fix 3
  Vulnerability:
The search term entered by the user was inserted directly into the HTML page without encoding. This allowed malicious HTML and JavaScript to be executed by the browser.

  Exploit:
The following payload demonstrated the vulnerability: <img src=x onerror=alert(1)>

  Fix:
An HTML encoding function (`escapeHtml()`) was created and used before displaying the search term.

  Why it Works:
HTML encoding converts special characters such as `<` and `>` into safe text, preventing the browser from interpreting them as HTML or JavaScript.


Fix 4
  Vulnerability:
Comments submitted by users were stored and later displayed without HTML encoding. Any visitor viewing the listing would execute malicious scripts stored in comments.

  Exploit:
A malicious comment containing an image with an `onerror` handler was successfully stored and executed when another user viewed the page.

  Fix:
On the server side, the application encodes comment bodies before displaying them: escapeHtml(c.body). Comment authors are also HTML encoded.

  Why it Works:
Encoding user input prevents browsers from interpreting it as executable HTML, while `textContent` displays the value as plain text instead of parsing it as markup.


Fix 5
  Part A
  Vulnerability:
The session cookie was created without any security attributes. JavaScript could read the cookie, and it would be sent with cross-site requests.

  Fix:
The cookie now includes:
  * HttpOnly
  * SameSite=Lax

  Why it Works
HttpOnly prevents JavaScript from reading the session cookie, reducing the impact of XSS attacks.
SameSite=Lax helps prevent Cross-Site Request Forgery (CSRF) attacks by restricting when cookies are sent with cross-site requests.

  Part B 
Vulnerability:
The application did not send a Content Security Policy header. As a result, browsers would execute injected inline scripts and event handlers.

  Fix:
A Content Security Policy middleware was added to every response

  Why it Works:
The CSP only allows trusted resources from the application's own origin. Inline scripts and injected event handlers are blocked, providing an additional layer of defense even if an XSS vulnerability exists.


Testing, after implementing all fixes, each exploit script was executed again, all secured.
