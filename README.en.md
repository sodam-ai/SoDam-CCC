# SoDam-CCC — Talk to Your Computer From Telegram

> 🇺🇸 English version. 한국어 기본 문서: [`README.md`](./README.md)
> This document is written for **people who have never coded before**, and for **people using AI, messaging apps, computers, or mobile devices for the first time.** Any technical term is explained in one plain sentence the first time it appears.

> ⚠️ **An honest status note before you read further:**
> - **What has actually been confirmed live on this PC**: the full 12-step install flow (marketplace registration → plugin install → token registration → pairing → safety lock) has been run in a fresh session and confirmed working. `/sodam-ccc:status` and `/sodam-ccc:fix` have also been run live and confirmed working.
> - **What has NOT been confirmed yet**: an actual **round trip — sending a message from the phone and receiving a reply** — has not been confirmed end-to-end as of this writing. This is not a bug; it's simply that the verification step (switching to a newly created bot mid-project) hasn't finished yet.
> - So some "this is what success looks like" notes below are **expected behavior based on design, code, and official documentation**, clearly distinguished from "confirmed 100% identical on an actual phone screen." This note will be removed once confirmed.

---

## 1. What Is This?

**One-sentence summary**: When you send a text message from your phone (via the Telegram app), Claude Code (an AI coding assistant) running on your computer reads it, does the work, and texts you back — this official capability is called **Claude Code Channels**. SoDam-CCC is a helper tool that makes **first-time setup, re-setup, and troubleshooting** of that feature painless.

```
[Phone: send "summarize my meeting notes"]
              │
              ▼
   ( through the channel SoDam-CCC helped you set up )
              │
              ▼
[Your computer's Claude Code does the work]
              │
              ▼
   [Reply arrives back on your phone]
```

SoDam-CCC itself is **not** the messaging feature. Sending and receiving messages (Claude Code Channels) is an **official Anthropic (the maker of Claude Code) feature**. SoDam-CCC is an assistant that installs, checks, and repairs that official feature for you.

### What it can do
- Check exactly **which of the 12 setup steps** are already done.
- Guide you through **only the steps that are missing**, in order (never re-asks you to redo a finished step).
- When the bot suddenly goes silent, **check the likely causes** and pinpoint the problem.
- Enforce the safety setting ("only approved people can send commands") so it can never be skipped.
- With your consent, **launch and stop** the background process that turns the channel on (explained below).

