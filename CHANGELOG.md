# 📋 ClanBot Changelog

All notable changes, new features, and bug fixes for ClanBot across every release are documented here.

---

## [v1.0.25] - 2026-10-17
### 🏷️ In-Game Clan Tag Requirement for Event Scoring
- **Mandatory Clan Tag Filtering**:
  - Enforced in-game clan tag verification on competition match processing: matches are only counted for tournament points if played under the server's configured clan tag (`[clanTag]`).
  - Matches played under other clan tags, without a clan tag, or while unaffiliated in-game are automatically excluded from event rankings.
- **Enhanced Event & Leaderboard Messaging**:
  - Displayed explicit `🏷️ Clan Tag Requirement` fields in `/event` creation and active status embeds.
  - Added clan tag requirement reminders to the `/leaderboard` scoring description and upcoming tournament embeds.

---

## [v1.0.24] - 2026-10-17
### 🏆 Tournament Scoring & Competition Leaderboard Fixes
- **Accurate OpenFront Games Tracking**:
  - Fixed competition scoring endpoint resolution: transitioned from deprecated/non-existent `/history` endpoint to the live `/public/player/:publicId/games` endpoint with cursor-based pagination.
  - Implemented exact game attribute parsing matching OpenFront's API schema (`start`, `totalPlayers`, `result === "victory"`).
  - Added robust match filtering: automatically filters out disconnected/incomplete games (`result === "incomplete"`), singleplayer bot games (`type === "Singleplayer"`), and games with $\le 1$ player.
  - Introduced smart 60-second in-memory games caching with historical depth tracking to maximize performance while minimizing API requests.
- **Enhanced Leaderboard Display & Member Tracking**:
  - Cross-references registered Discord participants and linked clan members against in-game roster snapshots so active tournament entrants are never omitted.
  - Refined leaderboard ranking: sorts strictly by tournament points descending, victories descending, and games played descending.
  - Formats active participants cleanly on the leaderboard, hiding clutter when members haven't played yet and displaying helpful fallback status messages.
- **Granular `/event` Command Access**:
  - Allowed all clan members to run `/event` to view current tournament schedule and registration status, while preserving admin-only restrictions on tournament modification options (`start_date`, `end_date`, `duration_minutes`, `clear`, etc.).

---

## [v1.0.23] - 2026-10-17
### 🔒 Command Access Control & Permission Verification Fixes
- **Resilient Member Permissions & Role Evaluation**:
  - Fixed an uncaught TypeError in `isGuildAdmin` and `canRunCommand` when handling Discord button interactions from uncached or API members (`member.guild.ownerId`, `member.permissions.has`, and `member.roles.cache.has`).
  - Added multi-format resolution supporting `GuildMember` instances, `APIInteractionGuildMember` objects, raw string/bigint permission bitfields, and direct role arrays.
- **Fixed Command Access Toggle Buttons in `/config`**:
  - Fixed button interaction routing in the Command Access Control dashboard so clicking command access toggle buttons cleanly updates command permissions in real time.
  - Optimized the layout of command buttons using 5 buttons per row (reducing rows from 5 to 3), preventing action row overflows and button cutoff.
- **Dynamic Permission Integration Across All Commands**:
  - Replaced hardcoded `isGuildAdmin` checks with dynamic `canRunCommand` checks across `setup`, `update`, `link`, `unlink`, and `reapprove`.
  - Removed hardcoded `setDefaultMemberPermissions` from `cfg` so custom admin roles configured in settings have full access.

---

## [v1.0.22] - 2026-10-17
### 📋 Inline Update Changelogs & File Integrity
- **Changelog Display on Update Completion**:
  - The `/update` (and `!cb update`) command now retrieves and displays the release notes and changelog bullets directly in the final confirmation message before the bot process restarts.
  - Formatted cleanly as a `📋 What's New in v<version>:` block, safely truncated to 1,000 characters to prevent Discord message length overflow.
- **Auto-Updater Changelog Preservation**:
  - Enhanced the file replacement step in `applyUpdate()` to automatically copy and update `CHANGELOG.md` alongside `tsconfig.json` and `dist/`.
  - Added `getChangelogForVersion(version)` to extract release-specific sections directly from `CHANGELOG.md` using regex parsing as an offline or fallback source.

---

## [v1.0.21] - 2026-10-17
### ⌨️ Prefix System Refinements & Dismiss Button Optimization
- **Default Prefix Changed to `!cb`**:
  - Changed default command prefix from `!` to `!cb` (e.g. `!cb help`, `!cb stats`, `!cb link`) to avoid collisions with other popular Discord bots.
  - Supports both `!cb <command>` and `!cb<command>` formats, as well as bot @mentions (`@ClanBot <command>`).
- **Dedicated `/prefix` Command**:
  - Added `/prefix [new_prefix]` (and `!cb prefix [new_prefix]`) allowing administrators to quickly inspect or update the server prefix directly from chat.
