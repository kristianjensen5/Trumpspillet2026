# Windsurf Workflow – Mini-TOC & Anchors

Dette dokument beskriver den aftalte arbejdsform for projektet **Trumpspillet 2026**, som er et stort single-file HTML/JS-spil (~1 MB). Formålet er at kunne arbejde effektivt med AI (fx Claude i Windsurf) uden at ramme kontekstgrænser.

---

## 1. Mini-TOC (projektkort)

Der er indsat en **Mini-TOC** øverst i filen i et almindeligt `<script>`-tag (ikke `application/json`).

Mini-TOC’en er **kun dokumentation** og ændrer ingen runtime-adfærd.

Den giver et overblik over:
- CORE / ENGINE / INPUT / UI
- Alle scener i spillet:
  - `SCENE_INTRO`
  - `SCENE_GREENLAND`
  - `SCENE_OVAL`
  - `SCENE_KREMLIN`
  - `SCENE_EPSTEIN` (document mini-game)
  - `SCENE_GOLF` (Mar-a-Lago Mini-Putt)

Mini-TOC’en bruges aktivt til at vælge **hvilket kode-anker** der arbejdes i.

---

## 2. Anchors (kommentar-baserede kodegrænser)

Koden er opdelt med **anchors** i form af kommentarer, som markerer sikre arbejdsområder.

### Globalt input-anker

```js
// INPUT_GLOBAL_KEYDOWN
addEventListener('keydown', ...)
...
// END INPUT_GLOBAL_KEYDOWN
```

Dette anker bruges til alle ændringer i tastatur-logik, input-routing og debug-keys.

### Scene-ankre

Hver scene har et start/slut-anker:

```js
// SCENE_GREENLAND (START)
...
// SCENE_GREENLAND (END)
```

Samme mønster bruges for:
- `SCENE_OVAL`
- `SCENE_KREMLIN`
- `SCENE_EPSTEIN`
- `SCENE_GOLF`

Et scene-anker indeholder:
- reset-funktion(er)
- scene-specifik state og helper-funktioner

Det indeholder **ikke** det globale update/render-loop.

---

## 3. Arkitektur-princip (vigtigt)

Scenerne er **ikke samlet ét sted** i koden.

Eksempel:
- `resetGreenland()` ligger under *Scene Reset Functions*
- Tegning og opdatering sker i et fælles game loop, gated af:

```js
if (scene === 'greenland') { ... }
```

Derfor:
- Scene-ankre placeres omkring reset + scene-logik
- Scene-specifik UI/hints implementeres som helper-funktioner, der kaldes betinget på `scene`

Dette er forventet og korrekt for projektet.

---

## 4. Workflow-regel (primær metode)

**Dette er det anbefalede workflow – kræver ingen kode-viden:**

1. **Beskriv opgaven** i naturligt sprog (fx "Jeg vil have Trump til at sige noget sjovt når han taber i golf")
2. **AI finder linjerne** – søger i koden og identificerer præcis hvor ændringen skal ske
3. **AI viser kontekst** – relevante linjenumre og kode-uddrag præsenteres
4. **AI laver ændringen** – kun de nødvendige linjer rettes
5. **Ingen ændringer uden for scope** – resten af koden forbliver urørt

Dette workflow sparer tokens, fordi kun relevante linjer læses og ændres.

---

## 5. Alternativt workflow (anchor copy/paste)

**Hvis du foretrækker at arbejde med anchors manuelt:**

1. Brug Mini-TOC til at vælge korrekt anker
2. Kopiér **kun** koden mellem `START` og `END` for dette anker
3. Indsæt kun dette udsnit i chatten
4. Arbejd udelukkende inden for ankeret
5. Returnér hele anker-blokken i fuld længde
6. Ingen refactors eller ændringer uden for ankeret

Eksempel:

```js
// SCENE_GOLF (START)
...
// SCENE_GOLF (END)
```

**Bemærk:** Dette workflow kræver at du selv finder og kopierer kode, og bruger flere tokens end den primære metode.

---

## 6. Formålet med Mini-TOC og Anchors

Denne struktur er indført for at:

- Reducere token-forbrug drastisk
- Gøre AI-ændringer sikre og isolerede
- Muliggøre scene-for-scene arbejde
- Undgå utilsigtede sideeffekter
- Gøre samarbejde med AI deterministisk ("ret kun dette område")

**Kort sagt:**
- Mini-TOC = kortet
- Anchors = grænserne
- Workflow = kopiér kun det relevante anker ind i chatten

---

## 7. Seneste session – Status & Linjereferencer

**Dato:** 2026-01-27

#### Stor opdatering: Medaljer, Golf-refactor, Lars Løkke

