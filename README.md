# Playwright Java BDD Automation Framework

A robust, industry-ready **Playwright** automation framework written in **Java** utilizing **Cucumber BDD** for clear, readable test scenarios.

## Features
- **Parallel Execution:** Native support for multiple simultaneous "workers" via JUnit.
- **Dynamic Configuration (Owner):** Type-safe, centralized configuration via `FrameworkConfig` interface.
- **Advanced Logging (Log4j2):** Thread-safe, professional-grade logging to console and `target/logs/framework.log`.
- **BasePage Pattern:** Standardized UI interactions with built-in logging and Allure steps.
- **Advanced Artifact Collection:**
  - Automated full-page **Screenshots** on failure.
  - Comprehensive Playwright **Tracing** (.zip logs) on failure.
  - Complete **Video Recording** of test executions.
- **Industry-Standard Reporting (Allure):** Detailed, visual step-by-step reports with `@Step` annotations.
- **Rich HTML Reporting:** Auto-generated visual Cucumber execution reports.
- **Zero-Setup Playwright:** Browsers (Chromium, Firefox, Webkit) are automatically downloaded.

## 1. Prerequisites & Setup
To set up this framework on your local system, follow these steps:

- **Java JDK 17 (or above)**:
  - Verify: `java -version`
- **Maven 3.8+**:
  - Verify: `mvn -version`
- **Playwright Browsers**:
  - The browsers are automatically downloaded during the first test run.
- **IDE Recommendations**:
  - **VS Code**: Install `Extension Pack for Java` and `Cucumber (Gherkin)` extensions.
  - **IntelliJ IDEA**: Install the `Cucumber for Java` plugin.

## 2. Running The Tests

### Standard Execution (Default 2 Workers)
```bash
mvn clean test
```

### Full Install (Including Updates)
To update your local repository and run all tests:
```bash
mvn clean install -U
```
*Note: The `install` command runs all tests by default. To install without running tests, use `mvn clean install -DskipTests`.*

### Configure Parallel Workers
```bash
mvn clean test -Dworkers=4
```

### Run Specific Tagged Scenarios
```bash
mvn clean test -D"cucumber.filter.tags=@SauceDemo"
```

## 3. Configuration & Test Data
All configurations are stored in **`src/test/resources/config.properties`**. 
- Values are read via the **Owner library** (`FrameworkConfig.java`).
- To run tests visibly, set `browser.headless=false`.

## 4. Viewing The Reports

This framework provides multiple types of reports:

### A. Allure Report (Advanced)
Allure provides a visual, step-by-step breakdown of test execution.
- **Generate & View (Recommended)**:
  ```bash
  mvn allure:serve
  ```
  *This generates a temporary report and opens it in your default browser.*
- **Generate Static Report**:
  ```bash
  mvn allure:report
  ```
  *Report will be located in `target/site/allure-maven-plugin/index.html`.*

### B. Cucumber HTML Report (Standard)
- **Location**: `target/cucumber-reports.html`
- **How to Open**: Right-click the file in your IDE and select "Open in Browser" or drag it into any web browser.

### C. Surefire Reports (Raw Data)
- **Location**: `target/surefire-reports/`
- Contains XML and text files used by CI/CD tools (like Jenkins or Azure DevOps) to track pass/fail statistics.

## 5. Artifact Collection
- **Execution Videos**: `target/videos/`
- **Failure Screenshots**: `target/screenshots/`
- **Playwright Traces**: `target/traces/`. View them at [trace.playwright.dev](https://trace.playwright.dev).

## 6. CI/CD Integration

### GitHub Setup
1. Create a new repository on GitHub.
2. Link your local project:
   ```bash
   git remote add origin <your-github-repo-url>
   git branch -M main
   git push -u origin main
   ```

### Jenkins Configuration
The framework includes a pre-configured `Jenkinsfile`.
- **Full Setup Guide**: See [Jenkins Setup & PR Guide](docs/jenkins_setup.md) for detailed installation and configuration steps.
- **Features**:
   - **Cross-Platform**: Automatically detects and runs on Windows or Linux agents.
   - **Artifacts**: Automatically archives test videos, screenshots, and traces.
   - **Reporting**: Generates both Allure and JUnit (Surefire) reports after every run.
