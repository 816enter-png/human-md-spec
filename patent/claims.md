# PATENT CLAIMS
## Biometric-Gated Portable Identity Module and Protocol for Human-AI Communication

**Inventor:** Geoffrey LaCorte
**Assignee:** 816 Enterprises LLC
**Date:** 2026-03-31

> **Note:** These claims are drafted for a provisional patent application to establish the earliest possible priority date. For the non-provisional filing, a registered patent attorney or agent should refine the claim language to meet formal requirements under 35 USC §112.

---

## INDEPENDENT CLAIMS

### Claim 1 — System Claim (Five-Component Integration)

A system for human identity projection to artificial intelligence agents, the system comprising:

(a) a consumer authoring application configured to guide a human user through creating a structured identity file comprising ten standardized sections including identity, communication preferences, cognitive style, accessibility needs, emotional boundaries, consent tiers, context profiles, sensory preferences, user interface dashboard configuration, and custom extensions, wherein each section includes a broadcast scope designation selected from the group consisting of public, request-only, private, and hidden;

(b) a biometric identity module comprising a microcontroller, a fingerprint sensor, a skin contact sensor, a secure element, a Bluetooth Low Energy radio, and a near-field communication interface, wherein the biometric identity module is form-factor agnostic and configured to operate in any of a plurality of physical embodiments;

(c) the biometric identity module further configured to authenticate the human user via the fingerprint sensor prior to any broadcasting of the identity file, to sign the identity file using an Ed25519 key pair stored in the secure element, and to broadcast the signed identity file to nearby artificial intelligence agents via Bluetooth Low Energy advertising and near-field communication;

(d) a cryptographic trust chain comprising five links: human biometric authentication, authenticated application transfer, Ed25519 file signature, hardware attestation of the biometric identity module, and AI agent signature verification; and

(e) a user interface projection capability wherein the biometric identity module serves a personal dashboard defined in the identity file to a compatible nearby display via Wi-Fi Direct or Bluetooth Low Energy, the dashboard rendered in a sandboxed context on the display with proximity-based session management and zero data persistence after session termination.

### Claim 2 — Biometric Identity Module Device Claim

A biometric identity module for broadcasting authenticated human identity to artificial intelligence agents, the module comprising:

a microcontroller with integrated Bluetooth Low Energy and Wi-Fi capability;
a capacitive fingerprint sensor with liveness detection;
a skin contact sensor configured to detect continuous physical contact with a human body;
a secure element configured to generate and store an Ed25519 key pair, wherein the private key never leaves the secure element;
a near-field communication interface;
non-volatile memory storing an encrypted identity file;

wherein the module is configured to:
enter an authenticated state only upon successful fingerprint match;
broadcast a Bluetooth Low Energy advertisement containing a module identifier, active profile name, and public section hash only in the authenticated state;
re-lock and cease broadcasting when the skin contact sensor detects loss of contact for a predetermined period;
serve the identity file contents via a GATT service with section-level access control based on broadcast scope designations;
sign the identity file using the Ed25519 key pair on the secure element; and
provide a hardware attestation certificate verifiable by receiving agents.

### Claim 3 — Method of Broadcasting Authenticated Identity

A method of broadcasting authenticated human identity information to artificial intelligence agents, the method comprising:

authenticating a human user via a fingerprint sensor on a portable biometric identity module;
verifying continuous skin contact between the module and the human user via a skin contact sensor;
broadcasting a Bluetooth Low Energy advertisement containing a module identifier and public section hash;
upon connection from an artificial intelligence agent, serving a signed identity file via a GATT service, wherein the identity file is signed with an Ed25519 key pair stored in a secure element of the module;
filtering served content based on per-section broadcast scope designations such that private and hidden sections are not transmitted;
providing a hardware attestation certificate to the connecting agent for device authenticity verification; and
ceasing all broadcasting upon loss of skin contact for a predetermined period until fingerprint re-authentication.

### Claim 4 — Method of Authoring Identity File via Guided Application

A method of authoring a human identity file, the method comprising:

