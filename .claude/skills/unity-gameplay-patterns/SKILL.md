---
name: unity-gameplay-patterns
description: Comprehensive Unity gameplay programming guide for Unity 6.3 LTS. Use when creating gameplay systems, MonoBehaviour scripts, ScriptableObjects, coroutines, or working with Unity component architecture, design patterns, event systems, performance optimization, and testing. Covers MonoBehaviour lifecycle, common design patterns (Service Locator, Object Pool, State Machine), component composition, data-driven design, async patterns, and Unity Test Framework.
---

# Unity Gameplay Programming Patterns

## Purpose

Establish consistency and best practices for gameplay programming in Unity 6.3 LTS projects using modern C# patterns and Unity-specific architectural approaches.

## When to Use This Skill

Automatically activates when working on:
- Creating or modifying MonoBehaviour scripts
- Implementing gameplay systems and mechanics
- Using ScriptableObjects for data-driven design
- Working with coroutines or async/await in Unity
- Building component-based architectures
- Implementing common game design patterns
- Event-driven communication between game objects
- Performance optimization for gameplay code
- Testing gameplay systems

---

## Quick Start

### New Gameplay Feature Checklist

- [ ] **MonoBehaviour**: Proper lifecycle usage (Awake vs Start)
- [ ] **Architecture**: Component composition over inheritance
- [ ] **Data**: ScriptableObjects for configuration
- [ ] **Events**: Clear communication pattern (UnityEvents, C# events, or ScriptableObject events)
- [ ] **Performance**: Optimized Update loops, caching, object pooling
- [ ] **Async**: Appropriate use of coroutines or async/await
- [ ] **Testing**: Unit tests for game logic, Play Mode tests for integration
- [ ] **Patterns**: Appropriate design patterns (Service Locator, State Machine, etc.)

### New Game System Checklist

- [ ] Clear separation of concerns (data, logic, presentation)
- [ ] Proper dependency management (avoid circular dependencies)
- [ ] Event-driven architecture for loose coupling
- [ ] Performance-conscious design (avoid FindObjectOfType in Update, cache references)
- [ ] Testable architecture (logic separated from MonoBehaviour when possible)
- [ ] Documentation (XML comments, README for complex systems)

---

## Core Concepts

### MonoBehaviour Lifecycle

Understanding the Unity lifecycle is fundamental to all gameplay programming.

**Initialization:**
- `Awake()` - Initialize internal state, get references to components on same GameObject
- `OnEnable()` - Subscribe to events, register with managers
- `Start()` - Initialize after all Awake() calls complete, safe to access other objects

**Update Loops:**
- `Update()` - Frame-rate dependent updates, input handling, state machines
- `FixedUpdate()` - Physics and fixed timestep logic (default 50 times/sec)
- `LateUpdate()` - Camera following, procedural animation (after all Update())

**Cleanup:**
- `OnDisable()` - Unsubscribe from events, unregister from managers
- `OnDestroy()` - Final cleanup, release resources

**📖 [Full lifecycle guide →](resources/monobehaviour-lifecycle.md)**

### Component Architecture

Unity's component-based architecture encourages composition over inheritance.

**Principles:**
1. **Single Responsibility** - Each component does one thing well
2. **Composition** - Combine simple components for complex behavior
3. **Communication** - Use interfaces, events, or references for inter-component communication
4. **Reusability** - Small, focused components are more reusable

**Example:**
```csharp
// Good: Small, focused components
public class Health : MonoBehaviour { }
public class Movement : MonoBehaviour { }
public class Combat : MonoBehaviour { }

// Not ideal: God class doing everything
public class Player : MonoBehaviour
{
    // Health, movement, combat, inventory, UI all in one class
}
```

**📖 [Component architecture patterns →](resources/component-architecture.md)**

### ScriptableObjects for Data-Driven Design

ScriptableObjects are Unity's solution for data management and configuration.

**Use Cases:**
- Game configuration (difficulty settings, game constants)
- Item/weapon/character stats
- Shared data between scenes
- Event channels (ScriptableObject events)
- Runtime sets (tracking all instances of a type)

**Benefits:**
- Designer-friendly (editable in Inspector)
- Reduced coupling (data separate from logic)
- Memory efficient (assets, not instances)
- Persistent across scenes

**📖 [ScriptableObject patterns →](resources/scriptable-objects.md)**

### Design Patterns for Unity

Common patterns that solve recurring Unity development problems.

**Essential Patterns:**
1. **Service Locator** - Global access to game services
2. **Object Pool** - Reuse expensive GameObjects (projectiles, particles)
3. **State Machine** - Character states, AI behavior, game flow
4. **Command Pattern** - Undo/redo, input buffering, replays
5. **Observer/Events** - Decouple game systems (achievements, UI updates)

**📖 [Design patterns with Unity examples →](resources/design-patterns.md)**

### Event Systems

Unity offers multiple approaches to event-driven programming.

**Options:**
1. **UnityEvents** - Inspector-assignable, serializable
2. **C# Events** - Type-safe, compile-time checking
3. **ScriptableObject Events** - Globally accessible, designer-friendly
4. **Message System** - Component-based, SendMessage/BroadcastMessage

**Decision Matrix:**
- Unity Events: Simple UI interactions, designer control needed
- C# Events: Type-safety critical, high-performance scenarios
- ScriptableObject Events: Cross-scene communication, decoupling systems
- Messages: Legacy compatibility, dynamic messaging

**📖 [Event system guide →](resources/event-systems.md)**

### Async Programming in Unity

Unity supports both coroutines and modern async/await.

**Coroutines:**
- Unity's traditional async approach
- Integrated with Unity's lifecycle
- Can yield on WaitForSeconds, WaitForFixedUpdate, etc.
- Stopped when GameObject disabled

**Async/Await:**
- Modern C# async pattern
- Better exception handling
- Not tied to GameObject lifecycle (careful!)
- Can use Task.Delay, await resources

**When to use each:**
- Coroutines: Frame-based timing, Unity-specific yields, simple sequences
- Async/Await: Network calls, file I/O, complex async flows, cancellation tokens

**📖 [Coroutines and async patterns →](resources/coroutines-async.md)**

### Performance Optimization

Gameplay code must be performance-conscious.

**Key Principles:**
1. **Cache References** - Get components in Awake/Start, not Update
2. **Avoid Expensive Calls** - No FindObjectOfType, GetComponent, or tag string comparisons in loops
3. **Object Pooling** - Reuse objects instead of Instantiate/Destroy
4. **Update Optimization** - Consider FixedUpdate frequency, use timers instead of frame checks
5. **Lazy Initialization** - Defer expensive operations until needed

**Common Anti-Patterns:**
```csharp
// ❌ DON'T: Expensive calls in Update
void Update()
{
    GameObject player = GameObject.FindWithTag("Player");
    Rigidbody rb = GetComponent<Rigidbody>();
}

// ✅ DO: Cache in Awake/Start
private GameObject player;
private Rigidbody rb;

void Awake()
{
    player = GameObject.FindWithTag("Player");
    rb = GetComponent<Rigidbody>();
}
```

**📖 [Performance patterns →](resources/performance-patterns.md)**

### Testing Gameplay Systems

Unity provides the Unity Test Framework for both unit and integration tests.

**Test Types:**
1. **Edit Mode Tests** - Unit tests for game logic, no Play Mode needed
2. **Play Mode Tests** - Integration tests, full Unity lifecycle
3. **Test Doubles** - Mocks, stubs, and fakes for isolated testing

**Best Practices:**
- Separate logic from MonoBehaviour when possible (easier to test)
- Use interfaces for dependencies (enables mocking)
- Test game logic in Edit Mode, integration in Play Mode
- Use `[UnityTest]` for asynchronous tests with yields

**📖 [Testing patterns →](resources/testing-patterns.md)**

---

## Resource Files

### Detailed Guides

Each topic has a comprehensive resource file with code examples and best practices:

1. **[monobehaviour-lifecycle.md](resources/monobehaviour-lifecycle.md)**
   - Execution order details
   - Awake vs Start patterns
   - Update vs FixedUpdate vs LateUpdate
   - Initialization and cleanup patterns

2. **[design-patterns.md](resources/design-patterns.md)**
   - Service Locator implementation
   - Object Pool system
   - State Machine variants
   - Command pattern for input
   - Observer pattern

3. **[scriptable-objects.md](resources/scriptable-objects.md)**
   - Configuration management
   - ScriptableObject events
   - Runtime sets pattern
   - Data architecture

4. **[performance-patterns.md](resources/performance-patterns.md)**
   - Reference caching strategies
   - Update loop optimization
   - Object pooling implementation
   - Memory management

5. **[event-systems.md](resources/event-systems.md)**
   - UnityEvent patterns
   - C# event implementation
   - ScriptableObject event channels
   - Event system comparisons

6. **[coroutines-async.md](resources/coroutines-async.md)**
   - Coroutine patterns and best practices
   - Async/await in Unity
   - Task vs Coroutine decision matrix
   - Cancellation and error handling

7. **[component-architecture.md](resources/component-architecture.md)**
   - Composition over inheritance
   - Component communication patterns
   - Dependency injection approaches
   - Coupling and cohesion

8. **[testing-patterns.md](resources/testing-patterns.md)**
   - Unity Test Framework setup
   - Edit Mode vs Play Mode tests
   - Test doubles and mocking
   - Testable architecture

---

## Integration with Unity 6.3 LTS

### Unity 6 Improvements

Unity 6.3 LTS includes several enhancements relevant to gameplay programming:

**Performance:**
- Improved job system integration
- Better burst compilation for C# code
- Enhanced ECS (Entity Component System) support

**C# Features:**
- C# 9 language features supported
- Better async/await integration
- Source generators support

**Workflow:**
- Faster enter/exit Play Mode
- Improved script compilation times
- Better profiler integration

**📖 For version-specific details, see the project's UNITY_6_COMPATIBILITY.md**

---

## Common Patterns by Use Case

### Player Controller
- MonoBehaviour lifecycle for input
- State machine for movement states
- Event system for jump/attack feedback
- Performance: cached references

### Enemy AI
- State machine for behavior states
- ScriptableObjects for AI configuration
- Object pooling for projectiles
- Coroutines for decision-making delays

### Inventory System
- ScriptableObjects for item data
- Event system for UI updates
- Service Locator for global access
- Command pattern for undo/redo

### Game Manager
- Singleton or Service Locator pattern
- ScriptableObject events for game flow
- Persistent across scenes
- Initialization order management

---

## Anti-Patterns to Avoid

### Finding Objects Continuously
```csharp
// ❌ DON'T
void Update()
{
    GameObject.Find("Player").transform.position;
    GameObject.FindWithTag("Enemy");
    FindObjectOfType<GameManager>();
}

// ✅ DO
private Transform player;
void Awake()
{
    player = GameObject.FindWithTag("Player").transform;
}
```

### String Comparisons in Loops
```csharp
// ❌ DON'T
if (other.gameObject.tag == "Enemy")

// ✅ DO
if (other.gameObject.CompareTag("Enemy"))
```

### Instantiate/Destroy in Tight Loops
```csharp
// ❌ DON'T
void ShootProjectile()
{
    Instantiate(projectilePrefab);
}

// ✅ DO: Use object pooling
void ShootProjectile()
{
    projectilePool.Get();
}
```

### God Classes
```csharp
// ❌ DON'T: Everything in one MonoBehaviour
public class Player : MonoBehaviour
{
    // 2000 lines of health, movement, combat, inventory, UI, etc.
}

// ✅ DO: Separate components
public class PlayerHealth : MonoBehaviour { }
public class PlayerMovement : MonoBehaviour { }
public class PlayerCombat : MonoBehaviour { }
```

---

## When to Break the Rules

These guidelines are strong recommendations, but Unity development sometimes requires pragmatism:

**GameObject.Find in Awake/Start** - Sometimes OK for one-time initialization of manager references
**Singleton Pattern** - Acceptable for truly global, single-instance systems (but consider Service Locator)
**Inheritance** - Fine for is-a relationships (Enemy types inheriting from BaseEnemy)
**SendMessage** - Useful for optional, loosely-coupled notifications (but slower than direct calls)

The key is understanding the trade-offs and making informed decisions.

---

## Quick Reference

### Lifecycle Execution Order
1. Awake() - All GameObjects
2. OnEnable() - All GameObjects
3. Start() - All GameObjects
4. Update/FixedUpdate/LateUpdate - Every frame/fixed step
5. OnDisable() - When disabled
6. OnDestroy() - When destroyed

### When to Use Each Event System
- **UnityEvents**: UI buttons, simple callbacks, designer control
- **C# Events**: Type-safe, performance-critical, compile-time checking
- **ScriptableObject Events**: Global events, cross-scene, decoupling
- **Interfaces**: Direct communication, type-safe, dependency injection

### Performance Checklist
- [ ] References cached in Awake/Start
- [ ] No Find/GetComponent in Update
- [ ] Tags compared with CompareTag()
- [ ] Object pooling for frequently spawned objects
- [ ] Appropriate Update frequency (consider timers)
- [ ] Profiler checked for bottlenecks

---

## Getting Help

### Resource Files
For detailed information on any topic, consult the resource files:
- Having issues with lifecycle? Read `monobehaviour-lifecycle.md`
- Need a design pattern? Check `design-patterns.md`
- ScriptableObject questions? See `scriptable-objects.md`
- Performance problems? Read `performance-patterns.md`

### Unity Documentation
- [Unity Manual](https://docs.unity3d.com/Manual/index.html)
- [Unity Scripting Reference](https://docs.unity3d.com/ScriptReference/index.html)
- [Unity Best Practices](https://unity.com/how-to/programmer-s-guide-unity)

### Progressive Disclosure
This skill follows the 500-line rule - the main file provides an overview, resource files contain deep dives. Load resource files as needed using the Read tool.

---

**Last Updated:** 2025-01-04
**Unity Version:** 6.3 LTS
**C# Version:** 9.0+
