# QA Portfolio - JMeter Performance Testing

A performance testing project built with Apache JMeter for evaluating the REST API of a full-stack portfolio web application.

The project demonstrates performance test design, workload modeling, non-GUI test execution, and analysis of response time, percentiles, throughput, and error rate under increasing workload levels.

## Tech Stack

- Apache JMeter 5.6.3
- REST API
- JTL Test Results
- JMeter HTML Dashboard
- CSV Test Summaries
- Git / GitHub

## Project Overview

This repository contains a performance testing project created for a full-stack QA portfolio web application.

The project includes:

- **Smoke Testing** — verifies that the API endpoint is available and the JMeter test plan is configured correctly
- **Baseline Testing** — establishes reference performance metrics under a small, controlled workload
- **Load Testing** — evaluates application behavior under an increased, controlled workload
- **Stress Testing** — progressively increases the workload from 100 to 1,000 virtual users
- **Performance Analysis** — evaluates response time, percentiles, throughput, and error rate
- **HTML Reporting** — generates JMeter HTML dashboards from raw JTL results

The current test suite targets the `GET /api/status` endpoint of the QA Automation Portfolio application.

## Test Coverage

The performance test suite currently includes:

- **Smoke test** with a single virtual user and request
- **Baseline test** with 5 virtual users and 50 total requests
- **Load test** generating 540 requests
- **100-user stress test**
- **200-user stress test**
- **500-user stress test**
- **1,000-user stress test**
- Response code and response content validation in the smoke test
- Raw JTL result collection
- JMeter HTML performance reports
- Performance metric analysis using average response time, median, p90, p95, p99, maximum response time, throughput, and error rate

## Project Structure

```text
QA-Portfolio-Performance
├── tests
│   ├── api-smoke.jmx
│   ├── api-baseline.jmx
│   ├── api-load.jmx
│   ├── api-stress.jmx
│   ├── api-stress-200.jmx
│   ├── api-stress-500.jmx
│   └── api-stress-1000.jmx
│
├── results
│   ├── baseline-results.jtl
│   ├── load-results.jtl
│   ├── stress-100-results.jtl
│   ├── stress-100-summary.csv
│   ├── stress-200-results.jtl
│   ├── stress-200-summary.csv
│   ├── stress-500-results.jtl
│   ├── stress-500-summary.csv
│   ├── stress-1000-results.jtl
│   └── stress-1000-summary.csv
│
├── reports
│   ├── baseline-report
│   ├── load-report
│   ├── stress-100-report
│   ├── stress-200-report
│   ├── stress-500-report
│   └── stress-1000-report
│
├── .gitignore
└── README.md
```

## Performance Test Scenarios

The test suite uses different workload profiles to validate the API under progressively increasing levels of load.

| Test | Virtual Users | Ramp-Up | Duration / Iterations | Request Pacing |
|---|---:|---:|---|---|
| Smoke | 1 | 1 sec | 1 iteration | — |
| Baseline | 5 | 5 sec | 10 iterations per user | — |
| Load | 20 | 5 sec | 30 sec duration | 1 sec |
| Stress 100 | 100 | 10 sec | 30 sec duration | 1 sec |
| Stress 200 | 200 | 10 sec | 30 sec duration | 1 sec |
| Stress 500 | 500 | 25 sec | 60 sec duration | 1 sec |
| Stress 1000 | 1,000 | 50 sec | 60 sec duration | 1 sec |

The baseline test uses a fixed number of iterations, while the load and stress tests use continuous execution for a configured duration.

For the duration-based tests, the configured duration includes the ramp-up period. A one-second Constant Timer is used for request pacing during load and stress testing.

The 200-, 500-, and 1,000-user stress tests use an average ramp-up rate of approximately 20 new virtual users per second.

### Smoke Test

The smoke test verifies that the `/api/status` endpoint is available and that the JMeter test plan is configured correctly before executing higher workload tests.

Configuration:

- 1 virtual user
- 1-second ramp-up
- 1 iteration
- `GET /api/status`
- HTTP status code assertion for `200`
- Response content assertion for the expected API status

The smoke test provides a quick validation of endpoint availability, request configuration, and response correctness before baseline, load, and stress testing.

### Baseline Test

The baseline test establishes reference performance metrics under a small, controlled workload before higher levels of load are applied.

