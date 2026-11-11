# Aoide — Project Briefing

> **Working name:** Aoide  
> **Project type:** Private, self-hosted music library management and discovery application  
> **Status:** Initial concept and research briefing  
> **Repository:** `aoide` (proposed)

## 1. Project vision

Aoide is a private, self-hosted application for managing, organizing, enriching, discovering and maintaining a local digital music collection.

The initial focus is an existing collection of MP3 files. Aoide should scan and analyze the collection, identify incomplete or incorrect metadata, match files to authoritative music databases, write consistent tags, retrieve artwork, and organize files into a predictable directory structure.

Beyond library maintenance, Aoide should provide a music discovery and acquisition workflow inspired by the *arr ecosystem. It must treat **individual tracks as first-class objects**, not merely as parts of albums. Users should be able to search for, monitor, acquire, import, rate and manage individual recordings as well as complete albums.

Aoide should also provide a personal music knowledge and discovery layer: artist and band information, discographies, album and track details, personal ratings, recommendations, playlists, release notifications and concert information.

Aoide is not intended to become a streaming service. Its primary responsibility is the user's own local music collection and the metadata, artwork, history and discovery workflows around it.

## 2. Name and positioning

**Aoide** (Ancient Greek: Ἀοιδή) means “song” and is the name of a Muse associated with song in Greek mythology.

The name is short, distinctive and thematically appropriate. It also complements **Mneme**, the related private project for movie and TV management.

Suggested positioning:

**Aoide — Personal Music Library & Discovery**

The name is a working choice, not a completed legal, trademark, domain or repository-availability clearance. Check GitHub, package registries, domains and existing software/product names before public release.

## 3. Product principles

1. **Local library first:** the user's files remain the primary assets and source of truth for file presence.
2. **Track-first and album-aware:** individual recordings and complete releases are equally important workflows.
3. **Metadata provenance:** retain source, identifier, retrieval time and confidence for externally sourced data.
4. **Non-destructive by default:** never rename, move, overwrite or delete media without a reviewable plan and explicit authorization.
5. **Human approval for ambiguous matches:** fingerprint and metadata matching may propose candidates, but uncertain matches require review.
6. **Provider independence:** external metadata, artwork, event, indexer and download services must be accessed through replaceable adapters.
7. **Idempotent operations:** rescans and imports should not create duplicate database records or repeat completed file operations.
8. **Auditable changes:** retain operation history and provide recovery information for tag and filesystem changes.
9. **Self-hosted and privacy-conscious:** keep credentials local, minimize external data disclosure and make outbound integrations configurable.
10. **Incremental delivery:** begin with reliable library management before adding acquisition and recommendation complexity.

## 4. Scope and functional domains

### 4.1 Library scanning and inventory

- Configure one or more library roots.
- Recursively scan directories and identify supported audio files.
- Initially prioritize MP3; design the domain model so other formats can be added later.
- Read existing tags and technical properties.
- Record path, size, duration, bitrate, sample rate, channels, codec and file hash.
- Detect missing tags, malformed tags, inconsistent values and suspicious filenames.
- Detect exact duplicates by cryptographic hash.
- Optionally detect likely duplicate recordings using identifiers, duration and acoustic fingerprints.
- Track file availability and changes between scans.
- Support scheduled and manually triggered scans.

### 4.2 Metadata matching and enrichment

- Search metadata by filename, existing tags, artist/title, album, ISRC and MusicBrainz identifiers.
- Generate acoustic fingerprints for unidentified files.
- Match files to MusicBrainz recordings and releases where possible.
- Present multiple candidate matches with confidence and relevant evidence.
- Allow manual correction and linking.
- Preserve external identifiers, including MBIDs, AcoustIDs and ISRCs.
- Retain source provenance and timestamps for imported fields.
- Support field-level source priorities and conflict resolution.
- Never silently replace a trusted/manual value with a lower-confidence value.

### 4.3 Tag editing

