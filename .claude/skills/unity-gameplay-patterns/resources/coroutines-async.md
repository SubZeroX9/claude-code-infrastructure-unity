# Coroutines and Async/Await

Asynchronous programming patterns in Unity.

## Coroutines

Unity's traditional async approach.

```csharp
IEnumerator AttackSequence() {
    yield return new WaitForSeconds(0.5f);
    DealDamage();
    yield return new WaitForSeconds(0.5f);
    // Done
}

void Start() {
    StartCoroutine(AttackSequence());
}
```

## Async/Await

Modern C# async pattern.

```csharp
async Task LoadDataAsync() {
    await Task.Delay(1000);
    // Load data
}
```

## Decision Matrix

- **Coroutines**: Frame-based timing, Unity yields, tied to GameObject
- **Async/Await**: Network calls, file I/O, not tied to lifecycle

Last Updated: 2025-01-04
