# claude-proxy

Small Node script that switches Claude Code between direct Anthropic access and a local [9router](https://www.npmjs.com/package/9router) proxy.

- `claude-proxy on` starts 9router in the background and writes the proxy env (base URL, auth token, model mapping) into `~/.claude/settings.json`.
- `claude-proxy off` stops 9router and removes those env keys from `~/.claude/settings.json`.
- `claude-proxy status` reports whether 9router is running and whether the proxy config is present.

## Prerequisites

- **OS:** macOS or Linux. The script shells out to `lsof`, `pgrep` and `ps`, which are standard on both. Windows is not supported.
- **Node.js:** on PATH. The script runs via `#!/usr/bin/env node`.
- **9router:** installed and on PATH, since `on` launches it with `9router --no-browser --tray`.
- **Claude Code:** installed, with a `~/.claude` directory. The script creates `~/.claude/settings.json` if it is missing, but not the directory.
- **Proxy auth token:** issued for your account and exported as `CLAUDE_PROXY_AUTH_TOKEN` (see step 2).
- **Install directory on PATH:** if you use the install command below, `~/.local/bin` must be on PATH.
- **Port 20128 free:** `on` expects 9router to listen there.

## Setup

1. Put the script on your PATH, for example:

   ```sh
   install -m 755 claude-proxy ~/.local/bin/claude-proxy
   ```

2. Export your proxy auth token in your shell profile:

   ```sh
   export CLAUDE_PROXY_AUTH_TOKEN="sk-..."
   ```

   `on` refuses to run if this is unset, so the token is never stored in the script or the repo.

## Usage

```sh
claude-proxy on
claude-proxy status
claude-proxy off
```

## Notes

- Port `20128` is hardcoded. `off` force-kills whatever listens on that port, not only 9router.
- Model mapping is in `PROXY_ENV` at the top of the script; edit it there.
