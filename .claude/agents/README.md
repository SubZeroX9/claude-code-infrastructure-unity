# Agents

Specialized agents for complex Unity development tasks.

---

## What Are Agents?

Agents are autonomous Claude instances that handle specific complex tasks. Unlike skills (which provide inline guidance), agents:
- Run as separate sub-tasks
- Work autonomously with minimal supervision
- Have specialized tool access
- Return comprehensive reports when complete

**Key advantage:** Agents are **standalone** - just copy the `.md` file and use immediately!

---

## Available Agents (6)

### code-architecture-reviewer
**Purpose:** Review Unity code for architectural consistency and best practices

**When to use:**
- After implementing a new Unity feature or system
- Before merging significant gameplay changes
- When refactoring MonoBehaviour or ScriptableObject code
- To validate Unity architectural decisions
- Checking adherence to Unity 6.3 LTS patterns

**Unity focus:** Reviews for component lifecycle issues, ScriptableObject usage, performance patterns, and Unity anti-patterns (FindObjectOfType in Update, string tag comparisons, etc.)

**Integration:** ✅ Copy as-is

---

### code-refactor-master
**Purpose:** Plan and execute comprehensive C# refactoring for Unity projects

**When to use:**
- Reorganizing Unity file structures
- Breaking down large MonoBehaviour components
- Extracting systems to ScriptableObjects
- Updating import paths after moves
- Improving Unity code maintainability

**Unity focus:** C# refactoring patterns, Unity-specific refactorings (component extraction, ScriptableObject migration), namespace organization for Unity projects

**Integration:** ✅ Copy as-is

---

### documentation-architect
**Purpose:** Create comprehensive documentation for Unity projects

**When to use:**
- Documenting new gameplay systems
- Creating Unity package documentation
- Writing developer guides for game systems
- Generating architectural overviews
- XML documentation for C# code

**Unity focus:** Unity XML documentation standards, asset documentation patterns, package documentation structure

**Integration:** ✅ Copy as-is

---

### plan-reviewer
**Purpose:** Review development plans before implementation

**When to use:**
- Before starting complex Unity features
- Validating game system architectural plans
- Identifying potential Unity-specific issues early
- Getting second opinion on Unity implementation approach

**Integration:** ✅ Copy as-is

---

### refactor-planner
**Purpose:** Create comprehensive refactoring strategies for Unity code

**When to use:**
- Planning Unity code reorganization
- Modernizing legacy Unity code to 6.3 LTS
- Breaking down large MonoBehaviour files
- Improving Unity project structure

**Unity focus:** C# refactoring patterns, Unity-specific considerations, Assembly Definition organization

**Integration:** ✅ Copy as-is

---

### web-research-specialist
**Purpose:** Research Unity technical issues online

**When to use:**
- Debugging obscure Unity errors
- Finding solutions to Unity-specific problems
- Researching Unity best practices
- Comparing Unity implementation approaches
- Searching Unity forums, discussions, and documentation

**Unity focus:** Searches Unity Discussions, Unity Forums, Unity Docs, Stack Overflow for Unity, GitHub Unity issues

**Integration:** ✅ Copy as-is

---

## How to Integrate an Agent

### Standard Integration (All Agents)

**Step 1: Copy the file**
```bash
cp showcase/.claude/agents/agent-name.md \
   your-unity-project/.claude/agents/
```

**Step 2: Verify (optional)**
```bash
# Check for hardcoded paths
grep -n "~/git/\|/root/git/\|/Users/" your-unity-project/.claude/agents/agent-name.md
```

**Step 3: Use it**
Ask Claude: "Use the [agent-name] agent to [task]"

That's it! Agents work immediately.

---

## When to Use Agents vs Skills

| Use Agents When... | Use Skills When... |
|-------------------|-------------------|
| Task requires multiple steps | Need inline guidance |
| Complex analysis needed | Checking best practices |
| Autonomous work preferred | Want to maintain control |
| Task has clear end goal | Ongoing development work |
| Example: "Review all gameplay controllers" | Example: "Creating a new MonoBehaviour" |

**Both can work together:**
- Skill provides Unity patterns during development
- Agent reviews the result when complete

---

## Agent Quick Reference

| Agent | Complexity | Customization | Unity-Specific |
|-------|-----------|---------------|----------------|
| code-architecture-reviewer | Medium | ✅ None | Yes - Unity patterns |
| code-refactor-master | High | ✅ None | Yes - C#/Unity |
| documentation-architect | Medium | ✅ None | Yes - XML docs |
| plan-reviewer | Low | ✅ None | Framework-agnostic |
| refactor-planner | Medium | ✅ None | Yes - Unity context |
| web-research-specialist | Low | ✅ None | Yes - Unity sources |

---

## For Claude Code

**When integrating agents for a Unity project:**

1. **Read [CLAUDE_INTEGRATION_GUIDE.md](../../CLAUDE_INTEGRATION_GUIDE.md)**
2. **Just copy the .md file** - agents are standalone
3. **Check for hardcoded paths:**
   ```bash
   grep "~/git/\|/root/" agent-name.md
   ```
4. **Update paths if found** to `$CLAUDE_PROJECT_DIR` or `.`
5. **Verify Unity context** is appropriate for the agent's purpose

**That's it!** Agents are the easiest components to integrate.

---

## Creating Your Own Unity Agents

Agents are markdown files with optional YAML frontmatter:

```markdown
# Agent Name

## Purpose
What this Unity agent does

## Instructions
Step-by-step instructions for autonomous execution in Unity context

## Tools Available
List of tools this agent can use

## Expected Output
What format to return results in (XML docs, analysis report, etc.)
```

**Tips for Unity agents:**
- Be very specific about Unity-specific instructions
- Reference Unity 6.3 LTS patterns and APIs
- Break complex tasks into numbered steps
- Specify exactly what to return
- Include examples of good Unity code output
- List available tools explicitly

---

## Troubleshooting

### Agent not found

**Check:**
```bash
# Is agent file present?
ls -la .claude/agents/[agent-name].md
```

### Agent fails with path errors

**Check for hardcoded paths:**
```bash
grep "~/\|/root/\|/Users/" .claude/agents/[agent-name].md
```

**Fix:**
```bash
sed -i 's|~/git/.*project|$CLAUDE_PROJECT_DIR|g' .claude/agents/[agent-name].md
```

---

## Next Steps

1. **Browse agents above** - Find ones useful for Unity work
2. **Copy what you need** - Just the .md file
3. **Ask Claude to use them** - "Use [agent] to [task]"
4. **Create your own** - Follow the pattern for Unity-specific needs

**Questions?** See [CLAUDE_INTEGRATION_GUIDE.md](../../CLAUDE_INTEGRATION_GUIDE.md)
