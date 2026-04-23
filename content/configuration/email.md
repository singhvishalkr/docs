---
title: Email
description: Configuration for email settings and templates.
---

:partial{content="config-env-vars"}

## Email Transport

| Variable             | Description                                                              | Default Value |
| -------------------- | ------------------------------------------------------------------------ | ------------- |
| `EMAIL_VERIFY_SETUP` | Check if email setup is properly configured.                             | `true`        |
| `EMAIL_TRANSPORT`    | What to use to send emails. One of `sendmail`, `smtp`, `mailgun`, `ses`. | `sendmail`    |

Based on the `EMAIL_TRANSPORT` used, you must also provide additional variables.

### Sendmail

| Variable                  | Description                             | Default Value        |
| ------------------------- | --------------------------------------- | -------------------- |
| `EMAIL_SENDMAIL_NEW_LINE` | What new line style to use in sendmail. | `unix`               |
| `EMAIL_SENDMAIL_PATH`     | Path to your sendmail executable.       | `/usr/sbin/sendmail` |

### SMTP

| Variable                | Description           | Default Value |
| ----------------------- | --------------------- | ------------- |
| `EMAIL_SMTP_HOST`       | SMTP server host.     |               |
| `EMAIL_SMTP_PORT`       | SMTP server port.     |               |
| `EMAIL_SMTP_USER`       | SMTP user.            |               |
| `EMAIL_SMTP_PASSWORD`   | SMTP password.        |               |
| `EMAIL_SMTP_POOL`       | Use SMTP pooling.     |               |
| `EMAIL_SMTP_SECURE`     | Enable TLS.           |               |
| `EMAIL_SMTP_IGNORE_TLS` | Ignore TLS.           |               |
| `EMAIL_SMTP_NAME`       | SMTP client hostname. |               |

### Mailgun

| Variable                | Description                                                                        | Default Value     |
| ----------------------- | ---------------------------------------------------------------------------------- | ----------------- |
| `EMAIL_MAILGUN_API_KEY` | Your Mailgun API key.                                                              |                   |
| `EMAIL_MAILGUN_DOMAIN`  | A domain from [your Mailgun account](https://app.mailgun.com/app/sending/domains). |                   |
| `EMAIL_MAILGUN_HOST`    | Specify a custom host.                                                             | `api.mailgun.net` |

### AWS SES

| Variable                                   | Description                 | Default Value |
| ------------------------------------------ | --------------------------- | ------------- |
| `EMAIL_SES_CREDENTIALS__ACCESS_KEY_ID`     | Your AWS SES access key ID. |               |
| `EMAIL_SES_CREDENTIALS__SECRET_ACCESS_KEY` | Your AWS SES secret key.    |               |
| `EMAIL_SES_REGION`                         | Your AWS SES region.        |               |

::callout{icon="material-symbols:info-outline"}

**Required IAM permissions**<br/>

For the SES transport to both send email and pass the `/server/health` check, the IAM identity used by `EMAIL_SES_CREDENTIALS__ACCESS_KEY_ID` needs:

- **Actions**: `ses:GetAccount` and `ses:SendRawEmail`.
- **Resources**: the verified SES identity that matches `EMAIL_FROM`, plus the dummy identity `arn:aws:ses:<EMAIL_SES_REGION>:<account-id>:identity/invalid@invalid` that Directus hits during the email health check.

If `ses:GetAccount` or the `invalid@invalid` identity is missing, password-reset emails still send but `/server/health` returns a confusing `Converting circular structure to JSON` error.

::

## Email Templates

Templates can be used to add custom templates for your emails, or to override the system emails used for things like resetting a password or inviting a user.

| Variable               | Description                               | Default Value          |
| ---------------------- | ----------------------------------------- | ---------------------- |
| `EMAIL_FROM`           | Email address from which emails are sent. | `no-reply@example.com` |
| `EMAIL_TEMPLATES_PATH` | Where custom templates are located        | `./templates`          |

In the `EMAIL_TEMPLATES_PATH`, you can create templates for your emails by adding [`.liquid`](https://liquidjs.com) files. 

### Overriding System Emails

There are a number of templates provided by Directus that can be overridden with a custom template: 

| Template           | File                    |
| ------------------ | ----------------------- |
| Password Reset     | `password-reset.liquid` |
| User Invitation    | `user-invitation.liquid` |
| User Registration  | `user-registration.liquid` |

When overriding the default email templates, make sure to include the provided `url` somewhere to ensure the email is functional.

## Email Rate Limiting

Emails can be rate limited by opting in to our [email rate limiters](/configuration/security-limits#email-rate-limiting)
