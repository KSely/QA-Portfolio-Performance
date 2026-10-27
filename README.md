# QA Portfolio - JMeter Performance Testing

A performance testing project built with Apache JMeter for evaluating the REST API of a full-stack QA portfolio application.

The project demonstrates workload modeling, non-GUI execution, performance analysis, result evidence management, and GitHub Actions CI.

The Application Under Test (AUT) is maintained in a separate repository:

[QA-Automation-Portfolio](https://github.com/KSely/QA-Automation-Portfolio)

---

## Project Overview

This repository contains **7 JMeter performance scenarios** covering smoke, baseline, load, and stress testing.

The current suite targets:

```text
GET /api/status
```

Coverage includes:

- Smoke testing
- Baseline testing
- Load testing
- Stress testing from 100 to 1,000 virtual users
- Response-code and response-content validation
- Request pacing with JMeter timers
- Non-GUI execution
- JTL result collection
- HTML dashboard generation
- Response-time and percentile analysis
- Throughput and error-rate analysis
- Lightweight GitHub Actions CI
- Manually triggered higher-load performance testing

The endpoint under test is a lightweight health-check endpoint. Results should be interpreted as controlled portfolio measurements rather than production capacity benchmarks.

---

## Tech Stack

- Apache JMeter 5.6.3
- REST API
- JTL result files
- JMeter HTML Dashboard
- CSV result summaries
- Git
- GitHub
- GitHub Actions

---

## Performance Scenarios

| Scenario | Virtual Users | Ramp-Up | Duration / Iterations | Request Pacing |
|---|---:|---:|---|---|
| Smoke | 1 | 1 sec | 1 iteration | — |
| Baseline | 5 | 5 sec | 10 iterations per user | — |
| Load | 20 | 5 sec | 30 sec | 1 sec |
| Stress 100 | 100 | 10 sec | 30 sec | 1 sec |
| Stress 200 | 200 | 10 sec | 30 sec | 1 sec |
| Stress 500 | 500 | 25 sec | 60 sec | 1 sec |
| Stress 1000 | 1,000 | 50 sec | 60 sec | 1 sec |

The baseline test uses a fixed iteration count.

Load and stress scenarios run continuously for the configured duration. Their total duration includes ramp-up.

A one-second Constant Timer controls request pacing during load and stress testing.

Configured virtual users are **not the same as simultaneous HTTP requests**. Users are introduced gradually during ramp-up and each request is paced.

---

## Scenario Design

### Smoke

The smoke scenario checks that the endpoint and JMeter plan are working before heavier testing.

Configuration:

- 1 virtual user
- 1-second ramp-up
- 1 iteration
- `GET /api/status`
- HTTP `200` assertion
- Expected response-content assertion

### Baseline

The baseline scenario establishes a small reference workload.

Configuration:

- 5 virtual users
- 5-second ramp-up
- 10 iterations per user
- 50 total requests

### Load

The load scenario evaluates behavior under a controlled increase in traffic.

Configuration:

- 20 virtual users
- 5-second ramp-up
- 30-second duration
- 1-second request pacing

### Stress

Stress scenarios increase the workload progressively:

- 100 users — 10-second ramp-up, 30-second duration
- 200 users — 10-second ramp-up, 30-second duration
- 500 users — 25-second ramp-up, 60-second duration
- 1,000 users — 50-second ramp-up, 60-second duration

The 200-, 500-, and 1,000-user scenarios introduce users at an average rate of about 20 users per second.

The highest scenario is configured for **up to 1,000 virtual users**. It does not sustain 1,000 users for the entire 60 seconds because the duration includes ramp-up.

---

## Project Structure

```text
QA-Portfolio-Performance/
├── tests/
│   ├── api-smoke.jmx
│   ├── api-baseline.jmx
│   ├── api-load.jmx
│   ├── api-stress.jmx
│   ├── api-stress-200.jmx
│   ├── api-stress-500.jmx
│   └── api-stress-1000.jmx
├── results/
│   ├── README.md
│   ├── baseline-results.jtl
│   ├── load-results.jtl
│   ├── stress-100-results.jtl
│   ├── stress-200-results.jtl
│   ├── stress-500-results.jtl
│   ├── stress-1000-results.jtl
│   ├── stress-1000-summary.csv
│   └── historical/
│       ├── stress-100-summary.csv
│       ├── stress-200-summary.csv
│       └── stress-500-summary.csv
├── reports/
│   ├── baseline-report/
│   ├── load-report/
│   ├── stress-100-report/
│   ├── stress-200-report/
│   ├── stress-500-report/
│   └── stress-1000-report/
├── .github/
│   └── workflows/
├── .gitignore
└── README.md
```

---

## Running the Tests

### Prerequisites

Before running the tests:

- Install Apache JMeter 5.6.3
- Start the QA Automation Portfolio application
- Confirm `http://localhost:3000/api/status` is available

Start the AUT from its repository:

```bash
npm start
```

### Non-GUI Execution

Final performance runs use JMeter non-GUI mode to reduce load-generator overhead.

Smoke:

```bash
jmeter -n -t tests/api-smoke.jmx -l results/smoke-results.jtl
```

Baseline:

```bash
jmeter -n -t tests/api-baseline.jmx -l results/baseline-results.jtl
```

Load:

```bash
jmeter -n -t tests/api-load.jmx -l results/load-results.jtl
```

Stress 100:

```bash
jmeter -n -t tests/api-stress.jmx -l results/stress-100-results.jtl
```

Stress 200:

```bash
jmeter -n -t tests/api-stress-200.jmx -l results/stress-200-results.jtl
```

Stress 500:

```bash
jmeter -n -t tests/api-stress-500.jmx -l results/stress-500-results.jtl
```

Stress 1000:

```bash
jmeter -n -t tests/api-stress-1000.jmx -l results/stress-1000-results.jtl
```

JMeter GUI is used for creating and debugging plans; non-GUI mode is used for final execution.

---

## Reporting

Raw measurements are stored in JTL files.

HTML dashboards can be generated from those files.

Example:

```bash
jmeter -g results/load-results.jtl -o reports/load-report
```

For the 1,000-user scenario:

```bash
jmeter -g results/stress-1000-results.jtl -o reports/stress-1000-report
```

The reports provide:

- Sample count
- Average and median response time
- p90, p95, and p99
- Minimum and maximum response time
- Throughput
- Error rate
- Response-time trends

---

## Performance Results

The committed raw JTL files are the **canonical evidence** for the results below.

The metrics were generated from the corresponding JTL files using JMeter 5.6.3 HTML report statistics.

See [results/README.md](results/README.md) for scenario mappings, recovered execution timestamps, and provenance notes.

The 100-, 200-, and 500-user CSV summaries are retained under `results/historical/` because they came from different executions and do not match the canonical JTL files exactly.

| Scenario | Samples | Avg | Median | p90 | p95 | p99 | Max | Error Rate | Throughput |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| Baseline | 50 | 2.80 ms | 2 ms | 4 ms | 5.45 ms | 25 ms | 25 ms | 0% | 12.70 req/s |
| Load | 540 | 1.73 ms | 2 ms | 3 ms | 3 ms | 5 ms | 34 ms | 0% | 18.81 req/s |
| Stress 100 | 2,450 | 1.51 ms | 1 ms | 2 ms | 3 ms | 4 ms | 27 ms | 0% | 84.87 req/s |
| Stress 200 | 4,901 | 1.27 ms | 1 ms | 2 ms | 2 ms | 4 ms | 28 ms | 0% | 169.43 req/s |
| Stress 500 | 23,497 | 0.83 ms | 1 ms | 1 ms | 2 ms | 2 ms | 30 ms | 0% | 398.50 req/s |
| Stress 1000 | 34,541 | 0.88 ms | 1 ms | 2 ms | 2 ms | 5 ms | 48 ms | 0% | 585.38 req/s |

---

## Results Summary

All recorded canonical executions completed with a **0% error rate**.

Observed stress-test throughput increased with workload:

```text
100 users   →  84.87 req/s
200 users   → 169.43 req/s
500 users   → 398.50 req/s
1,000 users → 585.38 req/s
```

At the highest tested workload:

```text
p95        = 2 ms
p99        = 5 ms
Max        = 48 ms
Error Rate = 0%
```

Throughput continued to increase as workload increased, although growth became less proportional at the higher workload levels.

No application breaking point was identified within the tested range.

This should **not** be interpreted as the application's maximum capacity. The AUT and JMeter load generator shared the same local machine, and the workload profiles were not identical across all stress levels.

The `/api/status` endpoint is also very lightweight, so small differences in millisecond-level averages are interpreted cautiously. Percentiles, throughput, error rate, maximum response time, and overall workload behavior are considered together.

---

## Test Environment and Limitations

The tests were executed in a local development environment with JMeter and the AUT running on the same machine.

Important limitations:

- Local network latency is minimal
- JMeter and the AUT share CPU and memory
- Testing focuses only on `GET /api/status`
- Application CPU and memory are not collected
- Database resource usage is not monitored
- Stress scenarios use different ramp-up periods and durations
- Higher-load scenarios have limited steady-state time after ramp-up
- No production SLA or formal acceptance threshold is enforced
- No endurance scenario is currently included

A production-oriented performance test would normally add:

- Dedicated load generators
- Production-like infrastructure
- CPU, memory, network, and database monitoring
- Representative business workflows
- Longer steady-state periods
- Realistic workload distributions
- Defined performance thresholds and SLAs
- Isolated capacity and breaking-point testing

---

## CI Strategy

The repository uses two GitHub Actions workflows.

| Workflow | Trigger | Scope | Purpose |
|---|---|---|---|
| JMeter CI | Push / pull request | Smoke + Baseline | Lightweight continuous verification |
| JMeter Performance Tests | Manual `workflow_dispatch` | Load / Stress | Higher-load investigation |

### JMeter CI

The regular CI workflow:

1. Checks out the performance repository
2. Checks out the AUT
3. Prepares the test environment
4. Starts PostgreSQL and the AUT
5. Verifies application availability
6. Installs and verifies JMeter
7. Runs smoke
8. Validates smoke results
9. Runs baseline
10. Validates baseline results
11. Handles artifacts and cleanup

Heavy load and stress scenarios are intentionally excluded from routine CI.

### JMeter Performance Tests

The second workflow is started manually from GitHub Actions.

It allows higher-load scenarios to be selected intentionally instead of running them on every code change.

This separation keeps routine CI faster and avoids unnecessary high-load traffic and GitHub Actions usage.

---

## Related Repositories

### Application Under Test

Full-stack Node.js / Express / PostgreSQL application tested by this project.

[QA-Automation-Portfolio](https://github.com/KSely/QA-Automation-Portfolio)

### Selenium Automation

Java Selenium / TestNG automation framework for UI, API, database, and cross-browser testing.

[QA-Portfolio-Selenium](https://github.com/KSely/QA-Portfolio-Selenium)

### Playwright Automation

JavaScript Playwright framework for UI, API, database, authentication, accessibility, and cross-browser testing.

[QA-Portfolio-Playwright](https://github.com/KSely/QA-Portfolio-Playwright)

---

## Skills Demonstrated

This project demonstrates practical experience with:

- Performance test planning
- Workload modeling
- JMeter test-plan design
- Smoke, baseline, load, and stress testing
- REST API performance testing
- Virtual-user and ramp-up configuration
- Request pacing
- Non-GUI execution
- JTL result collection
- HTML dashboard generation
- Percentile analysis
- Throughput and error-rate analysis
- Incremental stress testing
- Performance result interpretation
- Test-evidence provenance
- Test-environment limitation analysis
- GitHub Actions CI
- Manual higher-load workflow execution

---

## Purpose

This project demonstrates how Apache JMeter can be used to design, execute, analyze, and document REST API performance tests.

The focus is on realistic workload configuration, controlled execution, traceable evidence, clear interpretation of results, and honest reporting of test-environment limitations.

The repository is publicly available for review by potential employers and recruiters.

No open-source license is currently provided for this repository.
