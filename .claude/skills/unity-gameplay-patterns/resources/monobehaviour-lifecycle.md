# MonoBehaviour Lifecycle

Complete guide to Unity's MonoBehaviour execution order and lifecycle methods for Unity 6.3 LTS.

---

## Overview

Understanding the MonoBehaviour lifecycle is crucial for writing correct, performant Unity code. Methods execute in a specific order, and knowing when each runs prevents common bugs and performance issues.

---

## Lifecycle Phases

### 1. Initialization Phase

#### Awake()

**When:** Called once when the script instance is being loaded (before Start, before first frame).

**Purpose:** Initialize the object's internal state.

**Best Practices:**
- Initialize variables and get references to components on the same GameObject
- Set up references between scripts
- Runs even if the script component is disabled
- Guaranteed to run before any Start() method

**Example:**
```csharp
public class PlayerHealth : MonoBehaviour
{
    private Rigidbody rb;
    private Animator animator;
    [SerializeField] private int maxHealth = 100;
    private int currentHealth;

    void Awake()
    {
        // Initialize internal state
        currentHealth = maxHealth;

        // Get references to components on THIS GameObject
        rb = GetComponent<Rigidbody>();
        animator = GetComponent<Animator>();

        // Awake runs even if script is disabled!
    }
}
```

**Common Uses:**
- Initialize collections (lists, dictionaries)
- Get component references with GetComponent
- Set default values
- Create singleton instances

---

#### OnEnable()

**When:** Called every time the object is enabled (multiple times if disabled/re-enabled).

**Purpose:** Subscribe to events, register with managers.

**Best Practices:**
- Always pair with OnDisable() for cleanup
- Used for event subscription
- Called after Awake(), before Start()
- Called every time object becomes active

**Example:**
```csharp
public class PlayerInput : MonoBehaviour
{
    private InputActions inputActions;

    void Awake()
    {
        inputActions = new InputActions();
    }

    void OnEnable()
    {
        // Subscribe to events when enabled
        inputActions.Enable();
        inputActions.Player.Jump.performed += OnJump;
        EventManager.OnGamePaused += HandlePause;
    }

    void OnDisable()
    {
        // ALWAYS unsubscribe when disabled
        inputActions.Disable();
        inputActions.Player.Jump.performed -= OnJump;
        EventManager.OnGamePaused -= HandlePause;
    }

    void OnJump(InputAction.CallbackContext context)
    {
        // Handle jump
    }

    void HandlePause(bool isPaused)
    {
        // Handle pause
    }
}
```

**Common Uses:**
- Event subscription
- Re-registering with managers
- Enabling input actions
- Starting coroutines that should restart when re-enabled

---

#### Start()

**When:** Called once before the first frame update, after all Awake() and OnEnable() have completed.

**Purpose:** Initialize after all other objects are initialized.

**Best Practices:**
- Safe to access other objects (their Awake() has completed)
- Find and cache references to other GameObjects
- Only runs if GameObject is active
- Runs before first Update()

**Example:**
```csharp
public class PlayerController : MonoBehaviour
{
    [SerializeField] private Transform groundCheck;
    private Camera mainCamera;
    private GameManager gameManager;

    void Awake()
    {
        // Get references on THIS GameObject
        groundCheck = transform.Find("GroundCheck");
    }

    void Start()
    {
        // Safe to find other objects - their Awake() is done
        mainCamera = Camera.main;
        gameManager = FindObjectOfType<GameManager>();

        // gameManager.Awake() has definitely run by now
        gameManager.RegisterPlayer(this);
    }
}
```

**Awake vs Start Decision Matrix:**
- **Awake**: Self-contained initialization, doesn't depend on other objects
- **Start**: Needs to access other objects, depends on external state
- **Awake**: Component references on same GameObject
- **Start**: References to other GameObjects/managers

---

### 2. Physics Phase

#### FixedUpdate()

**When:** Called on a fixed time interval (default 0.02s = 50 times/second).

**Purpose:** Physics calculations and fixed timestep game logic.

