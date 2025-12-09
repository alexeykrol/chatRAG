# SNAPSHOT — chatRAG

*Last updated: 2025-12-08*

## Current State

**Version:** 1.6
**Status:** Production-ready (with minor technical debt)
**Branch:** main

---

## Project Overview

**Name:** chatRAG
**Description:** Полнофункциональное веб-приложение для работы с OpenAI Assistants API с поддержкой RAG (Retrieval-Augmented Generation), векторного поиска и мультиперсональных чатов.

**Tech Stack:**

### Frontend
- React 18 + TypeScript
- Vite (build tool)
- Tailwind CSS
- Zustand (state management)
- React Markdown + remark-gfm
- Lucide React (icons)

### Backend & Services
- Supabase (PostgreSQL + Auth + Storage)
- OpenAI Assistants API
- OpenAI Threads API
- OpenAI Vector Stores API
- OpenAI Embeddings API

### Development Framework
- Claude Code Starter Framework v2.1.1

---

## Current Structure

```
chatRAG/
├── src/                    # Application source code
│   ├── components/         # React UI (9 components)
│   ├── lib/                # Services (8 modules)
│   ├── store/              # Zustand state (815 lines)
│   ├── retrieval/          # Summarization system
│   └── pages/              # API routes
│
├── .claude/                # Framework meta files
│   ├── commands/           # 15 slash commands
│   ├── dist/               # Compiled framework tools
│   ├── SNAPSHOT.md         # This file
│   ├── ARCHITECTURE.md     # Detailed architecture
│   └── BACKLOG.md          # Task tracking
│
├── dialog/                 # Exported AI sessions (gitignored)
├── html-viewer/            # Dialog viewer (gitignored)
├── supabase/
│   ├── migrations/         # 5 SQL migrations
│   └── docs/               # Database changelog
│
├── CLAUDE.md               # AI agent instructions
├── FRAMEWORK_GUIDE.md      # Framework documentation
├── README.md               # Project documentation
└── package.json            # Dependencies + scripts
```

---

## Implemented Features

### ✅ Core Functionality

#### 1. **Multi-personality Chat System**
- Создание и управление кастомными ассистентами (personalities)
- Привязка OpenAI Assistant к каждой personality
- Автотранслитерация кириллических имен (русский → латиница)
- Синхронизация имен и промптов с OpenAI
- Переключение между personalities в реальном времени

#### 2. **OpenAI Assistants Integration**
- Полная интеграция с Assistants API
- Thread-based conversations (сохранение контекста)
- Polling-based статус проверка (30 сек timeout)
- Подсчет токенов (prompt + completion + total)
- Отображение статистики использования

#### 3. **RAG (Retrieval-Augmented Generation)**
- Загрузка файлов для personalities (PDF, DOCX, TXT)
- Автоматическая векторизация через OpenAI Vector Stores
- File Search инструмент для семантического поиска
- IntegrationService для атомарной интеграции (Vector Store + Assistant)
- Хранение метаданных файлов в JSONB (personalities.files)

#### 4. **Memory & Summarization**
- Опциональная память для personalities (`has_memory` flag)
- Настраиваемая суммаризация диалогов
- Выбор модели для суммаризации (gpt-4o, gpt-4-turbo, etc.)
- Кастомные промпты для суммаризации
- Background процесс суммаризации (fire-and-forget API)

#### 5. **Authentication & Security**
- Supabase Auth (email/password)
- Row Level Security (RLS) для всех таблиц
- Шифрование API ключей перед сохранением в БД
- Расшифровка в памяти браузера (не проходит через backend)
- Session-based authentication

#### 6. **File Processing**
- PDF parsing (pdfjs-dist)
- DOCX parsing (mammoth)
- TXT support
- Drag-and-drop file upload UI
- Метаданные файлов (name, size, type, status)

#### 7. **Chat Management**
- Создание/удаление чатов
- Автоматическое обновление заголовков (первое сообщение)
- История сообщений с сохранением в БД
- Переключение между чатами
- Привязка чатов к OpenAI Threads

