# USPTO PROVISIONAL PATENT APPLICATION FILING CHECKLIST
## Biometric-Gated Portable Identity Module and Protocol for Human-AI Communication

**Inventor:** Geoffrey LaCorte, Jersey City, NJ
**Assignee:** 816 Enterprises LLC
**Application Type:** Provisional Patent Application (35 U.S.C. § 111(b))
**Checklist Date:** 2026-03-31

---

> **Disclaimer:** This checklist is provided for informational purposes and does not constitute legal advice. Consult a registered patent attorney or agent for legal guidance before filing. The USPTO website (uspto.gov) is the authoritative source for current fees, forms, and procedures.

---

## Overview: Why File a Provisional?

A **Provisional Patent Application (PPA)** establishes a U.S. filing date (your "priority date") for 12 months without requiring formal claims. This gives you time to:
- Refine the invention
- Seek investors or partners
- File a full non-provisional application
- Use the phrase **"Patent Pending"**

The provisional expires after 12 months if not converted. It is **never examined** and **never becomes a patent** on its own.

---

## PART 1: BEFORE YOU FILE

### Step 1: Determine Micro Entity Status

Micro entity status reduces the filing fee by **80%** and requires:

- [ ] You qualify as a "small entity" (fewer than 500 employees)
- [ ] You have NOT filed more than 4 previous patent applications (excluding provisionals and foreign applications)
- [ ] Your gross income does not exceed 3x the median household income (currently approximately $225,000 for 2025/2026 — confirm at USPTO.gov)
- [ ] The assignee (816 Enterprises LLC) also meets micro entity income requirements

**If you qualify:** You will file Form PTO/SB/15A (Certification of Micro Entity Status — Gross Income Basis) with your application.

**If uncertain:** Default to Small Entity status (50% discount) to be safe, then request micro entity status correction later if needed.

### Step 2: Get a USPTO.gov Account

- [ ] Go to **https://www.uspto.gov**
- [ ] Click "Sign In / Register" --> Create a USPTO.gov account (free)
- [ ] Note: You will file through the **Patent Center** portal (replaced EFS-Web)
- [ ] URL: **https://patentcenter.uspto.gov**

### Step 3: Gather Required Information

- [ ] Inventor's full legal name: **Geoffrey LaCorte**
- [ ] Inventor's mailing address: Jersey City, NJ (full address needed)
- [ ] Assignee information: **816 Enterprises LLC** (address, state of incorporation)
- [ ] Invention title (keep it concise but descriptive — matches specification):
  **"Biometric-Gated Portable Identity Module and Protocol for Human-AI Communication"**

---

## PART 2: FILING FEES (as of early 2026)

> **Important:** USPTO fees change periodically. Verify current fees at **https://www.uspto.gov/learning-and-resources/fees-and-payment/uspto-fee-schedule** before filing.

| Entity Type | Provisional Filing Fee (Basic) |
|------------|-------------------------------|
| Large Entity | ~$320 |
| Small Entity | ~$160 |
| **Micro Entity** | **~$80** |

**For a provisional application, there is only ONE fee:** the basic filing fee. There is no search fee, examination fee, or issue fee for a provisional.

**Additional fees that are NOT required for provisionals:**
- No claims fee (provisional claims are not required and not examined)
- No oath/declaration fee
- No publication fee

---

## PART 3: DOCUMENTS TO PREPARE

### Document 1: Provisional Patent Application Cover Sheet (Required)

Use **Form PTO/SB/16** — Provisional Application for Patent Cover Sheet.

- [ ] Download from USPTO.gov: search "PTO/SB/16"
- [ ] Fill in:
  - Title of Invention: "Biometric-Gated Portable Identity Module and Protocol for Human-AI Communication"
  - Inventor: Geoffrey LaCorte, Jersey City, NJ
  - Correspondence Address (where USPTO will send mail)
  - Attorney/Agent: Leave blank if self-filing
  - Entity Status: Check "Micro Entity" (if qualified) or "Small Entity"
  - Assignee: 816 Enterprises LLC
- [ ] Do NOT leave the title blank — this is the title that establishes your priority date

### Document 2: Specification (Required)

File: `specification.md` (convert to PDF for submission)

- [ ] Ensure the specification includes:
  - [ ] Title (matches cover sheet exactly): "Biometric-Gated Portable Identity Module and Protocol for Human-AI Communication"
  - [ ] Field of the Invention
  - [ ] Background / Prior Art discussion (including me.md, me.txt/metxt.org, Token Ring/tokencore.com, Meta US20250252700A1, Nymi Band)
  - [ ] Summary of the Invention (5 components: authoring app, human.md file, BIM hardware, cryptographic trust chain, UI projection)
  - [ ] Brief Description of the Drawings (FIG. 1 through FIG. 11)
  - [ ] Detailed Description of Embodiments (including fingerprint sensor, secure element ATECC608B, skin contact sensor, Ed25519 signing, BIM form factors)
  - [ ] At least one use case or example
