# ongrid

Open-source AIOps. Install a lightweight `ongrid-edge` agent on your hosts, and
troubleshoot in natural language from the cloud — "show me the load", "what's
dropping packets", "find the runaway process" — answered by an LLM agent that
can read metrics, logs, traces, topology, and your registered source repos.

> Status: early / evolving. APIs and layout may change.

## How it works

```
hosts ──ongrid-edge── tunnel ──► ongrid (cloud)
 (metrics/logs/traces,            ├─ manager: edge + telemetry + AIOps agent
  read-only host tools)           ├─ knowledge base (RAG) + code reading
                                  └─ web UI (chat + dashboards)
```

- **edge** (`ongrid-edge`): a single agent per host; collects metrics/logs/traces
  and exposes read-only host inspection tools over a multiplexed tunnel.
- **cloud** (`ongrid`): the manager + an LLM coordinator that dispatches to
  specialist sub-agents and tools (PromQL / LogQL / TraceQL / topology /
  knowledge-base search / source-code reading) to answer ops questions.

## Build

```bash
# Go binaries → bin/ongrid, bin/ongrid-edge
make build            # or: make build-ongrid / build-ongrid-edge

# Frontend SPA
cd web && npm ci && npm run build

# Tests / lint / architecture boundaries
make test
make lint
make arch-lint        # enforces BC boundaries (iam / manager / edgeagent don't cross-import)
```

`make help` lists all targets. (Note: the cloud build embeds a local ONNX
embedder via CGO, so `ongrid` is built with `CGO_ENABLED=1`.)

## Repo layout (top level)

```
cmd/        # ongrid (cloud) + ongrid-edge entrypoints
api/        # proto definitions, grouped by bounded context
internal/
  iam/        # auth / JWT / org / user
  manager/    # edge + telemetry + aiops subdomains
  edgeagent/  # host collection & read-only tool handlers
  pkg/        # shared: tunnel / llm / prom / log / conf ...
web/        # React SPA (chat + dashboards)
agents/     # LLM agent persona definitions
skills/     # agent skill bundles
scripts/    # dev/build helpers
```

## License

[Apache-2.0](LICENSE).
