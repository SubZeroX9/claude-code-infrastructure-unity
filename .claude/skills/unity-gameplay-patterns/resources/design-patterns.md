# Unity Design Patterns

Common design patterns specifically adapted for Unity game development with C# examples.

---

## Overview

Design patterns are reusable solutions to common problems. This guide covers patterns most relevant to Unity development with practical implementations.

---

## 1. Service Locator Pattern

### Purpose
Provide global access to services without coupling code to specific implementations.

### When to Use
- Need global access to managers (AudioManager, ScoreManager, etc.)
- Want to decouple systems from each other
- Alternative to Singleton when multiple implementations possible

### Implementation

```csharp
// Service Locator
public static class ServiceLocator
{
    private static Dictionary<Type, object> services = new Dictionary<Type, object>();

    public static void RegisterService<T>(T service)
    {
        var type = typeof(T);

        if (services.ContainsKey(type))
        {
            Debug.LogWarning($"Service of type {type} already registered");
            return;
        }

        services[type] = service;
    }

    public static T GetService<T>()
    {
        var type = typeof(T);

        if (!services.ContainsKey(type))
        {
            throw new Exception($"Service of type {type} not registered");
        }

        return (T)services[type];
    }

    public static void UnregisterService<T>()
    {
        var type = typeof(T);
        services.Remove(type);
    }

    public static void Clear()
    {
        services.Clear();
    }
}

// Service Interface
public interface IAudioService
{
    void PlaySound(string soundName);
    void PlayMusic(string musicName);
    void SetVolume(float volume);
}

// Service Implementation
public class AudioManager : MonoBehaviour, IAudioService
{
    [SerializeField] private AudioSource musicSource;
    [SerializeField] private AudioSource sfxSource;

    void Awake()
    {
        ServiceLocator.RegisterService<IAudioService>(this);
    }

    void OnDestroy()
    {
        ServiceLocator.UnregisterService<IAudioService>();
    }

    public void PlaySound(string soundName)
    {
        // Play sound effect
        AudioClip clip = Resources.Load<AudioClip>($"Audio/SFX/{soundName}");
        sfxSource.PlayOneShot(clip);
    }

    public void PlayMusic(string musicName)
    {
        // Play background music
        AudioClip clip = Resources.Load<AudioClip>($"Audio/Music/{musicName}");
        musicSource.clip = clip;
        musicSource.Play();
    }

    public void SetVolume(float volume)
    {
        musicSource.volume = volume;
        sfxSource.volume = volume;
    }
}

// Usage
public class Player : MonoBehaviour
{
    void OnCollisionEnter(Collision collision)
    {
        if (collision.gameObject.CompareTag("Coin"))
        {
            // Get audio service and play sound
            var audioService = ServiceLocator.GetService<IAudioService>();
            audioService.PlaySound("CoinCollect");
        }
    }
}
```

### Advantages
- Decoupled from specific implementations
- Easy to mock for testing
- Clear service dependencies
- Flexible service swapping

### Disadvantages
- Global state (careful with testing)
- Hidden dependencies
- Runtime errors if service not registered

---

## 2. Object Pool Pattern

### Purpose
Reuse objects instead of creating/destroying them, improving performance.

### When to Use
- Frequently spawned objects (projectiles, particles, enemies)
- Performance-critical situations
- Objects with expensive Instantiate/Destroy costs

### Implementation

