# frist_hackathon

Run one Claude Code session per workstream. Use a GitHub issue as the shared message board and one Git branch per workstream. Every teammate can follow the discussion from GitHub or the terminal.

## Before the hackathon

You need Python 3.9+, Git, GitHub CLI (`gh`), and a working Claude Code session. Commands below use Bash or Zsh. For Windows, use WSL with the tools installed inside it.

The starter is on `main` and the [Agent board](https://github.com/PranavMahesh55/frist_hackathon/issues/1) is open. The repository is private. Add your teammates as collaborators and have them accept their invitations. Each teammate should run `gh auth login` if needed, then `gh auth status`.

Clone the repo and run the following from its root. Choose a unique agent name for each session.

```bash
git clone https://github.com/PranavMahesh55/frist_hackathon.git
cd frist_hackathon
export BOARD_ISSUE=https://github.com/PranavMahesh55/frist_hackathon/issues/1
export BOARD_AGENT=pranav-api
python3 tools/board read
python3 tools/board post claim "Pranav owns the API in src/api."
claude
```

Have a second teammate run `python3 tools/board unread --mark` and post a reply. Back in the first session, submit a prompt asking Claude to summarize the latest board update. The hooks attach the five most recent messages at session start and when you submit a prompt. Between prompts or during a long turn, the agent can check the board directly. Board names are per shell, so export them before launching Claude.

## Split the first sprint

| Session | Owns | Posts early |
|---|---|---|
| API | Endpoint and implementation | Route, payload, example response |
| UI | Main screen and loading/error states | Mock assumptions and integration needs |
| Demo | Seed data and end-to-end demo | Demo scenario and fixture location |

Example opening prompt for the API session:

> You own the API in src/api. Read the board, inspect the project, then post the proposed endpoint and sample JSON before you start implementation. Keep changes within your assignment. Run focused tests and post a handoff with the branch and results.

On separate laptops, use separate clones and branches. For several sessions on one laptop, create one worktree per branch **after** the starter is committed. For example, from a checkout with an up-to-date `main`:

```bash
git fetch origin
git worktree add --track -b hack/api ../hack-api origin/hack/api
git worktree add --track -b hack/ui ../hack-ui origin/hack/ui
git worktree add --track -b hack/demo ../hack-demo origin/hack/demo
```

Launch each Claude session from its worktree in a separate terminal with its own `BOARD_AGENT`. Each session keeps its usual Claude Code permissions. Push each branch and open a PR as you normally would, then post the link. Pick one person to review, merge, and run the final integrated demo.

## Board commands

```bash
python3 tools/board post contract "GET /api/session returns {score: number}."
python3 tools/board post blocker "Waiting on the demo fixture path."
python3 tools/board post ready "PR #12 is ready. API tests passed."
python3 tools/board read --limit 10
python3 tools/board unread --mark
python3 tools/board wait --seconds 25
```

`read` shows recent issue comments, including messages typed directly into GitHub. `unread` and `wait` use a cursor local to the machine and agent name. Only `unread --mark` advances it. Reusing a name on another machine starts with a fresh cursor. If GitHub is unavailable, you can still keep coding on each branch and use a local board for sessions on the same machine.

## Offline rehearsal on one laptop

In both shells use the **same absolute path** and set a different `BOARD_AGENT` in each. Use a fresh database path if repeating the exercise. The database must stay on the local machine.

```bash
export BOARD_LOCAL="$HOME/hackathon-rehearsal.sqlite3"
export BOARD_AGENT=pranav-api
python3 tools/board post contract "GET /api/session returns {score: number}."
BOARD_AGENT=teammate-ui python3 tools/board unread --mark
```

`BOARD_LOCAL` takes precedence if both variables are set. Run `unset BOARD_LOCAL` when switching back to GitHub. If the CLI works but Claude misses updates, relaunch Claude from the repo root with the same exported variables and check `/hooks` for the two entries. The CLI also works independently of Claude.
