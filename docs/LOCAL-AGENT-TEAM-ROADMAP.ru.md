# Local Agent Team Roadmap: Paperclip + Hermes + llama.cpp + Claude Code

**Status:** Planning / staged implementation  
**Repository:** convergentaru/paperclip  
**Goal:** Coordinate the installed Paperclip, working Hermes CLI, one local model served by llama.cpp, and Claude Code without loading duplicate model copies or losing project context.

> This is a plan, not a claim that every integration is already working. Preserve the known-good voice setup and production model endpoint until a test passes and the user approves a change.

## 1. Known baseline and constraints

- Paperclip is installed and opens.
- Hermes CLI works with voice input, voice output, and dialog mode.
- Hermes currently uses a local llama.cpp model endpoint.
- The machine has an NVIDIA RTX 3060 with 12 GB VRAM and about 11–12 GB system RAM. Resource contention matters.
- The user reports that Hermes often fails to retain or recall context. Memory is a first-class deliverable.
- The repository already has Hermes gateway test entry points in package.json: test:hermes-gateway-smoke, smoke:hermes-gateway-join, and smoke:hermes-gateway-e2e. Inspect and reuse these before building a parallel integration.
- Paperclip's upstream roadmap lists Memory / Knowledge and Automatic Organizational Learning as planned areas. Do not assume these currently solve this project's memory needs.

### Non-negotiable constraints

1. Do not reinstall or reconfigure Hermes voice, STT, or TTS.
2. Do not stop/restart the working llama.cpp server to run experiments. Use a separate test process/port.
3. Do not launch another copy of the same large model merely because another agent needs it. Prefer one shared inference endpoint.
4. Do not assume Claude Code can call the current llama.cpp endpoint directly. Verify API protocol compatibility first.
5. Start with one active model-generation job at a time. Paperclip can coordinate multiple tasks, but concurrent generations may overload this machine.
6. Back up before approved configuration changes. Never commit API keys, tokens, private keys, or other secrets.
7. Preserve the current context-window target and known-good model settings until measured tests justify changes.

## 2. Target architecture

~~~text
                         USER
                          |
                Paperclip dashboard
          goals / tasks / dependencies / approvals
                          |
                    Paperclip agents
                  /        |         \
             Hermes    Claude Code   Other agents
             adapter     adapter
                \          /
                 \        /
             Shared model API
              (one endpoint)
                    |
                 llama.cpp
                    |
             one loaded local model

       Durable project memory (separate layer)
       ├── current project state
       ├── decisions log
       ├── task outcomes / handoff notes
       └── searchable knowledge store (evaluate existing tools)
                    |
          referenced by tasks and agents
~~~

**Responsibilities**

- **Paperclip:** task ownership, goals, dependencies, approvals, execution history, and coordination.
- **Hermes:** general assistant, voice interface, tools, and agent work through its existing runtime.
- **Claude Code:** code-focused work once its adapter and local model path are verified.
- **llama.cpp:** inference and model residency; load the model once for compatible clients.
- **Memory layer:** durable, searchable facts and decisions that survive chat/session boundaries. Paperclip task history alone is not a complete personal/project memory system.

## 3. Implementation phases

### Phase 0 — Inventory without changing the working system

- [ ] Record Paperclip version, local URL, service method, and current project/company configuration.
- [ ] Record Hermes version, provider, base URL, model name, context limit, and session/memory settings.
- [ ] Record llama.cpp server command, port, model path, context size, and API format from the running process/configuration. Do not print credentials.
- [ ] Inspect the existing Hermes adapter/gateway implementation and smoke tests in this repository.
- [ ] Inspect Paperclip's configured agents and adapter types.
- [ ] Save timestamped private backups of relevant configuration files before later changes.
- [ ] Create docs/LOCAL-AGENT-TEAM-BASELINE.md with observed facts, unknowns, and diagnostic commands. Do not guess missing values.

**Exit criteria:** one factual baseline report; no service or voice behavior changed.

### Phase 1 — Verify the shared llama.cpp endpoint

- [ ] Confirm the production endpoint answers a health/model request and a short generation request.
- [ ] Record generation speed and memory use.
- [ ] Confirm the exposed API format (for example, OpenAI-compatible Chat Completions).
- [ ] Keep Hermes on the current endpoint; do not reload the model or change context settings.
- [ ] If tuning is needed, benchmark on a separate test port and compare results before proposing a production change.

**Exit criteria:** Hermes still works as before and a reproducible inference baseline exists.

### Phase 2 — Connect Paperclip to Hermes through the existing integration

- [ ] Read the Hermes adapter/gateway source and documentation in this repository.
- [ ] Prefer gateway mode if it can call the already-running Hermes service; otherwise verify local CLI adapter session behavior.
- [ ] Configure one test Hermes agent in a low-risk test project.
- [ ] Run the existing Hermes gateway smoke test(s) that match the chosen mode.
- [ ] Verify task dispatch, completion/failure visibility, session continuity, and output capture.
- [ ] Document recovery steps without touching voice or the inference server.

**Exit criteria:** Paperclip dispatches a small task to Hermes and captures the result; interactive Hermes remains usable.

### Phase 3 — Prove Claude Code can use the same model