```csharp
// Generic Object Pool
public class ObjectPool<T> where T : Component
{
    private T prefab;
    private Queue<T> objects = new Queue<T>();
    private Transform parent;

    public ObjectPool(T prefab, int initialSize = 10, Transform parent = null)
    {
        this.prefab = prefab;
        this.parent = parent;

        // Pre-populate pool
        for (int i = 0; i < initialSize; i++)
        {
            T obj = GameObject.Instantiate(prefab, parent);
            obj.gameObject.SetActive(false);
            objects.Enqueue(obj);
        }
    }

    public T Get()
    {
        T obj;

        if (objects.Count > 0)
        {
            obj = objects.Dequeue();
        }
        else
        {
            // Pool exhausted, create new object
            obj = GameObject.Instantiate(prefab, parent);
        }

        obj.gameObject.SetActive(true);
        return obj;
    }

    public void Return(T obj)
    {
        obj.gameObject.SetActive(false);
        objects.Enqueue(obj);
    }

    public void Clear()
    {
        while (objects.Count > 0)
        {
            T obj = objects.Dequeue();
            GameObject.Destroy(obj.gameObject);
        }
    }
}

// Pooled Projectile Example
public class Projectile : MonoBehaviour
{
    [SerializeField] private float lifetime = 3f;
    [SerializeField] private float speed = 10f;

    private float spawnTime;
    private ObjectPool<Projectile> pool;

    public void Initialize(ObjectPool<Projectile> pool)
    {
        this.pool = pool;
    }

    void OnEnable()
    {
        spawnTime = Time.time;
    }

    void Update()
    {
        // Move forward
        transform.position += transform.forward * speed * Time.deltaTime;

        // Return to pool after lifetime
        if (Time.time - spawnTime > lifetime)
        {
            pool.Return(this);
        }
    }

    void OnCollisionEnter(Collision collision)
    {
        // Hit something, return to pool
        pool.Return(this);
    }
}

// Weapon using Object Pool
public class Weapon : MonoBehaviour
{
    [SerializeField] private Projectile projectilePrefab;
    [SerializeField] private Transform firePoint;
    [SerializeField] private int poolSize = 20;

    private ObjectPool<Projectile> projectilePool;

    void Awake()
    {
        // Create pool
        projectilePool = new ObjectPool<Projectile>(projectilePrefab, poolSize);
    }

    public void Fire()
    {
        Projectile projectile = projectilePool.Get();
        projectile.transform.position = firePoint.position;
        projectile.transform.rotation = firePoint.rotation;
        projectile.Initialize(projectilePool);
    }
}
```

### Unity's Built-in Pool (Unity 2021+)

```csharp
using UnityEngine.Pool;

public class ModernWeapon : MonoBehaviour
{
    [SerializeField] private Projectile projectilePrefab;
    [SerializeField] private int defaultCapacity = 20;
    [SerializeField] private int maxSize = 100;

    private ObjectPool<Projectile> projectilePool;

    void Awake()
    {
        projectilePool = new ObjectPool<Projectile>(
            createFunc: () => Instantiate(projectilePrefab),
            actionOnGet: (proj) => proj.gameObject.SetActive(true),
            actionOnRelease: (proj) => proj.gameObject.SetActive(false),
            actionOnDestroy: (proj) => Destroy(proj.gameObject),
            collectionCheck: true,
            defaultCapacity: defaultCapacity,
            maxSize: maxSize
        );
    }

    public void Fire()
    {
        Projectile proj = projectilePool.Get();
        proj.transform.position = firePoint.position;
        proj.Initialize(projectilePool);
    }
}
```

---

## 3. State Machine Pattern

### Purpose
Manage object states and transitions cleanly.

### When to Use
- Character states (idle, walking, jumping, attacking)
- AI behavior (patrol, chase, attack, flee)
- Game states (menu, playing, paused, game over)
- UI flow

### Implementation - Simple Enum State Machine

