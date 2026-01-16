# Directory Structure

## Repository Organization

Singularity Code follows a monorepo structure inspired by OpenCode, optimized for scalability and modularity.

```
singularity-code/
├── .claude/                    # Claude Code configuration
│   ├── EXECUTORS/              # Automation executors
│   ├── CONFIGS/                # Configuration files
│   └── CLAUDE.md               # Master configuration
├── .github/                    # GitHub workflows and CI/CD
├── .singularity/               # Project-local config
│   ├── plans/                  # Execution plans
│   ├── session/                # Session data
│   └── singularity.json        # Project config
├── packages/                   # Monorepo packages
│   ├── opencode/               # Core CLI application
│   │   ├── src/
│   │   │   ├── acp/            # Agent Client Protocol
│   │   │   ├── cli/            # CLI commands and TUI
│   │   │   │   ├── cmd/        # Command implementations
│   │   │   │   └── ui.ts       # CLI UI utilities
│   │   │   ├── server/         # HTTP server (Hono)
│   │   │   ├── session/        # Session management
│   │   │   ├── provider/       # AI provider integrations
│   │   │   ├── plugin/         # Plugin system
│   │   │   ├── tool/           # Tool registry
│   │   │   ├── agent/          # Agent definitions
│   │   │   ├── permission/     # Permission system
│   │   │   ├── command/        # Custom commands
│   │   │   └── ...             # Other core modules
│   │   ├── script/             # Build scripts
│   │   ├── bin/                # Binary entry points
│   │   └── AGENTS.md           # Agent documentation
│   ├── sdk/                    # TypeScript SDK
│   ├── plugin/                 # Plugin API
│   └── ui/                     # Shared UI components
├── themes/                     # UI themes
├── nix/                       # Nix configuration
├── script/                    # Global build scripts
├── sdks/                      # Additional SDKs (VSCode, Python)
└── package.json               # Root dependencies
```

## Key Directories Explained

### Core Package (`packages/opencode/`)

The heart of Singularity Code containing all CLI functionality.

- **`src/cli/`**: Command-line interface with TUI components
- **`src/server/`**: Hono-based API server with OpenAPI specs
- **`src/session/`**: Message processing, LLM integration, prompt management
- **`src/provider/`**: AI provider abstractions (OpenAI, Anthropic, etc.)
- **`src/plugin/`**: Plugin system with hooks and extensions
- **`src/tool/`**: Tool registry and implementations
- **`src/agent/`**: Agent definitions and modes
- **`src/permission/`**: Permission rules for tool access
- **`src/command/`**: Custom slash commands system

### Configuration Locations

```
Global Config (~/.config/singularity/)
├── singularity.json        # Global settings
├── cache/                  # TUI cache
└── logs/                   # Application logs

Project Config (.singularity/)
├── singularity.json        # Project settings
├── rules/                  # Custom rules files
├── plans/                  # Execution plans
└── session/                # Session data
```

## File Naming Conventions

- **Modules**: `camelCase.ts` (e.g., `sessionManager.ts`)
- **Components**: `PascalCase.tsx` (e.g., `CommandPalette.tsx`)
- **Configs**: `kebab-case.json` (e.g., `singularity.json`)
- **Scripts**: `camelCase.ts` (e.g., `buildScripts.ts`)

## Import Patterns

```typescript
// Internal imports
import { SessionManager } from "../session/manager";
import type { ProviderConfig } from "../provider/types";

// External imports
import { Hono } from "hono";
import { z } from "zod";
```

## Best Practices

- Keep directories shallow (max 3 levels deep)
- Group related functionality in feature folders
- Use index.ts files for clean imports
- Separate types into dedicated files
- Follow the established naming conventions
