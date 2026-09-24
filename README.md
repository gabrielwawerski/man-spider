# Man-Spider

A Cities: Skylines II code-mod experiment built around a persistent autonomous superhero operating inside the city simulation.

The project starts with simulation integration, not character art. The first goal is to prove that a mod can detect an active criminal, resolve that criminal to a world position, send a persistent hero agent to the incident, and affect the underlying crime state.

## Project direction

### v1: autonomous hero

The hero acts independently:

1. patrols the city;
2. detects active criminal activity from the game's ECS state;
3. selects a target;
4. travels to the incident;
5. performs a short intervention;
6. resolves or hands off the criminal to the normal justice simulation;
7. resumes patrol.

Police remain relevant. The hero should deal with individual incidents rather than apply a global crime-rate modifier.

### v2: traversal

Replace the simplified v1 locomotion with autonomous web-swinging:

- building-aware anchor selection;
- momentum-preserving swing physics;
- obstacle avoidance;
- multi-swing route planning;
- fallback rooftop traversal where swinging is not viable.

A later v2.x may allow the player to take control of the same movement system.

## Development principle

Do not start with the final model, animations, or web swinging.

The first implementation milestone is deliberately small:

```text
active criminal
    -> world position
    -> visible debug hero
    -> autonomous travel
    -> crime resolution
```

If this pipeline works reliably in a real city, the core concept is viable.

## Documentation

- [Feasibility and research](docs/FEASIBILITY.md)
- [Architecture](docs/ARCHITECTURE.md)
- [Roadmap](docs/ROADMAP.md)
- [Development and testing](docs/DEVELOPMENT.md)

## Technical basis

Cities: Skylines II uses Unity's Entity Component System extensively. Code mods can register their own systems and inspect or mutate game entities and components. Existing public mods demonstrate access to crime-related components such as criminals, crime-producing buildings, police requests, citizen transport state, and arrest/jail state.

The project should prefer direct ECS integration over Harmony patches where the required state is already exposed through entities and components.

## Name and third-party IP

The repository is intentionally structured so that simulation and traversal code do not depend on any specific copyrighted character, costume, logo, audio, or animation asset.

Development should use original or placeholder assets unless the contributor has the right to distribute the asset. Public releases should be able to substitute a generic/original hero presentation without changing the simulation architecture.

## Status

**Phase:** documentation / technical spike planning.

No gameplay implementation has been accepted yet.
