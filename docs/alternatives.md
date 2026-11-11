# Alternatives

These are the other ways to hand a long-running session over to a fresh one, and why spares were chosen instead. Observations are from the Claude desktop app on macOS, 10 November 2026.

## A session clears itself

The session calls the app's clear-session tool on itself, then reloads its letter.

**Why not:** on a machine-started turn in auto permission mode, the auto-mode safety classifier denied it. The app's message noted that the user could add a permission rule for that tool. That route would mean a standing permission for a session to wipe its own context. It was not pursued or tested.

## A scheduled task starts the successor

A desktop scheduled task fires on a timer and runs the successor's work.

**Why not:** a session started by a scheduled task started with no click and could do core work: sub-agents, shell and git commands. But it could not start sessions, could not switch on Remote Control, and could not message another session with the usual tool. Nobody had opened it, so it could not be approved for anything. It also had no sidebar group. Making it a full seat would need more machinery, and it would still have no phone access.

See [Schedule recurring tasks in Claude Code Desktop](https://code.claude.com/docs/en/desktop-scheduled-tasks).

## The user types `/clear` from the phone

The user opens the session over Remote Control, types `/clear`, then sends one message pointing the session to its letter.

**Why not:** it works. It keeps the same session, title, model, permission mode and Remote Control link, with a clean context. But it needs a person at an unpredictable time, so it is not unattended. It is a good manual fallback.

See [Remote Control](https://code.claude.com/docs/en/remote-control).

## Compaction at a chosen threshold, plus a session-start hook

Let the session compact its context, and have a session-start hook load the hand-over letter after compaction.

**Why not:** compaction keeps a summary of the old conversation rather than giving a clean start. It also needs hooks configured. The `SessionStart` hook does fire after compaction and after `/clear`, and it can add context, so this is workable for people who accept a summary.

See the [hooks reference](https://code.claude.com/docs/en/hooks).

## Headless loops outside the desktop app

A script outside the app starts a fresh `claude -p` run for each task, reading the letter from a file.

**Why not:** it works unattended and gives each task a clean start. But it leaves the desktop app: no sidebar, no Code tab session to open, and a different way of working. For people who want to stay in the desktop app, spares keep everything there.

See [Run Claude Code programmatically](https://code.claude.com/docs/en/headless) and Anthropic's article [Effective harnesses for long-running agents](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents), which describes agents that carry work across context windows through progress files and git history.
