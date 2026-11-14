# How it works

This page states the app rules the technique relies on, then the full lifecycle of a spare, with failure modes and fallbacks.

The rules below were observed in the Claude desktop app (Code tab) on macOS on 10 November 2026. App behaviour can change without notice. Check it on your own version with the dry run in [what-was-tested.md](what-was-tested.md).

## Terms

- **Role**: a standing job that one session holds at a time. Examples here: Coordinator, Maintainer.
- **Sitting session**: the session that holds the role now.
- **Spare**: a session started in advance that waits to take over the role.
- **Hand-over letter**: a file the sitting session keeps current, from which a fresh session can continue the work.
- **Spares list**: a plain file that lists a role's spares in order, with their session ids and states.
- **The chain**: each spare that takes a seat archives its predecessor and starts one new spare, so the spares list stays at its number.

## The app rules

### 1. Only the user's typed word at the desktop can start a session

A session may start another session only on a turn the user typed at the desktop app. The app refused in every other case seen:

- On a turn begun by a machine event or another session's message, it refused with: "Start sessions only on the user's own words to you here; this turn runs an automated event, another session's message, or the task another session gave you."
- On a request typed from a phone over Remote Control, it refused with: "The user is working over Remote Control, and this turn can't start that session, so nothing was started."
- In a session started by a scheduled task, it refused with: "needs a person's approval, and nobody has opened this scheduled run."

This is why a hand-over normally waits for a person. Spares move the session start to a time when the user is at the desk.

### 2. A waiting session can be woken by another session, and works fully on that turn

A session started on the user's typed word was left idle for six hours. Another session, on a machine-started turn, then sent it a message. On the turn that message began, the woken session:

- launched a sub-agent and got its answer;
- messaged another session;
- ran shell and git commands, including a dry-run push;
- still had Remote Control on.

This is the rule the technique rests on. A wake-up message needs no person, and the woken session can do real work.

### 3. Remote Control carries over to started sessions

With the app setting "Connect new sessions to Remote Control" on, a session started by another session on the user's word had Remote Control on from its first turn. So a spare is reachable from the user's phone once it seats.

### 4. Sessions can rename session-set titles without a prompt

On a machine-started turn, a session renamed another session, and then itself, with no approval prompt, where those titles had been set by a session. The app asks the user to approve a change to a title the user set by hand.

Consequence: once you adopt the naming rule below, do not rename these sessions by hand. A hand-set title will make the next unattended rename wait for approval.

### 5. Use the same permission mode everywhere

All tested sessions ran in the same permission mode (auto). Cross-session messages can be held for approval when the sender and the receiver are in different permission modes. A held message waits for a person, which defeats the purpose. Create spares in the same permission mode as the session that will wake them.

### 6. Detach each spare when you create it, on the user's turn

Detach each spare to top level when you create it. Then it is not archived together with its creator, and its creator is not notified of its turns. Detaching was not available to unattended sessions, so do it on the user's turn, in the same turn that creates the spares.

### 7. The chain's two extra actions are the app's rules too

The chain adds two actions to a seat's first turn: archiving the predecessor, and starting a replacement spare. Starting a session is covered by rule 1. The app permits it only on a turn the user typed at the desktop, so it may be refused on the turn a spare is woken by a message. Detaching the replacement is covered by rule 6 for the same reason. These two actions were not tested on a woken spare's turn. Check them with the dry run. Where the app refuses, the seat reports the refusal text and does not work around it. The list is then topped up by hand.

### Tool names

App tool names seen at the time. They may differ by version.

| Action | Tool name seen |
| --- | --- |
| Start a session | `start_session` |
| Message a session | `SendMessage` / `send_message` |
| Rename a session | `set_session_title` |
| Detach a session | `detach_session` |
| Archive a session | `archive_session` |

## Lifecycle

### Create the spares (user's typed turn, at the desk)

The user types the start prompt ([templates/start-spares-prompt.md](../templates/start-spares-prompt.md)) into the sitting session. On that turn, the sitting session:

1. Starts `<N>` sessions (one or two, see Sizing) with the same model, effort and permission mode as itself.
2. Titles them `Spare 1 - <Role>` to `Spare <N> - <Role>`.
3. Gives each the waiting brief ([templates/spare-brief.md](../templates/spare-brief.md)).
4. Detaches each to top level.
5. Optionally files them in a sidebar group named `Spares`.
6. Records their session ids, in order, in the role's spares list, each with state `waiting`.

Each spare takes one short turn to read its brief and then stops. It does nothing more until it is woken.

Failure modes:

