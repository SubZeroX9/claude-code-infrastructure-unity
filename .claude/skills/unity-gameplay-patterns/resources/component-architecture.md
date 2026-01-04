# Component Architecture

Unity component-based architecture patterns including Assembly Definitions and namespaces.

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
namespace MyGame.Combat
{
    public interface IDamageable {
        void TakeDamage(int damage);
    }

    public class Health : MonoBehaviour, IDamageable {
        public void TakeDamage(int damage) {
            // Handle damage
        }
    }
}

namespace MyGame.Weapons
{
    using MyGame.Combat;

    public class Weapon : MonoBehaviour {
        void Hit(Collider other) {
            var damageable = other.GetComponent<IDamageable>();
            damageable?.TakeDamage(10);
        }
    }
}
```

## Namespaces Organization

**Best Practices:**
- Use company/project prefix: `MyCompany.ProjectName.Feature`
- Group related functionality
- Mirror folder structure

**Example Structure:**
```csharp
namespace MyGame.Core { }           // Core systems
namespace MyGame.Player { }         // Player-specific
namespace MyGame.Enemies { }        // Enemy AI
namespace MyGame.UI { }             // UI systems
namespace MyGame.Utilities { }      // Helper classes
```

## Assembly Definitions (.asmdef)

Assembly Definitions organize code into separate assemblies for faster compilation.

**Benefits:**
- Faster iteration (only recompile changed assemblies)
- Explicit dependencies
- Better code organization
- Reduced compile times

**Example: GameplayAssembly.asmdef**
```json
{
    "name": "MyGame.Gameplay",
    "rootNamespace": "MyGame.Gameplay",
    "references": [
        "MyGame.Core"
    ],
    "includePlatforms": [],
    "excludePlatforms": [],
    "allowUnsafeCode": false
}
```

**Recommended Structure:**
```
Assets/
├── _Project/
│   ├── Core/
│   │   └── Core.asmdef           (MyGame.Core)
│   ├── Gameplay/
│   │   └── Gameplay.asmdef       (MyGame.Gameplay - depends on Core)
│   ├── UI/
│   │   └── UI.asmdef             (MyGame.UI - depends on Core)
│   └── Editor/
│       └── Editor.asmdef         (MyGame.Editor - Editor only)
```

**Tips:**
- Create assembly per major system
- Core assembly for shared code
- Separate editor assemblies
- Use `rootNamespace` to enforce namespaces

Last Updated: 2025-01-04