```csharp
public class PlayerController : MonoBehaviour
{
    public enum PlayerState
    {
        Idle,
        Walking,
        Running,
        Jumping,
        Falling,
        Attacking
    }

    private PlayerState currentState = PlayerState.Idle;
    private Animator animator;
    private Rigidbody rb;

    // Cached animator parameter IDs
    private int stateHash = Animator.StringToHash("State");

    void Awake()
    {
        animator = GetComponent<Animator>();
        rb = GetComponent<Rigidbody>();
    }

    void Update()
    {
        UpdateState();
        ExecuteState();
    }

    void UpdateState()
    {
        switch (currentState)
        {
            case PlayerState.Idle:
                if (Input.GetAxisRaw("Horizontal") != 0)
                    ChangeState(PlayerState.Walking);
                if (Input.GetButtonDown("Jump"))
                    ChangeState(PlayerState.Jumping);
                break;

            case PlayerState.Walking:
                if (Input.GetButton("Run"))
                    ChangeState(PlayerState.Running);
                if (Input.GetAxisRaw("Horizontal") == 0)
                    ChangeState(PlayerState.Idle);
                if (Input.GetButtonDown("Jump"))
                    ChangeState(PlayerState.Jumping);
                break;

            case PlayerState.Jumping:
                if (rb.velocity.y < 0)
                    ChangeState(PlayerState.Falling);
                break;

            case PlayerState.Falling:
                if (IsGrounded())
                    ChangeState(PlayerState.Idle);
                break;
        }
    }

    void ExecuteState()
    {
        switch (currentState)
        {
            case PlayerState.Idle:
                // Idle behavior
                break;

            case PlayerState.Walking:
                Move(5f);
                break;

            case PlayerState.Running:
                Move(10f);
                break;

            case PlayerState.Jumping:
                // Jumping handled in OnJump()
                break;
        }
    }

    void ChangeState(PlayerState newState)
    {
        if (currentState == newState) return;

        // Exit current state
        OnStateExit(currentState);

        currentState = newState;

        // Enter new state
        OnStateEnter(newState);

        // Update animator
        animator.SetInteger(stateHash, (int)newState);
    }

    void OnStateEnter(PlayerState state)
    {
        switch (state)
        {
            case PlayerState.Jumping:
                rb.AddForce(Vector3.up * 10f, ForceMode.Impulse);
                break;
        }
    }

    void OnStateExit(PlayerState state)
    {
        // Cleanup when leaving state
    }

    void Move(float speed)
    {
        float h = Input.GetAxis("Horizontal");
        rb.velocity = new Vector3(h * speed, rb.velocity.y, 0);
    }

    bool IsGrounded()
    {
        return Physics.Raycast(transform.position, Vector3.down, 1.1f);
    }
}
```

### Implementation - Class-based State Pattern

```csharp
// Base State
public abstract class State
{
    public abstract void Enter();
    public abstract void Execute();
    public abstract void Exit();
}

// Concrete States
public class IdleState : State
{
    private PlayerController player;

    public IdleState(PlayerController player)
    {
        this.player = player;
    }

    public override void Enter()
    {
        player.Animator.Play("Idle");
    }

    public override void Execute()
    {
        if (player.InputMagnitude > 0.1f)
        {
            player.StateMachine.ChangeState(new WalkState(player));
        }
    }

    public override void Exit()
    {
        // Cleanup
    }
}

public class WalkState : State
{
    private PlayerController player;

    public WalkState(PlayerController player)
    {
        this.player = player;
    }

    public override void Enter()
    {
        player.Animator.Play("Walk");
    }

    public override void Execute()
    {
        player.Move(player.WalkSpeed);

        if (player.InputMagnitude < 0.1f)
        {
            player.StateMachine.ChangeState(new IdleState(player));
        }
    }

    public override void Exit()
    {
        // Cleanup
    }
}

// State Machine
public class StateMachine
{
    private State currentState;

    public void ChangeState(State newState)
    {
        currentState?.Exit();
        currentState = newState;
        currentState.Enter();
    }

    public void Update()
    {
        currentState?.Execute();
    }
}

// Player Controller
public class PlayerController : MonoBehaviour
{
    public StateMachine StateMachine { get; private set; }
    public Animator Animator { get; private set; }
    public float InputMagnitude { get; private set; }
    public float WalkSpeed = 5f;

    void Awake()
    {
        Animator = GetComponent<Animator>();
        StateMachine = new StateMachine();
    }

    void Start()
    {
        StateMachine.ChangeState(new IdleState(this));
    }

    void Update()
    {
        InputMagnitude = new Vector2(
            Input.GetAxis("Horizontal"),
            Input.GetAxis("Vertical")
        ).magnitude;

        StateMachine.Update();
    }

    public void Move(float speed)
    {
        // Movement logic
    }
}
```

