# Throughline

**The reliability layer for long-running LLM agents. Trace every step, score session-level behavior, and catch the failures at turn 40 that looked fine at turn 4.**

[demo GIF goes here once the v0 slice runs]

> Status: early and in active development. Building the thin vertical slice in the open. Star or watch to follow along.

---

## The problem

Agents are being deployed everywhere, and almost nobody can see inside them over a long session.

A model that behaves perfectly at turn 4 quietly breaks by turn 40. It forgets a constraint you set at the start. It gets agreeable and stops pushing back. It resolves a task before the work is done. Its voice flattens into a generic assistant. Each individual turn looks fine, so turn-level monitoring catches none of it. The failure is in the trajectory, not the turn.

Throughline measures the trajectory, with the discipline of production infrastructure.

## What it does

```
agent run ──► OTel instrumentation ──► traces + cost/latency ──► dashboard
                                          │
                                          └─► behavioral analyzers ──► session reliability scores
```

| Layer | What it provides |
|-------|------------------|
| **Tracing** | OpenTelemetry spans for every agent step and tool call. Drops in front of an existing agent without rewriting it. |
| **Cost & latency** | Token spend, $/run, and p99 latency per agent run |
| **Behavioral eval** | Session-level scoring for drift, sycophancy, premature resolution, persona breaks |
| **Serving** | A FastAPI surface so other tools can submit runs and read scores |

The eval layer scores across the **whole session**, not turn by turn, which is the part existing tooling does not do.

## Quickstart

> Target experience for v0. Not wired up yet, this is the shape it is being built toward.

```bash
pip install throughline
```

```python
from throughline import trace

# wrap your existing agent, no rewrite needed
agent = trace(my_agent)

# run it normally
agent.run("...")

# traces, cost, and behavioral scores show up on the dashboard
```

Then open the dashboard at `localhost:3000` to see traces, cost per run, and session reliability scores.

## The failure modes it measures

- **Constraint drift** — a rule set at turn 2 quietly stops being followed by turn 30.
- **Sycophancy creep** — the agent starts agreeing with the user instead of holding its reasoning.
- **Premature resolution** — tension or open questions get closed off too early.
- **Persona / voice drift** — a defined role slowly flattens into a generic assistant.
- **Mode collapse** — outputs converge toward repetition and lose variety.

The taxonomy behind these checks comes from nine years of studying narrative-AI behavior, where long-horizon coherence has always been the thing that separates a good session from a broken one. Those same failure modes are now agent-reliability problems.

## Stack

Python · FastAPI · OpenTelemetry · Docker · Kubernetes · Grafana / Prometheus

## Roadmap

**v0 (current)** — the thin vertical slice
- [ ] Instrument one agent framework, emit OpenTelemetry traces
- [ ] Traces to a dashboard, with cost and latency per run
- [ ] One behavioral check from the taxonomy, running end to end
- [ ] One-command run, Docker, demo GIF

**v1** — depth
- [ ] Additional behavioral analyzers from the full taxonomy
- [ ] LLM-as-judge scoring with documented bias controls, plus reward-model scoring
- [ ] Kubernetes manifests and Terraform
- [ ] FastAPI serving layer with auth

**v2** — if it has legs
- [ ] Multi-framework support
- [ ] Alerting on cost and behavioral regressions
- [ ] A small, curated benchmark set others can run against

## Status & contributing

A work in progress, built in the open. Issues and ideas welcome. If you work on agent reliability, long-horizon evaluation, or LLM observability, I would genuinely like to compare notes.

## License

MIT
