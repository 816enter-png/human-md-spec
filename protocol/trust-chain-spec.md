# Trust Chain Authentication Specification

**Version:** 1.0
**Date:** 2026-03-31
**Status:** Draft
**Author:** 816 Enterprises LLC

---

## Table of Contents

1. [Overview](#1-overview)
2. [Trust Chain Definition](#2-trust-chain-definition)
3. [Link 1: Human to Application (Biometric Binding)](#3-link-1-human-to-application)
4. [Link 2: Application to Signed File (Cryptographic Integrity)](#4-link-2-application-to-signed-file)
5. [Link 3: Signed File to Physical Module (Hardware Binding)](#5-link-3-signed-file-to-physical-module)
6. [Link 4: Physical Module to Agent (Hardware Attestation)](#6-link-4-physical-module-to-agent)
7. [Link 5: Agent Verification and Enforcement](#7-link-5-agent-verification-and-enforcement)
8. [Ed25519 Key Management](#8-ed25519-key-management)
9. [Signature Computation Algorithm](#9-signature-computation-algorithm)
10. [Hardware Attestation Protocol](#10-hardware-attestation-protocol)
11. [Key Revocation](#11-key-revocation)
12. [Threat Model](#12-threat-model)
13. [Error Handling](#13-error-handling)

---

## 1. Overview

The human.md trust chain establishes cryptographic proof that an identity file was authored by a specific human, signed by a specific physical device, and has not been tampered with. The chain consists of five links, each providing a distinct security property that cannot be achieved by software alone.

### 1.1 Conformance Language

The keywords MUST, MUST NOT, SHALL, SHALL NOT, SHOULD, SHOULD NOT, MAY are interpreted per RFC 2119.

### 1.2 Design Principles

- **Defense in depth:** Each link provides independent security value. Compromise of one link does not automatically compromise the others.
- **Hardware root of trust:** The ATECC608B secure element is the trust anchor. The private key never leaves the hardware.
- **Decentralized verification:** Any agent can verify the chain without contacting a central authority.
- **Fail-closed:** If any link cannot be verified, the entire chain is treated as broken.

---

## 2. Trust Chain Definition

```
Link 1          Link 2            Link 3              Link 4           Link 5
Human ────────► App ────────────► Signed File ───────► Module ────────► Agent
(fingerprint)   (authenticated    (Ed25519 over       (hardware        (verification
                 session)          file content)        attestation)     + enforcement)
```

### 2.1 Security Properties by Link

| Link | Property | Guarantees | Failure Mode |
|------|----------|------------|--------------|
| 1 | Biometric binding | File was authorized by the enrolled human | Unauthorized user cannot create or modify files |
| 2 | Authenticated transfer | File transferred over encrypted channel during valid auth session | Interception or injection during transfer is prevented |
| 3 | Cryptographic integrity | File has not been modified since signing | Any bit change invalidates the signature |
| 4 | Hardware binding | Signature was produced by a genuine physical module | Software emulators cannot produce valid attestations |
| 5 | Verified enforcement | Agent has confirmed the entire chain before applying the file | Agents do not act on unverified identity claims |

---

## 3. Link 1: Human to Application

### 3.1 Biometric Authentication Flow

```
State Machine: Fingerprint Authentication

    ┌──────────┐
    │  LOCKED  │◄────────────────────────────────┐
    └────┬─────┘                                  │
         │ finger detected                        │ skin contact lost > 30s
         ▼                                        │   OR 5 consecutive failures
    ┌──────────┐                                  │
    │ SCANNING │                                  │
    └────┬─────┘                                  │
         │ image captured                         │
         ▼                                        │
    ┌──────────────┐   match failed               │
    │  MATCHING    │──────────────────────────────►│
    └────┬─────────┘                              │
         │ match succeeded                        │
         ▼                                        │
    ┌──────────────┐                              │
    │AUTHENTICATED │──────────────────────────────┘
    └──────────────┘
```

### 3.2 Fingerprint Matching Parameters

| Parameter | Value | Description |
|-----------|-------|-------------|
| Sensor type | Capacitive | Area or swipe sensor with liveness detection |
| Template storage | ATECC608B secure element | Never in main flash or RAM |
| Match threshold | FAR < 1/50,000 | False acceptance rate |
| Liveness check | Capacitance pattern analysis | Rejects optical replay, printed fingerprints, conductive films |
| Max enrolled templates | 3 | Maximum 3 fingers per module |
| Failure lockout | 5 consecutive failures | 30-second lockout, doubling per subsequent lockout cycle (max 15 min) |

### 3.3 Skin Contact Monitoring

The capacitive skin contact sensor on the body-facing surface of the BIM continuously monitors for human contact.

- **Sample rate:** 4 Hz (every 250 ms)
- **Loss threshold:** 30 consecutive non-contact readings (7.5 seconds at 4 Hz) triggers a warning; 120 consecutive non-contact readings (30 seconds) triggers re-lock.
- **Re-lock behavior:** Module immediately ceases all BLE advertising, NFC responses, and Wi-Fi Direct connections. Returns to LOCKED state. Requires full fingerprint re-authentication.

### 3.4 Security Properties

- The human's biometric data never leaves the module.
- Template matching occurs entirely within the secure element.
- No biometric data is transmitted over BLE, NFC, or Wi-Fi.
- The companion app never receives fingerprint data.

---

## 4. Link 2: Application to Signed File

### 4.1 Authenticated Transfer Protocol

When the companion app transfers a new or updated HIF to the BIM:

1. **BLE Secure Connection:** App connects to BIM using LE Secure Connections (LESC) with ECDH P-256 key exchange and AES-CCM encryption.
2. **Mutual authentication:** App verifies BIM identity via its public key. BIM verifies app via a pairing token established during initial setup.
3. **Biometric gate:** BIM requires fingerprint authentication within the current session (authenticated state) before accepting file writes.
4. **Transfer:** App sends the HIF content over the encrypted BLE connection.
5. **Signing:** BIM receives the file, stores it in encrypted flash, and computes the Ed25519 signature using the secure element (see Section 9).
6. **Confirmation:** BIM sends the computed signature back to the app for display/logging.
7. **App deletion:** App deletes the local copy of the HIF. The only persistent copy exists on the BIM.

### 4.2 Transfer Integrity

The BLE link layer provides CRC-based error detection. Additionally, the app computes SHA-256 of the file content before transfer and sends the hash as a separate BLE write. The BIM verifies the hash after receiving the complete file. If the hashes do not match, the BIM rejects the transfer and sends an error code.

### 4.3 Session Binding

The transfer is bound to the current authenticated session. If the BIM re-locks (skin contact lost, timeout) during transfer, the partial transfer is discarded and the process must restart from step 1.

---

## 5. Link 3: Signed File to Physical Module

### 5.1 Signature Binding

The Ed25519 signature cryptographically binds the file content to the specific private key inside the ATECC608B. Because the private key cannot be extracted from the secure element, a valid signature proves:

1. The file was signed by this specific physical module (identified by `module_id`).
2. The file has not been modified since signing.
3. No other device can produce a valid signature for this module_id.

### 5.2 Key Properties

| Property | Value |
|----------|-------|
| Algorithm | Ed25519 (RFC 8032) with pre-hashing (Ed25519ph, using SHA-512) |
| Key size | 256 bits (32 bytes) |
| Signature size | 512 bits (64 bytes) |
| Key generation | On-device, inside ATECC608B, during initial provisioning |
| Key extraction | Impossible by design (ATECC608B hardware protection) |
| Key backup | Not supported. Loss of module = loss of key. New module requires new key pair. |

### 5.3 Module Identity

The `module_id` in the HIF front matter is a UUID v4 derived from the ATECC608B's unique serial number:

```
module_id = UUID_v4_from_bytes(SHA-256(ATECC608B_serial_number)[0:16])
```

The UUID is deterministic for a given secure element but does not reveal the serial number.

---

## 6. Link 4: Physical Module to Agent

### 6.1 Hardware Attestation

Hardware attestation proves to a receiving agent that the BIM is a genuine physical device with a real ATECC608B secure element, not a software emulator.

### 6.2 Attestation Certificate Chain

```
Root CA (816 Enterprises LLC)
    │
    ├── Intermediate CA (Manufacturing Facility)
    │       │
    │       └── Device Certificate (individual BIM)
    │               Subject: module_id
    │               Public Key: Ed25519 public key
    │               Signed by: Manufacturing Facility intermediate key
```

### 6.3 Attestation Certificate Format

The device attestation certificate is a compact binary structure (not X.509, to fit in BLE characteristic size constraints):

```
Offset  Size    Field
0x00    1       Version (0x01)
0x01    16      Module ID (UUID bytes, big-endian)
0x11    32      Device Ed25519 Public Key
0x31    8       Manufacturing Timestamp (Unix epoch, seconds, big-endian uint64)
0x39    2       Hardware Revision (major.minor as two uint8)
0x3B    2       Firmware Version at Provisioning (major.minor as two uint8)
0x3D    32      Intermediate CA Public Key
0x5D    64      Signature over bytes 0x00-0x5C by Intermediate CA key
0x9D    32      Root CA Public Key
0xBD    64      Signature over Intermediate CA Public Key by Root CA key
────────────────
Total: 253 bytes (0xFD)
```

### 6.4 Attestation Verification Procedure

An agent verifies hardware attestation:

1. Read the Attestation characteristic from the BIM's GATT service.
2. Extract Root CA Public Key (bytes 0x9D-0xBC).
3. Verify Root CA signature (bytes 0xBD-0xFC) over the Intermediate CA Public Key (bytes 0x3D-0x5C).
4. Extract Intermediate CA Public Key (bytes 0x3D-0x5C).
5. Verify Intermediate signature (bytes 0x5D-0x9C) over bytes 0x00-0x5C.
6. Extract Device Public Key (bytes 0x11-0x30).
7. Confirm Device Public Key matches the `public_key` in the HIF front matter.
8. Confirm Module ID (bytes 0x01-0x10) matches the `module_id` in the HIF front matter.
9. If all checks pass, the module is attested as genuine hardware.

### 6.5 Root CA Key Distribution

The Root CA public key is embedded in:
- The agent's firmware/software (for known agents)
- A public key registry at a well-known URL: `https://humanmd.dev/.well-known/root-keys.json`
- The attestation certificate itself (for agents that accept the TOFU model -- Trust On First Use)

Agents SHOULD pin the Root CA key after first successful verification.

---

## 7. Link 5: Agent Verification and Enforcement

### 7.1 Agent Verification Procedure

Upon receiving a HIF (via BLE, NFC, or Wi-Fi Direct), the agent MUST perform verification in this order:

```
Step 1: Parse HIF structure (validate per hif-spec.md Section 10)
    │ FAIL → discard, use default interaction mode
    ▼
Step 2: Verify Ed25519 signature (see Section 9)
    │ FAIL → discard, log failure
    ▼
Step 3: Verify hardware attestation (optional but RECOMMENDED)
    │ FAIL → continue with WARNING flag, reduced trust level
    ▼
Step 4: Check revocation status (see Section 11)
    │ REVOKED → discard, log revocation
    ▼
Step 5: Apply broadcast_scope filtering
    │ Process only public sections (and approved request-only sections)
    ▼
Step 6: Parse and apply consent tiers
    │ Enforce autonomous/confirm/prohibited
    ▼
Step 7: Apply active profile overrides
    │ Merge profile per hif-spec.md Section 7
    ▼
Step 8: Configure agent behavior per HIF content
```

### 7.2 Trust Levels

Based on how many links are verified, agents assign a trust level:

| Level | Links Verified | Agent Behavior |
|-------|---------------|----------------|
| Full | All 5 links | Full HIF enforcement including confirm-tier actions |
| Standard | Links 1-3, 5 (no attestation) | Full HIF enforcement with warning that hardware is unattested |
| Minimal | Link 3 + 5 only (signature valid, no attestation, no biometric confirmation) | Apply public sections only, all confirm-tier actions treated as prohibited |
| None | Verification failed | Discard HIF, default interaction mode |

### 7.3 Agent Compliance

Agents that claim human.md compatibility MUST:

1. Implement the full verification procedure (Steps 1-8).
2. Enforce all three consent tiers.
3. Respect broadcast_scope restrictions.
4. Purge all HIF data when the session ends (module leaves proximity).
5. Not retain, cache, or transmit HIF data beyond the active session unless explicitly permitted in the `autonomous` consent tier.

---

## 8. Ed25519 Key Management

### 8.1 Key Generation

Key generation occurs once during initial BIM provisioning (first-time setup):

1. The ATECC608B generates an Ed25519 key pair internally using its hardware random number generator (NIST SP 800-90A compliant DRBG).
2. The private key is stored in a locked key slot on the ATECC608B. The slot is configured as "never read" -- no command sequence can extract the private key.
3. The public key is exported and stored in the BIM's flash memory and included in the HIF front matter.
4. The public key is also embedded in the device attestation certificate during manufacturing.

### 8.2 Key Slot Configuration

```
ATECC608B Key Slot Layout:
Slot 0: Device Ed25519 Private Key (locked, sign-only, no read)
Slot 1: Device Ed25519 Public Key (read-only)
Slot 2: Fingerprint Template Encryption Key (locked, no read, no sign)
Slot 3: Flash Encryption Key (locked, no read, no sign)
Slot 4: Attestation Certificate (read-only data slot)
Slots 5-7: Reserved for future use
```

### 8.3 Key Lifecycle

| Event | Action |
|-------|--------|
| Provisioning | Key pair generated, public key exported, attestation cert written |
| Normal operation | Private key used for signing via ATECC608B sign command |
| Module loss/theft | Owner issues revocation (see Section 11) |
| Module replacement | New module generates new key pair. Old key is revoked. |
| Key compromise (theoretical) | Requires physical extraction from ATECC608B, which destroys the device |

### 8.4 No Key Migration

Ed25519 private keys CANNOT be migrated between modules. If a human replaces their BIM, the new module generates a new key pair. The old public key is revoked. Agents that previously interacted with the old module will see a different public key and should treat it as a new identity binding.

The human's identity continuity is maintained through the companion app, which transfers the HIF content to the new module. The new module signs it with the new key. The `module_id` changes.

---

## 9. Signature Computation Algorithm

### 9.1 Input Preparation

```
function prepare_signing_input(file_bytes):
    1. Decode file_bytes as UTF-8 string
    2. Normalize line endings: replace all CR+LF with LF
    3. Locate the signature field in YAML front matter
    4. Replace signature value with empty string:
       signature: "<any value>" → signature: ""
    5. Strip trailing whitespace from each line
    6. Ensure exactly one trailing LF at end of file
    7. Re-encode as UTF-8 bytes
    8. Return bytes
```

### 9.2 Signing Procedure (on BIM)

```
function sign_file(file_bytes, atecc608b):
    signing_input = prepare_signing_input(file_bytes)
    hash = SHA-512(signing_input)                        // 64 bytes
    signature = atecc608b.ed25519_sign(slot=0, hash)     // 64 bytes
    encoded = base64url_encode(signature)                // 88 characters
    return encoded
```

### 9.3 Verification Procedure (on Agent)

```
function verify_file(file_bytes, public_key_b64):
    public_key = base64url_decode(public_key_b64)        // 32 bytes
    signature_b64 = extract_signature_field(file_bytes)
    signature = base64url_decode(signature_b64)           // 64 bytes
    signing_input = prepare_signing_input(file_bytes)
    hash = SHA-512(signing_input)                        // 64 bytes
    valid = ed25519_verify(public_key, hash, signature)
    return valid                                         // boolean
```

### 9.4 Base64url Encoding

Per RFC 4648 Section 5:
- Alphabet: `A-Z`, `a-z`, `0-9`, `-`, `_`
- No line breaks
- Padding character `=` is included

---

## 10. Hardware Attestation Protocol

### 10.1 Attestation Request Flow

```
Agent                                BIM
  │                                   │
  │  Connect via BLE                  │
  │──────────────────────────────────►│
  │                                   │
  │  Read Attestation Characteristic  │
  │──────────────────────────────────►│
  │                                   │
  │  253-byte attestation certificate │
  │◄──────────────────────────────────│
  │                                   │
  │  Read Public Key Characteristic   │
  │──────────────────────────────────►│
  │                                   │
  │  32-byte Ed25519 public key       │
  │◄──────────────────────────────────│
  │                                   │
  │  [Local verification]             │
  │  1. Verify root CA sig            │
  │  2. Verify intermediate sig       │
  │  3. Match public key              │
  │  4. Match module_id               │
  │                                   │
```

### 10.2 Attestation is Stateless

The attestation certificate is a static blob written during manufacturing. The BIM does not perform any computation during attestation -- it simply serves the certificate bytes. All verification is performed by the agent.

### 10.3 Offline Verification

Attestation verification requires no network access. The agent needs only the Root CA public key (which can be embedded in the agent's firmware). This ensures the system works in air-gapped, offline, and low-connectivity environments.

---

## 11. Key Revocation

### 11.1 Revocation Model

Revocation uses a decentralized append-only revocation list. There is no central authority that can revoke keys unilaterally.

### 11.2 Revocation Record Format

```
Offset  Size    Field
0x00    1       Record Version (0x01)
0x01    32      Public Key being revoked
0x21    8       Revocation Timestamp (Unix epoch, seconds, big-endian uint64)
0x29    1       Reason Code
0x2A    64      Signature over bytes 0x00-0x29 by the key being revoked
────────────────
Total: 106 bytes (0x6A)
```

### 11.3 Reason Codes

| Code | Meaning |
|------|---------|
| 0x00 | Unspecified |
| 0x01 | Module lost |
| 0x02 | Module stolen |
| 0x03 | Module replaced (voluntary upgrade) |
| 0x04 | Key compromise suspected |
| 0xFF | Test/debug revocation (non-production only) |

### 11.4 Self-Signed Revocation

The revocation record is signed by the key being revoked. This ensures:
- Only the holder of the private key (i.e., the physical module) can revoke the key.
- No third party can revoke someone else's key.
- The revocation is cryptographically verifiable.

**Revocation procedure:**
1. Human authenticates to the BIM (fingerprint).
2. Human initiates revocation via companion app.
3. BIM's secure element signs the revocation record using the private key.
4. Revocation record is published to the revocation list.
5. BIM wipes its key slot and enters a permanently locked state.

### 11.5 Revocation List Distribution

Revocation records are distributed via:
- **Primary:** Append-only log at `https://humanmd.dev/.well-known/revocations.json`
- **Secondary:** BLE broadcast -- a revoking module can broadcast its revocation record as a final BLE advertisement before wiping.
- **Tertiary:** Peer-to-peer gossip -- agents that learn of a revocation propagate it to other agents they communicate with.

### 11.6 Revocation Checking

Agents SHOULD check the revocation list:
- On first connection to a new module_id.
- Periodically (at least once per 24 hours) for modules in their known-modules cache.
- If network access is unavailable, the agent MAY proceed with a warning flag but MUST check at the next opportunity.

---

## 12. Threat Model

### 12.1 Threats and Mitigations by Link

| Threat | Target Link | Mitigation | Residual Risk |
|--------|------------|------------|---------------|
| Stolen module, unauthorized use | Link 1 | Fingerprint required for every session. Skin contact monitoring. Lockout after 5 failures. | Sophisticated biometric spoofing (very high effort) |
| Man-in-the-middle during file transfer | Link 2 | BLE Secure Connections with ECDH. Mutual authentication. | BLE implementation bugs (mitigated by firmware updates) |
| File tampering after signing | Link 3 | Ed25519 signature over full file content. Single bit change invalidates. | None (computationally infeasible to forge Ed25519) |
| Software emulator impersonating a BIM | Link 4 | Hardware attestation certificate signed by manufacturing CA. | Compromised manufacturing CA (mitigated by key ceremony procedures) |
| Agent ignoring consent tiers | Link 5 | Compliance requirement in spec. Consent version monitoring. | Malicious agents (out of scope -- requires ecosystem enforcement) |
| Replay attack (old file broadcast) | Links 3+5 | `version` counter and `updated` timestamp. Agents compare against last known version. | First-contact replay (mitigated by attestation freshness) |
| Key extraction from secure element | Link 3 | ATECC608B hardware protection. Invasive attacks destroy the key. | Nation-state level physical attack (extremely high cost) |
| Lost module, identity theft | Links 1+3 | Revocation mechanism. New module = new keys. | Window between loss and revocation |
| Denial of service (jamming BLE) | Transport | Out of scope for this specification. BIM has no defense against RF jamming. | Physical proximity required |

### 12.2 Out of Scope Threats

The following threats are acknowledged but not addressed by the trust chain:

- **Malicious agents:** An agent that receives a valid HIF and intentionally ignores consent tiers. This requires ecosystem-level enforcement (certification programs, auditing).
- **Physical coercion:** A human forced to authenticate under duress. This is a physical security problem, not a cryptographic one.
- **Side-channel attacks on ATECC608B:** Theoretical timing or power analysis attacks against the secure element. Mitigated by the ATECC608B's built-in countermeasures.

---

## 13. Error Handling

### 13.1 Link 1 Failures (Biometric)

| Error | BIM Response |
|-------|-------------|
| Fingerprint no match | Remain in LOCKED state. Increment failure counter. LED flash: 2x red. |
| Liveness check failed | Remain in LOCKED state. Increment failure counter. LED flash: 3x red. |
| Lockout triggered (5 failures) | Remain in LOCKED state. 30s lockout. LED: solid red for lockout duration. |
| Skin contact lost | Start 30s countdown. LED: slow amber pulse. If contact not restored, re-lock. |
| Sensor hardware failure | Enter error state. LED: alternating red/amber. Require factory reset. |

### 13.2 Link 2 Failures (Transfer)

| Error | Response |
|-------|----------|
| BLE pairing failed | App retries up to 3 times. Then displays error to user. |
| Hash mismatch after transfer | BIM discards file. Sends error code 0x01 to app. App retries. |
| BIM re-locked during transfer | Transfer aborted. Partial data discarded. App prompts re-authentication. |
| File exceeds 64 KB | App rejects before transfer. Prompts user to reduce file size. |

### 13.3 Link 3 Failures (Signature)

| Error | Agent Response |
|-------|---------------|
| Signature verification failed | Discard entire file. Log: module_id, timestamp, hash of received file. Do not retry. |
| Public key format invalid | Discard. Log error. |
| Signing input canonicalization error | Discard. Log error with file hash for diagnostics. |

### 13.4 Link 4 Failures (Attestation)

| Error | Agent Response |
|-------|---------------|
| Root CA key unknown | Cannot verify. Proceed at Minimal trust level or reject. |
| Root CA signature invalid | Reject attestation. Proceed at Minimal trust level. Log warning. |
| Intermediate signature invalid | Reject attestation. Proceed at Minimal trust level. Log warning. |
| Public key mismatch (cert vs. HIF) | Reject entirely. This indicates a forged or mismatched certificate. Log critical error. |
| Module ID mismatch (cert vs. HIF) | Reject entirely. Log critical error. |
| Certificate version unsupported | Reject attestation. Proceed at Minimal trust level. |

### 13.5 Link 5 Failures (Enforcement)

| Error | Agent Response |
|-------|---------------|
| Consent section missing | Treat all actions as `confirm` (require human approval for everything). |
| Unknown action in consent tier | Treat unknown actions as `confirm`. |
| Profile referenced but not defined | Ignore profile, use base file. Log warning. |
| consent_version decreased | Reject file (possible replay). Log critical warning. |

---

*End of Trust Chain Authentication Specification v1.0*