---

## 4. Command Pattern

### Purpose
Encapsulate requests as objects, enabling undo/redo, queuing, and logging.

### When to Use
- Undo/redo systems
- Input buffering
- Replay systems
- Macro recording

### Implementation

```csharp
// Command Interface
public interface ICommand
{
    void Execute();
    void Undo();
}

// Concrete Commands
public class MoveCommand : ICommand
{
    private Transform transform;
    private Vector3 displacement;

    public MoveCommand(Transform transform, Vector3 displacement)
    {
        this.transform = transform;
        this.displacement = displacement;
    }

    public void Execute()
    {
        transform.position += displacement;
    }

    public void Undo()
    {
        transform.position -= displacement;
    }
}

public class RotateCommand : ICommand
{
    private Transform transform;
    private Quaternion rotation;
    private Quaternion previousRotation;

    public RotateCommand(Transform transform, Quaternion rotation)
    {
        this.transform = transform;
        this.rotation = rotation;
    }

    public void Execute()
    {
        previousRotation = transform.rotation;
        transform.rotation = rotation;
    }

    public void Undo()
    {
        transform.rotation = previousRotation;
    }
}

// Command Manager
public class CommandManager : MonoBehaviour
{
    private Stack<ICommand> commandHistory = new Stack<ICommand>();
    private Stack<ICommand> redoStack = new Stack<ICommand>();

    public void ExecuteCommand(ICommand command)
    {
        command.Execute();
        commandHistory.Push(command);
        redoStack.Clear(); // Clear redo stack on new command
    }

    public void Undo()
    {
        if (commandHistory.Count > 0)
        {
            ICommand command = commandHistory.Pop();
            command.Undo();
            redoStack.Push(command);
        }
    }

    public void Redo()
    {
        if (redoStack.Count > 0)
        {
            ICommand command = redoStack.Pop();
            command.Execute();
            commandHistory.Push(command);
        }
    }
}

// Usage
public class Player : MonoBehaviour
{
    private CommandManager commandManager;

    void Awake()
    {
        commandManager = FindObjectOfType<CommandManager>();
    }

    void Update()
    {
        if (Input.GetKeyDown(KeyCode.W))
        {
            ICommand move = new MoveCommand(transform, Vector3.forward);
            commandManager.ExecuteCommand(move);
        }

        if (Input.GetKeyDown(KeyCode.Z))
        {
            commandManager.Undo();
        }

        if (Input.GetKeyDown(KeyCode.Y))
        {
            commandManager.Redo();
        }
    }
}
```

---

## 5. Observer Pattern (Events)

### Purpose
Notify multiple objects when an event occurs without tight coupling.

### Option 1: Actions (Recommended for Simple Cases)

Actions are C# built-in delegate types that are perfect for simple event scenarios.