- Read, edit and write ID3 tags.
- Support common fields: title, artist, album, album artist, track number, disc number, date/year, genre, composer, conductor, publisher/label, copyright, lyrics where legally and technically available, and MusicBrainz identifiers.
- Support multiple artists and featured artists without flattening relationships into ambiguous strings.
- Preserve unknown/custom tags where possible.
- Preview tag changes before writing.
- Write tags safely and verify the result after writing.
- Keep a recoverable snapshot or change record for modified tags.

### 4.4 File and folder organization

- Define configurable folder and filename templates.
- Example target structure (to be validated):
  - `Music/<Album Artist>/<Year> - <Album>/`
  - `<Track Number> - <Track Title>.mp3`
- Handle compilation albums, Various Artists, multi-disc releases, featured artists, classical music, live albums, special characters and duplicate titles.
- Preview the complete rename/move plan.
- Detect destination collisions and invalid paths.
- Support dry-run mode.
- Execute operations with a journal and rollback/recovery strategy.
- Never delete source files as an implicit side effect of importing or reorganizing.

### 4.5 Artwork and images

- Retrieve album artwork from the Cover Art Archive and other permitted providers.
- Retrieve artist images and other relevant images from sources whose API and licensing terms allow local caching.
- Embed album artwork in MP3 files where appropriate.
- Optionally maintain external artwork files alongside media.
- Support multiple image types (front, back, booklet, artist image) where metadata exists.
- Define image resolution, format, naming, deduplication and fallback rules.
- Retain source attribution and usage/licensing metadata where required.
- Do not assume that an image URL grants permission to download, embed or redistribute the image.

### 4.6 Music discovery and acquisition

Provide two equivalent discovery paths:

**Album/release workflow**
- Search artists, albums and releases.
- Monitor artists and selected releases.
- Track wanted, missing, queued, downloading, imported and failed states.
- Integrate with Lidarr where useful.

**Individual-track workflow**
- Search by track title, artist, recording and identifiers.
- Search for specific recordings and versions (studio, live, remix, remaster, edit).
- Add individual tracks to a wanted list.
- Search configured providers/indexers.
- Send selected results to configured download clients.
- Import, identify, tag and organize completed downloads.
- Avoid importing an incorrect version solely because its title matches.

Potential integrations to investigate:
- Lidarr: artist/album monitoring and existing automation.
- Prowlarr: indexer management and search integration.
- qBittorrent and/or SABnzbd: download clients.
- slskd: potential source for individual-track discovery/acquisition.
- Other providers only where access, terms and applicable law permit.

Keep search providers and download clients behind separate adapter interfaces. Aoide should not hard-code a single acquisition ecosystem.

### 4.7 Personal library, ratings and playlists

- Rate artists, albums and individual tracks (candidate scale: 1–10).
- Mark favourites.
- Add personal notes.
- Track listening status/history if a suitable player or listening-history integration is available.
- Create and edit playlists.
- Support M3U/M3U8 export and relative paths.
- Provide filters for unrated, recently added, missing metadata, favourites, genres and years.
- Provide a personal wishlist for artists, albums and tracks.
- Keep personal ratings separate from external community ratings.

### 4.8 Music knowledge and recommendations

- Artist and band profiles.
- Biographies, origin, active years and member relationships where data is available.
- Discographies and release histories.
- Album and recording details.
- Related and similar artists.
- Similar albums and tracks.
- Recommendations based on personal ratings and library content.
- Optional use of ListenBrainz recommendations and listening statistics.
- Clearly distinguish imported facts, provider-generated recommendations and Aoide's own recommendations.

### 4.9 Events and release notifications

- Track upcoming concerts for selected artists.
- Filter events by configurable geographic region and date range.
- Display venue, date, location and ticket/event links.
- Track upcoming releases and relevant discography changes.
- Provide a notification mechanism (in-app first; other channels later).
- Event provider selection depends on API access, licensing, coverage and hobby-project eligibility.

## 5. Domain model — initial proposal

The domain model must distinguish a musical work, a recording, a release and a physical/digital file. These are related but not interchangeable.

Candidate entities:

