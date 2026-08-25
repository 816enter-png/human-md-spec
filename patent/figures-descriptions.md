# FIGURE DESCRIPTIONS FOR PATENT ILLUSTRATOR
## Biometric-Gated Portable Identity Module and Protocol for Human-AI Communication

**Inventor:** Geoffrey LaCorte | **Assignee:** 816 Enterprises LLC | **Date:** 2026-03-31

---

> These descriptions are intended for a patent illustrator to produce formal line-art figures suitable for USPTO provisional and non-provisional patent submissions. All figures should use standard black-and-white line art. Reference numerals are provided for each element.

---

## FIG. 1 — System Architecture Overview

**Description:** A high-level architecture diagram showing the five primary components of the invention and their relationships to external AI agent receiver systems. This figure establishes the "big picture" of the invention: (1) authoring app, (2) human.md file format, (3) Biometric Identity Module (BIM) hardware, (4) cryptographic trust chain, and (5) UI projection.

**Layout:** Five vertically stacked boxes on the left side of the figure (representing the five invention components), with arrows pointing right toward a column of receiver system icons on the right side.

**Elements and Reference Numerals:**

- **100** — The overall system, labeled "Biometric-Gated Portable Identity Module"
- **110** — Box: "Consumer Authoring Application" (top left)
  - **111** — Icon: mobile phone silhouette
  - **112** — Label: "Guided UI / No Technical Knowledge Required"
- **120** — Box: "Human Identity File (human.md)" (second from top, left)
  - **121** — Icon: document with markdown hash symbol
  - **122** — Label: "Open Format · Human-Readable · Machine-Parseable · User-Owned"
- **130** — Box: "Biometric Identity Module (BIM)" (center left)
  - **131** — Icon: multiple form-factor silhouettes (pendant, ring, clip, USB-C dongle, phone case)
  - **132** — Label: "ESP32-S3 · BLE 5.0 · NFC · Fingerprint · Secure Element · Skin Contact"
- **133** — Box: "Cryptographic Trust Chain" (fourth from top, left)
  - **134** — Icon: chain-link symbol with lock
  - **135** — Label: "Ed25519 Signing · Human→App→File→Module→Agent"
- **136** — Box: "UI Projection" (bottom left)
  - **137** — Icon: dashboard wireframe projected from BIM to nearby screen
  - **138** — Label: "BLE Detection · Wi-Fi Direct · HTTP Dashboard · Sandboxed Rendering"
- **140** — Double-headed arrow between 110 and 120: "Generate / Update"
- **150** — Double-headed arrow between 120 and 130: "Transfer via BLE Pairing (Ed25519 Signed)"
- **152** — Arrow from 133 spanning 110 through 130: "Trust Chain Encompasses All Components"
- **155** — Arrow from 136 to receiver column: "Proximity-Gated Dashboard Projection"
- **160** — Dashed bracket grouping 110, 120, 130, 133, 136 labeled "Invention"
- **170** — Arrow from 130 pointing right: "BLE Advertisement / NFC Tap"
- **180** — Column of receiver system icons on the right:
  - **181** — Icon: kiosk terminal, labeled "Retail / Public Kiosk"
  - **182** — Icon: humanoid robot, labeled "Autonomous Robot"
  - **183** — Icon: house outline, labeled "Smart Home Hub"
  - **184** — Icon: medical cross / hospital building, labeled "Medical System"
  - **185** — Icon: laptop/desktop computer, labeled "AI Software Agent"
- **190** — Note box at bottom right: "Any human.md-compliant AI system configures its behavior from user's identity file before interaction begins. Biometric gating ensures only the authenticated owner can broadcast."

---

## FIG. 2 — Consumer Authoring Application User Flow

**Description:** A flowchart / UI wireframe hybrid showing the step-by-step guided authoring experience in the Consumer Authoring Application, from first launch through human.md generation, Ed25519 signing, and transfer to the BIM. Covers all 10 sections.

**Layout:** Top-to-bottom flow with decision diamonds and screen wireframe callouts on the sides.

**Elements and Reference Numerals:**

