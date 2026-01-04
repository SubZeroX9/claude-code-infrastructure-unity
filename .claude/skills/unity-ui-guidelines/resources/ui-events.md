# UI Event Handling

Event system for UI Toolkit.

## Button Events

```csharp
button.clicked += OnClick;
button.RegisterCallback<ClickEvent>(OnClickEvent);
```

## Custom Events

```csharp
element.RegisterCallback<PointerEnterEvent>(OnPointerEnter);
element.RegisterCallback<PointerLeaveEvent>(OnPointerLeave);
```

## Event Propagation

```csharp
void OnClick(ClickEvent evt) {
    evt.StopPropagation();
    evt.PreventDefault();
}
```

Last Updated: 2025-01-04