**Generelt:**
- ✅ Medaljsystem implementeret (5 medaljer, én per scene)
- ✅ Medaljer gemmes i localStorage og vises i øverste højre hjørne
- ✅ Medalje-tildeling ved scene-completion med fanfare

**Grønland (SCENE_GREENLAND):**
- ✅ Dynamit E-tast fix (handled = true)
- ✅ Forbedret mine-grafik (træramme, minekart, hakke, glødende mineraler)
- ✅ Numre i shop/helikopter-menu (1. 2. 3. format)
- ✅ Flag-plantning ved mine efter Mette/Lars er væk
- ✅ **Lars Løkke erstatter Mette** - nyt udseende (jakkesæt, briller, gråt hår) + ny dialog
- ✅ Løkke-dialog: 3 runder → auto-hook til minigame (`startGreenlandLarsMinigame()`)

**Oval Office (SCENE_OVAL):**
- ✅ Sværere quiz-spørgsmål (blandede yes/no svar, trick questions)
- ✅ MAGA approval starter på 0%, +33% per korrekt svar
- ✅ Større breaking news TV (200x280 px)

**Epstein Files (SCENE_EPSTEIN):**
- ✅ Navne starter under highlight bar (scrollY = -120)
- ✅ Højere scroll-hastighed ved start (1.8 base i stedet for 1.2)

**Golf (SCENE_GOLF):**
- ✅ Pulserende guld startplads-markør med animeret kant og pil
- ✅ **Biden står ved hullet** med slag-tal i boble (ingen gennemspil)
- ✅ Rails på bane 3 skaleres nu korrekt til banens dimensioner

### Vigtige nye/ændrede funktioner:
| Funktion / Sted | Linje (ca.) | Beskrivelse |
|-----------------|-------------|-------------|
| `GS.medals` | 1320 | Medalje-tracking objekt |
| `MEDAL_INFO` | 1330 | Medalje-definitioner med ikoner |
| `awardMedal()` | 1340 | Tildeler medalje + fanfare |
| `saveMedals()` / `loadMedals()` | 1350-1365 | localStorage persistence |
| `updateMedalDisplay()` | 1370 | Opdaterer UI |
| `#medal-display` | 440 | HTML element til medaljer |
| `drawMette()` → Lars Løkke | 7777 | Nyt karakterudseende |
| `METTE_DIPLO` | 319 | Nye dialog-linjer til Lars Løkke |
| `startGreenlandLarsMinigame()` | 5996 | Hook til minigame efter 3 dialogrunder |
| `GL.state.larsDialogRounds` | 4444 | Tæller dialogrunder (max 3) |
| Approach marker | 3742-3766 | Pulserende guld startplads |
| Biden ved hul | 2831-2860 | Biden starter færdig ved cup |
| `golfEnsureOverlayRails()` | 3291 | Rails-skalering til bounds |
| Epstein scrollSpeed | 4900 | Øget base fra 1.2 til 1.8 |

### Medalje-betingelser:
| Medalje | Scene | Trigger |
|---------|-------|---------|
| 🇬🇱 Grønlandsflag | Greenland | `GL.mette.blownUp = true` |
| 📰 Pulitzer | Oval Office | `approvalRating >= 100` |
| 🏅 Nobel | Kremlin | `addSafe('kremlin', 'putin-handshake-win')` |
| 🎨 Art Diploma | Epstein | `endEpsteinGame('success')` |
| 🏆 FIFA Trophy | Golf | Player score < Biden score |

### Næste opgaver (ikke startet):
- ⏳ Grønland kampspil med healthbars

---

**Dato:** 2026-01-20

#### Kritiske bugfixes + Epstein redaction
- ✅ Tug-of-war registrerer nu F+K i fase 2 (input gate rettet)
- ✅ Epstein: birthday drawing default til lokal PNG + auto hit‑zone scan
- ✅ Epstein: final redaction = helt sort (ingen suit-alignment)
- ✅ Epstein: E-redaction kun når paper holdt ved tegningen (ingen auto-trigger)
- ✅ Dynamit kan plantes uden at stå ved objekt (E virker igen)

### Vigtige nye/ændrede funktioner (Input/Interaktion/Epstein):
| Funktion / Sted | Linje (ca.) | Beskrivelse |
|-----------------|-------------|-------------|
| Tug keygate | 1184-1185 | F+K accepteres i tug-of-war |
| `estimateBirthdayArtHitRel()` | 618-644 | Auto-detekter hit‑zone i drawing.png |
| Birthday art default | 809-812 | Sætter `drawing.png` som standard |
| Image load hook | 725-731 | Cacher auto hit‑zone rel |
| `updateEpsteinGame()` | 4884-4885 | PaperH defineret (scroll fix) |
| `drawBirthdayArt()` | 5266-5271 | Redaction = sort flade |
| `triggerFinalArtRedact()` / `tryRedactFinalArt()` | 5657-5676 | E-redaction gated på `atArtHold` |
| `interact()` | 5867-5884 | Dynamit plant uden objekt + syntaxfix |

