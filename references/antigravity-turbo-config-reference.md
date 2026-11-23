# Antigravity Turbo & Zero-Prompt Configuration Reference

## 1. Global `~/.gemini/config/config.json` Schema (`userSettings`)

To prevent Antigravity from prompting on non-workspace file reads/writes (`Allow read access to this path?`), artifact reviews, or routine terminal commands, configure `userSettings` in `C:\Users\ADMIN\.gemini\config\config.json`:

```json
{
  "userSettings": {
    "permissionPreset": "AGENT_PERMISSION_PRESET_TURBO",
    "autoExecutionPolicy": "CASCADE_COMMANDS_AUTO_EXECUTION_EAGER",
    "artifactReviewMode": "ARTIFACT_REVIEW_MODE_TURBO",
    "nonWorkspaceFileAccessPolicy": "AGENT_SETTING_POLICY_ALLOW",
    "internetAccessPolicy": "AGENT_SETTING_POLICY_ALLOW",
    "allowAgentAccessNonWorkspaceFiles": true,
    "allowAgentAccessGitignoreFiles": true,
    "allowCascadeAccessGitignoreFiles": true,
    "browserJsExecutionPolicy": "BROWSER_JS_EXECUTION_POLICY_TURBO",
    "globalPermissionGrants": {
      "allow": [
        "read_file(D:/)",
        "read_file(d:/)",
        "read_file(C:/Users/ADMIN)",
        "read_file(c:/Users/ADMIN)",
        "write_file(D:/)",
        "write_file(d:/)",
        "write_file(C:/Users/ADMIN/.gemini/config/skills)",
        "write_file(c:/Users/ADMIN/.gemini/config/skills)",
        "command(git)",
        "command(gh)",
        "command(python)",
        "command(node)",
        "command(npm)",
        "command(npx)",
        "command(pnpm)",
        "command(cargo)",
        "command(rustc)",
        "command(uv)",
        "command(rg)",
        "command(fd)",
        "command(typst)"
      ],
      "deny": [
        "command(Format-Volume)",
        "command(Clear-Disk)"
      ]
    }
  }
}
```

---

## 2. Project-Level Override Schema (`~/.gemini/config/projects/<uuid>.json`)

Every registered project JSON should include:

```json
{
  "settings": {
    "fileAccessPolicy": "AGENT_SETTING_POLICY_ALLOW",
    "internetPolicy": "AGENT_SETTING_POLICY_ALLOW",
    "autoExecutionPolicy": "CASCADE_COMMANDS_AUTO_EXECUTION_EAGER",
    "artifactReviewMode": "ARTIFACT_REVIEW_MODE_TURBO",
    "permissionPreset": "AGENT_PERMISSION_PRESET_TURBO",
    "sandboxMode": false
  }
}
```

---

## 3. Global & Workspace `GEMINI.md` Rule Directives

Place a concise `GEMINI.md` at `D:\GEMINI.md` and `D:\SkillAgent\GEMINI.md` so every conversation automatically inherits zero-prompt autonomous behavior without wasting context budget.
