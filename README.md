# Payesh Workspace

Local development and production orchestration for the Payesh platform microservices.

## Overview

This repo ties together the separate service repositories with PM2 process management. Clone all service repos into the same parent directory:

```
projects/
├── payesh-workspace/     ← this repo (ecosystem configs)
├── hoshmak-back/
├── hoshmak-front/
├── ai-services/
├── content-production/
├── deploy/
└── logs/                 ← runtime logs (gitignored)
```

## Clone all repos

```bash
mkdir -p ~/projects && cd ~/projects

git clone https://github.com/sedshahab0/payesh-workspace.git .
git clone https://github.com/sedshahab0/hoshmak-back.git
git clone https://github.com/sedshahab0/hoshmak-front.git
git clone https://github.com/sedshahab0/ai-services.git
git clone https://github.com/sedshahab0/content-production.git
git clone https://github.com/sedshahab0/auth-service.git authentication-and-authorization
git clone https://github.com/sedshahab0/captcha-service.git captcha
git clone https://github.com/sedshahab0/payesh-deploy.git deploy
```

## PM2 services

| PM2 name | Port | Description |
|----------|------|-------------|
| `hoshmak-back` | 4002 | NestJS main API |
| `hoshmak-front` | 4000 | Vite preview (React dashboard) |
| `ai-services` | 5000 | LLM gateway |
| `content-news` | 8000 | News crawl API |
| `content-translate` | 8001 | Translation API |
| `content-reflection` | 8002 | Reflection search API |
| `auth-service` | 4500 | Auth microservice |
| `captcha-service` | 8088 | Captcha service |

## Local development

```bash
# Build backend services first
cd hoshmak-back && npm install && npm run build && cd ..
cd ai-services && npm install && npm run build && cd ..
cd hoshmak-front && npm install && npm run build && cd ..

# Start all services
pm2 start ecosystem.config.cjs
pm2 logs
pm2 status
```

## Production deploy (GitHub)

All Payesh services live on GitHub under [sedshahab0](https://github.com/sedshahab0).

### First-time server setup (Germany VPS)

```bash
export GITHUB_TOKEN='ghp_...'   # read-only PAT — never commit this
sudo -E bash -c 'curl -fsSL https://raw.githubusercontent.com/sedshahab0/payesh-deploy/main/bootstrap-from-github.sh | bash'
```

Or after `deploy/` is already on the server:

```bash
export GITHUB_TOKEN='ghp_...'
sudo -E bash /opt/projects/deploy/bootstrap-from-github.sh
```

### Routine updates

```bash
export GITHUB_TOKEN='ghp_...'
export PAYESH_PURGE_IRAN_CACHE=1
sudo -E bash /opt/projects/deploy/update-from-github.sh
```

This pulls `main` from GitHub, rebuilds, reloads PM2, and optionally purges the Iran nginx cache.

## Production (Germany VPS) — PM2 only

```bash
# On server at /opt/projects
pm2 start ecosystem.server.cjs
pm2 save
```

Load email secrets from `deploy/hoshmak-resend.env` on the server (see payesh-deploy repo).

## Config files

| File | Purpose |
|------|---------|
| `ecosystem.config.cjs` | Local dev — paths relative to this directory |
| `ecosystem.server.cjs` | Production — paths under `/opt/projects` |

## Prerequisites

- Node.js 20 (via nvm)
- Python 3.11 with venv for content-production
- PostgreSQL, Redis
- PM2: `npm install -g pm2`

## Related repos

| Repo | Stack |
|------|-------|
| [hoshmak-back](https://github.com/sedshahab0/hoshmak-back) | NestJS API |
| [hoshmak-front](https://github.com/sedshahab0/hoshmak-front) | React dashboard |
| [ai-services](https://github.com/sedshahab0/ai-services) | LLM gateway |
| [content-production](https://github.com/sedshahab0/content-production) | Python FastAPI |
| [auth-service](https://github.com/sedshahab0/auth-service) | NestJS auth |
| [captcha-service](https://github.com/sedshahab0/captcha-service) | NestJS captcha |
| [payesh-deploy](https://github.com/sedshahab0/payesh-deploy) | Nginx, migrations, env templates |

## License

Private
