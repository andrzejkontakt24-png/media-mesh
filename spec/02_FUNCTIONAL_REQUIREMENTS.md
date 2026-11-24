# Functional Requirements

This document defines observable MVP behavior. **MUST** is required for MVP acceptance; **SHOULD** is preferred unless a platform limitation is made visible to the user. Requirements apply to Android, Web, Windows, and Linux unless a platform is named.

## FR-001 — Connect and manage Google accounts

The user MUST be able to connect multiple Google accounts using the supported Google OAuth flow, view their connection state, refresh account metadata, and disconnect an account.

**Acceptance criteria**

- A successful authorization adds the account identity without storing access or refresh tokens in the application database.
- The UI identifies each account consistently (at minimum by the Google account email) and shows authorization/connection errors without exposing tokens.
- A user can retry an expired or revoked authorization without losing local library records.
- Disconnecting an account stops its queued work and removes its credentials from platform secure storage. It MUST NOT delete Drive files or local originals.

## FR-002 — Inspect storage and select a destination account

The client MUST show the known storage/quota state for each connected account and explain when the information is stale, unavailable, or insufficient.

**Acceptance criteria**

- Before a new upload, the client considers only connected accounts whose known remaining quota can hold the file.
- When several accounts qualify, automatic placement selects the account with the greatest known free space.
- When online, the client refreshes quota before automatic placement. If refresh fails, previously cached quota is advisory and is not sufficient for automatic placement.
- The user can override automatic placement. If quota is unknown or stale, the user may explicitly choose a connected account after being warned that the upload may be rejected.
- If quota is unknown, stale, unavailable, or insufficient on all accounts, the client MUST NOT claim that an upload will succeed. It reports the reason and offers refresh, retry, or a warned manual choice.
- A remote quota rejection is reported as an upload failure, refreshes quota state when possible, and does not discard the queued item.

## FR-003 — Create and use a managed Drive folder

Each connected Google account MUST have a visible `MediaMesh` folder. MediaMesh may list and manage only files in that folder.

**Acceptance criteria**

- The client reuses the MediaMesh folder it previously created or finds within the authorized account; otherwise it creates one.
- A same-named folder is not sufficient proof that it is app-managed. If ownership cannot be verified, ask the user to confirm adoption or create a separate managed folder before indexing or writing files.
- The folder ID is stored locally against the account and validated before use.
- MediaMesh does not scan or change files outside the managed folder, including existing Drive photos outside that folder.
- A missing or inaccessible folder is reported and repaired only by creating/selecting a managed folder, never by broad Drive cleanup.

## FR-004 — Discover media sources

The client MUST support source selection appropriate to each MVP platform: selected device media/folders on Android, selected folders on Windows and Linux, and explicit file/folder selection on Web.

**Acceptance criteria**

- Source permissions are requested only after the user initiates source setup; denied/revoked access is explained and can be retried.
- Accept files the platform identifies as photos or videos, including RAW photo files. Store original bytes without conversion or recompression; preview/playback is best-effort and may use a placeholder when the platform cannot decode a format.
- The client discovers eligible photo and video files without modifying or moving them. MIME type and platform media classification are hints; they do not authorize execution or mutation of a file.
- Windows and Linux monitor selected folders while running and perform a rescan after restart. Web uploads require explicit user selection and do not imply persistent background monitoring.
- Android backup work uses platform-supported background scheduling and exposes when OS restrictions defer execution.
- A disappeared or unreadable source item is marked as needing attention; its Drive copy and other source items remain unchanged.

## FR-005 — Back up new or changed media

The client MUST offer one-way backup of eligible source items to the managed Drive folders. The source remains untouched.

**Acceptance criteria**

- A user can start, pause where the platform/API supports it, retry, and inspect queued/running/completed/failed work.
- Each item is uploaded to the account selected by FR-002, unless the user chose an eligible account manually.
- Upload progress and the destination account are visible.
- A changed source file is fingerprinted again and treated as new content for backup. MediaMesh MUST NOT delete or overwrite the previous managed copy automatically; any replacement/retention choice must be explicit.
- Network, quota, permission, and source-file failures retain enough job state for a safe retry and show an actionable error.

## FR-006 — Resume interrupted synchronization

The client MUST persist upload jobs and recover them after application restart, network loss, or an API interruption.

**Acceptance criteria**

- A recovered job is checked against the source identity and content fingerprint before it resumes.
- Where the Drive API supports resumable upload, the client resumes the existing upload session; if it is no longer valid, it safely starts a new session.
- Retrying a job MUST NOT create a second managed file when the prior request may already have succeeded; the client reconciles using the stored Drive file ID or content fingerprint before creating another file.
- Backoff for transient failures is bounded and does not busy-loop. Permanent authorization, permission, invalid-source, or quota errors wait for user action.

## FR-007 — Detect exact-content duplicates

The client MUST detect byte-for-byte identical media across the connected MediaMesh-managed folders and the current source batch using SHA-256 fingerprints.

**Acceptance criteria**

- Identical content is matched regardless of filename or folder.
- Same-name files with different bytes, and visually similar files with different bytes, remain separate items.
- When a matching managed item is known before upload, the client links the source item to that item and skips the upload.
- Clients have independent local databases and may upload the same new item concurrently. After reconciliation, duplicate matches are reported; MediaMesh MUST NOT silently delete a remote copy.
- A fingerprint is computed from file content, not inferred from names, timestamps, or image metadata.

## FR-008 — Browse the unified gallery

