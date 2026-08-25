# PROVISIONAL PATENT APPLICATION

**Title:** BIOMETRIC-GATED PORTABLE IDENTITY MODULE AND PROTOCOL FOR HUMAN-AI COMMUNICATION

**Inventor:** Geoffrey LaCorte, Jersey City, NJ
**Assignee:** 816 Enterprises LLC
**Filing Date:** [TO BE DETERMINED]
**Docket No:** 816E-2026-001

---

## FIELD OF THE INVENTION

This invention relates to systems, methods, and devices for authenticating human identity and communicating personal identity information to artificial intelligence agents, autonomous systems, and digital infrastructure. More specifically, this invention relates to a biometric-gated portable identity module that cryptographically signs and broadcasts a human-authored identity file to nearby AI agents, robots, kiosks, smart environments, and compatible display surfaces, enabling the human to project their identity, preferences, consent boundaries, and personal user interface outward to any receiving system.

## BACKGROUND OF THE INVENTION

### The Problem: Inward Extraction vs. Outward Projection

The relationship between humans and AI systems is fundamentally asymmetric. Every interaction with an AI agent, autonomous robot, retail kiosk, or smart environment requires the human to repeatedly disclose personal information — name, preferences, accessibility needs, communication style, consent boundaries — through fragmented, inconsistent, and agent-controlled interfaces. Each system extracts this data independently, stores it in proprietary silos, and applies its own defaults when information is missing.

This extraction model creates three critical failures:

1. **No portable identity.** A human's preferences, accessibility needs, and consent rules do not travel with them. Every new environment starts from zero.

2. **No proof of authorship.** Software-only identity files can be generated, modified, or forged by any AI agent. There is no mechanism to prove that a particular identity file was authored by the human it claims to represent.

3. **No human agency.** The human is a passive data subject. They do not control what is shared, how it is interpreted, or when the interaction ends.

The present invention inverts this model entirely. Instead of inward extraction — where agents pull data from humans — this system enables **outward projection** — where humans broadcast their authenticated identity to agents on their own terms.

### Limitations of the Prior Art

**Software-Only Identity Files (me.md, me.txt, and similar projects):** Several recent projects propose markdown-based identity files that describe a person's preferences for AI consumption. These are purely software constructs with no authentication mechanism. Any AI agent could generate an identical file claiming to be any human. There is no tamper detection, no proof of human authorship, no hardware binding, and no cryptographic integrity. These systems operate exclusively through inward extraction — the agent reads a file the human placed on a server. Specifically, the me.md project (published approximately February 2026) is a conceptual website proposing AI-generated markdown identity files with no working implementation, no authentication protocol, and no hardware component. The me.txt/metxt.org project (published approximately March 2026) is a developer-focused CLI specification for plain-text identity files with no authentication, no biometric binding, and no broadcast capability.

**Biometric Authentication Wearables (Nymi Band, Token Ring/tokencore.com, and similar):** Existing biometric wearables use fingerprint or heartbeat sensors to authenticate a user for payment, access control, or device unlocking. These devices authenticate identity but do not broadcast identity information. They answer "is this person authorized?" but not "who is this person, what do they need, and how should I interact with them?" Token's product line includes wearable, portable, and embedded form factors with biometric authentication but focuses on access control and payment authorization — not on projecting structured identity files, personal preferences, consent boundaries, or user interfaces to AI agents or autonomous systems. None of these devices project a structured identity file or personal user interface to receiving systems.

**Corporate Identity Extraction Systems (Meta Patent US20250252700A1 and similar):** Large technology companies have patented methods for extracting user personality traits, behavioral patterns, and preferences from usage data — then applying those extracted profiles to personalize AI interactions. These systems operate in the opposite direction from the present invention: they extract identity from observed behavior rather than allowing humans to project identity on their own terms. The human has no control, no consent mechanism, and no ability to revoke or modify what was extracted. The Meta patent specifically extracts personality traits from user interactions to customize AI agent responses — a fundamentally inward extraction model.

**Digital Identity Standards (DID, Verifiable Credentials):** W3C Decentralized Identifiers and Verifiable Credentials provide cryptographic identity verification but are designed for machine-to-machine authentication, not for projecting human preferences, accessibility needs, emotional boundaries, and personal interfaces to AI agents. They verify "this credential was issued by this authority" but do not communicate "here is how to interact with me as a human."

