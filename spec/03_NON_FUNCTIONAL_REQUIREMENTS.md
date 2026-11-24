# Non-Functional Requirements

These requirements are MVP release constraints. Requirement identifiers are stable and are referenced by the MVP scope, architecture, and data model.

## NFR-001 — Library scale and bounded resource use

- Each client MUST support a local index of at least 500,000 photo items.
- Library queries MUST use indexed paging with a maximum page size of 100 items. Gallery rendering MUST virtualize results and load thumbnails on demand.
- Scanning, hashing, thumbnail generation, and uploads MUST process bounded batches; the implementation MUST NOT hold an entire collection or complete media files in memory.
- Acceptance: load a generated 500,000-photo metadata dataset, browse/search/filter multiple pages, and verify that memory usage does not grow in proportion to all result objects being rendered at once.
- The same schema and query behavior apply to video items; the explicit scale floor is 500,000 photos.

## NFR-002 — Supported platforms and behavior

- The MVP MUST build and run on Android, Web, Windows, and Linux using Flutter and platform-specific adapters where required.
- Platform restrictions MUST be surfaced rather than represented as successful background work. In particular, browser folder selection is user-initiated, and mobile/desktop background execution is subject to OS scheduling.
- Acceptance: each platform has an automated or documented smoke test for startup, account setup, media selection, library browsing, and an upload/download flow.

## NFR-003 — Local-first persistence

- Each installation MUST keep its own durable local database for account metadata, library index, source configuration, thumbnail references, queued work, and sync history.
- Local state is not a cross-device synchronization service. Clients converge on the Drive-managed media through direct Drive API reads.
- The local database MUST support schema migrations. A failed migration MUST preserve or safely recover the user's queued work and cached data; it MUST NOT silently reset the database.

## NFR-004 — Offline and recovery behavior

- Cached library metadata and cached thumbnails MUST remain available without a network connection.
- Upload jobs MUST be persisted before network work begins and survive process termination and restart.
- Drive-dependent operations MUST wait for connectivity and resume through reconciliation when connectivity returns.
- A transient failure MUST not silently discard a source item or report a false completed state.

## NFR-005 — Backup integrity and non-destructive behavior

- Original media bytes MUST be uploaded unchanged. MediaMesh MUST NOT recompress, edit, move, or delete source media.
- SHA-256 MUST be used for exact-content matching and, when a known digest is available, post-download integrity verification.
- Upload state changes MUST be idempotent or reconciled before retrying an ambiguous request. Multiple independent clients may race; that limitation MUST be represented honestly, with duplicate discoveries reported rather than silently deleted.
- A Drive-side deletion MUST never cause local source deletion.

## NFR-006 — Authentication and authorization security

- All Google account access MUST use Google-supported OAuth 2.0 flows appropriate to the target platform. Distributed clients MUST NOT contain an OAuth client secret.
- Tokens MUST be kept out of the application database, logs, crash reports, analytics, and exported diagnostics. Native clients MUST use the platform secure credential store. Web clients MUST not persist bearer/refresh tokens in ordinary local storage; expired browser authorization may require reauthorization.
- Request the least-privilege Google Drive access that supports the managed-folder product. Verify access across Android/Web/Windows/Linux OAuth client registrations before release; never silently escalate to broad Drive access when an account or file is unavailable.
- Revoked, expired, or denied authorization MUST fail closed and provide a reauthorization path.

## NFR-007 — Local data protection and privacy

- The local database and sensitive cached metadata MUST be encrypted at rest on native platforms; encryption keys MUST be protected by the platform credential/key store.
- Web persistence MUST use browser cryptography and origin-bound storage where available. Documentation and UI MUST not claim that browser-side encryption protects data from same-origin script compromise or a compromised browser profile.
- If a platform secure store is unavailable, the client MUST explain the limitation and use a defined secure recovery/unlock path or refuse to persist credentials; it MUST NOT silently fall back to plaintext token storage.
- MediaMesh has no server in MVP. Media content, file names, search terms, and library metadata MUST not be sent to a MediaMesh-operated service.

## NFR-008 — Google Drive integration constraints

- Only a dedicated visible `MediaMesh` folder per connected account is in scope. All list, read, and write operations MUST be scoped to managed files and the permissions required to create/access that folder.
- A folder with the expected name but without a verifiable MediaMesh marker MUST NOT be indexed or modified until the user explicitly adopts it.
- MediaMesh-created folders and files MUST carry `appProperties.mediaMeshManaged=true` and `appProperties.mediaMeshSchemaVersion=1`; files MUST also carry `appProperties.mediaMeshSha256` as 64 lowercase hexadecimal characters when supported. Missing/invalid markers MUST NOT be treated as proof of ownership or content equality.
- API quota, storage quota, OAuth scope, cross-client app access, and resumable-upload behavior MUST be tested against the actual Google Cloud project before public release.
- Rate limits and transient service errors MUST use bounded backoff and must remain visible as recoverable work.

## NFR-009 — Accessibility and usability

- Web and desktop interfaces MUST target WCAG 2.2 AA for keyboard operation, focus visibility, semantic controls, contrast, and accessible status/error announcements.
- Android interfaces MUST provide accessible labels, scalable text, and screen-reader-readable progress and error state.
- Progress, unknown quota, cached/stale data, unsupported preview, and authorization state MUST have text labels and not rely on color alone.

## NFR-010 — Search and ordering consistency

- The library MUST use deterministic ordering with a stable tie-breaker so paging does not skip or repeat items when metadata is unchanged.
- Missing capture dates MUST remain distinguishable from verified capture dates, even when a fallback date is used for display.
- Filename search MUST use case-insensitive, locale-independent Unicode normalization consistently on all platforms.
- Search results are local-index results. The UI MUST indicate when a cache has not been refreshed from Drive.

## NFR-011 — Diagnostics and error handling

- Errors MUST be classified at least as authorization, permission, quota, network/API, source unavailable, unsupported/corrupt preview, local storage, and migration failures.
- Diagnostic output MUST be actionable and redacted. It MUST NOT include OAuth tokens, raw file contents, or unredacted sensitive local paths.
- Automatic retries MUST be bounded; permanent errors MUST wait for user action rather than retrying indefinitely.

## NFR-012 — Verification and release quality

- Unit tests MUST cover account selection, exact-content matching, job state transitions, and deletion-safety rules.
- Persistence tests MUST cover migrations, app restart recovery, and interrupted/ambiguous upload reconciliation.
- Integration tests MUST cover Google Drive folder scoping, quota/error responses, resumable transfer, and access between platform OAuth clients in a controlled test project.
- Cross-platform tests MUST exercise supported source selection and storage behavior on each target. Platform-specific unsupported behavior must have a tested, user-visible fallback.
- CI or documented release checks MUST run formatting, static analysis, tests, and build smoke checks for the four declared targets.
- Localization tests MUST verify that every user-visible resource required by FR-015 exists in both English and Polish.

## NFR-013 — Appearance accessibility and consistency

- Every light and dark variant of every palette MUST meet the applicable contrast and accessibility targets in NFR-009. Focus, selected, success, warning, and error states MUST remain distinguishable and MUST NOT rely on color alone.
- `System` mode MUST update when the operating system or browser color-scheme preference changes. Explicit `Light` or `Dark` mode MUST remain unchanged by system preference changes.
- Automated theme tests MUST cover all four palettes in both resolved light and dark variants, all three mode selections including system-mode changes, and fallback from missing or invalid persisted values.
- Cross-platform smoke coverage MUST verify preference selection, persistence after restart, live application, and media non-modification on Android, Web, Windows, and Linux.
