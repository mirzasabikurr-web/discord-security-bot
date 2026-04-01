# Workspace

## Overview

pnpm workspace monorepo using TypeScript. Each package manages its own dependencies.

## Stack

- **Monorepo tool**: pnpm workspaces
- **Node.js version**: 24
- **Package manager**: pnpm
- **TypeScript version**: 5.9
- **API framework**: Express 5
- **Database**: PostgreSQL + Drizzle ORM (Replit-managed)
- **Validation**: Zod
- **Build**: esbuild (ESM bundle for API), Vite (for dashboard)
- **Discord bot**: discord.js v14

## Structure

```text
/
├── artifacts/
│   ├── api-server/             # Express API server + Discord bot
│   └── discord-bot-dashboard/  # React + Vite web dashboard
├── lib/                        # Shared libraries
│   ├── api-client-react/       # React Query hooks for the API
│   ├── api-zod/                # Zod schemas for API responses
│   └── db/                     # Drizzle ORM schema + DB connection
├── pnpm-workspace.yaml
├── tsconfig.base.json
├── tsconfig.json
└── package.json
```

## Environment Variables

- `PORT=5000` — Dashboard frontend port
- `BASE_PATH=/` — Vite base path for dashboard
- `API_PORT=8080` — API server port
- `DATABASE_URL` — PostgreSQL connection string (Replit-managed secret)
- `DISCORD_TOKEN` — Discord bot token (user must supply)

## Workflows

- **Start application** — Runs the React dashboard on port 5000 (webview)

## Discord Security Bot Features

The bot (`artifacts/api-server/src/lib/discord-bot.ts`) includes:
- **Anti-Spam**: Detects message flooding, supports warn/mute/kick/ban
- **Anti-Raid**: Detects mass joins, supports lockdown/kick/ban
- **Anti-Link**: Blocks invites and/or URLs, allowlist support
- **Auto-Mod**: Banned words filter, excessive caps filter

### Required Setup
The bot needs a `DISCORD_TOKEN` secret set and **Privileged Gateway Intents** enabled in the Discord Developer Portal:
1. Go to https://discord.com/developers/applications
2. Select your application → Bot
3. Enable: **Server Members Intent** and **Message Content Intent**

### Bot Permissions
Invite the bot with these permissions: `ReadMessages`, `SendMessages`, `ManageMessages`, `KickMembers`, `BanMembers`, `ModerateMembers`

## Database Schema

All tables are in PostgreSQL (Replit-managed database):
- `guild_settings` — per-guild security configuration
- `mod_logs` — moderation action history
- `warnings` — per-user warning records
- `punishment_thresholds` — warning-count-based punishment rules

## API Endpoints (api-server on port 8080)

- `GET /api/healthz`
- `GET /api/bot/status`
- `GET /api/guilds`
- `GET /api/guilds/:guildId/settings`
- `PUT /api/guilds/:guildId/settings`
- `GET /api/guilds/:guildId/modlogs`
- `GET /api/guilds/:guildId/stats`
- `GET /api/stats/overview`

## Running the API Server

To run the full API server (requires DISCORD_TOKEN):
```bash
PORT=8080 pnpm --filter @workspace/api-server run dev
```

## Root Scripts

- `pnpm run build` — runs typecheck first, then recursively runs build in all packages
- `pnpm run typecheck` — runs tsc --build using project references
