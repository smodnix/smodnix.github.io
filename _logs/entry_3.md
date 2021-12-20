---
layout: post
title: "Entry 3"
date: 2021-12-19
permalink: /logs/entry-3 
tags: security
---

I read a blog post{% sidenote 1 "[Simple things that are actually hard: User Authentication](https://techblog.bozho.net/simple-things-that-are-actually-hard-user-authentication/){:target='_blank'}{:rel='noopener noreferrer'}" %} which talks about authentication and gives a nice inclusive checklist what should you pay attention when you building authentication in a system.

- **Cookie security** – how to make it so that a cookie doesn’t leak or can’t be forged. Should you even have a cookie, or use some stateless approach like JWT, use SameSite lax or strict?
- Bind cookie to IP and logout user if IP changes?
- **Password requirements** – minimum length, special characters? UI to help with selecting a password?
- **Storing passwords in the database** – bcrypt, scrypt, PBKDF2, SHA with multiple iterations?
- **Allow storing in the browser?** Generally “yes”, but some applications deliberately hash it before sending it, so that it can’t be stored automatically
- **Email vs username** – do you need a username at all? Should change of email be allowed?
- **Rate-limiting authentication attempts** – how many failed logins should block the account, for how long, should admins get notifications or at least logs for locked accounts? Is the limit per IP, per account, a combination of those?
- **Captcha** – do you need captcha at all, which one, and after how many attempts? Is Re-Captcha an option?
- **Password reset** – password reset token database table or expiring links with HMAC? Rate-limit password reset?
- **SSO** – should your service should support LDAP/ActiveDirectory authentication (probably yes), should it support SAML 2.0 or OpenID Connect, and if yes, which ones? Or all of them? Should it ONLY support SSO, rather than internal authentication?
- **2FA** – TOTP or other? Implement the whole 2FA flow, including enable/disable and use or backup codes; add option to not ask for 2FA for a particular device for a period of time? Configuring subset of AD/LDAP users to authenticate based on certain group memberships?
- **Force 2FA by admin configuration** – implement time window for activating 2FA after a global option is enabled?
- **Login by link** – should the option to send a one-time login link be email be supported?
- **XSS protection** – make sure no XSS vulnerabilities exist especially on the login page (but not only, as XSS can steal cookies)
- **Dedicated authentication log** – keep a history of all logins, with time, IP, user agent
- **Force logout** – is the ability to logout a logged-in device needed, how to implement it, e.g. with stateless tokens it’s not trivial.
- **Keeping a mobile device logged in** – what should be stored client-side? (certainly not the password)
- **Working behind proxy** – if the client IP matters (it does), make sure the X-Forwarded-For header is parsed
- **Capture login timezone** for user and store it in the session to adjust times in the UI?
- **TLS Mutual authentication** – if we need to support hardware token authentication with private key, we should enable TLS mutual. What should be in the truststore, does the web server support per-page mutual TLS or should we use a subdomain, if there’s a load balancer / reverse proxy, does it support it and how to forward certificate details?
- **Require account activation** or let the user login immediately after registration? Require account approval by back-office staff?
- **Initial password setting for accounts created by admins** – generate initial password and force changing it on first login? - Don’t generate password and start from a password reset flow?
- **Login anomalies** – how to detect them and should you inform the user? Should you rely on 3rd party tools (e.g. a SIEM), or have such functionality built-in?

So, it is necessary for developers and security professionals should to know how to implement this checklist in real world  if we are talking about secure and robust authentication system. 