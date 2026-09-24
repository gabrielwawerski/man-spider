# Feasibility and research

## Current assessment

The concept is technically plausible enough to prototype.

The strongest part of the proposal is v1: an autonomous hero that reacts to actual Cities: Skylines II crime simulation state. The highest-risk areas are custom animated character rendering and later web-swing traversal, not basic crime detection.

## Confirmed foundations

### Code mods can participate in the simulation

Cities: Skylines II uses Unity ECS extensively. Mods can register custom systems into the game's update phases and query or modify entities/components.

Useful references:

- Paradox Interactive, Code Modding dev diary:
  https://www.paradoxinteractive.com/games/cities-skylines-ii/modding/dev-diary-3-code-modding
- Cities2Modding ECS guide:
  https://github.com/Cities2Modding/wiki/blob/master/src/guides/ecs.md
- Official-mod example analysis:
  https://github.com/Cities2Modding/wiki/blob/master/src/reverse-engineering/official-mod-example.md

### Crime exists as entity/component state

Public code from Crime Remover demonstrates that a mod can work with:

- `Game.Citizens.Criminal`;
- `CriminalFlags`, including states such as `Robber`, `Planning`, `Arrested`, and `Prisoner`;
- `Game.Buildings.CrimeProducer`;
- police/emergency request entities;
- `CurrentTransport`;
- `Resident`;
- `Target`;
- `TravelPurpose` and `Purpose.GoingToJail`.

Reference:

https://github.com/roll-w/CSL2-CrimeRemover

This does not prove every desired behavior is safe or stable across game versions. It does establish that crime-related simulation state is accessible from code mods.

## Feasibility gates

The project should answer these in order.

### Gate A: criminal discovery

Can our system reliably enumerate active criminals without materially affecting simulation performance?

Pass condition:

- active criminal entities can be identified;
- already arrested/prisoner entities can be excluded;
- the query remains stable on a large city.

### Gate B: world position

Can an active criminal be resolved to a useful current world position?

Potential sources include the citizen entity itself, current transport/resident state, targets, and related object entities.

Pass condition:

- a selected criminal can be followed while moving;
- the reported location corresponds visually to the criminal or their current representation.

### Gate C: persistent hero entity

Can we maintain one persistent mod-owned hero state across normal simulation updates, save/load, pause, and speed changes?

The first version does not require a normal citizen entity. A mod-owned ECS entity or state object is acceptable.

### Gate D: visible representation

Can we render a visible hero at arbitrary world coordinates?

Initial pass condition:

- a debug primitive, marker, or repurposed game object can be shown and moved.

Final character rendering is a separate problem and must not block the first simulation prototype.

### Gate E: movement

Can the hero move through or over the city predictably without corrupting pathfinding or simulation state?

V1 may use simplified movement. It does not need citizen pathfinding if a mod-owned movement controller proves more reliable.

### Gate F: intervention

Can the mod resolve one selected criminal in a bounded way that cooperates with the normal justice simulation?

Candidate approaches:

1. mark the criminal arrested and let normal systems continue;
2. create/trigger an appropriate police-service request;
3. hand off to an existing police workflow after the hero animation completes.

We should prefer the least invasive approach.

## Character rendering risk

Official custom-asset support now exists for Cities: Skylines II, including buildings and props:

https://www.paradoxinteractive.com/games/cities-skylines-ii/news/adding-custom-assets

That does not by itself establish a supported workflow for an arbitrary custom animated humanoid with a completely new animation set.

Therefore the project should treat character rendering as an independent technical spike.

Possible architecture:

```text
Hero ECS/simulation state
        |
        +-- target / position / velocity / state
        |
        v
mod-owned render representation
        |
        +-- mesh
        +-- materials
        +-- animator
        +-- effects
```

The gameplay model should not depend on the hero being implemented as a normal cim.

## Web-swinging feasibility

Swinging is a later-stage feature.

The physics itself is tractable: gravity, rope-length constraint, tangential velocity, release, and reattachment. The harder problem is autonomous anchor selection.

A viable controller will need to consider:

- target direction;
- nearby building geometry;
- anchor height;
- line of sight;
- current velocity;
- minimum/maximum rope length;
- next-anchor reachability;
- collision risk;
- recovery when no suitable anchor exists.

V2 should therefore be approached as a traversal/path-planning project rather than only a rope-physics feature.

## Performance constraints

Cities: Skylines II simulations can contain very large entity counts. Avoid broad per-frame scans.

Expected strategy:

- use narrow `EntityQuery` definitions;
- update hero targeting at a lower cadence than rendering;
- cache the selected target;
- avoid allocation-heavy loops;
- use jobs/Burst only where they materially help;
- profile on a large city before calling a milestone complete.

## Compatibility risk

Game internals can change between patches.

Mitigations:

- isolate game-specific component access behind narrow adapters/systems;
- avoid unnecessary Harmony patches;
- document every dependency on game types/components;
- fail closed when an expected component is absent;
- keep simulation mutation minimal and explicit.

## IP/distribution constraint

The technical project should remain independent from Spider-Man-specific copyrighted assets.

Do not commit or distribute ripped models, animations, textures, logos, audio, or other proprietary assets. Placeholder/original hero assets should be sufficient for development and public builds.
