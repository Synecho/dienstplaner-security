# Security Policy

## Vulnerability Reports

If you discover a security vulnerability in Dienstplaner, please open an issue at
https://github.com/Synecho/dienstplaner-security/issues

Omit sensitive technical details in the public issue — we will arrange a private channel to handle the disclosure.

## Response Timeline

- **Acknowledge:** Within 7 days
- **Triage:** Within 14 days — severity assessment communicated
- **Patch:** Timeline communicated after triage, depending on severity
- **Disclosure:** Coordinated — fix ships before details are made public

## What to Expect

- You will receive an acknowledgment after triage
- We will credit you in the release notes unless you prefer anonymity
- We will not take legal action against good-faith security research

## Out of Scope

The following are not considered vulnerabilities for this project:

- Social engineering attacks against users or administrators
- Denial of service against individual user devices
- Vulnerabilities in third-party dependencies already publicly disclosed
- Physical access attacks (device already unlocked/compromised)
- Attacks requiring modification of the app binary

## Architecture

- **No central server.** All schedule data is stored on user devices only (P2P).
- **Sync encryption:** XSalsa20-Poly1305 (tweetnacl secretbox), group-scoped 256-bit keys.
- **Identity:** Ed25519 key pairs per device. Private keys never leave the device.
- **Firebase:** Used only for WebRTC signaling and temporary invite codes. No schedule data or personal information is stored on Firebase.
- **Keys:** Stored in iOS Keychain / Android Keystore.
