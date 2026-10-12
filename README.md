# Deploy and Host Codewhale on Railway

[![Deploy on Railway](https://railway.com/button.svg)](https://railway.com/new/template/codewhale?referralCode=sLjDXb&utm_medium=integration&utm_source=button&utm_campaign=codewhale)

[Codewhale](https://github.com/Hmbown/Codewhale) (formerly `deepseek-tui`) is an open-source coding agent written in Rust. It reads your project, edits files, runs commands and checks its own work, using DeepSeek, OpenRouter, Anthropic, OpenAI, Kimi or any other provider you connect. This template runs it on an always-on Ubuntu 24.04 box that you reach from a terminal in your browser or over SSH. Your home directory lives on a volume, so repos, Codewhale config and sessions survive every redeploy.

## About Hosting Codewhale

One service, one volume at `/root`. The image contains:

- `codewhale`, `git`, `gh`, Node 22, Python 3 with `uv`, `tmux`, `ripgrep`, `jq`, `vim` and build tools
- a `ttyd` web terminal behind a basic-auth login
- an OpenSSH server (password or key login)

The web terminal and SSH both drop you into the same `tmux` session. You can start a long Codewhale run from your laptop and check on it from your phone. SSH host keys are stored on the volume, so your client never shows a "host key changed" warning after a redeploy.

Codewhale's own `codewhale web` client is loopback-only by design, and upstream says not to expose it through a public reverse proxy. The password-protected browser terminal is the safe way to use it remotely.

## Common Use Cases

- Leave Codewhale working on a task 24/7 without keeping your laptop open
- Run cheap long agent sessions on DeepSeek, or any OpenRouter model, from an iPad, Chromebook or phone
- `codewhale exec --auto "..."` for scripted, headless agent runs next to your repos
- A persistent root workspace with a stable IP for side projects, scraping and CI debugging

## Dependencies for Codewhale Hosting

- An LLM provider key: DeepSeek, OpenRouter, Anthropic, OpenAI, Kimi, Gemini, or any OpenAI-compatible endpoint

### Deployment Dependencies

- [Codewhale on GitHub](https://github.com/Hmbown/Codewhale)
- [Codewhale providers guide](https://github.com/Hmbown/Codewhale/blob/main/docs/PROVIDERS.md)
- [DeepSeek API keys](https://platform.deepseek.com/api_keys) or [OpenRouter keys](https://openrouter.ai/keys)
- [ttyd](https://github.com/tsl0922/ttyd) web terminal

### Implementation Details

**First use:**

1. Open your Railway domain and log in as `WEB_USER` with the generated `WEB_PASSWORD` from the service's Variables tab.
2. Run `codewhale`.
3. If you set `DEEPSEEK_API_KEY` or `OPENROUTER_API_KEY` at deploy time, Codewhale picks the key up from the environment. Otherwise run `/provider` (or press F3) to add a key, then `/model` to choose a model.

**Other providers:** add their usual variable in Railway, for example `ANTHROPIC_API_KEY`, `OPENAI_API_KEY`, `KIMI_API_KEY` or `GEMINI_API_KEY`, and redeploy. All `*_API_KEY`, `*_BASE_URL`, `DEEPSEEK_*` and `CODEWHALE_*` variables are also exported to SSH sessions.

**SSH:** Railway creates a TCP proxy for port 22 on deploy. Copy the host and port from Settings → Networking and run `ssh root@<host> -p <port>` with `ROOT_PASSWORD`. For key-only login, add your public key to `AUTHORIZED_KEYS` and clear `ROOT_PASSWORD`.

**Handy commands inside the box:**

- `cw` starts Codewhale inside a detachable `tmux` session named `codewhale`. Run `tmux attach -t codewhale` to resume it.
- `/mode plan` explores without changing anything. `Shift+Tab` switches between Ask, Auto-Review and Full Access.
- `gh auth login` (or setting `GH_TOKEN`) lets you push to GitHub.

**Upgrading:** each redeploy rebuilds the image with the latest Codewhale release. `codewhale update` upgrades in place until the next redeploy.

Notes and limits:

- You are root. Anything you install outside `/root` disappears on redeploy. Anything under `/root` stays.
- It is light: the box idles under 50 MB of RAM and Codewhale uses about 100 MB while working. Give the service more if the agent runs heavy builds or test suites.
- There is no Docker daemon inside the box, and Codewhale's OS sandbox and Computer Use tools do not apply in a headless container.
- The web terminal password is the whole security model for a root shell. Keep it strong and do not share the URL.

## Why Deploy Codewhale on Railway?

Railway is a singular platform to deploy your infrastructure stack. Railway will host your infrastructure so you don't have to deal with configuration, while allowing you to vertically and horizontally scale it.

By deploying Codewhale on Railway, you are one step closer to supporting a complete full-stack application with minimal burden. Host your servers, databases, AI agents, and more on Railway.
