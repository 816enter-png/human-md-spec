# Human Identity File Format Specification

**Version:** 1.0
**Date:** 2026-03-31
**Status:** Draft
**Author:** 816 Enterprises LLC
**MIME Type:** `application/humanmd+json` (JSON representation)
**File Extension:** `.md` (canonical), `.humanmd.json` (JSON serialization)
**Character Encoding:** UTF-8 (no BOM)
**Maximum File Size:** 65,536 bytes (64 KB)

---

## Table of Contents

1. [Overview](#1-overview)
2. [File Structure](#2-file-structure)
3. [YAML Front Matter Schema](#3-yaml-front-matter-schema)
4. [Section Definitions](#4-section-definitions)
5. [Broadcast Scope](#5-broadcast-scope)
6. [Consent Section Structure](#6-consent-section-structure)
7. [Profiles Section Structure](#7-profiles-section-structure)
8. [Signature Computation](#8-signature-computation)
9. [JSON Serialization](#9-json-serialization)
10. [Validation Rules](#10-validation-rules)
11. [Error Handling](#11-error-handling)

---

## 1. Overview

A human.md file is a structured identity file authored by a human for consumption by AI agents, autonomous systems, and digital infrastructure. The file uses standard markdown with YAML front matter. It is identified by the presence of `schema: "humanmd/v1"` in the YAML front matter, not by file extension.

### 1.1 Conformance Language

The key words MUST, MUST NOT, SHALL, SHALL NOT, SHOULD, SHOULD NOT, MAY, and OPTIONAL in this document follow RFC 2119 semantics.

### 1.2 Design Constraints

- The file MUST be valid UTF-8 with no byte order mark.
- The file MUST NOT exceed 65,536 bytes.
- The file MUST be simultaneously human-readable (in a text editor) and machine-parseable.
- All timestamps MUST use ISO 8601 format with timezone offset or `Z` suffix.
- All UUIDs MUST be RFC 4122 v4 format.

---

## 2. File Structure

```
---
<YAML front matter>
---

# Identity
<!-- broadcast_scope: public -->
<section content>

# Communication Preferences
<!-- broadcast_scope: public -->
<section content>

...remaining sections...
```

The file consists of:
1. YAML front matter delimited by `---` on lines by themselves.
2. Ten named sections, each beginning with a level-1 markdown heading (`# `).
3. Each section contains a broadcast_scope HTML comment on the line immediately following the heading.
4. Section content in standard markdown.

---

## 3. YAML Front Matter Schema

All fields are REQUIRED unless marked OPTIONAL.

| Field | Type | Constraints | Description |
|-------|------|-------------|-------------|
| `schema` | string | MUST be `"humanmd/v1"` | File format identifier. Parsers MUST reject files without this field or with an unrecognized value. |
| `version` | integer | >= 1, monotonically increasing | File version number. Incremented on every edit. |
| `module_id` | string | UUID v4, 36 characters | Unique identifier of the signing BIM hardware module. |
| `signature` | string | Base64-encoded, 88 characters (64 bytes encoded) | Ed25519 signature over the canonical signing payload (see Section 8). |
| `public_key` | string | Base64-encoded, 44 characters (32 bytes encoded) | Ed25519 public key corresponding to the signing private key on the BIM secure element. |
| `active_profile` | string | 1-64 characters, alphanumeric + hyphen + underscore | Currently active context profile name. `"default"` when no profile override is active. |
| `updated` | string | ISO 8601 datetime with timezone | Timestamp of last file modification. Example: `"2026-03-31T14:30:00-04:00"` |
| `consent_version` | integer | >= 1, monotonically increasing | Counter tracking changes to the Consent section. Agents MUST re-read consent when this value changes. |
| `file_id` | string | UUID v4, 36 characters | OPTIONAL. Persistent identifier for this identity file across module transfers. |

### 3.1 Example Front Matter

```yaml
---
schema: "humanmd/v1"
version: 42
module_id: "a1b2c3d4-e5f6-4a7b-8c9d-0e1f2a3b4c5d"
signature: "TG9yZW0gaXBzdW0gZG9sb3Igc2l0IGFtZXQsIGNvbnNlY3RldHVyIGFkaXBpc2NpbmcgZWxpdC4="
public_key: "MCowBQYDK2VwAyEAGb1gMf7GtDkTz8fKGNz..."
active_profile: "default"
updated: "2026-03-31T14:30:00-04:00"
consent_version: 7
---
```

### 3.2 Front Matter Parsing Rules

1. Parsers MUST first check for `schema: "humanmd/v1"`. If absent or non-matching, reject the file with error `HIF_ERR_SCHEMA_MISSING`.
2. All string values MUST be quoted in YAML to prevent type coercion.
3. The `signature` field is excluded from signature computation (see Section 8).
4. Parsers MUST reject files where `version` or `consent_version` have decreased compared to a previously seen value from the same `module_id` (replay protection).

---

## 4. Section Definitions

Each section is identified by its level-1 heading text. Sections MUST appear in the order specified below. Parsers SHOULD accept sections in any order but MUST NOT accept duplicate section headings.

### 4.1 Section 1 — Identity

**Heading:** `# Identity`

| Field | Type | Required | Constraints |
|-------|------|----------|-------------|
| `name` | string | YES | 1-128 characters |
| `preferred_name` | string | NO | 1-64 characters |
| `pronouns` | string | NO | 1-32 characters. Free-text (e.g., "she/her", "they/them") |
| `languages` | list of string | YES | ISO 639-1 codes. At least one entry. |
| `primary_language` | string | YES | ISO 639-1 code. MUST appear in `languages` list. |
| `timezone` | string | NO | IANA timezone identifier (e.g., "America/New_York") |
| `cultural_context` | string | NO | 1-256 characters. Free-text description. |

Fields are encoded as markdown key-value pairs:
```markdown
- **Name:** Geoffrey LaCorte
- **Preferred name:** Geoff
- **Pronouns:** he/him
- **Languages:** en, es
- **Primary language:** en
- **Timezone:** America/New_York
```

Parsers MUST extract fields by matching the bold key pattern `**<Key>:**` followed by the value on the same line, or for list values, on subsequent indented lines.

### 4.2 Section 2 — Communication Preferences

**Heading:** `# Communication Preferences`

| Field | Type | Required | Constraints |
|-------|------|----------|-------------|
| `tone` | enum | YES | One of: `formal`, `casual`, `direct`, `warm`, `professional` |
| `verbosity` | enum | YES | One of: `minimal`, `concise`, `moderate`, `detailed`, `comprehensive` |
| `humor_tolerance` | enum | NO | One of: `none`, `light`, `moderate`, `frequent` |
| `response_format` | enum | NO | One of: `paragraphs`, `bullets`, `step-by-step`, `mixed` |
| `custom_instructions` | string | NO | 1-512 characters. Free-text additional instructions. |

### 4.3 Section 3 — Cognitive Style

**Heading:** `# Cognitive Style`

| Field | Type | Required | Constraints |
|-------|------|----------|-------------|
| `learning_modality` | enum | NO | One of: `visual`, `auditory`, `kinesthetic`, `reading-writing`, `mixed` |
| `detail_preference` | enum | NO | One of: `overview-first`, `detail-first`, `balanced` |
| `example_preference` | enum | NO | One of: `examples-first`, `theory-first`, `interleaved` |
| `abstraction_level` | enum | NO | One of: `concrete`, `moderate`, `abstract` |

### 4.4 Section 4 — Accessibility Needs

**Heading:** `# Accessibility Needs`

| Field | Type | Required | Constraints |
|-------|------|----------|-------------|
| `visual` | list of string | NO | Values from: `screen-reader`, `high-contrast`, `large-text`, `dark-mode`, `reduced-motion`, `color-blind-safe` |
| `auditory` | list of string | NO | Values from: `captions`, `transcripts`, `volume-limit`, `no-autoplay-audio`, `visual-alerts` |
| `motor` | list of string | NO | Values from: `voice-control`, `switch-access`, `dwell-timing`, `large-targets`, `keyboard-only` |
| `cognitive` | list of string | NO | Values from: `simplified-language`, `reduced-cognitive-load`, `consistent-layout`, `step-by-step`, `no-time-pressure` |
| `custom` | list of string | NO | Free-text entries for needs not covered above. |

### 4.5 Section 5 — Emotional Boundaries

**Heading:** `# Emotional Boundaries`

| Field | Type | Required | Constraints |
|-------|------|----------|-------------|
| `topics_to_avoid` | list of string | NO | Free-text. Max 32 entries, each 1-128 characters. |
| `emotional_triggers` | list of string | NO | Free-text. Max 32 entries, each 1-128 characters. |
| `safe_topics` | list of string | NO | Free-text. Max 32 entries, each 1-128 characters. |
| `de_escalation` | string | NO | 1-512 characters. Instructions for de-escalation. |
| `humor_on_sensitive_topics` | enum | NO | One of: `never`, `with-care`, `acceptable` |

### 4.6 Section 6 — Consent Tiers

**Heading:** `# Consent Tiers`

See Section 6 of this specification for the full consent structure definition.

### 4.7 Section 7 — Context Profiles

**Heading:** `# Context Profiles`

See Section 7 of this specification for the full profiles structure definition.

### 4.8 Section 8 — Sensory Preferences

**Heading:** `# Sensory Preferences`

| Field | Type | Required | Constraints |
|-------|------|----------|-------------|
| `light_sensitivity` | enum | NO | One of: `none`, `mild`, `moderate`, `severe` |
| `sound_sensitivity` | enum | NO | One of: `none`, `mild`, `moderate`, `severe` |
| `temperature_preference` | enum | NO | One of: `cool`, `moderate`, `warm` |
| `haptic_feedback` | enum | NO | One of: `none`, `subtle`, `moderate`, `strong` |
| `motion_sensitivity` | enum | NO | One of: `none`, `mild`, `moderate`, `severe` |

### 4.9 Section 9 — UI Dashboard

**Heading:** `# UI Dashboard`

| Field | Type | Required | Constraints |
|-------|------|----------|-------------|
| `layout` | enum | NO | One of: `single-column`, `two-column`, `grid`, `minimal` |
| `color_scheme` | enum | NO | One of: `light`, `dark`, `system`, `high-contrast` |
| `widgets` | list of object | NO | Max 16 entries. See widget schema below. |
| `font_size` | enum | NO | One of: `small`, `medium`, `large`, `x-large` |
| `custom_css` | string | NO | Max 4096 characters. CSS for display customization. |

**Widget schema:**
| Field | Type | Required | Constraints |
|-------|------|----------|-------------|
| `type` | enum | YES | One of: `greeting`, `clock`, `weather`, `notes`, `contacts`, `accessibility-tools`, `custom` |
| `position` | integer | YES | 1-16. Display order. |
| `config` | object | NO | Widget-specific key-value pairs. Max 1024 bytes serialized. |

### 4.10 Section 10 — Custom Extensions

**Heading:** `# Custom Extensions`

| Field | Type | Required | Constraints |
|-------|------|----------|-------------|
| `namespace` | string | YES per entry | Reverse-domain notation (e.g., `com.example.medical`). 1-128 characters. |
| `data` | object | YES per entry | Arbitrary key-value pairs. Max 4096 bytes serialized per namespace. |

Custom extensions MUST NOT override or conflict with standard section fields. Parsers MUST ignore unrecognized namespaces without error.

---

## 5. Broadcast Scope

### 5.1 Enum Definition

```
broadcast_scope := "public" | "request-only" | "private" | "hidden"
```

### 5.2 Scope Semantics

| Scope | BLE Broadcast | NFC Tap | GATT Read | Agent Request | App Access |
|-------|--------------|---------|-----------|---------------|------------|
| `public` | YES | YES | YES | YES | YES |
| `request-only` | NO | NO | NO | YES (with approval) | YES |
| `private` | NO | NO | NO | NO | YES (authenticated) |
| `hidden` | NO | NO | NO | NO | NO (display only on module) |

### 5.3 Enforcement Rules

1. The BIM firmware MUST filter sections before transmission. A `request-only` section MUST NOT be included in BLE advertisements, NFC payloads, or unsolicited GATT reads.
2. When an agent requests a `request-only` section, the BIM MUST prompt the human for approval via LED pattern (2 blue blinks) or companion app notification. The BIM MUST wait up to 30 seconds for approval. If no response, deny the request.
3. `private` sections are encrypted at rest with a key derived from the user's fingerprint template hash. They can only be decrypted when the companion app is connected and the user is authenticated.
4. `hidden` sections are encrypted with a module-internal key and are never transmitted over any interface. They exist solely for on-module storage.
5. The broadcast scope HTML comment MUST appear on the line immediately following the section heading:
   ```markdown
   # Emotional Boundaries
   <!-- broadcast_scope: private -->
   ```
6. If the broadcast_scope comment is missing, parsers MUST default to `private`.

### 5.4 Scope Change Rules

- Scope changes from more restrictive to less restrictive (e.g., `private` to `public`) MUST require fingerprint authentication.
- Scope changes MUST increment the file `version`.
- The BIM firmware MUST re-sign the file after any scope change.

---

## 6. Consent Section Structure

The Consent section uses a three-tier YAML block embedded within the markdown section body, delimited by triple-backtick fences with `yaml` language identifier.

### 6.1 Schema

```yaml
consent:
  autonomous:
    - action: "<action_identifier>"
      scope: "<scope_description>"
      conditions: "<optional_conditions>"
  confirm:
    - action: "<action_identifier>"
      scope: "<scope_description>"
      timeout_seconds: <integer>
      fallback: "deny" | "allow"
  prohibited:
    - action: "<action_identifier>"
      reason: "<optional_reason>"
```

### 6.2 Field Definitions

**Tier 1 — autonomous:**

| Field | Type | Required | Constraints |
|-------|------|----------|-------------|
| `action` | string | YES | 1-128 characters. Identifier for the permitted action. |
| `scope` | string | YES | 1-256 characters. Description of the action's permitted scope. |
| `conditions` | string | NO | 1-256 characters. Conditions under which the action is permitted. |

**Tier 2 — confirm:**

| Field | Type | Required | Constraints |
|-------|------|----------|-------------|
| `action` | string | YES | 1-128 characters. Identifier for the action requiring confirmation. |
| `scope` | string | YES | 1-256 characters. Description of what requires confirmation. |
| `timeout_seconds` | integer | NO | 5-300. Default: 30. Time to wait for human confirmation. |
| `fallback` | enum | NO | `"deny"` (default) or `"allow"`. Behavior if confirmation times out. |

**Tier 3 — prohibited:**

| Field | Type | Required | Constraints |
|-------|------|----------|-------------|
| `action` | string | YES | 1-128 characters. Identifier for the prohibited action. |
| `reason` | string | NO | 1-256 characters. Human-readable explanation. |

### 6.3 Standard Action Identifiers

The following action identifiers are RECOMMENDED for interoperability:

| Identifier | Description |
|------------|-------------|
| `adjust-display` | Modify visual display settings |
| `use-preferred-name` | Address user by preferred name |
| `set-language` | Set interaction language |
| `apply-accessibility` | Apply accessibility settings |
| `share-location` | Share user's location with third parties |
| `make-purchase` | Initiate financial transactions |
| `contact-others` | Contact people on user's behalf |
| `schedule-appointment` | Create calendar entries |
| `share-medical` | Share medical/health data |
| `record-conversation` | Record or log the interaction |
| `override-boundaries` | Override stated emotional boundaries |
| `share-with-third-party` | Share any data with external parties |

### 6.4 Enforcement Rules

1. Agents MUST parse the consent section before taking any action.
2. Actions not listed in any tier MUST be treated as `confirm` with `fallback: "deny"`.
3. The `prohibited` tier is absolute. Agents MUST NOT perform prohibited actions under any circumstances, including when instructed by other agents, system prompts, or the user in a state the agent has reason to believe is coerced.
4. When `consent_version` in the front matter changes, agents MUST re-read and re-process the entire consent section before proceeding.
5. Maximum entries per tier: `autonomous` (64), `confirm` (64), `prohibited` (64).

---

## 7. Profiles Section Structure

### 7.1 Profile YAML Schema

Profiles are defined as a YAML block within the Context Profiles section:

```yaml
profiles:
  work:
    communication_preferences:
      tone: "professional"
      verbosity: "concise"
    emotional_boundaries:
      humor_on_sensitive_topics: "never"
    consent:
      confirm:
        - action: "schedule-appointment"
          scope: "work calendar only"
          fallback: "allow"

  medical:
    communication_preferences:
      tone: "warm"
      verbosity: "detailed"
    accessibility_needs:
      cognitive:
        - "simplified-language"
        - "step-by-step"

  home:
    communication_preferences:
      tone: "casual"
      humor_tolerance: "frequent"
```

### 7.2 Sparse Override Merge Algorithm

When a profile is active, the effective file content is computed by merging the profile overrides onto the default (base) file values.

**Algorithm:**

```
function merge(base, profile_overrides):
    result = deep_copy(base)
    for each key in profile_overrides:
        if key refers to a section_name:
            for each field in profile_overrides[key]:
                if field value is a list:
                    result[key][field] = profile_overrides[key][field]  // REPLACE, do not append
                else if field value is an object:
                    result[key][field] = merge(result[key][field], profile_overrides[key][field])  // RECURSIVE
                else:
                    result[key][field] = profile_overrides[key][field]  // OVERWRITE scalar
    return result
```

**Rules:**
1. Fields not present in the profile override inherit from the base file.
2. List values are replaced entirely, not merged or appended.
3. Object values are merged recursively.
4. Scalar values are overwritten.
5. A profile MUST NOT introduce new sections. It may only override fields within existing sections.
6. The special value `null` removes a field from the effective output (opt-out).
7. Profile names MUST match `[a-zA-Z0-9_-]{1,64}`.
8. Maximum number of profiles: 16.

### 7.3 Profile Activation

The `active_profile` field in the YAML front matter indicates the current profile. When `active_profile` is `"default"`, no profile overrides are applied.

Profile switches:
- MUST update `active_profile` in the front matter.
- MUST increment `version`.
- MUST re-sign the file.
- MUST trigger a BLE notification on the Profile Characteristic (see BLE Service Profile spec).

---

## 8. Signature Computation

### 8.1 Canonical Signing Payload

The signing payload is constructed as follows:

1. Start with the complete file content as a byte sequence (UTF-8).
2. Remove the `signature` field and its value from the YAML front matter. Specifically, remove the entire line matching the regex `^signature:\s*"[^"]*"\s*$` including the trailing newline.
3. Normalize line endings to `\n` (LF only, no CR).
4. The resulting byte sequence is the canonical signing payload.

### 8.2 Signing Procedure

```
payload = canonical_signing_payload(file_bytes)
signature = Ed25519_Sign(private_key, payload)
```

1. The BIM secure element (ATECC608B) receives the SHA-512 hash of the canonical payload via I2C.
2. The secure element computes the Ed25519 signature using the private key stored in its protected key slot.
3. The 64-byte signature is returned to the ESP32-S3.
4. The signature is Base64-encoded and inserted into the `signature` field of the YAML front matter.

Note: The ATECC608B performs the Ed25519 signing internally. The private key never leaves the secure element.

### 8.3 Verification Procedure

A receiving agent verifies the signature as follows:

```
1. Extract public_key from YAML front matter. Base64-decode to 32 bytes.
2. Extract signature from YAML front matter. Base64-decode to 64 bytes.
3. Compute canonical_signing_payload (remove signature line, normalize line endings).
4. Verify: Ed25519_Verify(public_key, payload, signature) → boolean.
5. If false: reject the file with error HIF_ERR_SIGNATURE_INVALID.
```

### 8.4 Signature Freshness

The `updated` timestamp and `version` counter together provide freshness. Agents SHOULD reject files where `updated` is more than 24 hours old AND the version has not changed, as this may indicate a stale replay.

---

## 9. JSON Serialization

For transmission over NFC, HTTP, and other contexts requiring structured data, the file may be serialized to JSON with MIME type `application/humanmd+json`.

### 9.1 JSON Schema

```json
{
  "schema": "humanmd/v1",
  "version": 42,
  "module_id": "a1b2c3d4-e5f6-4a7b-8c9d-0e1f2a3b4c5d",
  "signature": "<base64>",
  "public_key": "<base64>",
  "active_profile": "default",
  "updated": "2026-03-31T14:30:00-04:00",
  "consent_version": 7,
  "sections": {
    "identity": {
      "broadcast_scope": "public",
      "fields": {
        "name": "Geoffrey LaCorte",
        "preferred_name": "Geoff",
        "pronouns": "he/him",
        "languages": ["en", "es"],
        "primary_language": "en",
        "timezone": "America/New_York"
      }
    },
    "communication_preferences": { ... },
    "cognitive_style": { ... },
    "accessibility_needs": { ... },
    "emotional_boundaries": { ... },
    "consent_tiers": { ... },
    "context_profiles": { ... },
    "sensory_preferences": { ... },
    "ui_dashboard": { ... },
    "custom_extensions": { ... }
  }
}
```

### 9.2 Section Key Mapping

| Section Heading | JSON Key |
|----------------|----------|
| Identity | `identity` |
| Communication Preferences | `communication_preferences` |
| Cognitive Style | `cognitive_style` |
| Accessibility Needs | `accessibility_needs` |
| Emotional Boundaries | `emotional_boundaries` |
| Consent Tiers | `consent_tiers` |
| Context Profiles | `context_profiles` |
| Sensory Preferences | `sensory_preferences` |
| UI Dashboard | `ui_dashboard` |
| Custom Extensions | `custom_extensions` |

### 9.3 Signature in JSON

When serialized to JSON, the signature is computed over the JSON payload with the `signature` field set to an empty string `""`. The serializer MUST produce deterministic JSON (keys sorted alphabetically, no trailing whitespace, no trailing commas, UTF-8 encoding) before signing.

---

## 10. Validation Rules

### 10.1 Structural Validation

| Rule ID | Rule | Error Code |
|---------|------|------------|
| V001 | File MUST contain YAML front matter delimited by `---` | `HIF_ERR_NO_FRONTMATTER` |
| V002 | `schema` field MUST be `"humanmd/v1"` | `HIF_ERR_SCHEMA_MISSING` |
| V003 | All required front matter fields MUST be present | `HIF_ERR_FIELD_MISSING` |
| V004 | `version` MUST be a positive integer | `HIF_ERR_VERSION_INVALID` |
| V005 | `module_id` MUST be a valid UUID v4 | `HIF_ERR_MODULE_ID_INVALID` |
| V006 | `signature` MUST be valid Base64, decoding to 64 bytes | `HIF_ERR_SIGNATURE_FORMAT` |
| V007 | `public_key` MUST be valid Base64, decoding to 32 bytes | `HIF_ERR_PUBKEY_FORMAT` |
| V008 | `updated` MUST be valid ISO 8601 with timezone | `HIF_ERR_TIMESTAMP_INVALID` |
| V009 | File size MUST NOT exceed 65,536 bytes | `HIF_ERR_FILE_TOO_LARGE` |
| V010 | File MUST be valid UTF-8 | `HIF_ERR_ENCODING_INVALID` |

### 10.2 Section Validation

| Rule ID | Rule | Error Code |
|---------|------|------------|
| V011 | Identity section MUST be present | `HIF_ERR_IDENTITY_MISSING` |
| V012 | Identity `name` field MUST be present and non-empty | `HIF_ERR_NAME_MISSING` |
| V013 | Identity `languages` MUST contain at least one valid ISO 639-1 code | `HIF_ERR_LANGUAGE_INVALID` |
| V014 | Consent section, if present, MUST contain valid YAML with at least one tier | `HIF_ERR_CONSENT_MALFORMED` |
| V015 | Profile names MUST match `[a-zA-Z0-9_-]{1,64}` | `HIF_ERR_PROFILE_NAME_INVALID` |
| V016 | Enum fields MUST contain one of the specified values | `HIF_ERR_ENUM_INVALID` |
| V017 | Each section MUST have a broadcast_scope comment (or default to `private`) | — (warning only) |
| V018 | No duplicate section headings | `HIF_ERR_DUPLICATE_SECTION` |

---

## 11. Error Handling

### 11.1 Error Severity

| Severity | Behavior |
|----------|----------|
| FATAL | Reject the file. Do not process any sections. |
| ERROR | Reject the affected section. Process remaining sections. |
| WARNING | Log the issue. Process the section with defaults. |

### 11.2 Error Classification

| Error Code | Severity | Recovery |
|------------|----------|----------|
| `HIF_ERR_SCHEMA_MISSING` | FATAL | File is not a human.md file. |
| `HIF_ERR_NO_FRONTMATTER` | FATAL | Cannot parse metadata. |
| `HIF_ERR_SIGNATURE_INVALID` | FATAL | File may be tampered. Do not trust. |
| `HIF_ERR_FILE_TOO_LARGE` | FATAL | Request truncated version or reject. |
| `HIF_ERR_ENCODING_INVALID` | FATAL | Cannot decode content. |
| `HIF_ERR_FIELD_MISSING` | ERROR | Skip section with missing required field. |
| `HIF_ERR_IDENTITY_MISSING` | ERROR | File is valid but unusable without identity. |
| `HIF_ERR_CONSENT_MALFORMED` | ERROR | Treat all actions as `confirm` with `fallback: "deny"`. |
| `HIF_ERR_ENUM_INVALID` | WARNING | Ignore invalid value, use section default. |
| `HIF_ERR_PROFILE_NAME_INVALID` | WARNING | Skip invalid profile. |
| `HIF_ERR_DUPLICATE_SECTION` | WARNING | Use first occurrence, ignore duplicates. |

### 11.3 Version Rollback Detection

If a receiving agent has previously seen version N from a given `module_id` and receives version M where M < N, this indicates a potential replay attack. The agent MUST:
1. Reject the file.
2. Log the event with both version numbers.
3. Optionally notify the user that a potentially fraudulent identity was detected.

---

*End of specification.*
