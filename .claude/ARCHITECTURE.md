# ARCHITECTURE — chatRAG

*Code structure and architecture documentation*

**Last Updated:** 2025-12-08
**Version:** 1.6
**Status:** Production-ready with minor tech debt

---

## Overview

**chatRAG** — это полнофункциональное веб-приложение для работы с OpenAI Assistants API с поддержкой RAG (Retrieval-Augmented Generation), векторного поиска и мультиперсональных чатов.

> 🤖 **AI Development:** This project uses [Claude Code Starter Framework](../FRAMEWORK_GUIDE.md) v2.1.1

---

## Tech Stack

### Frontend
- **React 18** — UI framework
- **TypeScript** — type safety
- **Vite** — build tool and dev server
- **Tailwind CSS** — utility-first styling
- **Zustand** — state management (lightweight alternative to Redux)
- **React Markdown** + **remark-gfm** — markdown rendering
- **React Syntax Highlighter** — code highlighting
- **Lucide React** — icon library

### Backend & Services
- **Supabase** — backend-as-a-service
  - PostgreSQL database with Row Level Security (RLS)
  - Authentication (email/password)
  - Storage (file uploads)
- **OpenAI APIs:**
  - Assistants API — multi-turn conversations
  - Threads API — conversation threads
  - Vector Stores API — semantic search
  - Embeddings API — document vectorization

### Additional Libraries
- **mammoth** — DOCX file parsing
- **pdfjs-dist** — PDF processing
- **openai** SDK v5.16.0 — OpenAI client
- **chokidar** — file watching (dialog export)
- **express** — HTTP server (dialog UI)

---

## Directory Structure

```
chatRAG/
├── src/                          # Application source code
│   ├── components/               # React UI components
│   │   ├── Auth.tsx             # Authentication (login/signup)
│   │   ├── ChatArea.tsx         # Main chat interface
│   │   ├── Personalities.tsx    # Assistant management
│   │   ├── Settings.tsx         # User settings (API keys)
│   │   ├── Sidebar.tsx          # Navigation sidebar
│   │   ├── FileDropZone.tsx     # File upload UI
│   │   ├── MemorySettings.tsx   # Summarization config
│   │   ├── Profile.tsx          # User profile
│   │   └── ProfileScreen.tsx    # Profile view
│   │
│   ├── lib/                      # Business logic (services)
│   │   ├── supabase.ts          # Supabase client & types
│   │   ├── openai.ts            # OpenAI base service
│   │   ├── assistantService.ts  # Assistant CRUD
│   │   ├── vectorStoreService.ts # Vector Store management
│   │   ├── integrationService.ts # Assistant + Vector Store integration
│   │   ├── ragService.ts        # RAG logic
│   │   ├── fileProcessing.ts    # PDF/DOCX/TXT processing
│   │   ├── encryption.ts        # API key encryption/decryption
│   │   └── openaiProxy.ts       # OpenAI API proxy (legacy?)
│   │
│   ├── retrieval/                # Summarization system
│   │   ├── index.ts             # Main retrieval exports
│   │   ├── types/               # TypeScript types
│   │   ├── services/
│   │   │   └── database.ts      # Supabase DB operations
│   │   └── summarization/
│   │       └── index.ts         # Summarization logic
│   │
│   ├── pages/                    # API routes (Vite convention)
│   │   └── api/
│   │       └── retrieval/
│   │           └── summarize.ts # Summarization API endpoint
│   │
│   ├── store/
│   │   └── useStore.ts          # Zustand global state (815 lines)
│   │
│   ├── App.tsx                   # Root component
│   ├── main.tsx                  # Entry point
│   ├── index.css                 # Global styles
│   └── vite-env.d.ts            # Vite type definitions
│
├── .claude/                      # Claude Code Starter Framework
│   ├── commands/                 # 15 slash commands
│   ├── dist/                     # Compiled framework tools
│   ├── templates/                # Document templates
│   ├── SNAPSHOT.md               # Current project state
│   ├── ARCHITECTURE.md           # This file
│   └── BACKLOG.md                # Task tracking
│
├── dialog/                       # Exported AI conversations (gitignored)
├── html-viewer/                  # Dialog HTML viewer (gitignored)
├── supabase/
│   ├── migrations/               # SQL schema migrations
│   └── docs/                     # Database documentation
│
├── dist/                         # Vite build output
├── public/                       # Static assets
├── node_modules/                 # Dependencies
│
├── package.json                  # NPM dependencies + scripts
├── tsconfig.json                 # TypeScript config
├── vite.config.ts                # Vite build config
├── tailwind.config.js            # Tailwind CSS config
├── postcss.config.js             # PostCSS config
├── .env.local                    # Environment variables (gitignored)
├── CLAUDE.md                     # AI agent instructions
├── FRAMEWORK_GUIDE.md            # Framework documentation
├── README.md                     # Project documentation
└── CHANGELOG.md                  # Version history
```

