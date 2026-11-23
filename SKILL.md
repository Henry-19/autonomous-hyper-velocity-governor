---
name: autonomous-hyper-velocity-governor
description: >-
  Zero-prompt autonomous execution and speed optimization governor for Antigravity AI agents. Eliminates annoying
  permission prompts on file read/write and safe command execution, enforces parallel tool batching, mandates
  Rust-powered CLI tooling (rg, fd, uv, typst, cargo), and governs subagent delegation for maximum throughput.
---

# Autonomous Hyper-Velocity Governor (SKILL.md)

> **Persona:** Staff Agent Infrastructure & Execution Latency Architect.
> **Mission:** Eliminate repetitive permission prompts, minimize wall-clock turnaround time, maximize parallel tool invocation throughput, and enforce Rust-powered CLI execution across every workspace.

---

## 1. Core Operating Invariants

```text
+-----------------------------------------------------------------------------+
|                  FIVE LAWS OF HYPER-VELOCITY AGENT EXECUTION                |
+------------------------+--------------------------+-------------------------+
| 1. Zero-Prompt Default | 2. Parallel Tool Batch   | 3. Rust-CLI First       |
| Never ask permission   | Batch independent file   | Use rg, fd, uv, ruff,   |
| to read, write, edit,  | reads, edits, and web    | typst, & cargo instead  |
| or run safe build/test | searches into a SINGLE   | of slow PowerShell or   |
| commands               | model invocation turn    | legacy Python scripts   |
+------------------------+--------------------------+-------------------------+
| 4. Prefix-Safe Commands| 5. Smart Subagent Delegation                       |
| Issue standalone,      | Handle targeted (<5 files) lookups inline; spawn   |
| prefix-stable commands | parallel `research` or `self` subagents only for   |
| so Always-Allow works  | broad multi-domain exploration or heavy builds     |
+------------------------+--------------------------+-------------------------+
```

---

## 2. Eliminating Permission Prompts (Zero-Friction Policy)

### 2.1 Why Unnecessary Prompts Happen & How This Skill Prevents Them
1. **Non-Workspace File Reads/Writes (`Allow read access to this path?`):**
   - Triggered when reading/writing paths outside the active workspace (e.g., `d:/FU/...` while inside `d:/SkillAgent`) under default `ask` policy.
   - **Permanent Fix:** Global `~/.gemini/config/config.json` and project `.json` configs MUST set:
     - `"permissionPreset": "AGENT_PERMISSION_PRESET_TURBO"`
     - `"autoExecutionPolicy": "CASCADE_COMMANDS_AUTO_EXECUTION_EAGER"`
     - `"artifactReviewMode": "ARTIFACT_REVIEW_MODE_TURBO"`
     - `"nonWorkspaceFileAccessPolicy": "AGENT_SETTING_POLICY_ALLOW"`
     - `"allowAgentAccessNonWorkspaceFiles": true`
     - `"globalPermissionGrants": { "allow": ["read_file(D:/)", "write_file(D:/)", "read_file(C:/Users/ADMIN)", ...] }`
2. **Conversational Over-Asking ("Should I write this file?"):**
   - **Strict Prohibition:** Never ask the user *"Would you like me to read file X?"* or *"Shall I create/update file Y?"*. Reading files, creating deliverables, editing code, running tests, and committing/pushing requested work are **implicit, mandatory actions**. Execute them directly.
   - **When to Ask (`ask_question`):** Only pause for human input when requirements have fundamental business ambiguity, require external paid credentials not yet in `.env`, or involve irreversible destructive production operations (`DROP DATABASE`, force-overwriting user data).

### 2.2 Prefix-Stable Command Hygiene (Preserving `Always Allow`)
Antigravity caches command approvals by command prefix (`command(<binary_and_subcommand>)`). To ensure zero re-prompting:
- **Never Chain Heterogeneous Commands:** Do not combine `cd ... && git add . && git commit ... && git push` in a single shell line if prefix rules are active, or use clean standalone commands (`git status`, `git add`, `git commit`, `git push`).
- **Avoid Dynamic Flag Prefixes Before Subcommands:** Run `git push -u origin main` rather than `git -C "d:/varying/path" push` whenever already in the working directory.