### What it cannot do
- It does not install Claude Code itself (Claude Code must already be installed and signed in).
- It does not create the Telegram bot for you (that's a phone-side, human-only step).
- It never fills in your bot token (a secret, password-like value) for you — **SoDam-CCC never sees, asks for, or stores this value.**

---

## 2. Table of Contents

1. [What Is This?](#1-what-is-this)
2. Table of Contents (this page)
3. [Prerequisites](#3-prerequisites-what-you-need-to-have)
4. [Required Programs](#4-required-programs-what-you-need-to-install)
5. [Download / Installation](#5-download--installation)
6. [Quick Start](#6-quick-start-check-in-3-minutes)
7. [How to Run It](#7-how-to-run-it)
8. [How to Use It](#8-how-to-use-it-what-to-text-from-your-phone)
9. [How It Works](#9-how-it-works)
10. [Command Reference](#10-command-reference)
11. [Changelog](#11-changelog)
12. [File & Document Locations](#12-file--document-locations)
13. [Workflow](#13-workflow)
14. [Architecture](#14-architecture)
15. [Security & Data Flow](#15-security--data-flow)
16. [Troubleshooting](#16-troubleshooting)
17. [FAQ](#17-faq)
18. [Legal · Copyright · License · Commercial Use](#18-legal--copyright--license--commercial-use)

---

## 3. Prerequisites (What You Need to Have)

| Item | Description |
|---|---|
| **A smartphone** | Any device that can install the Telegram app (iOS or Android) |
| **The Telegram app** | A messaging app like KakaoTalk or LINE. Search "Telegram" in your app store |
| **Internet connection** | Both the phone and the PC need to be online |
| **A powered-on PC** | ⚠️ **If the PC is off, phone messages simply disappear.** They are not queued for later. See [9. How It Works](#9-how-it-works) for why |
| **A paid Claude account** | Claude Pro or Max plan. This document assumes a personal (Pro/Max) account |

---

## 4. Required Programs (What You Need to Install)

| Program | What it is | Why it's needed |
|---|---|---|
| **Claude Code** | The AI coding assistant app you run on your PC (in a terminal/PowerShell window) | Everything here runs on top of it. **Assumed already installed and signed in** — SoDam-CCC does not help install Claude Code itself |
| **Bun** | A JavaScript runtime (most users just need to know the name) | The Telegram channel feature runs on top of this internally. If it's missing, `/sodam-ccc:install` tells you how to install it |
| **The Telegram bot plugin (`telegram`)** | A component provided by Claude Code's official marketplace | This is the actual part that sends and receives phone↔PC messages. `/sodam-ccc:install` walks you through installing it |

> Exact install commands and version numbers can vary (this is still an evolving preview feature), so this document doesn't hard-code them. Run `/sodam-ccc:install` to see exactly what's missing right now.

---

## 5. Download / Installation

1. **Inside Claude Code**, type:
   ```
   /sodam-ccc:install
   ```
2. It checks how many of the 12 steps are already done, then guides you through **only what's missing**.
3. Some steps happen **on your phone** (creating the bot, copying the token, pairing). SoDam-CCC always tells you whether a step happens "on your phone" or "inside Claude Code."
4. Installation is only marked "complete" once the safety lock (restricting who can send commands) is in place. **This step is never skipped** — SoDam-CCC is designed to never say "done" while the lock is missing.

> ✅ **This is what success looks like** (by design): at every step you'll see how far along you are (e.g., "7/12 steps") and exactly one next action. Steps already finished are shown briefly as "already done" and skipped.

> 💡 **If you're asked to choose a plugin scope, always pick `user`.** Picking anything else leads to the "it works in one folder but not another" problem (see Troubleshooting #5).

> 💡 **If the plugin can't be found** — your PC may have automatic updates turned off. Type `/plugin marketplace update claude-plugins-official` **inside Claude Code** once, then try again. `/sodam-ccc:install` checks for this too.

> ⚠️ **Reinstalling (already have a bot)?** SoDam-CCC keeps no history, so it judges purely from what it sees right now. If a token is already registered, it won't ask you to make a new bot — it reuses the existing one, cutting hands-on steps down to **3 or fewer** (copy token, confirm pairing code, approve pairing). A brand-new setup adds "create the bot" on top, for **4 or fewer**.

> ⚠️ **Still stuck?** → [17. FAQ #2](#17-faq) (bot not replying) or type `/sodam-ccc:fix` **inside Claude Code**.

---

## 6. Quick Start (Check in 3 Minutes)

The fastest way to see where you stand right now.

1. **Inside Claude Code**, type `/sodam-ccc:status` → instantly shows how far along you are and what's left.
2. If anything is missing, type `/sodam-ccc:install` **inside Claude Code** → walks you through only what remains.
3. Once all "N/12 steps" are filled in and the safety lock is on, **from your phone's Telegram, message your bot** — try "hello."
4. If you get a reply, you're done. Silence? → [17. FAQ #3](#17-faq) or `/sodam-ccc:fix`.

> ⏳ As noted at the top of this document, steps 3–4 (the phone round trip) haven't been confirmed end-to-end on this PC yet. Steps 1–2 (status check, install wizard) have been run multiple times and confirmed working.

---

## 7. How to Run It

To receive commands over Telegram, Claude Code needs to be started in a **special way** — a regular `claude` session cannot receive phone messages.

- **In PowerShell** (search "PowerShell" in the Windows search bar), type:
  ```
  claude --channels plugin:telegram@claude-plugins-official
  ```
- Phone messages are only received **while this session stays open.** Closing the window stops it immediately.
- `/sodam-ccc:install` shows you this exact command again whenever this step is needed.

### 🤖 SoDam-CCC can launch it for you

Instead of opening PowerShell and typing the command yourself, you can tell `/sodam-ccc:install` (or `/sodam-ccc:fix`) **inside Claude Code** to "turn the channel on," and SoDam-CCC will:
1. First **check whether it's already running** (it never launches a duplicate — two windows fighting over the same bot could conflict with each other).
2. If it's not running, show you the exact command it's about to run and **only proceed after you agree**, launching a new PowerShell window on your behalf.
3. Keep that window **visible, never hidden** — it never runs this silently in the background.
4. If this PC's other safeguards block the automatic launch (a normal, working second layer of protection), it automatically falls back to "please open PowerShell and type this command yourself."

The same applies to turning it off — ask SoDam-CCC to close the window it opened, and it will do so once you confirm.

> ✅ **This is what success looks like** (by design): the screen looks like a normal Claude Code launch, with no special error text.

> ⚠️ **Turning off the PC or closing this window** makes phone messages disappear ([17. FAQ #1](#17-faq)). **Sent something and got no reply?** → [17. FAQ #2–3](#17-faq) or run `/sodam-ccc:fix`.

---

## 8. How to Use It (What to Text From Your Phone)

Once setup is done and the channel is running, just message your bot **in plain, natural language** from Telegram on your phone — the same way you'd talk to Claude Code directly.

```
Examples:
"What's the weather like today?"        (your PC's Claude Code actually does the work)
"Show me the files I just created"
"Find the largest file in this folder"
```

### What doesn't work (by design — not a bug)
- **Slash commands don't work.** Commands like `/clear` or `/compact` are terminal-only and won't run over the channel (a feature request for this is tracked publicly).
- **References to past conversations, like "that thing from yesterday," won't be understood.** Telegram bots cannot look up past messages, and context doesn't carry over between sessions. Just restate what you need.
- **Replies sometimes look oddly cut off.** This is a Telegram message-length limit and can be adjusted (see Troubleshooting).

### When it asks for approval
When Claude Code wants to do something higher-risk — like running a command (`Bash`) or writing a file (`Write`) — **an approval prompt appears on your PC's terminal, and the session pauses until you respond.** You can approve this from your phone:
- Tap the **button** that appears in the Telegram message, or
- Type `yes <5-letter code>` as plain text

Nothing proceeds until it's approved. See [15. Security & Data Flow](#15-security--data-flow) for why this matters.

---

## 9. How It Works

```
[Phone: Telegram app]
       │  type & send a message
       ▼
[Telegram's own servers]   ← belongs to Telegram, not Anthropic or SoDam-CCC
       │
       ▼
[Your PC: Telegram channel plugin]   ← this must be "running" for the next step to happen
       │  forwards the message
       ▼
[Your PC: Claude Code session]        ← must be a session started with `--channels`
       │  actually does the work
       │  (asks for phone approval first if it's a risky action)
       ▼
[Your PC: result is generated]
       │
       ▼
[Telegram's servers] → [Phone: reply arrives in Telegram]
```

**The single most important fact**: if the middle of this chain (your PC) isn't alive, the whole thing breaks.

> ⚠️ **If the PC is off, or the window you started with `--channels` is closed, phone messages simply vanish.** They are not delivered later when you turn the PC back on — the channel wasn't alive at that moment, so the message was never relayed at all. (A feature to keep this always-on is planned for a later phase of this project, but doesn't exist yet.)

SoDam-CCC doesn't appear anywhere in this diagram — it's the assistant that helps you keep this chain alive by checking your setup, not a part of the message path itself.

---

## 10. Command Reference

**Commands you type inside Claude Code (SoDam-CCC)**

| Command | What it does |
|---|---|
| `/sodam-ccc:install` | Install wizard — guides you through only what's missing, in order |
| `/sodam-ccc:status` | Checks how far along you are and whether any risky settings remain |
| `/sodam-ccc:fix` | Checks likely causes when something's broken and tells you what to do |

**Official commands, typed inside Claude Code (SoDam-CCC shows the exact command and asks for consent before running it, or you type it yourself)**

| Command | What it does |
|---|---|
| `claude --channels plugin:telegram@claude-plugins-official` | (In PowerShell) starts Claude Code in the special mode that can receive channel messages |
| `/plugin marketplace add anthropics/claude-plugins-official` | Registers the official marketplace |
| `/plugin marketplace update claude-plugins-official` | Refreshes the marketplace catalog (needed when auto-update is off) |
| `/plugin install telegram@claude-plugins-official` | Installs the Telegram channel plugin (scope must be `user`) |
| `/reload-plugins` | Activates the plugin (only needed if prompted right after install) |
| `/telegram:configure <token from BotFather>` | Registers the bot token — **you fill in and run this yourself**; SoDam-CCC never sees this value |
| `/telegram:configure clear` | Deletes the registered bot token (resets the bot config) |
| `/telegram:access pair <5-letter code from your phone>` | Pairing (connects phone and bot) — **you fill in and run this yourself** |
| `/telegram:access policy allowlist` | **The safety lock** — restricts commands to approved senders only (the final required step of setup) |
| `/telegram:access set ackReaction 👀` | Turns on an automatic "message received" reaction (👀) |
| `/telegram:access remove <id>` | Removes a specific person from the allowlist |

**What you type from Telegram on your phone**

| Situation | What to type |
|---|---|
| An approval request arrives | Tap the button, or type `yes <5-letter code>` |

> These commands belong to a "research preview" feature and may change. If something doesn't work, try `/sodam-ccc:fix`.

> ⚠️ **Only one bot can be connected at a time.** Registering a new bot token automatically replaces the existing one (running two bots at once isn't supported by this plugin's design). To switch bots, tell `/sodam-ccc:fix` "I want to switch bots" for guidance.

---

## 11. Changelog

<details>
<summary><b>2026-08-21 — Added automatic channel process launch/restart and bot removal flow</b></summary>

- SoDam-CCC can now **launch the channel process** (`--channels`) on your behalf after your consent (with a duplicate-run check, and the window always stays visible)
- Added a shared procedure to handle "already running but stale config" (e.g., you just swapped bot tokens) — **terminates by exact process ID**, never by matching a process name, to avoid accidentally killing an unrelated process
- Added guidance for the official steps to **clear** a bot token or allowlist entry (`/telegram:configure clear`, `/telegram:access remove`)
- Fixed a false-positive bug where the security check (detecting risky launch flags) matched its own checking command
- Fixed plugin version handling so that `claude plugin update` no longer silently skips real content changes

</details>

<details>
<summary>2026-08-20 — First live end-to-end test run, safety lock actually resolved</summary>

- Ran `/sodam-ccc:install`, `/sodam-ccc:status`, and `/sodam-ccc:fix` live on this PC for the first time and confirmed the check logic works correctly
- The safety lock, which had been left open (`dmPolicy="pairing"`) for a while, was manually switched to locked (`allowlist`) and confirmed by re-checking
- Fixed a bug where file paths were referenced relatively, breaking when the plugin ran from its installed cache location

</details>

<details>
<summary>2026-08-19 — Fixed a progress-count error</summary>

- The 12-step progress counter was incorrectly including "phone approval support" (a fact that's always true and unrelated to actual progress), which made progress look further along than it really was. Fixed.

</details>

<details>
<summary>2026-08-09 — Initial Phase 1 design</summary>

- First design of the install wizard, status check, and troubleshooting skills
- Established the rule that the safety lock (restricting commands to approved senders) can never be skipped
- Designed phone approval and "message received" indicator guidance
- Wrote the first version of the 15-check (12 general + 3 security) and 11-symptom catalog

</details>

---

## 12. File & Document Locations

| What | Where | How SoDam-CCC treats it |
|---|---|---|
| Telegram settings (token, allowlist, pairing state) | `<your user folder>\.claude\channels\telegram\` | Only checks existence/pass-fail. **Never opens the token file (`.env`) contents** |
| Claude Code global settings & plugin cache | The `AppData\Roaming\claude-code\` folder (or `.claude` folder) | Read-only |
| This project's detailed technical documents (PRD) | The `.PRD` folder | Regular users don't need to read this — it's the design documentation used to build SoDam-CCC |
| This README (Korean/English, md/html) | Project root folder | `README.md` (Korean) · `README.en.md` (English) · `README.html` · `README.en.html` |

> ⚠️ Do not open files inside the Telegram settings folder yourself (especially `.env`, which holds the token). `/sodam-ccc:status` checks it safely (without ever opening it) and reports back.

---

## 13. Workflow

### Core flow
```
/sodam-ccc:status  →  find out how far along you are
     │
     ▼
/sodam-ccc:install →  only the missing steps, in order
     │
     ▼
Safety lock (allowlist) + phone approval check + received indicator (👀)
     │
     ▼
Text "hello" from your phone → a reply arrives  =  success
     │
     ▼
(if stuck) /sodam-ccc:fix → symptom → cause → fix
```

### Scenarios

**Scenario A — Starting fresh on a new PC**
1. Claude Code is already installed on the new PC
2. Run `/sodam-ccc:install`
3. It checks what you have, guides you through what's missing, step by step (including creating a bot; 4 or fewer hands-on steps)
4. Text "hello" from your phone → a reply arrives

**Scenario B — Picking up where you left off**
1. You started setting this up a while ago and don't remember how far you got
2. Run `/sodam-ccc:status`
3. It tells you exactly where you stopped — e.g. "the token is set, only pairing is left"
4. Continue from that exact step (reusing the existing bot; 3 or fewer hands-on steps)

**Scenario C — Sudden silence**
1. You texted from your phone but got no reply
2. Run `/sodam-ccc:fix`
3. It first runs all 15 checks automatically and shows failures first. If that doesn't find the cause, it asks about specific symptoms ("bot isn't replying at all," etc.) to narrow it down
4. It guides you to the matching fix (e.g., "an approval request is waiting on your PC — you can approve it from your phone")

### How the install wizard sorts steps internally (for reference)

`install` splits the 12 steps into five categories by nature, and never mixes their handling:

| Category | Example steps | How it's handled |
|---|---|---|
| **Read-only** | Checking a result | Just shows the result. Never asks you to act if there's nothing to do |
| **Human-only** | Creating the bot, copying the token (skipped on reinstall) | Guidance only; re-checks once you say it's done |
| **Official command with a secret value** | Registering the token, pairing | Shows only the command template — **you fill in the value and run it yourself**; SoDam-CCC never sees the value |
| **Official command, fixed text** | Registering the marketplace, installing the plugin, the safety lock | Shows the full command and asks for **fresh consent every time**, then re-checks after running it |
| **Channel process launch** | Turning `--channels` on/off | Checks for duplicates first → runs only after consent → keeps the window visible → confirms via re-check |

The reason for this split is simple: **secret values are never touched, and risky actions always get fresh consent.**

---

## 14. Architecture

### Project structure
```
SoDam-CCC/
├── .PRD/                  # Design documents (requirements, data model, phase plan, etc.)
├── .claude-plugin/
│   ├── plugin.json        # Plugin manifest (name, version, description)
│   └── marketplace.json   # Marketplace catalog (used for installation)
├── commands/               # Slash command entry points (/sodam-ccc:install, etc.)
├── skills/
│   ├── install/SKILL.md   # Install wizard logic
│   ├── status/SKILL.md    # Status check logic
│   ├── fix/SKILL.md       # Troubleshooting logic
│   └── tone/SKILL.md      # Shared beginner-friendly tone rules
├── data/
│   ├── checks.md          # The single source of truth for all 15 CHECK definitions
│   └── symptoms.md        # The 11-symptom troubleshooting catalog
└── README.md               # This document
```

### Trust boundary — the safety lines SoDam-CCC holds

This tool is different from a typical web app. There's no server, login, or sign-up — it's a **local (your-PC-only) tool.**

| Concept | In SoDam-CCC |
|---|---|
| Server / database / web page | **None** — everything runs only inside your own PC |
| Login / accounts / payment | **None** — a personal, single-user tool |
| **Who can send commands to your PC** (authorization boundary) | **The Telegram allowlist** — only people registered there |
| **The most sensitive secret** | **The bot token** (issued by Telegram) |
| **The most dangerous permission** | **Approval authority** — approving means a real file write or command execution happens |

### Three design principles (details in section 15)
1. **What isn't read can't leak** — the token file is only checked for existence, its contents are never opened
2. **What isn't touched directly can't be broken** — configuration changes only ever happen through Claude Code's official commands
3. **Only fixed strings are ever executed** — diagnostic commands never have externally-read values spliced into them (this structurally prevents command injection)

---

## 15. Security & Data Flow

### Where is my information stored?

The bot token (the most sensitive value here) is stored **only on your own computer** (`<your user folder>\.claude\channels\telegram\.env`). It is never sent to an external server, and SoDam-CCC never copies it anywhere.

**SoDam-CCC never opens this file's contents.** It only checks whether the file "exists or not" — like checking whether an envelope exists without ever opening it. Structurally, this means SoDam-CCC has no way to leak the token even if it wanted to.

### The allowlist = approval authority

Being on the Telegram allowlist isn't just "can send messages" — it means **can approve risky actions on your PC, like writing files or running commands.** Approval requests are sent to **everyone** on the allowlist. Because of this:
- SoDam-CCC **never automatically adds anyone besides you** to the allowlist.
- The moment setup finishes, it always displays how powerful this permission is.
- If two or more people are on the allowlist, `/sodam-ccc:status` warns you about it.

### Three-layer defense (output rules)

The three layers SoDam-CCC follows whenever it shows you something on screen.

```
Layer 1  Don't read it       → never accesses the token file's contents in the first place
Layer 2  Show only verdicts   → shows "found/not found, pass/fail" only, never raw file contents
Layer 3  Mask on the way out  → if a token-shaped string were ever about to be shown, it gets hidden right before display
```

### The approval flow (the security meaning behind section 8)

When Claude Code wants to write a file or run a command, it needs approval. In channel mode, this approval request can also reach your **phone** — convenient, but also the **single most powerful permission** in this whole system, which is why managing the allowlist carefully matters more than anything else here.

### Be careful with screenshots

If you plan to screenshot this tool's output and share it (with another person or online), double-check that it doesn't contain **your account folder path, user ID, or a token-shaped string.** SoDam-CCC's own output is designed to avoid showing these, but it can't mask what appears in a PowerShell window or any other screen it doesn't control.

### Automatic risk self-checks

`/sodam-ccc:status` and `/sodam-ccc:fix` automatically check the following every time:
- Is the allowlist 2 or more people? (warning)
- Is group-chat access turned on? (warning)
- Is there any sign of a risky launch flag (`--dangerously-...`) being used? (warning)

SoDam-CCC **never recommends or runs** risky launch flags (such as ones that skip permission checks).

---

## 16. Troubleshooting

`/sodam-ccc:fix` checks and guides you automatically, but here's a quick reference if you want to look something up yourself. Frequency: 🔴 very common · 🟡 occasional · 🟢 rare · ⚪ expected behavior (not a bug).

### 🔴 The bot isn't responding at all
Most common causes, in order: ① You ran plain `claude` instead of using `--channels` → close it and re-run the command in [section 7](#7-how-to-run-it). ② The Claude Code session itself is off → start a session (both the PC and the session need to be running). ③ The plugin isn't installed yet. ④ The token isn't registered yet.

### 🔴 It looks like the message sent, but there's no reply
The most common reason is **an approval request is waiting on your PC's terminal** (not a bug). Tap the button on your phone, or send `yes <5-letter code>`. It could also just be taking a while — turning on `ackReaction` (the "received" indicator) lets you at least confirm the message got through.

### 🔴 `/plugin install` is failing
Either the marketplace isn't registered yet (`/plugin marketplace add anthropics/claude-plugins-official`), or the catalog is stale (if auto-update is off, run `/plugin marketplace update claude-plugins-official` and try again).

### 🟡 The pairing code never arrives
The safety lock (`allowlist`) may already be on, and your account just isn't on the allowlist yet. Temporarily switch the policy back to `pairing`, complete pairing, then **make sure to switch it back to `allowlist`** (setup isn't considered complete until it's locked again). Also double-check the bot token itself is correct.

### 🟡 It works in one folder but not another
You likely chose `project` scope during install instead of `user`. Reinstall with `user` scope (see [section 5](#5-download--installation)).

### 🟡 It worked fine yesterday, but suddenly stopped
If you just updated Claude Code, this could be a temporary regression (this feature is still a preview). Check whether the version changed, and look for related public issue reports. It could also be a failed reconnection of an external tool (MCP) — restarting the session is worth trying.

### 🟢 Replies come back oddly cut off
This is a Telegram message-length limit. The reply-chunking settings can be adjusted.

### 🟢 It says the channel didn't register after install
Organization account policy may be turned off (this **doesn't apply to personal Pro/Max accounts**). Or the bot name might not have been specified correctly after `--channels`.

### ⚪ It didn't understand "that thing from yesterday" — not a bug
Telegram bots cannot look up past conversations. Just restate what you need.

### ⚪ Sending `/compact` or similar doesn't do anything — not a bug
Slash commands are terminal-only and currently don't run over the channel — this is expected behavior, not a failure.

### Still stuck?
Type `/sodam-ccc:fix` **inside Claude Code**. It automatically runs all 15 checks, shows failures first, and if it still can't find the cause, asks about specific symptoms to narrow things down.

---

## 17. FAQ

**Q1. Can I turn off my PC?**
No. If the PC turns off, or the channel session (the window opened with `--channels`) closes, phone messages disappear from that moment on. They don't arrive later once you turn it back on.

**Q2. The bot isn't replying.**
First check whether it was started with `--channels` (see [section 16](#16-troubleshooting)). `/sodam-ccc:fix` automatically checks the likely causes for you.

**Q3. I sent something and it's quiet.**
It may be waiting for approval on your PC (not a bug). Check the PC screen, or approve it from your phone.

**Q4. Can I approve from my phone?**
Yes. Tap the button that appears in the Telegram message, or send `yes <5-letter code>` as text.

**Q5. Can other people use this bot?**
Only people you've added to the allowlist. Keep in mind anyone on the allowlist gets the powerful ability to approve risky actions (file writes, command execution) on your PC — only add people who truly need that.

**Q6. I lost my bot token.**
Open your conversation with BotFather (the official bot management account) in Telegram and look up your bot list to find it again.

**Q7. How do I move to a new computer?**
The bot itself (its token) can be reused as-is. Install Claude Code and SoDam-CCC on the new PC, then run `/sodam-ccc:install` — it will guide you through the reinstall flow, reusing your existing bot.

**Q8. Does this cost money?**
SoDam-CCC itself is free (it's a personal tool, not sold to anyone). However, the paid Claude plan (Pro/Max) and each service's own terms apply separately.

**Q9. It didn't understand "that thing from yesterday."**
This is expected behavior (not a bug). Telegram bots cannot look up past conversations.

**Q10. Sending `/compact` or similar doesn't work.**
This is expected behavior (not a bug). Slash commands aren't yet supported over the channel.

**Q11. Isn't this risky?**
As long as the allowlist stays limited to just yourself and no risky launch flags (like ones that skip permission checks) are used, the structural risk here is fairly limited. SoDam-CCC checks and warns about both of these every time.

**Q12. Can I share a screenshot when asking for help?**
Yes. Just double-check first that your account path, user ID, or a token-shaped string isn't visible on screen, and mask it if it is.

---

## 18. Legal · Copyright · License · Commercial Use

> ⚠️ **This section is not legal advice.** Below, **confirmed facts** are strictly separated from **items that still need review.** If actual public release, distribution, delivery, or sale is ever planned, the "needs review" items must be confirmed by an actual legal professional first.

### Current status (facts)

| Item | Current value |
|---|---|
| **Distribution status** | **Private, for the developer's personal use only.** Not distributed, sold, or offered as a service to anyone else |
| **SoDam-CCC's own license** | **Unspecified.** There is no separate `LICENSE` file in this project |
| **External code dependencies** | **Zero.** No third-party code packages are included (only markdown configuration files and Claude Code's built-in features are used) |

### What "unspecified license" means (strict standard)

Under general copyright principles, **a work with no stated license is treated as "All Rights Reserved."** In other words, no one except the copyright holder (the developer) has been granted permission to **use, copy, modify, redistribute, or commercially exploit** it. This is not a problem right now since only the developer uses this privately, but the following 8 points are stated explicitly and clearly.

| Item | Current answer |
|---|---|
| **Copyright holder** | The developer who built this project personally — not a separate company or organization |
| **Scope of permitted use** | **Currently, the developer only.** No usage right is granted to any third party without prior permission |
| **Commercial use permitted?** | **No.** With no license in place, commercial use in any form (including sale or paid service) is not permitted |
| **Prohibited actions** | Copying, redistribution, resale, publishing derivative works, and commercial use — all prohibited without prior permission |
| **Limitation of liability** | The developer is not liable for any damage arising from use of this tool (misconfiguration, account issues, data loss, or otherwise) |
| **No warranty** | This tool is provided "as-is," with no warranty of fitness for a particular purpose or freedom from errors |
| **Caution about external material** | Each program this project depends on (see table below) carries **its own separate license or terms of service**, which apply independently of this document |
| **Modify / fork / redistribute / sell / operate as a service / use for education / deliver to a client** | **All currently not permitted** (unspecified license = all rights reserved) |

### Trademark & unofficial-status notice

SoDam-CCC is **not an official Anthropic (the maker of Claude Code) product, and has no affiliation or endorsement of any kind.** Its name is used only in the descriptive sense of "an unofficial personal tool for Claude Code," and it does not include any Anthropic or Telegram logos or trademark imagery.

### Separate terms for each dependency (independent of SoDam-CCC — check each on your own)

| Item | What to check |
|---|---|
| **Claude Code / Anthropic** | Terms of service · pricing plans · model usage policy |
| **Telegram / Telegram Bot API** | Bot terms of service · bot operating policy (the entity actually operating the bot is **the user themselves**) |
| **Official Telegram channel plugin** | Confirmed to be Apache License 2.0. SoDam-CCC does not bundle this plugin — users install it directly from the official marketplace |
| **Bun (runtime)** | Has its own separate runtime license. Installed directly by the user |

### About the code used in this project

A significant portion of this project's code and documentation was written together with AI (Claude Code). The official plugin's source code was **only read to understand how it works — it was never copied into this project** (any citation is by file path and line number only).

### If this is ever made public (not applicable right now)

If this project is ever turned into a public repository, or distributed/delivered/sold to someone else, the following **must** happen at that point — none of this applies yet, since this is currently private and personal-use only. This document states that fact in advance:
- Finalize a real license (e.g., Apache License 2.0) and add a `LICENSE` file
- Confirm whether a copyright notice (NOTICE) file is required
- Get **actual review from a legal professional** on AI-generated-work rights, the scope of permitted trademark references, and liability in case of delivery or sale

---

📌 This document is based on SoDam-CCC's actual design documents (the `.PRD` folder) and actual skill code (the `skills/` and `data/` folders). If actual behavior ever diverges from this document, the principle is to update the document alongside the code.
