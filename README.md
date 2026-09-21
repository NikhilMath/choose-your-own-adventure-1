# Keyboard Heist

## User intent and standing instructions

- Build and maintain a playable choose-your-own-adventure game in an HTML document with JavaScript and modern, clean CSS.
- The user is the story creator. Ask for their ideas, then ask a few focused questions to develop the setting, characters, stakes, and choices collaboratively. Do not independently invent or finalize major story directions.
- The chosen story is **Keyboard Heist**, a second-person dark office comedy. The protagonist is an IT Support Manager whose dull, miserable last few months have left life feeling pointless. A printer-driver ticket (their most hated IT task) brings them to a genuinely nice CEO. His ordinary $150 keyboard feels wonderful; buying one would not satisfy the protagonist, but stealing this one gives them purpose. They feel guilty and rationalize that the company wants happy employees. The fictional goal is to avoid being caught. Eventual absurd escalation is welcome; space was floated as a possibility, not an agreed plot point.
- Work one scene or decision at a time: develop the user's idea, propose branches for discussion, and implement the agreed direction. The live game is the full spine as a single path of single-choice buttons, ending at “The plateau” (an earlier version with placeholder choices is recoverable at commit `e4a95d8`). Do not invent continuations merely to fill checkpoints. Add choices back only when their destination scenes are written and agreed with the user.
- Keep scene text short. Aim for the smallest number of paragraphs that still reads well; each paragraph is a beat, not a chunk. Prefer terse, rhythmic prose over exposition, and combine adjacent beats whenever the writing survives it.
- Build the true spine (main storyline) first, using single choices only: each spine scene ends with one choice leading to the next spine scene, no forks. Diversions come after the spine exists. The user approved the spine in `spine-draft.txt`, and it is built in `index.html` as a first draft; the prose was written by the assistant from the outline and is open to the user's edits. The user said not to work on other branches yet.
- Spine length: aim for about 20 scenes to reach the ending, more if the user likes where it goes.
- Story structure: the user wants every path to eventually converge on one main storyline; side branches return the protagonist to the main timeline rather than ending the story. This supersedes the fail-ending guidance below for the long term. Merged scenes must be route-neutral unless the engine gains state (see the merge rule in the editing contract).
- Restart (user's direction): the opening (`arrival`) is approved and kept. Everything after it was scrapped and is being rebuilt, spine first, then diversions. The earlier office scene and `take_it_now` ending remain retrievable in git history (commit `5e5552f`) for reference or reuse only if the user asks.
- Story ending (user's direction): the main storyline ends with the protagonist finally getting the keyboard and realizing they are still bored with life; a keyboard doesn't fix anything. All branches should converge before this ending. Details of the middle of the story are not yet decided.
- When a branch goes somewhere the story shouldn't (until convergence is designed): use a fail ending (`ending: true`, comic tone, e.g. caught or fired, not death) for choices that lose the story; merge back into an existing scene for choices that only look different (the merged scene must not assume anything only one incoming route established); use a loop when the protagonist can't shake the urge. Choice hints should signal when a path may end badly so endings feel fair.
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
- `STORY`: 35 scenes on a single path, one button per scene: `arrival` (the approved opening, with an “A ticket pops up.” beat and Daniel Hall’s printer ticket), then office, drive_home, model_number, impostor, plan, recon, window, outage, realization, casting, briefing, visit, cancels, audit, label_maker, auditor, tour, swap, scan, choice, shock, run, lie, question, hero, field, liftoff, ascent, descent, van, guilt, home, typing, and the single ending `ending` (“The plateau”). Act 6 (field through van) is the near-space balloon escalation. The decoy keyboard goes to space; the real one stays in the protagonist’s bag.
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

Optional `ticket: { from, subject, body }` renders a support-ticket card after `text`; `afterTicket` adds narrative paragraphs below it. For an explicitly unfinished branch, set `checkpoint: true` and omit `choices`; the engine displays “To be continued” and retains back/restart controls. An optional `prompt` string overrides the line shown under the scene (default: “To be continued” for checkpoints). A checkpoint is not a story ending. For an ending, set `ending: true` and omit `choices`. Replay is generated by the engine. The sample above illustrates the schema; its placeholder destination is not a real scene.

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
arrival -> office -> drive_home -> model_number -> impostor -> plan -> recon -> window -> outage
 -> realization -> casting -> briefing -> visit -> cancels -> audit -> label_maker -> auditor -> tour
 -> swap -> scan -> choice -> shock -> run -> lie -> question -> hero -> field -> liftoff -> ascent
 -> descent -> van -> guilt -> home -> typing -> ending
No forks and no checkpoints. Single ending.
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

The spine is built as a first draft. Next, the user reviews and edits the prose scene by scene. The user said not to work on other branches yet; diversions (the opening’s other choices, walk-away and take-it-now ideas at commit `5e5552f`) come later, and must rejoin the spine with route-neutral scenes. Open items from `spine-draft.txt`: whether the balloon is weird enough, whether weirdness should start earlier, the inhaler and profanity choices. Verification so far: script syntax checked with macOS JavaScriptCore (no `node` installed), and the real engine was run against a stub DOM through all 35 scenes, the save, and the back button; no browser check, no ending-replay check, no mobile check. Follow the standing rule to summarize and suggest 2-3 next steps whenever starting a new path. If introducing inventory, conditional choices, or multiple stories, explicitly design state/save compatibility and revise this handoff.
