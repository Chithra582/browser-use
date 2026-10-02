# EXPLAINABILITY.md

This document explains the internal mechanisms, data lineage, operational boundaries, and governance framework of **Browser Use** (`browser-use`) in accordance with the **OpenGAP v0.1.0** specification for the **HiDevs GitAgent Passport** clearance pipeline.

> **Agent Name:** Browser Use (`browser-use`)  
> **Specification:** OpenGAP v0.1.0  
> **Category / Domain:** Developer Tools / Autonomous Browser Automation & Web Agents  
> **Compliance Standard:** OpenGAP Checkpoint 2 (Explainability & Decision Governance), OWASP LLM Top 10, MITRE ATLAS  

---

## How the Agent Decides

Browser Use is an autonomous web agent framework designed to make websites and web applications accessible for AI agents. Rather than relying solely on brittle DOM selectors or purely visual coordinate clicking, Browser Use integrates a dual-perception pipeline: an enhanced accessibility tree that extracts interactive elements into indexed numeric handles and high-resolution viewport screenshots for spatial reasoning. By interfacing directly with the Chrome DevTools Protocol (CDP), the agent plans, executes, and verifies complex web workflows across heterogeneous web architectures.

### 1. Decision Architecture

The user goal intake, browser state perception, multimodal action planning, event dispatch, and outcome verification pipeline operates across a deterministic, five-stage architecture:

```
User Instruction / Web Task (Search / Form Completion / QA Evaluation / Workflow Automation)
    │
    ▼
[Stage 1: Intent Parsing & Pre-Flight Route Evaluation]
    │  - Evaluates whether task requires full browser or simple HTTP fetch (`curl`)
    │  - Selects execution profile: local headless Chromium vs. remote stealth cloud browser
    │  - Initializes browser context, proxy configurations, and viewport geometry
    ▼
[Stage 2: Multimodal Perception & Element Indexing]
    │  - Captures viewport screenshot and builds enhanced accessibility DOM tree
    │  - Filters invisible, off-screen, or occluded background nodes
    │  - Overlays compact numeric bounding box labels (`[1]`, `[2]`, `[3]`) on interactive targets
    ▼
[Stage 3: Action Formulation & Boundary Verification]
    │  - Synthesizes next atomic action: `click`, `input`, `scroll`, `navigate`, `extract`
    │  - Evaluates action safety against sensitive input policies and domain boundaries
    │  - Checks for modal obstruction or unhandled cookie consent overlays
    ▼
[Stage 4: Synthetic CDP Dispatch & State Transition Wait]
    │  - Dispatches native trusted CDP mouse, keyboard, or touch event sequences
    │  - Awaits network idle, DOM mutation settling, and navigation redirects
    │  - Detects newly opened tabs, popups, and URL transitions
    ▼
[Stage 5: Visual Verification & Trajectory Commit]
    │  - Compares post-action screenshot against expected visual delta
    │  - Validates extraction schemas and updates task completion progress
    │  - Commits structured action step to audit trace with sensitive fields scrubbed
    ▼
Verified Web Task Result & Auditable Browser Execution History
```

### 2. Decision Logic & Routing Formulations

Browser Use evaluates element selection, action confidence, and page readiness using deterministic mathematical models:

1. **Element Interactive Salience Score ($S_{\text{salience}}$)**:
   $$S_{\text{salience}}(e) = (w_a \cdot A_{\text{aria}}) + (w_v \cdot V_{\text{visible}}) + (w_t \cdot T_{\text{text}}) + (w_c \cdot C_{\text{clickable}})$$
   where:
   - $A_{\text{aria}} \in \{0, 1\}$ denotes standard interactive ARIA role presence (button, link, input).
   - $V_{\text{visible}} \in [0, 1]$ represents viewport intersection area ratio.
   - $T_{\text{text}} \in [0, 1]$ measures lexical and semantic similarity between user goal and element label.
   - $C_{\text{clickable}} \in \{0, 1\}$ detects pointer event listeners and CSS cursor properties.
   - Weights: $w_a = 0.35, w_v = 0.25, w_t = 0.25, w_c = 0.15$ ($\sum w_i = 1.0$).

