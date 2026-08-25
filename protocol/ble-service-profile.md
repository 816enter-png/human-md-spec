# BLE GATT Service Profile

**Version:** 1.0
**Date:** 2026-03-31
**Status:** Draft
**Author:** 816 Enterprises LLC
**Bluetooth SIG Compliance:** Bluetooth Core Specification v5.0+

---

## Table of Contents

1. [Overview](#1-overview)
2. [Service Definition](#2-service-definition)
3. [Characteristic Definitions](#3-characteristic-definitions)
4. [BLE Advertisement Format](#4-ble-advertisement-format)
5. [Connection Security](#5-connection-security)
6. [MTU Negotiation and Chunked Transfer](#6-mtu-negotiation-and-chunked-transfer)
7. [Connection State Machine](#7-connection-state-machine)
8. [Error Codes](#8-error-codes)
9. [Power and Timing Parameters](#9-power-and-timing-parameters)

---

## 1. Overview

This specification defines the BLE GATT service exposed by the Biometric Identity Module (BIM) for broadcasting the human.md identity file to AI agents. The BIM operates as a GATT server (peripheral role). AI agents operate as GATT clients (central role).

### 1.1 Conformance Language

The keywords MUST, MUST NOT, SHALL, SHALL NOT, SHOULD, SHOULD NOT, MAY are interpreted per RFC 2119.

### 1.2 Prerequisites

- BIM must be in authenticated state (fingerprint verified, skin contact active).
- BIM advertises only while authenticated.
- All characteristics return ATT Error `0x80` (Application Error: Not Authenticated) if the BIM is not in authenticated state during a read.

---

## 2. Service Definition

### 2.1 Service UUID

**Primary Service UUID:** `7e3f1a94-d8b2-4c6e-a5f0-9b1d2e3c4f5a`

This is a 128-bit UUID. The BIM exposes this as a primary service in its GATT database.

### 2.2 Service Includes

No included services.

### 2.3 Service Availability

The service is present in the GATT database only while the BIM is in authenticated state. When the BIM re-locks, the service is removed from the GATT database and all active connections are terminated with reason code `0x13` (Remote User Terminated Connection).

---

## 3. Characteristic Definitions

### 3.1 Characteristic Summary

| # | Name | UUID | Properties | Format | Max Size |
|---|------|------|------------|--------|----------|
| 1 | File | `7e3f1a94-d8b2-4c6e-a5f0-9b1d2e3c4f01` | Read | UTF-8 | 65,536 B |
| 2 | Section | `7e3f1a94-d8b2-4c6e-a5f0-9b1d2e3c4f02` | Read | UTF-8 | 8,192 B |
| 3 | Profile | `7e3f1a94-d8b2-4c6e-a5f0-9b1d2e3c4f03` | Read, Notify | UTF-8 | 64 B |
| 4 | Consent Version | `7e3f1a94-d8b2-4c6e-a5f0-9b1d2e3c4f04` | Read, Notify | uint32 | 4 B |
| 5 | Signature | `7e3f1a94-d8b2-4c6e-a5f0-9b1d2e3c4f05` | Read | bytes | 64 B |
| 6 | Public Key | `7e3f1a94-d8b2-4c6e-a5f0-9b1d2e3c4f06` | Read | bytes | 32 B |
| 7 | Attestation | `7e3f1a94-d8b2-4c6e-a5f0-9b1d2e3c4f07` | Read | bytes | 253 B |

### 3.2 File Characteristic (0x4f01)

**UUID:** `7e3f1a94-d8b2-4c6e-a5f0-9b1d2e3c4f01`
**Properties:** Read
**Descriptors:**
- CCCD (0x2902): Not applicable (no Notify)
- Characteristic User Description (0x2901): "Human Identity File"

**Behavior:**

The File characteristic returns the full HIF content filtered by `broadcast_scope`:

1. Sections with `broadcast_scope: public` are always included.
2. Sections with `broadcast_scope: request-only` are included only if the agent has been approved for that section during this session.
3. Sections with `broadcast_scope: private` or `hidden` are never included.
4. The YAML front matter is always included (all fields).
5. If a profile is active, the returned content is the merged file (base + profile overrides).
6. The returned content is the canonical markdown format (UTF-8, LF line endings).

**Note:** The signature in the returned file covers the full merged file including non-public sections. An agent reading only public sections will not be able to verify the signature against the partial content. For signature verification of the broadcast subset, agents MUST use the Signature characteristic (which signs the broadcast-filtered content).

**Long Read:** This characteristic will typically exceed the MTU. The BIM MUST support ATT Read Blob Request (0x0C) for chunked reading. See Section 6.

### 3.3 Section Characteristic (0x4f02)

**UUID:** `7e3f1a94-d8b2-4c6e-a5f0-9b1d2e3c4f02`
**Properties:** Read
**Descriptors:**
- Characteristic User Description (0x2901): "Identity File Section"

**Behavior:**

The Section characteristic supports parameterized reads. The agent writes a section name to the characteristic's descriptor before reading:

**Section Request Descriptor:**
**UUID:** `7e3f1a94-d8b2-4c6e-a5f0-9b1d2e3c4f12`
**Properties:** Write
**Format:** UTF-8 string, section name in lowercase with hyphens:

| Section | Request Value |
|---------|--------------|
| Identity | `identity` |
| Communication | `communication` |
| Cognitive Style | `cognitive-style` |
| Accessibility | `accessibility` |
| Emotional Boundaries | `emotional-boundaries` |
| Consent | `consent` |
| Profiles | `profiles` |
| Sensory Preferences | `sensory-preferences` |
| UI Dashboard | `ui-dashboard` |
| Custom Extensions | `custom-extensions` |

**Flow:**
1. Agent writes section name to the Section Request Descriptor.
2. Agent reads the Section characteristic.
3. BIM returns the section content if `broadcast_scope` allows it.
4. If the section is `request-only` and not yet approved, BIM returns ATT Error `0x81` (Approval Required). The BIM simultaneously triggers an approval prompt (LED pattern or companion app notification). Once approved, the agent can re-read.
5. If the section is `private` or `hidden`, BIM returns ATT Error `0x82` (Access Denied).
6. If the section name is not recognized, BIM returns ATT Error `0x83` (Unknown Section).

### 3.4 Profile Characteristic (0x4f03)

**UUID:** `7e3f1a94-d8b2-4c6e-a5f0-9b1d2e3c4f03`
**Properties:** Read, Notify
**Descriptors:**
- CCCD (0x2902): Notifications enabled/disabled by client
- Characteristic User Description (0x2901): "Active Profile"

**Format:** UTF-8 string, 1-64 bytes. Value is the `active_profile` name (e.g., `"default"`, `"work"`, `"medical"`).

**Notify behavior:** When the human switches profiles (hardware button or companion app), the BIM sends a Notification with the new profile name. Agents receiving this notification MUST re-read the File or Section characteristics, as the merged content has changed.

### 3.5 Consent Version Characteristic (0x4f04)

**UUID:** `7e3f1a94-d8b2-4c6e-a5f0-9b1d2e3c4f04`
**Properties:** Read, Notify
**Descriptors:**
- CCCD (0x2902): Notifications enabled/disabled by client
- Characteristic User Description (0x2901): "Consent Version"

**Format:** uint32, little-endian. Value is the `consent_version` counter from the HIF front matter.

**Notify behavior:** When the consent section is updated (e.g., via companion app while the module is active), the BIM sends a Notification with the new consent_version value. Agents receiving this notification MUST re-read the Consent section before taking any further actions.

### 3.6 Signature Characteristic (0x4f05)

**UUID:** `7e3f1a94-d8b2-4c6e-a5f0-9b1d2e3c4f05`
**Properties:** Read
**Descriptors:**
- Characteristic User Description (0x2901): "Ed25519 Signature"

**Format:** 64 raw bytes. This is the Ed25519 signature, not Base64url-encoded. The raw binary is used for BLE to avoid encoding overhead.

**Note:** This signature covers the **broadcast-filtered** content (only sections the agent can see), not the full file. This allows agents to verify the integrity of the content they received. The signature is recomputed by the BIM whenever the broadcast filter changes (e.g., a request-only section is approved).

### 3.7 Public Key Characteristic (0x4f06)

**UUID:** `7e3f1a94-d8b2-4c6e-a5f0-9b1d2e3c4f06`
**Properties:** Read
**Descriptors:**
- Characteristic User Description (0x2901): "Ed25519 Public Key"

**Format:** 32 raw bytes. The Ed25519 public key corresponding to the signing private key in the ATECC608B.

### 3.8 Attestation Characteristic (0x4f07)

**UUID:** `7e3f1a94-d8b2-4c6e-a5f0-9b1d2e3c4f07`
**Properties:** Read
**Descriptors:**
- Characteristic User Description (0x2901): "Hardware Attestation Certificate"

**Format:** 253 raw bytes. The hardware attestation certificate as defined in `trust-chain-spec.md` Section 10.3.

---

## 4. BLE Advertisement Format

### 4.1 Advertisement Type

The BIM uses **ADV_IND** (connectable undirected advertising) to allow any agent to connect.

### 4.2 Advertisement Data Layout

The advertisement packet uses Bluetooth Manufacturer-Specific Data (AD Type `0xFF`).

**Company Identifier:** `0xFFFF` (reserved for development; production modules will register a Bluetooth SIG company ID).

### 4.3 Advertisement Payload Structure

```
Byte    Size    Field                   Description
────────────────────────────────────────────────────────
0x00    1       AD Length               Total length of this AD structure (29 bytes = 0x1D)
0x01    1       AD Type                 0xFF (Manufacturer Specific Data)
0x02    2       Company ID              0xFFFF (little-endian) [placeholder]
0x04    1       Protocol Version        0x01
0x05    16      Module UUID             module_id as 16 raw bytes (UUID binary form, big-endian)
0x15    4       Profile Name Hash       CRC-32 of active_profile UTF-8 bytes
0x19    2       Schema Version          Major.Minor as two uint8 (0x01, 0x00 for v1.0)
0x1B    4       Public Section Hash     CRC-32 of concatenated public section content (UTF-8 bytes)
────────────────────────────────────────────────────────
Total AD structure: 31 bytes (1 length + 1 type + 29 payload)
```

### 4.4 Additional AD Structures

The advertisement also includes:

```
Byte    Size    Field
────────────────────────────────────────────────────────
0x00    1       AD Length (0x03)
0x01    1       AD Type: 0x03 (Complete List of 16-bit Service UUIDs) [empty if no 16-bit UUIDs]
─── OR ───
0x00    1       AD Length (0x11)
0x01    1       AD Type: 0x07 (Complete List of 128-bit Service UUIDs)
0x02    16      Service UUID (7e3f1a94-d8b2-4c6e-a5f0-9b1d2e3c4f5a, little-endian byte order)
────────────────────────────────────────────────────────
```

### 4.5 Scan Response Data

The scan response packet contains:

```
Byte    Size    Field
────────────────────────────────────────────────────────
0x00    1       AD Length
0x01    1       AD Type: 0x09 (Complete Local Name)
0x02    N       Device name: "HMD-" + first 8 hex chars of module_id (e.g., "HMD-a3c1f9e7")
────────────────────────────────────────────────────────
```

### 4.6 Advertisement Interval

| State | Interval | Duration |
|-------|----------|----------|
| Normal (authenticated, no connection) | 1000 ms | Continuous while authenticated |
| Fast (after profile switch or consent change) | 100 ms | 30 seconds, then return to normal |
| Connected | Advertising suspended while a client is connected (single-client mode) |

### 4.7 Change Detection

Agents use the **Public Section Hash** (CRC-32) in the advertisement to detect content changes without connecting. If the hash differs from the last known value, the agent SHOULD connect and re-read the file. If the hash is unchanged, no connection is needed.

The **Profile Name Hash** (CRC-32) similarly allows agents to detect profile switches from the advertisement alone.

---

## 5. Connection Security

### 5.1 Pairing Method

The BIM requires **LE Secure Connections** (LESC) with **Numeric Comparison** or **Passkey Entry** association model for agent connections.

**Justification:** LE Secure Connections uses ECDH P-256 key exchange, providing protection against passive eavesdropping and MITM attacks. Legacy pairing is NOT supported.

### 5.2 Security Mode

**Security Mode 1, Level 4:** Authenticated LE Secure Connections pairing with encryption (AES-CCM with 128-bit key).

All characteristics in the human.md GATT service require Security Mode 1, Level 4. Reads without an encrypted connection return ATT Error `0x05` (Insufficient Authentication).

### 5.3 Bonding

The BIM supports bonding (storing paired agent keys) for up to 8 agents. Bonded agents can reconnect without re-pairing.

**Bond management:**
- When the bond table is full, the least-recently-used bond is evicted.
- The human can clear all bonds via the companion app or by long-pressing the hardware button for 10 seconds.
- Bonds are stored in encrypted flash, protected by the ATECC608B flash encryption key.

### 5.4 Agent Authentication

For the initial pairing, the BIM displays a 6-digit passkey via:
- LED blink pattern (each digit encoded as blinks: 1 blink = 1, 2 blinks = 2, etc., with a pause between digits)
- Companion app notification (if connected)

The agent must enter this passkey to complete pairing. This prevents unauthorized agents from silently connecting.

---

## 6. MTU Negotiation and Chunked Transfer

### 6.1 MTU Negotiation

The BIM supports ATT MTU exchange. The BIM's preferred MTU is **512 bytes**. The negotiated MTU will be the minimum of the BIM's and the agent's supported MTU.

**Minimum supported MTU:** 23 bytes (BLE default). The BIM MUST function at any MTU >= 23 bytes.

### 6.2 Chunked Transfer Protocol

For characteristics larger than (MTU - 3) bytes (the ATT header overhead), the BIM uses the standard ATT Long Read procedure:

```
Agent                                   BIM
  │                                      │
  │  ATT Read Request (handle)           │
  │─────────────────────────────────────►│
  │                                      │
  │  ATT Read Response (first MTU-1 bytes)│
  │◄─────────────────────────────────────│
  │                                      │
  │  ATT Read Blob Request (handle, offset=MTU-1)
  │─────────────────────────────────────►│
  │                                      │
  │  ATT Read Blob Response (next MTU-1 bytes)
  │◄─────────────────────────────────────│
  │                                      │
  │  ... repeat until response < MTU-1 bytes ...
  │                                      │
```

### 6.3 Transfer Size Estimates

| Characteristic | Typical Size | At 512B MTU | At 23B MTU |
|---------------|-------------|-------------|------------|
| File | 2-20 KB | 4-40 reads | 100-1000 reads |
| Section | 200-2000 B | 1-4 reads | 10-100 reads |
| Profile | 4-64 B | 1 read | 1-4 reads |
| Consent Version | 4 B | 1 read | 1 read |
| Signature | 64 B | 1 read | 4 reads |
| Public Key | 32 B | 1 read | 2 reads |
| Attestation | 253 B | 1 read | 13 reads |

### 6.4 Transfer Timeout

If an ATT Read Blob sequence is not completed within 30 seconds (no new Read Blob Request received), the BIM resets the read state for that characteristic. The agent must restart the read from offset 0.

### 6.5 Concurrent Reads

The BIM supports concurrent reads of different characteristics but NOT concurrent Read Blob sequences on the same characteristic. A new Read Request on a characteristic resets any in-progress Read Blob sequence for that characteristic.

---

## 7. Connection State Machine

```
                    ┌────────────────┐
                    │  ADVERTISING   │
                    └───────┬────────┘
                            │ connection request
                            ▼
                    ┌────────────────┐
                    │   CONNECTED    │
                    └───────┬────────┘
                            │ pairing initiated
                            ▼
                    ┌────────────────┐  pairing failed
                    │    PAIRING     │─────────────────► DISCONNECT
                    └───────┬────────┘
                            │ pairing succeeded
                            ▼
                    ┌────────────────┐
                    │   ENCRYPTED    │
                    └───────┬────────┘
                            │ service discovery
                            ▼
                    ┌────────────────┐
                    │    ACTIVE      │◄──── characteristic reads/notifications
                    └───────┬────────┘
                            │ disconnect (agent, BIM re-lock, range loss, timeout)
                            ▼
                    ┌────────────────┐
                    │  DISCONNECTED  │
                    └───────┬────────┘
                            │ BIM still authenticated
                            ▼
                    ┌────────────────┐
                    │  ADVERTISING   │ (resume advertising for next agent)
                    └────────────────┘
```

### 7.1 Connection Limits

The BIM supports **one active connection** at a time (single-client mode). While connected, advertising is suspended. If a second agent needs access, the first agent must disconnect.

**Rationale:** Single-client mode simplifies the security model and prevents resource exhaustion on the ESP32-S3.

### 7.2 Connection Timeout

If no ATT transaction occurs for 60 seconds on an active connection, the BIM terminates the connection with reason code `0x13` (Remote User Terminated Connection).

### 7.3 Disconnection Events

The BIM terminates the connection when:
- Agent sends a disconnect request.
- BIM re-locks (skin contact lost or fingerprint timeout).
- Connection timeout (60 seconds idle).
- Profile switch (BIM disconnects, updates advertisement, resumes advertising).
- Human initiates disconnect via hardware button (double-press).

---

## 8. Error Codes

### 8.1 Application-Specific ATT Error Codes

| Code | Name | Description |
|------|------|-------------|
| `0x80` | Not Authenticated | BIM is not in authenticated state |
| `0x81` | Approval Required | Section has `request-only` scope and has not been approved |
| `0x82` | Access Denied | Section has `private` or `hidden` scope |
| `0x83` | Unknown Section | Section name in request descriptor is not recognized |
| `0x84` | File Not Loaded | No HIF is stored on the BIM |
| `0x85` | Signing In Progress | BIM is recomputing signature (during profile switch) |
| `0x86` | Transfer Timeout | Read Blob sequence timed out; restart from offset 0 |

### 8.2 Standard ATT Errors Used

| Code | Name | When Used |
|------|------|-----------|
| `0x05` | Insufficient Authentication | Connection not encrypted (LESC required) |
| `0x06` | Request Not Supported | Write to a read-only characteristic |
| `0x07` | Invalid Offset | Read Blob offset beyond characteristic length |
| `0x0D` | Invalid Attribute Length | Section Request Descriptor write exceeds 64 bytes |

---

## 9. Power and Timing Parameters

### 9.1 BLE Radio Parameters

| Parameter | Value |
|-----------|-------|
| TX Power | 0 dBm (default), configurable: -12 to +9 dBm |
| PHY | LE 1M (default), LE 2M (if supported by agent) |
| Connection interval (preferred) | 15 ms - 30 ms |
| Slave latency | 4 |
| Supervision timeout | 4000 ms |

### 9.2 Power Consumption Estimates

| State | Current Draw | Notes |
|-------|-------------|-------|
| Advertising (1s interval) | ~1.5 mA average | ESP32-S3 light sleep between advertisements |
| Connected, idle | ~3 mA average | Maintaining encrypted connection |
| Connected, transferring | ~15 mA peak | Active radio TX/RX |
| Deep sleep (locked) | ~10 uA | ESP32-S3 deep sleep, skin contact sensor polling at 1 Hz |

### 9.3 Estimated Battery Life

| Scenario | Battery (150 mAh) | Battery (300 mAh) |
|----------|-------------------|-------------------|
| Advertising only, 12h/day | ~8 days | ~16 days |
| 4 connections/day, 5 min each | ~7 days | ~14 days |
| Continuous connection (worst case) | ~2 days | ~4 days |

---

*End of BLE GATT Service Profile v1.0*
