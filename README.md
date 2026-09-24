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

## How the work is checked

The interesting part of an agent tool is not that it runs, it is what happens when it does not. This one carries:

- 143 offline tests that need neither network nor Telethon
- GitHub Actions CI on every push
- an allowlist on every write target
- offline validation of an agent-researched plan before a single write happens
- a documented security policy and a private channel for vulnerability reports

The design rule I care about most: every write is read back from the server and reported under `verified`. The output says what actually happened, not what was intended.

## Running agents in production

A personal agent has been running on my own server since 17 June 2026 - inbox and channel monitoring, scheduling, document preparation, ten scheduled jobs. One user: me.

Along the way it surfaced three failures that had been silent for weeks: file delivery that dropped messages without an error, image sending that had never once succeeded across the entire log, and a cache cleanup quietly eating artefacts. None of them reported themselves. I write these down because a report that only lists wins is not a report.

## Attribution

My part is the requirements, the acceptance criteria, the security and quality decisions, the review, and running the result in production. The code is written with AI assistance, and the git history says so out loud - four of the five commits carry an AI co-author. I do not rewrite that history to make a profile look better.
