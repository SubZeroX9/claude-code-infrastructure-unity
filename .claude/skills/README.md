# Skills

Production-tested skills for Claude Code that auto-activate for Unity 6.3 LTS development.

---

## What Are Skills?

Skills are modular knowledge bases that Claude loads when needed. They provide:
- Unity-specific guidelines
- Best practices for Unity 6.3 LTS
- C# code examples
- Anti-patterns to avoid

**Problem:** Skills don't activate automatically by default.

**Solution:** This toolkit includes the hooks + configuration to make them activate for Unity development.

---

## Available Skills

### unity-gameplay-patterns
**Purpose:** Unity gameplay programming patterns for MonoBehaviour, ScriptableObjects, and game systems

**Files:** 8 resource files (453 lines main + resources)

**Covers:**
- MonoBehaviour lifecycle and best practices
- Design patterns (Service Locator, Object Pool, State Machine, Command, Observer)
- ScriptableObjects for data-driven design
- Component composition and architecture
- Assembly Definitions and namespaces
- Coroutines and async/await patterns
- Event systems (Actions, Events, UnityEvents, ScriptableObject Events)
- Performance optimization patterns
- Testing with Unity Test Framework

**Use when:**
- Creating gameplay systems or mechanics
- Working with MonoBehaviour scripts
- Implementing design patterns
- Building with ScriptableObjects
- Optimizing game performance
- Creating component-based architecture

**Customization:** ⚠️ Update `pathPatterns` in skill-rules.json to match your Unity project structure

**Example pathPatterns:**
```json
{
  "pathPatterns": [
    "Assets/Scripts/**/*.cs",
    "Assets/_Project/Scripts/**/*.cs",
    "Assets/Core/**/*.cs",
    "Assets/Gameplay/**/*.cs"
  ]
}
```

**[View Skill →](unity-gameplay-patterns/)**

---

### unity-ui-guidelines
**Purpose:** Unity UI development with UI Toolkit (primary) and UGUI reference

**Files:** 8 resource files (586 lines main + resources)

