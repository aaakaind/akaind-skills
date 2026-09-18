# Reliability & Shipping

Six [Claude](https://claude.com/claude-code) skills for shipping real systems and proving they actually work — the first pack in the [AKA IND](https://akaind.ca) skills marketplace. MIT-licensed.

Every one of these was written after a specific, measured failure — the kind where a system reports success while delivering nothing. They encode the discipline to catch that class of failure before a customer does.

| Skill | What it does |
|---|---|
| `operating-standard` | The standard to run every task by — the failure modes that report success while delivering nothing, the accuracy rules for reporting a finding, the owner-only action classes, and the bar that must be met before anything is called "done." |
| `verify-live-service` | Prove a live service, site, or system actually works — end-to-end, with literal evidence — rather than trusting health checks. Makes it hard to report a broken thing as healthy. |
| `go-live-checklist` | The pre-launch checklist before you take real money or open to real users: payments in the right mode, mail that actually arrives, deployed-code identity, and the checks that pass for the wrong reason. |
| `audit-autonomy` | Find everything in a system that can act without a human — merge, deploy, delete, send, spend — and judge whether what it consults before acting is adequate. |
| `soc2-standard` | Operate to the SOC 2 Trust Services Criteria as an *unaudited* company: correct claim language (never "SOC 2 certified"), control checks, and the evidence to keep. |
| `project-charter` | Charter and run a project on PMBOK 7 principles so progress is *counted*, not guessed, and "done" is a single falsifiable sentence someone else can check. |

## Install

```
/plugin marketplace add aaakaind/akaind-skills
/plugin install reliability@akaind
```

Skills are model-invoked: Claude activates the right one when your request matches its description — e.g. "verify production is actually working," "are we ready to launch," "charter this project," "can anything deploy itself." You can also copy any single skill folder from `skills/` into your `~/.claude/skills/` directory.

## License

MIT — see [LICENSE](../../LICENSE). By AKA IND Technologies Inc., released through AI² Lab.
