# Product Roadmap

The roadmap is directional, not an MVP commitment. Each phase depends on the security, reliability, and cross-platform foundations defined in `02_FUNCTIONAL_REQUIREMENTS.md` through `06_DATABASE_SCHEMA.md`. No roadmap item may weaken the MVP's source-preservation, credential-security, or managed-folder boundaries.

## V1 — MVP: Multi-account Google Drive backup

**Status:** Current product target.

Deliver the complete MVP on Android, Web, Windows, and Linux: multiple Google accounts, quota-aware account selection, platform-appropriate source intake, one-way backup, resumable/recoverable jobs, exact-content duplicate detection, a unified local gallery/search, thumbnails with safe fallbacks, explicit restore, and offline cached browsing/queueing.

**Exit condition:** All acceptance gates in `04_MVP_SCOPE.md` pass on every supported platform, including the 500,000-photo dataset test and security validation of OAuth scopes/cross-client Drive access.

## V2 — Albums and tags

Add user-defined collections and tags over the existing local library.

**Dependencies:** Stable local media identity, migrations, search/filter performance, and a product decision about whether album/tag metadata remains client-local or is represented in Drive. Until that decision is made, V2 must not imply cross-device metadata synchronization.

## V3 — AI-assisted organization

Evaluate optional AI features such as local classification or semantic search.

**Dependencies and gates:** Define whether processing is on-device or uses an external service; obtain explicit opt-in; document data sent off-device, retention, cost, supported platforms, deletion, and failure behavior; test accessibility and user correction. No media or metadata may be sent to a service without explicit product/privacy approval.

## V4 — Sharing

Evaluate controlled sharing of selected managed media.

**Dependencies and gates:** Define recipient authorization, revocation, link visibility, Drive permission ownership, abuse/reporting behavior, and whether a backend is required. Sharing is not implied by the no-backend MVP and must not broaden existing account scopes silently.

## V5 — Additional storage providers

Evaluate OneDrive, Dropbox, and S3-compatible storage through provider adapters.

**Dependencies and gates:** Define per-provider identity, quota semantics, OAuth/credential protection, folder/bucket isolation, upload resumption, duplicate fingerprint metadata, cost, and deletion policy. A provider adapter may be added only when it can meet the same source-preservation and error-recovery guarantees.

## Ordering and change control

- V1 is the only committed scope in the current specification.
- V2–V5 require their own detailed requirements and acceptance criteria before implementation.
- Roadmap version numbers express intended order, not delivery dates.
- If an item requires a backend, broader Drive permissions, remote deletion, or data transfer to an AI service, document that scope change and get explicit product/security approval before implementation.
