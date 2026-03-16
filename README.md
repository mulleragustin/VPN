# Self-Hosted WireGuard VPN (wg-easy)

Self-hosted WireGuard VPN with a web UI for managing clients, ready to deploy on [Coolify](https://coolify.io).

## Features

- **WireGuard** — Fast, modern, and lightweight VPN
- **wg-easy** — Web interface to create, delete, and manage clients via QR codes
- Real-time traffic stats and charts
- One-click deploy on Coolify

## Prerequisites

- A VPS with Docker (any provider works: Hetzner, DigitalOcean, etc.)
- [Coolify](https://coolify.io) installed on your VPS
- Ports `51820/udp` and `51821/tcp` open on your firewall

## Deploy on Coolify

### 1. Create the project

1. In Coolify, go to **Projects** → **New Project**
2. Select **Docker Compose** as the resource type
3. Connect this Git repository (or paste the `docker-compose.yml` directly)

### 2. Generate a password hash

Before deploying, generate a password hash for the web UI. Run this on your VPS:

```bash
docker run -it ghcr.io/wg-easy/wg-easy wgpw 'YOUR_SECURE_PASSWORD'
```

Copy the result. **Important:** replace every `$` with `$$` when pasting it into environment variables.

### 3. Set environment variables in Coolify

In the service configuration, set the following variables:

| Variable | Value | Required |
|---|---|---|
| `WG_HOST` | Your VPS public IP or domain | Yes |
| `PASSWORD_HASH` | The hash from step 2 (with `$$`) | Yes |
| `WG_PORT` | `51820` | No (default) |
| `UI_PORT` | `51821` | No (default) |
| `WG_DEFAULT_DNS` | `1.1.1.1, 8.8.8.8` | No (default) |
| `WG_ALLOWED_IPS` | `0.0.0.0/0, ::/0` | No (default) |

### 4. Open firewall ports

```bash
# WireGuard (UDP)
sudo ufw allow 51820/udp

# Web UI (TCP) — skip if using a reverse proxy
sudo ufw allow 51821/tcp
```

### 5. Deploy

Hit **Deploy** in Coolify and wait for the container to start.

## Client Setup

### Android / iOS

1. Install **WireGuard** from [Google Play](https://play.google.com/store/apps/details?id=com.wireguard.android) or [App Store](https://apps.apple.com/app/wireguard/id1441195209)
2. Open the web UI at `http://YOUR_IP:51821`
3. Log in and create a new client
4. Scan the QR code from the WireGuard app
5. Connect

### Windows / macOS / Linux

1. Download [WireGuard](https://www.wireguard.com/install/)
2. Create a client in the web UI and download the `.conf` file
3. Import the file into the WireGuard app
4. Connect

## Security Tips

- Use a strong password for the web UI
- Put the web UI behind a reverse proxy with HTTPS (Coolify can handle this automatically)
- After setting up your clients, you can close port `51821` externally and manage the UI only through the VPN

## Troubleshooting

| Problem | Solution |
|---|---|
| VPN doesn't connect | Check that port `51820/udp` is open on your VPS firewall **and** your hosting provider's panel |
| Web UI doesn't load | Check that port `51821/tcp` is open |
| Slow connection | Try changing DNS to `1.1.1.1` or `8.8.8.8` |

## License

MIT
