# ~/tools CLI Toolkit — Best Practices

Guidelines for building and maintaining CLI tools in `~/tools/`. Each tool is a single-purpose CLI wrapping a third-party API or local data source, designed for both human use and LLM agent consumption.

## Naming

All tools are named `{name}-cli` (directory name, GitHub repo, and global command).

## Default Language

**TypeScript** is the default for new tools. Use Ruby for macOS-only tools (AppleScript interop, SQLite access) or Bash for very thin wrappers.

| Language | When to use | Examples |
|----------|-------------|---------|
| **TypeScript** | Default. Complex APIs, platform SDKs exist | google-ads-cli, meta-ads-cli, x-ads-cli |
| **Ruby** | macOS-only (AppleScript, local DBs), simple API wrappers | imessages-cli, things-cli, calendly-cli |
| **Bash** | Ultra-thin wrappers around existing CLIs | youtube-transcript-cli, appfolio-workorder-cli |

## Required Files

Every tool **must** have all of these:

```
tool-cli/
├── README.md          # Human docs: what, why, prerequisites, setup, full reference
├── AGENTS.md          # LLM docs: terse command cheatsheet, env vars, output format
├── .gitignore         # Always includes: .env (even if no creds yet)
├── .env.example       # Template with all required env vars (if tool needs credentials)
├── [executable]       # Globally callable entry point (see "Global Executables" below)
└── [git repo]         # On GitHub as christiangenco/tool-cli, listed in sync.sh
```

### README.md

Human-facing documentation. Must include:
- One-line description
- Prerequisites (runtime, OS, external tools)
- Setup instructions (install deps, get credentials, configure .env)
- Full command reference with examples
- How it works (architecture, API notes)

### AGENTS.md

LLM-facing documentation. Terse and copy-paste ready. Structure:

```markdown
# tool-cli

One-line description.

## Commands

\`\`\`bash
tool-cli command1 [args]     # Description
tool-cli command2 [args]     # Description
\`\`\`

## Examples

\`\`\`bash
tool-cli do-thing "value"
tool-cli other-thing --flag
\`\`\`

Requires `.env` with `VAR1`, `VAR2`. See `.env.example`.
```

### .gitignore

Always includes `.env` even if the tool doesn't currently use credentials (it might later). Minimum:

```gitignore
.env
.DS_Store
```

Add language-specific entries:

```gitignore
# Ruby
vendor/
.bundle/

# Node/TypeScript
node_modules/
dist/
```

### .env.example

Required if the tool uses any credentials. Lists all env vars with blank or example values:

```env
SERVICE_API_KEY=
SERVICE_SECRET=
SERVICE_ACCOUNT_ID=your-account-id
```

## Output Format

**JSON is the default output format.** All tools output to stdout using the standard envelope:

```json
{"ok": true, "data": { ... }}
{"ok": false, "error": "message", "code": "ERROR_CODE"}
```

Rules:
- Data goes to **stdout** as single-line JSON
- Errors/warnings go to **stderr**
- Exit **0** on success, **1** on error
- If a tool has nice human-readable output (tables, colors), keep it available behind a `--pretty` flag

This makes every tool trivially parseable by LLMs and composable with `jq`.

## Global Executables

Every tool must be callable as `tool-cli args` from **any directory**. No `cd` required.

| Language | Method | Example |
|----------|--------|---------|
| **TypeScript/Node** | `"bin"` field in package.json → `npm link` | `meta-ads-cli campaigns list` |
| **Ruby** | Executable file (no extension, `#!/usr/bin/env ruby`) → symlink to `~/bin/` | `imessages-cli threads` |
| **Ruby (needs Bundler)** | Wrapper script in `~/bin/` | `calendly-cli types` |
| **Bash** | Executable script → symlink to `~/bin/` | `youtube-transcript-cli transcript ID` |

### Ruby wrapper script pattern (when Bundler is needed):

```bash
#!/usr/bin/env bash
cd ~/tools/tool-cli && bundle exec ruby cli.rb "$@"
```

### Node bin entry point pattern:

```js
#!/usr/bin/env node
import "../dist/cli.js";
```

## TypeScript Tool Template

```
tool-cli/
├── bin/tool-cli.js        # #!/usr/bin/env node → import "../dist/cli.js"
├── src/
│   ├── cli.ts             # Commander program definition
│   ├── config.ts          # dotenv loading + env var validation
│   ├── auth.ts            # OAuth flow on localhost:3456 (if needed)
│   └── commands/          # One file per resource (campaigns.ts, ads.ts, etc.)
├── package.json           # "type": "module", bin field, build/dev/auth scripts
├── tsconfig.json          # ES2022, Node16 module resolution, strict
├── .env.example
├── .gitignore             # .env, node_modules/, dist/
├── AGENTS.md
└── README.md
```

**package.json conventions:**
- `"type": "module"` (ESM)
- `"bin": { "tool-cli": "./bin/tool-cli.js" }`
- Scripts: `build` (tsc), `dev` (tsx src/cli.ts), `auth` (tsx src/auth.ts)
- CLI framework: `commander`
- Config: `dotenv`
- OAuth callback always on `localhost:3456`

## Ruby Tool Template

```
tool-cli/
├── tool-cli               # #!/usr/bin/env ruby — the executable (no .rb extension)
├── lib/                   # Supporting classes (if needed)
├── Gemfile
├── Gemfile.lock
├── .env.example           # (if credentials needed)
├── .gitignore             # .env, vendor/, .bundle/
├── AGENTS.md
└── README.md
```

**Conventions:**
- Single executable with `#!/usr/bin/env ruby`
- `dotenv` gem for credentials
- `JSON.generate(ok: true, data: ...)` for all output
- Symlink to `~/bin/` for PATH access

## Git & GitHub

- Every tool is its own repo under `christiangenco/tool-cli`
- Add to `sync.sh` repos table for cross-machine syncing
- Add to `~/tools/AGENTS.md` for LLM discovery
- Credentials (`.env` files) are synced separately via `sync.sh creds`

## Gold Standard Examples

- **imessages-cli**: Ruby, self-contained executable, {ok,data} JSON, AGENTS.md, on PATH
- **google-cli**: Ruby, full {ok,data} envelope, comprehensive AGENTS.md, multiple subcommands
- **meta-ads-cli**: TypeScript, Commander, OAuth flow, .env.example, AGENTS.md
