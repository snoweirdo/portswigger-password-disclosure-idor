# Lab Report: User ID Controlled by Request Parameter with Password Disclosure

## Lab Overview

This PortSwigger Web Security Academy Apprentice lab demonstrated an Insecure Direct Object Reference (IDOR) vulnerability that allowed access to another user's account information.

The account page displayed the current user's existing password in a masked input field. By modifying the user ID parameter, it was possible to access the administrator's account page and retrieve the administrator's password.

The objective was to obtain the administrator's password, log in to the administrator account, and delete the user `carlos`.

## Credentials

```text
Username: wiener
Password: peter
```

## Tools Used

- Burp Suite
- Burp Proxy
- Burp Repeater
- PortSwigger Web Security Academy browser

## Exploitation Steps

1. Logged in to the application using the provided credentials:

   ```text
   wiener:peter
   ```

2. Navigated to the **My account** page.

3. Captured the account-page request using Burp Proxy.

4. Sent the captured request to Burp Repeater.

5. Identified the `id` parameter used by the application to determine which user account should be displayed.

6. Changed the value of the `id` parameter from the authenticated user to:

   ```text
   administrator
   ```

7. Sent the modified request from Burp Repeater.

8. Inspected the response body and found that it contained the administrator's account information.

9. Located the administrator's password in the response. The password was exposed through the masked password field returned in the page source.

10. Logged out of the `wiener` account.

11. Logged in using the administrator username and the recovered password.

12. Accessed the administrator panel.

13. Deleted the user `carlos`.

14. Confirmed that the lab was successfully solved.

## Modified Request

```http
GET /my-account?id=administrator HTTP/2
Host: <lab-id>.web-security-academy.net
Cookie: session=<wiener-session>
```

The important change was replacing the original user identifier with `administrator`.

## Result

The server returned the administrator's account page to the authenticated `wiener` session. The response contained the administrator's password, even though the request was made using a non-administrator account.

The recovered password was then used to log in as the administrator and delete `carlos`, completing the lab.

## Vulnerability Identified

The application contained an **Insecure Direct Object Reference (IDOR)** vulnerability caused by a user-controlled account identifier.

The server trusted the `id` parameter without verifying that the authenticated user was authorized to access the requested account. This allowed `wiener` to request the administrator's account page.

The application also exposed a password in the page response. Passwords should never be returned to the client, even inside masked input fields, because masking only changes how the value is displayed and does not protect the value in the HTML source or HTTP response.

## Impact

An attacker with a valid account could:

- Access another user's private account information.
- Retrieve the administrator's password.
- Log in to the administrator account.
- Access administrative functionality.
- Delete or modify other users.
- Potentially take full control of the application.

In a real application, password disclosure could lead to account takeover, data exposure, unauthorized administrative actions, and compromise of other systems if the password was reused.

## Recommended Remediation

- Enforce server-side authorization checks for every account request.
- Verify that the authenticated user is authorized to access the requested user ID.
- Never return passwords in HTML, API responses, hidden fields, or masked input fields.
- Store passwords using a strong, one-way password-hashing algorithm.
- Require password reset or re-authentication instead of displaying an existing password.
- Return `403 Forbidden` or `404 Not Found` for unauthorized account requests.
- Use generic error messages that do not reveal sensitive account information.
- Monitor and alert on attempts to access other users' account identifiers.

## Key Takeaway

A masked password field is not a security control. If the password is included in the HTTP response, an attacker can inspect the page source or proxy traffic and recover it.

User-controlled IDs must always be combined with server-side authorization checks, and sensitive credentials must never be disclosed to the client.

## Disclaimer

This report was created for educational purposes and applies only to the authorized PortSwigger Web Security Academy lab environment.