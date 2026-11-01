
# 🔁 Iterative Loop

### The next best action. One prompt at a time.

**A lightweight, user-controlled development planning skill for OpenCode
and other CLI coding agents.**

![Status](https://img.shields.io/badge/status-concept-blue?style=for-the-badge)
![Mode](https://img.shields.io/badge/mode-user--mediated-7c3aed?style=for-the-badge)
![Network](https://img.shields.io/badge/network-policy-offline--only-16a34a?style=for-the-badge)
![Format](https://img.shields.io/badge/format-SKILL.md-f59e0b?style=for-the-badge)

**Inspect → Decide → Prompt → Execute → Evaluate → Repeat**
:::

------------------------------------------------------------------------

## ✨ What is Iterative Loop?

Iterative Loop is a prompt-driven development orchestration skill. It
examines the current state of a software project, identifies the
**single most valuable next action**, and writes a focused prompt for a
coding agent such as OpenCode.

It does not try to become another coding agent. It acts as the planning
layer around the agent you already use.

-   **Iterative Loop decides what should happen next.**
-   **Your CLI coding agent investigates and implements it.**
-   **You remain in control of when each step runs.**

The intended result is a more deliberate development process: smaller
tasks, clearer objectives, measurable completion criteria, and less
aimless prompting.

> \[!IMPORTANT\] Iterative Loop is a workflow and instruction set, not a
> standalone agent runtime. It does not automatically observe a CLI
> agent's execution. You provide the result or relevant output so the
> next iteration can evaluate it.

## 🎯 The idea in one picture

``` mermaid
flowchart TD
    A([Start / Open project]) --> B[Inspect local project]
    B --> C[Understand current state]
    C --> D{What matters most next?}
    D --> E[Write ONE actionable prompt]
    E --> F[User gives prompt to CLI agent]
    F --> G[CLI agent implements the task]
    G --> H[User returns result / evidence]
    H --> I[Evaluate outcome and regressions]
    I --> J{Project objectives met?}
    J -- No --> C
    J -- Yes --> K([Summarize and stop])
```

## ⚙️ How an iteration works

  -----------------------------------------------------------------------
  Stage                   Iterative Loop's        Output
                          responsibility          
  ----------------------- ----------------------- -----------------------
  1\. Reconnaissance      Inspect relevant local  Project understanding
                          files, structure, docs  
                          and Git state           

  2\. State analysis      Identify completed      Current-state summary
                          work, defects, blockers 
                          and gaps                

  3\. Prioritization      Choose one high-value   A clear priority
                          next action             

  4\. Prompt generation   Write a self-contained  Copy-ready prompt
                          task prompt             

  5\. Handoff             Wait for the user to    No claimed execution
                          run the prompt          

  6\. Evaluation          Compare the agent's     Verified progress or
                          result with the         follow-up
                          objective               

  7\. Repeat              Reassess priorities     Next prompt
                          using the latest        
                          evidence                
  -----------------------------------------------------------------------

### Example: building a SaaS application

``` mermaid
flowchart LR
    A["Iteration 1<br/>Authentication"] --> B["Iteration 2<br/>Session handling"]
    B --> C["Iteration 3<br/>Role-based access"]
    C --> D["Iteration 4<br/>Authorization tests"]
    D --> E["Iteration 5<br/>Dashboard"]
    style A fill:#164e63,stroke:#22d3ee,color:#fff
    style B fill:#1e3a8a,stroke:#60a5fa,color:#fff
    style C fill:#4c1d95,stroke:#c084fc,color:#fff
    style D fill:#713f12,stroke:#facc15,color:#fff
    style E fill:#14532d,stroke:#4ade80,color:#fff
```

Each step is selected after reviewing the actual project state. This is
an illustrative sequence, not a fixed roadmap: if authentication tests
fail, fixing them should take priority over starting the dashboard.

## 🧠 Prioritization model

The skill uses engineering judgment rather than blindly following a
feature checklist.

``` mermaid
flowchart TD
    A["Review project evidence"] --> B{"Critical bug or blocker?"}
    B -- Yes --> C["Fix blocker"]
    B -- No --> D{"Core functionality incomplete?"}
    D -- Yes --> E["Complete core functionality"]
    D -- No --> F{"Architecture or reliability gap?"}
    F -- Yes --> G["Address foundation / tests"]
    F -- No --> H["Advance the next useful milestone"]
    C --> I["Define acceptance criteria"]
    E --> I
    G --> I
    H --> I
    I --> J["Generate one prompt"]
```

Typical priority order:

1.  Critical bugs and blockers.
2.  Broken or incomplete core functionality.
3.  Missing architectural foundations.
4.  Required features.
5.  Testing and reliability.
6.  Performance and optimization.
7.  User experience and polish.
8.  Documentation and release preparation.

The order is a default, not a rigid law. Project requirements and
evidence can change it.

## 🟢 What is good about it?

  -----------------------------------------------------------------------
  Advantage                           Why it matters
  ----------------------------------- -----------------------------------
  **One task at a time**              Reduces sprawling prompts and makes
                                      work easier to review.

  **Agent-agnostic approach**         The prompt can be used with
                                      OpenCode or another CLI coding
                                      agent.

  **Human control**                   You decide when to execute,
                                      continue, pause or stop.

  **Evidence-based progression**      The next task depends on reported
                                      results, not assumed success.

  **Local-first workflow**            Project inspection and planning can
                                      use files and tools already on the
                                      machine.

  **Clear completion criteria**       Each prompt should describe what
                                      success and verification look like.

  **Low infrastructure overhead**     A Markdown skill does not need a
                                      separate database, server or
                                      desktop app.

  **Failure-aware iteration**         Failed tasks and regressions can be
                                      handled before adding more scope.
  -----------------------------------------------------------------------

## 🔴 What is bad about it?

A useful tool should be honest about its limitations. Iterative Loop has
real trade-offs.

  -----------------------------------------------------------------------
  Limitation              Practical consequence   Possible mitigation
  ----------------------- ----------------------- -----------------------
  **Manual handoff**      You must copy the       Add a small wrapper
                          prompt to the CLI agent later, if desired.
                          and return its result.  

  **No automatic          It cannot independently Ask for test output,
  execution visibility**  know what the agent     summaries and Git
                          actually changed.       diffs.

  **Context can become    Long projects may lose  Maintain
  stale**                 important decisions     `PROJECT_STATE.md` and
                          between sessions.       `ITERATION_LOG.md`.

  **Prompt quality        Incomplete logs or      Request concrete files,
  depends on evidence**   vague reports can lead  commands and test
                          to poor next steps.     results.

  **Not a security        Instructions cannot     Enforce restrictions at
  sandbox**               technically prevent a   the OS or network
                          CLI agent from running  layer.
                          prohibited commands.    

  **No guaranteed         A generated prompt can  Review prompts and
  correctness**           still be incomplete or  require verification.
                          misguided.              

  **Offline limits        Missing packages,       Use installed resources
  external help**         online docs or remote   or ask the user to
                          services may block a    resolve the dependency.
                          task.                   

  **Extra planning        Tiny changes may not    Keep iterations
  overhead**              justify a full planning proportional to task
                          cycle.                  complexity.
  -----------------------------------------------------------------------

### ⚖️ The central trade-off

``` mermaid
quadrantChart
    title Workflow trade-offs (conceptual)
    x-axis "Less user involvement" --> "More user involvement"
    y-axis "Less process structure" --> "More process structure"
    quadrant-1 "Controlled iteration"
    quadrant-2 "Highly structured automation"
    quadrant-3 "Ad-hoc prompting"
    quadrant-4 "Manual but deliberate"
    "Iterative Loop": [0.78, 0.82]
    "One giant prompt": [0.18, 0.20]
    "Fully autonomous agent": [0.12, 0.85]
```

*This is a qualitative illustration of workflow styles, not measured
benchmark data.*

Iterative Loop deliberately accepts more user involvement in exchange
for reviewability and control. If your main goal is unattended,
end-to-end automation, this design is not the right fit without
additional tooling.

## 🔒 Offline-only policy

Iterative Loop is designed to work with resources already available on
the machine.

### Intended restrictions

-   Do not download packages, dependencies, models, scripts, binaries or
    documentation.
-   Do not install missing dependencies automatically.
-   Do not access external APIs, remote repositories or package
    registries.
-   Do not recommend commands whose purpose is to retrieve external
    resources.
-   Do not create helper scripts that download external resources.
-   Use local project files, installed tools, existing dependencies and
    local documentation.
-   If something is missing, explain what is missing and stop that part
    of the workflow.

### What offline-only does --- and does not --- mean

  -----------------------------------------------------------------------
  It can do through its instructions  It cannot guarantee by itself
  ----------------------------------- -----------------------------------
  Tell the coding agent not to        Block network traffic at the
  download anything                   operating-system level

  Avoid suggesting installation       Prevent every tool or extension
  commands                            from making a network request

  Identify missing local dependencies Prove that the entire machine is
                                      disconnected

  Prefer local documentation and      Enforce a security boundary
  installed tools                     
  -----------------------------------------------------------------------

> \[!WARNING\] A `SKILL.md` is an instruction document, not a firewall.
> A CLI agent may ignore instructions or invoke another tool that
> accesses the network. For actual offline enforcement, use an OS
> firewall rule, restricted network namespace, VM policy or another
> administrator-controlled network boundary. Verify that the restriction
> covers the agent and its child processes.

## 🧩 Interaction modes

  -----------------------------------------------------------------------
  Mode                                Purpose
  ----------------------------------- -----------------------------------
  **Initial**                         Inspect the project and generate
                                      the first next-action prompt.

  **Continue**                        Evaluate the latest execution
                                      result and generate the next
                                      prompt.

  **Review**                          Assess project health and
                                      outstanding work without
                                      necessarily proposing
                                      implementation.

  **Focus**                           Generate the next prompt for a
                                      user-defined objective.

  **Stop**                            Stop the loop and summarize current
                                      progress and blockers.
  -----------------------------------------------------------------------

## 📁 Suggested project layout

The skill can remain simple. For longer projects, keep a small amount of
durable state alongside it.

``` text
iterative-loop/
├── SKILL.md                 # Core behavior and operating rules
├── README.md                # Overview, trade-offs and usage
└── references/
    ├── prompt-template.md   # Structure for generated prompts
    └── workflow.md          # Detailed iteration procedure

your-project/
├── PROJECT_STATE.md         # Compact, current project snapshot
└── ITERATION_LOG.md         # Actions, results and verification history
```

`PROJECT_STATE.md` should contain current goals, architecture, important
decisions, known blockers and the next milestone. `ITERATION_LOG.md`
should record what was requested, what happened, evidence, and what
remains. Keep these files concise and update them only when new evidence
warrants it.

## 📝 What a generated prompt should contain

A good next-action prompt should be self-contained and testable.

``` text
OBJECTIVE
What single outcome should be achieved?

PROJECT CONTEXT
What does the agent need to know about the current implementation?

INVESTIGATION
Which relevant files, components or tests should it inspect?

CONSTRAINTS
What architecture, compatibility and offline restrictions must it preserve?

DELIVERABLES
What should be changed or produced?

ACCEPTANCE CRITERIA
What observable conditions mean the task is complete?

VERIFICATION
Which existing local tests or checks should be run?
Report exact commands and results. Do not claim checks passed unless they ran.
```

The prompt should leave implementation choices to the coding agent where
practical, rather than prescribing unnecessary details.

## 🆚 Iterative Loop vs. CLI Orchestrator

  -----------------------------------------------------------------------
  Capability              Iterative Loop          Full CLI Orchestrator
  ----------------------- ----------------------- -----------------------
  Main purpose            Decide and formulate    Coordinate agent
                          the next action         execution and related
                                                  controls

  Primary output          One next-action prompt  Managed execution
                                                  workflow

  Execution               User-mediated           Depends on orchestrator
                                                  implementation

  Monitoring              Based on user-provided  Can include execution
                          evidence                monitoring

  Infrastructure          Markdown skill and      Application and
                          optional local notes    supporting components

  Complexity              Low                     Higher

  Security enforcement    Instructional only      Depends on implemented
                                                  controls

  Best suited for         Deliberate, controlled  More integrated agent
                          development             workflows
  -----------------------------------------------------------------------

Iterative Loop is not a replacement for every capability of CLI
Orchestrator. It intentionally removes execution management and
concentrates on planning, prompt quality and continuity.

## 🚀 Getting started

1.  Place `SKILL.md` in the skill location supported by your CLI agent.
2.  Open the target project in your CLI coding agent.
3.  Activate the skill using the agent's supported skill invocation
    method.
4.  Let it inspect the local project and produce the first prompt.
5.  Review the prompt before giving it to the coding agent.
6.  Return the execution summary and relevant evidence.
7.  Continue until the project objectives are verified or you decide to
    stop.

Exact skill installation and invocation paths depend on the coding agent
and its version. Do not download an installer or additional tooling as
part of this workflow.

## 🛣️ Possible future improvements

These are ideas, not features that the current skill already provides.

-   [ ] Structured project-state template.
-   [ ] Persistent iteration history.
-   [ ] Prompt quality checklist.
-   [ ] Test-result and Git-diff evidence checklist.
-   [ ] Optional task-size estimation.
-   [ ] Optional milestone and release tracking.
-   [ ] Agent-specific prompt formatting.
-   [ ] A local wrapper to reduce manual copy/paste, while retaining
    explicit user approval.

## 📊 How to measure whether it is useful

Do not judge the skill by how many prompts it generates. Judge it by
whether it improves development outcomes.

  -----------------------------------------------------------------------
  Metric                              What to observe
  ----------------------------------- -----------------------------------
  First-pass completion               How often a task meets its
                                      acceptance criteria without another
                                      repair iteration

  Rework rate                         How often completed tasks need to
                                      be redone

  Regression frequency                How often a change breaks existing
                                      behavior

  Prompt usefulness                   Whether the agent can act without
                                      repeatedly asking for missing
                                      context

  Verification coverage               Whether claimed completion is
                                      backed by actual checks

  User overhead                       Time spent reviewing, copying and
                                      returning results
  -----------------------------------------------------------------------

No performance improvement is guaranteed. Record a baseline before
claiming that Iterative Loop makes development faster or more reliable.

## 🧭 Design philosophy

1.  **One next action, not an overwhelming roadmap.**
2.  **Evidence over assumptions.**
3.  **Small steps over uncontrolled scope.**
4.  **Verification over claims.**
5.  **User control over unattended execution.**
6.  **Local resources over network dependency.**
7.  **Honest limitations over false guarantees.**

::: {align="center"}
### 🔁 Inspect. Decide. Prompt. Verify. Repeat.

**Iterative Loop --- Keep the project moving, one deliberate action at a
time.**

`<sub>`{=html}A planning skill, not an autonomous coding
agent.`</sub>`{=html}
:::