- [ ] Claims are **optional** in a provisional (but recommended — they inform the eventual non-provisional and are referenced in the specification)
- [ ] Abstract is **optional** in a provisional (include for best practice)
- [ ] Convert to PDF: File --> Print --> Save as PDF (or use a PDF converter)
- [ ] **Do not include page numbers** in the filename; USPTO will assign them

### Document 3: Drawings / Figures (Recommended, Required if Referenced)

File: `figures-descriptions.md` --> commission patent line art from illustrator

- [ ] Since the specification references figures (FIG. 1 through FIG. 11), the drawings must be filed **with** the application OR a note must be added that drawings will be submitted within the time period
- [ ] **11 figures total:**
  - FIG. 1 — System Architecture Overview (5 components)
  - FIG. 2 — Consumer Authoring Application User Flow (10 sections)
  - FIG. 3 — Human Identity File (human.md) Schema Diagram
  - FIG. 4 — Biometric Identity Module Hardware Block Diagram (with fingerprint sensor, skin contact sensor, secure element ATECC608B)
  - FIG. 5 — BLE and NFC Broadcast Sequence Diagram (with Ed25519 verification)
  - FIG. 6 — AI Agent Configuration Flow (with signature verification)
  - FIG. 7 — Context-Switching Sequence Diagram
  - FIG. 8 — Consent Tier Enforcement Diagram
  - FIG. 9 — Cryptographic Trust Chain (Human-->App-->Signed File-->Module-->AI Agent)
  - FIG. 10 — UI Projection Sequence (BLE detection through zero-persistence purge)
  - FIG. 11 — Biometric Authentication Flow (fingerprint + skin contact monitoring)
- [ ] USPTO standard for drawings: black and white line art, minimum 300 DPI, specific margin requirements
- [ ] See **37 CFR 1.84** for drawing requirements
- [ ] **Option if no illustrator yet:** File without drawings and submit a note in the specification that "drawings will be filed pursuant to 37 CFR 1.53(c)(2)." However, best practice is to include drawings.
- [ ] If self-drawing: Can be hand-drawn line art scanned to PDF at 300 DPI — does not need to be professional for a provisional

### Document 4: Micro Entity Certification (If Applicable)

Form **PTO/SB/15A** — Certification of Micro Entity Status (Gross Income Basis)

- [ ] Download from USPTO.gov
- [ ] Sign and date
- [ ] Upload with application

### Document 5: Claims (Optional but Recommended)

File: `claims.md` (convert to PDF)

- [ ] Include the drafted claims from this application package
- [ ] Claims in a provisional are **not examined** but help define scope for the eventual non-provisional
- [ ] Ensure claims reference all 5 invention components: authoring app, human.md file format, Biometric Identity Module (BIM), cryptographic trust chain (Ed25519), UI projection
- [ ] Tip: Include them — they cost nothing extra and preserve their content in the priority record

---

## PART 4: FILING STEPS (Patent Center)

### Step 1: Log into Patent Center

- [ ] Go to **https://patentcenter.uspto.gov**
- [ ] Sign in with your USPTO.gov account

### Step 2: Start a New Application

- [ ] Click "File" --> "File a New Application"
- [ ] Application Type: **Provisional**
- [ ] Click "Continue"

### Step 3: Upload Documents

Upload in this order (Patent Center will prompt for document type):

- [ ] **Application Data Sheet (ADS)** or manually enter bibliographic data in the web form:
  - Inventor name and address
  - Title of invention: "Biometric-Gated Portable Identity Module and Protocol for Human-AI Communication"
  - Assignee: 816 Enterprises LLC
  - Entity status
- [ ] **Specification** (PDF) — Document type: "Specification"
- [ ] **Claims** (PDF, if included) — Document type: "Claims"
- [ ] **Abstract** (PDF, if separate) — Document type: "Abstract"
- [ ] **Drawings** (PDF, 11 figures) — Document type: "Drawings"
- [ ] **Micro Entity Certification** (PTO/SB/15A) — Document type: "Statement"
- [ ] **Cover Sheet** (PTO/SB/16) — Document type: "Provisional Cover Sheet"

### Step 4: Calculate and Pay Fee

- [ ] Patent Center will calculate your fee based on entity status
- [ ] Expected fee (micro entity): approximately **$80**
- [ ] Payment options: credit card, USPTO deposit account, EFT
- [ ] Note your **Confirmation Number** immediately after submission

### Step 5: Receive Filing Receipt

- [ ] USPTO will email an electronic Filing Receipt within a few days (sometimes immediately)
- [ ] The Filing Receipt contains your **Application Number** (format: 63/XXX,XXX for provisionals)
- [ ] **Save this receipt** — the application number and filing date are your legal priority date record
- [ ] You may now use the phrase **"Patent Pending"** on your product, app, and marketing materials

---

## PART 5: AFTER FILING

### Within 12 Months: Convert to Non-Provisional

The provisional expires **exactly 12 months** from the filing date. To claim the priority date benefit, you must file a non-provisional application (or PCT international application) within that window.

