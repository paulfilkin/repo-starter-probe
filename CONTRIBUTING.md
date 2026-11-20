# Working on repo-starter-probe

How we work on this repository.  Read it before your first change.  Questions go to @paulfilkin.

New to Git or to this repository?  `Docs\Working with Git.md` gives the commands for each step, from installing the tools to getting a pull request merged.  Repo Starter, the desktop tool that set this repository up, walks the same steps with buttons.

## 1. Getting set up

1. Install Git, the GitHub CLI and the Python tools.
2. Sign in with `gh auth login` (github.com, HTTPS, browser), then run `gh auth setup-git`.
3. Clone the repository: `gh repo clone paulfilkin/repo-starter-probe`.
4. Point Git at the repository's hooks: `git config core.hooksPath .githooks`.  Repo Starter does this for you when it adds the clone.
5. Run the checks: `python --version`.  They should pass before you change anything.

## 2. How a change gets into main

Nobody pushes to `main`.  Every change arrives through a pull request, passes the automatic checks and is approved.  On a public repository GitHub enforces this; on a private one the hooks refuse the push and the Guard workflow reports anything that slips through (section 3).

Every change starts from an issue.  Open one with the **Feature** or **Bug** form on the repository's Issues tab.  The form adds the type label (`feature`, `bug`); add `docs` or `chore` where they fit, and `blocked` while an issue is waiting on something else.  An issue is ready to work on once it has acceptance criteria and isn't labelled `blocked`.

The [project board](https://github.com/users/paulfilkin/projects/6) shows every issue in one of GitHub's three columns: Todo, In Progress and Done.  The columns are left as GitHub made them, so its built-in automations do the moving.

- A new issue is added to the board automatically, in Todo.  One that is not ready to pick up is labelled `blocked`.
- Opening a pull request with `Closes #42` links it to the issue.
- You move it to In Progress when you assign it to yourself; Repo Starter's Start work does both.
- Merging the pull request, or closing the issue, moves it to Done.

1. Pick a ready issue and assign it to yourself, so nobody else starts the same work.
2. Start from the latest `main`: `git switch main`, then `git pull`.
3. Create a branch named for the issue: `feature/`, `fix/`, `docs/` or `chore/` followed by the issue number and a short name, for example `feature/42-feedback-export`.
4. Make the change in small commits.  Push the branch whenever you stop work, so nothing lives only on your machine.
5. Open a pull request into `main` and fill in the template, with `Closes #42` (the issue number) in the description.  The merge then closes the issue and links the two.  Keep a pull request to one issue; several small ones are reviewed faster than one large one.
6. The automatic checks run.  Fix anything they report.
7. The reviewer reviews.  Answer every comment, either with a change or a reply, and resolve the conversation.
8. The pull request is merged with **Squash and merge**, which makes it one commit on `main`.  The branch is then deleted and the issue closed.

## 3. What the automatic checks do

Every pull request runs two workflows on GitHub.  Both must pass before a merge.

**Build and test** (`.github/workflows/ci.yml`):

- It fails if a data file, key, certificate or `.env` file is in the repository.
- It builds the project with warnings treated as errors, then runs the tests and the self-test where the project has them.

**Guard** (`.github/workflows/guard.yml`):

- On a pull request it fails if the branch name doesn't follow the convention, if the description has no `Closes #<issue>` line, or if the branch is more than 10 commits behind `main`.
- On a push to `main` it looks for the pull request behind the commit.  If there is none, it opens an issue labelled `chore` so the slip is visible on the board.

Two local hooks in `.githooks\` apply the same rules before anything reaches GitHub: `commit-msg` checks the commit message (section 6) and `pre-push` refuses a push to `main`.

Run the same build and checks on your machine before you push.  A red check on GitHub costs everyone a round trip.

## 4. Standards for code

- **Read before you change.**  Read the code you are changing and the code that calls it.  Follow the patterns already there rather than adding new ones.
- **Every change has tests, in the same pull request**, where the project has a test project or a self-test: a unit test for logic that takes plain values, a self-test check for anything that goes through the running application.
- **Keep comments.**  Don't remove a comment unless you remove the code it describes.  Add one where the reason for the code isn't obvious.
- **No new packages without agreement.**  Ask before adding a dependency.
- **No data or secrets in the repository.**  Real data, passwords, API keys, tokens and certificates never go into Git, including in test code and screenshots.

## 5. Writing style

This applies to text in the application, comments, docs and commit messages.

- British English.
- No em-dashes; use a normal hyphen.
- Short and direct.  State the fact without decoration.
- In documents, two spaces between sentences.  In code, comments, commit messages and UI text, one.
- Markdown for documentation.  Ask before adding a new document.

## 6. Commit messages and pull requests

- First line: a short summary of the change, in the imperative ("Add feedback export", not "Added" or "Adds"), starting with a capital letter, no longer than 70 characters, no full stop at the end.
- Then a blank line and, where needed, what changed and why, wrapped at about 72 characters.
- The `commit-msg` hook refuses a message that breaks these rules; Repo Starter's Save and share shows the same checks as you type.
- The pull request description says what changed, why, and how you tested it.

## 7. Documentation

Documentation lives in `Docs\`, in Markdown.  Update it in the same pull request as the code when the change alters how something works or how it is used.  Ask before adding a new document.

## 8. AI coding assistants

Claude Code, Copilot and similar tools are allowed, with these conditions:

- You are responsible for every line you submit.  Understand it, test it and be able to explain it in review.
- Say in the pull request that an AI assistant was used and for what.
- The same checks and review apply.
- Don't paste personal data or secrets into an assistant.
- If you use Claude Code, the conventions in this file are the ones to give it.

## 9. What the reviewer checks

- It does what the pull request says, and nothing else.
- Tests cover the new behaviour (section 4), and they pass.
- No data, secrets or unagreed packages.
- Existing patterns are followed and comments are kept.
- The docs are updated, and the writing follows section 5.

## 10. Access

Collaborators get the **Write** role.  It lets you push branches and open pull requests.  Merging into `main` is done by the owner, after the checks and the review.
