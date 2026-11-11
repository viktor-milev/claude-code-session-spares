# What was tested

All tests ran in the Claude desktop app (Code tab) on macOS on 10 November 2026, unless the table says otherwise. App behaviour can change without notice.

## Results

| # | Test | Result |
| --- | --- | --- |
| 1 | A session starts another session on a turn the user typed at the desktop. | Worked. |
| 2 | A session starts another session on a turn begun by a machine event or another session's message. | Refused: "Start sessions only on the user's own words to you here; this turn runs an automated event, another session's message, or the task another session gave you." |
| 3 | A session starts another session on a request typed from a phone over Remote Control. | Refused: "The user is working over Remote Control, and this turn can't start that session, so nothing was started." |
| 4 | A session clears its own conversation with the app's clear-session tool, on a machine-started turn, in auto permission mode. | Denied by the auto-mode safety classifier. The app's message noted that the user could add a permission rule for that tool. Not pursued or tested further. |
| 5 | The user types `/clear` into a session from the phone over Remote Control, then one message pointing it to its letter. | Worked. Same session, title, model, permission mode and Remote Control link, with a clean context. Needs a person at an unpredictable time. |
| 6 | A session started by a desktop scheduled task. | Started with no click. Could launch a sub-agent and run shell and git commands. Could not start sessions. Could not switch on Remote Control ("needs a person's approval, and nobody has opened this scheduled run"). Its message to another session was refused ("SendMessage cannot reach another session from a session nobody opened in this app; use the send_message tool for that"). Had no sidebar group. |
| 7 | **Key test.** A session started on the user's typed word, left idle for six hours, then woken by a message another session sent on a machine-started turn. | Worked. On the woken turn it launched a sub-agent and got its answer, messaged another session, ran shell and git commands including a dry-run push, and still had Remote Control on. |
| 8 | With "Connect new sessions to Remote Control" on, a session started by another session on the user's word. | Remote Control was on from its first turn. |
| 9 | On a machine-started turn, a session renames another session, then itself, where the titles were set by a session. | Worked with no approval prompt. A title the user set by hand needs the user's approval to change. |
| 10 | Real use: coordinating sessions hand over to waiting spares, on macOS, the same evening. | Worked. |
| 11 | Real use on Windows. | The user reported success. Not independently checked. |

All sessions in these tests ran in the same permission mode (auto).

## What was not proven

- A spare that waited longer than six hours before it was woken.
- Starting more than eight spares in one turn.
- Windows. Success there is a user report only.
- Sessions in different permission modes. Messages between them can be held for approval, so this setup avoids the case rather than testing it.
- Any app version other than the one in use on 10 November 2026.

## Check it yourself: a dry run

This proves, on your own machine and app version, the rule the technique rests on: a waiting session can be woken by another session and can work on that turn. It changes nothing in your project.

1. **At the desk, type into any session:** "Start one throwaway session titled `Spare test`. Give it this brief: wait and do nothing until another session messages you. When it does, launch one helper sub-agent to answer 'what is 2 + 2', message the sender back with the answer and the output of `git status`, then stop. Detach it to top level. Use the same model and permission mode as yourself."
2. **Wait.** An hour or more makes the test meaningful. Overnight is better.
3. **Wake it from another session on a turn you did not type.** For example, ask a session to set a short timer or wait on a background command, and to message `Spare test` when that finishes: "You are woken for the test. Run your brief."
4. **Check the results.** The sender should receive a message with the answer `4` and a `git status` summary. In `Spare test`, confirm that the sub-agent ran and that Remote Control is still on, if you use it.
5. **Clean up.** Archive `Spare test`.

If step 3 or 4 fails, the technique will not work on your setup as described. Note the exact refusal text and do not work around it.
