# Commands & Tools Reference

## Environment
```bash
# Kali attacker IP
hostname -I
```
```powershell
# Windows victim IP
ipconfig | findstr IPv4
```

## GoPhish (Kali)
```bash
sudo apt update && sudo apt install gophish -y
sudo gophish-start          # admin UI at https://127.0.0.1:3333 (admin / kali-gophish)
```
Sending profile: `smtp.gmail.com:587` with a Gmail app password; From formatted as
`Display Name <address>`. Landing page: cloned M365 login with Capture Submitted Data
+ Capture Passwords enabled. Campaign URL points at `http://<kali-ip>`.

## Windows Universal Forwarder note (separate Splunk lab)
n/a for this project.

## Forensic hashing
```powershell
# Windows — hash the exported .eml
certutil -hashfile "Action Required Your password expires today.eml" SHA256
```
```bash
# Kali — hash the GoPhish results CSV
sha256sum "Copy of M365 Phishing Simulation - Results.csv"
```

## Email header / DNS analysis
- MXToolbox Email Header Analyzer: paste raw headers → SPF/DKIM/DMARC + relay path
- MX lookup: `mxtoolbox.com` → MX Lookup `proton.me`
- SPF: `dig TXT gmail.com +short | grep spf`  →  v=spf1 redirect=_spf.google.com
- MX (CLI): `dig MX proton.me +short`
