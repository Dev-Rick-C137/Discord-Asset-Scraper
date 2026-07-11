# 💾 Rickware Labs — Discord Asset Scraper V8

**A browser-based Discord asset & data scraper with a floating, tabbed UI.**

![Version](https://img.shields.io/badge/Version-8.0-blue?style=for-the-badge)
![Discord](https://img.shields.io/badge/Discord-7289DA?style=for-the-badge&logo=discord&logoColor=white)
![Browser](https://img.shields.io/badge/Browser-Chrome_Edge-F44336?style=for-the-badge)
![License](https://img.shields.io/badge/License-Personal_Use_Only-orange?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Active_Development-yellow?style=for-the-badge)

> Runs entirely client-side as a bookmarklet or console script. Scrapes Discord Shop collectibles and emojis, and pulls server/message data using your own logged-in session — no extension install required.

---

## 📸 Preview

| Shop Scraper | Emoji Scraper — Entire Server Emojis | Emoji Scraper — Single Emoji | Emoji Scraper — Multiple Emojis |
|---|---|---|---|
| ![Shop Scraper Preview](https://i.imgur.com/54wLALH.png) | ![Emoji Scraper Entire Server Emojis Preview](https://i.imgur.com/KMjMmeA.png) | ![Emoji Scraper Single Emoji Preview](https://i.imgur.com/AWuSdVX.png) | ![Emoji Scraper Multiple Emojis Preview](https://i.imgur.com/eq8vn0m.png) |

| User Scraper | Message Scraper | Server Scraper | Settings |
|---|---|---|---|
| ![User Scraper Preview](https://i.imgur.com/Rbgk2pE.png) | ![Message Scraper Preview](https://i.imgur.com/kl8vzXe.png) | ![Server Scraper Preview](https://i.imgur.com/DYqEZ3b.png) | ![Scraper Settings Preview](https://i.imgur.com/aKJkrw0.png) |

---

## 📑 Table of Contents

- [Features](#-features)
- [Output Examples](#-output-examples)
- [How to Use](#-how-to-use)
- [Authentication](#-authentication)
- [Responsible Use](#️-responsible-use)
- [Roadmap & Known Limitations](#️-roadmap--known-limitations)
- [Technical Details](#️-technical-details)
- [FAQ](#-faq)
- [License](#-license)

---

## ✨ Features

### 🛒 Shop Scraper (Main Tab)
- Real-time detection of **Nameplates**, **Avatar Decorations**, and **Profile Effects** while you browse `discord.com/shop`.
- Reads item names from on-page text and `aria-label`/`alt` attributes, and auto-categorizes each asset.
- Duplicate prevention — an item already queued or downloaded this session isn't added twice.
- Bulk downloading straight to a folder you choose, with 2 concurrent workers and a randomized delay between files.
- Live counters: assets loaded, names resolved, items already downloaded, plus per-category totals.

### 😀 Emoji Scraper
Full **static and animated** emoji support, in all three modes:
- **Single Emoji** — enter one Emoji ID, download it directly (auto-detects `.png` vs `.gif`).
- **Multiple Emojis** — paste one ID per line, get a single ZIP with every emoji.
- **Entire Server** — enter a Server ID; the tool lists every custom emoji on that server via the Discord API and zips them all in one go.
- Static emojis are saved as `.png`, animated ones as `.gif` — detected from the API's `animated` flag where available, with an automatic fallback check otherwise.

### 👤 User Scraper — 🚧 In Development
The tab and mode selector (**Single User / All from Channel / Entire Server**) already exist in the UI, but the extraction logic isn't wired up yet in this build — the "Extract User Data" button doesn't do anything at the moment. Once implemented, this tab is planned to resolve per user:

| Field | Description |
|---|---|
| Username | Account username |
| Display Name | Global display name |
| User ID | Numeric Discord ID |
| Avatar | Static or animated profile picture |
| Banner | Static or animated profile banner |
| Bio | "About Me" text |
| Pronouns | User-set pronouns |
| Status Text | Custom status message |
| Presence | Online / Idle / DND / Offline |
| Badges | Profile badges (HypeSquad, Staff, etc.) |
| Nitro Status | Whether the account has Nitro |

See [Roadmap](#️-roadmap--known-limitations) for where this stands, and [Responsible Use](#️-responsible-use) for a note on bulk ("Entire Server") mode once it ships.

### 💬 Message Scraper
- One target Channel ID → the most recent messages (up to 100 per request) are fetched and saved.
- DMs, group chats, and server text channels all use the same Discord message endpoint, so the same field works for any of them — just paste the relevant channel ID.
- The mode dropdown (**DM Chat / Server Chat / Group Chat / Entire Server**) is there for your own bookkeeping; all four currently read from the single channel ID you enter (see [Roadmap](#️-roadmap--known-limitations) for the planned multi-channel crawl).
- Exports as both a machine-readable **JSON** file and a human-readable **TXT** transcript, zipped together.

### 🏠 Server Scraper
- Enter a Guild ID to pull: server name, member count, role count + names, channel breakdown (categories / text / voice), and Community-feature status.
- Downloads the server's **icon** and **banner** (currently as static `.png`), a full JSON dump, and a `roles.txt`, all zipped into one archive.
- The owner's numeric ID is shown/exported, but their username/display name aren't resolved — keeping the export a bit more minimal by default.

### ⚙️ Settings
- Persistent save folders per asset category via the File System Access API.
- Adjustable min/max download delay (anti-rate-limit).
- UI opacity control, and a draggable, collapsible floating window.
- Debug mode for verbose console logging of every scrape/fetch/error.

---

## 📊 Output Examples

<details>
<summary><strong>Message Scraper Export</strong> (<code>channelID_messages.zip</code>)</summary>

**messages_123456789012345678.json**
```json
[
  {
    "id": "1345678901234567890",
    "timestamp": "2026-07-10T14:32:10.456Z",
    "author": {
      "id": "987654321098765432",
      "username": "ExampleUser",
      "global_name": "CoolExample",
      "discriminator": "0000"
    },
    "content": "This is a test message with an attachment!",
    "attachments": [
      {
        "id": "111222333444555666",
        "filename": "screenshot.png",
        "url": "https://cdn.discordapp.com/attachments/..."
      }
    ],
    "embeds": [],
    "mentions": []
  }
]
```

**messages_123456789012345678.txt**
```
[2026-07-10T14:32:10.456Z] ExampleUser: This is a test message with an attachment!
```
</details>

<details>
<summary><strong>Server Scraper Export</strong> (<code>guildID_guild_assets.zip</code>)</summary>

**server_info_123456789012345678.json**
```json
{
  "guild": {
    "id": "123456789012345678",
    "name": "My Awesome Server",
    "owner_id": "987654321098765432",
    "approximate_member_count": 2456,
    "icon": "abc123def456...",
    "banner": "xyz789abc123..."
  },
  "channels": [
    { "id": "111111111111111111", "name": "general", "type": 0, "parent_id": null }
  ],
  "roles": [
    { "id": "222222222222222222", "name": "Admin", "color": 16711680 }
  ]
}
```

**roles_123456789012345678.txt**
```
Admin (222222222222222222)
Moderator (333333333333333333)
Member (444444444444444444)
```

```
Guild Assets/
├── icon_123456789012345678.png
├── banner_123456789012345678.png
├── server_info_123456789012345678.json
└── roles_123456789012345678.txt
```
</details>

<details>
<summary><strong>Emoji Scraper ZIP Structure</strong></summary>

```
Emojis/
├── pepeLaugh_123456789012345678.gif      ← animated
├── thinkingFace_987654321098765432.png   ← static
├── partyParrot_555666777888999000.gif    ← animated
└── customEmoji_111222333444555666.png    ← static
```
</details>

---

## 🚀 How to Use

### Recommended: Bookmarklet
1. Create a new bookmark in your browser.
2. Paste the full script as the bookmark's URL.
3. Visit any `discord.com` page and click the bookmark.

### Alternative: Console
1. Go to `discord.com`.
2. Press `F12` → **Console**.
3. Paste the entire script and press **Enter**.

The floating UI appears bottom-right — drag it by the header, minimize it with **−**, or close it with **×**.

---

## 🔐 Authentication

The scraper reuses the token from your **already-logged-in** Discord session in the same browser tab, so you never paste a token in manually. If it can't find one, tabs that need the API (Emoji "Entire Server" lookup, Server Scraper, Message Scraper) will show an "Auth Error" instead of data.

---

## ⚠️ Responsible Use

- This runs as **your own account**, not a bot — Discord's automation rules for user accounts apply to how you use it, the same as for any script that calls the API on your behalf. The built-in delays exist to keep request rates reasonable.
- Server and Message Scraper output can include other people's names, roles, and message content. Only run those on servers you own or moderate, and don't redistribute what you export.
- The [License](#-license) below is personal-use-only, worth keeping in mind if this repo ever goes public.

---

## 🗺️ Roadmap & Known Limitations

- [ ] **User Scraper** — UI exists, extraction logic not yet implemented.
- [ ] **Animated server icon/banner** — Server Scraper currently saves both as static `.png` only; the Emoji Scraper already has working static/animated detection this could reuse.
- [ ] **True "Entire Server" message crawl** — Message Scraper currently reads one channel at a time (latest 100 messages); looping every channel with pagination is planned but not built yet.
- [ ] Bot Count and Admin Channel count on the Server Scraper tab are placeholders pending implementation.

---

## 🛠️ Technical Details

- Pure vanilla JavaScript — no external dependencies, no build step.
- Custom `LabsZip` class generates real ZIP archives (CRC32, local file headers, central directory, EOCD) entirely in-browser.
- DOM scraping via `TreeWalker` for on-page text (item names, totals).
- Downloads use exponential backoff + jitter, respecting Discord's `Retry-After` header on `429` responses.
- Files are written directly to disk via the File System Access API — this is why folder saving currently needs a Chromium-based browser (see [FAQ](#-faq)).

---

## ❓ FAQ

**The "Download all items" button is greyed out.**
It's only enabled while you're on a `discord.com/shop` page with at least one detected item.

**"Auth Error" shows up.**
The script couldn't find your session token. Make sure you're logged into Discord in the same tab/browser and try reloading the page before reopening the tool.

**A download failed with a rate-limit error.**
It retries automatically with backoff. If it keeps happening, raise the Min/Max Delay values in Settings.

**Why does "Users Resolved" always stay at 0?**
See [Roadmap](#️-roadmap--known-limitations) — the User Scraper tab isn't wired up to any extraction logic yet in this build.

**Does this work in Firefox or Safari?**
Not yet — folder-saving relies on the File System Access API, which is currently Chromium-only (Chrome, Edge, Brave, etc.).

---

## 📜 License

**Proprietary License – Personal Use Only**

- **Allowed**: Personal use on your own device.
- **Strictly Prohibited**: Redistribution, sharing, modification, reverse engineering, or commercial use.

**Rickware Labs © 2026**
All rights reserved.

---

**For personal use only.**

*Made with ❤️ by Rickware Labs*
