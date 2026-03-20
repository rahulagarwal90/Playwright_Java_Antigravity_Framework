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
   - Retrieve the initial admin password from `C:\ProgramData\Jenkins\.jenkins\secrets\initialAdminPassword`.
4. **Install Plugins**:
   - Select "Install suggested plugins."

---

## 2. Global Tool Configuration

1. Go to **Manage Jenkins** > **Tools**.
2. **JDK**: Name = `JDK 17`, Path = your JDK folder.
3. **Maven**: Name = `Maven 3.8`, click "Install automatically".
4. **Allure**: Name = `Allure`, click "Install automatically" from Maven Central.

---

## 3. Creating the Pipeline Job

1. Click **New Item** > Name it `Playwright-Java-Antigravity-Framework-Pipeline`.
2. Choose **Multibranch Pipeline**.
3. **Branch Sources**: Add **GitHub**.
4. **Repository URL**: `https://github.com/rahulagarwal90/Playwright_Java_Antigravity_Framework`.
5. **Credentials**: See Step 4 below.

---

## 4. GitHub Server Configuration (For Status Checkmarks)

1. Go to **Manage Jenkins** > **System** > **GitHub**.
2. **Add GitHub Server**:
   - **API URL**: `https://api.github.com`
   - **Credentials**: Click **Add** > **Secret text**.
   - **Secret**: Paste your GitHub PAT.
   - **ID**: `github-token`.
3. **Test Connection**: Ensure it says "verified".

---

## 5. Pull Request (PR) Workflow Setup

### A. GitHub Webhook
1. Go to GitHub Repo > **Settings** > **Webhooks** > **Add webhook**.
2. Payload URL: `http://<your-public-ip-or-ngrok-url>/github-webhook/`.
   - *Note: If on `localhost`, GitHub cannot "see" you without a tool like **ngrok**.*

### B. Branch Protection (The Gatekeeper)
1. Go to GitHub > **Settings** > **Branches** > **Add branch protection rule**.
2. **Branch pattern**: `main`.
3. Check: **"Require a pull request before merging"**.
4. Check: **"Require status checks to pass before merging"**.
5. Search for your Jenkins job name and select it.

---

## 6. Nightly Scheduling (Automated Runs)

The framework runs a full suite every night at **2:00 AM**.
- This is managed via the `Jenkinsfile` under `triggers { cron('H 02 * * *') }`.
- **Note on the `H` (Hash)**: In Jenkins, `H` stands for "Hash". Instead of starting exactly at 2:00:00 AM (which could overload the server), it tells Jenkins to pick a random minute between 2:00 AM and 2:59 AM. This is a professional best practice to balance server load.
- In Jenkins, these will show as **"Started by timer"**.

---

## 7. Monitoring & Custom Builds

### A. Build with Parameters
If you have fixed your credentials, you will see **"Build with Parameters"** in the left menu.
- Select your **Browser** (Chrome/Firefox/Webkit).
- Enter your **Tags** (e.g., `@SauceDemo`).

### B. Viewing Results
- **Logs**: Click Build # > **Console Output**.
- **Reports**: Click the **Allure Report** icon.
- **Videos/Traces**: Click Build # > **Last Successful Artifacts**.

---

## 8. Troubleshooting: Bad Credentials (401 Error)

If you see `org.kohsuke.github.HttpException: {"message": "Bad credentials"}`, follow this:

1. **Regenerate PAT**: Go to GitHub > Developer Settings > Tokens (classic). Ensure `repo` and `admin:repo_hook` are checked.
2. **Update Job Credentials**: 
   - Go to Job > **Configure** > **Branch Sources**.
   - Select/Create a **"Username with password"** type.
   - **Username**: Your GitHub ID.
   - **Password**: The NEW Personal Access Token.
3. **Update System Credentials**:
   - Go to **Manage Jenkins** > **System** > **GitHub**.
   - Use the same new Token as a **"Secret text"** type.

---

## 9. Troubleshooting: Password Management (Localhost)

If Chrome won't save your `localhost:8080` password, use the **Hosts Workaround**:
1. Edit `C:\Windows\System32\drivers\etc\hosts`.
2. Add: `127.0.0.1  jenkins.local`
3. Access Jenkins via `http://jenkins.local:8080`.

---

## 10. Navigating the Jenkins UI: Cheat Sheet

| To see this... | Go here... |
| :--- | :--- |
| **Real-time Logs** | Build # > **Console Output** |
| **Allure Dashboard** | Main job page > **Allure Report** icon |
| **Videos & Traces** | Build # > **Last Successful Artifacts** |
| **Trend Graphs** | Main job page > **Test Result Trend** |
| **Custom Run** | **Build with Parameters** |

---