- **200** — Start node: "User Opens App (First Launch)"
- **210** — Process box: "Welcome Screen / Concept Explanation"
- **220** — Process box: "Section 1: Basic Identity" with side callout **221** showing a wireframe of name, pronoun, language inputs
- **230** — Process box: "Section 2: Communication Preferences" with side callout **231** showing tone slider (Formal <--> Casual), verbosity toggle
- **240** — Process box: "Section 3: Cognitive Style" with side callout **241** showing step-by-step vs. summary toggle
- **250** — Process box: "Section 4: Accessibility Requirements" with side callout **251** showing checkboxes: visual, hearing, motor, reading
- **260** — Process box: "Section 5: Emotional Boundaries" with side callout **261** showing topic exclusion chips, tone restriction toggles
- **270** — Process box: "Section 6: Consent Tiers" with side callout **271** showing three-column drag-and-drop interface: Autonomous / Confirm / Prohibited
- **280** — Process box: "Section 7: Context Profiles" with side callout **281** showing named profile cards (Work, Home, Medical, Public)
- **290** — Process box: "Section 8: Sensory Preferences" with side callout **291** showing volume, pacing, contrast sliders
- **293** — Process box: "Section 9: UI Dashboard Preferences" with side callout **294** showing layout picker, theme selector, widget toggles for projected dashboard
- **295** — Process box: "Section 10: Custom Sections (Optional)"
- **296** — Process box: "Ed25519 Key Generation / Signing" with side callout **297** showing key-pair icon, file hash, digital signature badge
- **300** — Diamond: "Pair BIM?"
  - Yes --> **310**
  - No --> **320**
- **310** — Process box: "BLE Pairing with BIM + Fingerprint Enrollment" --> arrow to **330**
- **320** — Process box: "Local File Export (Files App / Download)"
- **330** — Process box: "Transfer Signed human.md to BIM via BLE"
- **340** — End node: "Setup Complete — BIM Active (Locked Until Fingerprint Auth)"
- **350** — Side note: "User may return to any section at any time to edit, re-sign, and re-sync"

---

## FIG. 3 — Human Identity File (human.md) Schema Diagram

**Description:** A structured diagram showing the human.md document hierarchy — the YAML front matter block, the 10 standard sections, the section field types, the Consent tier structure, the Profiles nested structure, and the UI Dashboard section. This figure functions as a visual schema reference.

**Layout:** Document outline style with nested indentation. Left column shows the markdown syntax representation; right column shows the data type / description annotation.

**Elements and Reference Numerals:**

- **400** — Top-level document box: "Human Identity File (human.md)"
- **410** — YAML front matter block:
  - **411** — `hif_version:` — String, schema version
  - **412** — `created:` — ISO 8601 timestamp
  - **413** — `updated:` — ISO 8601 timestamp
  - **414** — `file_version:` — Integer, monotonically increasing
  - **415** — `preferred_name:` — String, fast-parse field
  - **416** — `owner_id:` — Opaque string, privacy-preserving
  - **417** — `signature:` — Ed25519 digital signature (base64-encoded)
  - **418** — `public_key:` — Ed25519 public key (base64-encoded)
- **420** — Section box: `## Identity`
  - Fields: name, pronouns, language_primary, language_secondary
- **430** — Section box: `## Communication`
  - Fields: tone (scale), verbosity (scale), pacing (scale), modality (enum), complexity (scale)
- **440** — Section box: `## Cognitive Style`
  - Fields: reasoning_style (enum), instruction_preference (enum), ambiguity_tolerance (scale)
- **450** — Section box: `## Accessibility`
  - Fields: vision_impairment (bool), hearing_impairment (bool), motor_limitation (bool), custom_notes (text)
- **460** — Section box: `## Emotional Boundaries`
  - Fields: excluded_topics (list), tone_restrictions (list), wellbeing_flags (list)
- **470** — Section box: `## Consent` (highlighted with border)
  - **471** — `tier_1_autonomous:` — List of action categories
  - **472** — `tier_2_confirm:` — List of action categories
  - **473** — `tier_3_prohibited:` — List of action categories
  - **474** — `consent_version:` — Integer
  - **475** — `broadcast_scope:` — Enum (public / request-only / private per section)
- **480** — Section box: `## Profiles` (highlighted with border)
  - **481** — Nested box: `### Profile: Work` — sparse overrides of Communication, Consent
  - **482** — Nested box: `### Profile: Medical` — sparse overrides of Communication, Accessibility, Consent
  - **483** — Nested box: `### Profile: Home` — sparse overrides
  - **484** — Ellipsis: additional user-defined profiles
