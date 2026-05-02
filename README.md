# EcoSim v5: Research Hybrid

EcoSim v5 is a browser-based artificial life simulation where herbivores and carnivores evolve, explore, hunt, reproduce, and adapt inside a dynamic 2D ecosystem.

It combines neural-agent behavior, population-based training, curiosity rewards, pheromone fields, lidar-style perception, quality-diversity archiving, and environmental pressures such as weather, light, temperature, food growth, and obstacles.

The project runs as a single HTML file using Canvas, TensorFlow.js, and Chart.js.

---

## Features

### Neural agents

Each agent has a small neural network that receives sensory inputs and outputs movement decisions:

* Turn direction
* Forward thrust
* Pheromone/social signal strength

Agents do not follow fixed rules for navigation. Their behavior emerges from neural policies that mutate, reproduce, and spread through the population.

### Herbivores and carnivores

The ecosystem contains two agent types:

* **Herbivores** eat food, avoid predators, explore, and reproduce.
* **Carnivores** hunt herbivores, use aggressive morphology, and gain energy from kills.

Both agent types can evolve different speeds, sizes, aggression levels, sensory ranges, metabolisms, curiosity, social tendency, and coloration.

### Lidar-style perception

Agents perceive the world through radial sectors around their body. Each sector senses:

* Walls
* Food or prey
* Danger
* Nearby friends
* Obstacles

This gives agents a compact local view of the world instead of full global knowledge.

### Pheromone fields

Agents leave and read two pheromone channels:

* Green food scent
* Red danger scent

The pheromone field decays and diffuses over time. Rain causes pheromones to smear and decay differently, changing how agents navigate.

### Curiosity and novelty

Agents receive small rewards for entering underexplored areas of the world. This encourages exploration and helps prevent the population from collapsing into repetitive local behavior.

The novelty system uses a count-based visitation map over spatial cells.

### Population-Based Training

Successful agents can pass mutated neural policies to their offspring. This allows useful behaviors to spread while still preserving variation through mutation.

### Quality-Diversity archive

EcoSim v5 adds a quality-diversity archive inspired by MAP-Elites-style systems.

Instead of storing only the single best policy, the archive stores strong agents across different behavioral and morphological niches. This helps preserve useful stepping stones such as:

* Fast herbivores
* Aggressive carnivores
* Exploratory agents
* High-sight agents
* Social agents
* Low-metabolism survivors

New agents can occasionally inherit from this archive, creating a more open-ended evolutionary process.

### Dynamic environment

The world includes changing environmental conditions:

* Day and night cycle
* Light level
* Temperature
* Rain
* Cloud cover
* Drought
* Cold snaps
* Seasonal drift
* Food growth variation

Environmental conditions affect movement, visibility, food growth, and pheromone behavior.

### Food patches and obstacles

Food tends to grow in patches instead of appearing uniformly everywhere. Obstacles create spatial structure and make navigation more interesting.

### Social imitation

Agents can occasionally imitate nearby same-species agents that are performing better. This creates a simple cultural-learning effect on top of genetic evolution.

### Inspector and dashboard

The UI includes:

* Live population chart
* Herbivore count
* Carnivore count
* Food count
* Average age
* Best archived score
* Quality-diversity archive size
* FPS and TensorFlow tensor count
* Click-to-inspect neural sensor view

---

## How to run

1. Save the simulation as an HTML file, for example:

   ```text
   ecosim_v5_research_hybrid.html
   ```

2. Open the file in a modern browser.

3. Internet access is required because the page loads dependencies from CDNs:

   * TensorFlow.js
   * Chart.js

No build step is required.

---

## Controls

### Pop Limit

Sets the maximum number of agents allowed in the simulation.

Higher values create richer ecosystems but require more CPU/GPU resources.

### Sim Speed

Runs multiple simulation ticks per animation frame.

Higher speed makes evolution happen faster but may reduce frame rate.

### Food Richness

Controls food growth intensity.

Higher values help herbivores survive and can support more carnivores.

### Mutation

Controls the mutation strength applied to inherited brains and genomes.

Lower mutation creates stable lineages. Higher mutation creates more variation but can destabilize successful behaviors.

### Pheromone Field

Shows or hides the pheromone overlay.

Green indicates food-related scent. Red indicates danger-related scent.

### Vision / Lidar

Shows the selected agent’s perception rays.

Click an agent to inspect it.

### Quality-Diversity Map

Shows a small archive visualization. Each cell represents a stored high-performing policy niche.

### Adaptive Weather

Enables changing weather conditions such as rain, drought, clouds, and cold snaps.

