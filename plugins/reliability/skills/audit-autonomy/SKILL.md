---
name: "audit-autonomy"
description: "Find everything in a system that can act without a human, and check that what it consults before acting is adequate. Use when auditing automation, reviewing CI/CD permissions, asking \"can anything merge or deploy itself\", after an unexpected change appears, before granting an agent or bot write access, or when writing code that could merge, deploy, delete, send, or spend."
---

# Audit autonomy

Find everything that can act without a human, and judge whether what it consults before acting is good enough.

## The premise

Grants of autonomy are invisible by default. They are made once, in a moment when they seemed reasonable, and then they are not surfaced anywhere a person routinely looks. Nobody misreports them — the information simply isn't in the places people check.

A real case: GitHub-native auto-merge had been armed on Dependabot PRs for four days. A PR merged itself, unattended, minutes after unrelated work turned its checks green. Every review had stated auto-merge was off, honestly, because an armed PR looks **identical** to an unarmed one in every view except an explicit `autoMergeRequest` query. Three separate merge classifiers had been built to guard that decision, and none of them knew this path existed.

**A control asserted from belief rather than measurement is not a control.**

## What to sweep for

Anything that can take an irreversible or externally-visible action with no human in the loop:

- **Merge** — native auto-merge armed on open PRs; bots or workflows calling merge APIs; branch protection that permits it
- **Deploy** — push-triggered deploys, deploy hooks, anything where merging a PR *is* a production release
- **Delete** — retention jobs, cleanup scripts, anything with delete scope
- **Send** — mail, notifications, messages to customers or public channels
- **Spend** — anything that provisions paid resources or moves money
- **Write to source** — workflows with `contents: write`, `pull-requests: write`, and especially `workflows: write`

Then the second-order ones, which are easy to miss:

- **Scheduled jobs that mutate rather than report.** A cron that reads is a monitor. A cron that writes is an unattended actor.
- **Apps and integrations, and their scopes.** An app that can rewrite a workflow can make a required check report green without doing anything, because required checks match by **name**.
- **Tokens with write access to a protected branch**, including ones held by agents.
- **Webhooks** that trigger action on push or on external events.

## How to check — measure, never infer

**Query live state. Do not read configuration and conclude.** Configuration describes intent; state describes reality, and the gap between them is exactly where this class of surprise lives.

- Auto-merge: query the per-PR auto-merge field across all open PRs. Its absence from the PR list means nothing.
- Permissions: read the effective token permissions on a workflow run, not the defaults you assume.
- Apps: enumerate installed apps and their granted scopes, not the ones requested at install.
- Scheduled jobs: list them and read what each does, not what its name suggests.

If a check can only be satisfied by reading source, treat the answer as unverified.

## Judging the gate

Finding an autonomous actor is not the finding. **The finding is what it consulted before acting.**

The auto-merge case was not dangerous because it merged. It was dangerous because its only test was a semver string from dependency metadata — under which a "minor" bump of an auth library merges unattended, which is precisely how a production site had broken previously.

For each actor, ask:

1. What does it check before acting?
2. Could that check pass for something that should have been stopped? Construct the specific case.
3. What was assumed to be the backstop, and would it actually catch the case in (2)? Branch protection catches a PR that fails a build; it does not catch a PR that is green and wrong.
4. Is the action reversible, and has the reversal ever been performed?

A gate that only sees metadata cannot judge intent. A gate that only sees exit codes cannot judge magnitude.

## Writing code that acts

When adding anything that could merge, deploy, delete, send, or spend:

- **Default to proposing, not acting.** Open the PR; do not merge it. Draft the message; do not send it. The cost of a human clicking is far below the cost of the class of mistake this prevents.
- **Take the narrowest permission that works.** `read` unless `write` is genuinely required, and scoped to what it touches.
- **Make it discoverable.** A grant of autonomy findable only by reading source is one nobody will remember making. Name it in the PR description explicitly: *this can merge / deploy / delete / send / spend.*
- **Make the gate legible.** If it acts on a condition, the condition should be inspectable and testable in isolation, not buried in a conditional.
- **Fail closed.** If the gate cannot evaluate, do not act.

## Cadence

Sweep on a schedule, and after any unexpected change appears. Record the result with a date — including "nothing armed", which is only meaningful if it was measured rather than assumed.