---

**Dato:** 2026-01-19

#### Vælg level UI (choice overlay)
- ✅ Nyt kompakt level-menu overlay med farvede bokse + highlight
- ✅ Tastatur: pil op/ned + Enter/Space + numeriske valg
- ✅ Mouse hover highlight + venstreklik bekræfter (box eller aktuelt valg)

### Vigtige nye/ændrede funktioner (Choice overlay):
| Funktion / Sted | Linje (ca.) | Beskrivelse |
|-----------------|-------------|-------------|
| `CHOICE_BOX_COLORS` | 6662 | Farvepalette til level-bokse |
| `parseChoiceText()` | 6663 | Parser header + valgmuligheder |
| `layoutChoiceOverlay()` | 6677 | Beregner box-layout + hover |
| `openChoice()` | 6716 | Init af choice overlay state |
| Choice input i `INPUT_GLOBAL_KEYDOWN` | 1119-1134 | Pil op/ned + Enter/Space |
| Choice draw | 10623 | Farvede bokse + highlight |
| Choice click confirm | 6456-6468 | Mouse click vælger/bekræfter |

---

**Dato:** 2026-01-17 (eftermiddag)

### Færdiggjorte opgaver denne session:

#### Mar-a-Lago Mini-Putt (SCENE_GOLF) – Helikopter & Biden-først
- ✅ Helikopter smooth skalering (ingen jumpcut) – animerer fra 1.0 → 0.5 når Trump går væk
- ✅ Info-kort centreret på skærm (ikke på bane-bounds)
- ✅ Biden slår først med fast 3 slag per bane – spilleren ser målet før sin tur

### Vigtige nye/ændrede funktioner (Golf):
| Funktion / Sted | Linje (ca.) | Beskrivelse |
|-----------------|-------------|-------------|
| `GOLF_HELI_SCALE` | 2424 | Helikopter-skala ved landing og parkering |
| `golfStartArrival()` | 3286 | Starter heli-landing i golf |
| Helikopter tegning | 3675-3680 | Tegner parkeret heli med skala |
| Info-kort centrering | 3920-3926 | Ændret til skærm-center (W/2, H/2) |
| `GOLF.biden.parTarget` | 2800 | Par-mål for Biden (nu hul-par) |
| Intro → Biden først | 3967-3971 | Efter intro starter Biden med turn='biden' |
| Biden fortsætter | 3575-3580 | Biden slår alle slag før spiller får tur |
| Spiller efter slag | 3518-3521 | Skifter ikke længere til Biden efter spiller-slag |
| Info-tekst | 3944 | "Sleepy Joe slår først med 3 slag – slå ham!" |

### GOLF state ændringer:
- `GOLF_HELI_SCALE` – Helikopter-skala ved landing/parkering
- `biden.parTarget` – Par-mål pr. hul (sat fra course par)

---

### Tidligere session (2026-01-17 formiddag):

#### Epstein Files (SCENE_EPSTEIN) – Guitar Hero Refactor
- ✅ Guitar Hero-stil redacting – tryk E når navne rammer hit zone
- ✅ Horisontalt hit zone bånd med gylden farve – linje ~4931-4958
- ✅ E-tast indikator til venstre for båndet – linje ~4960-4974
- ✅ Tusser/marker animation ved redacting – linje ~4979-5010
- ✅ `getEpsteinHitZone()` – linje ~5587-5597
- ✅ `isLineInHitZone()` – linje ~5601-5606
- ✅ `handleEpsteinGuitarHero()` – linje ~5610-5695
- ✅ Birthday drawing rettet (korrekt torso-silhuet) – `drawBirthdayArtBase()` linje ~5388-5427
- ✅ Sharpie overdraw animation (Trump tegner jakkesæt på figuren) – `drawSharpieOverdraw()` linje ~5228-5315
- ✅ Sharpie marker animation – `drawSharpieMarker()` linje ~5338-5368
- ✅ Scroll stopper under sharpie-animation – linje ~4870-4876
- ✅ Klik-håndtering forenklet (kun final art klik) – `handleEpsteinClick()` linje ~5698-5731

