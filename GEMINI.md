# GEMINI.md - OpenClaw Development Context

## Project Overview

**OpenClaw** is a powerful, personal AI assistant platform designed to run on your own devices. It provides a central control plane (the **Gateway**) that connects various messaging channels (WhatsApp, Telegram, Slack, Discord, etc.) with AI agent runtimes and device-local nodes.

- **Main Technologies:** TypeScript, Node.js (≥22), pnpm, Vitest, Express/Hono, TypeBox, Docker.
- **Architecture:**
  - **Gateway (src/gateway):** The core control plane using WebSockets to manage sessions, presence, and events.
  - **Pi Agent (src/agents):** The agent runtime based on `pi-mono`, supporting tool streaming and coordination.
  - **Extensions (extensions/):** A modular system for channels (messaging integrations), memory, and specialized tools.
  - **Nodes (src/node-host):** Device-local executors for macOS, iOS, and Android companion apps.
  - **Control UI (ui/):** A web-based dashboard for managing the gateway and agents.
  - **CLI (src/cli):** The `openclaw` command-line entry point.

## Building and Running

The project uses `pnpm` for workspace management and `tsx` for running TypeScript directly during development.

### Key Commands

- **Install dependencies:** `pnpm install`
- **Full build:** `pnpm build` (builds both core and UI assets)
- **Onboarding/Setup:** `pnpm openclaw onboard` (recommended for new setups)
- **Start Gateway (Dev Mode):** `pnpm gateway:watch` (auto-reloads on changes)
- **Start Gateway (Normal):** `pnpm gateway:dev`
- **Run CLI directly:** `pnpm openclaw <command>`
- **Build UI:** `pnpm ui:build`

### Testing

- **Run all tests (parallel):** `pnpm test`
- **Unit tests (fast):** `pnpm test:fast` (uses Vitest)
- **Integration tests:** `pnpm test:e2e`
- **Live tests (requires real credentials):** `pnpm test:live`
- **Watch mode:** `pnpm test:watch`

## Development Conventions

- **Language:** Strictly TypeScript. Use `tsx` for running scripts.
- **Code Style:**
  - Linting: `oxlint` (run via `pnpm lint`).
  - Formatting: `oxfmt` (run via `pnpm format`).
  - Monorepo boundaries: Rigorously enforced; follow the `plugin-sdk` for extensions.
- **Testing:** New features or bug fixes MUST include Vitest tests. Prefer `src/**/*.test.ts` placement.
- **UI Development:**
  - The Control UI (in `ui/`) uses **Lit with legacy decorators** (`@state`, `@property`). Do NOT use standard `accessor` decorators yet.
- **Security:**
  - Inbound messages are treated as untrusted.
  - Non-main sessions (groups/channels) should run in Docker sandboxes (`agents.defaults.sandbox.mode: "non-main"`).
- **PR Guidelines:**
  - One topic per PR.
  - AI-assisted PRs are welcome but must be marked as such in the description.
  - If you have access to `codex`, run `codex review --base origin/main` locally before submitting.

## Project Structure

- `src/`: Core logic, Gateway, and CLI.
- `extensions/`: Modular integrations (Channels, Memory, Auth).
- `apps/`: Native companion apps (macOS, iOS, Android).
- `packages/`: Shared internal libraries.
- `ui/`: Web-based Control UI (Lit-based).
- `docs/`: Extensive documentation (Mintlify-ready).
- `scripts/`: Build and maintenance scripts.
- `skills/`: Bundled AI skills/prompts.

## Common Tasks (Reference)

- **Adding a Channel:** Look at `extensions/whatsapp` or `extensions/telegram` for reference. Channels must implement the `MessagingProvider` interface.
- **Adding a Tool:** Tools are typically defined within agents or as workspace skills.
- **Configuring the Gateway:** Config lives in `~/.openclaw/openclaw.json`. See `docs/gateway/configuration.md` for all keys.