#### 8. **UI/UX**
- Responsive sidebar с списком чатов
- Settings панель для API ключей
- Personalities панель для управления ассистентами
- Memory Settings для настройки суммаризации
- Markdown рендеринг с syntax highlighting
- Loading states и error handling

---

## Service Layer Architecture

### Реализованные сервисы (`src/lib/`):

1. **AssistantService** (`assistantService.ts`)
   - Управление OpenAI Assistants (CRUD)
   - Автотранслитерация кириллицы
   - Независим от Vector Stores и файлов

2. **VectorStoreService** (`vectorStoreService.ts`)
   - Создание Vector Stores
   - Загрузка файлов в Vector Stores
   - Векторизация документов

3. **IntegrationService** (`integrationService.ts`)
   - Оркестратор для Assistant + Vector Store
   - Атомарные операции (создание → загрузка → обновление)
   - Добавление file_search инструмента к Assistant

4. **OpenAIService** (`openai.ts`)
   - Базовая работа с Threads API
   - Создание тредов, добавление сообщений
   - Запуск Assistant на треде
   - Проверка статуса выполнения (polling)

5. **RAGService** (`ragService.ts`)
   - RAG логика и поиск по документам

6. **FileProcessing** (`fileProcessing.ts`)
   - Извлечение текста из PDF/DOCX/TXT

7. **Encryption** (`encryption.ts`)
   - Шифрование/дешифрование API ключей

8. **Supabase** (`supabase.ts`)
   - Client initialization
   - TypeScript types для БД

---

## Database Schema (Supabase)

### Таблицы:

1. **users** — Supabase Auth (email/password)
2. **chats** — История чатов с привязкой к OpenAI Threads
3. **messages** — Сообщения (user/assistant)
4. **personalities** — Кастомные ассистенты с метаданными
5. **user_settings** — Настройки пользователя (API ключи encrypted)

**RLS Enabled:** ✅ Row Level Security для всех таблиц

**Migrations:** 5 SQL файлов в `supabase/migrations/`

---

## Recent Progress

### Completed (по коду):

- [x] ✅ **Full OpenAI Assistants API integration**
  - Thread-based conversations
  - Assistant management
  - Token counting

- [x] ✅ **RAG Support**
  - Vector Stores integration
  - File upload (PDF, DOCX, TXT)
  - Semantic search via file_search tool

- [x] ✅ **Modular Architecture**
  - Service layer pattern
  - Independent services (Assistant, VectorStore, Integration, RAG)
  - Zustand state management (815 lines)

- [x] ✅ **Security**
  - API key encryption/decryption
  - Supabase RLS
  - Session-based auth

- [x] ✅ **Memory & Summarization**
  - Configurable summarization
  - Custom models and prompts
  - Background processing

- [x] ✅ **Cyrillic Name Auto-transliteration**
  - Russian → Latin conversion for OpenAI compatibility

- [x] ✅ **OpenAI Sync**
  - Name and prompt sync between local DB and OpenAI

### Completed (framework setup):

- [x] ✅ Initialized Claude Code Starter Framework v2.1.1 (2025-12-08)
- [x] ✅ Configured dialog export system (2025-12-08)
- [x] ✅ Fixed framework npm scripts and dependencies (2025-12-08)
- [x] ✅ Updated ARCHITECTURE.md with detailed code analysis (2025-12-08)

---

## Active Work

**Current Focus:** None — Bug fix sprint completed

**Recently Completed Sprint:** Bug Fixes (2025-12-08)
- Fixed `dialog:ui` script in package.json
- Fixed parasitic project folder bug in watcher.js
- Removed parasitic `chatRAG-dialog` folder from Claude system

**Previous Sprint:** Documentation Update (2025-12-08)
- [x] ARCHITECTURE.md — ✅ Complete (685 lines)
- [x] SNAPSHOT.md — ✅ Complete (384 lines)
- [x] BACKLOG.md — ✅ Complete (418 lines)
- [x] README.md — ✅ Rewritten (343 lines)
- [x] CHANGELOG.md — ✅ Created (~200 lines)
- [x] Version synchronization — ✅ Complete (v1.6.0)
- [x] Legacy cleanup — ✅ 25 files removed from git
- [x] .claude/ cleanup — ✅ Only active framework files remain

---

## Known Technical Debt

