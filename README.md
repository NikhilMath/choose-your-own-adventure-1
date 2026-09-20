# Choose Your Own Adventure

This repository is the starting point for a choose-your-own-adventure project. This guide explains how to get a local copy, contribute changes, and continue building it.

## Current status

The project is at the initial repository stage. It currently contains:

- `README.md`: setup and contributor guidance.
- `LICENSE`: the MIT license.
- `.gitattributes`: automatic text-file line-ending normalization.

There is no application code, selected framework, dependency manifest, or build/test tooling yet. There are currently no install, run, or test commands, and no environment variables or API keys are required.

## Get set up

### Prerequisites

- Git installed on your computer. Check with `git --version`.
- A text editor or IDE of your choice.
- A GitHub account if you want to submit changes. You will need write access to push directly to this repository; otherwise, fork it and clone your fork.

### Clone the repository

Open a terminal and run:

```sh
git clone https://github.com/NikhilMath/choose-your-own-adventure-1.git
cd choose-your-own-adventure-1
git status
```

Open this folder in your editor. You now have everything needed to work on the repository in its current state. No additional packages need to be installed yet.

## How to contribute

1. Start from the repository's default branch, with your existing work committed or safely set aside. Pull the latest changes:

   ```sh
   git pull --ff-only
   ```

2. Create a branch for one focused change. For example:

   ```sh
   git switch -c codex/add-first-story
   ```

3. Make your changes. Keep related documentation up to date, especially if you introduce a runtime, dependencies, or new commands.

4. Review your work:

   ```sh
   git diff
   git diff --check
   git status
   ```

   `git diff --check` checks whitespace errors; it does not test application behavior. Until a test suite exists, describe any manual checks in your pull request.

5. Stage the files you intend to include and commit them. For a README-only change:

   ```sh
   git add README.md
   git commit -m "Document project setup"
   git push -u origin HEAD
   ```

   For other changes, substitute the relevant file paths and a descriptive commit message. Do not commit secrets, credentials, or local environment files.

6. Open a pull request on GitHub against this repository's default branch. Explain what changed, how you checked it, and any unfinished work. If you used a fork, open the pull request from your fork to this repository.

## How to continue development

The following is a suggested starting sequence, not an implemented feature list:

1. **Define the first playable experience.** Decide where it runs (for example, a browser or terminal), the story premise, and what counts as an ending.
2. **Choose and document the implementation.** Select a language and framework, add the initial application files, and record the required runtime version and exact install/run commands here.
3. **Build a small complete story.** Start with an opening scene, choices that lead to other scenes, at least one ending, and a way to restart.
4. **Check the story flow.** Verify that every choice leads to an existing scene, endings are reachable, and restarting returns the player to the beginning.
5. **Add automated checks as the implementation develops.** Document how to run them and what they cover.

When application tooling is introduced, update this README with:

- Required tools and supported versions.
- Dependency installation and local startup commands.
- Configuration variables, with a safe example file if needed.
- Where story content lives and how to add scenes and choices.
- Test and build commands.
- Deployment instructions, if deployment is added.

## Leave a useful handoff

When handing work to another contributor, include these details in the pull request or a linked issue:

- **Completed:** what now works and where the relevant files are.
- **Verification:** commands or manual steps used to check the change.
- **Next step:** the next concrete task someone can pick up.
- **Open questions:** decisions, bugs, or limitations that still need attention.

Keep this README aligned with what is actually in the repository so the next person can follow it without needing prior conversation context.

## License

This project is licensed under the [MIT License](LICENSE).
