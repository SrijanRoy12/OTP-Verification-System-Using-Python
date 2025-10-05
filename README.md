# OTP Email Verification (Python)

A simple Python script to generate and send One-Time Passwords (OTP) over email using Gmail's SMTP server. This repository contains a minimal implementation suitable for learning and quick demos (e.g., running in Google Colab).

---

## Features

* Generates a 6-digit numeric OTP.
* Sends OTP via Gmail SMTP using an **App Password** (recommended).
* Accepts recipient email via user input and verifies OTP entered by the user.
* Minimal dependencies (uses Python standard library).

---

## Security note

**Do not commit secrets** (Gmail passwords or App Passwords) to source control. Use environment variables or secret managers.

Gmail no longer allows plain username/password SMTP access for accounts without 2-Step Verification. Use an **App Password** (16-character) after enabling 2-Step Verification on your Google account.

---

## Files

* `otp_send.py` – Example script that generates and emails OTPs.
* `README.md` – This file.

---

## Quick start

### 1. Prerequisites

* Python 3.7+
* A Gmail account with **2-Step Verification** enabled.
* An **App Password** generated for "Mail" (or named for your application).

### 2. Generate an App Password (short)

1. Go to your Google Account → **Security**.
2. Enable **2-Step Verification** if it isn't already.
3. Under **Signing in to Google**, open **App passwords**.
4. Create a new app password (select **Mail** and your device or "Other").
5. Copy the 16-character password (space separated in Google UI). Use it in place of your regular password.

### 3. Set environment variables (recommended)

Create a `.env` (do **not** commit) or set environment variables in your system:

```
GMAIL_ADDRESS=roysrijan53@gmail.com
GMAIL_APP_PASSWORD=abcd efgh ijkl mnop
```

In Python you can load these with `python-dotenv` or `os.environ`.

Example `.gitignore` additions:

```
.env
__pycache__/
```

### 4. Example usage (local)

```bash
python otp_send.py
```

The script will ask for the recipient email. After sending the OTP, it will prompt you to enter the OTP for verification.

### 5. Example usage (Google Colab)

* Colab notebooks may block SMTP attempts at times. If you run into connection blocks, consider running locally or using a trusted server.
* To safely enter secrets in Colab use `getpass`:

```python
from getpass import getpass
app_password = getpass('Enter your Gmail app password: ')
```

---

## Example script (`otp_send.py`)

> This is a short, safe example. It expects `GMAIL_ADDRESS` and `GMAIL_APP_PASSWORD` to be available as environment variables or you can prompt for them at runtime.

```python
import os
import random
import math
import smtplib
from getpass import getpass

# Load credentials from environment or prompt (safer than hardcoding)
GMAIL_ADDRESS = os.environ.get('GMAIL_ADDRESS') or input('Gmail address: ')
GMAIL_APP_PASSWORD = os.environ.get('GMAIL_APP_PASSWORD') or getpass('App password: ')

# Generate 6-digit OTP
digits = '0123456789'
OTP = ''.join(digits[math.floor(random.random()*10)] for _ in range(6))
message = f"Subject: Your OTP\n\nYour OTP is {OTP}"

# Prepare SMTP session
s = smtplib.SMTP('smtp.gmail.com', 587)
s.starttls()
s.login(GMAIL_ADDRESS, GMAIL_APP_PASSWORD)

recipient = input('Enter recipient email: ')
s.sendmail(GMAIL_ADDRESS, recipient, message)
print('OTP sent!')

# Verify
entered = input('Enter OTP: ')
if entered == OTP:
    print('OTP Verified')
else:
    print('Incorrect OTP')

s.quit()
```

---

## Troubleshooting

* `NameError: name 'smtplib' is not defined` — make sure you `import smtplib`.
* `SMTPAuthenticationError` — verify your app password and that 2-Step Verification is enabled.
* Connection blocked in Colab — try running locally or check network restrictions.

---

## Improvements and next steps

* Use a proper email body template (HTML) and include `From:` and `To:` headers.
* Rate-limit OTP requests and add expiry for each OTP.
* Store OTPs securely if verifying asynchronously (e.g., in Redis with TTL).
* Add logging and error handling for production use.

---

## License

MIT

---

## Credits

Created by the repository owner — adapt and improve as needed.
