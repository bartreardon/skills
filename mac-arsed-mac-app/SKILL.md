---
name: mac-arsed-mac-app
description: Use this skill when an agent is asked to create, refactor, critique, or plan a macOS app and the desired result should be a “Mac-arsed Mac app” rather than a generic desktop, web, Electron, iPad, or cross-platform app merely packaged for macOS.
license: Complete terms in LICENSE.txt
---

# Mac-Arsed Mac App Skill

## Purpose

Create macOS apps that feel like they belong on the Mac. A Mac-arsed Mac app is not just an app that runs on macOS or uses a few native-looking controls. It is an app that embraces macOS conventions, behaves like other good Mac apps, rewards user attention, respects power users, integrates with the system, and contains the small affordances that make users think: “I hoped that would work, and it did.”

Use this skill when designing, implementing, reviewing, or refactoring:

- Native macOS apps.
- Mac versions of iOS, iPadOS, web, Electron, or cross-platform apps.
- Document-based tools, productivity apps, utilities, creative tools, editors, menu bar apps, and professional Mac software.
- UI/UX specifications, SwiftUI/AppKit architecture, interaction models, QA checklists, or code review feedback for macOS software.

## Core definition

A Mac-arsed Mac app is:

- **Platform-specific**: It does not pretend the Mac is just a bigger iPad, a web browser, or a generic desktop shell.
- **Behaviourally native**: It follows native control behaviours, keyboard behaviours, menu behaviours, pasteboard behaviours, accessibility behaviours, window behaviours, and document behaviours.
- **Culturally Mac-like**: It participates in the evolving culture of Mac software rather than copying another platform wholesale.
- **User-respecting**: It saves meaningful state, supports user configuration, preserves flow, and does not make users repeat avoidable work.
- **Interoperable**: It collaborates with the Finder, Terminal, pasteboard, drag and drop, file formats, Services, Shortcuts/automation where appropriate, and other apps.
- **Deep but approachable**: It starts simple, then progressively discloses power without hiding it forever.
- **Polished in the small**: It implements details that many users may never consciously notice, but some users will rely on every day.

## Guiding philosophy

### 1. Behaviour beats appearance

A Mac app can look polished and still feel wrong if its controls do not behave like Mac controls. Prefer real native controls. When you cannot use them, reproduce native behaviours completely, including edge cases, keyboard handling, focus, selection, accessibility, contextual menus, drag and drop, undo, and pasteboard formats.

Do not stop at “it looks close.” Ask: “Would a long-time Mac user’s muscle memory work here?”

### 2. The Mac has strong but quiet opinions

macOS has conventions about menus, windows, keyboard shortcuts, text editing, selection, drag and drop, files, settings, and system integration. These conventions are not always loud, but users feel when an app ignores them.

When the app must depart from convention, do so intentionally and only when the result is better for the user in that specific context.

### 3. If a reasonable Mac user wonders whether something works, it should probably work

Mac apps should reward exploration. If users can see, select, drag, copy, resize, reorder, disclose, or open something, think through what they will expect to happen.

Examples of “should this work?” questions:

- Can I drag this item somewhere useful?
- Can I copy this selected thing and paste a sensible representation elsewhere?
- Can I select multiple items and act on them together?
- Can I drag a file, image, URL, or proxy icon into this app?
- Can I use the same shortcut here that works in other Mac apps?
- Can I resize or rearrange this UI and have the app remember it?
- Can I open more than one window when that would help?

If the answer is “a Mac user would reasonably expect it,” implement it or document why it cannot be supported.

### 4. The Mac rewards attention

Good Mac apps work for new users, but also contain depth for users who invest time. Support keyboard navigation, alternate commands, drag and drop, Services, contextual menus, configurable toolbars, saved state, scriptability or automation where appropriate, and power-user workflows.

### 5. Culture evolves; do not build a museum piece

Classic Mac, NeXT, UNIX, the web, iOS, and iPadOS have all influenced modern macOS. Do not reject an idea merely because it came from another platform. Also do not adopt an outside convention if it replaces a better Mac convention.

Use current macOS conventions as the default. Older Mac conventions are useful for understanding the culture, but the app should feel current.

## Required workflow for the agent

Follow this workflow whenever creating or reviewing a Mac app.

### Step 1: Classify the app