presenting a guided conversational interface to a human user comprising ten sequential authoring sections;
for each section, receiving human-authored content and a broadcast scope designation;
generating a structured identity file in markdown format with YAML front matter containing schema identifier, version number, and active profile designation;
establishing a secure Bluetooth connection with a biometric identity module after fingerprint authentication on the module;
transferring the identity file to the biometric identity module via the secure Bluetooth connection;
the biometric identity module signing the identity file using an Ed25519 key pair stored in a secure element; and
deleting the local copy of the identity file from the authoring device after successful transfer.

### Claim 5 — Method of AI Agent Configuration Based on Received Identity

A method of configuring an artificial intelligence agent based on a received human identity file, the method comprising:

detecting a Bluetooth Low Energy advertisement from a biometric identity module;
connecting to the module and reading a signed identity file via a GATT service;
verifying the Ed25519 signature of the identity file against a public key provided by the module;
verifying a hardware attestation certificate provided by the module;
parsing a consent section of the identity file to extract three tiers: autonomous actions, confirm-required actions, and prohibited actions;
configuring agent behavior according to the human's communication preferences, accessibility needs, cognitive style, and sensory preferences;
enforcing the consent tiers such that autonomous actions are performed freely, confirm-required actions prompt the human before execution, and prohibited actions are never performed; and
monitoring a consent version characteristic on the module for changes and re-reading the consent section when the version increments.

### Claim 6 — Method of Context-Profile Switching

A method of switching context profiles on a biometric identity module, the method comprising:

receiving a profile switch input selected from the group consisting of a hardware button press on the module, a command from a companion application, and a time-based rule trigger;
loading a named profile that specifies override values for a subset of sections of the identity file, all unspecified sections inheriting values from a default profile;
updating an active profile designation in the identity file YAML front matter;
re-signing the identity file with the Ed25519 key pair on the secure element;
changing a visual indicator on the module to a color corresponding to the activated profile;
transmitting a Bluetooth Low Energy notification to all connected artificial intelligence agents indicating the profile change; and
the connected agents reconfiguring their behavior based on the new profile's override values.

### Claim 7 — Biometric-Gated Identity Projection (Hardware + Crypto)

A method of projecting authenticated human identity to an artificial intelligence system, the method comprising:

authenticating a human user via a biometric sensor on a portable module;
upon authentication, retrieving a stored identity file from encrypted non-volatile memory on the module;
computing a digital signature over the identity file using a private key stored in a hardware secure element of the module, wherein the private key is generated on and cannot be extracted from the secure element;
broadcasting the signed identity file to one or more nearby artificial intelligence agents via a wireless communication protocol;
the receiving agent verifying the digital signature using a corresponding public key;
the receiving agent verifying a hardware attestation certificate proving the module is genuine hardware; and
the receiving agent configuring its behavior according to preference, accessibility, and consent information contained in the verified identity file.

### Claim 8 — UI Projection to Compatible Display

A method of projecting a personal user interface to a display device, the method comprising:

detecting, via a biometric identity module, a compatible display device advertising UI projection capability via Bluetooth Low Energy;
prompting the human user of the module for projection approval;
upon approval, establishing a Wi-Fi Direct peer-to-peer connection between the module and the display device;
serving a personal dashboard defined in a UI Dashboard section of a signed identity file stored on the module, the dashboard served over HTTP on the Wi-Fi Direct connection;
the display device rendering the dashboard in a sandboxed execution context with no access to the display device's operating system, filesystem, or network;
maintaining the projection session only while the module remains within wireless range of the display device; and
upon the module moving out of range or the human deactivating projection, terminating the session and purging all cached dashboard content from the display device such that zero data persists.

### Claim 9 — Guided Authoring Application with Module Transfer

A computer-implemented system for authoring a human identity file, the system comprising:

a processor executing a guided authoring application presenting ten sequential authoring sections through a conversational interface;
a Bluetooth Low Energy transceiver configured to establish a secure connection with a biometric identity module;
the application configured to generate a structured identity file in markdown format with YAML front matter upon completion of the authoring sections;
the application further configured to verify fingerprint authentication on the biometric identity module prior to file transfer;
the application configured to transfer the identity file to the module via the secure Bluetooth connection and delete the local copy upon successful transfer;

