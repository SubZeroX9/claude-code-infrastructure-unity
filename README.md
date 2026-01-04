# Claude Code Unity 6.3 LTS Toolkit

**Production-tested Claude Code infrastructure for Unity game development.**

Transformed from a web development showcase to a comprehensive Unity 6.3 LTS toolkit, this repository provides skills, agents, and automation patterns specifically designed for Unity developers using Claude Code.

> **This is a reference library** - Copy what you need into your Unity projects.

---

## What's Inside

**Unity-focused infrastructure for:**
- ✅ **Auto-activating skills** for Unity development
- ✅ **Gameplay programming patterns** (MonoBehaviour, ScriptableObjects, design patterns)
- ✅ **UI Toolkit development** (UXML, USS, modern Unity UI)
- ✅ **Editor extensions** (Custom Inspectors, Property Drawers, Tools)
- ✅ **Specialized agents** for Unity-specific tasks
- ✅ **Modular skill pattern** (500-line rule with progressive disclosure)

**Unity Version:** 6.3 LTS
**Language:** C# 9.0+

---

## Quick Start

### 🤖 Using Claude Code to Integrate?

**Claude:** Read [`CLAUDE_INTEGRATION_GUIDE.md`](CLAUDE_INTEGRATION_GUIDE.md) for step-by-step integration instructions tailored for Unity projects.

### 🎯 I want skill auto-activation for Unity

**The breakthrough feature:** Skills that actually activate when working on Unity code.

**What you need:**
1. The skill-activation hooks (2 files)
2. Unity-specific skills
3. 15 minutes

**👉 [Setup Guide: .claude/hooks/README.md](.claude/hooks/README.md)**

### 📚 I want to add ONE Unity skill

Browse the [skills catalog](.claude/skills/) and copy what you need.

**Available Unity Skills:**
- **unity-gameplay-patterns** - MonoBehaviour lifecycle, design patterns, ScriptableObjects
- **unity-ui-guidelines** - UI Toolkit (modern), UGUI reference
- **unity-editor-tools** - Custom Inspectors, PropertyDrawers, EditorWindows
- **skill-developer** - Meta-skill for creating your own skills

**👉 [Skills Guide: .claude/skills/README.md](.claude/skills/README.md)**

### 🤖 I want specialized agents

