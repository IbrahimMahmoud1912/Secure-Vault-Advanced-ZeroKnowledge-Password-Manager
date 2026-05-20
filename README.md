# 🛡️ Secure Vault: Advanced Zero-Knowledge Password Manager

**Secure Vault** is a military-grade, zero-knowledge password manager developed in Python. Designed with core cybersecurity principles, it ensures that your master password is never stored and your data remains completely private.

It utilizes **AES-256-GCM** for authenticated data encryption and **PBKDF2HMAC** (with 100,000 iterations) for secure key derivation, making it highly resistant to offline brute-force attacks. Additionally, the vault integrates the HaveIBeenPwned API using **K-Anonymity** to actively check for compromised credentials without ever transmitting your actual password over the internet.

---

## 🚀 Key Features

* **Zero-Knowledge Architecture:** Your master password is never saved to the disk.
* **Military-Grade Encryption:** Data is secured using `AES-256-GCM` ensuring both confidentiality and integrity.
* **Brute-Force Protection:** Uses `PBKDF2` with 100,000 iterations and a unique 16-byte Salt.
* **Leak Detection:** Integrates with HIBP API using K-Anonymity (sends only the first 5 characters of the SHA-1 hash).
* **Memory Protection:** Automatically clears the system clipboard 15 seconds after copying a password.
* **Strong Password Generator:** Built-in cryptographically secure password generator using Python's `secrets` module.

---

## 🛠️ Technology Stack

* **Language:** Python 3.x
* **Database:** SQLite3 (Serverless local storage)
* **GUI Framework:** CustomTkinter
* **Cryptography:** `cryptography` library
* **API Integration:** `requests` & `hashlib`

---

## ⚙️ How to Run

1. **Install required dependencies:**
   ```bash
   pip install customtkinter cryptography requests pyperclip
