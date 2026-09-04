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


                            │
                     contracts vendored
                            │
                            ▼
                       twin-gateway ──── keyed telemetry.raw ──┐
                    (the seam, L2+L3)                          │
                                                               ▼
  twin-turbofan            oss-recon                     openontology
 (no shared code —      (upstream fixes to            (separate platform,
  domain archetype)      deps we consume)               Go open-core)
```

Until 2026-09-04 there were **no edges at all** between the `twin-*` series and
`openontology`. They were built to the same architecture and had never been
connected. `twin-gateway` is that connection, and the two new edges below are
the first ones to cross it.

## Edges, with evidence

| From | Depends on | What is actually shared | Evidence |
|------|-----------|------------------------|----------|
| `twin-services` | `twin-hello` | `bridge/` (DDS↔MQTT), extended with the cmd→ROS 2 path | `bridge/src/bridge/__init__.py:1`, `CHANGELOG.md:17` |
| `twin-aas` | `twin-services` | forward-kinematics routine used by `feeder/` | `README.md:11`, `docs/context/ARCHITECTURE.md:59` |
| `twin-anomaly` | `twin-services` | `bridge/`; `state-svc`, `command-svc`, `viz-svc` unchanged | `README.md:86-93` |
| `twin-fleet` | `twin-services` | whole stack generalised from 1 → N robots via a registry | `README.md:3,13,22` |
| `twin-gateway` | `twin-fleet` | `contracts/models.py` vendored verbatim: `JointTelemetry`, the topic grammar, `UR5_JOINT_NAMES` | `src/twin_gateway/contracts.py:1-20` records source commit `a0608af5`; `CHANGELOG.md` "Vendored" |
| `twin-gateway` | `openontology` | mirrors the `telemetry.raw` record shape and produces to it, keyed by `asset_id` | `src/twin_gateway/records.py:1-30`; `docs/VERIFIED.md` §2: 8,070 records, 0 unkeyed |
| `openontology` | `twin-gateway` | the UR5 rule set and graph revision 004 exist to serve this feed | `ops/neo4j/004_ur5_cell.cypher:1-60`; `services/resolution-engine/rules.go`; `docs/adr/0002` |
| `twin-turbofan` | — | none. Different asset, different dataset (C-MAPSS) | no cross-references |
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
7. **`twin-gateway`** — the seam, once both sides it joins are familiar.

`oss-recon` is independent and can be read at any point. `openontology` is a
separate platform, but read it before `twin-gateway`: the gateway's whole design
is a response to that engine's record shape and partitioning.

**The honest state of the new edges.** The translation is proven end to end
against a real broker, a real fleet and openontology's real engine code
(`twin-gateway/docs/VERIFIED.md`). The Kafka hop between them is not: Docker
could not be started on the machine this was built on, so `KafkaSink` has never
addressed a broker and `ontology.mutations` has never been written to. The edges
are real; one link in each is verified by construction rather than by running.

## The 5-layer stack

Every `twin-*` repo maps its components onto the same five layers, so the
portfolio reads as one system rather than six unrelated demos.

| Layer | What it is | Repo that owns it |
|-------|-----------|-------------------|
| L5 Application | dashboards, 3D viewers | `twin-hello` |
| L4 Services | decomposed services, ML, scale | `twin-services`, `twin-anomaly`, `twin-fleet` |
| L3 Information model | AAS, OPC-UA, raw topics, ontology sensor projection | `twin-aas`, `twin-gateway` |
| L2 Transport | ROS 2 DDS ↔ MQTT, gRPC, MQTT ↔ Kafka | `twin-hello`, `twin-gateway` |
| L1 Physical / simulated | UR5 in Gazebo; C-MAPSS engines | `twin-hello`, `twin-turbofan` |