Identify the app’s Mac shape before designing UI.

Determine whether it is primarily:

- A **document-based app**: text editor, image editor, audio editor, writing tool, CAD/design app, project file editor.
- A **shoebox/library app**: notes, bookmarks, RSS, mail, photos, tasks, database-like collection.
- A **utility**: menu bar tool, preference-style tool, system helper, converter, sync tool.
- A **professional workspace**: code editor, creative suite, DAW, modelling tool, IDE.
- A **viewer/browser**: file viewer, feed reader, reference browser, media browser.
- A **hybrid**: combines files, library, cloud sync, and editing.

Then identify the app’s core objects:

- What can be opened?
- What can be saved?
- What can be selected?
- What can be dragged?
- What can be copied?
- What can be imported/exported?
- What can appear in multiple windows?
- What state should persist?
- What should be undoable?

### Step 2: Choose the native substrate

Use the least-custom technology that can deliver the correct Mac behaviour.

Preferred options:

- **AppKit** for deeply Mac-specific apps, complex document/window models, custom controls, pro apps, inspector panels, drag and drop, precise menus, and mature desktop behaviours.
- **SwiftUI** for modern declarative UI where it supports the required Mac behaviours well. Bridge to AppKit when SwiftUI does not provide mature Mac behaviour.
- **NSDocument / document architecture** for document-based apps unless there is a strong reason not to.
- **Native text, table, outline, collection, split view, toolbar, menu, panel, open/save, and accessibility APIs** wherever possible.

Acceptable only with extra scrutiny:

- **Catalyst or shared iPad code**: acceptable only when the Mac version receives Mac-specific windows, menus, keyboard shortcuts, toolbars, settings, drag and drop, pointer, file, accessibility, and pasteboard behaviour.
- **Electron/web/custom UI**: acceptable only if native behaviour, accessibility, menus, keyboard conventions, pasteboard, drag and drop, windowing, and system integration are intentionally implemented. A web app in a window is not enough.

### Step 3: Design the Mac affordance map

For each major screen, panel, list, editor, item, and command, create an affordance map.

Use this structure:

| Element | Native control/API | Selection behaviour | Keyboard support | Copy/paste | Drag/drop | Context menu | State to save | Accessibility role |
|---|---|---|---|---|---|---|---|---|
| Example list of documents | NSTableView / SwiftUI Table where sufficient | Single and multi-select, shift range, command toggle | Arrows, return, delete, command-a where appropriate | Plain text names, file URLs if applicable | Reorder, drag out file URLs, accept file drops if useful | Acts on full selection | Column widths, sort order, sidebar width | Table/list with labelled rows |

Every non-trivial UI should have an affordance map before implementation.

### Step 4: Build the command model first

A Mac app’s menus and keyboard shortcuts are not afterthoughts. Before polishing screens, define:

- App menu items.
- File menu items.
- Edit menu items.
- View menu items.
- Window menu items.
- Help menu items.
- Domain-specific menus where justified.
- Toolbar commands.
- Context menu commands.
- Keyboard shortcuts.
- Undo/redo actions.
- Command availability rules.

Every important action should be reachable from the menu bar or an appropriate contextual menu, not only from an unlabeled icon or hidden button.

### Step 5: Design windows and documents intentionally

Do not be afraid of windows. macOS is a windowing environment.

Decide what should be:

- A separate document window.
- A tab within a window.
- A secondary window.
- An inspector or utility panel.
- A sheet attached to a window.
- A popover.
- A sidebar or split view.
- A transient dialog.

For document-based apps, strongly consider:

- One window per document by default.
- Standard open/save panels.
- Autosave and versions where appropriate.
- Window title reflecting the document.
- Document proxy icon behaviour where available.
- Sensible default save locations based on user intent.
- Multiple windows or tabs if users may compare, edit, or reference multiple documents.

Avoid forcing everything into a single iPad-like window when multiple windows would better serve Mac users.

### Step 6: Design pasteboard and drag/drop behaviour

For each selected object, define pasteboard representations.

Use multiple representations when possible:

- Plain text.
- Rich text.
- File URL.
- Web URL.
- Image formats.
- App-specific structured data.
- Public UTTypes for interoperability.

Implement drag and drop:

- Into the app.
- Out of the app.
- Within the app.
- Between windows of the app.
- Between the app and Finder or other common apps.

If users can see a thing and it represents a file, image, URL, text, row, card, object, or document, ask whether it should be draggable or copyable.

### Step 7: Define state preservation and configuration

Mac apps should remember meaningful user choices.

Persist, where appropriate:

- Window size and position.
- Sidebar widths.
- Split view positions.
- Toolbar customisation.
- Sort order.
- Column visibility and widths.
- Disclosure states.
- Last selected mode or view.
- Recent locations.
- Per-document view state.
- User preferences.
- Inspector/palette placement.

Do not preserve meaningless transient positions, such as a warning dialog dragged aside temporarily.

### Step 8: Add progressive disclosure

Start with good defaults, then reveal depth as needed.

Use:

- Disclosure groups/triangles.
- Optional advanced sections.
- Configurable toolbars.
- Contextual menus.
- Option-key alternate commands where appropriate.
- Tooltips/help tags.
- Settings with clear defaults.
- Inspectors for advanced metadata or object properties.
- Sensible empty states that teach without becoming onboarding clutter.

Do not remove advanced capabilities merely to keep the first screen simple.

### Step 9: Verify with a Mac behaviour test plan

For every release or major feature, test:

- Menus and shortcuts.
- Text editing behaviours.
- Selection behaviours.
- Drag and drop.
- Copy/paste into multiple target apps.
- Undo/redo.
- Window restoration.
- Settings persistence.
- Accessibility with keyboard and VoiceOver.
- Light/dark/high contrast appearances.
- Multiple displays and different window sizes.
- Finder integration.
- Open/save panels.
- File import/export.
- App relaunch and document reopen.

If the environment cannot run macOS, do not claim runtime verification. Provide a manual macOS QA checklist instead.

## Detailed rules

### Native controls and behaviours

Prefer native controls because native controls carry decades of behaviours that are difficult to reimplement.

Use standard controls for:

- Text fields and text views.
- Buttons.
- Checkboxes and radio buttons.
- Pop-up buttons and menus.
- Sliders and steppers.
- Tables, outlines, browsers, and collection views.
- Split views and sidebars.
- Toolbars.
- Search fields.
- Open/save panels.
- Colour, font, print, and sharing panels.
- Alerts, sheets, and popovers.

When custom controls are unavoidable, match native behaviours:

- Focus ring and keyboard focus.
- Full keyboard access.
- VoiceOver role, label, value, help, and actions.
- Pointer and cursor changes.
- Disabled/enabled states.
- Hit targets.
- Context menu support.
- Copy/paste if selectable.
- Drag/drop if movable or exportable.
- Undo/redo where state changes.
- Standard selection semantics.
- Appearance changes for dark mode, high contrast, vibrancy, and active/inactive windows.

### Menus

Implement a real Mac menu bar.

Include standard menus where applicable:

- App menu: About, Settings, Services, Hide, Hide Others, Show All, Quit.
- File: New, Open, Open Recent, Close, Save, Save As/Duplicate/Export/Import/Page Setup/Print as appropriate.
- Edit: Undo, Redo, Cut, Copy, Paste, Paste and Match Style, Delete, Select All, Find, Spelling and Grammar, Substitutions, Transformations, Speech where appropriate.
- View: view modes, sidebar, toolbar, inspector, zoom, sort/group options.
- Window: Minimise, Zoom, Bring All to Front, window list, tabs where appropriate.
- Help: searchable help, documentation, release notes, support.

Rules:

- Use standard names and shortcuts unless there is a strong reason not to.
- Validate menu items based on current selection and focus.
- Do not make the menu bar decorative or empty.
- Do not put every command only inside custom web-style chrome.
- Keep menu item labels clear and sentence/title case according to platform convention.
- Use icons in menus only when they clarify; do not decorate every item unnecessarily.

### Keyboard and focus

Keyboard support is central to Mac feel.

Implement:

- Standard shortcuts: Command-N/O/S/W/Q/Z/X/C/V/A/F/P/Comma and variants where appropriate.
- Arrow-key navigation in lists, tables, grids, sidebars, and menus.
- Return/Enter default action where appropriate.
- Escape to cancel or dismiss where appropriate.
- Delete/backspace actions for selected items where appropriate.
- Tab/Shift-Tab focus movement.
- Type-to-select in lists where appropriate.
- Search focus shortcuts where appropriate.
- Window cycling and tab commands where appropriate.
- Keyboard equivalents for toolbar-only actions.