---

## Architecture Patterns

### 1. **Service Layer Pattern**

Бизнес-логика изолирована в независимых сервисах (`src/lib/`):

#### **AssistantService** (`assistantService.ts`)
**Ответственность:** Управление OpenAI Assistants
**Методы:**
- `createAssistant()` — создание Assistant с автотранслитерацией кириллических имен
- `updateAssistant()` — обновление промптов и настроек
- `deleteAssistant()` — удаление из OpenAI
- `getAssistant()` — получение информации

**Особенности:**
- Автоматическая транслитерация русских имен (кириллица → латиница)
- Не зависит от Vector Store или File management

#### **VectorStoreService** (`vectorStoreService.ts`)
**Ответственность:** Управление Vector Stores
**Методы:**
- `createVectorStore()` — создание хранилища для embedding'ов
- `uploadFiles()` — загрузка файлов в Vector Store
- `deleteVectorStore()` — удаление хранилища

**Особенности:**
- Независим от AssistantService
- Используется для семантического поиска по документам

#### **IntegrationService** (`integrationService.ts`)
**Ответственность:** Интеграция Assistant + Vector Store
**Методы:**
- `addFilesToPersonality()` — добавление файлов к Assistant с векторизацией
- Связывает Assistant с Vector Store через `tool_resources`
- Добавляет `file_search` инструмент к Assistant

**Особенности:**
- Оркестрирует AssistantService и VectorStoreService
- Атомарные операции (создание Vector Store + загрузка файлов + обновление Assistant)

#### **RAGService** (`ragService.ts`)
**Ответственность:** RAG логика и поиск по документам
**Примечание:** Детали требуют дополнительного анализа

#### **OpenAIService** (`openai.ts`)
**Ответственность:** Базовая работа с OpenAI API
**Методы:**
- `createThread()` — создание треда для разговора
- `addMessage()` — добавление сообщения в тред
- `runAssistant()` — запуск Assistant на треде
- `checkRun()` — проверка статуса выполнения
- `getThreadMessages()` — получение сообщений из треда
- `updateAssistant()` — обновление Assistant (синхронизация)
- `deleteAssistant()` — удаление Assistant

**Особенности:**
- Работает с Threads API для multi-turn conversations
- Polling-based статус проверка (30 секунд timeout)

#### **FileProcessing** (`fileProcessing.ts`)
**Ответственность:** Извлечение текста из файлов
**Поддержка:** PDF, DOCX, TXT

#### **Encryption** (`encryption.ts`)
**Ответственность:** Шифрование/дешифрование API ключей
**Особенности:**
- API ключи шифруются перед сохранением в Supabase
- Расшифровываются при загрузке в память

---

### 2. **State Management Pattern**

**Zustand Store** (`src/store/useStore.ts`) — 815 строк

**State Разделы:**
```typescript
{
  // Authentication
  user: User | null
  isLoading: boolean

  // Chats
  chats: Chat[]
  currentChatId: string | null
  messages: Message[]
  totalTokens: number

  // Settings
  settings: UserSettings | null

  // Personalities (Assistants)
  personalities: Personality[]
  activePersonality: Personality | null

  // UI State
  isGenerating: boolean
  sidebarOpen: boolean
  showSettings: boolean
  showPersonalities: boolean
  showMemorySettings: boolean
  uploading: boolean
  error: string | null

  // Services (singleton instances)
  openaiService: OpenAIService
  assistantService: AssistantService
  vectorStoreService: VectorStoreService
  integrationService: IntegrationService
}
```

