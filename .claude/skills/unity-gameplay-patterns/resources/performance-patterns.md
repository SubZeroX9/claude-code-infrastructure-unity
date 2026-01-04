# Performance Patterns

Unity performance optimization patterns for gameplay code.

## Key Principles

1. **Cache References** - GetComponent in Awake/Start, not Update
2. **Object Pooling** - Reuse instead of Instantiate/Destroy  
3. **Update Optimization** - Use timers, not every-frame checks
4. **Avoid Allocations** - Minimize GC pressure

## Examples

### Cache References
```csharp
// ❌ DON'T
void Update() {
    GetComponent<Rigidbody>().AddForce(Vector3.forward);
}

// ✅ DO
private Rigidbody rb;
void Awake() { rb = GetComponent<Rigidbody>(); }
void FixedUpdate() { rb.AddForce(Vector3.forward); }
```

### Use CompareTag
```csharp
// ❌ Slower
if (other.tag == "Player")

// ✅ Faster
if (other.CompareTag("Player"))
```

### Timer Instead of Every Frame
```csharp
private float nextCheck;
void Update() {
    if (Time.time >= nextCheck) {
        ExpensiveOperation();
        nextCheck = Time.time + 1f;  // Check every second
    }
}
```

Last Updated: 2025-01-04