### High Priority

1. **Vector Store Cleanup** (`src/store/useStore.ts:765`)
   - Проблема: Файлы удаляются из БД, но остаются в OpenAI Vector Stores
   - TODO: Добавить tracking `vector_store_id` для proper cleanup
   - Impact: Потенциальная утечка ресурсов и расходы

### Medium Priority

2. **Polling Instead of Webhooks** (`src/store/useStore.ts:286-307`)
   - Проблема: Assistant runs проверяются через polling (каждую секунду)
   - TODO: Migrate to OpenAI webhooks
   - Impact: Неэффективно для production

3. **No Error Boundaries**
   - Проблема: React компоненты без Error Boundaries
   - TODO: Добавить graceful error handling
   - Impact: Ошибки могут крашить приложение

### Low Priority

4. **API Routes in Frontend** (`src/pages/api/retrieval/summarize.ts`)
   - Проблема: API routes не работают в static build
   - TODO: Migrate to serverless functions или backend
   - Impact: Deployment ограничения

5. **No Tests**
   - Проблема: Полное отсутствие тестов
   - TODO: Add Vitest + Playwright
   - Impact: Риск регрессий при изменениях

---

## Next Steps

### Documentation (Current Sprint)

- [ ] Обновить BACKLOG.md с реальными задачами из анализа кода
- [ ] Синхронизировать версии (package.json, README, SNAPSHOT)
- [ ] Проверить README.md на соответствие фактическим возможностям
- [ ] Проверить FRAMEWORK_GUIDE.md

### Technical Improvements (Future)

- [ ] Implement Vector Store cleanup mechanism
- [ ] Replace polling with webhooks for Assistant runs
- [ ] Add Error Boundaries to React components
- [ ] Set up comprehensive test suite (Vitest + Playwright)
- [ ] Migrate API routes to serverless functions (Vercel/Netlify)
- [ ] Add CI/CD pipeline (GitHub Actions)

### Features (Backlog)

- [ ] Real-time collaboration (multi-user chats)
- [ ] Voice input/output (Speech-to-Text / Text-to-Speech)
- [ ] Advanced RAG (custom embeddings, hybrid search)
- [ ] Cost analytics dashboard (token usage per chat/personality)
- [ ] Export conversations (PDF, Markdown)
- [ ] Code splitting and lazy loading for better performance

---

## Key Metrics

**Codebase Size:**
- React Components: 9 files
- Services: 8 modules
- Zustand Store: 815 lines (single file)
- Total TypeScript: ~3000+ lines (estimated)

**Database:**
- Tables: 5 (users, chats, messages, personalities, user_settings)
- Migrations: 5 SQL files
- RLS: Enabled on all tables

**Dependencies:**
- Production: 11 packages
- Dev: 9 packages
- Framework: Claude Code Starter v2.1.1

**Framework Assets:**
- Slash commands: 15
- Dialog export: ✅ Configured
- Git hooks: ✅ Available

---

## Development Commands

```bash
# Application
npm run dev              # Start dev server (Vite on :5173)
npm run build            # Build for production
npm run preview          # Preview production build
npm run lint             # Run ESLint

# Dialog Management (Framework)
npm run dialog:export    # Export AI conversations
npm run dialog:list      # List all sessions
npm run dialog:ui        # Launch web UI (:3333)
npm run dialog:watch     # Auto-export watcher
```

---

## Environment

**Required:**
- Node.js 18+
- Supabase project (URL + Anon Key)
- OpenAI API key (added via Settings UI, не в .env)

**Configuration:**
- `.env.local` — Supabase credentials
- Settings UI — OpenAI API key (encrypted storage)

---

## Notes

- **Privacy:** Dialogs exported to `dialog/` (gitignored by default)
- **Framework:** Claude Code Starter v2.1.1 manages project meta
- **Version:** v1.6.0 (synchronized across all documentation)
- **Production:** Development-only setup, requires deployment config for production
- **Testing:** No tests implemented yet (высокий приоритет для будущего)

---

*Quick-start context for AI sessions*
*Framework: Claude Code Starter v2.1.1*
*Analyzed: 2025-12-08 by Claude Sonnet 4.5*
