# MVP Scope

## Release objective

Deliver a usable, one-way photo and video backup application that can connect multiple Google Drive accounts and present their MediaMesh-managed folders as one client-local library on Android, Web, Windows, and Linux.

## Included capabilities

| Capability | Requirement coverage | MVP acceptance |
|---|---|---|
| Google account connection | FR-001 | Connect, list, reauthorize, and disconnect multiple accounts without exposing credentials. |
| Storage visibility and placement | FR-002 | Show known quota state; select the eligible account with most free space; support manual override and clear unknown/full-quota handling. |
| Managed Drive folders | FR-003 | Use a visible `MediaMesh` folder per account and do not touch files outside it. |
| Source selection and discovery | FR-004 | Android selected device sources; Windows/Linux selected-folder monitoring while running plus startup scan; explicit Web file/folder selection; accept platform-identified photos/videos including RAW and preserve original bytes. |
| One-way backup | FR-005, FR-013 | Upload original media without changing or deleting local originals; never propagate deletion. |
| Resumption and retry | FR-006 | Persist jobs, recover after restart/connectivity loss, reconcile ambiguous upload results, and surface non-retryable errors. |
| Exact-content deduplication | FR-007 | Use SHA-256 across known managed folders; skip known duplicates and report concurrent duplicate discoveries without automatic remote deletion. |
| Unified gallery and search | FR-008, FR-011 | Browse/filter/search locally indexed items from all connected accounts with paged/virtualized results. |
| Thumbnails/previews | FR-010 | Generate/cache previews when supported; show a placeholder when not supported without blocking library use. |
| Explicit restore/download | FR-009 | Download selected items, verify known fingerprints, and avoid overwriting local files by default. |
| Offline experience | FR-012 | Browse cached metadata/thumbnails and queue eligible uploads; defer Drive-dependent actions until online. |
| User-visible status/errors | FR-014 | Show work progress, destination, actionable error category, retryability, and recovery outcome. |
| Localization | FR-015 | Provide complete English and Polish UI, OS/browser language detection, and an in-app language setting. |
| Appearance preferences | FR-016, NFR-013 | Choose System/Light/Dark mode and Ocean/Forest/Violet/Sunset palette, apply immediately, persist locally, and keep media unchanged. |
| Client-local persistence | NFR-003, NFR-004 | Persist metadata and jobs independently on each installation; apply platform-appropriate local protection. |

## Platform delivery

All four platforms are part of MVP, not roadmap:

- **Android:** selected device media/folders, platform-scheduled backup, gallery, search, and explicit restore.
- **Web:** Google OAuth through a browser-appropriate flow, explicit file/folder upload, local cached library, gallery/search, and explicit download. No persistent background folder watching is promised.
- **Windows and Linux:** Google OAuth through a desktop-appropriate flow, selected-folder monitoring while running, startup rescans, gallery/search, and explicit restore.

Use Flutter as the shared implementation target. Platform-specific authentication callbacks, secure storage, file access, database persistence, background scheduling, thumbnail decoding, and download behavior may use adapters. The platform-specific constraints and required fallbacks are in `05_ARCHITECTURE.md`.

## MVP acceptance gate

The MVP is acceptable only when:

1. The release builds and launches on Android, Web, Windows, and Linux using the documented setup instructions.
2. A user can connect multiple Google accounts and MediaMesh stores managed files only in each account's dedicated folder.
3. Source discovery follows the platform intake model, accepts platform-identified photos/videos (including RAW), and never modifies, converts, moves, or deletes the original.
4. An eligible file is routed to the account with the most known free space; unknown or insufficient quota produces an actionable outcome rather than a false success.
5. Uploads survive network interruption/application restart without silent job loss or an avoidable duplicate.
6. Exact-content matches are recognized across managed folders; concurrent client uploads are reconciled without automatic remote deletion.
7. The unified gallery and local metadata search work with cached data offline and are tested with a 500,000-photo dataset without loading the complete dataset into memory.
8. A user can explicitly download a file without overwriting an existing local file by default.
9. Unsupported previews, revoked access, missing sources, quota failures, network failures, and inaccessible storage have visible recovery paths.
10. Every user-visible flow and error covered by MVP acceptance is available in English and Polish on all four platforms.
11. Appearance settings work on Android, Web, Windows, and Linux; mode and palette choices persist locally, all palette variants meet accessibility requirements, and media remains unchanged.
12. Tests cover business rules, persistence, localization, appearance variants, platform-dependent adapters, OAuth/Drive integration boundaries, and the critical cross-platform upload/download flows.
13. No requirement implies a hosted MediaMesh backend, two-way sync, AI processing, sharing, or automatic remote deletion.

## Explicitly out of scope

- Two-way sync, remote change propagation to local sources, or deletion propagation.
- Automatic cleanup/deletion of remote files or deduplication by deleting a cloud copy.
- Hosted accounts, server-side metadata, server-side upload proxy, or cross-device synchronization of local state.
- Album/tag management, AI classification or semantic search, sharing/collaboration, and non-Google providers.
- Guaranteed background processing when the operating system or browser does not permit it.
- Guaranteed preview/playback for every image/video codec on every browser/desktop platform.
- Custom color editing or synchronization of appearance preferences through Google Drive.

## Release dependencies

- Register and configure Google OAuth clients/redirects for the supported platform types; obtain any required Google verification before public release.
- Confirm Drive API scopes and app access behavior against Google's current authorization/verification requirements.
- Implement and test a platform-compatible local encrypted persistence strategy and credential storage. Web security limitations must be disclosed.
- Validate the current Google Drive API quota reporting, resumable upload, and app-managed folder behavior before claiming storage guarantees.

## Source of truth

Functional acceptance criteria live in `02_FUNCTIONAL_REQUIREMENTS.md`; quality limits in `03_NON_FUNCTIONAL_REQUIREMENTS.md`; architecture and platform constraints in `05_ARCHITECTURE.md`; local entities in `06_DATABASE_SCHEMA.md`. If a roadmap description conflicts with MVP scope, this document and the numbered MVP requirements take precedence.
