---
name: unity-editor-tools
description: Unity Editor scripting and extension development for Unity 6.3 LTS. Use when creating custom inspectors, property drawers, editor windows, asset importers, build pipeline extensions, gizmos, handles, or any Unity Editor customization. Covers EditorWindow, CustomEditor, PropertyDrawer, ScriptedImporter, menu items, and UI Toolkit for editor UI.
---

# Unity Editor Extensions

## Purpose

Guide for extending Unity Editor with custom tools, inspectors, and workflows for Unity 6.3 LTS.

## When to Use

- Custom Inspector design
- Property Drawer creation
- Editor Windows and tools
- Asset import customization
- Build pipeline modifications
- Scene view visualization (Gizmos/Handles)

---

## Quick Start

### Custom Inspector

```csharp
using UnityEditor;
using UnityEngine;

[CustomEditor(typeof(Enemy))]
public class EnemyEditor : Editor
{
    public override void OnInspectorGUI()
    {
        Enemy enemy = (Enemy)target;

        EditorGUILayout.LabelField("Enemy Settings", EditorStyles.boldLabel);
        enemy.health = EditorGUILayout.IntField("Health", enemy.health);
        enemy.speed = EditorGUILayout.Slider("Speed", enemy.speed, 0f, 10f);

        if (GUILayout.Button("Reset to Default"))
        {
            enemy.health = 100;
            enemy.speed = 3f;
        }

        EditorUtility.SetDirty(enemy);
    }
}
```

### Property Drawer

```csharp
[CustomPropertyDrawer(typeof(MinMaxRange))]
public class MinMaxRangeDrawer : PropertyDrawer
{
    public override void OnGUI(Rect position, SerializedProperty property, GUIContent label)
    {
        var minProp = property.FindPropertyRelative("min");
        var maxProp = property.FindPropertyRelative("max");

        EditorGUI.BeginProperty(position, label, property);

        position = EditorGUI.PrefixLabel(position, label);

        float minValue = minProp.floatValue;
        float maxValue = maxProp.floatValue;

        EditorGUI.MinMaxSlider(position, ref minValue, ref maxValue, 0f, 100f);

        minProp.floatValue = minValue;
        maxProp.floatValue = maxValue;

        EditorGUI.EndProperty();
    }
}
```

### Editor Window

```csharp
public class LevelDesignWindow : EditorWindow
{
    [MenuItem("Tools/Level Design Window")]
    public static void ShowWindow()
    {
        GetWindow<LevelDesignWindow>("Level Design");
    }

    void OnGUI()
    {
        GUILayout.Label("Level Tools", EditorStyles.boldLabel);

        if (GUILayout.Button("Generate Terrain"))
        {
            GenerateTerrain();
        }

        if (GUILayout.Button("Place Props"))
        {
            PlaceProps();
        }
    }

    void GenerateTerrain() { /* Implementation */ }
    void PlaceProps() { /* Implementation */ }
}
```

---

## Resource Files

1. **[custom-inspectors.md](resources/custom-inspectors.md)** - Custom editor creation
2. **[property-drawers.md](resources/property-drawers.md)** - Property drawer patterns
3. **[editor-windows.md](resources/editor-windows.md)** - Editor window development
4. **[asset-importers.md](resources/asset-importers.md)** - Custom asset import
5. **[gizmos-handles.md](resources/gizmos-handles.md)** - Scene visualization
6. **[editor-utilities.md](resources/editor-utilities.md)** - Menu items, utilities
7. **[ui-toolkit-editor.md](resources/ui-toolkit-editor.md)** - UI Toolkit for editors
8. **[build-pipeline.md](resources/build-pipeline.md)** - Build customization

---

## Best Practices

1. Always use `#if UNITY_EDITOR` for editor-only code
2. Use SerializedObject/SerializedProperty for Undo support
3. Call EditorUtility.SetDirty() after changes
4. Prefer UI Toolkit for new editor windows (Unity 6+)
5. Use Gizmos for constant visualization, Handles for interactive tools

---

**Last Updated:** 2025-01-04
**Unity Version:** 6.3 LTS