- **Artist:** person, band, orchestra or other credited artist.
- **ArtistCredit:** ordered credit for one or more artists, including join phrases and featured credits.
- **ReleaseGroup:** conceptual album/release grouping (e.g. an album independent of a particular edition).
- **Release:** a specific edition or publication with date, country, label, barcode and format.
- **Medium:** disc or medium within a release.
- **Track:** track position on a specific medium/release.
- **Recording:** the underlying recorded performance, which can appear on multiple releases.
- **AudioFile:** a local file representing a recording or a candidate recording.
- **Identifier:** external IDs such as MBID, AcoustID, ISRC and provider-specific IDs.
- **Genre/Tag:** normalized genres and user-defined tags.
- **Artwork:** image asset, type, dimensions, source, attribution and local storage reference.
- **Rating:** user's rating of an artist, release group, release or recording.
- **Playlist / PlaylistItem:** ordered user-curated collection of tracks.
- **ListeningEvent:** optional record of a play/listen event.
- **DownloadRequest / SearchResult / DownloadJob:** acquisition lifecycle.
- **FileOperation:** planned and completed filesystem/tag operations with status and recovery metadata.
- **Provider / ProviderRecord:** external source configuration and provenance.

Important modelling rules:
- A track on a release is not necessarily the same thing as a recording.
- A recording can appear on multiple releases.
- One recording may have multiple local files (different encodings or editions).
- A release may contain multiple media/discs.
- Artist credits can contain multiple artists and ordering/join information.
- A local file may remain unmatched until a user or matching workflow resolves it.
- External identifiers must be namespaced and unique within their provider, not treated as universal IDs.

## 6. External services and tools to evaluate

### 6.1 Metadata and artwork

| Service/tool | Potential role | Research points |
|---|---|---|
| MusicBrainz API | Primary artist, release, recording and relationship metadata | Rate limits, entity relationships, matching quality, caching and attribution |
| Cover Art Archive | Release artwork | Image types, resolution, availability, local caching terms |
| AcoustID | Fingerprint-to-recording matching | API key, quota/terms, match confidence and unmatched cases |
| Chromaprint / fpcalc | Local acoustic fingerprint generation | Packaging, supported formats, CPU cost |
| Discogs API | Supplementary release/edition metadata | API access, rate limits, image/data licensing and terms |
| ListenBrainz API | Listening statistics and recommendations | Authentication, recommendation coverage, privacy and API stability |
| Last.fm API | Similar artists, tags and supplementary discovery | API terms, data coverage, attribution and current access requirements |

### 6.2 Existing music and *arr ecosystem

| Component | Potential role | Research points |
|---|---|---|
| Lidarr | Album/artist monitoring and automation | API coverage, import workflow, limitations for individual tracks |
| Prowlarr | Indexer configuration and search | API usage, categories and provider compatibility |
| qBittorrent | Download client | API, categories, paths, completion handling |
| SABnzbd | Usenet download client | API, categories, completion handling |
| slskd | Potential individual-track source | API, reliability, legal use, metadata and automation |
| beets | Music library organization/tagging reference or optional integration | Plugin ecosystem, import model, database and CLI integration |
| MusicBrainz Picard | Reference implementation for matching/tagging/artwork | Reusable libraries versus process integration; avoid duplicating its functionality unnecessarily |

### 6.3 Audio and file-processing libraries

Evaluate:
- Mutagen for ID3 and audio metadata reading/writing.
- FFmpeg/FFprobe for media probing and optional transformations.
- Chromaprint/fpcalc for fingerprints.
- Hashing libraries for exact duplicate detection.
- A robust filesystem operation/journaling strategy.

Do not transcode files as part of normal metadata organization. Preserve original audio data unless the user explicitly requests a separate conversion operation.

### 6.4 Events and concerts

Potential providers:
- Songkick
- Bandsintown
- Ticketmaster Discovery API
- Other regional ticket/event APIs or permitted feeds

**Important:** Do not assume that a public-looking API is available for an independent hobby application. Confirm approval requirements, pricing, rate limits, geographic coverage, caching rules, attribution and display requirements before selecting a provider.

## 7. Architecture proposal

The following is a starting point for evaluation, not a final technology commitment.

### 7.1 Application shape

Prefer a modular monolith over microservices:

