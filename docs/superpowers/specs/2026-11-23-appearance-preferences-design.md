# MediaMesh Appearance Preferences Design

## Goal

Let users personalize the visual appearance of MediaMesh consistently across Android, Web, Windows, and Linux without changing media content or weakening accessibility.

## MVP behavior

Add an Appearance section to application settings with:

- **Theme mode:** `System`, `Light`, or `Dark`. `System` is the default and follows the current operating-system/browser theme. Explicit `Light` and `Dark` selections override later system changes.
- **Color palette:** `Ocean`, `Forest`, `Violet`, or `Sunset`. `Ocean` is the default. Each palette defines coordinated light and dark variants.
- Palette selection is a preset choice; custom color editing is out of MVP.
- Preferences are stored locally on the current installation. They are not written to Google Drive and are not synchronized across clients.
- Changes apply immediately to application chrome and controls. They do not tint, filter, modify, or recolor photos, videos, thumbnails, or original files.
- Semantic colors for success, warning, error, and informational states retain their meaning across palettes. Status must also be conveyed with text/icons, not color alone.

## Architecture and data

The presentation layer consumes a shared theme configuration exposed by application-level theme settings. Theme resolution combines the saved mode, current system brightness (when mode is `System`), and selected palette. Each palette has explicit light and dark design tokens. All application-owned screens and components use those tokens rather than hard-coded colors.

Persist `theme_mode` and `color_palette` in the existing client-local `settings` store. Valid values are the three modes and four palette identifiers above. A missing, invalid, or unsupported value falls back to `System` and `Ocean`; migration must preserve unrelated settings. These settings are independent from UI language and account configuration.

## Accessibility and failure behavior

Every palette/mode combination must satisfy the product's contrast and accessibility requirements. Palette color must not be the only indication of selection, focus, progress, backup status, or errors. Respect platform high-contrast settings where available; if a platform setting cannot be detected, preserve accessible contrast through the application's own tokens.

If a stored preference is invalid, use the defined defaults and replace the invalid values safely. If settings persistence fails, keep the current in-memory selection for the running session, notify the user that it may not persist after restart, and do not corrupt unrelated settings.

## Verification

- Unit tests cover theme resolution for each mode, system brightness change, palette variants, and invalid/missing preference fallback.
- Persistence tests prove preferences survive restart independently on each platform and that changing them does not alter account, media, upload-job, or language data.
- Widget/snapshot tests cover settings selection and verify representative screens use theme tokens rather than fixed palette colors.
- Accessibility checks cover text/control contrast, keyboard focus, screen-reader labels, and non-color status cues across every shipped palette and mode.
- Platform smoke tests confirm immediate appearance updates on Android, Web, Windows, and Linux.

## Files affected by the product-spec update

- `spec/01_PROJECT_VISION.md` — state that users can personalize appearance.
- `spec/02_FUNCTIONAL_REQUIREMENTS.md` — add stable requirement `FR-016`.
- `spec/03_NON_FUNCTIONAL_REQUIREMENTS.md` — add stable requirement `NFR-013` for accessible contrast, cross-platform consistency, and verification.
- `spec/04_MVP_SCOPE.md` — include the feature in MVP and acceptance gates.
- `spec/05_ARCHITECTURE.md` — define the shared theme configuration boundary and palette tokens.
- `spec/06_DATABASE_SCHEMA.md` — define the allowed local `settings` keys and values.

`spec/07_FUTURE_ROADMAP.md` remains unchanged; custom color editing and cloud-synced preferences are outside MVP.
