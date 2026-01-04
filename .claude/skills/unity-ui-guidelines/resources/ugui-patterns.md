# UGUI Patterns (Legacy)

UGUI reference for legacy projects.

## Canvas Types

- **Screen Space - Overlay**: UI on top of everything
- **Screen Space - Camera**: UI in camera space
- **World Space**: 3D UI in world

## RectTransform Anchors

```csharp
// Stretch to fill
rt.anchorMin = Vector2.zero;
rt.anchorMax = Vector2.one;
rt.offsetMin = Vector2.zero;
rt.offsetMax = Vector2.zero;
```

## Best Practices

- One Canvas per layer
- Use CanvasGroup for group operations
- Minimize raycast targets
- Use atlases for sprites

Last Updated: 2025-01-04
