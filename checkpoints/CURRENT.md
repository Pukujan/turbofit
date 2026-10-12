# Current Repository Checkpoint

<!-- continuity:current {"active_task":null,"active_task_file":null,"protocol_version":"0.1.0-draft","schema":"project-continuity.current.v1"} -->

This is an as-of projection; live GitHub issues own progression. Link the owning leaf, parent ancestry and dependencies for active work.

## Program state

Phase: cockpit UI verified, port pending. The cockpit was generated with the app-builder-automation Pro pipeline (model `ih/ali/qwen3.8-flash`) from `generator/specs/cockpit.md`. The Pro run was killed mid-build by the host (low system memory) after writing the data contract, fixtures, and full stylesheet; the remaining component JSX and wiring were completed by hand against those interfaces. The built page passes every ABA gate, a Playwright two-input interaction contract, and visual comparison against the reference. Next phase is porting into `dashboard/` as an optional view, then a cockpit-only upstream PR after design review.

## Completed

- continuity protocol initialized (minimal profile, GitHub authority, managed-worktrees mode).
- ACS coordination surface installed into the fork: `.coord/` assignment and lock (`state=READY`, `hotload_check=OK`, `oio_install=OK`), `stack-manifest.json` pinning PCM 851bcf7, CGM 62340f3 (0.5.12), OIO a4bba77.
- CGM adapter authored in `.content-system/` (6 files) and passes `validate_content_system.py` (helper + adapter): `VALID: content-generation-modules contract and target adapter`.
- OIO issue-log surface installed via the pinned installer under WSL and verified (`VALID: OIO package, project ontology, and managed files match`).
- CGM always-on writing block and the PCM issue-log-format block appended to `AGENTS.md`; `continuity validate` returns VALID.
- Fork `main` realigned to mirror `upstream/main` (43a8c06) plus fork-only commits: agent-ops scaffolding (5753f46) and the cockpit design packet with reference images (cac4895). Superseded commit 71a71d5 carried identical docs content, preserved on `cockpit` (3432ec5).
- `cockpit` branch rebased onto `upstream/main` (one commit ahead: the design packet) and pushed to the fork.
- Cockpit design packet `docs/cockpit/README.md` plus reference images committed (`cockpit-target.png`, `nous-girl-target.png`).
- Cockpit generated (2026-10-11): Pro pipeline wrote `turbofitStatus.ts` (endpoint contract + runtime-shaped types + `unavailable` formatters), `demoData.ts` (`tps: null`, missing per-GPU utilisation, never-run smoke test), and the 94-class `cockpit.css`; the component layer (`Cockpit/Scene/Hud/Console/views`), `Index.tsx` and the `cockpit.css` import were completed by hand against those interfaces after the run was killed.
- Verified (2026-10-11): ABA `verify-app.mjs` 7/7 at 1440x900 and 375x812 (all spec hooks mounted, no console errors, no dead links); interaction contract 8/8 (scroll drives the road only and is reversible; the five console buttons drive the view only; reduced-motion holds the road); `tsc -b` clean; `pnpm run build` green. Desktop render visually checked against `docs/cockpit/references/cockpit-target.png`: composition, anchors, wheel-over-cluster occlusion, copilot ring placeholder, and visible `unavailable` values all match. Evidence: `generator/out/cockpit/PORTING.md` + `preview-desktop.png`/`preview-mobile.png` in the ABA working copy.
- Scaffold substrate restored byte-identical to HEAD after the run (`pipeline/scaffold/src/globals.css`).

## Active

- none.

## Queued

- Port the verified cockpit into `dashboard/` as an optional view reading `GET /status` (`dashboard/plugin_api.py:204`); substitute `demoData.ts` with the fetch (field names already match the runtime payload, `plugin_tools.py:822-857`); render unmeasured metrics as unavailable; keep the `DEMO DATA` badge until wiring is real; no second `Registry.register`; run `python -m pytest tests/ -q` in the plugin repo.
- Open the fork issue that owns the porting task, then create its TASK projection (`continuity task new --issue <URL>`).
- After maintainer design review, open the cockpit-only upstream PR from a filtered branch (no agent-ops scaffolding).

## Blockers

None known. Note: the CGM `--check-adopter-docs` gate reports three prose "tool-dump" tells that originate in upstream's own docs (two files) and one literal `SKILL.md` mention in the cockpit design packet; the core helper+adapter gate passes VALID. No exclusion flag exists in the validator, so this gate is not achievable on this fork without editing upstream prose; treat as advisory.

## Next atomic action

Open the cockpit-port GitHub issue on the fork (pending consent — public action) and create its TASK projection, or begin the port directly if the issue is deferred.
