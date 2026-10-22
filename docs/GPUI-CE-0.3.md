# GPUI CE 0.3 compatibility

The workspace uses the stable `gpui-ce`, `gpui_ce_platform`, `gpui_ce_macros`,
and `gpui_ce_web` 0.3 releases together. Consumers must use the same GPUI version
as the component crates to avoid incompatible element and context types.

GPUI now provides `Styled::refine_style`. Import `gpui::Styled` for that method;
the duplicate `gpui_base::StyledExt` method has been removed. Other `StyledExt`
helpers retain their existing paths.

The editor retains its row model, UTF-8 offsets, atomic inline elements, and
continuation indentation. The internal `text_compat` module owns the previous
GPUI wrapping algorithm, adapted to shape character widths through the 0.3
backend. This preserves fallback-font measurements. Single-line caret geometry
and hit testing also use the backend rather than inspecting glyph runs.

Rich text selection, range highlights, row extents, and shimmer painting use
GPUI's visual lines, paint fragments, and backend cluster geometry. StyledText
layouts already include alignment, so these consumers do not align them again.
The old system-font reverse-lookup optimization has been removed: GPUI 0.3
caches font resolution and no longer exposes the resolved font descriptor.

The port also adapts content masks, paint builders, shadow backgrounds,
activation, inspector registration, and the deterministic text test fixtures.
The component widget and theme APIs otherwise remain unchanged.

## Validation

Run `cargo fmt --all --check`, then the Base and Component library tests:

```sh
cargo test -p gpui_ce_components_base -p gpui_ce_components --lib
```

The `text_compat` tests shape through Parley with the repository's bundled IBM
Plex font. They cover Unicode byte boundaries, atomic elements, and caret round
trips without depending on installed system fonts.

A full workspace check also needs the platform development dependencies for
Story and WebView. Native Story, accessibility, macOS, and Windows checks remain
part of platform validation; a headless library test is not a substitute for them.