- **Frontend:** responsive web UI.
- **Backend/API:** domain logic, authentication, provider adapters and orchestration.
- **Worker process:** long-running scans, fingerprinting, metadata lookups, artwork retrieval, downloads and imports.
- **Database:** relational persistence for library entities, ratings, jobs and audit history.
- **Media storage:** existing local/NAS directory tree; Aoide stores references and only manages files through explicit workflows.
- **Configuration/secrets:** externalized configuration and protected credentials.

### 7.2 Candidate technology stack

| Layer | Initial candidate | Reason / validation |
|---|---|---|
| Backend | Python + FastAPI | Strong ecosystem for metadata, audio processing and integrations |
| Frontend | React + TypeScript | Mature component ecosystem and typed client development |
| Database | PostgreSQL | Relational domain, constraints, search and migration support |
| ORM/migrations | SQLAlchemy + Alembic | Mature Python persistence and schema migrations |
| Background jobs | Evaluate Dramatiq, Celery or a simpler database-backed worker | Long-running and retryable workflows |
| Audio tags | Mutagen | Direct tag manipulation |
| Fingerprinting | Chromaprint/fpcalc + AcoustID | Established identification workflow |
| Media probing | FFprobe | Technical media properties |
| Deployment | Docker Compose initially | Straightforward self-hosted deployment |
| Testing | Pytest + frontend test tooling | Domain, API, integration and UI tests |

Before adopting this stack, compare it with the established architecture and conventions of Mneme. Reuse proven patterns where appropriate, but do not force shared components where the music domain has materially different needs.

### 7.3 Architectural boundaries

Keep distinct modules for:
- Library scanning and inventory.
- Metadata search and matching.
- Tag reading/writing.
- Artwork acquisition and storage.
- Filesystem planning and execution.
- Discovery/search providers.
- Download-client integrations.
- Import and post-processing.
- Ratings, playlists and recommendations.
- Events and notifications.

Provider adapters should map external schemas into Aoide's normalized domain model. Provider-specific IDs and raw responses should be retained where useful for debugging and provenance.

### 7.4 Operational and security requirements

- No credentials or API keys in source control.
- Restrict access to the local application and its API.
- Validate filesystem paths and prevent path traversal.
- Avoid arbitrary remote URL fetching and unsafe archive extraction.
- Apply timeouts, retry/backoff and rate limits to external APIs.
- Keep a clear boundary between trusted library roots, download staging and temporary processing directories.
- Do not automatically delete files after failed imports.
- Provide backups for the database and configuration.
- Log operations without exposing secrets.
- Make external integrations individually configurable and disableable.

## 8. User experience and main screens

Initial navigation proposal:

1. **Dashboard:** library health, recent additions, pending matches, failed jobs, upcoming events and release notifications.
2. **Library:** artists, albums, tracks and files with filters and sortable columns.
3. **Inbox / Needs attention:** unmatched files, incomplete metadata, duplicates and conflicts.
4. **Discovery:** search artists, albums, releases and individual recordings.
5. **Wanted:** wanted artists, albums and tracks.
6. **Activity:** search, download, import, scan and file-operation history.
7. **Playlists:** personal playlists and exports.
8. **Artist detail:** biography, discography, ratings, related artists and concerts.
9. **Album detail:** release/edition information, tracklist, artwork and rating.
10. **Track detail:** recording information, versions, file quality, credits and rating.
11. **Settings:** library roots, naming templates, providers, download clients, artwork, notifications and backup.

The UI should make uncertain matches and destructive operations conspicuous. Bulk actions should be previewable and reversible where technically possible.

## 9. Development phases

### Phase 0 — Research and architecture decisions

- Confirm name availability and project naming.
- Review Mneme's project structure and technology choices.
- Compare metadata providers and licensing.
- Verify MusicBrainz/AcoustID/Discogs API requirements.
- Test fingerprint matching against representative files.
- Evaluate Lidarr's API for album and track use cases.
- Test candidate single-track search and acquisition providers.
- Verify concert/event API access for a private hobby project.
- Define the initial domain model and filesystem safety strategy.
- Record key decisions as ADRs.

**Exit criteria:** documented architecture, provider shortlist, domain model, legal/terms review and an achievable MVP scope.