- **490** — Section box: `## Sensory`
  - Fields: volume_level, speech_rate, visual_contrast, haptic_feedback
- **492** — Section box: `## UI Dashboard` (highlighted with border)
  - **493** — Fields: layout (enum: compact / expanded / minimal), theme (enum: light / dark / system), widgets (list of enabled dashboard components), projection_timeout (integer, seconds), auto_approve_known_agents (bool)
  - **494** — Note: "Controls the appearance and behavior of the proximity-projected UI dashboard served via Wi-Fi Direct"
- **495** — Section box: `## Custom`
  - **496** — Nested box: User-defined subsections with arbitrary key-value pairs
- **498** — Note: "Ed25519 signature in front matter covers all section content; receivers MUST verify signature before applying identity file"
- **499** — Note: "Fields in Profiles sections override corresponding base fields when profile is active (sparse override model)"

---

## FIG. 4 — Biometric Identity Module Hardware Block Diagram

**Description:** An electronic hardware block diagram showing the internal components of the BIM, their interconnections, and the external interfaces. Includes fingerprint sensor, skin contact sensor, and secure element. This figure supports the hardware claims. The BIM is form-factor agnostic (pendant, ring, clip, USB-C dongle, phone case).

**Layout:** Central microcontroller block with peripheral blocks connected by labeled bus/signal lines. Power tree on one side; wireless radios on the other; biometric sensors grouped together; secure element adjacent to microcontroller; external interfaces at the bottom.

**Elements and Reference Numerals:**

- **500** — Central block: "ESP32-S3 SoC" with sub-labels:
  - CPU, BLE 5.0 Radio (integrated), Wi-Fi (for UI projection / dormant), Flash Controller, GPIO
- **502** — Block: "Secure Element (ATECC608B)" connected to 500 via I2C bus **503**
  - **504** — Label: "Ed25519 Private Key Storage · Hardware-Bound Signing · Tamper-Resistant"
- **506** — Block: "Fingerprint Sensor (capacitive, e.g. FPC1025 or equivalent)" connected to 500 via SPI bus **507**
  - **508** — Label: "Template Storage in Secure Element · Liveness Detection"
- **509** — Block: "Skin Contact Sensor (capacitive touch / bioimpedance)" connected to 500 via ADC/GPIO **509a**
  - **509b** — Label: "Continuous Wear Detection · Re-Lock on Contact Loss"
- **510** — Block: "NFC IC (e.g. ST25R3916 or equivalent)" connected to 500 via SPI bus **511**
  - **512** — NFC Antenna Loop (external antenna symbol)
- **520** — Block: "NFC Type 4 Tag Emulation Mode" — annotation on block 510
- **530** — Block: "SPI NOR Flash (4MB-16MB)" connected to 500 via SPI bus **531**
  - Label: "human.md Storage (signed plaintext + binary index)"
- **550** — Block: "Power Management IC" connected to:
  - **551** — LiPo Battery (labeled "3.7V LiPo, min. 150mAh")
  - **552** — USB-C Connector (charging + data)
  - **553** — 3.3V regulated output to 500
- **560** — Block: "Status LED (RGB)" connected to 500 via GPIO **561**
- **570** — Block: "Haptic Motor (optional)" connected to 500 via GPIO **571**
- **580** — Block: "Hardware Button(s)" connected to 500 via GPIO **581**
  - Label: "Profile Switch / Power"
- **590** — Block: "BLE Antenna" — integrated in ESP32-S3 or PCB trace antenna, annotated
- **595** — Outer dashed box labeled "Form-Factor Agnostic Enclosure (Pendant / Ring / Clip / USB-C Dongle / Phone Case)" encompassing all blocks
- **596** — Arrow from 590 pointing outside enclosure: "BLE Advertisement / GATT"
- **597** — Arrow from 512 pointing outside enclosure: "NFC Tap"
- **598** — Arrow from 552 pointing outside enclosure: "USB-C (Charge / human.md Transfer)"
- **599** — Annotation arrow from 502 to 506: "Fingerprint template verification routed through secure element"

---

## FIG. 5 — BLE and NFC Broadcast Sequence Diagram

