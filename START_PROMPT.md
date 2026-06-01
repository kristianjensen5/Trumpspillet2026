Vi arbejder på Trumpspillet 2026 - et stort single-file HTML/JS spil.

## Arbejdskontrakt (AI)
Følg WORKFLOW_ANCHORS.md. Disse regler er bindende:
- **Scope:** Ret kun det område vi har bekræftet (anker/funktion/linje). Ingen ændringer uden for scope.
- **Ingen refactors:** Ingen oprydning, omstrukturering eller ekstra ændringer uden eksplicit OK.
- **Før ændring:** Vis præcis placering + relevant kodeudsnit (linjer) og vent på OK.
- **Hvis uklart:** Stop og spørg før du ændrer.
- **Efter ændring:** Kort liste over hvad der blev ændret + hvor i filen.
- **Efter patch/batch:** Tilføj kort resume i `START_PROMPT.md`.
- **Test:** Hvis du ikke kan teste, sig det.
- **Mini‑TOC:** Brug Mini‑TOC til at vælge korrekt anker før ændringer.
- **Anchors:** Ændringer må kun ske mellem `START`/`END`.
- **Input:** Tastatur‑ændringer kun i `INPUT_GLOBAL_KEYDOWN`.
- **Arkitektur:** Scene‑reset/helper ligger i anker; render/update ligger globalt — rør ikke global loop uden eksplicit OK.
- **Anchor‑udsnit:** Hvis jeg indsætter et anker‑udsnit, returnér hele anker‑blokken i fuld længde (kun målrettede ændringer).

## Workflow
- Jeg beskriver en opgave
- Du finder lokationen først (linjenummer + funktion)
- Jeg bekræfter
- Du implementerer

## Seneste status (2026-06-01) — Git, Cloudflare og mobiltest

### Git/deploy
- Projektmappen er nu et git-repo på `main`, tracking `origin/main`.
- Remote: `https://github.com/kristianjensen5/Trumpspillet2026.git`
- Seneste commit: `0e97ea6 Improve mobile Greenland intent taps`
- Git-status ved stop: ren (`main...origin/main`).
- Cloudflare Pages-projekt oprettet: `trumpspillet2026`
- Fast testlink: `https://trumpspillet2026.pages.dev/`
- Mobiltestlink: `https://trumpspillet2026.pages.dev/Trumpspillet2026Mobil?mobileTest=1`
- Arbejdsregel fra user: Deploy som standard efter hver færdig update, medmindre andet aftales.

### Asset/musikstruktur
- Assets er flyttet fra roden til `assets/` og registreret som renames i git.
- `assets/music/README.md` er oprettet som placeholder for kommende musikfiler.
- Anbefalede fremtidige musikfilnavne: `intro.mp3`, `greenland.mp3`, `oval.mp3`, `kremlin.mp3`, `epstein.mp3`, `golf.mp3`.
- Musikmotoren er endnu ikke ændret til filbaseret playback; den bruger stadig eksisterende WebAudio/chiptune i `index.html`.

### Mobiltest — Greenland portrait
Primær testfil: `Trumpspillet2026Mobil.html`.

Implementeret:
- Forced landscape slået fra.
- `?mobileTest=1` kan tvinge mobilmode i desktop/emulator.
- Gamle synlige mobilknapper skjules i testflow.
- iOS/browser long-press selection/callout slået fra med `user-select:none` og `-webkit-touch-callout:none`.
- Talebobler i Greenland er gjort større og mere læsbare i mobilmode.
- Hold venstre/højre side af canvas = Trump går venstre/højre.
- Kort tap = interaktion.
- Mobil-only “intent tap” i Greenland:
  - tap nær butik → auto-walk + åbn butik
  - tap nær Lars/Mette → auto-walk + tal
  - tap nær mine/rig/heli/items/glaciers/coins → auto-walk + interagér
  - tap på tomt område → fallback til `interact()` hvis Trump allerede står tæt på noget
- Ingen labels/outline må tilføjes til interaktionshjælp; user ønsker usynlig assistance.

Kendte mobil-observationer / næste punkter:
- Mobilfilen er ældre end desktop: Greenland bruger stadig Mette-figur i stedet for Lars Løkke.
- Hop mangler i det nye touch-flow. Mulig løsning: swipe-up = `doJump()`.
- Butikken kan åbnes, men shopknapper er ikke touch-optimeret endnu.
- Kamera/beskæring er tæt på korrekt, men Trump kan muligvis zoomes lidt større.
- To-fils-strategi giver vedligeholdelsesrisiko; overvej senere fælles data for dialoger/assets/scene config.

## Seneste ændringer (2026-04-28) — Continue-skærm + nye game overs

### Continue-skærm visuelt redesign
- Mørk navy/rød gradient baggrund med dekorative baggrundsstjerner
- Maskinen: navy kasse, rød topstribe, guld-label "DIEBOLD NIXONDORF™", blåt glow-skærm-ramme, afrundede knapper
- Trump-figur (2,8× skaleret `drawIntroTrump`) til højre med taleboble: "VOTE TRUMP!" → "YOU'RE HIRED! BIGLY!" ved landing → "WITCH HUNT! RIGGED!" ved Democrat-klik
- Roterende guld-stjerner + pulserende "+1 LIFE — TREMENDOUS!" når Trump lander
- DEMOCRAT-knap klikkabel: slot viser "RIGGED!", 1,8 sek. delay → `fullRestartToIntro()`
- `GO.lastScene` gemmer hvilken bane Trump døde i → fortsæt sender spilleren tilbage til samme bane