**Actions (методы):**
- **Auth:** `setUser()`, `loadSettings()`, `updateSettings()`
- **Chats:** `loadChats()`, `createChat()`, `selectChat()`, `deleteChat()`, `updateChatTitle()`
- **Messages:** `sendMessage()` — основной метод для отправки сообщений
- **Personalities:** `loadPersonalities()`, `createPersonality()`, `updatePersonality()`, `deletePersonality()`, `setActivePersonality()`, `uploadPersonalityFile()`, `deletePersonalityFile()`
- **UI:** `toggleSidebar()`, `toggleSettings()`, `togglePersonalities()`, `toggleMemorySettings()`, `setIsGenerating()`

**Ключевые особенности:**
- Централизованное управление состоянием
- Прямое обращение к Supabase из actions (без отдельного API layer)
- Services хранятся как singleton'ы в state

---

### 3. **Component Architecture**

**Presentation vs Container:**
- Компоненты используют Zustand hooks напрямую
- Бизнес-логика вынесена в store actions
- UI компоненты отвечают только за рендеринг

**Основные компоненты:**
- `App.tsx` — root, auth routing
- `ChatArea.tsx` — основной интерфейс чата
- `Sidebar.tsx` — список чатов
- `Personalities.tsx` — управление ассистентами
- `Settings.tsx` — настройки API ключей
- `MemorySettings.tsx` — настройки суммаризации

---

## Data Flow

### 1. Message Flow (отправка сообщения)

```
User Input (ChatArea)
    ↓
useStore.sendMessage()
    ↓
1. Check auth & API key
2. Get/Create chat
3. Get/Create OpenAI Thread
    ↓
OpenAIService.addMessage(threadId, content)
    ↓
OpenAIService.runAssistant(threadId, assistantId)
    ↓
[Polling] OpenAIService.checkRun() — каждую секунду, до 30 сек
    ↓
Status === 'completed'?
    ↓
OpenAIService.getThreadMessages(threadId)
    ↓
Update Zustand state (messages)
    ↓
Save to Supabase (messages table)
    ↓
[Optional] Trigger summarization API (/api/retrieval/summarize)
    ↓
Update chat title (if first message)
```

### 2. Personality Creation Flow (создание Assistant с файлами)

```
User: Create Personality + Upload Files
    ↓
useStore.createPersonality(name, prompt)
    ↓
AssistantService.createAssistant()
    ↓
Save to Supabase (personalities table)
    ↓
useStore.uploadPersonalityFile(file)
    ↓
IntegrationService.addFilesToPersonality()
    ↓
  1. VectorStoreService.createVectorStore()
  2. VectorStoreService.uploadFiles()
  3. AssistantService.updateAssistant() — add file_search tool + vector_store_id
    ↓
Update Supabase (personalities.files JSON)
    ↓
Update Zustand state
```

### 3. Authentication Flow

```
User: Login/Signup
    ↓
Supabase Auth
    ↓
useStore.setUser(user)
    ↓
Parallel loading:
  - loadChats()
  - loadSettings()
  - loadPersonalities()
    ↓
Decrypt API key (encryption.decrypt)
    ↓
Initialize services with API key
```

### 4. Summarization Flow (опционально)

```
Message saved to DB
    ↓
Check: personality.has_memory && personality.summarization_enabled?
    ↓
Fetch: /api/retrieval/summarize
    ↓
POST { thread_id, user_msg_id, assistant_msg_id }
    ↓
API: Summarization logic (src/retrieval/summarization/)
    ↓
Save summary to DB (background process)
```

---

## Database Schema (Supabase PostgreSQL)

### Tables

#### **users** (Supabase Auth)
- `id` — UUID (primary key)
- `email`, `password`, etc.

#### **chats**
- `id` — UUID
- `user_id` — FK to users
- `title` — string
- `openai_thread_id` — string (OpenAI Thread ID)
- `created_at`, `updated_at` — timestamps

