# UI Toolkit Data Binding

Reactive UI patterns.

## Observable Properties

```csharp
private int score;
public int Score {
    get => score;
    set {
        score = value;
        UpdateScoreUI();
    }
}

void UpdateScoreUI() {
    scoreLabel.text = $"Score: {score}";
}
```

## MVVM Pattern

Separate data (Model), display (View), and logic (ViewModel).

Last Updated: 2025-01-04
