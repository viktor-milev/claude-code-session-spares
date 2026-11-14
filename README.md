# Spares: unattended hand-over for long-running Claude Code sessions

A technique for people who run long-lived coordinating sessions in the Code tab of the Claude desktop app, and need those sessions to hand over to a fresh session while nobody is at the desk.

## The problem

A long-running coordinating session fills its context window in hours. The clean fix is a hand-over. The session writes a hand-over letter, and a fresh session continues from it.

The desktop app lets a session start another session only on a message the user typed at the desktop. So every hand-over waits for a person. If you are away, the work stops until you come back. At night that costs hours.

## The idea

Start the successors before you need them.

While you are at the desk, the sitting session starts a small number of successor sessions in advance, on one sentence you type. These are the spares. They wait and do nothing.

When the sitting session is full, it writes its letter and wakes the first unused spare with an ordinary session-to-session message. No session needs to be started at hand-over time, so no person is needed.

## The chain

Keep the list short: two waiting spares for a role that hands over often, one for a role that hands over rarely. Each spare that takes a seat keeps the list at that number itself:

1. It archives its predecessor's session.
2. It starts one new spare to replace the one it was.

So the list stays at its number with no top-up from you. Used spares are archived once their seat has passed. If no spare is left, behaviour falls back to the manual route: the session asks you to start a new one. Optionally, if the list ever falls below its number, the seat sends you one notification, "Top up the spares", and only then.

Your recurring step shrinks to the occasional sentence at the desk: "Top up the spares."

## Why the chain

Every waiting spare is a running process, about 200 MB of memory each. A long list of idle spares, plus retired seats left open, used up the memory of a 16 GB machine. The chain keeps only a few spares alive and archives each seat once it has passed.

```mermaid
sequenceDiagram
    actor User as User at the desk
    participant Sitting as Sitting session
    participant Spare as Spare 1
    User->>Sitting: "Start the spares" (typed at the desktop)
    Sitting->>Spare: start session with the waiting brief
    Note over Spare: waits, does nothing
    Note over Sitting: hours later, context is full
    Sitting->>Sitting: write the hand-over letter
    Sitting->>Spare: message: "You are my successor. Letter: <path>"
    Spare->>Spare: read letter, re-read instructions, take over duties
    Spare->>Sitting: rename predecessor to "<Project> <Role> v<N>"
    Spare->>Spare: rename itself to "<Project> <Role>"
    Spare->>Sitting: archive the predecessor
    Spare->>Spare: start one new spare, so the list stays full
    Spare->>Sitting: "Seated."
```

## What you get

- Hand-overs that run at any hour without a person.
- Each successor starts with a clean context window, not a summary.
- A waiting spare does nothing after its first short turn. It still holds a running process, so keep the list short.
- The list refills itself: each new seat archives its predecessor and starts one spare.
- If the spares run out, behaviour is exactly as before: the session asks you to start a new one.

## What it does not solve

- How often a session fills. That depends on your work.
- A crashed or stuck session. You recover that by hand.
- You still need to be at the desk for the first start and for a top-up after the list runs dry.
- The chain's replacement step starts a session. The app starts sessions only on a turn the user typed at the desktop. Whether it allows this on a woken spare's turn is not covered by the tests here, see [what was tested](docs/what-was-tested.md). Where the app refuses, the seat reports it and the list is topped up by hand.
- Any change Anthropic makes to these app rules. If the rules change, the technique may stop working.

## Install

AI coding agents: read [INSTALL.md](INSTALL.md) and follow it. People: point your coding agent at this repository.

More: [how it works](docs/how-it-works.md), [what was tested](docs/what-was-tested.md), [alternatives](docs/alternatives.md), [templates](templates/).

## Tested on

The Claude desktop app on macOS, 10 November 2026. Hand-overs to waiting spares also worked on Windows by user report. That was not independently checked.

## Note

Unofficial and not affiliated with Anthropic. It describes app behaviour observed on one date, which can change without notice. MIT licence, see [LICENSE](LICENSE).
