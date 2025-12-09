# chatRAG

> 🤖 **AI Development:** This project uses [Claude Code Starter Framework](./FRAMEWORK_GUIDE.md) v2.1.1

**Version:** 1.6.0 | **Last Updated:** 2025-12-08

Полнофункциональное веб-приложение для работы с OpenAI Assistants API с поддержкой RAG (Retrieval-Augmented Generation), векторного поиска и мультиперсональных чатов.

---

## 📚 Documentation

### For Developers & AI Agents

- **[.claude/ARCHITECTURE.md](.claude/ARCHITECTURE.md)** — Detailed system architecture and technical decisions
- **[.claude/SNAPSHOT.md](.claude/SNAPSHOT.md)** — Current project state and implemented features
- **[.claude/BACKLOG.md](.claude/BACKLOG.md)** — Task tracking and roadmap
- **[CLAUDE.md](CLAUDE.md)** — AI agent instructions (Cold Start Protocol)
- **[supabase/docs/DATABASE_CHANGELOG.md](supabase/docs/DATABASE_CHANGELOG.md)** — Database schema history

### Quick Start for AI Agents

1. Run `начать` or `start` to execute Cold Start Protocol
2. Read [.claude/ARCHITECTURE.md](.claude/ARCHITECTURE.md) for system design
3. Read [.claude/SNAPSHOT.md](.claude/SNAPSHOT.md) for current status
4. Read [.claude/BACKLOG.md](.claude/BACKLOG.md) for priorities

---

## ✨ Key Features

### Core Functionality

- **Multi-personality Chat System** — Create and manage custom AI assistants
- **RAG Support** — Upload and search documents (PDF, DOCX, TXT)
- **Vector Search** — Semantic search via OpenAI Vector Stores
- **Memory & Summarization** — Configurable conversation summarization
- **Token Tracking** — Monitor API usage and costs
- **Secure Storage** — Encrypted API key storage

### Technical Highlights

- **Modular Architecture** — Independent services (Assistant, VectorStore, Integration, RAG)
- **Cyrillic Auto-transliteration** — Russian names automatically converted for OpenAI
- **OpenAI Sync** — Automatic synchronization with OpenAI Assistants
- **Thread-based Conversations** — Full context preservation via Threads API
- **Type Safety** — Full TypeScript coverage

---

## 🛠️ Tech Stack

**Frontend:**
- React 18 + TypeScript
- Vite (build tool)
- Tailwind CSS
- Zustand (state management)
- React Markdown + Syntax Highlighting

**Backend & Services:**
- Supabase (PostgreSQL + Auth + Storage)
- OpenAI Assistants API
- OpenAI Threads API
- OpenAI Vector Stores API
- OpenAI Embeddings API

**Development:**
- Claude Code Starter Framework v2.1.1
- 15 slash commands for development workflows
- Dialog export and management system

---

## 🚀 Quick Start

### Prerequisites

