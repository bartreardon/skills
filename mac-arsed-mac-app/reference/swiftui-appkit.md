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

These are concrete places where pure SwiftUI is hard or currently impossible to make fully Mac-native. Know them so you can recognise them, test for them, and decide where a targeted AppKit bridge is worth it. Treat this as a snapshot: SwiftUI is evolving and these gaps close over time — re-check against the current OS rather than assuming. The WWDC27 releases already closed or narrowed several of the items below, which is exactly why the fallback advice is "bridge the specific piece for your deployment target," not "prefer AppKit." Each item notes its current status; availability is version-gated, so a gap "solved" on the newest OS still needs the fallback if you deploy to older systems.

### Selection and focus states

macOS distinguishes three layers of "highlighted," and getting all three right is what makes a list feel native:

1. **Active vs inactive window.** SwiftUI exposes this via the `\.appearsActive` environment value. System controls like `List` and `Button` adjust automatically; custom controls should read `\.appearsActive` and tone down their selection/tint when the window is not key.
2. **Selected but not focused ("emphasized").** AppKit gives rows `NSTableRowView.isEmphasized` to distinguish a selection in the focused control (vivid) from one in an unfocused control (grey). *Improved in WWDC27:* SwiftUI now exposes the `\.backgroundProminence` environment value plus the `.selection` shape style, which `List` and `Table` drive automatically — read `\.backgroundProminence` in a row to adjust foreground styling for the emphasized/de-emphasized state. This only covers `List`/`Table`; a **custom** list still needs the manual approach — build it yourself (`ScrollView` + `LazyVStack`), track whether it holds focus with `.focusable`/`.focused`, and propagate emphasis through the environment (alongside `\.appearsActive`).
3. **Context-menu target.** When you right-click an unselected item, macOS draws a focus ring around *that* item for the duration of the menu. *Still unsolved as of WWDC27:* SwiftUI gives you no signal that a context menu is open, so this remains effectively impossible to reproduce in custom views — only `List` does it correctly, because it is backed by `NSTableView`. If this behaviour matters, use `List` or bridge to AppKit.

### Drag and drop

SwiftUI's drag/drop APIs have been through several generations — `onDrag`/`onDrop` (built on `NSItemProvider`), then the `Transferable` protocol with `draggable`/`dropDestination`, and newer `DropSession`-based and container multi-item variants on recent macOS. Pick the newest that your deployment target supports.

The historical limitation was source-side visibility: SwiftUI gave the **drag source** almost no insight into the session, so you couldn't reliably dim the dragged element in flight, tell whether the user dropped outside the window, or avoid items getting stuck half-dimmed on a failed drop. *Largely solved in WWDC27:* the `.onDragSessionUpdated` modifier (macOS 26+) reports the session from begin through end — matching what AppKit's `NSDraggingSource` provided — so you can update UI reliably as a drag starts and finishes. The new `.reorderable` modifier also covers basic list reordering without hand-rolling it.

Fallback still applies for older deployment targets: if you must support systems before these APIs, bridge the draggable view to AppKit's `NSDraggingSource` for full session control.

### Keyboard: arrow navigation and text-field focus

- **Arrow-key movement** in custom views uses the `.onMoveCommand` modifier on macOS. *Still macOS-only as of WWDC27* (unavailable on iOS despite being on tvOS), so shared iPad/Mac code that also wants hardware-keyboard arrow support needs a platform split. As a cross-platform workaround you can drop to `.onKeyPress`, though it works at the level of raw key events rather than movement intent.
- **Text fields capture keys.** Once a SwiftUI `TextField` is focused it consumes keyboard events, which makes the long-standing Mac pattern of *typing in a search field while arrow-keying the results* (Spotlight-style) hard to build in pure SwiftUI. If you need continuous typing plus list navigation, expect to bridge the field (or the key handling) to AppKit.

### Window toolbars

SwiftUI toolbar items use semantic placements — `.primaryAction`, `.secondaryAction`, `.navigation`, etc. — which resolve differently on each platform and are hard to predict inside three-pane split views, because the bar is assembled by collecting `.toolbar` modifiers from across the view hierarchy. *Partially improved in WWDC27:* the `.visibilityPriority` modifier gives you some control over what collapses into the overflow menu, but it doesn't address the core difficulty of arranging one deliberate Mac toolbar. If you need precise item order, grouping, and overflow, an `NSToolbar` bridge still gives you direct control.

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
