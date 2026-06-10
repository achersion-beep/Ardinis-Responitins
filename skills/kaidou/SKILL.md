---
name: kaidou
description: >
  Kaidou is a single-file HTML application — a Kanban-style workflow board for
  game design requirements management. This skill should be used when the user
  asks to modify, extend, debug, or add features to the workflow-board.html
  file. It covers the data model, CSS architecture, key functions, modal
  system, gameplay framework panel, design document generation, and known
  pitfalls based on extensive prior development sessions.
agent_created: true
---

# Kaidou — Workflow Board Skill

Single-file HTML Kanban board for managing game design requirements. Built as a
self-contained application with no external dependencies, using localStorage for
persistence.

## Quick Start

The application lives at `assets/workflow-board.html`. When the user asks to
work on it, copy it to their workspace and open it:

```bash
cp assets/workflow-board.html <workspace>/workflow-board.html
```

Then use `preview_url` with the absolute file path.

## Data Model

### Requirements Array

Each requirement is an object:

```js
{
  id: number,          // unique, auto-incremented (nextId starting at 100)
  title: string,
  type: 'feature' | 'bug' | 'optimize' | 'urgent',
  priority: 'high' | 'medium' | 'low',
  assignee: string,    // short identifier (e.g. 'zs', 'ls')
  currentStage: 'backlog' | 'analyzing' | 'designing' | 'developing' | 'testing' | 'done',
  desc: string,        // optional description
  designDoc: {         // optional planning document (added by openDesignDoc)
    overview: string,
    disassembly: string,
    flow: string,
    ui: string,
    data: string,
    tech: string,
    acceptance: string
  }
}
```

### Custom Links Array

```js
{
  id: number,          // unique, auto-incremented (nextLinkId starting at 200)
  from: number,        // source requirement ID
  to: number,          // target requirement ID
  type: 'depends' | 'blocks' | 'related' | 'custom',
  label: string        // optional label
}
```

### Stage Definitions

```js
const stageNames = {
  backlog: '待处理',
  analyzing: '分析中',
  designing: '设计中',
  developing: '开发中',
  testing: '测试中',
  done: '已完成'
};
```

### Type Definitions

```js
const typeNames = {
  feature: '功能',
  bug: '缺陷',
  optimize: '优化',
  urgent: '紧急'
};
```

## CSS Architecture

### CSS Variables (`:root`)

| Variable | Default | Usage |
|----------|---------|-------|
| `--bg` | `#f6f7f9` | Page background |
| `--surface` | `#ffffff` | Card/modal surfaces |
| `--border` | `#e2e5ea` | Borders |
| `--text` | `#1a1a2e` | Primary text |
| `--text-secondary` | `#6b7280` | Secondary text |
| `--text-muted` | `#9ca3af` | Muted/placeholder text |
| `--purple` | `#7C5CBF` | Brand/accent color |
| `--purple-light` | `#f3eefc` | Light accent background |
| `--radius` | `12px` | Standard border radius |
| `--transition` | `0.2s cubic-bezier(0.4,0,0.2,1)` | Standard transition |

### Key Style Patterns

- **Cards**: `.card` with `background: var(--surface)`, `border: 1px solid var(--border)`, `border-radius: 8px`
- **Buttons**: `.btn` base style; `.btn-primary` for purple accent; `.btn-danger` for delete actions
- **Modals**: `.modal-overlay` fixed overlay + `.modal` centered card
- **Columns**: `.column` for Kanban columns, each with `.column-header` and `.column-body`

## Key UI Components

### Board Layout

The board renders 6 columns (one per stage) using `renderBoard()`. Columns are
filterable by type using filter pills (全部/功能/缺陷/优化/紧急).

### Card Display

Each card shows: title, type badge, priority indicator, assignee avatar,
action buttons (edit/delete). Cards with design docs show a green dot
(`.fw-doc-dot`). Cards are draggable between columns.

### Modal System

Four modal types, all using `.modal-overlay` + `.modal` pattern:

| Modal | ID | Open | Close |
|-------|-----|------|-------|
| Add/Edit | `addModal` | `openAddModal()` / `openEditModal(id)` | `closeAddModal()` |
| Link | `linkModal` | `openLinkModal()` | `closeLinkModal()` |
| Link Manager | `linkManagerModal` | `openLinkManager()` | `closeLinkManager()` |
| Design Doc | `docModal` | `openDesignDoc(id)` | `closeDocModal()` |
| Confirm | `confirmModal` | `showConfirm()` | `closeConfirmModal()` |
| Stage Select | `stageSelectModal` | `showStageSelect()` | `closeStageSelect()` |

All modals support click-outside-to-close via `onclick="if(event.target===this)closeXxx()"`.

### Gameplay Framework Panel

A full-screen overlay (`#frameworkOverlay`) that visualizes requirements as an
interactive node graph. Nodes are positioned by category (battle/core/economy/
system/infra) with colored borders and labels.

**White theme CSS**: Background `#ffffff`, colored text per category, no
backdrop-filter. Framework panel CSS is in a separate block starting at
`/* Gameplay Framework Panel — White theme */`.

### Design Document (策划文档)

Opened via `openDesignDoc(id)`. Has 7 editable sections:

1. 概述 (overview)
2. 功能拆卸 (disassembly)
3. 玩法流程 (flow)
4. UI交互 (ui)
5. 数据模型 (data)
6. 技术方案 (tech)
7. 验收标准 (acceptance)

Auto-generated via `autoGenDoc(req)` which analyzes the requirement's title and
description keywords to produce tailored content.

## Function Reference

### Board Rendering

