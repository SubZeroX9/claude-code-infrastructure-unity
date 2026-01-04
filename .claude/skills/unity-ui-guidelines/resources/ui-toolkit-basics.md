# UI Toolkit Basics

Core concepts and architecture of Unity UI Toolkit.

## Visual Element Hierarchy

All UI elements inherit from `VisualElement`:
- `Label` - Text display
- `Button` - Clickable button
- `TextField` - Text input
- `ScrollView` - Scrollable container
- `ListView` - Data-driven lists

## Query Elements

```csharp
var root = uiDocument.rootVisualElement;
var button = root.Q<Button>("MyButton");
button.clicked += OnClick;
```

## Manipulation

```csharp
// Change text
label.text = "New Text";

// Toggle visibility
element.style.display = DisplayStyle.None;

// Add/Remove classes
element.AddToClassList("highlighted");
element.RemoveFromClassList("highlighted");
```

Last Updated: 2025-01-04