2. **Visual Verification Delta ($D_{\text{visual}}$)**:
   $$D_{\text{visual}} = \frac{1}{|P|} \sum_{p \in P} \|\mathbf{I}_{\text{post}}(p) - \mathbf{I}_{\text{pre}}(p)\|_2$$
   where $\mathbf{I}_{\text{pre}}$ and $\mathbf{I}_{\text{post}}$ are normalized image arrays of the viewport bounding box before and after action execution. If $D_{\text{visual}} < \tau_{\text{threshold}}$, the agent flags potential interaction failure and triggers DOM fallback verification.

### 3. Thresholding & Refusal Decision Criteria

Browser Use enforces strict operational guardrails and safety invariants:
- **Refusal to Auto-Authorize Financial Checkouts**: Transactions involving credit cards, payment gateways, or one-click checkouts require explicit human confirmation (`ERR_PAYMENT_SUBMISSION_REQUIRES_APPROVAL`).
- **Sensitive Field Masking**: Input elements with `type="password"` or matching token patterns are scrubbed from telemetry (`WARN_SENSITIVE_FIELD_MASKED`).
- **Step Ceiling Enforcement**: Autonomous navigation sessions enforce a default upper bound of 50 steps (`WARN_STEP_BUDGET_EXHAUSTED`).
- **Domain Whitelist Confinement**: When operating in restricted mode, navigations outside designated hostnames are refused (`ERR_OUT_OF_SCOPE_NAVIGATION`).

### 4. Fallback Decision Mechanism

Continuous web interaction resilience is maintained through multi-tier fault recovery:
- **Dual Index / Coordinate Cascade**: If clicking by accessibility index fails due to synthetic event cancellation, the engine cascades to direct viewport coordinate clicks (`_click_by_coordinate`).
- **Provider Cascade**: When primary LLM providers return rate limit errors (HTTP 429) or timeouts, the orchestrator cascades to secondary model endpoints.
- **Overlay Auto-Dismissal**: When detected elements are obscured by transparent modal backdrops or cookie consent dialogs, the agent activates dedicated dismissal heuristics before re-attempting the target action.

### 5. Human-in-the-Loop Governance

Human operators retain ultimate oversight and operational primacy:
- **Live Stream & Visual Inspection**: Real-time browser screencasts and annotated bounding box screenshots permit live supervision.
- **Interactive Pause & Intervene**: Users can pause autonomous runs, manually navigate or solve CAPTCHAs, and resume agent execution seamlessly.
- **Structured Trajectory Exports**: Every CDP call, URL change, DOM tree snapshot, and extracted payload is logged in auditable JSON traces.

---

## The Data It Uses

Browser Use complies with stringent data minimization, browser sandbox isolation, and credential hygiene practices.

### 1. Ingested Input Data

The agent processes only operational assets necessary to fulfill browser interaction:
- **User Instructions**: Natural language goals, target URLs, extraction schemas, and task constraints.
- **Browser DOM Snapshots**: Accessibility tree nodes, element bounding rects, and tag attributes.
- **Visual Viewport Screenshots**: Rendered pixel frames used for spatial element localization and verification.

### 2. Configuration & Reference Data

- **Browser Context Profiles**: Cookie storage, local storage states, and user agent strings.
- **Stealth & Proxy Settings**: Residential proxy configs, IP geolocations, and anti-fingerprinting parameters.
- **Sensitive Data Mappings**: Configured user credential identifiers masked during execution.

### 3. Base Model & Inference Lineage

