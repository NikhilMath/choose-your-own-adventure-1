# Keyboard Heist

## User intent and standing instructions

- Build and maintain a playable choose-your-own-adventure game in an HTML document with JavaScript and modern, clean CSS.
- The user is the story creator. Ask for their ideas, then ask a few focused questions to develop the setting, characters, stakes, and choices collaboratively. Do not independently invent or finalize major story directions.
- The chosen story is **Keyboard Heist**, a second-person dark office comedy. The protagonist is an IT Support Manager whose dull, miserable last few months have left life feeling pointless. A printer-driver ticket (their most hated IT task) brings them to a genuinely nice CEO. His ordinary $150 keyboard feels wonderful; buying one would not satisfy the protagonist, but stealing this one gives them purpose. They feel guilty and rationalize that the company wants happy employees. The fictional goal is to avoid being caught. Eventual absurd escalation is welcome; space was floated as a possibility, not an agreed plot point.
- Work one scene or decision at a time: develop the user's idea, propose branches for discussion, and implement the agreed direction. For the approved opening, choices lead to explicit “To be continued” checkpoints until their scenes are developed with the user. Do not invent continuations merely to fill those checkpoints. For subsequent scenes, agree on destinations with the user.
- Keep scene text short. Aim for the smallest number of paragraphs that still reads well; each paragraph is a beat, not a chunk. Prefer terse, rhythmic prose over exposition, and combine adjacent beats whenever the writing survives it.
- When a branch goes somewhere the story shouldn't: use a fail ending (`ending: true`, comic tone, e.g. caught or fired, not death) for choices that lose the story; merge back into an existing scene for choices that only look different (the merged scene must not assume anything only one incoming route established); use a loop for `walk_away`, where the protagonist can't shake the urge. Choice hints should signal when a path may end badly so endings feel fair.
- Whenever starting work on a new path/branch, open with the quickest possible summary of where that path stands and what is happening, then suggest 2-3 next steps for the user to choose from.
- Keep the game easy to open and share as a small set of static files (currently `index.html` + `styles.css`) with no build step, backend, or dependencies.
- After completing any change, always commit and push to `origin main` without asking for permission each time. Do not force-push or overwrite unrelated changes.
- This README is the durable place for standing instructions the user gives. Whenever the user states a preference, rule, or standing instruction, record it here so any AI assistant (not only Claude) picks it up in a future session. Update it when architecture, behavior, verification, or next steps change.
- The user runs VS Code Live Server for live preview. Do not spin up your own local HTTP server or open Claude's browser preview pane for visual checks — the user already sees changes update on their live server.

## Current implementation

The application is two static files with no runtime dependencies:

- `index.html`: markup, inline SVG keyboard illustration, and the story engine script.
- `styles.css`: responsive cream-and-green layout, serif narrative headings, accessible focus states, reduced-motion support. Linked from the HTML head; keep new styles here, not inline.
- Static HTML: cover panel and inline SVG keyboard illustration on the left, interactive story on the right; stacked on narrow screens.
- `STORY`: the opening, one developed second scene (`office`), one completed ending (`take_it_now`), and four explicit continuation checkpoints. The opening contains the plateau paragraph, Daniel’s 4:46 p.m. printer-driver ticket, and three choices: visit his office, suggest restarting, or check the model. The office scene walks the protagonist to Daniel’s desk (Ned-Flanders-nice CEO), leaves them alone with the keyboard while Daniel takes a call, plays the keyboard-touch moment, and forks into: take it now, come back later, or walk away. `take_it_now` is a single ending scene (“The spare”): the protagonist walks out with the keyboard, lies that it’s broken, Daniel cheerfully tells them how to fix it, fixes it, offers to give it to them, then drops and breaks it and says he’ll use the spare. The protagonist installs the printer driver and returns to their desk.
- Game engine: renders safe text using `textContent`, follows choices, supports going back, confirms restarting an active journey, shows visited scenes, and offers replay at endings.
- `path`: array of visited scene IDs, starting at `arrival`.
- `SAVE_KEY`: `keyboard-heist-v1`. Persists the path in localStorage; validates the saved route before restoring it. Falls back to in-memory play if storage is unavailable.

