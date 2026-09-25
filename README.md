# Ilya Balobanov

I operate businesses and run AI agents inside them. This account holds the part of that work that can be verified by anyone: the tooling, the tests, and the failure reports.

## hermes-telegram-checklist

Native Telegram To-Do lists for AI agents - create, read, append and toggle real interactive checklist messages in chats and forum topics through a Telethon user session.

**Why it exists.** Telegram's Bot API sends checklists only on behalf of a connected business account, and its checklist methods take no forum-topic parameter, so a regular bot has no documented way to post one into a team group or a forum topic. MTProto under a user session can, which is what this tool does.

**What it is.** A single-file Python CLI - stdlib plus Telethon, strict JSON in and out, no daemon, no background listener, no message scraping. It ships as a skill for the Hermes agent by Nous Research, but any agent harness or plain shell can call it.

- Repository: https://github.com/bablobanov/hermes-telegram-checklist
- Latest release: https://github.com/bablobanov/hermes-telegram-checklist/releases/latest
- Install: `npx skills add bablobanov/hermes-telegram-checklist`
- Listed in [awesome-hermes-agent](https://github.com/0xNyk/awesome-hermes-agent)

## hermes-agent-telegram-dashboard

A read-only status dashboard for the Hermes agent, kept in one pinned Telegram message and edited in place on a schedule: gateway health, message freshness, the last database backup, config drift, and the Claude, Codex, Grok and Kimi account limits.

**Why it exists.** Hermes can report each of these through its own commands, one at a time, which adds up to a dozen small actions. I wanted one message that stays on screen in the chat I already work in and reads at a glance. A Mini App or a web page would put the effort into sign-in, hosting and layout, the parts that rot first, and an updater with a bot of its own would mean a second token to create and guard. A pinned message needs none of that.

**What it is.** A plugin that runs inside the Hermes gateway and edits the message through the gateway's own Telegram adapter, so the bot token never leaves the engine. Plain text, no LLM calls, nothing installed into the engine's environment; it needs Hermes 0.21.1 or later. The screen text is in Russian.

- Repository: https://github.com/bablobanov/hermes-agent-telegram-dashboard
- Release: https://github.com/bablobanov/hermes-agent-telegram-dashboard/releases/tag/v0.3.1
- Install: one command, then enable the plugin in `config.yaml`, restart the gateway and pin the message it sends:

  ```bash
  git clone https://github.com/bablobanov/hermes-agent-telegram-dashboard.git && cd hermes-agent-telegram-dashboard && install -d "${HERMES_HOME:-$HOME/.hermes}/plugins" && cp -r plugin/telegram_dashboard_probe "${HERMES_HOME:-$HOME/.hermes}/plugins/"
  ```

## How the work is checked

The interesting part of an agent tool is not that it runs, it is what happens when it does not. Both projects run their checks in GitHub Actions on every push.

**hermes-telegram-checklist**

- 144 offline tests that need neither network nor Telethon, on Python 3.9, 3.11 and 3.13
- one more leg on Python 3.12 with the real Telethon installed, where an integration test checks the exact MTProto request signatures against the library
- an allowlist on every write target
- offline validation of an agent-researched plan before a single write happens
- every write is read back from the server and reported under `verified`
- a documented security policy and a private channel for vulnerability reports

**hermes-agent-telegram-dashboard**

- 268 offline tests on Python 3.11, 3.12 and 3.13, plus `ruff` and `mypy --strict`; five more tests drive the plugin through the real engine objects and need the engine installed, so CI skips them
- a manifest check: `SHA256SUMS` must match every file of the plugin folder, and nothing unlisted may ship with it
- a value the dashboard cannot prove is shown as `unknown`, a source this installation does not have as `unsupported`; neither is ever drawn as zero or turns the status green, and a limit line without a number names its reason
- a freshness stamp on every line: the screen carries the time its data was read and the time Telegram last confirmed the edit, each provider limit line carries its own data time, and a message not confirmed for two periods gets a banner on its first line
- a watchdog outside the plugin checks the message's freshness on a schedule and alerts when the plugin stops editing. It runs in the same gateway, so it covers a dead plugin, not a dead gateway
- a private channel for vulnerability reports

The design rule I care about most is the same in both: the output says what actually happened, not what was intended. A pinned dashboard with a dead updater looks exactly like a calm system, and that is the failure it exists to prevent.

## Running agents in production

A personal agent has been running on my own server since 17 June 2026 - inbox and channel monitoring, scheduling, document preparation, ten scheduled jobs. One user: me. The dashboard above has been running on this same agent since 11 September 2026.

Along the way it surfaced three failures that had been silent for weeks: file delivery that dropped messages without an error, image sending that had never once succeeded across the entire log, and a cache cleanup quietly eating artefacts. None of them reported themselves. I write these down because a report that only lists wins is not a report.

## Attribution

My part is the requirements, the acceptance criteria, the security and quality decisions, the review, and running the result in production. The code of both projects is written with AI assistance. The two histories show it differently:

- hermes-telegram-checklist: five of its eight non-merge commits carry an AI co-author trailer (`Co-Authored-By: Claude`); the latest ones do not
- hermes-agent-telegram-dashboard: none of its commits carries one. I no longer add AI trailers to my repositories and say it here in words instead; a missing trailer is a choice about commit metadata, not a claim about who wrote the code. Its public history starts with one commit that holds all the work up to version 0.3.0, and the development history before it is not public

I do not rewrite published history to make a profile look better.
