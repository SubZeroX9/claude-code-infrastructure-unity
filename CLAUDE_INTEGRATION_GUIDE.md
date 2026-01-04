# Claude Integration Guide for Unity Projects

**FOR CLAUDE CODE:** When a user asks you to integrate components from this Unity toolkit into their project, follow these instructions carefully.

---

## Overview

This repository is a **reference library** of Claude Code infrastructure components for Unity 6.3 LTS development. Users will ask you to help integrate specific pieces into their Unity projects. Your role is to:

1. **Ask clarifying questions** about their Unity project structure
2. **Copy the appropriate files**
3. **Customize configurations** for their Unity setup
4. **Verify the integration** works correctly

**Key Principle:** ALWAYS ask before assuming project structure. What works for one Unity project won't work for another.

---

## Unity 6.3 LTS Focus

**This toolkit is specifically designed for:**
- Unity 6.3 LTS (Long Term Support)
- C# 9.0+
- UI Toolkit (modern Unity UI system)
- MonoBehaviour-based gameplay programming
- ScriptableObject data-driven design
- Unity Editor extensions

**Verify compatibility** when integrating into user projects.

---

## General Integration Pattern

When user says: **"Add [component] to my Unity project"**

1. Identify component type (skill/hook/agent/command)
2. Ask about their Unity project structure
3. Copy files
4. Customize for their Unity setup
5. Verify integration
6. Provide next steps

---

## Integrating Skills

### Step-by-Step Process

**When user requests a Unity skill** (e.g., "add unity-gameplay-patterns"):

#### 1. Understand Their Unity Project

**ASK THESE QUESTIONS:**
- "What's your Unity project structure? Standard Assets/ layout or custom organization?"
- "Where is your gameplay code located? (e.g., Assets/Scripts/, Assets/_Project/Scripts/)"
- "Do you have separate folders for UI, Editor, or other systems?"

**Common Unity structures:**
```
Standard:
Assets/
├── Scripts/
├── Prefabs/
├── Scenes/
└── Editor/

Custom:
Assets/
├── _Project/
│   ├── Scripts/
│   ├── UI/
│   └── Editor/
└── Plugins/

Modular:
Assets/
├── Core/
├── Gameplay/
├── UI/
└── Editor/
```

#### 2. Copy the Skill

```bash
cp -r /path/to/unity-toolkit/.claude/skills/[skill-name] \
      $CLAUDE_PROJECT_DIR/.claude/skills/
```

#### 3. Handle skill-rules.json

**Check if it exists:**
```bash
ls $CLAUDE_PROJECT_DIR/.claude/skills/skill-rules.json
```