Configuration:

- 5 virtual users
- 5-second ramp-up
- 10 iterations per user
- 50 total requests
- `GET /api/status`

Unlike the duration-based load and stress tests, the baseline test uses a fixed number of iterations. Each virtual user executes 10 requests, producing 50 requests in total.

The baseline results provide a reference point for comparing response time, percentiles, throughput, and error rate under higher workload levels.

### Load Test

The load test evaluates API performance under an increased, controlled workload and provides a comparison with the baseline test.

Configuration:

- 20 virtual users
- 5-second ramp-up
- 30-second total duration
- Infinite loop during the configured duration
- 1-second Constant Timer for request pacing
- `GET /api/status`

The virtual users are introduced gradually during the 5-second ramp-up period and continue executing requests until the 30-second Thread Group duration expires.

The Constant Timer introduces a one-second delay before each request, providing controlled request pacing instead of allowing each virtual user to send requests continuously without delay.

The test evaluates response time, percentiles, throughput, and error rate under increased workload.

### Stress Testing

The stress tests progressively increase the workload to evaluate API behavior under higher levels of virtual-user activity and to observe changes in response time, throughput, percentiles, and error rate.

The stress test suite includes:

- **100 virtual users** — 10-second ramp-up, 30-second total duration
- **200 virtual users** — 10-second ramp-up, 30-second total duration
- **500 virtual users** — 25-second ramp-up, 60-second total duration
- **1,000 virtual users** — 50-second ramp-up, 60-second total duration

All stress tests use:

- Infinite loop during the configured duration
- 1-second Constant Timer for request pacing
- `GET /api/status`

The 200-, 500-, and 1,000-user tests use an average ramp-up rate of approximately 20 new virtual users per second.

The workload was increased incrementally to observe performance behavior at progressively higher load levels. No application breaking point was identified within the tested range.

Because the Thread Group duration includes the ramp-up period, the highest workload test represents a test configured with up to 1,000 virtual users rather than 1,000 users sustained for the full 60-second duration.

## Running the Tests

### Prerequisites

Before running the tests, make sure the following are available:

- Apache JMeter 5.6.3
- The QA Automation Portfolio application running locally
- Application available at `http://localhost:3000`
- `/api/status` endpoint available

### Run Tests in Non-GUI Mode

Final performance test executions are performed in JMeter non-GUI mode to reduce load-generator overhead.

Example — Load Test:

```bash
jmeter -n -t tests/api-load.jmx -l results/load-results.jtl
```

Example — 1,000-User Stress Test:

```bash
jmeter -n -t tests/api-stress-1000.jmx -l results/stress-1000-results.jtl
```

JMeter command-line options:

- `-n` — runs JMeter in non-GUI mode
- `-t` — specifies the JMeter test plan (`.jmx`)
- `-l` — specifies the raw results file (`.jtl`)

The JMeter GUI is used for creating, configuring, and debugging test plans, while non-GUI mode is used for final performance test execution.

## HTML Reporting

Raw performance test results are stored in JTL files and used to generate JMeter HTML dashboards for detailed analysis.

Example — generate the Load Test report:

```bash
jmeter -g results/load-results.jtl -o reports/load-report
```

Example — generate the 1,000-User Stress Test report:

```bash
jmeter -g results/stress-1000-results.jtl -o reports/stress-1000-report
```

JMeter command-line options:

- `-g` — specifies the existing JTL results file used to generate the report
- `-o` — specifies the output directory for the HTML dashboard

The generated HTML reports provide performance metrics and visualizations including:

- Sample count
- Average and median response time
- p90, p95, and p99 response-time percentiles
- Minimum and maximum response time
- Throughput
- Error rate
- Response-time trends over the test duration

The reports are stored in the `reports` directory, with a separate dashboard for each baseline, load, and stress test execution.

## Performance Test Results

The following results were collected from the JMeter HTML reports generated after the final non-GUI test executions.