```csharp
using System;

// Event Publisher using Action
public class Health : MonoBehaviour
{
    // Action<T> for events with parameters
    public static Action<int> OnHealthChanged;
    public static Action<GameObject> OnDeath;

    // Action (no parameters) for simple events
    public static Action OnCriticalHealth;

    [SerializeField] private int maxHealth = 100;
    private int currentHealth;
    private bool isCritical = false;

    void Awake()
    {
        currentHealth = maxHealth;
    }

    public void TakeDamage(int damage)
    {
        currentHealth -= damage;
        currentHealth = Mathf.Max(0, currentHealth);

        // Invoke action (null-safe with ?.)
        OnHealthChanged?.Invoke(currentHealth);

        // Check for critical health (below 25%)
        if (!isCritical && currentHealth < maxHealth * 0.25f)
        {
            isCritical = true;
            OnCriticalHealth?.Invoke();
        }

        if (currentHealth <= 0)
        {
            OnDeath?.Invoke(gameObject);
        }
    }

    public void Heal(int amount)
    {
        currentHealth += amount;
        currentHealth = Mathf.Min(maxHealth, currentHealth);

        if (currentHealth >= maxHealth * 0.25f)
        {
            isCritical = false;
        }

        OnHealthChanged?.Invoke(currentHealth);
    }
}

// Subscriber 1: UI
public class HealthUI : MonoBehaviour
{
    [SerializeField] private Slider healthBar;
    [SerializeField] private Image healthBarFill;
    [SerializeField] private Color normalColor = Color.green;
    [SerializeField] private Color criticalColor = Color.red;

    void OnEnable()
    {
        Health.OnHealthChanged += UpdateHealthBar;
        Health.OnCriticalHealth += ShowCriticalWarning;
    }

    void OnDisable()
    {
        Health.OnHealthChanged -= UpdateHealthBar;
        Health.OnCriticalHealth -= ShowCriticalWarning;
    }

    void UpdateHealthBar(int currentHealth)
    {
        healthBar.value = currentHealth;
    }

    void ShowCriticalWarning()
    {
        healthBarFill.color = criticalColor;
        // Play warning sound, flash UI, etc.
    }
}

// Subscriber 2: Achievement System
public class AchievementTracker : MonoBehaviour
{
    void OnEnable()
    {
        Health.OnDeath += CheckSurvivalAchievement;
    }

    void OnDisable()
    {
        Health.OnDeath -= CheckSurvivalAchievement;
    }

    void CheckSurvivalAchievement(GameObject deadEntity)
    {
        if (deadEntity.CompareTag("Enemy"))
        {
            Debug.Log("Enemy defeated!");
            // Award achievement points
        }
    }
}
```

**Advantages of Actions:**
- Built-in C# delegates (no custom delegate needed)
- Clean, simple syntax
- Type-safe
- Good performance

**Disadvantages:**
- Static events can cause memory leaks if not unsubscribed
- No Inspector visibility
- Limited to 16 type parameters (Action<T1, T2, ..., T16>)

---

### Option 2: Custom Events (Recommended for Encapsulation)

Custom events provide better encapsulation and prevent external code from invoking your events.

```csharp
using System;

// Event Publisher with custom events
public class Player : MonoBehaviour
{
    // Custom event - can only be invoked within this class
    public event Action<int> OnHealthChanged;
    public event Action<Vector3> OnPositionChanged;
    public event Action OnDeath;

    [SerializeField] private int maxHealth = 100;
    private int currentHealth;
    private Vector3 lastPosition;

    void Awake()
    {
        currentHealth = maxHealth;
        lastPosition = transform.position;
    }

    void Update()
    {
        // Notify position changes
        if (transform.position != lastPosition)
        {
            lastPosition = transform.position;
            OnPositionChanged?.Invoke(transform.position);
        }
    }

    public void TakeDamage(int damage)
    {
        currentHealth -= damage;
        currentHealth = Mathf.Max(0, currentHealth);

        OnHealthChanged?.Invoke(currentHealth);

        if (currentHealth <= 0)
        {
            OnDeath?.Invoke();
            Die();
        }
    }

    void Die()
    {
        // Death logic
        gameObject.SetActive(false);
    }
}

// Subscriber
public class PlayerTracker : MonoBehaviour
{
    [SerializeField] private Player player;

    void OnEnable()
    {
        // Subscribe to events
        player.OnHealthChanged += LogHealthChange;
        player.OnPositionChanged += UpdateMinimap;
        player.OnDeath += HandlePlayerDeath;
    }

    void OnDisable()
    {
        // CRITICAL: Always unsubscribe!
        player.OnHealthChanged -= LogHealthChange;
        player.OnPositionChanged -= UpdateMinimap;
        player.OnDeath -= HandlePlayerDeath;
    }

    void LogHealthChange(int newHealth)
    {
        Debug.Log($"Player health: {newHealth}");
    }

    void UpdateMinimap(Vector3 position)
    {
        // Update minimap marker
    }

    void HandlePlayerDeath()
    {
        Debug.Log("Player has died!");
    }
}
```

