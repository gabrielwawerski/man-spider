# Architecture

## Goals

The architecture should support three progressively more complex products without rewriting the simulation core:

1. autonomous crime-response prototype;
2. polished autonomous hero;
3. swinging and optional player control.

The central separation is between **simulation intent** and **presentation/traversal**.

## Proposed layers

```text
Cities: Skylines II simulation
        |
        v
CrimeObservationSystem
        |
        v
HeroDecisionSystem
        |
        +-------------------+
        |                   |
        v                   v
HeroMovementSystem     HeroInterventionSystem
        |
        v
HeroPresentationSystem
```

## Core state

A mod-owned hero entity should carry only the state required by our systems.

Conceptual components:

```csharp
HeroTag
HeroState
HeroTransform
HeroVelocity
HeroTarget
HeroCooldown
HeroStatistics
```

Possible state machine:

```text
Uninitialized
    -> Patrol
    -> AcquireTarget
    -> Respond
    -> Arrive
    -> Intervene
    -> Handoff
    -> Patrol
```

Failure/recovery states should be explicit:

```text
TargetLost
MovementBlocked
NoValidPosition
Recovery
```

## Crime observation

`CrimeObservationSystem` is responsible for translating game-specific criminal state into a small internal representation.

It should not move the hero or mutate criminals.

Example internal record:

```text
Incident
- criminal entity
- position
- flags
- detectedAt
- priority
- stillValid
```

Responsibilities:

- query candidates;
- exclude arrested/prisoner/deleted entities;
- resolve candidate positions;
- expose a bounded candidate set;
- invalidate targets that cease to exist.

## Target selection

`HeroDecisionSystem` should choose one incident at a time.

Initial scoring can remain simple:

```text
score =
    severityWeight
  + ageWeight
  - distanceWeight
```

V1 does not need sophisticated AI. Deterministic, debuggable behavior is preferable.

Selection rules should prevent rapid target thrashing. Use a commitment window unless the target becomes invalid.

## Movement abstraction

Traversal must be behind an interface or strategy boundary.

Conceptually:

```text
IHeroMovementController
    Move(hero, target, deltaTime)
    CanReach(target)
    Reset()
```

Implementations:

```text
DebugDirectMovementController
SimpleAerialMovementController
RooftopTraversalController
WebSwingMovementController
PlayerMovementController
```

This boundary is important. V2 should replace movement behavior without changing crime observation or intervention semantics.

## Intervention

`HeroInterventionSystem` owns all mutations to crime-related game state.

Rules:

- mutate only the currently assigned target;
- validate the entity immediately before mutation;
- do not globally modify crime values;
- prefer normal game transitions where possible;
- log intervention decisions in debug builds.

The system should first attempt the least invasive supported transition. Directly forcing many unrelated components is a last resort.

## Presentation

Presentation should consume hero state and avoid owning gameplay truth.

```text
simulation position/state
        |
        v
presentation adapter
        |
        +-- placeholder marker
        +-- character mesh
        +-- animations
        +-- web visual
        +-- impact/effect visual
```

Animation events must not be the sole source of simulation correctness. If an animation fails, the state machine must recover.

## Update cadence

Different systems do not need the same frequency.

Suggested starting point:

- criminal candidate refresh: several times per simulated second;
- target validity check: frequent but lightweight;
- hero movement: every relevant simulation update;
- presentation interpolation: render/update cadence;
- statistics/UI: low frequency.

These values should be measured rather than assumed.

## Save/load

Persistent state worth saving:

- hero enabled/disabled;
- lifetime statistics;
- optional current state/target if restoration proves safe;
- configuration.

Current targets should be considered disposable. On load, reacquiring a target is safer than restoring an entity reference that may no longer be valid.

## Logging

Use structured categories:

```text
[ManSpider/Crime]
[ManSpider/Targeting]
[ManSpider/Movement]
[ManSpider/Intervention]
[ManSpider/Presentation]
```

Debug logging should make it possible to reconstruct:

- why a target was selected;
- why it was abandoned;
- how the position was resolved;
- what simulation mutation occurred.

## Compatibility boundary

Keep direct references to volatile game internals concentrated in adapters/systems rather than spread across the project.

For example:

```text
Game criminal components
        |
        v
CrimeObservationSystem
        |
        v
our Incident representation
```

This lowers the cost of adapting to a game update.