wherein the biometric identity module signs the transferred identity file using an Ed25519 key pair stored in a hardware secure element.

---

## DEPENDENT CLAIMS

### Claims Dependent on Claim 1 (System)

**Claim 10.** The system of Claim 1, wherein the broadcast scope designations are enforced by firmware on the biometric identity module such that sections designated as private or hidden are never transmitted to any external device regardless of requests from artificial intelligence agents.

**Claim 11.** The system of Claim 1, wherein the consent tiers comprise three levels: autonomous actions that the artificial intelligence agent may perform without human prompting, confirm-required actions that require explicit human approval before execution, and prohibited actions that must never be performed under any circumstances.

**Claim 12.** The system of Claim 1, wherein the identity file uses standard markdown syntax identified by a schema field in the YAML front matter rather than a proprietary file extension.

**Claim 13.** The system of Claim 1, further comprising a key revocation mechanism wherein the human user can revoke the Ed25519 key pair associated with a lost or stolen module through the companion application, the revocation published to a decentralized append-only revocation list verifiable by artificial intelligence agents.

### Claims Dependent on Claim 2 (Module Device)

**Claim 14.** The module of Claim 2, wherein the module is embodied as a pendant configured to be worn on a chain or lanyard, comprising a fingerprint sensor on a front face and a skin contact sensor on a back face.

**Claim 15.** The module of Claim 2, wherein the module is embodied as a ring comprising a capacitive touch authentication surface on an outer band and a skin contact sensor on an inner band.

**Claim 16.** The module of Claim 2, wherein the module is embodied as a clip or badge comprising a spring clip or magnetic attachment mechanism, a fingerprint sensor on a front face, and a near-field communication antenna in the housing.

**Claim 17.** The module of Claim 2, wherein the module is embodied as a USB-C stick comprising a USB-C connector for direct connection to host devices, a fingerprint sensor on a body or cap surface, and configured to broadcast via Bluetooth Low Energy when not physically connected and to transfer data via USB when physically connected.

**Claim 18.** The module of Claim 2, wherein the module is embodied as a phone case comprising a fingerprint sensor on a rear surface and configured to use a paired smartphone's Bluetooth and Wi-Fi radios for broadcasting and UI projection.

**Claim 19.** The module of Claim 2, further comprising a hardware button configured to cycle through named context profiles upon short press and to enter pairing mode upon long press, and an LED indicator configured to display a color corresponding to the active profile.

**Claim 20.** The module of Claim 2, wherein the predetermined period for skin contact loss before re-locking is approximately 30 seconds.

**Claim 21.** The module of Claim 2, wherein the fingerprint sensor implements rate limiting after a predetermined number of consecutive failed authentication attempts.

### Claims Dependent on Claim 3 (Broadcast Method)

**Claim 22.** The method of Claim 3, wherein the identity file comprises a consent section structured as three tiers: autonomous, confirm, and prohibited, and the method further comprises providing the consent section to the connecting agent with a monotonically increasing consent version counter enabling the agent to detect consent updates.

**Claim 23.** The method of Claim 3, wherein serving the signed identity file via the GATT service comprises providing individual section access by section name in addition to full file access.

**Claim 24.** The method of Claim 3, further comprising transmitting a Bluetooth Low Energy notification to connected agents when the active context profile changes on the module.

### Claims Dependent on Claim 5 (AI Agent Configuration)

**Claim 25.** The method of Claim 5, further comprising rejecting the identity file and notifying the human user if any verification step — signature verification or hardware attestation verification — fails.

**Claim 26.** The method of Claim 5, further comprising checking a key revocation list before applying the identity file and rejecting files signed by revoked key pairs.

**Claim 27.** The method of Claim 5, further comprising detecting a profile change notification from the module and reconfiguring agent behavior based on the new profile's sparse override values merged with the default profile.

