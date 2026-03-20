# Jenkins Setup & Configuration Guide

This guide provides step-by-step instructions for installing Jenkins, configuring the Playwright Java framework, and setting up automated Pull Request (PR) triggers.

## 1. Installation Guide (Windows)

1. **Download Jenkins**:
   - Go to the [Jenkins Download Page](https://www.jenkins.io/download/).
   - Download the **Windows (LTS)** installer (.msi).
2. **Install**:
   - Run the installer.
   - **Service Logon Credentials**: Use "Run service as LocalSystem" (simplest for local setup) or provide specific account details.
   - **Port Selection**: Default is `8080`. Ensure it's not in use.
3. **Unlock Jenkins**:
   - Open `http://localhost:8080` in your browser.
   - Retrieve the initial admin password from the path shown on the screen (usually `C:\ProgramData\Jenkins\.jenkins\secrets\initialAdminPassword`).
4. **Install Plugins**:
   - Select "Install suggested plugins."

---

## 2. Framework Configuration

Once Jenkins is running, you need to configure it to talk to Maven, Java, and Allure.

### A. Install Required Plugins
1. Go to **Manage Jenkins** > **Plugins** > **Available Plugins**.
2. Search and install:
   - **Allure Jenkins Plugin**
   - **GitHub Integration Plugin** (usually included in suggested plugins)

### B. Global Tool Configuration
1. Go to **Manage Jenkins** > **Tools**.
2. **JDK**:
   - Name: `JDK 17`
   - JAVA_HOME: Path to your JDK folder (e.g., `C:\Program Files\Java\jdk-17`).
3. **Maven**:
   - Name: `Maven 3.8` (must match the name in your `Jenkinsfile`).
   - Click "Install automatically" or provide the path to your local Maven.

---

## 3. Creating the Pipeline Job

1. Click **New Item**.
2. Name it `Playwright-Framework-Pipeline`.
3. Select **Pipeline** (or **Multibranch Pipeline** for PR support).
4. Under **Pipeline** section:
   - Definition: `Pipeline script from SCM`.
   - SCM: `Git`.
   - Repository URL: `https://github.com/rahulagarwal90/Playwright_Java_Antigravity_Framework`.
   - Script Path: `Jenkinsfile`.

---

## 4. Pull Request (PR) Workflow Setup

### A. GitHub Webhook
1. Go to your GitHub Repository > **Settings** > **Webhooks**.
2. Click **Add webhook**.
3. Payload URL: `http://<your-jenkins-url>/github-webhook/`.
4. Events: Select "Let me select individual events" and check:
   - Pushes
   - Pull Requests

### B. Branching Strategy
- **`main`**: Protected branch. No direct commits allowed.
- **Feature Branches (`feature/login`, `feature/cart`)**: Developers work here.
- **The Process**:
  1. Create a PR from `feature` to `main`.
  2. Jenkins **automatically** detects the PR and runs `mvn clean test`.
  3. If tests pass, the PR can be merged.
  4. If tests fail, Allure reports will show exactly what went wrong.
