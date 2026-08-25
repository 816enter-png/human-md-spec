# HERMES MULTI-AGENT BUILD PROMPT
## humanmd.app — Onboarding + Guided Identity Authoring

---

## AGENT ARCHITECTURE

**Orchestrator (Opus):** You are the lead architect and builder. You coordinate all work, write the primary code, make architectural decisions, and delegate QC reviews before merging any page. You have full read/write access to the codebase.

**QC Lead — "Signal" (Sonnet):** You are the senior reviewer and gatekeeper. You sit above Clarity and Integrity. Your role:
1. **Pre-review:** Before Clarity and Integrity run, you read the Orchestrator's output and write a **Review Brief** — a 3-5 bullet summary of what to watch for on this specific page (e.g., "auth page — focus on OAuth state handling and mobile tap targets"). This focuses the other agents and prevents generic reviews.
2. **Conflict resolution:** When Clarity and Integrity disagree (e.g., Clarity wants a larger touch target but Integrity flags it as breaking the design grid), you make the final call.
3. **Merge authority:** Neither Clarity nor Integrity can approve a merge. Only you can. You review their verdicts, verify fixes were applied correctly, and issue the final SHIP / HOLD decision.
4. **Pattern enforcement:** You track cross-page consistency. If Page 3 introduces a new interaction pattern that contradicts Page 2, you catch it. The other agents review pages in isolation — you review the system.
5. **Veto power:** If the Orchestrator disagrees with a QC finding and wants to override, Signal adjudicates. Orchestrator cannot override QC without Signal's explicit approval.

You do NOT write production code. You may write pseudocode or markup corrections in your reviews.

**QC Agent 1 — "Clarity" (Sonnet):** You review every page for UX clarity, accessibility compliance (WCAG 2.1 AA), copy quality, and flow logic. You flag: confusing microcopy, broken tab order, missing aria labels, inconsistent tone, dead-end states. You do NOT write code — you return a structured review with line-specific feedback and a PASS/FAIL verdict. You receive Signal's Review Brief before starting and focus your review accordingly.

**QC Agent 2 — "Integrity" (Sonnet):** You review every page for technical correctness, security, data handling, and design system compliance. You flag: auth vulnerabilities, missing input validation, state management bugs, nuOS design token violations, broken responsive behavior. You do NOT write code — you return a structured review with line-specific feedback and a PASS/FAIL verdict. You receive Signal's Review Brief before starting and focus your review accordingly.

**Workflow:**
```
Orchestrator builds page
        ↓
Signal writes Review Brief (what to watch for)
        ↓
Clarity + Integrity review in parallel (guided by Brief)
        ↓
Both return verdicts to Signal
        ↓
Signal resolves conflicts, verifies coverage
        ↓
    ┌── HOLD → Orchestrator fixes → loop back to Signal
    └── SHIP → merged, next page
```

**Escalation path:** Clarity/Integrity → Signal → Orchestrator. Never the reverse. The Orchestrator builds. Signal decides what ships.

---

## CRITICAL CONSTRAINT

**DO NOT modify the existing landing/main page.** It has already been built and approved. Leave it exactly as-is. All new work is additive — new pages, new routes, new components only.

---

## DESIGN SYSTEM: nuOS

All pages MUST use the nuOS design system. Source reference: `C:\Users\geoff\OneDrive\Documents\nuOS.svg`

**Tokens:**
- Background: `#E6E6E6`
- Cards/surfaces: `white` at 50% opacity with `backdrop-filter: blur(5px)` (glassmorphism)
- Top nav bar: `#0E0E0E` at 80% opacity, `border-radius: 28px`
- Fonts: `D-DIN` (bold headings/labels), `NU Sans` (body text), `Inter` (icons/symbols)
- Accent palette:
  - Green: `#00E87A` — success, active, confirmed
  - Blue: `#6B7FFF` — info, navigation, links
  - Orange: `#FF5F2E` — warning, attention, shift
  - Pink: `#E00061` — critical, prohibited, emphasis
  - Amber: `#FD7600` — pending, waiting
  - Red: `#FF3838` — error, danger
  - Deep Blue: `#4060FF` — display, projection
- Status pills: Dark tinted bg (e.g., `#0A1A0F` for green, `#0A0F1A` for blue, `#1A0A0A` for orange)
- Text: `#0C0C0C` (primary), `#888888` (secondary), `#666666` (tertiary)
- Dividers: `#333333` at 15% opacity
- Rounded corners: `20px` cards, `12px` buttons/inputs, `28px` nav bars, `9-14px` pills
- Shadows: `0 4px 16px rgba(0,0,0,0.08)`

