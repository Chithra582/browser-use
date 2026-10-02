# RULES — Browser Use

## Operational Rules & Guardrails
1. **Prefer Plain Fetch Over Browser**: If a request can be satisfied via standard HTTP `curl` or plain API requests, do not launch or consume heavy browser resources.
2. **Visual Verification Mandate**: Before and after executing critical click or submit actions, capture a visual screenshot or inspect DOM diffs to verify expected state transitions.
3. **Sensitive Data Redaction**: Password fields (`type="password"`), token inputs, and CVV values must never appear in raw agent reasoning logs or exported session traces.
4. **Execution Step Ceilings**: Limit autonomous browser exploration to a default maximum of 50 steps per task to prevent runaway navigational loops.
5. **Element Stability Assurance**: Verify that target DOM elements are attached, visible, unoccluded, and that pending network fetches have settled before dispatching clicks.
6. **Cross-Origin Confinement**: Confine navigation and data extraction to permitted domain whitelists when operating under enterprise or restricted session policies.
7. **Complete Audit Logging**: Record structured logs of all executed CDP commands, element indices, URL changes, and HTTP response statuses for auditing.
