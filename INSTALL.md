# Install spares: instructions for an AI coding agent

You are an AI coding agent. Your user pointed you to this repository and wants spares for their project. Spares are successor sessions started in advance, so that a long-running session can hand over to a fresh one while nobody is at the desk.

Follow these steps in order. Ask the user where a step says so. Do not skip the safety section.

## Steps

1. **Read [docs/how-it-works.md](docs/how-it-works.md) in full.** It states the app rules this technique depends on and the lifecycle you will set up. Read [docs/what-was-tested.md](docs/what-was-tested.md) too, so you know what is proven and what is not.

2. **Ask the user two questions.**
   - Which long-running roles need spares? A role is a standing job one session holds at a time, such as "Coordinator" or "Maintainer".
   - About how many hand-overs a day does each role see? If they do not know, ask how many hours a session in that role lasts before it fills.

   Size each list from the answer: hand-overs per day plus a margin. A session that fills in three to four hours needs about eight spares for a day.

3. **Adapt the templates into the user's project.** Use the project's own conventions for where such files live. Look at how the project already stores instructions, checklists and state before you add anything. For each role, create:
   - a spares list file, from [templates/spares-list.txt](templates/spares-list.txt);
   - a hand-over letter location, from [templates/handover-letter.md](templates/handover-letter.md);
   - the waiting brief, from [templates/spare-brief.md](templates/spare-brief.md).

   Replace every placeholder (`<Project>`, `<Role>`, `<N>`, paths). Remove what does not fit the project.

4. **Add the hand-over and seating steps to each role's standing instructions.** Use [templates/handover-steps.md](templates/handover-steps.md) and [templates/seating-steps.md](templates/seating-steps.md). Put them where a session in that role reads its duties, for example the project's instruction file or the role's checklist. Add the naming rule. If any script finds a seat by its session title, make it match the bare name `<Project> <Role>`.

5. **Ask the user to type the start prompt at the desktop.** Give them the filled-in text from [templates/start-spares-prompt.md](templates/start-spares-prompt.md). Tell them to type it into the sitting session for that role, at the desktop app itself, not from a phone.

   Explain why: the app lets a session start another session only on a message the user typed at the desktop. A message from a phone, from a scheduled task or from another session cannot start one. You cannot do this step for them.

6. **Verify, after the sitting session has created the spares.** Check and report each item:
   - The list file holds `<N>` entries for the role, in order, each with a session id and the state `waiting`.
   - Each spare is titled `Spare <n> - <Role>`.
   - Each spare is detached to top level.
   - Each spare runs in the same permission mode as the sitting session.

   Report anything that does not match. Do not fix it by working around an app refusal.

7. **Offer the dry-run check** in [docs/what-was-tested.md](docs/what-was-tested.md). It proves, on the user's own machine and app version, that a waiting session can be woken by another session and can work on that woken turn. Run it only if the user agrees.

8. **Tell the user their recurring step.** Give them the filled-in text from [templates/top-up-prompt.md](templates/top-up-prompt.md). They type it at the desk, for example each evening.

## Safety

- **Do not work around the app's refusals.** If the app refuses to start a session, rename a title, or deliver a message, report the refusal text to the user. Do not try another route to the same effect.
- **Do not add or change permission rules or settings yourself.** If a step seems to need a new permission, tell the user what it is and why, and let them decide.
- **Start nothing the user did not ask for.** Create spares only on the user's own typed request, in the number they agreed.
- **Adapt the templates; do not paste them blindly.** They are starting points. Match the project's file layout, naming and tone. Remove steps that do not apply.
- App behaviour can change without notice. If what you observe differs from [docs/how-it-works.md](docs/how-it-works.md), trust what you observe and tell the user.