- **Config Window Deduplication**:
  - Excluded the dismiss button from `/config` / `!cb config` menus, eliminating the redundant button alongside the existing `❌ Close Window` control.
- **Universal Dismiss Button Support**:
  - Ensured all other prefix command replies (across initial replies, deferred edits, followups, and errors) include the `🗑️ Dismiss` button so callers can easily clean up bot responses.
- **Visual Prefix Button in `/config`**:
  - Added a dedicated prefix management button (`⌨️ Prefix: <prefix>`) directly on the main settings dashboard.

---

## [v1.0.20] - 2026-10-17
### ⚡ Instant Dual-Dispatch Command System & Ephemeral Help
- **Instant Prefix Command Support (`!command` & `@ClanBot command`)**:
  - Added support for prefix-based commands running alongside Discord slash commands.
  - Bypasses Discord's client caching and global command propagation delays, executing text commands instantly in chat upon deployment.
  - Supports default prefix `!`, bot @mentions (`@ClanBot command`), and server-customizable prefixes via `/config`.
  - Built an internal `MessageCommandAdapter` that seamlessly translates Discord text messages to the application command runner with positional and subcommand parsing.
  - Added interactive "🗑️ Dismiss" buttons on prefix command replies that mimic ephemeral behavior.
- **New Ephemeral `/help` & `!help` Command**:
  - Added a categorized command directory covering Account Linking, Stats & Ranks, Support Tickets, Clan Economy & Raffles, Events & Competitions, and Administration.
  - Strictly marked **ephemeral** on Discord slash commands so the help directory is visible only to the member who executed it.

---

## [v1.0.19] - 2026-10-17
### 🔗 Pre-Linking Member Support & Redundant Link Optimization
- **Self-Linking for Pre-Existing Members**:
  - Adjusted the redundant link prevention checks across `/link`, `/application panel`, and `executeLinkFlow` to specifically test `hasMemberRole && isAlreadyLinked`.
  - Server members who obtained the "Clan Member" role prior to the introduction of the linking system or through manual role assignment can now link their OpenFront account without encountering a roadblock.
  - Users who are both actively linked in the database and holding the member role remain gracefully blocked from redundant re-linking.
  - Full staff override functionality (`/link public_id:<id> user:<target>`) is preserved for admins and reviewers.

---

## [v1.0.18] - 2026-10-17
### 🔒 Ticket System Hardening & Link Flow Restrictions
- **Mandatory Reason Modal on Ticket Close**:
  - All close buttons across support and application tickets (`ticket-*` and `link-*`) now open a modal prompting for a mandatory closing reason.
  - Button labels updated to **`Close with Reason`** with a lock icon.
- **Mod+ Only Ticket Closure**:
  - Restricted ticket closure permissions strictly to server staff (Administrators, Manage Channels, Moderate Members, or configured Support / Reviewer / Admin roles).
  - Ticket openers and regular members can no longer close tickets.
- **New Slash Command**:
  - Added `/ticket close [reason]` allowing moderators to close any ticket directly from chat with a documented reason.
- **Transcript Logging**:
  - The closing reason, staff member, and timestamp are permanently recorded in the generated transcript log and transcript channel embed.
- **Redundant `/link` Protection**:
  - Prevented users who already possess the configured clan member role from using `/link` or submitting duplicate applications on `/application panel`.
  - Informs existing members that re-linking is redundant while preserving staff overrides (`/link public_id:<id> user:<target>`).

---

## [v1.0.17] - 2026-10-16
### ⭐ OpenFront Player Levels & Autonomous `/update` Installer
- **Live Player Levels & XP Progression**:
  - Integrated OpenFront's level and progression system into `/stats`.
  - Added live level badges (`👑 Legend (Lv. 100)`, `🎖️ Level X (Prestige Y)`, `⭐ Level X`).
  - Added dynamic 10-segment XP progress bars with current, needed, and total lifetime XP.
  - Automatically caches player level, prestige, and legend status in the database.
- **Autonomous `/update` Overhaul**:
  - Upgraded `/update` to autonomously handle Git pulls (`git pull origin main`) or release tarball fallback downloads.
  - Automatically generates `tsconfig.json` and patches `package.json` to prevent build failures on hosting providers (Railway, Render, Pterodactyl).
  - Automatically executes dependency synchronization (`npm install --omit=dev --no-audit --no-fund`).
  - Automatically deploys updated slash commands to Discord (`node dist/deploy-commands.js`).
  - Added real-time step-by-step progress edits in Discord before graceful reboot.

---

