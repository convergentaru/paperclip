# Hermes Profile Team → Paperclip Company: “Ещё один удачный день”

**Date:** 2026-10-26  
**Status:** Proposed rollout plan; not deployed or tested end to end  
**Target:** First personal Paperclip company backed by the user's existing Hermes profiles

## Goal

Create the first useful agent company without rebuilding work already done in Hermes.

The inspiration is Akshay Pachaar's [Hermes Agent Masterclass on X](https://x.com/akshay_pachaar/status/2054564519280804028), available in an archived readable form at [Rattibha](https://en.rattibha.com/thread/2054564519280804028), and the companion [Hermes Agent Crash Course video](https://www.youtube.com/watch?v=bNp6YcKBLgY). The article confirms three separate Hermes profile roles: **Programmer**, **Researcher**, and **Designer**. It credits **Pixel** as the agent that designed the article's illustrations, so Pixel is the proposed persona/name for the Designer profile, to be confirmed against the actual local profiles. The Programmer in the article routes code execution through Claude Code; our deployment must use only tools actually installed and approved locally, not silently assume Claude Code or cloud inference is available.

This plan adapts the three tutorial profiles into Paperclip's company control plane and adds two roles for our workflow: a Paperclip CEO/Coordinator (required as the first employee) and an independent Morning Foreman. Those are our additions, not part of the source tutorial's initial trio.

**Company name:** Robot Factory — Ещё один удачный день  
**Company goal:** Every night, complete a bounded, useful task with inspectable evidence; each morning, independently verify results and give the owner a truthful report with diffs, tests, blockers, and one next action.

## Sources reviewed

