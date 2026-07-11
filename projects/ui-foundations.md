# UI foundations

The long-running effort to grow GPUI's UI layer: views, element identity,
text, selection, the inspector, and RTL. The three big bets were **views**,
**identity**, and **Parley** — views are done, identity's collision half is
done (the always-on half awaits its benchmark), Parley awaits commitment.
Everything else is independent elbow grease or downstream of those.

## Done ✅

- **Root ICB fill** — [zed#60739](https://github.com/zed-industries/zed/pull/60739):
  auto-sized window roots fill the viewport
- **Projections / smart-pointer lattice** — [zed#60740](https://github.com/zed-industries/zed/pull/60740):
  `ReadEntity`, `Projection`/`ProjectionMut`, weak twins, `use_projection`
  hooks + `project!` macro, relay-entity identity
- **View identity for components** — solved by relay projections (the
  `(EntityId, TypeId)` extension was considered and rejected)
- **Default keybinding system** — [zed#60742](https://github.com/zed-industries/zed/pull/60742):
  `keybinding!` macro, `namespace = crate`, load-at-run,
  `Application::without_default_key_bindings`, Zed opt-out

## Now — no dependencies, can start anytime

- **Identity benchmark** ([#5](https://github.com/mikayla-maki/gpui-projects/issues/5)) —
  release build with `inspector` feature, measure, intern paths if needed;
  de-risks selection *and* the always-on inspector
- **Logical property builders** (`ps_*`/`pe_*`) ([#6](https://github.com/mikayla-maki/gpui-projects/issues/6)) —
  additive; every new physical call site is RTL migration debt
- **Container query element** ([#7](https://github.com/mikayla-maki/gpui-projects/issues/7)) —
  prepaint two-pass (`uniform_list` pattern), no deferred draws needed
- **Transitions layer** ([#8](https://github.com/mikayla-maki/gpui-projects/issues/8)) —
  declarative `StyleRefinement`-diffing; the one real design decision is
  interruption semantics (store animated value + velocity, not target)
- **Clip tree** ([#9](https://github.com/mikayla-maki/gpui-projects/issues/9)) —
  WebRender-style clip chains (the preferred "generalize clipping" route),
  *or* the quicker mechanical rounded-corner patch if the visual fix is
  wanted sooner

## Next — unblocked as foundations land

*Views have landed, so these are ready too:*

- **Text input / TextArea components** — graduate from `view_example` into a
  new components crate; `impl Into<ProjectionMut<String>>` signature; a11y
  roles non-optional; `keybinding!` defaults (⬅ the agreed next big build)
- **Scroll container protocol** — wrapper-based scrolling with a descendant
  protocol: scroll-into-view, sticky, anchoring
- **Semantics baked into primitives** — a11y roles required at construction
  in Button/Checkbox/etc.

*When identity goes always-on (after the benchmark):*

- **Text selection v0** — tree-order ranges, single element first,
  cross-element falls out; the white whale

*When the clip tree lands:*

- **Rounded-corner clipping** (first client)
- **Element transforms** (second client; v1 = translate/scale only)

## After — compound features

- **Inspector ported into gpui, always-on, extensible** ← views + input +
  identity (element-tree panel, panels-as-registry, entity/observability
  side, write-back-to-source, Chrome-style docking)
- **Full selection: bidi + find-in-page** ← selection v0 + Parley
- **Inline / rich text flow** ← Parley (inline boxes, floats/exclusions
  already supported)
- **Full RTL mirroring** ← logical properties + Parley

## Open decisions

1. **Parley: commit or wait?** (current read: commit — the stack under it is
   Google-production-proven; budget for version churn)
2. **Clip tree vs. mechanical patch** for rounded corners ([#9](https://github.com/mikayla-maki/gpui-projects/issues/9))
3. **When identity goes always-on** (gated on the benchmark, [#5](https://github.com/mikayla-maki/gpui-projects/issues/5))
4. **Components crate location** — separate Apache crate vs. inside gpui
   (licensing + inspector-dependency argue for separate)
