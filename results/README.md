# Performance Result Evidence

The committed raw JTL files in this directory are the canonical performance-test evidence. The numerical metrics in the main [README](../README.md) were verified against JMeter 5.6.3 HTML report statistics generated from these JTL files.

## Canonical result files

All recorded requests target `http://localhost:3000/api/status`. The documented execution environment ran Apache JMeter and the Application Under Test (AUT) on the same local machine.

| Scenario | Test plan | Canonical JTL | Workload configuration | Samples | UTC execution window recovered from JTL |
|---|---|---|---|---:|---|
| Smoke | [api-smoke.jmx](../tests/api-smoke.jmx) | No committed JTL | 1 user, 1-second ramp-up, 1 iteration | — | Not available |
| Baseline | [api-baseline.jmx](../tests/api-baseline.jmx) | [baseline-results.jtl](baseline-results.jtl) | 5 users, 5-second ramp-up, 10 iterations per user | 50 | 2026-09-23 17:43:25.174–17:43:29.111 UTC |
| Load | [api-load.jmx](../tests/api-load.jmx) | [load-results.jtl](load-results.jtl) | 20 users, 5-second ramp-up, 30-second total duration, 1-second pacing | 540 | 2026-09-24 14:50:11.788–14:50:40.496 UTC |
| Stress 100 | [api-stress.jmx](../tests/api-stress.jmx) | [stress-100-results.jtl](stress-100-results.jtl) | 100 users, 10-second ramp-up, 30-second total duration, 1-second pacing | 2,450 | 2026-09-24 14:55:17.891–14:55:46.757 UTC |
| Stress 200 | [api-stress-200.jmx](../tests/api-stress-200.jmx) | [stress-200-results.jtl](stress-200-results.jtl) | 200 users, 10-second ramp-up, 30-second total duration, 1-second pacing | 4,901 | 2026-09-24 14:58:46.862–14:59:15.788 UTC |
| Stress 500 | [api-stress-500.jmx](../tests/api-stress-500.jmx) | [stress-500-results.jtl](stress-500-results.jtl) | 500 users, 25-second ramp-up, 60-second total duration, 1-second pacing | 23,497 | 2026-09-24 15:00:38.970–15:01:37.934 UTC |
| Stress 1000 | [api-stress-1000.jmx](../tests/api-stress-1000.jmx) | [stress-1000-results.jtl](stress-1000-results.jtl) | 1,000 users, 50-second ramp-up, 60-second total duration, 1-second pacing | 34,541 | 2026-09-24 15:07:49.262–15:08:48.268 UTC |

The execution windows use the earliest JTL `timeStamp` and the latest sample end time (`timeStamp + elapsed`). JTL response-time and latency values are recorded in milliseconds. The test plans use a one-second Constant Timer for load and stress request pacing.

## Reporting method

The main README reports sample count, average, median, p90, p95, p99, maximum response time, error rate, and throughput from JMeter 5.6.3 HTML report statistics derived from the canonical JTL files. Generated HTML dashboards are local report artifacts and are not committed.

Different JMeter report formats can apply different rounding and percentile calculations. Metrics from a CSV summary should therefore not be substituted for the documented HTML report statistics unless the CSV is generated from the same canonical JTL with a recorded method.

## Historical CSV summaries

The following CSV files are retained as historical evidence from different executions. Their sample counts and response-time distributions do not match the corresponding canonical JTL files, and their exact run provenance is unknown:

- [stress-100-summary.csv](historical/stress-100-summary.csv)
- [stress-200-summary.csv](historical/stress-200-summary.csv)
- [stress-500-summary.csv](historical/stress-500-summary.csv)

[stress-1000-summary.csv](stress-1000-summary.csv) remains in this directory. Its sample count, maximum response time, throughput, and transfer rates align with the canonical 1,000-user JTL, so it likely represents the same execution. Its whole-number average and p99 differ from the HTML report statistics because of reporting precision or percentile calculation differences. The available evidence does not confirm its exact provenance.

The historical CSV files must not be presented as summaries of the canonical JTL files unless their provenance is independently established.

## Unavailable execution metadata

The available evidence does not identify or preserve:

- The AUT commit SHA used for each execution
- The exact JMeter execution command
- Machine specifications
- Complete operating-system, Java, and environment metadata
- A run identifier connecting each CSV export to a raw JTL file

These values cannot be recovered reliably and must not be inferred from filenames alone.