- [Original X article](https://x.com/akshay_pachaar/status/2054564519280804028)
- [Archived article](https://en.rattibha.com/thread/2054564519280804028)
- [Hermes Agent Crash Course video and chapter list](https://www.youtube.com/watch?v=bNp6YcKBLgY). Relevant chapters: 30:36 Programmer / Claude Code; 34:59 multiple profiles; 36:49 custom Designer; 40:42 Hermes home layout; 45:59 skill bundles.
- [Hermes profile documentation](https://github.com/NousResearch/hermes-agent/blob/main/website/docs/user-guide/profiles.md)
- This Paperclip fork's [Hermes adapter](https://github.com/convergentaru/paperclip/tree/master/packages/adapters/hermes), [core concepts](https://github.com/convergentaru/paperclip/blob/master/docs/start/core-concepts.md), and [routine API](https://github.com/convergentaru/paperclip/blob/master/docs/api/routines.md).

The chapter list and archived article confirm the roles and architecture, but do not prove the current local profile names, files, credentials, or usability. Local profile audit is mandatory. The original tutorial uses Telegram bots, but this company MVP must not require three separate Telegram bots; Paperclip can invoke Hermes as a local CLI worker.

## Architecture

- **Paperclip:** company, agents, tasks, dependencies, schedules, review gates, activity, and work products.
- **Hermes:** execution runtime with existing profiles, skills, tools, memory, session management, and context compression.
- **Markdown + Obsidian:** canonical human-readable knowledge, decisions, project map, and daily report.
- **GBrain:** derived index over explicitly selected canonical files; never the only copy.
- **Git:** history of code, skill and instruction changes.
- **Models:** interchangeable execution choices. Durable knowledge and role identity must not depend on a model's transient context.

Paperclip uses a strict organization tree: the CEO is the first agent and reports to the human board; every other agent has one manager. Issues have a single assignee and a tracked lifecycle. Scheduled routines have concurrency and catch-up policies. Check the deployed version before relying on any feature.

## Proposed org chart

Start with the smallest useful tree. All employees report to the CEO until there is a concrete reason to add another management layer.

    Oleg / Board
    └── CEO / Coordinator (Paperclip's required first agent)
        ├── Programmer / Night Engineer (existing Hermes programmer profile)
        ├── Researcher (existing Hermes researcher profile)
        ├── Pixel / Designer (existing Hermes designer profile; verify actual name)
        └── Morning Foreman (new role; independent verification)

The CEO coordinates goals, issue decomposition, priorities, blockers, and routing. It does not approve consequential changes. The Foreman independently reviews evidence. Oleg remains the board/owner and approves merges, releases, model changes, destructive system operations, USB writes, and other irreversible actions.

### Roles

#### CEO / Coordinator

Translate the company goal into bounded issues, assign work to the appropriate role, enforce prerequisites, and keep progress visible. Reuse the existing default Hermes profile only after auditing its purpose and configuration. Do not clone model/runtime settings blindly. Delegations need acceptance criteria, allowed paths, tools, stop conditions, and required evidence. The CEO cannot self-approve deliverables or bypass human approval gates.

#### Programmer / Night Engineer

Use the existing Hermes programming profile, as found by audit. Reuse the working GitHub and browser skills; do not recreate them. Work in the assigned project and an isolated branch/worktree where supported. Produce a diff, actual test results and exit codes, changed-file list, artifact links, risks, and unknowns. Route execution through Claude Code only if the CLI and authorization are actually available and the user approves. Otherwise use the existing tested local tool route. No self-merge, automatic push/release, USB write, or internal-disk changes without explicit approval.

For model scaling, Ministral 8B remains the immutable known-working baseline. A candidate 14B may be tried only through an isolated model override/profile/config after checking actual server context, compression behavior, tool calling, RAM/VRAM/swap, and stop conditions. Do not change the working default just to make an overnight job run on 14B.

#### Researcher

Use the existing Hermes researcher profile, confirmed during audit. Read source material, official documentation, GitHub issues and project evidence; return compact, source-linked findings that distinguish fact, hypothesis, and unknown. Nightly research must be limited to a selected project or issue; do not dump whole repositories or transcripts into the model context. Produce a concise Markdown report in the canonical project folder and link it from Paperclip. A daily digest is a later phase, after delivery has been tested.

#### Pixel / Designer

Use the existing Hermes designer profile. The article credits Pixel with its illustrations, but audit actual profile names and SOUL.md before assuming the profile is named Pixel. Generate visual assets, diagrams and reusable design workflows from references. Preserve current design skills and references. Keep credentials out of SOUL.md, notes and Git. If the required image provider is not configured, report the limitation rather than silently using a cloud service.

#### Morning Foreman (new role)

Independently validate the Night Engineer's output in a fresh Paperclip heartbeat. Run deterministic checks first, then use the model to inspect semantics and explain ambiguity.

- Start only after the worker has stopped and artifacts exist.
- Check issue criteria, actual diff, branch/commit, test logs and exit codes, artifact availability, and safety-sensitive actions.
- Do not treat the worker's report as proof. Mark checks that were not run as “not run.”
- Return PASS / NEEDS_REVIEW / BLOCKED with evidence links.
- Do not edit the candidate while reviewing it. Failed checks result in a focused follow-up issue.
- Use the stable smaller model first. A 14B night model and 8B Foreman must run sequentially on the current limited inference resource unless safe concurrency is demonstrated.

## Hermes profile audit and mapping

Before creating Paperclip employees:

1. Use the installed Hermes CLI's own help to discover how to list and inspect profiles. Record actual profile names/aliases, role descriptions, config paths, SOUL.md files, skills, model aliases, and current health.
2. Confirm each profile has a distinct home directory; do not run two agent processes that write the same profile concurrently.
3. Preserve profiles in place. Do not export, clone, rename, overwrite, or rotate credentials during discovery.
4. Check how Paperclip selects a profile. The current fork's Hermes local adapter builds a command of the form “configured executable + chat + query flags”. Test whether the profile's generated executable alias selects its own home, using harmless help/read-only calls first. Do not assume a directory name automatically selects a profile.
5. Configure each Paperclip employee's model only after confirming the active endpoint and model alias. Start with the current known-working 8B path; do not run 14B during company bootstrap.
6. Keep Paperclip role/capability descriptions aligned with the profile's SOUL.md and skills, but do not duplicate the whole SOUL into every task.
7. Keep per-profile credentials isolated. Do not mix the board token, Hermes gateway key, ordinary Paperclip agent key, task-bridge key, and provider credentials.

### Adapter facts

This fork has built-in Hermes adapter types: **hermes_local** starts the local Hermes CLI for a heartbeat; **hermes_gateway** calls an already-running Hermes API server. The adapter supports fields such as model, timeoutSec, persistSession, toolsets, hermesCommand, extraArgs, and worktreeMode. Confirm the exact schema of the deployed version before use.

Prefer hermes_local when Paperclip and Hermes are on the same trusted host. Use hermes_gateway only if that route is already understood and verified. Do not expose a gateway through non-loopback plain HTTP or reuse credentials across trust boundaries.

If the adapter cannot reliably isolate profiles, stop and implement/test a supported profile selector before registering all employees.

## Night → Foreman → owner workflow

1. Paperclip assigns one bounded task to the Night Engineer.
2. The worker checks out the issue atomically, reads only relevant skill/docs, performs the task in scope, runs the available checks, and saves a manifest plus artifacts.
3. The worker sets the issue to in_review or blocked, never done solely because it finished generating a response.
4. Foreman starts only after the worker has stopped and the inference server is available. It receives criteria and artifact links, not the entire worker transcript.
5. Foreman checks the evidence and returns PASS / NEEDS_REVIEW / BLOCKED.
6. Paperclip records the verdict, comments and work products. Oleg sees the concise morning report.
7. The report is written into the existing canonical Markdown/Obsidian system with Paperclip issue IDs and links to Git evidence. GBrain indexes only approved source folders.
8. Oleg and Luna review the report and decide what to accept, reject, or do next.

### Morning report contract

- Outcome: completed, needs review, blocked, or no safe work performed.
- What changed: project, issue ID, branch/commit, files and diff.
- Evidence: tests actually run and their exit codes, logs/artifact links, and physical-device results where applicable.
- Foreman verdict: PASS / NEEDS_REVIEW / BLOCKED, with evidence links.
- Risks and unknowns.
- Owner approvals needed.
- Exactly one primary next action.

Do not claim Telegram/email/push delivery until it is configured and tested end to end.

## Scheduling, budgets and hardware limits

- Initial routines run sequentially; do not run the night model and Foreman concurrently on the RTX 3060 without measured evidence.
- Configure routine concurrency to coalesce/skip while a run is active rather than duplicating work. Verify behavior on the installed Paperclip build.
- Use a bounded issue count, timeouts, max turns, low concurrency, and explicit stop conditions.
- Abort or mark blocked on context errors, provider errors, unbounded swap growth, missing outputs, unexplained server restarts, or missing logs.
- Local inference may avoid hosted token costs but still consumes compute time and energy.
- No autonomous changes to global Hermes config or model defaults. Experimental model runs use isolated configuration; 8B remains fallback.

## Phased implementation

### Phase 0 — read-only discovery

- Confirm Paperclip deployment/version and local access path.
- Preserve all local uncommitted changes.
- Inventory Hermes profiles and verify how the adapter selects each one.
- Confirm the deployed adapter schema and execution mode.
- Find the canonical Obsidian home note and approved GBrain source folders.
- No company creation, model changes, or routine activation until these facts are known.

### Phase 1 — create a minimal company

- Create **Robot Factory — Ещё один удачный день** with the goal defined above.
- Create the required CEO first.
- Add Programmer, Researcher, Pixel/Designer and Foreman only after their profile/adapter paths pass a harmless smoke test.
- Start with a manual documentation task and one review task. No unattended schedule yet.
- Confirm that the assignment, heartbeat/run, logs, work product and independent review are visible in Paperclip.

### Phase 2 — prove night/morning handoff

- Add one short nightly routine with a narrow project allowlist.
- Add a morning review routine that verifies the worker issue is ready and the inference server is idle.
- If the worker is still running, defer/skip the review. Do not overlap model generations.
- Seed a deliberately flawed documentation/test artifact to prove Foreman can catch a known defect and refuses to pass missing evidence.
- Repeat several times and confirm there are no duplicate tasks or cross-profile session reuse.

### Phase 3 — add workstreams

- Add tasks for the existing browser/GitHub skills and project acer-rescue-usb.
- USB work begins read-only. Never write a device or alter an internal disk without explicit confirmation and target identification.
- Add Researcher digests only after report delivery is tested.
- Assign visual work to Pixel only where an actual design artifact is needed.

### Phase 4 — test 14B for night engineering

- Preserve a complete baseline record for Ministral 8B.
- Create an isolated 14B candidate.
- Compare tool calling, correctness, continuation after compression, latency, memory usage and failure rates.
- Run sequentially. Only try bounded overnight tasks after short and repeated long-context tests pass.
- Never automatically replace the working 8B profile.

### Phase 5 — model-independent learning

- Keep canonical knowledge in Markdown; index selected approved folders with GBrain.
- Use the meta-skill/evaluation process and mandatory GEPA for targeted instruction evolution.
- Keep candidate skills separate; require regression tests and human approval.
- Run fresh-session retrieval tests after model/profile changes.
- Do not confuse GEPA/prompt improvements with changing model weights.

## Definition of done

- [ ] Company and goal exist in the deployed Paperclip instance.
- [ ] CEO is created first and reports to the human board.
- [ ] The tutorial's three roles are mapped to the actual Hermes profiles; Pixel is verified, not guessed.
- [ ] Foreman is a separate role with independent review rules.
- [ ] A safe manual task has a recorded result through Paperclip.
- [ ] Foreman catches a known faulty/missing-evidence test result.
- [ ] Active Hermes profile and model are confirmed for each heartbeat.
- [ ] No overlapping use of one profile/home or the constrained model server.
- [ ] Diff, checks, artifacts and review are visible in Paperclip and link to canonical Markdown/Obsidian.
- [ ] Durable knowledge can be retrieved in a fresh session.
- [ ] No autonomous merge, publish, USB write, internal-disk write, or global model/config change occurs.
- [ ] Several repeated cycles pass before unattended schedules are enabled.

## Motto

**Ещё один удачный день.**

A successful day means bounded overnight work, independent morning verification, transparent evidence, no hidden destructive actions, and one clear next step for the owner.