### Summary of the Gap

No prior art system combines all of the following in a single integrated invention:

1. A human-readable, human-authored identity file in standard markdown format
2. Biometric authentication (fingerprint) binding the file to its human author
3. Cryptographic signing (Ed25519) ensuring tamper detection and integrity
4. A physical hardware module that stores, signs, and broadcasts the identity file
5. Form-factor agnosticism — the module functions in any physical form (pendant, ring, clip, USB-C stick, phone case)
6. Per-section privacy controls allowing granular broadcast scope (public, request-only, private, hidden)
7. Three-tier consent enforcement (autonomous, confirm, prohibited)
8. Context-switching profiles with sparse override model
9. UI projection — the module serves the human's personal dashboard to any compatible nearby screen
10. Proximity-based session management (session begins when module is detected, ends when module leaves)

The present invention is novel under 35 USC §102 because no single prior art reference anticipates this complete combination. The invention is non-obvious under 35 USC §103 because: (a) the data flow direction is inverted relative to all prior art (outward projection vs. inward extraction), (b) there is no motivation in the prior art to combine biometric authentication wearables with structured identity broadcasting and UI projection, and (c) the combination produces a new technical result — cryptographically verified human identity projection — that none of the individual prior art components achieve.

## SUMMARY OF THE INVENTION

The present invention is a biometric-gated portable identity module and communication protocol for human-AI interaction. The system comprises five integrated components:

**In a first aspect,** the invention provides a consumer authoring application that guides a human through creating a structured identity file (human.md) through conversational prompts. The application covers ten standardized sections: Identity, Communication Preferences, Cognitive Style, Accessibility Needs, Emotional Boundaries, Consent Tiers, Context Profiles, Sensory Preferences, UI Dashboard Configuration, and Custom Extensions. The application transfers the completed file to a physical module via secure Bluetooth connection after biometric authentication.

**In a second aspect,** the invention provides the human.md file format — a portable, human-readable markdown file with YAML front matter containing cryptographic signatures, module identifiers, and version metadata. The file uses standard markdown syntax readable by any text editor while containing structured sections parseable by AI agents. Each section carries an independent broadcast scope designation (public, request-only, private, hidden) enabling granular privacy control.

**In a third aspect,** the invention provides a biometric identity module (BIM) — a form-factor agnostic physical device containing a microcontroller (ESP32-S3), fingerprint sensor, skin contact sensor, secure element (ATECC608B), BLE 5.0 radio, NFC interface, and optional Wi-Fi Direct capability. The module stores the human.md file in encrypted flash memory, signs it with Ed25519 keys held in the secure element, and broadcasts it to nearby AI agents only after fingerprint authentication confirms the human owner's presence.

**In a fourth aspect,** the invention provides a cryptographic trust chain ensuring end-to-end verified identity: Human (fingerprint biometric) → Authenticated Application → Signed Identity File (Ed25519) → Physical Module (hardware attestation) → AI Agent (signature verification and consent enforcement). Each link in the chain provides a specific security property that software-only systems cannot achieve.

**In a fifth aspect,** the invention provides a UI projection capability wherein the biometric identity module serves the human's personal dashboard — defined in the UI Dashboard section of the human.md file — to any compatible nearby screen via Wi-Fi Direct or BLE connection. The projected interface runs in a sandboxed rendering context on the receiving display, persists only while the module remains in proximity, and leaves zero data on the host device after the session ends.

## BRIEF DESCRIPTION OF THE DRAWINGS

- **FIG. 1** — System architecture overview showing five components and their relationships
- **FIG. 2** — Consumer authoring application user flow
- **FIG. 3** — Human.md file schema diagram with YAML front matter and ten sections
- **FIG. 4** — Biometric Identity Module (BIM) hardware block diagram
- **FIG. 5** — BLE and NFC broadcast sequence diagram
- **FIG. 6** — AI agent configuration and consent enforcement flow
- **FIG. 7** — Context-switching sequence diagram
- **FIG. 8** — Consent tier enforcement diagram
- **FIG. 9** — Cryptographic trust chain diagram
- **FIG. 10** — UI projection session sequence diagram
- **FIG. 11** — Biometric authentication flow diagram

## DETAILED DESCRIPTION OF THE INVENTION

### Overview