**Advantages of Custom Events:**
- Cannot be invoked externally (encapsulation)
- Prevent accidental event triggering
- Clear ownership
- Same performance as Actions

**Disadvantages:**
- Requires `event` keyword
- Still no Inspector visibility

---

### Option 3: UnityEvents (Recommended for Inspector Configuration)

UnityEvents allow you to configure event listeners directly in the Inspector, perfect for designer workflows.

```csharp
using UnityEngine;
using UnityEngine.Events;

// Custom UnityEvent types for parameters
[System.Serializable]
public class IntEvent : UnityEvent<int> { }

[System.Serializable]
public class Vector3Event : UnityEvent<Vector3> { }

// Event Publisher with UnityEvents
public class GameManager : MonoBehaviour
{
    [Header("Events")]
    public IntEvent OnScoreChanged;
    public UnityEvent OnGameStart;
    public UnityEvent OnGameOver;
    public UnityEvent OnHighScore;

    private int score;
    private int highScore;
    private bool gameActive;

    void Awake()
    {
        // Initialize UnityEvents (prevents null reference)
        if (OnScoreChanged == null) OnScoreChanged = new IntEvent();
        if (OnGameStart == null) OnGameStart = new UnityEvent();
        if (OnGameOver == null) OnGameOver = new UnityEvent();
        if (OnHighScore == null) OnHighScore = new UnityEvent();
    }

    public void StartGame()
    {
        score = 0;
        gameActive = true;
        OnGameStart?.Invoke();
    }

    public void AddScore(int points)
    {
        if (!gameActive) return;

        score += points;
        OnScoreChanged?.Invoke(score);

        if (score > highScore)
        {
            highScore = score;
            OnHighScore?.Invoke();
        }
    }

    public void EndGame()
    {
        gameActive = false;
        OnGameOver?.Invoke();
    }
}

// Subscribers can be connected via Inspector or code
public class ScoreDisplay : MonoBehaviour
{
    [SerializeField] private GameManager gameManager;
    [SerializeField] private TMPro.TextMeshProUGUI scoreText;

    void Start()
    {
        // Subscribe via code
        gameManager.OnScoreChanged.AddListener(UpdateScoreDisplay);
        gameManager.OnGameStart.AddListener(ResetDisplay);
    }

    void UpdateScoreDisplay(int newScore)
    {
        scoreText.text = $"Score: {newScore}";
    }

    void ResetDisplay()
    {
        scoreText.text = "Score: 0";
    }
}

// This component's methods can be connected in Inspector!
public class GameOverScreen : MonoBehaviour
{
    [SerializeField] private GameObject gameOverPanel;

    public void ShowGameOver()  // Connected to OnGameOver in Inspector
    {
        gameOverPanel.SetActive(true);
    }

    public void ShowHighScoreEffect()  // Connected to OnHighScore in Inspector
    {
        // Play particle effect, sound, etc.
    }
}
```

**Advantages of UnityEvents:**
- Inspector-configurable (designer-friendly)
- Serialized (persists in scenes/prefabs)
- No code needed for simple connections
- Visual debugging

**Disadvantages:**
- Slower than Actions/Events (uses reflection)
- More memory overhead
- Limited type safety in Inspector

---

### Comparison Matrix

| Feature | Action | Event | UnityEvent |
|---------|--------|-------|------------|
| **Type Safety** | ✅ Compile-time | ✅ Compile-time | ⚠️ Inspector is not type-safe |
| **Performance** | ✅ Fast | ✅ Fast | ⚠️ Slower (reflection) |
| **Encapsulation** | ❌ Can be invoked externally | ✅ Only class can invoke | ⚠️ Can be invoked via Inspector |
| **Inspector** | ❌ Not visible | ❌ Not visible | ✅ Fully configurable |
| **Memory** | ✅ Lightweight | ✅ Lightweight | ⚠️ Heavier |
| **Best For** | Code-only simple events | Code-only with encapsulation | Designer workflows, prototyping |

