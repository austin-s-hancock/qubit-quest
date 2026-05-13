# Qubit Quest Educator Toolkit

**Created by Austin Hancock**

**A guide for educators interested in building browser-based educational games using HTML, CSS, JavaScript, and AI-assisted development.**

------------------------------------------------------------------------

## Table of Contents

1.  [Who This Toolkit Is For](#who-this-toolkit-is-for)
2.  [What Qubit Quest Is](#what-qubit-quest-is)
3.  [Tools and Setup](#tools-and-setup)
4.  [Why a Single HTML File?](#why-a-single-html-file)
5.  [Architecture Overview](#architecture-overview)
6.  [How the Major Systems Work](#how-the-major-systems-work)
    -   [The Bloch Sphere (Three.js)](#the-bloch-sphere-threejs)
    -   [Gate Transforms](#gate-transforms)
    -   [Level Type System](#level-type-system)
    -   [Scoring Model](#scoring-model)
    -   [Mission Map and Navigation](#mission-map-and-navigation)
    -   [KaTeX Math Rendering](#katex-math-rendering)
7.  [Design Decisions and Why They Were Made](#design-decisions-and-why-they-were-made)
8.  [How to Modify the Game](#how-to-modify-the-game)
    -   [Adding a New Level](#adding-a-new-level)
    -   [Adding a New Gate](#adding-a-new-gate)
    -   [Changing the Visual Theme](#changing-the-visual-theme)
    -   [Adding a New Level Type](#adding-a-new-level-type)
9.  [File Organization](#file-organization)
10. [The Role of AI in Development](#the-role-of-ai-in-development)
11. [Lessons Learned](#lessons-learned)
12. [References and Resources](#references-and-resources)

------------------------------------------------------------------------

## Who This Toolkit Is For

This toolkit is written for educators who want to build interactive browser-based educational games and:

-   Have some programming experience (took a class once, wrote some scripts, can read code) but are not professional web developers
-   Want a concrete example of a working game they can study, modify, and learn from
-   Are interested in how game design decisions connect to educational theory (Bloom's Taxonomy, cognitive load, scaffolding)
-   May be curious about using AI tools like Claude as a development resource

You do not need to know quantum physics to follow this toolkit. The quantum content is the *subject matter* of the game; however, the *techniques* for building it such as 3D rendering, game state management, level progression, and scoring apply to any educational topic.

------------------------------------------------------------------------

## What Qubit Quest Is

Qubit Quest is a puzzle game that teaches single-qubit quantum gate operations through direct manipulation of a 3D Bloch Sphere. Players apply quantum gates (X, Y, Z, H, S) to rotate a qubit to a target state, with four distinct level types that progress through Bloom's Taxonomy:

-   **Apply**: navigate to a target with live sphere feedback
-   **Predict**: place a prediction marker before a sequence plays
-   **Deconstruct**: identify gates from start/end states with no sphere feedback
-   **Evaluate**: compare two circuits and reason about correctness

The game has 21 levels across 5 worlds (1 tutorial + 4 mission worlds). Each mission world is defined by a single mechanic: World 1 is all Apply (live sphere feedback), World 2 is all Deconstruct (frozen sphere, build sequence blind), World 3 is all Predict (commit to a prediction before the sequence plays), and World 4 is a capstone mixing all types. The game also features a New Recruit / Returning Engineer choice at the start where new players go through a mandatory guided tutorial with gate introduction cards and auto-expanded concept explanations and returning players can skip directly to the mission map.

The entire game is a single HTML file (approximately 2,300 LoC) that runs in any modern browser with no installation.

------------------------------------------------------------------------

## Tools and Setup

### What You Need

**To play the game:**

Any modern web browser (Chrome, Firefox, Safari, Edge). Open the HTML file and play. An internet connection is required because the game loads three libraries from content delivery networks (CDNs) that serve the library files.

**To edit the game:**

Only a text editor. There is no build step, no compilation, no package manager. You edit the HTML file, save it, and refresh your browser to capture the changes.

### Recommended Text Editors

If you don't already have a preferred editor, here are options from simplest to most powerful:

| Editor | Platform | Best For | Link |
|------------------|------------------|------------------|------------------|
| **Notepad++** | Windows | Quick edits, lightweight | [notepad-plus-plus.org](https://notepad-plus-plus.org/) |
| **TextEdit** | macOS | Quick edits | Built into macOS |
| **VS Code** | All | Full-feature support | [code.visualstudio.com](https://code.visualstudio.com/) |

**VS Code is strongly recommended** if you plan to do more than minor edits. It provides syntax highlighting (colours different parts of the code), error detection, search-and-replace across the file, and a built-in terminal.

### Browser Developer Tools

Every modern browser has built-in developer tools that are invaluable for debugging:

-   **Chrome/Edge:** Press `F12` or `Ctrl+Shift+I` (Windows) / `Cmd+Option+I` (Mac)
-   **Firefox:** Press `F12`
-   **Safari:** Enable in Preferences -> Advanced -> "Show Develop menu", then `Cmd+Option+I`

The **Console** tab shows JavaScript errors, so if something breaks after you edit the file, this is where you'll see what went wrong. The **Elements** tab lets you inspect the HTML structure. The **Network** tab shows whether the CDN libraries loaded successfully.

### External Libraries (loaded via CDN)

The game loads three libraries from the internet when the page opens. You do not need to install these as they are fetched automatically.

| Library | Version | What It Does | CDN URL |
|------------------|------------------|-------------------|------------------|
| **Three.js** | r128 | 3D rendering to draw and animate the Bloch Sphere | `cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js` |
| **KaTeX** | 0.16.9 | Render LaTeX math notation (quantum state vectors, matrices) | `cdn.jsdelivr.net/npm/katex@0.16.9/` |
| **Google Fonts** | -- | Typefaces: Orbitron (headings), Share Tech Mono (code), Exo 2 (body) | `fonts.googleapis.com` |

**If you need the game to work offline**, you can download these library files and change the `<script src="...">` and `<link href="...">` tags to point to local copies instead of CDN URLs.

### Verifying Gate Transforms

**What this is and why you need it:** When you add a new level, you need to confirm that your declared start state actually reaches your declared target state through the gate sequence you intend. You *could* test this by playing the level in the browser, but that only tests one level at a time and requires you to manually apply gates and visually confirm the result. A verification script lets you check ALL levels at once and catch mistakes before a player encounters an unsolvable puzzle.

**What is Node.js?** Node.js lets you run JavaScript outside of a web browser, similar to how you might run a Python script from the command line. You write a `.js` file, then execute it in your computer's terminal application. This is useful because the game's gate transform functions are written in JavaScript, so you can copy them directly into a verification script without translating to another language.

**Setup:**

1. Download and install Node.js from [nodejs.org](https://nodejs.org/) (choose the LTS version)
2. Open your terminal application:
   - **Mac:** Open "Terminal" (find it in Applications > Utilities, or search Spotlight)
   - **Windows:** Open "Command Prompt" (search for "cmd" in the Start menu)
3. Type `node --version` and press Enter. If you see a version number (e.g., `v20.11.0`), it's installed correctly.

**Writing a verification script:**

Create a new file called `verify.js` in any text editor. The gate transform functions should be copied directly from the game file. In `qubit-quest.html`, search for `GATE_TRANSFORMS` -- you'll find an object containing all the gate functions. Copy the ones you need into your script:

``` javascript
const PI = Math.PI;

// These are copied directly from the GATE_TRANSFORMS object in qubit-quest.html.
// In the game file, search for "const GATE_TRANSFORMS" to find them.
const GATE_TRANSFORMS = {
  X: (theta, phi) => {
    const x = Math.sin(theta)*Math.cos(phi);
    const y = Math.sin(theta)*Math.sin(phi);
    const z = Math.cos(theta);
    const nt = Math.acos(Math.max(-1, Math.min(1, x)));
    let np = Math.atan2(-y, -z); if (np < 0) np += 2*PI;
    return { theta: nt, phi: np };
  },
  H: (theta, phi) => {
    const x = Math.sin(theta)*Math.cos(phi);
    const y = Math.sin(theta)*Math.sin(phi);
    const z = Math.cos(theta);
    const nt = Math.acos(Math.max(-1, Math.min(1, x)));
    let np = Math.atan2(y, z); if (np < 0) np += 2*PI;
    return { theta: nt, phi: np };
  },
  Z: (theta, phi) => ({ theta, phi: phi + PI }),
  S: (theta, phi) => ({ theta, phi: phi + PI/2 }),
  // Add Y, T, or any other gates you need to verify
};

// Helper: apply a sequence of gates to a starting state
function applySequence(gates, startTheta, startPhi) {
  let state = { theta: startTheta, phi: startPhi };
  for (const g of gates) {
    state = GATE_TRANSFORMS[g](state.theta, state.phi);
  }
  return state;
}

// Test a level: does H applied to |0> give |+> (theta=PI/2, phi=0)?
const result = applySequence(['H'], 0, 0);
console.log('H from |0>:');
console.log('  theta:', result.theta.toFixed(4), '(expected:', (PI/2).toFixed(4), ')');
console.log('  phi:  ', result.phi.toFixed(4),   '(expected:', (0).toFixed(4), ')');

// Test another: X then H from |0> should give |-> (theta=PI/2, phi=PI)
const result2 = applySequence(['X', 'H'], 0, 0);
console.log('X->H from |0>:');
console.log('  theta:', result2.theta.toFixed(4), '(expected:', (PI/2).toFixed(4), ')');
console.log('  phi:  ', result2.phi.toFixed(4),   '(expected:', (PI).toFixed(4), ')');
```

**Running it:**

1. Save the file as `verify.js` in any folder
2. In your terminal, navigate to that folder. The easiest way:
   - Type `cd ` (with a space after it)
   - Drag the folder from your file browser into the terminal window
   - Press Enter
3. Type `node verify.js` and press Enter
4. You should see the theta and phi values printed. If they match the expected values, your gate transform is correct. If they don't match, your level's target state or gate sequence has an error.

------------------------------------------------------------------------

## Why a Single HTML File?

This is probably the most unusual design decision in the project. Most web applications are built from many files, such as separate `.css` for styling, `.js` for code, image files for graphics, and a build system to package everything together. This approach is standard for professional development, but creates a barrier for educators as you need to understand file relationships, build tools, package managers, and hosting before you can even look at the code.

A single HTML file helps eliminates all of this. The CSS is in a `<style>` block at the top, the JavaScript is in a `<script>` block at the bottom, and the HTML structure is in-between. By opeining one file, you can see everything, and have no build step,no dependencies to install, and no folder structure to navigate. You can email the file to someone and they can play the game immediately.

The consequence of a single HTML file is that it can become quite large. In this case, Qubit Quest is about 2,300 LoC. Despite this, with a good text editor's search function (`Ctrl+F`), you can find any section quickly. Standard coding practice can be followed and code can organized into clearly labelled sections with comment headers:

``` javascript
/* ================================================================
   6. GATE TRANSFORMS
   ================================================================ */
```

For a different educational topic, this same approach works well. You could build a chemistry bonding game, a music theory trainer, or a geometry proof builder as a single HTML file using the same architecture. The Bloch Sphere is specific to quantum computing, but the game loop, level system, scoring model, and UI patterns are all reusable.

------------------------------------------------------------------------

## Architecture Overview

The game strives to have a clear separation between *data* (what the levels contain) and *code* (how the game runs). This means you can change all the content (levels, text, gate sets) without touching the game engine.

### The File Structure (Top to Bottom)

```         
qubit-quest.html
  <head>
    - KaTeX stylesheet (CDN link)
    - KaTeX scripts (CDN links, deferred)
    - <style> -- ALL CSS in one block (~220 lines)
        - Design tokens (CSS variables for colours, fonts)
        - Screen layouts (title, game, complete)
        - Left panel components
        - Right panel / sphere
        - Overlays and modals (including gate card overlay)
        - Mission map
        - Animations (@keyframes)
        - Responsive breakpoints

  <body>
    - Starfield canvas (background decoration)
    - #screen-title     -- Title / start screen (New Recruit / Returning Engineer)
    - #screen-game      -- Main game screen
        - .panel-left   -- Mission brief, gates, controls
        - .panel-right  -- Bloch sphere, HUD, progress
    - #screen-complete  -- World complete screen
    - #overlay-map      -- Mission select overlay
    - #overlay-success  -- Success modal
    - #overlay-fail     -- Fail modal
    - #overlay-confirm-menu -- Menu confirmation
    - #gate-card-overlay -- Gate introduction card (tutorial)

  Three.js script (CDN)

  <script> -- ALL JavaScript in one block (~1,800 lines)
    - Constants, level type enum, world/level data arrays
    - Game state variables (including tutorialCompleted flag)
    - Starfield animation
    - Three.js Bloch Sphere setup
    - Gate transform functions
    - Gate application (Apply/Sandbox modes) with transform labels
    - Predict mode mechanics (with 250-point fail threshold)
    - Deconstruct mode mechanics (with frozen sphere indicator)
    - Evaluate mode mechanics
    - Sandbox mode
    - Solution checking and scoring
    - Level progression and world complete
    - Level loading (with gate cards, frozen badge, hover preview)
    - Concept box toggle
    - Scoring helpers
    - Mission map renderer (with world locking)
    - Gate tooltips
    - Screen navigation (New Recruit / Returning Engineer flow)
    - Gate card, transform label, hover preview functions
    - Toast notifications
```

### Data vs. Code Separation

The most important architectural concept is that levels are data, not code. Each level is a JavaScript object in the `LEVELS` array:

``` javascript
{
  id: 'L1',                           // Unique identifier
  world: 1,                           // Which world this belongs to
  type: LT.APPLY,                     // Level type (Apply/Predict/etc.)
  tag: 'World 1 * L1',               // Display tag in header
  title: 'First Flip',               // Level name
  mission: 'Chamber 1. Start...',    // Mission brief text
  startState: {theta:0, phi:0},       // Starting qubit position
  targetState: {theta:PI/2, phi:PI},  // Target position
  startLabel: '|0>',                 // Human-readable start
  targetLabel: '|->',                // Human-readable target
  availableGates: ['X','H'],          // Which gate buttons appear
  solution: ['X','H'],                // Canonical solution
  successMsg: '...',                  // Shown on success
  concept: '<p>...</p>',              // Elective concept box HTML
}
```

To add a new level, you add an object to this array. You don't need to touch any of the game engine code. The `loadLevel()` function reads the object and sets up everything automatically. This is the most important thing to understand if you want to adapt the game.

**Why does each level define a targetState?** The game engine doesn't compute whether a gate sequence is theoretically correct, but it checks whether the player's current sphere position matches the declared `targetState` within a small angular tolerance. The `targetState` serves double duty: it tells the game where to render the gold target dot on the sphere, AND it's the condition the checking code evaluates against. Without it, the game wouldn't know where the player needs to go or what counts as success. The `solution` field is separate as it's only used for hints and optimal gate counting, not for determining whether the player solved the puzzle.

------------------------------------------------------------------------

## How the Major Systems Work

### The Bloch Sphere (Three.js)

The 3D sphere is rendered using Three.js, a JavaScript library for 3D graphics. If you've never used 3D rendering before, the core concepts are:

-   **Scene**: the 3D world containing all objects
-   **Camera**: the viewpoint the player sees the scene from
-   **Renderer**: draws the scene to a `<canvas>` HTML element
-   **Meshes**: 3D objects made of geometry (shape) + material (appearance)
-   **Animation loop**: a function that runs \~60 times per second to update and redraw

The Bloch Sphere is built from several meshes inside a `Group` (a container that can be rotated as a unit):

```         
sphereGroup
-- Wireframe sphere (semi-transparent, shows the sphere shape)
-- Hit mesh (invisible but raycasted; used for predict click detection)
-- Equator ring (flat ring at the equator plane)
-- Prime meridian ring (vertical ring)
-- X/Y/Z axis lines (coloured: red, green, blue)
-- State labels (|0>, |1>, |+>, |-> as sprite textures)
-- State vector arrow (cyan normally, grey in Deconstruct mode)
-- Target dot (gold, pulsing; shows where the player needs to go)
-- Prediction dot (white; player's prediction marker in Predict mode)
-- Ghost preview dot (dim cyan ring; shows where a gate would land on hover)
```

Additional overlay elements positioned on the sphere canvas:

- **Transform label**: floating text (e.g., "|0> -> |1>") that appears briefly after each gate application in Tutorial and World 1, connecting the button click to the visual change
- **Frozen badge**: "BUILDING CIRCUIT" text shown during Deconstruct mode to communicate that the frozen sphere is intentional
- **Gate introduction cards**: mandatory brief overlay when a gate first appears in the tutorial (New Recruit path only)

**Coordinate mapping:** Three.js uses Y-up coordinates but the Bloch Sphere uses Z-up physics convention. The `blochToCartesian()` function handles the mapping:\
- Bloch X (sin theta cos phi) -> Three.js X\
- Bloch Z (cos theta) -> Three.js Y (up)\
- Bloch Y (sin theta sin phi) -> Three.js Z (depth)

This mapping is critical to the game functionality. If you're building a different 3D visualization, you'll most likely have a similar coordinate convention to manage.

**Drag rotation** is handled by tracking mousedown/mousemove/mouseup events and rotating `sphereGroup` accordingly. A `DRAG_THRESHOLD` (5 pixels) distinguishes drags from clicks which is important for Predict mode where clicks place markers.

### Gate Transforms

Each quantum gate is a pure function that takes `(theta, phi)` and returns new `{theta, phi}`:

``` javascript
const GATE_TRANSFORMS = {
  X: (theta, phi) => { /* 180 degrees rotation around X axis */ },
  Y: (theta, phi) => { /* 180 degrees rotation around Y axis */ },
  Z: (theta, phi) => ({ theta, phi: phi + PI }),  // simple phase flip
  H: (theta, phi) => { /* swaps x and z on Bloch sphere */ },
  S: (theta, phi) => ({ theta, phi: phi + PI/2 }),  // 90 degrees phase
};
```

**If you're adapting this to a different domain**, the gate transforms are where your subject-specific logic lives. For a chemistry game, this might be bond-angle calculations. For a music theory game, this might be interval transpositions. The key pattern is: pure functions that transform state, displayed as buttons the player clicks.

### Level Type System

The game defines five level types as an enum:

``` javascript
const LT = {
  APPLY:       'apply',
  PREDICT:     'predict',
  DECONSTRUCT: 'deconstruct',
  EVALUATE:    'evaluate',
  SANDBOX:     'sandbox',
};
```

Each level's `type` field determines what the `initActionZone()` function builds in the left panel. This is the core pattern for having multiple gameplay modes in one game. A single `loadLevel()` function reads the level data and branches based on type to set up the appropriate UI and mechanics.

Examples of the mechanical differences :

| Type | Sphere | Player Action | Scoring |
|------------------|------------------|-------------------|------------------|
| Apply | Live, updates on every gate | Click gates, watch vector move | 200 base + gate efficiency |
| Predict | Frozen until confirm | Click sphere to place marker | Angular distance (400/250/100/50) |
| Deconstruct | Frozen until submit | Build gate sequence blind | 400 correct / 0 wrong |
| Evaluate | Shows playback after submit | Answer 2 multiple choice Qs | 200 per correct answer |
| Sandbox | Live | Free exploration | No scoring |

### Scoring Model

Scoring is intentionally tied to real quantum computing concepts:

-   **Apply:** Gate efficiency bonus maps to the real engineering concept that every gate adds noise on actual quantum hardware. Fewer gates = better score = better real-world circuit.
-   **Predict:** Partial credit based on angular distance encourages reasoning even when wrong. Getting close means the player's mental model was mostly right.
-   **Deconstruct:** Binary (correct/wrong) because circuit identification is pass/fail; either you found a valid decomposition or you didn't.
-   **Evaluate:** 200 per question because evaluation has two parts; identifying the correct answer AND explaining why.

### Mission Map and Navigation

The mission map is rendered dynamically by iterating over `WORLDS` and `LEVELS`:

``` javascript
WORLDS.forEach(world => {
  // Create world header with score summary
  for (let i = world.levelRange[0]; i <= world.levelRange[1]; i++) {
    // Create a card for each level
    // Show type badge, gates, score, completion status
  }
});
```

Free navigation (jumping to any level) is enabled by the `jumpToLevel(idx)` function which simply calls `loadLevel(idx)` with the selected index. This was a deliberate design decision so that players could be able to go back to earlier levels to rebuild confidence or skip ahead if curious to explore a different topic.

### KaTeX Math Rendering

Quantum notation uses LaTeX extensively (state vectors like $|+\rangle$, matrices, equations). KaTeX renders these inline in the browser. The pattern is:

1.  Write HTML strings with `$...$` delimiters: `'Apply $|0\\rangle$ to reach $|{+}\\rangle$'`
2.  After inserting HTML into the DOM, call `renderMath(element)` which invokes KaTeX's auto-render
3.  KaTeX finds all `$...$` instances and replaces them with formatted math

**Important gotcha:** In JavaScript strings, backslashes must be doubled (`\\\\` for a single `\` in the output) because JavaScript consumes one level of escaping before KaTeX sees the string.

If your educational game involves any mathematical notation such as chemistry equations, physics formulas, or statistical expressions, KaTeX can be a valuable tool to use and can often function faster than MathJax.

------------------------------------------------------------------------

## Design Decisions and Why They Were Made

### Educational Framework Alignment

The game's structure was designed against specific educational frameworks from the course material:

**Bloom's Taxonomy** drove the level type progression. Apply levels sit at the Apply tier. Predict levels push into Analyze (tracing a circuit mentally). Deconstruct is also Analyze (reasoning backwards). Evaluate is Evaluate (comparing and judging). This wasn't just a cosmetic decision but for each level type to require a genuinely different cognitive operation.

**Cognitive Load Theory** shaped the UI and scaffolding. The tutorial introduces one gate per level (managing intrinsic load). The left panel was redesigned to separate reading zones from action zones after recognizing that mission briefs and gate buttons competing for attention was extraneous load. Concept boxes start collapsed to avoid overwhelming beginners.

**Chocolate Broccoli** (from the Scaffolding lecture) was the core design constraint. The quantum physics is the game mechanic as the player rotates an actual Bloch Sphere using actual gate transformations. The one Evaluate level that uses multiple choice was a deliberate exception, acknowledged as quiz-adjacent but defended on the grounds that circuit comparison is genuinely an Evaluate-level task.

**Inquiry-Based Learning** informed the progression from guided tutorial -> open exploration (sandbox) -> increasingly autonomous problem-solving. The lecture material's Exposition -> Guidance -> Reflection framework maps onto: mission briefs (exposition) -> sphere feedback during play (guidance) -> concept boxes and success messages (reflection).

### Why Predict Levels Randomize on Retry

If a player sees the answer is \|1> on their first attempt, they'll just click \|1> on retry without re-reasoning. Each Predict level has four start-state variants so that the same gate sequence applied from different starting positions, producing different results. On retry, a different variant is chosen randomly so memorization doesn't help.

### Why the Concept Box Is Elective

The concept box contains deep physics (matrices, Pauli algebra, quantum Fourier transform connections). Making it mandatory would add cognitive load and the decision was made for it to be elective as it lets curious players go deeper while keeping the core experience clean.

### Why Each World Has One Mechanic

An earlier design mixed Apply levels into every world. INFO 560's instructor feedback identified that this made worlds feel interchangeable as the player was doing the same thing regardless of which world they were in. The restructure gives each world a single defining mechanic: Apply (World 1), Deconstruct (World 2), Predict (World 3), mixed capstone (World 4). This means each world transition IS the difficulty increase, and the Bloom's progression is felt through the mechanic change rather than just described in the proposal.

### Why No Two Levels Share a Solution

Instructor feedback identified that six levels shared gate sequences with other levels (e.g., X->H appeared twice, Z->H appeared three times). Players noticed and felt confused entering the same answer repeatedly. The v3 rework ensures all 14 multi-gate sequences across the entire game are unique and verified by script before any level content was written. This required computing all valid 2-gate and 3-gate paths between named states on the Bloch Sphere to produce a menu of unique sequences to allocate across levels.

### Why New Recruit vs Returning Engineer

The tutorial was universally praised but forcing a returning player through it is unnecessary. A session flag (`tutorialCompleted`) gates access: New Recruits must complete the tutorial before mission worlds unlock, while Returning Engineers get immediate access to everything. Gate introduction cards, auto-expanded concept boxes, post-gate transform labels, and gate hover previews only appear for New Recruits and in early worlds so that scaffolding comes off as the player progresses.

### Visual Design

All graphics are generated programmatically and there are no external image files. The Bloch Sphere is built from Three.js primitives (wireframe sphere, rings, lines, sprites). State labels are rendered onto `<canvas>` elements and used as sprite textures. This means the entire game is self-contained with no asset dependencies.

The colour palette uses CSS custom properties (variables) defined in `:root`:

``` css
:root {
  --bg:      #040a12;    /* deep space background */
  --accent:  #00e5ff;    /* cyan -- primary interactive colour */
  --accent2: #7c3aed;    /* purple -- secondary accent */
  --gold:    #f59e0b;    /* gold -- targets and scores */
  --success: #10b981;    /* green -- completion */
  --danger:  #ef4444;    /* red -- errors and warnings */
}
```

To re-theme the entire game, you can change these six values.

------------------------------------------------------------------------

## How to Modify the Game

### Adding a New Level

This is the most common modification. You need to:

1.  **Add an object to the `LEVELS` array** at the position you want it to appear
2.  **Update the corresponding `WORLDS` entry** if the level range changes
3.  **Add an entry to `OPTIMAL_GATES`** at the matching array index

Example of adding a new Apply level to World 1:

``` javascript
{
  id: 'L2b', world: 1, type: LT.APPLY,
  tag: 'World 1 * L2b', title: 'Your New Level',
  mission: 'Start: $|0\\rangle$. Target: $|{+i}\\rangle$.',
  startState: {theta:0, phi:0},
  targetState: {theta: PI/2, phi: PI/2},
  startLabel: '|0>', targetLabel: '|+i>',
  availableGates: ['X','Z','H','S'],
  solution: ['H','S'],
  successMsg: 'Your success message here.',
  concept: '<p>Your concept text here.</p>',
},
```

Then update `OPTIMAL_GATES` by inserting `2` (the solution length) at the matching index.

**Always verify your solution mathematically** using the Node.js verification approach described earlier. A level with an incorrect target state will be unsolvable.

### Adding a New Gate

1.  **Add a transform function** to `GATE_TRANSFORMS`:

``` javascript
T: (theta, phi) => ({ theta, phi: phi + PI / 4 }),  // 45 degrees phase rotation
```

2.  **Add tooltip info** to `GATE_INFO`:

``` javascript
T: {
  name: 'T Gate (Phase-45 degrees)',
  desc: '45 degrees phase rotation. T^2=S.',
  matrix: '$T = \\begin{pmatrix} 1 & 0 \\\\ 0 & e^{i\\pi/4} \\end{pmatrix}$',
},
```

3.  **Add `'T'` to `GATE_DISPLAY_ORDER`** if you want it to appear in the grid.

4.  **Include `'T'` in the `availableGates` array** of any levels that should offer it.

**Why would you add a gate?** The game includes five gates (X, Y, Z, H, S) but real quantum computing uses more. The T gate (45-degree phase rotation) is already defined in the game's transform list but not used in any puzzle levels; so an educator targeting a more advanced audience could create T-gate puzzles. Beyond that, quantum computing uses arbitrary-angle rotation gates (Rx, Ry, Rz) and controlled gates (CNOT, CZ) that could extend the game for university-level courses. Adding gates also lets you create more diverse puzzles. With only five gates currently, the number of unique short sequences between named states is limited, which is why the game carefully allocates them in an attempt to avoid duplicates.

### Changing the Visual Theme

All colours are controlled by CSS variables in the `:root` block. The six main variables control everything:

``` css
--bg:      /* main background -- change for a lighter or different base */
--accent:  /* primary colour -- buttons, highlights, state vector */
--gold:    /* targets, scores, warnings */
--success: /* completion indicators */
--danger:  /* errors, fail states */
--text:    /* main text colour */
```

The starfield background can be modified in the `initStarfield()` function to change star count, colours, or even remove it entirely.

### Adding a New Level Type

This is the most involved modification. You need to:

1.  Add a new value to the `LT` enum
2.  Add a new branch in `initActionZone()` to build the UI
3.  Add a new submission/evaluation function
4.  Handle it in `loadLevel()` for sphere labels, state display, etc.
5.  Add scoring logic
6.  Add map display logic in `renderMap()`

Study how `LT.PREDICT` or `LT.DECONSTRUCT` are implemented as they follow the same pattern of:

-   build UI -> accept input -> evaluate -> show result

------------------------------------------------------------------------

## File Organization

Since the game is a single file, "organization" means section order within that file. Here's why things are ordered the way they are:

```         
CSS           -- must come first so elements are styled when they appear
HTML          -- the page structure; references CSS classes defined above
Three.js CDN  -- must load before our script uses THREE.*
Our Script    -- must come last; references HTML elements and Three.js
```

Within the script, the order matters because JavaScript functions must be defined before they're called in some contexts:

```         
Data (WORLDS, LEVELS)  -- defined first, referenced by everything
State variables        -- defined before any function that reads them
Three.js setup         -- must run before any rendering function
Gate transforms        -- used by Apply, Predict, Deconstruct, Evaluate
Level-type mechanics   -- each type's submit/evaluate function
Solution checking      -- shared by Apply, called after gate application
Level loading          -- orchestrates everything, defined after all pieces exist
Map/navigation/UI      -- top-level user actions, defined last
```

**If you add new code**, put it near the section it's most related to. If you add a new level type, put its mechanics between the existing level type sections (11-14) and add its UI branch in `initActionZone()` (section 17).

------------------------------------------------------------------------

## The Role of AI in Development

This game was built by a solo developer with a background in software engineering but not in HTML, CSS, or JavaScript. AI (Claude by Anthropic) was used as a development partner throughout the project. This section documents how that worked because it's relevant to the toolkit's goal of making game development more accessible to educators.

### What AI Was Used For

-   **Translating design decisions into working code**

    -   The developer made decisions about game structure, level design, educational alignment, and UX, then having AI implement those decisions in HTML/CSS/JS. When the developer said "the concept box should always be elective, never locked," AI wrote the code changes and verified no stale references remained.

-   **Technical domain bridging**

    -   Three.js, KaTeX, CSS animations, raycasting for sphere click detection are specific web technologies the developer hadn't used before. AI provided working implementations based on described requirements rather than requiring the developer to learn each library from scratch first.

-   **Mathematical verification**

    -   All 21 levels have verified gate transform solutions. AI wrote Node.js scripts to verify that each gate sequence starting from the declared start state actually reaches the declared target state. Every level was tested before being included.

-   **Design pressure-testing**

    -   When the developer proposed a design, AI would sometimes push back with concerns grounded in provided course material (cognitive load theory, Bloom's taxonomy, chocolate broccoli, etc.). The developer would accept, reject, or modify the pushback based on their own judgment. This back-and-forth produced better designs than either party would have reached alone.

### Example Prompts and Interactions

Here are examples of the types of prompts that worked well during development, to give a sense of how the AI collaboration actually functioned:

**Design prompt (structural decision):**

> "The professor shared constructive feedback that the worlds could benefit by being defined by their mechanic instead of mixed into every world. Here's the full feedback. What restructure makes sense to you and what are the trade-offs?"

This type of prompt worked well because it provided context (professor's feedback), a clear constraint (one mechanic per world), and solicited analysis rather than just code.

**Technical prompt (bug fix):**

> "The predict marker isn't registering clicks on the sphere. The raycaster seems to be hitting the wrong position when the sphere is rotated. Here's the current click handler code."

Providing the specific code that's failing and describing the symptom ("wrong position when rotated") helped AI identify that the coordinate transform from screen space to sphere-local space was incorrect.

**Pushback example (design disagreement):**

AI suggested adding single-word labels to gate buttons (like "X / flip" or "H / split"). The developer was concerned about accuracy, i.e. "flip" only describes X's behavior at the poles, not the equator, and any one-word simplification would invite legitimate criticism from someone who actually knows quantum mechanics. After discussion, both agreed that one-word labels are a trap. The solution became post-gate transform labels that show the *specific* result ("|0> -> |1>") rather than a general description of the gate. These are more mathematically accurate because they describe what actually happened, not what the gate generally does.

**Prompt that needed iteration:**

> "Make the UI look better."

This is too vague to produce useful results. What worked instead was: "The left panel isn't visually distinct enough, as a peer reviewer didn't notice it for 40 seconds. Can you add a subtle attention pulse on the first tutorial level that stops after the player clicks their first gate?" 

Providing a specific problem, a specific constraint, and a specific success condition helped AI provide recommendations and variations for the developer to consider.

### What AI Was Not Used For

-   **Educational design decisions**

    -   Which level types to include, how to structure the Bloom's progression, whether certain features should be removed, how the research plan should work, etc. were all human decisions informed by the course lectures and the developer's judgment.

-   **Peer review engagement**

    -   All peer feedback was read and evaluated by the developer. AI helped implement the resulting changes but which feedback to act on was the developer's judgement.

### The Key Insight for Educators

AI doesn't replace the need to understand your subject matter or make good design decisions. What it does is **dramatically lower the technical barrier** to implementing those decisions. An educator who knows what they want to teach, understands how learning works, and can articulate clear requirements can now build interactive educational tools that would previously have required a professional development team.

This supports the "resource equalizer" argument: AI makes the gap between "I have a great idea for a learning game" and "I have a working prototype" much smaller. The expertise that matters is the educational expertise; understanding your learners, your learning outcomes, and the pedagogical frameworks that make games effective rather than decorative.

------------------------------------------------------------------------

## Lessons Learned

### Things That Worked Well

-   **Single-file architecture** made iteration incredibly fast and supportive with AI. Change code, save, refresh browser, test. No build step meant no waiting.

-   **Data-driven level design** was the best decision in the project. Adding or modifying a level means changing one object in an array. The game engine handles everything else.

-   **Mathematical verification before building** prevented unsolvable levels. Every start/target pair was verified with a script before the level content was written.

-   **Progressive disclosure in the tutorial** (one gate per level) received universally positive feedback from peer reviewers. Players who felt lost about quantum computing still felt they could make progress.

-   **Eliminating duplicate solutions** through scripted verification of the entire solution space before designing levels. Computing all valid 2-gate and 3-gate paths between named states produced a menu of unique sequences to allocate across levels, preventing the "am I entering the same answer again?" confusion that plagued earlier versions.

-   **Instructor/peer feedback loop as a design tool.** Multiple rounds of feedback drove structural improvements (world theming, duplicate elimination, predict fail threshold) that the developer would not have identified alone. Treating peer review as design input rather than just evaluation made the game significantly stronger.

### Things That Were Harder Than Expected

-   **Coordinate system mapping** (Bloch physics convention to Three.js Y-up) caused subtle bugs that were difficult to diagnose. If a gate transform looked right in the math but wrong on screen, it was almost always a coordinate mapping issue.

-   **Raycasting for predict mode** (clicking the sphere to place a marker) went through multiple implementations before working correctly. An example of such is that as the sphere rotates, click positions in screen space don't map directly to positions in sphere-local space. The final solution used Three.js's built-in `intersectObject` and then `worldToLocal()` to convert back.

-   **KaTeX escaping in JavaScript strings** can genuinely be confusing. A single backslash in rendered LaTeX requires four backslashes in a JS string inside an HTML file (`\\\\`). This caused many rendering bugs that looked like typos but were actually escaping issues.

-   **Balancing educational content with gameplay** is an ongoing tension. Peer feedback was often split where some wanted more explanation, others wanted less. The elective concept box was the compromise to make things always available but never mandatory.

-   **Younger players skipping all educational content.** At a showcase demo, junior high students clicked gates randomly until matching the target, ignoring mission briefs, concept boxes, and success messages entirely while an adult with an engineering background thrived with the same content. This led to adding gate introduction cards, post-gate transform labels, and gate hover previews as scaffolding that puts the teaching moments directly into the interaction rather than in optional text that can be ignored.

------------------------------------------------------------------------

## References and Resources

### Core Technologies

-   **Three.js documentation:** [threejs.org/docs](https://threejs.org/docs/)
    -   The official docs are comprehensive but can be overwhelming.
-   **Three.js fundamentals (tutorial):** [threejs.org/manual/#en/fundamentals](https://threejs.org/manual/#en/fundamentals)
    -   Something more beginner-friendly than the API docs
-   **KaTeX documentation:** [katex.org/docs/api](https://katex.org/docs/api)
    -   Covers all supported LaTeX commands
-   **KaTeX auto-render extension:** [katex.org/docs/autorender](https://katex.org/docs/autorender)
    -   The specific extension used to find and render `$...$` delimiters
-   **MDN Web Docs (CSS):** [developer.mozilla.org/en-US/docs/Web/CSS](https://developer.mozilla.org/en-US/docs/Web/CSS)
    -   The definitive reference for CSS.
-   **MDN Web Docs (JavaScript):** [developer.mozilla.org/en-US/docs/Web/JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript)
    -   The definitive reference for JavaScript.

### Quantum Computing References

-   **Bloch Sphere on Wikipedia:** [en.wikipedia.org/wiki/Bloch_sphere](https://en.wikipedia.org/wiki/Bloch_sphere)
    -   Clear explanation of the coordinate convention used in the game
-   **Quantum Gates on Wikipedia:** [en.wikipedia.org/wiki/Quantum_logic_gate](https://en.wikipedia.org/wiki/Quantum_logic_gate)
    -   Reference for gate matrices and their Bloch sphere rotations
-   **IBM Quantum Learning:** [learning.quantum.ibm.com](https://learning.quantum.ibm.com/)
    -   Interactive tutorials that cover the same gates the game teaches

### Educational Game Design

-   **Kirschner, Sweller, & Clark (2006)** "Why Minimal Guidance During Instruction Does Not Work"
    -   Referenced in the course lectures on inquiry-based learning. Important context for why the tutorial uses guided instruction before free exploration.
-   **Bloom's Taxonomy (revised, 2001)**
    -   The Apply -> Analyze -> Evaluate progression that structures the four level types
-   **Cognitive Load Theory (Sweller)**
    -   The framework behind progressive gate disclosure and the separated reading/action zones in the UI

### Development Tools

-   **VS Code:** [code.visualstudio.com](https://code.visualstudio.com/)
    -   Recommended editor
-   **Node.js:** [nodejs.org](https://nodejs.org/)
    -   Used for running verification scripts outside the browser
-   **Chrome DevTools documentation:** [developer.chrome.com/docs/devtools](https://developer.chrome.com/docs/devtools/)
    -   Guide to using browser developer tools for debugging.

### Helpful Techniques Discovered During Development

-   **CSS custom properties (variables)** for theming
    -   Define colours once in `:root`, reference everywhere.
        -   Changing six values re-themes the entire game
-   **`requestAnimationFrame()`** for smooth animation
    -   The browser calls your render function at the display's refresh rate. Used for the sphere rotation, state vector animation, and dot pulsing
-   **`data:` URLs for inline assets**
    -   Not used in this project but worth knowing that you can embed small images directly in HTML/CSS as base64 strings and avoid external files entirely
-   **Three.js `Sprite` with `CanvasTexture`** for text labels in 3D
    -   Render text onto a `<canvas>` element, then use it as a texture on a sprite that always faces the camera.
        -   This is how the \|0>, \|1>, \|+>, \|-> labels on the sphere work

------------------------------------------------------------------------

## License and Attribution

This game and toolkit were created by **Austin Hancock** as a course project for a graduate-level Serious STEM Games course (560). The game is provided as-is for educational purposes. The external libraries (Three.js, KaTeX, Google Fonts) are each under their own open-source licenses.

If you build something using this toolkit as a starting point, no attribution is required, but it would be appreciated. Good luck with your game.