## [v1.0.16] - 2026-10-16
### 🚀 Linking System Overhaul, Application Panel & Hardcore Obfuscation
- **Unified Link Architecture (`LinkService`)**:
  - Centralized OpenFront ID verification, membership checks, in-game admission, and role assignment.
  - Added Discord `guildMemberRemove` listener to automatically strip clan roles and track left members.
  - Added Discord `guildMemberAdd` listener to restore linked roles if a departing member rejoins.
  - Added two-way clan auditing to cleanly separate transient API errors from actual clan departure.
- **Permanent Application Panel**:
  - Added `/application panel` for persistent, interactive self-service account linking and membership applications.
  - Customizable title, description, and button text configurable via `/application setup` and `/cfg`.
- **Bug Fixes**:
  - Fixed duplicate clan join request spam by adding idempotency caching.
  - Corrected raffle team-wins calculation logic.
  - Fixed tournament `/join` double counting.
  - Resolved headless browser client mutex deadlocks during concurrent requests.
- **Hardcore Cryptographic Obfuscation**:
  - Upgraded release pipeline to 4-stage polymorphic obfuscation: monolithic esbuild bundle → Pass 1 deep AST obfuscation → Brotli compression (q=11) → 64-byte rolling XOR → authenticated AES-256-GCM encryption with 10 split key shards and SHA-256 integrity check → Pass 2 VM loader armor.

---

## [v1.0.15] - 2026-10-15
### 🏷️ Dynamic Branding
- Replaced hardcoded bot names with dynamic, server-agnostic guild branding across all embeds, notifications, and logging.

---

## [v1.0.14] - 2026-10-15
### 🛡️ Licensing & Remote Stability
- Added remote server licensing validation and stability enforcement.

---

## [v1.0.13] - 2026-10-15
### 🔄 Background Auto-Updater
- Introduced automated 3-hour update checks with toggleable `/update auto_update:[true|false]`.
- Implemented robust semantic version comparison and persistent version tracking in `data/version.json`.

---

## [v1.0.12] - 2026-10-15
### 📡 Diagnostics & Telemetry
- Added automated developer diagnostic check-ins and environment validation.

---

## [v1.0.11] - 2026-10-15
### 🧹 Command Registration Cleanup
- Streamlined global and guild-specific slash command deployments to eliminate duplicate command entries in Discord.

---

## [v1.0.10] - 2026-10-14
### 👑 Administration Enhancements
- Added superadmin bypass list and management tooling for cross-server support.

---

## [v1.0.9] - 2026-10-14
### 🎖️ Rank Tier Customization
- Added `ranksIncludeTeamWins` configuration toggle, allowing server owners to decide whether team wins factor into rank role tiers alongside FFA wins.

---

## [v1.0.8] - 2026-10-13
### 📊 Statistics Accuracy & Parsing
- Prioritized live clan roster data for exact clan contributions.
- Improved player profile parsing across multiple game categories (Public, Private, Singleplayer, Ranked 1v1).

---

## [v1.0.7] - 2026-10-13
### 📑 Roster Pagination & Direct Profile Fallbacks
- Added multi-page fetching for large clans to retrieve up to 1,000 members via the OpenFront API.
- Enabled direct player profile lookups in `/stats` and `/sync` if a member is not yet in the initial cached roster.

---

## [v1.0.6] - 2026-10-13
### 🔍 Strict Player & Clan Verification
- Added cross-checks verifying that a player exists on OpenFront and is actively in the clan before granting roles or linking accounts.

---

## [v1.0.5] - 2026-10-12
### ⚙️ Headless Browser Isolation
- Protected browser evaluation contexts against node-scope identifier collisions and obfuscation variable clashes.

---

## [v1.0.4] - 2026-10-12
### ⚡ Instant Auto-Join for Open Clans
- Allowed instant automated account linking without opening ticket channels when a clan is configured as open with auto-join enabled.

---

## [v1.0.3] - 2026-10-12
### 📦 Monolithic Distribution Packaging
- Switched distribution builds to monolithic esbuild bundles with multi-pass code obfuscation.

---

## [v1.0.2] - 2026-10-11
### 📝 Open Clan Applications & Review Modes
- Added support for open clans, ticket interviews, and customizable review modes.

---

## [v1.0.1] - 2026-10-10
### 🛠️ Initial Refinements & Fixes
- Addressed initial edge cases in rank assignment, database schema synchronization, and Discord role caching.

---

## [v1.0.0] - 2026-10-09
### 🎉 Initial Release
- **Clan Stats & Roster Sync**: Automatic synchronization of OpenFront.io clan members, FFA wins, and team wins.
- **Automated Rank Roles**: Dynamic rank assignment based on customizable win thresholds.
- **Custom Economy (Artcoins)**: Server currency awarded for game wins, with admin minting, transfers, and balance checks.
- **FFA Tournaments & Competitions**: Automated point calculation based on game player counts and placements.
- **Raffles & Tickets**: Interactive raffle ticket purchases, countdowns, and automated winner drawings.
- **Interactive Configuration Dashboard**: Webhook and button-based `/cfg` dashboard for total server management.
