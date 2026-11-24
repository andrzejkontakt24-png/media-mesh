# Appearance Preferences Specification Update Plan

> **For agentic workers:** Execute this plan task by task and verify each documentation deliverable before moving on.

**Goal:** Add an implementation-ready MVP requirement for theme mode and preset color palette preferences across all supported platforms.

**Architecture:** Keep `spec/01` through `spec/06` as the product source of truth and add one stable functional requirement plus one cross-cutting quality requirement. Persist `theme_mode` and `color_palette` in the existing client-local settings store; do not introduce backend or Drive synchronization.

**Tech Stack:** English Markdown product specifications; product UI target is Flutter on Android, Web, Windows, and Linux.

## Global Constraints

- Theme mode: `System`, `Light`, or `Dark`; `System` is the default.
- Palette: `Ocean`, `Forest`, `Violet`, or `Sunset`; `Ocean` is the default.
- Each palette defines coordinated light and dark variants.
- Appearance preferences are stored locally on the current installation and are not synchronized through Google Drive.
- Changes affect application UI only, never the appearance or bytes of user media.
- Preserve semantic status colors and meet accessibility contrast requirements for every mode/palette combination.
- Custom color editing and cloud-synced preferences are outside MVP.

---

### Task 1: Add appearance preference behavior and architecture

**Files:**
- Modify: `spec/01_PROJECT_VISION.md`
- Modify: `spec/02_FUNCTIONAL_REQUIREMENTS.md`
- Modify: `spec/04_MVP_SCOPE.md`
- Modify: `spec/05_ARCHITECTURE.md`

**Interfaces:**
- Consumes: Approved behavior in `docs/superpowers/specs/2026-11-23-appearance-preferences-design.md`.
- Produces: Stable requirement `FR-016`; MVP traceability; shared theme-resolution and design-token responsibilities.

- [ ] **Step 1: Add FR-016**

Add a functional requirement specifying Appearance settings with System/Light/Dark mode, four named palettes, System and Ocean defaults, immediate UI application, persistence across restart, local-only scope, system-change behavior, and no effect on user media. Acceptance criteria must cover invalid preference fallback and cross-platform behavior.

- [ ] **Step 2: Link the feature from the vision and MVP**

State that users can personalize the application appearance in the vision. Add an MVP table row referencing FR-016 and an acceptance gate that checks the settings on Android, Web, Windows, and Linux.

- [ ] **Step 3: Define the architecture boundary**

Document an application-level theme configuration/resolution responsibility. It combines saved mode, current system brightness, and selected palette; all application-owned components consume semantic design tokens rather than hard-coded palette colors. Specify that palette changes do not tint media and do not change semantic success/warning/error meaning.

### Task 2: Add persistence, accessibility, and verification criteria

**Files:**
- Modify: `spec/03_NON_FUNCTIONAL_REQUIREMENTS.md`
- Modify: `spec/06_DATABASE_SCHEMA.md`

**Interfaces:**
- Consumes: FR-016 and the architecture contract from Task 1.
- Produces: Stable requirement `NFR-013`; valid `settings` keys `theme_mode` and `color_palette`.

- [ ] **Step 1: Add NFR-013**

Specify that all palette/mode combinations must meet the existing accessibility target, selected/focus/status states cannot rely on color alone, system mode responds to system brightness changes, and explicit modes override system changes. Require tests for all palette/mode combinations and smoke coverage on all four platforms.

- [ ] **Step 2: Extend the settings schema**

Define `theme_mode` allowed values as `system`, `light`, `dark`, default `system`; define `color_palette` allowed values as `ocean`, `forest`, `violet`, `sunset`, default `ocean`. State that invalid/missing values fall back safely, values remain client-local, and migrations preserve unrelated settings.

### Task 3: Review specification consistency

**Files:**
- Review: `spec/01_PROJECT_VISION.md` through `spec/06_DATABASE_SCHEMA.md`

- [ ] **Step 1: Verify requirement traceability**

Confirm FR-016 is present exactly once and referenced from the MVP scope; confirm NFR-013 is present exactly once and linked to appearance verification; confirm defaults and allowed values match across all documents.

- [ ] **Step 2: Verify wording and repository diff**

Confirm the edited product specs remain in English, contain no placeholders, document local-only preferences and media non-modification consistently, and pass `git diff --check`.