Do not trap common shortcuts for surprising actions.

### Text editing

Text controls are sacred because users have deep muscle memory.

Use native text controls whenever possible. They should support:

- Command-A/C/X/V/Z.
- Option-arrow and Command-arrow movement.
- Shift-selection combinations.
- Standard word and line selection.
- Double-click word selection and triple-click paragraph/line selection where applicable.
- Emacs-style keybindings such as Control-A and Control-E where the system provides them.
- Spell checking, substitutions, smart quotes/dashes, grammar, and text replacements where appropriate.
- Services and contextual text actions.
- Drag-selection behaviours, including scrolling while selecting outside the visible region.
- Correct handling of Unicode, emoji, composed characters, bidirectional text, and input methods.

Do not replace native text fields with custom fields unless you can match this behaviour.

### Selection

Use standard Mac selection semantics.

For selectable collections:

- Click selects.
- Shift-click extends contiguous ranges.
- Command-click toggles non-contiguous items.
- Command-A selects all when sensible.
- Escape clears or cancels when sensible.
- Right-click/context-click on a selection acts on the full selection, not just the clicked item.
- Dragging a selection drags the full selection.
- Delete acts on the selected set when appropriate.
- Selection is visibly distinct from keyboard focus.
- Empty selection, single selection, and multi-selection states are handled intentionally.

If multiple selection would save repetitive work, support it.

### Drag and drop

Drag and drop is a core Mac behaviour.

Support, when meaningful:

- Dropping files from Finder.
- Dropping images, text, URLs, folders, or app-specific objects.
- Dragging content out to Finder or other apps.
- Reordering within lists or outlines.
- Dragging between windows.
- Dragging proxy/file representations where relevant.
- Spring-loaded navigation behaviour where provided by the system.
- Modifier-key variations such as move/copy/alias where standard and appropriate.
- File promises for generated files where appropriate.

When dragging paths or shell-relevant text to terminal-like contexts, escape or represent the data correctly.

### Pasteboard and copy/paste

Copy and paste should work beyond obvious text editors.

For any selected object, ask what users might want if they press Command-C. Provide sensible pasteboard contents.

Examples:

- Selected files: file URLs and names.
- Selected rows: tabular plain text and structured data.
- Selected rich content: rich text plus plain text fallback.
- Selected image: image data plus file promise if generated.
- Selected URL-like object: URL plus title text.
- Selected app-specific object: app-specific type plus public fallback.

On paste, accept common formats when useful, not only your private format.

### Windows, panels, tabs, and sheets

Use the right container for the task.

Guidelines:

- Use document windows for independent documents.
- Use tabs when grouping related documents helps, but allow separate windows where useful.
- Use sheets for modal actions tied to a specific window.
- Use alerts for brief decisions, not complex workflows.
- Use inspectors/panels for persistent secondary controls or metadata.
- Use popovers for lightweight contextual controls.
- Use sidebars for navigation or persistent structure.
- Allow advanced users to arrange workspaces when the app is complex enough.
- Remember meaningful window and panel placement.

Avoid:

- Global modal dialogs for document-specific tasks.
- Forcing all documents into one giant window without a reason.
- Losing user window layouts on relaunch.
- Using custom window chrome that breaks standard traffic-light controls, title bars, full screen, tabs, or accessibility.

### Document and file behaviour

For document-based apps:

- Use standard open/save flows.
- Support drag opening from Finder and Dock where appropriate.
- Register document types and UTTypes correctly.
- Provide Quick Look thumbnails/previews where valuable.
- Use autosave, versions, duplicate, revert, and export where appropriate.
- Preserve the relationship between imported source files and exported output when that matches user intent.
- Choose sensible default folders in open/save panels based on workflow, not arbitrary global defaults.
- Support recent documents.
- Do not hide files from users unless the app is explicitly library/database-oriented.

For library/shoebox apps:

- Still support import, export, drag out, copy, share, backup, and migration where sensible.
- Make ownership and location of user data understandable.

### State preservation

Respect the time users spend arranging the app.

Save state that reflects user intention:

