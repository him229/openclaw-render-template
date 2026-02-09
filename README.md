# OpenClaw Render Template (1-click deploy)

> **Forked from [clawdbot-railway-template](https://github.com/vignesh07/clawdbot-railway-template)** by [@vignesh07](https://github.com/vignesh07)
>
> This is an adaptation for [Render.com](https://render.com) deployment.

This repo packages **OpenClaw** for Render with a web-based **/setup** wizard so users can deploy and onboard **without running any commands**.

## What you get

- **OpenClaw Gateway + Control UI** (served at `/` and `/openclaw`)
- A friendly **Setup Wizard** at `/setup` (protected by a password)
- Persistent state via **Render Disk** (config/credentials/memory survive redeploys)
- One-click **Export backup** and **Import backup** from `/setup`

## Deploy to Render

[![Deploy to Render](https://render.com/images/deploy-to-render-button.svg)](https://render.com/deploy?repo=https://github.com/YOUR_USERNAME/openclaw-render-template)

> **Note:** Replace `YOUR_USERNAME` with your GitHub username after forking.

### What happens when you click Deploy:

1. Render creates a new service from the `render.yaml` Blueprint
2. You'll be prompted to set `SETUP_PASSWORD`
3. Docker image builds and deploys
4. Visit `https://<your-service>.onrender.com/setup` to complete onboarding

## Environment Variables

| Variable | Required | Description |
|----------|----------|-------------|
| `SETUP_PASSWORD` | Yes | Password to access `/setup` wizard |
| `NODE_OPTIONS` | Recommended | Memory limit: `--max-old-space-size=400` (Starter) or `1800` (Standard) |
| `OPENCLAW_STATE_DIR` | Auto-set | `/data/.openclaw` |
| `OPENCLAW_WORKSPACE_DIR` | Auto-set | `/data/workspace` |
| `OPENCLAW_GATEWAY_TOKEN` | Auto-generated | Secure token for gateway auth |

### Choosing a Render Plan

| Plan | RAM | NODE_OPTIONS Value |
|------|-----|-------------------|
| Starter ($7/mo) | 512MB | `--max-old-space-size=400` |
| Standard ($25/mo) | 2GB | `--max-old-space-size=1800` |

The Blueprint defaults to Starter. To use Standard, change `plan: standard` in `render.yaml` and update `NODE_OPTIONS`.

## How it works

1. The container runs a wrapper web server on port 8080
2. The wrapper protects `/setup` with `SETUP_PASSWORD`
3. During setup, the wrapper runs `openclaw onboard --non-interactive ...`
4. After setup, all traffic proxies to the local OpenClaw gateway

## Getting chat tokens

### Telegram bot token
1. Open Telegram and message **@BotFather**
2. Run `/newbot` and follow the prompts
3. Copy the token (looks like: `123456789:AA...`)

### Discord bot token
1. Go to [Discord Developer Portal](https://discord.com/developers/applications)
2. **New Application** → pick a name
3. Open **Bot** tab → **Add Bot** → copy the **Bot Token**
4. Enable **MESSAGE CONTENT INTENT** in Bot → Privileged Gateway Intents

## Local testing

```bash
docker build -t openclaw-render-test .

docker run --rm -p 8080:8080 \
  -e PORT=8080 \
  -e SETUP_PASSWORD=test \
  -e OPENCLAW_STATE_DIR=/data/.openclaw \
  -e OPENCLAW_WORKSPACE_DIR=/data/workspace \
  -v $(pwd)/.tmpdata:/data \
  openclaw-render-test

# open http://localhost:8080/setup (password: test)
```

## License

MIT - See [LICENSE](LICENSE)

---

**Original template by [@vignesh07](https://github.com/vignesh07)** • Render adaptation maintained by [YOUR_NAME]
