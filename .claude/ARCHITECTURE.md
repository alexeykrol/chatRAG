# ARCHITECTURE — chatRAG

*Code structure and architecture documentation*

## Overview

> 🤖 **AI Development:** This project uses [Claude Code Starter Framework](./FRAMEWORK_GUIDE.md). See [usage guide](./FRAMEWORK_GUIDE.md) for details.

**Tech Stack:**

- Node.js/JavaScript
- React + TypeScript
- Vite (build tool)
- Supabase (backend)
- OpenAI API

## Directory Structure

```
chatRAG/
├── src/                    # React application source
├── dist/                   # Vite build output
├── .claude/                # Framework meta files
│   ├── commands/           # 15 slash commands
│   ├── dist/               # Compiled framework tools
│   └── templates/          # Document templates
├── dialog/                 # Exported AI conversations (gitignored)
├── html-viewer/            # Dialog HTML viewer (gitignored)
├── CLAUDE.md               # AI agent instructions
├── FRAMEWORK_GUIDE.md      # Framework usage guide
└── package.json            # Dependencies + dialog scripts
```

## Key Components

### Main Application
**Location:** `./src`
**Purpose:** Core application code

### Configuration
**Location:** `./config`
**Purpose:** Project configuration

## Architecture Patterns

**Pattern:** See existing code
**Description:** Analyzed from existing project

## Data Flow

```
See existing documentation
```

## External Dependencies

See package.json or requirements.txt

## Configuration

**Environment:** See existing configuration files
**Build:** See existing build scripts

## Testing Strategy

See existing tests

## Deployment

See existing deployment configuration

---
*This file is auto-generated during framework initialization and can be manually updated*
