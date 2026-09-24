# Development and testing

## Development order

Work from simulation risk outward:

```text
load mod
  -> detect criminal
  -> locate criminal
  -> create debug hero
  -> reach criminal
  -> resolve incident
  -> improve movement
  -> improve presentation
  -> add swinging
```

Do not reverse this order.

## Initial implementation rules

1. Use placeholders for rendering.
2. Keep crime mutation in one system.
3. Keep game-specific component interpretation in one observation layer.
4. Log state transitions while the project is experimental.
5. Add a setting to disable gameplay behavior when practical.
6. Prefer ECS access to broad Harmony patches when ECS already exposes the required state.
7. Never assume an entity reference remains valid across frames without checking.

## Source references

Useful living references:

- Paradox code-modding overview:
  https://www.paradoxinteractive.com/games/cities-skylines-ii/modding/dev-diary-3-code-modding
- Cities2Modding wiki:
  https://github.com/Cities2Modding/wiki
- CS2 Modding Handbook:
  https://github.com/BrokeAssSoftware/cs2-modding-guide
- Crime Remover:
  https://github.com/roll-w/CSL2-CrimeRemover

When borrowing implementation techniques from another project, verify its license and record the source/commit if code is copied or adapted.

## Testing matrix

Every meaningful gameplay milestone should be tested under at least:

### Simulation state

- paused;
- 1x;
- accelerated simulation;
- no active crime;
- one active criminal;
- multiple simultaneous criminals;
- target disappears before arrival;
- criminal becomes arrested by police first.

### City topology

- dense downtown;
- low-density suburb;
- sparse/outlying development;
- separated districts;
- large city with high entity count.

### Lifecycle

- new game;
- existing save;
- save;
- load;
- return to menu;
- reopen city;
- enable/disable mod where supported.

## Assertions for targeting

For each target acquisition:

- entity exists;
- target is still criminal;
- target is not already arrested/prisoner;
- position resolves;
- hero has no higher-priority committed target;
- a timeout/recovery path exists.

## Assertions for intervention

Immediately before changing game state:

- entity still exists;
- expected components are present;
- criminal has not already been resolved;
- mutation is limited to the selected incident;
- failure leaves the simulation in a valid state.

## Performance testing

Do not judge performance only in a small test city.

Record at minimum:

- criminal query count;
- candidate count;
- target-refresh frequency;
- worst/average update duration where measurable;
- allocations caused by the mod;
- behavior in a high-population save.

Avoid converting large queries to temporary arrays every frame unless profiling shows the cost is acceptable.

## Debug tooling

Useful temporary debug features:

- draw/mark every detected criminal;
- highlight current target;
- show resolved target coordinates;
- show hero state;
- show distance to target;
- force target reacquisition;
- spawn/mark a test criminal only in a controlled development mode if needed.

Debug tooling should be removable or gated before release.

## Definition of done for a milestone

A milestone is not complete because the happy path worked once.

It is complete when:

- acceptance criteria are written;
- the behavior works in-game;
- common invalid-state paths recover;
- no obvious unrelated simulation breakage is observed;
- logs do not show repeated exceptions;
- performance is acceptable for the milestone;
- implementation assumptions are documented.

## Asset policy

Do not commit ripped or otherwise unauthorized game/film/comic assets.

Development assets should be:

- self-created;
- appropriately licensed;
- public-domain/CC assets compatible with distribution; or
- simple placeholders.

The core mod must remain usable with an original character presentation.
