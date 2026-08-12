---
max_turns: 30
timeout_seconds: 900
allowed_tools: [Read, Glob, Grep, Skill]
---

The last column of my table rows shows a short status word — QUEUED, RUNNING, FAILED, CANCELLED. The long ones wrap onto two lines and make those rows taller than the rest, which looks broken.

```css
.row {
  display: grid;
  grid-template-columns: 2fr 1fr 1fr auto;
  gap: 12px;
  align-items: center;
}
.row .status {
  font: 500 13px/1.2 "Inter", system-ui, sans-serif;
  text-transform: uppercase;
  letter-spacing: 0.04em;
  padding: 4px 10px;
  border-radius: 999px;
}
```

`.row` is used by every table in the app. Write me a short implementation plan for the fix, including the exact column width to use, so I can hand it to someone else to carry out.