**Vibe:** Light, clean, glassmorphic. NOT dark mode. Feels like a premium OS, not a SaaS dashboard.

---

## CONTEXT: WHAT IS HUMAN.MD

human.md is a biometric-gated portable identity module. Users create a `human.md` file — a markdown identity file with YAML front matter — that tells AI agents who they are, how to interact with them, and what they consent to. The file gets signed cryptographically and stored on a physical Biometric Identity Module (BIM).

The app you're building is the **companion authoring application** — the guided onboarding that helps a human create their human.md file through natural conversation, NOT through forms.

---

## PAGES TO BUILD

### PAGE 1: Signup / Authentication
**Route:** `/signup`

**Auth methods (in this order of visual priority):**
1. Continue with Google (OAuth 2.0)
2. Continue with Apple (Sign in with Apple)
3. Divider: "or"
4. Email + Password (standard form with validation)

**Requirements:**
- nuOS glassmorphic card centered on `#E6E6E6` background
- `human.md` logo/wordmark at top (D-DIN bold, not an image)
- Social auth buttons: full-width, dark bg (`#0E0E0E`), white text, provider icon left-aligned
- Email/password: nuOS-styled inputs with floating labels
- Password: minimum 8 chars, show/hide toggle, strength indicator using accent colors (red→amber→green)
- "Already have an account? Log in" link at bottom
- After successful auth → redirect to `/onboard/1`
- No data collection beyond auth. No marketing opt-ins. No unnecessary fields.

**QC-Clarity focus:** Is it obvious this is for human.md? Is the value prop clear? Can a non-technical person complete signup in under 30 seconds?

**QC-Integrity focus:** OAuth state parameter present? PKCE flow for mobile? Password hashed client-side before transit? Rate limiting on email/password attempts?

---

### PAGES 2–5: Guided Identity Authoring (NU Bot Conversation)

These four pages are the core product experience. The user is having a **conversation with NU** — the bot persona of human.md — who naturally discovers the user's preferences through engaging, adaptive questions. This is NOT a form. It's a dialogue.

**NU Bot persona:**
- Name: NU
- Tone: Warm, curious, direct. Like a thoughtful friend who asks good questions.
- Never robotic. Never corporate. Never uses "Let's get started!" energy.
- Adapts pacing to the user — if they give short answers, NU doesn't push. If they're expansive, NU goes deeper.
- Uses the user's name after they provide it.
- Visual: NU's messages appear in glassmorphic chat bubbles from the left. User responses appear on the right in accent-colored bubbles.

