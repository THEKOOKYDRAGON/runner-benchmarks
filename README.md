# Runner benchmarks

Manually dispatched, single-sample comparisons on Monk CI and Blacksmith Ubuntu 24.04 x86-64 runners with 32 vCPUs each. Source commits and toolchains are pinned in each YAML workflow. No restored dependency/build caches. Gradle distributions and dependency downloads are included in measured workload time. Queue, setup, verification and upload are separate.

Five independent workflows: Lucene assembly, Lucene test compilation, Pulsar broker test compilation, TypeScript compiler/test-code build, and a multi-stage Docker build of TypeScript. Compilation workflows do not execute tests. Docker timing includes pulling the pinned base image, installing dependencies, compilation, and image assembly.

All outcomes must be retained, including failures and workloads where Blacksmith wins. One observation is exploratory, not evidence of a stable statistical advantage. Hardware details, exact source, tool versions and logs are uploaded by each job.

## Results

[Full comparison and methodology](results/2026-11-24/comparison.md) · [Comparison table](results/2026-11-24/comparison.html) · [CSV](results/2026-11-24/comparison.csv)
