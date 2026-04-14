# Datenschutzerklärung — Dienstplaner

Stand: April 2026

Diese Datenschutzerklärung beschreibt, welche personenbezogenen Daten die App Dienstplaner verarbeitet, wo sie gespeichert werden und an wen sie weitergegeben werden.

---

## Verantwortlicher

Die Person oder Organisation, die Dienstplaner als Planer einsetzt (Admin-Rolle), ist datenschutzrechtlich Verantwortliche im Sinne von Art. 4 Nr. 7 DSGVO. Dienstplaner ist eine P2P-Software ohne zentralen Betreiber — verantwortlich ist stets derjenige, der die Gruppe anlegt und Mitglieder einlädt.

---

## Verarbeitete Personendaten

| Datenkategorie | Beschreibung |
|---|---|
| Anzeigename | Frei wählbarer Name, der anderen Gruppenmitgliedern angezeigt wird |
| Dienstpläne & Schichten | Schichtzeiten, Schichttypen und Zuweisungen pro Mitarbeiter |
| Verfügbarkeiten & Wünsche | Eingetragene Verfügbarkeiten und Schichtwünsche |
| Qualifikationen & Vertragsart | Optionale Angaben zu Wochenstunden, Vertragstyp und freigegebenen Schichttypen |
| Push-Token | Gerätespezifischer Token für Push-Benachrichtigungen (iOS/APNs) |
| Geräteschlüssel | Ed25519-Schlüsselpaar zur Geräteidentifikation — privater Schlüssel verlässt das Gerät nie |

Es werden keine Standortdaten, keine Gesundheitsdaten und keine biometrischen Daten verarbeitet.

---

## Lokale Speicherung

Alle Daten verbleiben auf dem Gerät. Es gibt keinen zentralen Datenbankserver.

| Speicherort | Inhalt |
|---|---|
| SQLite (y-expo-sqlite) | Dienstplandaten als Yjs-CRDT-Dokument, iOS-Sandbox-geschützt |
| MMKV (verschlüsselt) | Nutzer-ID, Anzeigename, Einstellungen, Sicherheitsprotokoll |
| iOS Keychain | Symmetrische Gruppenschlüssel und Geräteschlüsselpaar (AES-256) |
| App-Dokumentenverzeichnis | Backup-Datei (verschlüsselte Yjs-Snapshots) |
| MMKV-Ringpuffer | Sicherheitsprotokoll: max. 500 Einträge, 90 Tage, nur Ereignistyp + Zeitstempel |

---

## Datenweitergabe

### Gruppenmitglieder (P2P-Sync)

Dienstplandaten, Schichtzuweisungen und Mitgliederprofile (Name, Push-Token, Qualifikationen) werden verschlüsselt an andere Mitglieder derselben Gruppe übertragen.

- **Verschlüsselung:** XSalsa20-Poly1305 (tweetnacl secretbox), gruppenspezifischer 256-Bit-Schlüssel — Ende-zu-Ende
- **Übertragungsweg:** WebRTC DataChannel, direkte Peer-to-Peer-Verbindung
- **Wann:** Nur wenn beide Geräte gleichzeitig online und im Vordergrund sind

### Firebase Realtime Database (Google)

**Betreiber:** Google Ireland Limited, Gordon House, Barrow Street, Dublin 4, Irland

Firebase wird ausschließlich für zwei technisch notwendige Zwecke genutzt:

1. **WebRTC-Signaling:** Austausch von SDP-Offer/Answer-Nachrichten für den Verbindungsaufbau zwischen Geräten. Diese Daten sind temporär (Sekunden bis Minuten) und enthalten keine Inhaltsdaten.
2. **Einladungs-Codes:** Temporäre Datensätze mit Gruppen-ID, Schlüssel-Hash, Token und Ablaufzeit. Kein Name, keine Schichtdaten. Maximale Gültigkeitsdauer: 24 Stunden.

Keine Dienstplandaten, keine Namen und keine persönlichen Inhalte werden auf Firebase gespeichert.

**Rechtsgrundlage:** Art. 6 Abs. 1 lit. b DSGVO (technisch notwendig für P2P-Verbindungsaufbau)

### STUN-Server (Google)

Für den WebRTC-Verbindungsaufbau wird `stun.l.google.com:19302` (Google LLC) kontaktiert. Dabei wird ausschließlich die öffentliche IP-Adresse des Geräts für die NAT-Traversal-Aushandlung übermittelt — keine Nutzerinhalte.

### Push-Relay (optional)

Für Push-Benachrichtigungen werden APNs-Push-Token, Benachrichtigungstext und Gruppen-ID an einen Push-Relay-Server übermittelt. Dieser Dienst ist standardmäßig inaktiv und wird nur aktiv, wenn ein Administrator ihn konfiguriert. Die eigentliche Zustellung erfolgt über Apple Push Notification Service (APNs).

**Rechtsgrundlage:** Art. 6 Abs. 1 lit. a DSGVO (Einwilligung durch Aktivierung auf Betriebssystemebene)

### Nextcloud (optional)

Wenn der Nutzer in den Einstellungen einen eigenen Nextcloud-Server konfiguriert, wird eine verschlüsselte Backup-Datei dorthin hochgeladen. Betreiber ist ausschließlich der Nutzer selbst. Dieser Dienst ist standardmäßig inaktiv.

---

## Datenlöschung

| Aktion | Effekt |
|---|---|
| App deinstallieren | Entfernt alle lokalen Daten (SQLite, MMKV, Keychain) vollständig |
| Gruppe verlassen | Entfernt Rollenzugehörigkeit; Admin kann Profil manuell löschen |
| Einladungs-Codes | Automatisch nach 24 Stunden aus Firebase gelöscht |
| P2P-Daten bei anderen Geräten | Yjs ist ein CRDT — einmal synchronisierte Daten können nicht rückwirkend bei anderen Geräten gelöscht werden (technische Eigenschaft) |

---

## Rechtsgrundlagen

| Verarbeitungszweck | Rechtsgrundlage |
|---|---|
| P2P-Sync & Kernfunktion | Art. 6 Abs. 1 lit. b DSGVO — Vertragserfüllung |
| Sicherheitsprotokoll | Art. 6 Abs. 1 lit. c DSGVO — rechtliche Verpflichtung |
| Push-Benachrichtigungen | Art. 6 Abs. 1 lit. a DSGVO — Einwilligung |

---

## Betroffenenrechte

Du hast folgende Rechte gegenüber dem Verantwortlichen (Administrator deiner Gruppe):

- **Auskunft** (Art. 15 DSGVO)
- **Berichtigung** (Art. 16 DSGVO) — Anzeigename direkt in der App änderbar
- **Löschung** (Art. 17 DSGVO) — Gruppe verlassen + App deinstallieren
- **Widerspruch** (Art. 21 DSGVO) — Push-Benachrichtigungen in iOS-Einstellungen deaktivierbar

Für Anfragen wende dich an den Administrator deiner Gruppe.
