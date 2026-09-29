# CUPS 2.4.16 — Local Privilege Escalation (CVE-2026-34990)

![CUPS](https://img.shields.io/badge/CUPS-2.4.16-red)
![CVE](https://img.shields.io/badge/CVE-2026--34990-orange)
![Python](https://img.shields.io/badge/Python-3.8%2B-blue)
![License](https://img.shields.io/badge/License-MIT-green)

## 📋 Summary

CUPS 2.4.16 contains a **local privilege escalation** vulnerability
(also tracked as **GHSA-c54j-2vqw-wpwp**).

### The Vulnerability

1. **World-writable socket** — `/run/cups/cups.sock` is world-writable,
   allowing any local user to send IPP requests to the CUPS daemon
   (running as **root**).

2. **Local token leak** — `CUPS-Create-Local-Printer` can be abused to
   leak the `Local` authorization token by pointing `device-uri` to an
   attacker-controlled IPP server.

3. **Arbitrary file write** — With the leaked token, an attacker can create
   a printer with `device-uri=file:///etc/sudoers.d/<user>`, causing CUPS
   (as root) to write arbitrary content to any path on disk.

## 🎯 Impact

- **Local privilege escalation to root**
- Arbitrary file write as root

## 🚀 Usage

```bash
# Basic usage
python3 cups_pwn.py --user lowpriv

# Custom sudoers path
python3 cups_pwn.py --user lowpriv --sudoers /etc/sudoers.d/custom

# After success
sudo -i

🧠 How It Works
text

┌─────────────────────────────────────────────────────────────────┐
│ 1. Start fake IPP server on 127.0.0.1:9189                       │
│ 2. Send CUPS-Create-Local-Printer with device-uri=ipp://...      │
│ 3. CUPS connects to our fake server → sends Local token          │
│ 4. Extract the token                                             │
│ 5. Create printer with device-uri=file:///etc/sudoers.d/...      │
│ 6. Print gzip payload with "user ALL=(ALL) NOPASSWD: ALL"        │
│ 7. CUPS writes the payload as root                               │
│ 8. sudo -n id → root                                             │
└─────────────────────────────────────────────────────────────────┘

🛡️ Disclaimer

This tool is provided for authorized security testing and educational
purposes only. Do not use it against systems you do not own or have
explicit permission to test. The author is not responsible for any misuse
or damage caused by this tool.
📚 References

    GHSA-c54j-2vqw-wpwp

    CVE-2026-34990

    CUPS Security Advisories

📄 License

MIT — see LICENSE file.
text


### Ficheiro 3: `LICENSE`

```bash
nano LICENSE