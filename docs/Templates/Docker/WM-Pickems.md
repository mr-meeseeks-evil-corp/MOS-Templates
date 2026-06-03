---
title: ⚽ WM Pickems
layout: default
parent: 🐳 Docker
grand_parent: 🗂️ Templates
nav_order: 30
---

# ⚽ WM Pickems

<img src="https://raw.githubusercontent.com/J000K3R/MOS-Templates/main/icons/wm-pickems.png" width="80" />

**WM Tips** is a World Cup 2026 prediction game for you and your friends.
Predict the whole tournament up front, tip every individual match, and compete
on private leaderboards. Ships as a single Docker image — one Go binary serving
the API and the embedded SvelteKit app.

🏷️ **Category:** Productivity, Entertainment

🐳 **Image:** `ghcr.io/floholz/wm-pickems:latest`

---

## 🔗 Links

| | |
|---|---|
| 📦 **Project** | [github.com/floholz/wm-pickems](https://github.com/floholz/wm-pickems) |
| 🐛 **Support** | [GitHub Issues](https://github.com/floholz/wm-pickems/issues) |
| 🌐 **Demo** | [tips.fhun.site](https://tips.fhun.site) |

---

## 🌐 Ports

| Port | Protocol | Description |
|---|---|---|
| `8090` | TCP | Web interface |

---

## 💾 Volumes

| Host Path | Container Path | Mode | Description |
|---|---|---|---|
| `/mnt/cache/appdata/wm-pickems` | `/pb_data` | RW | PocketBase database, uploaded files & state |

---

## ⚙️ Environment Variables

| Name | Key | Default | Description |
|---|---|---|---|
| HTTP Port | `HTTP_PORT` | `8090` | Host port the server listens on |
| API Football Key | `API_FOOTBALL_KEY` | *(empty)* | API-Football key for live results (paid plan only). Leave empty for openfootball |
| Results Source | `RESULTS_SOURCE` | `auto` | Results source: `auto`, `apifootball`, or `openfootball` |
| Admin Email | `PB_ADMIN_EMAIL` | *(empty)* | PocketBase superuser email (created on first boot) |
| Admin Password | `PB_ADMIN_PASSWORD` | *(empty)* | PocketBase superuser password (created on first boot) |
| Google Client ID | `GOOGLE_CLIENT_ID` | *(empty)* | Google OAuth 2.0 Client ID. Leave empty to disable Google sign-in |
| Google Client Secret | `GOOGLE_CLIENT_SECRET` | *(empty)* | Google OAuth 2.0 Client Secret. Leave empty to disable Google sign-in |

---

## 🚀 Quick Start

1. Open the **MOS Hub** and search for **WM-Pickems**
2. Click **Install**
3. Access WM Tips at `http://your-server-ip:8090`
4. Create an admin account via the PocketBase admin UI at `/_/`
5. Create a **League** and share the invite code with your colleagues
6. Start predicting! 🎯

---

## 🏆 Features

| Feature | Description |
|---|---|
| **Match Tips** | Predict the score of every one of the 104 WC2026 matches. Editable until kickoff |
| **Tournament Forecast** | Full group standings (1st–4th), best-third qualifiers, and knockout bracket — locks at first kickoff |
| **Private Leagues** | Create leagues and invite colleagues via code or shareable `/join/<code>` link |
| **Global Leaderboard** | Every user is auto-joined to a shared Global league |
| **Scoring** | Up to 6 points per match: result (3) + exact score (1) + total goals (1) + goal difference (1) |
| **Live Results** | Auto-synced from API-Football or free openfootball JSON |
| **PWA** | Installable on mobile, offline shell, push notifications |
| **Google Sign-In** | Optional OAuth2 login via Google |

---

## 👤 Creating an Admin (Superuser)

After first boot, create a superuser to access the admin UI (`/_/`) and
management endpoints:

```sh
docker compose exec wm-pickems wm-pickems superuser create you@example.com 'a-strong-password' --dir=/pb_data
```

Or set `PB_ADMIN_EMAIL` / `PB_ADMIN_PASSWORD` in your environment before
starting — the superuser will be created automatically on first boot.

---

## ⚽ Results Sync

- **auto** (default): Uses API-Football if the key can reach WC2026
  (requires a **paid** plan — free tier has no WC2026 access),
  otherwise falls back to the free **openfootball** community JSON.
- **openfootball**: Always use community data (updated hours, not real-time).
- **apifootball**: Force API-Football (requires a paid key).

Manual override for admins: `POST /api/admin/matches/{id}/result`  
Force sync: `POST /api/sync/refresh` (superuser)

---

## 💾 Backup

The entire state is the `pb_data` volume. Snapshot while running:

```sh
docker run --rm -v wm-pickems_pb_data:/d -v "$PWD":/b alpine \
  tar czf /b/pb_data-backup.tgz -C /d .
```

---

## 🔒 Reverse Proxy / TLS

Terminate TLS at a reverse proxy (Caddy, Traefik, nginx) and forward to the
container port. Example Caddy:

```
pickems.example.com {
    reverse_proxy localhost:8090
}
```

---

## 🩺 Health Check

`GET /api/health` returns `200` when the app is up — use it for container
or proxy health checks.

---

> 💡 **Tip:** The app is fully playable without an API-Football key — it uses
> free openfootball data for results. You only need a paid key for real-time
> live scores.

> ⚠️ **Note:** The container runs as root (`--user 0:0`) to ensure PocketBase can write its database on MOS bind-mount volumes. The upstream Dockerfile uses UID 10001, but MOS volume permissions require root access for write operations.
> ⚠️ **Note:** The app is branded **WM Tips** in the UI. The repo/module name
> is **wm-pickems**.

> 📚 **For more information:** Visit the
> [wm-pickems documentation](https://github.com/floholz/wm-pickems) and
> [DEPLOY.md](https://github.com/floholz/wm-pickems/blob/main/DEPLOY.md)
> for advanced configuration, scoring customization, and operations guide.
