# UXML Templates

UXML structure and reusability.

## Basic Structure

```xml
<ui:UXML xmlns:ui="UnityEngine.UIElements">
    <ui:VisualElement name="Container" class="my-class">
        <ui:Label text="Hello" />
        <ui:Button text="Click Me" name="MyButton" />
    </ui:VisualElement>
</ui:UXML>
```

## Template Reuse

```xml
<ui:Template name="ItemSlot" src="ItemSlot.uxml" />
<ui:Instance template="ItemSlot" />
```

Last Updated: 2025-01-04
