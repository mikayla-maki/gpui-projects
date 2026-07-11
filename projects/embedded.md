# Embedded plugin platform

Goal: a UI-extension model for GPUI apps (Zed first) where a plugin is an
ordinary GPUI program — entities, elements, flexbox, input, async — compiled
to `wasm32-wasip2` and rendered by the host as just another element in its
tree. The spike lives at
[zed-industries/embedded_gpui](https://github.com/zed-industries/embedded_gpui).

## Status

- **Proven end to end on macOS**: guest-side GPUI platform over a WIT
  protocol (windows, retained display lists, mouse/keyboard, timers, SVG and
  images, host-side text shaping); a wasmtime host runtime that replays
  guest display lists as native GPUI primitives and never calls wasm from
  the frame path; shared entities (`Remote`, `observe`/`subscribe`,
  refcounted release, capability `Ref`s); typed interfaces via
  `#[shared_interface]`/`#[shared]`; OCAP utilities (`Revocable`,
  `Attenuated`, `Audited`, `Mirror`). Release component is ~3.8 MB.
- **Upstream hook PR'd**: `run_embedded`/`ApplicationHandle` —
  [zed#60574](https://github.com/zed-industries/zed/pull/60574); the git
  dependency moves from the `gpui-embedded-in-gpui` branch to `main` once it
  lands.
- Architecture + invariants in the repo's `DESIGN.md`; the gap list between
  "proven" and "product" in `TODO.md`.

## Design principles (decided)

- **The host never calls into the guest synchronously from the frame path.**
  Guests render when ticked; the host caches and replays retained display
  lists.
- **The wasmtime store lives on a background worker** — a hung plugin cannot
  stall the UI; FIFO request/effect pairing preserves ordering.
- **Text is shaped and rasterized by the host**; guests emit symbolic glyph
  primitives.
- **Symmetric, stringless object model**: one root object per side at a
  reserved address, everything else discovered through typed method calls;
  authority is reachability from your root; object ids are random u64s.
- **Capabilities over configuration**: the WASI sandbox grants nothing by
  default; everything a plugin can do to the app is reachable from the root
  you install, so tailoring a plugin's world is constructing a different
  root.

## Now

- **Views as replicated objects** (⬅ agreed next build) — renderability
  becomes a feature of a shared entity: `view("panel")` dissolves into a
  typed ref-returning method (the last string identifier in the protocol),
  inline surfaces travel as anonymous renderable refs in payloads, input
  routes backward along entity identity, and revocation/attenuation compose
  for free. Guests mount renderables in one hidden composition window and
  slice per-region display lists at serialization.

## Later

- **Object model follow-ups**: promise pipelining via caller-allocated ids,
  share dedup, root versioning discipline (the root schema *is* the
  extension-API compatibility surface), a public symmetric `Peer`-style
  handle.
- **Platform completeness**: resource limits (epoch interruption, memory
  caps, fuel), IME/marked text proxying, gradients/video/sprite transforms/
  inset shadows, atlas eviction + session-scoped `FontId` hygiene.
- **Multi-plugin routing**: several stores behind one host; the host routes
  by who homes an id (Cap'n-Proto vats without the tables) and becomes the
  policy chokepoint — membranes, audit, powerbox consent. Discovery is a
  host-homed, opt-in registry entity.
- **Advanced OCAPs**: tagged refs on the wire, deep membranes, loopback
  routing, expiring/N-use grants, sealer/unsealer pairs, multi-subscriber
  homes.
- **Zed integration**: mount points declared as root-object methods,
  packaging through the extension registry, WIT protocol versioning.