**Description:** A UML-style sequence diagram showing the communication protocol between the BIM and an AI agent receiver system (e.g., a kiosk or robot). Shows both the BLE passive advertisement path and the NFC tap path. Includes Ed25519 signature verification step.

**Layout:** Three vertical lifelines: User/BIM (left), AI Agent Receiver (center), AI Agent Configuration Layer (right). Time flows top to bottom. Two horizontal sections separated by a divider: top section = BLE flow, bottom section = NFC flow.

**Elements and Reference Numerals:**

**BLE Flow (top section):**

- **600** — BIM Lifeline, labeled "BIM (Authenticated)"
- **601** — AI Agent Lifeline, labeled "AI Agent Receiver System"
- **602** — Config Layer Lifeline, labeled "Agent Configuration Layer"
- **605** — Precondition note on BIM lifeline: "Fingerprint authenticated · Skin contact active"
- **610** — BIM --> broadcast: "BLE ADV_IND: ServiceUUID=HIF-Broadcaster, preferred_name, hif_version, file_version, available_sections_bitmask, signature_present=true" (arrow from 600 to 601)
- **620** — AI Agent: Decision diamond "HIF broadcaster? Relevant sections available?" Yes --> continue
- **630** — AI Agent --> BIM: "BLE CONNECT_REQ" (arrow from 601 to 600)
- **635** — BIM --> AI Agent: "CONNECT confirmation" (arrow from 600 to 601)
- **637** — AI Agent --> BIM: "GATT Read: Public key + Signature" (arrow from 601 to 600)
- **638** — AI Agent: "Verify Ed25519 signature against file content" (self-arrow on 601)
- **639** — Diamond on 601: "Signature valid?" No --> **639a** "Reject / warn user". Yes --> continue
- **640** — AI Agent --> BIM: "GATT Read: Communication characteristic" (arrow from 601 to 600)
- **645** — BIM --> AI Agent: "GATT Response: Communication section data" (arrow from 600 to 601)
- **650** — AI Agent --> BIM: "GATT Read: Consent characteristic" (arrow from 601 to 600)
- **655** — BIM --> AI Agent: "GATT Response: Consent section data" (arrow from 600 to 601)
- **660** — AI Agent --> BIM: "GATT Subscribe: Profile-change notifications" (arrow from 601 to 600)
- **665** — BIM --> AI Agent: "Subscription confirmed" (arrow from 600 to 601)
- **670** — AI Agent --> Config Layer: "Apply human.md: tone=formal, verbosity=concise, tier1=[...], tier3=[...]" (arrow from 601 to 602)
- **675** — Config Layer note: "Agent interaction behavior configured"
- **680** — (later) BIM --> AI Agent: "GATT Notification: active_profile=Medical" (arrow from 600 to 601)
- **685** — AI Agent --> Config Layer: "Re-apply Medical profile overrides" (arrow from 601 to 602)

**NFC Flow (bottom section, after horizontal divider):**

- **690** — BIM --> AI Agent: "NFC field detected / tag presented" (tap gesture icon on arrow from 600 to 601)
- **692** — AI Agent --> BIM: "NDEF Read request" (arrow from 601 to 600)
- **694** — BIM --> AI Agent: "NDEF Response: human.md broadcast-scope subset as signed text record" (arrow from 600 to 601)
- **695** — AI Agent: "Verify Ed25519 signature" (self-arrow on 601)
- **696** — AI Agent --> Config Layer: "Apply human.md from NFC tap (signature verified)" (arrow from 601 to 602)

---

## FIG. 6 — AI Agent Configuration Flow

**Description:** A flowchart showing the decision logic an AI agent receiver system executes upon detecting a BIM — from BLE advertisement detection through signature verification, behavior configuration, and consent enforcement during interaction. This figure supports the method claims for AI agent configuration.

**Layout:** Top-to-bottom flowchart with decision diamonds and side annotation boxes.

**Elements and Reference Numerals:**