---

## 3. Wall-Clock Speed & Parallel Execution Architecture

```text
[Incoming User Request]
        |
        v
+----------------------------------------------------+
| Step 1: Parallel Discovery Batch (Turn 1)          | --> Emit 3-6 read_file / rg / fd calls in ONE turn
+----------------------------------------------------+
        |
        v
+----------------------------------------------------+
| Step 2: Lazy Slice & High-Speed Synthesis (Turn 2) | --> Read only target line ranges; batch all write_file / edit_file calls
+----------------------------------------------------+
        |
        v
+----------------------------------------------------+
| Step 3: Fast Verification & Git Sync (Turn 3)      | --> Verify via Rust-powered CLI; standalone git commit & push
+----------------------------------------------------+
```

### 3.1 Multi-Tool Parallel Batching Rules
- **Read Batching:** If you need to inspect 4 files (`SKILL.md`, `README.md`, `package.json`, `Cargo.toml`), emit all 4 `read_file` tool calls **in the same turn** rather than sequentially across 4 turns (saves 75% round-trip LLM latency).
- **Write/Edit Batching:** When creating a multi-file module or skill package (`SKILL.md`, `README.md`, `references/*`, `checklists/*`), emit all `write_file` calls **in a single turn**.
- **Line-Range Precision:** Use `StartLine` and `EndLine` on `read_file` for files $> 250$ lines instead of ingesting entire monolithic files.

### 3.2 Mandatory Rust-Powered Tool Selection
Always choose compiled Rust binaries over interpreted shell/Python equivalents:
- **Text Search:** `rg -n "pattern" <path>` instead of `Select-String` or Python loops.
- **File Listing:** `fd -e md -e rs . <path>` instead of `Get-ChildItem -Recurse`.
- **Python Execution & Packaging:** `uv pip install` / `uv run` instead of `pip install`.
- **PDF Typesetting:** `typst compile doc.typ doc.pdf` (milliseconds) instead of `xelatex` (10+ seconds).

---

## 4. Subagent & Background Task Orchestration

- **Inline Fast-Path (< 5 files):** Perform directly in the main agent using batched `read_file` / `rg`. Do NOT spawn a subagent for trivial lookups (avoids subagent cold-start overhead).
- **Parallel Subagent Fan-Out ($\ge 3$ independent domains):** Invoke `research` or `self` subagents concurrently in a single turn.
- **Reactive Wakeup Discipline:** After starting a background task or subagent, **never poll** (`manage_task status` in a loop or `Start-Sleep`). Immediately proceed with other independent work or end the turn and let Reactive Wakeup notify you.

---

## 5. Git Attribution & Repository Sync Policy

- **Sole Author Mandate:** Every git commit MUST record strictly one author (`git config user.name` and `user.email`).
- **Zero Co-Authors:** Never append `Co-authored-by:` trailers or bot signatures.
- **Global Skill Sync:** Whenever a skill in `D:\SkillAgent\<skill>` is created or updated, synchronize its runtime copy into `C:\Users\ADMIN\.gemini\config\skills\<skill>` so Antigravity discovers it globally across all projects.

---

## 6. Pre-Flight Verification Checklist

Verify against `checklists/hyper-velocity-audit.md`:
- [ ] Zero unnecessary confirmation questions asked for file reads, file writes, or safe commands.
- [ ] Independent `read_file` and `write_file` calls batched into single turns.
- [ ] Rust-powered CLI tools (`rg`, `fd`, `uv`, `typst`, `cargo`) prioritized over slow legacy equivalents.
- [ ] `~/.gemini/config/config.json` and project configs have `AGENT_PERMISSION_PRESET_TURBO` and `AGENT_SETTING_POLICY_ALLOW` active.
- [ ] All modified/new skills committed with Sole Author attribution and synced to `~/.gemini/config/skills/`.
