# Top-up prompt

The user types this into the sitting session, at the desktop app itself, for example each evening. It must be typed at the desktop for the same reason as the start prompt.

The short form is enough once the steps are in the role's standing instructions:

---

Top up the spares.

---

The long form, if the steps are not yet in the standing instructions. Replace `<N>`, `<Role>` and the paths.

---

Top up the spares.

For each role list (`<path to spares list>` for <Role>):

- Count the entries with state "waiting".
- Start enough new sessions to bring that count back to <N>, with your own model, effort and permission mode.
- Number and title them after the highest number in the list: "Spare <n> - <Role>".
- Give each the waiting brief at `<path to spare brief>`.
- Detach each one to top level, and file it in the "Spares" sidebar group.
- Append each to the list with its session id and state "waiting".

Then report how many you started for each role.
