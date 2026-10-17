---
name: code-optimization
description: Use this skill whenever the user asks to optimize code or make an application faster, more reliable under load, lower-latency, higher-throughput, or more resource-efficient. Trigger for profiling and performance audits; CPU or memory hot spots; slow endpoints; database queries, N+1 patterns, indexes, and round trips; or backend/API call latency, fan-out, and concurrency. Establish evidence, target the real bottleneck, and make the smallest maintainable change that improves the relevant outcome without weakening correctness, reliability, or YAGNI.
---

# Code Optimization

Improve user-visible performance and reliability while keeping the code understandable and the operational cost proportionate. Optimize the workload that exists; do not add complexity for hypothetical future scale.

## Workflow

1. **Define the objective and guardrails.** Identify the relevant endpoint, job, query, workload, and desired outcome. Choose only the metrics that matter: for example, p95/p99 latency, throughput, CPU, memory, allocation rate, database query count, dependency round trips, error rate, or saturation. Preserve behavior, consistency, security, and existing error contracts. Ask one focused question only when a missing target or workload prevents a sound choice; otherwise infer what you can from the request and code.
2. **Establish a baseline.** Use existing traces, profiles, query plans, metrics, tests, or a representative benchmark. Keep input, environment, and concurrency comparable before and after. Do not claim a speedup from intuition or a single noisy timing. Avoid running load tests or expensive query analysis against production without authorization.
3. **Find the bottleneck.** Trace the end-to-end critical path and use profiling or instrumentation to distinguish CPU work, blocking, database time, network waits, serialization, lock contention, and queueing. Inspect the actual query/call pattern and representative data. Prefer evidence over broad cleanup or micro-optimization. If the bottleneck is not established, report the uncertainty and propose the smallest useful measurement before making invasive changes.
4. **Choose the smallest effective change.** Optimize one material bottleneck at a time when practical. Preserve output, ordering, failure behavior, transaction semantics, and security checks. Do not trade away correctness or reliability for a benchmark win. Follow the repository's conventions and avoid unrelated refactors.
5. **Verify both outcomes.** Run relevant correctness tests and compare the same representative workload before and after. Check that the target metric improved and that guardrails—especially tail latency, errors, memory, and dependency saturation—did not regress. If a meaningful comparison is unavailable, say so and avoid presenting an unmeasured change as a proven optimization.
6. **Report the result.** Briefly state the bottleneck and evidence, the change made or recommended, the validation and measured impact (or why it could not be measured), and any material trade-off. For an audit or plan request, prioritize findings by evidence, likely impact, effort, and risk instead of changing code.

## Database and data-access optimization

- Count queries and inspect ORM/database access patterns. Remove N+1 reads or writes with an appropriate join, batch, or set-based operation; verify the query count and preserve result ordering and missing-record behavior.
- Fetch only needed columns and rows. Use pagination or streaming for large result sets; push suitable filtering and aggregation to the database instead of loading large datasets into application memory.
- Use the query plan and real predicates to justify indexes. Check selectivity, sort/join needs, existing indexes, and write/storage overhead. Do not add speculative or duplicate indexes. Validate the plan and latency on representative data; use execution-based plan analysis only in a safe environment.
- Reduce unnecessary round trips and keep transactions and lock scopes no larger than required. Do not weaken constraints, isolation, validation, or consistency as a shortcut.
- Add caching only when measured reuse justifies it. Define freshness, invalidation, key scope, and failure behavior; do not introduce a cache as a default response to a slow query.

## Backend and service-call optimization

- Remove redundant calls, batch compatible requests, and parallelize only calls that are truly independent. Keep concurrency bounded, preserve required ordering/dependencies, and account for downstream capacity.
- Reuse clients and connection pools. Set deadlines/timeouts that fit the request budget and propagate cancellation where supported.
- Retry only transient failures when the operation is safe to retry. Bound attempts and total retry time, use backoff with jitter, and avoid retrying at multiple layers in ways that amplify load. Do not add retries, circuit breakers, or queues without a demonstrated failure mode they address.
- Reduce oversized payloads or repeated serialization only when they are material to the measured path. Keep required service failure semantics intact; make graceful degradation explicit for optional dependencies.

## Runtime, reliability, and maintainability

- Prefer the highest-leverage fix supported by evidence: improve algorithmic complexity, remove repeated work, reduce unnecessary allocation/copying, or address blocking and contention before low-level micro-optimizations.
- Keep resource use bounded under concurrency: consider memory, queues, pools, fan-out, timeouts, cancellation, and backpressure. Ensure failures do not create retry storms, unbounded work, or resource leaks.
- Treat performance and reliability as system-level outcomes. A faster caller that overloads a database or increases error rates is not an optimization.
- Apply YAGNI: do not add a dependency, abstraction, cache, index, async layer, configuration option, or architecture change for a hypothetical bottleneck. Prefer a clear local change; if complexity is required, explain the measured benefit and its maintenance cost.
- Do not remove input validation, authorization, observability, or safety checks to save negligible time. Do not replace readable code with a harder-to-maintain micro-optimization unless the measured gain is material.

## Verification notes

- Compare equivalent workloads with repeatable runs and enough samples to distinguish signal from noise. Use distributions (especially tail latency) where relevant, not only averages.
- Verify functional behavior and edge cases affected by batching, caching, parallelism, pagination, retries, or concurrency. Add focused tests where they protect a meaningful invariant, such as query count, ordering, timeout behavior, or optional-service fallback.
- Re-profile after the change: bottlenecks move, and a local improvement can shift cost to another service or resource.
- If the optimization does not produce a material improvement or its complexity outweighs the benefit, revert it or recommend against it.
