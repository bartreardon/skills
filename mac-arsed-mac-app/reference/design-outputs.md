# Design outputs to produce

When asked to design or plan a Mac app (as opposed to reviewing one), produce these sections. They correspond to Steps 1–9 of the main workflow.

## 1. Mac identity statement

Explain what makes this app a Mac app:

- App category.
- Primary Mac workflows.
- Why the window/document model fits macOS.
- Which Mac conventions it will embrace.
- Where it intentionally departs from convention and why.

## 2. Affordance map

Provide a table for major UI elements covering native controls, menus, keyboard, selection, pasteboard, drag/drop, state, and accessibility. Build one for each major screen, panel, list, editor, item, and command.

| Element | Native control/API | Selection behaviour | Keyboard support | Copy/paste | Drag/drop | Context menu | State to save | Accessibility role |
|---|---|---|---|---|---|---|---|---|
| Example list of documents | NSTableView / SwiftUI Table where sufficient | Single and multi-select, shift range, command toggle | Arrows, return, delete, command-a where appropriate | Plain text names, file URLs if applicable | Reorder, drag out file URLs, accept file drops if useful | Acts on full selection | Column widths, sort order, sidebar width | Table/list with labelled rows |

Every non-trivial UI should have an affordance map before implementation.

## 3. Command/menu plan

List menus, commands, shortcuts, validation rules, toolbar placement, and context menu placement. Every important action should be reachable from the menu bar or an appropriate contextual menu — not only from an unlabeled icon or hidden button.

## 4. Window/document plan

Describe windows, tabs, panels, sheets, document handling, restoration, and multi-window behaviour. Decide what should be a separate document window, a tab, a secondary window, an inspector/utility panel, a sheet, a popover, a sidebar/split view, or a transient dialog.

## 5. Interoperability plan

Describe supported file types, pasteboard types, drag/drop types, import/export, Finder integration, automation, and sharing.

## 6. Settings and state plan

Describe settings, defaults, persisted layout, per-document state, and reset behaviour.

## 7. Accessibility plan

Describe keyboard access, VoiceOver structure, labels, contrast, motion, custom controls, and testing.

## 8. QA checklist

Provide manual tests for Mac behaviour, including the "I wonder if this works" tests in `reference/review-and-qa.md`.
