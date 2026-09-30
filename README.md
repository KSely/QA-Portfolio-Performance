# QA Portfolio - JMeter Performance Testing

A performance testing project built with Apache JMeter for evaluating the REST API of a full-stack portfolio web application.

The project demonstrates performance test design, workload modeling, non-GUI execution, CI integration, and analysis of response time, percentiles, throughput, and error rate under increasing workload levels.

The Application Under Test (AUT) is maintained in a separate repository:

[QA-Automation-Portfolio](https://github.com/KSely/QA-Automation-Portfolio)

---

## Tech Stack

- Apache JMeter 5.6.3
- REST API
- JTL Test Results
- JMeter HTML Dashboard
- CSV Test Summaries
- Git / GitHub
- GitHub Actions

---

## Project Overview

This repository contains a performance testing project created for a full-stack QA portfolio web application.

The project includes:

- **Smoke Testing** — verifies that the API endpoint is available and the JMeter test plan is configured correctly
- **Baseline Testing** — establishes reference performance metrics under a small, controlled workload
- **Load Testing** — evaluates application behavior under an increased, controlled workload
- **Stress Testing** — progressively increases the workload from 100 to 1,000 virtual users
- **Performance Analysis** — evaluates response time, percentiles, throughput, and error rate
- **Non-GUI Execution** — runs final performance tests with reduced load-generator overhead
- **HTML Reporting** — generates JMeter HTML dashboards from raw JTL results
- **CI/CD** — separates lightweight continuous integration checks from manually triggered heavier performance testing

The current performance suite targets the:

```text
GET /api/status
```

endpoint of the QA Automation Portfolio application.

---

## Test Coverage

The performance test suite currently includes:

- **Smoke test** with a single virtual user and request
- **Baseline test** with 5 virtual users and 50 total requests
- **Load test** generating 540 requests
- **100-user stress test**
- **200-user stress test**
- **500-user stress test**
- **1,000-user stress test**
- HTTP response code validation
- Response content validation in the smoke test
- Raw JTL result collection
- JMeter HTML performance reports
- CSV stress-test summaries
- Performance metric analysis using:
  - Average response time
  - Median response time
  - p90
  - p95
  - p99
  - Maximum response time
  - Throughput
  - Error rate

---

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
├── .github
│   └── workflows
│       ├── JMeter CI workflow
│       └── JMeter Performance Tests workflow
│
├── .gitignore
└── README.md
```

The repository uses two separate GitHub Actions workflows:

- **JMeter CI** — lightweight automated smoke and baseline validation
- **JMeter Performance Tests** — manually triggered load and stress testing

---

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

For the duration-based tests, the configured duration includes the ramp-up period.

A one-second Constant Timer is used for request pacing during load and stress testing.

The 200-, 500-, and 1,000-user stress tests use an average ramp-up rate of approximately 20 new virtual users per second.

---

## Smoke Test

The smoke test verifies that the `/api/status` endpoint is available and that the JMeter test plan is configured correctly before executing higher workload tests.

Configuration:

- 1 virtual user
- 1-second ramp-up
- 1 iteration
- `GET /api/status`
- HTTP status code assertion for `200`
- Response content assertion for the expected API status

The smoke test provides a quick validation of endpoint availability, request configuration, and response correctness before baseline, load, and stress testing.

---

## Baseline Test

The baseline test establishes reference performance metrics under a small, controlled workload before higher levels of load are applied.

Configuration:

- 5 virtual users
- 5-second ramp-up
- 10 iterations per user
- 50 total requests
- `GET /api/status`

Unlike the duration-based load and stress tests, the baseline test uses a fixed number of iterations.

Each virtual user executes 10 requests, producing 50 requests in total.

The baseline results provide a reference point for comparing response time, percentiles, throughput, and error rate under higher workload levels.

---

## Load Test

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

---

## Stress Testing

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

The workload was increased incrementally to observe performance behavior at progressively higher workload levels.

No application breaking point was identified within the tested range.

Because the Thread Group duration includes the ramp-up period, the highest workload test represents a test configured with up to 1,000 virtual users rather than 1,000 users sustained for the full 60-second duration.

---

## Running the Tests

### Prerequisites

Before running the tests, make sure the following are available:

- Apache JMeter 5.6.3
- The QA Automation Portfolio application running locally
- Application available at:

```text
http://localhost:3000
```

- `/api/status` endpoint available

Start the Application Under Test from the AUT repository:

```bash
npm start
```

---

## Run Tests in Non-GUI Mode

Final performance test executions are performed in JMeter non-GUI mode to reduce load-generator overhead.

### Smoke Test

```bash
jmeter -n -t tests/api-smoke.jmx -l results/smoke-results.jtl
```

### Baseline Test

```bash
jmeter -n -t tests/api-baseline.jmx -l results/baseline-results.jtl
```

### Load Test

```bash
jmeter -n -t tests/api-load.jmx -l results/load-results.jtl
```

### 100-User Stress Test

```bash
jmeter -n -t tests/api-stress.jmx -l results/stress-100-results.jtl
```

### 200-User Stress Test

```bash
jmeter -n -t tests/api-stress-200.jmx -l results/stress-200-results.jtl
```

### 500-User Stress Test

```bash
jmeter -n -t tests/api-stress-500.jmx -l results/stress-500-results.jtl
```

### 1,000-User Stress Test

```bash
jmeter -n -t tests/api-stress-1000.jmx -l results/stress-1000-results.jtl
```

JMeter command-line options:

- `-n` — runs JMeter in non-GUI mode
- `-t` — specifies the JMeter test plan (`.jmx`)
- `-l` — specifies the raw results file (`.jtl`)

The JMeter GUI is used for creating, configuring, and debugging test plans.

Non-GUI mode is used for final performance test execution because it reduces unnecessary load-generator overhead.

---

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
- Average response time
- Median response time
- p90 response time
- p95 response time
- p99 response time
- Minimum response time
- Maximum response time
- Throughput
- Error rate
- Response-time trends over the test duration

The reports are stored in the `reports` directory, with a separate dashboard for each baseline, load, and stress test execution.

---

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

---

## Key Observations

- All recorded test executions completed with a **0% error rate**
- Stress-test throughput increased from **84.87 req/s at 100 virtual users** to **585.38 req/s at 1,000 virtual users**
- Response-time percentiles remained low throughout the tested workload range
- At the highest workload, p95 was **2 ms**
- At the highest workload, p99 was **5 ms**
- The maximum observed response time increased to **48 ms** at the 1,000-user workload
- Throughput continued to increase as the workload increased
- Throughput growth became less proportional at the higher workload levels
- No application breaking point was identified within the tested workload range

---

## Results Analysis

The performance results show stable API behavior across the tested workload range, with no recorded request failures.

During incremental stress testing, observed throughput increased as the number of virtual users increased:

```text
100 users   → 84.87 req/s
200 users   → 169.43 req/s
500 users   → 398.50 req/s
1,000 users → 585.38 req/s
```

Response-time percentiles remained low throughout the stress tests.

At the highest tested workload of 1,000 virtual users:

```text
p95        = 2 ms
p99        = 5 ms
Max        = 48 ms
Error Rate = 0%
```

Throughput growth became less proportional between the 500-user and 1,000-user tests.

However, this is not treated as evidence of the application's maximum capacity because the application and JMeter load generator were running in the same local environment, and the workload profiles were not identical across all stress levels.

The results did not identify an application breaking point within the tested workload range.

Small differences in average response time between workload levels are interpreted cautiously.

The `/api/status` endpoint is lightweight and response times are measured at the millisecond and sub-millisecond level, where normal local execution variability can noticeably affect averages.

For this reason, performance is evaluated using multiple metrics together:

- Response-time percentiles
- Throughput
- Error rate
- Maximum response time
- Overall behavior under increasing workload

rather than relying on average response time alone.

---

## Test Environment and Limitations

The performance tests were executed in a local development environment.

Apache JMeter and the Application Under Test were running on the same local machine.

The tested `/api/status` endpoint is a lightweight health-check endpoint with minimal processing compared with more complex application workflows.

Because of these conditions, the recorded response times and throughput values should be interpreted as controlled portfolio test results rather than production capacity benchmarks.

The stress tests were configured with up to 1,000 virtual users.

This does not mean that 1,000 HTTP requests were executing simultaneously because virtual users were introduced gradually through ramp-up periods and request execution was controlled using a one-second Constant Timer.

The current test environment has the following limitations:

- JMeter and the Application Under Test share local system resources
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

---

## CI/CD

The project uses two separate GitHub Actions workflows to separate lightweight continuous integration checks from resource-intensive performance testing.

This design prevents higher-load JMeter scenarios from running automatically on every code change.

---

### JMeter CI

The regular JMeter CI workflow is designed for lightweight and repeatable validation.

It executes:

- **JMeter Smoke Test**
- **Smoke Test Result Validation**
- **JMeter Baseline Test**
- **Baseline Test Result Validation**

The workflow also prepares the complete test environment by:

1. Checking out the performance testing repository
2. Checking out the Application Under Test
3. Initializing required containers
4. Setting up Node.js
5. Installing application dependencies
6. Creating the application environment
7. Creating the database schema
8. Starting the Application Under Test
9. Waiting for the application to become available
10. Setting up Java
11. Installing Apache JMeter
12. Verifying the JMeter installation
13. Running the JMeter smoke test
14. Validating smoke test results
15. Running the JMeter baseline test
16. Validating baseline test results
17. Handling test artifacts and cleanup

Workflow:

```text
Repository Change
        ↓
GitHub Actions
        ↓
Prepare Test Environment
        ↓
Start Application Under Test
        ↓
Application Health Check
        ↓
Install / Verify JMeter
        ↓
Smoke Test
        ↓
Validate Smoke Results
        ↓
Baseline Test
        ↓
Validate Baseline Results
        ↓
Test Artifacts / Cleanup
```

The heavier load and stress tests are intentionally excluded from this workflow.

This keeps routine CI execution lightweight and avoids generating unnecessary high-load traffic during normal repository changes.

---

### JMeter Performance Tests

A separate GitHub Actions workflow is used for heavier performance testing.

This workflow uses:

```text
workflow_dispatch
```

and is started manually from the GitHub Actions interface using the **Run workflow** control.

The performance scenario is selected before starting the workflow.

Examples of manually selectable scenarios include:

- Load testing
- Stress testing with 100 virtual users
- Stress testing with 500 virtual users
- Stress testing with 1,000 virtual users

Additional performance scenarios can be maintained in the repository as separate JMeter test plans.

Workflow:

```text
Manual Run
        ↓
Select Performance Test
        ↓
GitHub-Hosted Runner
        ↓
Prepare Test Environment
        ↓
Start Application Under Test
        ↓
Application Health Check
        ↓
Install / Verify JMeter
        ↓
Run Selected Performance Scenario
        ↓
Collect Results
        ↓
Upload Performance Artifacts
```

This workflow allows higher-load tests to be executed only when they are specifically needed.

---

## CI Strategy

The two-workflow design separates routine validation from heavier performance testing.

| Workflow | Trigger | Test Scope | Purpose |
|---|---|---|---|
| JMeter CI | Regular CI | Smoke + Baseline | Fast functional and baseline performance validation |
| JMeter Performance Tests | Manual `workflow_dispatch` | Load / Stress | Higher-load performance investigation |

This design provides several advantages:

- Keeps regular CI execution faster
- Avoids running 100–1,000 virtual-user tests unnecessarily
- Reduces unnecessary GitHub Actions resource usage
- Allows performance scenarios to be selected intentionally
- Separates continuous verification from dedicated performance testing
- Makes performance testing easier to demonstrate and explain during technical review

---

## Key Skills Demonstrated

This project demonstrates practical performance testing skills including:

- Performance test planning
- Workload modeling
- JMeter test plan design and configuration
- Smoke testing
- Baseline testing
- Load testing
- Stress testing
- REST API performance testing
- Virtual user configuration
- Ramp-up configuration
- Request pacing using JMeter timers
- Non-GUI performance test execution
- JTL result collection
- CSV result summaries
- JMeter HTML report generation
- Average and median response-time analysis
- p90, p95, and p99 percentile analysis
- Throughput analysis
- Error-rate analysis
- Incremental stress testing up to 1,000 virtual users
- Performance result interpretation
- Identification of test-environment limitations
- GitHub Actions CI/CD
- Separation of lightweight CI from resource-intensive performance testing
- Manual performance workflow execution

---

## Related Repositories

### Application Under Test

Full-stack Node.js / Express / PostgreSQL application tested by this project.

[QA-Automation-Portfolio](https://github.com/KSely/QA-Automation-Portfolio)

### Selenium Automation

Java Selenium / TestNG automation framework for UI, API, database, and cross-browser testing.

[QA-Portfolio-Selenium](https://github.com/KSely/QA-Portfolio-Selenium)

### Playwright Automation

JavaScript Playwright automation framework for UI, API, database, cross-browser, and defect regression testing.

[QA-Portfolio-Playwright](https://github.com/KSely/QA-Portfolio-Playwright)

---

## Purpose

This project was created as a practical performance testing portfolio demonstrating how Apache JMeter can be used to design, execute, analyze, and document REST API performance tests.

The project focuses on:

- Performance test planning
- Workload design
- Progressive load generation
- Non-GUI execution
- Smoke and baseline CI validation
- Manually controlled high-load testing
- Response-time analysis
- Percentile analysis
- Throughput analysis
- Error-rate analysis
- Performance reporting
- CI/CD integration
- Interpretation of test-environment limitations

The repository is publicly available for review by potential employers and recruiters.

No open-source license is currently provided for this repository.