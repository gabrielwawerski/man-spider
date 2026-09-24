# Roadmap

The roadmap is ordered by technical risk, not by visual appeal.

## Phase 0: repository and research

### 0.0 Documentation

- [x] define v1 and v2 scope;
- [x] document architecture;
- [x] document feasibility gates;
- [x] document testing expectations.

### 0.1 Mod bootstrap

- [ ] create a minimal Cities: Skylines II code-mod project;
- [ ] load successfully in the current game build;
- [ ] emit a startup log line;
- [ ] verify clean disable/unload behavior.

**Exit condition:** the mod loads reliably without gameplay behavior.

## Phase 1: prove the simulation pipeline

### 0.2 Criminal detection

- [ ] query active criminals;
- [ ] classify relevant criminal flags;
- [ ] exclude deleted/arrested/prisoner entities;
- [ ] log bounded diagnostic output;
- [ ] profile query cost on a large city.

**Exit condition:** active criminal entities can be identified reliably.

### 0.3 Position resolution

- [ ] resolve a selected criminal to current world coordinates;
- [ ] follow position updates while the criminal moves;
- [ ] handle criminals represented through transport/resident state;
- [ ] recover when position cannot be resolved.

**Exit condition:** the selected criminal can be located visually in-game.

### 0.4 Debug hero

- [ ] create one persistent hero state/entity;
- [ ] render a debug marker or primitive;
- [ ] move it toward the selected criminal;
- [ ] handle pause and simulation speeds;
- [ ] reacquire when a target disappears.

**Exit condition:** a visible debug hero autonomously reaches real criminal incidents.

### 0.5 Intervention

- [ ] choose a bounded crime-resolution mechanism;
- [ ] validate the target before mutation;
- [ ] resolve one criminal;
- [ ] verify the normal simulation remains coherent afterward;
- [ ] ensure unrelated police/crime systems continue to work.

**Exit condition:** the complete gameplay loop works with placeholders.

At this point the core concept is considered technically validated.

## Phase 2: v1 autonomous hero

### 0.6 Patrol and prioritization

- [ ] idle/patrol behavior;
- [ ] target scoring;
- [ ] target commitment/cooldowns;
- [ ] sensible behavior when no crime exists;
- [ ] recovery from unreachable targets.

### 0.7 Simplified traversal

- [ ] fast aerial/rooftop travel;
- [ ] basic obstacle handling;
- [ ] arrival positioning;
- [ ] no teleporting in the polished path unless used as recovery.

### 0.8 Character presentation

- [ ] prove custom animated representation;
- [ ] original/placeholder model;
- [ ] idle;
- [ ] run/airborne;
- [ ] landing;
- [ ] intervention/fight animation;
- [ ] animation state driven by simulation state.

### 0.9 Feedback

- [ ] optional hero status panel;
- [ ] current activity;
- [ ] target distance;
- [ ] crimes stopped;
- [ ] criminals handled;
- [ ] enable/disable setting.

### 1.0 v1 release candidate

Acceptance criteria:

- one autonomous hero;
- reacts to real simulation crime;
- visible travel to incidents;
- visible intervention;
- bounded effect on the actual criminal;
- stable save/load;
- stable pause/speed changes;
- acceptable large-city performance;
- no dependency on unauthorized third-party character assets.

## Phase 3: v2 traversal

### 1.1 Rooftop traversal research

- [ ] query nearby building/geometry information;
- [ ] identify safe landing surfaces;
- [ ] implement large jumps;
- [ ] handle low-density areas.

### 2.0 Autonomous web swinging

- [ ] candidate anchor discovery;
- [ ] anchor scoring;
- [ ] rope constraint;
- [ ] gravity and momentum;
- [ ] release timing;
- [ ] collision handling;
- [ ] chained anchor planning;
- [ ] recovery/fallback traversal.

Acceptance criteria:

- the hero can cross a dense district autonomously;
- movement trends toward the target rather than oscillating;
- collisions do not permanently wedge the controller;
- low-density zones have a fallback strategy.

## Phase 4: optional player control

### 2.x Take-control mode

- [ ] camera mode;
- [ ] keyboard input;
- [ ] gamepad input;
- [ ] player-directed swing anchors or assisted anchors;
- [ ] relinquish control back to AI;
- [ ] preserve the same traversal physics where practical.

This phase is intentionally optional. It should not be required for the autonomous mod to be considered complete.

## Explicit non-goals for early versions

Do not block early milestones on:

- a final superhero model;
- cinematic combat;
- destructible environments;
- pedestrians reacting to the hero;
- villains/boss fights;
- advanced mission scripting;
- player-controlled traversal;
- perfect web physics.

Those features can only be justified after the crime-response loop is proven.