### Phase 1 — Foundation

- Create repository and development environment.
- Establish backend, frontend and database.
- Add configuration, structured logging and migrations.
- Add basic UI shell and API health checks.
- Add automated tests and development documentation.
- Define library roots and access permissions.

### Phase 2 — Read-only library inventory

- Scan configured music directories.
- Read MP3 tags and technical properties.
- Store files, tracks and current paths.
- Detect exact duplicates and incomplete metadata.
- Build library and needs-attention views.
- Make rescans idempotent.

**Do not modify media files in this phase.**

### Phase 3 — Metadata matching and artwork

- Integrate MusicBrainz.
- Integrate AcoustID/Chromaprint.
- Build candidate matching and manual review.
- Retrieve Cover Art Archive images.
- Store identifiers and metadata provenance.
- Add artist, release, recording and track detail views.

### Phase 4 — Safe tag and filesystem management

- Implement tag editing with Mutagen.
- Define configurable folder/file templates.
- Build dry-run operation plans.
- Add conflict detection, execution journal and recovery.
- Add artwork embedding and sidecar handling.
- Verify writes and record before/after values.

### Phase 5 — Discovery and acquisition

- Implement album/release search.
- Integrate Lidarr and Prowlarr where useful.
- Integrate at least one download client.
- Implement a separate track-level search workflow.
- Add wanted lists, job status and post-download import.
- Add quality and duplicate checks.

### Phase 6 — Personal music experience

- Add ratings, favourites and notes.
- Add playlists and M3U/M3U8 export.
- Add artist relationships and recommendations.
- Evaluate ListenBrainz and Last.fm integrations.
- Add optional listening history.

### Phase 7 — Events and refinement

- Integrate an approved event source.
- Add concert and release notifications.
- Improve dashboards, search and bulk workflows.
- Review security, backup, performance and operational reliability.

## 10. Research backlog — decisions required before implementation

The following questions should be investigated and documented before committing to the relevant implementation.

### A. Product scope
- Is MP3 the only format for the MVP, or should FLAC/M4A be supported from the first release?
- Is Aoide strictly a library manager, or should it eventually include playback?
- Should it manage one library root or multiple independent libraries?
- Should it support multiple users, or remain single-user?
- Which parts of Mneme's architecture and UI conventions should be reused?

### B. Metadata quality and matching
- Which provider is authoritative for each field?
- How should conflicting MusicBrainz and Discogs release data be resolved?
- How should compilations, classical music, remasters, live recordings, remixes and featured artists be modelled?
- What matching-confidence threshold permits automatic matching?
- How are unmatched and ambiguous tracks handled?
- Should metadata edits be written back to external databases? (Default recommendation: no.)

### C. Tagging and filesystem safety
- Which ID3 versions and frames should be supported?
- Which fields are mandatory for a “complete” track?
- What is the canonical folder and filename template?
- How should multi-disc albums, compilations and Various Artists be handled?
- How should embedded artwork and external image files coexist?
- What rollback guarantees are feasible when a tag write or file move fails?
- How should files modified outside Aoide be detected?

### D. Artwork rights and storage
- Which sources permit downloading, embedding and local caching?
- What attribution is required?
- Should artist images be stored locally or referenced remotely?
- What image sizes and formats should be retained?
- How should duplicate images and alternative artwork be managed?

### E. Discovery and acquisition
- Which indexers and sources support individual tracks reliably?
- Can Prowlarr search be used independently of Lidarr's album model?
- What is the best integration boundary between Aoide and Lidarr?
- Should slskd be supported, and under what conditions?
- Which download clients are required for the first release?
- How are releases/versions and audio quality verified?
- How are legal access restrictions and provider terms enforced?

### F. Recommendations and listening data
- Should recommendations be based solely on local ratings or also external listening data?
- Is ListenBrainz account integration desirable?
- How are similar artists and tracks sourced?
- Should personal listening history be collected, imported or remain optional?
- How should recommendation explanations and provenance be presented?

### G. Events and external APIs
- Which event provider permits hobby/personal projects?
- What geographic coverage is available for Switzerland and neighbouring countries?
- Are caching, event display, ticket links and notifications permitted?
- How are duplicate events across providers reconciled?