### Vigtige nye/ændrede funktioner (Epstein):
| Funktion | Linje (ca.) | Beskrivelse |
|----------|-------------|-------------|
| `getEpsteinHitZone()` | 5587 | Beregner hit zone position |
| `isLineInHitZone()` | 5601 | Tjekker om linje er i zonen |
| `handleEpsteinGuitarHero()` | 5610 | Håndterer E-tast redacting |
| `drawBirthdayArt()` | 5217 | Wrapper der kalder base + overdraw |
| `drawSharpieOverdraw()` | 5228 | Animeret jakkesæt-tegning |
| `drawSharpieMarker()` | 5338 | Tegner sharpie-markør under animation |
| `drawBirthdayArtBase()` | 5388 | Tegner torso-silhuet (rettet) |

### EP state tilføjelser:
- `hitZoneY: 0.5` – Relativ position af hit zone (0-1)
- `hitZoneHeight: 60` – Højde i pixels
- `markerAnim` – Aktiv tusser-animation
- `lastRedactTime` – Cooldown for double-hits
- `finalArt.redactStart` – Timestamp for sharpie-animation start

### Seneste session (2026-01-18):

#### Mar-a-Lago Mini-Putt (SCENE_GOLF) – Ankomst, kølle, Biden AI, baner 2-3
- ✅ Ny “approach” fase efter heli-landing: Trump går selv til tee før intro starter
- ✅ “Start her” markør + kølle-pickup ved tee
- ✅ Golfkølle tegnes (sort L-form), Trump og Biden bærer kølle
- ✅ Helikopter skala stabil og parkeret ved faktisk landing
- ✅ Biden AI justeret: par = hul-par, delay før han går efter bolden, starter på alle huller, hole-out fix
- ✅ Bane 2-3: inline Lottie JSON i HTML + PNG overlay for bane 2/3
- ✅ PNG-baserede rails på bane 3 (`railsFromOverlay`) + sort-streg detektion til rails

### Vigtige nye/ændrede funktioner (Golf):
| Funktion / Sted | Linje (ca.) | Beskrivelse |
|-----------------|-------------|-------------|
| `GOLF.approach` / `clubPicked` | 2398-2399 | Ny approach-state + kølle pickup flag |
| `GOLF_HELI_SCALE` | 2424 | Fælles helikopter-skala |
| `golfAfterHeliLanding()` | 3313 | Starter approach og positionerer Trump |
| `golfUpdate()` | 3397 | Approach-walk logic og Biden AI fixes |
| `drawGolfClubBase()` | 4108 | Sort L-formet kølle |
| `drawGolfClubInHand()` | 4137 | Kølle i hånd (Trump/Biden) |
| `golfEnsureLottieData()` | 2705 | Inline Lottie parsing (ingen fetch) |
| `applyLottieToHole()` | 2688 | Respektér `railsFromOverlay` |
| `golfResetHole()` | 2740 | Overlay-rails når ønsket + Biden starter hvert hul |
| `golfIsBrownPixel()` | 3142 | Sort pixels tæller som rails |
| `course-mar-a-lago-2/3` | 11087 | Inline Lottie JSON i HTML |

### Tidligere sessioner (2026-01-16):

#### Oval Office (SCENE_OVAL)
- ✅ Yes-group farver ændret til rød/hvid/blå (MAGA-tema) – linje ~8713-8716
- ✅ MAGA-hatte tilføjet til yes-group – linje ~8739-8744
- ✅ Skilte (YES!/BOO!) ved reaktion – linje ~8758-8764
- ✅ `booChoir()` funktion tilføjet – linje ~8803
- ✅ Gæste-navneskilt gjort større – linje ~8640-8648

#### Kremlin (SCENE_KREMLIN)
- ✅ Laser hastighed øget (4 sek i stedet for 8) – linje ~8840
- ✅ To kameraer i stedet for ét – linje ~4685
- ✅ `updateAllSecurityCameras()` og `drawSingleCamera()` – linje ~8859-8868
- ✅ Tankeboble når Trump er tæt på statuer – linje ~10171-10183
- ✅ Wagner-soldater ved tyveri i laserlys – linje ~9033-9054
- ✅ Guldsko-flugt mekanik – `updateWagnerChase()` (linje ~9072), `drawWagnerSoldiers()` (linje ~9144)

#### Armlægning (tug-of-war)
- ✅ Ukraine-kort forbedret – `drawEnhancedWorldMap()` linje ~7067-7181
- ✅ Armlægnings-grafik – `drawArmWrestling()` linje ~6808-6896
- ✅ Fase-skift: A+D → F+K halvvejs – `handleTugKey()` linje ~6793
- ✅ Nyt overlay med arm-grafik – linje ~10284-10310

### Næste opgaver (ikke startet):
- (Ingen specificeret endnu)

---

*Dette dokument kan uploades direkte i Windsurf og bruges som fast kontekst for Claude.*
