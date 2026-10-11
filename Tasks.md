# Tasks

## Active
- [ ] Execute `wiki/Tracker-repair-roadmap.md` in priority order; one coherent repair batch per PR.
- [ ] Review and, if desired, split the historical feature commits before upstream submission.
- [ ] Decide separately whether to rewrite old Git history to remove cache blobs; this is destructive and requires explicit approval.

## Completed
- [x] Define one canonical Git checkout and clean the parent workspace.
- [x] Add `AGENTS.md`, `Goal.md`, `State.md`, `Philosophy.md`, and `Tasks.md`.
- [x] Classify the old outer-workspace files in `/home/kukuruza/orca/archives/qbit-search-plugins/2026-10-11/`.
- [x] Exclude session exports, caches, bytecode, logs, and generated plugin data.
- [x] Run targeted standalone tests: 80 passed.
- [x] Run `git diff --check` and verify a clean working tree.

## Completion gate
No active task is complete until `git diff --check`, targeted tests, license checks, and a clean classification of `git status` pass.

## Historical task cards
Detailed execution cards are archived outside the repository. They are evidence, not active instructions.
