# Figures

Drawings and diagrams referenced from the docs. Source-controlled as SVG so they diff,
scale, and need no build step.

| File | Shows | Referenced from |
| --- | --- | --- |
| `general-arrangement.svg` | Concept general arrangement — A elevation, B plan, C mechanism sequence, parts list, title block | [README](../../README.md), [ADR 005](../decisions/005-coupled-manipulator-scope.md) |

## Conventions

- **Self-contained and theme-independent.** Each figure paints its own light "paper"
  ground and uses presentation attributes rather than CSS variables, so it renders
  identically on GitHub in light or dark theme, in an `<img>` tag, or opened directly.
- **Colour carries meaning, not decoration.** Oxide red marks motion and torque; teal
  marks sensing; dashed grey marks hidden geometry or an alternative pose. The legend
  appears on any figure using more than one.
- **Every figure has a title block** stating status, scale, and which decision records it
  depends on — so a drawing can never quietly outlive the decision behind it.
- Concept figures are explicitly marked **not to scale**. Once CAD exists, dimensioned
  drawings replace them rather than sitting alongside them.
