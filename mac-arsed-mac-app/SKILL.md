---
name: mac-arsed-mac-app
description: Design, build, review, or refactor native macOS apps that follow Mac conventions—menus, keyboard, drag and drop, windows, pasteboard, accessibility—rather than web, Electron, or iPad apps merely packaged for macOS. Use this skill whenever the user is creating, critiquing, planning, or porting a Mac app and wants it to feel genuinely native ("Mac-arsed" or "Mac-assed"), even if they don't use that exact phrase.
license: Complete terms in LICENSE.txt
---

# Mac-Arsed Mac App Skill

## Purpose

Create macOS apps that feel like they belong on the Mac. A Mac-arsed Mac app is not just an app that runs on macOS or uses a few native-looking controls. It is an app that embraces macOS conventions, behaves like other good Mac apps, rewards user attention, respects power users, integrates with the system, and contains the small affordances that make users think: "I hoped that would work, and it did."

> The term appears in two spellings: **Mac-arsed** (used throughout this skill) and **Mac-assed** (the originator's spelling — see Sources). Treat them as synonyms.

Use this skill when designing, implementing, reviewing, or refactoring:

- Native macOS apps.
- Mac versions of iOS, iPadOS, web, Electron, or cross-platform apps.
- Document-based tools, productivity apps, utilities, creative tools, editors, menu bar apps, and professional Mac software.
- UI/UX specifications, SwiftUI/AppKit architecture, interaction models, QA checklists, or code review feedback for macOS software.

## Core definition

A Mac-arsed Mac app is:

- **Platform-specific**: It does not pretend the Mac is just a bigger iPad, a web browser, or a generic desktop shell.
- **Behaviourally native**: It follows native control, keyboard, menu, pasteboard, accessibility, window, and document behaviours.
- **Culturally Mac-like**: It participates in the evolving culture of Mac software rather than copying another platform wholesale.
- **User-respecting**: It saves meaningful state, supports configuration, preserves flow, and does not make users repeat avoidable work.
- **Interoperable**: It collaborates with the Finder, Terminal, pasteboard, drag and drop, file formats, Services, and Shortcuts/automation where appropriate.
- **Deep but approachable**: It starts simple, then progressively discloses power without hiding it forever.
- **Polished in the small**: It implements details that many users never consciously notice, but some rely on every day.

## Guiding philosophy

**1. Behaviour beats appearance.** A Mac app can look polished and still feel wrong if its controls do not behave like Mac controls. Prefer real native controls. When you cannot use them, reproduce native behaviours completely — edge cases, keyboard handling, focus, selection, accessibility, contextual menus, drag and drop, undo, and pasteboard formats included. Do not stop at "it looks close." Ask: "Would a long-time Mac user's muscle memory work here?"

**2. The Mac has strong but quiet opinions.** macOS has conventions about menus, windows, shortcuts, text editing, selection, drag and drop, files, settings, and system integration. They are not always loud, but users feel when an app ignores them. Depart from convention only intentionally, and only when the result is better for the user in that specific context.

**3. If a reasonable Mac user wonders whether something works, it should probably work.** If users can see, select, drag, copy, resize, reorder, disclose, or open something, think through what they will expect to happen — can I drag this somewhere useful? copy it and paste a sensible representation? select multiple items and act on them together? use the same shortcut that works in other Mac apps? If a Mac user would reasonably expect it, implement it or document why it cannot be supported.

**4. The Mac rewards attention.** Good Mac apps work for new users but also contain depth for those who invest time: keyboard navigation, alternate commands, drag and drop, Services, contextual menus, configurable toolbars, saved state, scriptability, and power-user workflows.

**5. Culture evolves; do not build a museum piece.** Classic Mac, NeXT, UNIX, the web, iOS, and iPadOS have all influenced modern macOS. Do not reject an idea merely because it came from another platform, and do not adopt an outside convention if it replaces a better Mac one. Use current macOS conventions as the default; older conventions help you understand the culture, but the app should feel current.

## Required workflow

Follow these steps whenever creating or reviewing a Mac app. The deep rules behind each step live in [`reference/detailed-rules.md`](reference/detailed-rules.md) — consult the relevant section as you work.

**Step 1 — Classify the app.** Identify its Mac shape before designing UI: document-based (editor/CAD/writing tool), shoebox/library (notes, mail, photos, tasks), utility (menu bar, converter, helper), professional workspace (IDE, DAW, creative suite), viewer/browser, or hybrid. Then identify the core objects: what can be opened, saved, selected, dragged, copied, imported/exported, shown in multiple windows, persisted, and undone?

**Step 2 — Choose the native substrate.** Use the least-custom technology that delivers correct Mac behaviour, and use native text/table/outline/collection/split/toolbar/menu/panel APIs wherever possible (NSDocument for document apps). SwiftUI is the default and the direction Apple is investing in; prefer it. A handful of Mac behaviours are still hard or impossible in pure SwiftUI (context-menu focus ring, emphasized selection, source-side drag feedback, type-while-arrow search, precisely arranged toolbars) — where one genuinely blocks the Mac experience, bridge that specific piece to AppKit rather than defaulting to AppKit for the whole project; see [`reference/swiftui-appkit.md`](reference/swiftui-appkit.md). Catalyst, shared iPad code, and Electron/web are acceptable only with extra scrutiny — and only when Mac-specific windows, menus, shortcuts, toolbars, settings, drag and drop, pointer, file, accessibility, and pasteboard behaviour are intentionally implemented. A web app in a window is not enough.

**Step 3 — Design the Mac affordance map.** For each major screen, panel, list, editor, item, and command, map native control/API, selection behaviour, keyboard support, copy/paste, drag/drop, context menu, state to save, and accessibility role. See the affordance-map template in [`reference/design-outputs.md`](reference/design-outputs.md). Every non-trivial UI should have one before implementation.

**Step 4 — Build the command model first.** Menus and shortcuts are not afterthoughts. Before polishing screens, define App/File/Edit/View/Window/Help menu items, any domain-specific menus, toolbar and context-menu commands, keyboard shortcuts, undo/redo actions, and command-availability rules. Every important action should be reachable from the menu bar or a contextual menu — not only from an unlabeled icon. See the Menus and Keyboard sections of the detailed rules.

**Step 5 — Design windows and documents intentionally.** macOS is a windowing environment; do not be afraid of windows. Decide what should be a document window, a tab, a secondary window, an inspector/utility panel, a sheet, a popover, a sidebar/split view, or a transient dialog. For document apps, strongly consider one window per document, standard open/save panels, autosave/versions, proxy-icon behaviour, sensible default save locations, and multiple windows or tabs when users compare or reference documents. See the Windows and Document sections of the detailed rules.

**Step 6 — Design pasteboard and drag/drop behaviour.** For each selected object, define multiple pasteboard representations (plain text, rich text, file URL, web URL, image, app-specific data, public UTTypes). Implement drag and drop into, out of, within, and between windows of the app, and to/from Finder and other apps. If a thing represents a file, image, URL, text, row, card, object, or document, ask whether it should be draggable or copyable. See the Drag and drop and Pasteboard sections of the detailed rules.

**Step 7 — Define state preservation and configuration.** Persist meaningful choices: window size/position, sidebar widths, split positions, toolbar customisation, sort order, column visibility/widths, disclosure states, last view/mode, recent locations, per-document view state, and preferences. Do not persist meaningless transient positions (e.g. a warning dialog dragged aside). See the State preservation and Configurability sections of the detailed rules.

**Step 8 — Add progressive disclosure.** Start with good defaults, then reveal depth: disclosure groups, optional advanced sections, configurable toolbars, contextual menus, Option-key alternates, tooltips, settings with clear defaults, inspectors, and sensible empty states that teach without becoming onboarding clutter. Do not remove advanced capabilities merely to keep the first screen simple.

**Step 9 — Verify with a Mac behaviour test plan.** Test menus and shortcuts, text editing, selection, drag and drop, copy/paste into multiple apps, undo/redo, window restoration, settings persistence, accessibility (keyboard + VoiceOver), light/dark/high-contrast appearances, multiple displays and sizes, Finder integration, open/save panels, import/export, and relaunch/document-reopen. Use the rubric and "I wonder if this works" tests in [`reference/review-and-qa.md`](reference/review-and-qa.md). If the environment cannot run macOS, do not claim runtime verification — deliver a manual QA checklist instead.

## Reference material

Load these as needed rather than keeping them all in mind:

| When you are… | Read |
|---|---|
| Working through the behaviour of a specific area (controls, menus, keyboard, text, selection, drag/drop, pasteboard, windows, documents, state, config, interop, accessibility, undo, performance, visual design) | [`reference/detailed-rules.md`](reference/detailed-rules.md) |
| Producing a design/plan deliverable for the user | [`reference/design-outputs.md`](reference/design-outputs.md) |
| Reviewing, scoring, or building a test plan for an app | [`reference/review-and-qa.md`](reference/review-and-qa.md) |
| Writing or reviewing SwiftUI/AppKit code | [`reference/swiftui-appkit.md`](reference/swiftui-appkit.md) |

## Operating principle

Do not merely produce a screen that satisfies the immediate feature request. Produce a Mac workflow. For every visible object and every user action, ask:

1. What would macOS normally do here?
2. What would a long-time Mac user try here?
3. What public formats or system services should this object participate in?
4. What state would the user expect to be remembered?
5. What keyboard, menu, drag/drop, copy/paste, undo, and accessibility behaviours are implied?
6. Is any departure from convention justified by a better user outcome?

A Mac-arsed Mac app is built by answering those questions again and again, even for small details.

## Sources / further reading

This skill collects and organises principles from the Mac development community; it does not originate them. Credit belongs to the sources below.

- **Origin of the term**: "Mac-assed Mac app" was coined by Collin Donnell and popularised by Brent Simmons; John Gruber discussed it on Daring Fireball, 20 March 2020 (<https://daringfireball.net/linked/2020/03/20/mac-assed-mac-apps>). Simmons' framing — apps that are "unapologetically Mac apps... platform-specific and not trying to wow us with custom not-Mac-like UI" — is the seed of the philosophy here.
- **Accidental Tech Podcast — "Mac-Assed Mac Apps"** (ATP Dev, 28 June 2026; <https://atp.fm/atp-dev-mac-assed-mac-apps>): the main basis for this skill's discussion. It co-opts the older term rather than originating it.
- **Paulo Andrade, "Using SwiftUI to Build a Mac-assed App in 2026"** — <https://pfandrade.me/blog/mac-assed-swiftui-app/>, with the **WWDC27 update** at <https://pfandrade.me/blog/swiftui-mac-assed-wwdc27-update/>: source of the concrete SwiftUI/AppKit specifics in [`reference/swiftui-appkit.md`](reference/swiftui-appkit.md) (named environment values, selection/focus layers, drag-session APIs, toolbar placement) and their current status. The posts take a pointed, AppKit-leaning view; this skill keeps SwiftUI as the default and treats AppKit as a targeted fallback, so read them for the technical detail rather than the stance.