- [ ] **Month 1-3:** Refine the invention, build BIM prototype, test claims with a patent attorney
- [ ] **Month 3-6:** Engage a registered patent attorney or agent to draft formal non-provisional claims
- [ ] **Month 9:** Target date for non-provisional filing (allows buffer before 12-month deadline)
- [ ] **Month 11:** Final deadline reminder — do not miss the 12-month window
- [ ] **Month 12:** Provisional expires — non-provisional must be on file by this date

### Track Application Status

- [ ] Use **Patent Center** or **Patent Application Information Retrieval (PAIR)** at USPTO.gov to track status
- [ ] Your provisional will show as "Pending" and then "Expired" after 12 months (normal — it does not mean your patent is denied)

### Consider Foreign Filing

If you want patent protection outside the U.S.:

- [ ] PCT (Patent Cooperation Treaty) application preserves rights in 150+ countries
- [ ] PCT must be filed within **12 months** of the provisional filing date
- [ ] PCT filing requires a patent attorney and significant additional fees (~$3,000-$5,000+)
- [ ] Key markets to consider: EU (EPO), Canada, Japan, South Korea, China, Australia

### Mark Your Product

Once the provisional is filed:

- [ ] You may label hardware (BIM devices), app stores (humanmd.app), websites (humanmd.dev), and marketing materials with **"Patent Pending"**
- [ ] Do NOT say "Patent No." until a non-provisional is granted (this is illegal under 35 U.S.C. § 292)

---

## PART 6: PRIOR ART CITATIONS

The specification and filing should reference the following prior art to distinguish the invention:

| Prior Art | Type | Key Distinction from This Invention |
|-----------|------|-------------------------------------|
| **me.md** | Open-source project | Software-only identity file; no hardware module, no biometric gating, no cryptographic signing, no broadcast protocol |
| **me.txt / metxt.org** | Open-source project | Plain-text identity file; no structured schema, no hardware, no biometric authentication, no trust chain |
| **Token Ring / tokencore.com** | Commercial product | NFC-only wearable; no BLE broadcast, no biometric gating, no identity file format, no AI agent configuration protocol |
| **Meta US20250252700A1** | Published patent application | AI persona configuration via platform; centralized/cloud-dependent, no portable hardware, no user-owned file, no biometric binding |
| **Nymi Band** | Commercial product (discontinued) | ECG-based continuous authentication wearable; authentication-only (no identity file, no AI configuration, no consent tiers, no file format standard) |

---

## PART 7: RECORD-KEEPING CHECKLIST

- [ ] Save the original specification, claims, figures, and cover sheet files with date stamps
- [ ] Save the USPTO Filing Receipt (PDF from Patent Center)
- [ ] Record the Application Number, Filing Date, and Confirmation Number in a secure location
- [ ] Create a calendar reminder for:
  - [ ] **6 months from filing:** Check-in / attorney engagement
  - [ ] **9 months from filing:** Target non-provisional filing
  - [ ] **11 months from filing:** Hard reminder — deadline approaching
  - [ ] **12 months from filing:** Provisional expiration — non-provisional must be filed

---

## PART 8: RESOURCES

| Resource | URL |
|---------|-----|
| USPTO Patent Center (file here) | https://patentcenter.uspto.gov |
| USPTO Fee Schedule | https://www.uspto.gov/learning-and-resources/fees-and-payment/uspto-fee-schedule |
| PTO/SB/16 Cover Sheet | Search "SB/16" at USPTO.gov Forms page |
| PTO/SB/15A Micro Entity Form | Search "SB/15A" at USPTO.gov Forms page |
| 37 CFR 1.84 Drawing Requirements | https://www.ecfr.gov --> Title 37 --> Part 1 --> SS 1.84 |
| USPTO Pro Bono Program (free legal help) | https://www.uspto.gov/patents/patent-pro-bono-program |
| Patent and Trademark Resource Centers (in-person help) | https://www.uspto.gov/learning-and-resources/support-centers/patent-and-trademark-resource-centers-ptrcs |
| humanmd.app (product domain) | https://humanmd.app |
| humanmd.dev (developer domain) | https://humanmd.dev |

---

## SUMMARY: MINIMUM VIABLE PROVISIONAL FILING

To establish a priority date immediately, the **minimum required** documents are:

1. **Cover Sheet (PTO/SB/16)** — filled out with title "Biometric-Gated Portable Identity Module and Protocol for Human-AI Communication"
2. **Specification** — the specification.md converted to PDF (drawings referenced but not yet filed is acceptable)
3. **Filing Fee** — ~$80 micro entity

Everything else (claims, abstract, drawings, micro entity certification) is strongly recommended but not technically required to get the priority date. File with everything you have ready; do not delay the filing date waiting for perfect documents.

---

*Prepared as part of provisional patent application package for 816 Enterprises LLC / G DESIGN Studio.*
*This document does not constitute legal advice. Consult a registered patent attorney (USPTO.gov attorney search: https://oedci.uspto.gov/OEDCI/) before filing.*