The client MUST present one library view of media known from all connected managed folders.

**Acceptance criteria**

- Each item exposes its filename, media type, size, capture date when available, backup state, and owning Google account.
- Missing metadata has a stable fallback (for example, file creation time for display) and is not presented as verified capture time.
- The gallery uses bounded paging/virtualization and stable ordering; it MUST NOT load all 500,000 supported items into memory at once.
- Account-level filtering and a visible loading/empty/error state are available.
- A stale local listing is labeled as cached and can be refreshed when online.

## FR-009 — Download or restore media

The user MUST be able to explicitly download a managed item to a destination on the current device.

**Acceptance criteria**

- The user chooses the item and, where supported, a destination. Download never runs as an implicit consequence of browsing.
- The client verifies downloaded content against its known SHA-256 fingerprint when available.
- A name collision is resolved by asking the user or choosing a non-destructive unique name; the default behavior MUST NOT overwrite an existing local file.
- A failed or interrupted download is reported and can be retried without affecting the Drive copy.

## FR-010 — Generate and cache thumbnails

The client SHOULD generate and cache previews for media formats it can decode on the current platform.

**Acceptance criteria**

- Thumbnail generation is separate from original-file upload; originals are uploaded byte-for-byte and are never recompressed.
- Unsupported or corrupt media uses a safe type-specific placeholder and an actionable preview error; the gallery remains usable.
- Thumbnail work is bounded and can be regenerated or evicted without deleting original media or Drive files.
- Web preview support may depend on the browser's codec support; the UI MUST distinguish unavailable preview from missing media.

## FR-011 — Search and filter

The client MUST support local search and filtering over indexed library metadata.

**Acceptance criteria**

- Search matches filenames; filters include media type, connected account, backup state, and available capture-date ranges.
- Filename matching is case-insensitive and uses locale-independent Unicode normalization so results are stable across platforms.
- Search works against the cached local database while offline and clearly indicates that remote results may be stale.
- Search does not upload filenames, media metadata, or query text to a MediaMesh service; no such service exists in MVP.

## FR-012 — Operate with limited connectivity

The client MUST provide the offline behavior defined by NFR-004.

**Acceptance criteria**

- Cached library metadata and cached thumbnails remain browsable offline.
- Eligible upload requests can be queued persistently and start when connectivity returns, provided the source is still accessible.
- Drive listing refreshes, OAuth authorization, quota checks, uploads, and downloads wait for connectivity.
- If a queued source is no longer available, the job is held and the user is asked to reselect it; the client does not silently drop it.

## FR-013 — Preserve local originals and prevent destructive sync

The MVP MUST be a one-way backup system and MUST NOT propagate deletions.

**Acceptance criteria**

- Removing a local source file does not delete its Drive copy.
- A Drive item deleted outside MediaMesh is marked missing in the cached library after reconciliation; the source is not deleted.
- Disconnecting an account, clearing a cache, or removing a monitored source does not delete managed Drive files.
- Any future remote-delete/cleanup function requires a separate explicit confirmation flow and is outside MVP.

## FR-014 — Report operational state and recoverable errors

The client MUST make background and user-triggered work understandable and recoverable.

**Acceptance criteria**

- Every queued item has a durable state and, on failure, a user-safe category/message and retryability.
- Logs exclude tokens, OAuth callback values, raw media contents, and sensitive local paths unless the user explicitly exports a diagnostic package with redaction.
- The client distinguishes authentication, permission, quota, network, unsupported-media, source-unavailable, and storage errors.
- Successful recovery clears the corresponding active error while retaining enough local history for the user to understand the result.

## FR-015 — Provide English and Polish UI

The MVP MUST provide complete English and Polish user interfaces on all four platforms.

**Acceptance criteria**

- The application selects a supported language from the operating-system/browser preference on first launch and provides an in-app language setting.
- If the detected system language is not supported, the application falls back to English. A user-selected language is persisted locally and takes precedence over later system-language changes.
- All user-visible navigation, controls, onboarding, empty/loading states, progress, errors, permission explanations, and recovery guidance have English and Polish translations.
- User-visible dates, numbers, and sizes use locale-appropriate formatting while retaining unambiguous UTC values in stored data.
- Translations are held in localization resources, not hard-coded into UI logic. Missing translations are caught by a localization test and do not render untranslated resource keys to users.

## FR-016 — Personalize application appearance

The user MUST be able to choose an appearance mode (`System`, `Light`, or `Dark`) and one of four preset color palettes (`Ocean`, `Forest`, `Violet`, or `Sunset`) in application settings. The defaults are `System` mode and the `Ocean` palette. Every palette MUST provide coordinated light and dark variants.

**Acceptance criteria**

- A changed preference is applied immediately and persists across application restarts on the current installation.
- In `System` mode, the application follows the operating system or browser color-scheme preference and updates when that preference changes. Explicit `Light` and `Dark` modes override system changes.
- The selected palette is used with the resolved light or dark mode. Application-owned UI uses semantic design tokens rather than hard-coded palette colors.
- Appearance preferences are client-local and are not uploaded to or synchronized through Google Drive.
- Missing or invalid stored values resolve safely to `System` and `Ocean` without preventing startup or changing unrelated settings.
- On Android, Web, Windows, and Linux, the user can select and verify every mode and palette. Palette changes MUST NOT tint, edit, convert, or otherwise change the appearance or bytes of source or backed-up media.
- Success, warning, and error states retain their semantic meaning across palettes and are not communicated by color alone.