| Test | Samples | Avg | Median | p90 | p95 | p99 | Max | Error Rate | Throughput |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| Baseline | 50 | 2.80 ms | 2 ms | 4 ms | 5.45 ms | 25 ms | 25 ms | 0% | 12.70 req/s |
| Load | 540 | 1.73 ms | 2 ms | 3 ms | 3 ms | 5 ms | 34 ms | 0% | 18.81 req/s |
| Stress 100 | 2,450 | 1.51 ms | 1 ms | 2 ms | 3 ms | 4 ms | 27 ms | 0% | 84.87 req/s |
| Stress 200 | 4,901 | 1.27 ms | 1 ms | 2 ms | 2 ms | 4 ms | 28 ms | 0% | 169.43 req/s |
| Stress 500 | 23,497 | 0.83 ms | 1 ms | 1 ms | 2 ms | 2 ms | 30 ms | 0% | 398.50 req/s |
| Stress 1000 | 34,541 | 0.88 ms | 1 ms | 2 ms | 2 ms | 5 ms | 48 ms | 0% | 585.38 req/s |

### Key Observations

- All recorded test executions completed with a **0% error rate**.
- Stress-test throughput increased from **84.87 req/s at 100 virtual users** to **585.38 req/s at 1,000 virtual users**.
- Response-time percentiles remained low throughout the tested workload range.
- At the highest workload, p95 was **2 ms** and p99 was **5 ms**.
- The maximum observed response time increased to **48 ms** at the 1,000-user workload.
- Throughput continued to increase as the workload increased, although the growth became less proportional at the higher workload levels.
- No application breaking point was identified within the tested workload range.

## Results Analysis

The performance results show stable API behavior across the tested workload range, with no recorded request failures.

During incremental stress testing, observed throughput increased as the number of virtual users increased:

- **100 users:** 84.87 req/s
- **200 users:** 169.43 req/s
- **500 users:** 398.50 req/s
- **1,000 users:** 585.38 req/s

Response-time percentiles remained low throughout the stress tests. At the highest tested workload of 1,000 virtual users, p95 was 2 ms and p99 was 5 ms, while the error rate remained at 0%.

Throughput growth became less proportional between the 500-user and 1,000-user tests. However, this is not treated as evidence of the application's maximum capacity because the application and JMeter load generator were running in the same local environment, and the workload profiles were not identical across all stress levels.

The results did not identify an application breaking point within the tested workload range.

Small differences in average response time between workload levels are interpreted cautiously. The `/api/status` endpoint is lightweight and response times are measured at the millisecond and sub-millisecond level, where normal local execution variability can noticeably affect averages.

For this reason, performance is evaluated using multiple metrics together — response-time percentiles, throughput, error rate, and overall behavior under increasing workload — rather than relying on average response time alone.

## Test Environment and Limitations

The performance tests were executed in a local development environment. Apache JMeter and the application under test were running on the same local machine.

The tested `/api/status` endpoint is a lightweight health-check endpoint with minimal processing compared with more complex application workflows.

Because of these conditions, the recorded response times and throughput values should be interpreted as controlled portfolio test results rather than production capacity benchmarks.

The stress tests were configured with up to 1,000 virtual users. This does not mean that 1,000 HTTP requests were executing simultaneously, because virtual users were introduced gradually through ramp-up periods and request execution was controlled using a one-second Constant Timer.

The current test environment has the following limitations:

- JMeter and the application under test share local system resources
- Network latency is minimal because the application is hosted locally
- Testing currently focuses on a lightweight `GET /api/status` endpoint
- Application server CPU and memory utilization are not collected
- Database resource utilization is not monitored
- Stress-test workload profiles use different ramp-up periods and durations
- The highest workload tests have limited steady-state time after ramp-up
- No production SLA or formal performance acceptance criteria are applied

A production-oriented performance testing approach would additionally include:

- Dedicated load generators
- Production-like application infrastructure
- CPU, memory, network, and database monitoring
- Multiple representative API and user workflows
- Longer steady-state load periods
- Realistic workload distribution and user behavior
- Defined performance SLAs and acceptance criteria
- Capacity and breaking-point testing in an isolated environment

## Key Skills Demonstrated

This project demonstrates practical performance testing skills including:

- Performance test planning and workload design
- JMeter test plan design and configuration
- Smoke, baseline, load, and stress testing
- REST API performance testing
- Virtual user and ramp-up configuration
- Request pacing using JMeter timers
- Non-GUI performance test execution
- JTL result collection
- JMeter HTML report generation
- Response-time and percentile analysis
- Throughput and error-rate analysis
- Incremental stress testing up to 1,000 virtual users
- Performance result interpretation
- Identification of test-environment limitations