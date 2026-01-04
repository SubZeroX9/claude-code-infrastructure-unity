# Hooks

Claude Code hooks that enable skill auto-activation and file tracking for Unity projects.

---

## What Are Hooks?

Hooks are scripts that run at specific points in Claude's workflow:
- **UserPromptSubmit**: When user submits a prompt
- **PreToolUse**: Before a tool executes
- **PostToolUse**: After a tool completes
- **Stop**: When user requests to stop

**Key insight:** Hooks can modify prompts, block actions, and track state - enabling features Claude can't do alone.

---

## Essential Hooks (Start Here)

### skill-activation-prompt (UserPromptSubmit)

**Purpose:** Automatically suggests relevant Unity skills based on user prompts and file context

**How it works:**
1. Reads `skill-rules.json`
2. Matches user prompt against Unity trigger patterns
3. Checks which Unity files user is working with (.cs, .uxml, .uss)
4. Injects skill suggestions into Claude's context

**Why it's essential:** This is THE hook that makes Unity skills auto-activate.

**Integration:**
```bash
# Copy both files
cp skill-activation-prompt.sh your-unity-project/.claude/hooks/
cp skill-activation-prompt.ts your-unity-project/.claude/hooks/

# Make executable (Git Bash, WSL, or Unix)
chmod +x your-unity-project/.claude/hooks/skill-activation-prompt.sh

# Install dependencies
cd your-unity-project/.claude/hooks
npm install
```

**Add to settings.json:**
```json
{
  "hooks": {
    "UserPromptSubmit": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/skill-activation-prompt.sh"
          }
        ]
      }
    ]
  }
}
```

**Customization:** ✅ None needed - reads skill-rules.json automatically for Unity triggers

**Unity-specific triggers:**
- MonoBehaviour, ScriptableObject, UI Toolkit keywords
- Assets/**/*.cs file patterns
- MonoBehaviour, UIDocument content patterns

---

### post-tool-use-tracker (PostToolUse)

**Purpose:** Tracks file changes to maintain context across Unity development sessions

**How it works:**
1. Monitors Edit/Write/MultiEdit tool calls
2. Records which Unity scripts were modified
3. Creates cache for context management
4. Auto-detects Unity project structure (Assets/Scripts, Assets/UI, Assets/Editor, etc.)

**Why it's essential:** Helps Claude understand what parts of your Unity project are active.

**Integration:**
```bash
# Copy file
cp post-tool-use-tracker.sh your-unity-project/.claude/hooks/

# Make executable (Git Bash, WSL, or Unix)
chmod +x your-unity-project/.claude/hooks/post-tool-use-tracker.sh
```

**Add to settings.json:**
```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|MultiEdit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/post-tool-use-tracker.sh"
          }
        ]
      }
    ]
  }
}
```

**Customization:** ✅ None needed - auto-detects Unity Assets folder structure

---

## Stop Hooks (Explicitly Disabled)

**Status:** Stop hooks are explicitly disabled in this Unity toolkit.

**Reason:** The previous web-focused configuration had TypeScript compilation checks (tsc-check.sh) that don't apply to Unity development and caused errors on Windows systems.

**Current settings.json configuration:**
```json
{
  "hooks": {
    "Stop": []  // Explicitly disabled
  }
}
```

**Windows Compatibility Note:**
The original Stop hooks used shell variable syntax (`$CLAUDE_PROJECT_DIR`) that doesn't work in Windows CMD. By setting `Stop: []`, we override any global settings that might cause errors.

**For Unity Projects:**
Unity compilation happens in the Unity Editor, not via command-line TypeScript checks. If you want to add Unity-specific Stop hooks later, you could create:
- Unity compilation check (checking Unity console logs)
- Unity test runner validation
- Unity build verification

These would need to be custom-built for your Unity workflow.

---

## For Claude Code

**When setting up hooks for a Unity project:**

1. **Read [CLAUDE_INTEGRATION_GUIDE.md](../../CLAUDE_INTEGRATION_GUIDE.md)** first
2. **Always start with the two essential hooks**
3. **Skip Stop hooks** - they're explicitly disabled for Unity
4. **Verify after setup:**
   ```bash
   # Git Bash, WSL, or Unix
   ls -la .claude/hooks/*.sh | grep rwx
   ```

**Windows Compatibility:**
- Hooks require Git Bash, WSL, or Unix environment
- Native Windows CMD does not support these shell scripts
- Use Git Bash (comes with Git for Windows) for best compatibility
- settings.json explicitly disables Stop hooks to prevent CMD errors

**Questions?** See [CLAUDE_INTEGRATION_GUIDE.md](../../CLAUDE_INTEGRATION_GUIDE.md)
