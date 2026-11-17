# A2DP sink audit: manifest

- Run started: 2026-11-17 07:22 UTC
- Output folder (OUT): /home/user/audio (working copy of tunlezah/audio, empty before this run, not an audited repo)
- Configuration source: the owner's answers in the run session. Six repos selected. AudioBurger (tunlezah/AudioBurger) was left out at the owner's direction.

## Repos

| ID | Repo path | Remote | Owner's one-line description |
|----|-----------|--------|------------------------------|
| R1 | /home/user/a2dplaya | tunlezah/a2dplaya | (none given) |
| R2 | /home/user/AudioBurgerSonnet | tunlezah/AudioBurgerSonnet | (none given) |
| R3 | /home/user/AudioRouter | tunlezah/AudioRouter | (none given) |
| R4 | /home/user/Aurilis | tunlezah/Aurilis | (none given) |
| R5 | /home/user/awdeo | tunlezah/awdeo | (none given) |
| R6 | /home/user/SonicFlow | tunlezah/SonicFlow | (none given) |

## Snapshot (read-only commands, taken 2026-11-17 07:22 UTC)

| ID | Git repo | Default branch | Current branch | HEAD (git rev-parse HEAD) | git status --porcelain | Branch count (git branch -a) | Stash count |
|----|----------|----------------|----------------|---------------------------|------------------------|------------------------------|-------------|
| R1 | yes | main (see note 1) | claude/inspiring-galileo-a9dlia | 70ad071385a8dc79886a6b5596495f68cf9eb824 | empty | 4 | 0 |
| R2 | yes | main (see note 1) | claude/inspiring-galileo-a9dlia | 1d878c2e1b15c4cdb2baf342c30fbb247d491737 | empty | 4 | 0 |
| R3 | yes | main (see note 1) | claude/inspiring-galileo-a9dlia | 1bdb71dfb0e7319db0aa2f6cf41aedc853369204 | empty | 4 | 0 |
| R4 | yes | main (see note 1) | claude/inspiring-galileo-a9dlia | d740bc2b39836a0af00d8ad35144d31511bc9060 | empty | 4 | 0 |
| R5 | yes | awdeo_x86_kube (see note 2) | claude/inspiring-galileo-a9dlia | e556801cdd966f7bb86b8e7d2400718d38e8866c | empty | 4 | 0 |
| R6 | yes | main (see note 1) | claude/inspiring-galileo-a9dlia | 3046b9e8baea2b9150963706525ca95d76f983bc | empty | 4 | 0 |

Notes on the snapshot:

1. refs/remotes/origin/HEAD is not set in any clone, so the default branch could not be read from the remote. `main` is recorded as the default branch because it is the only branch besides the session branch.
2. R5 has no `main` branch. `awdeo_x86_kube` is recorded as the default branch because it is the only branch besides the session branch.
3. In every repo the current branch `claude/inspiring-galileo-a9dlia` was created by the session tooling when the repo was cloned. In every repo it points at the same commit as the branch recorded as default, and at the same commit as its origin counterpart. It holds no work of its own.
4. The branch count of 4 in each repo is two local branches (the default and the session branch) plus their two origin counterparts.
5. Tag count is 0 in every repo. No clone is shallow. Commits reachable from all refs: R1 4, R2 35, R3 1, R4 3, R5 4, R6 22.

## Owner notes

none
