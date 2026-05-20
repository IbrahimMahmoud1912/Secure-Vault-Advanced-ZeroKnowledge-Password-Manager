# 🔐 Secure Vault — Password Manager

A fully local, encrypted password manager built in Python for the CCY2001 Introduction to Cybersecurity course (Project 29 — Advanced ★★★).

Passwords are stored in an encrypted SQLite database using **AES-256-GCM**. The master password is never written to disk — only a derived cryptographic key is used. Every credential gets its own unique random salt and IV, so even identical passwords produce completely different ciphertext.

---

## Features

- **Add** — Store a new credential. Choose to generate a strong 16-character random password or type your own. Runs a HaveIBeenPwned breach check before saving.
- **Get** — Retrieve a password by site name (case-insensitive). Copies silently to clipboard and auto-clears after 30 seconds in a background thread.
- **Search** — Partial-match search across site names and usernames using SQL `LIKE`.
- **Update** — Change the username or password for any stored entry. Handles multiple accounts per site.
- **Delete** — Permanently remove a credential after confirmation.
- **List** — Show all stored sites and usernames without decrypting anything.
- **GUI** — Chrome-style dark vault list view. Click any site row to open a detail panel with Copy / Show / Edit / Delete buttons per account. Live search bar filters as you type.
- **CLI** — Full-featured command-line interface with hidden password input and a live countdown clipboard wipe.

---

## Security Design

| Property | How it is achieved |
|---|---|
| Master password never stored | Held in RAM only; never written to disk |
| Unique key per credential | Every entry has its own `os.urandom(16)` salt |
| IV never reused | Fresh `os.urandom(12)` nonce for every encryption |
| Tamper detection | AES-GCM authentication tag detects any modification |
| Brute-force resistance | PBKDF2 with 100,000 iterations of HMAC-SHA256 |
| Privacy-preserving breach check | k-anonymity: only 5 chars of SHA-1 hash sent externally |
| Clipboard protection | Auto-wiped after 30s via non-daemon background thread |
| Secure password generation | `secrets` module (CSPRNG), not `random` |

---

## Project Structure
secure-vault/
├── crypto_utils.py       # PBKDF2 key derivation + AES-256-GCM encrypt/decrypt
├── database_manager.py   # SQLite CRUD — add, get, search, update, delete, list
├── hibp_checker.py       # HaveIBeenPwned k-anonymity breach check
├── gui.py                # Graphical interface (customtkinter)
├── main.py               # Command-line interface (argparse)
├── requirements.txt      # Runtime dependencies
└── vault.db              # Created automatically on first run (gitignored)
---

## Installation

**Requirements:** Python 3.10 or higher

```bash
# 1. Clone the repository
git clone https://github.com/YOUR_USERNAME/secure-vault.git
cd secure-vault

# 2. Install dependencies
pip install -r requirements.txt
```

---

## Usage

### Graphical Interface

```bash
python gui.py
```

Enter your master password to unlock the vault. The vault list opens automatically showing all stored sites.

### Command-Line Interface

```bash
# Add a new credential
python main.py add --site github.com --user alice@mail.com

# Retrieve a password (copies to clipboard, clears in 30s)
python main.py get --site github.com

# Retrieve and print to screen instead
python main.py get --site github.com --show

# Search by partial site name or username
python main.py search --query git

# Update a credential
python main.py update --site github.com

# Delete a credential
python main.py delete --site github.com

# List all stored sites
python main.py list
```

---

## Dependencies

| Library | Version | Purpose |
|---|---|---|
| `cryptography` | ≥ 42.0.0 | PBKDF2HMAC key derivation + AESGCM authenticated encryption |
| `customtkinter` | ≥ 5.2.2 | Dark-mode GUI framework built on tkinter |
| `pyperclip` | ≥ 1.8.0 | Cross-platform clipboard copy and auto-clear |
| `requests` | ≥ 2.28.0 | HTTP requests to the HaveIBeenPwned API |

Standard library modules used: `os`, `sqlite3`, `hashlib`, `argparse`, `getpass`, `threading`, `time`, `secrets`, `string`.

---

## How It Works

### Saving a password
1. User enters site, username, and password (or generates one)
2. `os.urandom(16)` generates a unique random salt
3. PBKDF2-HMAC-SHA256 runs 100,000 iterations to derive a 256-bit AES key
4. `os.urandom(12)` generates a unique random IV
5. AES-256-GCM encrypts the password → produces ciphertext + auth tag
6. Five values are stored in SQLite: `site`, `username`, `encrypted_password`, `iv`, `salt`

### Retrieving a password
1. SQLite returns the stored row for that site
2. PBKDF2 re-derives the same AES key using the stored salt
3. AES-GCM decrypts — if the master password is wrong, an `InvalidTag` exception is raised immediately
4. Password is copied to clipboard; a non-daemon background thread clears it after 30 seconds

### Breach check (k-anonymity)
1. SHA-1 hash of the password is computed locally
2. Only the first 5 characters are sent to `api.pwnedpasswords.com`
3. The API returns all matching hash suffixes
4. The comparison happens locally — the full password never leaves your machine

---

## Notes

- `vault.db` is created automatically in the project folder on first run
- Never share or commit `vault.db` — it contains your encrypted credentials
- The master password is never stored anywhere; if you forget it, the vault cannot be decrypted
- Add `vault.db` to your `.gitignore`

---

## .gitignore
vault.dbpycache/*.pyc.env
---

## Course

CCY2001 — Introduction to Cybersecurity | Group Project Compendium | Project 29 — Advanced ★★★