### Isbjørn slår Trump ihjel (Grønland)
- 3 bid → game over: "EATEN BY A BEAR!" / "This is very unfair treatment!"
- HUD viser "🐻 Bear attack! (1/3)" osv. Dynamit sætter `hp=0` så chase starter straks (tidligere bug: chase gated af `hp<=0`)
- Artwork: bjørn med Trump i munden, blodstænk på isblå baggrund

### Golf-tab til Biden → avisforside
- Scorecard erstattet. Banner siger "Sleepy Joe wins!" → game over "LOSER!" med de rigtige slagtal
- Artwork: MAGA GAZETTE forside — Biden fejrer, Trump hænger med hovedet

### Reference: alle game over causes
| Cause-ID | Scene | Trigger |
|---|---|---|
| `lars-fight` | Grønland | Taber kampen mod Lars Løkke |
| `ice` | Grønland | Sprænger isbjerget med dynamit |
| `bear` | Grønland | Isbjørn bider Trump 3 gange |
| `nuke` | Oval | SURPRISE-knappen |
| `kremlin` | Kreml | Sprænger Putin med dynamit |
| `wagner` | Kreml | Wagner Group fanger Trump |
| `golf-loss` | Golf | Trump taber til Biden |
| `epstein_success` | Epstein | Korrekt dokumentgennemgang (succes-ending) |
| `epstein_fail` | Epstein | Forkert klik / timeout |

---

## Seneste ændringer (2026-04-27) — Dynamit-bugfix-batch

### PLANT ReferenceError (kritisk)
`beginPlant()` indeholdt `PLANT = null` — en rest fra et gammelt hold-to-plant system. I strict mode kaster assignment til en ikke-deklareret variabel ReferenceError. Bivirkning: `lastPlantAt` nåede at blive opdateret FØR fejlen → 900ms-cooldown-spærringen aktiveredes ved første tryk → alle efterfølgende tryk virkede stille.

**Fix:** Fjernet `PLANT = null` og den dead `completePlant()`-funktion fra `beginPlant()`.

### nearestGL returnerede null for store isbjerge (kritisk)
`nearestGL()` målte afstand til center — men isbjerge er 240–300px brede. Spilleren "ved" isbjerget var 100+ px fra centret → `null` → ground-fallback i stedet.

Første fix (distToRect i nearestGL) var for aggressivt: isbjergets bounding box overlappede butikken → butikken kunne ikke klikkes.

