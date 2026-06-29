# Review rubric, QA tests, and delivery checklists

Use this when reviewing or critiquing an app, building a test plan, or confirming a design is complete. The main workflow's Step 9 (verify) and the review use cases point here.

## Review rubric

Score each category from 0 to 3.

| Category | 0 | 1 | 2 | 3 |
|---|---|---|---|---|
| Native behaviour | Mostly custom or web-like | Some native controls, many broken behaviours | Mostly native, minor gaps | Native behaviours feel complete |
| Menus/commands | Missing or decorative menus | Basic menus only | Good menus and shortcuts | Full command model with validation/context |
| Keyboard/focus | Mouse-only | Partial shortcuts | Standard navigation works | Power-user keyboard flow is excellent |
| Text handling | Custom/incomplete | Basic editing | Standard text mostly works | Full native text behaviours preserved |
| Selection | Single/awkward | Partial multi-select | Standard selection mostly works | Selection model is complete and predictable |
| Drag/drop | Absent | One-way/basic | Useful common cases | Deep in/out/within app support |
| Copy/paste | Only obvious text | Limited formats | Sensible public formats | Rich multi-format pasteboard integration |
| Windows/documents | iPad/web-like | Basic windows | Good window model | Excellent Mac window/document workflow |
| State/config | Forgets user choices | Minimal settings | Good persistence/settings | Deep, respectful configurability |
| Interoperability | Siloed | Import/export only | Good system integration | Feels like part of the Mac ecosystem |
| Accessibility | Not considered | Labels only | Usable with assistive tech | Fully accessible including custom UI |
| Craft/detail | Merely functional | Some polish | Good polish | Rewards attention with delightful affordances |

Interpretation:

- **0-12**: Not a Mac-arsed Mac app.
- **13-24**: Runs on Mac, but feels generic or incomplete.
- **25-31**: Solid Mac app with fixable gaps.
- **32-36**: Strong Mac-arsed Mac app.

## "I wonder if this works" QA tests

Run these tests manually where relevant. If the environment cannot run macOS, do not claim runtime verification — deliver this as a manual checklist instead.

- Press Command-A/C/X/V/Z in every selectable/editable context.
- Copy selected rows/items and paste into TextEdit or another plain text target.
- Copy rich content and paste into a rich text target and a plain text target.
- Drag files from Finder into the app.
- Drag app content out to Finder or another app.
- Drag between two windows of the app.
- Shift-click and Command-click in every list/table/outline.
- Right-click a multi-selection and verify the command applies to the whole selection.
- Resize sidebars, columns, split views, and windows, then relaunch.
- Change view modes, sort order, and disclosure states, then relaunch.
- Open multiple documents/windows and use the Window menu.
- Use the app without touching the mouse for common workflows.
- Use VoiceOver to navigate the main workflow.
- Try dark mode, high contrast, reduced motion, and different display sizes.
- Try open/save panels and verify default folders make sense.
- Try undo after edits, deletes, moves, and reorders.
- Try app workflows with files stored outside obvious folders.
- Try unusual filenames: spaces, punctuation, emoji, non-Latin scripts, very long names.

## Common anti-patterns

Avoid these unless you have an explicit, user-benefiting reason:

- A web app wrapped in a Mac window with no real Mac menus.
- Custom controls that look nice but break keyboard, selection, text, or accessibility behaviour.
- Ignoring the menu bar.
- Hiding all important actions behind unlabeled icons.
- Using a single-window iPad-style layout for a Mac workflow that benefits from multiple windows.
- No Settings window despite obvious user preferences.
- No drag and drop for file/content workflows.
- No copy/paste representation for selected objects.
- Single selection only where multi-selection would clearly help.
- Context menus that ignore the current selection.
- Save/open panels that always start in an unhelpful folder.
- Forgetting user-chosen window sizes, columns, sidebars, or toolbars.
- Breaking standard shortcuts.
- Replacing native text controls with incomplete custom fields.
- Treating accessibility as optional.
- Treating the Finder and file system as irrelevant.
- Treating Mac as merely another deployment target for an iPad/web app.

## Refactoring guidance for non-Mac-like apps

When adapting an existing web, Electron, Catalyst, or cross-platform app:

1. Keep the product's core model, but redesign the Mac shell.
2. Add a real menu bar and command routing.
3. Replace custom controls with native controls where possible.
4. Implement standard shortcuts and focus behaviour.
5. Add Mac windowing: multiple windows, tabs, inspectors, sheets, or panels as appropriate.
6. Add Settings via Command-Comma.
7. Add drag/drop and pasteboard support around core objects.
8. Add Finder/file/open/save/import/export behaviour.
9. Preserve meaningful user state.
10. Audit accessibility.
11. Run the "I wonder if this works" checklist.
12. Keep iterating until the app's behaviour, not just its styling, feels Mac-like.

## Final delivery checklist

Before calling a Mac app design or implementation complete, ensure:

- The app has a clear Mac identity.
- Native controls are used or native behaviours are reproduced.
- Menus, shortcuts, and command validation are implemented.
- Text fields and selection behave like Mac users expect.
- Copy/paste and drag/drop provide useful public representations.
- Multi-window/document behaviour is intentional.
- Meaningful state is saved and restored.
- Settings and customisation exist where users need them.
- Accessibility is tested.
- Interoperability with Finder and other apps is considered.
- The app contains at least a few thoughtful affordances that reward exploration.
