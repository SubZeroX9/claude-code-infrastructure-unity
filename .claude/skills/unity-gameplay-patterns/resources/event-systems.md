# Event Systems

Guide to event-driven architecture in Unity.

## Event Types

1. **Action** - Simple, fast, code-only
2. **C# Events** - Encapsulated, type-safe
3. **UnityEvents** - Inspector-configurable
4. **ScriptableObject Events** - Global, decoupled

## When to Use Each

- **Action**: Code-only, simple events
- **Event**: Need encapsulation
- **UnityEvent**: Designer configuration needed
- **SO Events**: Cross-scene communication

## Example: ScriptableObject Event Channel

```csharp
[CreateAssetMenu(menuName = "Events/Int Event")]
public class IntEvent : ScriptableObject {
    private event Action<int> onRaised;
    
    public void Raise(int value) => onRaised?.Invoke(value);
    public void AddListener(Action<int> listener) => onRaised += listener;
    public void RemoveListener(Action<int> listener) => onRaised -= listener;
}
```

Last Updated: 2025-01-04
