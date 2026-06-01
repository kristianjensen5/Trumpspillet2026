# Plan C Refaktorering: Trumpspillet

## Mål
Omstrukturere `Trumpspillet_2026.html` fra 9275 linjer til en velorganiseret single-file webapp hvor data er adskilt fra logik, så AI-assistenter kan arbejde mere effektivt med specifikke sektioner.

## Princip
- **Behold én HTML-fil** (krævet af CMS-system CUE)
- **Ekstraher data til JSON-blocks** (reducerer "støj" i kodebasen)
- **Organiser kode i tydelige sektioner** (gør det nemt for AI at navigere)
- **Bevar al funktionalitet 100%** (ingen breaking changes)

---

## Fil-struktur (efter refaktorering)

```html
<!DOCTYPE html>
<html lang="da">
<head>
  <meta charset="utf-8" />
  <title>Grønlandssatire – TrumpV2</title>
  
  <!-- ==================== STYLES (uændret) ==================== -->
  <style>
    /* Eksisterende CSS (~130 linjer) */
  </style>
  
  <!-- ==================== DATA DEFINITIONS ==================== -->
  
  <script id="game-config" type="application/json">
    {
      "CFG": { "NON_VIOLENT": false, "SPEED": 210, "INTERACT_DIST": 48, ... },
      "GAME_CONSTANTS": { "CHAR": {...}, "UI": {...}, ... }
    }
  </script>
  
  <script id="sprite-definitions" type="application/json">
    {
      "spriteDefs": { /* alle sprite-definitioner */ },
      "animData": { /* alle animationsdata */ }
    }
  </script>
  
  <script id="level-definitions" type="application/json">
    {
      "levelDefs": { /* alle level specs */ },
      "sceneGraphs": { /* alle scene graphs */ }
    }
  </script>
  
  <script id="character-data" type="application/json">
    {
      "trumpSprite": { /* Trump character data */ },
      "npcData": { /* alle NPC'er */ }
    }
  </script>
  
  <script id="item-definitions" type="application/json">
    {
      "itemDefs": { /* alle item definitioner */ },
      "propCatalog": { /* prop catalog hvis relevant */ }
    }
  </script>
  
  <script id="interaction-data" type="application/json">
    {
      "interactions": { /* NPC interactions */ },
      "puzzles": { /* puzzle logik */ },
      "dialogues": { /* dialogue trees */ }
    }
  </script>
</head>

<body>
  <!-- ==================== HTML MARKUP (uændret) ==================== -->
  <canvas id="game"></canvas>
  <div class="ui collapsed" id="help-card">...</div>
  <div id="hud" class="hud"></div>
  <div id="inv" class="inv"></div>
  <div id="areas2" class="areas"></div>
  <div id="intro" class="intro-screen">...</div>

  <!-- ==================== EXTERNAL DEPENDENCIES ==================== -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/lottie-web/5.12.2/lottie.min.js"></script>

  <!-- ==================== GAME CODE ==================== -->
  <script>
    (() => {
      'use strict';
      
      // ===== LOAD DATA FROM JSON BLOCKS =====
      const loadData = (id) => JSON.parse(document.getElementById(id).textContent);
      
      const { CFG, GAME_CONSTANTS } = loadData('game-config');
      const { spriteDefs, animData } = loadData('sprite-definitions');
      const { levelDefs, sceneGraphs } = loadData('level-definitions');
      const { trumpSprite, npcData } = loadData('character-data');
      const { itemDefs, propCatalog } = loadData('item-definitions');
      const { interactions, puzzles, dialogues } = loadData('interaction-data');
      
      
      // ==================== 1. UTILITY FUNCTIONS ====================
      // Hjælpefunktioner, math utils, collision detection osv.
      
      function lerp(a, b, t) { /* ... */ }
      function clamp(v, min, max) { /* ... */ }
      function rectOverlap(r1, r2) { /* ... */ }
      // ... alle utility functions
      
      
      // ==================== 2. SPRITE & ANIMATION SYSTEM ====================
      // Canvas sprite rendering, animation handling
      
      function drawPixelSprite(ctx, sprite, x, y, options) { /* ... */ }
      function updateAnimation(entity, dt) { /* ... */ }
      // ... sprite system
      
      
      // ==================== 3. PHYSICS & COLLISION ====================
      // Movement, gravity, collision detection, tilemap interaction
      
      function applyGravity(entity, dt) { /* ... */ }
      function checkTileCollisions(entity, tilemap) { /* ... */ }
      function resolveCollision(entity, normal) { /* ... */ }
      // ... physics system
      
      
      // ==================== 4. CAMERA SYSTEM ====================
      // Camera following, bounds, smoothing
      
      const camera = { x: 0, y: 0, targetX: 0, targetY: 0 };
      function updateCamera(target, bounds, dt) { /* ... */ }
      // ... camera logic
      
      
      // ==================== 5. RENDERING SYSTEM ====================
      // Main draw loop, layer rendering, UI rendering
      
      function drawTilemap(ctx, tilemap, camera) { /* ... */ }
      function drawEntities(ctx, entities, camera) { /* ... */ }
      function drawUI(ctx, gameState) { /* ... */ }
      // ... rendering functions
      
      
      // ==================== 6. INPUT HANDLING ====================
      // Keyboard, touch, gamepad input
      
      const keys = {};
      function setupInputHandlers() { /* ... */ }
      function handleInput(gameState, dt) { /* ... */ }
      // ... input system
      
      
      // ==================== 7. GAME STATE MANAGEMENT ====================
      // Level loading, state transitions, save/load
      
      const gameState = {
        currentLevel: null,
        player: null,
        entities: [],
        inventory: [],
        flags: {}
      };
      
      function loadLevel(levelKey) { /* ... */ }
      function saveGameState() { /* ... */ }
      function resetGame() { /* ... */ }
      // ... state management
      
      
      // ==================== 8. INTERACTION SYSTEM ====================
      // NPC interactions, item pickup, dialogue, puzzles
      
      function checkInteractables(player, entities) { /* ... */ }
      function triggerInteraction(entity, player) { /* ... */ }
      function handleDialogue(npc, player) { /* ... */ }
      // ... interaction logic
      
      
      // ==================== 9. UI SYSTEM ====================
      // HUD, inventory, dialogue bubbles, menus
      
      function updateHUD(gameState) { /* ... */ }
      function showInventory() { /* ... */ }
      function showDialogueBubble(text, entity) { /* ... */ }
      // ... UI functions
      
      
      // ==================== 10. AUDIO SYSTEM ====================
      // Sound effects, music (hvis relevant)
      
      function playSound(soundKey) { /* ... */ }
      // ... audio logic
      
      
      // ==================== 11. GAME LOOP ====================
      // Main update and render loop
      
      let lastTime = 0;
      function gameLoop(currentTime) {
        const dt = Math.min((currentTime - lastTime) / 1000, 0.1);
        lastTime = currentTime;
        
        // Update
        handleInput(gameState, dt);
        updatePhysics(gameState, dt);
        updateCamera(gameState.player, gameState.currentLevel.bounds, dt);
        updateAnimations(gameState.entities, dt);
        
        // Render
        clearCanvas();
        drawTilemap(ctx, gameState.currentLevel.tilemap, camera);
        drawEntities(ctx, gameState.entities, camera);
        drawUI(ctx, gameState);
        
        requestAnimationFrame(gameLoop);
      }
      
      
      // ==================== 12. INITIALIZATION ====================
      // Setup canvas, load initial level, start game loop
      
      function init() {
        const canvas = document.getElementById('game');
        const ctx = canvas.getContext('2d');
        
        setupCanvas(canvas);
        setupInputHandlers();
        loadLevel('greenland');
        
        // Start intro screen
        showIntroScreen(() => {
          requestAnimationFrame(gameLoop);
        });
      }
      
      // Start game when DOM is ready
      if (document.readyState === 'loading') {
        document.addEventListener('DOMContentLoaded', init);
      } else {
        init();
      }
      
    })();
  </script>
  
  <!-- ==================== DEVELOPER TOOLS (optional) ==================== -->
  <!-- Prop importer og andre dev tools kan forblive i bunden -->
  
</body>
</html>
```

