# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

TicketDesk is a PWA (Progressive Web App) for ticket resale management. The entire application lives in a single `index.html` file — no build step, no package manager, no framework. Open the file in a browser to run it.

## Running locally

Serve with any static file server (required for the service worker and OAuth redirect URI):

```
npx serve .
# or
python -m http.server 8080
```

Then open `http://localhost:3000` (or whichever port). Opening `index.html` directly as a `file://` URL breaks OAuth.

## Architecture

Everything is in `index.html`:

- **CSS** (`<style>` block, lines 17–190): CSS custom properties define the dark theme. No utility framework.
- **HTML** (lines 193–404): Static shell with six tab pages (`page-dashboard`, `page-journal`, `page-wts`, `page-gmail`, `page-costs`, `page-settings`) and two bottom-sheet modals. Pages are shown/hidden by toggling `.active` class.
- **JavaScript** (`<script>` block, lines 405–1246): All logic in a single inline script. No modules.
- **Login screen** (lines 1247–1264): Rendered after the script block; `checkAuth()` hides or shows it on load.

**State** is held in module-level `let` variables (`transactions`, `costs`, `gmailToken`, etc.) and persisted to `localStorage`.

| localStorage key | Contents |
|---|---|
| `ticketdesk_tx` | `transactions` array |
| `ticketdesk_costs` | `costs` array |
| `ticketdesk_gmail` | Google OAuth access token |
| `ticketdesk_drive_fid` | Drive file ID for the sync file |
| `ticketdesk_creds` | `{ user, pin }` login credentials |
| `ticketdesk_gmail_email` | Connected Google email address |
| `ticketdesk_last_sync` | ISO timestamp of last Drive sync |

`sessionStorage.getItem('td_unlocked')` gates the login screen within a tab session.

## Google OAuth

Uses the **implicit grant flow** (`response_type=token`). After authorization, Google redirects back to the same URL with `#access_token=…` in the hash. The token is read on page load (~line 1209) and stored in `localStorage`.

The `GOOGLE_CLIENT_ID` constant (line ~408) must be a real Google Cloud OAuth client ID. The redirect URI registered in Google Cloud must match `window.location.origin + window.location.pathname`.

Required scopes: `gmail.readonly` and `drive.appdata`.

## Drive sync

Data is saved as `ticketdesk-data.json` in the `appDataFolder` space (hidden from the user's Drive). Syncs are debounced 1.5 s after any write. `driveLoad()` returns `true` if remote data was found and merged.

## Gmail scanning

`gmailScan()` queries three search terms against the Gmail API, then `parseTicketMail()` extracts event name, date, price, quantity, category, and placement from Ticketmaster, Fnac Spectacles, and Stade de France confirmation emails using regex. A ticket is discarded if it has no date or a zero price.

## External dependencies (CDN, no install needed)

- **Chart.js 4.4.1** — bar and doughnut charts on the Dashboard
- **Google Fonts** — Bebas Neue (logo/headings), DM Sans (body)