The present invention provides a complete system for human identity projection to AI agents and autonomous systems. The system comprises five integrated components: (1) a consumer authoring application, (2) a structured identity file format (human.md), (3) a biometric identity module (BIM), (4) a cryptographic trust chain, and (5) a UI projection capability. These components work together to enable any human to create, authenticate, sign, store, broadcast, and project their identity to any receiving AI system.

### Component 1: Consumer Authoring Application

#### 1.1 Purpose and Design Philosophy

The authoring application is a guided, conversational interface — available as a mobile application, web application, or desktop application — that walks a human through creating their personal human.md identity file. The application uses plain language prompts rather than technical form fields, making it accessible to non-technical users.

The application does not store the human.md file on any server. The file is generated locally on the user's device and transferred to the biometric identity module via encrypted Bluetooth connection after biometric authentication. This ensures the human's identity data never leaves their physical control.

#### 1.2 Ten Authoring Sections

The application guides the human through ten standardized sections:

**Section 1 — Identity:** Name, preferred name, pronouns, languages spoken, timezone, cultural context.

**Section 2 — Communication Preferences:** Preferred tone (formal, casual, direct), verbosity level, humor tolerance, response format preferences (bullets, paragraphs, step-by-step).

**Section 3 — Cognitive Style:** Learning modality (visual, auditory, kinesthetic), detail preference (overview first vs. detail first), example preference, abstraction comfort level.

**Section 4 — Accessibility Needs:** Visual (screen reader, high contrast, large text), auditory (captions, volume limits), motor (voice control, switch access, dwell timing), cognitive (simplified language, reduced cognitive load, consistent layout).

**Section 5 — Emotional Boundaries:** Topics to avoid, emotional triggers, safe topics, de-escalation preferences, comfort with humor about sensitive topics.

**Section 6 — Consent Tiers:** Three-tier consent model defining what AI agents may do autonomously, what requires confirmation, and what is absolutely prohibited. Each tier contains enumerated action categories.

**Section 7 — Context Profiles:** Named profiles (e.g., Work, Medical, Home, Travel) using a sparse override model — each profile only specifies what differs from the default, inheriting all other values.

**Section 8 — Sensory Preferences:** Light sensitivity, sound sensitivity, temperature preferences, haptic feedback preferences, animation/motion sensitivity.

**Section 9 — UI Dashboard Configuration:** Layout, widgets, color scheme, and tools for the user's personal interface that will be projected to compatible screens.

**Section 10 — Custom Extensions:** User-defined key-value pairs for domain-specific needs not covered by the standard sections.

#### 1.3 Privacy Controls

For each section, the application allows the human to set a broadcast scope:

- **Public:** Always included in broadcasts to any receiving agent.
- **Request-only:** Not broadcast by default; the receiving agent must request access, and the module prompts the human for approval.
- **Private:** Encrypted in storage; only accessible when the module is connected to the companion app.
- **Hidden:** Never leaves the module under any circumstances; exists only for the human's personal reference.

#### 1.4 File Generation and Transfer

Upon completing all sections, the application generates a valid human.md file with YAML front matter and markdown body. The file is transferred to the biometric identity module via BLE Secure Connection (LE Secure Connections with ECDH key exchange) after the module confirms fingerprint authentication. The application then deletes the local copy, ensuring the only persistent copy exists on the module.

#### 1.5 Version Control

Each edit to the human.md file increments a version counter in the YAML front matter. The application maintains a local audit log (encrypted, on-device only) of all changes with timestamps, enabling the human to review their edit history and revert if needed.

### Component 2: human.md File Format

#### 2.1 Format Overview

The human.md file uses standard markdown syntax with YAML front matter delimited by triple-dash (`---`) boundaries. This format was chosen for three reasons:

1. **Human-readable:** Any human can open and read the file in a standard text editor.
2. **Machine-parseable:** Standard YAML and markdown parsers in every programming language can extract structured data.
3. **Familiar format:** Markdown is widely understood by developers, increasingly by general users, and natively supported by AI language models.

The file is identified not by a proprietary extension but by its YAML front matter content — specifically the `schema: "humanmd/v1"` field. The file uses the standard `.md` extension. The schema identifier in the front matter is authoritative for type detection.

#### 2.2 YAML Front Matter

The front matter contains the following metadata fields:

