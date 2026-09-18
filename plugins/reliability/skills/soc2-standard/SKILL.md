---
name: "soc2-standard"
description: "Apply the SOC 2 operating standard for an unaudited company — correct claim language, control checks, and evidence requirements. Use when answering a security questionnaire or RFP, writing website or sales copy that touches security/compliance/trust, running an access or credential review, assessing whether a control is actually met, or when anyone asks \"are we SOC 2 compliant\"."
---

# SOC 2 operating standard — unaudited

For a company that operates to the SOC 2 Trust Services Criteria as an internal rule but holds no audit report.

## The claim boundary — check this first, every time

**Never write or say:**

- "SOC 2 compliant" · "SOC 2 certified" · "SOC 2 accredited"
- "SOC 2 Type I" / "Type II" — these name specific audit reports
- "Audited" · "independently assessed" · "attested"
- Any badge, seal, or implied report

There is no SOC 2 certification even for audited firms — it is an attestation report issued by a CPA. Claiming it without one is a misrepresentation that a buyer's security team will find, and it lands hardest on companies whose product is trust.

**Use instead:**

> We operate to the SOC 2 Trust Services Criteria. We are not audited and hold no SOC 2 report. Our controls are documented and reviewed quarterly, and we are happy to walk you through them.

Stronger than silence, safer than a claim, and what a buyer evaluating a company this size expects.

**"Are you SOC 2 compliant?"** → **No**, then the paragraph above. Do not answer "in progress" unless an auditor is engaged and nameable.

Apply the same rule to adjacent frameworks: ISO 27001, HIPAA, PCI DSS. Operating to a standard is not certification against it.

## Assessing a control

Mark every control **Met / Partial / Gap** against observed reality, never intent. A control that has drifted is marked drifted — not quietly left as Met.

The failure mode to hunt: **a control that reports healthy because it never ran.** A CI step that exits 0 with no config. A linter disabled by a version conflict. A `continue-on-error: true` on a check. A health endpoint asserting a subsystem it never queries. A probe checking status codes rather than content. Each of these looks like a passing control and is worth less than no control, because it manufactures confidence.

Before marking anything Met, ask what observable evidence proves it — and whether that evidence could be produced by a broken system.

## Controls that matter most

**Deployed-code identity.** Does the running system report a commit, and does something compare it against what should be deployed? A stale deploy presents as a perfectly healthy service. This is the single highest-value monitoring control.

**Access.** Enforced MFA. Least privilege at the data layer. Secrets never readable from source. Third-party apps and their scopes reviewed on a schedule — apps with write access to CI can rewrite the checks that gate merges.

**Credential lifecycle.** A register with every credential, where it lives, when it was last rotated, and when it expires. Expiry dates held in memory are a future outage.

**Change management.** All change through reviewed PRs. Required status checks on every repo that matters. Nothing merges itself. A rollback path that has been used, not just assumed.

**Backups.** An unrestored backup is a hypothesis. Restore one to a scratch environment at least annually and record the date.

**Billing continuity.** A failed payment method silently taking down infrastructure is an availability incident. Alert on payment failure.

**Data retention.** A stated period for customer data. A buyer will ask; the answer must already exist.

## The solo-operator or small-team problem

Segregation of duties and independent approval cannot be met by one person. Say so plainly rather than fudging — the fudge is what fails inspection.

Describe compensating controls honestly:

- Nothing merges itself; every change is a deliberate human act.
- Automated review is independent of the author — static analysis and security scanning review what an agent proposes.
- Irreversible actions — production promotes, credential handling, money movement, destructive operations — are reserved to the owner regardless of instruction.
- Verification asserts observable output from outside the system, not the system's own account of its health.

Compensating controls are not the original control. Do not claim they are.

## Evidence to keep

Dated, and real: access reviews, the credential register, PR history as the change record, incident write-ups with timeline and correction, monitoring run history, and backup restore tests.

Correct incident records **in place** with strikethroughs rather than rewriting them. The record of having been wrong, and when it was corrected, is itself evidence of a functioning control environment.

## Review

Quarterly. Re-read the claim boundary before any security questionnaire, RFP response, or website copy touching security, compliance, or trust.