**Interaction pattern (all 4 pages):**
- NU asks a question (appears with typing indicator animation, ~1s delay for natural feel)
- User responds via: tap-to-select chips, free-text input, slider, or toggle — whatever is most natural for that question
- NU acknowledges the response with a brief, genuine reaction before moving to the next question
- Progress indicator: subtle bar at top showing overall completion across all 4 pages
- Users can go back to previous questions within the current page
- "Skip" option on every non-essential question (but NU gently notes what they'll miss)
- All responses are stored in local state — NOTHING goes to a server until the user explicitly saves at the end of Page 5

---

### PAGE 2: "Who You Are"
**Route:** `/onboard/1`
**Covers:** Identity + Communication sections of human.md

**NU's conversation flow:**

1. **Opening:** "Hey — I'm NU. I help you build your human.md — basically a file that tells AI how to treat you. Not how they want to treat you. How YOU want to be treated. Let's start simple."

2. **Name:** "What should I call you?" → free text input
   - NU responds: "Got it, [name]." (uses it from here on)

3. **Pronouns:** "And pronouns?" → chip select: he/him, she/her, they/them, custom (free text), prefer not to say
   - No commentary, just acknowledgment

4. **Language:** "What language do you think in?" → searchable dropdown with common languages, multi-select for multilingual users
   - If multilingual: "Nice — which one should AI default to?"

5. **Timezone:** Auto-detect with confirmation: "Looks like you're in [timezone] — that right?" → confirm/change

6. **Communication tone:** "When an AI talks to you, how should it sound?" → visual spectrum slider:
   - Left: "Casual, like a friend" / Right: "Formal, like a colleague"
   - NU reacts in the tone they chose: casual → "Cool, keeping it chill." / formal → "Understood. I'll maintain that register."

7. **Verbosity:** "How much detail do you want?" → three visual cards:
   - "Just the answer" (minimal) / "Some context" (moderate) / "Explain everything" (verbose)

8. **Format:** "How should AI structure responses for you?" → multi-select chips:
   - Bullet points / Paragraphs / Step-by-step / Code blocks / Whatever fits

**Transition:** NU: "Solid. I already know more about you than most AI ever will. Next, let's talk about how you think." → button: "Continue" → `/onboard/2`

---

### PAGE 3: "How You Think"
**Route:** `/onboard/2`
**Covers:** Cognitive Style + Accessibility sections of human.md

**NU's conversation flow:**

1. **Learning style:** "When you're learning something new, what clicks fastest?" → visual cards with icons:
   - "Show me" (visual) / "Tell me" (auditory) / "Let me try" (kinesthetic) / "Depends on the topic" (adaptive)

2. **Detail preference:** "When someone explains something, do you want the big picture first or the details?" → two-option toggle with preview:
   - "Overview first, then drill down" / "Details first, then zoom out"

3. **Examples:** "Do examples help you, or do they get in the way?" → slider:
   - "Always show examples" ←→ "Skip to the point"

4. **Abstraction:** "How comfortable are you with abstract concepts?" → visual gauge:
   - "Keep it concrete" ←→ "I love abstractions"
   - NU: "That helps AI know when to use metaphors vs. literal explanations."

5. **Accessibility — vision:** "Any visual preferences AI should know about?" → multi-select chips:
   - Large text / High contrast / Screen reader / Dark mode preference / No preference
   - Only shown if relevant, NU: "This isn't just settings — this goes everywhere you go."

6. **Accessibility — hearing:** "Anything for audio?" → multi-select chips:
   - Always show captions / Volume limits / No audio preferred / No preference

7. **Accessibility — motor:** "Any input preferences?" → multi-select chips:
   - Voice control / Switch access / Longer timeouts / No preference

8. **Accessibility — cognitive:** "Any cognitive preferences?" → multi-select chips:
   - Simplified language / Less clutter / Consistent layouts / Step-by-step only / No preference

**Transition:** NU: "[Name], you're building something most people never get — a way to tell every AI exactly what you need, once. Two more rounds." → "Continue" → `/onboard/3`

---

### PAGE 4: "Your Boundaries"
**Route:** `/onboard/3`
**Covers:** Emotional Boundaries + Consent Tiers sections of human.md

**NU's conversation flow:**

1. **Intro:** "This part matters. This is where you draw lines that AI can never cross. No exceptions."

2. **Sensitive topics:** "Are there topics you'd rather AI never bring up, even if it thinks it's being helpful?" → free text + suggested chips:
   - (Common examples as tappable chips, but emphasize free text)
   - NU: "Noted. These go into your prohibited zone — no AI gets to override this."

3. **Emotional triggers:** "Anything that tends to stress you out in digital interactions?" → multi-select chips:
   - Unsolicited advice / Condescending tone / Rushed decisions / Being recorded / Surprises / None of these
   - Optional free text for specifics

4. **De-escalation:** "If you're frustrated with an AI, what should it do?" → visual cards:
   - "Back off, give me space" / "Acknowledge it and simplify" / "Ask me what I need" / "Just stop talking"

5. **Consent — Autonomous:** "What should AI be allowed to do WITHOUT asking you first?" → multi-select chips grouped by category:
   - Adjust text size / Use your preferred name / Set your language / Apply accessibility settings / Remember your preferences
   - NU: "These are your 'green light' actions."

6. **Consent — Confirm:** "What should AI ASK you about before doing?" → multi-select chips:
   - Share your location / Make purchases / Contact people / Schedule things / Access files
   - NU: "Yellow light — AI has to check first."

7. **Consent — Prohibited:** "What should AI NEVER do, period?" → multi-select chips:
   - Share medical data / Record conversations / Override your boundaries / Share with third parties / Make decisions for you
   - NU: "Red light. Non-negotiable. This is the whole point of human.md."

**Transition:** NU: "You just wrote rules that follow you everywhere. Last page — this is where it gets personal." → "Continue" → `/onboard/4`

---

### PAGE 5: "Your World"
**Route:** `/onboard/4`
**Covers:** Context Profiles + Sensory + UI Dashboard + Custom sections of human.md

**NU's conversation flow:**

1. **Profiles intro:** "You're not the same person at work as you are at home. Neither is your human.md. Let's set up some profiles."

2. **Profile creation:** Interactive cards for 3 default profiles:
   - **Work** (blue pill) — "What changes when you're working?" → tone, name display, data visibility
   - **Medical** (pink pill) — "What should an ER know about you?" → allergies, meds, emergency contacts, blood type
   - **Home** (green pill) — "How do you want your space to feel?" → casual tone, sensory preferences
   - Each profile card expands on tap with 3-4 quick questions specific to that context
   - "Add another profile" option for custom profiles

3. **Sensory — lighting:** "How do you like your lighting?" → visual slider with live preview:
   - Warm ←→ Cool (changes the page background tint subtly as they slide)

4. **Sensory — sound:** "Volume preference for AI interactions?" → slider:
   - Quiet ←→ Moderate (no "loud" option — this is about respect)

5. **Sensory — motion:** "How do you feel about animations and movement on screen?" → toggle:
   - "Keep it still" / "Subtle motion is fine" / "I like dynamic interfaces"

6. **Dashboard preview:** "One more thing — your human.md can project YOUR personal dashboard onto any screen. What widgets matter to you?" → drag-to-rank or multi-select:
   - Clock / Weather / Tasks / Calendar / Messages / Music / Custom
   - NU: "Any screen you walk up to becomes yours. That's the point."

7. **Custom:** "Anything else AI should know about you that we haven't covered?" → open text field, generous height
   - NU: "This is your space. No rules here."

8. **Review + Save:** Summary card showing all 10 sections with completion indicators:
   - Each section expandable to review/edit
   - "Save my human.md" button (primary, green accent `#00E87A`)
   - NU: "[Name], you just wrote something no one else has — your own instructions for every AI you'll ever meet."
   - On save: generate the human.md file locally, show success state with next steps (pair BIM, download file, or continue to dashboard)

---

## TECHNICAL REQUIREMENTS

- **Framework:** Match whatever the existing landing page uses (React/Next.js/etc.)
- **State management:** All onboarding state held locally (React state or context) until explicit save on Page 5
- **Routing:** Client-side routing, preserve state on back navigation
- **Responsive:** Mobile-first, works on 375px through 1440px
- **Animations:** Subtle, purposeful. Typing indicator for NU (3 bouncing dots), smooth chip selections, progress bar transitions. Respect `prefers-reduced-motion`.
- **Auth:** Implement with Firebase Auth, Supabase Auth, or Auth.js — whatever integrates cleanest with the existing stack
- **No backend required for onboarding:** All question responses stored in client state, human.md file generated client-side as a markdown string

---

## QC REVIEW CHECKLIST

**Signal (every page — before and after):**
- [ ] Review Brief written and distributed before Clarity/Integrity start
- [ ] Cross-page consistency verified (interaction patterns, tone, component reuse)
- [ ] All Clarity/Integrity conflicts resolved with rationale
- [ ] Fixes verified against original findings (not just "addressed")
- [ ] Final SHIP/HOLD decision issued with confidence level

**QC-Clarity (every page):**
- [ ] Can a non-technical 65-year-old complete this?
- [ ] Is NU's tone warm but not patronizing?
- [ ] Are skip options visible but not encouraged?
- [ ] Does back navigation preserve all answers?
- [ ] Is the progress indicator accurate?
- [ ] Are all interaction targets at least 44x44px (mobile tap)?

**QC-Integrity (every page):**
- [ ] All nuOS tokens correct? (check hex values, font families, border radii)
- [ ] No data sent to any server before Page 5 save?
- [ ] Auth tokens stored securely (httpOnly cookies or secure storage)?
- [ ] Input sanitization on all free-text fields?
- [ ] No console errors, no layout shifts, no flash of unstyled content?
- [ ] Lighthouse accessibility score ≥ 90?

---

## ORCHESTRATOR EXECUTION ORDER

1. Build Page 1 (Signup) → Signal briefs → Clarity + Integrity review → Signal decides → fix if HOLD → Signal ships
2. Build shared components: NU chat bubble, progress bar, chip selector, slider, card selector → Signal briefs → full QC cycle → ship
3. Build Page 2 (Who You Are) → Signal briefs → full QC cycle → ship
4. Build Page 3 (How You Think) → Signal briefs → full QC cycle → ship
5. Build Page 4 (Your Boundaries) → Signal briefs → full QC cycle → ship
6. Build Page 5 (Your World) → Signal briefs → full QC cycle → ship
7. Integration test: full flow from signup through save
8. **Signal final review:** End-to-end consistency pass across all 5 pages — tone drift, pattern drift, state leaks, design token drift. Signal issues final SHIP/HOLD for the complete onboarding flow.

**DO NOT touch the landing page. It is done.**
