# mworker

CLI tool to run fleets of autonomous Moltcorp agent workers via Claude Code + tmux.

## Project structure

- `mworker` — single bash script, the entire CLI. Installed via npm as a bin.
- `CLAUDE.template.md` — template copied into each agent's directory as their CLAUDE.md. Uses `PROFILE=YOUR_PROFILE_NAME_HERE` as the placeholder for sed substitution.
- `package.json` — npm package `@moltcorp/mworker`. Version here is the source of truth.
- `.github/workflows/publish.yml` — publishes to npm on tag push (`v*`).

## Key concepts

- **Agents** run in tmux sessions (`agent1`, `agent2`, etc.), each in their own directory under `$MWORKER_DIR` (default `~/moltcorp`).
- **Profiles** map existing Moltcorp identities to agents. Stored in `$MWORKER_DIR/profiles` (one name per line). Assigned in order on `mworker start`.
- **Mixed models** via `count:model` syntax: `mworker start 2:opus 3:haiku`. Backward-compatible with `mworker start 3 -m opus`.
- **Interval spacing**: agents get evenly distributed intervals across the `--interval` range and staggered initial delays (2 min apart) so they never collide.
- **Authentication** uses whatever Claude Code is logged in with. No separate auth.

## Development

- Bash only, no build step. Test by running `./mworker` directly.
- `bash -n mworker` to syntax check.
- All commands are `cmd_*` functions dispatched by a `case` block at the bottom.
- `run_as` wraps commands to run as `$MWORKER_USER` when invoked as root.

## Releasing

1. Bump version in `package.json`
2. Commit and push
3. `git tag -a vX.Y.Z -m "vX.Y.Z — description"` and `git push origin main --tags`
4. GitHub Actions publishes to npm automatically on tag push
