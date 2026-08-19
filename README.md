# Task Management App

![HTML](https://img.shields.io/badge/HTML5-E34F26?style=flat&logo=html5&logoColor=white)
![CSS](https://img.shields.io/badge/CSS3-1572B6?style=flat&logo=css3&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=flat&logo=javascript&logoColor=black)
![ES6 Modules](https://img.shields.io/badge/ES6-Modules-orange?style=flat)

A single-page to-do list built with vanilla JavaScript ES6 modules. Tasks can be added and deleted, the list persists across page refreshes using `localStorage`, and an empty-state view is shown automatically when there are no tasks.

Note: the checkbox, filter tabs, and "clear completed" controls exist in the markup and styling but are not yet functional — see Known Issues below.

---

## Features

- Add new tasks via an input field and submit button
- Tasks persist across page refreshes using `localStorage`
- Remove individual tasks with a delete button
- Empty-state view (icon + message) shown automatically when the list is cleared
- Responsive layout for mobile and desktop
- CSS design token system for consistent spacing, colors, and typography

---

## How it works — step by step

**Step 1 — Page load**
`tasks` is imported from `data/task.js` (starts as an empty array). On load, `localStorage.getItem('punchlist_tasks')` is checked first. If it returns data, that becomes the working `tasks` array; if it's empty, the imported `initialTasks` is used instead and immediately saved back to `localStorage`.

**Step 2 — render()**
If `tasks.length === 0`, a pre-built `emptyHtml` string is injected into `.js-task-list` and rendering stops there. Otherwise, `tasks.forEach()` builds an HTML string per task containing a checkbox button, the task label, and a delete button with `data-task-id`. The full string is injected via `innerHTML` in one DOM write.

**Step 3 — attachDeleteListeners()**
Called at the end of every `render()`, since `innerHTML` replaces the DOM and wipes out any previously attached listeners. It queries all `.task-item__delete` buttons and attaches a fresh click listener to each one.

**Step 4 — Add task**
Clicking the submit button calls `e.preventDefault()`, trims the input value, and — if non-empty — pushes a new task object (`{ id: Date.now(), text }`) onto the `tasks` array, clears the input, calls `saveToStorage()`, then `render()`.

**Step 5 — Delete task**
Clicking a delete button walks up to the parent `.task-item` via `.closest()`, reads its `data-task-id`, finds the matching task with `.findIndex()`, and removes it with `.splice()`. `saveToStorage()` and `render()` are called again to sync the UI.

**Step 6 — saveToStorage()**
Serializes the current `tasks` array with `JSON.stringify()` and writes it to `localStorage` under the key `punchlist_tasks`, so the list survives a page refresh.

---

## Folder structure

```
Task-management-app/
├── index.html         # Single page — header, add-task form, filters, task list
├── style.css           # Design token system + all component styles
├── script.js           # Render logic, task state, event listeners, localStorage
└── data/
    └── task.js         # Initial task seed array (empty by default)
```

---

## Design system

The CSS uses a token system defined via `:root` variables for colors, typography, spacing, and shadows, keeping styling consistent across components.

| Token | Use |
|---|---|
| `--color-accent` | Buttons, checkboxes, focus rings |
| `--color-danger` | Delete hover state |
| `--color-bg` | Page background |
| `--color-panel` | Card surface |
| `--font-display` | Headings |
| `--font-body` | Body text, labels, buttons |

---

## Run locally

```bash
git clone <your-repo-url>
cd Task-management-app
# Open index.html via Live Server (required for ES6 modules)
```

ES6 `import` doesn't work over `file://`. Use VS Code Live Server or any local dev server.

---

## Known issues / things to improve

- Checkbox click doesn't do anything yet — there's no `.is-completed` toggle logic or `completed` field on the task object, even though the CSS already supports a checked/struck-through state
- The All / Active / Completed filter buttons in `index.html` have no click listeners or filtering logic behind them — every task always shows regardless of which tab is active
- The footer's `data-tasks-remaining` count is hardcoded as `"1 item left"` in the HTML and never recalculated by JS — should use `tasks.filter()` to reflect the real count
- "Clear completed" button has no event listener wired up
- `data/task.js` ships with an empty `tasks` array, so the app starts blank on first load with no sample data to reference
- Only a click listener is attached to the submit button — no `submit` listener on `.task-form` itself, which is a less resilient pattern than handling the form's native submit event
