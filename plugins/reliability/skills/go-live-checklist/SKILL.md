---
name: "go-live-checklist"
description: "Run the pre-launch checklist before taking real money or opening a product to real users — payments, mail delivery, deployed-code identity, and the checks that pass for the wrong reason. Use when going live, switching a payment processor from test to live mode, inviting a first paying customer, launching a product, or asking \"are we ready to launch\"."
---

# Go-live checklist

Run this before the first real customer pays. It exists because the expensive failures at launch are not errors — they are things that report success while delivering nothing.

## First: what is actually deployed?

Before checking anything else, establish that production is running the code you think it is.

- Get the commit or build identifier the **running** service reports, and compare it against the branch head.
- If the service does not expose one, that is finding number one. Add it. Until then every other check below is being run against unknown code.
- Check the deployment platform's actual last successful production deploy — its date and its commit message. A production last deployed weeks ago on a dependency bump is a stale deploy, and a stale deploy presents as a perfectly healthy service.

A failing build in the deployment platform will quietly hold production on old code indefinitely. Check for one before assuming nobody pushed.

## Payments

- **Which mode is live?** Test and live are separate catalogues with separate objects and separate webhook signing secrets. A working test purchase proves the machinery, not the readiness.
- Every purchasable tier resolves to a price ID that **exists in the mode you are about to run in**. Derive the sellable list from the same source the checkout endpoint uses, so a tier cannot be advertised that checkout will reject.
- The webhook endpoint verifies a signed payload and **lands a row**. Assert the row, not the 200.
- Entitlement is granted on payment, and revoked on cancellation and refund.
- Prices and discounts shown on the site match what will actually be charged. A "30% off" string with no coupon behind it is a claim you cannot honour.

## The failure path, not the happy path

This is where the real damage lives. For every action that can fail:

- Force the failure and look at **what the user sees**.
- A success screen rendered over a 4xx/5xx is the worst outcome available — the customer walks away believing something happened. Hunt for it specifically.
- Every call-to-action must either complete or explain and capture. None may dead-end in an error or a fake success.

## Delivery — does the thing they bought arrive?

Most launch checklists verify that payment succeeded and entitlement was granted, then stop. **That is not the product.**

- If a dry-run or test-mode flag exists for mail or notifications, **read its value in production.** These commonly default to "don't send", and the send function commonly returns success for a dry run. A customer can pay, be entitled correctly, and receive nothing, with every instrument green.
- Platform auth mail (signup confirmation, password reset) is usually a **separate path** from application mail and does not use application mail code. Verify both. Check whether the platform's built-in mailer is still in use — it is typically rate-limited, unbranded, and sends from a shared domain, which makes a confirmation email look like phishing at the exact moment trust matters most.
- Get a provider message ID, or record delivery as UNVERIFIED.
- Sending domain: SPF, DKIM and DMARC all present and valid. **Two DMARC records at the same name means no policy is in force** — duplicates nullify each other. Confirm the Return-Path subdomain has its own SPF, since that is what is evaluated.

## Claims and copy

- Every public claim is either provable or removed. Availability, pricing, capability.
- Status pages, incident records and portfolio pages do not describe an outage that has ended, or a beta that has shipped.
- Disclaimers that limit liability are present wherever the product is described commercially.

## The checks themselves

Audit the instruments before trusting them:

- Does the health check assert **content**, or only status codes? A probe that checks seven routes return 200 will stay green through a total inability to take money.
- Does any status field report a subsystem's health **without checking it**? A field that is always false is worse than no field.
- Is the linter or test suite actually running? A step that exits 0 because it found no config, or that carries `continue-on-error: true`, is a passing check that checks nothing.
- Does anything degrade to "looks fine" when it cannot run? Verification that cannot run must read as FAIL.

## Output

For each item: PASS, FAIL, or UNVERIFIED, with the literal evidence. Then the ordered list of what must happen before the first payment, separating what can be done by anyone from what needs the owner — production promotes, credential entry, live-mode switches, and destructive changes.

Do not promote to production, switch a processor to live mode, or move money. Prepare the state, verify it, and hand the irreversible step over with the exact values needed.