- **The app refuses to start a session.** The turn was not a typed desktop turn, or the rule has changed. Report the refusal. Do not retry by another route.
- **Fewer spares started than asked.** Record the ones that did start. Tell the user the list is short.

### Wait

A waiting spare does nothing, but it holds a running process of about 200 MB. That is why the list is kept short. It also holds the project instructions it loaded at creation, which may be a day old by the time it is woken. That is why its brief tells it to re-read the instruction files from disk when woken.

### Hand over (sitting session, any kind of turn)

When the sitting session is near full, it follows [templates/handover-steps.md](../templates/handover-steps.md):

1. Finish the work in hand, or bring it to a clean stopping point.
2. Let running helpers finish, or record what they were doing.
3. Make the hand-over letter current and save it.
4. Message the first `waiting` spare in the list, naming the letter's path.
5. Mark that spare `used` in the list.
6. Stop when the spare confirms it has seated.

Failure modes and fallbacks:

- **The spare has not seated within ten minutes.** Mark it `failed`, wake the next `waiting` spare the same way.
- **No spare is left.** Fall back to proposing a new session to the user, and notify them. This is exactly the behaviour without spares. The user starts the successor when they next see the request.
- **The message is held for approval.** The permission modes differ. Treat it as a failed spare and wake the next. Tell the user so they can fix the mode at the next top-up.

### Seat (the woken spare)

The woken spare follows [templates/seating-steps.md](../templates/seating-steps.md):

1. Read the hand-over letter.
2. Re-read the project's instruction files from disk.
3. Take over the role's standing duties.
4. Update whatever file or record tells other sessions and scripts who holds the role.
5. Apply the naming rule.
6. Confirm to the predecessor that it has seated.
7. Archive the predecessor's session.
8. Start one new spare to replace itself, and add it to the spares list as `waiting`.
9. Move itself out of the `Spares` sidebar group, if one is used.
10. If the list now holds fewer than `<N>` `waiting` spares, optionally send the user one notification with the action "Top up the spares". Send it only then.

Failure modes:

- **The letter is missing or unreadable.** Tell the predecessor and wait for a corrected path. If the predecessor does not answer, notify the user.
- **The app refuses to archive the predecessor, or to start or detach the replacement spare.** Report the refusal text. Do not work around it. Continue the other steps. The list is one short, so the user tops it up at the desk.
- **The spare crashes or gets stuck.** The predecessor's ten-minute rule wakes the next spare. A session that is stuck after seating needs recovery by hand.

### Naming rule

- The sitting session carries the bare role name: `<Project> <Role>`.
- In its first turn, the new seat renames its predecessor into a numbered lineage, for example `<Project> <Role> v12`.
- Then it renames itself to the bare name.
- Its own number lives in the hand-over letter, not in its title. The next seat uses that number when it renames this one.
- Scripts that find a seat by its title must match the bare name.

### The chain

Each seat does two things on its first turn, so that no person is needed to refill the list:

1. It archives its predecessor. Spares that have been used are archived once their seat has passed, so retired seats do not pile up as open processes.
2. It starts one new spare, numbered after the highest number in the list, to replace the one it was.

The list stays at `<N>` `waiting` spares. If no spare is left when a seat fills, the manual route applies: a card or a message to the user.

### Why the chain

The first design kept a long list, for example eight spares, and asked the user to top it up each evening. Each idle spare is a running process of about 200 MB, so many idle spares plus retired seats left open used up the memory of a 16 GB machine. The chain keeps the list short and archives what has passed.

### Top up (user's typed turn, at the desk)

The user types the top-up prompt ([templates/top-up-prompt.md](../templates/top-up-prompt.md)) at the desk. With the chain this is needed only when the list has run dry or the seat's notification arrives. The sitting session brings each role's list back to its number of `waiting` spares, the same way it created them.

### Sizing

Keep the list short: two `waiting` spares for a role that hands over often, one for a role that hands over rarely. The chain refills it as seats pass, so the list does not need to cover a whole day of hand-overs.

## What this does not solve

- How often a session fills.
- A crashed or stuck session. Recover it by hand.
- The need for the user at the desk for the first start and for a top-up when the list has run dry.
- Any change Anthropic makes to these rules.

With no spare left, behaviour is exactly as it was before spares.

## Related documentation

- [Use Claude Code Desktop](https://code.claude.com/docs/en/desktop)
- [Message your other Claude Code sessions](https://code.claude.com/docs/en/cross-session-messaging): how messages are delivered, and when they are held depending on the two sessions' permission modes.
- [Permission modes](https://code.claude.com/docs/en/permission-modes): auto mode and its classifier.
- [Remote Control](https://code.claude.com/docs/en/remote-control): continuing a local session from a phone or browser.