- Window frame and screen where appropriate.
- Sidebar visibility and width.
- Inspector visibility and placement.
- Toolbar configuration.
- View mode.
- Sort/group/filter settings.
- Search scopes where useful.
- Column order, width, and visibility.
- Disclosure states when useful rather than noisy.
- Last-used folders if they reflect workflow.
- Per-document view settings.

Do not save accidental state:

- Temporary alert positions.
- Error dialog locations.
- Half-completed transient UI unless restoration helps.
- State that would surprise users after relaunch.

### Configurability

Mac apps should have settings. They should also be configurable beyond a settings window when the UI itself invites adjustment.

Support, as appropriate:

- Settings window via Command-Comma.
- Sensible defaults.
- Configurable toolbar.
- Reorderable sidebar sections/items.
- Adjustable split views.
- Hide/show optional panels.
- Per-feature preferences for repeated workflows.
- Import/export of settings for pro tools where useful.
- Reset-to-default paths where customisation can become confusing.

Avoid the false simplicity of “no settings” when users clearly need control.

### Progressive disclosure

Make the common case obvious and the advanced case reachable.

Use:

- Disclosure controls.
- Advanced sections.
- Inspectors.
- Contextual menus.
- Toolbar customisation.
- Option-key alternates.
- Searchable settings/help.
- Inline validation and concise help.

Do not overload new users with every option at once. Do not deny power users access to important controls.

### Interoperability

A Mac app should collaborate with the rest of the system.

Consider supporting:

- Standard file formats for import/export.
- Plain text fallbacks.
- Rich text where relevant.
- Common image, audio, video, archive, or data formats relevant to the domain.
- Finder drag/drop.
- Quick Look.
- Spotlight metadata.
- Share sheet.
- Services.
- Shortcuts/App Intents.
- AppleScript or scripting dictionaries for pro/productivity tools where valuable.
- URL schemes or universal links where useful.
- Printing and PDF export where relevant.
- Open With and document type registration.
- Handoff/Continuity/iCloud only when they serve real workflows.

Avoid private silos unless the product category requires one.

### Accessibility

Accessibility is not optional and is part of Mac feel.

Implement and test:

- VoiceOver labels, roles, values, and actions.
- Full keyboard access.
- Focus order.
- Sufficient contrast.
- Dark mode.
- High contrast/increase contrast.
- Reduce motion.
- Reduced transparency where applicable.
- Dynamic text sizing where applicable to the app category.
- Hit targets and pointer affordances.
- Error messages that are announced and understandable.
- Custom controls exposed as real accessibility elements.

Custom UI that cannot be navigated or understood by assistive technologies is not Mac-arsed.

### Undo and reversible actions

Mac users expect undo to work broadly.

Support undo/redo for:

- Text editing.
- Object edits.
- Reordering.
- Deletions.
- Formatting changes.
- Document-level changes.
- Batch operations where practical.

Use meaningful undo action names, such as “Undo Rename,” “Undo Delete,” or “Undo Move.”

For destructive actions:

- Prefer undo over confirmation when safe.
- Use confirmation only when undo is impossible or consequences are severe.
- Make recovery paths visible.

### Performance and responsiveness

A polished Mac app feels direct.

- Keep typing, selection, scrolling, resizing, menus, and drag operations responsive.
- Do not block the main thread for network, disk, indexing, or rendering work.
- Show progress for long operations.
- Support cancellation where appropriate.
- Preserve user work during crashes or relaunch.
- Avoid web-app latency in basic desktop interactions.

### Visual design

Visual design should support Mac behaviour, not replace it.

- Use system spacing, materials, typography, and control sizes where possible.
- Respect active/inactive window states.
- Respect light/dark modes.
- Avoid excessive custom chrome.
- Avoid novelty controls where standard controls would be clearer.
- Use animation sparingly and purposefully.
- Make resizing layouts robust.
- Do not rely only on colour to convey meaning.

### Current convention check

Before finalising design, compare against:

- Current macOS Human Interface Guidelines.
- Current Apple apps in the same category.
- Respected third-party Mac apps in the same category.
- Older Mac conventions only when they clarify why a current behaviour matters.

When sources disagree, prefer the behaviour that best preserves user expectations, accessibility, interoperability, and workflow efficiency on current macOS.

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

