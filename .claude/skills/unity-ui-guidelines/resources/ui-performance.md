# UI Performance Optimization

Optimize UI Toolkit performance.

## Best Practices

1. Cache VisualElement references
2. Use USS classes instead of inline styles
3. Batch property changes
4. Disable picking on non-interactive elements
5. Use object pooling for dynamic lists

## Example

```csharp
// ❌ BAD
label.style.color = Color.red;
label.style.fontSize = 20;

// ✅ GOOD
label.AddToClassList("warning-label");
```

Last Updated: 2025-01-04
