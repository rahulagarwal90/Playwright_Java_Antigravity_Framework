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

## 5. Best Practices for Credentials

In a professional automation environment, security is paramount. Here is how to handle your Jenkins credentials:

### A. Jenkins Admin Account (Web UI)
- **Password Manager**: Use a tool like **LastPass**, **1Password**, or **Dashlane**.
  - *Note: If Google Password Manager won't save your password for `localhost`, see the **[Troubleshooting guide](#6-troubleshooting-password-management-google-chrome)** at the bottom.*
- **SSO/LDAP (Industry Standard)**: In corporate setups, Jenkins is linked to Active Directory for single-login security.

### B. Project Secrets (GitHub Tokens, API Keys)
- **Jenkins Credential Store**: Never hardcode secrets in your `pom.xml`, `config.properties`, or `Jenkinsfile`.
- **How to Use**:
  1. Go to **Manage Jenkins** > **Credentials**.
  2. Add your secret as a "Secret text" or "Username with password".
  3. Reference them in your `Jenkinsfile` using environment variables. This ensures they are **masked** (shown as `****`) in the logs.

### C. The `initialAdminPassword`
- This file is **temporary**. Once you create your first admin user during the setup wizard, you should delete this file or simply ignore it, as it will no longer be valid.
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

---

## 6. Troubleshooting: Password Management (Google Chrome)

If Google Password Manager does not save your Jenkins credentials for `localhost`, follow these steps:

### The "Hosts" Workaround
1. **Open Notepad as Administrator**:
   - Press Windows Key, type **Notepad**, right-click it, and select **Run as administrator**.
2. **Open the Hosts File**:
   - Go to `File > Open` and go to `C:\Windows\System32\drivers\etc\hosts`.
   - *Note: You must set the file filter to "All Files (*.*)" to see it.*
3. **Add the Alias**:
   - At the bottom of the file, add: `127.0.0.1  jenkins.local`
   - Save the file (**Ctrl + S**).
4. **Result**:
   - Use `http://jenkins.local:8080` in your browser. Google will now treat it as a real site and sync your passwords!
