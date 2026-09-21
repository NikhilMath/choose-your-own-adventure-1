# Keyboard Heist

## User intent and standing instructions

- Build and maintain a playable choose-your-own-adventure game in an HTML document with JavaScript and modern, clean CSS.
- The user is the story creator. Ask for their ideas, then ask a few focused questions to develop the setting, characters, stakes, and choices collaboratively. Do not independently invent or finalize major story directions.
- The chosen story is **Keyboard Heist**, a second-person office comedy (the user asked that it be billed as “an office comedy,” not “dark”). The protagonist is an IT Support Manager whose dull, miserable last few months have left life feeling pointless. A printer-driver ticket (their most hated IT task) brings them to a genuinely nice CEO. His ordinary $150 keyboard feels wonderful; buying one would not satisfy the protagonist, but stealing this one gives them purpose. They feel guilty and rationalize that the company wants happy employees. The fictional goal is to avoid being caught. The user reviewed a near-space weather-balloon escalation and cut it as too wacky; do not reintroduce space or balloons unless the user asks.
- Work one scene or decision at a time: develop the user's idea, propose branches for discussion, and implement the agreed direction. The live game is the full spine as a single path of single-choice buttons, ending at “The plateau” (an earlier version with placeholder choices is recoverable at commit `e4a95d8`). Do not invent continuations merely to fill checkpoints. Add choices back only when their destination scenes are written and agreed with the user.
- Keep scene text short. Aim for the smallest number of paragraphs that still reads well; each paragraph is a beat, not a chunk. Prefer terse, rhythmic prose over exposition, and combine adjacent beats whenever the writing survives it.
- Endings (user's direction): the button at an ending goes back to the last choice, not to the beginning.
- Link previews: `index.html` has Open Graph and Twitter card tags pointing at `og-image.jpg` (2400x1260 JPEG at quality 95, made from an SVG rendered at 2x because a 1200x630 PNG looked blurry in LinkedIn’s Post Inspector; the user wants it to say only “Keyboard Heist” in the site’s serif style, cream “Keyboard” over lime italic “Heist.” on the dark green background, with no other text or art). LinkedIn caches previews; refresh with https://www.linkedin.com/post-inspector/.
- Wrong choices (user's direction): early wrong choices grow into mini-adventures, and the user asked that the early dead ends be brought back into the main storyline. Five early branches now rejoin (see the graph); the funniest stay as real endings. Because there is no memory of route, every scene with more than one way in (`office`, `plan`, `outage`) must read correctly for all incoming routes; keep them route-neutral.
- Build order (user's direction): the main story (trunk) first, then diversions. The trunk was first drafted as a single path (`spine-draft.txt`, 29 scenes, recoverable in the game at commit `7acb237`); the user then asked for about 20 scenes with about 10 endings, and the game now implements `story-map.txt`. The user said not to worry about other branches beyond what the map shows.
- Perspective: always second person (“you”). The adventure is about the player, not a fictional character: never use first person (“I”) or refer to the protagonist in the third person outside quoted dialogue. Choice labels and hints must also read as addressed to “you.”
- Structure (user's sketch): started as 20 scenes with 10 endings. The game is now 32 scenes with 11 endings: a 10-scene trunk, three early mini-adventures (restarting, walking away, just asking), and five early dead ends that rejoin the trunk. Built from `story-map.txt`; the prose is an assistant first draft open to the user's edits.
- Spine length: aim for about 20 scenes to reach the ending, more if the user likes where it goes.
- Story structure: the user wants every path to eventually converge on one main storyline; side branches return the protagonist to the main timeline rather than ending the story. This supersedes the fail-ending guidance below for the long term. Merged scenes must be route-neutral unless the engine gains state (see the merge rule in the editing contract).
- Restart (user's direction): the opening (`arrival`) is approved and kept. Everything after it was scrapped and is being rebuilt, spine first, then diversions. The earlier office scene and `take_it_now` ending remain retrievable in git history (commit `5e5552f`) for reference or reuse only if the user asks.
- Story ending (user's direction): the main storyline ends with the protagonist finally getting the keyboard and realizing they are still bored with life; a keyboard doesn't fix anything. All branches should converge before this ending. Details of the middle of the story are not yet decided.
- When a branch goes somewhere the story shouldn't (until convergence is designed): use a fail ending (`ending: true`, comic tone, e.g. caught or fired, not death) for choices that lose the story; merge back into an existing scene for choices that only look different (the merged scene must not assume anything only one incoming route established); use a loop when the protagonist can't shake the urge. Choice hints should signal when a path may end badly so endings feel fair.
- Whenever starting work on a new path/branch, open with the quickest possible summary of where that path stands and what is happening, then suggest 2-3 next steps for the user to choose from.
- Keep the game easy to open and share as a small set of static files (`index.html`, `styles.css`, the icon files `favicon.svg`, `favicon.png`, `apple-touch-icon.png`, and the link-preview image `og-image.jpg`) with no build step, backend, or dependencies.
- After completing any change, always commit and push to `origin main` without asking for permission each time. Do not force-push or overwrite unrelated changes.
- This README is the durable place for standing instructions the user gives. Whenever the user states a preference, rule, or standing instruction, record it here so any AI assistant (not only Claude) picks it up in a future session. Update it when architecture, behavior, verification, or next steps change.
- The user runs VS Code Live Server for live preview. Do not spin up your own local HTTP server or open Claude's browser preview pane for visual checks. The user already sees changes update on their live server.
- Never use em dashes anywhere: not in story text, UI copy, code comments, or documentation. Use commas, periods, colons, or parentheses instead. Check before every commit.

## Current implementation

The application is two static files with no runtime dependencies:

- `index.html`: markup, inline SVG keyboard illustration, and the story engine script.
- `styles.css`: responsive cream-and-green layout, serif narrative headings, accessible focus states, reduced-motion support. Linked from the HTML head; keep new styles here, not inline.
- Static HTML: no site header, footer, or cover eyebrow (the user removed the “OTHER PATHS / STORIES YOU STEP INTO” bar, the “Employee happiness is a company priority. / YOUR CHOICES. YOUR STORY.” footer, and the “Adventure No. 001 · Office comedy” line). Keep the `.eyebrow` CSS rule: the engine still uses that class for each scene’s location tag. The cover is now just the title, the scenes/endings line, and the illustration; the “Steal a keyboard just to feel something.” tagline was removed from the page but is deliberately kept in the `description`, `og:description` and `twitter:description` meta tags so shared links still have copy. the page opens straight into the cover panel and inline SVG keyboard illustration on the left, interactive story on the right; stacked on narrow screens.
- `STORY`: 32 scenes. Trunk: `arrival`, `office`, `plan`, `outage`, `grandmother`, `visit`, `audit`, `swap`, `choice`, `lie`. Mini-adventures: restart (`restart`, `ceremony`, `lemon`; Employee of the Month), walk away (`walk_away`, `resigned`, `draft`, `typo`; the resignation email), just ask (`ask`, `garage`, `declined`; Daniel’s founding-keyboard story), plus `printer_model`. Rejoin scenes (`printer_model`, `lemon`) lead back to `office`; `draft` and `typo` lead to `plan`; `declined` leads to `outage`. Endings (11): `end_plateau` (the canonical ending), `end_spare`, `end_mom`, `end_method`, `end_red`, `end_running`, `end_sick`, `end_speech`, `end_sabbatical`, `end_founding`, `end_borrowed`. The protagonist leaves with the real keyboard in their bag; the decoy stays on Daniel’s desk. Scenes have one to three buttons.
- Game engine: renders safe text using `textContent`, follows choices, scrolls the new scene’s top into view after a choice, back, journey click, or restart when that top is above the viewport (needed on phones, where the page would otherwise stay scrolled to the bottom), supports going back, confirms restarting an active journey, shows visited scenes in “Your journey” (a `<details open>` list that starts open, per the user; every earlier step is a button that jumps back to that scene and trims the route; the current step is plain text), and at an ending offers “Try a different choice,” which goes back to the last choice you made (not the beginning; “Start over” in the toolbar still resets to the opening).
- `path`: array of visited scene IDs, starting at `arrival`.
- `SAVE_KEY`: `keyboard-heist-v2`. Persists the path in localStorage; validates the saved route before restoring it. Falls back to in-memory play if storage is unavailable.

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

Optional `ticket: { from, subject, body }` renders a support-ticket card after `text`; `afterTicket` adds narrative paragraphs below it. For an explicitly unfinished branch, set `checkpoint: true` and omit `choices`; the engine displays “To be continued” and retains back/restart controls. An optional `prompt` string overrides the line shown under the scene (default: “To be continued” for checkpoints). The canonical ending `end_plateau` uses it to say “The end.” and also sets `finale: true`, which is what distinguishes it from the joke endings. `finale` does three things: the prompt line gets the `.decision.finale` style (large Georgia, 46px, not uppercase), the scene shows no choice button at all so the story reads as truly over (players still escape via the small “↻ Start over” text button in the toolbar and “← Previous choice” below; without one of those a saved finale would trap them on reload), and the joke endings keep the small grey “Every ending is another beginning” with a button that steps back one choice. The anticlimax is deliberate: the ending must make clear the letdown is the point, so it echoes the opening’s plateau/Outlook line and has the protagonist wait for a payoff that never arrives. Do not “fix” it by adding a triumphant beat. A checkpoint is not a story ending. For an ending, set `ending: true` and omit `choices`. Replay is generated by the engine. The sample above illustrates the schema; its placeholder destination is not a real scene.

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
arrival -> office, restart, printer_model
office  -> end_spare, plan, walk_away
plan    -> outage, ask
outage  -> grandmother
grandmother -> visit, end_mom
visit   -> audit, end_method
audit   -> swap, end_red
swap    -> choice
choice  -> lie, end_running
lie     -> end_plateau
Rejoin the main story:
printer_model -> office
restart -> ceremony, end_sick;   ceremony -> end_speech, lemon;   lemon -> office
walk_away -> resigned, draft;    draft -> plan;   resigned -> end_sabbatical, typo;   typo -> plan
ask -> garage, end_founding;     garage -> end_founding, end_borrowed, declined;   declined -> outage
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
- Live site (Netlify): https://keyboardheist.netlify.app
- Default working/push branch: `main`.
- Inspect `git status` before editing or committing. Preserve unrelated user work.
- Stage only task files, use a descriptive commit, and push to `origin main` after verification.
- GitHub CLI (`gh`) is installed and authenticated on this machine, so `git push origin main` works over HTTPS without prompting. If auth breaks, re-run `gh auth login` and retry.
- The site is hosted on Netlify at the URL above; the user set it up outside this repository, so there is no deployment configuration in the repo. Assume pushes to `main` deploy it, but verify with the user rather than claiming it is live.
- License: MIT, see `LICENSE`.

## Next work

The map, three early mini-adventures, and five rejoin routes are built and live as a first draft (32 scenes, 11 endings). Next, the user plays it on the live site and reports what they like and dislike; edit scene by scene from that feedback. Open questions: whether the main path (11 scenes) should be longer, which endings to keep or cut, and the inhaler and profanity choices. Verification so far: script syntax checked with macOS JavaScriptCore (no `node` installed), and every one of the 11 endings and all five rejoin routes were reached through the real engine with a stub DOM, including the ending button going back exactly one choice, the journey links were tested, and the scroll-to-scene behavior was tested against a stub (not on a real iPhone); no browser, mobile, or keyboard-navigation check, and the favicon was only viewed as a rendered PNG, not in a browser tab. Follow the standing rule to summarize and suggest 2-3 next steps whenever starting a new path. If introducing inventory, conditional choices, or multiple stories, explicitly design state/save compatibility and revise this handoff.
