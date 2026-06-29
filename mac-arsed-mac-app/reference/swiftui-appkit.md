# Implementation guidance for SwiftUI/AppKit agents

Read this when actually writing code (or reviewing code) for the app, after the workflow has settled the design.

## Prefer AppKit when needed

Use AppKit or AppKit bridging when SwiftUI cannot yet deliver a required Mac behaviour reliably. This is especially common for:

- Advanced text editing.
- Complex tables/outlines.
- Fine-grained drag and drop.
- Multi-window document workflows.
- Toolbar customisation.
- Panels and inspectors.
- Complex menu validation.
- Custom accessibility.

The principle: bridge to AppKit rather than accepting broken Mac behaviour. A SwiftUI-only implementation that drops native behaviour is not Mac-arsed.

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
