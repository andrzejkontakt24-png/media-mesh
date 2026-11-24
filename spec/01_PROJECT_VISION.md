# MediaMesh Photos Hub — Product Vision

## Product summary

MediaMesh Photos Hub is a cross-platform personal photo and video backup application. It presents media stored in multiple Google Drive accounts as one library and automatically chooses an eligible account for each new backup.

The MVP targets Android, Web, Windows, and Linux. Every client connects directly to Google Drive and maintains its own local database. There is no MediaMesh server and no shared cloud database. Google Drive stores managed media; the local database stores the metadata, cache, and work queue needed by that installation.

The MVP interface is available in English and Polish.
Users can personalize the application appearance with a system/light/dark mode and a preset color palette. These preferences are local to the current installation.

## Problem

People with large personal photo and video collections may exceed the storage capacity of one Google account. Managing several accounts separately makes it hard to know where a file was backed up, avoid repeated uploads, browse one collection, or restore a file.

## Product outcome

Users can configure multiple Google accounts, select media sources, and back up their media into a dedicated `MediaMesh` folder in those accounts. They can browse and search the combined managed library without needing to know which account owns each file, and explicitly download a file when they want a local copy.

## Target user and primary journey

The MVP serves an individual who owns the connected Google accounts and the source media. The primary journey is:

1. Connect one or more Google accounts.
2. Grant the platform-appropriate permission to select media sources.
3. Let MediaMesh identify new media, calculate exact-content fingerprints, and select an eligible Drive account.
4. Review backup progress and any items needing attention.
5. Browse or search the managed library and explicitly restore selected items.

## Product principles

- **Preserve originals:** Uploading never removes, edits, or moves the source file. A missing or deleted Drive copy never deletes a local original.
- **One-way backup:** The MVP backs up to Drive. It is not a bidirectional file synchronization or conflict-resolution system.
- **User-owned storage:** Each client talks directly to Google Drive. MediaMesh does not receive or proxy media through a central service.
- **One managed namespace:** MediaMesh may manage only files in its dedicated visible `MediaMesh` folder in each connected Drive account. It must not scan, move, modify, or delete unrelated Drive files.
- **Transparent placement:** Explain which account was selected and why; allow manual account selection and report quota uncertainty.
- **Offline-tolerant:** A cached library remains browsable and eligible uploads can wait in a persistent queue until the network and source file are available.
- **Platform-appropriate:** Reuse product behavior across platforms while using each platform's supported source picker, background-work limits, secure storage, and browser capabilities.
- **Personalizable without altering media:** Users can choose an appearance mode and preset palette; appearance settings affect application-owned UI only, never the appearance or bytes of user media.

## MVP platforms and intake model

| Platform | Media intake | MVP behavior |
|---|---|---|
| Android | User-selected device folders/media sources | Discover new eligible media and enqueue one-way backups. Do not promise uninterrupted background execution when Android restricts it. |
| Windows | User-selected folders | Monitor selected folders while the application is running and scan again when it restarts. |
| Linux | User-selected folders | Monitor selected folders while the application is running and scan again when it restarts. |
| Web | Explicit file or folder selection/upload | Do not imply persistent background folder monitoring after the browser session closes. |

## Scope boundaries

The MVP includes multiple Google accounts, quota-aware account selection, managed-folder storage, media discovery, resumable/retryable uploads, exact-content duplicate detection, a unified cached gallery, search, thumbnails or a safe preview fallback, explicit downloads, and the offline behavior defined in the functional and non-functional requirements.

The MVP does not include two-way synchronization, automatic deletion or cleanup of Drive files, a hosted MediaMesh account/service, cross-device synchronization of the local database, sharing, AI tagging/search, albums/tags, or non-Google storage providers. These are possible later roadmap items, not implicit MVP promises.

## Product terminology

- **Managed media:** A photo or video stored in a MediaMesh-managed Drive folder and represented in the local library.
- **Source item:** A file discovered in a user-selected device folder or explicitly selected in the Web client.
- **Backup:** Creating or updating a managed Drive copy while leaving the source item untouched.
- **Restore/download:** A user-initiated transfer of a managed Drive copy to a destination on the current device.
- **Exact-content duplicate:** A byte-for-byte identical media file, identified by SHA-256. Matching names, dates, or visual similarity alone do not make files duplicates.
- **Client-local state:** Metadata, thumbnails, settings, and queued work stored on one installation. It is not synchronized to other MediaMesh clients.

## Related specifications

- Functional behavior and acceptance criteria: [02_FUNCTIONAL_REQUIREMENTS.md](02_FUNCTIONAL_REQUIREMENTS.md)
- Quality attributes and measurable limits: [03_NON_FUNCTIONAL_REQUIREMENTS.md](03_NON_FUNCTIONAL_REQUIREMENTS.md)
- Included and excluded release scope: [04_MVP_SCOPE.md](04_MVP_SCOPE.md)
- Component boundaries and data flow: [05_ARCHITECTURE.md](05_ARCHITECTURE.md)
- Persistent local entities: [06_DATABASE_SCHEMA.md](06_DATABASE_SCHEMA.md)
- Post-MVP sequence: [07_FUTURE_ROADMAP.md](07_FUTURE_ROADMAP.md)
