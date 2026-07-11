# gpui-projects

Mikayla's personal working notes and task queue for her work on
[GPUI](https://github.com/zed-industries/zed/tree/main/crates/gpui), Zed.

**This is not an official Zed Industries roadmap.** It's one person's todo
list, kept public so people can see what I'm working on and where GPUI might
be headed. Please don't file Zed bugs here — those go to
[zed-industries/zed](https://github.com/zed-industries/zed/issues).

## How this repo works

- **[Issues](../../issues)** are small, discrete tasks — things that could be
  finished within a couple weeks of starting.
- **[`projects/`](projects/)** holds living docs for larger efforts — anything
  with phases, dependencies, or open design decisions.
- Issues link up to their parent project doc; docs link down to their active
  issues. When an issue outgrows itself, it graduates into a doc.

## Projects

- [UI foundations](projects/ui-foundations.md) — views, element identity,
  text (Parley), selection, the inspector, RTL
- [Mobile](projects/mobile.md) — touch input, the gesture arena, the iOS
  platform
- [Embedded plugin platform](projects/embedded.md) — GPUI plugins in Wasm
  components, shared entities, capabilities, the Zed extension model
