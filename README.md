# claude-proxy

Small Node script that switches Claude Code between direct Anthropic access and a local [9router](https://www.npmjs.com/package/9router) proxy.

## Why

We use a Claude Pro subscription day to day, but it runs out of usage limit partway through long coding sessions. When Claude is out of quota, `claude-proxy on` routes Claude Code through 9router to models from any provider 9router supports, so work can continue on another subscription we already pay for. `claude-proxy off` switches back to direct Anthropic access.

- `claude-proxy on` starts 9router in the background and writes the proxy env (base URL, auth token, model mapping) into `~/.claude/settings.json`.
- `claude-proxy off` stops 9router and removes those env keys from `~/.claude/settings.json`.
- `claude-proxy status` reports whether 9router is running and whether the proxy config is present.

## Prerequisites

- **OS:** macOS or Linux. The script shells out to `lsof`, `pgrep` and `ps`, which are standard on both. Windows is not supported.
- **Node.js:** on PATH. The script runs via `#!/usr/bin/env node`.
- **9router:** installed and on PATH, since `on` launches it with `9router --no-browser --tray`.
- **Claude Code:** installed, with a `~/.claude` directory. The script creates `~/.claude/settings.json` if it is missing, but not the directory.
- **Proxy auth token:** issued for your account, either in the config file or exported as `CLAUDE_PROXY_AUTH_TOKEN` (see step 2).
- **Install directory on PATH:** if you use the install command below, `~/.local/bin` must be on PATH.
- **Port 20128 free:** `on` expects 9router to listen there.

## Setup

1. Put the script on your PATH, for example:

   ```sh
   install -m 755 claude-proxy ~/.local/bin/claude-proxy
   ```

2. Create your config from the example. The proxy env (base URL, auth token, model mapping) lives here, not in the script:

   ```sh
   mkdir -p ~/.config/claude-proxy
   cp config.example.json ~/.config/claude-proxy/config.json
   ```

   Fill in `ANTHROPIC_AUTH_TOKEN`. To keep the token out of the file, leave the placeholder and export it in your shell profile instead:

   ```sh
   export CLAUDE_PROXY_AUTH_TOKEN="sk-..."
   ```

   `CLAUDE_PROXY_AUTH_TOKEN`, when set, overrides the file. `on` refuses to run if neither provides a token.

   To use a config somewhere else, set `CLAUDE_PROXY_CONFIG=/path/to/config.json`.

## Usage

```sh
claude-proxy on
claude-proxy status
claude-proxy off
```

## Notes

- Port `20128` is hardcoded. `off` force-kills whatever listens on that port, not only 9router.
- Model mapping and base URL live in `config.json`; edit them there. Every key in the config is written into `settings.json` on `on` and removed on `off`.
- `off` reads the config to know which keys to remove. If the config is unreadable, it stops 9router but leaves `settings.json` unchanged.

## License

[MIT](LICENSE)
