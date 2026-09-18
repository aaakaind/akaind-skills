# AKA IND Skills

Public [Claude](https://claude.com/claude-code) skill packs from **AKA IND Technologies Inc.**, released free under the MIT license through [AI² Lab](https://akaind.ca), our R&D and open-source arm.

These skills are distilled from running a real, solo-operated multi-venture business with an agent fleet — hard-won operating discipline made reusable. Each is vendor-neutral: internal infrastructure, private paths, account identifiers, and financials have been removed so you can drop them into your own setup.

This is a **marketplace that holds multiple packs**. Add it once, then install whichever packs you want.

```
/plugin marketplace add aaakaind/akaind-skills
/plugin install reliability@akaind
```

## Packs

| Pack | Status | Skills |
|---|---|---|
| **[Reliability & Shipping](./plugins/reliability)** | ✅ Available | `operating-standard`, `verify-live-service`, `go-live-checklist`, `audit-autonomy`, `soc2-standard`, `project-charter` |
| **AI Operator Fleet** | Planned | A multi-agent operator system: lane operators (HQ, tech, finance, legal, sales, media, AI-engineering, archivist), the Council / Inspectorate / Watchtower / Growth-Engine systems, and Vanessa, a chief-of-staff control layer. |
| **Content & Growth** | Planned | Scroll-stopping social content, multi-channel program orchestration, media and sales operators. |
| **Canadian Finance & Tax** | Planned | CCPC tax strategy, T2 corporate-return prep, and finance-operations discipline. |
| **Design & Build** | Planned | An industrial-designer + full-stack build persona. |

Each new pack is added to this same marketplace — install it, and you're up to date.

## How skills work

Skills are **model-invoked**: Claude reads each skill's description and pulls in the matching one when your request calls for it. You don't invoke them by name — just ask for the thing (e.g. "verify production is actually working," "are we ready to launch") and the right skill activates. Any single skill also works standalone: copy its folder into `~/.claude/skills/` (personal) or a project's `.claude/skills/`.

## Provenance

Authored by AKA IND Technologies Inc.; released through AI² Lab. Sanitized for public release — no private infrastructure, credentials, account identifiers, or financials remain.

## License

MIT — see [LICENSE](./LICENSE). Use them, fork them, adapt them. Attribution appreciated, not required.