---

## Detaljeret refaktorerings-instruktion

### FASE 1: Ekstraher data til JSON-blocks

**Opgave 1.1: Ekstraher konfiguration**
```javascript
// Find linje ~167-177 (CFG object)
// Find linje ~179-450 (GAME_CONSTANTS object)
// Flyt begge til <script id="game-config" type="application/json">
```

**Opgave 1.2: Ekstraher sprite-definitioner**
```javascript
// Find ~linje 500-2500 (spriteDefs object)
// Find animData object (hvis separat)
// Flyt til <script id="sprite-definitions" type="application/json">
```

**Opgave 1.3: Ekstraher level-data**
```javascript
// Find levelDefs object (~1000+ linjer)
// Find sceneGraphs object
// Flyt til <script id="level-definitions" type="application/json">
```

**Opgave 1.4: Ekstraher character-data**
```javascript
// Find Trump sprite definition
// Find alle NPC-definitioner
// Flyt til <script id="character-data" type="application/json">
```

**Opgave 1.5: Ekstraher item-definitioner**
```javascript
// Find itemDefs object
// Find eventuelle prop catalogs
// Flyt til <script id="item-definitions" type="application/json">
```

**Opgave 1.6: Ekstraher interaction-data**
```javascript
// Find NPC dialogue data
// Find puzzle logik data
// Flyt til <script id="interaction-data" type="application/json">
```

