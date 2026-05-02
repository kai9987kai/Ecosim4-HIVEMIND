# EcoSim v7: Open-Ended Ecology Lab

EcoSim v7 is a single-file browser simulation of an evolving predator-prey ecosystem. Agents are controlled by small neural networks, inherit mutable genomes, leave pheromone trails, explore through curiosity rewards, and evolve inside a quality-diversity archive rather than converging on only one best policy.

The v7 build keeps the v5/v6 feature set and adds a more open-ended ecology layer: speciation pressure, carrion and nutrient cycling, migration pulses, rare-niche protection, trophic autopilot, regime shifts, and expanded telemetry.

---

## Quick start

1. Download or clone `ecosim_v7_research_hybrid_plus.html`.
2. Open it in a modern desktop browser.
3. Watch the population dashboard and click an agent to inspect its sensors, genome, energy, and neural decisions.

No build step is required.

### Optional local server

Opening the HTML file directly should work in most browsers. For a cleaner development workflow, run a tiny static server from the folder containing the file:

```bash
python3 -m http.server 8080
```

Then open:

```text
http://localhost:8080/ecosim_v7_research_hybrid_plus.html
```

### Dependency note

The simulation is self-contained except for the Chart.js CDN script used by the population chart:

```html
<script src="https://cdn.jsdelivr.net/npm/chart.js@4.5.1/dist/chart.umd.min.js"></script>
```

If you need fully offline use, download Chart.js locally and replace the CDN URL with a local path, or remove the chart panel.

---

## What you are seeing

EcoSim contains two trophic groups:

- **Herbivores**: green agents that seek food, avoid danger, leave food-oriented scent, reproduce, and diversify into behavioral niches.
- **Carnivores**: red agents that hunt herbivores, scavenge carrion, leave danger scent, and evolve predator strategies.

The world also contains:

- **Food patches** that drift, grow, and respond to weather and nutrient pulses.
- **Obstacles** that block movement and appear in lidar-like vision.
- **Pheromone fields** for food and danger trails.
- **Day/night and weather** that affect light, movement, food growth, scent decay, and ecological pressure.
- **Carrion** created by deaths, which can feed carnivores or decay into nutrients.
- **Regime shifts** such as Nutrient Bloom, Famine Belt, Prey Refuge, and Migration Pulse.

---

## Controls

| Control | What it does |
|---|---|
| **Pop Limit** | Maximum number of agents allowed in the world. Higher values create denser ecosystems but cost more CPU. |
| **Sim Speed** | Runs multiple simulation ticks per animation frame. Higher speeds advance evolution faster but reduce visual smoothness. |
| **Food Richness** | Scales food growth and food availability. Low values create harsh selection; high values support larger populations. |
| **Mutation** | Controls gene and neural-weight mutation strength during reproduction and archive sampling. |
| **Speciation** | Increases rare-niche protection and reduces homogenization from dominant lineages. |
| **Pheromone Field** | Toggles the visible scent overlay: green for food scent, red for danger scent. |
| **Vision / Lidar** | Shows the selected agent's radial sensing sectors. |
| **Quality-Diversity Map** | Shows the archive of diverse high-scoring policies near the bottom-left of the screen. |
| **Adaptive Weather** | Enables dynamic weather states such as Sunny, Cloudy, Rain, Drought, Cold Snap, and Wind Bloom. |
| **Brain Economy Mode** | Reduces neural decision frequency for older/stable agents to improve performance. |
| **Ecosystem Autopilot** | Applies gentle rescue pressure to prevent total collapse while preserving boom/bust dynamics. |
| **Nutrient Cycling** | Enables carrion, scavenging, decay, and nutrient feedback into food patches. |
| **PAUSE / RESUME** | Stops or resumes the simulation loop. |
| **STEP** | Advances one tick while paused. |
| **RESET** | Reinitializes the world, archive, populations, food, weather, and telemetry. |
| **SEED FOOD** | Adds a burst of food to active patches. |
| **+ HERBIVORES** | Injects additional herbivores. |
| **+ CARNIVORES** | Injects additional carnivores. |

---

## Dashboard metrics

| Metric | Meaning |
|---|---|
| **Agents** | Total living agents. |
| **Herbivores** | Living plant-eating agents. |
| **Carnivores** | Living predator/scavenger agents. |
| **Food** | Available food items. |
| **Avg Age** | Mean age of living agents. |
| **Best Score** | Best quality-diversity archive score observed. |
| **QD Cells** | Number of occupied behavior niches in the archive. |
| **Species** | Number of coarse species keys currently represented. |
| **Novelty** | Average recent novelty/exploration signal. |
| **Stability** | Heuristic ecosystem balance score. |
| **Carrion** | Dead biomass available for scavenging or nutrient recycling. |
| **FPS / Nets** | Render performance and number of active neural networks. |