Production-tested agents for Unity development:
- Code architecture review (Unity-specific)
- Refactoring assistance (C#/Unity)
- Documentation generation (XML docs)
- Error debugging
- And more...

**👉 [Agents Guide: .claude/agents/README.md](.claude/agents/README.md)**

---

## What Makes This Different?

### Unity 6.3 LTS Focused

**Built specifically for Unity 6.3 LTS:**
- ✅ UI Toolkit (modern Unity UI system)
- ✅ C# 9.0+ language features
- ✅ MonoBehaviour best practices
- ✅ Assembly Definitions organization
- ✅ ScriptableObject patterns
- ✅ Editor scripting with UI Toolkit

### The Auto-Activation Breakthrough

**Problem:** Claude Code skills just sit there. You have to remember to use them.

**Solution:** UserPromptSubmit hook that:
- Analyzes your prompts for Unity keywords
- Checks file context (.cs, .uxml, .uss)
- Automatically suggests relevant Unity skills
- Works via `skill-rules.json` configuration

**Result:** Skills activate when you need them, not when you remember them.

### Modular Skills (500-Line Rule)

Large skills hit context limits. The solution:

```
skill-name/
  SKILL.md                  # <500 lines, high-level guide
  resources/
    topic-1.md              # <500 lines each
    topic-2.md
    topic-3.md
```

**Progressive disclosure:** Claude loads main skill first, loads resources only when needed.

---

## Repository Structure

```
.claude/
├── skills/                 # 4 Unity skills
│   ├── unity-gameplay-patterns/  (8 resource files)
│   ├── unity-ui-guidelines/      (8 resource files)
│   ├── unity-editor-tools/       (8 resource files)
│   ├── skill-developer/          (7 resource files)
│   └── skill-rules.json    # Unity-specific activation rules
├── hooks/                  # 2 essential hooks
│   ├── skill-activation-prompt.*  (ESSENTIAL)
│   └── post-tool-use-tracker.sh   (ESSENTIAL)
├── agents/                 # Specialized Unity agents
│   ├── code-architecture-reviewer.md
│   ├── refactor-planner.md
│   └── ... more agents
└── commands/               # Slash commands
    └── dev-docs.md
```

---

## Component Catalog

### 🎮 Unity Skills (4)

| Skill | Focus | Best For |
|-------|-------|----------|
| [**unity-gameplay-patterns**](.claude/skills/unity-gameplay-patterns/) | MonoBehaviour, ScriptableObjects, Patterns | Core gameplay programming |
| [**unity-ui-guidelines**](.claude/skills/unity-ui-guidelines/) | UI Toolkit, UXML, USS | Modern Unity UI |
| [**unity-editor-tools**](.claude/skills/unity-editor-tools/) | CustomEditor, PropertyDrawer | Editor extensions |
| [**skill-developer**](.claude/skills/skill-developer/) | Meta-skill | Creating custom skills |

**All skills follow the modular pattern** - main file + resource files for progressive disclosure.

**👉 [How to integrate skills →](.claude/skills/README.md)**

### 🪝 Hooks (2 Essential)

| Hook | Type | Essential? |
|------|------|-----------|
| skill-activation-prompt | UserPromptSubmit | ✅ YES |
| post-tool-use-tracker | PostToolUse | ✅ YES |

**Start with the two essential hooks** - they enable skill auto-activation and work out of the box.

**👉 [Hook setup guide →](.claude/hooks/README.md)**

### 🤖 Agents

**Standalone - just copy and use!**

| Agent | Purpose |
|-------|---------|
| code-architecture-reviewer | Review Unity code for best practices |
| code-refactor-master | Plan and execute C# refactoring |
| documentation-architect | Generate comprehensive documentation |
| plan-reviewer | Review development plans |
| refactor-planner | Create refactoring strategies |
| web-research-specialist | Research Unity issues online |

**👉 [How agents work →](.claude/agents/README.md)**

---

## Key Concepts

### Hooks + skill-rules.json = Auto-Activation

**The system:**
1. **skill-activation-prompt hook** runs on every user prompt
2. Checks **skill-rules.json** for Unity trigger patterns
3. Suggests relevant skills automatically
4. Skills load only when needed

**Unity-specific triggers:**
- Keywords: MonoBehaviour, ScriptableObject, UI Toolkit, CustomEditor
- File patterns: `Assets/**/*.cs`, `Assets/**/*.uxml`, `Assets/**/Editor/**/*.cs`
- Content patterns: `class.*MonoBehaviour`, `using UnityEngine.UIElements`

### Progressive Disclosure (500-Line Rule)

**Problem:** Large skills hit context limits

**Solution:** Modular structure
- Main SKILL.md <500 lines (overview + navigation)
- Resource files <500 lines each (deep dives)
- Claude loads incrementally as needed

**Example:** unity-gameplay-patterns has 8 resource files covering lifecycle, patterns, ScriptableObjects, performance, etc.

---

## ⚠️ Important: What Won't Work As-Is

### settings.json
The included `settings.json` is streamlined for Unity:
- Removed web-specific MCP servers (mysql, playwright)
- Removed TypeScript-specific hooks (tsc-check)
- Kept essential hooks (UserPromptSubmit, PostToolUse)

**To use it:**
1. Copy UserPromptSubmit and PostToolUse hooks
2. Optionally add Unity-specific Stop hooks
3. Update MCP server list for your setup

### Unity Project Structure
Skills assume common Unity folder structures:
- `Assets/Scripts/` - Gameplay code
- `Assets/UI/` - UI files
- `Assets/Editor/` - Editor extensions

**Customize:**
- Update pathPatterns in skill-rules.json for your structure
- Examples: `Assets/_Project/Scripts/`, `Assets/Core/`, etc.

---

## Integration Workflow

**Recommended approach:**

### Phase 1: Skill Activation (15 min)
1. Copy skill-activation-prompt hook
2. Copy post-tool-use-tracker hook
3. Update settings.json
4. Install hook dependencies (Node.js for TypeScript hooks)

### Phase 2: Add First Skill (10 min)
1. Pick ONE relevant skill (start with unity-gameplay-patterns)
2. Copy skill directory to your project
3. Skill-rules.json already configured
4. Test with Unity C# file

### Phase 3: Test & Iterate (5 min)
1. Edit a MonoBehaviour script - skill should activate
2. Ask "how do I create a ScriptableObject?" - skill should suggest
3. Add more skills as needed

### Phase 4: Optional Enhancements
- Add agents you find useful
- Customize skill-rules.json path patterns
- Add Unity-specific Stop hooks (compilation checks, tests)

---

## Getting Help

### For Users
**Issues with integration?**
1. Check [CLAUDE_INTEGRATION_GUIDE.md](CLAUDE_INTEGRATION_GUIDE.md)
2. Ask Claude: "Why isn't [skill] activating in my Unity project?"
3. Open an issue with your Unity project structure

### For Claude Code
When helping users integrate:
1. **Read CLAUDE_INTEGRATION_GUIDE.md FIRST**
2. Ask about their Unity project structure
3. Customize pathPatterns in skill-rules.json
4. Verify after integration

---

## What This Solves

### Before This Infrastructure

❌ Skills don't activate for Unity code
❌ Have to remember Unity-specific patterns
❌ No Unity 6 UI Toolkit guidance
❌ Manual MonoBehaviour lifecycle lookups
❌ Searching for C# design patterns
❌ Editor scripting trial and error

### After This Infrastructure

✅ Skills suggest themselves for Unity code
✅ MonoBehaviour best practices at your fingertips
✅ UI Toolkit patterns and examples ready
✅ ScriptableObject architecture guidance
✅ Custom Inspector templates
✅ Consistent Unity 6.3 LTS patterns

---

## Community

**Found this useful?**

- ⭐ Star this repo
- 🐛 Report Unity-specific issues
- 💬 Share your own Unity skills/agents
- 📝 Contribute Unity examples

**History:**
Originally a web development infrastructure showcase, transformed into a Unity 6.3 LTS toolkit based on community needs for game development workflows.

---

## License

MIT License - Use freely in your Unity projects, commercial or personal.

---

## Quick Links

- 📖 [Claude Integration Guide](CLAUDE_INTEGRATION_GUIDE.md) - For AI-assisted setup
- 🎮 [Unity Skills Documentation](.claude/skills/README.md)
- 🪝 [Hooks Setup](.claude/hooks/README.md)
- 🤖 [Agents Guide](.claude/agents/README.md)

**Start here:** Copy the two essential hooks, add unity-gameplay-patterns skill, and experience auto-activation for Unity development.

---

**Unity Version:** 6.3 LTS
**C# Version:** 9.0+
**Last Updated:** 2025-01-04
