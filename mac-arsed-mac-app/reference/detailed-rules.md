# Detailed rules

Deep reference for each area of Mac behaviour. Read the section relevant to whatever you are designing, implementing, or reviewing. Step 3 onward of the main workflow draws on these rules.

## Table of contents

- [Native controls and behaviours](#native-controls-and-behaviours)
- [Menus](#menus)
- [Keyboard and focus](#keyboard-and-focus)
- [Text editing](#text-editing)
- [Selection](#selection)
- [Drag and drop](#drag-and-drop)
- [Pasteboard and copy/paste](#pasteboard-and-copypaste)
- [Windows, panels, tabs, and sheets](#windows-panels-tabs-and-sheets)
- [Document and file behaviour](#document-and-file-behaviour)
- [State preservation](#state-preservation)
- [Configurability](#configurability)
- [Progressive disclosure](#progressive-disclosure)
- [Interoperability](#interoperability)
- [Accessibility](#accessibility)
- [Undo and reversible actions](#undo-and-reversible-actions)
- [Performance and responsiveness](#performance-and-responsiveness)
- [Visual design](#visual-design)
- [Current convention check](#current-convention-check)

## Native controls and behaviours

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

## Menus

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

## Keyboard and focus

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

Two Mac-specific patterns worth calling out (with SwiftUI notes in [`swiftui-appkit.md`](swiftui-appkit.md)):

- **Arrow-key movement** in custom lists/grids should be handled deliberately (in SwiftUI, via `.onMoveCommand` on macOS).
- **Type-ahead while navigating**: the classic Mac search pattern lets the user keep typing in a search field while arrow-keying through the results (Spotlight-style). A focused text field that swallows every key blocks this — preserve the ability to navigate results while the field keeps focus.

## Text editing

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

## Selection

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

macOS distinguishes three layers of highlighting, and native lists get all three right:

- **Active vs inactive window**: selection is vivid in the key window and muted elsewhere (SwiftUI exposes this as `\.appearsActive`).
- **Selected but not focused ("emphasized")**: a selection in the focused control looks stronger than the same selection in an unfocused control (AppKit's `NSTableRowView.isEmphasized`).
- **Context-menu target**: right-clicking an unselected item draws a focus ring around that item while the menu is open.

Reproducing all three in custom views is one of the harder parts of Mac UI — see [`swiftui-appkit.md`](swiftui-appkit.md) for which are hard or impossible in pure SwiftUI and the AppKit fallbacks.

## Drag and drop

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

Pay attention to **source-side** drag feedback, not just the drop target: the dragged element should give visual feedback while in flight, a drop outside any valid target should be handled gracefully (no elements left stuck in a half-dimmed state), and a cancelled drag should restore the original state. Pure SwiftUI historically gave the drag source little visibility into the session; recent releases add source-side session APIs — see [`swiftui-appkit.md`](swiftui-appkit.md) for current status and the AppKit (`NSDraggingSource`) fallback for older targets.

## Pasteboard and copy/paste

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

## Windows, panels, tabs, and sheets

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

## Document and file behaviour

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

## State preservation

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

## Configurability

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

Avoid the false simplicity of "no settings" when users clearly need control.

## Progressive disclosure

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

## Interoperability

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

## Accessibility

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

## Undo and reversible actions

Mac users expect undo to work broadly.

Support undo/redo for:

- Text editing.
- Object edits.
- Reordering.
- Deletions.
- Formatting changes.
- Document-level changes.
- Batch operations where practical.

Use meaningful undo action names, such as "Undo Rename," "Undo Delete," or "Undo Move."

For destructive actions:

- Prefer undo over confirmation when safe.
- Use confirmation only when undo is impossible or consequences are severe.
- Make recovery paths visible.

## Performance and responsiveness

A polished Mac app feels direct.

- Keep typing, selection, scrolling, resizing, menus, and drag operations responsive.
- Do not block the main thread for network, disk, indexing, or rendering work.
- Show progress for long operations.
- Support cancellation where appropriate.
- Preserve user work during crashes or relaunch.
- Avoid web-app latency in basic desktop interactions.

## Visual design

Visual design should support Mac behaviour, not replace it.

- Use system spacing, materials, typography, and control sizes where possible.
- Respect active/inactive window states — tint, selection, and emphasis should soften when the window is not key (SwiftUI exposes this as `\.appearsActive`; custom controls should read it).
- Respect light/dark modes.
- Avoid excessive custom chrome.
- Avoid novelty controls where standard controls would be clearer.
- Use animation sparingly and purposefully.
- Make resizing layouts robust.
- Do not rely only on colour to convey meaning.

## Current convention check

Before finalising design, compare against:

- Current macOS Human Interface Guidelines.
- Current Apple apps in the same category.
- Respected third-party Mac apps in the same category.
- Older Mac conventions only when they clarify why a current behaviour matters.

When sources disagree, prefer the behaviour that best preserves user expectations, accessibility, interoperability, and workflow efficiency on current macOS.