#### **messages**
- `id` — UUID
- `chat_id` — FK to chats
- `role` — enum ('user', 'assistant')
- `content` — text
- `created_at` — timestamp

#### **personalities**
- `id` — UUID
- `user_id` — FK to users
- `name` — string
- `prompt` — text (instructions for Assistant)
- `openai_assistant_id` — string (OpenAI Assistant ID)
- `is_active` — boolean
- `has_memory` — boolean
- `summarization_enabled` — boolean
- `summarization_model` — string (e.g., 'gpt-4o')
- `summarization_prompt` — text
- `files` — JSONB array (PersonalityFile[])
  ```json
  [{
    "openai_file_id": "file-xxx",
    "file_name": "document.pdf",
    "file_size": 12345,
    "file_type": "application/pdf",
    "status": "ready",
    "uploaded_at": "2025-12-08T..."
  }]
  ```
- `created_at`, `updated_at` — timestamps

#### **user_settings**
- `id` — UUID
- `user_id` — FK to users
- `openai_api_key` — text (encrypted)
- `model` — string (default model, e.g., 'gpt-4o')
- `created_at`, `updated_at` — timestamps

**Note:** Row Level Security (RLS) enabled для всех таблиц

---

## Security

### 1. **API Key Protection**
- **Encryption at rest:** API ключи шифруются перед сохранением (`encryption.encrypt()`)
- **Decryption in memory:** Расшифровываются только в браузере при загрузке
- **No server exposure:** Ключи хранятся только в Supabase, не проходят через бэкенд

### 2. **Row Level Security (RLS)**
- Supabase RLS policies ограничивают доступ к данным по `user_id`
- Пользователи видят только свои чаты, сообщения, personalities

### 3. **Authentication**
- Supabase Auth (email/password)
- Session-based authentication
- Автоматический logout при истечении сессии

---

## External Dependencies

### Production Dependencies (package.json)
```json
{
  "@supabase/supabase-js": "^2.56.0",  // Supabase client
  "openai": "^5.16.0",                  // OpenAI SDK
  "react": "^18.3.1",                   // UI framework
  "zustand": "^5.0.8",                  // State management
  "mammoth": "^1.10.0",                 // DOCX parsing
  "pdfjs-dist": "^5.4.54",              // PDF processing
  "react-markdown": "^10.1.0",          // Markdown rendering
  "lucide-react": "^0.344.0",           // Icons
  "chokidar": "^5.0.0",                 // File watcher
  "express": "^4.22.1",                 // Dialog UI server
  "dotenv": "^17.2.2"                   // Environment variables
}
```

### Dev Dependencies
```json
{
  "vite": "^5.4.2",                     // Build tool
  "typescript": "^5.5.3",               // Type safety
  "tailwindcss": "^3.4.1",              // CSS framework
  "@vitejs/plugin-react": "^4.3.1",     // React plugin for Vite
  "eslint": "^9.9.1"                    // Linting
}
```

---

## Configuration

### Environment Variables (`.env.local`)

```bash
# Supabase
VITE_SUPABASE_URL=https://xxx.supabase.co
VITE_SUPABASE_ANON_KEY=eyJxxx...

# OpenAI (stored encrypted in database, not in .env)
# Users add their API keys via Settings UI
```

### Build Configuration

**Vite** (`vite.config.ts`):
- React plugin enabled
- Dev server на порту 5173
- Build output в `dist/`

**TypeScript** (`tsconfig.json`):
- Strict mode enabled
- ES2020 target
- Module resolution: bundler

**Tailwind CSS** (`tailwind.config.js`):
- Utility-first CSS
- Custom theme configuration

---

## Testing Strategy

**Current State:** ❌ **No tests implemented**

**Recommended Testing Approach:**

### Unit Tests
- Services (`lib/*`) — мокировать OpenAI и Supabase clients
- Encryption functions
- File processing utilities

### Integration Tests
- Zustand store actions
- Supabase queries
- OpenAI API interactions (with test API keys)

### E2E Tests
- Authentication flow
- Chat creation and messaging
- Personality management
- File upload

