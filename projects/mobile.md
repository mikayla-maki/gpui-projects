# GPUI on mobile

Goal: run GPUI apps natively on iOS, with the core work
deliberately shaped so Android is nearly free afterward.

## Status

- **Landed**: the mobile/touch API surface in gpui core —
  [zed#60496](https://github.com/zed-industries/zed/pull/60496): raw touch
  events (`TouchEvent`, `TouchId`, `TouchPhase::Cancelled`), gesture
  vocabulary (`GestureTuning`, `GestureKinds`, `LongPressEvent`,
  `PlatformGestures`), mobile window surface (`WindowInsets`, back handling,
  soft keyboard, `TextInputStateChange`), and `ClickEvent::Touch` +
  `is_secondary()`.
- **Prototyped**: a working iOS platform crate on the
  [`gpui-ios-platform`](https://github.com/zed-industries/zed/tree/gpui-ios-platform)
  branch — wgpu/Metal into a `UIWindow`, GCD executors, raw `UITouch`
  delivery, multi-touch example in the simulator (`script/ios-example`). The
  full design synthesis lives in `crates/gpui_ios/HANDOFF.md` on that branch.

## Design principles (decided)

- **No mouse synthesis from touches.** Touches never produce mouse events,
  hover state, or cursor changes. Gesture recognition is the only sanctioned
  synthesis point, and it emits semantic events (`ClickEvent`, scroll,
  pinch), never mouse-mechanical ones.
- **Implicit capture.** A touch is hit-tested once at start; every later
  event routes to the elements under the starting position. Matches web
  Touch Events, web Pointer Events' implicit capture, UIKit, and Android.
- **One portable gesture arena in gpui core** (Flutter-style); platforms may
  contribute native recognizers as ordinary contestants.
- **Secondary activation is semantic**: right click and long-press converge
  on `ClickEvent::is_secondary()` / the aux-click path, not on fabricated
  right-click events.
- **Mobile IME must be first-class**: `UITextInput` + `UITextInteraction`
  bridged to `PlatformInputHandler`, not a bare key-input shim.
- **Desktop `Platform` APIs on mobile**: three tiers — must-work (core calls
  them), loud no-op (shared app code plausibly calls them), panic (genuinely
  nonsensical).

## Active issues

- [#1](https://github.com/mikayla-maki/gpui-projects/issues/1) — semantic
  secondary activation (unwind macOS ctrl-click synthesis)
- [#2](https://github.com/mikayla-maki/gpui-projects/issues/2) — gesture
  arena: design doc + recognizers
- [#3](https://github.com/mikayla-maki/gpui-projects/issues/3) — reland the
  `gpui_ios` platform crate

## Later

- Android platform crate
  ([#12](https://github.com/mikayla-maki/gpui-projects/issues/12)) — the
  arena, capture semantics, and lifecycle API are already shaped for it
- Raw pointer-event unification (mouse/touch/pen) — north star, triggered
  only if custom-element authors end up duplicating mouse+touch code;
  `TouchEvent` is already pointer-shaped so it stays a rename, not a redesign
- Stylus: `TouchKind` field on `TouchEvent` (Apple Pencil arrives as a
  `UITouch` with tilt/force — cheap and additive)
