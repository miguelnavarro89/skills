---
name: manr:bug-triage
description: "Classify, prioritize, and structure bug reports into actionable triage summaries. Use when a bug report is ambiguous, incomplete, or needs severity assessment. Triggers on: error logs, stack traces, 'something is broken', bug reports missing repro steps, severity disputes, or any unstructured failure signal. Produces a structured triage block with severity, blast radius, reproducibility, and next steps. Complements triage-issue (Jira operations) by handling the assessment layer."
---

# Bug Triage

Turn unclear bug reports into actionable, classified triage summaries. This skill handles the **assessment** — severity, impact, scope, repro, next steps. For Jira operations (duplicate search, ticket creation), hand off to `triage-issue`.

## When to Use

- Bug report lacks repro steps, expected vs. actual, or environment info
- Severity/priority is unclear or disputed
- Raw error log or stack trace pasted with no context
- Need to route an issue (frontend/backend/data/infra) with minimal back-and-forth
- Incident reported and you need to classify before investigation

**When NOT to use:**
- Bug is already well-scoped with reliable repro and clear owner → just fix it
- Need to search Jira for duplicates → use `triage-issue`
- Feature request → use `spec-to-backlog`

---

## Phase 1: Intake — Extract What You Can

Don't ask for things you can already infer. Extract immediately from the raw input:

| Field | What to Look For |
|-------|-----------------|
| **Stage** | local, CI, staging, prod, pipeline, review app |
| **Symptom** | Error message, metric, user complaint, alert title |
| **When** | Onset time, correlation with deploy/config change |
| **Scope** | Users affected, % of traffic, which services/endpoints |
| **Environment** | Browser/device, OS, version/commit, infra (k8s, Lambda, etc.) |
| **System** | Language, framework, key services involved |

If you have enough to classify severity, **do it now** — refine later. Ask for missing context in parallel, never block on it.

### What to Ask For (Not Guess)

Only ask **targeted questions** — never "can you provide more details?"

- "Which environment? prod/staging/local?"
- "How often does this reproduce? Every time, intermittent, or one-off?"
- "Any recent deploys or config changes in the last 24h?"
- "Do you have a request ID or timestamp?"

---

## Phase 2: Classify Severity

Severity = **impact on users**. Priority = **when to fix it**. These are independent axes.

```
P0 — Critical
  Any ONE of:
  - Complete outage of customer-facing system
  - Data loss or corruption actively happening
  - Active security breach
  - SLA breach imminent (<15 min)
  → Immediate. All hands.

P1 — High
  Any ONE of:
  - Major feature broken for significant % of users
  - Latency >2x baseline on critical path
  - Payment/auth/billing flows impaired
  - Error rate >5% above baseline and climbing
  → 30-min response. On-call owns it.

P2 — Medium
  - Non-critical feature broken, workaround exists
  - Error rate elevated but below SLA threshold
  - Staging broken, blocking releases
  - CI pipeline broken, blocking merges
  → 4-hour SLA. Current sprint.

P3 — Low
  - Cosmetic issue, no functional impact
  - Single flaky test (not a pattern)
  - Dev local environment issue
  - Low-CVSS vuln with no active exploit
  → Backlog.
```

### Escalation Triggers

Flag these immediately, regardless of initial severity:

- **Data being written** to a misbehaving system → data integrity risk → escalate
- **Auth/session tokens** in the error path → security implication → P1 minimum
- **Payment processor** in the error path → revenue impact → P1 minimum
- Started **during a maintenance window or migration** → coordinated failure → bump severity

### Severity ≠ Priority

High severity + rare + mitigated = can be lower priority.
Low severity + affects all users + no workaround = higher priority.

Always provide a one-line rationale for both.

---

## Phase 3: Assess Reproducibility

| Level | Meaning | Action |
|-------|---------|--------|
| **Deterministic** | Repros every time with known steps | Ready for engineering |
| **Intermittent** | Repros sometimes, conditions unclear | Need more data — logs, request IDs, timestamps |
| **One-off** | Reported once, can't reproduce | Monitor — add logging/metrics, revisit if recurs |
| **Environment-specific** | Only in certain env/config | Narrow the delta — what's different? |