**Best Practices:**
- Use for all physics (Rigidbody) operations
- Frame-rate independent
- May be called multiple times per frame (or zero times)
- Use Time.fixedDeltaTime for timing

**Example:**
```csharp
public class CharacterMovement : MonoBehaviour
{
    [SerializeField] private float moveSpeed = 5f;
    [SerializeField] private float jumpForce = 10f;

    private Rigidbody rb;
    private Vector3 moveDirection;
    private bool jumpRequested;

    void Awake()
    {
        rb = GetComponent<Rigidbody>();
    }

    void Update()
    {
        // Read input in Update (frame-rate dependent is OK for input)
        float h = Input.GetAxis("Horizontal");
        float v = Input.GetAxis("Vertical");
        moveDirection = new Vector3(h, 0, v).normalized;

        if (Input.GetButtonDown("Jump"))
        {
            jumpRequested = true;
        }
    }

    void FixedUpdate()
    {
        // Apply physics in FixedUpdate
        rb.MovePosition(rb.position + moveDirection * moveSpeed * Time.fixedDeltaTime);

        if (jumpRequested)
        {
            rb.AddForce(Vector3.up * jumpForce, ForceMode.Impulse);
            jumpRequested = false;
        }
    }
}
```

**Common Uses:**
- Rigidbody movement and forces
- Physics-based character controllers
- Projectile movement
- Fixed timestep AI decisions

**Important:**
- Never use Input.GetButtonDown() in FixedUpdate - it can miss inputs!
- Store input flags in Update(), consume them in FixedUpdate()

---

### 3. Update Phase

#### Update()

**When:** Called once per frame.

**Purpose:** Frame-rate dependent logic, input handling, state updates.

**Best Practices:**
- Use Time.deltaTime for frame-rate independence
- Handle input here
- Update non-physics game state
- Keep Update() lightweight for performance

**Example:**
```csharp
public class PlayerAnimation : MonoBehaviour
{
    private Animator animator;
    private CharacterMovement movement;
    private Health health;

    // Cached animator parameter IDs for performance
    private int speedHash;
    private int isGroundedHash;

    void Awake()
    {
        animator = GetComponent<Animator>();
        movement = GetComponent<CharacterMovement>();
        health = GetComponent<Health>();

        // Cache parameter hashes (faster than strings)
        speedHash = Animator.StringToHash("Speed");
        isGroundedHash = Animator.StringToHash("IsGrounded");
    }

    void Update()
    {
        // Update animation parameters based on state
        animator.SetFloat(speedHash, movement.CurrentSpeed);
        animator.SetBool(isGroundedHash, movement.IsGrounded);

        // Handle input
        if (Input.GetKeyDown(KeyCode.E))
        {
            Interact();
        }

        // Update cooldown timer
        if (attackCooldown > 0)
        {
            attackCooldown -= Time.deltaTime;
        }
    }

    void Interact()
    {
        // Interaction logic
    }
}
```

**Common Uses:**
- Input reading
- Animation updates
- Timer updates (use Time.deltaTime)
- State machine updates
- Camera following (or use LateUpdate)

---

#### LateUpdate()

**When:** Called once per frame, after all Update() methods.

**Purpose:** Operations that should happen after all Update() logic.

**Best Practices:**
- Camera following (after character Update())
- Procedural animation (IK, look-at)
- Final position adjustments
- Order-dependent updates

**Example:**
```csharp
public class CameraFollow : MonoBehaviour
{
    [SerializeField] private Transform target;
    [SerializeField] private Vector3 offset = new Vector3(0, 2, -5);
    [SerializeField] private float smoothSpeed = 5f;

    void LateUpdate()
    {
        // Follow target AFTER target's Update() has moved it
        Vector3 desiredPosition = target.position + offset;
        Vector3 smoothedPosition = Vector3.Lerp(
            transform.position,
            desiredPosition,
            smoothSpeed * Time.deltaTime
        );
        transform.position = smoothedPosition;

        // Look at target
        transform.LookAt(target);
    }
}
```