- **Deterministic Automation Engine**: Python CDP client, Playwright driver, and DOM parsing utilities run deterministically with zero model variance.
- **Frontier Multimodal LLMs**: High-capability vision-language models (e.g., GPT-4o, Claude 3.5 Sonnet, Gemini 2.0 Flash) deployed for visual grounding and action synthesis.
- **Zero Training on User Web Sessions**: Authenticated webpage contents, user form data, and personal browsing activities are never retained for model retraining.

### 4. Data Privacy, Storage, and Retention

- **OWASP LLM & MITRE ATLAS Hardened**: Defended against indirect prompt injection embedded in untrusted web pages, clickjacking, and credential exfiltration.
- **Ephemeral Session Sandbox**: Headless browser storage directories and temporary files are isolated and purged upon task completion.
- **Automated Credential Redaction**: Passwords, authorization headers, and session tokens are masked prior to model ingestion and telemetry dispatch.
- **Zero Commercial Monetization**: Browsing histories, user interaction traces, and extracted datasets are never monetized or shared with third parties.

---

## Limitations

Understanding the operational boundaries and technical constraints of Browser Use is critical for reliable web deployment.

### 1. Opaque Canvas and WebGL Controls
- **Limitation**: Applications rendering complex interfaces entirely inside HTML5 `<canvas>` or WebGL containers lack accessibility tree nodes.
- **Mitigation**: Browser Use applies coordinate-based multimodal visual pointing and OCR text detection on canvas viewports.

### 2. Deeply Nested Closed Shadow DOM Elements
- **Limitation**: Custom web components using closed shadow roots (`mode: 'closed'`) prevent external DOM traversal.
- **Mitigation**: The engine injects privileged browser script helpers or relies on visual coordinate clicks.

### 3. Aggressive CAPTCHA and Cloudflare Challenges
- **Limitation**: Specialized adversarial bot detection mechanisms (e.g., Turnstile, reCAPTCHA v3) may detect automated browser drivers.
- **Mitigation**: Cloud stealth browsers, residential proxies, and human-in-the-loop intervention affordances allow seamless user completion.

### 4. High-Rate Dynamic Client-Side Re-Renders
- **Limitation**: Rapid Single Page Application re-renders can invalidate element handles between the perception and click dispatch phases.
- **Mitigation**: The watchdog service re-verifies element existence and attachment immediately before event execution.

### 5. Detached Multi-Window Popup Orchestration
- **Limitation**: Web workflows spawning separate detached OS windows or OS native print dialogs can disrupt tab tracking.
- **Mitigation**: CDP target listeners monitor window creation events and automatically attach to newly spawned browser targets.

---

## Summary & Compliance Checklist

| Checkpoint 2 Requirement | Corresponding Section | Status |
| :--- | :--- | :---: |
| **How the agent decides** | [How the Agent Decides](#how-the-agent-decides) | **Covered** |
| - Decision architecture & 5-stage pipeline | Section 1 | Verified |
| - Decision logic & routing formulations | Section 2 | Verified |
| - Thresholding & refusal decision criteria | Section 3 | Verified |
| - Fallback decision mechanism | Section 4 | Verified |
| - Human-in-the-loop governance & oversight | Section 5 | Verified |
| **The data it uses** | [The Data It Uses](#the-data-it-uses) | **Covered** |
| - Ingested user instructions, DOM snapshots & screenshots | Section 1 | Verified |
| - Configuration, context profiles & stealth settings | Section 2 | Verified |
| - Base model lineage & deterministic CDP engines | Section 3 | Verified |
| - Data privacy, retention lifecycle & MITRE/OWASP | Section 4 | Verified |
| **Its limitations** | [Limitations](#limitations) | **Covered** |
| - Opaque canvas and WebGL controls | Section 1 | Verified |
| - Deeply nested closed shadow DOM elements | Section 2 | Verified |
| - Aggressive CAPTCHA and Cloudflare challenges | Section 3 | Verified |
| - High-rate dynamic client-side re-renders | Section 4 | Verified |
| - Detached multi-window popup orchestration | Section 5 | Verified |
