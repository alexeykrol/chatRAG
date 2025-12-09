# BACKLOG — chatRAG

*Task tracking for chatRAG*

**Last Updated:** 2025-12-08
**Current Sprint:** Documentation Update

---

## Active Sprint: Documentation Update

### High Priority
- [x] ✅ Update ARCHITECTURE.md with real code analysis (2025-12-08)
- [x] ✅ Update SNAPSHOT.md with current state (2025-12-08)
- [x] ✅ Update BACKLOG.md with real tasks (2025-12-08)
- [x] ✅ Synchronize project versions (README v1.6 vs package.json v0.0.0) (2025-12-08)
- [x] ✅ Review and update README.md for accuracy (2025-12-08)
- [x] ✅ Review FRAMEWORK_GUIDE.md for relevance (2025-12-08)
- [x] ✅ Create CHANGELOG.md for version history (2025-12-08)
- [x] ✅ Create .env.example template (2025-12-08)
- [x] ✅ Remove legacy files from git (25 files) (2025-12-08)
- [x] ✅ Clean up .claude/ directory (2025-12-08)

---

## Technical Debt

### High Priority

#### 1. Vector Store Cleanup Implementation
**Priority:** 🔴 Critical
**File:** `src/store/useStore.ts:765`
**Problem:**
- Файлы удаляются из БД, но остаются в OpenAI Vector Stores
- Нет tracking `vector_store_id` для каждого файла
- Потенциальная утечка ресурсов и расходы на OpenAI

**Tasks:**
- [ ] Добавить `vector_store_id` в `PersonalityFile` type
- [ ] Реализовать `deleteFile()` метод в VectorStoreService
- [ ] Обновить `deletePersonalityFile()` в useStore для вызова cleanup
- [ ] Добавить migration для добавления `vector_store_id` в personalities.files
- [ ] Тестирование cleanup logic

**Estimate:** Medium complexity
**Impact:** High — предотвращает утечку ресурсов

---

### Medium Priority

#### 2. Replace Polling with Webhooks
**Priority:** 🟡 Medium
**File:** `src/store/useStore.ts:286-307`
**Problem:**
- Assistant runs проверяются через polling (каждую секунду, до 30 сек)
- Неэффективно для production, избыточные API calls

**Tasks:**
- [ ] Research OpenAI webhooks implementation
- [ ] Create webhook endpoint (serverless function)
- [ ] Implement webhook handler для Assistant run completion
- [ ] Update frontend для WebSocket/SSE connection
- [ ] Add fallback to polling if webhooks fail
- [ ] Security: validate webhook signatures

**Estimate:** High complexity
**Impact:** Medium — улучшение performance и снижение API costs

---

#### 3. Add Error Boundaries
**Priority:** 🟡 Medium
**Problem:**
- React компоненты без Error Boundaries
- Ошибки могут крашить всё приложение

**Tasks:**
- [ ] Create ErrorBoundary component
- [ ] Wrap main sections (ChatArea, Sidebar, Settings)
- [ ] Add fallback UI для graceful degradation
- [ ] Implement error reporting/logging
- [ ] Add error recovery mechanisms

**Estimate:** Low complexity
**Impact:** Medium — улучшение UX при ошибках

---

### Low Priority

#### 4. Migrate API Routes to Serverless
**Priority:** 🟢 Low
**File:** `src/pages/api/retrieval/summarize.ts`
**Problem:**
- API routes не работают в static build
- Deployment ограничения (Vercel/Netlify требуют serverless functions)

**Tasks:**
- [ ] Research Vercel/Netlify serverless functions
- [ ] Migrate `/api/retrieval/summarize` to serverless
- [ ] Update frontend fetch calls
- [ ] Test в production environment
- [ ] Update deployment documentation

**Estimate:** Low-Medium complexity
**Impact:** Low — блокирует только static hosting

---

#### 5. Add Comprehensive Test Suite
**Priority:** 🟢 Low (но важно для будущего)
**Problem:**
- Полное отсутствие тестов
- Риск регрессий при изменениях кода