**Camera Following Pattern:**
```csharp
// ✅ CORRECT
void LateUpdate()  // Camera script
{
    FollowPlayer();  // Player's Update() already moved them
}

// ❌ WRONG
void Update()  // Camera script
{
    FollowPlayer();  // Player might move AFTER this!
}
```

**Common Uses:**
- Camera scripts (following, orbit, zoom)
- IK (Inverse Kinematics) solvers
- Look-at constraints
- UI positioning based on world objects

---

### 4. Rendering Phase

#### OnWillRenderObject()

**When:** Called before camera renders the object.

**Purpose:** Per-camera rendering setup.

**Example:**
```csharp
void OnWillRenderObject()
{
    // Update material properties before rendering
    material.SetFloat("_CameraDistance",
        Vector3.Distance(Camera.current.transform.position, transform.position));
}
```

#### OnPreRender() / OnPostRender()

**When:** Attached to Camera, before/after camera renders.

**Example:**
```csharp
// On Camera object
void OnPreRender()
{
    // Setup render texture, effects, etc.
}

void OnPostRender()
{
    // Cleanup, restore state
}
```

---

### 5. Cleanup Phase

#### OnDisable()

**When:** Called when object becomes inactive or is destroyed.

**Purpose:** Unsubscribe from events, cleanup.

**Best Practices:**
- Mirror OnEnable() - always unsubscribe events here
- Called before OnDestroy()
- Called when GameObject disabled, not just destroyed

**Example:**
```csharp
public class ScoreDisplay : MonoBehaviour
{
    void OnEnable()
    {
        ScoreManager.OnScoreChanged += UpdateDisplay;
    }

    void OnDisable()
    {
        // Prevent memory leaks - always unsubscribe!
        ScoreManager.OnScoreChanged -= UpdateDisplay;
    }

    void UpdateDisplay(int newScore)
    {
        // Update UI
    }
}
```

**Memory Leak Prevention:**
```csharp
// ❌ WRONG - Memory leak if OnDisable not implemented
void OnEnable()
{
    GameManager.OnGameOver += HandleGameOver;
}
// OnDisable() missing - event reference keeps object alive!

// ✅ CORRECT
void OnEnable()
{
    GameManager.OnGameOver += HandleGameOver;
}

void OnDisable()
{
    GameManager.OnGameOver -= HandleGameOver;
}
```

---

#### OnDestroy()

**When:** Called when object is destroyed.

**Purpose:** Final cleanup, resource release.

**Example:**
```csharp
public class ResourceLoader : MonoBehaviour
{
    private Texture2D loadedTexture;
    private AudioClip loadedAudio;

    void Start()
    {
        loadedTexture = Resources.Load<Texture2D>("Textures/MyTexture");
        loadedAudio = Resources.Load<AudioClip>("Audio/MySound");
    }

    void OnDestroy()
    {
        // Release resources loaded from Resources
        if (loadedTexture != null)
        {
            Resources.UnloadAsset(loadedTexture);
        }

        if (loadedAudio != null)
        {
            Resources.UnloadAsset(loadedAudio);
        }

        // Cleanup other resources
        CloseNetworkConnection();
    }
}
```

**Common Uses:**
- Release manually loaded resources
- Close file handles
- Disconnect network connections
- Final statistics logging

---

## Complete Execution Order

```
GameObject Created
    ↓
Awake()           ← Initialize self
    ↓
OnEnable()        ← Subscribe to events
    ↓
Start()           ← Initialize with other objects
    ↓
╔═══════════ Game Loop ═══════════╗
║                                  ║
║  FixedUpdate()  (0.02s default)  ║
║      ↓                          ║
║  Update()       (every frame)    ║
║      ↓                          ║
║  LateUpdate()   (every frame)    ║
║      ↓                          ║
║  Rendering                       ║
║                                  ║
╚══════════════════════════════════╝
    ↓
OnDisable()       ← Unsubscribe
    ↓
OnDestroy()       ← Final cleanup
    ↓
GameObject Destroyed
```

---

## Common Patterns

### Singleton Pattern

