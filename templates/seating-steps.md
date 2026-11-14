# Seating steps (for the woken spare)

Add these steps to the role's standing instructions. A spare runs them on the turn its predecessor's message begins.

Replace `<Role>`, `<Project>` and the paths.

---

1. **Read the hand-over letter** at the path the message names. If it is missing or unreadable, tell the predecessor and wait for a corrected path. If no answer comes, notify the user.
2. **Re-read the project's instruction files from disk.** The copy you loaded when you were started may be a day old.
3. **Take over the role's standing duties,** as the instructions and the letter describe. Pick up the next actions the letter lists.
4. **Record that you hold the role.** Update `<path to the file or record that names the current holder>` with your session id, so other sessions and scripts reach you.
5. **Apply the naming rule.**
   - Rename your predecessor to `<Project> <Role> v<predecessor's number>`. Its number is in the letter.
   - Rename yourself to `<Project> <Role>`.
   - Keep your own number in the letter, not in your title. Your number is the predecessor's number plus one.
6. **Confirm to the predecessor:** "Seated as <Role>."
7. **Archive your predecessor.** Archive its session, now that you hold the role. If it is in the spares list, set its state to `archived`.
8. **Start one new spare to replace the one you were.** Start a session with your own model, effort and permission mode, titled `Spare <n> - <Role>`, where `<n>` is one more than the highest number in `<path to spares list>`. Give it the waiting brief at `<path to spare brief>`, detach it to top level, and add it to the list with state `waiting`. This keeps the list at `<N>` waiting spares with no top-up from the user. Start exactly one.
9. **Leave the spares group.** If the project uses a sidebar group named "Spares", move yourself out of it.
10. **Tell whoever the letter says to tell** that you now hold the role.
11. **Check the list.** If it holds fewer than `<N>` `waiting` spares, for example because step 8 was refused, optionally send the user one notification with the action "Top up the spares". Send it only then.

If the app refuses any of these actions, report the refusal text and continue with the rest. Do not work around it. Starting a session may be refused on a turn the user did not type. Then the list stays one short until the user tops it up.
