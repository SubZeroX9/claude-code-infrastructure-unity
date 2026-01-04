# ScriptableObjects for Data-Driven Design

Comprehensive guide to using ScriptableObjects for data management, configuration, and runtime architecture in Unity.

---

## Overview

ScriptableObjects are Unity assets that store data independently of GameObject instances. They're essential for data-driven game design and provide a powerful alternative to traditional data storage approaches.

---

## What are ScriptableObjects?

**ScriptableObject** is a Unity class that allows you to create data containers saved as assets in your project.

```csharp
using UnityEngine;

[CreateAssetMenu(fileName = "NewItem", menuName = "Game/Item")]
public class ItemData : ScriptableObject
{
    public string itemName;
    public string description;
    public Sprite icon;
    public int maxStackSize = 99;
    public float weight;
}
```

**Key Characteristics:**
- Asset files (not scene objects)
- Shared across scenes
- Memory efficient (single instance in memory)
- Designer-friendly (edit in Inspector)
- Version control friendly

---

## Use Cases

### 1. Game Configuration

Store game settings, difficulty parameters, and constants.

```csharp
[CreateAssetMenu(menuName = "Game/Game Config")]
public class GameConfig : ScriptableObject
{
    [Header("Player Settings")]
    public float playerMoveSpeed = 5f;
    public float playerJumpForce = 10f;
    public int playerMaxHealth = 100;

    [Header("Enemy Settings")]
    public float enemyDetectionRange = 10f;
    public float enemyAttackCooldown = 2f;

    [Header("Game Rules")]
    public int pointsPerCoin = 10;
    public int pointsPerEnemy = 100;
    public float respawnTime = 3f;
}

// Usage
public class PlayerController : MonoBehaviour
{
    [SerializeField] private GameConfig config;

    void Start()
    {
        // Use config values
        maxHealth = config.playerMaxHealth;
        moveSpeed = config.playerMoveSpeed;
    }
}
```

### 2. ScriptableObject Events

Globally accessible event system for decoupling.

```csharp
[CreateAssetMenu(menuName = "Events/Void Event")]
public class VoidEvent : ScriptableObject
{
    private event Action OnRaised;

    public void Raise()
    {
        OnRaised?.Invoke();
    }

    public void AddListener(Action listener)
    {
        OnRaised += listener;
    }

    public void RemoveListener(Action listener)
    {
        OnRaised -= listener;
    }
}
```

### 3. Runtime Sets Pattern

Track all active instances of a type.

```csharp
[CreateAssetMenu(menuName = "Sets/Enemy Set")]
public class EnemyRuntimeSet : ScriptableObject
{
    public List<Enemy> items = new List<Enemy>();

    public void Add(Enemy enemy)
    {
        if (!items.Contains(enemy))
            items.Add(enemy);
    }

    public void Remove(Enemy enemy)
    {
        items.Remove(enemy);
    }
}

// Enemy registers itself
public class Enemy : MonoBehaviour
{
    [SerializeField] private EnemyRuntimeSet allEnemies;

    void OnEnable() => allEnemies.Add(this);
    void OnDisable() => allEnemies.Remove(this);
}
```

---

## Best Practices

### Don't Modify at Runtime

```csharp
// ❌ BAD - Modifies the asset!
data.health -= damage;

// ✅ GOOD - Copy to instance variable
private int currentHealth;
void Awake() { currentHealth = data.maxHealth; }
```

---

**Last Updated:** 2025-01-04
