---
name: "project-charter"
description: "Charter and run a project on PMBOK 7 principles, tuned for a one-operator or small-team context — use when starting any new project or workstream, when an existing one has drifted and nobody can say what \"done\" means, when progress needs to be countable rather than estimated, or when the user asks to set up, scope, plan, or close out a project properly."
---

# Project charter — PMBOK 7 tailored

A project is chartered when four questions have written answers: **why it exists, who decides, what done means, and what would make it fail.** Until then it is a pile of tasks, and a pile of tasks cannot be reported on — which is how work drifts for weeks while feeling busy.

Built on the **PMBOK Guide, Seventh Edition** — principles and performance domains rather than the 49 processes of the Sixth. That shift matters here. PMBOK 7 is outcomes-based and explicitly **tailoring-first**, which is what makes it usable by one operator running an agent fleet. A process-heavy framework would be ceremony nobody performs.

Two of its twelve principles do most of the work in practice: **Tailor based on context** (why this is one page, not a binder) and **Build quality into processes and deliverables** (why every deliverable carries a measurement rather than an opinion).

---

## When to charter

Charter anything that outlives one working session or touches more than one system. Do **not** charter a single fix — that is a ticket, and forcing a charter onto it is the ceremony this document exists to avoid.

If unsure: can you name a deliverable someone else could verify without asking you? If no, charter it. If yes, ticket it.

---

## The charter — one page

Written before the work, not after. A section that cannot be filled in is the first finding.

### 1 · Why this exists — *Delivery domain*

One paragraph. The problem in the world, not the solution. **State what happens if nothing is done.** PMBOK 7 frames delivery as value rather than output; a project that cannot say what value is lost by skipping it is usually a preference, and is cheaper to kill at charter time than at 40% complete.

### 2 · Done means — *Delivery + Measurement*

The single sentence that ends the project, **falsifiable by someone who was not involved.**

- Weak: *"the rename is complete"*
- Strong: *"no file, config, DNS record, webhook or callback references the old host, and a real charge succeeds end to end on the new one"*

The second can be checked. The first is an opinion — and an opinion is what allowed a rename to be declared done because DNS resolved.

### 3 · Explicitly out of scope

What this project is **not** doing, especially the adjacent things someone will assume are included. Unwritten exclusions become mid-project arguments.

### 4 · Deliverables — *Planning domain*

Decompose until each item is **binary: done or not done.** A deliverable that is "70% done" is several deliverables that have not been separated yet.

Progress is **items done ÷ items total, counted** — never estimated, never weighted by feel. If a bar cannot be produced this way, the decomposition is unfinished.

### 5 · Who decides — *Stakeholder + Team domains*

PMBOK 7's Team domain assumes humans. Here the team is largely agents, so most of it — shared ownership, psychological safety — does not transfer. **One part transfers completely: clear authority.** Separate *doing* from *deciding*, and enumerate owner-only actions **at charter time rather than discovering them mid-flight**:

- credentials and secrets — values never handled by an agent, only names
- moving money, live pricing, anything billable
- deleting anything (the standard is archive, never delete)
- production writes, merges, deploys
- anything irreversible

A project that hits an unenumerated owner-only action stalls at exactly the wrong moment.

### 6 · Risks — *Uncertainty domain*

Not generic. Start from the register below — failure modes real systems have actually produced — then add project-specific ones. Each risk gets: what happens, **how you would notice**, and what you would do. PMBOK 7 treats uncertainty as a domain to be navigated continuously, not a list filed once at kickoff.

---

## Measurement — the domain that matters most here

PMBOK 7 made Measurement a performance domain in its own right and warned specifically about **misleading measures**: proxies that are easy to collect and don't mean what they appear to. That warning is the diagnosis of nearly every failure seen in practice.

For every deliverable answer: **what would I run, and what would it return, to prove this?**

| Instead of | Write |
|---|---|
| "webhook is configured" | "Stripe delivery log shows a 2xx in the last hour for each subscribed event" |
| "secret is added" | "workflow dispatched by hand reports the count it found, not exit 0" |
| "prices are correct" | "a live charge takes the advertised amount and the receipt states the same figure" |
| "the fix is merged" | "a request with another account's id returns 403" |
| "monitoring is set up" | "the alert fired once during a deliberately broken run" |

Three rules follow:

- **Green is not evidence.** A job that passes without asserting a magnitude proves only that it ran. Require the number.
- **Absence must be loud.** If the check cannot evaluate — missing key, missing config — it must fail, not pass with a warning. A warning on a passing run is a warning nobody reads.
- **Exercise, do not read.** Confirm by running the thing. Reading the code that would do it is how a defect gets fixed twice and a working system gets "repaired".

---

## Pre-seeded risk register

Observed in practice, not hypothetical. Start every charter with this and strike what genuinely does not apply.

| Risk | How it shows up | Detection |
|---|---|---|
| **Fail-open** | Absent config read as permission or health; a missing secret interpolates to empty string and the job passes | Grep every unguarded `if (config)`; test the check with the input deliberately removed |
| **Stale assertion** | A fact measured hours ago repeated as current; status claimed from notes rather than queried | Re-query any mutable state before restating it, especially into a written deliverable |
| **Silent monitor** | A scheduled job failing for weeks; nobody notices because absence looks like calm | The missing report is itself the alarm; assert the monitor's output from outside it |
| **Shadowed config** | Two values, same name, different scopes; you edit the one that isn't running | Enumerate every scope before changing anything named |
| **Near-miss name** | A plausible name referenced by nothing, indistinguishable from a missing one | Compare referenced names against existing names, character by character |
| **Generational drift** | Old and new versions both live; code speaks the old vocabulary | Count how many places a fact appears; more than one means no source of truth |
| **Doing before measuring** | Building what already exists, or fixing a defect that isn't there | Capability preflight and a live state read before the first change |

---

## Running the project — *Project Work domain*

**Capability preflight before any substantive work.** State what you can and cannot reach — credentials, folders, APIs, repos. If something required is missing, stop and name it. Do not work around it, and do not work for twenty minutes before discovering it.

**Progress is counted.** Report `6 of 39`, not "good progress". Where something cannot be counted, say so and show no bar rather than inventing one.

**Change control.** A scope change is written into the charter with a date and a reason, never absorbed. *"While I was in there I also…"* is how a two-day project becomes a fortnight.

**Corrections stay visible.** When something turns out wrong, correct it in place with the wrong claim struck through and dated. A record quietly edited to look clean is worth less than a messy one showing its own corrections — the correction record is the evidence the process works.

**Development approach.** PMBOK 7 asks you to choose predictive, adaptive or hybrid deliberately. Most work of this kind is adaptive because the state of the system is discovered rather than known. Repairs with a measured cause are predictive. Say which, because it determines whether a fixed deliverable list is a plan or a fiction.

---

## Closure

A project closes when the done-means sentence is demonstrably true — **demonstrated, not asserted.**

1. Run the measurement; record what it returned.
2. List anything descoped or deferred and where it went. Work that evaporates at closure reappears later as a surprise.
3. Write the lessons, specifically anything belonging in the risk register next time.
4. State the honest ceiling. *"Every failure path found is closed"* is earned by looking. *"The class is exhausted"* almost never is. Claim the first.

---

## Deliverable

One markdown file per project, `charter.md`, living with the work. Tickets link to it. It is updated in place as the project changes and never rewritten to look tidier in hindsight — its history is part of what it is for.

