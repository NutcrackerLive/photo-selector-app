# How This App Was Built

This entire application was built in a single conversation with [Claude Code](https://claude.ai/code), Anthropic's AI coding assistant. The development process is documented below.

## Starting Point

The project began with an existing Ruby on Rails application that helped users select the best photos from a batch using AI analysis. The Rails app worked but required users to have Ruby, Docker, and run commands in the terminal - not ideal for non-technical users who just want to cull their vacation photos.

## The Goal

Create a standalone desktop application that anyone can download and run with a double-click. No terminal, no Docker, no Ruby installation required.

## Development Process

### Phase 1: Architecture & Setup

Claude designed the Electron + React + TypeScript architecture:

```
photo-selector-electron/
├── src/
│   ├── main/           # Electron main process (Node.js)
│   ├── renderer/       # React frontend
│   ├── preload/        # Secure IPC bridge
│   └── shared/         # Shared types
```

Key technology choices:
- **Electron** for cross-platform desktop app
- **React + TailwindCSS** for the UI
- **better-sqlite3** for local database (replacing Active Record)
- **sharp** for image processing (replacing Ruby's image_processing gem)
- **keytar** for secure API key storage in OS keychain
- **@anthropic-ai/sdk** for Claude API integration

### Phase 2: Core Implementation

Claude implemented the full application including:

1. **First-run setup wizard** - Guides users through choosing storage location and entering API key
2. **Photo management** - Upload, thumbnail generation, duplicate detection via file hashing
3. **AI analysis** - Batch processing with Claude's vision API, progress tracking, cost estimation
4. **Similarity grouping** - AI-powered detection of burst shots and near-duplicates
5. **Export functionality** - Convert selected photos to optimized JPEGs

### Phase 3: Testing & Refinement

Through iterative testing, Claude fixed various issues:

- **Preload script module loading** - Electron's sandboxed preload can't import modules normally
- **Native module compatibility** - Required `electron-rebuild` for better-sqlite3
- **RAW/HEIC handling** - Sharp can't process these formats, so placeholder thumbnails are created
- **SQL syntax** - Fixed datetime string quoting for SQLite
- **IPC parameter mismatches** - Fixed batch selection using correct parameter names

### Phase 4: UX Improvements

Based on feedback during testing:

- Added score range slider for filtering photos
- Improved similarity grouping UI with success messages and clear option
- Added export confirmation modal with editable destination
- Made API key requirement more prominent
- Styled the UI with elegant, modern design

### Phase 5: Packaging & Release

Claude handled the release process:

- Built macOS DMG files for both Apple Silicon and Intel
- Created GitHub release with download links
- Updated README with installation instructions

## Key Files

| File | Purpose |
|------|---------|
| `src/main/index.ts` | Electron entry point, window management |
| `src/main/ipc-handlers.ts` | All IPC communication handlers |
| `src/main/keychain.ts` | Secure API key storage |
| `src/main/services/claude-vision.ts` | AI photo analysis |
| `src/main/services/similarity-grouping.ts` | Duplicate detection |
| `src/main/services/photo-manager.ts` | File operations, thumbnails |
| `src/renderer/pages/ProjectView.tsx` | Main photo grid and controls |
| `src/preload/preload.ts` | Secure IPC bridge with channel whitelist |

## Security Considerations

Claude implemented several security measures:

- **Context isolation** - Renderer process can't access Node.js directly
- **IPC whitelist** - Only explicitly allowed channels can be invoked
- **API key encryption** - Uses OS keychain with AES-256-GCM fallback
- **No new windows** - Prevents potential security issues from window.open

## What Claude Did Well

- Understood the full architecture from Rails to Electron
- Made sensible technology choices for the desktop context
- Handled edge cases (RAW files, rate limiting, duplicate detection)
- Wrote clean, well-organized TypeScript code
- Fixed bugs quickly when they were discovered during testing

## Limitations Encountered

- **RAW/HEIC export** - Sharp can't convert these to JPEG without additional libraries
- **Code signing** - Would need Apple Developer account for proper signing
- **Windows/Linux builds** - Only macOS was built and tested in this session

## Time to Build

The entire application was built in a single extended conversation, from initial architecture discussion through working release with GitHub downloads.

---

*Built with [Claude Code](https://claude.ai/code) by Anthropic*
