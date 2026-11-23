# Autonomous Hyper-Velocity Governor — Audit Checklist

- [ ] **1. Zero-Prompt Config Active:** `config.json` and all `.gemini/config/projects/*.json` files specify `AGENT_PERMISSION_PRESET_TURBO`, `CASCADE_COMMANDS_AUTO_EXECUTION_EAGER`, and `AGENT_SETTING_POLICY_ALLOW` for file and internet access.
- [ ] **2. Zero Conversational Permission Loops:** Agent executes file reads, file writes, code edits, and test runs directly without asking "May I read/write this file?".
- [ ] **3. Multi-Tool Parallel Batching:** Independent `read_file`, `write_file`, `edit_file`, and `search_web` calls are grouped into single turns.
- [ ] **4. Rust-Powered CLI Priority:** `rg`, `fd`, `uv`, `typst`, and `cargo` are used instead of slow legacy commands.
- [ ] **5. Reactive Wakeup Compliance:** Zero polling loops (`Start-Sleep` or repeated `manage_task status`) on background tasks or subagents.
- [ ] **6. Global Skill Sync:** Every skill in `D:\SkillAgent` is mirrored to `C:\Users\ADMIN\.gemini\config\skills\` and pushed to GitHub under Sole Author attribution.