- **700** — Start: "Idle — BLE Scanning Active"
- **710** — Event: "BLE advertisement received"
- **715** — Diamond: "ServiceUUID = HIF-Broadcaster?" No --> **716** (Return to 700). Yes --> **720**
- **716** — Process: "Ignore advertisement"
- **718** — Diamond: "signature_present flag set?" No --> **719** (Reject unsigned broadcast). Yes --> **720**
- **719** — Process: "Discard — unsigned identity files not accepted"
- **720** — Diamond: "file_version > cached version OR no cache?" No --> **721** (skip to 760). Yes --> **730**
- **721** — Process: "Use cached human.md — no re-fetch needed"
- **730** — Process: "Initiate GATT connection to BIM"
- **733** — Process: "Fetch Ed25519 public key and signature via GATT"
- **735** — Process: "Request relevant human.md sections (Communication, Accessibility, Consent, Active Profile, UI Dashboard)"
- **736** — Diamond: "Ed25519 signature valid?" No --> **737** "Reject identity file; notify user of tamper warning". Yes --> **740**
- **740** — Diamond: "broadcast_scope permits requested sections?" No --> **741**. Yes --> **750**
- **741** — Process: "Present trust token (if registered) OR omit restricted sections"
- **750** — Process: "Receive and parse human.md sections"
- **755** — Process: "Apply active context profile sparse overrides to base fields"
- **760** — Process box (highlighted): "Configure Agent Behavior:"
  - **761** — Set interaction tone per Communication.tone
  - **762** — Set verbosity per Communication.verbosity
  - **763** — Set modality per Communication.modality
  - **764** — Apply accessibility accommodations per Accessibility fields
  - **765** — Load consent tier lists from Consent section
  - **766** — Load UI Dashboard preferences for projection readiness
- **770** — Process: "Subscribe to BLE notifications for profile changes"
- **780** — Process: "Initiate interaction (pre-configured)"
- **790** — During-interaction loop:
  - **791** — Event: "Agent about to take action on user's behalf"
  - **792** — Diamond: "Action in tier_1_autonomous?" Yes --> **793** (Proceed). No --> **794**
  - **794** — Diamond: "Action in tier_2_confirm?" Yes --> **795** (Request user confirmation). No --> **796**
  - **796** — Diamond: "Action in tier_3_prohibited?" Yes --> **797** (Decline, disclose to user). No --> **798** (proceed with caution, action not categorized)
  - **795** — Diamond: "User confirms?" Yes --> proceed. No --> abort.
- **799** — Side note: "Consent tier enforcement is mandatory for all human.md-compliant agent systems. Signature verification is mandatory before any configuration is applied."

---

## FIG. 7 — Context-Switching Sequence Diagram

**Description:** A sequence diagram showing the full context-profile switching flow, from trigger event through BIM state update to AI agent reconfiguration. Shows three trigger types: manual hardware button, app command, and time-based schedule. BIM must be in authenticated state (fingerprint verified, skin contact active) for any profile switch to broadcast.

**Layout:** Four lifelines: User (leftmost), BIM (center-left), App (center-right), AI Agent (rightmost). Time flows top to bottom. Three labeled sections show the three trigger types.

**Elements and Reference Numerals:**

- **800** — Lifelines: User **801**, BIM **802**, App **803**, AI Agent **804**

**Section A — Hardware Button Trigger:**
- **810** — User presses BIM hardware button (arrow from 801 to 802)
- **812** — BIM: verify skin contact still active and authenticated state valid
- **815** — BIM: cycles to next profile in ordered list; updates working state; updates BLE ADV data; re-signs with Ed25519 via secure element
- **820** — BIM --> AI Agent: "BLE Notification: active_profile='Medical'" (arrow from 802 to 804)
- **825** — AI Agent: re-fetches Medical profile overrides via GATT, re-verifies signature
- **830** — AI Agent: applies Medical profile configuration (tone=gentle, detail=high, consent updated)
- **835** — BIM: LED color changes to Medical profile indicator color

**Section B — App Command Trigger:**
- **840** — User selects "Work Mode" in App (arrow from 801 to 803)
- **845** — App --> BIM: "BLE Write: set_active_profile='Work'" (arrow from 803 to 802)
- **850** — BIM --> App: "Acknowledgment" (arrow from 802 to 803)
- **855** — BIM --> AI Agent: "BLE Notification: active_profile='Work'" (arrow from 802 to 804)
- **860** — AI Agent: applies Work profile configuration

**Section C — Time-Based / Calendar Trigger:**
- **865** — App: Calendar integration detects scheduled event "Doctor Appointment" at 14:00
- **870** — App --> BIM at 13:55: "BLE Write: set_active_profile='Medical'" (arrow from 803 to 802)
- **875** — BIM --> AI Agent: "BLE Notification: active_profile='Medical'" (arrow from 802 to 804)
- **880** — AI Agent: applies Medical profile pre-emptively before appointment begins

