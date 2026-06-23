---
name: agent-reach
description: >
  Use when the task needs Agent Reach platform backends or login/session access:
  XiaoHongShu/RedNote/XHS, Xueqiu, Xiaoyuzhou podcast transcription, Reddit,
  Twitter/X, Bilibili, V2EX, RSS, Exa/Jina fallback, LinkedIn MCP, OpenCLI,
  rdt-cli, twitter-cli, bili-cli, mcporter, or yt-dlp. Prefer official Codex
  tools for generic web search, public URLs, GitHub, Google Drive, and Gmail; a
  URL alone should not trigger this skill.
---

# Agent Reach — internet capability router

13 platforms, multiple backends each. **Prefer official/native Codex, Claude,
and Gemini tools first; use this skill only when those tools are insufficient,
the platform needs a login/session-backed or specialized backend, or the user
explicitly asks for Agent Reach.**

## Standing rules (apply for the whole session)

0. **Official tools first**: for ordinary public URLs, generic web search,
   GitHub repositories/issues/PRs, Google Drive, Gmail, and other officially
   connected services, use the current agent's official/native tools first. Do
   not trigger Agent Reach just because the user pasted a URL.
1. **Health-check only when needed**: when you need Agent Reach for a
   multi-backend platform (XiaoHongShu / RedNote / Reddit / Bilibili /
   Twitter), run `agent-reach doctor --json` first and pick the command group
   matching each platform's `active_backend`.
2. **Announce what you use**: say "using agent-reach, platform X via backend Y"
   before starting.
3. **On failure, follow the retry chains in references/** — never guess
   commands.
4. **For broad research tasks**: combine platforms (Exa for web search +
   Twitter/Reddit for discussions + XiaoHongShu/Bilibili for Chinese
   perspectives) only when official web search/browser coverage is not enough
   or when social/login-backed platforms are required.
5. **Watch versions without losing the fork**: after finishing a substantial
   multi-platform task, you may run `agent-reach check-update` (fast, one API
   call). If upstream has a new version, only tell the user that the
   `JingzhengQin/Agent-Reach` fork branch `qin/official-first-routing` should
   be rebased/merged with upstream and then reinstalled from that fork branch.
   Do not use the upstream update guide directly because it would overwrite
   this forked install. Never interrupt the current task to update; never nag
   about the same version twice.

## Routing table

| User intent | Category | Details |
|---------|------|---------|
| Web / code search fallback when official web/GitHub tools are insufficient | search | [references/search.md](references/search.md) |
| XiaoHongShu / Twitter / Bilibili / V2EX / Reddit | social | [references/social.md](references/social.md) |
| Jobs / LinkedIn when no official connector is available or MCP is needed | career | [references/career.md](references/career.md) |
| GitHub / code, preferring official GitHub connector or gh first | dev | [references/dev.md](references/dev.md) |
| Web pages / articles / RSS, preferring official web/browser first | web | [references/web.md](references/web.md) |
| YouTube / Bilibili / podcast transcripts | video | [references/video.md](references/video.md) |

## Zero-config quick commands

```bash
# Exa web search
mcporter call 'exa.web_search_exa(query: "query", numResults: 5)'

# Read any web page
curl -s "https://r.jina.ai/URL"

# GitHub search (prefer official GitHub connector; CLI fallback)
gh search repos "query" --sort stars --limit 10

# YouTube subtitles (NOTE: never use yt-dlp for Bilibili — see video.md)
yt-dlp --write-sub --skip-download -o "/tmp/%(id)s" "URL"

# V2EX hot topics
curl -s "https://www.v2ex.com/api/topics/hot.json" -H "User-Agent: agent-reach/1.0"

# Bilibili search (bili-cli, no login needed)
bili search "query" --type video -n 5
```

## Login-backed platforms (pick by doctor's active_backend)

```bash
# Twitter search (twitter-cli preferred; retry chain in social.md)
twitter search "query" -n 10

# Reddit (NO zero-config path — OpenCLI or rdt-cli, login required)
opencli reddit search "query" -f yaml   # desktop
rdt search "query" --limit 10            # legacy/server

# XiaoHongShu / RedNote (desktop prefers OpenCLI)
# If login lands on https://www.rednote.com/explore, use the RedNote adapter.
opencli rednote whoami -f yaml
opencli rednote search "query" -f yaml
opencli rednote note "NOTE_URL" -f yaml
opencli rednote download "NOTE_URL" --output /tmp/rednote-downloads -f yaml

# Use the old domain adapter only when the active session is still on
# www.xiaohongshu.com.
opencli xiaohongshu search "query" -f yaml
```

## Environment check

```bash
# Channel availability + which backend serves each platform
agent-reach doctor --json
```

## Workspace rules

**Never create files in the agent workspace.** Use `/tmp/` for temporary
output and `~/.agent-reach/` for persistent data.

## Detailed references

Read the matching file when you need specifics (commands above cover the
common cases; references hold per-backend command groups, caveats, retry
chains — note: reference docs are written in Chinese, commands are universal):

- [Search](references/search.md) — Exa AI search
- [Social](references/social.md) — XiaoHongShu, Twitter, Bilibili, V2EX, Reddit (multi-backend groups)
- [Career](references/career.md) — LinkedIn
- [Dev](references/dev.md) — GitHub CLI
- [Web](references/web.md) — Jina Reader, RSS
- [Video](references/video.md) — YouTube, Bilibili, Xiaoyuzhou

## Configure a channel

If a channel needs setup, fetch the fork branch install guide and use only the
per-channel configuration steps:
https://raw.githubusercontent.com/JingzhengQin/Agent-Reach/qin/official-first-routing/docs/install.md

Do not reinstall Agent Reach from upstream unless the user explicitly asks.
This machine intentionally uses the `JingzhengQin/Agent-Reach`
`qin/official-first-routing` fork branch so local routing fixes survive
updates.

The user only provides cookies / one extension click; the agent does the rest.