### Claims Dependent on Claim 7 (Biometric-Gated Projection)

**Claim 28.** The method of Claim 7, wherein the biometric sensor is a capacitive fingerprint sensor with liveness detection and the authentication comprises matching a captured fingerprint template against an enrolled template stored in the hardware secure element.

**Claim 29.** The method of Claim 7, wherein the hardware secure element is an ATECC608B or equivalent tamper-resistant cryptographic element.

**Claim 30.** The method of Claim 7, further comprising locking the module and ceasing all broadcasting upon detection by a skin contact sensor that the module has lost physical contact with the human user for a predetermined period.

### Claims Dependent on Claim 8 (UI Projection)

**Claim 31.** The method of Claim 8, wherein the personal dashboard is defined in one of two formats: a declarative JSON schema specifying widgets and layout, or a self-contained HTML/CSS/JS bundle not exceeding 256 kilobytes.

**Claim 32.** The method of Claim 8, wherein the sandboxed execution context prevents the dashboard from accessing the display device's local storage, cookies, filesystem, operating system services, and network connections beyond the Wi-Fi Direct link to the module.

**Claim 33.** The method of Claim 8, wherein the dashboard content is cryptographically signed as part of the identity file such that the display device can verify content integrity before rendering.

### Claims Dependent on Claim 4 (Authoring Method)

**Claim 34.** The method of Claim 4, wherein the ten sequential authoring sections comprise identity, communication preferences, cognitive style, accessibility needs, emotional boundaries, consent tiers, context profiles, sensory preferences, user interface dashboard configuration, and custom extensions.

**Claim 35.** The method of Claim 4, further comprising maintaining a local encrypted audit log of all changes to the identity file with timestamps, enabling the human user to review edit history and revert to previous versions.

### Claims Dependent on Claim 6 (Profile Switching)

**Claim 36.** The method of Claim 6, wherein the time-based rule trigger comprises a calendar schedule defined in the profiles section of the identity file specifying which profile to activate at specified times or upon detecting specified contextual signals.

### Claims Dependent on Claim 9 (Authoring Application)

**Claim 37.** The system of Claim 9, wherein the conversational interface uses plain language prompts rather than technical form fields, configured to be accessible to non-technical users.

**Claim 38.** The system of Claim 9, further comprising an accessibility preview mode enabling the human user to simulate how their identity file would be interpreted by different categories of artificial intelligence agents before transferring the file to the module.

---

## CLAIM SUMMARY

| # | Type | Scope | Subject |
|---|------|-------|---------|
| 1 | Independent | System | Five-component integrated system |
| 2 | Independent | Device | Biometric Identity Module hardware |
| 3 | Independent | Method | Broadcasting authenticated identity |
| 4 | Independent | Method | Authoring identity file via guided app |
| 5 | Independent | Method | AI agent configuration from received file |
| 6 | Independent | Method | Context-profile switching |
| 7 | Independent | Method | Biometric-gated identity projection (crypto) |
| 8 | Independent | Method | UI projection to compatible display |
| 9 | Independent | System | Guided authoring app with module transfer |
| 10–13 | Dependent | System | Claim 1 dependents (scope, consent, format, revocation) |
| 14–21 | Dependent | Device | Claim 2 dependents (5 form factors, button, timing, rate limiting) |
| 22–24 | Dependent | Method | Claim 3 dependents (consent version, section access, profile notification) |
| 25–27 | Dependent | Method | Claim 5 dependents (rejection, revocation check, profile merge) |
| 28–30 | Dependent | Method | Claim 7 dependents (fingerprint, secure element, skin contact lockout) |
| 31–33 | Dependent | Method | Claim 8 dependents (dashboard format, sandbox, content signing) |
| 34–35 | Dependent | Method | Claim 4 dependents (section list, audit log) |
| 36 | Dependent | Method | Claim 6 dependent (calendar trigger) |
| 37–38 | Dependent | System | Claim 9 dependents (plain language, preview mode) |

**Total: 9 Independent Claims + 29 Dependent Claims = 38 Claims**