---

## FIG. 8 — Consent Tier Enforcement Diagram

**Description:** A detailed diagram showing the three-tier consent model encoded in the human.md Consent section, how AI agents query the model, and the decision outcomes for each tier. Includes Ed25519 signature verification as a precondition. This figure directly supports the consent tier claims.

**Layout:** Left panel shows the human.md Consent section structure. Right panel shows the AI agent decision flowchart. Center shows a sample population of action categories distributed across tiers.

**Elements and Reference Numerals:**

**Left Panel — human.md Consent Section:**
- **900** — Box: "## Consent section (from human.md, Ed25519 signed)"
- **910** — Sub-box (green background): "Tier 1: Autonomous"
  - **911** — Sample entries: "send-calendar-invite, read-email-subject, set-reminder, adjust-display-settings"
- **920** — Sub-box (yellow background): "Tier 2: Confirmation Required"
  - **921** — Sample entries: "send-email, make-purchase-under-$20, share-location, book-appointment"
- **930** — Sub-box (red background): "Tier 3: Prohibited"
  - **931** — Sample entries: "share-medical-history, record-audio, access-financial-accounts, contact-emergency-services-without-confirmation"
- **940** — Box: "consent_version: 7" (version counter)
- **941** — Box: "signature: <Ed25519 signature>" (tamper-proof indicator)

**Center Panel — Action Category Examples:**
- **950** — Three columns of example action chips, each colored green/yellow/red to match their assigned tier
- **951** — Note: "User assigns each action category via Consent Tier Editor in App; changes require re-signing"

**Right Panel — AI Agent Decision Flow:**
- **955** — Precondition: "Verify Ed25519 signature on human.md" --> Diamond "Valid?" No --> "Reject all actions". Yes --> continue
- **960** — Event: "Agent instructed to perform action X"
- **965** — Process: "Look up action X category in Consent section"
- **970** — Diamond: "Action X in tier_1_autonomous?" Yes --> **971** "Perform autonomously". No --> **975**
- **975** — Diamond: "Action X in tier_2_confirm?" Yes --> **976** "Request user confirmation via UI prompt" --> Diamond "Confirmed?" Yes --> "Perform" / No --> "Abort". No --> **980**
- **980** — Diamond: "Action X in tier_3_prohibited?" Yes --> **981** "Decline; disclose prohibition to user". No --> **985** "Perform with caution; log uncategorized action; prompt user to categorize in App"
- **990** — Note at bottom: "If consent_version in cached human.md differs from current BIM value, agent must re-fetch Consent section before taking any action"

---

## FIG. 9 — Cryptographic Trust Chain

**Description:** A diagram showing the five-node cryptographic trust chain that ensures identity file integrity from the human owner through to the AI agent consumer. Includes a comparison panel contrasting the human.md trust model against software-only identity approaches.

**Layout:** Top section: five linked nodes in a horizontal chain, left to right. Each node has a security property annotation below it. Bottom section: two-column comparison panel (software-only vs. human.md).

**Elements and Reference Numerals:**

**Top Section — Trust Chain:**
- **1000** — Overall label: "Cryptographic Trust Chain (Ed25519)"
- **1010** — Node 1 (leftmost): "Human Owner"
  - **1011** — Security property: "Biometric binding — fingerprint authenticates identity to device"
  - **1012** — Icon: fingerprint symbol
- **1020** — Link arrow from 1010 to 1030: "Authors via"
- **1030** — Node 2: "Authoring App"
  - **1031** — Security property: "Key generation — Ed25519 keypair created on first use, private key transferred to secure element"
  - **1032** — Icon: mobile app with key symbol
- **1040** — Link arrow from 1030 to 1050: "Produces"
- **1050** — Node 3: "Signed human.md File"
  - **1051** — Security property: "Content integrity — Ed25519 signature covers all section content; any modification invalidates signature"
  - **1052** — Icon: document with seal/checkmark
- **1060** — Link arrow from 1050 to 1070: "Stored in"
- **1070** — Node 4: "Biometric Identity Module (BIM)"
  - **1071** — Security property: "Hardware binding — private key in ATECC608B secure element; signing operations never expose key; skin contact required for active state"
  - **1072** — Icon: BIM device with lock symbol