---

## Agent inspector

Click an agent to inspect it.

The inspector shows:

- Agent ID, trophic type, parent lineage, and generation.
- Energy, health, age, meals, kills, births, and species key.
- Genome values such as speed, size, aggression, sight, curiosity, sociality, and color traits.
- Fitness, novelty score, rare-niche bonus, and discovered novelty cells.
- A compact neural/sensor visualization:
  - Green sectors indicate food or prey signals.
  - Red sectors indicate danger or predators.
  - Blue sectors indicate nearby allies.
  - Gray sectors indicate obstacles or walls.
  - Bars show turn, speed, signal, and novelty-related output.

---

## Core simulation systems

### 1. Neural agents

Each agent has a small feed-forward neural network implemented with typed arrays for speed. The network reads sensory inputs and produces three actions:

- **Turn**: rotation direction and strength.
- **Thrust**: movement effort.
- **Signal**: pheromone emission behavior.

v7 no longer depends on TensorFlow.js for agent inference. This reduces tensor allocation, improves frame stability, and keeps all neural policies lightweight and cloneable.

### 2. Mutable genomes

Agents inherit genes that affect:

- Speed
- Body size
- Aggression
- Sight range
- Metabolism
- Curiosity
- Social imitation tendency
- Display color
- Species identity

Mutation affects both genes and neural weights. The active regime can temporarily increase mutation during ecological disruption.

### 3. Quality-diversity archive

Instead of keeping only the single best lineage, EcoSim stores elite agents across many behavioral descriptors. Archive descriptors include traits such as speed, aggression, sight, novelty rate, homeostasis, and species fragments.

The archive can emit new agents through several modes:

- **Elite emitter**: samples strong high-scoring policies.
- **Novelty emitter**: samples exploratory policies.
- **Coverage emitter**: samples broadly from occupied cells to preserve diversity.

### 4. Curiosity and count-based novelty

Agents receive novelty bonuses for visiting underexplored spatial cells. This encourages exploration, dispersal, and new ecological behaviors rather than pure local optimization.

### 5. Speciation pressure

The **Speciation** slider increases rare-niche protection. Rare species receive a fitness/archive bonus and are less likely to be overwritten by dominant lineages through social imitation.

### 6. Pheromones

Agents emit two scent channels:

- Food/opportunity scent
- Danger/predator scent

Scent diffuses and decays over time. Rain increases smear; day/night and weather alter decay. Wind can bias pheromone drift.

### 7. Nutrient cycling

When nutrient cycling is enabled:

- Dead agents can become carrion.
- Carnivores can scavenge carrion.
- Uneaten carrion decays.
- Decay increases local nutrient pulses.
- Nutrient pulses improve future food growth.

This adds a third ecological resource loop beyond simple food and predation.

### 8. Regime shifts

The world can enter temporary ecological regimes:

- **Quiet**: normal ecosystem dynamics.
- **Nutrient Bloom**: increased food growth.
- **Famine Belt**: reduced food growth and harsher selection.
- **Prey Refuge**: predator pressure is reduced and prey recovery is encouraged.
- **Migration Pulse**: new edge-spawned agents enter, often increasing diversity.

### 9. Ecosystem autopilot

Autopilot is a soft stability mechanism. It does not freeze the simulation or force a perfect balance. Instead, it gently rescues dangerously low populations, triggers migration pulses when diversity drops, and reduces avoidable total collapses.

### 10. Spatial hashing

EcoSim uses spatial hashes for agents, food, and obstacles. This keeps sensing, collision checks, hunting, scavenging, and food lookup efficient as population size increases.

---

## Performance tips

For smoother runs:

1. Keep **Brain Economy Mode** enabled.
2. Lower **Pop Limit** if FPS drops.
3. Disable **Vision / Lidar** unless inspecting an agent.
4. Disable **Pheromone Field** for a small rendering boost.
5. Lower **Sim Speed** if the browser becomes unresponsive.
6. Avoid setting **Pop Limit** near 900 on older laptops.

For faster evolution experiments:

1. Increase **Sim Speed**.
2. Increase **Mutation** moderately.
3. Keep **Quality-Diversity Map** enabled to watch archive coverage.
4. Raise **Speciation** to protect unusual lineages.
5. Use **SEED FOOD** after crashes instead of resetting immediately.