**Tasks:**
- [ ] Set up Vitest (unit/integration tests)
- [ ] Set up Playwright (E2E tests)
- [ ] Add tests для services (lib/*)
  - [ ] AssistantService
  - [ ] VectorStoreService
  - [ ] IntegrationService
  - [ ] OpenAIService
  - [ ] Encryption
  - [ ] FileProcessing
- [ ] Add tests для Zustand store actions
- [ ] Add E2E tests для key flows
  - [ ] Authentication
  - [ ] Chat creation
  - [ ] Message sending
  - [ ] Personality management
  - [ ] File upload
- [ ] Set up CI/CD для auto-testing

**Estimate:** High complexity
**Impact:** Low (immediate), High (long-term quality)

---

#### 6. Version Synchronization
**Priority:** 🟢 Low
**Problem:**
- README.md показывает v1.6
- package.json показывает v0.0.0
- Несоответствие версий

**Tasks:**
- [ ] Определить правильную версию проекта
- [ ] Обновить package.json
- [ ] Обновить README.md
- [ ] Обновить SNAPSHOT.md
- [ ] Установить процесс version management (semantic versioning)

**Estimate:** Trivial
**Impact:** Low — косметическое

---

## Features Backlog

### Near-term Features

#### 7. Code Splitting & Performance Optimization
**Priority:** 🟡 Medium
**Problem:** Initial bundle size может быть большим

**Tasks:**
- [ ] Analyze current bundle size (npm run build)
- [ ] Implement lazy loading для:
  - [ ] Settings component
  - [ ] Personalities component
  - [ ] MemorySettings component
- [ ] Add React.memo для тяжелых компонентов
- [ ] Implement useMemo для вычислений
- [ ] Add virtual scrolling для long message lists
- [ ] Debounce user input в чате
- [ ] Measure performance improvements

**Estimate:** Medium complexity
**Impact:** Medium — faster load times

---

#### 8. Cost Analytics Dashboard
**Priority:** 🟡 Medium
**Value:** High для пользователей с ограниченным бюджетом

**Tasks:**
- [ ] Design analytics schema (token usage by chat/personality)
- [ ] Create analytics DB tables/views
- [ ] Implement tracking logic (уже есть token_usage)
- [ ] Create analytics UI component
  - [ ] Token usage by personality
  - [ ] Token usage over time (charts)
  - [ ] Estimated costs (OpenAI pricing)
  - [ ] Top consuming chats
- [ ] Add export functionality (CSV, PDF)

**Estimate:** Medium complexity
**Impact:** High — user value

---

### Long-term Features

#### 9. Real-time Collaboration
**Priority:** 🟢 Low (nice-to-have)
**Value:** High для team use cases

**Tasks:**
- [ ] Design multi-user architecture
- [ ] Add real-time sync (Supabase Realtime или WebSockets)
- [ ] Implement presence indicators
- [ ] Handle concurrent edits
- [ ] Add permissions system (owner/viewer/editor)
- [ ] Update UI для collaborative features

**Estimate:** Very High complexity
**Impact:** Low (current) — new feature segment

---

#### 10. Voice Input/Output
**Priority:** 🟢 Low
**Value:** Medium — accessibility и UX

**Tasks:**
- [ ] Research Speech-to-Text options (Web Speech API, OpenAI Whisper)
- [ ] Research Text-to-Speech options (Web Speech API, OpenAI TTS)
- [ ] Implement voice input в chat
- [ ] Implement voice output для assistant responses
- [ ] Add voice controls (start/stop/pause)
- [ ] Handle permissions (microphone access)
- [ ] Add UI indicators для voice activity

**Estimate:** High complexity
**Impact:** Medium — accessibility

---

#### 11. Advanced RAG Features
**Priority:** 🟢 Low
**Value:** High для power users

**Tasks:**
- [ ] Custom embeddings (fine-tuned models)
- [ ] Hybrid search (keyword + semantic)
- [ ] Chunking strategies (configurable chunk size)
- [ ] Re-ranking algorithms
- [ ] Multi-document synthesis
- [ ] Citation tracking (source references в ответах)
- [ ] RAG performance metrics

**Estimate:** Very High complexity
**Impact:** Medium — advanced feature

---

#### 12. Conversation Export
**Priority:** 🟢 Low
**Value:** Medium

**Tasks:**
- [ ] Export to PDF (formatted conversation)
- [ ] Export to Markdown
- [ ] Export to JSON (structured data)
- [ ] Include metadata (personality, tokens, timestamps)
- [ ] Batch export (multiple chats)
- [ ] Schedule auto-export (backup)

**Estimate:** Low-Medium complexity
**Impact:** Low — convenience feature

---

#### 13. Mobile App (React Native)
**Priority:** 🟢 Very Low
**Value:** High если есть mobile users

**Tasks:**
- [ ] Evaluate React Native vs Flutter
- [ ] Set up mobile project
- [ ] Port UI components
- [ ] Handle mobile-specific UX (touch, gestures)
- [ ] Optimize для mobile performance
- [ ] Implement push notifications
- [ ] Deploy to App Store / Google Play

**Estimate:** Very High complexity
**Impact:** Low (current) — new platform

---

## Improvements Backlog

### Code Quality

- [ ] Add ESLint rules для code consistency
- [ ] Set up Prettier для auto-formatting
- [ ] Add Husky pre-commit hooks
- [ ] Implement Code Review checklist
- [ ] Add JSDoc comments для public APIs
- [ ] Refactor large components (ChatArea > 500 lines?)
- [ ] Extract magic numbers в constants

### Security

- [ ] Security audit для encryption implementation
- [ ] Add rate limiting для API calls
- [ ] Implement CSRF protection
- [ ] Add Content Security Policy (CSP) headers
- [ ] Audit dependencies для vulnerabilities (npm audit)
- [ ] Add input sanitization для user content
- [ ] Implement proper secrets management (vault)

### DevOps

- [ ] Set up CI/CD pipeline (GitHub Actions)
- [ ] Add automated testing в CI
- [ ] Implement staging environment
- [ ] Add Docker containerization
- [ ] Create deployment scripts
- [ ] Set up monitoring (Sentry, LogRocket)
- [ ] Add performance monitoring (Web Vitals)

### Documentation

- [ ] Add API documentation (если создаём backend API)
- [ ] Create user guide (screenshots, tutorials)
- [ ] Add contributing guidelines (CONTRIBUTING.md)
- [ ] Document database schema (ERD diagrams)
- [ ] Add troubleshooting guide
- [ ] Create video tutorials

---

## Bugs / Known Issues

### Current Known Issues

1. **Summarization API не работает в static build**
   - Location: `src/pages/api/retrieval/summarize.ts`
   - Workaround: Deploy с serverless functions
   - Fix: Task #4 (Migrate to serverless)

2. **OpenAI API errors не всегда gracefully handled**
   - Location: `src/store/useStore.ts` (various actions)
   - Workaround: User sees generic error
   - Fix: Improve error handling + Task #3 (Error Boundaries)

3. **Vector Store files не удаляются из OpenAI**
   - Location: `src/store/useStore.ts:765`
   - Workaround: Manual cleanup in OpenAI dashboard
   - Fix: Task #1 (Vector Store Cleanup)

---

## Completed Tasks

### Recently Completed (2025-12-08)

#### Framework Setup
- [x] ✅ Initialized Claude Code Starter Framework v2.1.1
- [x] ✅ Configured dialog export system (npm scripts + dependencies)
- [x] ✅ Fixed framework npm scripts and dependencies (CommonJS compatibility)
- [x] ✅ Created dialog/ and html-viewer/ directories
- [x] ✅ Set main as default branch

#### Documentation Sprint
- [x] ✅ Updated ARCHITECTURE.md with detailed code analysis (685 lines)
- [x] ✅ Updated SNAPSHOT.md with current project state (384 lines)
- [x] ✅ Updated BACKLOG.md with real tasks from code analysis (this file)

### Previously Completed (from code analysis)

#### Core Features
- [x] ✅ Full OpenAI Assistants API integration
- [x] ✅ Thread-based conversations
- [x] ✅ RAG support (Vector Stores + file_search)
- [x] ✅ Multi-personality system
- [x] ✅ File upload (PDF, DOCX, TXT)
- [x] ✅ Memory & summarization system
- [x] ✅ Cyrillic name auto-transliteration
- [x] ✅ API key encryption/decryption
- [x] ✅ Supabase Auth + RLS
- [x] ✅ Token counting and display
- [x] ✅ Markdown rendering с syntax highlighting

#### Architecture
- [x] ✅ Modular service layer (8 services)
- [x] ✅ Zustand state management (815 lines)
- [x] ✅ Component architecture (9 React components)
- [x] ✅ Database schema (5 tables с RLS)
- [x] ✅ TypeScript types для всего проекта

---

## Notes

**Sprint Planning:**
- Documentation Sprint: 2025-12-08 (ongoing)
- Next sprint: TBD (after documentation complete)

**Priority Legend:**
- 🔴 Critical — blocks production or causes major issues
- 🟡 Medium — important but not blocking
- 🟢 Low — nice-to-have or future enhancement

**Complexity Estimates:**
- Trivial: < 1 hour
- Low: 1-4 hours
- Medium: 1-2 days
- High: 3-5 days
- Very High: 1-2 weeks

---

*Use `/feature` command to plan new features*
*Use `/fix` command to address bugs*
*Use `/fi` command for sprint completion*
