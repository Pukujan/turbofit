# TurboFit Cockpit — Development Context Packet

> **Status:** design/planning only. This is an experimental **optional UI** for the existing TurboFit project, not a new inference engine. No backend behavior is changed by this document.
>
> **Repositories:** [working fork](https://github.com/Pukujan/turbofit) · [upstream](https://github.com/SouthpawIN/turbofit)

## Goal

Build an immersive, **first-person racing-car cockpit** dashboard for TurboFit. The car stays in the foreground; scrolling makes the road/environment appear to advance (and reverse when scrolling up). TurboFit metrics appear as transparent augmented-reality overlays on the windshield **and** as digital gauges in the car's instrument cluster. The car's visible touchscreen/buttons switch active dashboard modes and update the corresponding HUD content together.

**Visual north star:** photorealistic driver POV at night, reflective neon-blue/orange roadway, dark cockpit materials, glass-like cyan/orange HUDs, steering wheel foreground, and an interactive right-hand center console. A matching **daylight/light theme** changes the environment as well as the UI. The image supplied in the design conversation is a *target composition*, **not** a functioning UI or a source of real telemetry.

### Nous Girl — critical reference

Use the **specific black-and-white Nous-girl reference portrait supplied by the contributor**: strong black fills and white negative space; **straight blunt bangs**, **wide white headband**, shoulder-length dark hair with outward-curving ends, and **three-quarter profile facing left**. Do **not** substitute a generic anime girl (even one with a headband). First choice: a small cyan/white line-art holographic portrait on the upper-right windshield. Optional micro-animations only: idle shimmer, brief greeting/wave, and status acknowledgment. She must not cover system metrics. Validate use of the mascot/branding with the project maintainer before an upstream PR.

**Reference asset handoff:** source images were supplied in the design conversation; place the accepted cockpit composition at `docs/cockpit/references/cockpit-target.png` and the mascot reference at `docs/cockpit/references/nous-girl-target.png`. They are **not included in this initial docs commit**. Do not approximate them based on the previous generic-girl mockups.

## Read these first (in order)

| File | Why it matters |
|---|---|
| [`README.md`](../../README.md), [`SKILL.md`](../../SKILL.md) | TurboFit product scope, model fit, commands, expected integrations. |
| [`dashboard/manifest.json`](../../dashboard/manifest.json) | Hermes Dashboard plugin registration and entry files. |
| [`dashboard/dist/index.js`](../../dashboard/dist/index.js), [`dashboard/dist/style.css`](../../dashboard/dist/style.css) | Existing Hermes Dashboard UI to preserve and extend. Note: currently committed **dist** files; determine their source/build pipeline before editing. |
| [`desktop/plugin.js`](../../desktop/plugin.js) | Separate Hermes Desktop extension; not identical to Hermes Dashboard. |
| [`dashboard/plugin_api.py`](../../dashboard/plugin_api.py) | Actual status/configuration HTTP handlers for the Hermes Dashboard integration. |
| [`dashboard/index.html`](../../dashboard/index.html) | Standalone speedometer UI; **some numbers are generated with `Math.random()`** and are not live telemetry. Do not use as ground truth. |
| [`plugin_tools.py`](../../plugin_tools.py), [`product_ops.py`](../../product_ops.py) | Status snapshots, configuration and operational functions. |
| [`src/turbofit_runtime/controller.py`](../../src/turbofit_runtime/controller.py), [`policy.py`](../../src/turbofit_runtime/policy.py), [`runtime_service.py`](../../src/turbofit_runtime/runtime_service.py) | Adaptive runtime selection, pressure reactions, state and transitions. |
| [`src/turbofit_runtime/routes.py`](../../src/turbofit_runtime/routes.py), [`scripts/turbofit-gateway.py`](../../scripts/turbofit-gateway.py) | Active main/aux routing and OpenAI-compatible inference gateway. |
| [`tests/`](../../tests/) | Existing behavior, regressions, baseline test failures. |

## Interaction contract

- **Inference / Main Road:** throughput (only if backed by an actual measurement), active model, request/route health, inference status.
- **Models / Garage:** available models, active main/aux routes, model-fit recommendations. Prefer read-only initially.
- **Hardware / Engine Bay:** real GPU/CPU/memory state, VRAM pressure, current backend.
- **Profiles / Drive Modes:** Auto, Speed, Balanced, Intelligence (only expose choices actually supported by the installed TurboFit revision). Never silently change the active model when the user merely scrolls.
- **Diagnostics / Pit Lane:** backend health, runtime failures, log/trace links, smoke-test status.

**Two independent inputs:** (1) scrolling changes the visual road/camera progress and revealed section; (2) car-console buttons select the view. The animation is reversible, deterministic and navigable without scrolling. Keep critical data and actions reachable while the scene is stationary.

## Data and architecture

- **Reuse the existing Python inference gateway and Hermes Dashboard FastAPI extension.** Do not build a replacement inference backend.
- Inspect the JSON shapes of `/status`, `/profiles`, `/backends`, `/recommendations`, `/hardware-tiers`, and supported `POST` actions before implementing frontend bindings.
- Keep a small, typed frontend adapter separating TurboFit status payloads from HUD view state.
- Real telemetry only: represent missing metrics as **unavailable**, not guessed numbers; label demo fixtures explicitly.
- Existing runtime persists selections and routes in files. **No SQL database required for the MVP**; consider SQLite only if historical time-series retention is requested.
- Don't expose privileged configuration or model-operation endpoints directly to a public browser. Follow Hermes authentication/authorization and use trusted local/private development access.
- React/HTML/SVG is preferred for crisp interactive HUD text; Three.js / React Three Fiber or 2.5D layered scenery may render the road. Use scroll-driven GSAP only where it improves determinism and performance.
- Preserve the original dashboard and its working routes; Cockpit initially ships as an **optional toggle/view**.

## Milestones (small, reviewable PRs)

1. **Discovery:** verify endpoint payloads, dashboard build/setup, auth, current tests, and a reproducible dev path. Hermes already exists on contributor's MacBook. Google Colab GPU can be used for *temporary* inference smoke tests; **no requirement** to deploy Hermes on PC first.
2. **Visual skeleton:** one convincing cockpit, windshield HUD, instrument cluster, console hit targets, and nighttime environment, using labeled mock data.
3. **Functional HUD:** console buttons switch the five views; backend status and model/hardware data actually populate HUDs; loading/offline/error states work.
4. **Driving & themes:** bidirectional scroll-to-road parallax, transitions between track environments, daylight/light mode, reduced-motion fallback, responsive screen layout.
5. **Copilot:** faithful Nous-girl hologram from approved visual reference; restrained 2–3 animation states.
6. **Safe controls & verification:** integrate supported configuration actions with confirmation; verify real inference and failures; run tests; submit small upstream PR(s) after maintainer design review.

## Definition of done for first demo

The user sees the selected cockpit composition; can click in-car controls and observe coherent windshield/instrument-cluster changes; can scroll forwards/backwards without scene mismatch; can switch day/night; sees one **verified real TurboFit status reading**; and sees a recognizably faithful (not generic) mascot reference.

## Important open questions

- Should the optional experience live inside Hermes Dashboard, Hermes Desktop, or both? Start by proving the Dashboard extension path.
- Which runtime metrics are genuinely measured/exposed, at what sampling rate? Avoid replicating fake values from `dashboard/index.html`.
- Which approved Nous visual assets may be committed and redistributed?
- Will Colab tests be in-notebook smoke tests (preferred) or a supported, private cross-machine dev environment?