- Node.js 18+ (LTS recommended)
- npm or yarn
- Supabase account ([supabase.com](https://supabase.com))
- OpenAI API key ([platform.openai.com](https://platform.openai.com))

### Installation

```bash
# 1. Clone repository
git clone <your-repo-url>
cd chatRAG

# 2. Install dependencies
npm install

# 3. Setup environment
cp .env.example .env.local
# Edit .env.local with your Supabase credentials:
# VITE_SUPABASE_URL=your_supabase_url
# VITE_SUPABASE_ANON_KEY=your_anon_key

# 4. Apply database migrations
cd supabase/migrations
# Apply migrations in order (check file dates)
# Use Supabase SQL Editor or supabase CLI

# 5. Start development server
npm run dev
```

### First Run

1. Open http://localhost:5173/
2. Sign up with email/password
3. Go to Settings (⚙️ icon) and add your OpenAI API key
4. Create your first personality/assistant
5. Upload files (optional) for RAG
6. Start chatting!

---

## 📦 Project Structure

```
chatRAG/
├── src/                    # Application source
│   ├── components/         # React UI components
│   ├── lib/                # Services & business logic
│   ├── store/              # Zustand state management
│   ├── retrieval/          # Summarization system
│   └── pages/              # API routes
├── .claude/                # Framework meta files
│   ├── commands/           # 15 slash commands
│   ├── ARCHITECTURE.md     # Detailed architecture
│   ├── SNAPSHOT.md         # Current state
│   └── BACKLOG.md          # Task tracking
├── supabase/
│   ├── migrations/         # Database migrations
│   ├── scripts/            # Migration helpers
│   └── docs/               # Database documentation
├── CLAUDE.md               # AI agent instructions
└── package.json            # Dependencies
```

---

## 🔧 Development Commands

### Application

```bash
npm run dev              # Start dev server (Vite on :5173)
npm run build            # Build for production
npm run preview          # Preview production build
npm run lint             # Run ESLint
```

### Dialog Management (Framework)

```bash
npm run dialog:export    # Export AI conversation sessions
npm run dialog:list      # List all sessions
npm run dialog:ui        # Launch web UI (:3333)
npm run dialog:watch     # Auto-export watcher
```

### Slash Commands (AI Development)

Available via Claude Code CLI or AI sessions:

- `/commit` — Create git commit with proper message
- `/pr` — Create pull request with summary
- `/fix` — Help find and fix bugs
- `/feature` — Plan new feature implementation
- `/review` — Code review for recent changes
- `/test` — Help write tests
- `/security` — Security audit
- `/optimize` — Performance optimization
- `/refactor` — Code refactoring assistance
- ... and more (15 total)

---

## 🗄️ Database Setup

### Supabase Configuration

1. Create new Supabase project at [supabase.com](https://supabase.com)
2. Get your project URL and anon key from Settings → API
3. Add to `.env.local`:
   ```
   VITE_SUPABASE_URL=https://xxx.supabase.co
   VITE_SUPABASE_ANON_KEY=eyJxxx...
   ```

### Apply Migrations

**Option 1: Supabase Dashboard (easiest)**
1. Go to SQL Editor in Supabase dashboard
2. Copy content from `supabase/migrations/` files (in date order)
3. Run each migration

**Option 2: Migration Scripts**
```bash
cd supabase/scripts
node apply-all-migrations.mjs
```

**Option 3: Supabase CLI**
```bash
supabase db push
```

---

## 🔐 Security

- **API Keys Encrypted** — OpenAI API keys encrypted before storage
- **Row Level Security** — Supabase RLS enabled on all tables
- **Session-based Auth** — Supabase Auth with email/password
- **No Server Exposure** — Keys decrypted only in browser

---

## 📊 Architecture Overview

### Service Layer

**8 Independent Services** (`src/lib/`):

1. **AssistantService** — OpenAI Assistant CRUD
2. **VectorStoreService** — Vector Store management
3. **IntegrationService** — Assistant + Vector Store integration
4. **OpenAIService** — Threads API operations
5. **RAGService** — RAG logic and document search
6. **FileProcessing** — PDF/DOCX/TXT extraction
7. **Encryption** — API key encryption/decryption
8. **Supabase** — Database client and types

### State Management

- **Zustand Store** — Centralized state (815 lines)
- **Actions** — Auth, Chats, Messages, Personalities, Settings
- **Services** — Singleton instances in state

### Database Schema

5 main tables:
- `users` — Supabase Auth
- `chats` — Chat history + OpenAI Thread IDs
- `messages` — User/assistant messages
- `personalities` — Custom assistants + metadata
- `user_settings` — User config + encrypted API keys

**See [.claude/ARCHITECTURE.md](.claude/ARCHITECTURE.md) for details**

---

## 🐛 Known Issues & Technical Debt

### High Priority

1. **Vector Store Cleanup** (`src/store/useStore.ts:765`)
   - Files deleted from DB but remain in OpenAI Vector Stores
   - TODO: Add `vector_store_id` tracking for proper cleanup

### Medium Priority

2. **Polling Instead of Webhooks** (`src/store/useStore.ts:286-307`)
   - Assistant runs checked via polling (every 1s, 30s timeout)
   - TODO: Migrate to OpenAI webhooks

3. **No Error Boundaries**
   - React components without Error Boundaries
   - TODO: Add graceful error handling

### Low Priority

4. **No Tests**
   - Complete absence of tests
   - TODO: Add Vitest + Playwright

5. **API Routes in Frontend** (`src/pages/api/retrieval/summarize.ts`)
   - Won't work in static build
   - TODO: Migrate to serverless functions

**See [.claude/BACKLOG.md](.claude/BACKLOG.md) for complete list**

---

## 📈 Roadmap

### Near-term

- [ ] Vector Store cleanup mechanism
- [ ] Error Boundaries
- [ ] Code splitting & performance optimization
- [ ] Cost analytics dashboard

### Long-term

- [ ] Real-time collaboration
- [ ] Voice input/output
- [ ] Advanced RAG (custom embeddings, hybrid search)
- [ ] Mobile app (React Native)
- [ ] Export conversations (PDF, Markdown)

**See [.claude/BACKLOG.md](.claude/BACKLOG.md) for details**

---

## 🤝 Contributing

This project uses **Claude Code Starter Framework v2.1.1** for AI-driven development.

### For AI Agents

Run `начать` or `start` to load context and begin work.

### For Humans

1. Check [.claude/BACKLOG.md](.claude/BACKLOG.md) for open tasks
2. Create feature branch
3. Make changes
4. Run `npm run lint`
5. Submit PR

---

## 📄 License

Private project — see repository settings for details.

---

## 🙏 Acknowledgments

- Built with [Claude Code](https://claude.com/code)
- Framework: [Claude Code Starter](https://github.com/anthropics/claude-code)
- Powered by [OpenAI Assistants API](https://platform.openai.com/docs/assistants)
- Backend: [Supabase](https://supabase.com)

---

*Developed with Claude Code Starter Framework v2.1.1*
*Last updated: 2025-12-08*