**Covers:**
- UI Toolkit architecture (UXML, USS, C#)
- VisualElement hierarchy and styling
- Data binding and MVVM patterns
- UI Builder workflow
- UGUI patterns (legacy reference)
- Event handling and manipulators
- UI performance optimization
- Responsive UI design

**Use when:**
- Creating UI with UI Toolkit
- Working with UXML or USS files
- Building menus, HUDs, or dialogs
- Styling UI elements
- Implementing data binding
- Debugging UI issues

**Customization:** ⚠️ Update `pathPatterns` in skill-rules.json to match your UI directory structure

**Example pathPatterns:**
```json
{
  "pathPatterns": [
    "Assets/**/UI/**/*.cs",
    "Assets/**/*.uxml",
    "Assets/**/*.uss",
    "Assets/Menus/**/*.cs"
  ]
}
```

**[View Skill →](unity-ui-guidelines/)**

---

### unity-editor-tools
**Purpose:** Unity Editor extensions, custom inspectors, and tools

**Files:** 8 resource files (138 lines main + resources)

**Covers:**
- Custom Inspector fundamentals
- Property Drawers and attributes
- Editor Windows and dockable windows
- MenuItem and context menus
- Gizmos and Handles API
- ScriptedImporter and AssetPostprocessor
- UI Toolkit for Editor extensions
- Build pipeline integration

**Use when:**
- Creating custom inspectors
- Building editor tools or windows
- Extending the asset pipeline
- Visualizing scene data with Gizmos
- Customizing import settings
- Creating menu items or shortcuts

**Customization:** ⚠️ Update `pathPatterns` in skill-rules.json to match your Editor directory structure

**Example pathPatterns:**
```json
{
  "pathPatterns": [
    "Assets/**/Editor/**/*.cs",
    "Assets/**/*.Editor.cs",
    "Editor/**/*.cs"
  ]
}
```

**[View Skill →](unity-editor-tools/)**

---

### skill-developer (Meta-Skill)
**Purpose:** Creating and managing Claude Code skills

**Files:** 7 resource files (426 lines total)

**Use when:**
- Creating new skills for your Unity project
- Understanding skill structure
- Working with skill-rules.json
- Debugging skill activation
- Customizing trigger patterns

**Customization:** ✅ None - copy as-is

**[View Skill →](skill-developer/)**

---

## How to Add a Skill to Your Unity Project

### Quick Integration

**For Claude Code:**
```
User: "Add the unity-gameplay-patterns skill to my project"

Claude should:
1. Ask about Unity project structure
2. Copy skill directory
3. Update skill-rules.json with their Unity paths
4. Verify integration
```

See [CLAUDE_INTEGRATION_GUIDE.md](../../CLAUDE_INTEGRATION_GUIDE.md) for complete instructions.

### Manual Integration

**Step 1: Copy the skill directory**
```bash
cp -r claude-code-infrastructure-unity/.claude/skills/unity-gameplay-patterns \
      your-unity-project/.claude/skills/
```

**Step 2: Update skill-rules.json**

If you don't have one, create it:
```bash
cp claude-code-infrastructure-unity/.claude/skills/skill-rules.json \
   your-unity-project/.claude/skills/
```

Then customize the `pathPatterns` for your Unity project structure:
```json
{
  "skills": {
    "unity-gameplay-patterns": {
      "fileTriggers": {
        "pathPatterns": [
          "Assets/Scripts/**/*.cs",  // ← Update to match your structure!
          "Assets/_Project/Scripts/**/*.cs"
        ]
      }
    }
  }
}
```

**Step 3: Test**
- Edit a MonoBehaviour script in your Unity project
- The skill should activate automatically

---

## skill-rules.json Configuration

### What It Does

Defines when skills should activate based on:
- **Keywords** in user prompts ("MonoBehaviour", "ScriptableObject", "UI Toolkit")
- **Intent patterns** (regex matching user intent)
- **File path patterns** (editing Unity C# scripts)
- **Content patterns** (code contains `class.*MonoBehaviour`)

### Configuration Format

```json
{
  "skill-name": {
    "type": "domain",
    "enforcement": "suggest",
    "priority": "high",
    "promptTriggers": {
      "keywords": ["MonoBehaviour", "ScriptableObject"],
      "intentPatterns": ["(create|add|implement).*?(gameplay|system)"]
    },
    "fileTriggers": {
      "pathPatterns": ["Assets/Scripts/**/*.cs"],
      "pathExclusions": ["**/Editor/**/*.cs"],
      "contentPatterns": ["class.*MonoBehaviour"]
    }
  }
}
```

### Enforcement Levels

- **suggest**: Skill appears as suggestion, doesn't block (recommended for Unity skills)

---

## Creating Your Own Unity Skills

See the **skill-developer** skill for complete guide on:
- Skill YAML frontmatter structure
- Resource file organization
- Trigger pattern design for Unity
- Testing skill activation

**Quick template for Unity:**
```markdown
---
name: my-unity-skill
description: What this Unity skill does
---

# My Unity Skill Title

## Purpose
[Why this Unity skill exists]

## When to Use This Skill
[Auto-activation scenarios for Unity development]

## Quick Reference
[Key Unity patterns and C# examples]

## Resource Files
- [topic-1.md](resources/topic-1.md)
- [topic-2.md](resources/topic-2.md)
```

---

## Troubleshooting

### Skill isn't activating

**Check:**
1. Is skill directory in `.claude/skills/`?
2. Is skill listed in `skill-rules.json`?
3. Do `pathPatterns` match your Unity Assets folder structure?
4. Are hooks installed and working?
5. Is settings.json configured correctly?

**Debug:**
```bash
# Check skill exists
ls -la .claude/skills/

# Validate skill-rules.json
cat .claude/skills/skill-rules.json | jq .

# Check hooks are executable (Unix/Mac/Git Bash)
ls -la .claude/hooks/*.sh

# Test hook manually (Git Bash on Windows)
./.claude/hooks/skill-activation-prompt.sh
```

### Skill activates too often

Update skill-rules.json:
- Make keywords more specific
- Narrow `pathPatterns` to specific Unity directories
- Increase specificity of `intentPatterns`

### Skill never activates

Update skill-rules.json:
- Add more Unity-specific keywords
- Broaden `pathPatterns` to include more Asset directories
- Add more `intentPatterns` for Unity tasks

---

## For Claude Code

**When integrating a Unity skill for a user:**

1. **Read [CLAUDE_INTEGRATION_GUIDE.md](../../CLAUDE_INTEGRATION_GUIDE.md)** first
2. Ask about their Unity project structure
3. Customize `pathPatterns` in skill-rules.json to match their Assets folder structure
4. Verify the skill file has no hardcoded Unity project paths
5. Test activation after integration

**Common Unity project structures:**
```
Assets/
├── Scripts/              # Standard Unity structure
├── _Project/Scripts/     # Custom Unity structure
├── Core/                 # Modular structure
├── Gameplay/
└── Editor/
```

**Common mistakes:**
- Keeping example paths (Assets/Scripts/)
- Not asking about their Unity folder organization
- Copying skill-rules.json without customization for Unity

---

## Next Steps

1. **Start simple:** Add unity-gameplay-patterns skill to match your Unity work
2. **Verify activation:** Edit a MonoBehaviour script, skill should suggest
3. **Add more:** Once first skill works, add unity-ui-guidelines or unity-editor-tools
4. **Customize:** Adjust triggers based on your Unity workflow

**Questions?** See [CLAUDE_INTEGRATION_GUIDE.md](../../CLAUDE_INTEGRATION_GUIDE.md) for comprehensive Unity integration instructions.