```csharp
public class GameManager : MonoBehaviour
{
    public static GameManager Instance { get; private set; }

    void Awake()
    {
        // Singleton pattern
        if (Instance == null)
        {
            Instance = this;
            DontDestroyOnLoad(gameObject);
        }
        else
        {
            Destroy(gameObject);
            return;
        }

        // Initialize manager
        InitializeGame();
    }
}
```

### Object Pooling

```csharp
public class ProjectilePool : MonoBehaviour
{
    [SerializeField] private GameObject projectilePrefab;
    [SerializeField] private int poolSize = 20;

    private Queue<GameObject> pool;

    void Awake()
    {
        // Create pool in Awake
        pool = new Queue<GameObject>();

        for (int i = 0; i < poolSize; i++)
        {
            GameObject obj = Instantiate(projectilePrefab);
            obj.SetActive(false);
            pool.Enqueue(obj);
        }
    }

    public GameObject GetProjectile()
    {
        if (pool.Count > 0)
        {
            GameObject obj = pool.Dequeue();
            obj.SetActive(true);  // Triggers OnEnable()
            return obj;
        }

        // Pool exhausted, create new
        return Instantiate(projectilePrefab);
    }

    public void ReturnProjectile(GameObject obj)
    {
        obj.SetActive(false);  // Triggers OnDisable()
        pool.Enqueue(obj);
    }
}
```

---

## Anti-Patterns to Avoid

### ❌ Expensive Operations in Update()

```csharp
// DON'T
void Update()
{
    GameObject player = GameObject.Find("Player");
    Rigidbody rb = GetComponent<Rigidbody>();
}

// DO
private GameObject player;
private Rigidbody rb;

void Awake()
{
    player = GameObject.Find("Player");
    rb = GetComponent<Rigidbody>();
}
```

### ❌ Physics in Update()

```csharp
// DON'T
void Update()
{
    rb.AddForce(Vector3.forward * speed);
}

// DO
void FixedUpdate()
{
    rb.AddForce(Vector3.forward * speed);
}
```

### ❌ Forgetting OnDisable()

```csharp
// DON'T - Memory leak!
void OnEnable()
{
    EventBus.OnEvent += Handler;
}
// Missing OnDisable()

// DO
void OnEnable()
{
    EventBus.OnEvent += Handler;
}

void OnDisable()
{
    EventBus.OnEvent -= Handler;
}
```

---

## Decision Trees

### Where to Initialize?

```
Need component on THIS GameObject?
    YES → Awake()

Need component on OTHER GameObject?
    YES → Start()

Need to subscribe to events?
    YES → OnEnable()
```

### Which Update?

```
Is it physics-related?
    YES → FixedUpdate()

Does it need to run after all Updates?
    YES → LateUpdate()

Otherwise → Update()
```

### Cleanup Location?

```
Need to match OnEnable()?
    YES → OnDisable()

Final cleanup only?
    YES → OnDestroy()
```

---

## Performance Tips

1. **Cache GetComponent** - Call once in Awake/Start
2. **Cache Tags** - Use CompareTag() instead of tag comparison
3. **Use Hashes** - Animator.StringToHash() for animator parameters
4. **Minimize Updates** - Use timers instead of every-frame checks
5. **Disable When Inactive** - Disabled scripts don't run Update()

---

## Testing

```csharp
using NUnit.Framework;
using UnityEngine;
using UnityEngine.TestTools;
using System.Collections;

public class LifecycleTests
{
    [UnityTest]
    public IEnumerator Awake_CalledBeforeStart()
    {
        var testObject = new GameObject();
        var component = testObject.AddComponent<LifecycleTracker>();

        yield return null;  // Wait one frame

        Assert.IsTrue(component.AwakeCalled);
        Assert.IsTrue(component.StartCalled);
        Assert.Less(component.AwakeTime, component.StartTime);

        Object.Destroy(testObject);
    }
}
```

---

## Additional Resources

- Unity Manual: [Order of Execution](https://docs.unity3d.com/Manual/ExecutionOrder.html)
- Unity Scripting Reference: [MonoBehaviour](https://docs.unity3d.com/ScriptReference/MonoBehaviour.html)

**Last Updated:** 2025-01-04
