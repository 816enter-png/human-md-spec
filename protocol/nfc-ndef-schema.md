# NFC NDEF Schema

**Version:** 1.0
**Date:** 2026-03-31
**Status:** Draft
**Author:** 816 Enterprises LLC
**NFC Forum Compliance:** NFC Forum Type 4 Tag Operation Specification v3.0

---

## Table of Contents

1. [Overview](#1-overview)
2. [NDEF Record Format](#2-ndef-record-format)
3. [Payload Structure](#3-payload-structure)
4. [Compact Field Abbreviations](#4-compact-field-abbreviations)
5. [Size Constraints and Fallback](#5-size-constraints-and-fallback)
6. [Signature Inclusion](#6-signature-inclusion)
7. [Tap-to-Share Flow](#7-tap-to-share-flow)
8. [Security Considerations](#8-security-considerations)
9. [Error Handling](#9-error-handling)

---

## 1. Overview

The BIM exposes an NFC Forum Type 4 Tag interface that delivers a JSON representation of the human's public identity sections when tapped by an NFC-enabled device (smartphone, tablet, kiosk, robot). The NFC payload is a read-only snapshot of the public sections -- no approval flow, no request-only sections, no interactive negotiation.

### 1.1 Conformance Language

The keywords MUST, MUST NOT, SHALL, SHALL NOT, SHOULD, SHOULD NOT, MAY are interpreted per RFC 2119.

### 1.2 Design Constraints

- **Maximum payload:** 4,096 bytes (4 KB). This is the practical limit for NFC Forum Type 4 Tags with typical NDEF container sizes.
- **No connection state:** NFC is a single-tap, read-only interaction. There is no session, no pairing, no ongoing connection.
- **Public sections only:** NFC does not support the request-only approval flow. Only sections with `broadcast_scope: public` are included.
- **Authenticated state required:** The BIM MUST be in authenticated state (fingerprint verified, skin contact active) for NFC to respond. An unauthenticated BIM returns an empty NDEF message.

---

## 2. NDEF Record Format

### 2.1 NDEF Message Structure

The NFC payload is a single NDEF message containing one NDEF record.

```
NDEF Message:
┌─────────────────────────────────────────────────┐
│ NDEF Record                                     │
│   TNF:     0x02 (Media-type as defined in RFC 2046) │
│   TYPE:    "application/humanmd+json"           │
│   ID:      (empty)                              │
│   PAYLOAD: <JSON bytes, UTF-8>                  │
└─────────────────────────────────────────────────┘
```

### 2.2 NDEF Record Header (Byte Layout)

```
Byte    Bits    Field           Value
────────────────────────────────────────────
0x00    7       MB (Message Begin)    1
        6       ME (Message End)      1
        5       CF (Chunk Flag)       0
        4       SR (Short Record)     0 or 1 (see below)
        3       IL (ID Length present) 0
        2-0     TNF                   0x02
0x01    7-0     Type Length           24 (0x18) = length of "application/humanmd+json"
0x02    31-0    Payload Length        4 bytes if SR=0, 1 byte if SR=1
        ...     Type                  "application/humanmd+json" (24 bytes, UTF-8)
        ...     Payload               JSON content (UTF-8 bytes)
────────────────────────────────────────────
```

**Short Record (SR) bit:** If payload length <= 255 bytes, SR=1 and Payload Length is 1 byte. Otherwise SR=0 and Payload Length is 4 bytes (big-endian uint32).

### 2.3 NFC Forum Type 4 Tag Structure

The BIM's NFC interface presents an ISO 14443-4 / ISO 7816-4 compliant Type 4 Tag:

```
NDEF Tag Application (AID: D2760000850101)
├── CC File (Capability Container)
│   ├── CCLEN:          0x000F (15 bytes)
│   ├── Mapping Version: 0x20 (v2.0)
│   ├── MLe:            0x00FF (max read = 255 bytes)
│   ├── MLc:            0x00FF (max write = 255 bytes)
│   ├── NDEF File TLV:
│   │   ├── T: 0x04 (NDEF File Control)
│   │   ├── L: 0x06
│   │   ├── File ID: 0xE104
│   │   ├── Max NDEF size: 0x1000 (4096 bytes)
│   │   ├── Read access: 0x00 (no security)
│   │   └── Write access: 0xFF (no write access)
│   └── (end of CC)
└── NDEF File (File ID: 0xE104)
    ├── NLEN: 2 bytes (big-endian, actual NDEF message length)
    └── NDEF Message bytes
```

**Write access = 0xFF:** The NFC tag is read-only. No external device can write to the BIM via NFC.

---

## 3. Payload Structure

### 3.1 Full JSON Payload

When the total payload fits within 4,096 bytes:

```json
{
  "s": "humanmd/v1",
  "v": 14,
  "m": "a3c1f9e7-4b2d-4e8a-9f01-2d3c4e5f6a7b",
  "sig": "<base64url, 88 chars>",
  "pk": "<base64url, 44 chars>",
  "ap": "default",
  "u": "2026-03-31T10:30:00-04:00",
  "cv": 3,
  "sec": {
    "id": {
      "n": "Geoffrey LaCorte",
      "pn": "Geoff",
      "pr": "he/him",
      "la": ["en-US", "es"],
      "tz": "America/New_York",
      "cc": "Italian-American, East Coast US"
    },
    "com": {
      "to": "direct",
      "vb": "concise",
      "ht": "moderate",
      "rf": "bullets"
    },
    "cs": {
      "lm": "visual",
      "dp": "overview-first",
      "ep": "when-complex",
      "al": "moderate"
    },
    "acc": {
      "vi": ["high-contrast", "large-text"],
      "au": ["captions"],
      "mo": [],
      "co": []
    },
    "con": {
      "a": ["adjust_text_size", "use_preferred_name", "set_language", "apply_accessibility_settings"],
      "c": ["share_location", "make_purchase", "contact_others"],
      "p": ["share_medical_data", "record_conversation_audio", "override_emotional_boundaries"]
    },
    "sp": {
      "ls": "none",
      "ss": "mild",
      "tp": "cool",
      "hf": "moderate",
      "ms": "none"
    }
  }
}
```

### 3.2 Section Inclusion Rules

Only sections with `broadcast_scope: public` appear in the NFC payload. Sections are included in the `"sec"` object using their abbreviated keys (see Section 4).

Sections that are absent from the file or have non-public scope are omitted from the JSON. Absent keys indicate the section is unavailable (either not defined or not public).

---

## 4. Compact Field Abbreviations

NFC payloads use abbreviated keys to minimize payload size. The mapping between full names and compact names is fixed by this specification.

### 4.1 Top-Level Field Abbreviations

| Full Name | Compact | Type |
|-----------|---------|------|
| `schema` | `s` | string |
| `version` | `v` | integer |
| `module_id` | `m` | string |
| `signature` | `sig` | string |
| `public_key` | `pk` | string |
| `active_profile` | `ap` | string |
| `updated` | `u` | string |
| `consent_version` | `cv` | integer |
| `sections` | `sec` | object |

### 4.2 Section Key Abbreviations

| Section | Compact Key |
|---------|-------------|
| Identity | `id` |
| Communication | `com` |
| Cognitive Style | `cs` |
| Accessibility | `acc` |
| Emotional Boundaries | `eb` |
| Consent | `con` |
| Profiles | `pro` |
| Sensory Preferences | `sp` |
| UI Dashboard | `uid` |
| Custom Extensions | `ext` |

### 4.3 Identity Field Abbreviations

| Full Name | Compact |
|-----------|---------|
| `name` | `n` |
| `preferred_name` | `pn` |
| `pronouns` | `pr` |
| `languages` | `la` |
| `timezone` | `tz` |
| `cultural_context` | `cc` |

### 4.4 Communication Field Abbreviations

| Full Name | Compact |
|-----------|---------|
| `tone` | `to` |
| `verbosity` | `vb` |
| `humor_tolerance` | `ht` |
| `response_format` | `rf` |

### 4.5 Cognitive Style Field Abbreviations

| Full Name | Compact |
|-----------|---------|
| `learning_modality` | `lm` |
| `detail_preference` | `dp` |
| `example_preference` | `ep` |
| `abstraction_level` | `al` |

### 4.6 Accessibility Field Abbreviations

| Full Name | Compact |
|-----------|---------|
| `visual` | `vi` |
| `auditory` | `au` |
| `motor` | `mo` |
| `cognitive` | `co` |

### 4.7 Consent Field Abbreviations

| Full Name | Compact |
|-----------|---------|
| `autonomous` | `a` |
| `confirm` | `c` |
| `prohibited` | `p` |

### 4.8 Sensory Preferences Field Abbreviations

| Full Name | Compact |
|-----------|---------|
| `light_sensitivity` | `ls` |
| `sound_sensitivity` | `ss` |
| `temperature_preference` | `tp` |
| `haptic_feedback` | `hf` |
| `motion_sensitivity` | `ms` |

---

## 5. Size Constraints and Fallback

### 5.1 Maximum Payload Size

The total NDEF message (header + type + payload) MUST NOT exceed 4,096 bytes. Given NDEF overhead of approximately 30 bytes, the maximum JSON payload is approximately 4,066 bytes.

### 5.2 Size Calculation

The BIM firmware computes the NFC payload on each profile switch, consent update, or file update. The computation procedure:

```
function compute_nfc_payload(hif, active_profile):
    merged = apply_profile(hif, active_profile)
    public_sections = filter_by_scope(merged, "public")
    payload = serialize_compact_json(public_sections)

    if byte_length(payload) <= 4066:
        return payload                          // Full payload
    else:
        return compute_fallback_payload(merged) // Fallback
```

### 5.3 Fallback Payload

If the full public payload exceeds 4,066 bytes, the BIM constructs a fallback payload containing only the essential sections:

**Fallback priority (in order):**
1. Identity (`id`) -- ALWAYS included
2. Accessibility (`acc`) -- ALWAYS included in fallback
3. Consent (`con`) -- ALWAYS included in fallback
4. Signature and metadata fields -- ALWAYS included

**Fallback structure:**

```json
{
  "s": "humanmd/v1",
  "v": 14,
  "m": "a3c1f9e7-4b2d-4e8a-9f01-2d3c4e5f6a7b",
  "sig": "<base64url>",
  "pk": "<base64url>",
  "ap": "default",
  "u": "2026-03-31T10:30:00-04:00",
  "cv": 3,
  "fb": true,
  "sec": {
    "id": { ... },
    "acc": { ... },
    "con": { ... }
  }
}
```

The `"fb": true` field indicates this is a fallback payload. Agents receiving a fallback payload know that additional public sections exist but were omitted for size. They SHOULD connect via BLE to retrieve the full file.

### 5.4 Fallback Size Guarantee

The fallback payload (Identity + Accessibility + Consent + metadata) is designed to fit within 2 KB in all reasonable cases. If even the fallback exceeds 4,066 bytes (which would require extremely large consent lists or accessibility notes), the BIM truncates the consent lists to the first 20 items per tier and the accessibility notes field.

### 5.5 Empty Payload

If no sections have `broadcast_scope: public`, the NFC payload is a minimal metadata-only message:

```json
{
  "s": "humanmd/v1",
  "v": 14,
  "m": "a3c1f9e7-4b2d-4e8a-9f01-2d3c4e5f6a7b",
  "pk": "<base64url>",
  "ap": "default",
  "sec": {}
}
```

No signature is included because there is no section content to sign.

---

## 6. Signature Inclusion

### 6.1 What is Signed

The NFC signature covers the NFC payload content specifically, not the full HIF file. This is because the NFC payload contains only public sections (and in compact format), so the file-level signature would not match.

### 6.2 NFC Signature Computation

```
function compute_nfc_signature(nfc_json, atecc608b):
    // Create signing input: JSON with signature set to empty
    signing_json = copy(nfc_json)
    signing_json["sig"] = ""
    canonical = json_serialize(signing_json, sort_keys=true, no_whitespace=true)
    canonical_bytes = utf8_encode(canonical)
    hash = SHA-512(canonical_bytes)
    signature = atecc608b.ed25519_sign(slot=0, hash)
    return base64url_encode(signature)
```

### 6.3 NFC Signature Verification

```
function verify_nfc_payload(nfc_json, public_key_b64):
    public_key = base64url_decode(nfc_json["pk"])
    signature = base64url_decode(nfc_json["sig"])

    signing_json = copy(nfc_json)
    signing_json["sig"] = ""
    canonical = json_serialize(signing_json, sort_keys=true, no_whitespace=true)
    canonical_bytes = utf8_encode(canonical)
    hash = SHA-512(canonical_bytes)

    return ed25519_verify(public_key, hash, signature)
```

### 6.4 Signature and Fallback

The signature covers whichever content is actually in the payload (full or fallback). The `"fb"` field is included in the signed content, so agents can verify whether the module itself flagged the payload as a fallback (vs. an attacker stripping sections).

---

## 7. Tap-to-Share Flow

### 7.1 Sequence Diagram

```
NFC Reader Device                    BIM
       │                              │
       │  RF field detected           │
       │  (13.56 MHz, ISO 14443)      │
       │─────────────────────────────►│
       │                              │
       │  [BIM checks auth state]     │
       │                              │ Not authenticated?
       │                              │ → Return empty NDEF
       │                              │
       │                              │ Authenticated?
       │                              │ → Continue
       │                              │
       │  RATS (Request for ATS)      │
       │─────────────────────────────►│
       │                              │
       │  ATS (Answer to Select)      │
       │◄─────────────────────────────│
       │                              │
       │  SELECT NDEF Application     │
       │  (AID: D2760000850101)       │
       │─────────────────────────────►│
       │                              │
       │  OK (0x9000)                 │
       │◄─────────────────────────────│
       │                              │
       │  SELECT CC File (0xE103)     │
       │─────────────────────────────►│
       │                              │
       │  CC File contents            │
       │◄─────────────────────────────│
       │                              │
       │  SELECT NDEF File (0xE104)   │
       │─────────────────────────────►│
       │                              │
       │  READ BINARY (offset 0)      │
       │─────────────────────────────►│
       │                              │
       │  NLEN + NDEF Message bytes   │
       │◄─────────────────────────────│
       │                              │
       │  [Additional READ BINARY     │
       │   if payload > MLe]          │
       │─────────────────────────────►│
       │                              │
       │  Remaining bytes             │
       │◄─────────────────────────────│
       │                              │
       │  [Reader has full NDEF msg]  │
       │  Parse JSON, verify sig      │
       │                              │
```

### 7.2 Timing

| Phase | Expected Duration |
|-------|------------------|
| RF field detection and activation | < 50 ms |
| RATS/ATS exchange | < 10 ms |
| Application selection | < 20 ms |
| CC file read | < 10 ms |
| NDEF file read (2 KB payload) | < 100 ms |
| NDEF file read (4 KB payload) | < 200 ms |
| **Total tap-to-data** | **< 300 ms** |

### 7.3 User Experience

1. Human holds BIM near NFC reader (within 4 cm).
2. BIM LED flashes once (blue) to confirm NFC read occurred.
3. Reader device processes the JSON payload.
4. Reader device applies identity settings (name, accessibility, preferences).
5. Human removes BIM from NFC range.

There is no confirmation prompt for NFC (unlike BLE request-only sections). NFC shares only `public` scope sections, which the human has explicitly designated for unrestricted sharing.

### 7.4 Repeated Taps

Each NFC read returns the current state of the payload. If the profile or file has changed since the last tap, the new content is returned. There is no caching or state between taps.

---

## 8. Security Considerations

### 8.1 NFC-Specific Threats

| Threat | Mitigation | Residual Risk |
|--------|------------|---------------|
| Eavesdropping (passive NFC sniffing) | Only public sections are shared. Content is deliberately public. Signature prevents tampering. | An attacker within ~1m can read the same data. Acceptable because the data is public-scope. |
| Relay attack (extending NFC range) | BIM requires physical proximity (skin contact sensor). NFC range is ~4 cm. | Sophisticated relay hardware could extend range, but only public data is exposed. |
| Data modification in transit | Ed25519 signature over payload. Any modification invalidates signature. | None (computationally infeasible). |
| Replay (recording and replaying NFC data) | `version` and `updated` fields allow detection. However, NFC has no anti-replay mechanism. | A replayed payload is valid but stale. Agents should check `updated` timestamp. |
| Unauthorized NFC read | BIM only responds when authenticated (fingerprint + skin contact). | None when BIM is in correct state. |
| Tag cloning | Signature is bound to the module's private key. A cloned tag cannot produce new valid signatures. | Cloned data is valid but static (frozen at time of cloning). |

### 8.2 No Encryption on NFC

The NFC payload is not encrypted. This is by design: NFC shares only `public` sections, which are intended for any receiver. Encryption would add complexity without security benefit for public data.

### 8.3 No Write Access

The BIM's NFC interface is strictly read-only. The CC file specifies write access = 0xFF (no write). No external NFC device can modify the BIM's stored data via NFC.

---

## 9. Error Handling

### 9.1 BIM-Side Errors

| Condition | BIM Behavior |
|-----------|-------------|
| BIM not authenticated | Return empty NDEF message (NLEN = 0x0000) |
| No HIF stored | Return empty NDEF message |
| NFC payload computation failed | Return empty NDEF message, set error LED (red flash) |
| NFC hardware fault | No RF response (tag appears absent to reader) |

### 9.2 Reader-Side Errors

| Condition | Recommended Reader Behavior |
|-----------|---------------------------|
| Empty NDEF message | Display "Identity module not active" or equivalent |
| JSON parse error | Discard payload. Do not apply any settings. |
| Schema not `"humanmd/v1"` | Discard payload. Unknown format. |
| Signature verification failed | Discard payload. Display "Identity could not be verified." |
| `"fb": true` (fallback mode) | Apply available sections. Optionally prompt user to connect via BLE for full file. |
| `"sec"` is empty | Module has no public sections. Display "No public identity information available." |
| Payload truncated (NFC read incomplete) | Discard partial payload. Prompt user to re-tap. |

### 9.3 Compatibility

Devices that do not understand `application/humanmd+json` will treat the NDEF record as an unknown MIME type. Standard NFC reader behavior for unknown MIME types is to offer to open the data with an appropriate application. This enables a future companion app to register as a handler for this MIME type.

---

*End of NFC NDEF Schema v1.0*
