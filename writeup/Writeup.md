[[README]]

## Good Structure of Write-up

To make your write-ups clear and actionable, you should follow a consistent structure for each one. Here's a format that works well:

**Title**** - A short, descriptive heading (e.g. "Unauthenticated SQL Injection in Login Form")

**Risk Rating** - A risk rating for the discovered vulnerability. Vulnerabilities should always be rated in isolation, as if all other vulnerabilities did not exist and should either use the client's risk rating matrix or a public one, such as CVSS.

**Summary** - A brief explanation of the vulnerability and its potential impact in plain language.

**Background** - Provide additional context to explain the vulnerability and why it matters. This is especially important if the reader is unfamiliar with it. Remember that the developers who will fix the vulnerability are potentially not security experts, so more guidance to help them understand the root cause of the vulnerability will aid them in remediating the issue accurately.

**Technical Details & Evidence** - Where and how the issue was found. Include requests, responses, payloads, and screenshots or code snippets if needed.

**Impact** - What an attacker could realistically do with this vulnerability. This shows that you are not just providing the vulnerability without thinking about how a real threat actor could leverage it in the specific system or application where you found it. For example, it is common to say with XSS that the threat actor would steal the user's cookie to perform session hijacking. But what if the application uses tokens instead? Does that now mean that the impact is lower? Make sure to contextualise the impact to the specific system that you are testing.

**Remediation Advice** - Clear, actionable steps to resolve the issue. It is critical to ensure that your remediation advice will address the root cause of the vulnerability. While you may want to provide additional measures that will aid in further mitigation, your first recommendation should address the vulnerability at its core. Consider, for example, SQL Injection. While sanitisation and input validation can help mitigate the vulnerability and make it harder to exploit, parameterisation is required to address the vulnerability at its core. This ensures that regardless of the input, there can be no confusion between the SQL command and user-supplied input. Always ensure that your recommendation will fully resolve the vulnerability, not just mitigate its impact. If you wish to provide further defence-in-depth controls, make sure to mention that these cannot be implemented in isolation.

**References** - (Optional) Links to relevant vendor documentation or guidance to support the fix.

## Example: SQL Injection Write-Up
**Title: Unauthenticated SQL Injection in Login Form

**Risk Rating: High (CVSS 3.1 Base Score: 8.6)

**Summary**: An unauthenticated SQL injection vulnerability was identified in the login form of the TryBankMe application. This could allow an attacker to bypass authentication or extract sensitive customer information.

**Background:**

SQL Injection occurs when user-supplied input is unsafely included in SQL queries without proper parameterisation. The SQL interpreter is unable to discern SQL commands from the user-supplied input, leading to confusion that allows a threat actor to inject directly into the SQL command itself. This vulnerability is commonly exploited to bypass authentication or extract sensitive data.

**Technical Details & Evidence:**

The login form at /login was vulnerable to SQL Injection via the username field. Using Burp Suite, the following HTTP request was intercepted and modified:
```
POST /login HTTP/1.1
Host: trybankme.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 45
username=' OR 1=1--&password=randomvalue
```

The application responded with a 302 Found redirect to the authenticated user dashboard:
```
HTTP/1.1 302 Found
Location: /dashboard
Set-Cookie: sessionid=abc123xyz456; Path=/; HttpOnly
```

This confirmed that authentication was bypassed using a classic SQL Injection payload.

**Impact:

An attacker could log in as any user without valid credentials. Furthermore, even though the injection points were a blind-injection, a threat actor could leverage error-based injections to enumerate the information from the database.

**Remediation Advice:

Parameterised queries should be used for all database operations involving user input. For example, in a .NET application using MS SQL as the database, the login query should be altered to look like this:

```
using (SqlConnection connection = new SqlConnection(connectionString))
{
  string query = "SELECT * FROM Users WHERE Username = @username AND Password = @password";
  SqlCommand command = new SqlCommand(query, connection);
  command.Parameters.AddWithValue("@username", inputUsername);
  command.Parameters.AddWithValue("@password", inputPassword);
  connection.Open();
  SqlDataReader reader = command.ExecuteReader();
  if (reader.HasRows)
  {
      // Login successful
  }
}
```

As shown in the example above, the user input is distinctly split from the SQL command, ensuring that the SQL engine cannot be confused.

**References:

    https://tryhackme.com/room/sqlinjectionlm

Your vulnerability write-ups are the section that most readers will spend the most time in, especially those tasked with fixing the issues. Clear structure, solid evidence, and practical guidance make the difference between being ignored and being taken seriously.