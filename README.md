# Qubit Quest

**A browser-based puzzle game that teaches quantum gate operations through direct manipulation of a 3D Bloch Sphere.**

Created by Austin Hancock -- Graduate course project, Serious STEM Games (560)

---

## Play the Game

**[Launch Qubit Quest](https://austin-s-hancock.github.io/qubit-quest/qubit-quest.html)**

No installation required. Opens in any modern browser (Chrome, Firefox, Safari, Edge).
Requires an internet connection to load the 3D rendering and math libraries.

---

## Educator Toolkit

**[Read the Toolkit](./TOOLKIT.md)**

A guide for educators interested in building browser-based educational games using
HTML, CSS, JavaScript, and AI-assisted development. Covers:

- How the game is structured and why
- How each major system works (3D sphere, gate transforms, level types, scoring)
- Design decisions and the educational frameworks behind them
- How to modify the game -- adding levels, gates, or visual themes
- The role of AI in development and example prompts
- Lessons learned and references

---

## About the Game

Qubit Quest teaches single-qubit quantum gate operations through a puzzle game
set aboard the fictional Station Helix-7. A rogue AI has scrambled the station's
quantum memory core -- the player must restore each chamber by applying quantum
gates to rotate a qubit on a live 3D Bloch Sphere to match the target state.

**21 levels across 5 worlds**, each world defined by a distinct mechanic:

| World | Name | Mechanic |
|-------|------|----------|
| Tutorial | Station Orientation | Guided gate introduction + free sandbox |
| World 1 | The Quantum Awakening | Apply gates with live sphere feedback |
| World 2 | Dark Chambers | Build gate sequences blind (sphere frozen) |
| World 3 | Signal Forecast | Predict circuit outcomes before they play |
| World 4 | The Memory Core | Capstone mixing all level types |

**Four level types** mapping onto Bloom's Taxonomy:

- **Apply** -- navigate to a target using gate sequences (Apply)
- **Deconstruct** -- identify gates from start and end states alone (Analyze)
- **Predict** -- place a prediction marker before a sequence executes (Analyze)
- **Evaluate** -- compare two circuits and explain why one fails (Evaluate)

**Gates covered:** X, Y, Z, H, S

---

## Files

| File | Description |
|------|-------------|
| `qubit-quest.html` | The complete game -- playable AND editable in any text editor |
| `TOOLKIT.md` | Educator guide to understanding and modifying the game |
| `README.md` | This file |

The game is a single self-contained HTML file. To edit it, open it in a text editor
such as VS Code. No build tools, package managers, or installation required.

---

## Built With

- [Three.js r128](https://threejs.org/) -- 3D Bloch Sphere rendering
- [KaTeX 0.16.9](https://katex.org/) -- LaTeX math notation
- [Google Fonts](https://fonts.google.com/) -- Orbitron, Share Tech Mono, Exo 2
- [Claude (Anthropic)](https://claude.ai/) -- AI-assisted development

---

## Course Context

This project was developed for a graduate-level Serious STEM Games course. The design
draws on Bloom's Taxonomy, Cognitive Load Theory, Inquiry-Based Learning, and the
Science of Serious Games. The toolkit documents how these frameworks shaped specific
game design decisions.
