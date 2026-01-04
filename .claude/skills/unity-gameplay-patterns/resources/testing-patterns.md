# Testing Patterns

Unity Test Framework patterns for gameplay code.

## Test Types

1. **Edit Mode** - Unit tests, no Play Mode needed
2. **Play Mode** - Integration tests, full lifecycle
3. **Test Doubles** - Mocks, stubs, fakes

## Example: Edit Mode Test

```csharp
using NUnit.Framework;

public class HealthTests {
    [Test]
    public void TakeDamage_ReducesHealth() {
        var health = new HealthSystem(100);
        health.TakeDamage(30);
        Assert.AreEqual(70, health.CurrentHealth);
    }
}
```

## Example: Play Mode Test

```csharp
using UnityEngine.TestTools;
using System.Collections;

public class PlayerTests {
    [UnityTest]
    public IEnumerator Jump_IncreasesVerticalVelocity() {
        var player = new GameObject().AddComponent<Player>();
        player.Jump();
        
        yield return new WaitForFixedUpdate();
        
        Assert.Greater(player.Velocity.y, 0);
    }
}
```

## Best Practices

- Separate logic from MonoBehaviour (easier to test)
- Use interfaces for dependencies
- Test logic in Edit Mode, integration in Play Mode

Last Updated: 2025-01-04