**If NO (doesn't exist):**
- Copy the template from unity-toolkit
- Customize pathPatterns for their Unity structure

**If YES (exists):**
- Read their current skill-rules.json
- Add the new skill entry
- Merge carefully to avoid breaking existing skills

#### 4. Customize Path Patterns for Unity

**CRITICAL:** Update `pathPatterns` in skill-rules.json to match THEIR Unity Assets structure:

**Example - Standard Unity structure:**
```json
{
  "unity-gameplay-patterns": {
    "fileTriggers": {
      "pathPatterns": [
        "Assets/Scripts/**/*.cs"
      ],
      "pathExclusions": [
        "Assets/Scripts/Editor/**/*.cs"
      ]
    }
  }
}
```

**Example - Custom _Project structure:**
```json
{
  "unity-gameplay-patterns": {
    "fileTriggers": {
      "pathPatterns": [
        "Assets/_Project/Scripts/**/*.cs",
        "Assets/_Project/Gameplay/**/*.cs"
      ]
    }
  }
}
```

**Example - Modular structure:**
```json
{
  "unity-gameplay-patterns": {
    "fileTriggers": {
      "pathPatterns": [
        "Assets/Core/**/*.cs",
        "Assets/Gameplay/**/*.cs",
        "Assets/Systems/**/*.cs"
      ],
      "pathExclusions": [
        "**/Editor/**/*.cs",
        "**/Tests/**/*.cs"
      ]
    }
  }
}
```

**Safe Generic Patterns** (when unsure):
```json
{
  "pathPatterns": [
    "Assets/**/*.cs"  // All C# in Assets
  ],
  "pathExclusions": [
    "Assets/**/Editor/**/*.cs",  // Exclude Editor scripts
    "Assets/Plugins/**/*.cs",    // Exclude third-party
    "Assets/**/*.Tests.cs"       // Exclude tests
  ]
}
```

#### 5. Verify Integration

```bash
# Check skill was copied
ls -la $CLAUDE_PROJECT_DIR/.claude/skills/[skill-name]

# Validate skill-rules.json syntax
cat $CLAUDE_PROJECT_DIR/.claude/skills/skill-rules.json | jq .
```

**Tell user:** "Try editing a MonoBehaviour script in [their-path] and the skill should activate."

---

### Skill-Specific Notes

#### unity-gameplay-patterns
- **Use for:** MonoBehaviour scripts, gameplay systems, ScriptableObjects
- **Ask:** "Where do you keep your gameplay C# scripts?"
- **Customize:** pathPatterns for Scripts directory
- **Example paths:** `Assets/Scripts/`, `Assets/_Project/Scripts/`, `Assets/Gameplay/`
- **Triggers on:** MonoBehaviour keywords, `.cs` files with `class.*MonoBehaviour` content

#### unity-ui-guidelines
- **Use for:** UI Toolkit development (UXML, USS, VisualElements)
- **Ask:** "Where do you keep UI files?"
- **Customize:** pathPatterns for UI directory + .uxml/.uss files
- **Example paths:** `Assets/UI/`, `Assets/_Project/UI/`, any `**/*.uxml` or `**/*.uss`
- **Triggers on:** UI Toolkit keywords, .uxml/.uss files, `using UnityEngine.UIElements`

#### unity-editor-tools
- **Use for:** Custom Inspectors, Editor Windows, Property Drawers
- **Ask:** "Where do you keep Editor scripts?"
- **Customize:** pathPatterns for Editor directories
- **Example paths:** `Assets/Editor/`, `Assets/**/Editor/`, `Assets/**/*.Editor.cs`
- **Triggers on:** CustomEditor keywords, Editor directory files, `using UnityEditor`

#### skill-developer
- **Use for:** Creating new skills for Unity
- **Copy as-is** - meta-skill, fully generic, teaches skill creation for Unity

---

## Integrating Hooks

### Essential Hooks (Always Safe to Copy)

#### skill-activation-prompt (UserPromptSubmit)

**Purpose:** Auto-suggests Unity skills based on user prompts

**Integration (NO customization needed for Unity):**

```bash
# Copy both files
cp unity-toolkit/.claude/hooks/skill-activation-prompt.sh \
   $CLAUDE_PROJECT_DIR/.claude/hooks/
cp unity-toolkit/.claude/hooks/skill-activation-prompt.ts \
   $CLAUDE_PROJECT_DIR/.claude/hooks/

# Make executable (Git Bash, WSL, or Unix)
chmod +x $CLAUDE_PROJECT_DIR/.claude/hooks/skill-activation-prompt.sh

# Install dependencies if needed
if [ -f "unity-toolkit/.claude/hooks/package.json" ]; then
  cp unity-toolkit/.claude/hooks/package.json \
     $CLAUDE_PROJECT_DIR/.claude/hooks/
  cd $CLAUDE_PROJECT_DIR/.claude/hooks && npm install
fi
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

**This hook is FULLY GENERIC** - works for Unity projects, no customization needed!

**Windows Note:** Requires Git Bash (comes with Git for Windows), WSL, or Unix environment.

#### post-tool-use-tracker (PostToolUse)

**Purpose:** Tracks Unity file changes for context management

**Integration (NO customization needed for Unity):**

```bash
# Copy file
cp unity-toolkit/.claude/hooks/post-tool-use-tracker.sh \
   $CLAUDE_PROJECT_DIR/.claude/hooks/

# Make executable (Git Bash, WSL, or Unix)
chmod +x $CLAUDE_PROJECT_DIR/.claude/hooks/post-tool-use-tracker.sh
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

**This hook is FULLY GENERIC** - auto-detects Unity Assets folder structure!

---

### Stop Hooks (Explicitly Disabled)

**Unity toolkit has Stop hooks disabled:**
```json
{
  "hooks": {
    "Stop": []  // Explicitly disabled
  }
}
```

**Reason:**
- Previous web configuration had TypeScript checks that don't apply to Unity
- Unity compilation happens in Unity Editor, not via CLI
- Stop hooks caused errors on Windows systems

**For Unity projects:** Don't add Stop hooks unless user specifically requests Unity-specific validation.

---

## Integrating Agents

**Agents are STANDALONE** - easiest to integrate for Unity!

### Standard Agent Integration

```bash
# Copy the agent file
cp unity-toolkit/.claude/agents/[agent-name].md \
   $CLAUDE_PROJECT_DIR/.claude/agents/
```

**That's it!** Agents work immediately for Unity projects, no configuration needed.

### Check for Hardcoded Paths

Some agents may reference paths. **Before copying, read the agent file and check for:**

- `~/git/old-project/` → Should be `$CLAUDE_PROJECT_DIR` or `.`
- `/root/git/project/` → Should be `$CLAUDE_PROJECT_DIR` or `.`

**If found, update them:**
```bash
sed -i 's|~/git/old-project/|.|g' $CLAUDE_PROJECT_DIR/.claude/agents/[agent].md
```

### Unity-Specific Agent Uses

**code-architecture-reviewer:**
- Reviews Unity code for MonoBehaviour lifecycle issues
- Checks ScriptableObject usage patterns
- Identifies Unity anti-patterns (FindObjectOfType in Update, etc.)

**code-refactor-master:**
- C# refactoring for Unity scripts
- Component extraction patterns
- Assembly Definition organization

**documentation-architect:**
- Unity XML documentation standards
- Package documentation for Unity packages
- Game system architecture docs

**All agents work as-is** - just copy and use!

---

## Common Unity Integration Patterns

### Pattern: Asking About Unity Project Structure

**DON'T assume:**
- ❌ "I'll add this for your Assets/Scripts folder"
- ❌ "Configuring for your standard Unity layout"

**DO ask:**
- ✅ "What's your Unity project folder structure?"
- ✅ "Where do you keep your gameplay scripts in Assets?"
- ✅ "Do you use a custom organization like Assets/_Project?"

### Pattern: Customizing skill-rules.json for Unity

**Standard Unity structure:**
```json
{
  "pathPatterns": [
    "Assets/Scripts/**/*.cs",
    "Assets/Prefabs/**/*.cs"
  ]
}
```

**Custom _Project structure:**
```json
{
  "pathPatterns": [
    "Assets/_Project/Scripts/**/*.cs",
    "Assets/_Project/Gameplay/**/*.cs"
  ]
}
```

**Modular Unity structure:**
```json
{
  "pathPatterns": [
    "Assets/Core/**/*.cs",
    "Assets/Gameplay/**/*.cs",
    "Assets/Systems/**/*.cs"
  ]
}
```

### Pattern: settings.json Integration

**NEVER copy the unity-toolkit settings.json directly!**

Instead, **extract and merge** the sections they need:

1. Read their existing settings.json
2. Add the hook configurations they want
3. Preserve their existing config

**Example merge:**
```json
{
  // ... their existing config ...
  "hooks": {
    // ... their existing hooks ...
    "UserPromptSubmit": [  // ← Add this section
      {
        "hooks": [
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/skill-activation-prompt.sh"
          }
        ]
      }
    ],
    "PostToolUse": [  // ← Add this section
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

---

## Verification Checklist

After integration, **verify these items:**

```bash
# 1. Hooks are executable (Git Bash, WSL, Unix)
ls -la $CLAUDE_PROJECT_DIR/.claude/hooks/*.sh
# Should show: -rwxr-xr-x

# 2. skill-rules.json is valid JSON
cat $CLAUDE_PROJECT_DIR/.claude/skills/skill-rules.json | jq .
# Should parse without errors

# 3. Hook dependencies installed (if TypeScript hooks)
ls $CLAUDE_PROJECT_DIR/.claude/hooks/node_modules/
# Should show packages if package.json exists

# 4. Settings.json is valid JSON
cat $CLAUDE_PROJECT_DIR/.claude/settings.json | jq .
# Should parse without errors
```

**Then ask user to test:**
- "Try editing a MonoBehaviour script in Assets/Scripts/ - the unity-gameplay-patterns skill should activate"
- "Try asking me about UI Toolkit - I should suggest the unity-ui-guidelines skill"

---

## Common Mistakes to Avoid

### ❌ DON'T: Copy settings.json as-is
**Why:** May have incompatible configurations or paths
**DO:** Extract only UserPromptSubmit and PostToolUse sections

### ❌ DON'T: Keep example Unity paths
**Why:** User's Assets structure may differ
**DO:** Ask about their actual Unity folder organization and update

### ❌ DON'T: Skip making hooks executable
**Why:** Hooks won't run without execute permission
**DO:** Always `chmod +x` after copying (on Git Bash/WSL/Unix)

### ❌ DON'T: Assume standard Unity structure
**Why:** Many Unity projects use custom folder organization
**DO:** Ask first, then customize pathPatterns

### ❌ DON'T: Add all skills at once
**Why:** Overwhelming and may not all be relevant
**DO:** Ask which skills match their Unity work (gameplay, UI, editor tools)

### ❌ DON'T: Forget Windows compatibility
**Why:** Hooks require Git Bash/WSL on Windows
**DO:** Mention Git Bash requirement for Windows users

---

## Example Integration Conversations

### Example 1: Adding Unity Gameplay Skill

**User:** "Add the unity-gameplay-patterns skill to my project"

**Claude should respond:**
```
I'll add the unity-gameplay-patterns skill. First, I need to understand
your Unity project structure:

1. Where do you keep your gameplay C# scripts? (e.g., Assets/Scripts/, Assets/_Project/Scripts/)
2. Do you have any custom folder organization in your Assets directory?
3. Are there any folders I should exclude from skill activation (Editor, Tests, Plugins)?
```

**User:** "Scripts are in Assets/_Project/Scripts/, exclude Editor and ThirdParty folders"

**Claude should:**
1. Copy the skill directory
2. Update skill-rules.json with:
   - pathPattern: `"Assets/_Project/Scripts/**/*.cs"`
   - pathExclusions: `["Assets/**/Editor/**/*.cs", "Assets/ThirdParty/**/*.cs"]`
3. Verify it's copied correctly
4. Respond:
```
✅ Added unity-gameplay-patterns skill!

Configuration:
- Skill triggers when editing C# files in Assets/_Project/Scripts/
- Excludes Editor and ThirdParty folders
- Activated by keywords like "MonoBehaviour", "ScriptableObject", "coroutine"

Try it: Edit a MonoBehaviour script and you should see the skill suggestion.
```

### Example 2: Setting Up Hooks for Unity

**User:** "Set up the skill activation hooks"

**Claude should respond:**
```
I'll set up the Unity skill auto-activation system. This requires two hooks:
1. skill-activation-prompt - Suggests Unity skills based on your prompts
2. post-tool-use-tracker - Tracks Unity file changes

Important: These hooks require Git Bash (on Windows), WSL, or a Unix environment.
Do you have Git Bash installed? (It comes with Git for Windows)

Do you have a .claude/settings.json file already, or should I create one?
```

**User:** "I have Git Bash and settings.json already"

**Claude should:**
1. Read their settings.json
2. Copy both hook files
3. Run npm install in hooks directory
4. Add hook configurations to their settings.json (preserving existing config)
5. Make hooks executable
6. Respond:
```
✅ Unity skill activation hooks installed!

What I did:
- Copied skill-activation-prompt hook
- Copied post-tool-use-tracker hook
- Installed dependencies
- Added to your settings.json
- Made scripts executable

Test it: Ask me "how do I create a MonoBehaviour?" and I should suggest
the unity-gameplay-patterns skill automatically.
```

### Example 3: Adding Unity UI Skill

**User:** "Add the UI Toolkit skill"

**Claude should:**
```
I'll add the unity-ui-guidelines skill for UI Toolkit development.

Questions:
1. Where do you keep your UI files? (e.g., Assets/UI/, Assets/_Project/UI/)
2. Do you use .uxml and .uss files, or just C# UI scripts?
```

**User:** "UI is in Assets/UI/, we use both UXML and C# scripts"

**Claude should:**
1. Copy the skill directory
2. Update skill-rules.json with:
   ```json
   "pathPatterns": [
     "Assets/UI/**/*.cs",
     "Assets/**/*.uxml",
     "Assets/**/*.uss"
   ]
   ```
3. Respond:
```
✅ Added unity-ui-guidelines skill!

Configuration:
- Triggers for C# files in Assets/UI/
- Triggers for .uxml and .uss files anywhere in Assets
- Activated by keywords like "UI Toolkit", "UXML", "VisualElement"

Try it: Edit a .uxml file or ask about UI Toolkit and the skill will activate.
```

---

## Quick Reference Tables

### What Needs Customization?

| Component | Customization | What to Ask |
|-----------|--------------|-------------|
| **unity-gameplay-patterns** | ⚠️ Paths | "Where are gameplay scripts?" |
| **unity-ui-guidelines** | ⚠️ Paths | "Where are UI files?" |
| **unity-editor-tools** | ⚠️ Paths | "Where are Editor scripts?" |
| **skill-developer** | ✅ None | Copy as-is |
| **skill-activation-prompt** | ✅ None | Copy as-is |
| **post-tool-use-tracker** | ✅ None | Copy as-is |
| **All agents** | ✅ Minimal | Check paths only |

### Unity-Specific Triggers

| Skill | Keywords | File Patterns | Content Patterns |
|-------|----------|---------------|------------------|
| unity-gameplay-patterns | MonoBehaviour, ScriptableObject, coroutine | Assets/**/*.cs | class.*MonoBehaviour |
| unity-ui-guidelines | UI Toolkit, UXML, USS, VisualElement | Assets/**/*.uxml, **/*.uss | using UnityEngine.UIElements |
| unity-editor-tools | CustomEditor, PropertyDrawer, EditorWindow | Assets/**/Editor/**/*.cs | using UnityEditor |

---

## Final Tips for Claude

**When user says "add everything":**
- Start with essentials: skill-activation hooks + 1 relevant Unity skill
- Don't overwhelm them with all 4 skills
- Ask what they're currently working on (gameplay, UI, editor tools)

**When something doesn't work:**
- Check verification checklist
- Verify paths match their Unity Assets structure
- Test hooks manually (Git Bash required on Windows)
- Check for JSON syntax errors

**When user is on Windows:**
- Confirm they have Git Bash installed
- Explain hooks require Git Bash, WSL, or Unix
- Note that Stop hooks are explicitly disabled to prevent CMD errors

**When user is unsure:**
- Recommend starting with just skill-activation hooks
- Add unity-gameplay-patterns skill as first skill
- Add more later as needed (UI, editor tools)

**Always explain what you're doing:**
- Show the commands you're running
- Explain why you're asking about Unity structure
- Provide clear next steps after integration

---

## Unity 6.3 LTS Compatibility Notes

**This toolkit is designed for:**
- Unity 6.3 LTS (released 2024)
- C# 9.0+ language features
- UI Toolkit as primary UI system (UGUI as legacy reference)
- Modern Unity Editor with UI Toolkit support
- Assembly Definitions for project organization

**If user has older Unity version:**
- Unity 2022 LTS: Skills should work with minor adjustments
- Unity 2021 or older: May need significant updates (UI Toolkit, C# features)
- Ask about their Unity version if compatibility is unclear

**Key Unity 6 features in skills:**
- UI Toolkit patterns (VisualElement, UXML, USS)
- Modern MonoBehaviour patterns
- ScriptableObject runtime sets
- Assembly Definition organization
- Performance optimization for Unity 6

---

**Remember:** This is a reference library for Unity 6.3 LTS projects. Your job is to help users cherry-pick and adapt components for THEIR specific Unity project structure.
