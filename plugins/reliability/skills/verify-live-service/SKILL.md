---
name: "verify-live-service"
description: "Verify whether a live service, site, or system is actually working — end-to-end proof rather than health checks. Use when asked to \"test the systems\", \"is X actually working\", \"verify production\", \"run a systems test\", \"check whether the fix landed\", or before going live, taking payment, or telling a customer something works. Also use whenever a health check, dashboard, or test suite is reporting green and there is reason to doubt it."
---

# Verify a live service

The purpose of this skill is to establish what is **actually true** about a running system, with evidence, and to make it hard to report a broken thing as healthy.

## Why this exists

In the production systems I've worked on, every significant failure has been silent, and each one reported healthy while broken:

- Mail returned `ok:true` while sending nothing for months.
- Checkout rendered a success screen over 503 responses.
- A `/health` endpoint returned 200 while every database endpoint returned 500.
- An expired token produced a *working* org scan covering 1 repository instead of 67.
- A delivery probe was green 8 runs out of 8 while the site could not take money — it asserted status codes on seven routes and never asserted *what* was being served.
- A paginated DNS table rendered 20 of 23 records; the three off-screen rows were the entire mail configuration, and their absence became a planned "fix" for a problem that did not exist.
- Five rounds of diagnosis were spent reading a hardcoded template string as if it were a live API response.

The through-line: **liveness is not correctness, a return value is not evidence, and a partial read is not a read.** A check that degrades to "looks fine" is worse than no check, because it manufactures confidence.

## The rules

**1. Assert on observable output, from outside the system where possible.**
A provider message ID. A row in the database. An HTTP status from a plain `curl`. A commit SHA the running app reports. A count. Not a log line the app wrote about itself.

**2. Assert magnitude, not just success.**
Wherever a shrunken-but-passing result is possible, assert the number. "The scan succeeded" hid a 67 → 1 collapse. "10 samples load" is a real assertion; "samples load" is not.

**3. Reconcile totals before concluding anything is absent.**
If a UI says "23 records" and you counted 20, you have not finished reading. Prefer a filter or search that returns a complete small set over scrolling a large one. Absence in a truncated view is not absence.

**4. Never present a reconstructed string as evidence.**
If the live response was not captured, say so. Do not infer what a remote system said and then report the inference as what it said.

**5. Fail closed.**
If verification cannot run, that must not read as pass. **UNVERIFIED is a real verdict and an expected one.** Use it rather than guessing. A report with five honest UNVERIFIEDs is worth more than one with five assumed PASSes.

**6. Hunt the fake-success case specifically.**
The worst failure is not an error — it is a success screen over a failed request. For every user-facing action that can fail, check what the user sees when it does. This is where the real damage lives, because the customer walks away believing something happened.

## What to test

Adapt to the system, but cover these classes:

- **Every call-to-action, followed to its terminus.** Not "the button exists" — click it, record the real HTTP status, record what the user sees. Especially anything that takes money.
- **Public promises.** If the site says "10 samples, no account", verify the count is 10 and that they load with no session or cookie. Spot-check that linked citations resolve — a soft-404 returning 200 is not a resolved link.
- **Auth.** Test the endpoint, not the UI. Confirm the actual signup/login state rather than repeating what a status page claims.
- **Payments.** Which mode is it wired to? Test and live have separate catalogues and separate webhook signing secrets. Does a session create? Does a signed webhook verify and land a row?
- **Mail.** The repeat offender. Do not trust a 200. Get a provider message ID or declare it UNVERIFIED. Note that platform auth mail (e.g. Supabase Auth) is a **separate path** from application mail and does not use application mail code — test both.
- **Scheduled or background pipelines.** Is anything actually producing and delivering, or is it a priced tier with no producer behind it?
- **Notifications and monitoring.** Does a watch actually fire, end to end, to a real destination?
- **Safety features.** Test the fail-closed direction *and* the pass direction. A link checker that rejects all 14 hostile inputs but can also never mark a legitimate URL safe is inert, not safe.
- **What is actually deployed.** Compare the commit the running service reports against the branch head. A stale deploy presents as a fully healthy service serving old code — the most expensive failure mode in practice and the hardest to see.

## Output

One report. For each system: what was tested, the **literal evidence**, and PASS / FAIL / UNVERIFIED.

Then rank findings by whether they block the next real business outcome — taking money from a first paying customer, shipping to a real user, surviving a specific deadline. Not by severity in the abstract.

Close with:

- **Honest unknowns**, listed explicitly.
- **Things that look fine and aren't** — the checks that pass for the wrong reason. Call these out separately; they are the most valuable part of the report.
- **Any side effects you caused** (test rows written, records created), so they can be cleaned up.

## Boundaries

Test first, fix second, and keep them separate — a report written by someone mid-fix is a report about intentions. Do not deploy or promote to production; that decision belongs to the owner. Do not touch secrets. Do not delete anything. Note what needs a human's hands (dashboard toggles, credential entry, destructive changes) and hand those over cleanly with the exact values needed.

