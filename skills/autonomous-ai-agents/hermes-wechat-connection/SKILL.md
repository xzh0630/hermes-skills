---
name: hermes-wechat-connection
description: Complete guide to connecting Hermes Agent to WeChat (Weixin) on Windows via the native weixin platform adapter. Covers QR login, config fixes, common pitfalls, and troubleshooting.
version: 1.0.0
author: Hermes Agent (learned from experience)
metadata:
  hermes:
    tags: [wechat, weixin, messaging, gateway, windows, qr-login, ilink]
---

# Hermes WeChat (Weixin) Connection on Windows

Hermes Agent has a **native** WeChat (Weixin) platform adapter at `gateway/platforms/weixin.py`. It uses Tencent's iLink Bot API at `https://ilinkai.weixin.qq.com` — no need for OpenClaw or third-party plugins.

## Architecture

WeChat App (phone) -> Tencent iLink API -> Hermes Gateway (weixin adapter) -> Hermes Agent

## Prerequisites

- `aiohttp` and `cryptography` Python packages (included in Hermes One-Click bundle)
- Hermes config.yaml with `platforms.weixin` section
- A WeChat account on your phone to scan the QR code

## Configuration

### config.yaml

```yaml
platforms:
  weixin:
    token: YOUR_ACCOUNT_ID@im.bot:YOUR_TOKEN_SECRET
    extra:
      account_id: YOUR_ACCOUNT_ID@im.bot
      base_url: https://ilinkai.weixin.qq.com   # MUST be the iLink API URL
      dm_policy: pairing
      group_policy: disabled
    enabled: true
```

**Critical:** The `base_url` must be `https://ilinkai.weixin.qq.com`. Do NOT set it to an `npx` install command (common migration mistake from OpenClaw).

### Token Format

`account_id@im.bot:token_secret` — the token comes from the QR login flow.

## QR Login Flow

### Method 1: Gateway setup wizard

```cmd
chcp 65001
set PYTHONIOENCODING=utf-8
hermes gateway setup
```
Select option **14. Weixin / WeChat** and follow the prompts.

### Method 2: Direct Python script

```python
import sys
sys.path.insert(0, r'path\to\hermes\python\Lib\site-packages')
import asyncio
from gateway.platforms.weixin import qr_login

result = asyncio.run(qr_login(r'C:\Users\Username\.hermes'))
print('Login result:', result)
```

A URL like `https://liteapp.weixin.qq.com/q/7GiQu1?qrcode=...&bot_type=3` will appear. Open this in WeChat on your phone to scan.

On success, the function saves credentials to `~/.hermes/weixin/accounts/{account_id}.json`.

## Starting the Gateway

### Step 1: Clean up stale PID files

```cmd
del /f %USERPROFILE%\.hermes\gateway.pid
```

Stale PID files cause `SystemError: <built-in function kill>` on Windows.

### Step 2: Check port conflicts

OpenClaw gateway often occupies port 18789. Stop it:
```cmd
taskkill /PID <openclaw_pid> /F
```

### Step 3: Start the gateway

```cmd
chcp 65001
set PYTHONIOENCODING=utf-8
hermes gateway run
```

Or directly (more reliable on Windows):
```cmd
chcp 65001
set PYTHONIOENCODING=utf-8
path\to\python.exe -m gateway.run
```

### Step 4: Verify connection

Check the gateway log for:
- `[Weixin] Connected account=XXXX base=https://ilinkai.weixin.qq.com` — connected OK
- `[Weixin] Session expired; pausing for 10 minutes` — token expired, need fresh QR login
- No errors — successfully polling for messages

## Windows-Specific Issues

### Chinese locale Unicode errors

Hermes CLI uses Unicode characters that crash on GBK code pages. Always set:
```cmd
chcp 65001
set PYTHONIOENCODING=utf-8
```

### Stale gateway.pid

The `get_running_pid()` function calls `os.kill(pid, 0)`. On Windows, if the PID references a non-existent process, this raises `SystemError`. Fix: delete `gateway.pid`.

### Direct python -m gateway.run is more reliable

On Windows, `hermes gateway run` may fail with the `os.kill` bug. Running `python.exe -m gateway.run` directly bypasses the CLI wrapper's PID check.

## Troubleshooting

| Symptom | Cause | Fix |
|---------|-------|------|
| Session expired (10 minute pause) | Token expired | Run fresh QR login through Hermes |
| RemoteProtocolError | Network/proxy drops | Use OpenRouter or check VPN stability |
| UnicodeEncodeError: gbk codec | Python using GBK encoding | Set PYTHONIOENCODING=utf-8 + chcp 65001 |
| OSError WinError 87 | Stale gateway.pid | Delete gateway.pid |
| Poll error with npx URL encoded | base_url set to shell command | Set to https://ilinkai.weixin.qq.com |
| OpenClaw responds instead of Hermes | QR login done via OpenClaw | Login via Hermes qr_login() function |
