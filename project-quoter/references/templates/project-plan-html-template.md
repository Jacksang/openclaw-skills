# Project Plan HTML Template

Self-contained HTML page for client engineering review. No external dependencies.

## Key Sections

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>{PROJECT} Platform — Project Plan & Effort Estimates</title>
<style>
  /* CSS variables for phase colors */
  :root {
    --p1: #2563eb; --p1bg: #dbeafe;
    --p2: #7c3aed; --p2bg: #ede9fe;
    --p3: #059669; --p3bg: #d1fae5;
    --bg: #f8fafc; --card: #fff; --text: #1e293b; --muted: #64748b;
  }
  /* ... full CSS from ELY PROJECT_PLAN.html reference ... */
</style>
</head>
<body>
<div class="container">

  <!-- Header -->
  <div class="header">
    <h1>🚀 {PROJECT} Platform — Project Plan</h1>
    <div class="sub">{One-line description}</div>
    <div class="date">Generated: {DATE} | Source: E01–E{NN} module plans</div>
  </div>

  <!-- Summary Cards: 4 cards in a row -->
  <div class="summary">
    <div class="summary-card">
      <div class="number">{N Phases}</div>
      <div class="label">Delivery Phases</div>
    </div>
    <!-- Repeat for: Modules, Total Hours, Calendar Weeks -->
  </div>

  <!-- Timeline: embed PNG — do NOT use Mermaid -->
  <img src="assets/timeline-milestones.png?v=1" alt="Project timeline and milestones" style="width:100%;max-width:900px">

  <!-- Gantt: embed PNG -->
  <img src="assets/phase-gantt.png?v=1" alt="Phase Gantt chart" style="width:100%;max-width:900px">

  <!-- Per-Phase Module Tables -->
  <!-- Phase badge + title + subtitle -->
  <!-- Table: Module ID | Description | Complexity | Dev hrs | Test hrs | Calendar Days | Dependencies -->
  <!-- Phase total row with bold totals -->

  <!-- Gantt Chart -->
  <!-- Replaced by phase-gantt.png above — regenerate PNGs when timeline changes -->

  <!-- Team Recommendation Table -->
  <!-- Role | Phase 1 | Phase 2 | Phase 3 headcount -->

  <div class="footer">
    {PROJECT} Platform · Project Plan v1.0 · Generated from E01–E{NN} module plans
  </div>

</div>
</body>
</html>
```

## Design Rules

- **Self-contained:** All CSS inline in `<style>`, no external fonts or CDN.
- **Color coding:** Blue (P1), Purple (P2), Green (P3). Consistent across all HTML outputs.
- **Complexity badges:** High = red, Medium = amber, Low = green.
- **Responsive:** Use CSS Grid with media queries for mobile.
- **Phase total rows:** Must visually stand out with background color and bold text.
- **Dependency tags:** Small colored pills showing module dependencies.

## CSS Gotchas

- **Total row background conflict:** Global CSS rules like `.total-row td { background: #f8fafc }` can override inline styles. Apply backgrounds directly to `td` elements in the total row.
- **Light-on-light text:** Always verify text contrast against its actual rendered background. Use pure white `#ffffff` on colored backgrounds.
- **Note callouts:** Pull qualification notes out of bullet lists into separate bordered boxes with left-border accent color and distinct background.