---

## Suggested experiments

### Predator-prey stability

1. Start with default settings.
2. Let the simulation run for several in-sim days.
3. Watch whether herbivores and carnivores settle into cycles or collapse.
4. Toggle **Ecosystem Autopilot** off and compare stability.

### Diversity pressure

1. Set **Speciation** low.
2. Let one lineage dominate.
3. Reset and set **Speciation** high.
4. Compare species count, QD cells, and behavior diversity.

### Harsh ecology

1. Lower **Food Richness** to 45-65.
2. Increase **Mutation** to 18-25.
3. Watch for compact, efficient herbivore strategies and opportunistic scavenging.

### Open-ended search

1. Keep **Adaptive Weather**, **Nutrient Cycling**, and **Ecosystem Autopilot** enabled.
2. Set **Sim Speed** to 4-7.
3. Observe the QD map over time.
4. Click agents from different niches and compare genomes, sensors, and behavior.

---

## File structure

EcoSim v7 is intentionally contained in one HTML file:

```text
ecosim_v7_research_hybrid_plus.html
```

Inside the file:

```text
<head>
  CSS, layout, Chart.js script
<body>
  Canvas layers and UI panels
<script>
  Spatial hashes
  Typed-array neural networks
  QD archive
  Agent class
  Food/carrion/obstacle systems
  Weather and regime logic
  Simulation loop
  Rendering and inspector UI
```

Canvas layers:

| Canvas | Purpose |
|---|---|
| `mainCanvas` | Background, patches, obstacles, food, carrion, agents. |
| `pheroCanvas` | Pheromone overlay above the opaque world canvas. |
| `debugCanvas` | Vision rays, QD map, and selected-agent overlays. |

---

## Notable v7 improvements over v6

- Added **speciation pressure** and rare-niche protection.
- Added **carrion**, scavenging, decay, and nutrient feedback.
- Added **migration pulses** and ecological regime shifts.
- Added **ecosystem autopilot** to reduce uninteresting extinction states.
- Added **wind** and more expressive weather ecology.
- Added richer telemetry: species, novelty, stability, carrion, and regime.
- Improved social imitation so rare species are less likely to be erased.
- Improved archive scoring with novelty, homeostasis, trophic success, and rarity.
- Preserved the v6 pheromone-layer fix.
- Preserved the v6 tick-order fix: movement updates happen before contact-heavy interaction queries.
- Replaced TensorFlow.js inference with typed-array networks for lower overhead.
- Added render throttling and dirty-hash handling for smoother high-speed runs.

---

## Troubleshooting

### The chart does not appear

Chart.js may not have loaded from the CDN. Check your internet connection or replace the CDN script with a local Chart.js file.

### The simulation feels slow

Lower **Pop Limit**, lower **Sim Speed**, and keep **Brain Economy Mode** enabled. You can also turn off **Vision / Lidar** and **Pheromone Field**.

### Everyone dies

This can happen under harsh settings. Try one or more of the following:

- Press **SEED FOOD**.
- Add herbivores with **+ HERBIVORES**.
- Enable **Ecosystem Autopilot**.
- Increase **Food Richness**.
- Lower **Mutation** if lineages are destabilizing too quickly.

### Predators disappear

Predators need enough herbivores and carrion to survive. Increase food richness first so herbivores recover, then add carnivores.

### One lineage dominates

Increase **Speciation**, increase **Mutation** slightly, or wait for a Migration Pulse/regime shift.

---

## Known limitations

- This is an artificial-life toy model, not a biological model.
- The neural networks evolve through mutation, imitation, archive sampling, and selection-like survival pressure, not gradient descent.
- Species are coarse behavioral/genetic clusters, not formal reproductive species.
- Results are stochastic and depend on browser performance, screen size, and random initialization.
- Long-running simulations may still drift into collapse or dominance; this is part of the experiment.

---

## Development notes

The code favors readable single-file experimentation over a modular build system. If you extend it, good next targets include:

- Save/load archive snapshots.
- Export CSV telemetry.
- Add graph controls for viewing novelty, species count, stability, and archive coverage over time.
- Add terrain types with local movement or food-growth modifiers.
- Add reproduction compatibility rules for more explicit speciation.
- Add keyboard shortcuts for pause, step, and debug overlays.
- Add deterministic seeds for reproducible experiments.

---

## License

No license is currently specified. Add a license before publishing or redistributing as an open-source project.
