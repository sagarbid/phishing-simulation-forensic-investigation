# Phishing Attack Simulation & Digital Forensic Investigation

A hands-on, fully authorised phishing simulation built end-to-end in an isolated
home lab, followed by a structured digital forensic investigation of the artefacts
it produced. The exercise plays both sides — attacker and investigator.

**Analyst:** Sagar Bidari
**Lab:** VMware Workstation — Kali Linux 2026.2 (attacker) + Windows 11 (victim)
**Tooling:** GoPhish · Gmail SMTP relay · Proton Mail · MXToolbox · certutil / sha256sum

---

## Summary

Using **GoPhish** on Kali, a spoofed **Microsoft 365 "password expiry"** email was sent
to an analyst-owned Proton mailbox. The victim (Windows 11) opened it, clicked the
deceptive link, and submitted credentials into a cloned Microsoft sign-in page hosted
on the attacker's machine. Credentials were captured in plaintext — a successful
simulated compromise — and every artefact was preserved with SHA-256 hashes and analysed.

| | |
|---|---|
| **Attack outcome** | Credentials harvested |
| **Severity** | HIGH |
| **Evidence integrity** | Verified (SHA-256) |

## The headline finding

The phishing email **passed SPF, DKIM and DMARC** — because it was relayed through a
genuine Gmail account via Google's real SMTP. Authentication aligning to `gmail.com`
does **not** mean an email is safe; abusing a legitimate free webmail account is a
current "legitimate-service abuse" evasion technique. Header analysis also recovered
two attacker indicators: `X-Mailer: gophish` and a `Message-ID` ending in `@kali`.

## Attack chain (MITRE ATT&CK)

| Stage | Technique | Evidence |
|---|---|---|
| Resource Development | T1585.002 — Establish Accounts: Email | Attacker Gmail + Proton accounts |
| Reconnaissance | T1598 — Phishing for Information | Password-expiry pretext |
| Initial Access | T1566.002 — Spearphishing Link | Deceptive link to fake M365 page |
| Credential Access | T1056.003 — Web Portal Capture | Cloned login harvests credentials |

## Lab topology

```
[ Kali attacker 192.168.100.134 ]  --SMTP-->  [ Gmail relay ]  --->  [ Proton mailbox ]
        GoPhish 0.12.1                                                        |
             ^                                                                v
             |------- credentials POST -------[ Windows 11 victim 192.168.100.135 ]
                     (fake M365 page)                click link
```

## Five-stage forensic process

1. **Identification** — recognise the email, enumerate artefacts
2. **Preservation** — export `.eml`, compute SHA-256 hashes
3. **Collection** — GoPhish results, captured credentials, browser history, DNS/MX
4. **Analysis** — email headers (SPF/DKIM/DMARC), routing, X-Mailer, delivery path
5. **Correlation & Reporting** — reconcile timelines (incl. clock skew), report findings

## Evidence register

| ID | Artefact | SHA-256 |
|---|---|---|
| E1 | Phishing email (.eml) | `fc6e514b06d443da9119766c98c2f529ec6c7bbbbebe78731d9755547488684e` |
| E2 | GoPhish results CSV | `c9b1da95d7fdcb4bfa3f2721b9e638424fbf1a7aebd506500666f93a87b0862c` |
| E3 | Captured credentials | GoPhish timeline (plaintext) |
| E4 | Victim browser history | Edge history artefact |
| E5 | Email headers + DNS/MX | derived from E1 |

## Indicators of Compromise (defanged)

- Sender (abused): `cybersupport.sbid[@]gmail[.]com`
- Originating host HELO: `kali` (`187.13.204.105`)
- Phishing URL: `hxxp://192.168.100[.]134/?rid=IUH8ofC`
- Header artefacts: `X-Mailer: gophish`, `Message-ID …@kali`
- Lure subject: `Action Required: Your password expires today`

## Contents

- `Phishing_Forensic_Report.pdf` — full report with 28 evidence figures
- `Phishing_Forensic_Report.docx` — editable source
- `commands.md` — the exact lab & forensic commands used

## Ethics

Conducted entirely in a private, isolated lab on the analyst's own hardware. Every
account was analyst-owned; no third party was targeted. "Harvested" credentials were
fabricated lab values. Educational purpose only.