### H. Operations and deployment
- Where will Aoide run: NAS, dedicated server, VM or another host?
- Which paths will be mounted read/write?
- Should the worker have narrower filesystem permissions than the API?
- What backup and restore model is required?
- How will long-running jobs, retries and interrupted operations be handled?
- What monitoring and notification mechanisms are appropriate?

## 11. Initial MVP recommendation

Avoid implementing all domains at once. The first useful release should solve the most painful and foundational problem: making the existing local collection accurate, consistent and maintainable.

**Recommended MVP:**
1. Read-only recursive MP3 inventory.
2. Tag and technical metadata analysis.
3. MusicBrainz search and manual matching.
4. AcoustID fingerprinting for unmatched files.
5. Album artwork retrieval.
6. A clear “needs attention” workflow.
7. Safe tag editing and configurable file organization with dry-run and recovery.

Defer downloads, recommendations, concerts and advanced playlists until the library model and file-operation engine are reliable.

## 12. Key risks

| Risk | Mitigation |
|---|---|
| Incorrect track/release matching | Confidence scoring, candidate review and manual overrides |
| Accidental data loss | Dry-run, operation journal, backups and no implicit deletion |
| API restrictions or service changes | Provider adapters, caching and documented terms |
| Poor single-track acquisition coverage | Separate track-first provider research; do not rely on Lidarr alone |
| Artwork licensing uncertainty | Store source/attribution and verify local caching rights |
| Complex music relationships | Model recording, release, track and artist credits separately |
| Long-running imports and scans | Background workers, progress state, retries and idempotency |
| Scope expansion | Phase-based delivery and explicit MVP exit criteria |

## 13. Development-agent working instructions

Act as a senior software architect and developer. Build Aoide incrementally, prioritizing correctness, maintainability, security and data integrity.

- Begin each substantial task by inspecting the current repository and its actual state.
- Do not assume proposed components or features are already implemented.
- Explain architecture and material trade-offs before significant implementation.
- Work in small, verifiable milestones.
- Avoid unnecessary dependencies and premature microservices.
- Use typed, modular and well-tested code.
- Add tests for domain logic, metadata matching, filesystem operations, API adapters and security-sensitive behaviour.
- Document significant architectural decisions as ADRs.
- Update project documentation when behaviour or architecture changes.
- Never commit credentials, API keys or private media metadata unnecessarily.
- Never modify or delete user media without an explicit, reviewable operation.
- Do not silently resolve ambiguous metadata.
- For every milestone, report changes, tests executed, limitations and recommended next steps.

## 14. Initial GitHub repository metadata

**Description**

Personal music library management, metadata tagging, track and album discovery, ratings, and recommendations.

**Topics**

`music-library` `music-management` `music-metadata` `mp3` `id3` `musicbrainz` `acoustid` `music-discovery` `self-hosted` `media-management`

## 15. Reference links

- MusicBrainz API: https://musicbrainz.org/doc/MusicBrainz_API
- MusicBrainz rate limiting: https://musicbrainz.org/doc/MusicBrainz_API/Rate_Limiting
- Cover Art Archive API: https://musicbrainz.org/doc/Cover_Art_Archive/API
- AcoustID: https://musicbrainz.org/doc/AcoustID
- MusicBrainz Picard documentation: https://picard-docs.musicbrainz.org/
- ListenBrainz API: https://listenbrainz.readthedocs.io/en/latest/users/api/
- ListenBrainz recommendations: https://listenbrainz.readthedocs.io/en/latest/users/api/recommendation.html
- Lidarr API: https://lidarr.audio/docs/api/
- Prowlarr API: https://prowlarr.com/docs/api/
- Songkick API: https://www.songkick.com/developer
- Bandsintown API information: https://help.artists.bandsintown.com/en/articles/7053475-what-is-the-bandsintown-api
- Spotify Developer Policy: https://developer.spotify.com/policy
- Discogs API: https://www.discogs.com/developers

---

**Document status:** Initial project briefing. Architecture, provider selection, legal/API access and final MVP scope remain subject to the research phase.
