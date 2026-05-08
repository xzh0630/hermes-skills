---
name: hermes-wechat-windows
description: Connect Hermes Agent to WeChat/Weixin on Windows — QR login, gateway startup workaround, and Windows encoding fixes.
tags: [hermes, wechat, weixin, windows, gateway, qr-login]
---

# Hermes WeChat (Weixin) on Windows - Quick Reference

Hermes Agent has a native Weixin adapter at `gateway/platforms/weixin.py` using Tencent iLink Bot API (`https://ilinkai.weixin.qq.com`).

## QR Login (Fresh Token)

Create a script `wechat_login.py`:

```python
import sys
sys.path.insert(0, r'PATH_TO_HERMES\Lib\site-packages')
import asyncio
from gateway.platforms.weixin import qr_login
result = asyncio.run(qr_login(r'C:\Users\USERNAME\.hermes'))
print(result)
```

Run with UTF-8:
```cmd
chcp 65001
set PYTHONIOENCODING=utf-8
python wechat_login.py
```

Scan the QR link (`https://liteapp.weixin.qq.com/q/...`) with phone's WeChat. Credentials saved to `~/.hermes/weixin/accounts/`.

## Gateway Start (Windows Workaround)

`hermes gateway run` crashes on Windows with stale PID files. Use:

```cmd
del /f %USERPROFILE%\.hermes\gateway.pid
chcp 65001
set PYTHONIOENCODING=utf-8
python -m gateway.run
```

Check `~/.hermes/logs/gateway.log`:
- `[Weixin] Connected account=XXX base=https://ilinkai.weixin.qq.com` = success
- `Session expired` = token stale, re-run QR login

## Windows Gotchas

| Issue | Fix |
|-------|-----|
| GBK UnicodeEncodeError | `set PYTHONIOENCODING=utf-8` |
| Garbled terminal | `chcp 65001` |
| os.kill SystemError | Delete `gateway.pid`, use `python -m gateway.run` |
| MSYS2 path conversion | `cmd.exe //c` or `MSYS2_ARG_CONV_EXCL="*"` |
| Path with spaces | Use 8.3 short names (`Hermes~1`) |

## Base URL Pitfall

The config key `extra.base_url` must be `https://ilinkai.weixin.qq.com`. Setting it to a shell command (e.g. `npx ... install`) causes URL-encoded garbage like `npx%20-y%20.../ilink/bot/getupdates` in error logs.

## Language Enforcement

To force Chinese responses, add a `boot.instructions` section to config with the desired language direction.
