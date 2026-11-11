# Hand-over steps (for the sitting session)

Add these steps to the role's standing instructions. They run on any kind of turn, including one no person typed.

Replace `<Role>`, `<Project>` and the paths.

---

## When to hand over

Hand over when your context window is near full, or when the user asks.

## Steps

1. **Finish the work in hand.** Bring it to a clean stopping point. Do not start anything new.
2. **Let helpers finish.** Wait for running sub-agents and background commands, or record in the letter what each was doing.
3. **Make the letter current and save it** at `<path to hand-over letter>`. Use the skeleton in `<path to letter template>`. Include your own version number.
4. **Wake the first spare.** Open `<path to spares list>`. Find the first entry with state `waiting`. Send that session this message:

   > You are my successor as <Role>. Your hand-over letter is at `<path to hand-over letter>`. Seat yourself by your brief and confirm to me when you are seated.

5. **Mark it used.** Set that entry's state to `used` in the list and save it.
6. **Wait for the confirmation.** When the spare confirms it has seated, stop. Take no further action in the role.

## If something goes wrong

- **No confirmation within ten minutes:** set that entry's state to `failed`, then repeat steps 4 to 6 with the next `waiting` entry.
- **The message is held for approval:** the permission modes differ. Treat it as failed and wake the next spare. Note it in the letter for the user.
- **No spare is left:** propose a new session to the user in the usual way, and notify them that the spares are used up. This is the same as working without spares.
- **The app refuses an action:** report the refusal text. Do not work around it.