### FASE 2: Organiser kode i sektioner

**Organiserings-princip:**
- Hver sektion får en tydelig `// ==================== SEKTION NAVN ====================` header
- Indenfor hver sektion: grouped functions der hører sammen
- Rækkefolge: utilities → core systems → game logic → initialization

**Sektioner at oprette:**

1. **UTILITY FUNCTIONS** (~200 linjer)
   - Math utils (lerp, clamp, distance, etc.)
   - Collision detection helpers
   - Array/object helpers

2. **SPRITE & ANIMATION SYSTEM** (~300 linjer)
   - drawPixelSprite
   - Animation update logic
   - Sprite rendering functions

3. **PHYSICS & COLLISION** (~400 linjer)
   - Gravity/velocity
   - Tile collision
   - Entity collision
   - Movement resolution

4. **CAMERA SYSTEM** (~100 linjer)
   - Camera following
   - Bounds checking
   - Camera smoothing

5. **RENDERING SYSTEM** (~400 linjer)
   - Tilemap drawing
   - Entity drawing
   - Layer management
   - UI rendering prep

6. **INPUT HANDLING** (~200 linjer)
   - Keyboard listeners
   - Touch handlers
   - Input state management

7. **GAME STATE MANAGEMENT** (~300 linjer)
   - Level loading
   - State transitions
   - Save/load logic
   - Game flags

8. **INTERACTION SYSTEM** (~400 linjer)
   - Interactable detection
   - NPC dialogues
   - Item pickup
   - Puzzle triggers

9. **UI SYSTEM** (~300 linjer)
   - HUD updates
   - Inventory display
   - Dialogue bubbles
   - Menu rendering

10. **AUDIO SYSTEM** (~50 linjer hvis relevant)
    - Sound playback
    - Music control

11. **GAME LOOP** (~100 linjer)
    - Main update/render loop
    - Delta time handling
    - RAF management

12. **INITIALIZATION** (~100 linjer)
    - Canvas setup
    - Event listeners
    - Initial level load
    - Game start

### FASE 3: Tilføj data-loading i toppen

```javascript
<script>
(() => {
  'use strict';
  
  // ===== LOAD DATA FROM JSON BLOCKS =====
  const loadData = (id) => {
    const el = document.getElementById(id);
    if (!el) {
      console.error(`Missing data block: ${id}`);
      return {};
    }
    try {
      return JSON.parse(el.textContent);
    } catch (err) {
      console.error(`Failed to parse ${id}:`, err);
      return {};
    }
  };
  
  // Load all game data
  const configData = loadData('game-config');
  const CFG = configData.CFG || {};
  const GAME_CONSTANTS = configData.GAME_CONSTANTS || {};
  
  const spriteData = loadData('sprite-definitions');
  const spriteDefs = spriteData.spriteDefs || {};
  const animData = spriteData.animData || {};
  
  const levelData = loadData('level-definitions');
  const levelDefs = levelData.levelDefs || {};
  const sceneGraphs = levelData.sceneGraphs || {};
  
  const charData = loadData('character-data');
  const trumpSprite = charData.trumpSprite || {};
  const npcData = charData.npcData || {};
  
  const itemData = loadData('item-definitions');
  const itemDefs = itemData.itemDefs || {};
  
  const interactionData = loadData('interaction-data');
  const interactions = interactionData.interactions || {};
  const puzzles = interactionData.puzzles || {};
  
  // ... resten af spillet ...
})();
</script>
```

