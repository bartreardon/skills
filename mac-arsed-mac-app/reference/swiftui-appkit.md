# Implementation guidance for SwiftUI/AppKit agents

Read this when actually writing code (or reviewing code) for the app, after the workflow has settled the design.

## Choosing between SwiftUI and AppKit

SwiftUI is the default for new Mac apps and is the direction Apple is investing in. Prefer it, and verify that the specific Mac behaviours you need (see [`detailed-rules.md`](detailed-rules.md)) actually work.

When you hit a behaviour SwiftUI cannot currently deliver, bridge to AppKit (via `NSViewRepresentable` / `NSHostingView`) **for that specific piece** — not for the whole app. The guiding principle is: bridge rather than ship broken Mac behaviour. A SwiftUI-only implementation that silently drops a native behaviour is not Mac-arsed.

What this is *not*: a reason to default to AppKit for new projects on the belief that older APIs are inherently more "Mac-arsed." That argument recurs at every Mac platform transition, and the framework gaps below have narrowed over successive releases. Reach for AppKit when a gap genuinely blocks the Mac experience, and only for the part that needs it. If a developer deliberately chooses an AppKit-first architecture, support them — but do not push anyone there.

Behaviours where bridging is commonly needed today:

- Advanced text editing.
- Complex tables/outlines.
- Fine-grained drag and drop.
- Multi-window document workflows.
- Toolbar customisation.
- Panels and inspectors.
- Complex menu validation.
- Custom accessibility.

## Current SwiftUI gaps on the Mac (and AppKit fallbacks)

These are concrete places where pure SwiftUI is hard or currently impossible to make fully Mac-native. Know them so you can recognise them, test for them, and decide where a targeted AppKit bridge is worth it. Treat this as a snapshot: SwiftUI is evolving and some of these will close over time — re-check against the current OS rather than assuming.

### Selection and focus states

macOS distinguishes three layers of "highlighted," and getting all three right is what makes a list feel native:

1. **Active vs inactive window.** SwiftUI exposes this via the `\.appearsActive` environment value. System controls like `List` and `Button` adjust automatically; custom controls should read `\.appearsActive` and tone down their selection/tint when the window is not key.
2. **Selected but not focused ("emphasized").** AppKit gives rows `NSTableRowView.isEmphasized` to distinguish a selection in the focused control (vivid) from one in an unfocused control (grey). SwiftUI has no built-in equivalent. If you need it in a custom list, the workaround is to build the list yourself (`ScrollView` + `LazyVStack`), track whether the scroll view holds focus with `.focusable`/`.focused`, and propagate it to rows through the environment (e.g. a custom `\.isEmphasized` key that rows read alongside `\.appearsActive`).
3. **Context-menu target.** When you right-click an unselected item, macOS draws a focus ring around *that* item for the duration of the menu. SwiftUI gives you no signal that a context menu is open, so this is effectively impossible to reproduce in custom views — only `List` does it correctly, because it is backed by `NSTableView`. If this behaviour matters, use `List` or bridge to AppKit.

### Drag and drop

SwiftUI's drag/drop APIs have been through several generations — `onDrag`/`onDrop` (built on `NSItemProvider`), then the `Transferable` protocol with `draggable`/`dropDestination`, and newer `DropSession`-based and container multi-item variants on recent macOS. Pick the newest that your deployment target supports.

The structural limitation to plan around: as the **drag source**, SwiftUI gives you almost no visibility into the drag session. You can't reliably dim or remove the dragged element while it's in flight, you can't tell whether the user dropped it outside the window, and items can get stuck in a half-dimmed state on a failed drop. AppKit's `NSDraggingSource` exposes the full session (begin, move, end, and the operation performed). When source-side drag feedback matters, bridge the draggable view to AppKit.

### Keyboard: arrow navigation and text-field focus

- **Arrow-key movement** in custom views uses the `.onMoveCommand` modifier on macOS. Note it is macOS-only, so shared iPad/Mac code that also wants hardware-keyboard arrow support needs a platform split.
- **Text fields capture keys.** Once a SwiftUI `TextField` is focused it consumes keyboard events, which makes the long-standing Mac pattern of *typing in a search field while arrow-keying the results* (Spotlight-style) hard to build in pure SwiftUI. If you need continuous typing plus list navigation, expect to bridge the field (or the key handling) to AppKit.

### Window toolbars

SwiftUI toolbar items use semantic placements — `.primaryAction`, `.secondaryAction`, `.navigation`, etc. — which resolve differently on each platform and are hard to predict inside three-pane split views, because the bar is assembled by collecting `.toolbar` modifiers from across the view hierarchy. If you need one deliberately-arranged Mac toolbar (precise item order, grouping, overflow), an `NSToolbar` bridge gives you direct control.

## SwiftUI guidance

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

**`List` vs `Table`, and the cost of customising `List`.** `List` inherits `NSTableView`'s selection behaviour — including the context-menu focus ring above — essentially for free, which makes it the safest choice for standard selectable lists. The trade-off is that it resists visual customisation: overriding the selection colour with `.listRowBackground()` can break the built-in selection fade-out animation, and its context-menu focus ring can't be restyled. Weigh that when deciding between `List`, `Table`, and a hand-built `ScrollView`/`LazyVStack` — there is no single right answer, only the trade-off between free native behaviour and visual control.

## AppKit guidance

When using AppKit:

- Use `NSDocument` for document apps where appropriate.
- Use `NSTextField`, `NSTextView`, `NSTableView`, `NSOutlineView`, `NSSplitView`, `NSToolbar`, `NSMenu`, `NSOpenPanel`, `NSSavePanel`, and system panels where appropriate.
- Use responder chain and menu validation.
- Use `NSPasteboard` with multiple representations.
- Use drag source and destination APIs thoughtfully.
- Use `NSUserInterfaceValidations` or equivalent patterns.
- Use `NSUserDefaults` for preferences and state where appropriate.
- Use Accessibility APIs for custom controls.
