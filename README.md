# agent-toolkit

My curated collection of CLI tools for automating business operations, designed to work standalone or as an LLM agent's toolbox. Each tool follows a [standard contract](BEST_PRACTICES.md): JSON output, `.env` credentials, global PATH access.

Custom tools live in `~/tools/` as individual git repos. Third-party tools are installed via Homebrew/npm.

## Quick start

```bash
# Clone this repo (it's the index, not the tools themselves)
git clone https://github.com/christiangenco/agent-toolkit ~/tools/agent-toolkit

# Clone all custom tools + install deps
~/tools/agent-toolkit/sync.sh clone

# Copy credentials from another machine
~/tools/agent-toolkit/sync.sh creds pull
```

## Tools

### Communication

| Tool | What it does | Install |
|------|-------------|---------|
| [google-cli](https://github.com/christiangenco/google-cli) | Gmail, Calendar, Drive, Tasks | custom (Ruby) |
| [imessages-cli](https://github.com/christiangenco/imessages-cli) | Read/send iMessages, search history | custom (Ruby, macOS) |
| [twilio-sms-cli](https://github.com/christiangenco/twilio-sms-cli) | SMS via Twilio + conversation threads | custom + `brew install twilio` |
| [calendly-cli](https://github.com/christiangenco/calendly-cli) | Manage event types, bookings, availability | custom (Ruby) |
| [contacts-cli](https://github.com/christiangenco/contacts-cli) | Search local contacts CSV | custom (Ruby) |
| [groove-cli](https://github.com/christiangenco/groove-cli) | Helpdesk inbox triage with Claude | custom (Ruby) |

```bash
google-cli emails list --limit 5                     # Recent inbox
google-cli emails send --to "a@b.com" --subject "Hi" --text "Hello"
google-cli calendar list                              # Next 7 days
google-cli calendar create --summary "Lunch" --start "2026-03-01T12:00:00" --end "2026-03-01T13:00:00"
imessages-cli threads --limit 10                      # Recent conversations
imessages-cli send "mom" "On my way"                  # Send by alias
twilio api:core:messages:create --from "+18176685965" --to "+1234567890" --body "Hello" -o json
calendly-cli types --active                           # Active booking links
calendly-cli events --status active                   # Upcoming bookings
```

### Advertising

| Tool | What it does | Install |
|------|-------------|---------|
| [google-ads-cli](https://github.com/christiangenco/google-ads-cli) | Google Ads campaigns, ad groups, conversions | custom (TypeScript) |
| [meta-ads-cli](https://github.com/christiangenco/meta-ads-cli) | Facebook/Instagram campaigns, creatives, audiences | custom (TypeScript) |
| [tiktok-ads-cli](https://github.com/christiangenco/tiktok-ads-cli) | TikTok campaigns, ad groups, creatives | custom (TypeScript) |
| [x-ads-cli](https://github.com/christiangenco/x-ads-cli) | X/Twitter campaigns, line items, promoted tweets | custom (TypeScript) |
| [x-cli](https://github.com/christiangenco/x-cli) | Organic X/Twitter posting, threads, timeline | custom (TypeScript) |
| [linkedin-cli](https://github.com/christiangenco/linkedin-cli) | LinkedIn campaigns, creatives, audiences | custom (TypeScript) |
| [dataforseo-cli](https://github.com/christiangenco/dataforseo-cli) | Keyword research, search volume, domain metrics | custom (Ruby) |

```bash
google-ads-cli campaigns list                         # All campaigns
meta-ads-cli campaigns create --name "Sale" --objective OUTCOME_TRAFFIC --budget 10
tiktok-ads-cli insights --level campaign --date-range last_7d
x-ads-cli analytics                                   # Last 7 days performance
```

All ad tools default to JSON output. Add `--pretty` for human-readable tables.

### Payments

| Tool | What it does | Install |
|------|-------------|---------|
| [stripe](https://stripe.com/docs/stripe-cli) | Products, prices, customers, payment links, webhooks | `brew install stripe/stripe-cli/stripe` |

```bash
stripe products list
stripe customers create --email="a@b.com" --name="Jane"
stripe payment_links create --line-items[0][price]=price_XXX --line-items[0][quantity]=1
stripe listen --forward-to localhost:3000/webhooks
```

### Domains & DNS

| Tool | What it does | Install |
|------|-------------|---------|
| [namecheap-cli](https://github.com/christiangenco/namecheap-cli) | Domain registration, DNS management | custom (Ruby) |
| [vercel](https://vercel.com/docs/cli) | Deploy frontend apps, manage projects | `npm i -g vercel` |

```bash
namecheap-cli check example.com                       # Check availability
namecheap-cli register example.com                    # Register
namecheap-cli dns:vercel example.com                  # Point to Vercel
vercel --prod                                         # Deploy to production
```

### Cloud & Infrastructure

| Tool | What it does | Install |
|------|-------------|---------|
| [doctl](https://docs.digitalocean.com/reference/doctl/) | DigitalOcean droplets, DNS, spaces | `brew install doctl` |
| [aws](https://aws.amazon.com/cli/) | AWS services (S3, EC2, Lambda, etc.) | `brew install awscli` |
| [gcloud](https://cloud.google.com/sdk/docs/install) | Google Cloud services | [installer](https://cloud.google.com/sdk/docs/install) |
| [firebase](https://firebase.google.com/docs/cli) | Firebase hosting, functions, Firestore | `npm i -g firebase-tools` |
| [supabase](https://supabase.com/docs/guides/cli) | Supabase DB, auth, edge functions | `brew install supabase` |
| [terraform](https://developer.hashicorp.com/terraform/cli) | Infrastructure as code | `brew install terraform` |
| [docker](https://docs.docker.com/engine/reference/commandline/cli/) | Containers | `brew install docker` |

```bash
doctl compute droplet list                            # List droplets
doctl compute droplet create web --region nyc1 --size s-1vcpu-2gb-amd --image ubuntu-24-04-x64 --wait
aws s3 ls                                             # List S3 buckets
gcloud compute instances list                         # List VMs
firebase deploy --only hosting                        # Deploy site
supabase db push                                      # Push migrations
```

### Web browsing & search

| Tool | What it does | Install |
|------|-------------|---------|
| [agent-browser](https://github.com/vercel-labs/agent-browser) | Sandboxed browser automation for agents | `npm i -g agent-browser` |
| [agent-chrome-cli](https://github.com/christiangenco/agent-chrome-cli) | Control user's real Chrome via CDP | custom (JS) |
| [ddgr](https://github.com/jarun/ddgr) | DuckDuckGo search from terminal | `brew install ddgr` |

```bash
agent-browser --session s1 open "https://example.com" && agent-browser --session s1 snapshot -c
agent-chrome-cli --tab t1 snapshot -ic                # Interactive elements in real Chrome
ddgr --json -n 5 "search query"                       # Web search as JSON
```

### macOS Automation

| Tool | What it does | Install |
|------|-------------|---------|
| [things-cli](https://github.com/christiangenco/things-cli) | Things 3 todos and projects | custom (Ruby, macOS) |
| [apple-notes-cli](https://github.com/christiangenco/apple-notes-cli) | Apple Notes CRUD and search | custom (Ruby, macOS) |
| [imessages-cli](https://github.com/christiangenco/imessages-cli) | iMessage read/send | custom (Ruby, macOS) |
| [cliclick](https://github.com/BlueM/cliclick) | Simulate mouse clicks/keyboard | `brew install cliclick` |

```bash
things-cli today                                      # Today's todos
things-cli add "Buy groceries" --when today           # Quick add
apple-notes-cli search "meeting notes"                # Search notes
apple-notes-cli create "Title" --body "Content"       # Create note
```

### Media

| Tool | What it does | Install |
|------|-------------|---------|
| [youtube-transcript-cli](https://github.com/christiangenco/youtube-transcript-cli) | Fetch YouTube transcripts | custom (Bash) |
| [yt-dlp](https://github.com/yt-dlp/yt-dlp) | Download YouTube/web video | `brew install yt-dlp` |
| [ffmpeg](https://ffmpeg.org/) | Video/audio conversion | `brew install ffmpeg` |
| [humanizer](https://github.com/brandonwise/humanizer) | Detect/remove AI writing patterns | `npm i -g .` in repo |

```bash
youtube-transcript-cli transcript dQw4w9WgXcQ         # Get transcript
yt-dlp "https://youtube.com/watch?v=ID"               # Download video
ffmpeg -i input.mov -c:v libx264 output.mp4           # Convert video
```

### Property Management

| Tool | What it does | Install |
|------|-------------|---------|
| [appfolio-workorder-cli](https://github.com/christiangenco/appfolio-workorder-cli) | AppFolio maintenance work orders | custom (Bash) |

```bash
appfolio-workorder-cli list                           # Open work orders
appfolio-workorder-cli create "Leaky faucet in unit 4"
appfolio-workorder-cli update 19397-1 "Plumber scheduled for Tuesday"
```

### Dev Tools

| Tool | What it does | Install |
|------|-------------|---------|
| [gh](https://cli.github.com/) | GitHub repos, PRs, issues, actions | `brew install gh` |
| [rg](https://github.com/BurntSushi/ripgrep) | Fast recursive text search | `brew install ripgrep` |
| [fd](https://github.com/sharkdp/fd) | Fast file finder | `brew install fd` |
| [jq](https://jqlang.github.io/jq/) | JSON processor | `brew install jq` |
| [fzf](https://github.com/junegunn/fzf) | Fuzzy finder | `brew install fzf` |
| [eza](https://github.com/eza-community/eza) | Modern `ls` replacement | `brew install eza` |

```bash
rg "TODO" --type ts                                   # Search TypeScript files
fd "\.env" ~/tools                                     # Find .env files
gh pr list                                             # List open PRs
gh issue create --title "Bug" --body "Details"
cat data.json | jq '.items[] | .name'                 # Parse JSON
```

### Background Processes

| Tool | What it does | Install |
|------|-------------|---------|
| [tmux](https://github.com/tmux/tmux) | Terminal multiplexer for background tasks | `brew install tmux` |

```bash
tmux new-session -d -s server "npm run dev"           # Start background process
tmux capture-pane -t server -p -S -50                 # Read recent output
tmux kill-session -t server                           # Stop it
```

⚠️ Never run `tmux kill-server` — it kills all sessions including the agent's own.

## Output format

All custom tools output JSON to stdout:

```json
{"ok": true, "data": [...]}
{"ok": false, "error": "message"}
```

Add `--pretty` (where supported) for human-readable tables. Pipe through `jq` for formatting.

## Credentials

Custom tools store credentials in `.env` files (gitignored). See each tool's `.env.example`.

Third-party tools use their own auth:
- stripe: `stripe login` → `~/.config/stripe/`
- twilio: `twilio profiles:create` → `~/.twilio-cli/config.json`
- doctl: `doctl auth init` → `~/.config/doctl/config.yaml`
- aws: `aws configure` → `~/.aws/credentials`
- gcloud: `gcloud auth login` → `~/.config/gcloud/`
- gh: `gh auth login` → `~/.config/gh/`
- vercel: `vercel login` → `~/.local/share/com.vercel.cli/`
- firebase: `firebase login` → stored by Google auth
- supabase: `supabase login` → `~/.supabase/`

## Syncing custom tools

```bash
~/tools/agent-toolkit/sync.sh              # git pull & push all repos
~/tools/agent-toolkit/sync.sh creds pull   # pull .env files from archy.local
~/tools/agent-toolkit/sync.sh creds push   # push .env files to archy.local
~/tools/agent-toolkit/sync.sh clone        # first-time setup: clone all repos
```

## Adding a new tool

See [BEST_PRACTICES.md](BEST_PRACTICES.md) for the full template. Quick checklist:

1. Create repo `christiangenco/{name}-cli`
2. Add README.md, AGENTS.md, .gitignore (always include `.env`), .env.example
3. Make it globally callable (`npm link` or symlink to `~/bin/`)
4. Output JSON `{ok, data}` to stdout by default
5. Add entry to this README and to `sync.sh`
