# Security Policy

## Supported Versions

| Version | Supported | Security Updates Until |
|---------|-----------|----------------------|
| 1.x     | Yes       | TBD_AT_RELEASE + 5 years |

The support commitment is 5 years from v1.0 release date, per EU Cyber Resilience Act (2024/2847) requirements.

## Reporting a Vulnerability

We take security seriously. If you discover a vulnerability in Dienstplaner, please report it responsibly.

### How to Report

**Preferred:** Use [GitHub Security Advisories](https://github.com/Synecho/dienstplaner-security/security/advisories/new) for private vulnerability reporting. This allows us to work on a fix before public disclosure. You do not need access to the main codebase — reports submitted here are reviewed by the maintainers.

**Alternative:** Email security@synecho.de with a description of the vulnerability, steps to reproduce, and potential impact.

### Response Timeline

- **Acknowledge:** Within 72h of report
- **Triage:** Within 7 days — severity assessment and fix timeline communicated
- **Patch (critical):** Within 90 days
- **Patch (high):** Within 120 days
- **Disclosure:** Coordinated — fix ships first, CVE published after patch is available in App Store

### What to Expect

1. You will receive an acknowledgment within 72h
2. We will keep you informed of progress
3. We will credit you in the advisory (unless you prefer anonymity)
4. We will not take legal action against good-faith security research

## Disclosure Policy

We follow **coordinated disclosure**:
- Reported vulnerabilities are fixed before public disclosure
- CVE identifiers are requested after the patch is available
- Reporters are credited unless they request anonymity

## Out of Scope

The following are NOT considered vulnerabilities for this project:

- Social engineering attacks against users or maintainers
- Denial of service attacks against user devices (mobile app, local data)
- Vulnerabilities in third-party dependencies that are already publicly disclosed (check npm audit)
- Physical access attacks (device already compromised)
- Attacks requiring modification of the app binary (sideloading)

## Architecture Security Summary

### Data Storage
- **No central server.** All schedule data is stored exclusively on user devices (P2P architecture).
- **Encryption:** All sync data is encrypted with XSalsa20-Poly1305 (via tweetnacl secretbox). Group symmetric keys are distributed via the invite flow and stored in iOS Keychain / Android Keystore via react-native-keychain.
- **Identity:** Ed25519 key pairs for device identity (tweetnacl sign).

### SBOM (Software Bill of Materials)
- CycloneDX JSON format, generated for every release
- Scope: npm production dependencies only
- Native dependencies (CocoaPods, Gradle) are excluded — these are Apple/Google SDK supply chains managed through Expo, not Dienstplaner product components. Their security is managed through Expo SDK updates.
- Available in the release artifacts as `sbom.json`

### Firebase Realtime Database
- Used ONLY for WebRTC signaling and invite phrase exchange
- No schedule data or user PII is stored on Firebase
- Security rules:
  - Root level: `.read: false`, `.write: false` (deny all by default)
  - `invitePhrases/$code`: `.read: true`, `.write: true` with validation (per-code access only, no enumeration)
  - Invite phrases expire after 24 hours and are single-use
  - Write validation enforces required fields: `groupId`, `keyHex`, `token`, `expiry`

### Audit Log
- Security events are logged to an on-device MMKV ring buffer (500 entries, 90-day retention)
- Events: invite lifecycle, key generation, group membership changes, backup operations
- Visible to planner role in Settings > Sicherheitsprotokoll
- No audit data leaves the device

## Planned Security Measures

- [ ] Penetration test before v1.0 public launch
- [ ] CE marking / Konformitaetserklaerung (post-v1.0, requires third-party auditor)
- [ ] ENISA Single Reporting Platform registration (activated upon discovery of actively exploited vulnerability)

## Dependencies

This project uses `npm audit --audit-level=high` in CI to block high and critical CVEs in production dependencies. Moderate and low severity issues are reported but non-blocking.