## Design outputs the agent should produce

When asked to design or plan a Mac app, produce these sections.

### 1. Mac identity statement

Explain what makes this app a Mac app:

- App category.
- Primary Mac workflows.
- Why the window/document model fits macOS.
- Which Mac conventions it will embrace.
- Where it intentionally departs from convention and why.

### 2. Affordance map

Provide a table for major UI elements covering native controls, menus, keyboard, selection, pasteboard, drag/drop, state, and accessibility.

### 3. Command/menu plan

List menus, commands, shortcuts, validation rules, toolbar placement, and context menu placement.

### 4. Window/document plan

Describe windows, tabs, panels, sheets, document handling, restoration, and multi-window behaviour.

### 5. Interoperability plan

Describe supported file types, pasteboard types, drag/drop types, import/export, Finder integration, automation, and sharing.

### 6. Settings and state plan

Describe settings, defaults, persisted layout, per-document state, and reset behaviour.

### 7. Accessibility plan

Describe keyboard access, VoiceOver structure, labels, contrast, motion, custom controls, and testing.

### 8. QA checklist

Provide manual tests for Mac behaviour, including “I wonder if this works” tests.

## Implementation guidance for SwiftUI/AppKit agents

### Prefer AppKit when needed

Use AppKit or AppKit bridging when SwiftUI cannot yet deliver a required Mac behaviour reliably. This is especially common for:

- Advanced text editing.
- Complex tables/outlines.
- Fine-grained drag and drop.
- Multi-window document workflows.
- Toolbar customisation.
- Panels and inspectors.
- Complex menu validation.
- Custom accessibility.

### SwiftUI guidance

When using SwiftUI:

- Use `.commands` for menu commands.
- Use keyboard shortcuts intentionally.
- Use `FocusedValues` and focus state for command routing.
- Use `UndoManager` for reversible actions.
- Use `Transferable`, `NSItemProvider`, or AppKit bridging for drag/drop and pasteboard as needed.
- Use `DocumentGroup` only when it matches the document model.
- Use `Settings` scene for preferences.
- Use `@AppStorage`, scene storage, or explicit persistence for state.
- Test on macOS, not only previews.
- Bridge to AppKit rather than accepting broken Mac behaviour.

### AppKit guidance

When using AppKit:

- Use `NSDocument` for document apps where appropriate.
- Use `NSTextField`, `NSTextView`, `NSTableView`, `NSOutlineView`, `NSSplitView`, `NSToolbar`, `NSMenu`, `NSOpenPanel`, `NSSavePanel`, and system panels where appropriate.
- Use responder chain and menu validation.
- Use `NSPasteboard` with multiple representations.
- Use drag source and destination APIs thoughtfully.
- Use `NSUserInterfaceValidations` or equivalent patterns.
- Use `NSUserDefaults` for preferences and state where appropriate.
- Use Accessibility APIs for custom controls.

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

## “I wonder if this works” QA tests

Run these tests manually where relevant:

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

## Refactoring guidance for non-Mac-like apps

When adapting an existing web, Electron, Catalyst, or cross-platform app:

1. Keep the product’s core model, but redesign the Mac shell.
2. Add a real menu bar and command routing.
3. Replace custom controls with native controls where possible.
4. Implement standard shortcuts and focus behaviour.
5. Add Mac windowing: multiple windows, tabs, inspectors, sheets, or panels as appropriate.
6. Add Settings via Command-Comma.
7. Add drag/drop and pasteboard support around core objects.
8. Add Finder/file/open/save/import/export behaviour.
9. Preserve meaningful user state.
10. Audit accessibility.
11. Run the “I wonder if this works” checklist.
12. Keep iterating until the app’s behaviour, not just its styling, feels Mac-like.

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

## Operating principle for agents

Do not merely produce a screen that satisfies the immediate feature request. Produce a Mac workflow. For every visible object and every user action, ask:

1. What would macOS normally do here?
2. What would a long-time Mac user try here?
3. What public formats or system services should this object participate in?
4. What state would the user expect to be remembered?
5. What keyboard, menu, drag/drop, copy/paste, undo, and accessibility behaviours are implied?
6. Is any departure from convention justified by a better user outcome?

A Mac-arsed Mac app is built by answering those questions again and again, even for small details.
