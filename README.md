# twin-arch

> Portfolio hub for the `twin-*` digital-twin series — six repos that build
> one industrial-IoT stack, one layer at a time. No source code here; this
> is the map.

## The series

| Repo | Layer | What it proves | Status |
|------|-------|----------------|--------|
| [`twin-hello`](https://github.com/joshikeyur01/twin-hello) | L1+L2+L5 | A minimum viable twin: UR5 in Gazebo → MQTT → InfluxDB → Grafana | ✅ complete |
| [`twin-services`](https://github.com/joshikeyur01/twin-services) | L4 | Four containerised services, versioned contracts, scripted chaos test | ✅ complete |
| [`twin-aas`](https://github.com/joshikeyur01/twin-aas) | L3 | BaSyx AAS vs OPC-UA vs raw MQTT, benchmarked with a stopwatch | ✅ complete |
| [`twin-anomaly`](https://github.com/joshikeyur01/twin-anomaly) | L4 | Fault injection → labelled dataset → ML detection | ✅ complete |
| [`twin-fleet`](https://github.com/joshikeyur01/twin-fleet) | L4 | The same stack at N robots, with load tests that find the limits | ✅ complete |
| [`twin-turbofan`](https://github.com/joshikeyur01/twin-turbofan) | — | RUL prediction: the predictive-maintenance archetype | 🚧 pipeline correct, awaiting real C-MAPSS data |

**Adjacent, independent:**

| Repo | What it is | Status |
|------|-----------|--------|
| [`openontology`](https://github.com/joshikeyur01/openontology) | Open-core digital-twin platform — Go ontology resolution engine, Redis state, streaming telemetry | 🚧 active |
| [`oss-recon`](https://github.com/joshikeyur01/oss-recon) | Upstream bug reproductions and fixes for `asyncua`, `ccsdspy`, ROS 2 docs | 📓 log |

## How they fit together

See [`DEPENDENCIES.md`](DEPENDENCIES.md) for the dependency graph, the
evidence behind every edge, and the 5-layer model each repo maps onto.

Short version: `twin-hello` establishes the loop. `twin-services`
decomposes it into the architectural spine. `twin-aas`, `twin-anomaly`,
and `twin-fleet` each extend that spine along one axis — semantics,
intelligence, scale. `twin-turbofan` is a deliberate outlier: a different
asset class, proving the patterns generalise beyond one robot arm.

## Shared conventions

Every `twin-*` repo inherits the same skeleton, so once you can navigate
one you can navigate all six:

- **Stack:** Python 3.12, Pydantic v2, FastAPI, `uv`, `just`, Docker Compose
- **Gates:** `ruff`, `mypy --strict`, `pytest`, GitHub Actions, pre-commit
- **Docs:** `docs/context/{VISION,ARCHITECTURE,STYLE,ROADMAP}.md` + `docs/adr/`
- **Decisions:** any new dependency or cross-layer choice gets an ADR
- **Licence:** Apache-2.0 (`openontology` is open-core: Apache-2.0 + BUSL-1.1)

Every repo runs standalone with `just up` — no shared package to install
first. That independence is bought with deliberate vendoring, tracked in
each repo's `CHANGELOG.md`.

## Start here

New to the series? Read [`twin-hello`](https://github.com/joshikeyur01/twin-hello)
first, then follow the reading order in [`DEPENDENCIES.md`](DEPENDENCIES.md).

## Licence

Apache-2.0 — see [`LICENSE`](LICENSE).