---

## Validerings-checklist

Efter refaktorering, test følgende:

- [ ] Spillet starter korrekt
- [ ] Intro screen vises
- [ ] Trump kan bevæge sig
- [ ] Alle animationer virker
- [ ] Kollisioner fungerer
- [ ] NPC interaktioner virker
- [ ] Inventory vises korrekt
- [ ] Level transitions virker
- [ ] Alle hotkeys fungerer (E, I, B, T, etc.)
- [ ] Touch controls virker (hvis relevant)
- [ ] Ingen console errors

---

## Fordele ved denne struktur

### For AI-assistenter (Windsurf):
- **Kan fokusere på specifikke sektioner**: "Ret kun PHYSICS & COLLISION sektionen"
- **Data er adskilt fra logik**: "Opdater sprite-definitions JSON uden at røre koden"
- **Mindre context per opgave**: Kan ignorere irrelevante sektioner

### For udvikler:
- **Nemmere at finde kode**: Intuitive sektionsnavne
- **Nemmere at debugge**: Isolerede systemer
- **Bevar deployment**: Stadig én fil til CUE

### For performance:
- **Ingen ændring**: Stadig én HTML-fil, ingen ekstra HTTP requests
- **Evt. bedre**: JSON parsing kan være marginalt hurtigere end object literals

---

## Troubleshooting

### Problem: "Missing data block"
- **Årsag**: En JSON-block mangler eller har forkert ID
- **Fix**: Tjek at alle `<script id="...">` tags eksisterer og har korrekte IDs

### Problem: "Failed to parse JSON"
- **Årsag**: Syntaks-fejl i JSON (trailing commas, comments, etc.)
- **Fix**: JSON må ikke have trailing commas, comments, eller undefined values

### Problem: Spillet crasher ved start
- **Årsag**: En variabel findes ikke længere (manglende data-load)
- **Fix**: Tjek at alle loadData() kald matcher de oprindelige variabelnavne

---

## Næste skridt efter refaktorering

Når Plan C er implementeret, kan du nemt:

1. **Arbejde med data separat**: Eksporter JSON-blocks til .json filer under udvikling
2. **AI kan fokusere bedre**: "Ret kun rendering system" giver meget mindre context
3. **Nemmere at udvide**: Tilføj nye levels/items/NPCs ved at redigere JSON
4. **Versioner af data**: Git kan bedre tracke ændringer når data er adskilt

---

## Estimeret reduktion

- **Før**: 9275 linjer i ét script-tag
- **Efter**: 
  - ~3000 linjer data i JSON-blocks (ikke "kode" der skal læses)
  - ~2000-2500 linjer aktiv game code (organiseret i sektioner)
  - ~130 linjer CSS
  - ~100 linjer HTML markup

**Resultat**: AI skal kun parse ~2500 linjer kode ad gangen i stedet for 9000+

---

## Brug denne plan i Windsurf

Copy/paste denne fil til Claude/GPT i Windsurf og sig:

> "Her er Trumpspillet_2026.html. Implementer venligst Plan C refaktorering som beskrevet i REFACTORING_PLAN.md. Start med FASE 1, og spørg mig hvis du er i tvivl om hvor en given data-struktur hører hjemme."

Eller mere specifikt:

> "Start med FASE 1.1: Ekstraher CFG og GAME_CONSTANTS til game-config JSON-block. Behold al funktionalitet."

---

## Yderligere optimeringsmuligheder (efter Plan C)

Når Plan C er implementeret, kan du overveje:

1. **Minify JSON-data**: Reducer whitespace i JSON-blocks (gem ~15%)
2. **Lazy-load levels**: Load kun current level data (kræver mere refaktorering)
3. **Split til hybrid**: Behold main game i HTML, eksternt host store assets (sprites/levels)

Men start med Plan C - det giver 80% af fordelene med 20% af arbejdet.
