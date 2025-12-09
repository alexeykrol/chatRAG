# Changelog

All notable changes to chatRAG project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## [1.6.0] - 2025-12-08

### Added
- **Documentation Update Sprint** — Complete documentation overhaul
  - Created detailed ARCHITECTURE.md (685 lines) with full system description
  - Created comprehensive SNAPSHOT.md (384 lines) with current state
  - Created detailed BACKLOG.md (418 lines) with task tracking
  - Rewrote README.md with accurate information
  - Created CHANGELOG.md for version history tracking
  - Created .env.example for easier setup

### Changed
- **Project Naming**
  - Updated package.json: name from "vite-react-typescript-starter" to "chatrag"
  - Updated package.json: version from 0.0.0 to 1.6.0
  - Fixed project title in README from "ChatOpenAI Integration Assistant" to "chatRAG"

### Fixed
- Removed references to non-existent files (AGENTS.md, WORKFLOW.md)
- Fixed incorrect documentation links and paths
- Synchronized version numbers across all documentation
- Updated FRAMEWORK_GUIDE.md version reference

### Documented
- Service layer architecture (8 independent services)
- Data flow diagrams for key operations
- Database schema (5 tables with RLS)
- Known technical debt with priorities
- Security implementation details
- Deployment options and considerations

---

## [1.3.0] - 2025-10-XX (estimated from README)

### Added
- **Modular Architecture** — Independent services for assistants and vector stores
- **File Vectorization** — Automatic vectorization of uploaded files
- **Cyrillic Auto-transliteration** — Russian to Latin name conversion for OpenAI
- **OpenAI Sync** — Automatic synchronization of assistant names and prompts

### Changed
- **API Optimization** — Removed duplicate OpenAI API calls
- Improved service separation and modularity

---

## [1.0.0] - 2025-XX-XX (estimated)

### Added - Initial Release

#### Core Features
- **Multi-personality Chat System**
  - Create and manage custom AI assistants
  - Switch between personalities in real-time
  - Store assistant configurations in database

- **OpenAI Assistants Integration**
  - Full integration with OpenAI Assistants API
  - Thread-based conversations with context preservation
  - Token counting and usage monitoring

- **RAG (Retrieval-Augmented Generation)**
  - Document upload (PDF, DOCX, TXT)
  - Automatic vectorization via OpenAI Vector Stores
  - Semantic search using file_search tool
  - File metadata storage in JSONB

- **Memory & Summarization**
  - Optional memory for personalities
  - Configurable conversation summarization
  - Custom models and prompts for summarization
  - Background summarization processing

- **Security**
  - API key encryption before database storage
  - Row Level Security (RLS) on all Supabase tables
  - Session-based authentication via Supabase Auth
  - Client-side decryption only

- **File Processing**
  - PDF parsing (pdfjs-dist)
  - DOCX parsing (mammoth)
  - TXT support
  - Drag-and-drop upload UI

#### Architecture
- **Service Layer**
  - AssistantService — OpenAI Assistant CRUD operations
  - VectorStoreService — Vector Store management
  - IntegrationService — Assistant + Vector Store integration
  - OpenAIService — Threads API operations
  - RAGService — RAG logic and document search
  - FileProcessing — Text extraction from files
  - Encryption — API key security
  - Supabase — Database client

- **State Management**
  - Zustand for centralized state (815 lines)
  - Reactive UI updates
  - Service singletons in state

- **Database Schema**
  - users (Supabase Auth)
  - chats (with OpenAI Thread IDs)
  - messages (user/assistant)
  - personalities (with file metadata)
  - user_settings (encrypted API keys)

#### Tech Stack
- React 18 + TypeScript
- Vite (build tool)
- Tailwind CSS
- Zustand (state management)
- Supabase (backend)
- OpenAI Assistants API
- OpenAI Vector Stores API

#### Development
- Claude Code Starter Framework v2.1.1
- 15 slash commands for workflows
- Dialog export system
- Git hooks and automation

---

## Known Issues & Technical Debt

### High Priority
1. **Vector Store Cleanup** (`src/store/useStore.ts:765`)
   - Files deleted from DB remain in OpenAI Vector Stores
   - Need to track `vector_store_id` for proper cleanup

### Medium Priority
2. **Polling Instead of Webhooks** (`src/store/useStore.ts:286-307`)
   - Assistant runs checked via polling (1s intervals, 30s timeout)
   - Should migrate to OpenAI webhooks for efficiency

3. **No Error Boundaries**
   - React components lack Error Boundaries
   - Application crashes on unhandled errors

### Low Priority
4. **No Tests**
   - Complete absence of test suite
   - High risk of regressions

5. **API Routes in Frontend** (`src/pages/api/retrieval/summarize.ts`)
   - Won't work in static builds
   - Need serverless functions migration

---

## Planned Features (Roadmap)

See [.claude/BACKLOG.md](.claude/BACKLOG.md) for detailed roadmap.

### Near-term
- Vector Store cleanup mechanism
- Error Boundaries implementation
- Code splitting & performance optimization
- Cost analytics dashboard

### Long-term
- Real-time collaboration
- Voice input/output
- Advanced RAG (custom embeddings, hybrid search)
- Mobile app (React Native)
- Conversation export (PDF, Markdown)

---

## Migration Guide

When upgrading between versions, check the following:

### From 1.3.0 to 1.6.0
- No breaking changes
- Documentation structure updated
- Package name changed (only affects package.json)
- No code changes required

---

## Links

- [Documentation](.claude/)
- [Architecture](.claude/ARCHITECTURE.md)
- [Backlog](.claude/BACKLOG.md)
- [Issues](https://github.com/your-repo/issues)

---

*Format: [Version] - YYYY-MM-DD*
*Types: Added, Changed, Deprecated, Removed, Fixed, Security*
