# ArtBot (Public Release)

Official distribution build of ArtBot — the automated Discord Bot for OpenFront.io clans featuring FFA tracking, automated rank roles, live leaderboards, and ticket applications.

## Requirements
- **Node.js 20+** installed on your server/machine
- A **Discord Bot** created at [Discord Developer Portal](https://discord.com/developers/applications)

---

## Quick Start Guide

### 1. Installation
Clone the repository:
```bash
git clone https://github.com/LoSt543215543/artbot-public.git
cd artbot-public
npm install --omit=dev
```

### 2. Environment Configuration
Create your `.env` file from the example:
```bash
cp .env.example .env
```
Edit `.env` and provide your Discord bot token:
```env
DISCORD_TOKEN=your_discord_bot_token_here
CLIENT_ID=your_discord_client_id_here
```

### 3. Deploy Slash Commands
Register the bot's slash commands with Discord:
```bash
npm run deploy-commands
```

### 4. Start the Bot
```bash
npm start
```
*(Or use a process manager like PM2: `pm2 start dist/index.js --name artbot`)*

---

## In-Discord Configuration

Once the bot connects to your Discord server:

1. **/setup**  
   Run `/setup` to open the interactive setup panel. Configure your clan tag, applicant roles, clan member roles, logs channels, and OpenFront authentication.
2. **/application panel**  
   Post a permanent, customizable self-service Clan Application & Account Linking button panel (or customize its title, description, and button text via `/application setup` or `/cfg`).
3. **/cfg** *(or **/config**)*  
   Open the interactive server configuration dashboard to manage rank tiers, economy, raffles, tournament scoring, nickname sync, and auto-unlink rules.
4. **/settoken**  
   Update or refresh your OpenFront `refreshToken` cookie at any time.
5. **/update**  
   Whenever a new version of ArtBot is released, run `/update` directly in Discord to automatically pull the latest release and update your bot.

---

## Changelog

For a full list of changes across all versions, see [CHANGELOG.md](./CHANGELOG.md).
