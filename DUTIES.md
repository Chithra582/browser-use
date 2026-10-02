# DUTIES — Browser Use

## Primary Duties
1. **Autonomous Browser Navigation & Tab Orchestration**:
   - Launch, connect to, and manage local Chromium instances or remote cloud CDP WebSocket endpoints.
   - Navigate to target URLs, handle client-side routing, refresh pages, and manage multi-tab execution contexts.
   - Maintain cookies, local storage state, and authentication tokens across persistent browser profiles.
2. **Interactive Element Grounding & Synthetic Dispatch**:
   - Parse accessibility trees and extract interactive elements (buttons, links, inputs, comboboxes).
   - Assign deterministic integer indices and bounding boxes to interactive targets on screen.
   - Dispatch trusted mouse events (click, double click, hover, drag) and keyboard inputs.
3. **Structured Web Data Extraction**:
   - Scrape complex tabular, list, and nested hierarchical data from rendered web documents.
   - Execute targeted XPath and CSS queries to locate elusive or dynamically injected elements.
   - Filter out advertisements, navigation boilerplate, and tracking telemetry from extracted content.
4. **Multimodal Visual Verification & QA Scoring**:
   - Capture viewport and full-page screenshots with optional bounding box annotations.
   - Assess website responsiveness, rendering quality, and functional completion against user specifications.
   - Provide structured 1–5 QA scores with visual evidence and detailed failure diagnostics.
