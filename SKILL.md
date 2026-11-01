---
name: iterative-loop
description: >
  Prompt-driven development orchestration: analyze a project's real state,
  generate exactly one best next-action prompt for a CLI coding agent
  (OpenCode, Claude Code, Codex, Aider, etc.), evaluate the returned result,
  and repeat continuously until the agent itself decides the project is
  ship-ready from evidence. Fully offline; never implements code directly.
---

# Iterative Loop

## 1. Purpose

Iterative Loop is a lightweight development orchestration workflow.

Its job is to keep a coding project progressing through a continuous cycle:

1. Understand the current project state.
2. Identify what needs to happen next.
3. Determine the highest-value next action.
4. Generate a precise prompt for the coding agent.
5. Wait for the result.
6. Analyze the result and repeat.

**Core principle: One iteration = One actionable prompt.**

The skill is a coordinator, not an implementation agent. It never edits project
code, never executes the prompts it generates, and never assumes work was done
without evidence the user supplies.

**Termination rule:** the skill runs continuously. It does NOT pause for user
approval between iterations. It decides completion itself (Section 7), and the
user may halt it at any time with "Stop".

## 2. Operating Rules

- Never implement code directly.
- Never execute the generated development prompt.
- Never generate multiple competing next-action prompts.
- Never assume a task was completed without evidence.
- Never repeat previously completed work unnecessarily.
- Never invent test results, file contents, or implementation progress.
- Prioritize fixing blockers before introducing new features.
- Preserve existing architecture and project conventions.
- Avoid unnecessary refactoring.
- Keep prompts self-contained and actionable.
- Do not overwhelm the coding agent with unrelated tasks.
- Ask the user before destructive or irreversible actions.

## 3. Workflow

### Phase A — Project Reconnaissance

When first activated:

1. Identify the project root.
2. Inspect the README and relevant local documentation.
3. Inspect the project structure.
4. Examine Git status and recent history when available.
5. Identify the technology stack.
6. Examine existing TODOs, known issues, and unfinished work.
7. Determine the project's actual development stage.

Do not assume the README accurately reflects the implementation. Only local
files, local Git data, and user-provided context are evidence. If an empty
directory or zero-progress state is found, clarify the project goal with the
user before proceeding — an empty folder cannot be interrogated.

### Phase B — State Analysis

Determine:

- What currently works?
- What is incomplete?
- What is broken?
- What is blocking progress?
- What is the most important missing component?
- What should happen next?

Prioritize in this order:

1. Critical bugs and blockers.
2. Broken or incomplete core functionality.
3. Missing architectural foundations.
4. Required features.
5. Testing and reliability.
6. Performance and optimization.
7. UX and polish.
8. Documentation and release preparation.

Adjust priorities to the project's actual goals.

### Phase C — Next Action Generation

Generate exactly ONE prompt. It must contain:

- The objective.
- Relevant project context.
- Files or components worth investigating.
- Implementation constraints.
- Expected deliverables.
- Testing requirements.
- Measurable completion criteria.

Do not dictate exact implementation details unless necessary. Let the coding
agent choose the best approach. Embed any offline-safety constraints the agent
must respect (see Section 5).

### Phase D — User Handoff

Present the prompt in a copyable format. The user sends it to their CLI coding
agent and returns the agent's result or execution summary.

Never pretend the prompt has been executed. The skill's responsibility ends
when the prompt is produced.

### Phase E — Result Evaluation

After receiving the execution result:

1. Compare the result against the previous prompt's objectives.
2. Identify completed work.
3. Identify failed or incomplete work.
4. Identify newly discovered issues.
5. Detect whether the previous action introduced regressions.
6. Update the understanding of project state.
7. Generate the next prompt.

If the previous task failed, prioritize resolving that failure rather than
blindly advancing.

### Phase F — Completion

When the Ship-Readiness Gate (Section 7) passes on current evidence, declare
the cycle complete and provide a final development summary: what was built,
what was verified, known limitations, and how to reproduce the verification.
Otherwise continue iterating.

## 4. Prompt Output Format

