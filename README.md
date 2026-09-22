# CSV to Excel Processor — Coding Standards Workshop

Maven Java demo used in a programming / coding-standards awareness session.

The application:

1. Reads employee data from a CSV file
2. Calculates bonus, tax, grade, and net pay
3. Writes a payroll Excel (`.xlsx`) report

The source is **intentionally non-compliant**. It is designed to fail a typical SonarQube quality gate so participants can find and fix the issues.

## Requirements

- JDK 17+ (the demo compiles with `--release 17`)
- Apache Maven 3.8+, or use the included Maven Wrapper (`mvnw.cmd` on Windows / `./mvnw` on macOS/Linux)
- Optional: SonarQube / SonarCloud for the quality-gate exercise

## Project layout

```
csv-excel-processor/
├── .github/workflows/quality-gate.yml
├── pom.xml
├── sonar-project.properties
├── src/main/java/com/training/codingstandards/
│   ├── App.java                 # entry point
│   ├── CsvEmployeeReader.java   # CSV input
│   ├── EmployeeProcessor.java   # business rules
│   ├── ExcelReportWriter.java   # Excel output
│   ├── Employee.java
│   ├── ReportConfig.java
│   ├── SecurityUtil.java        # crypto / secrets (vulnerable)
│   └── DatabaseHelper.java      # SQL / OS command (vulnerable)
├── src/main/resources/
│   ├── employees.csv
│   └── application.properties   # hardcoded credentials (vulnerable)
└── src/test/java/...            # minimal tests (coverage will be low)
```

## Run the demo

From this folder (Windows):

```bat
mvnw.cmd -q test
mvnw.cmd -q exec:java
```

From this folder (macOS / Linux, or if Maven is already on PATH):

```bash
./mvnw -q test
./mvnw -q exec:java
```

Or after packaging:

```bash
mvnw.cmd -q package
java -jar target/csv-excel-processor-1.0.0-SNAPSHOT.jar
```

Custom paths:

```bash
java -jar target/csv-excel-processor-1.0.0-SNAPSHOT.jar path/to/employees.csv payroll-report.xlsx
```

Defaut
mvnw.cmdput Excel is written to `payroll-report.xlsx` in the working directory.

## Scan with SonarQube

With a local SonarQube server:

```bash
mvn -q verify sonar:sonar -Dsonar.host.url=http://localhost:9000 -Dsonar.token=<TOKEN>
```

Expect the quality gate to **fail** on this baseline (bugs, vulnerabilities, security hotspots, code smells, coverage, duplications, cognitive complexity).

## GitHub Actions quality gate

[`.github/workflows/quality-gate.yml`](.github/workflows/quality-gate.yml) starts **SonarQube Community LTS in the workflow** (service container on `localhost:9000`), runs `mvn verify sonar:sonar`, and waits for the quality gate.

The baseline reports a **failed quality gate** (`sonar.qualitygate.wait=true`) without failing the GitHub Actions job, so the Sonar report is always uploaded for the workshop. After participants fix the findings, the report should show a passing gate.

The Maven log link `http://localhost:9000/dashboard?id=csv-excel-processor` is **only on the GitHub Actions runner**. You cannot open it from your laptop.

Download the report from the workflow run instead:

1. GitHub → **Actions** → **Quality Gate** → the failed run
2. **Artifacts** → `sonar-quality-gate-report`
3. Open `quality-gate-report.md` (summary) plus `quality-gate.json` / `issues.json`

Requires no SonarCloud account or repository secrets. Triggered on `push` to `main`, pull requests, and manual `workflow_dispatch`.

## Workshop flow (suggested)

1. Run the app and open `payroll-report.xlsx`
2. Run Sonar and show a failed quality gate
3. Ask participants to fix findings without changing the CSV → Excel behaviour
4. Re-run tests, regenerate Excel, re-scan until the gate passes