- **1080** — Link arrow from 1070 to 1090: "Broadcasts to"
- **1090** — Node 5 (rightmost): "AI Agent"
  - **1091** — Security property: "Verification — agent verifies Ed25519 signature using embedded public key before applying any identity configuration"
  - **1092** — Icon: robot/agent with checkmark shield

**Bottom Section — Comparison Panel:**
- **1100** — Two-column table or side-by-side boxes
- **1110** — Left column: "Software-Only Identity (Prior Art)"
  - **1111** — "No biometric binding to owner"
  - **1112** — "File can be copied, modified, replayed"
  - **1113** — "No hardware root of trust"
  - **1114** — "No continuous wear verification"
  - **1115** — "Identity is a file, not a verified assertion"
- **1120** — Right column: "human.md with BIM (This Invention)"
  - **1121** — "Fingerprint binds identity to biological owner"
  - **1122** — "Ed25519 signature prevents tampering"
  - **1123** — "ATECC608B secure element as hardware root of trust"
  - **1124** — "Skin contact sensor ensures continuous physical presence"
  - **1125** — "Identity is a biometrically-verified, cryptographically-signed, hardware-bound assertion"

---

## FIG. 10 — UI Projection Sequence

**Description:** A UML-style sequence diagram showing the full UI projection flow: BLE proximity detection, user approval prompt, Wi-Fi Direct handshake, HTTP dashboard serving, sandboxed rendering on the receiver display, proximity monitoring, and zero-persistence purge upon departure.

**Layout:** Four vertical lifelines: User/BIM (left), Receiver Device Display (center-left), Receiver Wi-Fi Radio (center-right), Receiver AI Agent (right). Time flows top to bottom.

**Elements and Reference Numerals:**

- **1200** — Lifelines: BIM **1201**, Receiver Display **1202**, Receiver Wi-Fi **1203**, Receiver AI Agent **1204**
- **1205** — Precondition note on BIM lifeline: "BIM authenticated (fingerprint + skin contact active)"

**Detection Phase:**
- **1210** — BIM: "BLE ADV includes UI-projection-capable flag and dashboard_hash" (broadcast arrow from 1201)
- **1215** — Receiver AI Agent detects BLE advertisement with UI projection flag (arrow from 1201 to 1204)
- **1220** — Receiver AI Agent --> Receiver Display: "Show approval prompt: 'Geoffrey's identity module requests to project a dashboard. Allow?'" (arrow from 1204 to 1202)
- **1225** — User approves on Receiver Display (arrow from 1202 to 1204 labeled "Approved")

**Handshake Phase:**
- **1230** — Receiver AI Agent --> BIM: "BLE message: UI projection approved, initiate Wi-Fi Direct" (arrow from 1204 to 1201)
- **1235** — BIM activates Wi-Fi Direct radio (self-arrow on 1201)
- **1240** — BIM <--> Receiver Wi-Fi: "Wi-Fi Direct P2P handshake (WPA3 encrypted)" (double arrow between 1201 and 1203)
- **1245** — BIM: "Start embedded HTTP server on Wi-Fi Direct link (port 80, local only)" (self-arrow on 1201)

**Dashboard Serving Phase:**
- **1250** — Receiver Wi-Fi --> BIM: "HTTP GET /dashboard" (arrow from 1203 to 1201)
- **1255** — BIM --> Receiver Wi-Fi: "HTTP 200: HTML/CSS/JS dashboard (single-page, self-contained, no external resources)" (arrow from 1201 to 1203)
- **1260** — Receiver Wi-Fi --> Receiver Display: "Render in sandboxed iframe / webview (no localStorage, no cookies, no network access beyond BIM)" (arrow from 1203 to 1202)
- **1265** — Receiver Display: "Dashboard visible — shows identity summary, active profile, consent tiers, communication preferences per UI Dashboard section settings" (annotation on 1202)

**Proximity Monitoring Phase:**
- **1270** — Loop box: "Continuous BLE RSSI monitoring"
  - **1271** — Receiver AI Agent: "Check BLE RSSI from BIM every N seconds"
  - **1272** — Diamond: "RSSI below proximity threshold OR BLE connection lost?" No --> continue loop. Yes --> **1280**