- **version:** Incrementing version number for this file instance.
- **schema:** Schema identifier (`humanmd/v1`) enabling any parser to recognize the file as a human.md identity file.
- **signature:** Ed25519 digital signature computed over the file body (excluding the signature field itself) using the private key stored in the module's secure element.
- **module_id:** Unique identifier of the biometric identity module that signed this file.
- **active_profile:** Currently active context profile name.
- **updated:** ISO 8601 timestamp of last modification.
- **public_key:** The Ed25519 public key corresponding to the signing key, enabling any receiver to verify the signature without contacting a central authority.
- **consent_version:** Monotonically increasing counter tracking consent section changes, enabling agents to detect consent updates.

#### 2.3 Section Schema

Each of the ten sections follows a consistent structure with a section heading, a machine-readable broadcast_scope comment (invisible when rendered as standard markdown), and section content in standard markdown. This allows the same file to be simultaneously human-readable (when opened in a text editor or markdown viewer) and machine-parseable (when processed by an AI agent's parser).

#### 2.4 Consent Section Structure

The Consent section uses a three-tier model:

- **Autonomous:** Actions the AI agent may perform freely without prompting the human. Examples: adjust text size, use preferred name, set language, apply accessibility settings.
- **Confirm:** Actions the AI agent must ask the human about before proceeding. Examples: share location, make purchases, contact others, schedule appointments.
- **Prohibited:** Actions the AI agent must never perform under any circumstances. Examples: share medical data with third parties, record conversations, override emotional boundaries.

AI agents receiving this section MUST enforce these tiers. The consent_version counter ensures agents detect and re-read consent changes.

#### 2.5 Profiles Section Structure

The Profiles section uses a sparse override model. Each named profile (e.g., Work, Medical, Home) only specifies the fields that differ from the default file. All unspecified fields are inherited from the default. This minimizes redundancy and ensures that changes to the default automatically propagate to all profiles unless explicitly overridden.

Profiles can be switched via hardware button press on the module, companion app command, or time-based rules.

#### 2.6 Portability and Openness

The human.md format is designed to be an open standard. Any developer, company, or AI system can implement a parser. The file contains no proprietary encoding, no binary sections, and no vendor-specific extensions in the standard sections. The Custom section (Section 10) allows vendor-specific extensions without polluting the standard schema.

#### 2.7 Privacy Architecture

The broadcast_scope mechanism operates at the section level, not the file level. This enables a human to make their Identity and Accessibility sections public while keeping their Emotional Boundaries private and their Medical profile request-only. This granularity is enforced by the biometric identity module's firmware, not by the receiving agent — ensuring privacy is controlled by the human, not by the system they are interacting with.

### Component 3: Biometric Identity Module (BIM)

#### 3.1 Hardware Overview

The biometric identity module is a form-factor agnostic device. The invention claims the functional combination of components regardless of physical form. Specific embodiments include: pendant (worn on chain or lanyard), ring (worn on finger), clip/badge (attached to clothing), USB-C stick (plugged into devices), and phone case (integrated with smartphone). All embodiments share a common hardware core.

#### 3.2 Common Hardware Core

The module contains the following components:

**Microcontroller:** ESP32-S3 (Espressif Systems) — dual-core Xtensa LX7 processor, 240 MHz, integrated Wi-Fi 802.11 b/g/n and Bluetooth 5.0 (LE), 512 KB SRAM, USB OTG support.

**Secure Element:** ATECC608B (Microchip Technology) — hardware-protected key storage, Ed25519 key pair generation and signing, tamper-resistant design, I2C interface. The private key is generated on the secure element and never leaves it. Signing operations occur on the secure element itself.

**Fingerprint Sensor:** Capacitive fingerprint sensor with liveness detection. Template storage on the secure element (not in main flash). Match-on-device architecture — fingerprint data never leaves the module.

**Skin Contact Sensor:** Capacitive proximity sensor on the module's body-facing surface. Detects whether the module is being worn or held by a human. When skin contact is lost, the module enters a locked state and ceases all broadcasting until fingerprint re-authentication.

**NFC Interface:** NFC Forum Type 4 Tag with NDEF message capability. Enables tap-to-share identity transfer to NFC-enabled devices.

**BLE 5.0 Radio:** Integrated in the ESP32-S3. Supports BLE advertising with custom manufacturer-specific data, GATT server for full file transfer, and BLE Secure Connections for encrypted transfer from the companion app.

**Wi-Fi Direct (Optional):** Used for UI projection to compatible displays. The module operates as a Wi-Fi Direct Group Owner, serving the human's dashboard interface to connecting displays.

**Storage:** 4 MB SPI flash (minimum) for encrypted human.md file storage, firmware, and profile data.

**Power:** Rechargeable LiPo battery with USB-C charging. Target: 7-day continuous BLE operation, 4-hour Wi-Fi Direct UI projection, 30-day standby.

**User Interface:** Single hardware button for profile switching (short press cycles profiles, long press enters pairing mode), LED indicator for status (color-coded by active profile).

#### 3.3 Biometric Authentication Flow

1. Human touches the fingerprint sensor.
2. Sensor captures fingerprint image and performs liveness check (capacitance pattern analysis, not optical replay).
3. Fingerprint template is matched against enrolled template stored on the secure element.
4. If match succeeds: module enters authenticated state, begins BLE advertising, enables NFC responses.
5. If match fails: module remains locked, increments failure counter, applies rate limiting after 5 consecutive failures.
6. Skin contact sensor continuously monitors: if contact lost for more than 30 seconds, module re-locks and requires fingerprint re-authentication.

#### 3.4 BLE Broadcast Protocol

In the authenticated state, the module broadcasts a BLE advertisement containing:

- **Manufacturer-Specific Data:** A compact advertisement payload containing the module UUID, active profile name, schema version, and a hash of the public sections (enabling agents to detect changes without full file transfer).

The module also exposes a GATT service with characteristics for:
- Full file transfer (public and request-only sections, filtered by broadcast_scope)
- Single section access by name
- Active profile name with change notifications
- Consent version counter with change notifications
- Ed25519 signature for verification
- Public key for signature verification

#### 3.5 NFC Protocol

When an NFC-enabled device taps the module, it reads an NDEF message containing a JSON representation of the public sections of the human.md file, including the signature, public key, and module ID. The NDEF record uses MIME type `application/humanmd+json`. The NFC payload is a compact subset optimized for single-tap transfer (approximately 4 KB maximum).

#### 3.6 Hardware Attestation

The module supports a hardware attestation protocol allowing an AI agent to verify that the module is genuine hardware (not a software emulator). The secure element generates an attestation certificate during manufacturing, signed by a device attestation key. This certificate can be verified by any agent to confirm the module is authentic hardware with a genuine secure element.

#### 3.7 Form Factor Embodiments

**Pendant:** Enclosed in a circular or rectangular housing (approximately 35mm x 35mm x 8mm), worn on a chain or lanyard. Fingerprint sensor on the front face. Skin contact sensor on the back face. NFC antenna in the housing perimeter.

**Ring:** Enclosed in a band-style housing. Capacitive touch authentication (simplified biometric due to form factor constraints). Skin contact sensor on inner band. BLE only (no NFC due to size constraints).

**Clip/Badge:** Rectangular housing (approximately 45mm x 30mm x 6mm) with spring clip or magnetic attachment. Fingerprint sensor on front face. NFC antenna in housing. Enterprise-friendly form factor.

**USB-C Stick:** USB-C connector form factor (approximately 50mm x 18mm x 8mm). Fingerprint sensor on the cap or body. Plugs directly into computers, kiosks, and displays. BLE broadcasting when unplugged. Powers from host USB when plugged in.

**Phone Case:** Integrated into a smartphone protective case. Fingerprint sensor on the rear surface. Uses the phone's BLE/Wi-Fi radios when paired. NFC passthrough to the phone's NFC hardware.

#### 3.8 Power Management

The module implements aggressive power management: deep sleep when skin contact is lost, BLE advertising at 1-second intervals in normal mode (100ms during active connection), Wi-Fi Direct activated only for UI projection sessions and powered down after session ends.

### Component 4: Cryptographic Trust Chain

#### 4.1 Trust Chain Overview

The present invention establishes a five-link cryptographic trust chain:

Human (fingerprint) → Authenticated App → Signed File (Ed25519) → Physical Module (attestation) → AI Agent (verification)

Each link provides a specific security guarantee:

#### 4.2 Link 1: Human → App (Biometric Binding)

The fingerprint sensor ensures that only the enrolled human can create, modify, or authorize broadcasting of the identity file. The fingerprint template is stored on the secure element and matched on-device. No biometric data leaves the module. This link guarantees: the file was authored by the claimed human, not by an AI agent or third party.

#### 4.3 Link 2: App → Signed File (Cryptographic Integrity)

When the companion application transfers the human.md file to the module, the module's secure element computes an Ed25519 signature over the file body. Any modification to the file after signing invalidates the signature. This link guarantees: the file has not been tampered with since the human authored it.

#### 4.4 Link 3: Signed File → Physical Module (Hardware Binding)

The Ed25519 private key exists only inside the secure element of a specific physical module. The key cannot be extracted, cloned, or transferred. The signature proves the file was signed by a specific physical device, not by software running on a general-purpose computer. This link guarantees: the identity is bound to a specific physical object the human possesses.

#### 4.5 Link 4: Physical Module → AI Agent (Hardware Attestation)

The module's secure element contains a device attestation certificate generated during manufacturing. An AI agent can verify this certificate against the device attestation root of trust, proving the module is genuine hardware. This link guarantees: the broadcasting device is authentic hardware, not a forgery.

#### 4.6 Link 5: AI Agent Verification and Enforcement

The receiving AI agent performs verification: read the public key, verify the file signature, verify the hardware attestation certificate, parse the consent section and enforce the three-tier model, respect broadcast_scope designations, and monitor for consent version changes. If any verification step fails, the agent MUST NOT apply the identity file.

#### 4.7 Key Revocation

If a module is lost or stolen, the human can revoke the module's key pair through the companion application. Revocation is published to an optional decentralized revocation list (append-only, publicly verifiable). AI agents checking the revocation list will reject files signed by revoked keys. Agents that do not check the list still benefit from the skin contact sensor lockout — a stolen module without the owner's fingerprint cannot broadcast.

### Component 5: UI Projection

#### 5.1 Overview

The UI projection capability enables the biometric identity module to serve the human's personal dashboard interface to any compatible nearby screen. This transforms any display — a retail kiosk, a hotel room TV, an office monitor, a smart home panel — into the human's personal screen, running their own interface, with their own tools and layout.

#### 5.2 Dashboard Definition

The UI Dashboard section (Section 9) of the human.md file defines the personal interface in two possible formats:

**Format A — JSON Schema:** A declarative specification of widgets, layout, and data bindings. The receiving display renders the dashboard according to the schema using its own rendering engine.

**Format B — HTML/CSS/JS Bundle:** A self-contained web application (maximum 256 KB) served directly by the module. The receiving display renders it in a sandboxed iframe or webview.

#### 5.3 Projection Protocol

1. Human approaches a compatible display (within BLE range).
2. Display detects the module's BLE advertisement and sends a UI projection request.
3. Module prompts the human for approval (LED flash pattern or companion app notification).
4. Upon approval, the module activates Wi-Fi Direct and establishes a peer-to-peer connection.
5. Module serves the dashboard content over HTTP on the Wi-Fi Direct link.
6. Display renders the dashboard in a sandboxed context (no access to host filesystem, network, or other applications).
7. Session persists while the module remains within Wi-Fi Direct range.
8. When the module moves out of range, the session terminates immediately.
9. Display purges all cached content — zero persistence.

#### 5.4 Security Properties

- **Sandboxed rendering:** The dashboard runs in an isolated context with no access to the host display's operating system, filesystem, or network.
- **Proximity-bound session:** The session exists only while the module is in range. Walking away terminates the session.
- **Zero persistence:** No data, cookies, local storage, or cached content remains on the host display after session termination.
- **Consent-gated:** The human must explicitly approve each projection session.
- **Content integrity:** The dashboard content is signed along with the rest of the human.md file.

### System Integration: How Five Components Work Together

#### 6.1 Initial Setup Flow

1. Human downloads the companion application.
2. Human unboxes the biometric identity module and enrolls their fingerprint (module guides through enrollment via LED patterns).
3. Human completes the guided authoring process in the app, creating their human.md file.
4. Human authenticates on the module (fingerprint), and the app transfers the file via BLE Secure Connection.
5. Module's secure element signs the file. Setup is complete.

#### 6.2 Standard Encounter Flow (BLE)

1. Human enters an environment with AI agents (store, hospital, smart home, airport).
2. Module broadcasts BLE advertisements (authenticated state, skin contact maintained).
3. AI agent detects the advertisement, reads the module UUID and public section hash.
4. Agent connects via GATT and reads the full human.md file (public sections).
5. Agent verifies the Ed25519 signature against the embedded public key.
6. Agent verifies the hardware attestation certificate.
7. Agent configures its behavior according to the human's preferences and consent tiers.
8. Interaction proceeds with the human's identity and rules applied.

#### 6.3 NFC Tap Flow

1. Human taps the module against an NFC-enabled kiosk or device.
2. Device reads the NDEF record containing the compact JSON representation of public sections.
3. Device verifies the signature.
4. Device configures its interface and behavior accordingly.

#### 6.4 Context-Switching Flow

1. Human presses the hardware button on the module (short press).
2. Module cycles to the next profile.
3. LED changes color to indicate the active profile.
4. Module sends a BLE notification to all connected agents with the new profile name.
5. Agents reconfigure based on the new profile's overrides.

#### 6.5 UI Projection Flow

1. Human approaches a compatible display surface.
2. Module detects the display's BLE beacon indicating projection capability.
3. Module prompts the human for approval.
4. Human approves (button press or app confirmation).
5. Module activates Wi-Fi Direct, establishes connection, serves dashboard content.
6. Display renders in sandboxed context.
7. Human walks away — session terminates — display purges all content.

#### 6.6 File Update Flow

1. Human opens the companion app and modifies a section.
2. App increments the version counter (and consent_version if consent rules changed).
3. Human authenticates on the module (fingerprint).
4. App transfers the updated file via BLE Secure Connection.
5. Module's secure element re-signs the file.
6. Connected agents receive notification of the change.

## USE CASES

### Use Case 1: Medical Emergency

A human wearing a pendant-form BIM enters an emergency room while unconscious. The pendant's skin contact sensor maintains the authenticated state. The ER's triage system detects the BLE advertisement and reads the Medical profile's public sections: blood type, allergies, current medications, emergency contacts, accessibility needs. The system verifies the signature to ensure data authenticity. Treatment begins with critical information immediately available.

### Use Case 2: Retail Environment

A human enters a retail store. Their clip-form BIM broadcasts to the store's kiosks. The kiosk reads the human's language preference, accessibility needs, and dietary restrictions. The kiosk adapts its interface. The consent section specifies `prohibited: share_with_third_parties`. When the human approaches a projection-capable screen, their personal shopping list appears.

### Use Case 3: Autonomous Robot Interaction

A delivery robot approaches a human's front door. The robot detects the BIM broadcast: preferred name, volume preference, movement sensitivity, consent rules for delivery actions. The robot adapts its interaction. Without the BIM, the robot uses generic defaults.

### Use Case 4: Smart Home

A family, each with their own BIM, enters their smart home. Each person's sensory preferences are read: Person A prefers warm lighting; Person B prefers cool lighting. Person C's consent section prohibits voice recording — microphones in their detected rooms are disabled. Guest visitors without BIMs receive default settings.

### Use Case 5: Airport Navigation

A traveler's BIM broadcasts language (Japanese), accessibility needs (wheelchair access), and dietary restrictions (halal). Every kiosk switches to Japanese, highlights wheelchair routes, filters food to halal options. At the gate, they project their dashboard with boarding pass and connecting flight information.

### Use Case 6: Workspace

A professional's USB-C form BIM is plugged into their laptop. Work profile active: formal tone, organization affiliation visible, limited personal data. They project their dashboard to a conference room screen. When they unplug and leave, the screen clears. Button press switches to Home profile before heading home.

## ABSTRACT

A biometric-gated portable identity module and communication protocol for human-AI interaction. The system comprises a consumer authoring application for creating a structured identity file (human.md) in standard markdown format with YAML front matter; a biometric identity module (BIM) that authenticates the human owner via fingerprint sensor, stores the identity file in a secure element, and cryptographically signs it with Ed25519; a Bluetooth Low Energy and NFC broadcast protocol for transmitting the signed identity file to nearby AI agents, robots, kiosks, and smart environments; a five-link cryptographic trust chain providing end-to-end verified identity from human biometric to AI agent; and a UI projection capability enabling the module to serve the human's personal dashboard to any compatible nearby display surface with proximity-based session management and zero data persistence. The module is form-factor agnostic, functioning as a pendant, ring, clip, USB-C stick, or phone case. The system inverts the conventional AI interaction model from inward extraction to outward projection — enabling humans to broadcast their authenticated identity, preferences, accessibility needs, consent boundaries, and personal interface to any receiving system on their own terms.
