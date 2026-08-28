# Portfolio dependencies

How the repos relate. Every edge below is backed by a real code or doc
reference, not an aspiration — the "evidence" column says where to look.

## Dependency graph

```
                        twin-hello
                    (L1+L2+L5 baseline)
                            │
                  bridge/ vendored ──┐
                            │        │
                        twin-services ◄── the architectural spine
                    (contracts/ + 4 services)
                            │
        ┌───────────────────┼───────────────────┐
        │                   │                   │
   FK vendored      bridge + 3 svcs        stack generalised
        │            vendored, unchanged     to N robots
        ▼                   ▼                   ▼
    twin-aas          twin-anomaly          twin-fleet
   (L3 models)       (L4 intelligence)      (L4 scale)


  twin-turbofan          openontology            oss-recon
 (no shared code —      (separate platform,   (upstream fixes to
  domain archetype)      Go open-core)         deps we consume)
```

## Edges, with evidence

| From | Depends on | What is actually shared | Evidence |
|------|-----------|------------------------|----------|
| `twin-services` | `twin-hello` | `bridge/` (DDS↔MQTT), extended with the cmd→ROS 2 path | `bridge/src/bridge/__init__.py:1`, `CHANGELOG.md:17` |
| `twin-aas` | `twin-services` | forward-kinematics routine used by `feeder/` | `README.md:11`, `docs/context/ARCHITECTURE.md:59` |
| `twin-anomaly` | `twin-services` | `bridge/`; `state-svc`, `command-svc`, `viz-svc` unchanged | `README.md:86-93` |
| `twin-fleet` | `twin-services` | whole stack generalised from 1 → N robots via a registry | `README.md:3,13,22` |
| `twin-turbofan` | — | none. Different asset, different dataset (C-MAPSS) | no cross-references |
| `openontology` | — | none. Independent Go platform | no cross-references |
| `oss-recon` | — | upstream bug fixes to `asyncua`, `ccsdspy`, ROS 2 docs | `docs/PR_LOG.md` |

**Vendoring is deliberate, not laziness.** Each repo stands alone and
runs with one `just up`. Copying the bridge instead of publishing a shared
package keeps every repo independently clonable, which is the point of a
portfolio — at the cost of drift, which the CHANGELOGs track.

## Reading order

1. **`twin-hello`** — the minimum viable twin. Start here.
2. **`twin-services`** — decomposition into services with versioned contracts.
3. **`twin-aas`** — three information models, benchmarked against each other.
4. **`twin-anomaly`** — ML anomaly detection with fault injection.
5. **`twin-fleet`** — the same stack at fleet scale.
6. **`twin-turbofan`** — a different archetype: RUL prediction.

`openontology` and `oss-recon` are independent and can be read at any point.

## The 5-layer stack

Every `twin-*` repo maps its components onto the same five layers, so the
portfolio reads as one system rather than six unrelated demos.

| Layer | What it is | Repo that owns it |
|-------|-----------|-------------------|
| L5 Application | dashboards, 3D viewers | `twin-hello` |
| L4 Services | decomposed services, ML, scale | `twin-services`, `twin-anomaly`, `twin-fleet` |
| L3 Information model | AAS, OPC-UA, raw topics | `twin-aas` |
| L2 Transport | ROS 2 DDS ↔ MQTT, gRPC | `twin-hello` |
| L1 Physical / simulated | UR5 in Gazebo; C-MAPSS engines | `twin-hello`, `twin-turbofan` |
