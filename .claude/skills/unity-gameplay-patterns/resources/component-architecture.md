# Component Architecture

Unity component-based architecture patterns.

## Composition Over Inheritance

```csharp
// ✅ Good: Small, focused components
public class Health : MonoBehaviour { }
public class Movement : MonoBehaviour { }
public class Combat : MonoBehaviour { }

// ❌ Avoid: God class
public class Player : MonoBehaviour {
    // 1000 lines of everything
}
```

## Communication Patterns

1. **Direct Reference** - Simple, tightly coupled
2. **GetComponent** - Flexible, same GameObject
3. **Events** - Decoupled, one-to-many
4. **Interfaces** - Polymorphic, testable

## Example: Interface-based Communication

```csharp
public interface IDamageable {
    void TakeDamage(int damage);
}

public class Health : MonoBehaviour, IDamageable {
    public void TakeDamage(int damage) {
        // Handle damage
    }
}

public class Weapon : MonoBehaviour {
    void Hit(Collider other) {
        var damageable = other.GetComponent<IDamageable>();
        damageable?.TakeDamage(10);
    }
}
```

Last Updated: 2025-01-04