**Suggested Tools:**
- **Vitest** — unit/integration tests (Vite-native)
- **Playwright** — E2E tests
- **Mock Service Worker (MSW)** — API mocking

---

## Deployment

**Current State:** Development-only setup (Vite dev server)

### Production Deployment Options

#### Option 1: Static Hosting (Vercel/Netlify)
```bash
npm run build  # Build to dist/
# Deploy dist/ to Vercel/Netlify
```

**Pros:**
- Simple deployment
- Free tier available
- Automatic HTTPS

**Cons:**
- API routes (`/api/retrieval/summarize`) не работают в static build
- Требует переноса на serverless functions

#### Option 2: Node.js Server
```bash
npm run build
npm run preview  # или custom Express server
```

**Требования:**
- Node.js 18+
- Environment variables настроены
- Supabase project deployed

---

## Known Technical Debt

### 1. **Vector Store Cleanup** (High Priority)
**File:** `src/store/useStore.ts:765`
```typescript
// TODO: Store vector_store_id in personality or file metadata for proper cleanup
```

**Problem:**
- При удалении файлов они удаляются из БД, но остаются в OpenAI Vector Stores
- Нет механизма отслеживания `vector_store_id` для каждого файла

**Impact:**
- Потенциальная утечка ресурсов
- Расходы на неиспользуемые Vector Stores в OpenAI

**Solution:**
- Добавить `vector_store_id` в `PersonalityFile` type
- Реализовать `deleteFile()` в VectorStoreService
- Вызывать при удалении файлов из personality

### 2. **Polling Instead of Webhooks** (Medium Priority)
**File:** `src/store/useStore.ts:286-307`

**Problem:**
- Статус Assistant runs проверяется через polling (каждую секунду, до 30 сек)
- Неэффективно для production

**Solution:**
- Использовать OpenAI webhooks для уведомлений о завершении run
- Требует backend endpoint для приема webhooks

### 3. **No Error Boundaries** (Medium Priority)
- React компоненты не имеют Error Boundaries
- Ошибки могут крашить всё приложение

**Solution:**
- Добавить Error Boundary компоненты
- Graceful fallback UI при ошибках

### 4. **API Routes in Frontend** (Low Priority)
**File:** `src/pages/api/retrieval/summarize.ts`

- API routes находятся в frontend коде
- Не работают в production static build
- Требуют serverless functions или backend

---

## Performance Considerations

### Optimization Opportunities

1. **Code Splitting**
   - Lazy load компоненты (Settings, Personalities)
   - Reduce initial bundle size

2. **Memoization**
   - React.memo для тяжелых компонентов
   - useMemo для вычислений

3. **Virtual Scrolling**
   - Для длинных списков сообщений
   - Библиотека: `react-virtuoso`

4. **Debouncing**
   - User input в чате
   - API calls

---

## Development Workflow

### NPM Scripts

```bash
npm run dev              # Start dev server (Vite)
npm run build            # Build for production
npm run preview          # Preview production build
npm run lint             # Run ESLint

# Dialog Management (Claude Code Starter Framework)
npm run dialog:export    # Export AI conversations
npm run dialog:list      # List all sessions
npm run dialog:ui        # Launch web UI (port 3333)
npm run dialog:watch     # Auto-export watcher
```

### Git Workflow

- **Main branch:** `main`
- **Commits:** Conventional Commits format
- **Framework:** Claude Code Starter v2.1.1 с 15 slash commands

---

## Future Improvements

### Features
- [ ] Real-time collaboration (multi-user chats)
- [ ] Voice input/output (Speech-to-Text / Text-to-Speech)
- [ ] Advanced RAG (custom embeddings, hybrid search)
- [ ] Cost analytics dashboard (token usage per chat/personality)
- [ ] Export conversations (PDF, Markdown)

### Technical
- [ ] Migrate to Next.js (для API routes и SSR)
- [ ] Add comprehensive test suite
- [ ] Implement proper Vector Store cleanup
- [ ] WebSocket для real-time updates
- [ ] CI/CD pipeline (GitHub Actions)
- [ ] Docker containerization

---

*Last updated: 2025-12-08*
*Analyzed by: Claude Sonnet 4.5*