**Endelig fix:** Ny `nearestPlantableGlacier()` helper ([index.html:7158](Trumpspillet%20Master%202026/index.html#L7158)) — bruger `distToRect()` med 30px edge-threshold, men KUN i dynamit-plant-stien. `nearestGL()` urørt.

### Øvrige bugfixes fra samme session
- **Birthday drawing sti** (~linje 915): `'drawing.png'` → `'assets/drawing.png'` — tegningen vistes ikke i intro
- **Golf victory walk-back**: Trump går nu til helikopter efter sejr — genbruger 'leaving'-state-maskinen (samme som Esc)
- **Golf medal** (~linje 4590): `awardMedal('golf')` tilføjet i `playerWon`-branch

### Reference: alle sprængsomme objekter (`completePlantImmediate`, ~linje 8389)
| Type-ID | Scene | Effekt |
|---|---|---|
| `mette` | Grønland | Flyver ud af skærm, `awardMedal('greenland')`, flag dropper |
| `bear` | Grønland | Isbjørn bliver rasende + chaser (300 px/s) |
| `rig` | Grønland | Flammer spawner i toppen af riggen |
| `glacier` | Grønland | Begge isbjerge blown=true + `startGlacierExplosion()` efter 600ms |
| `putin` | Kreml | Game over: "World Peace?" efter 500ms |
| `ground` | Alle | Eksplosionslyd, rystelse, gnister, besked |

Isbjerge kræver `nearestPlantableGlacier()` i plantstien (center-afstand er for striks for store objekter).

---

## Seneste ændringer (2026-04-26) — Musik-overlap-fix

User-rapporteret bug: gammel banes musik fortsatte i ~10-15 sek efter scene-skift fordi noter allerede var scheduleret ind i AudioContext-tidslinjen.

### Fix: per-track gain node
- Tilføjet `Music.trackGain` mellem note-oscillatorer og `masterGain`
- Hver track får sin egen trackGain ved `Music.start(name)`
- Ved `Music.stop()`: fade gammel trackGain → 0 over 50ms, disconnect efter 200ms
- Resultat: scheduleret-men-ikke-spillet-endnu noter passerer gennem en silent gain-node = ingen overlap
- `masterGain` urørt (mute/volume virker uafhængigt)

### Implementering ([Music engine](Trumpspillet%20Master%202026/index.html#L1849))
- `start()`: opretter ny `audioContext.createGain()` for trackGain efter `stop({keepCurrent:true})`
- `_scheduleNote()`: forbinder noteGain → `this.trackGain || this.masterGain` (fallback hvis ikke initialiseret)
- `stop()`: `cancelScheduledValues + setValueAtTime + linearRampToValueAtTime(0, +0.05s)` + setTimeout disconnect

### Bi-effekt
Åbner for ægte crossfade i fremtiden — i stedet for 50ms hard fade kan fade-tider gøres længere (300-500ms) for blødere overgange. Ikke implementeret endnu (trin 3-arbejde).

---

## Seneste ændringer (2026-04-26) — MIDI soundtrack trin 2 (alle 5 baner)

Tilføjet 4 yderligere chiptune-tracks til musik-engine. Alle baner har nu egen tema-musik. Greenland-bassen sænket til 0.035 efter user-feedback.

### Nye tracks ([index.html:TRACKS-objekt](Trumpspillet%20Master%202026/index.html#L1858))
| Bane | Tempo | Toneart | Vibe |
|---|---|---|---|
| Greenland | 100 BPM | A-mol | Kold, sparse, eftertænksom |
| Oval | 110 BPM | C-dur | Pompøs march, fanfare lead |
| Kremlin | 95 BPM | D-dorisk | Spy-thriller, walking bass |
| Epstein | 130 BPM | E-frygisk | Tense, hurtig, ascending arps |
| Golf | 105 BPM | C-dur | Yacht rock, jazzy bossa |

Alle 8 bars, 4/4, samme gain-balance (bass 0.035, lead 0.10). Square-bass + triangle-lead (undtagen Oval som har square-lead for fanfare-stilen).

### SCENES-registry udvidet
Hver scene har nu `track: 'name'`-feltet. `switchScene` håndterer overgangen automatisk: ny track starter, gammel stoppes (ingen crossfade endnu — det kommer i trin 3).

### Trin 3 (godkendt plan, IKKE implementeret)
- Mute-knap (M-tast + localStorage persistence)
- Crossfade ved scene-skift (~300ms ud, ~500ms ind) for blødere overgange
- Separat music/sfx volume-control hvis user vil

---

## Seneste ændringer (2026-04-26) — MIDI soundtrack trin 1 (Greenland)

Bygger programmatisk chiptune-musik via eksisterende AudioContext. Ingen eksterne filer (Cloudflare-friendly). Trin 1 af 3-trins plan (engine + Greenland-track først, så user kan teste stilen før resten af scenerne komponeres).

### Ny infrastruktur ([index.html:1814-1944](Trumpspillet%20Master%202026/index.html#L1814-L1944))

**`NOTE_FREQ`** — note-navn til frekvens lookup (C2-B5, equal temperament A4=440Hz)

**`TRACKS.greenland`** — 8-bar loop, 100 BPM, A-mol:
- Bass (square, gain 0.06): A2 → E2 → F2 → G2 progression, slow 4-beat changes
- Lead (triangle, gain 0.10): sparse arpeggio melody med rests for "weite" feel

**`Music`-engine:**
- `Music.start(trackName)` — starter loop, retries hvis audioContext er suspended
- `Music.stop()` — clearer loop timer + scheduled notes spiller naturligt ud (≤1 bar tail)
- `Music.setMuted(bool)` — fade master gain (klar til mute-knap i trin 3)
- `pendingStart` håndterer race-condition på første scene-load (user-gesture unlocker context async)

### Integration ([index.html:6912-6917](Trumpspillet%20Master%202026/index.html#L6912-L6917))
- `SCENES[SCENE.GREENLAND].track = 'greenland'`
- `switchScene` checker `def.track` → `Music.start(track)` eller `Music.stop()` hvis ingen track
- Andre scener har endnu ingen track → musik stopper når man forlader Greenland

### Volume-balance
- Master gain: 0.5
- Bass: 0.06 per-note gain
- Lead: 0.10 per-note gain
- ADSR per note: 15ms attack, decay til 70% ved 60% af duration, ramp til 0 ved end

### Næste trin (godkendt plan, IKKE implementeret)
- **Trin 2:** Komponere 4 yderligere tracks (Oval/Kremlin/Epstein/Golf) i samme stil-familie men med scene-tema
- **Trin 3:** Mute-knap (M-tast + localStorage), crossfade ved scene-skift, separat music/sfx volume

---

## Seneste ændringer (2026-04-26) — BANNER-system (P6 Option B)

Centraliserede in-canvas banner-styling i ét konstantobjekt + helper. Fortsætter den unifikation P6 Option A startede.

### Ny infrastruktur ([index.html:2115-2143](Trumpspillet%20Master%202026/index.html#L2115-L2143))
**`BANNER` konstantobjekt:**
```js
const BANNER = {
  BG:          'rgba(0,0,0,0.7)',
  ACCENT:      '#ffd700',     // gold border for celebrate/active
  TEXT:        '#fff',
  RADIUS:      8,
  RADIUS_PILL: 12,
  BORDER_W:    2
};
```

**`drawBannerBg(x, y, w, h, opts)` helper:**
- `opts.highlight`: gold border (true) eller ingen border (false)
- `opts.radius`: override radius (0 = firkantet, default = BANNER.RADIUS)

### Refaktoreringer
- **Golf-titel** ([4681](Trumpspillet%20Master%202026/index.html#L4681)): manuel `fillStyle + roundRect + fill` → `drawBannerBg(...)` (1 linje)
- **"Par!"-banner** ([4728](Trumpspillet%20Master%202026/index.html#L4728)): manuel `fillRect + strokeRect` → `drawBannerBg(..., { highlight: true, radius: 0 })` (1 linje)
- **Score panel** ([4693](Trumpspillet%20Master%202026/index.html#L4693)): hardcoded farve → `BANNER.BG` + `BANNER.TEXT`
- **Charge bar** ([4657](Trumpspillet%20Master%202026/index.html#L4657)): hardcoded farver → `BANNER.BG` + `BANNER.ACCENT` + `BANNER.BORDER_W`. Bonus: `#ffd54f` (lysegul) → `#ffd700` (matcher "Par!"-banner)

### Hvad denne refactor giver
- Én kilde til banner-farve: skift `BANNER.BG` ét sted og alle banners følger med
- "Par!" og charge bar deler nu eksakt samme gold-tone (var `#ffd700` vs `#ffd54f`)
- Fremtidige bannere kan bruge helperen direkte → ensartet look ud af boksen

### Hvad blev IKKE rørt (uden for scope)
- HTML-overlays (HUD chips, areas pills, medal-display) — CSS-styled
- Captions ("TEE", "Drop +1") — egen transient styling
- "Sleepy Joe is lining up..." waiting box (Golf) — uden for tidligere godkendt P6-scope

---

## Seneste ændringer (2026-04-26) — fullRestartToIntro routing fix

Lukker den latente bug fundet i #8-sessionen. `fullRestartToIntro()` ([index.html:5271](Trumpspillet%20Master%202026/index.html#L5271)) sætter ikke længere `scene = SCENE.INTRO` direkte — den ruter nu gennem `switchScene(SCENE.INTRO)`.

### Hvorfor det matter
Hvis spilleren er i Golf og rammer game-over → Shift+R kalder `fullRestartToIntro`. Før blev `SCENES[SCENE.GOLF].onLeave` (som kører `golfUnbindInput`) springet over → golf-pointer-handlers forblev bundet til canvas. Nu ryddes de korrekt op.

### Status: scene-routing er nu fuldt centraliseret
Kun ÉT sted i kodebasen muterer `scene` direkte: line 6718 inde i `switchScene` selv. Alt andet går igennem `switchScene(SCENE.X)`.

---

## Seneste ændringer (2026-04-26) — Lars-kampspil komplet ✅

Implementerede planen fra forrige session. Kampen er nu reelt vindbar OG tabbar.

### A. `SFX.punchHit()` tilføjet ([index.html:1793-1796](Trumpspillet%20Master%202026/index.html#L1793-L1796))
Kort impact-lyd (~50ms): low sawtooth thud + click overlay

### B. Lars's jab dealer skade ([index.html:6964-7003](Trumpspillet%20Master%202026/index.html#L6964-L7003))
- Initial state + `start()` tilføjer `hit:false` til `lars.jab`-objekt
- Mid-fase hit-window (jab.t 63-99ms af 180ms): check afstand → 12 dmg på trumpHP
- Nulstilling af `jab.hit` ved aktivering (parallelt med Trump's punch.hit)

### C. Trump hit-flash ([index.html:7163-7169](Trumpspillet%20Master%202026/index.html#L7163-L7169))
`_trumpFlashUntil` mirror af `_larsFlashUntil` — hvid flash 120ms på Trump's sprite ved hit

### D. Lose-state når `trumpHP <= 0`
Triggers `triggerShake` + `SFX.error()` + `gameOverCause('lars-fight', { title:'KO!', subtitle:'Lars fik dig. Forsøg igen.', delayMs:600 })`. Stopper FightEngine med `winner:'lars'` (ingen flag drop).

### E. Lyd + partikler på Trump-hits ([index.html:6934-6938](Trumpspillet%20Master%202026/index.html#L6934-L6938))
- Tilføjet `SFX.punchHit()` + `spawnSparks(midpoint, 8)` ved normale hits
- Ved KO (HP=0): `spawnSparks(midpoint, 30)` — større burst

### Balance (justérbart)
- Trump: 10 dmg / 260ms cd → ~38 dps
- Lars: 12 dmg / 900ms cd → ~13 dps

### Test-checklist
1. Start Lars-kamp via dialog (3 runder → fight)
2. Bevæg dig væk fra Lars → han drifter mod dig
3. Lars's jab visualisere → check at trumpHP falder ved nær-kontakt + lyd + sparks + flash
4. Tab kampen → KO!-skærm + voting machine continueOverlay
5. Vind kampen → flag drop som hidtil
6. Esc afbryder stadig → walks tilbage til heli

---

## Seneste ændringer (2026-04-26) — startGame routing fix (#8)

`startGame()` ([index.html:1919–1928](Trumpspillet%20Master%202026/index.html#L1919-L1928)) sætter ikke længere `scene = 'greenland'` direkte. Den ruter nu gennem `switchScene(SCENE.GREENLAND)` så SCENES-registry-hooks (reset, onLeave, evt. fremtidige onEnter) respekteres.

### Hvad blev ændret
- `scene = 'greenland'; resetGreenland();` → `switchScene(SCENE.GREENLAND);`
- `scheduleHeliIntro('Perfect landing! ...')` bevares som override af resetGreenland's standard "Touchdown..."-besked

### Fundet undervejs (ikke fixet)
- `fullRestartToIntro()` ([index.html:5271](Trumpspillet%20Master%202026/index.html#L5271)) sætter også `scene = SCENE.INTRO` direkte, bypasser `switchScene`. **Latent bug:** Hvis spilleren er i Golf og rammer game-over → Shift+R, kalder fullRestartToIntro → scene = INTRO direkte. `SCENES[SCENE.GOLF].onLeave` (som kalder `golfUnbindInput`) kører IKKE, så golf-pointer-handlers forbliver bundet.
- Ikke godkendt i denne scope — kan tages som separat fix hvis det viser sig at give user-observed bugs

---

## Seneste ændringer (2026-04-26) — Banner-style unification (P6)

Konservativ udgave — standardisér Golf-canvas-banners til samme palette som HTML HUD-pills (`rgba(0,0,0,0.7)` baggrund).

### Ændringer (4 stk i SCENE_GOLF draw)
- **Titel-banner** ([4644](Trumpspillet%20Master%202026/index.html#L4644)): `#6b3fb5` (lilla) → `rgba(0,0,0,0.7)` — matcher HUD pills
- **Score-panel** ([4658](Trumpspillet%20Master%202026/index.html#L4658)): `rgba(0,0,0,0.55)` → `rgba(0,0,0,0.7)`
- **Charge-bar** ([4621](Trumpspillet%20Master%202026/index.html#L4621)): `rgba(0,0,0,0.6)` → `rgba(0,0,0,0.7)`
- **Banner ("Par!")** ([4691](Trumpspillet%20Master%202026/index.html#L4691)): `rgba(0,0,0,0.75)` → `rgba(0,0,0,0.7)`

### Resultat
- Alle Golf-banners deler nu identisk dark-mode look
- Gold-border bevares kun på "Par!" og charge-bar (celebrate/active accents — funktionel forskel)
- Titlen står stadig ud via fontstørrelse + position, ikke længere via afvigende farve

### Hvad blev IKKE rørt
- HTML HUD-chips (uden baggrund — bevidst light look)
- Speech-bubbles (per-speaker palet — funktionelt korrekt)
- Modal overlays (allerede konsistente)
- "Sleepy Joe is lining up..." waiting box (linje 4677 — uden for godkendt scope)

### Mulig opfølger (ikke gjort)
- 🅱️ `BANNER_STYLES`-konstant + `drawBannerBg()`-helper for centraliseret banner-system

---

## Seneste ændringer (2026-04-26) — Overlay-system centralisering (#7)

Konservativ refactor af deep review's hotspot #7. Ingen omskrivning af eksisterende state/draw/input — kun et centralt abstraktionslag.

### Hvad blev tilføjet ([index.html:1186–1217](Trumpspillet%20Master%202026/index.html#L1186-L1217))
- **Dokumentationsblok** der lister alle 4 overlays (choice, info, flag, continue) + deres open/close/draw/input-ansvar
- **`anyOverlayActive()`** helper — returnerer true hvis ét af de 3 modale overlays (choice/info/flag) er aktivt. Bruges til at gate mouse/movement input. Excluderer `continueOverlay` (den har eget GS.over-gating)
- **`closeAllOverlays()`** helper — lukker alle 4 overlays. Kalder kun `closeX()` hvis overlay'et er aktivt, så monkey-patched cleanups (fx `closeInfoCard`'s nobel-drop trigger) ikke fyrer på no-op close

### Hvor det erstatter eksisterende kode
- `mousedown` interact-gate ([7819](Trumpspillet%20Master%202026/index.html#L7819)): `!choiceOverlay && !infoCard && !flagOverlay` → `!anyOverlayActive()`
- Manual movement-gate ([11552](Trumpspillet%20Master%202026/index.html#L11552)): samme erstatning
- `doJump()` ([8016–8023](Trumpspillet%20Master%202026/index.html#L8016-L8023)): tre sequential `if (X) return;` collapset til ét `if (anyOverlayActive()) return;`

### Latent bug lukket
- `fullRestartRun()` ([5244](Trumpspillet%20Master%202026/index.html#L5244)) kalder nu `closeAllOverlays()` som første handling
- Forhindrer at en åben choice/info/flag/continue-overlay bløder ind i en hard restart

### Hvad der IKKE blev gjort
- Ingen samlet `Overlay`-manager der absorberer state — vurderet for risikabelt i bug-tæt kode
- Ingen ændring af keydown-routing-blokken (1245-1266) — overlays har forskellig per-type logik
- Ingen ændring af draw-funktioner eller open/close-funktioner

---

## Seneste ændringer (2026-04-26) — OVAL_GUESTS → JSON

Følger projekt-standarden fra global CLAUDE.md ("redaktionelt indhold må ikke spredes tilfældigt rundt i runtime-koden"). Hotspot #10 fra deep review.

### Hvad blev gjort
- **Tilføjet `OVAL`-blok i game-text-data JSON** ([index.html:315–410](Trumpspillet%20Master%202026/index.html#L315-L410)): `OVAL.GUESTS` (6 gæster) + `OVAL.BONUS` (spørgsmål + duration)
- **Loader-opdatering** ([index.html:586–588](Trumpspillet%20Master%202026/index.html#L586-L588)): tilføjet `OVAL_DATA = textData.OVAL || {}` + destructured `OVAL_GUESTS` / `OVAL_BONUS` med fallbacks
- **Fjernet inline-definition** fra SCENE_OVAL (~80 linjer) — erstattet med 1-linjes kommentar der peger på JSON-blokken
- 7 udvikler-kommentarer (`// Trick: …` på `correctAnswer`-felter) droppet — de var kun til udviklere, påvirkede ikke runtime
- Brug-kode (`OVAL_GUESTS[guestIdx]`, `OVAL_BONUS.question`, etc.) urørt — variabel-navnene er identiske

### Hvad det betyder for redaktionen
- Du kan nu opdatere gæste-spørgsmål, svar-tekster, breaking-news-overskrifter direkte i JSON-blokken uden at røre kode-flow
- Ny gæste-tilføjelse = nyt objekt i `OVAL.GUESTS`-array (samme felter som de eksisterende)
- JSON valideret via Python parse: 6 gæster + BONUS læses korrekt

---

## Seneste ændringer (2026-04-25) — UI deep review + cleanup

Fokus: ikoner overlappede, talebobler/skilte gennemgået for clean look og overblik. UI-deep-review afdækkede 7 problemer (P1–P7) — 5 fixet i denne session.

### Top-right ikon-stak (P1) — fixet
- `.medal-display` flyttet fra `top:8/right:8` til `top:12/right:16` (matcher HUD's højre-kant)
- `.hud` flyttet fra `top:24` til `top:48` (giver plads til medal-display)
- Resultat: ingen overlap mellem medaljer og første HUD-chip (Lives)

### Duplikat gold-chip (P2) — fjernet
- `renderHUD` viste gold-tællingen TO gange: `addGoldBarChip` (med ikon + "X/Y") + `addChip('💰 ' + count)`
- Fjernet duplikat-chippen — den med guldbar-ikonet beholdes (har totalcounter)

### Golf-titel-banner (P3) — flyttet ned
- Lilla titel-banner i Golf flyttet fra canvas `y=28` til `y=64` — kollidererede med `.areas`-pills (HTML overlay top:12)
- Score-info-panel til venstre uændret (statsX=24, statsY=58)

### Epstein top-stripe (P4) — `.areas` skjules
- I Epstein scenen konkurrerede 4 elementer om toppen (medals + .ui flyttet hertil + .areas pills + HUD)
- Tilføjet til `switchScene` (~linje 6699): når `uiPlacement === 'epsteinTop'`, sættes `areasEl.style.display = 'none'`
- Andre scener: areas vises som før

### Inventory buffer (P7)
- `.inv` `top:200px` → `top:240px` — defensivt buffer mod HUD-chip-overflow ved mange aktive boost-chips

### Lars-bubble overlap (user-observed bugfix)
- I Greenland-Lars-dialogen overlappede Trump's og Lars's talebobler
- `renderNpcBubble(GL.mette, ...)` ([index.html:11700](Trumpspillet%20Master%202026/index.html#L11700)) får nu `yAdjust = -70*CFG.SCALE`
- Lars's boble løftes 70 game-pixels op → garanterer at den altid sidder over Trump's

### Udskudt til senere
- **P5** Golf-bubble auto-stack — ikke nødvendigt i praksis (Trump og Biden er spatialt adskilt; `golfBubble` kaldes kun fra ét sted med Biden-position)
- **P6** Unification af 8 forskellige skilt-/banner-stilarter — større designopgave, kan tages som dedikeret session

---

## Seneste ændringer (2026-04-24)

**Filnavn:** `Trumpspillet 2026.html` → `index.html` (for Cloudflare-publicering)

### Deep review gennemført
- Leveret efter brief i `claude_review_brief_trumpspillet_2026_anchor_version.md`
- Executive summary, anchor-based system map, scene map, hotspots, reviewplan
- Identificerede top 12 risici; prioriteret liste med 5 cleanup-opgaver (alle udført i denne session)

### User-observed bugfixes (Golf + fight)
- **Parkeret heli DPR-fix** (~linje 4120 i golfDraw): `setTransform(1,…)` → `setTransform(DPR,…)` — fixede "heli jumper hen i baggrunden" efter landing
- **START HER hit-box** (i golfUpdate approach-branch): cirkel-trigger (radius 26) → AABB-trigger (48×48 halv-bredde) — intro starter nu når Trumps fødder krydser den synlige boks
- **Aim-preview i charging-state**: preview-blok udvidet fra `'aim'` til `'aim' || 'charging'` — stiplet linje vises mens man holder E (længde skalerer med charge.power)
- **Golf Esc = walk til heli + heli-menu**:
  - Ny `GOLF.state = 'leaving'` introduceret (erstatter instant-teleport)
  - golfHandleKeyDown Esc-branch: GOLF.approach fra playerStand, leaveTarget fra parkedHeli (med screen→world coord-konvertering)
  - golfUpdate håndterer 'leaving' (180 px/s walk → exitGolf ved afstand < 14)
  - Ny helper `openHeliTravelMenu()` — samme menu som Greenland's heli-interact
  - Draw-blokke opdateret: `(state === 'approach' || state === 'leaving')` for både Trump-pose og kølle

### Prioriteret oprydning (alle 5 punkter fra deep review)
1. **`r`-reset fall-through** (linje 1227): Kremlin/Epstein kaldte fejlagtigt `resetOval()`. Nu eksplicit else-if for alle fire scener.
2. **Scene-strings → SCENE-constants** (63 steder): `switchScene('greenland')` → `switchScene(SCENE.GREENLAND)` osv. for alle scene-sammenligninger. Forebygger tavse brud ved fremtidig omdøbning. Data-nøgler (fx `addSafe('greenland',...)`, `GS.cleared.greenland`) rørt IKKE.
3. **Fight-bridge oprydning**:
   - Scene-guard `if (scene !== SCENE.GREENLAND) return;` i bridge keydown/keyup
   - Debug-telemetri (6 counters + _debug fields) gatet bag `window.DEBUG_FIGHT_STATE`
   - Misvisende "probe (debug)"-kommentar opdateret (probe er load-bearing for __fightKeysHeld)
4. **Golf state-dokumentation + dead code**:
   - 40-linjers kommentarblok i top af SCENE_GOLF (state-maskine + flags + entry/exit points)
   - `GOLF.introShown` fjernet (3 skrivninger, 0 læsninger)
   - `GOLF.arrival` fjernet (3 skrivninger som altid `{active:false}`, 0 læsninger — `state === 'arrival'` gør arbejdet)
5. **FightEngine.stop() symmetrisk**:
   - Rydder nu `player.walking`/`walkCycle`, `this.keys.clear()`, `window.__fightKeysHeld.clear()`
   - Dev-invariant bag `DEBUG_FIGHT_STATE`: `console.warn` hvis post-stop state lækker

### Bonus — scene-reset taleboble-oprydning
- `player.speakingUntil = 0; player.say = '';` tilføjet i resetGreenland, resetOval, resetKremlin, resetEpstein
- Forhindrer gamle talebobler hænger ved efter 'r'-reset / scene-skift

### Yderligere hotspot-oprydning (fra deep review's top 12)
- **#6 Rename** `resetStateDefaults` → `resetGreenlandRunState` — funktionen rører næsten kun GL.state + shopTutorialShown; gammelt navn var vildledende. 3 forekomster (definition + 2 kaldesteder).
- **#9 Fjern dublet exitGolf cleanup** (~linje 1867): `golfUnbindInput()` blev kaldt direkte og igen via `SCENES[SCENE.GOLF].onLeave`. Fjernet direkte kald + tilføjet kommentar der forklarer at onLeave håndterer det.
- **#11 Nulstil `GS.cleared` + `_safe`-sæt ved fullRestartRun** (~linje 5132): `addSafe()` akkumulerede cleared-tellere + `_safe` sets på tværs af restart-runs. Nu nulstillet eksplicit i fullRestartRun.

---

## Tidligere ændringer
- **Scene routing refactor (minimal):**
  - SCENE constants + SCENES registry indsat ved `scene` state
  - `switchScene(to)` bruger nu SCENES hooks (reset/onLeave/uiPlacement)
  - Heli‑choice handlers bruger `SCENE.*` i stedet for strings (kun i heli‑menus)
- **Golf tutorial overlay (nyt):**
  - `GOLF.tutorial` state tilføjet (active/dismissReady)
  - Tutorial aktiveres i `golfStartCourse()` + delay til dismiss (300ms)
  - Tutorial overlay tegnes før intro (always‑win)
  - Input blokeres/dismisses via `golfHandleKeyDown`
- **Full restart helpers (centraliseret):**
  - `fullRestartRun()` + `fullRestartToIntro()` tilføjet i reset‑sektionen
  - Shift+R (game over) kalder nu `fullRestartToIntro(...)`
  - Shift+R (ingame) kalder nu `fullRestartRun(...)`
- **Patch C — golf via SCENES:**
  - `switchScene(to, params)` kalder nu `def.enter(params)` efter reset
  - `SCENES[SCENE.GOLF].enter` kalder `golfEnter(entry, opts)`
  - `startGolf(...)` wrapper → `switchScene(SCENE.GOLF, {entry, opts})`
  - `golfEnter(...)` indeholder tidligere startGolf‑krop (scene‑assign fjernet)
- **Golf tutorial overlay (bugfix):**
  - `GOLF.tutorialShown` nulstilles i `golfStartCourse()`
  - Overlay blokke bruger igen `W/H` (fjernet canvas resize) for at undgå DPR/transform-reset
- **Golf tutorial timing (flow):**
  - Tutorial aktiveres først når Trump rammer start‑området (approach → intro)
  - StartCourse sætter tutorial inactive + flytter dismiss‑delay til approach‑trigger
- **Golf tutorial centering (DPR):**
  - Overlay‑blokke bruger `ctx.setTransform(DPR,0,0,DPR,0,0)` + `canvas.width/DPR` til center
- **Golf Biden offsets + par:**
  - `GOLF_HOLE_CONFIG` med bidenDx/bidenDy + par override (hole 2 = 5)
  - Biden draw/bubbles bruger per‑hole offsets + tail peger mod hoved (via golfBubble tailTarget)
- **Greenland Lars‑dialog → fight trigger (placeholder):**
  - `startGreenlandLarsDialogue` + `finishGreenlandLarsDialogue` med onComplete callback
  - `startGreenlandFight` placeholder (idempotent; sætter flag + TODO‑caption)
  - Lars‑dialog sætter onComplete og kalder finish på sidste linje
- **Fight overlay mode (debug):**
  - `FightEngine` object med start/stop/update/draw + key handlers
  - Keydown/keyup routes til FightEngine når aktiv
  - Freeze normal movement når fight er aktiv
  - Fight overlay tegnes og returnerer før entryHeli/missile overlays
- **Fight overlay input tweak:**
  - Q‑stop accepterer nu både `key==='q'` og `code==='KeyQ'`
- **Fight overlay input capture:**
  - FightEngine tilføjer/remover keydown/keyup listeners i capture‑fasen under start/stop
- **Fight overlay movement fix:**
  - Nulstiller walkTween og walkCycle ved fight start
  - Spring stepWalkTween over når FightEngine er aktiv (forhindrer auto‑walk override)
- **Fight overlay movement fix (entry heli):**
  - Deaktiverer entryHeli ved fight start så entry‑heli ikke overskriver player.x/y
- **Fight input repeat filter removed:**
  - Tillader keydown repeats i fight input bridge (for at undgå fastlåst movement)
- **Fight debug overlay:**
  - `window.DEBUG_FIGHT_STATE = true` viser dx/dy, position og aktive keys på fight overlay
  - Viser også seneste keydown (last: k/code)
  - Viser også fokusstatus (document.hasFocus + activeElement tag)
  - Viser også `gkeys` (global keys-set) som fallback
  - Viser også keydown counts (bridge/any) + lastAny
  - Viser også bridgeSeen + activeLast (om bridge handleren kører + om FE.active var true)
  - Viser også keydown‑tid FE.active / overlayActive (keyFE/keyOverlay)
- **Fight key hold fallback:**
  - Global keydown/keyup probe vedligeholder `window.__fightKeysHeld`, som fight‑update kan bruge til movement
- **Fight overlay key passthrough:**
  - Under fight overlay tilføjes keys også til global `keys` set i keydown‑probe (og fjernes på keyup)
- **FightEngine instance unification:**
  - Main loop + input bridge bruger `window.FightEngine` som kilde, for at undgå multiple instances
- **Fix intro crash:**
  - Fjernet dobbelt `const FE` i `step()` for at undgå SyntaxError
- **Fight bridge fallback:**
  - Input‑bridge bruger `window.FightEngine || FightEngine` for at undgå mismatch mellem instance og bridge
- **Fight bridge auto‑activate:**
  - Hvis `GL.state.larsFightRequested` er true, tvinges `FE.active = true` i bridge‑handleren
- **Fight overlay active flag:**
  - `window.__fightOverlayActive` sættes i FightEngine.start/stop og bruges i bridge‑routing
- **Oval Office "Presidential Decisions" mini-game** - komplet redesign:
  - OVAL_GUESTS data med 3 gæster: ~linje 4133-4171 (journalist, MAGA supporter, scientist)
  - resetOval() med 3 knapper (JA/NEJ/SURPRISE): ~linje 4173-4237
  - updateOvalDecisions() auto-walk + gæste-logik: ~linje 4249-4342
  - startNextGuest() + resumeCurrentGuest(): ~linje 4344-4372
  - handleOvalAnswer() med approval rating + breaking news: ~linje 4374-4404
  - drawOvalGuest() i Trump-stil med gående ben: ~linje 7384-7477
  - drawOvalWallScreen() med Breaking News + MAGA Dashboard: ~linje 7253-7378
- MAGA supporter kommer altid sidst (guestOrder): ~linje 4233
- Selvbruner-check: Uden tan → session pauses, Trump må købe selvbruner og vende tilbage
- Surprise-knap trigger ALTID atombombe: ~linje 5303-5312

## Vigtige lokationer
- Scene anchors: SCENE_GREENLAND (4062-4129), SCENE_OVAL (4131-4404), SCENE_KREMLIN (4406-4423), SCENE_EPSTEIN (4425+), SCENE_GOLF (2331-4033)
- OV state objekt: ~linje 2273-2296
- Oval render loop: ~linje 8680-8720
- Knap-interaktion oval: ~linje 5294-5328
- drawOvalDesk(): ~linje 7318-7382
- Hint-tegning: ~7930
- Render greenland: ~8522
- Interaktionssystem: ~5016
- drawMette(): ~6500
- drawTrumpFallback(): ~6507

## Fil
/Users/kristian.jensen/Documents/CODE/Masterversioner/Trumpspillet Master 2026/index.html
