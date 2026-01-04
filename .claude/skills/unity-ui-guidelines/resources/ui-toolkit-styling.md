# UI Toolkit Styling

USS (Unity Style Sheets) guide.

## Flexbox Layout

```css
.container {
    flex-direction: row;
    justify-content: center;
    align-items: center;
    flex-wrap: wrap;
}
```

## Responsive Design

```css
@media (max-width: 768px) {
    .container {
        flex-direction: column;
    }
}
```

## Common Patterns

```css
.button {
    background-color: blue;
    transition-duration: 0.2s;
}

.button:hover {
    background-color: lightblue;
    scale: 1.1;
}
```

Last Updated: 2025-01-04