- **`renderAll()`** — Master render: calls `renderBoard()`, updates link stats, updates framework if open
- **`renderBoard()`** — Renders all 6 columns with filtered cards
- **`renderColumn(colKey)`** — Renders a single column's cards

### Card Operations

- **`openAddModal()`** — Opens add/edit modal (empty form)
- **`openEditModal(id)`** — Opens add/edit modal with card data pre-filled
- **`saveCard()`** — Saves new or edited card; auto-generates ID if new
- **`deleteCard(id)`** — Deletes card and its associated links
- **`moveCard(id, newStage)`** — Moves card to a different stage column

### Link Management

- **`toggleLinkMode()`** — Toggles link drawing mode
- **`openLinkModal(fromId, toId)`** — Modal to create a link between two cards
- **`saveLink(fromId, toId, type, label)`** — Saves a new link
- **`deleteLink(id)`** — Deletes a link
- **`openLinkManager()`** — Full link list with edit/delete
- **`exitLinkMode()`** — Exits link mode, cleans up UI

### Data Persistence

- **`saveAll()`** — Saves `requirements` and `customLinks` to localStorage
- **`loadData()`** — Loads from localStorage, with backward compatibility for old formats; defaults to sample data if empty
- **`resetBoard()`** — Restores default sample requirements and links
- **`exportData()`** — Exports as JSON download
- **`importDataPrompt()`** — Opens file picker to import JSON

### Batch Operations

- **`completeAll()`** — Opens stage-select modal for bulk stage update
- **`deleteAll()`** — Shows confirm dialog, then clears all requirements and links
- **`showStageSelect()`** — Renders 6 stage tabs (with emoji icons and current counts), user picks target stage
- **`applyStageToAll()`** — Bulk-applies selected stage to all requirements

### Design Document

- **`openDesignDoc(id)`** — Opens the 7-section design doc modal; auto-fills missing sections from `autoGenDoc()`
- **`saveDesignDoc()`** — Persists all 7 textarea values to `req.designDoc`
- **`autoGenDoc(req)`** — Generates document content from title/description keywords using if/else branching; returns object with all 7 sections. **The `disassembly` section produces 24 sub-modules across 4 layers (核心引擎层/数据配置层/交互表现层/服务集成层), each with 6 items.**
- **`updateDocTitle()`** — Updates the doc title display in the modal

### Framework Panel

- **`openFramework()`** — Opens the gameplay framework overlay, calls `renderFramework()`
- **`renderFramework()`** — Builds the node graph from requirements with customLinks; renders SVG connections with animated dashed lines
- **`closeFramework()`** — Hides the framework overlay
- **`showFwTooltip(card, event)`** — Shows card details on hover in framework

### Utilities

- **`escapeHtml(str)`** — HTML-escapes user input for safe rendering
- **`showToast(msg)`** — Shows a brief toast notification
- **`setFilter(type, el)`** — Filters board by requirement type
- **`showConfirm(msg, onOk, okText)`** — Custom confirm dialog (replaces broken native `confirm()` in sandbox)

## How to Extend

### Adding a New Modal

1. Add HTML: `<div class="modal-overlay" id="xxxModal" style="display:none" onclick="if(event.target===this)closeXxxModal()">`
2. Add JS: `openXxxModal()`, `closeXxxModal()`, and action functions
3. Add button in `.header-actions` to trigger

### Adding a New Stage

1. Add entry to `stageNames` object
2. Add corresponding column in `renderBoard()` (look for the 6-column loop)
3. Add stage tab in `showStageSelect()` (automatically handled via `stageNames` iteration)
4. Update card drag targets

### Modifying Design Doc Layout

1. Change the `sections` array in `openDesignDoc()` to add/remove/reorder sections
2. Update `saveDesignDoc()` to match
3. Update `autoGenDoc()` to generate content for new sections
4. Adjust CSS for new textarea heights if needed

### Changing Theme Colors

Modify CSS variables in `:root`. The framework panel has its own separate color
definitions under `/* Gameplay Framework Panel — White theme */`.

## Known Pitfalls

1. **Never use native `confirm()`** — It is blocked in the WorkBuddy preview
   sandbox. Always use `showConfirm()` custom modal instead.

2. **`autoGenDoc()` must use if/else, NOT nested ternary expressions** —
   Deeply nested ternaries with template literals cause hard-to-debug syntax
   errors. The function was rewritten to use if/else branches after a
   production incident where mismatched parentheses froze the entire page.

3. **SVG connection opacity** — Framework panel connections use `opacity` inline
   (not CSS). When changing background colors, adjust the opacity value in
   `renderFramework()` to maintain visibility. Current values: 0.8 for custom,
   0.35 for auto.

4. **localStorage keys** — `workflow-requirements` and `workflow-links`. Both
   store JSON strings. `loadData()` handles backward compatibility for older
   formats.

5. **Card IDs and link IDs** — Use separate auto-increment counters (`nextId`
   starting at 100, `nextLinkId` starting at 200). When clearing all data,
   reset both counters.

6. **Backward compatibility for designDoc** — Older requirements may lack the
   `disassembly` field. `openDesignDoc()` checks for missing fields and
   auto-generates them from `autoGenDoc()`.

7. **CSS selector specificity** — The framework panel uses class selectors
   (`.fw-node.battle`, `.fw-node.core`, etc.) with high specificity. When
   adding new node types, follow the same pattern to avoid unexpected
   cascading.

## File Delivery

When the user asks to view or share the workflow board, deliver the HTML file
using `deliver_attachments` and call `open_result_view` for the result panel.
Also use `preview_url` for in-app preview.