---

### When to Use Each

**Use Action when:**
- Simple events with parameters
- Code-only connections
- Performance is critical
- You need quick prototyping

**Use Event when:**
- Need encapsulation (prevent external invoke)
- Building public APIs
- Want clear ownership
- Code-only connections

**Use UnityEvent when:**
- Designers need to configure listeners
- Prototyping gameplay
- UI button callbacks
- Inspector visualization needed
- Performance is not critical

---

### Combining Approaches

You can use multiple event types in the same class:

```csharp
public class Weapon : MonoBehaviour
{
    // Public UnityEvent for Inspector configuration
    public UnityEvent OnShoot;

    // Private event for code-only subscriptions (performance-critical)
    public event Action<int> OnAmmoChanged;

    private int ammo = 30;

    public void Shoot()
    {
        if (ammo > 0)
        {
            ammo--;

            // Invoke both events
            OnShoot?.Invoke();  // UnityEvent (UI feedback, sound, etc.)
            OnAmmoChanged?.Invoke(ammo);  // Fast event (UI update)
        }
    }
}
```

---

## Pattern Selection Guide

| Pattern | Use When | Avoid When |
|---------|----------|------------|
| **Service Locator** | Need global service access | Services have complex lifecycles |
| **Object Pool** | Frequently spawned objects | Objects rarely created |
| **State Machine** | Complex state transitions | Only 2-3 simple states |
| **Command** | Need undo/redo | Simple one-time actions |
| **Observer (Action)** | Code-only, simple events | Need Inspector config |
| **Observer (Event)** | Need encapsulation | Need Inspector config |
| **Observer (UnityEvent)** | Designer configuration | Performance-critical |

---

## Unity-Specific Considerations

### Serialization
```csharp
// ✅ Unity can serialize these patterns
[System.Serializable]
public class PoolSettings
{
    public GameObject prefab;
    public int initialSize = 10;
}

// ❌ Can't serialize interfaces/abstract classes/Actions/Events
public abstract class State { } // Won't show in Inspector
public Action OnEvent; // Won't show in Inspector
public event Action OnEvent2; // Won't show in Inspector

// ✅ UnityEvents ARE serializable
public UnityEvent OnEvent3; // Shows in Inspector!
```

### Performance
- **Object Pooling**: 10-100x faster than Instantiate/Destroy
- **State Machines**: Minimal overhead (switch statements are fast)
- **Service Locator**: Dictionary lookup (fast, but not free)
- **Command Pattern**: Small allocation per command
- **Action/Event**: Direct delegate invocation (very fast)
- **UnityEvent**: Reflection-based (slower, but usually acceptable)

### Memory Leaks Prevention

```csharp
// ❌ WRONG - Memory leak!
public class BadSubscriber : MonoBehaviour
{
    void Start()
    {
        SomeManager.OnEvent += Handler;
        // Never unsubscribes - object won't be garbage collected!
    }

    void Handler() { }
}

// ✅ CORRECT - Always unsubscribe in OnDisable/OnDestroy
public class GoodSubscriber : MonoBehaviour
{
    void OnEnable()
    {
        SomeManager.OnEvent += Handler;
    }

    void OnDisable()
    {
        SomeManager.OnEvent -= Handler;  // Prevents memory leak
    }

    void Handler() { }
}
```

---

## Additional Resources

- [Game Programming Patterns](http://gameprogrammingpatterns.com/)
- [Refactoring Guru - Design Patterns](https://refactoring.guru/design-patterns)
- Unity Unite talks on architecture
- [C# Events and Delegates](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/events/)

**Last Updated:** 2025-01-04