### Buttons

* **Pause / Resume**: Stops or resumes the simulation.
* **Step**: Advances one simulation tick while paused.
* **Reset**: Restarts the ecosystem.
* **Seed Food**: Adds a burst of food.
* **+ Herbivores**: Adds new herbivores.
* **+ Carnivores**: Adds new carnivores.

---

## Agent inputs

Each agent receives a compact neural input vector made from:

1. Sector-based lidar perception
2. Local pheromone readings
3. Pheromone gradients
4. Internal state
5. Environmental state
6. Short memory of recent actions

The neural network does not receive full world coordinates of every object. It only sees a local sensory approximation.

---

## Agent outputs

The neural network produces three outputs:

1. **Turn**: left/right steering
2. **Thrust**: forward movement intensity
3. **Signal**: pheromone/social signaling strength

These outputs are converted into physical movement and environmental interaction.

---

## Genome

Each agent also has a mutable genome controlling morphology and life-history traits:

* Speed
* Size
* Aggression
* Sight range
* Metabolism
* Curiosity
* Social imitation tendency
* Hue and saturation

Genome traits affect survival, reproduction, energy use, perception, and appearance.

---

## Fitness

Agent fitness is based on a mixture of:

* Age
* Meals eaten
* Kills made
* Offspring produced
* Curiosity score
* Remaining energy

Fitness is not used as a direct hand-authored behavior rule. It is used to decide which policies are worth storing in the quality-diversity archive.

---

## Architecture overview

### Main systems

* `SpatialHash`: Optimizes neighbor lookup for agents and food.
* `QDArchive`: Stores strong policies across behavior/genome niches.
* `Agent`: Contains genome, neural network, sensing, movement, metabolism, reproduction, and interaction logic.
* Pheromone grid: Stores food and danger scent fields.
* Environment model: Controls light, weather, temperature, and food growth.
* Renderer: Draws food, agents, pheromones, obstacles, debug views, and UI state.

### Rendering layers

The simulation uses three canvas layers:

1. `pheroCanvas`: pheromone field
2. `mainCanvas`: world, food, obstacles, agents
3. `debugCanvas`: selected-agent vision and QD overlay

---

## Performance notes

EcoSim v5 is designed to run in a browser, but neural inference and agent interactions can still become expensive.

Performance optimizations include:

* Spatial hashing for nearby-agent lookup
* Separate food spatial hash
* Reusable pheromone image buffers
* Synchronous TensorFlow.js inference through `dataSync()`
* Tensor disposal for inherited and archived policies
* Optional simulation speed control
* Population limit control

If performance drops, reduce:

* Population limit
* Simulation speed
* Food richness
* Browser zoom level
* Number of open tabs

---

## Suggested experiments

Try changing one variable at a time:

### Predator-prey balance

Increase food richness and observe whether carnivores become more stable over time.

### Mutation pressure

Raise mutation and watch whether behavior becomes more chaotic or whether new strategies emerge.

### Environmental stress

Enable adaptive weather and observe how drought or cold snaps affect population cycles.

### Curiosity effects

Watch whether high-curiosity herbivores spread into less crowded regions and avoid predator pressure.

### Quality-diversity archive

Let the simulation run for several minutes and watch the QD cell count. A larger archive usually means the ecosystem has discovered more distinct survival strategies.

---

## Known limitations

EcoSim v5 is an artificial life toy model, not a biologically accurate ecological simulator.

Important simplifications include:

* No sexual reproduction
* No true reinforcement learning training loop
* No backpropagation from reward
* No explicit species separation beyond herbivore/carnivore type
* No terrain resources other than food patches
* No persistent save/load system
* No GPU particle system for very large populations

The neural policies evolve through mutation, selection, imitation, and archive reuse rather than gradient-based learning.

---

## Possible future improvements

* Save/load ecosystem state
* Export best agents or archive policies
* Add multiple plant types
* Add disease or parasites
* Add terrain biomes
* Add energy transfer efficiency
* Add mating compatibility
* Add speciation clusters
* Add Web Workers for simulation ticks
* Add WebGPU rendering for large populations
* Add offline bundled dependencies
* Add interactive charts for genome distributions
* Add replay mode for notable lineages

---

## Dependencies

Loaded through CDN:

* TensorFlow.js 4.22.0
* Chart.js 4.5.1

The simulation itself is plain HTML, CSS, and JavaScript.

---

## License

No license has been specified yet.

Before publishing or sharing this project, add a license such as MIT, Apache-2.0, GPL-3.0, or another license that fits your goals.