**Termination Phase:**
- **1280** — Receiver AI Agent --> Receiver Display: "Tear down dashboard" (arrow from 1204 to 1202)
- **1282** — Receiver Display: "Close sandboxed webview" (self-arrow on 1202)
- **1284** — Receiver Wi-Fi: "Disconnect Wi-Fi Direct link" (self-arrow on 1203)
- **1286** — BIM: "Deactivate Wi-Fi Direct radio (return to BLE-only low-power mode)" (self-arrow on 1201)
- **1290** — Receiver Display: "Zero-persistence purge: clear all cached dashboard content, HTML, CSS, JS; no data persists after departure" (annotation with emphasis on 1202)
- **1295** — Note at bottom: "The BIM never transmits data to the internet. All dashboard content is served over the local Wi-Fi Direct link. The receiver device is prohibited from caching or storing any dashboard content beyond the active session."

---

## FIG. 11 — Biometric Authentication Flow

**Description:** A flowchart showing the complete biometric authentication lifecycle of the BIM: fingerprint capture, liveness check, template matching against the secure element, transition to authenticated state with BLE advertising enabled, and the continuous skin contact monitoring loop that re-locks the device upon contact loss.

**Layout:** Two connected sections. Left/top section: linear authentication flowchart (top to bottom). Right/bottom section: circular skin contact monitoring loop with re-lock path.

**Elements and Reference Numerals:**

**Authentication Flow (left/top section):**
- **1300** — Start node: "BIM Powered On (Locked State — No BLE Advertising)"
- **1305** — Note: "In locked state, BIM does not broadcast any BLE advertisements and does not respond to NFC reads"
- **1310** — Event: "User places finger on fingerprint sensor"
- **1315** — Process: "Fingerprint Sensor: Capture fingerprint image"
- **1320** — Diamond: "Liveness check passed? (capacitive pattern analysis, not a static image or mold)"
  - No --> **1321** "Reject — display red LED, increment failed attempt counter"
  - **1322** — Diamond: "Failed attempts >= threshold (e.g., 5)?" Yes --> **1323** "Temporary lockout (progressive delay)". No --> return to **1310**
  - Yes --> **1330**
- **1330** — Process: "Extract fingerprint template (minutiae points)"
- **1340** — Process: "Send template to Secure Element (ATECC608B) for comparison"
- **1345** — Diamond: "Template matches enrolled fingerprint?"
  - No --> **1346** "Reject — display red LED, increment failed attempt counter" --> return to **1322**
  - Yes --> **1350**
- **1350** — Process box (highlighted): "AUTHENTICATED STATE"
  - **1351** — "Enable BLE advertising with human.md data"
  - **1352** — "Enable NFC tag emulation"
  - **1353** — "Display green LED confirmation"
  - **1354** — "Start skin contact monitoring loop"
  - **1355** — "Record authentication timestamp"

**Skin Contact Monitoring Loop (right/bottom section):**
- **1360** — Loop start: "Skin Contact Monitor Active"
- **1365** — Process: "Read skin contact sensor (capacitive touch / bioimpedance) every N seconds (e.g., every 2 seconds)"
- **1370** — Diamond: "Skin contact detected?"
  - Yes --> **1375** "Maintain authenticated state" --> return to **1365** (loop continues)
  - No --> **1380**
- **1380** — Process: "Start grace period timer (e.g., 10 seconds — configurable in App)"
- **1385** — Diamond: "Skin contact restored within grace period?"
  - Yes --> **1375** (return to monitoring loop)
  - No --> **1390**
- **1390** — Process box (highlighted, red border): "RE-LOCK DEVICE"
  - **1391** — "Immediately cease all BLE advertising"
  - **1392** — "Disable NFC tag emulation"
  - **1393** — "Display amber LED (locked indicator)"
  - **1394** — "Clear any active Wi-Fi Direct connections (terminate UI projection)"
  - **1395** — "Return to Locked State (1300) — re-authentication required"
- **1398** — Note: "The skin contact sensor ensures that the BIM only broadcasts identity when physically worn by the authenticated user. Removal from the body triggers immediate re-lock, preventing unauthorized use of a removed device."
- **1399** — Note: "Fingerprint templates are stored exclusively in the ATECC608B secure element and never leave the hardware boundary. No biometric data is transmitted over BLE, NFC, or Wi-Fi Direct."