If repro doesn't exist yet:
1. Ask for **exact steps**, not descriptions ("click X" not "interact with the form")
2. Request **artifacts**: screenshots, request IDs, HAR files, timestamps
3. Ask about **frequency**: "every time", "1 in 10", "happened once"

---

## Phase 4: Structure the Report

Rewrite the bug into this format regardless of how it came in:

```
TRIAGE SUMMARY
──────────────
Title       : [Component] [Error] — [Symptom]
Severity    : P[0-3] — [one-line rationale]
Priority    : [Critical/High/Medium/Low] — [one-line rationale]
Stage       : [local | ci | staging | prod]
Symptom     : [one precise sentence]
Blast Radius: [who/what is affected and how broadly]
Repro       : [deterministic | intermittent | one-off | unknown]
Regressed?  : [yes — worked before deploy X | no | unknown]
SLA Risk    : [yes — breach in ~N min | no | unknown]
Started     : [timestamp or "unknown"]
Trigger     : [deploy | config change | traffic spike | dependency | unknown]

EXPECTED VS. ACTUAL
  Expected: [what should happen]
  Actual  : [what happens instead]

REPRO STEPS
  1. [step]
  2. [step]
  3. [step]

NEXT STEP   : [one concrete action]
OWNER       : [team/person or "unassigned"]
```

### Categorization Tags

Apply one or more:

| Category | Examples |
|----------|---------|
| **UI/UX** | Layout broken, styling off, unresponsive element |
| **Functional** | Feature doesn't work as specified |
| **Performance** | Slow response, high latency, memory leak |
| **Security** | Auth bypass, data exposure, injection |
| **Data** | Corruption, wrong values, missing records |
| **Compatibility** | Browser/device/OS specific |
| **Infrastructure** | Deploy failure, service down, connectivity |

---

## Phase 5: Determine Next Step

Based on the triage, recommend exactly **one** concrete next action:

| Situation | Next Step |
|-----------|-----------|
| P0/P1, prod | Escalate immediately — page on-call, start incident |
| Repro exists, owner clear | Assign to owner with triage summary |
| No repro yet | Request specific artifacts (IDs, timestamps, logs) |
| Prod-only, can't reproduce locally | Add targeted logging/metrics, then monitor |
| Possible regression | Bisect recent deploys, check changelog |
| Duplicate suspected | Hand off to `triage-issue` for Jira search |
| Needs more info | Ask 2-3 targeted questions (not open-ended) |

---

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Conflating severity and priority | Assess independently — high severity can be low priority if rare and mitigated |
| Asking vague questions | Be specific: "which environment?" not "more details please" |
| Skipping repro assessment | Always classify reproducibility, even as "unknown" |
| Over-classifying severity | P0 means "drop everything" — don't cry wolf |
| Under-classifying security issues | Auth/payment/data in error path = P1 minimum until proven otherwise |
| Not checking for regression | Always ask: "Did this work before? When did it stop?" |

---

## Examples

### Raw Error Log → Triage

**Input:** User pastes a stack trace with no context.

```
TypeError: Cannot read properties of undefined (reading 'email')
    at UserProfile.render (UserProfile.tsx:47)
    at processChild (react-dom.js:1234)
```

**Triage:**
1. Infer: Frontend, React component, accessing undefined user object
2. Ask: "Is this in prod or staging? How often does it happen? Any recent deploys to the user service?"
3. Classify: Likely P2 (feature broken, probably has workaround) pending scope info
4. Next step: Check if `user` object is conditionally loaded — likely null guard needed

### Ambiguous Report → Structured Ticket

**Input:** "Payments are broken for some users"

**Triage:**
1. Extract: Payment system affected, subset of users, prod (implied)
2. Escalate flag: Payment processor in error path → P1 minimum
3. Ask: "Which payment flow? Error message or status code? Since when? Approximate % of users affected?"
4. Don't wait — start with P1 classification, refine as info comes in

### Disputed Severity

**Input:** "PM says this is P0 but it's a typo on the settings page"

**Triage:**
1. Assess: Cosmetic issue, no functional impact → P3 severity
2. But: Check if it's user-facing text that causes confusion (e.g., wrong currency symbol) → could be P2
3. Output severity AND priority with rationale
4. Let the data settle the dispute, not opinion
