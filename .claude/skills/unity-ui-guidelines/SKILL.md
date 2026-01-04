---
name: unity-ui-guidelines
description: Unity UI development guide for Unity 6.3 LTS focusing primarily on UI Toolkit (modern, recommended approach). Use when creating user interfaces, HUDs, menus, dialogs, or working with UXML, USS, C# UI scripting, VisualElements, data binding, event systems, responsive layouts, and UI performance optimization. Includes UGUI guidance for legacy projects and world-space UI needs.
---

# Unity UI Development Guidelines

## Purpose

Establish best practices for UI development in Unity 6.3 LTS with **primary focus on UI Toolkit** (Unity's modern, performant UI system).

## When to Use This Skill

Automatically activates when working on:
- Creating UI Toolkit interfaces (UXML, USS, C#)
- Building menus, HUDs, dialogs, and forms with UI Toolkit
- UI data binding and reactive patterns
- Responsive and adaptive layouts
- UI performance optimization
- UI event handling and custom controls
- UGUI (legacy) - for existing projects or world-space UI

---

## Quick Start

### UI Toolkit Workflow

**1. Create UXML (Structure)**
**2. Create USS (Styling)**
**3. Create C# Controller (Logic)**

```csharp
// MainMenuController.cs
using UnityEngine;
using UnityEngine.UIElements;

public class MainMenuController : MonoBehaviour
{
    private UIDocument uiDocument;
    private Button startButton;
    private Button settingsButton;
    private Label titleLabel;

    void OnEnable()
    {
        uiDocument = GetComponent<UIDocument>();
        var root = uiDocument.rootVisualElement;

        // Query UI elements (like querySelector in web)
        startButton = root.Q<Button>("StartButton");
        settingsButton = root.Q<Button>("SettingsButton");
        titleLabel = root.Q<Label>("Title");

        // Register events
        startButton.clicked += OnStartClicked;
        settingsButton.clicked += OnSettingsClicked;
    }

    void OnDisable()
    {
        // Always unregister to prevent memory leaks
        startButton.clicked -= OnStartClicked;
        settingsButton.clicked -= OnSettingsClicked;
    }

    void OnStartClicked() => LoadGameScene();
    void OnSettingsClicked() => OpenSettings();
}
```

**📖 [Complete UI Toolkit guide →](resources/ui-toolkit-basics.md)**

---

## Why UI Toolkit?

### UI Toolkit (Unity 6.3 LTS Recommended)

**Advantages:**
- ✅ **Better Performance** - Efficient batching, less draw calls
- ✅ **CSS-like Styling** - Reusable styles with USS
- ✅ **Modern Workflow** - UI Builder visual editor
- ✅ **Responsive Design** - Flexbox layout system
- ✅ **Data Binding** - MVVM patterns supported
- ✅ **Active Development** - Unity's future for UI
- ✅ **Editor Integration** - Same system for runtime and editor tools

**When to Use:**
- All new projects
- Menus, HUDs, inventory screens
- In-game UI panels
- Settings screens
- Mobile UI

### UGUI (Legacy - Limited Support)

**Use ONLY when:**
- Maintaining existing UGUI projects
- Need world-space UI (nameplates, healthbars above characters)
- Require TextMeshPro 3D text
- Specific asset store packages require UGUI

**Note:** For new projects in Unity 6.3 LTS, **use UI Toolkit**.

**📖 [UGUI reference →](resources/ugui-patterns.md)** (for legacy projects)

---

## Core Concepts

### 1. UXML - Structure

UXML defines UI hierarchy (like HTML).

**Example: MainMenu.uxml**
```xml
<ui:UXML xmlns:ui="UnityEngine.UIElements">
    <ui:VisualElement name="Container" class="menu-container">
        <ui:Label name="Title" text="My Game" class="title" />

        <ui:VisualElement class="button-group">
            <ui:Button name="StartButton" text="Start Game" class="menu-button primary" />
            <ui:Button name="SettingsButton" text="Settings" class="menu-button" />
            <ui:Button name="QuitButton" text="Quit" class="menu-button" />
        </ui:VisualElement>

        <ui:Label name="Version" text="v1.0.0" class="version-label" />
    </ui:VisualElement>
</ui:UXML>
```

**Common Elements:**
- `VisualElement` - Container (like `<div>`)
- `Label` - Text display
- `Button` - Clickable button
- `TextField` - Text input
- `Slider`, `Toggle`, `DropdownField` - Input controls
- `ScrollView` - Scrollable container
- `ListView` - Data-driven lists

**📖 [UXML guide →](resources/uxml-templates.md)**

---

### 2. USS - Styling

USS is CSS-like styling for UI Toolkit.

**Example: MainMenu.uss**
```css
/* Container styles */
.menu-container {
    flex-grow: 1;
    justify-content: center;
    align-items: center;
    background-color: rgba(0, 0, 0, 0.9);
    padding: 20px;
}

/* Typography */
.title {
    font-size: 64px;
    color: white;
    -unity-font-style: bold;
    margin-bottom: 40px;
    -unity-text-align: middle-center;
}

.version-label {
    position: absolute;
    bottom: 10px;
    right: 10px;
    font-size: 14px;
    color: rgba(255, 255, 255, 0.5);
}

/* Button styles */
.menu-button {
    width: 300px;
    height: 60px;
    margin: 10px;
    font-size: 24px;
    background-color: rgb(50, 50, 50);
    color: white;
    border-radius: 10px;
    transition-duration: 0.2s;
}

.menu-button:hover {
    background-color: rgb(80, 80, 80);
    scale: 1.05;
}

.menu-button:active {
    background-color: rgb(30, 30, 30);
}

.menu-button.primary {
    background-color: rgb(0, 120, 215);
}

.menu-button.primary:hover {
    background-color: rgb(0, 150, 255);
}

/* Flexbox layout */
.button-group {
    flex-direction: column;
    align-items: center;
}
```

**USS Flexbox Properties:**
- `flex-direction`: row | column
- `justify-content`: flex-start | center | flex-end | space-between
- `align-items`: flex-start | center | flex-end | stretch
- `flex-grow`, `flex-shrink`, `flex-basis`

**📖 [USS styling guide →](resources/ui-toolkit-styling.md)**

---

### 3. C# Scripting

Query and manipulate UI elements in code.

```csharp
using UnityEngine;
using UnityEngine.UIElements;

public class PlayerHUD : MonoBehaviour
{
    [SerializeField] private UIDocument hudDocument;

    private Label healthLabel;
    private ProgressBar healthBar;
    private Label ammoLabel;

    // Cached queries (do once in OnEnable)
    void OnEnable()
    {
        var root = hudDocument.rootVisualElement;

        // Query by name (ID selector)
        healthLabel = root.Q<Label>("HealthLabel");
        healthBar = root.Q<ProgressBar>("HealthBar");
        ammoLabel = root.Q<Label>("AmmoLabel");
    }

    // Update UI
    public void UpdateHealth(int current, int max)
    {
        healthLabel.text = $"HP: {current}/{max}";
        healthBar.value = (float)current / max * 100;
        healthBar.title = $"{current}/{max}";

        // Change style based on health
        if (current < max * 0.25f)
        {
            healthBar.AddToClassList("low-health");
        }
        else
        {
            healthBar.RemoveFromClassList("low-health");
        }
    }

    public void UpdateAmmo(int current, int max)
    {
        ammoLabel.text = $"{current}/{max}";
    }
}
```

**Query Methods:**
```csharp
root.Q<Button>("MyButton");              // By name (most common)
root.Q<Button>(className: "my-class");   // By class
root.Q<Label>(null, "class1", "class2"); // Multiple classes
root.Q<VisualElement>("#my-id");         // ID selector (deprecated, use name)
```

**📖 [C# scripting patterns →](resources/ui-toolkit-basics.md)**

---

### 4. Data Binding

Automatically sync data with UI.

```csharp
using UnityEngine;
using UnityEngine.UIElements;

public class PlayerStatsUI : MonoBehaviour
{
    [SerializeField] private UIDocument statsDocument;

    // Observable properties
    private int health = 100;
    public int Health
    {
        get => health;
        set
        {
            health = value;
            OnHealthChanged();
        }
    }

    private ProgressBar healthBar;
    private Label healthLabel;

    void OnEnable()
    {
        var root = statsDocument.rootVisualElement;
        healthBar = root.Q<ProgressBar>("HealthBar");
        healthLabel = root.Q<Label>("HealthLabel");

        // Initial update
        OnHealthChanged();
    }

    void OnHealthChanged()
    {
        if (healthBar != null)
        {
            healthBar.value = health;
            healthLabel.text = $"Health: {health}";
        }
    }
}
```

**📖 [Data binding patterns →](resources/ui-toolkit-bindings.md)**

---

### 5. Event Handling

**Button Events:**
```csharp
button.clicked += OnButtonClicked;  // Most common
button.RegisterCallback<ClickEvent>(OnClick);  // Lower-level
```

**Custom Events:**
```csharp
myElement.RegisterCallback<PointerEnterEvent>(OnPointerEnter);
myElement.RegisterCallback<PointerLeaveEvent>(OnPointerLeave);
myElement.RegisterCallback<PointerDownEvent>(OnPointerDown);
```

**Event Propagation:**
```csharp
void OnClick(ClickEvent evt)
{
    // Stop event from bubbling up
    evt.StopPropagation();

    // Prevent default behavior
    evt.PreventDefault();
}
```

**📖 [Event system guide →](resources/ui-events.md)**

---

## UI Builder Tool

**Unity's visual editor for UI Toolkit** (like Photoshop/Figma for UI).

**Workflow:**
1. Window → UI Toolkit → UI Builder
2. Design layout visually
3. Apply USS styles
4. Export to UXML
5. Reference in UIDocument component
6. Script behavior in C#

**Best Practices:**
- Use UI Builder for layout and structure
- Write USS manually for reusable styles
- Use C# for logic and data binding

---

## Responsive Design

**Flexbox Layout:**
```css
.responsive-container {
    flex-direction: row;
    flex-wrap: wrap;
    justify-content: space-around;
}

/* Mobile - vertical layout */
@media (max-width: 768px) {
    .responsive-container {
        flex-direction: column;
    }
}
```

**Percentage Sizing:**
```css
.full-width {
    width: 100%;
}

.half-width {
    width: 50%;
}
```

**Unity Units:**
```css
.fixed {
    width: 200px;           /* Pixels */
}

.percentage {
    width: 50%;             /* Percentage of parent */
}
```

---

## Performance Optimization

**Key Principles:**

1. **Minimize Rebuilds** - Batch property changes
2. **Use USS Classes** - More efficient than inline styles
3. **Object Pooling** - For dynamic lists
4. **Disable PickingMode** - On non-interactive elements
5. **Optimize Queries** - Cache VisualElement references

**Example:**
```csharp
// ❌ BAD - Multiple rebuilds
label.text = "Score: ";
label.style.color = Color.red;
label.style.fontSize = 20;

// ✅ GOOD - Single update + USS class
label.AddToClassList("score-label");
label.text = "Score: 100";
```

**USS Class Toggles:**
```csharp
// Better than inline style changes
element.AddToClassList("highlighted");
element.RemoveFromClassList("highlighted");
element.ToggleInClassList("highlighted");
```

**📖 [Performance guide →](resources/ui-performance.md)**

---

## Common Patterns

### Modal Dialog

```csharp
public class DialogManager : MonoBehaviour
{
    [SerializeField] private UIDocument dialogDocument;
    private VisualElement overlay;
    private VisualElement dialogBox;
    private Label messageLabel;
    private Button confirmButton;
    private Button cancelButton;

    private System.Action onConfirmAction;

    void OnEnable()
    {
        var root = dialogDocument.rootVisualElement;
        overlay = root.Q("Overlay");
        dialogBox = root.Q("DialogBox");
        messageLabel = root.Q<Label>("Message");
        confirmButton = root.Q<Button>("ConfirmButton");
        cancelButton = root.Q<Button>("CancelButton");

        confirmButton.clicked += OnConfirm;
        cancelButton.clicked += OnCancel;

        HideDialog();
    }

    public void ShowDialog(string message, System.Action onConfirm = null)
    {
        messageLabel.text = message;
        onConfirmAction = onConfirm;
        overlay.style.display = DisplayStyle.Flex;
    }

    void HideDialog()
    {
        overlay.style.display = DisplayStyle.None;
    }

    void OnConfirm()
    {
        onConfirmAction?.Invoke();
        HideDialog();
    }

    void OnCancel()
    {
        HideDialog();
    }
}
```

---

## Resource Files

1. **[ui-toolkit-basics.md](resources/ui-toolkit-basics.md)** - Core concepts, VisualElement hierarchy
2. **[ui-toolkit-styling.md](resources/ui-toolkit-styling.md)** - USS mastery, flexbox, responsive design
3. **[uxml-templates.md](resources/uxml-templates.md)** - UXML structure, templates, best practices
4. **[ui-toolkit-bindings.md](resources/ui-toolkit-bindings.md)** - Data binding, reactive UI
5. **[ui-events.md](resources/ui-events.md)** - Event handling, custom controls
6. **[ui-performance.md](resources/ui-performance.md)** - Optimization techniques
7. **[ui-accessibility.md](resources/ui-accessibility.md)** - Accessibility best practices
8. **[ugui-patterns.md](resources/ugui-patterns.md)** - UGUI reference (legacy)

---

## Quick Reference

### Common USS Properties
```css
/* Layout */
flex-direction: row | column;
justify-content: flex-start | center | flex-end | space-between;
align-items: flex-start | center | flex-end | stretch;

/* Sizing */
width: 100px | 50% | auto;
height: 100px | 50% | auto;
flex-grow: 1;

/* Spacing */
margin: 10px;
padding: 10px;

/* Colors */
background-color: rgb(255, 0, 0);
color: rgba(255, 255, 255, 0.5);

/* Typography */
font-size: 16px;
-unity-font-style: bold | italic;
-unity-text-align: upper-left | middle-center | lower-right;

/* Borders */
border-width: 2px;
border-color: white;
border-radius: 10px;
```

### USS Selectors
```css
.class-name { }      /* Class */
#element-name { }    /* Name (ID) */
Button { }           /* Type */
.parent .child { }   /* Descendant */
:hover { }           /* Pseudo-class */
:active { }
:focus { }
:disabled { }
```

---

**Last Updated:** 2025-01-04
**Unity Version:** 6.3 LTS
**UI System:** UI Toolkit (Primary)
