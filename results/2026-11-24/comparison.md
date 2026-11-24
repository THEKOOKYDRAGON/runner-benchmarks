# Monk CI vs Blacksmith — measured benchmark results

Five new workload pairs ran on 24 November 2026 IST. All ten jobs passed, exactly one attempt per provider per workload. No benchmarks ran locally. Workflows and all commands are YAML in this repository.

**Finding:** Blacksmith had shorter workload time in all five new pairs. Kafka remains Monk CI's strongest existing measured win: 148.22s vs 171.61s, or 13.6% less build time. Pulsar broker assembly was 5.0% shorter on Monk CI. These earlier results are explicitly dated below; none were rerun for this batch.

## Workload time — lower is better

Both providers: 32 vCPUs, Ubuntu 24.04, x86-64. Times include the downloads made by the workload command and exclude queue, checkout, toolchain setup, verification and artifact upload. Bold marks the lower time. Each cell is one observation, not an average or percentile.

| Workload | Run date (IST) | Blacksmith | Monk CI | Shorter duration |
|---|---|---:|---:|---|
| [Kafka](https://github.com/THEKOOKYDRAGON/benchmark-kafka/actions/runs/44594019819) | 2026-11-20 | 2m 51.61s | **2m 28.22s** | Monk CI (13.6% less time) |
| [Pulsar broker assembly](https://github.com/THEKOOKYDRAGON/pulsar/actions/runs/44600330752) | 2026-11-20 | 1m 43.38s | **1m 38.23s** | Monk CI (5.0% less time) |
| [Helix](https://github.com/THEKOOKYDRAGON/benchmark-helix/actions/runs/44594033728) | 2026-11-20 | 50.54s | **50.12s** | Monk CI (0.8% less time) |
| [Zed](https://github.com/THEKOOKYDRAGON/benchmark-zed/actions/runs/44594026885) | 2026-11-20 | **10m 56.81s** | 11m 00.49s | Blacksmith (0.6% less time) |
| [Traefik](https://github.com/THEKOOKYDRAGON/benchmark-traefik/actions/runs/44594041372) | 2026-11-20 | **1m 09.17s** | 1m 14.72s | Blacksmith (7.4% less time) |
| [Lucene Assembly](https://github.com/THEKOOKYDRAGON/runner-benchmarks/actions/runs/45332944043) | 2026-11-24 | **1m 08.10s** | 1m 16.74s | Blacksmith (11.3% less time) |
| [Lucene Test Compilation](https://github.com/THEKOOKYDRAGON/runner-benchmarks/actions/runs/45332948095) | 2026-11-24 | **1m 13.59s** | 1m 25.44s | Blacksmith (13.9% less time) |
| [Pulsar Broker Test Compilation](https://github.com/THEKOOKYDRAGON/runner-benchmarks/actions/runs/45332953160) | 2026-11-24 | **1m 49.36s** | 1m 50.11s | Blacksmith (0.7% less time) |
| [TypeScript Build](https://github.com/THEKOOKYDRAGON/runner-benchmarks/actions/runs/45332958281) | 2026-11-24 | **12.65s** | 15.91s | Blacksmith (20.5% less time) |
| [TypeScript Docker Build](https://github.com/THEKOOKYDRAGON/runner-benchmarks/actions/runs/45332962922) | 2026-11-24 | **19.63s** | 52.94s | Blacksmith (62.9% less time) |

Percent reductions use the slower duration as denominator. The percentage is a reduction in time, not a throughput increase. The table includes all five new workloads and the latest completed pair for each of the five earlier workloads; it does not select the best attempt per provider.

## What each new job does

- **[Lucene Assembly](https://github.com/THEKOOKYDRAGON/runner-benchmarks/actions/runs/45332944043)** — [pinned source](https://github.com/apache/lucene/tree/51190f35a16d2ce433139abfe0fd8365791b352a); `./gradlew --no-build-cache --console=plain assemble`.
- **[Lucene Test Compilation](https://github.com/THEKOOKYDRAGON/runner-benchmarks/actions/runs/45332948095)** — [pinned source](https://github.com/apache/lucene/tree/51190f35a16d2ce433139abfe0fd8365791b352a); `./gradlew --no-build-cache --console=plain testClasses`.
- **[Pulsar Broker Test Compilation](https://github.com/THEKOOKYDRAGON/runner-benchmarks/actions/runs/45332953160)** — [pinned source](https://github.com/apache/pulsar/tree/4c493c05b3eb28d0247948d47e0b3d30fc699f49); `./gradlew --no-build-cache --console=plain :pulsar-broker:testClasses`.
- **[TypeScript Build](https://github.com/THEKOOKYDRAGON/runner-benchmarks/actions/runs/45332958281)** — [pinned source](https://github.com/microsoft/TypeScript/tree/c63de15a992d37f0d6cec03ac7631872838602cb); `npm ci --no-audit --no-fund && npm run build`.
- **[TypeScript Docker Build](https://github.com/THEKOOKYDRAGON/runner-benchmarks/actions/runs/45332962922)** — [pinned source](https://github.com/microsoft/TypeScript/tree/c63de15a992d37f0d6cec03ac7631872838602cb); `docker build --no-cache --pull --progress=plain -f "$RUNNER_TEMP/Benchmark.Dockerfile" -t benchmark-typescript .`.

Lucene assembly produces library artifacts (140 executed Gradle tasks on both). Lucene test compilation compiles test code and dependencies (161 tasks on both), without running tests. Pulsar broker test compilation builds broker test classes and required modules (100 tasks on both), without running tests. TypeScript installs locked dependencies and builds both compiler and test code, without executing the test suite. Docker performs a multi-stage build of the same TypeScript revision, including base-image pull, npm install, compiler/test-code build, and final image assembly. The compiler's version is verified as 5.9.3 outside the timed section.

## Queue and whole-job durations

These timings matter for developer wait time. Queue = GitHub job created → started; execution = started → completed; total = created → completed. Total includes queue, setup, workload, verification and cleanup.

| Workload | Monk CI queue | Blacksmith queue | Monk CI execution | Blacksmith execution | Monk CI total | Blacksmith total |
|---|---:|---:|---:|---:|---:|---:|
| Kafka | 10s | 7s | 162s | 186s | 172s | 193s |
| Pulsar broker assembly | 12s | 36s | 112s | 119s | 124s | 155s |
| Helix | 57s | 6s | 64s | 65s | 121s | 71s |
| Zed | 65s | 7s | 696s | 692s | 761s | 699s |
| Traefik | 152s | 7s | 96s | 84s | 248s | 91s |
| Lucene Assembly | 8s | 8s | 91s | 84s | 99s | 92s |
| Lucene Test Compilation | 48s | 7s | 99s | 89s | 147s | 96s |
| Pulsar Broker Test Compilation | 53s | 7s | 122s | 124s | 175s | 131s |
| TypeScript Build | 57s | 6s | 32s | 31s | 89s | 37s |
| TypeScript Docker Build | 161s | 7s | 66s | 38s | 227s | 45s |

For the new batch Monk CI's queue ranged from 8s to 161s; Blacksmith's from 6s to 8s. GitHub timestamps alone do not identify the underlying provisioning cause.

## Configuration and limitations

- New workflow commit: [93c67ffb3b99a0927acb7e6ab4f5695292fb9c2e](https://github.com/THEKOOKYDRAGON/runner-benchmarks/tree/93c67ffb3b99a0927acb7e6ab4f5695292fb9c2e).
- Java: Temurin `21.0.13+9.0.LTS` resolved successfully on both. Node: `22.14.0`. Docker base: `node:22.14.0-bookworm-slim@sha256:1c18d9ab3af4585870b92e4dbc5cac5a0dc77dd13df1a5905cea89fc720eb05b`.
- Both providers passed `nproc == 32`. Hardware is not identical: Monk CI reports AMD EPYC 9B45 and about 121.8 GiB visible RAM; Blacksmith reports AMD EPYC and about 113.4 GiB. Provider images, disks, network paths and Docker versions can differ.
- Fresh Gradle/npm directories; no restored build/dependency caches. Docker uses `--no-cache --pull`. Provider-side download acceleration and infrastructure caches are not controlled; this does not prove identical cold infrastructure.
- Five workflows were dispatched close together. Each provider received the same workload; queue measurements reflect this batch, not a general burst-queue percentile.
- Docker uses the whole checked-out source as context (~451 MB), including checkout metadata, without a custom .dockerignore. This is a specific packaging workload, not a universal Docker benchmark. Monk CI's log reports a 27.4s WORKDIR stage versus ~0.1s on Blacksmith; stages can overlap, so these times must not be summed. The log does not establish why that stage stalled.
- No statistically reliable ranking follows from one sample. Helix's 0.42s advantage and Pulsar test compilation's 0.75s difference are particularly small. Historical Helix and Traefik changed winner between batches.
- No GitHub-hosted baseline, cache-transfer benchmark, or queue p50 was measured in this batch. The website's older GitHub speedup figures use different configurations and are not inserted into this table.
- Logs, hardware and measurements are attached to each linked run (30-day retention), with durable downloaded copies in the local evidence directory. The JSON/CSV alongside this report retain the timing summary.

For public messaging, the supported statement is “Kafka build completed in 148.22s on Monk CI versus 171.61s on Blacksmith in this single matched run.” A broad “Monk CI is faster than Blacksmith” claim is not supported by these results.