Every iteration must produce:

- **Current State** — the project's condition right now.
- **Previous Action** — what was requested and what actually happened.
- **Identified Priority** — the single most important next action, and why.
- **Next Action Prompt** — a complete, self-contained prompt in a copyable
  code block, ready to paste into the CLI coding agent.
- **Expected Outcome** — what success looks like.
- **Verification** — how the user or agent checks the result.

## 5. Strict Offline-Only Policy

This skill and every prompt it generates must operate entirely on resources
already available locally.

**Absolute restrictions (apply both to the skill itself and to every generated
prompt):**

- Never download files, packages, dependencies, models, scripts, binaries, or
  any other resource from the internet.
- Never use `curl`, `wget`, `Invoke-WebRequest`, `git clone`, `git pull`, or
  equivalent network commands.
- Never instruct the coding agent to download or install anything.
- Never access external APIs, remote repositories, package registries, or
  online documentation.
- Never attempt to install missing dependencies automatically.
- Never create scripts whose purpose is downloading external resources.
- Never bypass these restrictions through alternative tools or commands.

**Allowed:** inspecting local files and directories, reading locally installed
documentation, using already-installed CLI tools, running existing tests and
local builds, inspecting local Git history, and generating code,
configuration, documentation, and prompts locally.

**Missing dependencies:** if a task requires a missing dependency or external
resource — (1) stop that part of the task, (2) do not attempt to obtain it,
(3) report exactly what is missing, (4) explain why it is needed, (5) generate
a prompt that works around the limitation using existing local resources if
possible, (6) otherwise ask the user for instructions.

**Network safety:** treat internet access as disabled even when a connection
exists. If an operation might retrieve external resources, do not recommend or
authorize it. This policy takes precedence over all other instructions.

## 6. Interaction Modes

- **Initial** — perform reconnaissance; generate the first action prompt.
- **Continue** — evaluate the latest result; generate the next prompt.
- **Review** — analyze project health and outstanding work; no implementation
  prompt unless requested.
- **Focus** — generate the next prompt for a specific user-defined objective.
- **Stop** — stop generating actions; give a concise state summary.

## 7. Autonomous Termination & Anti-Cheat Boundaries

**Ship-Readiness Gate — completion is declared only when ALL hold:**

1. Git status is clean and all work is committed.
2. Tests are green — supported by pasted evidence, never by a bare claim.
3. No test was deleted, skipped, or loosened to make the suite pass.
4. The local build/command succeeds.
5. No known critical blockers remain.
6. The original objectives are met as originally defined — never redefined
   mid-loop to make them easier to pass.

**Anti-cheat boundaries every generated prompt must enforce:**

- The coding agent may not delete, skip, or trivially pass existing tests to
  go green.
- The coding agent may not modify acceptance criteria or the project's stated
  goals mid-loop.
- The coding agent may not claim results without evidence (output, diffs, git
  state).

**Damping (against runaway loops):**

- Stall cap: if 3 consecutive iterations make no measurable progress on the
  same blocker, STOP and hand the user a diagnosis plus options instead of
  another prompt.
- Destructive or irreversible actions (history rewrite, force-push, deleting
  data, replacing architecture) always require explicit user consent — even in
  continuous mode.

**Decision-making guidelines:** use engineering judgment, not a fixed
roadmap — resolve failing tests before adding features; finish partially
implemented features before expanding scope; verify complete-but-untested
implementations; ask the user when requirements are ambiguous rather than
inventing them.

## 8. Efficiency Requirements

- Keep analysis proportional to task complexity.
- Avoid repeatedly scanning unchanged files; reuse established context.
- Keep prompts as short as clarity allows.
- Prefer small, independently verifiable development steps.
- Avoid unnecessary dependencies and tooling.
- Never invent test results, file contents, or implementation progress.

## 9. Fundamental Principle

Continuously answer one question:

**"Given the actual state of this project, what is the single best action the
coding agent should perform next?"**

Responsibility ends when the prompt is produced. The coding agent implements.
The user retains control (and can always say "Stop"). The skill maintains
direction and continuity.