- [ ] Inspect the installed Claude Code version and its supported provider configuration.
- [ ] Determine whether it can call the current endpoint directly. Do not infer compatibility from a shared URL: Claude Code commonly expects an Anthropic Messages-compatible API, while llama.cpp may expose an OpenAI-compatible API.
- [ ] If direct connection is supported, configure a separate test profile.
- [ ] If formats differ, evaluate the smallest reversible translation/proxy layer. Do not rebuild the old LiteLLM stack without proving it is needed.
- [ ] Test a tiny prompt and a small code edit in a disposable repository.
- [ ] Verify requests reach the existing model server and do not start a second model process.
- [ ] Record limitations in tool calling, streaming, context length, and model behavior.

**Exit criteria:** a controlled test succeeds, or a specific blocker and tested alternative are documented.

### Phase 4 — Create the first Paperclip agent team

Start with two agents:

1. **Coordinator / Hermes:** clarifies the task, reads project context, creates a plan, and reports blockers.
2. **Implementation / Claude Code:** changes code in the assigned repository/worktree and returns changed files, tests, and limitations.

- [ ] Define responsibilities, workspace paths, and permissions.
- [ ] Create a workflow: request → plan → approval → implementation → verification → handoff.
- [ ] Add task dependencies so implementation waits for plan approval when appropriate.
- [ ] Require a completion report: changed files, tests/commands, results, and unresolved issues.
- [ ] Start with one model-generation job at a time.
- [ ] Test sequential execution first. Parallelize non-model work only after resource behavior is measured.
- [ ] Require human approval for risky system changes and production model/voice changes.

**Exit criteria:** one low-risk task completes through the workflow with a readable audit trail.

### Phase 5 — Fix memory and recall (priority)

A longer context window is not persistent memory. Agents need a durable memory contract.

#### 5.1 Diagnose before adding a database

- [ ] Reproduce a harmless recall failure: tell Hermes a project fact, end/restart the session, and ask for it later.
- [ ] Determine whether the failure is due to history not persisting, memory not being written, memory not being searched, or retrieved information not being injected into the prompt.
- [ ] Inspect Hermes session/memory behavior and Paperclip task/session records without changing working configuration.
- [ ] Inspect existing GBrain and Obsidian workflows before introducing another memory service.

#### 5.2 Establish a minimal durable memory structure

Start with versioned text files; add search/indexing only when needed:

- memory/PROJECTS.md — active projects and current state.
- memory/DECISIONS.md — dated decisions, rationale, and superseded decisions.
- memory/KNOWN-GOOD.md — working configurations and recovery instructions; never overwrite a known-good baseline without a new version.
- memory/HANDOFF.md — current next steps, blockers, and latest verified commands/results.
- Project README.md / AGENTS.md — purpose, rules, entry points, tests, and agent workflow.

Keep secrets and credentials out of these files and out of Git.

#### 5.3 Read-before-work and write-after-work contract

Every agent task must:

1. Read project instructions and relevant current-state/handoff notes.
2. Search prior decisions before proposing an architecture or repeating completed work.
3. State which known facts it relies on and flag contradictions instead of silently overwriting them.
4. After work, write a concise handoff: what changed, tests, failures, exact next step, and relevant file/commit links.
5. Update durable memory only with verified useful facts. Date entries and distinguish facts from hypotheses.
6. Preserve the previous known-good state and provide rollback steps for important configuration changes.

#### 5.4 Test memory, not just the UI

- [ ] Verify recall in a fresh Hermes session.
- [ ] Verify another Paperclip agent can retrieve the relevant fact/decision.
- [ ] Verify prior decisions are found before conflicting changes are proposed.
- [ ] Verify a failed task leaves enough context to resume without rereading a huge transcript.
- [ ] Check for stale or false memories.

**Exit criteria:** a project fact and a decision survive a new session, can be retrieved by the relevant agent, and are used correctly. If retrieval fails, the agent must say it could not find the information rather than inventing it.

### Phase 6 — Operationalize

- [ ] Document the tested architecture and setup.
- [ ] Record exact service start/stop/status commands only after verifying them on the user's machine.
- [ ] Document logs, health checks, common failures, and recovery.
- [ ] Keep secrets out of Git and redact credentials from logs.
- [ ] Add a smoke-test checklist for upgrades.
- [ ] Maintain a changelog and rollback path.

**Exit criteria:** the system can be checked and recovered using verified instructions, not conversational memory.

## 4. First practical milestone

Do only these steps first:

1. Inspect the existing Paperclip repository and configured agents.
2. Produce the Phase 0 baseline report.
3. Run the Hermes gateway smoke test(s) appropriate to the configured integration.
4. Confirm the shared-model endpoint and protocol without restarting it.
5. Reproduce one Hermes memory failure and classify the cause.
6. Report findings and propose the smallest reversible next change. Wait for approval before modifying production settings.

## 5. Definition of done

- Paperclip dispatches a task to Hermes and captures the result.
- Claude Code uses the same model endpoint successfully, or a specific compatibility blocker and tested alternative are documented.
- The model is not loaded twice unnecessarily.
- Model calls are queued conservatively for the available hardware.
- A decision and project fact are recalled correctly in a fresh session by the relevant agent.
- Every completed task leaves a durable handoff and test evidence.
- Voice input/output and the current known-good Hermes workflow remain intact.
- Recovery instructions use commands and configuration verified on the user's machine.

## 6. Unknowns to measure, not guess

- Exact Paperclip version and configured agent adapter types.
- Exact Hermes gateway endpoint and whether the current CLI session is addressable by it.
- Exact production llama.cpp endpoint, model, and API format.
- Whether the installed Claude Code build accepts a custom local endpoint directly.
- Root cause of Hermes's reported memory/recall failures.
- Which GBrain/Obsidian components are currently active and suitable for retrieval.
