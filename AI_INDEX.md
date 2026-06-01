# AI_INDEX — Trumpspillet 2026 (kort indgangsfil)

Formål: Hurtig onboarding for AI. Detaljer findes i:
- `WORKFLOW_ANCHORS.md` (anchors, Mini‑TOC, workflow‑regler)
- `START_PROMPT.md` (arbejdskontrakt + status)

## Filer (kort)
- `index.html` — alt spil (HTML/CSS/JS, scenes, input, render). Filen blev omdøbt fra `Trumpspillet 2026.html` for Cloudflare Pages-publicering.
- `Trumpspillet2026Mobil.html` — separat mobiltestfil. Aktuelt fokus: portrait-first Greenland-test med touch/intent-tap.
- `assets/` — spilassets. Tidligere root-assets er flyttet hertil.
- `assets/music/` — placeholder-struktur til kommende egne musikfiler.
- `WORKFLOW_ANCHORS.md` — anchors + workflow
- `START_PROMPT.md` — arbejdskontrakt + seneste ændringer

## Deploy/status
- Git remote: `https://github.com/kristianjensen5/Trumpspillet2026.git`
- Cloudflare Pages project: `trumpspillet2026`
- Production URL: `https://trumpspillet2026.pages.dev/`
- Mobiltest: `https://trumpspillet2026.pages.dev/Trumpspillet2026Mobil?mobileTest=1`
- User-regel: deploy som standard efter hver færdig update, medmindre andet aftales.

## Hot zones (ændr kun med eksplicit OK)
- Global render/update loop (scene‑gated)
- `INPUT_GLOBAL_KEYDOWN` (alt tastaturinput)
- Save/load/localStorage‑logik
- Fight-input-bridge (`installFightInputBridge` IIFE) — capture-phase listeners + scene-guard + debug telemetri bag `DEBUG_FIGHT_STATE`
- `Trumpspillet2026Mobil.html` touch-input: ændr kun mobilflowet; desktop `index.html` må ikke røres ved mobiltest uden eksplicit OK.

## Scene/State‑API (koncept)
- `resetScene()`‑funktioner initierer scene‑state
- Update/render sker i global loop med `if (scene === SCENE.X)`
- Scene‑specifik UI/hints kaldes betinget på `scene`
- **Brug altid `SCENE.*`-constants** i sammenligninger og `switchScene()`-kald — strings (`'greenland'`, etc.) er udfaset
- `SCENES`-registry styrer scene-livscyklus (reset/enter/onLeave-hooks). Scene-skift går igennem `switchScene(SCENE.X, params)`

## Globals (må ikke omdøbes)
- `CFG`, `GAME_CONSTANTS`, `GS`, `GL`, `OV`, `KR`, `EP`, `GOLF`
- `SCENE` (constants), `SCENES` (registry)

## Anchors (navne)
- `SCENE_GREENLAND`, `SCENE_OVAL`, `SCENE_KREMLIN`, `SCENE_EPSTEIN`, `SCENE_GOLF`
- `INPUT_GLOBAL_KEYDOWN`
- SCENE_GOLF har 40-linjers state-maskine-dokumentation i toppen — start dér ved Golf-debugging

## Debug-flags (kun til udvikling)
- `window.DEBUG_FIGHT_STATE = true` — viser fight-overlay HUD + tæller bridge-events + advarer ved post-stop state-leaks
