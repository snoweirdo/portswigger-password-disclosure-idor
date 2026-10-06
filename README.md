# PortSwigger: User ID Controlled by Request Parameter with Password Disclosure

A writeup for the PortSwigger Web Security Academy Apprentice lab:

**User ID controlled by request parameter with password disclosure**

## Overview

This lab demonstrates an Insecure Direct Object Reference (IDOR) vulnerability in the user account page.

The application uses a user-controlled `id` parameter to determine which account information is displayed. By changing the parameter from the authenticated user to `administrator`, it is possible to retrieve the administrator's password from the account page.

The recovered administrator credentials can then be used to log in and delete the user `carlos`.

## Lab Details

- Difficulty: Apprentice
- Vulnerability: IDOR
- Impact: Password disclosure and unauthorized account deletion
- Tool: Burp Suite

## Credentials

```text
Username: wiener
Password: peter
```

## TL;DR

1. Log in as `wiener`.
2. Open the **My account** page.
3. Capture the request in Burp Suite.
4. Send the request to Burp Repeater.
5. Change the `id` parameter to `administrator`.
6. Inspect the response and recover the administrator's password.
7. Log in as the administrator.
8. Delete the user `carlos`.
9. Confirm that the lab is solved.

## Example Request

```http
GET /my-account?id=administrator HTTP/2
Host: <lab-id>.web-security-academy.net
Cookie: session=<wiener-session>
```

## Key Takeaway

User identifiers must never be treated as authorization controls. Even if an application displays account information based on an `id` parameter, it must verify on the server that the authenticated user is authorized to access the requested account.

Sensitive values such as passwords should also never be returned to the client or stored in a form that makes them retrievable through unauthorized requests.

## Remediation

- Enforce server-side authorization for every account request.
- Verify that the authenticated user owns the requested account.
- Never expose passwords in HTML, API responses, or form fields.
- Store passwords using secure, one-way password hashing.
- Use generic error messages for unauthorized account access.
- Monitor attempts to access other users' account identifiers.

## Disclaimer

This writeup is for educational purposes and applies only to the authorized PortSwigger Web Security Academy lab environment.