No framework, package manager, build pipeline, backend, generated story API, inventory, or conditional choices exists. Story expansion currently happens through source edits by an assistant. Do not imply the game generates new scenes at runtime.

## Run

Open `index.html` directly in a modern browser. No installation is required. File-URL storage behavior varies between browsers.

For a stable local origin, optionally run:

```sh
python3 -m http.server 8000 --bind 127.0.0.1
```

Then visit http://localhost:8000. No build step or API keys are needed.

## Story schema and editing contract

Scenes are properties of `const STORY` in the embedded script:

```js
unique_scene_id: {
  location: 'Short location label',
  title: 'Scene heading',
  text: ['First narrative paragraph.', 'Second narrative paragraph.'],
  choices: [
    { label: 'Action the player takes', hint: 'Non-spoiler context.', next: 'existing_scene_id' }
  ]
}
```

Optional `ticket: { from, subject, body }` renders a support-ticket card after `text`; `afterTicket` adds narrative paragraphs below it. For an explicitly unfinished branch, set `checkpoint: true` and omit `choices`; the engine displays “To be continued” and retains back/restart controls. A checkpoint is not a story ending. For an ending, set `ending: true` and omit `choices`. Replay is generated by the engine. The sample above illustrates the schema; its placeholder destination is not a real scene.

When expanding the story:

1. Read the existing scenes and all routes leading into the area being changed.
2. Write complete destination scenes first, then attach choices to them.
3. Give every scene a unique ID. Every `next` must name an existing scene.
4. Keep all scenes reachable from `arrival` and ensure every branch can reach an ending or explicit work-in-progress checkpoint. Avoid accidental cycles.
5. Give choices distinct consequences and preserve narrative continuity. Merged scenes cannot assume an item or knowledge missing from one incoming route.
6. Keep cover metadata accurate; do not advertise endings or playtime that do not exist.
7. Preserve existing save compatibility where practical. Bump `SAVE_KEY` if changed story semantics require a fresh playthrough; invalid saved routes are ignored already.
8. Update this handoff to reflect the resulting state.

Current graph:

```text
arrival -> office, restart_reply, printer_model
office  -> take_it_now, return_later, walk_away
endings: take_it_now
checkpoints: restart_reply, printer_model, return_later, walk_away
```

## Verification expectations

For changes to the engine or story graph, check:

- JavaScript parses; all choice targets exist; all scenes are reachable; every route terminates at an ending or explicit checkpoint.
- Every added branch works in the browser with no console errors.
- Backtracking, restart confirmation/cancellation, ending replay, and journey history work.
- Refresh restores a valid saved path; malformed/stale saves and unavailable storage do not break play.
- Desktop and narrow mobile layouts remain usable; choices work with keyboard navigation.
- `git diff --check` passes.

No automated test suite is checked into the repository yet. Report actual verification and any limitations honestly; do not claim browser checks solely from reading the source.

## Git and delivery

- Repository: https://github.com/NikhilMath/choose-your-own-adventure-1
- Default working/push branch: `main`.
- Inspect `git status` before editing or committing. Preserve unrelated user work.
- Stage only task files, use a descriptive commit, and push to `origin main` after verification.
- GitHub CLI (`gh`) is installed and authenticated on this machine, so `git push origin main` works over HTTPS without prompting. If auth breaks, re-run `gh auth login` and retry.
- A GitHub source push does not itself configure website hosting. There is no deployment configuration in this repository.
- License: MIT, see `LICENSE`.

## Next work

The office scene and the `take_it_now` ending are written. Next, develop one of the remaining office branches (`return_later`, `walk_away`) collaboratively. Open question on the `walk_away` branch: does it end the story or loop the protagonist back the next day, unable to shake it? Ask the user before writing that branch. The `restart_reply` and `printer_model` branches remain untouched checkpoints — develop when the user chooses. If introducing inventory, conditional choices, or multiple stories, explicitly design state/save compatibility and revise this handoff.
