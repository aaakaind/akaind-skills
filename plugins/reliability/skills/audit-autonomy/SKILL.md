---
name: "audit-autonomy"
description: "Find everything in a system that can act without a human, and check that what it consults before acting is adequate. Use when auditing automation, reviewing CI/CD permissions, asking \"can anything merge or deploy itself\", after an unexpected change appears, before granting an agent or bot write access, or when writing code that could merge, deploy, delete, send, or spend."
---

# Audit autonomy

Find everything that can act without a human, and judge whether what it consults before acting is good enough.

## The premise

Grants of autonomy are invisible by default. They are made once, in a moment when they seemed reasonable, and then they are not surfaced anywhere a person routinely looks. Nobody misreports them â€” the information simply isn't in the places people check.

A real case: GitHub-native auto-merge had been armed on Dependabot PRs for four days. A PR merged itself, unattended, minutes after unrelated work turned its checks green. Every review had stated auto-merge was off, honestly, because an armed PR looks **identical** to an unarmed one in every view except an explicit `autoMergeRequest` query. Three separate merge classifiers had been built to guard that decision, and none of them knew this path existed.

**A control asserted from belief rather than measurement is not a control.**

## What to sweep for

Anything that can take an irreversible or externally-visible action with no human in the loop:

- **Merge** â€” native auto-merge armed on open PRs; bots or workflows calling merge APIs; branch protection that permits it
- **Deploy** â€” push-triggered deploys, deploy hooks, anything where merging a PR *is* a production release
- **Delete** â€” retention jobs, cleanup scripts, anything with delete scope
- **Send** â€” mail, notifications, messages to customers or public channels
- **Spend** â€” anything that provisions paid resources or moves money
- **Write to source** â€” workflows with `contents: write`, `pull-requests: write`, and especially `workflows: write`

Then the second-order ones, which are easy to miss:

- **Scheduled jobs that mutate rather than report.** A cron that reads is a monitor. A cron that writes is an unattended actor.
- **Apps and integrations, and their scopes.** An app that can rewrite a workflow can make a required check report green without doing anything, because required checks match by **name**.
- **Tokens with write access to a protected branch**, including ones held by agents.
- **Webhooks** that trigger action on push or on external events.

## How to check â€” measure, never infer

**Query live state. Do not read configuration and conclude.** Configuration describes intent; state describes reality, and the gap between them is exactly where this class of surprise lives.

- Auto-merge: query the per-PR auto-merge field across all open PRs. Its absence from the PR list means nothing.
- Permissions: read the effective token permissions on a workflow run, not the defaults you assume.
- Apps: enumerate installed apps and their granted scopes, not the ones requested at install.
- Scheduled jobs: list them and read what each does, not what its name suggests.

If a check can only be satisfied by reading source, treat the answer as unverified.

## Judging the gate

Finding an autonomous actor is not the finding. **The finding is what it consulted before acting.**

The auto-merge case was not dangerous because it merged. It was dangerous because its only test was a semver string from dependency metadata â€” under which a "minor" bump of an auth library merges unattended, which is precisely how a production site had broken previously.

›ÜˆXXÚXİÜ‹\ÚÎ‚‚ŒKˆÚ]Ù\È]ÚXÚÈ™Y›Ü™HXİ[™ÏÂŒ‹ˆÛİ[]ÚXÚÈ\ÜÈ›ÜˆÛÛY][™È]Úİ[]™H™Y[ˆİÜYÈÛÛœİXİHÜXÚYšXÈØ\ÙK‚ŒËˆÚ]Ø\È\Üİ[YYÈ™HH˜XÚÜİÜ[™Ûİ[]XİX[HØ]ÚHØ\ÙH[ˆ
ŠOÈœ˜[˜Ú›İXİ[ÛˆØ]Ú\ÈHˆ]˜Z[ÈHZ[È]Ù\È›İØ]ÚHˆ]\ÈÜ™Y[ˆ[™Ü›Û™Ë‚ˆ\ÈHXİ[Ûˆ™]™\œÚX›K[™\ÈH™]™\œØ[]™\ˆ™Y[ˆ\™›Ü›YYÂ‚HØ]H]Û›HÙY\ÈY]Y]HØ[››İYÙH[[ˆHØ]H]Û›HÙY\È^]ÛÙ\ÈØ[››İYÙHXYÛš]YK‚‚ˆÈÈÜš][™ÈÛÙH]XİÂ‚•Ú[ˆY[™È[][™È]Ûİ[Y\™ÙK\ŞK[]KÙ[™ÜˆÜ[™‚‚‹H
Š‘Y˜][È›ÜÜÚ[™Ë›İXİ[™ËŠŠˆÜ[ˆHÈÈ›İY\™ÙH]ˆ˜YHY\ÜØYÙNÈÈ›İÙ[™]ˆHÛÜİÙˆH[X[ˆÛXÚÚ[™È\È˜\ˆ™[İÈHÛÜİÙˆHÛ\ÜÈÙˆZ\İZÙH\È™]™[Ë‚‹H
Š•ZÙHH˜\œ›İÙ\İ\›Z\ÜÚ[Ûˆ]ÛÜšÜËŠŠˆ™XY[›\ÜÈÜš]X\ÈÙ[Z[™[H™\]Z\™Y[™ØÛÜYÈÚ]]İXÚ\Ë‚‹H
Š“XZÙH]\ØÛİ™\˜X›KŠŠˆHÜ˜[Ùˆ]]Û›Û^Hš[™X›HÛ›HH™XY[™ÈÛİ\˜ÙH\ÈÛ™H›Ø›ÙHÚ[™[Y[X™\ˆXZÚ[™Ëˆ˜[YH][ˆHˆ\ØÜš\[Ûˆ^XÚ]Nˆ
\ÈØ[ˆY\™ÙHÈ\ŞHÈ[]HÈÙ[™ÈÜ[™Š‚‹H
Š“XZÙHHØ]HYÚX›KŠŠˆYˆ]XİÈÛˆHÛÛ™][Û‹HÛÛ™][ÛˆÚİ[™H[œÜXİX›H[™\İX›H[ˆ\ÛÛ][Û‹›İ\šYY[ˆHÛÛ™][Û˜[‚‹H
Š‘˜Z[ÛÜÙYŠŠˆYˆHØ]HØ[››İ]˜[X]KÈ›İXİ‚‚ˆÈÈØY[˜ÙB‚”İÙY\ÛˆHØÚY[K[™Y\ˆ[H[™^XİYÚ[™ÙH\X\œËˆ™XÛÜ™H™\İ[Ú]H]H8 %[˜ÛY[™È››İ[™È\›YY‹ÚXÚ\ÈÛ›HYX[š[™Ù[Yˆ]Ø\ÈYX\İ\™Y˜]\ˆ[ˆ\Üİ[YY‚‚