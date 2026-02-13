# EcoSim4-HIVEMIND

**EcoSim4-HIVEMIND** is a single-file, browser-based ecosystem + neuroevolution sandbox built around a “hivemind” style learning loop: agents run lightweight neural policies in real time, reproduce with mutation, and optionally “borrow” from a small bank of best-performing policies (Population-Based Training / PBT).

You get a fast, visual playground for:
- predator/prey dynamics (herbivores vs carnivores)
- simple “lidar” perception
- pheromone field overlays
- day/night + temperature modifiers
- click-to-inspect neural inputs/outputs and agent state

---

## What’s in this repo

- `index.html` — **the entire simulator** (UI + rendering + simulation + learning logic)

No build step, no bundler.

---

## Quick start

### Run locally (recommended)
Because browsers can be picky about local file permissions, serving over HTTP is the most reliable:

```bash
git clone https://github.com/kai9987kai/Ecosim4-HIVEMIND.git
cd Ecosim4-HIVEMIND
python -m http.server 8000
