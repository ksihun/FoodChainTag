# FoodChainTag Unity Project Rules

## Project scope

FoodChainTag is a Unity 2D top-down multi-agent reinforcement-learning
simulation. The first milestone is deliberately small:

- A, B, and C agents with the cyclic relationship `A -> B -> C -> A`.
- Equal base movement stats for every species.
- Movement, prey pursuit, predator evasion, observations, actions, and an
  ML-Agents training loop.

Do not add energy, food, corpses, reproduction, obstacles, bushes, RNN, or
spectator UI until the Phase 1 training loop has been verified.

## Unity project conventions

- This directory is the Unity project root. Use Unity `6000.3.12f1` unless a
  deliberate project-wide upgrade is requested.
- Keep gameplay code under `Assets/Scripts/`, scenes under `Assets/Scenes/`,
  and reusable Unity assets under `Assets/Prefabs/` or `Assets/Materials/`.
- Commit Unity `.meta` files along with their assets. Never commit `Library/`,
  `Temp/`, `Logs/`, `obj/`, `Build/`, or `UserSettings/`.
- Make changes in small, coherent units. Preserve scene and prefab references
  when moving or renaming assets.

## Agent and simulation rules

- Keep agent observation and action logic separate from spectator-only UI.
- Treat a live prey, a live predator, food, and a corpse as distinct entity
  states when those systems are introduced.
- Use result-based rewards. Do not reward merely approaching a target, which
  can train endless pursuit without capture.
- Maintain energy conservation in later reproduction work: child energy comes
  from the parent and a configured reproduction loss, never from nowhere.

## Verification

For Unity code or editor changes, use a light verification loop:

1. Inspect only the relevant scene, scripts, and console state.
2. Apply the smallest coherent change.
3. Wait for compilation and check Unity Console errors.
4. Re-read the changed asset or script and report the evidence.

The `hera-agent-unity` CLI is installed, but FoodChaintag does not yet expose
an active editor connector. After the connector is added and this project is
open, start each editor-automation session with `doctor`, `status`, and a
compact tool listing. Do not use stale connections for other Unity projects.
